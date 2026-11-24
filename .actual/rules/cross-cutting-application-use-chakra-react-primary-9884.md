---
version: 2
name: cross-cutting-application-use-chakra-react-primary-9884
description: "Adoption of @chakra-ui/react as Primary Component and Theming Library: Application Use Chakra React Primary Component"
metadata:
  actual:
    adr_id: 9884ff9a-80eb-4c15-af7e-98ba3e0a518f
    status: accepted
    candidate_group_id: 8ec0eb73-bba3-4664-ab02-5c257c095e8a
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - react-plugin-tools/react_plugin_template/src/**
---

# Adoption of @chakra-ui/react as Primary Component and Theming Library: Application Use Chakra React Primary Component

Scope: applies to `dev/react-plugin-tools/react_plugin_template/src/**`.

These rules are ALWAYS ACTIVE for:
- All React presentation components, pages, layout containers, and root application entry points
- Design token definitions, color mode contexts, and styling configurations

### Rules

- **R-LM-001** MUST: The application MUST use `@chakra-ui/react` as the primary component library for presentation components, layout structures, and UI primitives.

### Verify

- Discover and run the project static analysis and linting scripts to verify compliance with UI library import standards
- Discover and run the project type check script to ensure all component props and theme definitions align with resolved dependency types

**Accept when:**

- Verification commands succeed with zero errors reported by type checking and linting systems
- All UI components render using the adopted design system tokens and provider context

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis and type checking in the continuous integration pipeline
- Verified by: Peer code review of presentation components and styling imports
- Violation handling: Pull requests introducing unapproved styling mechanisms or bypassing the designated UI library are blocked pending remediation
- Violation handling: Violations flagged during static analysis must be refactored to use standard component primitives and tokens
- Exception process: Developers requesting an alternative UI component or custom styling approach outside the adopted component library must submit an architectural exception documenting necessity, bundle impact, and accessibility compliance
</enforcement>
