# Personalization Campaign Studio — Design Spec
**Status:** Ready for eng (`thorough-writing-plans`) · **Date:** 2026-07-07 · **Author:** PM (workshop) ·
**Repo:** Umbraco-CMS @ `ba6ec7ab` (shallow clone at `workshop/codebase/Umbraco-CMS`)
*(Workshop copy — for real use, save as `docs/specs/YYYY-MM-DD-campaign-studio-design.md` and commit.)*

## Problem & context
Marketers can't run a trustworthy personalization experiment end-to-end: no campaign workspace,
stopped variants can resurface, segment previews aren't trusted. Cluster: 25 deduped accounts /
$2.12M at risk; $873k churned. Full evidence: `roadmap-recommendation.md`; prototype + assumptions:
`campaign-studio-prototype.html` + `-notes.md`.

## Goals / Non-goals
- **Goals:** marketer completes create → per-segment preview → results → promote/stop unaided;
  a stopped variant cannot resurface via any publish or request path (server-enforced); shareable
  per-segment preview links; no editor p95 regression (guardrail).
- **Non-goals (v1):** AI test ideation; full audit console; approval chains beyond a lock state;
  rigorous stats engine (v1 uses labeled simple thresholds); headless variant exposure (post-MVP);
  a new top-level backoffice section (v1 ships as a workspace under an existing section).

## Design
- **Approach:** replace the core's no-op segment service with a real provider (note: that seam is
  a segment *catalog* — it lists segments; it evaluates nothing), add a **per-request segment
  assignment engine** (member/session → segment) and harden
  the client-supplied segment headers, add a campaign/variant model on content nodes, enforce
  lifecycle as a **default-deny serving policy** (a variant serves only while its campaign is
  live), build the 4-screen studio as a workspace, capture basic events for a results readout,
  ship behind a feature flag.
- **Components:** segment provider + assignment engine · campaign/variant persistence (+ migration
  with upgrade-path registration) · serving-policy gate + cache/CDN invalidation · per-segment
  preview links · studio workspace UI · event capture + readout API · flag.
- **Conventions to follow:** service registration patterns of `src/Umbraco.Core/DependencyInjection/`;
  management-API and backoffice-package conventions of the webhook slice (the in-repo analog).

## Verified assumptions
(each re-verified at emission, 2026-07-07, @ `ba6ec7ab` — command + full output)
- [Verified] Content variation models segments.
  `grep -n "Segment = 2" src/Umbraco.Core/Models/ContentVariation.cs`
  → `30:    Segment = 2,`
- [Verified] A segment-service seam exists with a no-op default registered.
  `grep -n "public interface ISegmentService" src/Umbraco.Core/Services/ISegmentService.cs`
  → `9:public interface ISegmentService`
  `grep -n "public class NoopSegmentService" src/Umbraco.Core/Services/NoopSegmentService.cs`
  → `10:public class NoopSegmentService : ISegmentService`
  `grep -n "ISegmentService" src/Umbraco.Core/DependencyInjection/UmbracoBuilder.cs`
  → `462:            Services.AddUnique<ISegmentService, NoopSegmentService>();`
- [Verified] Segments are off by default.
  `sed -n '10p;17p' src/Umbraco.Core/Configuration/Models/SegmentSettings.cs`
  → `    private const bool StaticEnabled = false;` / `    public bool Enabled { get; set; } = StaticEnabled;`
- [Verified] Exactly 5 files reference `ISegmentService` (interpretation of what that means for
  blast radius lives in the design body and RAD, not here).
  `grep -rln "ISegmentService" src --include="*.cs"`
  → `NoopSegmentService.cs · ISegmentService.cs · UmbracoBuilder.cs · Document/AvailableSegmentsController.cs · Segment/AllSegmentController.cs`
- [Verified] Affected-subsystem sizes (context only; the estimate's sizing anchor is the webhook
  analog in ESTIMATE.md §8).
  `git ls-files -- 'src/Umbraco.Core/**' | grep -E '\.cs$' | xargs cat | wc -l` → `212613`
  `git ls-files -- 'src/Umbraco.Web.UI.Client/**' | grep -Ev '/(node_modules|dist|wwwroot|bin|obj)/' | grep -E '\.(ts|tsx|js|html)$' | grep -v '\.min\.' | xargs cat | wc -l` → `440931`
  (An earlier draft reported 48,690 / 39,584 via `xargs wc -l | tail -1` — an undercount: xargs
  batches the file list and `tail -1` keeps only the last batch's total. Corrected in review.)
- [Verified] Backoffice client has a test entrypoint defined (that the script *runs* is not
  verified here — see Unverified).
  `grep -n '"test"' src/Umbraco.Web.UI.Client/package.json`
  → `218:		"test": "npm run generate:check-const-test && npm run check:module-dependencies && web-test-runner"`

### Verified absences (what is NOT there)
- [Verified absent] Segment handling in the two content/publish orchestration services checked —
  lifecycle enforcement is new work, not configuration.
  `grep -ci "segment" src/Umbraco.Core/Services/ContentPublishingService.cs` → `0`
  `grep -ci "segment" src/Umbraco.Core/Services/ContentService.cs` → `0`
- [Verified absent] Server-side segment assignment on request paths — the inbound segment context
  is **client-supplied**: `grep -n "X-UMB-SEGMENT" src/Umbraco.Web.Common/Extensions/HttpRequestExtensions.cs`
  → `49: => request.Headers.TryGetValue("X-UMB-SEGMENT", ...)`; `grep -n "Accept-Segment"
  src/Umbraco.Core/Constants-DeliveryApi.cs` → `85: public const string AcceptSegment = "Accept-Segment";`
  — and none of the 5 `ISegmentService` files (list above) sit on the render path. The request
  middleware builds the variation context from the client header unconditionally:
  `grep -n "ClientSegment" src/Umbraco.Web.Common/Middleware/UmbracoRequestMiddleware.cs`
  → `91: ... new VariationContext(..., context.Request.ClientSegment());`
- [Verified absent] A serving-side gate on the segments-enabled setting — its only consumer
  outside configuration is a backoffice presentation factory:
  `grep -rn "_segmentSettings.Enabled" src --include="*.cs"`
  → `Umbraco.Cms.Api.Management/Factories/ConfigurationPresentationFactory.cs:71: UseSegments = _segmentSettings.Enabled,`
  Implication (design body): "segments off by default" is a backoffice UI toggle, not a serving
  control; the studio needs an assignment engine, and unhardened headers are a stopped-variant
  bypass.

### Deprecation & staleness check (mandatory)
- [Verified] Parts of the seam are already deprecated (the per-document method and its controller —
  the interface itself is not).
  `grep -n "Obsolete" src/Umbraco.Core/Services/ISegmentService.cs`
  → `28:    [Obsolete("This method is temporary. A more permanent solution will follow. Scheduled for removal in Umbraco 20.")]`
  `grep -n "Obsolete" src/Umbraco.Cms.Api.Management/Controllers/Document/AvailableSegmentsController.cs`
  → `22:[Obsolete("This controller is temporary. A more permanent solution will follow. Scheduled for removal in Umbraco 20.")]`
  `grep -n "Obsolete" -A1 src/Umbraco.Core/Configuration/Models/SegmentSettings.cs`
  → `22:[Obsolete("This functionality will be moved to a client-side extension. Scheduled for removal in Umbraco 19.")]` (`AllowCreation`)
  Current version: `grep '"version"' version.json` → `"version": "18.1.0-rc"`.
  **Implication:** removal in v20 from an 18.x codebase means core is redesigning this surface in
  the current major — the per-document segment API our targeting picker needs is officially
  temporary. Written coordination with the core team is a blocking dependency for the studio's
  create screen (see RAD).

## Unverified assumptions & risks (RAD)
- [Assumption] Campaign variants attach to existing content nodes without a schema migration —
  1-hour spike before milestone 1 (`umbracoPropertyData` appears segment-aware; confirm end to end).
- [Assumption] An analytics/event surface is readable or cheaply capturable for the results
  readout — 2-hour spike; a closed surface adds ~1 sprint (top sensitivity).
- [Assumption] Build/test commands run as documented — not re-verified here (`dotnet` unavailable
  in this environment); engineering to confirm on a dev machine.
- [Risk] 🔴 Default-deny serving must hold across **every** path that resolves a variant —
  enumerate all `VariationContext` construction sites during design review; treat as a correctness
  guarantee with its own test suite (note: the integration-test harness defaults can produce
  false-green cache tests — write the caching-enabled variants as their own cases).
- [Risk] 🔴 Client-supplied segment headers are a lock bypass until hardened (see Verified absences).
- [Risk] 🔴 Shareable per-segment preview links are themselves a segment-impersonation vector —
  they need token scoping in design review, and header hardening must carve out the backoffice's
  existing authenticated preview flow (which uses `X-UMB-SEGMENT` today) without breaking it.
- [Risk] 🟡 Studio UI flows through the single frontend engineer; 50% designer availability can
  stall it — pairing + pre-staged design specs are schedule requirements.
- [Dependency] Core team: replacement direction for the obsolete segment API — written outcome
  gates the create-screen design. [Dependency] Delivery API team for headless exposure (post-MVP).

## Requirements (MoSCoW)
- **P0:** campaign CRUD; segment provider + per-request assignment; hardened segment-header
  ingress; server-side default-deny lifecycle; per-segment preview links; results readout from
  captured events; feature flag; no editor p95 regression.
- **P1:** promote/stop permissions; overlapping-campaign precedence UI; preview-failure retry UX.
- **P2:** AI test-ideation backlog; confidence-interval stats. **Won't (v1):** audit console,
  approval chains, headless exposure, new top-level section.

## User stories + acceptance criteria
- As a growth marketer, I want to launch a segment test myself, so results don't live in
  spreadsheets. — Given a Gold+ campaign with variants A/B, When I complete the create flow, Then
  the test serves to members resolved into that segment (not merely to header-bearing requests)
  and shows on Campaigns as Live.
- As a compliance reviewer, I want stopped variants locked, so expired offers can't resurface. —
  Given a Stopped 🔒 campaign, When any editor republishes the page **or any request carries a
  segment header for it**, Then the stopped variant is not served and the audit line records it.
- As a marketer, I want a per-segment preview link, so stakeholders see exactly what a segment
  sees. — Given a live campaign, When I share the preview link for "EU visitor", Then it renders
  variant B exactly as served.

## Success metrics
- **Leading:** campaigns created/week by non-developers; time-to-first-campaign; preview links
  shared. **Lagging:** cluster ARR at risk $2.12M → <$700k (2 qtrs); escalated cluster tickets
  → ≤4; guardrail: editor p95 unchanged.

## Open questions
- Precedence for overlapping campaigns (owner: eng design — blocks milestone-1 design).
- v1 winner-call rigor (owner: PM — doesn't block).

## Handoff
**Next step (engineering):** run `thorough-writing-plans` against this spec. Effort/cost live in
`ESTIMATE.md`. This spec is the PM-side stopping point; no code was written.
