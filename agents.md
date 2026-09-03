# Engineering Principles

These principles apply to **all code changes** in this repository.

## Core Principles

Before implementing any change, ensure the solution is:

1. **Accurate**

   * Correctly solves the stated business and technical requirement.
   * Do not make assumptions about unclear requirements; identify them first.

2. **Consistent**

   * Follow the existing architecture, patterns, conventions, naming, and implementation style.
   * Prefer extending existing patterns over introducing new ones unnecessarily.

3. **Performant**

   * Avoid unnecessary database queries, API calls, computation, network calls, rendering, or memory usage.
   * Consider performance impact for both normal and high-volume scenarios.

4. **Scalable**

   * The solution should continue to work as data volume, users, traffic, and system complexity increase.
   * Avoid approaches that work only for the current scale.

5. **Reliable**

   * Handle errors, failures, edge cases, invalid inputs, and unexpected conditions appropriately.
   * Avoid fragile implementations and single points of failure where reasonably possible.

6. **Great User Experience**

   * Keep the experience intuitive, responsive, predictable, and consistent with the existing application.
   * Avoid unnecessary changes to existing user workflows.

7. **No Regression**

   * Do not break existing functionality, workflows, integrations, APIs, data flows, security, or downstream dependencies.
   * Consider backward compatibility and side effects before making changes.

8. **Secure**

   * Preserve existing security controls and do not introduce security vulnerabilities.
   * Consider authentication, authorization, access control, data exposure, input validation, and sensitive data handling where applicable.

9. **Maintainable**

   * Prefer clear, simple, readable, and testable solutions.
   * Avoid unnecessary complexity, duplication, abstractions, or over-engineering.
   * Another developer should be able to understand and maintain the implementation.

---

# Change Workflow

For any non-trivial change, follow this workflow:

**Understand → Plan → Review → Implement → Validate**

## 1. Understand

Before changing code:

* Understand the existing implementation relevant to the task.
* Identify affected components, services, APIs, database objects, configurations, pipelines, and dependencies.
* Inspect only the files and code paths relevant to the change.
* Do not unnecessarily scan or analyze unrelated parts of the repository.

## 2. Plan

Before implementation:

* Explain the proposed approach.
* Identify the files/components that will change.
* Identify important dependencies and side effects.
* Identify potential risks and edge cases.
* Explain how the approach satisfies the Engineering Principles above.
* Prefer the smallest change that correctly solves the problem.

**Do not modify files during the planning phase.**

For non-trivial changes, wait for explicit user approval before implementation.

## 3. Implement

After approval:

* Implement the agreed approach.
* Keep the scope limited to the requested change.
* Do not make unrelated refactoring or cleanup unless it is necessary for the change.
* Follow existing project conventions and architecture.
* Reuse existing functionality where appropriate rather than creating duplicate implementations.

## 4. Validate

After implementation:

* Verify that the requested functionality works correctly.
* Run the relevant tests, build, linting, type checks, or other available validation.
* Check for regressions in related functionality.
* Review the implementation against all 9 Engineering Principles.
* Report any remaining risks, limitations, or assumptions.

---

# Context Efficiency

Context is a limited resource.

* **Be context-efficient.**
* Inspect only what is necessary to understand and implement the task.
* Do not read the entire repository unless there is a specific reason.
* Do not repeatedly inspect the same files or repeat information already established.
* Prefer targeted searches over broad repository exploration.
* Keep plans and explanations concise and focused on the current task.
* Do not include large amounts of source code in explanations when a summary is sufficient.
* Avoid unrelated refactoring, analysis, or recommendations.
* Preserve relevant context, but do not unnecessarily expand the working context.

**Goal: maximize engineering quality while minimizing unnecessary context usage.**

---

# Decision Rules

When multiple approaches are possible:

1. Prefer the approach that fits the existing architecture.
2. Prefer the simplest solution that satisfies the requirements.
3. Prefer reuse over duplication.
4. Prefer minimal, focused changes over broad changes.
5. Prefer solutions that are easy to test and maintain.
6. Consider performance, scalability, reliability, security, and regression risk before choosing an approach.
7. Do not introduce new technologies, libraries, patterns, or abstractions unless there is a clear benefit.

When requirements conflict, prioritize:

**Correctness → Security → Reliability → No Regression → Performance → Scalability → Maintainability → User Experience**, while using engineering judgment based on the specific task.

---

# Communication

When planning or implementing:

* Be concise and explicit.
* State assumptions when they matter.
* Clearly distinguish facts from assumptions.
* Do not claim something was tested or verified unless it was actually verified.
* If the existing implementation creates a constraint or risk, explain it before proceeding.
* If the requested approach would violate one of the Engineering Principles, flag it and propose a safer alternative.
