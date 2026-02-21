# KitchenWithMe MVP Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Build a Web MVP that validates "companionship-first cooking" with cooking-session voice guidance, post-cooking reflection, and quick recipe selection.

**Architecture:** Use a thin vertical slice architecture with a single web app, a lightweight backend API, and a realtime voice gateway. Start with domain-limited intent routing (Plan B), while preserving interface contracts so the system can upgrade to open-ended realtime voice (Plan A) without redesigning UI and state machine.

**Tech Stack:** TypeScript, React (web), Node.js API, WebSocket/WebRTC realtime channel, PostgreSQL (or SQLite for local MVP), Playwright/Cypress for E2E, Vitest/Jest for unit tests.

---

### Task 1: Scaffold baseline project structure

**Files:**
- Create: `apps/web/src/main.tsx`
- Create: `apps/web/src/App.tsx`
- Create: `apps/api/src/index.ts`
- Create: `packages/shared/src/types.ts`
- Create: `pnpm-workspace.yaml`

**Step 1: Write the failing test**

```ts
import { describe, it, expect } from 'vitest';
import { appBootStatus } from '../src/bootstrap';

describe('bootstrap', () => {
  it('returns ready when web app bootstraps', () => {
    expect(appBootStatus()).toBe('ready');
  });
});
```

**Step 2: Run test to verify it fails**

Run: `pnpm vitest apps/web/tests/bootstrap.test.ts -r`
Expected: FAIL with module/function not found

**Step 3: Write minimal implementation**

```ts
export function appBootStatus() {
  return 'ready';
}
```

**Step 4: Run test to verify it passes**

Run: `pnpm vitest apps/web/tests/bootstrap.test.ts -r`
Expected: PASS

**Step 5: Commit**

```bash
git add apps/web apps/api packages/shared pnpm-workspace.yaml
git commit -m "chore: scaffold mvp workspace baseline"
```

### Task 2: Implement recipe quick-pick entry flow

**Files:**
- Create: `apps/web/src/features/start/RecipePicker.tsx`
- Create: `apps/web/src/features/start/useRecipeList.ts`
- Test: `apps/web/src/features/start/RecipePicker.test.tsx`

**Step 1: Write the failing test**

```ts
it('lets user select an existing recipe and enter session', async () => {
  render(<RecipePicker recipes={[{ id: 'r1', name: '番茄鸡蛋面' }]} />);
  await user.click(screen.getByText('番茄鸡蛋面'));
  expect(screen.getByRole('button', { name: '开始陪我做饭' })).toBeEnabled();
});
```

**Step 2: Run test to verify it fails**

Run: `pnpm vitest apps/web/src/features/start/RecipePicker.test.tsx -r`
Expected: FAIL with missing component/behavior

**Step 3: Write minimal implementation**

```tsx
export function RecipePicker({ recipes }: { recipes: { id: string; name: string }[] }) {
  // minimal selectable list + enter button
  return null;
}
```

**Step 4: Run test to verify it passes**

Run: `pnpm vitest apps/web/src/features/start/RecipePicker.test.tsx -r`
Expected: PASS

**Step 5: Commit**

```bash
git add apps/web/src/features/start
git commit -m "feat: add quick recipe pick start flow"
```

### Task 3: Implement cooking session state machine

**Files:**
- Create: `apps/web/src/features/cooking/sessionMachine.ts`
- Create: `apps/web/src/features/cooking/sessionMachine.types.ts`
- Test: `apps/web/src/features/cooking/sessionMachine.test.ts`

**Step 1: Write the failing test**

```ts
it('transitions from listening to speaking to listening', () => {
  const state = createSessionMachine();
  state.dispatch({ type: 'USER_SPEECH_DETECTED' });
  state.dispatch({ type: 'ASSISTANT_REPLY_STARTED' });
  state.dispatch({ type: 'ASSISTANT_REPLY_FINISHED' });
  expect(state.value.mode).toBe('listening');
});
```

**Step 2: Run test to verify it fails**

Run: `pnpm vitest apps/web/src/features/cooking/sessionMachine.test.ts -r`
Expected: FAIL with missing state machine implementation

**Step 3: Write minimal implementation**

```ts
export function createSessionMachine() {
  // minimal deterministic transitions for MVP states
}
```

**Step 4: Run test to verify it passes**

Run: `pnpm vitest apps/web/src/features/cooking/sessionMachine.test.ts -r`
Expected: PASS

**Step 5: Commit**

```bash
git add apps/web/src/features/cooking/sessionMachine*
git commit -m "feat: add cooking session state machine"
```

### Task 4: Implement domain-limited intent router (Plan B)

**Files:**
- Create: `apps/api/src/intent/router.ts`
- Create: `apps/api/src/intent/catalog.ts`
- Test: `apps/api/src/intent/router.test.ts`

**Step 1: Write the failing test**

```ts
it('routes "下一步" to NEXT_STEP intent', () => {
  expect(routeIntent('下一步')).toEqual({ intent: 'NEXT_STEP' });
});
```

**Step 2: Run test to verify it fails**

Run: `pnpm vitest apps/api/src/intent/router.test.ts -r`
Expected: FAIL with missing intent mapping

**Step 3: Write minimal implementation**

```ts
const rules = [{ pattern: /下一步/, intent: 'NEXT_STEP' }];
export function routeIntent(text: string) {
  // rule-based route for MVP
}
```

**Step 4: Run test to verify it passes**

Run: `pnpm vitest apps/api/src/intent/router.test.ts -r`
Expected: PASS

**Step 5: Commit**

```bash
git add apps/api/src/intent
git commit -m "feat: add domain-limited intent router"
```

### Task 5: Add realtime voice gateway contract

**Files:**
- Create: `apps/api/src/realtime/wsSession.ts`
- Create: `packages/shared/src/realtimeEvents.ts`
- Test: `apps/api/src/realtime/wsSession.test.ts`

**Step 1: Write the failing test**

```ts
it('emits transcript and assistant_reply events in order', async () => {
  const events = await simulateVoiceTurn('火候怎么样');
  expect(events.map((e) => e.type)).toEqual(['transcript', 'assistant_reply']);
});
```

**Step 2: Run test to verify it fails**

Run: `pnpm vitest apps/api/src/realtime/wsSession.test.ts -r`
Expected: FAIL with missing ws session handler

**Step 3: Write minimal implementation**

```ts
export function createWsSession() {
  // stream input audio/text and publish normalized events
}
```

**Step 4: Run test to verify it passes**

Run: `pnpm vitest apps/api/src/realtime/wsSession.test.ts -r`
Expected: PASS

**Step 5: Commit**

```bash
git add apps/api/src/realtime packages/shared/src/realtimeEvents.ts
git commit -m "feat: add realtime voice event contract"
```

### Task 6: Build cooking UI with voice states

**Files:**
- Create: `apps/web/src/features/cooking/CookingSessionPage.tsx`
- Create: `apps/web/src/features/cooking/VoiceOrb.tsx`
- Test: `apps/web/src/features/cooking/CookingSessionPage.test.tsx`

**Step 1: Write the failing test**

```ts
it('shows listening, speaking and interrupted states', async () => {
  render(<CookingSessionPage />);
  expect(screen.getByTestId('voice-state')).toHaveTextContent('listening');
});
```

**Step 2: Run test to verify it fails**

Run: `pnpm vitest apps/web/src/features/cooking/CookingSessionPage.test.tsx -r`
Expected: FAIL with missing page/states

**Step 3: Write minimal implementation**

```tsx
export function CookingSessionPage() {
  // bind session state machine to voice orb UI
  return null;
}
```

**Step 4: Run test to verify it passes**

Run: `pnpm vitest apps/web/src/features/cooking/CookingSessionPage.test.tsx -r`
Expected: PASS

**Step 5: Commit**

```bash
git add apps/web/src/features/cooking
git commit -m "feat: add cooking session ui states"
```

### Task 7: Implement reflection capture and storage

**Files:**
- Create: `apps/web/src/features/reflection/ReflectionForm.tsx`
- Create: `apps/api/src/reflections/reflection.controller.ts`
- Test: `apps/web/src/features/reflection/ReflectionForm.test.tsx`
- Test: `apps/api/src/reflections/reflection.controller.test.ts`

**Step 1: Write the failing test**

```ts
it('submits taste and mood notes under 30 seconds flow', async () => {
  render(<ReflectionForm sessionId="s1" />);
  await user.type(screen.getByLabelText('味道'), '清淡，暖胃');
  await user.type(screen.getByLabelText('心情'), '放松');
  await user.click(screen.getByRole('button', { name: '轻轻记下' }));
  expect(await screen.findByText('已收好这次味道')).toBeInTheDocument();
});
```

**Step 2: Run test to verify it fails**

Run: `pnpm vitest apps/web/src/features/reflection/ReflectionForm.test.tsx -r`
Expected: FAIL with missing form/submit flow

**Step 3: Write minimal implementation**

```tsx
export function ReflectionForm({ sessionId }: { sessionId: string }) {
  // minimal two-field submit for MVP
  return null;
}
```

**Step 4: Run test to verify it passes**

Run: `pnpm vitest apps/web/src/features/reflection/ReflectionForm.test.tsx -r`
Expected: PASS

**Step 5: Commit**

```bash
git add apps/web/src/features/reflection apps/api/src/reflections
git commit -m "feat: add post-cooking reflection capture"
```

### Task 8: Add edge-state fallback paths

**Files:**
- Create: `apps/web/src/features/edge/EdgeStatePanel.tsx`
- Modify: `apps/web/src/features/cooking/CookingSessionPage.tsx`
- Test: `apps/web/src/features/edge/EdgeStatePanel.test.tsx`

**Step 1: Write the failing test**

```ts
it('shows warm retry copy when mic permission denied', () => {
  render(<EdgeStatePanel state="MIC_DENIED" />);
  expect(screen.getByText('我们先用点按继续，也可以稍后再打开麦克风')).toBeInTheDocument();
});
```

**Step 2: Run test to verify it fails**

Run: `pnpm vitest apps/web/src/features/edge/EdgeStatePanel.test.tsx -r`
Expected: FAIL with missing panel/copy

**Step 3: Write minimal implementation**

```tsx
export function EdgeStatePanel({ state }: { state: string }) {
  // render fallback copy for known edge states
  return null;
}
```

**Step 4: Run test to verify it passes**

Run: `pnpm vitest apps/web/src/features/edge/EdgeStatePanel.test.tsx -r`
Expected: PASS

**Step 5: Commit**

```bash
git add apps/web/src/features/edge apps/web/src/features/cooking/CookingSessionPage.tsx
git commit -m "feat: add warm fallback edge states"
```

### Task 9: Wire analytics for MVP success metrics

**Files:**
- Create: `packages/shared/src/analyticsEvents.ts`
- Modify: `apps/web/src/features/cooking/CookingSessionPage.tsx`
- Modify: `apps/web/src/features/reflection/ReflectionForm.tsx`
- Test: `apps/web/src/analytics/analyticsEvents.test.ts`

**Step 1: Write the failing test**

```ts
it('tracks session completion and reflection saved events', () => {
  const events = collectAnalyticsSimulation();
  expect(events).toContain('session_completed');
  expect(events).toContain('reflection_saved');
});
```

**Step 2: Run test to verify it fails**

Run: `pnpm vitest apps/web/src/analytics/analyticsEvents.test.ts -r`
Expected: FAIL with missing analytics hooks

**Step 3: Write minimal implementation**

```ts
export const MVP_EVENTS = ['session_started', 'session_completed', 'reflection_saved'] as const;
```

**Step 4: Run test to verify it passes**

Run: `pnpm vitest apps/web/src/analytics/analyticsEvents.test.ts -r`
Expected: PASS

**Step 5: Commit**

```bash
git add packages/shared/src/analyticsEvents.ts apps/web/src/features/cooking/CookingSessionPage.tsx apps/web/src/features/reflection/ReflectionForm.tsx
git commit -m "feat: track mvp companionship metrics"
```

### Task 10: Add end-to-end acceptance suite

**Files:**
- Create: `apps/web/e2e/mvp-main-flow.spec.ts`
- Create: `apps/web/e2e/mvp-edge-states.spec.ts`
- Modify: `apps/web/playwright.config.ts`

**Step 1: Write the failing test**

```ts
test('main flow: select recipe -> cooking -> reflection', async ({ page }) => {
  await page.goto('/');
  await page.getByText('番茄鸡蛋面').click();
  await page.getByRole('button', { name: '开始陪我做饭' }).click();
  await expect(page.getByText('已收好这次味道')).toBeVisible();
});
```

**Step 2: Run test to verify it fails**

Run: `pnpm playwright test apps/web/e2e/mvp-main-flow.spec.ts`
Expected: FAIL before full feature wiring

**Step 3: Write minimal implementation**

```ts
// complete missing route wiring/selectors to satisfy E2E contract
```

**Step 4: Run test to verify it passes**

Run: `pnpm playwright test apps/web/e2e/mvp-main-flow.spec.ts apps/web/e2e/mvp-edge-states.spec.ts`
Expected: PASS

**Step 5: Commit**

```bash
git add apps/web/e2e apps/web/playwright.config.ts
git commit -m "test: add mvp e2e acceptance suite"
```

### Task 11: Final verification and release readiness docs

**Files:**
- Modify: `docs/prd/core/2026-02-21-kitchenwithme-mvp-prd.md`
- Modify: `docs/prd/handoff/2026-02-21-kitchenwithme-mvp-engineering-handoff.md`
- Create: `docs/prd/release/2026-02-21-mvp-go-no-go.md`

**Step 1: Write the failing test**

```md
Checklist should fail if any P0 release gate is unchecked.
```

**Step 2: Run test to verify it fails**

Run: `pnpm vitest apps/web/src/release/releaseGate.test.ts -r`
Expected: FAIL until all gates are represented

**Step 3: Write minimal implementation**

```ts
export function evaluateReleaseGate(input: GateInput): 'GO' | 'NO_GO' {
  return input.p0 && input.edge && input.metrics ? 'GO' : 'NO_GO';
}
```

**Step 4: Run test to verify it passes**

Run: `pnpm vitest apps/web/src/release/releaseGate.test.ts -r`
Expected: PASS

**Step 5: Commit**

```bash
git add docs/prd/core/2026-02-21-kitchenwithme-mvp-prd.md docs/prd/handoff/2026-02-21-kitchenwithme-mvp-engineering-handoff.md docs/prd/release/2026-02-21-mvp-go-no-go.md
git commit -m "docs: add mvp go-no-go and release verification"
```
