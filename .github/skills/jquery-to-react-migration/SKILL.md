---
name: jquery-to-react-migration
description: Incrementally migrates legacy jQuery features, pages, DOM manipulation, event handlers, AJAX calls, templates, and plugins to React with TypeScript while preserving behavior, API contracts, CSS hooks, accessibility, and test coverage. Use this skill when analyzing, planning, implementing, reviewing, or validating a jQuery-to-React migration.
---

# jQuery-to-React Migration Skill

## Purpose

Use this skill to migrate a legacy jQuery application to React safely and incrementally.

The primary objective is functional parity. Preserve current user-visible behavior, API contracts, validation rules, CSS hooks, accessibility behavior, and integration points before attempting redesign or architectural cleanup.

## Core principles

- Migrate one feature, widget, page, or user journey at a time.
- Do not rewrite the complete application in one change.
- Analyze the legacy implementation before editing files.
- Add characterization tests when existing behavior is not adequately tested.
- Keep React-owned and jQuery-owned DOM regions separate.
- Never allow jQuery to mutate a DOM subtree owned by React.
- Preserve server API contracts unless the user explicitly requests a change.
- Preserve existing CSS selectors, IDs, and class names during the parity phase.
- Use TypeScript for new React code unless the repository explicitly uses JavaScript.
- Use functional components and React hooks.
- Prefer native React capabilities before adding dependencies.
- Do not delete legacy code until the replacement passes all verification steps.
- Keep each change small, reviewable, testable, and reversible.

## Required workflow

### Phase 1: Repository discovery

Before proposing a migration:

1. Inspect package files, build scripts, linting, formatting, test configuration, and TypeScript configuration.
2. Identify the current application entry points.
3. Identify whether React already exists in the repository.
4. Identify server-rendered templates, static HTML, or client-side templates.
5. Identify current browser support requirements.
6. Identify existing conventions for API services, components, tests, and styles.
7. Reuse repository conventions unless they conflict with the migration requirements.

Do not replace the existing build system merely because a different tool is preferred. Propose build-system changes separately when required.

### Phase 2: Analyze the selected legacy feature

Before modifying code, locate and document:

1. Feature entry points and initialization code.
2. jQuery selectors and the elements they target.
3. Direct and delegated event handlers.
4. DOM reads and DOM mutations.
5. Form submission and validation behavior.
6. AJAX calls, URLs, HTTP methods, headers, query parameters, payloads, and response transformations.
7. Loading, success, empty, validation, and failure states.
8. Global variables, shared mutable state, and cross-feature dependencies.
9. Timers, subscriptions, observers, and browser event listeners.
10. Third-party jQuery plugins and their lifecycle methods.
11. Templates, HTML fragments, CSS selectors, data attributes, and accessibility attributes.
12. Existing unit, integration, end-to-end, and snapshot tests.
13. Analytics, logging, feature flags, and telemetry.
14. Behavior that is ambiguous or not inferable from the code.

Do not invent behavior. Flag uncertainty and preserve the current implementation until it is resolved.

### Phase 3: Produce a migration plan

The plan must include:

- Legacy behavior summary.
- Dependency and side-effect map.
- Proposed React ownership boundary.
- Components and responsibilities.
- Props and TypeScript interfaces.
- Local state and shared state.
- API service changes.
- Required custom hooks.
- Mount or routing integration point.
- Plugin adapter strategy, if needed.
- Test cases.
- Rollback strategy.
- Files to create, modify, and eventually remove.
- Migration complexity: low, medium, or high.
- Risks and assumptions.

Prefer the smallest independently deployable migration unit.

### Phase 4: Add characterization coverage

Before changing behavior-heavy code:

1. Add tests that capture the current observable behavior.
2. Cover successful, empty, validation, loading, and failure scenarios.
3. Capture request URL, method, parameters, payload, and important headers.
4. Capture keyboard and focus behavior where applicable.
5. Capture cleanup behavior for listeners, timers, subscriptions, and plugins.

Do not assert internal implementation details unless no stable user-visible assertion is possible.

### Phase 5: Implement the React replacement

When implementing:

- Use TypeScript interfaces for props, domain models, and API data.
- Use controlled inputs for forms unless an uncontrolled input is explicitly justified.
- Put HTTP calls in a service module rather than directly in presentation components.
- Represent user-visible UI changes with React state and JSX.
- Use stable IDs from domain data as React keys. Do not use array indexes when list order may change.
- Include explicit loading, validation, empty, success, and error states.
- Preserve existing CSS IDs and classes during the first migration phase.
- Preserve analytics and telemetry events.
- Abort or ignore stale asynchronous requests when appropriate.
- Clean up listeners, timers, subscriptions, observers, and plugins.
- Avoid unrelated refactoring.
- Avoid introducing global state when local state is sufficient.
- Do not use `dangerouslySetInnerHTML` unless necessary and documented.
- Do not use `useEffect` as a general replacement for event-driven logic.

### Phase 6: Integrate safely

Use an explicit React mount boundary, for example:

```html
<div id="react-customer-search-root"></div>
```

React may own the contents of this element. Legacy jQuery code must not read, append, remove, reorder, hide, show, or alter descendants of the React root.

If a feature flag is available, keep the legacy and React implementations selectable until parity is verified. Do not render both implementations with active event handling at the same time.

### Phase 7: Verify

Run the repository's applicable commands for:

- Dependency installation, if needed.
- Type checking.
- Linting.
- Unit tests.
- Component or integration tests.
- End-to-end tests, when available.
- Production build.

Also verify:

- Request URLs, methods, parameters, payloads, and response handling are unchanged.
- Validation messages are unchanged.
- CSS IDs, class names, and data attributes required by other systems are preserved.
- Keyboard navigation and focus behavior are preserved or improved without breaking expectations.
- Loading, empty, success, and error states match the legacy feature.
- Analytics and telemetry still fire correctly.
- No jQuery code manipulates React-owned DOM.
- No listener, timer, subscription, observer, request, or plugin lifecycle leak remains.

If a command cannot be executed, report the exact command and reason. Do not claim that validation passed when it was not run.

### Phase 8: Cleanup

Remove legacy code only after the React implementation passes verification.

Cleanup may include:

- Replaced jQuery selectors and handlers.
- Obsolete templates or HTML fragments.
- Unused global variables.
- Feature-specific plugin initialization.
- Unused CSS that is confirmed to have no remaining consumers.
- Dependencies that have no remaining repository usage.

Search the repository before deleting shared code or dependencies.

## Standard jQuery-to-React mappings

### DOM selection

Legacy:

```javascript
const $message = $("#message");
```

React alternative:

- Use props or state when the element represents application data.
- Use a ref only for imperative browser or third-party integration.

```tsx
const messageRef = useRef<HTMLDivElement>(null);
```

### Show and hide

Legacy:

```javascript
$("#message").show();
$("#message").hide();
```

React:

```tsx
{isVisible && <div id="message">Message</div>}
```

### Class changes

Legacy:

```javascript
$("#save-button").addClass("disabled");
```

React:

```tsx
<button className={isDisabled ? "disabled" : ""} disabled={isDisabled}>
  Save
</button>
```

### Text and HTML updates

Legacy:

```javascript
$("#status").text(statusMessage);
```

React:

```tsx
<p id="status">{statusMessage}</p>
```

Prefer JSX over raw HTML insertion.

### Append and remove

Legacy:

```javascript
$("#results").append(itemHtml);
```

React:

```tsx
<ul id="results">
  {items.map((item) => (
    <li key={item.id}>{item.name}</li>
  ))}
</ul>
```

### Event handlers

Legacy:

```javascript
$("#save-button").on("click", saveItem);
```

React:

```tsx
<button onClick={saveItem}>Save</button>
```

For delegated events, move the semantic handler to the rendered child or use a parent React handler only when event delegation is still appropriate.

### AJAX

Legacy:

```javascript
$.ajax({
  url: "/api/customers",
  method: "GET",
  data: { query }
});
```

React service:

```typescript
export async function searchCustomers(
  query: string,
  signal?: AbortSignal
): Promise<Customer[]> {
  const params = new URLSearchParams({ query });
  const response = await fetch(`/api/customers?${params.toString()}`, {
    method: "GET",
    headers: { Accept: "application/json" },
    signal
  });

  if (!response.ok) {
    throw new Error(`Customer search failed: ${response.status}`);
  }

  return response.json() as Promise<Customer[]>;
}
```

Preserve credentials, CSRF behavior, headers, serialization, timeout behavior, and error interpretation from the original application.

### Document ready

Legacy:

```javascript
$(document).ready(initializeFeature);
```

React:

Mount the component from the relevant application entry point. Do not place application initialization inside an arbitrary component effect when it belongs in the entry point.

### Global mutable variables

Legacy:

```javascript
let selectedCustomer = null;
```

React alternatives, in preferred order:

1. Component state.
2. Lifted state shared by nearby components.
3. Context for stable cross-tree dependencies.
4. An existing repository state solution for genuinely global or complex state.

Do not introduce a new state library without demonstrated need.

## jQuery plugin migration

Prefer replacing a plugin with an accessible React-compatible implementation. When immediate replacement is too risky, create a temporary adapter:

```tsx
import { useEffect, useRef } from "react";
import $ from "jquery";

export function LegacyDatePicker() {
  const inputRef = useRef<HTMLInputElement>(null);

  useEffect(() => {
    const element = $(inputRef.current!);
    element.datepicker();

    return () => {
      element.datepicker("destroy");
    };
  }, []);

  return <input ref={inputRef} type="text" />;
}
```

Plugin adapter rules:

- Keep the adapter small and isolated.
- Initialize the plugin once unless its documented lifecycle requires otherwise.
- Destroy it during cleanup.
- Synchronize values deliberately rather than allowing hidden two-way mutation.
- Document the adapter as temporary technical debt.
- Add tests for initialization, updates, events, and cleanup when practical.

## Component design standards

- Keep each component focused on one responsibility.
- Prefer named exports unless repository conventions require default exports.
- Define explicit prop interfaces.
- Extract reusable behavior into custom hooks only when reuse or complexity justifies it.
- Keep event handlers short and intention-revealing.
- Keep API response adaptation outside presentation JSX.
- Prefer semantic HTML.
- Preserve labels, roles, focus behavior, and accessible names.
- Avoid prop drilling only when it causes a real maintainability problem.

## Testing standards

Use the repository's existing testing libraries and conventions. For React component tests, prefer user-visible behavior:

- Search by role, label, or visible text.
- Exercise actual user interactions.
- Mock at the network or service boundary.
- Test loading, empty, success, validation, and error scenarios.
- Test stale-request handling when searches can overlap.
- Test cleanup when the component owns external resources.
- Avoid testing React state or private implementation details directly.

## Required output format

For every migration request, return the following sections:

### 1. Legacy behavior

Summarize current user-visible behavior and contracts.

### 2. Dependency map

List selectors, handlers, APIs, global state, plugins, templates, CSS, analytics, and external consumers.

### 3. Migration plan

Describe the smallest React boundary, component structure, state, service modules, tests, integration, and rollback.

### 4. Implementation

List files created and modified, then make only the scoped changes.

### 5. Verification

Report each command executed and whether it passed. Include manual checks that remain.

### 6. Cleanup

List legacy code that is safe to remove and code that must remain.

### 7. Risks and assumptions

State unresolved behavior, unexecuted validation, integration risks, and assumptions explicitly.

## Definition of done checklist

A feature migration is complete only when every applicable item below is checked. If an item is not applicable, mark it as `N/A` and provide a short reason. Do not mark an item complete unless it was verified.

### Scope and planning

- [ ] The migrated feature boundary is clearly defined.
- [ ] Existing user-visible behavior is documented.
- [ ] jQuery selectors, handlers, plugins, templates, APIs, CSS hooks, and external dependencies are inventoried.
- [ ] Ambiguous behavior and assumptions are documented.
- [ ] The change is limited to the approved migration scope.
- [ ] A rollback or feature-flag strategy is documented when required.

### React implementation

- [ ] The feature is implemented with React and TypeScript, unless the repository explicitly requires JavaScript.
- [ ] Components have clear responsibilities and explicit prop types.
- [ ] Local state is used unless shared or global state is demonstrably required.
- [ ] API access is separated from presentation components.
- [ ] Loading, validation, empty, success, and error states are implemented.
- [ ] Stable domain identifiers are used as list keys where available.
- [ ] No unnecessary dependencies or state-management libraries were introduced.
- [ ] No unrelated refactoring was included.

### DOM ownership and interoperability

- [ ] React has an explicit mount point or routing boundary.
- [ ] React exclusively owns all descendants of its mount root.
- [ ] No jQuery code reads or mutates React-owned DOM.
- [ ] Remaining jQuery features continue to operate outside the React boundary.
- [ ] Temporary jQuery plugin adapters are isolated and documented.
- [ ] Plugin, listener, timer, observer, and subscription cleanup is implemented.
- [ ] Active or stale asynchronous requests are aborted or safely ignored when appropriate.

### Functional parity

- [ ] Existing API URLs are preserved or intentionally changed and documented.
- [ ] HTTP methods, headers, credentials, query parameters, payloads, and serialization remain compatible.
- [ ] Response mapping and error interpretation remain compatible.
- [ ] Validation rules and messages match the legacy feature.
- [ ] Loading, empty, success, and failure behavior matches the legacy feature.
- [ ] Keyboard interactions and focus behavior are preserved.
- [ ] Existing analytics, logging, telemetry, and feature flags remain functional.
- [ ] Important browser and navigation behavior is preserved.

### Styling and accessibility

- [ ] Required CSS IDs, classes, data attributes, and integration hooks are preserved.
- [ ] The migrated feature visually matches the legacy implementation during the parity phase.
- [ ] Semantic HTML is used where practical.
- [ ] Inputs have accessible labels.
- [ ] Buttons and controls have accessible names.
- [ ] Error and status messages are exposed appropriately to assistive technologies.
- [ ] Keyboard-only operation has been verified.
- [ ] Focus order and visible focus indicators have been verified.

### Testing

- [ ] Characterization tests cover important legacy behavior.
- [ ] Component tests cover user-visible behavior rather than internal state.
- [ ] Validation behavior is tested.
- [ ] Loading behavior is tested.
- [ ] Empty-result behavior is tested.
- [ ] Successful behavior is tested.
- [ ] Failure behavior is tested.
- [ ] API requests and payloads are tested at the appropriate boundary.
- [ ] Overlapping or stale requests are tested when applicable.
- [ ] Resource cleanup is tested when applicable.
- [ ] Existing relevant tests continue to pass.

### Automated verification

Record the command and result for every applicable check.

- [ ] Dependencies install successfully.
- [ ] Type checking passes.
- [ ] Linting passes.
- [ ] Formatting checks pass.
- [ ] Unit tests pass.
- [ ] Component and integration tests pass.
- [ ] End-to-end tests pass when available.
- [ ] The production build passes.
- [ ] The application starts successfully in the expected environment.
- [ ] No new relevant browser-console errors or warnings are present.

### Manual verification

- [ ] The primary user journey was tested manually.
- [ ] Validation and error recovery were tested manually.
- [ ] Rapid or repeated interactions were tested where relevant.
- [ ] Keyboard navigation was tested manually.
- [ ] Responsive behavior was checked at supported sizes.
- [ ] Supported browsers were checked according to project requirements.
- [ ] Network failure or slow-network behavior was checked where relevant.

### Cleanup and documentation

- [ ] Replaced jQuery code has been removed or retained with a documented reason.
- [ ] Obsolete selectors, handlers, templates, and initialization code have been removed.
- [ ] Unused imports, variables, styles, assets, and dependencies have been removed after repository-wide verification.
- [ ] Shared legacy code was not deleted without confirming all consumers.
- [ ] Temporary adapters and remaining technical debt are documented.
- [ ] Remaining jQuery dependencies are listed.
- [ ] Architecture or developer documentation is updated where necessary.
- [ ] The pull request includes migration scope, validation evidence, risks, and rollback instructions.

### Final approval

- [ ] All applicable checklist items are complete.
- [ ] Every `N/A` item includes a reason.
- [ ] Unresolved risks are explicitly documented and accepted by the responsible reviewer.
- [ ] The React implementation can be rolled back without affecting unrelated features.
- [ ] The migrated feature is ready for deployment.

### Completion report template

Use the following format in the final migration report:

```markdown
## Definition of done result

- Scope: Passed | Failed | N/A
- React implementation: Passed | Failed | N/A
- DOM ownership: Passed | Failed | N/A
- Functional parity: Passed | Failed | N/A
- Styling and accessibility: Passed | Failed | N/A
- Testing: Passed | Failed | N/A
- Automated verification: Passed | Failed | N/A
- Manual verification: Passed | Failed | N/A
- Cleanup and documentation: Passed | Failed | N/A
- Final approval: Passed | Failed

### Commands executed

- `<command>`: Passed | Failed | Not run

### Remaining risks

- `<risk or "None identified">`

### N/A justifications

- `<checklist item>`: `<reason>`
```

### Sample completed migration report

The following example shows how to complete the report after migrating the customer search feature from jQuery to React. Replace the example values with evidence from the actual migration.

```markdown
## Definition of done result

**Feature:** Customer search  
**Legacy source:** `legacy/customer-search.js`  
**React entry point:** `src/customer-search-entry.tsx`  
**Migration owner:** Frontend Platform Team  
**Verification date:** 2026-10-01  
**Overall result:** Passed with one documented follow-up

- Scope: Passed
- React implementation: Passed
- DOM ownership: Passed
- Functional parity: Passed
- Styling and accessibility: Passed
- Testing: Passed
- Automated verification: Passed
- Manual verification: Passed
- Cleanup and documentation: Passed
- Final approval: Passed

### Migration summary

The customer search feature was migrated from jQuery to React with TypeScript.
The migration preserves the existing `/api/customers` endpoint, the `query`
parameter, validation messages, CSS IDs, CSS classes, loading behavior, empty
state, error state, and keyboard form submission.

React now owns the contents of `#react-customer-search-root`. Legacy jQuery code
continues to manage the rest of the page and does not access or mutate elements
inside the React root.

### Files created

- `src/features/customer-search/types.ts`
- `src/features/customer-search/customerService.ts`
- `src/features/customer-search/CustomerSearch.tsx`
- `src/features/customer-search/CustomerSearch.test.tsx`
- `src/customer-search-entry.tsx`

### Files modified

- `legacy/customer-page.html`
- `package.json`
- `README.md`

### Files removed

- `legacy/customer-search.js`

### Behavior verified

- Empty input displays `Enter a customer name.` and focuses the input.
- A valid submission sends `GET /api/customers?query=<value>`.
- Leading and trailing spaces are removed before submission.
- The Search button is disabled while a request is active.
- The loading message is visible while a request is active.
- Successful responses display customer names and email addresses.
- Empty responses display `No customers found.`.
- Failed requests display `Unable to search for customers. Try again.`.
- Submitting with the Enter key works.
- Starting a new search aborts the previous active request.
- Existing CSS IDs and classes remain unchanged.

### DOM ownership verification

- React mount point: `#react-customer-search-root`
- React exclusively owns all descendants of the mount point.
- Repository search found no remaining jQuery selector targeting descendants
  of `#react-customer-search-root`.
- No duplicate customer-search event handlers were observed.

### API compatibility

- URL: `/api/customers`, unchanged
- Method: `GET`, unchanged
- Query parameter: `query`, unchanged
- Accept header: `application/json`
- Response structure: unchanged
- Authentication and cookie behavior: unchanged
- Error message shown to the user: unchanged

### Tests added

- Empty-query validation
- Query trimming
- Successful customer rendering
- Empty-result rendering
- Request failure handling
- Loading state and disabled submit button
- Overlapping request cancellation
- Component unmount cleanup

### Commands executed

- `npm ci`: Passed
- `npm run typecheck`: Passed
- `npm run lint`: Passed
- `npm run format:check`: Passed
- `npm test -- --run`: Passed, 42 tests
- `npm run build`: Passed
- `npm run e2e -- customer-search`: Passed, 6 scenarios

### Manual verification

- Chrome desktop: Passed
- Edge desktop: Passed
- Keyboard-only navigation: Passed
- Empty query: Passed
- Successful search: Passed
- Empty result: Passed
- API failure and retry: Passed
- Rapid consecutive searches: Passed
- Responsive layout at supported widths: Passed
- Browser console errors or warnings: None observed

### Cleanup completed

- Removed the legacy customer-search submit handler.
- Removed obsolete customer-search selectors and initialization code.
- Replaced the legacy customer-search HTML with the React mount point.
- Confirmed that other legacy features do not import `customer-search.js`.
- Retained shared customer-search CSS because the React component uses the
  existing classes during the parity phase.

### Remaining jQuery dependencies

- Legacy order-history accordion
- Legacy date-picker plugin
- Legacy notification banner

These dependencies are outside the scope of this migration.

### Remaining risks

- The customer-search CSS remains in the legacy shared stylesheet. Moving it to
  a feature-owned stylesheet is a follow-up task and does not block deployment.

### N/A justifications

- Screen-reader regression automation: N/A. The repository does not currently
  include an automated accessibility test runner. Labels, roles, live regions,
  and keyboard behavior were reviewed manually.
- Feature-flag rollback: N/A. Deployment rollback restores the previous static
  asset bundle, and the feature has no server-side data migration.

### Rollback plan

1. Revert the customer-search migration commit.
2. Restore `legacy/customer-search.js` and the original HTML markup.
3. Rebuild and deploy the previous static asset bundle.
4. Run the legacy customer-search smoke test.

No database, API, or server configuration rollback is required.

### Follow-up actions

- Move shared customer-search styles into a feature-owned stylesheet.
- Add automated accessibility checks to the frontend test pipeline.

### Approval

- Engineering review: Approved
- QA review: Approved
- Product acceptance: Approved
- Deployment readiness: Approved
```

When generating a real completion report, use actual repository paths, command
results, test counts, browsers, risks, and reviewer decisions. Never copy the
sample's `Passed` values without performing the corresponding verification.

## Rollback checklist

Use this checklist before approving a migration for deployment and whenever a rollback is initiated. Every applicable item must be checked. Mark non-applicable items as `N/A` and provide a reason. Do not claim that rollback succeeded until post-rollback verification is complete.

### Rollback readiness before deployment

- [ ] The last known-good legacy version or deployment artifact is identifiable.
- [ ] The migration commits or pull requests that must be reverted are documented.
- [ ] The rollback owner and backup owner are identified.
- [ ] The conditions that trigger rollback are documented.
- [ ] The maximum acceptable rollback decision time is documented.
- [ ] Required repository, deployment, monitoring, and feature-flag access has been verified.
- [ ] The rollback procedure has been reviewed by an engineer familiar with the legacy feature.
- [ ] The rollback procedure does not depend on undocumented local files or credentials.
- [ ] The legacy implementation can be restored without affecting unrelated features.
- [ ] The previous frontend bundle or application artifact is available and deployable.
- [ ] Required legacy dependencies remain available or are included in the rollback artifact.
- [ ] Legacy HTML, templates, CSS, assets, selectors, and initialization code can be restored.
- [ ] API and server compatibility with the legacy client has been confirmed.
- [ ] Database, cache, queue, session, and configuration compatibility has been assessed.
- [ ] Data written by the React implementation remains readable by the legacy implementation.
- [ ] Any irreversible data or schema change is identified and has a separate recovery plan.
- [ ] Feature flags provide a safe fallback when available.
- [ ] The rollback procedure has been tested in a non-production environment when practical.
- [ ] Expected rollback duration and service impact are documented.
- [ ] Stakeholders and communication channels are identified.

### Rollback triggers

Initiate or evaluate rollback when one or more of these conditions occur:

- [ ] A critical user journey is unavailable or materially degraded.
- [ ] Error rate, failed requests, or frontend exceptions exceed the agreed threshold.
- [ ] API requests, payloads, authentication, or response handling are incompatible.
- [ ] Data loss, corruption, duplication, or inconsistent state is observed.
- [ ] Accessibility or keyboard use is materially worse than the legacy feature.
- [ ] A security or privacy regression is identified.
- [ ] Performance exceeds the agreed latency, bundle-size, or responsiveness threshold.
- [ ] React and jQuery both manipulate the same DOM subtree.
- [ ] A jQuery plugin, listener, timer, observer, subscription, or request causes instability or leaks.
- [ ] Monitoring is insufficient to determine whether the migration is operating safely.
- [ ] The agreed observation period reveals an unacceptable regression.

Document the evidence and decision. Do not use a rollback to conceal unresolved data integrity or security issues. Escalate those issues according to the project's incident process.

### Pre-rollback checks

- [ ] Record the incident, start time, affected environment, and observed symptoms.
- [ ] Capture relevant logs, metrics, traces, screenshots, request samples, and browser-console errors.
- [ ] Record the currently deployed application version, commit, and artifact identifier.
- [ ] Confirm that the issue is associated with the migrated feature or deployment.
- [ ] Confirm that rollback is safer than a forward fix under the current conditions.
- [ ] Identify any in-flight user actions or requests that rollback could interrupt.
- [ ] Pause further deployments to the affected environment.
- [ ] Notify engineering, QA, product, operations, and support stakeholders as applicable.
- [ ] Confirm that the target rollback artifact passed its original validation.
- [ ] Confirm that required backups or snapshots exist for stateful changes.
- [ ] Confirm that the rollback will not revert unrelated fixes or security updates.
- [ ] Obtain the required rollback approval according to the team's incident policy.

### Rollback execution

Perform only the steps applicable to the repository and deployment model:

- [ ] Disable the React implementation through the feature flag, if available.
- [ ] Stop or pause the active rollout.
- [ ] Revert only the migration commits, or redeploy the last known-good artifact.
- [ ] Restore the legacy mount markup, templates, and initialization code.
- [ ] Restore the legacy jQuery handlers, selectors, plugins, and required dependencies.
- [ ] Restore legacy CSS and assets required by the feature.
- [ ] Remove or disable the React entry point for the rolled-back feature.
- [ ] Confirm that React and legacy handlers are not both active.
- [ ] Restore compatible configuration and environment values when required.
- [ ] Execute the separately approved database or data rollback procedure when required.
- [ ] Clear or invalidate caches only when required and document the scope.
- [ ] Build the rollback version successfully.
- [ ] Deploy the rollback version using the standard deployment pipeline.
- [ ] Record each command, pipeline run, artifact, operator, timestamp, and result.
- [ ] Stop and escalate if an unexpected destructive change or compatibility issue appears.

### Post-rollback automated verification

- [ ] The deployment or rollback pipeline completed successfully.
- [ ] The expected legacy version is running in the target environment.
- [ ] Application startup and health checks pass.
- [ ] Type checking, linting, tests, and build checks pass when they are part of the rollback pipeline.
- [ ] Critical smoke tests pass.
- [ ] API request and response compatibility is restored.
- [ ] Authentication, authorization, and session behavior work as expected.
- [ ] Monitoring shows error rates returning to the accepted range.
- [ ] No duplicate event handlers or network requests are present.
- [ ] No new relevant browser-console errors or warnings are present.
- [ ] No data integrity issue was introduced or left unresolved by the rollback.

### Post-rollback manual verification

- [ ] The primary legacy user journey works end to end.
- [ ] Validation, loading, empty, success, and failure states work.
- [ ] Keyboard navigation and focus behavior work.
- [ ] Required CSS, layout, and responsive behavior are restored.
- [ ] Supported browsers have been checked according to project requirements.
- [ ] Analytics, logging, telemetry, and feature flags operate correctly.
- [ ] Adjacent features not included in the migration still work.
- [ ] Customer support or business representatives have verified the affected workflow when required.

### Closure and follow-up

- [ ] Stakeholders have been informed that rollback completed and service was verified.
- [ ] The incident timeline and rollback evidence are documented.
- [ ] The root cause is recorded or assigned for investigation.
- [ ] The failed migration remains disabled until corrective work is reviewed and verified.
- [ ] A new forward-fix plan includes tests that reproduce the regression.
- [ ] Monitoring or alerting gaps discovered during the incident have follow-up actions.
- [ ] Data repair or reconciliation work has an owner and due date, if applicable.
- [ ] Temporary operational changes have been reverted or documented.
- [ ] The definition-of-done checklist will be repeated before redeployment.
- [ ] Lessons learned are added to this skill or project instructions when reusable.

### Rollback completion report template

Use this format after executing a rollback:

```markdown
## Rollback completion report

**Feature:** `<feature name>`  
**Environment:** `<environment>`  
**Incident or change ID:** `<identifier>`  
**Rollback owner:** `<name or team>`  
**Rollback started:** `<date and time with timezone>`  
**Rollback completed:** `<date and time with timezone>`  
**Previous version:** `<version, commit, or artifact>`  
**Restored version:** `<version, commit, or artifact>`  
**Overall result:** Successful | Partially successful | Failed

### Rollback trigger

- `<observed issue and threshold>`

### User and business impact

- `<affected users, journeys, duration, and severity>`

### Evidence captured

- `<logs, metrics, traces, screenshots, or request samples>`

### Actions executed

- `<command, pipeline, feature-flag action, or deployment step>`: Passed | Failed

### Automated verification

- Health checks: Passed | Failed | Not run
- Smoke tests: Passed | Failed | Not run
- API compatibility: Passed | Failed | Not run
- Monitoring recovery: Passed | Failed | Not run
- Data integrity: Passed | Failed | Not run

### Manual verification

- Primary user journey: Passed | Failed | Not run
- Validation and error recovery: Passed | Failed | Not run
- Keyboard and focus behavior: Passed | Failed | Not run
- Visual and responsive behavior: Passed | Failed | Not run
- Adjacent features: Passed | Failed | Not run

### Remaining risks

- `<risk or "None identified">`

### Data repair or reconciliation

- `<required action, owner, due date, or "Not required">`

### Follow-up actions

- `<action>`: `<owner>` by `<date>`

### Approval and closure

- Engineering: Approved | Pending
- QA: Approved | Pending
- Operations: Approved | Pending
- Product or business owner: Approved | Pending
- Incident closed: Yes | No
```

### Sample rollback completion report

```markdown
## Rollback completion report

**Feature:** Customer search  
**Environment:** Production  
**Incident or change ID:** INC-1042  
**Rollback owner:** Frontend Platform Team  
**Rollback started:** 2026-10-01 14:20 IST  
**Rollback completed:** 2026-10-01 14:34 IST  
**Previous version:** `web-2026.10.01.3`  
**Restored version:** `web-2026.09.30.7`  
**Overall result:** Successful

### Rollback trigger

- Customer-search requests returned HTTP 401 for signed-in users because the
  migrated fetch request did not preserve the legacy credential behavior.

### User and business impact

- Customer search failed for signed-in users for 18 minutes.
- No customer data was created, modified, or deleted.

### Evidence captured

- Browser network trace showing HTTP 401 responses.
- Frontend error-rate dashboard for the customer-search route.
- Deployment and application logs for both artifact versions.

### Actions executed

- Disabled the `react-customer-search` feature flag: Passed
- Redeployed artifact `web-2026.09.30.7`: Passed
- Restored the legacy customer-search smoke-test route: Passed

### Automated verification

- Health checks: Passed
- Smoke tests: Passed
- API compatibility: Passed
- Monitoring recovery: Passed
- Data integrity: Passed

### Manual verification

- Primary user journey: Passed
- Validation and error recovery: Passed
- Keyboard and focus behavior: Passed
- Visual and responsive behavior: Passed
- Adjacent features: Passed

### Remaining risks

- The React migration remains disabled until credential behavior is corrected
  and covered by an integration test.

### Data repair or reconciliation

- Not required. The feature performs read-only customer searches.

### Follow-up actions

- Add credential-preservation integration test: Frontend Platform Team by 2026-10-03
- Correct fetch credential configuration: Frontend Platform Team by 2026-10-03
- Repeat staging parity verification: QA Team by 2026-10-04

### Approval and closure

- Engineering: Approved
- QA: Approved
- Operations: Approved
- Product or business owner: Approved
- Incident closed: Yes
```

The sample is illustrative. Use actual incidents, commands, versions, timestamps,
results, impact, and approvals. Never report a successful rollback until the
restored feature and adjacent critical functionality have been verified.


