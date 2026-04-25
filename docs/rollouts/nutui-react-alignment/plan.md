# NutUI React Alignment Implementation Plan

This is a planning package only. Do not generate `rollout.py` yet. The rollout contains API compatibility decisions, platform feasibility checks, and component prioritization that should be reviewed before unattended execution.

The YAML block is still kept complete so a runner can be generated later after the open decisions are resolved.

<!-- rollout-plan:start -->
```yaml
rollout:
  name: "nutui-react-alignment"
  repo_root: "/home/afu/dev/nutui-react-native"
  workdir: ".codex-rollout/nutui-react-alignment"
  codex_cmd: null
  model: null
  max_fix_attempts: 1
  allow_dirty: false
  commit_per_batch: false
  sources_of_truth:
    - "docs/rollouts/nutui-react-alignment/spec.md"
    - "docs/rollouts/nutui-react-alignment/plan.md"
    - "docs/rollouts/nutui-react-alignment/alignment-pipelines.md"
    - "docs/rollouts/nutui-react-alignment/matrix.md"
    - "docs/rollouts/nutui-react-alignment/api-compatibility.md"
    - "docs/rollouts/nutui-react-alignment/token-strategy.md"
    - "docs/rollouts/nutui-react-alignment/component-report-template.md"
    - ".agents/skills/spec-plan-rollout/SKILL.md"
    - "sites/config.json"
    - "components/index.ts"
    - "demo/demoList.ts"
    - "../nutui-react/src/config.json"
    - "../nutui-react/src/packages"
  planning_notes:
    - "Current RN package has 66 component directories excluding hooks, utils, and portal; target has 106 package directories."
    - "Current config has 93 package entries; target config has 105 package entries."
    - "Current repo has zero component tests under components; target package tree has 135 test files."
    - "Current tsconfig only includes Vite docs files, so component type coverage must be introduced carefully."
    - "Alignment target is API, visual language, tokens, and documentation parity as far as RN allows."
    - "Implementation work should be one component per coding task after the matrix chooses the queue."
    - "Target repo is read-only and must be used as reference evidence only."
  success_metrics:
    - "Every target package is classified as complete, partial, missing, config-only, risky, unsupported, or renamed-compatible."
    - "Each changed component has a component report covering props, missing props, RN substitutes, tokens, migration advice, and tests."
    - "P0 components consume component-local or shared token modules for visual alignment where practical."
    - "No visible config entry points to missing implementation, docs, demo, or export."
    - "Newly aligned or newly added components include focused verification."
    - "yarn lint, yarn typecheck, yarn test --passWithNoTests, and yarn prepack pass at phase gates."
  global_context:
    - "This repository is a React Native and Expo package using Yarn 1, Node 16.18.1, components/<name>/, and react-native-builder-bob."
    - "The target sibling repo is NutUI React 3.x with H5 and Taro implementations under src/packages."
    - "React Native platform correctness wins over direct DOM, SCSS, or Taro parity."
    - "Props are aligned before internals; RN implementation uses RN primitives such as Pressable, Text, View, Modal, Animated, and platform APIs."
    - "The directory name commutiy is canonical in this repo and must not be corrected casually."
  hard_rules:
    - "Do not edit ../nutui-react."
    - "Do not migrate package manager or Node runtime without a dedicated approved ADR."
    - "Do not remove existing RN public exports without a compatibility bridge and migration note."
    - "Do not mark a component show: true until implementation, docs, demo, exports, and verification are ready."
    - "Do not introduce DOM, className, CSS, SCSS, styled-components, or CSS runtime as the RN implementation path."
    - "Keep implementation changes to one component per coding task unless the batch is analysis or tooling only."
    - "Keep changes scoped to the current batch and preserve unrelated user changes."
    - "Use RN style helpers and style-splitting utilities already present in components/utils."
  batch_prompt_suffix:
    - "Finish only this batch and anything strictly required to make it pass."
    - "Capture intentional platform differences from NutUI React in docs or the alignment report."
phases:
  - id: "00-baseline-contract"
    title: "Baseline And Contract"
    goal: "Turn the current drift into explicit, reviewable evidence and decisions."
    depends_on: []
    summary: "Create the inventory and compatibility contract before changing component behavior."
    entry_criteria:
      - "Both repositories are available locally."
      - "The target repository is treated as read-only."
    exit_criteria:
      - "A repeatable parity inventory exists."
      - "Each target component has an initial status."
      - "API compatibility rules are documented."
    risks:
      - "The target API may be interpreted too literally for RN."
      - "Existing config entries may be mistaken for implemented components."
    batches:
      - id: "00-01-inventory-command"
        title: "Inventory And Scan Commands"
        kind: "analysis"
        execution: "codex"
        goal: "Create repeatable scans for catalog, props, tokens, docs, demos, tests, and exports."
        depends_on: []
        deliverables:
          - "Add tools/align/scan-nutui-react.ts, tools/align/scan-rn.ts, tools/align/diff-props.ts, tools/align/extract-scss-vars.ts, tools/align/generate-token.ts, and tools/align/generate-report.ts, or JS equivalents if TS tooling is not ready."
          - "Add scripts/check-nutui-react-alignment.js as a stable wrapper if needed."
          - "Generate docs/rollouts/nutui-react-alignment/inventory.md."
          - "Report component dirs, config entries, props, SCSS token sources, docs, demos, tests, exports, and missing target packages."
        acceptance:
          - "The report separates implemented, config-only, target-missing, RN-only, and renamed-compatible candidates."
          - "The scan output can feed matrix.md and per-component reports."
          - "The command accepts a target repo path and fails clearly if required files are missing."
        evidence_to_capture:
          - "Inventory command output committed in inventory.md."
        verify_commands:
          - "node scripts/check-nutui-react-alignment.js --target ../nutui-react --markdown docs/rollouts/nutui-react-alignment/inventory.md"
        files_to_touch:
          - "tools/align/**"
          - "scripts/check-nutui-react-alignment.js"
          - "docs/rollouts/nutui-react-alignment/inventory.md"
        prompt_context:
          - "Use sites/config.json and ../nutui-react/src/config.json as config sources."
          - "Do not infer implementation from config alone."
      - id: "00-02-api-compatibility-adr"
        title: "API Compatibility ADR And Report Template"
        kind: "docs"
        execution: "codex"
        goal: "Document how NutUI React APIs should map to React Native APIs and how every component report is written."
        depends_on:
          - "00-01-inventory-command"
        deliverables:
          - "Add docs/rollouts/nutui-react-alignment/api-compatibility.md."
          - "Maintain docs/rollouts/nutui-react-alignment/component-report-template.md."
          - "Create or update docs/rollouts/nutui-react-alignment/components/button.md as the first report."
          - "Define rules for events, style props, className, icons, refs, portal behavior, accessibility, and unsupported features."
        acceptance:
          - "The ADR distinguishes additive target-compatible props from RN-only platform props."
          - "The report template includes target props, current RN props, missing props, unsupported props, token gaps, migration plan, and tests."
          - "The ADR calls out unresolved decisions that block advanced components."
        evidence_to_capture:
          - "ADR reviewed against Button, Popup, Picker-like, and media-like examples."
        verify_commands:
          - "test -f docs/rollouts/nutui-react-alignment/api-compatibility.md"
          - "test -f docs/rollouts/nutui-react-alignment/component-report-template.md"
          - "test -f docs/rollouts/nutui-react-alignment/components/button.md"
        files_to_touch:
          - "docs/rollouts/nutui-react-alignment/api-compatibility.md"
          - "docs/rollouts/nutui-react-alignment/component-report-template.md"
          - "docs/rollouts/nutui-react-alignment/components/**"
        prompt_context:
          - "Do not promise DOM or Taro-only behavior in RN docs."
      - id: "00-03-parity-matrix"
        title: "Parity Matrix"
        kind: "docs"
        execution: "codex"
        goal: "Create the authoritative component matrix and first P0/P1/P2 sequencing decision."
        depends_on:
          - "00-01-inventory-command"
          - "00-02-api-compatibility-adr"
        deliverables:
          - "Maintain docs/rollouts/nutui-react-alignment/matrix.md."
          - "Classify all target packages by API status, style status, dependency level, risk, priority, and phase."
        acceptance:
          - "All 106 target package directories are represented."
          - "P0, P1, and P2 queues are explicit."
          - "RN-only entries such as sidenavbaritem, subsidenavbar, and timepannel have compatibility decisions."
        evidence_to_capture:
          - "Matrix generated or checked from inventory data."
        verify_commands:
          - "test -f docs/rollouts/nutui-react-alignment/matrix.md"
        files_to_touch:
          - "docs/rollouts/nutui-react-alignment/matrix.md"
        prompt_context:
          - "Use dependency order, not target nav order, when choosing implementation priority."
      - id: "00-04-token-strategy"
        title: "Token Strategy"
        kind: "docs"
        execution: "codex"
        goal: "Document how NutUI React SCSS variables become RN-safe TypeScript tokens."
        depends_on:
          - "00-01-inventory-command"
        deliverables:
          - "Maintain docs/rollouts/nutui-react-alignment/token-strategy.md."
          - "Define component-local tokens, optional shared tokens, unsupportedTokens, and the scaling decision path."
        acceptance:
          - "The strategy forbids CSS runtime and direct SCSS copying."
          - "The strategy names the Button seed tokens and the unresolved pt/px scaling decision."
        evidence_to_capture:
          - "Token strategy reviewed against Button SCSS variables."
        verify_commands:
          - "test -f docs/rollouts/nutui-react-alignment/token-strategy.md"
        files_to_touch:
          - "docs/rollouts/nutui-react-alignment/token-strategy.md"
        prompt_context:
          - "Keep tokens RN-safe and document CSS-only features instead of copying them."
      - id: "00-05-verification-baseline"
        title: "Verification Baseline"
        kind: "analysis"
        execution: "codex"
        goal: "Record current verification behavior and define the safe path to component type and test coverage."
        depends_on:
          - "00-01-inventory-command"
          - "00-03-parity-matrix"
        deliverables:
          - "Add docs/rollouts/nutui-react-alignment/verification-baseline.md."
          - "Document current results for yarn lint, yarn typecheck, yarn test --passWithNoTests, yarn prepack, and docs generation."
          - "Propose a dedicated component typecheck config before broadening root tsconfig."
        acceptance:
          - "Known failures or blind spots are documented without hiding them."
          - "The next phase has clear verification entry criteria."
        evidence_to_capture:
          - "Command outputs summarized with date and environment."
        verify_commands:
          - "test -f docs/rollouts/nutui-react-alignment/verification-baseline.md"
        files_to_touch:
          - "docs/rollouts/nutui-react-alignment/verification-baseline.md"
        prompt_context:
          - "Do not make broad typecheck changes in this batch."

  - id: "01-foundation"
    title: "Foundation"
    goal: "Create shared tooling and conventions so component work does not drift again."
    depends_on:
      - "00-baseline-contract"
    summary: "Tighten the repo mechanics before bulk component alignment."
    entry_criteria:
      - "Matrix, API compatibility ADR, component report template, and token strategy exist."
      - "Verification baseline is documented."
    exit_criteria:
      - "Registry, docs, demo, export, and type/test checks can catch common drift."
      - "Token extraction and report generation can support one-component tasks."
      - "Shared utilities needed by early component batches are available."
    risks:
      - "Turning on component typecheck may reveal pre-existing debt."
      - "Generator changes may affect docs and package imports at the same time."
    batches:
      - id: "01-01-component-typecheck-tests"
        title: "Component Typecheck And Test Scaffold"
        kind: "code"
        execution: "codex"
        goal: "Add a safe component verification lane without destabilizing the existing root typecheck."
        depends_on: []
        deliverables:
          - "Add a dedicated component tsconfig or scoped typecheck command."
          - "Add Jest helpers for ConfigProvider-wrapped component tests."
          - "Add one low-risk smoke test proving the test harness."
        acceptance:
          - "Existing yarn typecheck remains available."
          - "The new component check can be run independently."
        evidence_to_capture:
          - "New typecheck and smoke test output."
        verify_commands:
          - "yarn test --passWithNoTests"
          - "yarn typecheck"
        files_to_touch:
          - "package.json"
          - "tsconfig*.json"
          - "components/**/__tests__/**"
        prompt_context:
          - "Avoid fixing unrelated component type debt unless required by the new smoke path."
      - id: "01-02-registry-export-checks"
        title: "Registry And Export Checks"
        kind: "code"
        execution: "codex"
        goal: "Prevent config, exports, docs, and demo registry from drifting silently."
        depends_on:
          - "01-01-component-typecheck-tests"
        deliverables:
          - "Extend or add scripts that validate sites/config.json against components directories."
          - "Check components/index.ts exports for visible and exportEmpty components."
          - "Check demo/demoList.ts coverage for visible demoable components."
        acceptance:
          - "The check reports missing dirs, docs, demos, and exports with actionable messages."
          - "Known intentional gaps are represented by explicit allowlist or status metadata."
        evidence_to_capture:
          - "Registry check output."
        verify_commands:
          - "node scripts/check-nutui-react-alignment.js --target ../nutui-react"
          - "npm run generate:docs"
        files_to_touch:
          - "scripts/**"
          - "sites/config.json"
          - "components/index.ts"
          - "demo/demoList.ts"
        prompt_context:
          - "Do not make incomplete components visible just to satisfy the check."
      - id: "01-03-token-extraction-tooling"
        title: "Token Extraction Tooling"
        kind: "code"
        execution: "codex"
        goal: "Create the token extraction path from NutUI React SCSS variables to RN-safe TypeScript tokens."
        depends_on:
          - "01-01-component-typecheck-tests"
        deliverables:
          - "Implement or wire tools/align/extract-scss-vars.ts and tools/align/generate-token.ts."
          - "Generate a Button token draft or fixture without changing Button visuals yet."
          - "Document unsupported CSS features in token output."
        acceptance:
          - "The token tool can extract Button-related variables from ../nutui-react."
          - "The output uses numbers for px values and structured unsupportedTokens for CSS-only behavior."
          - "No CSS runtime, SCSS runtime, or styled-components dependency is introduced."
        evidence_to_capture:
          - "Token extraction output for Button."
        verify_commands:
          - "node scripts/check-nutui-react-alignment.js --target ../nutui-react"
          - "yarn typecheck"
        files_to_touch:
          - "tools/align/**"
          - "scripts/**"
          - "components/button/tokens.ts"
          - "components/tokens/**"
        prompt_context:
          - "If TS execution is not available for tools yet, add a JS wrapper or defer executable wiring but keep output shape documented."
      - id: "01-04-shared-utils-hooks-locales"
        title: "Shared Utilities Hooks And Locales"
        kind: "code"
        execution: "codex"
        goal: "Port or adapt low-risk target utilities needed by upcoming components."
        depends_on:
          - "01-01-component-typecheck-tests"
          - "01-03-token-extraction-tooling"
        deliverables:
          - "Compare current components/utils and components/hooks with target src/utils and src/hooks."
          - "Add RN-safe helpers such as merge, clamp, date, is-empty, use-props-value, and use-ref-state where needed."
          - "Expand locale strategy only for components scheduled in the next phases."
        acceptance:
          - "Helpers are RN-safe and covered by small tests when logic is non-trivial."
          - "No web-only helpers are exported as if they worked in RN."
        evidence_to_capture:
          - "Utility inventory notes in parity matrix or implementation docs."
        verify_commands:
          - "yarn test --passWithNoTests"
          - "yarn typecheck"
        files_to_touch:
          - "components/utils/**"
          - "components/hooks/**"
          - "components/configprovider/**"
        prompt_context:
          - "Prefer existing local helper patterns and avoid broad utility rewrites."
      - id: "01-05-doc-demo-scaffold"
        title: "Docs And Demo Scaffold"
        kind: "code"
        execution: "codex"
        goal: "Make docs and demo setup cheap and consistent for later component batches."
        depends_on:
          - "01-02-registry-export-checks"
          - "01-03-token-extraction-tooling"
        deliverables:
          - "Add or document a scaffold command/checklist for RN component docs and demos."
          - "Ensure scaffold output includes a component report placeholder and optional tokens.ts."
          - "Support community docs in zh-CN, zh-TW, and en-US."
          - "Decide and implement JD doc fallback or explicit missing-doc handling."
        acceptance:
          - "A new component can be scaffolded without missing canonical files."
          - "Docs generation fails clearly for visible components missing docs."
        evidence_to_capture:
          - "Scaffold/check output and docs generation output."
        verify_commands:
          - "npm run generate:docs"
          - "npm run generate:jd:docs"
        files_to_touch:
          - "scripts/**"
          - "components/**/commutiy/**"
          - "components/**/jd/**"
        prompt_context:
          - "Keep the canonical commutiy spelling."

  - id: "02-p0-component-alignment"
    title: "P0 Component Alignment"
    goal: "Align the highest-value existing components through one-component API, token, docs, and test tasks."
    depends_on:
      - "01-foundation"
    summary: "This phase proves the one-component workflow. Grouped lanes below must be expanded into one-component tasks before runner generation."
    entry_criteria:
      - "Component verification scaffold is available."
      - "API compatibility ADR exists."
      - "Registry checks can distinguish visible and hidden gaps."
    exit_criteria:
      - "P0 components have parity notes, token notes or token modules, updated docs, and focused tests for changed behavior."
      - "Current RN-only exports have compatibility decisions."
    risks:
      - "Existing users may rely on old RN props or defaults."
      - "Target visual changes may not map exactly to RN."
    batches:
      - id: "02-01-button-api-token-pilot"
        title: "Button API Token Pilot"
        kind: "code"
        execution: "codex"
        goal: "Align Button as the first one-component API, token, implementation, docs, and test pilot."
        depends_on: []
        deliverables:
          - "Use docs/rollouts/nutui-react-alignment/components/button.md as the source report."
          - "Add ButtonFill, xlarge/mini sizes, rightIcon, onClick compatibility, and textStyle if accepted by the ADR."
          - "Keep onPress as the RN primary event and guard onPress/onClick when disabled or loading."
          - "Generate components/button/tokens.ts from target Button variables before reshaping styles."
          - "Update Button docs, demo, and focused tests."
        acceptance:
          - "Existing import paths continue to work."
          - "Button does not use DOM, className, CSS, SCSS, or browser-only APIs."
          - "Visible Button docs and demo match implemented RN behavior."
        evidence_to_capture:
          - "Updated Button report, token output, and test output."
        verify_commands:
          - "yarn test --passWithNoTests"
          - "yarn typecheck"
          - "npm run generate:docs"
        files_to_touch:
          - "components/button/**"
          - "docs/rollouts/nutui-react-alignment/components/button.md"
        prompt_context:
          - "Prefer Pressable, Text, View, and ActivityIndicator if refactoring internals; keep behavior compatible."
          - "Map existing plain to fill='outline' unless fill is explicitly passed."
      - id: "02-02-p0-base-display-queue"
        title: "P0 Base And Display Queue"
        kind: "code"
        execution: "codex"
        goal: "Align one P0 base or display component selected from Cell, CellGroup, Icon, Image, Tag, Badge, Avatar, Divider, Progress, or Skeleton."
        depends_on:
          - "02-01-button-api-token-pilot"
        deliverables:
          - "Create or update the component report before code changes."
          - "Align props and token-backed styles for exactly one selected component."
          - "Update docs, demo, and tests for that component only."
        acceptance:
          - "The diff touches one component family plus shared tooling only when strictly required."
          - "The matrix row for the component is updated."
        evidence_to_capture:
          - "Component report, token notes, and test output."
        verify_commands:
          - "yarn test --passWithNoTests"
          - "yarn typecheck"
          - "npm run generate:docs"
        files_to_touch:
          - "components/cell/**"
          - "components/cellgroup/**"
          - "components/icon/**"
          - "components/image/**"
          - "components/tag/**"
          - "components/badge/**"
          - "components/avatar/**"
          - "components/divider/**"
          - "components/progress/**"
          - "components/skeleton/**"
          - "docs/rollouts/nutui-react-alignment/components/**"
        prompt_context:
          - "Before runner generation, duplicate this planning lane into one batch per selected component."
      - id: "02-03-p0-input-selection-queue"
        title: "P0 Input And Selection Queue"
        kind: "code"
        execution: "codex"
        goal: "Align one P0 input or selection component selected from Input, SearchBar, Switch, Checkbox, Radio, or Rate."
        depends_on:
          - "02-01-button-api-token-pilot"
        deliverables:
          - "Create or update the component report before code changes."
          - "Align target-compatible props, existing RN events, disabled state, controlled value behavior, and tokens for exactly one component."
          - "Update docs, demo, and tests for that component only."
        acceptance:
          - "Existing RN event names keep working."
          - "Target-compatible prop additions are additive and documented."
        evidence_to_capture:
          - "Data entry parity notes and test output."
        verify_commands:
          - "yarn test --passWithNoTests"
          - "yarn typecheck"
          - "npm run generate:docs"
        files_to_touch:
          - "components/checkbox/**"
          - "components/checkboxgroup/**"
          - "components/input/**"
          - "components/radio/**"
          - "components/radiogroup/**"
          - "components/rate/**"
          - "components/searchbar/**"
          - "components/switch/**"
          - "docs/rollouts/nutui-react-alignment/components/**"
        prompt_context:
          - "Before runner generation, duplicate this planning lane into one batch per selected component."
      - id: "02-04-p0-feedback-overlay-queue"
        title: "P0 Feedback And Overlay Queue"
        kind: "code"
        execution: "codex"
        goal: "Align one P0 feedback component selected from Toast, Dialog, Popup, or Overlay."
        depends_on:
          - "02-01-button-api-token-pilot"
        deliverables:
          - "Create or update the component report before code changes."
          - "Review portal, overlay, visibility, callback, and imperative API parity."
          - "Update docs with RootSiblingParent or portal requirements."
        acceptance:
          - "Overlay and portal behavior remains stable across demo and tests."
          - "Imperative APIs have documented cleanup behavior."
        evidence_to_capture:
          - "Feedback component test output."
        verify_commands:
          - "yarn test --passWithNoTests"
          - "yarn typecheck"
          - "npm run generate:docs"
        files_to_touch:
          - "components/dialog/**"
          - "components/overlay/**"
          - "components/popup/**"
          - "components/toast/**"
          - "docs/rollouts/nutui-react-alignment/components/**"
        prompt_context:
          - "Portal-style components depend on react-native-root-portal."
          - "Before runner generation, duplicate this planning lane into one batch per selected component."
      - id: "02-05-p1-p2-reclassification"
        title: "P1 And P2 Reclassification"
        kind: "analysis"
        execution: "codex"
        goal: "Move non-P0 existing components into P1 or P2 queues with explicit RN migration notes."
        depends_on:
          - "02-01-button-api-token-pilot"
        deliverables:
          - "Classify Calendar, Picker-like dependencies, Swiper, ActionSheet, Collapse, NoticeBar, Navbar, Tabbar, Pagination, InputNumber, Table, Virtuallist, Drag, BackTop, and animation/media components."
          - "Update matrix priority and component report placeholders."
          - "Document which components need RN redesign before code changes."
        acceptance:
          - "P1 and P2 queues are explicit and no high-risk component is treated as a simple copy task."
          - "TimePannel typo compatibility is resolved or documented in the matrix."
        evidence_to_capture:
          - "Updated matrix and component report placeholders."
        verify_commands:
          - "test -f docs/rollouts/nutui-react-alignment/matrix.md"
        files_to_touch:
          - "docs/rollouts/nutui-react-alignment/matrix.md"
          - "docs/rollouts/nutui-react-alignment/components/**"
        prompt_context:
          - "Do not implement P1 or P2 components in this batch."

  - id: "03-missing-foundation-components"
    title: "Missing Foundation Components"
    goal: "Add missing low-risk primitives and navigation foundations that unblock later work."
    depends_on:
      - "02-p0-component-alignment"
    summary: "Implement missing target components that are mostly composition, layout, or lightweight state."
    entry_criteria:
      - "Existing base components are aligned enough to compose new primitives."
      - "Scaffold and registry checks are available."
    exit_criteria:
      - "Foundation missing components are implemented, hidden or visible according to readiness, and covered by docs/demo/tests."
    risks:
      - "Scroll and sticky behavior may differ between native and web."
      - "Naming overlap between SideNavBar and Sidebar may confuse users."
    batches:
      - id: "03-01-layout-primitives"
        title: "Layout Primitives"
        kind: "code"
        execution: "codex"
        goal: "Implement Layout, SafeArea, Space, and Sticky for RN."
        depends_on: []
        deliverables:
          - "Add component directories with implementation, styles, docs, demos, exports, and config status."
          - "Use RN-safe layout and safe-area behavior."
        acceptance:
          - "Components work in Expo Web demo and do not require unsupported DOM APIs."
          - "Sticky limitations are documented if native parity is partial."
        evidence_to_capture:
          - "Layout primitive demos and tests."
        verify_commands:
          - "yarn test --passWithNoTests"
          - "yarn typecheck"
          - "npm run generate:docs"
        files_to_touch:
          - "components/layout/**"
          - "components/safearea/**"
          - "components/space/**"
          - "components/sticky/**"
          - "sites/config.json"
          - "components/index.ts"
          - "demo/demoList.ts"
        prompt_context:
          - "SafeArea may need react-native safe area primitives or documented fallback."
      - id: "03-02-tabs-sidebar-hoverbutton"
        title: "Tabs Sidebar And HoverButton"
        kind: "code"
        execution: "codex"
        goal: "Implement Tabs, TabPane, Sidebar, SidebarItem, HoverButton, and HoverButtonItem."
        depends_on:
          - "03-01-layout-primitives"
        deliverables:
          - "Add component directories and compatibility notes for existing SideNavBar family."
          - "Implement selection state, controlled/uncontrolled APIs, and demos."
        acceptance:
          - "Target names are available without breaking existing SideNavBar names."
          - "Docs clearly separate Sidebar from SideNavBar if both remain."
        evidence_to_capture:
          - "Navigation primitive tests and demo smoke notes."
        verify_commands:
          - "yarn test --passWithNoTests"
          - "yarn typecheck"
          - "npm run generate:docs"
        files_to_touch:
          - "components/tabs/**"
          - "components/tabpane/**"
          - "components/sidebar/**"
          - "components/sidebaritem/**"
          - "components/hoverbutton/**"
          - "components/hoverbuttonitem/**"
          - "sites/config.json"
          - "components/index.ts"
          - "demo/demoList.ts"
        prompt_context:
          - "Resolve naming compatibility in the ADR before changing public exports."
      - id: "03-03-lightweight-display-feedback"
        title: "Lightweight Display And Feedback"
        kind: "code"
        execution: "codex"
        goal: "Implement Loading, ResultPage, Segmented, Animate, AnimatingNumbers, and CircleProgress."
        depends_on:
          - "03-01-layout-primitives"
        deliverables:
          - "Add RN-safe versions of the target lightweight display and feedback components."
          - "Use existing animation helpers or RN Animated where appropriate."
        acceptance:
          - "Components have demos and tests for basic rendering and state."
          - "Animation limitations are documented."
        evidence_to_capture:
          - "Rendering and animation smoke test output."
        verify_commands:
          - "yarn test --passWithNoTests"
          - "yarn typecheck"
          - "npm run generate:docs"
        files_to_touch:
          - "components/loading/**"
          - "components/resultpage/**"
          - "components/segmented/**"
          - "components/animate/**"
          - "components/animatingnumbers/**"
          - "components/circleprogress/**"
          - "sites/config.json"
          - "components/index.ts"
          - "demo/demoList.ts"
        prompt_context:
          - "Avoid adding heavy animation dependencies without approval."

  - id: "04-data-entry-stack"
    title: "Data Entry Stack"
    goal: "Implement missing input-heavy components in dependency order."
    depends_on:
      - "03-missing-foundation-components"
    summary: "Picker and form primitives come before date, address, and upload workflows."
    entry_criteria:
      - "Shared value-control utilities are available."
      - "Existing data entry components have parity notes."
    exit_criteria:
      - "Missing data entry components are implemented or explicitly deferred for native dependency reasons."
    risks:
      - "Keyboard, popup, scroll, and native file APIs may need platform-specific handling."
      - "Complex components can become too large without shared primitives."
    batches:
      - id: "04-01-picker-stack"
        title: "Picker Stack"
        kind: "code"
        execution: "codex"
        goal: "Implement Picker and PickerView, then wire them for future date and address components."
        depends_on: []
        deliverables:
          - "Add picker primitives with controlled value behavior."
          - "Document differences from target scroll and Taro behavior."
        acceptance:
          - "Picker supports basic selection and confirm/cancel flows."
          - "Tests cover controlled and uncontrolled value paths."
        evidence_to_capture:
          - "Picker tests and docs generation output."
        verify_commands:
          - "yarn test --passWithNoTests"
          - "yarn typecheck"
          - "npm run generate:docs"
        files_to_touch:
          - "components/picker/**"
          - "components/pickerview/**"
          - "components/utils/**"
          - "components/index.ts"
          - "sites/config.json"
          - "demo/demoList.ts"
        prompt_context:
          - "Picker likely depends on Popup and shared value hooks."
      - id: "04-02-form-menu-range-keyboard"
        title: "Form Menu Range And Keyboard"
        kind: "code"
        execution: "codex"
        goal: "Implement Form, FormItem, Menu, MenuItem, Range, NumberKeyboard, ShortPassword, and Signature."
        depends_on:
          - "04-01-picker-stack"
        deliverables:
          - "Add components with docs, demos, exports, and tests."
          - "Document validation and keyboard differences from target."
        acceptance:
          - "Form and FormItem support a minimal validated workflow."
          - "Range, NumberKeyboard, ShortPassword, and Signature have RN-safe interaction models."
        evidence_to_capture:
          - "Data entry interaction test output."
        verify_commands:
          - "yarn test --passWithNoTests"
          - "yarn typecheck"
          - "npm run generate:docs"
        files_to_touch:
          - "components/form/**"
          - "components/formitem/**"
          - "components/menu/**"
          - "components/menuitem/**"
          - "components/range/**"
          - "components/numberkeyboard/**"
          - "components/shortpassword/**"
          - "components/signature/**"
          - "components/index.ts"
          - "sites/config.json"
          - "demo/demoList.ts"
        prompt_context:
          - "Use target async-validator behavior only if dependency and bundle impact are accepted."
      - id: "04-03-date-address-cascader"
        title: "Date Address And Cascader"
        kind: "code"
        execution: "codex"
        goal: "Implement DatePicker, DatePickerView, CalendarCard, Cascader, and Address."
        depends_on:
          - "04-01-picker-stack"
          - "04-02-form-menu-range-keyboard"
        deliverables:
          - "Add date and hierarchical selection components with demos and docs."
          - "Reuse Picker, Popup, Calendar, and Cascader utilities where possible."
        acceptance:
          - "Selection flows are covered by tests."
          - "Large data and locale behavior are documented."
        evidence_to_capture:
          - "Date/address component test output."
        verify_commands:
          - "yarn test --passWithNoTests"
          - "yarn typecheck"
          - "npm run generate:docs"
        files_to_touch:
          - "components/datepicker/**"
          - "components/datepickerview/**"
          - "components/calendarcard/**"
          - "components/cascader/**"
          - "components/address/**"
          - "components/index.ts"
          - "sites/config.json"
          - "demo/demoList.ts"
        prompt_context:
          - "Address depends on Cascader or Elevator-like rendering in the target repo."
      - id: "04-04-uploader"
        title: "Uploader"
        kind: "code"
        execution: "codex"
        goal: "Implement or explicitly defer Uploader based on RN file/media dependency strategy."
        depends_on:
          - "04-02-form-menu-range-keyboard"
        deliverables:
          - "Document dependency decision for file picking and upload."
          - "Implement minimal RN-safe Uploader if dependencies are approved."
          - "Keep component hidden if the dependency decision is unresolved."
        acceptance:
          - "No visible docs promise file APIs that are not implemented."
          - "Dependency choices are captured in the ADR."
        evidence_to_capture:
          - "Uploader decision and test output if implemented."
        verify_commands:
          - "yarn test --passWithNoTests"
          - "yarn typecheck"
          - "npm run generate:docs"
        files_to_touch:
          - "components/uploader/**"
          - "docs/rollouts/nutui-react-alignment/api-compatibility.md"
          - "package.json"
          - "sites/config.json"
          - "demo/demoList.ts"
        prompt_context:
          - "Do not add native dependencies silently."

  - id: "05-feedback-media-business"
    title: "Feedback Media And Business"
    goal: "Implement higher-risk components with explicit dependency and platform decisions."
    depends_on:
      - "04-data-entry-stack"
    summary: "Portal-heavy, media-heavy, and business widgets come after foundations."
    entry_criteria:
      - "Portal, popup, layout, picker, and data entry foundations are stable."
      - "Dependency strategy for native modules is documented."
    exit_criteria:
      - "Advanced missing components are implemented or intentionally deferred with public docs."
    risks:
      - "Media and cropper APIs may require native modules that are not acceptable as bundled dependencies."
      - "Tour and popover positioning may be hard to verify across RN platforms."
    batches:
      - id: "05-01-popover-loading-scroll"
        title: "Popover InfiniteLoading PullToRefresh"
        kind: "code"
        execution: "codex"
        goal: "Implement Popover, InfiniteLoading, and PullToRefresh."
        depends_on: []
        deliverables:
          - "Add portal/positioning and scroll interaction components."
          - "Document platform limitations for measurement and nested scroll views."
        acceptance:
          - "Basic positioning and callbacks are tested."
          - "Scroll behaviors have Expo Web demos."
        evidence_to_capture:
          - "Feedback advanced test output."
        verify_commands:
          - "yarn test --passWithNoTests"
          - "yarn typecheck"
          - "npm run generate:docs"
        files_to_touch:
          - "components/popover/**"
          - "components/infiniteloading/**"
          - "components/pulltorefresh/**"
          - "components/index.ts"
          - "sites/config.json"
          - "demo/demoList.ts"
        prompt_context:
          - "Use existing portal and client-rect utilities where possible."
      - id: "05-02-media-preview-lottie"
        title: "Media Preview And Lottie"
        kind: "code"
        execution: "codex"
        goal: "Implement ImagePreview, Audio, Video, and Lottie or document deferred dependency decisions."
        depends_on:
          - "05-01-popover-loading-scroll"
        deliverables:
          - "Add components only when dependency strategy is approved."
          - "Use optional peer dependency notes for native media or animation packages."
        acceptance:
          - "Components degrade safely when optional capabilities are unavailable."
          - "Docs list required setup clearly."
        evidence_to_capture:
          - "Dependency decision and media smoke test notes."
        verify_commands:
          - "yarn test --passWithNoTests"
          - "yarn typecheck"
          - "npm run generate:docs"
        files_to_touch:
          - "components/imagepreview/**"
          - "components/audio/**"
          - "components/video/**"
          - "components/lottie/**"
          - "package.json"
          - "sites/config.json"
          - "demo/demoList.ts"
        prompt_context:
          - "Do not bundle heavy native dependencies without approval."
      - id: "05-03-tour-watermark-barrage-cropper"
        title: "Tour Watermark Barrage Cropper"
        kind: "code"
        execution: "codex"
        goal: "Implement Tour, Watermark, Barrage, and AvatarCropper or record explicit deferrals."
        depends_on:
          - "05-01-popover-loading-scroll"
          - "05-02-media-preview-lottie"
        deliverables:
          - "Add business and advanced display components where feasible."
          - "Document any target behavior that is not practical in RN."
        acceptance:
          - "No component is made visible without a working demo and documented setup."
          - "Cropper and barrage dependency risks are decided before implementation."
        evidence_to_capture:
          - "Advanced component demos, tests, or deferral notes."
        verify_commands:
          - "yarn test --passWithNoTests"
          - "yarn typecheck"
          - "npm run generate:docs"
        files_to_touch:
          - "components/tour/**"
          - "components/watermark/**"
          - "components/barrage/**"
          - "components/avatarcropper/**"
          - "docs/rollouts/nutui-react-alignment/api-compatibility.md"
          - "sites/config.json"
          - "demo/demoList.ts"
        prompt_context:
          - "Use deferral notes instead of half-implemented public APIs."

  - id: "06-docs-demo-release"
    title: "Docs Demo And Release"
    goal: "Finalize catalog visibility, docs, demo coverage, CI checks, and release notes."
    depends_on:
      - "05-feedback-media-business"
    summary: "Promote ready components, document gaps, and prove the package is releasable."
    entry_criteria:
      - "All implementation phases are complete or explicitly deferred."
      - "Parity matrix has final statuses."
    exit_criteria:
      - "Docs and demo match implemented catalog."
      - "Global verification passes."
      - "Release notes explain compatibility changes and remaining gaps."
    risks:
      - "Making too many components visible at once can hide broken docs or demos."
      - "CI may need updates after type/test coverage expands."
    batches:
      - id: "06-01-config-doc-demo-promotion"
        title: "Config Docs Demo Promotion"
        kind: "code"
        execution: "codex"
        goal: "Promote ready components and keep deferred components honest."
        depends_on: []
        deliverables:
          - "Update sites/config.json show/exportEmpty states based on parity matrix."
          - "Update demo/demoList.ts or generated demo registry."
          - "Ensure community and JD docs behavior is consistent."
        acceptance:
          - "Visible docs import successfully."
          - "Demo registry contains every visible demoable component."
        evidence_to_capture:
          - "Docs generation and registry check output."
        verify_commands:
          - "node scripts/check-nutui-react-alignment.js --target ../nutui-react"
          - "npm run generate:docs"
          - "npm run generate:jd:docs"
        files_to_touch:
          - "sites/config.json"
          - "demo/demoList.ts"
          - "components/**/commutiy/**"
          - "components/**/jd/**"
        prompt_context:
          - "Do not set show: true for components classified as deferred or unsupported."
      - id: "06-02-ci-and-build-hardening"
        title: "CI And Build Hardening"
        kind: "code"
        execution: "codex"
        goal: "Update local and CI checks to cover the aligned package reliably."
        depends_on:
          - "06-01-config-doc-demo-promotion"
        deliverables:
          - "Update scripts or CI only where needed for new checks."
          - "Ensure bob build and docs generation remain deterministic."
        acceptance:
          - "CI checks match documented local verification."
          - "No unnecessary package-manager migration is introduced."
        evidence_to_capture:
          - "Local verification command output."
        verify_commands:
          - "yarn lint"
          - "yarn typecheck"
          - "yarn test --passWithNoTests"
          - "yarn prepack"
        files_to_touch:
          - "package.json"
          - ".github/workflows/**"
          - "scripts/**"
          - "tsconfig*.json"
        prompt_context:
          - "Keep Yarn 1 and Node 16.18.1 unless a separate ADR approved a change."
      - id: "06-03-release-notes-and-final-evidence"
        title: "Release Notes And Final Evidence"
        kind: "docs"
        execution: "codex"
        goal: "Capture the final alignment state and user-facing migration notes."
        depends_on:
          - "06-02-ci-and-build-hardening"
        deliverables:
          - "Update changelog or release notes with aligned components, API additions, and deprecations."
          - "Update parity matrix final statuses."
          - "Add final evidence summary."
        acceptance:
          - "Users can see what changed, what stayed compatible, and what remains deferred."
          - "Definition of done in the spec is checked off or explicitly waived."
        evidence_to_capture:
          - "Final verification outputs and parity matrix summary."
        verify_commands:
          - "yarn lint"
          - "yarn typecheck"
          - "yarn test --passWithNoTests"
          - "yarn prepack"
        files_to_touch:
          - "CHANGELOG.md"
          - "docs/rollouts/nutui-react-alignment/**"
          - "README.md"
          - "README_ZH.md"
        prompt_context:
          - "Keep release notes concise and explicit about RN-specific differences."
```
<!-- rollout-plan:end -->

## Phase Notes

### 00-baseline-contract

This phase exists to keep the effort honest. The current repo already lists many target-like names in config, but implementation and visibility are different questions. The first phase should produce the evidence that all later batches use.

### 01-foundation

This phase avoids copying target components into a repo that cannot yet catch drift. The most important outcomes are repeatable checks and a documented API mapping rule.

### 02-p0-component-alignment

P0 components are user-facing today and cheap enough to prove the agent workflow. Button is the pilot; the remaining P0 queues should be expanded into one-component tasks before any runner is generated.

### 03-missing-foundation-components

This phase adds missing primitives and navigation foundations that other target components are likely to depend on. It should be conservative about making components visible.

### 04-data-entry-stack

Data entry components have shared value, validation, popup, keyboard, and scroll behavior. They should be implemented in dependency order, not alphabetically.

### 05-feedback-media-business

Advanced components are where hidden native dependency costs are most likely. These batches must decide dependency strategy before public registration.

### 06-docs-demo-release

The final phase promotes only what is real, verifies the full package, and leaves a clear public map for unsupported or deferred target features.
