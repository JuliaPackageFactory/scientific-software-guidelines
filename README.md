# Scientific Software Development Guidelines

**Development practices for research software, distilled from 26 published sources and cited clause by clause.**

Every clause below is backed by at least one of the sources listed in [Appendix D](#appendix-d-sources). The `> Sources:` line under each clause names the source and the section, rule number, or page where the recommendation appears, so that any clause can be checked against the original. Practices that are widely believed but not supported by these sources are not stated as clauses; they are collected in [Appendix B](#appendix-b-commonly-held-beliefs-without-support-in-the-sources). Decisions that an organization may want to make beyond what the sources say are listed as candidates in [Appendix C](#appendix-c-candidate-organizational-policies).

The guidelines are language-agnostic. Part I covers software development in general. Part II covers what scientific computing adds, and begins by stating how research software differs from other software.

---

## Contents


**[Part I: Software development in general](#part-i-software-development-in-general)**

- [G1. Principles and proportionality](#g1-principles-and-proportionality)
- [G2. Planning and scope](#g2-planning-and-scope)
- [G3. Version control](#g3-version-control)
- [G4. Project layout, licensing and dependencies](#g4-project-layout-licensing-and-dependencies)
- [G5. Design and coding](#g5-design-and-coding)
- [G6. Testing](#g6-testing)
- [G7. Automation and CI](#g7-automation-and-ci)
- [G8. Documentation](#g8-documentation)
- [G9. Versioning and releases](#g9-versioning-and-releases)
- [G10. Code review and collaboration](#g10-code-review-and-collaboration)
- [G11. Security and secrets](#g11-security-and-secrets)
- [G12. AI coding agents](#g12-ai-coding-agents)
- [G13. People and operations](#g13-people-and-operations)

**[Part II: Additional practices for scientific computing](#part-ii-additional-practices-for-scientific-computing)**

- [S1. What is different about research software](#s1-what-is-different-about-research-software)
- [S2. Documenting the model, its assumptions and its domain of validity](#s2-documenting-the-model-its-assumptions-and-its-domain-of-validity)
- [S3. Verification and validation](#s3-verification-and-validation)
- [S4. Floating point and numerical computation](#s4-floating-point-and-numerical-computation)
- [S5. Units, coordinate frames and physical constants](#s5-units-coordinate-frames-and-physical-constants)
- [S6. Random numbers](#s6-random-numbers)
- [S7. Reproducibility and provenance](#s7-reproducibility-and-provenance)
- [S8. Data management](#s8-data-management)
- [S9. Analysis code, libraries and notebooks](#s9-analysis-code-libraries-and-notebooks)
- [S10. Reporting uncertainty and limitations](#s10-reporting-uncertainty-and-limitations)
- [S11. Publication, citation and maintenance](#s11-publication-citation-and-maintenance)
- [Appendix A. Adopting practices in stages](#appendix-a-adopting-practices-in-stages)
- [Appendix B. Commonly held beliefs without support in the sources](#appendix-b-commonly-held-beliefs-without-support-in-the-sources)
- [Appendix C. Candidate organizational policies](#appendix-c-candidate-organizational-policies)
- [Appendix D. Sources](#appendix-d-sources)
- [Appendix E. How this document was made](#appendix-e-how-this-document-was-made)

---

## How to read this document

### Requirement levels

| Level | Condition for assigning it |
|---|---|
| **(MUST)** | At least one source states the practice as *must*, *shall*, or *required* within its stated scope, and no source contradicts it within that scope. Scope conditions are given in the heading or the first sentence, for example "when publishing or distributing", "if you declare Semantic Versioning", "for JOSS submissions", "in repositories maintained by several people". |
| **(SHOULD)** | At least one source states the practice as *should*, *recommend*, or as a practice in the imperative. |
| **(MAY)** | The sources use *may*, *consider*, or give it as an example only. (No MAY clauses survived selection for this edition.) |

No clause is stronger than its sources. When several sources agree but none goes beyond *should*, the clause stays at SHOULD. When a single source says *must* but its scope is narrow (NASA's standard for models supporting critical decisions, ColPrac for Julia community packages, JOSS review requirements), the scope is stated as a condition. **(single source)** at the end of a heading marks a clause backed by one source only; such clauses were kept only where scientific computing needs them.

This edition keeps clauses that at least three sources support, plus MUST clauses, plus clauses in Part II that two sources support. Clause numbers follow the full edition, so gaps in numbering (for example G6.24 followed by G6.26) are clauses omitted here. The full set of 343 clauses, including weaker ones, and the extraction tables behind every citation remain in the authors' working files and can be requested.

### Reading a Sources line

```
> Sources: **W14** §Make incremental changes, 4a · **DLR** EST.4 [AC1+] (must) · **S13** Rule 6 — 3 sources.
```

- **W14** is a source key from Appendix D. What follows it is the section, rule, box, requirement number, or page in that source.
- `(must)` marks a passage in which the source itself uses *must*, *shall*, or *required*.
- `[AC1+]` is the DLR application class (1–3, cumulative) from which the recommendation applies. `[NASA scope]` restricts a requirement to models and simulations within the scope of NASA-STD-7009. `[JOSS]` restricts it to JOSS submissions. `[if SemVer]` restricts it to software that declares Semantic Versioning.
- "N sources" counts the sources that state the practice as a recommendation; sources that only give examples or background are not counted. Three Wilson et al. papers (W14, W17, TW17) share authors, and the NASA standard (N7009) and its handbook (N7009H) are one family; the Note line says so where it matters.
- `> Note:` records disagreements between sources, limits the sources themselves state, and common misreadings.

### Proportionality

These practices are not meant to be applied uniformly. The sources agree that the rigor of quality practices should match the use, scale, user base, and consequences of failure (G1.1). A one-off exploratory analysis script needs little; software distributed to third parties or supporting published results needs much more. [Appendix A](#appendix-a-adopting-practices-in-stages) lists how each source stages its recommendations.

### What this document does not say

Many familiar rules have no support in this set of sources: numeric coverage targets, line-count limits for pull requests, "compare floating-point numbers with a tolerance and you are safe", "avoid global variables", "commit lock files". Appendix B lists them, with what the sources do and do not say. An item in Appendix B does not mean the practice is wrong; it means these sources give no basis for it.

---

# Part I: Software development in general

## G1. Principles and proportionality

### G1.1 (MUST) Match the rigor of quality practices to the software
Do not impose the same kinds and stringency of quality practices on all software. Choose them to fit the purpose, size, range of users, development period and the consequences of failure. Whatever level you choose, be aware that more rigorous practices exist.
> Sources: **DLR** §Application Classes (must); §Evaluation of the achieved Application Class (must) · **RSQ** three_tier_view §"History and context; software_maintenance §"How do you maintain your · **TW** code-reuse §"Reusable Code"; code-reuse-overview · **TW17** §Introduction · **W14** §Introduction · **N7009H** §5.1.1.4 — 6 sources.
> Note: The way levels are divided differs between sources (DLR's four classes, RSQ's three tiers, NASA's criticality matrix, TW17's "subject of a paper"), and they are not mapped onto one another. RSQ states that there is no agreed mapping of indicators to tiers [RSQ].

### G1.2 (SHOULD) Decide and record the target category at the start, and review it periodically
At the start of development, have the person responsible decide and record the software's target category (class, tier or criticality) and the practices to be applied accordingly. Review the category just before use and before entering a new phase.
> Sources: **DLR** §Definition of the intended Application Class, Figure 1; EQA.1, §Qualification [AC1+] · **N7009** 4.1.1.2 [M&S 6] [NASA scope] (must); 4.1.3a · **N7009H** §5.1.1.3 [M&S 6]; §5.6.1.1 · **RSQ** life_cycle §"Lifecycle Stages"; three_tier_view — 4 sources.

### G1.3 (MUST) Treat distribution to third parties and long-term development as triggers for moving to a higher level
If you distribute the software to third parties outside your institution, apply at least the lowest category (DLR application class 1) and consider a license. If the scope of functionality is large and development is expected to continue after the people who hold the knowledge leave (as a guide, more than two years), apply the next category up. Also raise the level above that for personal use when the software becomes the subject of a paper or is handed over to others (SHOULD).
> Sources: **DLR** §Definition of the intended Application Class (must); §Definition of the intended Application Class, "Period (must) · **TW17** §Introduction; §Conclusion · **RSQ** software_maintenance §"How do you maintain your; life_cycle §"Research Software Lifecycle & Three-Tier — 3 sources.
> Note: Trigger thresholds differ between sources. DLR uses two years and one person-year; TW17 uses "once it has been used three or four times for slightly different purposes" as a guide.

### G1.4 (SHOULD) Aim for the most rigorous level when the impact of failure is high, regardless of size
If the results affect human safety, mission success, organizational liability or important outcomes, aim for the most rigorous category regardless of size or duration.
> Sources: **DLR** §Definition of the intended Application Class, "Risks; §Application Class 3 · **N7009H** §5.1.1.4 · **N7009** §4.1.1.2; App. D.1, D.3 · **RSQ** life_cycle §"Research Software Lifecycle & Three-Tier — 4 sources.
> Note: NASA's handbook cautions that compliance with procedures does not correlate with the level of credibility achieved [N7009H].

### G1.5 (SHOULD) Do not impose heavy practices on small, one-off or personal code
Do not require comprehensive documentation, unit tests, coverage measurement, CI and the like for code that answers a specific question about specific data only once, or for small personal scripts. For such code, running it on that data may be the only reasonable check.
> Sources: **TW17** §Conclusion · **W17** What we left out; §Introduction · **DLR** §Application Class 0; §Definition of the intended Application Class, "Scope · **RSQ** static_analysis §"How do you use static analysis to; software_maintenance §"How do you maintain your · **TW** compendia §"Summary; code-reuse §"Reusable Code"; code-reuse-overview · **CX** Turn repeatable work into skills — 6 sources.
> Note: "Do not impose heavy practices" does not mean "do nothing". RSQ says not to wait until software is widely used before establishing good habits [RSQ], and DLR recommends introducing the recommendations of higher classes early in a weaker form [DLR]. W14, aimed at people who already do a significant amount of computing, recommends practices that W17 left out (code review, unit testing, etc.).

### G1.6 (SHOULD) Introduce practices gradually rather than all at once
When introducing practices to a research group or an existing codebase, start with the core ones and add the rest gradually over a period of time. When introducing static analysis or similar tools to existing code, use the tool's baseline mechanism and address existing warnings gradually.
> Sources: **W14** §Conclusion · **W17** What we left out, Branches · **TW** overview-barriers §"Limited incentives to give · **RSQ** using_version_control §"How do you implement version; static_analysis §"How do you use static analysis to · **CX** Turn repeatable work into skills — 5 sources.

### G1.7 (SHOULD) You may drop recommendations that do not apply, after recording the reason
Interpret general standards and checklists for your own project, and adjust them if necessary. Record the reason for each item you do not meet or have dropped. If it is unclear how an alternative satisfies the risk-reduction intent, follow the standard.
> Sources: **DLR** §Definition of the intended Application Class, Figure 1; §Evaluation of the achieved Application Class; C3 · **N7009** §4 · **N7009H** §4.2; §4.8 · **TW** reviewing-checklist §"Checklist for code review process; code-reuse §"Reusable Code"; code-reuse-overview — 4 sources.

### G1.8 (SHOULD) Self-assess achievement with a checklist, and treat the results as a diagnosis
Periodically check how far the recommendations for the target category have been implemented, and record unmet items and future tasks. Use assessment results and quality metrics as a diagnosis for finding improvements, not to score the software or its authors. Do not equate them with quality itself.
> Sources: **DLR** §Evaluation of the achieved Application Class; C3 · **NL** best_practices, §Checklists · **N7009H** §4.1; App. D.2 b–c · **TW** reviewing-checklist §"Checklist for code review process · **RSQ** fair_rs §"Tools and practices for FAIR; quality_dimensions intro — 5 sources.

### G1.9 (SHOULD) Decide on improvement measures by cost-effectiveness, and avoid over-engineering
If the target level has not been reached, decide on improvement measures in light of the remaining development time and resources. Do not chase findings that do not affect correctness or explicitly stated requirements.
> Sources: **DLR** §Evaluation of the achieved Application Class (must) · **CC** Add an adversarial review step, Callout · **N7009H** §5.6.3.1.1; App. D.10 · **GEP** review/reviewer/standard intro — 4 sources.

## G2. Planning and scope

### G2.1 (SHOULD) At the outset, briefly write down the purpose, intended use, constraints and what is out of scope, and agree on them with stakeholders
Early in development, briefly document the purpose, intended use, essential requirements, constraints (mandatory languages, environments and legal aspects) and what is out of scope, and agree on them with stakeholders. This document may be revised later. For models that support important decisions, recording the intended use is required (MUST).
> Sources: **DLR** EAM.1, §Requirements Management [AC1+]; EAM.3, §Requirements Management [AC1+] · **N7009** 4.1.1.1 [M&S 40] [NASA scope] (must); 4.2.2b · **N7009H** §4.6; §5.1.1.2 [M&S 40]; §5.1.1.2 Note · **RSQ** life_cycle §"Requirements", §"Design · **OSG** starting-a-project / Setting your goals; best-practices / Write down your project's vision · **CC** Let Claude interview you · **CX** Strong first use: Context and prompts — 7 sources.
> Note: W14 states that scientific programmers' requirements are rarely fixed, so methods that depend on upfront requirements specifications fit poorly [W14]. This clause does not require detailed specifications to be fixed in advance.

### G2.2 (SHOULD) Define completion and acceptance criteria for work in advance
For each task or change, define in advance what counts as done (tests pass, the bug no longer reproduces, etc.). For models and simulations, set verification, validation and uncertainty criteria early.
> Sources: **CX** Strong first use: Context and prompts · **CC** Let Claude interview you · **MS** code-reviews/pull-requests §"General Process · **N7009** 4.1.1.5 [M&S 43] [NASA scope] (must) · **N7009H** §5.6.3.2 [M&S 31; Rec. 4.3.7a–c] · **RSQ** testing_software §"What types of software tests — 6 sources.

### G2.3 (SHOULD) State quality requirements and users early, and prioritize them
State quality requirements such as accuracy, uncertainty, performance and operational limits early, even if provisional, and prioritize them, because they conflict with one another. Also describe the user groups and their tasks.
> Sources: **DLR** §Requirements Management; EAM.4, §Requirements Management [AC2+] · **N7009H** §5.1.1.4 · **MS** engineering-fundamentals-checklist §"Design Reviews · **RSQ** life_cycle §"Requirements", §"Design · **NL** technology/user_experience, §Design Thinking; technology/user_experience, §UX disciplines — 5 sources.

### G2.4 (SHOULD) Assume that requirements and plans will change, and iterate in small units
Rather than planning months ahead in detail, work in small steps with frequent feedback and course correction. Produce requirements, plans and assessments early, even provisionally, and update them at later stages.
> Sources: **W14** Practice 3a, §Make Incremental Changes; §Make Incremental Changes · **N7009H** §4.6; §4.6 a; §5 Note; App. A.2 · **DLR** EAM.9, §Requirements Management [AC2+] · **TW** rdm-fair §"The FAIR Principles — 4 sources.

### G2.5 (SHOULD) Manage requirements and work items in a list, prioritize them and review them regularly
Manage functional requirements and work items in a list (such as an issue tracker), give each an identifier, description, priority and origin, and review them regularly with stakeholders. DLR requires this for application class 2 and above, and W14 once a team grows beyond a certain size.
> Sources: **DLR** EAM.2, §Requirements Management [AC2+]; EAM.9, §Requirements Management [AC2+] · **TW** reviewing-checklist §"Documentation · **MS** engineering-fundamentals-checklist §"Work Item Tracking · **W14** Practice 8c, §Collaborate — 4 sources.

### G2.6 (SHOULD) For medium-sized and larger software, maintain and publish a roadmap
For software developed over a long period by many people, maintain an outline plan showing what will be achieved in which version and by when, and put it where users and contributors can see it.
> Sources: **DLR** EÄM.4, §Change Management [AC2+] · **RSQ** life_cycle §"Planning · **OSG** best-practices / Write down your project's vision — 3 sources.

### G2.7 (SHOULD) Plan from development through maintenance to retirement, and secure maintenance as an ongoing cost
Plan development, maintenance, publication and archiving (retirement), for example in the form of a software management plan, and name the people or organizations responsible. Deliberately set aside time, staff and funding for maintenance as a continuing cost.
> Sources: **N7009** 4.1.1.3 [M&S 41] [NASA scope] (must) · **RSQ** life_cycle §"Lifecycle Stages"; three_tier_view; software_maintenance §"How do you maintain your · **NL** best_practices, §Software management plans · **OSG** starting-a-project / Setting your goals · **F4RS** Discussion, Netherlands eScience Center; Discussion, ELIXIR — 5 sources.

### G2.8 (SHOULD) For uncertain or large changes, separate investigation and planning from implementation
For changes where the approach is uncertain, that span multiple files, or that touch unfamiliar code, investigate before writing code and make a plan, including how to split the work. Skip the plan for small fixes whose diff can be described in one sentence.
> Sources: **CC** Explore first, then plan, then code; Explore first, then plan, then code, Callout · **CX** Plan first for difficult tasks; Common mistakes; Plan first for difficult tasks · **GEP** review/developer/small-cls §"Splitting CLs — 3 sources.
> Note: CC and CX are vendor documents for AI agents. CC itself says that skipping the plan is sometimes the right choice for exploratory tasks [CC].

### G2.9 (SHOULD) For categories with particularly high risk, perform risk management and requirements traceability
In the highest category, record and track development risks together with their likelihood and impact, and make it possible to trace, for each requirement, its rationale and verification method, as well as changes from requirements to code and tests.
> Sources: **DLR** EAM.6, §Requirements Management [AC3+]; EAM.10, §Requirements Management [AC3+] · **N7009H** §5.3.1 c(1) · **MS** engineering-fundamentals-checklist §"Design Reviews — 3 sources.

## G3. Version control

### G3.1 (MUST) Put code under version control from the start
Manage a project's code and scripts in a version control system (usually Git) from the moment you start writing them. This lets you identify which version of the code produced which result, and return to that state.
> Sources: **W14** Practice 3b, §Make Incremental Changes · **W17** 5h, Version control systems · **S13** Rule 4 · **TW17** Rule 1 · **TW** vcs §"Motivation (must); vcs-checklist §"Make Use of Git"; vcs-workflow · **NL** best_practices, §Version control; best_practices, §Version control (must) · **RSQ** quality §"Aspects of Research Software Quality · **DLR** EÄM.7, §Change Management [AC1+] · **N7009** 4.2.2g · **N7009H** §5.7 [Rec. 4.1.2b, 4.1.3a] — 10 sources.
> Note: For beginners, W17 also accepts starting with manual, systematic versioning [W17]. S13 gives, as a minimal alternative, saving copies of scripts from time to time [S13].

### G3.2 (SHOULD) Put everything created by people under version control as soon as it is created
Put anything that people create or edit by hand, such as source code, scripts, configuration files, dependency specifications, build scripts, tool configuration, documentation sources and instruction files for AI agents, under version control when it is created.
> Sources: **W14** Practice 3c, §Make Incremental Changes · **TW17** Rule 1 · **W17** 5a, §Keeping track of changes · **RSQ** using_version_control §"Why does version control; software_project_structure §"Use version control · **DLR** EÄM.7, §Change Management [AC1+]; EAA.10, §Automation and Dependency Management [AC1+] · **TW** vcs §"Motivation (must) · **CC** Write an effective CLAUDE — 7 sources.

### G3.3 (SHOULD) Do not put secrets or data with restricted sharing in the repository
Do not put secrets such as passwords, private keys or tokens in the repository; keep them in local private files that are excluded from tracking. Do not put legally restricted data in public repositories either. Before publishing, confirm that none of these are in the history (details in G11).
> Sources: **MS** engineering-fundamentals-checklist §"Source Control; source-control/secrets-management intro · **W17** Inadvertent sharing · **RSQ** software_project_structure §"Use version control · **OSG** starting-a-project / Your pre-launch checklist — 4 sources.
> Note: No source states this as a must. If your organization makes it mandatory, place it in Appendix C.

### G3.4 (SHOULD) Do not version-control generated files that can be regenerated (small results may be an exception)
As a rule, do not commit anything that can be regenerated from the code and raw data, such as compiled binaries or intermediate analysis files. However, you may commit small data or results so that collaborators can refer to them easily and compare them across versions.
> Sources: **TW17** Rule 1 · **TW** vcs-checklist §"Make Use of Git · **W14** Practice 3c, §Make Incremental Changes · **W17** What not to put under version control — 4 sources.
> Note: TW17 and TW say not to commit generated files, while W17 recommends committing them if they are small. Sources also split on raw data: W17 says tracking its versions is unnecessary [W17], while TW puts everything in the VCS [TW] (see S8).

### G3.5 (SHOULD) Do not commit large files or frequently changing binaries directly to the repository
Do not directly commit large data or result files, or frequently changing binaries (images, videos, compiled code). Store them elsewhere, and leave metadata describing their contents and how to obtain them in the repository. As a guide, W17 cites GitHub's per-file limit of 100 MB.
> Sources: **W17** What not to put under version control · **MS** source-control/git-guidance/README §"Working with · **W14** Practice 3c, §Make Incremental Changes · **RSQ** software_project_structure §"Use version control; using_version_control §"How do you choose the right · **DLR** EÄM.7, §Change Management [AC1+] — 5 sources.
> Note: On Git LFS, W17 (2017) says it is not mature, MS mentions it as one example, and RSQ cautions that its current state should be checked before adopting it.

### G3.6 (SHOULD) Limit each commit to one purpose
Make each commit a small, logical change with a single purpose. Separate bug fixes from new features, and refactoring or reformatting from functional changes. A guide for splitting is "the unit you would want to revert as a whole in the future".
> Sources: **TW** vcs-checklist §"Make Use of Git · **DLR** EÄM.8, §Change Management [AC1+] · **MS** source-control/git-guidance/README §"Committing; source-control/git-guidance/README §"Commit Best · **W17** 5b, §Keeping track of changes · **CCOM** FAQ "What do I do if the commit conforms to more than · **GEP** review/developer/small-cls §"Separate Out Refactorings · **CP** §Guidance on contributing PRs — 7 sources.
> Note: W17 explicitly says to avoid splitting too finely as well.

### G3.7 (SHOULD) Write what and why in commit messages
Make the first line a short summary that makes sense on its own, and after a blank line write the context in the body: the reason, impact, related issues and so on. Do not write messages that do not convey their content, such as "update" or "Fix bug". Detailed rules, such as keeping the first line to about 50 characters in the imperative mood, are optional (MAY).
> Sources: **MS** engineering-fundamentals-checklist §"Source Control; source-control/git-guidance/README §"Commit Message · **GEP** review/developer/cl-descriptions intro; review/developer/cl-descriptions §"First Line · **DLR** EÄM.8, §Change Management [AC1+]; EÄM.10, §Change Management [AC3+] · **TW** vcs-checklist §"Make Use of Git"; vcs-workflow · **RSQ** using_version_control §"Why does version control · **W17** 5d, §Keeping track of changes · **CC** Explore first, then plan, then code, Step "Commit · **CCOM** Summary — 8 sources.

### G3.8 (SHOULD) Commit small and often, and share and integrate changes frequently
Commit frequently in small units and regularly pull in others' changes so that individual versions do not diverge. Resolve merge conflicts only after understanding both versions, and confirm the resolution with a build and tests.
> Sources: **W17** 5c, §Keeping track of changes · **TW** vcs-checklist §"Make Use of Git · **MS** source-control/git-guidance/README §"Committing; source-control/git-guidance/README §"Merge Conflicts · **RSQ** using_version_control §"Why does version control · **DLR** EAA.8, §Automation and Dependency Management [AC2+] — 5 sources.

### G3.9 (SHOULD) Do not put broken or unfinished code on shared branches
Before committing to a shared branch, run the build and tests locally, and keep the repository in a consistently working state.
> Sources: **W17** 5d, §Keeping track of changes · **MS** source-control/git-guidance/README §"Commit Best (must) · **DLR** EÄM.8, §Change Management [AC1+] — 3 sources.
> Note: This is in tension with "commit frequently so you do not lose work" (G3.8). It can be read as separating work in progress on working branches from the main branch, but no source describes how to reconcile the two.

### G3.10 (SHOULD) Tag releases and the commits that produced a paper's results
Tag releases and the exact commits that produced the results in papers and reports, so that you can return to those states at any time. Distinguish submitted versions and thesis versions by tags or releases, not by version numbers in file names.
> Sources: **RSQ** using_version_control §"Why does version control · **DLR** ERM.1, §Release Management [AC1+] · **JOSS** RC §Good practices; SUB §Review Process [JOSS] (must) · **TW17** Rule 4 · **S13** Rule 4 — 4 sources.

### G3.11 (SHOULD) In repositories developed by several people, use working branches and pull requests
Do not change the main branch directly; create a working branch for each bug fix or feature, and merge it only after review and testing through a pull request. Do not squash commits while a review is in progress.
> Sources: **MS** engineering-fundamentals-checklist §"Source Control (must); source-control/git-guidance/README §"Merge Conflicts · **CP** §Contributing PRs (must); §Guidance on contributing PRs · **NL** CONTRIBUTING, §Technical · **TW17** Rule 1 · **TW** vcs-checklist §"Make Use of Git · **RSQ** using_version_control §"How do you implement version — 6 sources.
> Note: W17 says that branches add complexity for beginners and can be introduced after the basic workflow has been learned [W17]. TW17 likewise says to use feature branches "once you are comfortable" [TW17][TW17]. The only sources that state must are MS (enterprise customer projects) and CP (community packages).

### G3.12 (SHOULD) In multi-person projects, decide the branching strategy and naming conventions at the start
Decide and document as a team, when development starts, the branching, merging and release strategy and the branch naming conventions. For small projects, a main branch plus short-lived working branches is often enough. Limit each branch to one purpose, and delete it after it has been merged.
> Sources: **MS** engineering-fundamentals-checklist §"Source Control; source-control/README §"General Guidance", §"Creating · **RSQ** using_version_control §"How do you implement version · **DLR** EÄM.9, §Change Management [AC2+] · **TW** vcs-checklist §"Make Use of Git — 4 sources.

### G3.13 (SHOULD) Agree on commit message conventions as a team
Decide and document conventions for commit messages and PR descriptions when the project starts. Conventional Commits is one option, and adopting it is optional (MAY). If you adopt it, follow the specification's MUSTs (the `<type>: <description>` format, `feat` / `fix`, and `!` or `BREAKING CHANGE:` for breaking changes).
> Sources: **MS** source-control/git-guidance/README §"Commit Message · **RSQ** using_version_control §"Why does version control · **CCOM** Summary; Specification 1 (must) — 3 sources.
> Note: No source makes Conventional Commits a general obligation.

### G3.14 (SHOULD) Choose version control tools and hosting early, based on what those around you use
Choose what your colleagues or community already use. If you handle data, also consider whether platforms provided by your institution are available and what legal conditions apply.
> Sources: **W14** §Make Incremental Changes · **RSQ** using_version_control §"How do you choose the right · **TW** vcs-git-in-research §"Tools", §"Git for research — 3 sources.

## G4. Project layout, licensing and dependencies

### G4.1 (MUST) Put a README at the root
Put a README at the root of the repository that describes what the software does, how to install it and its requirements, basic usage, and where to get help. Also state its status there, such as being incomplete or not accepting contributions (for details of the content, see G8).
> Sources: **RSQ** documenting_software_project §"How to document your (must); creating_good_readme §"Requirements (must) · **OSG** starting-a-project / Launching your own open source; starting-a-project / Writing a README · **TW** open-checklist §"Open Source Software"; compendia · **W17** 3a, §Collaboration · **JOSS** RC §Good practices; RC §Documentation · **MS** engineering-fundamentals-checklist §"Source Control · **DLR** EÄM.2, §Change Management [AC1+] — 7 sources.

### G4.2 (MUST) License code that you publish or distribute to others
When you publish or distribute code to others, decide on a license beforehand and attach it. Because of copyright, others cannot use, modify or redistribute code that has no license. Merely placing code in a public repository does not license it.
> Sources: **OSG** legal / Why do people care so much about the legal (must); legal / Are public GitHub projects open source? (must) · **NL** best_practices, §Licensing (must) · **JOSS** RC §Good practices [JOSS] (must); SUB §Submission requirements [JOSS] (must) · **DLR** ERM.9, §Release Management [AC1+] (must); §Definition of the intended Application Class (must) · **W17** 3d, §Collaboration · **TW** code-reuse-overview §"Repeatable Recommendations · **RSQ** documenting_software_project §"How to document your · **F4RS** FAIR4RS Principles, R1.1 · **MS** engineering-fundamentals-checklist §"Source Control — 9 sources.

### G4.3 (SHOULD) Put the license text in a LICENSE file at the root
Put the license text as plain text in a file named LICENSE (or COPYING) at the root, and also state the license name in the README. Mentioning it only in the README is not enough.
> Sources: **TW** licensing §"What, How and Where of License · **JOSS** RC §Software license · **W17** 3d, §Collaboration · **RSQ** documenting_software_project §"How to document your; software_project_structure §"Directory structure · **MS** engineering-fundamentals-checklist §"Source Control · **OSG** starting-a-project / Launching your own open source · **DLR** ERM.2, §Release Management [AC1+] — 7 sources.

### G4.4 (SHOULD) Use a standard open-source license as is
Do not write your own license; use a standard OSI-approved license unmodified. Avoid custom or modified terms. For submission to JOSS, OSI approval is required (MUST).
> Sources: **OSG** legal / Just give me the TL;DR…; starting-a-project; legal / Just give me the TL;DR… · **JOSS** RC §Software license; RC §Good practices [JOSS] (must) · **TW** licensing-checklist §"Checklist — 3 sources.

### G4.5 (SHOULD) Choose a license early, in light of your goals
Choose early, considering the kind of reuse you want, the licenses of your dependencies, the licenses common in your field, and the policies of your institution and funders. If unsure, consult someone knowledgeable.
> Sources: **W17** 3d, §Collaboration; 3d, What not to do · **NL** best_practices, §Licensing · **OSG** legal / Which open source license is appropriate for; legal / What if I want to change the license of my · **RSQ** licensing_software §"What license should you apply to · **DLR** ERM.9, §Release Management [AC1+] (must); EAA.3, §Automation and Dependency Management [AC2+] · **TW** code-reuse-overview §"Repeatable Recommendations; licensing §"Summary"; reviewing-checklist §"Legal — 6 sources.
> Note: Making a permissive license the first choice is the majority view (W17, NL, OSG), but OSG recommends GPLv3 / AGPLv3 depending on the goals [OSG], and RSQ is neutral. There is no basis for rejecting the GPL. If your organization sets a default license, place it in Appendix C.

### G4.6 (MUST) Check and comply with the license terms of dependencies and third-party code
Check and record the licenses of the software you depend on and of third-party code you incorporate, and confirm their compatibility with your own license. Include the required license information in distributions. Do not use others' code that has no license. In DLR, a procedure for checking compatibility each time a dependency is adopted applies to application class 2 and above.
> Sources: **OSG** legal / Which open source license is appropriate for (must); legal / What does my company's legal team need to know? (must) · **DLR** ERM.9, §Release Management [AC1+] (must); EAA.2, §Automation and Dependency Management [AC1+] · **TW** reviewing-checklist §"Legal"; code-reuse-overview; code-reuse-overview §"Repeatable Recommendations · **RSQ** licensing_software §"What license should you apply to — 4 sources.

### G4.8 (SHOULD) Provide a CONTRIBUTING file if you accept contributions, and a CITATION file if you want to be cited
If you accept contributions from others, put a CONTRIBUTING file at the root and link to it from the README (for its content, see G10.28). If you want the software to be cited, provide a file with citation information, such as CITATION.cff (for its content, see S11).
> Sources: **OSG** starting-a-project / Launching your own open source; starting-a-project / Writing your contributing · **MS** engineering-fundamentals-checklist §"Source Control · **DLR** EÄM.2, §Change Management [AC1+] · **W17** 3a, §Collaboration; 3e, §Collaboration · **TW** open-checklist §"Open Source Software · **JOSS** RC §Good practices; RC §Documentation, Community guidelines · **RSQ** documenting_software_project §"How to document your; creating_good_readme §"Citation", §"Why does your · **CFF** SG §General structure of a CITATION.cff file (must); RM §Why should I add a CITATION.cff file to my — 7 sources.
> Note: OSG asks for CONTRIBUTING in "every project", but TW, W17 and RSQ limit it to cases where contributors are sought.

### G4.9 (SHOULD) Use a consistent directory structure
Keep each project in its own directory, put metadata (README, LICENSE, CITATION, dependency specifications) at the root, and divide the rest into subdirectories by type of content, without mixing code, data and generated outputs. Follow the conventions of the language and build tools, and be consistent across all projects in the team. The specific directory names are optional (MAY).
> Sources: **W17** 4a, §Project organization; 4b, §Project organization · **RSQ** writing_readable_code §"Why is it important to make; software_project_structure §"Directory structure · **TW** compendia §"Structure of a Research Compendium; compendia §"Separating Methods, Data, Output · **DLR** EÄM.7, §Change Management [AC1+] · **MS** engineering-fundamentals-checklist §"Source Control — 5 sources.
> Note: Sources split on whether to keep the paper in the same place as the software. RSQ recommends not mixing the package and the paper [RSQ], W17 and TW put them in the same project [W17][TW], and JOSS requires the same repository [JOSS] (see S9).

### G4.10 (SHOULD) Give files names that describe their contents
Give files and directories names that describe their contents or role. Do not use names whose meaning changes, such as sequence numbers (result1.csv) or positions in the final manuscript (fig_3_a.png). Avoid special characters and spaces.
> Sources: **W17** 4f, §Project organization · **TW** compendia §"Creating a Compendium · **RSQ** software_project_structure §"Directory structure; software_project_structure §"Naming conventions — 3 sources.

### G4.11 (SHOULD) Do not hard-code values and paths that may change; separate them out as configuration
Do not write values that may change between runs or environments (parameters, file paths, logging settings) into the code; provide them as parameters with sensible defaults, through configuration files or command-line arguments. If users cannot run the software without editing it, the design is wrong. Keep configuration files under version control, excluding secrets.
> Sources: **TW17** Rule 3; Rule 8 · **MS** engineering-fundamentals-checklist §"Observability; automated-testing/README §"Build for Testing · **TW** code-reuse-overview §"Re-runnable Recommendations · **RSQ** software_project_structure §"Directory structure; using_version_control §"Why does version control · **DLR** EAA.10, §Automation and Dependency Management [AC1+] — 5 sources.

### G4.12 (MUST) Declare dependencies in machine-readable form
Declare the libraries and tools you depend on per project, in machine-readable form, using the language's standard mechanism, and commit the declaration to the repository. Write requirements that the language's mechanism cannot express (OS, interpreter version, additional software, computing resources) in the README.
> Sources: **TW17** Rule 6 · **W17** 2g, §Software · **JOSS** RC §Documentation, Installation instructions · **RSQ** creating_good_readme §"Requirements (must); using_version_control §"Why does version control · **DLR** EAA.2, §Automation and Dependency Management [AC1+]; EAA.10, §Automation and Dependency Management [AC1+] · **TW** renv-options §"Package Management Systems · **MS** engineering-fundamentals-checklist §"Developer · **F4RS** FAIR4RS Principles, R2 — 8 sources.

### G4.13 (SHOULD) Make dependencies installable automatically, and install them that way yourself as a developer
Make dependencies installable automatically with a package manager (and a virtual environment, depending on the language). As a developer, install from the dependency specification yourself, and confirm that it works.
> Sources: **JOSS** RC §Documentation, Installation instructions · **MS** engineering-fundamentals-checklist §"Developer · **TW17** Rule 6; Rule 5 · **RSQ** reproducible_software_environments §"How do you create · **TW** renv-options §"Package Management Systems — 5 sources.

### G4.14 (SHOULD) Reuse established libraries, and choose dependencies carefully
Do not write your own solutions to established problems such as numerical integration or matrix inversion; find and use well-maintained existing libraries. Because dependencies are also a liability, use only those with measurable benefits, choose ones that are robust and available from stable sources, test them before relying on them, and keep the dependency tree small.
> Sources: **W14** Practice 4c, §Don't Repeat Yourself (or Others) · **W17** 2d, §Software; 2e, §Software · **TW17** Rule 5 · **RSQ** software_maintenance §"How do you maintain your; writing_readable_code §"Why is it important to make · **JOSS** SUB §Scope and significance, Design thinking · **N7009H** §5.3.2 g; §5.4.2; §5.5 Note · **TW** reviewing-checklist §"Function/Method definitions — 6 sources.

### G4.15 (SHOULD) Record the exact versions of the dependencies used for results
Record the exact versions of the software and dependencies used when the results were obtained, so that the same environment can be reconstructed later. Make dependency updates traceable as changes in the recorded versions.
> Sources: **RSQ** software_maintenance §"How do you maintain your; quality §"Aspects of Research Software Quality · **S13** Rule 3 · **DLR** EAA.2, §Automation and Dependency Management [AC1+]; EAA.6, §Automation and Dependency Management [AC3+] · **TW** renv-options §"Package Management Systems — 4 sources.
> Note: There is a tension within RSQKit. The maintenance section recommends pinning exact versions [RSQ], while the environments section says there is little need to worry about specific versions during development [RSQ]. No source in the corpus mentions lock files as a means. No source describes the split "version ranges for libraries, pinned versions for analyses" either.

### G4.17 (SHOULD) Preserve the dependency software needed to reproduce results and releases
Preserve the exact versions of the dependency software needed to reproduce important results and releases, so that they can be reconstructed even if they disappear from public repositories.
> Sources: **DLR** EAA.4, §Automation and Dependency Management [AC3+] · **S13** Rule 3 · **W17** 5a, §Keeping track of changes — 3 sources.

### G4.19 (MUST) Before distributing outside your institution, check whether export control regulations apply (single source)
Before distributing software to third parties outside your institution, check whether it contains regulated elements such as cryptography, and whether the recipients fall under the regulations. The checking procedure and point of contact are organization-specific (Appendix C).
> Sources: **DLR** ERM.10, §Release Management [AC1+] (must) — 1 sources.

## G5. Design and coding

### G5.1 (SHOULD) Write for people to read
Write programs not for the computer but so that people, including your future self and new members, can read and understand them.
> Sources: **W14** Practice 1, Box 1; §Write Programs for People, Not · **RSQ** writing_readable_code §"Why is it important to make · **TW** code-reuse-overview §"Modifiable Recommendations · **CP** §Contributing PRs — 4 sources.

### G5.2 (SHOULD) Divide programs into short, single-purpose functions
Divide programs into functions that each do a single, easily understood job, and keep the number of things a reader must hold in mind at once small. Consider splitting functions that are too long or take many arguments.
> Sources: **W14** Practice 1a, §Write Programs for People, Not Computers · **W17** §Software; 2b, §Software · **RSQ** writing_readable_code §"Why is it important to make · **MS** code-reviews/process-guidance/reviewer-guidance · **TW** reviewing-checklist §"Classes and types; reviewing-checklist §"Function/Method definitions · **DLR** EDI.2, §Design and Implementation [AC1+] — 6 sources.
> Note: The guides for line counts and numbers of arguments vary between sources (W17: about 60 lines and no more than five or six arguments; MS: more than three arguments may indicate excessive complexity). There is no agreed threshold.

### G5.3 (SHOULD) Eliminate duplication
Do not copy code; put it into functions and reuse it. Group many closely related variables into data structures, and do not duplicate the same information in code and comments.
> Sources: **W14** Practice 4b, §Don't Repeat Yourself (or Others) · **W17** 2c, §Software · **DLR** EDI.9, §Design and Implementation [AC1+] · **TW** reviewing-checklist §"Function/Method definitions — 4 sources.
> Note: No source gives a threshold for how many repetitions warrant factoring code out.

### G5.4 (MUST) Give data and constants a single authoritative representation
Give every piece of data a single authoritative representation in the system. Define physical constants and the like only once, so that the whole program uses the same value.
> Sources: **W14** Practice 4a, §Don't Repeat Yourself (or Others) (must); Practice 4a · **DLR** EDI.9, §Design and Implementation [AC1+] — 2 sources.

### G5.5 (SHOULD) Prefer simple, understandable solutions
At the level of lines, functions and classes, check whether readers can understand the code quickly and whether changes to it are likely to introduce bugs. Choose general, complex solutions deliberately on the basis of requirements, or grow them gradually through refactoring.
> Sources: **DLR** EDI.10, §Design and Implementation [AC1+] · **GEP** review/reviewer/looking-for §"Complexity · **MS** code-reviews/process-guidance/reviewer-guidance · **TW** reviewing-checklist §"Architecture; reviewing-checklist §"Function/Method definitions · **W14** Practice 1a, §Write Programs for People, Not Computers · **N7009H** §5.2.1.2 — 6 sources.

### G5.6 (SHOULD) Do not generalize or add features merely on speculation about the future
Solve the problems you know need solving now, and do not generalize merely on the speculation that it might be needed in the future. Solve future problems once they actually appear and their shape is known.
> Sources: **GEP** review/reviewer/looking-for §"Complexity · **MS** code-reviews/process-guidance/reviewer-guidance · **DLR** EDI.10, §Design and Implementation [AC1+] · **CC** Add an adversarial review step, Callout — 4 sources.
> Note: Some sources ask you to consider extensibility in advance [N7009H][TW][JOSS]. The two are compatible if such extensibility is treated as a deliberate choice based on requirements.

### G5.7 (SHOULD) Check inputs and assumptions, and stop rather than silently continue when they are invalid
At startup and at function entry, check that inputs are within valid ranges, and raise an error on impossible inputs instead of silently processing them. Check behavior with assertions and precondition and postcondition checks, and stop immediately if there is an error. Decide how much checking to do according to the consequences of incorrect results.
> Sources: **TW17** Rule 3; Rule 5 · **W14** Practice 5a, §Plan for Mistakes · **N7009H** §5.4.2 [Rec. 4.2.2c] · **TW** testing-unittest §"Unit Testing Tips; reviewing-checklist §"Security of new codes — 4 sources.

### G5.8 (SHOULD) Handle errors explicitly, and on failure give messages that show the cause and the remedy
Check the return values and errors of library calls, and log errors or report them to the user. On failure, show where it failed, what the problem is, and how to fix it or where to look in the documentation.
> Sources: **MS** code-reviews/process-guidance/reviewer-guidance · **TW** reviewing-checklist §"Security of new codes · **N7009H** §5.4.2 [Rec. 4.2.2c] · **N7009** 4.2.2c · **RSQ** documenting_code §"Write meaningful error messages — 4 sources.
> Note: No source explicitly says "do not swallow exceptions" or "use a logging mechanism rather than print".

### G5.10 (SHOULD) Consider testability in the design
From the design stage, consider whether components can be set to a defined state and their effects observed, and divide the software into independently working parts with clear and few dependencies.
> Sources: **DLR** ESA.3, §Software Architecture [AC2+] · **TW** reviewing-checklist §"Classes and types; testing §"Motivation and Background"; testing-unittest · **MS** automated-testing/README §"Build for Testing · **W14** §Plan for Mistakes — 4 sources.

### G5.11 (SHOULD) Follow the existing architecture, patterns and terminology
Model changes on the architecture and coding patterns of the existing codebase, and use the terminology defined in the requirements and architecture consistently in the implementation.
> Sources: **CC** Provide specific context in your prompts, "Reference · **MS** code-reviews/process-guidance/reviewer-guidance · **DLR** EDI.4, §Design and Implementation [AC2+] — 3 sources.

### G5.13 (SHOULD) Record major design decisions together with alternatives and reasons
Document, at least briefly, the design concepts and decisions that cannot be read from the code (how components interact, the parallelization approach, why a library was chosen, rejected options and the reasons for rejecting them), date them, and keep them in the repository.
> Sources: **MS** engineering-fundamentals-checklist §"Design Reviews; documentation/README §"Challenges · **JOSS** RC §Software paper content, Software design [JOSS] (must) · **DLR** ESA.7, §Software Architecture [AC2+]; ESA.2, §Software Architecture [AC1+] · **N7009H** §5.3.1 c(4); §5.3.1 c(2) · **W14** Practice 7, §Document Design and Purpose, Not Mechanics · **DX** explanation.rst / Provide context · **RSQ** software_maintenance §"How do you maintain your — 7 sources.
> Note: No source specifies a format for the record (such as ADRs).

### G5.14 (SHOULD) Refactor in small, continuous steps, and clean up the complexity you introduce within the same change
Do small refactorings on the spot under the safety net of tests, and clean up complexity introduced by a change within that change rather than postponing it. Do not rewrite everything at once for large improvements; split them into small steps that can be reviewed and verified individually.
> Sources: **DLR** §Design and Implementation; EDI.5, §Design and Implementation [AC2+] · **RSQ** software_maintenance §"How do you track and manage · **GEP** review/reviewer/pushback §"Cleaning It Up Later (must) — 3 sources.

### G5.15 (SHOULD) Use meaningful, consistent names
Give functions, variables, classes and modules consistent names that convey what they are and what they do, and follow the language's naming conventions. Avoid single-letter or generic names (tmp, results2) except for loop counters in short scopes.
> Sources: **W14** Practice 1b · **W17** 2f, §Software · **GEP** review/reviewer/looking-for §"Naming · **MS** code-reviews/process-guidance/reviewer-guidance · **DLR** EDI.1, §Design and Implementation [AC1+] · **RSQ** writing_readable_code §"Why is it important to make · **OSG** starting-a-project / Your pre-launch checklist — 7 sources.

### G5.16 (MUST) In code that is shared or distributed, consistently follow agreed style rules
Consistently apply rules agreed in the team (at least for formatting and comments), based on the language community's style guide, and match the existing style when making changes.
> Sources: **W14** Practice 1c · **DLR** EDI.1, §Design and Implementation [AC1+] · **GEP** review/reviewer/standard §"Principles"; review (must); review/reviewer/standard §"Principles"; review · **CP** §Contributing PRs · **RSQ** writing_readable_code §"Why is it important to make · **OSG** starting-a-project / Your pre-launch checklist · **MS** CI-CD/continuous-integration §"Code Style Checks (must) — 7 sources.
> Note: Sources differ on what to do when the style guide and existing code disagree. GEP treats the guide as absolute [GEP], while RSQ and CP prioritize consistency with existing code [RSQ][CP]. A style guide is not required for a project in its launch phase [OSG].

### G5.17 (SHOULD) Automate style checks and static analysis with tools
Check compliance with simple style rules automatically with formatters, linters and static analysis tools, and build them into CI pass/fail checks or pre-commit hooks. Agree on and document what is enforced and how warning suppressions are handled.
> Sources: **DLR** EDI.7, §Design and Implementation [AC2+]; ESA.10, §Software Architecture [AC3+] · **MS** CI-CD/continuous-integration §"Code Style Checks (must) · **RSQ** ci_cd §"Considerations", §"Solutions"; static_analysis; static_analysis §"How do you use static analysis to · **OSG** best-practices / Use tools to automate basic · **NL** best_practices, §Code Quality / EditorConfig; best_practices, §Code Quality — 5 sources.
> Note: This does not apply to throwaway scripts or exploratory notebooks [RSQ]. Static analysis is not a substitute for testing or review [RSQ]. Overly complex rules become a barrier to contribution [OSG].

### G5.18 (SHOULD) Write "why" in comments, and if an explanation is needed, first rewrite the code
Use comments mainly to explain why the code exists, and do not write comments that merely restate what it does. If a long explanation becomes necessary, rewrite the code more simply before writing the explanation. Regular expressions and complex algorithms are exceptions.
> Sources: **GEP** review/reviewer/looking-for §"Comments; review/reviewer/comments §"Accepting Explanations · **MS** code-reviews/process-guidance/reviewer-guidance §"Style · **RSQ** documenting_code §"Document as you code · **W14** Practice 7a; Practice 7b · **DLR** EDI.1, §Design and Implementation [AC1+] — 5 sources.

### G5.19 (SHOULD) Describe the purpose, inputs, outputs and assumptions at the top of functions and programs
At the top of each function, method and program, describe in a form embedded in the software itself, such as a docstring, what it does, its inputs and outputs (including units), and its preconditions and postconditions. Comment on edge cases and pitfalls where they occur, writing these while you code and keeping them up to date. However short the program, put a description and a usage example at the top.
> Sources: **W14** Practice 7, §Document Design and Purpose, Not Mechanics; Practice 7a · **W17** 2a, §Software · **TW** reviewing-checklist §"Function/Method declarations · **CP** §Contributing PRs · **RSQ** documenting_code §"Document as you code · **OSG** starting-a-project / Your pre-launch checklist · **DLR** EDI.1, §Design and Implementation [AC1+] · **GEP** review/reviewer/looking-for §"Comments — 8 sources.
> Note: No source makes docstrings mandatory for every public function (all are should or weaker).

### G5.20 (SHOULD) Do not leave unused or commented-out code
Do not leave unused code fragments or debugging code (version control keeps old versions), and do not commit tests that are commented out. Switch behavior with conditionals, not by commenting code out.
> Sources: **TW** vcs §"Motivation; reviewing-checklist §"Function/Method definitions · **CP** §Guidance on contributing PRs · **MS** CI-CD/continuous-integration §"Avoid Commenting Out · **W17** 2h, §Software — 4 sources.

### G5.23 (MUST) If you use SemVer, declare the public API and distinguish it from internal names
For software versioned with SemVer, declare the public API precisely and comprehensively, in the code or in the documentation. Make internal names not intended for external use distinguishable, and consider carefully before exporting generic names that could clash with existing names (both of these are (SHOULD)).
> Sources: **SEMVER** Specification §1 [if SemVer] (must); Specification §1 · **CP** §Definition of Public API; §Changes that are not considered breaking, New exports — 2 sources.
> Note: Only TW says to keep the public API "minimal"; SemVer and CP merely require it to be declared.

### G5.28 (SHOULD) Do not waste resources; choose algorithms and data structures suited to the scale
Avoid unnecessary CPU and memory use and redundant disk writes, and choose data structures and algorithms that scale appropriately with the size of the problem.
> Sources: **RSQ** quality §"Aspects of Research Software Quality · **MS** code-reviews/process-guidance/reviewer-guidance · **NL** technology/datasets, §Data processing libraries on a — 3 sources.

### G5.29 (SHOULD) If performance is a requirement, write dedicated performance tests
When run time or resource usage is a requirement, design dedicated tests that measure the performance of specific code or subsystems, rather than adding timers to existing tests. Performance is nearly orthogonal to other kinds of tests; code can become ten times slower even when all other tests pass.
> Sources: **BSSw** §Types of Tests, Performance Tests; §Types of Tests, Performance Tests (must) · **TW** testing-unittest §"Unit Testing Tips · **MS** automated-testing/README §"The Fundamentals; automated-testing/README §"Build for Testing · **RSQ** testing_software §"What types of software tests · **DLR** EST.3, §Software Test [AC3+] — 5 sources.

## G6. Testing

Tests do not prove correctness; they give confidence that code works as expected and ensure that it keeps working after later changes [DLR][RSQ][TW][NL].

### G6.1 (SHOULD) Write tests for code that is used repeatedly or shared
For code used in research, write tests that confirm it works as expected and ensure that it keeps working after later changes. Even if you cannot cover existing code all at once, start by writing some tests, and add tests to any new code you write. Do not use lack of time as a reason to skip tests. For one-off exploratory code, follow G6.3.
> Sources: **NL** best_practices, §Testing · **TW** testing §"Summary; testing §"Motivation and Background · **MS** automated-testing/README §"Why Testing; automated-testing/README §"The Fundamentals · **OSG** best-practices / Require tests and other checks… · **W14** §Plan for Mistakes · **DLR** §Software Test (must) · **RSQ** testing_software §"What types of software tests · **JOSS** RC §Tests; SUB §Submission Process · **TW17** Rule 9 — 9 sources.
> Note: W17 drops unit testing from its recommendations for novices because it is rarely adopted in solo exploratory work [W17]. TW17 says that for code that answers a question only once, "running it on that data" may be the only reasonable test [TW17]. The must in DLR concerns deciding on a test strategy; no source makes writing tests itself a must. If your organization makes it mandatory, place it in Appendix C.

### G6.2 (SHOULD) Provide simple examples with known inputs and outputs
Provide a simple example or test dataset that confirms that a known correct output is obtained for a known simple input, so that anyone who installs the software can run it and confirm that the software works. No source, from beginner-oriented guides to standards, opposes this.
> Sources: **W17** 2i, §Software; 3a, §Collaboration · **TW17** Rule 9 · **TW** testing-overview §"Overview of Testing Types", §"Types · **JOSS** RC §Functionality; RC §Tests · **N7009** 4.2.2e · **N7009H** §5.6.2.1 — 6 sources.
> Note: The sources agree and none opposes this, but none states it as a must. If your organization makes it mandatory, place it in Appendix C.

### G6.3 (SHOULD) Raise the level of testing and automation step by step according to scale and use
Introduce unit tests, coverage measurement, CI, and build tools step by step according to the project's size, the number of collaborators, and its use by others. For a one-off analysis, you may start with the simple examples of G6.2 and a script for rerunning it.
> Sources: **W17** What we left out, Build tools; What we left out, Unit tests · **TW17** §Conclusion · **RSQ** life_cycle §"Research Software Lifecycle & Three-Tier; three_tier_view §"History and context · **TW** code-reuse §"Reusable Code"; code-reuse-overview · **DLR** §Application Class 0; §Application Class 2 — 5 sources.

### G6.4 (SHOULD) Rerun existing tests automatically after every change
Every time you change the code, automatically rerun the existing tests to confirm that a change in one place has not broken other parts.
> Sources: **W14** §Plan for Mistakes · **TW** testing-acceptance-regression §"Regression testing · **N7009H** §5.5.2.2 a · **RSQ** quality §"Aspects of Research Software Quality · **DLR** EST.2, §Software Test [AC2+] · **MS** automated-testing/README §"The Fundamentals"; CI-CD — 6 sources.
> Note: The sources agree and none opposes this, but none states it as a must. If your organization makes it mandatory, place it in Appendix C.

### G6.5 (MUST) In collaborative development, include tests for behavior changes in the same change
For a PR that adds or changes behavior, add new tests or update existing ones that verify it, and include them in the same PR. Do not settle for "tests later."
> Sources: **CP** §Contributing PRs (must); §Guidance on contributing PRs · **GEP** review/reviewer/looking-for §"Tests"; review/developer · **MS** automated-testing/README §"The Fundamentals; CI-CD/continuous-integration §"Deliver Quickly and · **TW** reviewing-checklist §"For the coder; testing-guidance §"Write Tests - Any Tests! — 4 sources.

### G6.6 (SHOULD) Turn discovered bugs into test cases
When you find a bug, first write a failing test that reproduces it, and then fix the bug. Keep that test to detect any recurrence. DLR requires this for nontrivial errors at DLR application class 3 and above.
> Sources: **W14** Practice 5c, §Plan for Mistakes · **DLR** EST.5, §Software Test [AC3+] · **TW** testing-unittest §"Unit Testing Tips · **CC** Provide specific context in your prompts, "Describe · **CP** §Accidental breaking releases, Example — 4 sources.

### G6.7 (SHOULD) Make tests automatic and self-checking
Do not throw away checks done with print statements or by eye; turn them into test functions with built-in comparisons against expected values, and collect them into a test suite that runs automatically. Do not insert manual steps into judging the results.
> Sources: **TW** testing §"Motivation and Background", §"The advantages; testing-acceptance-regression §"Regression testing · **RSQ** testing_software §"What types of software tests; testing_software §"How do we test our code?", §"How do · **W14** §Plan for Mistakes · **DLR** EST.1, §Software Test [AC2+] — 4 sources.

### G6.8 (SHOULD) Use an existing test framework
Use a widely used unit-testing library suited to the language, so that preparing inputs, running tests, and reporting results are handled uniformly.
> Sources: **W14** Practice 5b, §Plan for Mistakes · **TW** testing-guidance §"Use a Testing Framework · **RSQ** testing_software §"How do we test our code?", §"How do · **BSSw** §Granularity of Tests, Unit Tests · **DLR** EST.2, §Software Test [AC2+] — 5 sources.

### G6.9 (SHOULD) Build on unit tests and supplement them with integration and system tests
Center your testing on fast unit tests that target individual functions and classes, and supplement them with integration tests that check interactions between components and system tests that exercise the whole system at the level of user operations. Do not rely on system-level tests alone for regression testing.
> Sources: **DLR** EDI.3, §Design and Implementation [AC2+]; §Software Test · **BSSw** §Granularity of Tests, Unit Tests (must); §Granularity of Tests, Integration Tests · **TW** testing-overview §"Overview of Testing Types", §"Types; testing-unittest §"Unit Testing Tips", §"Unit Testing · **RSQ** testing_software §"What types of software tests · **MS** engineering-fundamentals-checklist §"Testing; automated-testing/README §"The Fundamentals"; CI-CD · **W14** §Plan for Mistakes · **GEP** review/reviewer/looking-for §"Tests"; review/developer — 7 sources.
> Note: No source specifies a ratio.

### G6.10 (SHOULD) Distinguish the "type" and the "granularity" of tests, and combine them
Treat what each test checks (verification, acceptance/validation, no-change (characterization), performance) as an axis separate from granularity (unit, integration, system), and combine them according to purpose (for verification tests in scientific computing, see S3).
> Sources: **BSSw** §Definitions and Categories of Tests; §Types of Tests, Verification Tests · **RSQ** testing_software §"What types of software tests · **MS** automated-testing/README §"Map of Outcomes to Testing · **TW** testing-overview §"Overview of Testing Types", §"Types; testing-acceptance-regression §"Acceptance testing — 4 sources.

### G6.11 (SHOULD) Do not judge floating-point output by bitwise equality
In tests that compare floating-point results, do not assume exact bitwise equality; determine the tolerance and state it explicitly in the tests and the documentation (see S4 for details).
> Sources: **BSSw** §Types of Tests, No-Change/Characterization Tests · **TW17** Rule 10 · **CP** §Changes that are not considered breaking — 3 sources.

### G6.12 (SHOULD) Make tests deterministic, and find and fix the cause of flaky tests
Make tests produce the same result for the same input regardless of environment. Treat a test that fails or passes without any change as a sign of a defect, and identify and fix the cause (dependence on external services, interactions between threads, and so on). When using random numbers, make it possible to supply a seed (S6).
> Sources: **DLR** EST.6, §Software Test [AC3+] · **RSQ** testing_software §"What types of software tests · **TW17** Rule 10 — 3 sources.
> Note: No source recommends automatically rerunning failed tests until they pass.

### G6.13 (SHOULD) Keep tests independent of one another, and replace external dependencies with test doubles in unit tests
Do not make any test depend on the state of other tests. Where possible, replace dependencies on networks or external services with stubs or mocks. Do not add tests to the production environment.
> Sources: **RSQ** testing_software §"How do we test our code? (must); testing_software §"What types of software tests · **TW** testing-guidance §"Use test doubles/stubs/mocking; testing-unittest §"Unit Testing Tips · **MS** automated-testing/README §"The Fundamentals"; CI-CD · **DLR** EST.6, §Software Test [AC3+] — 4 sources.

### G6.14 (SHOULD) Keep unit tests fast
Keep unit tests fast to build and fast to run. For long-running checks, set a maximum timeout so that they fail early.
> Sources: **BSSw** §Granularity of Tests, Unit Tests (must) · **RSQ** testing_software §"What types of software tests · **TW** testing-guidance §"Consider how long it takes your · **MS** CI-CD/continuous-integration §"End-to-End Integration — 4 sources.
> Note: No source gives a specific time limit.

### G6.15 (SHOULD) Tier how often tests run according to their run time
Run light tests on every change, and run time-consuming tests on a schedule, such as nightly or weekly. Keep running them periodically even while development is paused, to detect breakage caused by external changes.
> Sources: **BSSw** §Discussion 2 · **TW** testing-guidance §"Run the tests"; testing-checklist; testing-guidance §"Consider how long it takes your · **RSQ** software_maintenance §"How do you maintain your — 3 sources.

### G6.16 (SHOULD) Run tests from early in development, and run them often
Write and run tests alongside the code from the start of development, and do not leave long gaps between runs.
> Sources: **TW** testing-guidance §"Run the tests"; testing-checklist · **RSQ** testing_software §"What types of software tests · **N7009H** §5.4.1 · **W14** §Plan for Mistakes — 4 sources.

### G6.17 (SHOULD) Have a person check that the tests themselves are valid
In review, have a person check whether the tests actually fail when the code breaks, whether they produce false positives, and whether they rest on reasonable assumptions. Treat tests as code that must also be maintained.
> Sources: **GEP** review/reviewer/looking-for §"Tests (must); review/reviewer/looking-for §"Tests · **MS** code-reviews/process-guidance/reviewer-guidance §"Tests; code-reviews/process-guidance/reviewer-guidance intro · **TW** reviewing-checklist §"For the reviewer; reviewing-checklist §"Tests · **RSQ** code_review §"Consideration - what to look for in a · **CP** §Changes that are not considered breaking, string — 5 sources.

### G6.18 (SHOULD) Test boundary conditions, edge cases, and error conditions, not only the normal path
Test boundary conditions and edge cases, and test that invalid input produces errors. Prioritize critical execution paths, complex logic, and parts that could affect the correctness of reported results.
> Sources: **RSQ** testing_software §"What types of software tests · **MS** code-reviews/process-guidance/reviewer-guidance §"Tests · **GEP** review/reviewer/looking-for §"Functionality · **TW** reviewing-checklist §"Tests; testing-unittest §"Unit Testing Tips", §"Unit Testing — 4 sources.

### G6.19 (SHOULD) Keep each test focused, and name it so that its purpose is clear
Focus each test on a single condition, give it a name that conveys its purpose, and structure it so that the cause of a failure is easy to trace.
> Sources: **RSQ** testing_software §"What types of software tests · **TW** testing-guidance §"Aim to have a good code coverage; testing-guidance §"Use test doubles/stubs/mocking · **GEP** review/reviewer/looking-for §"Tests — 3 sources.
> Note: No source limits tests by number, such as "one assertion per test."

### G6.20 (SHOULD) Measure coverage to find gaps (do not make numeric targets an obligation)
Measure coverage and use it as a clue for finding important parts that are untested. Do not aim solely for 100%, and keep in mind that high coverage does not guarantee correctness.
> Sources: **RSQ** testing_software §"What types of software tests; quality_dimensions intro · **TW** testing-guidance §"Aim to have a good code coverage · **MS** CI-CD/continuous-integration §"Code Coverage Checks; engineering-fundamentals-checklist §"Testing · **DLR** EST.8, §Software Test [AC2+] · **N7009H** §5.5.2.2 a · **BSSw** §Test Analysis Tools, Code Coverage; §Discussion 4 — 5 sources.
> Note: There is no consensus on numeric targets. The MS checklist gives ">90% if possible," and the MS CI page only gives the example that "many tools fail the build below 80%"; the other sources give no figures. W17 does not recommend even measuring coverage for novices [W17].

### G6.21 (SHOULD) Manage test code, test data, and run conditions in the repository
Keep test code under version control as part of the codebase, and ideally keep the required data, procedures, and environment parameters in the repository as well. For data that is too large, store a reference to it. Make test data realistic, reflecting the messiness of real data.
> Sources: **RSQ** testing_software §"Why is it important to test our · **DLR** EST.10, §Software Test [AC1+] · **MS** CI-CD/continuous-integration §"Test Automation Data · **N7009** 4.2.2e · **N7009H** §5.5.2.3 [M&S 16]; §5.5.3.1 [M&S 18] · **TW** testing-guidance §"Test Realistic Cases; testing §"Motivation and Background", §"The advantages · **W17** 2i, §Software — 7 sources.
> Note: The sources agree and none opposes this, but none states it as a must. If your organization makes it mandatory, place it in Appendix C.

### G6.22 (SHOULD) Make it possible to run all tests with a single command, and make the results easy to understand
Make it possible to run all tests with a single command, and document how to run them and what resources they require. Make the output understandable to non-specialists.
> Sources: **MS** CI-CD/continuous-integration §"End-to-End Integration; engineering-fundamentals-checklist §"Developer · **TW17** Rule 9 · **TW** testing-guidance §"Document the tests and how to run · **OSG** best-practices / Require tests and other checks… · **W17** 3a, §Collaboration — 5 sources.

### G6.23 (SHOULD) Test in an environment close to operational use and on all supported platforms
Test basic functionality in an environment close to the operational one, and always do so before a release. Confirm that the software works on each supported environment (operating system, etc.).
> Sources: **DLR** EST.4, §Software Test [AC1+]; §Automation and Dependency Management (must) · **MS** CI-CD/continuous-integration §"Isolated Environments · **RSQ** quality §"Aspects of Research Software Quality · **TW17** Rule 7 · **N7009** App. E.3.3 · **TW** testing-acceptance-regression §"Regression testing — 4 sources.

### G6.24 (SHOULD) Before refactoring, prepare tests that pin down the current behavior
When refactoring code that has no tests, first prepare tests that check its current behavior, and confirm that the behavior is unchanged before and after the refactoring.
> Sources: **RSQ** software_maintenance §"How do you track and manage · **GEP** review/developer/small-cls §"Keep related test code in; review/reviewer/looking-for §"Tests"; review/developer · **DLR** EDI.5, §Design and Implementation [AC2+] · **BSSw** §Types of Tests, No-Change/Characterization Tests — 4 sources.

### G6.26 (SHOULD) Use manual testing as a supplement, and document the procedure
You may check manually anything that requires human judgment, such as GUIs, visual output, and usability. In that case, document the procedure and the expected results, and turn the parts that can be repeated into automated tests.
> Sources: **RSQ** testing_software §"How do we test our code?", §"How do · **DLR** EST.3, §Software Test [AC3+]; EST.4, §Software Test [AC1+] · **JOSS** RC §Tests — 3 sources.

### G6.27 (SHOULD) For code under long-term development, decide on a test strategy and review it periodically
Based on the quality requirements, decide and document which test levels to perform, at what intensity, when, and in which environments, and review this periodically.
> Sources: **DLR** EST.1, §Software Test [AC2+]; §Software Test (must) · **MS** automated-testing/README §"Map of Outcomes to Testing · **RSQ** testing_software §"What types of software tests — 3 sources.

## G7. Automation and CI

### G7.1 (MUST) Let the computer do repetitive work
Instead of repeatedly typing the same commands by hand, save the commands you used to a file as a script, and consistently automate routine tasks (especially each step of the build).
> Sources: **W14** Practice 2a, §Let the Computer Do the Work; Practice 2b, §Let the Computer Do the Work · **DLR** §Automation and Dependency Management (must); §Change Management · **MS** CI-CD/continuous-integration §"Goals — 3 sources.

### G7.2 (SHOULD) Make it possible to build and regenerate with a single command
Make it possible to build from source and dependencies, or to regenerate results from raw data, with a single command that does not depend on a particular IDE. Use a build tool (such as Make) or a script that reruns everything.
> Sources: **W14** Practice 2c, §Let the Computer Do the Work · **MS** CI-CD/continuous-integration §"Build Task", §"Build · **DLR** EAA.1, §Automation and Dependency Management [AC1+] · **TW** make §"Summary", §"Why use Make for Reproducibility?; make §"Why use Make for Reproducibility? · **W17** What we left out, Build tools · **TW17** Rule 6 — 6 sources.
> Note: W17 drops build tools such as Make from its recommendations for novices, saying that a shell script that reruns everything achieves the same effect.

### G7.3 (SHOULD) Set up CI to build and test automatically on every push or PR
On every push or PR to a shared repository, automatically run the build and the tests in an integration environment. Integrate changes frequently in small units.
> Sources: **RSQ** quality §"Aspects of Research Software Quality · **MS** engineering-fundamentals-checklist §"CI/CD", §"Code; CI-CD/continuous-integration intro · **OSG** best-practices / Require tests and other checks… · **DLR** EAA.8, §Automation and Dependency Management [AC2+] · **TW** testing-guidance §"Run the tests"; testing-checklist · **JOSS** RC §Tests; RC §Good practices — 6 sources.
> Note: W17 drops CI from its recommendations, saying that CI is useful for large projects but offers little return for small projects built for a specific analysis [W17]. JOSS does not require CI and also accepts documented manual procedures [JOSS].

### G7.4 (SHOULD) In collaborative development with CI, make passing CI a condition for merging
Set protection rules or merge conditions on the main branch so that changes that fail the build, the tests, or static checks cannot be merged.
> Sources: **CP** §Reviewing, Approving, and Merging PRs; §Guidance on automatically enforcing guidelines · **MS** CI-CD/continuous-integration §"Branch Policy; engineering-fundamentals-checklist §"CI/CD", §"Code · **OSG** best-practices / Require tests and other checks… · **TW** reviewing-checklist §"For the coder — 4 sources.
> Note: The sources agree and none opposes this, but none states it as a must. If your organization makes it mandatory, place it in Appendix C.

### G7.5 (SHOULD) Run the build and tests locally before submitting changes
Before committing or opening a PR, run the build, tests, and linters locally, and confirm that all tests, old and new, pass.
> Sources: **MS** source-control/git-guidance/README §"Commit Best (must); automated-testing/README §"The Fundamentals · **TW** reviewing-checklist §"For the coder · **OSG** best-practices / Require tests and other checks… — 3 sources.

### G7.6 (SHOULD) Fix broken builds immediately, and make failures visible
Fix problems found by the integration build ahead of other work, and keep the main branch in a working state at all times. Make sure that CI failures are reported to the people involved, together with the logs.
> Sources: **DLR** EAA.8, §Automation and Dependency Management [AC2+] · **MS** engineering-fundamentals-checklist §"CI/CD; code-reviews/pull-requests §"General Process", §"Best · **GEP** review/developer/small-cls §"Don't Break the Build · **TW** testing-guidance §"Run the tests"; testing-checklist — 4 sources.

### G7.7 (SHOULD) Run linters and static analysis in CI in addition to tests
Include formatting checks against the agreed coding standard, linters, and static analysis as pass/fail criteria, and take minor comments out of human review.
> Sources: **MS** engineering-fundamentals-checklist §"CI/CD", §"Code; CI-CD/continuous-integration §"Code Style Checks (must) · **RSQ** ci_cd §"Considerations", §"Solutions"; static_analysis · **DLR** EDI.7, §Design and Implementation [AC2+] · **CP** §Guidance on automatically enforcing guidelines · **OSG** best-practices / Use tools to automate basic — 5 sources.

### G7.8 (SHOULD) Enforce rules that must always be followed through mechanisms, not documents
Enforce rules that must be followed every time without exception (passing tests, formatting, etc.) through automated checks such as merge conditions, CI gates, and hooks, rather than relying on instructions or documents.
> Sources: **CP** §Guidance on automatically enforcing guidelines · **CC** Set up hooks · **RSQ** testing_software §"What types of software tests · **MS** code-reviews/process-guidance/README §"Automate — 4 sources.

## G8. Documentation

### G8.1 (MUST) Explain the purpose and features in the README
State in the README what the software does, what problem it solves, and who it is for. Also state its status explicitly, for example that it is unfinished, does not accept contributions, or is not ready for production use.
> Sources: **RSQ** documenting_software_project §"How to document your (must); creating_good_readme §"What sections should you · **TW17** Rule 2; Rule 2, README item 1 · **OSG** starting-a-project / Launching your own open source; starting-a-project / Writing a README · **W17** 3a, §Collaboration · **TW** open-checklist §"Open Source Software"; compendia; reviewing-checklist §"Documentation · **JOSS** RC §Documentation; RC §Documentation, A statement of need · **MS** documentation/README §"Challenges; engineering-fundamentals-checklist §"Source Control — 7 sources.

### G8.2 (MUST) Document installation steps and dependencies
Document the dependencies (required hardware, tested operating systems, libraries) and the installation steps. Do not assume prior knowledge in the steps, and show every command to run. If the software cannot be installed from scratch using only the documentation, update the documentation.
> Sources: **RSQ** documenting_software_project §"How to document your (must); creating_good_readme §"Installation process · **TW17** Rule 2, README item 2; Rule 2, README item 3 · **TW** code-reuse-overview §"Documentation (must); open-checklist §"Open Source Software"; compendia · **JOSS** RC §Good practices; RC §Documentation, Installation instructions · **DLR** ERM.2, §Release Management [AC1+] · **NL** best_practices, §Documentation — 6 sources.

### G8.3 (MUST) Include usage examples that can be tried right away
Include in the README at least one runnable example that lets users start using the software right away. If possible, make it an example that solves a real analysis problem.
> Sources: **TW17** Rule 2, README item 5 · **W17** 3a, §Collaboration; 2a, §Software · **JOSS** RC §Good practices; RC §Documentation, Example usage · **RSQ** documenting_code §"Include usage examples", §"Include; documenting_software_project §"How to document your (must) · **TW** code-reuse-overview §"Documentation (must) · **NL** best_practices, §Documentation — 6 sources.

### G8.4 (MUST) Update documentation together with changes
For changes that affect the public API, update the public documentation in the same change. For changes that affect how the software is built, installed, used, or released, also update the relevant documentation in the same change, and confirm in review that the update has been made (these are (SHOULD)). When removing or deprecating code, consider removing the corresponding documentation as well.
> Sources: **CP** §Contributing PRs (must) · **GEP** review/reviewer/looking-for §"Documentation · **MS** code-reviews/process-guidance/reviewer-guidance §"Pull · **RSQ** documenting_software_project §"How to document your · **W14** Practice 7c · **DLR** ESA.9, §Software Architecture [AC2+] — 6 sources.

### G8.5 (SHOULD) Provide reference documentation for the public API
For libraries and tools that others call, document the public API. Ideally, give every public function an input/output example; at a minimum, document the core API.
> Sources: **JOSS** RC §Good practices; RC §Documentation, API documentation · **NL** best_practices, §Documentation · **SEMVER** FAQ "Documenting the entire public API is too much; Specification §1 · **CP** §Changes that are considered breaking · **GEP** review/reviewer/looking-for §"Comments · **RSQ** documenting_code §"Understand purpose and audience; documenting_code §"Document Command Line Interface — 6 sources.
> Note: W17 drops comprehensive documentation from its recommendations for novices and treats an explanatory comment at the top of each script as the first step [W17]. TW17 leaves documentation beyond the README and usage to the developer's discretion [TW17].

### G8.6 (SHOULD) Embed reference documentation in the code, generate it with tools, and version it in the repository
Write reference documentation in the source code (docstrings, structured comments), and format it with a documentation generator. Keep the documentation in the repository, version it in the same workflow as the code, and, if possible, generate and publish it in CI. Do not regard automatically generated reference material alone as sufficient documentation.
> Sources: **W14** Practice 7c · **RSQ** documenting_code §"Use automated documentation tools; documenting_code §"How to document your code? · **DLR** ERM.2, §Release Management [AC1+] · **DX** reference.rst / Reference as description — 4 sources.

### G8.7 (SHOULD) Provide different kinds of documentation for different purposes and readers
Provide separate documentation for each purpose and audience, such as for users, for developers, and for installation and deployment. Do not lean toward either internal implementation details only or a minimal quick start only.
> Sources: **NL** best_practices, §Documentation · **RSQ** documenting_code §"Understand purpose and audience · **MS** documentation/README §"What Documentation Should Exist; documentation/README intro, §"Goals · **TW** code-reuse-overview §"Documentation (must) · **DLR** §Software Architecture · **DX** start-here.rst / The four kinds of documentation — 6 sources.
> Note: Sources classify documentation differently (NL uses three levels; RSQ uses users, developers, and deployment; DX uses four kinds), and none mandates a particular classification.

### G8.8 (SHOULD) Update outdated documentation, delete it, or mark it as outdated
When documentation no longer matches the implementation, update it. If you cannot update it, delete it or mark it clearly as outdated. Outdated documentation can do more harm than no documentation.
> Sources: **OSG** best-practices / Documenting your processes · **NL** CONTRIBUTING, §Zen of the Guide 7–8 · **MS** documentation/README §"Challenges · **DLR** ESA.9, §Software Architecture [AC2+] · **RSQ** documenting_software_project §"How to document your — 5 sources.

### G8.9 (SHOULD) State contact information, how to contribute, the license, and acknowledgments in the README
State how to get help and current contact information, how contributions are accepted, the license and attribution, and acknowledgments of funders and contributors.
> Sources: **W17** 3a, §Collaboration · **OSG** starting-a-project / Writing a README; starting-a-project / Writing your contributing · **RSQ** creating_good_readme §"What sections should you · **TW** reviewing-checklist §"Documentation; licensing §"What, How and Where of License · **TW17** Rule 2, README item 6 · **DLR** ERM.2, §Release Management [AC1+] · **JOSS** RC §Documentation, Community guidelines — 7 sources.

### G8.10 (SHOULD) Document how to set up the development environment and how to run the tests
Describe in one place how to set up a local build environment, the required dependencies, and how to run the tests. If possible, provide a setup script.
> Sources: **MS** CI-CD/continuous-integration §"Document Local Setup · **DLR** EÄM.2, §Change Management [AC1+] · **W17** 3a, §Collaboration · **TW** testing-guidance §"Document the tests and how to run; open-checklist §"Open Source Software"; compendia · **OSG** best-practices / Require tests and other checks… — 5 sources.

### G8.11 (SHOULD) Do not postpone documentation; document continuously from early in development
Do not produce documentation all at once at the end; write it throughout development. Write a README even while the software is unfinished. Even a bulleted list is better than nothing.
> Sources: **MS** documentation/README intro, §"Challenges · **RSQ** documenting_code §"Document as you code · **OSG** starting-a-project / Writing a README; best-practices / Documenting your processes · **W17** 2a, §Software — 4 sources.

## G9. Versioning and releases

### G9.1 (SHOULD) Give each release distributed to others a unique version number and a tag
Give every release that you distribute to others or cite in a paper a unique version number, and make the originating commit identifiable with a version-control tag. Make the version number displayable by the software itself (TW17 only). For JOSS submissions, a tagged release made after review is complete is (MUST).
> Sources: **DLR** ERM.1, §Release Management [AC1+] · **TW17** Rule 4 · **RSQ** releasing_software §"How to Create Code Releases; using_version_control §"Why does version control · **JOSS** RC §Good practices; SUB §Review Process [JOSS] (must) · **NL** best_practices, §Releases · **SEMVER** FAQ "Is "v1.2.3" a semantic version? — 6 sources.

### G9.2 (SHOULD) Do not modify released versions, and keep past versions available
Do not change the contents of a version once it has been released. Release every fix as a new version. Keep past versions available. If you adopt SemVer, not changing the contents of released versions is (MUST).
> Sources: **SEMVER** Specification §3 [if SemVer] (must); FAQ "What do I do if I accidentally release… [if SemVer] (must) · **TW17** Rule 4 · **RSQ** fair_rs §"How can we make our software accessible? — 3 sources.

### G9.3 (SHOULD) Decide on and declare a versioning scheme (with SemVer as the first choice)
Decide which versioning scheme to use, and declare it in the README or the changelog. If you want to convey the distinction between breaking changes, new features, and bug fixes, use Semantic Versioning and link to the specification from the README.
> Sources: **RSQ** releasing_software §"How to Create Code Releases; software_maintenance §"How do you maintain your · **MS** source-control/component-versioning §"Version Numbers · **CP** §Guidance on Package Releases, Incrementing the · **SEMVER** Why Use Semantic Versioning? · **KC** Guiding Principles; intro CHANGELOG · **TW17** Rule 4 · **DLR** ERM.1 · **TW** renv §"What is a Computational Environment? — 5 sources.
> Note: RSQ also lists calendar versioning as an option.

### G9.4 (MUST) If you declare SemVer, increment version numbers according to its rules
Use version numbers of the form X.Y.Z, and increment MAJOR when you make backward-incompatible changes to the public API, MINOR for backward-compatible feature additions or deprecations, and PATCH for backward-compatible bug fixes only.
> Sources: **SEMVER** Specification §2 (must); Specification §6 (must) · **MS** source-control/component-versioning §"Version Numbers · **RSQ** software_maintenance §"How do you maintain your · **CP** §Guidance on Package Releases, Incrementing the · **CCOM** FAQ "How does this relate to SemVer? — 5 sources.

### G9.6 (SHOULD) Judge breaking changes against the public API, and state them explicitly
Judge whether a change is breaking by whether it breaks functionality of the declared public API, and state explicitly, at the time a PR is opened, that it contains a breaking change. ColPrac holds that specific floating-point values may change at any time and that users should rely only on approximate accuracy, and it regards changes that break documentation examples or tutorials as breaking (single source).
> Sources: **SEMVER** Specification §8 (must) · **CP** §Changes that are considered breaking; §Guidance on contributing PRs · **CCOM** Summary; FAQ "How does this relate to SemVer? · **MS** source-control/component-versioning §"Version Numbers · **RSQ** software_maintenance §"How do you maintain your — 5 sources.

### G9.7 (SHOULD) Announce deprecations, and remove only after at least one intervening release
When deprecating functionality of the public API, announce it in the documentation and the changelog, issue at least one release that contains the deprecation, and then remove the functionality in the next major version. Always list deprecations, removals, and breaking changes in the changelog.
> Sources: **SEMVER** FAQ "How should I handle deprecating functionality?; Specification §7 (must) · **CP** §Incrementing the package version · **KC** Ignoring Deprecations; Types of changes · **GEP** review/reviewer/looking-for §"Documentation — 4 sources.
> Note: No source specifies the length of the deprecation period.

### G9.9 (SHOULD) Maintain a changelog written for people to read
Keep in the repository a changelog (CHANGELOG.md) that records the notable changes in each version, and update it before release work. Put the newest version first, give each version its release date, and group changes by type (Added / Changed / Deprecated / Removed / Fixed / Security). Do not use a diff of commit logs as the changelog as is.
> Sources: **DLR** EÄM.6, §Change Management [AC2+] · **RSQ** releasing_software §"How to Create Code Releases; documenting_software_project §"How to document your · **KC** How do I make a good changelog? / Guiding Principles; Guiding Principles · **W17** 5f, Manual versioning · **JOSS** SUB §Pre-review screening criteria, 3 [JOSS] (must) — 5 sources.

### G9.10 (SHOULD) Do not overwrite a mistaken release; fix it with a new version
If you mistakenly release a backward-incompatible change in a non-major version, immediately release a new patch version that reverts the offending change, and decide how to proceed after that. Record the problematic version in the changelog, and mark yanked versions prominently. Add a test to prevent recurrence.
> Sources: **SEMVER** FAQ "What do I do if I accidentally release…; FAQ "What do I do if I accidentally release… (must) · **CP** §Accidental breaking releases; §Accidental breaking releases, Example · **KC** FAQ "What about yanked releases? — 3 sources.

### G9.11 (SHOULD) Provide release notes and documentation shipped with the release
Include in each release, or reference from it, at least installation instructions, usage instructions, contact information, and release notes. Write the release notes briefly for end users, based on the changelog, and list known issues that remain unfixed.
> Sources: **DLR** ERM.2, §Release Management [AC1+]; EÄM.6, §Change Management [AC2+] · **RSQ** releasing_software §"How to Create Code Releases; publishing_software · **N7009H** §5.5.5.3 Note — 3 sources.

### G9.12 (SHOULD) Document the release procedure, and automate most of it
Agree on and document the procedure for creating and approving releases, and automate most of it. Also define a shortened procedure for releasing fixes for critical errors quickly.
> Sources: **DLR** ERM.4, §Release Management [AC2+]; ERM.5, §Release Management [AC3+] · **MS** CI-CD/continuous-integration §"Branch Strategy; source-control/component-versioning §"Semantic · **CP** §Releases — 3 sources.

### G9.15 (SHOULD) Choose a distribution method suited to the intended users
Choose the distribution method (tags, the hosting service's release feature, package registries) according to the number and expertise of the intended users. Distribution through a registry is (MAY).
> Sources: **TW17** Rule 4 · **RSQ** packaging_software §"Considerations", §"Solutions · **NL** best_practices, §Packaging — 3 sources.

### G9.16 (SHOULD) Create a release that corresponds to the results of a paper, and pin it with a tag
When publishing a paper that used the software, release the software at the same time, tag the exact commit that produced the results, and cite that specific release in the paper (for archiving and DOIs, see S11).
> Sources: **TW17** Rule 4 · **NL** best_practices, §Releases · **RSQ** using_version_control §"Why does version control; life_cycle §"Archiving", §"Research Software Lifecycle · **W17** 2j, §Software · **JOSS** SUB §Review Process [JOSS] (must) — 5 sources.

## G10. Code review and collaboration

For collaborative development practices, the level the sources demand differs clearly with project size and number of people. W17 drops code review from its recommendations for novices and small groups [W17], ColPrac states that it is not suited to projects with a single maintainer [CP], and DLR requires structured review from DLR application class 3 [DLR]. What follows assumes a shared repository maintained by several people.

### G10.1 (SHOULD) Have someone other than the author review shared code before it is merged
Before shared code that several people change is merged into the main branch, have someone other than the author review it on a pull request. If review is not made a condition for merging, it soon stops happening.
> Sources: **W14** Practice 8a, §Collaborate; §Collaborate · **RSQ** code_review §"Solution - code review tips & tools · **MS** code-reviews/README intro, §"Goals"; code-reviews · **CP** §Reviewing, Approving, and Merging PRs (must); §Contributing PRs (must) · **DLR** EDI.11, §Design and Implementation [AC3+] · **JOSS** RC §Collaborative effort · **NL** best_practices, §Code Reviews; CONTRIBUTING, §Technical · **TW** testing-checklist §"Good practice checks"; reviewing — 8 sources.
> Note: Informal review is beneficial even at small scale [TW], and having students show their code at lab meetings is a lightweight alternative [W17].

### G10.2 (MUST) In repositories maintained by several people, do not approve your own changes
Require at least one approval from someone other than the author for merging, and enforce the required number of approvals mechanically through the repository settings.
> Sources: **CP** §Reviewing, Approving, and Merging PRs (must); §Reviewing, Approving, and Merging PRs · **MS** engineering-fundamentals-checklist §"Code Reviews; code-reviews/process-guidance/author-guidance §"Add · **DLR** EDI.11, §Design and Implementation [AC3+] — 3 sources.
> Note: The required number differs among sources (CP: one; MS: usually two; DLR: one or two).

### G10.3 (SHOULD) Keep changes small and focused on a single purpose
Make each PR the smallest self-contained unit that does only one thing, and err on the side of too small rather than too large. Put refactoring, code moves, and large-scale reformatting in separate PRs.
> Sources: **GEP** review/developer/small-cls §"Why Write Small CLs?; review/developer/small-cls §"What is Small? · **MS** code-reviews/process-guidance/README §"General; code-reviews/pull-requests §"Size Guidance", §"Best · **CP** §Guidance on contributing PRs — 3 sources.
> Note: No source sets an upper limit on the number of lines. GEP only offers as a rule of thumb that "100 lines is usually reasonable, and 1000 lines is usually too large" [GEP].

### G10.4 (SHOULD) Write the "what" and the "why" in the PR description
Write a title that conveys the content in one sentence, and describe the problem being solved, why this approach was chosen, the drawbacks of the approach, and related issues. Because the description becomes a permanent part of the history, give enough context to understand it without relying on external links.
> Sources: **MS** code-reviews/process-guidance/author-guidance · **TW** vcs-checklist §"Contribute to Someone Else's Project · **GEP** review/developer/cl-descriptions intro; review/developer/cl-descriptions §"First Line · **CP** §Guidance on contributing PRs — 4 sources.

### G10.5 (SHOULD) In review, look at the design first and give it the most weight
As a reviewer, check the design (the overall structure and its integration with the existing system), functionality, unnecessary complexity, adequacy of the tests, naming and comments, style, and updates to related documentation. Design is the most important aspect.
> Sources: **GEP** review/index §"What Do Code Reviewers Look For?; review/reviewer/looking-for §"Design · **MS** code-reviews/process-guidance/reviewer-guidance intro; code-reviews/process-guidance/reviewer-guidance · **RSQ** code_review §"Consideration - what to look for in a · **TW** reviewing-checklist §"For the reviewer · **DLR** EDI.11, §Design and Implementation [AC3+] — 5 sources.

### G10.7 (MUST) Clean up new complexity in the current PR, and file out-of-scope problems as separate issues
Clean up changes that introduce new complexity before merging, except in emergencies. Do not defer in-scope fixes that have been requested on the grounds of "doing it later." File existing out-of-scope problems as separate issues, and do not hold up the current PR.
> Sources: **GEP** review/reviewer/pushback §"Cleaning It Up Later (must); review/reviewer/pushback §"Cleaning It Up Later · **MS** code-reviews/process-guidance/author-guidance §"Be (must); code-reviews/process-guidance/author-guidance §"Be — 2 sources.

### G10.10 (SHOULD) Write comments respectfully, about the code rather than the person, and in the form of questions
Write review comments that are courteous, clear, and helpful. Write about the code rather than about the author, and prefer questions to assertions. Do not expect experts in a research field to have complete knowledge of advanced software engineering.
> Sources: **GEP** review/reviewer/comments §"Courtesy · **MS** code-reviews/process-guidance/reviewer-guidance · **RSQ** code_review §"Consideration - what not to look for in · **CP** §Guidance on reviewing PRs · **COC** Our Standards — 5 sources.

### G10.11 (SHOULD) Distinguish nits with labels such as "Nit:", and do not let them block progress
Mark comments that do not require action with "Nit:" (or Optional / FYI), so that the author knows they need not be addressed in this PR. Comments meant for learning may be left at any time, as long as they are labeled.
> Sources: **GEP** review/reviewer/standard intro; §"Mentoring; review/reviewer/comments §"Label comment severity · **MS** code-reviews/process-guidance/reviewer-guidance §"Be · **RSQ** code_review §"Consideration - what not to look for in · **CP** §Guidance on reviewing PRs — 4 sources.

### G10.13 (SHOULD) Set a target response time for reviews, and return the first response quickly
Set and document a target response time for review requests, and return the first response within it. When you cannot look at the whole change right away, say when you can, or suggest another reviewer. What matters is the speed of individual responses rather than the speed of the whole process.
> Sources: **GEP** review/reviewer/speed §"How Fast Should Code Reviews; review/reviewer/speed §"Speed vs. Interruption · **MS** engineering-fundamentals-checklist §"Code Reviews; code-reviews/process-guidance/README §"General · **OSG** best-practices / Communicate your expectations — 3 sources.
> Note: Targets differ greatly among sources. GEP sets a maximum of one business day for in-house full-time developers, while OSG says that volunteer maintainers should be honest about the time they have available.

### G10.15 (SHOULD) As an author, do not take criticism as a personal attack, and respond to every comment
Read the constructive intent in review criticism and respond to it. When you make a fix, mark the comment as resolved; when you do not, give a clear reason. For parts a reviewer says they cannot understand, first rewrite the code itself rather than settling for an explanation in the review tool.
> Sources: **GEP** review/developer/handling-comments §"Don't Take it; review/developer/handling-comments §"Think · **COC** Our Standards · **MS** code-reviews/process-guidance/reviewer-guidance; code-reviews/process-guidance/author-guidance §"Be — 3 sources.

### G10.16 (MUST) Do not respond to review comments in anger (single source)
Step away until you have calmed down, and then reply politely. Replies remain in the review tool permanently.
> Sources: **GEP** review/developer/handling-comments §"Don't Take it (must) — 1 sources.

### G10.21 (SHOULD) Self-check before submitting, and add annotations that help the review
Before submitting a PR, confirm that it meets the project's standards and that the existing and new tests pass locally. Point out in comments the reasons for the changes and the parts you especially want reviewers to look at.
> Sources: **TW** reviewing-checklist §"For the coder · **MS** code-reviews/pull-requests §"General Process", §"Best; code-reviews/process-guidance/author-guidance §"Use · **CP** §Guidance on contributing PRs — 3 sources.

### G10.22 (SHOULD) Leave fine points of style to tools, and do not debate them in review
Do not debate in review style preferences that the team has not agreed on. Leave formatting and linting to automation, and let human review focus on design and functionality.
> Sources: **RSQ** code_review §"Consideration - what not to look for in · **MS** code-reviews/process-guidance/README §"Automate · **TW** reviewing-checklist §"For the reviewer · **CP** §Guidance on automatically enforcing guidelines · **GEP** review/reviewer/standard §"Principles"; review — 5 sources.

### G10.23 (SHOULD) Agree as a team on the review procedure, roles, and checklist
Agree on and document the review roles and procedure, and review them periodically. A checklist is a source of ideas; items that do not apply may be ignored.
> Sources: **MS** engineering-fundamentals-checklist §"Code Reviews · **DLR** §Change Management; EÄM.1, §Change Management [AC2+] · **TW** reviewing-checklist §"Checklist for code review process · **NL** best_practices, §Checklists · **CX** Improve reliability with testing and review — 5 sources.

### G10.24 (SHOULD) Track issues, bugs, and ideas in one place
Record known bugs, open issues, and ideas for improvement in a single shared and visible place (a shared to-do list for small projects; centralized ticket management at DLR application class 2 and above). Write each item so that newcomers can understand it.
> Sources: **W14** Practice 8c, §Collaborate · **W17** 3b, §Collaboration · **RSQ** life_cycle §"Maintenance"; software_maintenance §"How · **DLR** EÄM.5, §Change Management [AC1+]; §Change Management · **N7009** 4.1.1.8 [M&S 51] [NASA scope] (must) — 5 sources.

### G10.25 (SHOULD) Record reproduction steps, severity, and affected versions in bug reports
Give change requests and bug reports an identifier, a short description, and the submitter's contact information, and for bugs also record how to reproduce them, their severity, and the affected versions. Projects that receive reports from outside should describe how to report in CONTRIBUTING and consider using templates.
> Sources: **DLR** EÄM.3, §Change Management [AC2+] · **MS** engineering-fundamentals-checklist §"Engineering; code-reviews/pull-requests §"General Process · **OSG** starting-a-project / Writing your contributing; best-practices / Be proactive — 3 sources.
> Note: No source makes templates mandatory.

### G10.26 (SHOULD) Discuss large changes in an issue before starting work
Discuss large changes and new features in an issue and reach agreement before starting work on them.
> Sources: **CP** §Guidance on contributing PRs · **NL** CONTRIBUTING, §For significant changes / additions · **TW** vcs-checklist §"Contribute to Someone Else's Project · **GEP** review/reviewer/navigate §"Step One: Take a broad view; review/developer/small-cls §"Can't Make it Small Enough · **OSG** best-practices / Be proactive — 5 sources.

### G10.28 (SHOULD) If you accept contributions, describe the contribution process in CONTRIBUTING
State that contributions are welcome, how to set up the environment and run the tests, how to report bugs, how to ask for support, and the technical requirements for contributions. It can be simple at first; add frequently asked questions later.
> Sources: **W17** 3a, §Collaboration · **OSG** starting-a-project / Writing your contributing · **RSQ** documenting_software_project §"How to document your · **JOSS** RC §Good practices; RC §Documentation, Community guidelines · **TW** open-checklist §"Open Source Software — 5 sources.

### G10.29 (SHOULD) Document the roles of contributors and maintainers
Document how contributions are reviewed and accepted, what kinds of contributions are accepted, the rules for approval and merging, when it is acceptable to send a reminder, and how much time can be devoted to maintenance, and link to this documentation from the README.
> Sources: **CP** intro; §Appendix: Marking a Repository as following ColPrac · **OSG** best-practices / Communicate your expectations · **MS** engineering-fundamentals-checklist §"Code Reviews — 3 sources.

### G10.30 (SHOULD) Decide on and publish the channels for communication, support, and feedback
Explicitly decide on the channels for communication among members and with external users, and provide a point of contact where users can report anomalous results or problems.
> Sources: **W17** 3c, §Collaboration · **RSQ** life_cycle §"Community Feedback"; software_maintenance · **JOSS** RC §Good practices; RC §Documentation, Community guidelines · **N7009** 4.2.2f — 4 sources.

### G10.31 (SHOULD) Hold discussions in shared venues, and record conclusions in PRs or issues
Hold discussions about the project in shared venues, such as issues and PRs, whenever possible. For decisions made in private conversations or meetings, also record what was agreed and why.
> Sources: **OSG** best-practices / Keep communication public · **MS** code-reviews/process-guidance/author-guidance §"Be · **GEP** review/reviewer/standard §"Resolving Conflicts · **JOSS** SUB §Review Process — 4 sources.

### G10.32 (SHOULD) Use code review for knowledge sharing and learning
Use review not only to find defects but also to share knowledge and learn within the team. Include members who are not familiar with the project or the language as reviewers. In labs where members change, this prevents knowledge from being lost when people leave.
> Sources: **MS** code-reviews/README intro, §"Goals"; code-reviews; code-reviews/process-guidance/author-guidance §"Add · **RSQ** code_review §"Why it matters in research?; code_review §"Consideration - what not to look for in · **W14** §Collaborate · **NL** best_practices, §Code Reviews · **GEP** review/reviewer/standard intro; §"Mentoring — 5 sources.

### G10.34 (SHOULD) Label issues for newcomers, and show ongoing contributors a path to responsibility
Label issues that new contributors can easily take on with a label such as "good first issue." Document the path by which ongoing contributors can gain merge rights and similar responsibilities.
> Sources: **OSG** best-practices / Share the workload; best-practices / Embrace mentorship · **RSQ** life_cycle §"Community Feedback"; software_maintenance · **CP** §Becoming a Collaborator — 3 sources.

### G10.35 (SHOULD) In projects with several participants, adopt an existing code of conduct early
Establish a code of conduct as early as possible, and follow it in all interactions. Do not write your own; adopt an existing code such as the Contributor Covenant, and place it at the repository root as CODE_OF_CONDUCT.
> Sources: **OSG** code-of-conduct / Establishing a code of conduct; code-of-conduct / Deciding how you'll enforce your · **RSQ** documenting_software_project §"How to document your · **CP** §Community Standards (must) · **COC** Our Pledge (must); Scope (must) — 4 sources.
> Note: Only OSG requires a code of conduct for "all projects"; RSQ limits it to projects with multiple contributors. A code of conduct that is not enforced is worse than none [OSG].

### G10.36 (MUST) Once you adopt a code of conduct, prepare to enforce it and protect reporters
In the code of conduct, state the expected behavior and the unacceptable behavior (sexualized language or conduct, trolling, insults or personal attacks, harassment, publishing others' private information without permission), its scope, the response to violations, and how to report them. Before any violation occurs, decide and publish who will enforce it and how, and fill in the blank for the reporting contact when you adopt it. Provide a way to report violations privately, and protect the privacy and safety of reporters.
> Sources: **OSG** code-of-conduct / Establishing a code of conduct; code-of-conduct / Deciding how you'll enforce your · **COC** Our Standards (must); Enforcement Responsibilities (must) — 2 sources.

### G10.37 (MUST) Investigate reports promptly and fairly, and communicate the conclusion and the reasons
Take every report seriously, and investigate it promptly and fairly. Understand what happened before taking action. Even when you judge that no violation occurred, tell the reporter so and explain why. Escalate measures in proportion to the impact, and resort to expulsion only when it is clear that the matter cannot be resolved otherwise.
> Sources: **COC** Enforcement (must); Enforcement Guidelines (must) · **OSG** code-of-conduct / Gather information about the; code-of-conduct / Your responsibilities as a maintainer (must) — 2 sources.

### G10.38 (SHOULD) Use welcoming, inclusive, and plain language
Use welcoming and inclusive language in documentation and interactions, and write in plain language for readers whose first language is not English.
> Sources: **OSG** starting-a-project / How you write (and code) affects · **COC** Our Pledge (must) · **MS** code-reviews/process-guidance/README §"General — 3 sources.

## G11. Security and secrets

### G11.1 (SHOULD) Keep secrets out of version control
Do not commit credentials such as passwords, private keys and API keys to the repository. Keep secrets in local private files and exclude them from tracking with .gitignore. MS regards exclusion with .gitignore as the best way to prevent leaks.
> Sources: **MS** engineering-fundamentals-checklist §"Source Control; source-control/secrets-management intro · **RSQ** software_project_structure §"Use version control · **W17** Inadvertent sharing · **OSG** starting-a-project / Your pre-launch checklist — 4 sources.
> Note: The sources themselves state this as should or leave the strength unspecified. No source describes what to do after a leak (revocation, reissue, removal from history). The sources agree and none disagrees, but none says must. An organization that wants to make this mandatory should place it in Appendix C.

### G11.5 (SHOULD) Keep other sensitive data out of public repositories as well
Do not track data files that contain sensitive information in a public version control system. Treat data as "as open as possible, as closed as necessary."
> Sources: **W17** Inadvertent sharing · **RSQ** software_project_structure §"Use version control · **TW** rdm-fair §"Theory; overview-definitions §"Reproducible But Not Open · **MS** engineering-fundamentals-checklist §"Observability — 4 sources.

### G11.6 (SHOULD) Apply least privilege to access rights and privileges
Grant access to repositories and cloud environments only to the people who need it, when they need it, and periodically review who has access. Except in very special circumstances, do not require root or other privileges to install or use the software you build. TW17 gives as reasons that a bug then behaves much like malicious software, and that such requirements hinder trial use on clusters.
> Sources: **MS** engineering-fundamentals-checklist §"Security"; CI-CD · **RSQ** using_version_control §"How do you implement version · **TW17** Rule 7 — 3 sources.
> Note: One source (OSG) recommends that at least two people hold administrator rights on a public project, so avoiding dependence on a single person must be balanced against least privilege. For the permissions of AI agents, see G12.

### G11.7 (SHOULD) Check for security flaws in code review
During review, check for security flaws. As items to check, the sources list unchecked errors returned by libraries, unvalidated input, improperly encoded output, and the handling of invalid parameters. If any part, such as security or privacy, is outside what you are qualified to judge, add a qualified reviewer.
> Sources: **MS** code-reviews/process-guidance/reviewer-guidance · **TW** reviewing-checklist §"Security of new codes · **GEP** review/reviewer/looking-for §"Every Line — 3 sources.

### G11.10 (SHOULD) Keep dependencies small and track their versions and updates
Treat dependencies also as liabilities that can bring in changes, deprecations and security problems. Keep a small, well-understood dependency tree and choose well-maintained projects. Record the exact versions of dependencies and make updates deliberate and auditable.
> Sources: **RSQ** software_maintenance §"How do you maintain your · **MS** security/README §"OWASP Cheat Sheets", §"Recommended · **W17** 2d, §Software · **TW17** Rule 5 — 3 sources.
> Note: No source states, at should or stronger, that dependencies should be scanned regularly for known vulnerabilities (MS gives this only as an example). W17 and TW17 also recommend choosing well-maintained libraries, but not for security reasons.

### G11.11 (MUST) Check export control regulations before distributing outside the organization (single source)
Before distributing software to third parties outside the organization, confirm that it complies with export control regulations (for example, cryptography, sanctions lists, countries that require a license). This applies only when distributing software of DLR application class 1 or higher outside the organization.
> Sources: **DLR** ERM.10, §Release Management [AC1+] (must) · **N7009H** §4.5.3 — 1 sources.
> Note: Which laws apply is outside the scope of the sources.

## G12. AI coding agents

Much of the evidence in this section comes from the product documentation of two vendors of AI coding agents (CC, CX; retrieved 2026-10-19), which is revised frequently, and CC itself presents its patterns as starting points rather than fixed rules [CC]. When submitting to JOSS, disclosing the use of generative AI in the paper is a requirement [JOSS][JOSS] (G12.13).

### G12.1 (SHOULD) Hold AI-assisted work to the same standards and checks as human work
Require the same correctness and quality of code, READMEs and documents written with AI assistance as of handwritten ones, and accept them only after they pass the same checks (tests, static analysis such as linting and type checking, review of the diff). For JOSS submissions, the authors bear full responsibility for the correctness, licensing and legal compliance of the work, whether or not AI was used (MUST).
> Sources: **RSQ** static_analysis §"How do you use static analysis to; creating_good_readme §"What sections should you · **JOSS** SUB §AI Usage Policy, Author use [JOSS] (must) · **CX** Improve reliability with testing and review — 3 sources.
> Note: The must applies only to responsibility for JOSS submissions; the rest is should. No source explicitly states that human review procedures (approvers, merge rights) apply unchanged to changes made by AI.

### G12.13 (MUST) When submitting to JOSS, follow the JOSS AI policy (single source)
This applies only to submission to and review for JOSS. Observe the following.
- Include a section in the paper disclosing the use of generative AI, stating the tools and models used and their versions, where they were used, the nature and extent of the assistance, and how the quality and correctness of the generated output were verified. If AI was not used, say so.
- Human authors review, edit and verify all AI-assisted output, make the problem framing, key design decisions and architectural choices themselves, and state that they have done so.
- As an author, do not use AI in communications with editors and reviewers (except for translation).
- As a reviewer, use AI only for non-substantive tasks such as grammar checking, disclose that use, and leave evaluative judgments such as acceptance, novelty and correctness to humans.

> Sources: **JOSS** RC §Software paper content, AI usage disclosure [JOSS] (must); SUB §AI Usage Policy, Author use [JOSS] (must) — 1 sources.
> Note: In the original, including the disclosure section is must, while stating non-use and describing how AI was used and how the output was verified are should. Incomplete or inaccurate disclosure may be treated as an ethics violation (JOSS). Sources other than JOSS require neither disclosure of AI use in papers nor human review of all AI-assisted output, and outside JOSS the latter remains (SHOULD).

## G13. People and operations

### G13.1 (SHOULD) Keep knowledge in the repository rather than in individuals' heads, and do not depend on a single maintainer
Document important decisions, architecture and operational knowledge in the repository itself rather than in any one person's memory. Have at least two people hold administrator rights and ownership. A sole maintainer should, where possible, share ownership with someone who can act in their absence.
> Sources: **RSQ** software_maintenance §"How do you maintain your; life_cycle §"Research Software Lifecycle & Three-Tier · **OSG** starting-a-project / Your pre-launch checklist; starting-a-project / Setting your goals · **DLR** §Definition of the intended Application Class, "Period (must) · **MS** documentation/README intro, §"Goals · **W14** §Document Design and Purpose, Not Mechanics; §Collaborate — 5 sources.
> Note: Targets for the number of people go no further than "at least one other person" and "at least two people."

### G13.3 (SHOULD) Set up project and team practices so that new members can join easily
Even in a project that started small, make it possible for new collaborators (including yourself after a period away) to set up the environment, find tasks they can work on, and understand how to contribute. Write down the team's workflow in a team manual or similar document and keep it up to date. Do not postpone onboarding documentation.
> Sources: **W17** §Collaboration; 3a, §Collaboration · **MS** documentation/README intro, §"Goals; documentation/README intro, §"Challenges · **RSQ** using_version_control §"How do you implement version; life_cycle §"Community Feedback"; software_maintenance · **TW** rdm §"Motivation and Background · **NL** README, §Guide / §Resources; CONTRIBUTING, §Zen of the Guide 9–16 · **OSG** best-practices / Embrace mentorship — 6 sources.

### G13.4 (SHOULD) Identify the skills needed, and provide training and tools
Identify the skills required for your role and target class, and fill gaps with training. Training should cover at least programming languages, version control and the use of development tools. Provide the necessary tools.
> Sources: **DLR** §Qualification; EQA.3, §Qualification [AC1+] · **N7009** 4.1.4a; 4.1.4b(1) · **RSQ** using_version_control §"How do you implement version; using_version_control §"How do you choose the right · **NL** best_practices, §Know your tools; best_practices, §Use an editor that helps you develop · **TW** overview-barriers §"Requires additional skills — 5 sources.

### G13.5 (SHOULD) Do not expect complete knowledge from less experienced participants; support them with mentoring and graduated tasks
Do not expect researchers who are domain experts to have complete knowledge of advanced software engineering. Make review a two-way learning opportunity, and offer easy tasks and mentoring for first contributions. Do not overwhelm people who have not yet mastered the tools by recommending many practices at once.
> Sources: **RSQ** code_review §"Consideration - what not to look for in · **OSG** best-practices / Embrace mentorship · **W14** Practice 8b, §Collaborate · **W17** What we left out — 4 sources.

# Part II: Additional practices for scientific computing

## S1. What is different about research software

### S1.2 (SHOULD) Treat distribution, publication, handover and long-term development as triggers to raise the level of quality measures
If the software will be distributed to third parties outside the institute, made the subject of a paper, handed over to someone else, or developed further after the person holding the knowledge has left, raise it from the personal level to a higher level. As a rule of thumb, TW17 suggests that the point at which the software has been used three or four times for slightly different purposes is a candidate time for hardening it. DLR makes this mandatory for distribution outside the institute (application class 1 or higher) and for large-scale, long-term development (application class 2).
> Sources: **DLR** §Definition of the intended Application Class [AC1+] (must); §Definition of the intended Application Class, "Period (must) · **TW17** §Introduction; §Conclusion · **RSQ** software_maintenance §"How do you maintain your; life_cycle §"Research Software Lifecycle & Three-Tier — 3 sources.
> Note: The trigger thresholds differ among the sources (DLR uses total effort and duration, TW17 the number of reuses), and there is no common figure.

### S1.3 (SHOULD) Aim for the most rigorous level for computations that support critical decisions, regardless of size
Aim for the most rigorous level for models and software whose results bear on critical decisions, such as human safety or mission success, regardless of their size or duration. The requirements (shall) of the NASA standard N7009 are imposed only on models that a criticality assessment places within this scope. For other models, the standard may be used as best-practice guidance.
> Sources: **DLR** §Definition of the intended Application Class, "Risks; §Application Class 3 · **N7009H** §5.1.1.4 · **N7009** §1.2.1; §4.1.1.2 · **RSQ** life_cycle §"Research Software Lifecycle & Three-Tier — 4 sources.
> Note: N7009H cautions that there is no correlation between compliance with the standard's requirements and the level of credibility achieved [N7009H]. Satisfying the procedures does not necessarily mean that rigor has been achieved.

### S1.4 (SHOULD) Distinguish being reproducible, being correct and being open
Do not treat the fact that the same answer can be reproduced from the same data and code as evidence that the answer is correct. Also distinguish being reproducible from being open, and being FAIR from working correctly (for checking correctness, see S3.2). Reproducibility terms can have opposite meanings in different fields.
> Sources: **TW** overview-definitions §"Definitions", §"Table of; overview-definitions §"Reproducible But Not Open · **N7009H** §3.2; §4.7 · **S13** Rule 6 · **RSQ** quality §"FAIR Research Software"; fair_rs §"Tools and — 2 sources.
> Note: The definitional frameworks differ among the sources (TW uses four categories; N7009H uses three: repeatability, reproducibility and replicability), and there is no basis for standardizing on either.

### S1.5 (SHOULD) Record what was done and what was not done
For verification, validation and uncertainty assessment, record what was done and the level reached. Record aspects that were not addressed as not done, and give the reasons for not addressing them. The record need not be a new document; it is enough to be able to cite or reference evidence of the activities and their results (papers, reports, user guides and so on).
> Sources: **N7009** §1.1; §4 · **N7009H** §4.4 Note; §4.7 — 2 sources.
> Note: The evidence comes only from a single NASA lineage (the standard N7009 and its handbook N7009H), and the must in N7009 applies only to models that support critical decisions. NASA requires a record of what was and was not done rather than the performance of particular techniques, and it does not set the level to be achieved for each factor.

### S1.6 (SHOULD) Make it work correctly first, and optimize later
Optimize only after the software works correctly. Before optimizing, determine whether performance is actually a bottleneck.
> Sources: **W14** Practice 6, §Optimize Software Only after It Works · **W17** What we left out, Profiling and performance tuning — 2 sources.
> Note: No source gives an overall order of priority among correctness, reproducibility, readability and performance. This is the only explicitly stated priority contrast in the corpus.

## S2. Documenting the model, its assumptions and its domain of validity

### S2.1 (SHOULD) Record the model's assumptions, abstractions and known issues
For simulation and analytical models, record the assumptions and abstractions underlying the conceptual model and the implementation (what was included and what was left out), together with their rationale and their effect on the intended use. Also make known issues and constraints explicit, and describe surrogate models (surrogates and ML models) as inheriting the assumptions and limitations of the original model. For models that support critical decisions, recording the assumptions and abstractions is required (MUST).
> Sources: **N7009** 4.2.1.4 [M&S 11] [NASA scope] (must); 4.3.9b · **N7009H** §4.4 b–f; §5.2.1.1 · **DLR** §Application Class 1 — 3 sources.
> Note: The evidence for recording assumptions and abstractions comes only from a single NASA lineage, and the must in N7009 applies only to models that support critical decisions. DLR supports only the part about making known issues and constraints explicit (the definition of application class 1).

### S2.2 (SHOULD) Document permissible uses and the cases in which the model must not be used
Document the "permissible uses," as determined by the intended use, the assumptions, the limitations and the verification and validation domains, together with details of usage and calibration. Also describe the cases in which the model must not be used, with the rationale. For models that support critical decisions, recording the permissible uses is required (MUST).
> Sources: **N7009** 4.2.1.7 [M&S 14] [NASA scope] (must) · **N7009H** §4.4 b–f; §5.2.1.3.1 a — 2 sources.
> Note: The evidence comes only from a single NASA lineage, and the must in N7009 applies only to models that support critical decisions.

### S2.3 (SHOULD) Record the verification domain, the validation domain and the limits
Record the range of inputs over which the model was verified (the verification domain), the range over which it agreed well with referent data (the validation domain), and the limits within which it is designed to operate. Agreement at a single point is only point validation, so establish the domain by evaluating at each point as the inputs are varied. Extrapolation beyond the domain is possible only within the limits of the model.
> Sources: **N7009** 4.2.3.2 [M&S 16] [NASA scope] (must); 4.2.5.2 [M&S 18] [NASA scope] (must) · **N7009H** §3.2; §5.5.3.2.1 — 2 sources.
> Note: The evidence comes only from a single NASA lineage, and the must in N7009 applies only to models that support critical decisions. N7009H itself acknowledges that exploratory research often uses models outside their domain [N7009H].

### S2.4 (SHOULD) Maintain a user guide
Maintain a guide to using the model or analysis software that includes the following. For models that support critical decisions, this is required (MUST). For existing or commercial models, documentation of the concept, structure and mathematics may in some cases be replaced by a reference to a published user guide.
- Good practice for setup, execution and analysis of results, and calibration of parameters
- Computational requirements (hardware and software versions, memory, disk, compile options) and criteria for obsolescence
- The concept, structure and mathematics of the model (the equations solved and their sources, the methods)
- All inputs with their permissible ranges, accuracy, and the treatment of uncertainty
- Examples of expected results (including examples of good and poor agreement with referent data)
> Sources: **N7009** 4.2.1.9 [M&S 47] [NASA scope] (must); 4.2.1.5 [M&S 12] [NASA scope] (must) · **N7009H** §5.4.1 h; App. C.2–C.3 [Rec. 4.2.2e] — 2 sources.
> Note: The evidence comes only from a single NASA lineage, and the must in N7009 applies only to models that support critical decisions. The table of contents for the guide (N7009H) is a suggestion, and its details are optional.

## S3. Verification and validation

### S3.1 (SHOULD) Distinguish verification from validation (single source)
Distinguish verification (confirming that the implementation behaves according to the specification or mathematical model and meets its accuracy targets) from validation (confirming, by comparison with referent data, how well the model or computation represents reality for the intended use). Record and report separately which of the two was done. Satisfying verification in the software engineering sense does not establish agreement with reality.
> Sources: **N7009** §3.2 Validation; §3.2 Verification · **N7009H** §4.7 · **BSSw** §Types of Tests, Verification Tests; §Types of Tests, Acceptance Tests · **RSQ** testing_software §"What is code testing? — 1 sources.
> Note: The scope of the terms differs slightly among the sources. RSQ calls "the output is correct" validation, which is broader than NASA's usage, while BSSw restricts validation to that based on UQ and experimental data. The only basis for this as a recommendation is the NASA standard N7009, and its must applies only to models that support critical decisions.

### S3.2 (MUST) Check the correctness of results separately from passing tests or reproducibility
Check the correctness of scientific computing results separately from whether the tests pass or whether the same results can be reproduced. The means include comparison with known solutions (including analytical solutions), simplified cases, experimental data, and trusted existing programs or alternative implementations (a prototype in a high-level language, a different model), as well as discussion with colleagues. DLR makes this mandatory at the level where others use the software (application class 1 or higher).
> Sources: **DLR** EST.4 , §Software Test [AC1+] (must) · **TW** overview-barriers §"Being reproducible does not mean; testing-acceptance-regression §"Limitations of · **W14** §Plan for Mistakes; §Optimize Software Only after It Works Correctly · **N7009H** App. E.2 Note — 4 sources.
> Note: No source indicates how to choose among the means or how to prioritize them. What is mandatory is checking correctness; the choice of means is optional.

### S3.3 (MUST) Verify the correctness of regression-test reference outputs before relying on them
Regression tests that only compare against past outputs show only that "nothing has changed." If you use their reference outputs as grounds for the correctness of results, verify the correctness of those reference outputs separately and independently. When regenerating the reference, also verify the new reference outputs before adopting them.
> Sources: **BSSw** §Types of Tests, No-Change/Characterization Tests (must); §Types of Tests, No-Change/Characterization Tests · **TW** testing-acceptance-regression §"Limitations of — 2 sources.
> Note: Regression tests are useful in themselves, and BSSw considers unit-level characterization tests sufficient to drive refactoring [BSSw].

### S3.4 (SHOULD) Preserve verification and validation test cases and referent data, and use them for regression testing
Keep the plans, procedures, test cases (input-output pairs for confirming correct execution) and referent data used in verification and validation under configuration management. Rerun them as regression tests whenever the code is changed thereafter. Choose test cases that cover the range of the defined limits of applicability.
> Sources: **N7009** 4.2.2e · **N7009H** §5.5.2.3 [M&S 16]; §5.5.3.1 [M&S 18]; §5.5.2.2 a — 2 sources.
> Note: The evidence comes only from a single NASA lineage. This presupposes that the stored reference outputs have been verified (S3.3).

### S3.5 (SHOULD) Evaluate existing software before relying on it
Treat the libraries and third-party tools you use as subjects of evaluation too, and confirm that they fit the purpose and are trustworthy (by checking the documentation, testing reproducibility, and observing behavior when parameters are varied) before relying on them. Do not use the default settings of statistical methods or tools without understanding what they do.
> Sources: **RSQ** life_cycle §"Use & Evaluation · **W17** 2e, §Software · **TW** overview-barriers §"Being reproducible does not mean · **N7009** App. E.2 — 4 sources.
> Note: No source indicates how thorough the evaluation should be.

### S3.6 (SHOULD) Write verification tests so that pass or fail follows from mathematical properties or the specification
Write tests that check whether the implementation correctly implements the intended algorithm so that, based on the algorithm's mathematical properties or a clear specification, the requirement is evident from the test's specification and pass/fail criteria alone (for example, the conjugate gradient method fully solves a linear system with N distinct eigenvalues in exactly N iterations). For code verification, perform inspection, code tracing, unit testing, and comparison of the implementation with the conceptual and mathematical models.
> Sources: **BSSw** §Types of Tests, Verification Tests · **N7009H** §5.5.2.2 a; §5.5.2.3 · **N7009** App. E.3.3 — 2 sources.
> Note: No source specifically names which properties or techniques to use, such as conservation laws, order of convergence or manufactured solutions. The only example in the sources is a single iteration-count property.

### S3.7 (SHOULD) Detect impossible values and conditions that invalidate results, and write negative tests
Insert checks or assertions that detect conditions that can invalidate results, such as physically or mathematically impossible states (for example, a particle exceeding the speed of light), function evaluations outside the domain, division by zero, inputs outside the range of a lookup table, and reaching the maximum number of iterations before convergence. Also write tests that confirm that such conditions produce an error (negative tests).
> Sources: **N7009H** §5.4.2 f–i; §5.4.2 [Rec. 4.2.2c] · **W14** Practice 5a, §Plan for Mistakes · **TW** testing-overview §"Overview of Testing Types — 3 sources.
> Note: One source states that when run-time constraints are tight, the checks may be placed under conditional compilation [N7009H].

### S3.8 (SHOULD) Perform validation and calibration after verification, and record what was calibrated and its domain
Perform validation by comparison with referent data, and parameter calibration, after the implementation has been verified. If you calibrate, record what was calibrated and its domain of application, and check the results against data not used for calibration (the sources give this as an example of a means). If calibrating a verified model does not achieve the required agreement, question the model's assumptions or the design itself.
> Sources: **N7009H** §5.5.1; App. D.5 Note; §5.5.1, Fig. 6 · **N7009** 4.2.4a; App. E.3.4 — 2 sources.
> Note: The evidence comes only from a single NASA lineage. The body of N7009 also states that V&V often occurs cyclically, depending on the development process, and does not impose a strict one-way order.

### S3.9 (SHOULD) Record the verification and validation methods used and their status
Record the verification methods used, the verification status of each of the conceptual, mathematical and computational models, and the validation methods (including experimental design and analysis), metrics, referent data and results. Record aspects that were not addressed according to S1.5. For models that support critical decisions, performing verification and validation itself is required (MUST).
> Sources: **N7009** 4.2.3.1 [M&S 15] [NASA scope] (must); 4.2.5.1 [M&S 17] [NASA scope] (must) · **N7009H** §5.5.2; App. D.4 — 2 sources.
> Note: The evidence comes only from a single NASA lineage, and the must in N7009 applies only to models that support critical decisions. The standard does not prescribe methods, and the recording is should.

### S3.10 (SHOULD) Assess the quality of referent data, and judge agreement by both the mean difference and the uncertainty
Assess the provenance, acquisition and processing methods, and uncertainty of the referent data used for validation with the same rigor as the verification of the model. Data acquired for other purposes (such as health monitoring) may differ from the data needed for validation in both purpose and tolerance, so confirm that they are compatible. In judging agreement, consider both the difference in means and the spread; use overlaid plots with uncertainties only as a quick check, and use statistical metrics (for example, a t-test) when the overlap is limited.
> Sources: **N7009** §4.2.5; App. E.3.4 · **N7009H** §5.5.3.1; §5.5.3.2; App. B.2; App. B.2 — 2 sources.
> Note: The evidence comes only from a single NASA lineage. The choice of specific metrics is left to domain-specific recommended practices.

### S3.11 (SHOULD) Set acceptance criteria and allowable uncertainty before validation (single source)
Decide and agree on the acceptance criteria and allowable uncertainty for judging whether computational results are adequate for their use at or before the start of validation, not after seeing the comparison process or its results. To that end, also decide in advance which referent data to obtain. Setting the criteria after seeing the results biases judgment.
> Sources: **N7009H** §5.5.3.2; §5.5.3 — 1 sources.
> Note: The evidence comes only from the NASA handbook N7009H.

## S4. Floating point and numerical computation

### S4.1 (SHOULD) Assume that floating-point results can change at the bit level
Floating-point results can change at the bit level with changes in the order of operations (including refactoring), compiler and optimization settings, the use of extended precision or fused multiply-add (FMA), math libraries, and the configuration of the OS, hardware and computing infrastructure. Design tests and comparisons of results on this assumption. A case has been reported in which an equality comparison in the same program gave a different result depending only on whether optimization was enabled.
> Sources: **GB91** §"Languages and Compilers" > "Ambiguity; §"Differences Among IEEE 754 Implementations · **BSSw** §Types of Tests, No-Change/Characterization Tests · **TW17** Rule 10 · **CP** §Changes that are not considered breaking · **TW** overview-barriers §"Big data and complex computational · **N7009** App. E.3.3 — 3 sources.
> Note: The main text of GB91 (Goldberg) states that if intermediate results differ between IEEE-conforming machines, it is a software bug [GB91]. An addendum by Sun in the same source corrects this, stating that IEEE 754 does not guarantee identical results across implementations.

### S4.2 (SHOULD) Do not make test pass/fail depend on specific floating-point bit patterns
For tests that compare results across code changes or versions, judge pass or fail by approximate accuracy, numerical stability or statistical properties rather than by specific bit patterns. Do not rely on binary-match tests of floating-point output, especially at the integration and system levels, because they fail merely when the order of operations changes and thus hinder refactoring.
> Sources: **CP** §Changes that are not considered breaking · **BSSw** §Types of Tests, No-Change/Characterization Tests · **TW17** Rule 10 — 3 sources.
> Note: As a goal for reruns of the same version in the same environment, bit-for-bit agreement is supported (S7.9). One source also points out that the difference between exact and approximate reproduction is large and that it is hard to draw conclusions from approximate agreement [S13].

### S4.3 (SHOULD) When comparing within a tolerance, decide the value, record it, and state it in the documentation and tests
When results are compared within a tolerance (floating-point equality checks, convergence checks for iterations, comparisons when the seed cannot be fixed, and so on), decide the value explicitly, record it as part of the run configuration, and state it in the documentation and tests.
> Sources: **TW17** Rule 10 · **N7009H** §5.6.2.1, Table 7; §5.5.2; App. D.4 · **N7009** 4.3.1.4 [M&S 25] [NASA scope] (must) — 2 sources.
> Note: Comparison within a tolerance is not a cure-all either; deciding the width of the error bound, handling values of opposite sign, and the failure of transitivity are problems [GB91]. No source shows how to choose and justify the tolerance value (the closest is the statement that the required accuracy is determined by the conclusions to be drawn from the results, which only the programmer knows [GB91]).

### S4.4 (SHOULD) Estimate and record numerical errors, and include accepted errors in the uncertainty
Identify and estimate the numerical errors in computational results (insufficient discretization, insufficient iterative convergence, numerical approximation, finite-precision arithmetic, sampling and interpolation, and so on), assess their effect on the required accuracy, and record them. If any error sources are not addressed, give the reasons. If you accept a numerical error that is not minimal for the sake of computation time, include the difference between the apparent asymptotic solution and the solution at the adopted settings in the uncertainty of the results.
> Sources: **N7009** 4.2.4b; App. E.3.3 · **N7009H** §5.5.2; App. D.4; §5.5.2.2 b — 2 sources.
> Note: The evidence comes only from a single NASA lineage. No source gives specific methods for error estimation (such as Richardson extrapolation).

### S4.5 (SHOULD) Check convergence by varying implementation-specific parameters, but do not treat convergence as a guarantee of accuracy (single source)
For approximate solutions, systematically vary implementation-specific parameters, such as iterative solver tolerances, time steps and the spatial sampling of the mesh, until the solution converges to its asymptotic limit and stabilizes. Distinguish this from sensitivity analysis, which varies the parameters of the real system. Even if the numerical solution converges and is stable, that does not mean the model meets its accuracy requirements (that is judged by validation).
> Sources: **N7009H** §5.5.2.2 b; §5.2.1.2 c(3)C — 1 sources.
> Note: The evidence comes only from the NASA handbook N7009H. No source mentions specific techniques, such as how to judge convergence or how to check the order of convergence.

### S4.6 (SHOULD) Assess the effects of finite precision, and consider rewriting expressions that involve catastrophic cancellation
Assess the effects of finite precision (rounding, cancellation, underflow and overflow) on the accuracy of results. As an optional measure, consider whether expressions that subtract nearly equal values containing rounding error, or that overflow prematurely, can be rewritten into algebraically equivalent forms that avoid the problem (for example, rewriting x²−y² as (x−y)(x+y), or choosing between forms of the quadratic formula).
> Sources: **N7009H** §5.5.2.2 b · **GB91** §"Cancellation; §"Cancellation", formulas (4)(5) — 2 sources.
> Note: If the inputs themselves are approximate values, such as measurements, a rewrite that merely turns catastrophic cancellation into benign cancellation is not worthwhile if it is costly [GB91]. There are also examples in which "simplification" by approximation actually worsens accuracy [GB91].

### S4.7 (SHOULD) Detect NaN, infinities and floating-point exceptions, and check that comparisons and loops do not misbehave
Catch floating-point exceptions such as NaN, infinity and division by zero as conditions that can invalidate results (by trapping exceptions or signals, checking status flags, or using assertions). Because ordering and equality comparisons with NaN are always false, check that termination conditions and branches do not lead to infinite loops or wrong branches when NaN occurs.
> Sources: **N7009H** §5.4.2 [Rec. 4.2.2c]; §5.5.2.2 a · **GB91** §"Special Quantities" > "Infinity; §"Exceptions, Flags and Trap Handlers — 2 sources.
> Note: GB91 also emphasizes situations in which the default behavior of returning NaN or infinity on an exception and continuing the computation is useful [GB91][GB91][GB91]. Rather than stopping uniformly, distinguish places where the computation may continue from places where the result is invalidated.

## S5. Units, coordinate frames and physical constants

### S5.1 (SHOULD) Record the units and coordinate frames of quantities, inputs and outputs, and document conversions when they are mixed
For the quantities, input and output variables and parameters handled by the model and code, identify and record their units and (for vector quantities) coordinate frames, and also state the units in the documentation of function inputs and outputs. When units or coordinate frames are mixed, identify and document the necessary conversions, and check at design time whether conversions are needed at the interfaces between models. For computations that support critical decisions, recording units and coordinate frames is required (MUST).
> Sources: **N7009** 4.2.1.3 [M&S 46] [NASA scope] (must) · **N7009H** §5.2.1.3.4; §5.3.2 c–d [M&S 46]; §5.3.2 · **TW** reviewing-checklist §"Function/Method declarations; testing §"Motivation and Background — 3 sources.
> Note: Only a single NASA lineage mentions coordinate frames. No source recommends a means of implementation, such as carrying units in types or using a units library.

### S5.2 (SHOULD) Do not embed units in data values (single source)
In tabular data, do not include units in the values (3.4, not "3.4kg"); store them in a separate variable, such as the column name, or in metadata.
> Sources: **W17** 1d, §Data management — 1 sources.

### S5.3 (SHOULD) Define physical constants exactly once, in one place (single source)
Define physical constants exactly once so that the whole program uses the same value. This is an instance of the principle that every piece of data must have a single authoritative representation in the system.
> Sources: **W14** Practice 4a, §Don't Repeat Yourself (or Others) (must); Practice 4a — 1 sources.
> Note: No source requires recording the source or version of constant values.

## S6. Random numbers

### S6.1 (SHOULD) Record the seed in analyses that use random numbers
In analyses that involve random numbers, record the random seed used so that the results can be reproduced exactly by supplying the same seed. If the program chooses the seed internally (for example, from the time), write the seed to the output so that it can be reused later. Even when the seed cannot be recorded, at a minimum record which analysis steps involve random numbers.
> Sources: **S13** Rule 6; Rule 6 (minimum) · **TW17** Rule 10 · **TW** overview-barriers §"Big data and complex computational — 2 sources.
> Note: In parallel computing it is difficult to obtain identical results from run to run even with a fixed seed, and some software (for example, TensorFlow) cannot fix random numbers without sacrificing parallelism [TW]. No source shows how to partition random number streams in parallel computing. The sources agree and none disagrees, but none says must. An organization that wants to make this mandatory should place it in Appendix C.

### S6.2 (SHOULD) Allow the seed to be supplied as an input parameter (single source)
In programs that use random numbers, allow users to optionally supply the random seed as an input parameter (such as a command-line argument), so that the program can be made to run deterministically when needed.
> Sources: **TW17** Rule 10; Rule 3 — 1 sources.
> Note: No source recommends a design in which random number generators or seeds are passed as arguments through function or class APIs.

### S6.3 (SHOULD) When the seed cannot be fixed, state the allowable discrepancy in the documentation and tests
If the seed cannot be fixed, or fixing it does not yield identical results, record which analysis steps involve random numbers, determine the degree of discrepancy expected on reproduction (the tolerance), and state it in the documentation and tests.
> Sources: **S13** Rule 6 (minimum) · **TW17** Rule 10 · **TW** overview-barriers §"Big data and complex computational — 2 sources.
> Note: No source shows how to assess the degree of discrepancy statistically or how to test stochastic code with statistical tests.

## S7. Reproducibility and provenance

### S7.1 (SHOULD) Record how every reported result was produced
For any result that may be used in a paper or report, record during the work how it was produced, including pre- and post-processing (the programs, inputs, and steps used). At minimum, keep enough detail on programs, parameters, and manual steps that your future self can approximately reproduce the result (as a guide, S13 cites yourself one year later). Record the entire process automatically, in a format that other programs can read (SHOULD).
> Sources: **S13** Rule 1; Rule 1 (minimum) · **W14** §Let the Computer Do the Work · **W17** 1e, §Data management · **N7009** 4.3.2e; 4.3.1.4 [M&S 25] [NASA scope] (must) · **F4RS** FAIR4RS Principles, R1.2 · **TW** vcs §"Summary — 6 sources.
> Note: Only N7009 states this as a must, and it applies only to models that support critical decisions [N7009].

### S7.2 (MUST) Record the versions of programs and libraries used, the parameters, and the inputs
For every program (however small) and library used to produce a result, record its name and a unique version number. Also record the parameter values and inputs (data files and their versions) used to produce each output, linked to that output.
> Sources: **S13** Rule 1; Rule 3 · **W14** §Let the Computer Do the Work, 2; §Let the Computer Do the Work, 3 · **N7009** 4.3.2b; App. F.1 · **N7009H** §5.6.2.1 [M&S 24, 25; Rec. 4.3.2c, 4.3.2d] · **DLR** EAA.6, §Automation and Dependency Management [AC3+] · **TW** code-reuse-overview §"Portable Recommendations"; renv (must) · **TW17** Rule 10 — 7 sources.

### S7.3 (SHOULD) Have programs write their parameters, versions, and inputs to their output
At startup, have the program write all parameters and software versions to standard output or a log file along with the results. Include the version number in every output, including debugging traces. Where possible, log all inputs, including interactive selections, and check them against the intended inputs to detect typos and operator errors.
> Sources: **TW17** Rule 10; Rule 4 · **MS** automated-testing/README §"Build for Testing · **N7009H** §5.6.2.2 · **N7009** App. E.3.3 — 4 sources.

### S7.4 (SHOULD) Modify data with programs, and record unavoidable manual steps
Perform data modification, format conversion, and processing with scripts or programs that can be rerun, and avoid hand-editing data files or copying and pasting between documents. Where manual steps are unavoidable, record at minimum which files were changed or moved and for what purpose, and which menus or ranges were selected.
> Sources: **S13** Rule 2; Rule 2 (minimum) · **W17** 1e, §Data management · **W14** Practice 2a, §Let the Computer Do the Work; Practice 2b, §Let the Computer Do the Work · **TW** code-reuse-overview §"Repeatable Recommendations — 4 sources.
> Note: The sources agree and none dissent, but none states this as a must. If an organization makes it mandatory, place it in Appendix C.

### S7.5 (SHOULD) Make the entire analysis rerunnable with a single command
Describe the entire analysis, from raw data to figures, tables, and manuscript, in executable form (e.g., a shell script, a Makefile, a workflow management system) so that it can be regenerated with a single command. For small projects, a single driver script that runs everything is sufficient.
> Sources: **S13** Rule 1 · **W14** Practice 2c, §Let the Computer Do the Work · **TW** code-reuse-overview §"Repeatable Recommendations; make §"Summary", §"Why use Make for Reproducibility? · **W17** 4d, §Project organization, Box 2; What we left out, Build tools · **RSQ** quality §"Aspects of Research Software Quality · **N7009H** §5.2.1.2 d; §5.3.2 j–l — 6 sources.
> Note: W17 deliberately leaves build tools out of its recommendations for beginners, stating that a shell script that reruns everything achieves the same effect. There is no basis for requiring build tools across the board.

### S7.6 (MUST) Record the computational environment in a form others can recreate
Record the computational environment that produced the results (operating system, versions and configuration of installed software and packages, compiler options, execution platform, and hardware characteristics such as the number of CPU cores) so that the environment in which the code ran can be recreated.
> Sources: **TW** code-reuse-overview §"Portable Recommendations"; renv (must); renv §"Motivation and Background", §"Researchers · **N7009H** §5.6.2.1 [M&S 24, 25; Rec. 4.3.2c, 4.3.2d] · **RSQ** quality §"Aspects of Research Software Quality; documenting_software_project §"How to document your · **DLR** EAA.7, §Automation and Dependency Management [AC3+] — 4 sources.

### S7.7 (SHOULD) Declare dependencies explicitly, and capture and preserve the environment by means suited to the project
Declare dependencies explicitly for each project, with their names, versions, and sources. As needed, capture the environment with whichever of a package manager's version specification file, a virtual environment, a container, or a virtual machine suits the nature of the project (which one to use is optional). Preserve external programs that may disappear from where they are obtained, and the dependencies of at least the versions used for releases or reports.
> Sources: **W17** 2g, §Software · **DLR** EAA.2, §Automation and Dependency Management [AC1+]; EAA.4, §Automation and Dependency Management [AC3+] · **TW** renv-options §"Capturing Computational Environments; renv-options §"Package Management Systems · **RSQ** life_cycle §"Release & Deployment"; packaging_software; reproducible_software_environments §"Why should you · **S13** Rule 3 · **NL** best_practices, §Packaging · **N7009** 4.2.1.2 [M&S 45] [NASA scope] (must); 4.2.2h · **N7009H** §4.6 b — 8 sources.
> Note: There is tension within RSQKit over version pinning. One passage says that in many cases you need not worry about specific versions [RSQ], while another says to pin and record dependencies explicitly for maintenance [RSQ]; it does not say when to use which.

### S7.8 (MUST) Put the scripts that produced results under version control
Put every custom script that produced results under version control, so that you can return to the exact state of the code at the time the results were produced (see also Part I, G3). At minimum, save copies of the scripts from time to time to keep a rough record of their state during development.
> Sources: **S13** Rule 4; Rule 4 (minimum) · **TW** vcs §"Motivation (must) · **W14** Practice 3b, §Make Incremental Changes · **W17** 5h, Version control systems; §Keeping track of changes · **TW17** Rule 1 — 5 sources.

### S7.9 (SHOULD) Aim for the same results from the same version, inputs, and environment
Aim for a given released version, run with the same parameters, the same data, and the same computational environment, to return the same result every time (both correct output and errors). Identify and remove the causes of nondeterministic behavior. Accept that exact agreement may not be achieved during development.
> Sources: **TW17** Rule 4; Rule 10 · **RSQ** quality §"Aspects of Research Software Quality · **N7009H** §5.2.1.2 d; §5.3.2 j–l · **DLR** EST.6, §Software Test [AC3+] · **S13** Rule 6 — 4 sources.
> Note: Exact reproduction across parallel computation or across different infrastructure configurations (operating systems, clouds, HPC facilities) is difficult [TW][TW]. No source requires identical results across environments.

### S7.10 (SHOULD) Decide the level of reproducibility (at minimum yourself; third parties for results used in papers)
At minimum, make sure you yourself can reproduce your results. For results used in papers, grant applications, diagnoses, and the like, aim for a level at which others can rerun the entire analysis, from the data to the libraries.
> Sources: **S13** §Introduction; Rule 1 (minimum) · **TW17** Rule 10 · **TW** renv §"Science (must); overview-definitions §"Definitions", §"Table of — 3 sources.
> Note: Research that uses sensitive data can be made reproducible without publishing everything [TW]. Reproduction by third parties does not necessarily presuppose publication. The statement that reproducibility by yourself is the minimum [S13] is a should; the must in TW concerns publishing and distributing the entire analysis to make it reproducible.

### S7.11 (MUST) Save intermediate results and the data underlying figures
At minimum, save the intermediate files generated while running the analysis, unless they are prohibitively large. For each figure, save the underlying data (including values after plotting-time processing such as binning) and the code that created the figure so that they can be retrieved; at minimum, record which data the figure is based on and how it can be reconstructed. Save all intermediate results explicitly as files, in standardized formats where possible (SHOULD).
> Sources: **S13** Rule 5; Rule 5 (minimum) · **W17** §Data management; Supplementary materials · **N7009H** §5.6.4 · **TW** vcs §"Summary (must) — 4 sources.

### S7.12 (SHOULD) Link statements in the text to the results that support them
For statements in papers and notes (interpretations, claims, conclusions), include at minimum enough detail (such as the file paths of detailed results) to trace them back to the underlying results in the future. Link them systematically from the time of writing (SHOULD).
> Sources: **S13** Rule 9; Rule 9 (minimum) · **N7009** 4.3.9b — 2 sources.
> Note: The sources agree and none dissent, but none states this as a must. If an organization makes it mandatory, place it in Appendix C.

### S7.13 (SHOULD) Record the rationale for the setup, and link result versions to the scenarios that produced them
For simulation and analysis setups, record the data and auxiliary software used to prepare the inputs, the rationale and assumptions of the setup, the calibration and its domain, and key settings (time step, number of iterations, convergence tolerance, stopping criteria, etc.). Track each version of the run results together with the scenario (inputs and settings) that produced it, and place them under configuration management.
> Sources: **N7009** 4.3.1.4 [M&S 25] [NASA scope] (must); 4.3.2d · **N7009H** §5.6.2.1 [M&S 24, 25; Rec. 4.3.2c, 4.3.2d]; §5.6.2.1, Table 7 · **W17** 1a, §Data management — 3 sources.
> Note: The evidence comes mainly from a single NASA lineage (W17 supports only the part on procedures for obtaining external data), and the N7009 must applies only to models that support critical decisions. Placing results under configuration management is in tension, as to where they are kept, with the statement that results that can be regenerated are not version-controlled [TW17].

### S7.15 (SHOULD) At publication, bundle everything into a research compendium
When publishing a paper or similar work, bundle the data, code, computational environment specification, README, license, and citation metadata into a single research compendium and publish it together with the paper. To that end, organize the project's folders from the start on the assumption that the final product is a compendium, and describe in the README the steps to reproduce the paper's results.
> Sources: **TW** open-checklist §"Open Source Software"; compendia; compendia §"Summary", §"Motivation · **RSQ** life_cycle §"Publication; documenting_software_project §"How to document your · **TW17** Rule 4 · **F4RS** Discussion, ZB MED — 3 sources.

### S7.16 (MUST) Publish the data and code that support the results
Publish the input data, scripts, versions, parameters, and intermediate results that support the results, and make them easily accessible (SHOULD). At minimum, submit the main data and source code as supplementary material, and be prepared to respond to inquiries about details of the data and methods (MUST). Data subject to legal or ethical restrictions are excluded (S8.9).
> Sources: **S13** Rule 10; Rule 10 (minimum) · **TW** vcs-checklist §"Make Sure That Your Data Is; renv §"Science (must) · **W17** 1g, §Data management; 2j, §Software — 3 sources.
> Note: Reproducibility does not necessarily require publication [TW]. Publication also does not imply an obligation on the authors to support extension or reuse of the data or code [TW].

## S8. Data management

### S8.1 (SHOULD) Keep raw data as obtained, and do not modify it
Store raw data in the form obtained from the instrument or survey, and do not overwrite it with cleaned versions. Treat raw data as the single canonical version, and prevent accidental changes or hand edits, for example by making it read-only.
> Sources: **W17** 1a, §Data management; What not to put under version control · **RSQ** software_project_structure §"Directory structure · **TW** compendia §"Separating Methods, Data, Output · **W14** Practice 4a; Practice 4a, §Don't Repeat Yourself (or Others) (must) — 4 sources.
> Note: The must in W14 concerns giving each element of data a single authoritative representation (DRY); no source states not modifying raw data itself as a must.

### S8.2 (SHOULD) Keep raw data, intermediate data, and results in separate locations
Divide data into raw, cleaned, intermediate, and processed, and keep them in separate subdirectories (e.g., `data/raw`, `data/processed`, `results`). Distinguish read-only material, material created by people (code, documents), and material generated by the project (figures, output).
> Sources: **RSQ** software_project_structure §"Directory structure · **TW** compendia §"Structure of a Research Compendium; compendia §"Separating Methods, Data, Output · **W17** 4c, §Project organization — 3 sources.
> Note: The directory names are examples; there is no official standard [RSQ].

### S8.3 (SHOULD) Back up data to multiple locations
Back up raw data and material created by people (scripts, documents, packages depended on) to multiple locations as soon as they are created. If you use external disks, store them in a location separate from the original. Keep the project in a folder that is mirrored off the working machine (as a guide, W17 suggests syncing at least daily).
> Sources: **W17** 1b, §Data management; 5a, §Keeping track of changes · **TW** rdm §"Summary", §"Motivation and Background; vcs-git-in-research §"Potential · **RSQ** using_version_control §"How do you implement version — 3 sources.
> Note: No source specifies the number of copies or the type of media. The sources agree and none dissent, but none states this as a must. If an organization makes it mandatory, place it in Appendix C.

### S8.4 (SHOULD) Record the origin and provenance of data
Give raw data a unique identifier and version number, and record its origin (e.g., in a README in the data folder). For data not stored locally, record the retrieval procedure, the official version number, and the download date. Make clear the quality and origin of input data (measured, estimated, assumed, or derived from another model) (SHOULD).
> Sources: **W14** §Let the Computer Do the Work, 1 · **W17** 1a, §Data management · **RSQ** software_project_structure §"Directory structure · **N7009** §3.2 Data Pedigree; §3.2 Input Pedigree · **N7009H** §5.1.1.5 Note; §5.6.1.3 — 5 sources.
> Note: Grading data by quality, and the point that the credibility of inputs obtained from another model is bounded by that model's credibility, are guidance found only in N7009H. The N7009 must applies only to models that support critical decisions.

### S8.5 (SHOULD) Store and exchange data in open standard formats
Convert data from closed proprietary formats to open standard formats readable by both machines and people (e.g., CSV for tables; JSON, YAML, or XML for nested structures; HDF5 or NetCDF for large structured data) for storage, and read and write it in line with the community standards of the field. For supplementary material, choose long-established formats over newer ones.
> Sources: **W17** 1c, File formats; Supplementary materials · **NL** best_practices, §Use standards; technology/datasets, intro · **TW** open-checklist §"Open Data · **F4RS** FAIR4RS Principles, I1 · **RSQ** fair_rs §"How can we make our software interoperable? · **S13** Rule 5 — 6 sources.

### S8.6 (SHOULD) Describe data with a README and metadata
Accompany data with a README and metadata stating what it is, how it was collected, its strengths and weaknesses, and its terms of use, following the metadata standards of the field. For files placed in an archive, put the metadata describing their contents under version control.
> Sources: **TW** rdm §"Summary"; open-checklist §"Open Data"; rdm-fair; open §"Summary · **W17** 1c, File names; 1g, §Data management · **TW17** Rule 1 · **W14** Practice 3c, §Make Incremental Changes — 4 sources.

### S8.7 (SHOULD) Decide where to keep data and results by size and by whether they can be regenerated
Keep intermediate data and results that can be regenerated, and large data files, out of version control, for example in an archive system, and put the metadata describing their contents under version control. Exclude from tracking files that are too large or that contain sensitive information. If you need to track versions of data, check beforehand whether a version control tool for data is suitable.
> Sources: **W17** 1a, §Data management; What not to put under version control · **TW17** Rule 1 · **W14** Practice 3c, §Make Incremental Changes · **RSQ** software_project_structure §"Use version control · **TW** vcs-checklist §"Make Sure That Your Data Is — 5 sources.
> Note: One source says to version-control small files that can be regenerated [W17], and another says not to [TW17]. On tools for data, one source calls them immature [W17] while another recommends considering them [TW], and the only numerical guide given is GitHub's per-file limit (100 MB) [W17].

### S8.8 (SHOULD) Deposit published data and code in a repository that issues DOIs
Deposit published data and code in a trusted repository that issues persistent identifiers such as DOIs (e.g., Zenodo, Figshare, Dryad, domain-specific repositories) so that others can access and cite them. Development hosting services do not guarantee long-term preservation, so distinguish them from archives.
> Sources: **W17** 1g, §Data management; 2j, §Software · **TW** rdm-fair §"Theory"; open-checklist §"Open Data; compendia §"Publishing a Compendium · **RSQ** life_cycle §"Archiving", §"Research Software Lifecycle; releasing_software §"How to Create Code Releases — 3 sources.

### S8.9 (SHOULD) Do not put sensitive data in public repositories
Handle data "as open as possible, as closed as necessary." Do not put data whose sharing is legally prohibited, or data containing sensitive information, in public version control systems or public repositories.
> Sources: **TW** rdm-fair §"Theory; overview-definitions §"Reproducible But Not Open · **W17** Inadvertent sharing · **RSQ** software_project_structure §"Use version control — 3 sources.
> Note: The sources agree and none dissent, but none states this as a must. If an organization makes it mandatory, place it in Appendix C.

## S9. Analysis code, libraries and notebooks

The evidence for this section is thin. Few sources discuss separating libraries from analyses or the handling of notebooks, and each clause below rests on one or two sources.

### S9.1 (SHOULD) Apply different standards to one-off analysis code and to code that is reused or handed over
Do not require comprehensive documentation or flexible configuration of analysis code that answers a single question about a particular dataset once. Once code is used repeatedly for slightly different purposes (as a guide, TW17 suggests three or four times), becomes important to a publication or the lab, or is handed over to someone else, consider hardening it with tests, documentation, and packaging. Publish analysis code as well, together with the paper or other output, once it is complete.
> Sources: **TW17** §Introduction; §Conclusion · **RSQ** life_cycle §"Research Software Lifecycle & Three-Tier; three_tier_view §"History and context — 2 sources.
> Note: RSQ itself states that there is not yet consensus on which quality indicators should be addressed at each tier [RSQ].

### S9.2 (SHOULD) Make code used in multiple projects into a separate package
If you are building tools used in multiple projects, make the shared code a separate project and bundle its modules into a package.
> Sources: **W17** 4a, §Project organization · **TW** code-reuse-overview §"Re-runnable Recommendations — 2 sources.
> Note: No source gives a criterion for how much to separate.

## S10. Reporting uncertainty and limitations

### S10.1 (SHOULD) Accompany results with an estimate of uncertainty and how it was obtained
When reporting results, include a quantitative estimate of uncertainty, a qualitative description of it, or an explicit statement that neither is available. Even for deterministic analyses, state what the result represents (e.g., minimum, most likely value, maximum). When giving uncertainty, explain the method used to obtain it (e.g., Monte Carlo, test data), how thoroughly its sources were identified, and why that method was chosen.
> Sources: **N7009** 4.3.8.2 [M&S 33] [NASA scope] (must); 4.3.8.3 [M&S 34] [NASA scope] (must) · **N7009H** §4.4 g; §5.6.3.1 — 2 sources.
> Note: The evidence comes from a single NASA lineage only, and the N7009 must applies only when the results of models that support critical decisions are reported to decision makers. This corpus provides no basis for requiring uncertainty reporting in research papers in general.

### S10.2 (SHOULD) Identify, classify, and record sources of uncertainty
Identify the sources of uncertainty (inputs and parameters, model construction, run settings, numerical error, output analysis), and record them qualitatively or quantitatively for the inputs, the results, and quantities derived from the results. Classify each source as aleatory or epistemic, and do not casually classify a source as aleatory (irreducible). Also record important physical processes, effects, and scenarios that were not considered.
> Sources: **N7009** §3.2 Aleatory / Epistemic Uncertainty, 13; §3.2 Uncertainty Characterization · **N7009H** §4.5; §5.2.1.3.3 — 2 sources.
> Note: The evidence comes from a single NASA lineage only, and the N7009 must applies only to models that support critical decisions. Tracking uncertainty in a ledger (N7009H) is an example and is optional.

### S10.3 (SHOULD) Record the scope and results of sensitivity analysis
Use sensitivity analysis to identify the main drivers of the results, and record the ranges of the inputs varied and the results. Keep the ranges of variation within the permissible uses. Where possible, compare whether the model's sensitivities are consistent with those of the real system, and document the comparison.
> Sources: **N7009** 4.3.5 [M&S 30] [NASA scope] (must); App. E.4.5 · **N7009H** §5.6.2.3 [M&S 30]; §5.6.3.1.2; App. D.11 — 2 sources.
> Note: The evidence comes from a single NASA lineage only, and the N7009 must applies only to models that support critical decisions.

### S10.4 (SHOULD) Report runtime warnings and errors, and conditions that may invalidate results
Record the warnings, errors, and failure modes that occur during computation, and explain each one. When reporting results, present as explicit caveats any unmet acceptance criteria, violations of assumptions or limitations, unexplained warnings or errors, problems during setup or execution, and unresolved defects, and estimate their impact at least qualitatively. If there is no evidence that none of these apply, state that as well.
> Sources: **N7009** 4.3.1.6 [M&S 27] [NASA scope] (must); 4.3.2h · **N7009H** §4.4 g; §5.6.2.2 [M&S 27; Rec. 4.3.2h] — 2 sources.
> Note: The evidence comes from a single NASA lineage only, and the N7009 must applies only to models that support critical decisions.

### S10.5 (SHOULD) Use models within their permissible uses, and attach warnings to results obtained outside them
Each time you use a model or code, check the proposed use against its permissible uses (assumptions, parameter ranges, accuracy) and its domain of verification and validation (S2.2, S2.3). If you use it outside them, attach to the results a warning stating the type and degree of the limits exceeded and an assessment of the impact on the results (e.g., a flow calculation based on the perfect-gas assumption applied to high-temperature flow where real-gas effects appear).
> Sources: **N7009** 4.3.1.2 [M&S 23] [NASA scope] (must); 4.3.1.5 [M&S 26] [NASA scope] (must) · **N7009H** §5.6.1.2, Table 6 [M&S 22, 23]; §5.6.1.2 — 2 sources.
> Note: The evidence comes from a single NASA lineage only, and the N7009 must applies only to models that support critical decisions.

## S11. Publication, citation and maintenance

The FAIR4RS principles are positioned by their own authors as aspirational, and they do not indicate requirement strength [F4RS][TW]. FAIR says nothing about what software does, so ensure correctness through other practices such as testing [RSQ].

### S11.1 (MUST) License code before publishing or distributing it
Before publishing or distributing code that others are expected to use or modify, decide on the license to apply and include in the repository a plain-text LICENSE file containing its text. Making a repository public does not license it, and without a license others cannot legally reuse the code. Choosing a license and placing the file are covered in G4.
> Sources: **RSQ** licensing_software §"Why should you assign a license (must); licensing_software §"Why should you assign a license · **OSG** legal / Why do people care so much about the legal (must); legal / Are public GitHub projects open source? (must) · **NL** best_practices, §Licensing (must) · **W17** 3d, §Collaboration · **JOSS** RC §Software license; RC §Good practices [JOSS] (must) · **DLR** ERM.9, §Release Management [AC1+] (must); ERM.2, §Release Management [AC1+] · **F4RS** FAIR4RS Principles, R1.1 · **TW** code-reuse-overview §"Repeatable Recommendations; licensing §"What, How and Where of License · **TW17** Rule 2, README item 6 — 9 sources.

### S11.2 (SHOULD) Before publishing, check intellectual property, institutional policy, and legal compliance
Before publishing software, check the ownership of intellectual property (third-party rights, employer ownership, grant conditions) and your institution's open source and intellectual property policies, and notify the legal office or other relevant contact. When distributing outside the organization, also confirm compliance with export control regulations. The specific contacts and procedures differ by organization.
> Sources: **RSQ** licensing_software §"Why should you assign a license · **OSG** legal / Which open source license is appropriate for; legal / What if I want to change the license of my · **NL** best_practices, §Licensing · **DLR** ERM.10, §Release Management [AC1+] (must) — 4 sources.

### S11.3 (SHOULD) Publish the code that produced a paper's results at the same time as the paper
Publish the code that generated a paper's results (scripts, versions used, parameters, required input data, and intermediate results) at the same time the paper is published, in a form others can easily access. Publish analysis code together with the paper and dataset once it has answered the research question. Do not rely on PDF alone for supplementary material: put results likely to be reused in separate text-format files (e.g., CSV, JSON, HDF5), and commands and code in source files or shell scripts.
> Sources: **S13** Rule 10; Rule 10 (minimum) · **RSQ** life_cycle §"Research Software Lifecycle & Three-Tier; life_cycle §"Publication · **TW** compendia §"Summary", §"Motivation; compendia §"Publishing a Compendium · **W17** 2j, §Software; Supplementary materials · **TW17** Rule 4 — 5 sources.
> Note: Being reproducible does not go so far as to require the authors to support extension or reuse, and publication does not imply an obligation to provide support [TW]. Data that cannot legally be shared are excluded [W17].

### S11.4 (SHOULD) In papers, cite the software used at its specific version
When referring to software in a paper, whether your own or someone else's, cite the specific release used to obtain the results, with its name, authors, and a stable reference such as a DOI. Citing software is also a way to credit developers who are not coauthors of the paper. The sources do not go so far as to require citing every dependency in the paper.
> Sources: **RSQ** life_cycle §"Publication"; citing_software §"Why does; citing_software §"What is software citation? · **NL** best_practices, §Releases · **TW** code-reuse-overview §"Repeatable Recommendations — 3 sources.

### S11.5 (SHOULD) Provide a citation file and citation metadata for software you want cited
For software you want cited, place a citation file (CITATION.cff is common) in the root, stating how to cite the project as a whole and where to find and how to cite data, code, and figures that have their own DOIs. Also include citation metadata in the README, or point to the citation file. Include in the citation metadata the name, the specific version used in the research, the authors, a stable reference such as a DOI, and the repository URL; you may add the authors' ORCID iDs (MAY).
> Sources: **W17** 3e, §Collaboration; What we left out, A bibliography manager · **RSQ** citing_software §"What is software citation?; documenting_software_project §"How to document your · **CFF** RM §Why should I add a CITATION.cff file to my; SG §Typical example · **TW** code-reuse-overview §"Repeatable Recommendations; compendia §"Executable Compendium · **NL** best_practices, §Releases — 4 sources.
> Note: No source requires the CFF format. W17 gives a plain-text CITATION file as an example, and RSQ says only that CFF has advantages. In CFF, version, date-released, doi, and repository-code are optional keys.

### S11.6 (MUST) If you provide CITATION.cff, make it conform to the specification
This applies only when you provide a CITATION.cff file. Name the file exactly CITATION.cff, including case; include the required keys cff-version, message, title, and authors; and make it a valid YAML 1.2 file that conforms to CFF schema 1.2.0. Include authors, title, and type in each reference under references or preferred-citation. Check it with a validation tool (e.g., cffconvert) (SHOULD).
> Sources: **CFF** SG §General structure of a CITATION.cff file (must); SG §Minimal example; §Valid keys (must) · **RSQ** citing_software §"What is software citation? — 2 sources.

### S11.7 (SHOULD) Deposit the version a paper refers to with a DOI, and give each version an identifier
When submitting a paper, deposit the code, like the data, in a trusted repository that issues DOIs (e.g., Zenodo, Figshare), and refer to it from the paper by that DOI. So that the versions subject to citation and reproduction can be distinguished, give each released version its own persistent identifier (e.g., DOI, SWHID), and list it separately from the identifier that covers all versions (concept DOI).
> Sources: **W17** 2j, §Software · **RSQ** life_cycle §"Archiving", §"Research Software Lifecycle; releasing_software §"How to Create Code Releases · **JOSS** SUB §Review Process [JOSS] (must); SUB §Other venues for reviewing and publishing · **F4RS** FAIR4RS Principles, F1; FAIR4RS Principles, F1.2 · **TW** compendia §"Publishing a Compendium · **CFF** SG §Valid keys, doi / identifiers; §definitions.doi — 4 sources.
> Note: W17 asks for a single deposit at paper submission, and JOSS asks for a DOI for the accepted version. Only RSQ and F4RS (aspirational) call for an identifier per version.

### S11.8 (SHOULD) Archive releases in long-term preservation services so they remain available after development ends
Archive released software in services intended for long-term preservation (e.g., Software Heritage, Zenodo), and also archive the main deliverables at phase boundaries and at the end of the life cycle. GitHub and GitLab are not designed to guarantee long-term preservation, so do not rely on them alone. After development ends, keep the code, including past versions, and its metadata available through standard protocols, and keep the metadata accessible even if the software itself is no longer available.
> Sources: **RSQ** life_cycle §"Archiving", §"Research Software Lifecycle; releasing_software §"How to Create Code Releases · **N7009H** §4.6 b; §5.5.5–5.5.5.1 · **DLR** ERM.11, §Release Management [AC3+] · **F4RS** FAIR4RS Principles, A1; FAIR4RS Principles, A2 · **TW17** Rule 4 · **TW** code-reuse §"Reusable Code"; open-checklist §"Open; vcs-git-in-research §"Fictive example · **NL** CONTRIBUTING, §Create a PDF file — 6 sources.
> Note: The sources disagree on where to preserve releases. RSQ names Software Heritage and Zenodo, while N7009H names the organization's management of work products.

### S11.9 (SHOULD) Host code in a public repository likely to last, and register it in a registry where possible
Host published code in a freely accessible public repository that is likely to last a long time (e.g., GitHub, GitLab, language-specific package repositories). Ideally, also register it in a general-purpose or domain-specific software registry (e.g., bio.tools, Research Software Directory). JOSS makes it a submission requirement that the hosting let outside people clone, browse, open issues, and propose changes without manual account approval or payment.
> Sources: **RSQ** fair_rs §"How can we make our software findable? · **TW** code-reuse §"Reusable Code"; open-checklist §"Open · **JOSS** RC §Where can the repository be hosted? [JOSS] (must); SUB §Submission requirements [JOSS] (must) · **F4RS** FAIR research software examples, Comet; FAIR research software examples, PuReGoMe — 3 sources.

### S11.10 (SHOULD) Provide metadata in a machine-readable standard format, and include references to the work you build on
Place a metadata file in an interoperable standard (e.g., CodeMeta, Citation File Format) alongside the source code, describing the identifier, version, authors, dependencies, license, description, and keywords. When you build on other people's software or data, record references to it in the metadata to credit its creators (e.g., references in CITATION.cff, softwareRequirements in codemeta.json). The metadata needed depends on the purpose: citation metadata matters for academic credit, versions and dependencies for reproduction, and keywords and descriptions for discovery.
> Sources: **RSQ** life_cycle §"Publication"; software_metadata; software_project_structure §"Directory structure · **F4RS** FAIR4RS Principles, F2; FAIR4RS Principles, F3 · **TW** open §"Summary · **JOSS** SUB §Submission Process · **CFF** SG §Referencing other work; SG §Valid keys, references — 5 sources.
> Note: No source specifies whether to use CodeMeta or CFF. JOSS treats metadata files as optional [JOSS].

### S11.11 (SHOULD) Refer to the FAIR4RS principles as aspirational goals, and work on them from development onward
Refer to the FAIR4RS principles as aspirational goals when publishing and preserving research software, and apply them while distinguishing whether each principle targets the software itself (F1, F1.1, F1.2, A1, I, R) or its metadata (F3, F4, A2). Treat meeting individual principles as optional, and use checklists and FAIRness assessment tools for diagnosis, to find areas for improvement, not for scoring. Do not leave making software FAIR and preparing metadata and citation metadata until publication; start early in development and finish no later than the preparation for publication.
> Sources: **F4RS** FAIR4RS Principles, F; Discussion, Research community maturation · **TW** code-reuse §"Reusable Code"; open-checklist §"Open; rdm-fair §"Theory", §"The FAIR Principles · **RSQ** quality §"FAIR Research Software"; fair_rs §"Tools and; life_cycle §"Publication"; software_metadata · **NL** best_practices, §Checklists — 4 sources.
> Note: Making software FAIR does not replace quality assurance; correctness and operation on multiple platforms must be ensured by testing and other means [RSQ]. Community practices for identifiers and metadata vocabularies are not yet standardized, and the principles themselves are scheduled for revision [F4RS][F4RS].

### S11.12 (SHOULD) Distinguish FAIR from open, and handle access-restricted material within the FAIR framework
FAIR is not synonymous with open. Handle software and data that require access restrictions within the FAIR framework as well, through retrieval procedures with authentication and authorization and through publication of their metadata, following the principle "as open as possible, as closed as necessary."
> Sources: **F4RS** FAIR4RS Principles, A1.2 · **TW** rdm-fair §"Theory; overview-barriers §"Support additional users — 2 sources.
> Note: TW concerns data; no source explicitly states "as closed as necessary" for software.

### S11.13 (SHOULD) Before publishing, use a checklist to confirm that everything needed is in place
When publishing software, check the following items with a checklist.
- Release notes, changelog, and documentation
- Metadata for discovery, and records of dependencies and the environment
- Open license, citation file, contributing guide, and code of conduct
- Stable, versioned release
- Archive with a DOI
> Sources: **RSQ** publishing_software · **OSG** starting-a-project / Launching your own open source; starting-a-project / Your pre-launch checklist · **NL** best_practices, §Checklists · **JOSS** SUB §Pre-review screening criteria, 3 [JOSS] (must) — 4 sources.
> Note: The weight of each item varies with the tier and purpose of the software [RSQ].

### S11.14 (SHOULD) For software you plan to publish, consider developing it in the open from an early stage
For software you plan to publish, rather than publishing it all at once after completion, consider moving to development in a public repository once you are comfortable with others seeing it. If you plan to submit to JOSS, move to open development early: JOSS requires more than six months of public development history before submission and does not count software developed privately and recently made public as having that history.
> Sources: **OSG** starting-a-project / Launching your own open source · **RSQ** life_cycle §"Release & Deployment · **JOSS** RC §Open development; SUB §Scope and significance [JOSS] (must) — 3 sources.
> Note: OSG says that going public is fine at the idea stage, partway through the work, or after years of being closed, and leaves the timing to the author's judgment.

### S11.15 (SHOULD) Designate who is responsible for maintenance, and do not let maintenance depend on one person
For each piece of software that is published or shared, designate a person (or organization) who understands the overall technical picture and is responsible for quality and maintenance, and before publishing, also decide who will maintain it and respond to issues and PRs after publication. Even as the sole maintainer, document important decisions, the architecture, and operational knowledge in the repository, and where possible share ownership with a second person who can respond in your absence. For public projects, OSG lists having at least two people with administrator rights as a pre-launch check item.
> Sources: **DLR** §Definition of the intended Application Class, Figure 1; §Evaluation of the achieved Application Class; C3 · **OSG** starting-a-project / Setting your goals; starting-a-project / Your pre-launch checklist · **N7009** 4.1.1.3 [M&S 41] [NASA scope] (must) · **F4RS** FAIR research software examples · **RSQ** software_maintenance §"How do you maintain your — 5 sources.
> Note: No source specifies the authority, term, or succession procedure for the responsible person. Departure and handover are covered in G13.

### S11.16 (SHOULD) For software that is used long-term or shared, create a software management plan proportionate to its scale
This applies only to software that will be used for a long time or shared with others. In the software management plan, state how the software will be developed, maintained, published, and archived (and, if necessary, retired) and who is responsible, at a level of detail proportionate to the nature and intended use of the software.
> Sources: **NL** best_practices, §Software management plans · **RSQ** life_cycle §"Lifecycle Stages"; three_tier_view; three_tier_view §"History and context · **N7009** 4.1.1.3 [M&S 41] [NASA scope] (must) · **F4RS** Discussion, ELIXIR; Discussion, Netherlands eScience Center — 3 sources.

### S11.17 (SHOULD) Plan maintenance as an ongoing cost, and tell users the status of maintenance
Plan the maintenance of shared or published software as an ongoing cost, and explicitly set aside time for maintenance and refactoring. Tell users honestly how much time is available for maintenance (e.g., part-time, volunteer), and if maintenance can no longer continue, do not silently abandon the software; signal this with a prominent notice in the README, by archiving the repository, or with a deprecation statement. The scope of maintenance grows with the number of users, but do not wait until the software is widely used to establish good habits.
> Sources: **RSQ** software_maintenance §"How do you maintain your; software_maintenance §"How do you track and manage · **OSG** starting-a-project / Setting your goals; starting-a-project / Writing a README — 2 sources.

### S11.18 (SHOULD) Write known problems, limitations, and uses to avoid where users will see them
Record known bugs, important unresolved issues, and status such as "incomplete" or "not ready for production use" where users will see them, such as the README, issue tracker, and release notes. List in the release notes existing errors that could not be fixed before approval. For models used in decision making, maintain records of their permissible uses and limitations.
> Sources: **DLR** EÄM.5, §Change Management [AC1+]; ERM.6, §Release Management [AC1+] · **RSQ** life_cycle §"Maintenance"; software_maintenance §"How · **OSG** starting-a-project / Writing a README · **N7009** 4.2.1.6 [M&S 13] [NASA scope] (must); 4.2.1.7 [M&S 14] [NASA scope] (must) · **N7009H** App. C.2–C.3 [Rec. 4.2.2e] — 5 sources.

### S11.19 (SHOULD) Credit contributors and funders
Identify the individuals who contributed to the software in the authors of CITATION.cff, the acknowledgments in the README, or an AUTHORS/CONTRIBUTORS file. If individual names are not known, use the name of the group. Also acknowledge funding agencies, after checking each agency's policy.
> Sources: **CFF** SG §How to deal with unknown individual authors?; SG §Valid keys, authors (must) · **RSQ** life_cycle §"Publication"; citing_software §"Why does; documenting_software_project §"How to document your · **TW17** Rule 2, README item 6 · **JOSS** SUB §Review Process; SUB §Policies [JOSS] (must) · **NL** CONTRIBUTING, §Technical — 5 sources.
> Note: No source gives criteria for whom to include as authors of software. JOSS gives only criteria for coauthorship of the paper.

### S11.20 (SHOULD) If you publish a paper about the software itself, have minimum robustness and openness practices in place
If you publish a paper about the software itself, have the following in place so that others can install it and verify its core functionality. For new software, have colleagues try it out.
- Versioned release
- README, installation instructions, and usage examples
- Explicit dependencies and automated installation
- Automated tests, or documented verification procedures
- Contributing guide and a point of contact for support
> Sources: **TW17** §Introduction · **JOSS** RC §Good practices; RC §Documentation, Installation instructions — 2 sources.
> Note: TW17 cautions that not all scientific software needs to be robust.

### S11.21 (SHOULD) Disclose the use of generative AI, and verify AI-drafted material before publishing
When submitting a software paper, disclose the use of generative AI in code, documentation, and paper writing (tools, models, and versions; where it was used; the extent of assistance; and a statement that people reviewed and verified all output), and if none was used, say so. For JOSS submissions this disclosure is required (MUST), and incomplete or inaccurate disclosure may be treated as an ethics violation. Before publishing a README or other material drafted with AI assistance, verify that its commands, requirements, and citation metadata are correct.
> Sources: **JOSS** RC §Software paper content, AI usage disclosure [JOSS] (must); SUB §AI Usage Policy, Author use [JOSS] (must) · **RSQ** creating_good_readme §"What sections should you — 2 sources.
> Note: JOSS is the only source that requires disclosure of generative AI use; RSQ addresses only verification. General rules on AI use are covered in G12.

---

# Appendices

## Appendix A. Adopting practices in stages

The sources agree that practices should be applied in stages according to the nature of the software (G1.1). However, each source draws its stages differently, and the stages have not been mapped onto one another: RSQ (three_tier_view, quality guidance for each tier; contested). The table below lists each source's stages as they are, without mapping them.

### A.1 Stages by source

| Source | Stages | Criteria |
|---|---|---|
| DLR | Application classes 0–3 (cumulative) | AC0: personal use, not used by colleagues; no recommendations: DLR (Application Class 0; Definition of the intended Application Class). AC1: software distributed to third parties outside the institute must be at least AC1: DLR (Definition of the intended Application Class, "Distribution of the software"; must). AC2: if the functional scope is large and development continues after the knowledge holders leave (roughly more than 2 years), the software must be AC2: DLR (Definition of the intended Application Class, "Period of further development"; must). AC3: high impact of failure: DLR (Definition of the intended Application Class, "Risks for the facility"). "Small" means at most 1 person-year of total effort, and lines of code are not used as a criterion: DLR (Definition of the intended Application Class, "Scope"). |
| W17 | Beginners and small projects (one person or a few, days to months) | Deliberately leaves unit tests, coverage, CI, build tools, branches, comprehensive documentation, code review, and pair programming out of its recommendations: W17 (What we left out, pp. 17–19). |
| W14 | Researchers already doing a substantial amount of computing | Recommends some of the practices W17 left out, such as unit tests, build tools, and code review: W14 (Practices 5b, 2c, 8a). But do not adopt them all at once: W14 (Conclusion). |
| TW17 | Software that is the subject of a paper | The 10 rules apply "at least" to software that is the subject of a paper: TW17 (Introduction). One-off analysis code is excluded: TW17 (Conclusion). Triggers for considering hardening: the code is used three or four times for slightly different purposes, becomes important to the lab, or is handed to others: TW17 (Conclusion). |
| RSQKit | Three tiers (analysis code / prototype / research software infrastructure) | Higher tiers call for more quality indicators: RSQ (life_cycle, Three-Tier Model; three_tier_view, History and context). Throwaway scripts do not even need static analysis: RSQ (static_analysis). Do not wait until the software is widely used to establish good habits: RSQ (software_maintenance). There is no consensus on a table mapping indicators to tiers: RSQ (three_tier_view, quality guidance for each tier; contested). |
| NASA-STD-7009 | Criticality (consequence of the decision × influence of the results on the decision) | The standard applies to models and simulations that support critical decisions: N7009 (§4.1.1.2); N7009H (§5.1.1.4). |

### A.2 DLR practices by application class

DLR assigns its 77 recommendations to application classes (class 1: 25; class 2: +33; class 3: +19). Clauses in the main text that rest on DLR carry an `AC n+` tag in their evidence line. The main ones are listed below.

- **From class 1** (code used on an ongoing basis or shared): version control and single-purpose commits DLR (EÄM.7; EÄM.8); a README or CONTRIBUTING file DLR (EÄM.2); a list of known issues DLR (EÄM.5); automation of simple builds DLR (EAA.1); a dependency list (name, version, license, source) DLR (EAA.2); basic tests in an environment close to operation DLR (EST.4); release numbers and tags DLR (ERM.1); license and export-control checks DLR (ERM.9; ERM.10); modularization and naming and style rules DLR (EDI.2; EDI.1); a brief record of design decisions DLR (ESA.2); problem definition and constraints DLR (EAM.1; EAM.3).
- **From class 2** (long-term, many contributors): a list of requirements with periodic review DLR (EAM.2; EAM.9); quality requirements and user groups DLR (EAM.4; EAM.5); mapping of the architecture to the requirements DLR (ESA.7); module tests and a test strategy DLR (EDI.3; EST.1); test automation DLR (EST.2); automated style checks DLR (EDI.7); integration builds (CI) DLR (EAA.8); a changelog DLR (EÄM.6); a roadmap DLR (EÄM.4); regular releases DLR (ERM.3); a documented and automated release procedure DLR (ERM.4); a branching strategy DLR (EÄM.9); coverage trends DLR (EST.8).
- **From class 3** (high impact of failure): risk management DLR (EAM.6); requirements traceability DLR (EAM.10); structured code review DLR (EDI.11); a regression test for every non-trivial bug DLR (EST.5); elimination of non-deterministic tests DLR (EST.6); trend analysis of metrics DLR (EST.7; EST.9); automated release builds DLR (EAA.9); long-term preservation of dependent software DLR (EAA.4); a shortened release procedure for critical errors DLR (ERM.5).

### A.3 Sandve's "as a minimum"

Sandve et al.'s 10 rules attach an "as a minimum" fallback to almost every rule, for example S13 (Rule 4, minimum). All S13 statements are should. The main text gives the full form and the minimum form side by side, and marks a clause Required only when another source states it as must (see S7).

## Appendix B. Commonly held beliefs without support in the sources

These are practices that are widely followed or believed but that the sources in this corpus do not support, or support only in part. Each item gives the commonly held belief and only what the sources actually say.

### Version control and configuration

- **Commit the lock file (commit it for analyses, not for libraries).** No source mentions "lock file" or treats analyses and libraries differently; the sources support only recording exact versions (RSQ (software_maintenance), S13 (Rule 3), DLR (EAA.6)), and RSQ (reproducible environments) says that in many cases specific versions need not matter.
- **Specify version ranges for libraries and pinned versions for applications (analyses).** No source states this split; ranges appear only in CP and in examples (SEMVER, TW17 (Rule 6)), and pinning only in RSQ (software_maintenance).
- **Never commit generated files.** TW17 (Rule 1) and TW (vcs-checklist) support this, but W17 (What not to put under version control) recommends versioning small data and results, so there is no basis for an absolute ban.
- **Use Git LFS for large files / keep data over 1 MB out of Git.** W17 judged LFS (as of 2017) not mature enough to recommend, MS gives it as one example, and RSQ (using_version_control) says only that it "may be considered". The only numeric figure is GitHub's 100 MB per-file limit cited by W17; RSQ (software_project_structure) gives no threshold.
- **Everyone uses feature branches and PRs from the start.** W17 (What we left out, Branches) does not recommend them to beginners and TW17 says "once you are comfortable"; only MS (ISE engagements) and CP (community packages) state them as must, and both assume team development.
- **Use Conventional Commits and generate the changelog from commits.** Only MS recommends this, as an example for deriving versions from commits (semantic-release versioning is may); CCOM, as a specification, only states its own benefits, and KC rejects commit-log diffs as changelogs.
- **Wrap commit messages at 50 characters for the subject and 72 for the body.** The 50-character figure appears only in TW (about 50) and MS (may), and the 72-character figure only in MS.
- **Always squash merge / keep a linear history.** MS recommends squashing, but MS also asks the team to choose between linear and nonlinear history, and CP discourages squashing during review.
- **Avoid GPL; use MIT.** W17 (3d) recommends permissive licenses, but OSG recommends GPLv3/AGPLv3 depending on the goal, and RSQ is neutral.
- **Put a CODE_OF_CONDUCT in every repository.** Only OSG says "every project"; RSQ limits it to projects with multiple contributors.
- **Produce an SBOM.** OSG only explains (as background) that it may be a legal obligation depending on the country, industry, or contract; the sources support only DLR's dependency list (EAA.2: name, version, purpose, license, source).
- **Standardize on a particular layout such as the src layout / decide between a monorepo and multiple repositories.** RSQ (software_project_structure) states there is no official standard and gives the src layout as may, and W17's layout is an example; on monorepos, W17 (4a) only gives guidance on how to split projects.
- **Sign commits and releases (GPG signatures, signed tags, published checksums).** A search of the original texts found nothing; the corpus does not cover this.
- **Update dependencies automatically (e.g., Dependabot) and audit them regularly for known vulnerabilities.** RSQ (software_maintenance) lists automated update PRs as may, with the emphasis on making updates deliberate and auditable. On vulnerabilities, MS only gives scanning tools as examples and RSQ recommends keeping dependencies few; no statement at should or above requires regular audits.

### Design and code

- **Avoid global variables; build on pure functions and push I/O, randomness, and time to the edges.** No source uses "global", "pure", or "side effect" for such a recommendation (in W14 and W17, "side effect" means a by-product of modularization); the closest is W17 (2b), that functions should not refer to outside information (should). An earlier draft cited W14, which does not say this, and JPF, which is not among the sources.
- **Replace magic numbers with named constants.** Neither term appears in any original; the closest are W14 (Practice 4a: define physical constants once), TW17 (Rule 3: do not hard-code values that may change), and MS (consider parameterizing; may). W14 is cited for this but does not say it.
- **Keep functions under N lines and N arguments; factor out code that appears three times.** The only line figure is W17 (2b: one page, about 60 lines), and argument guidance differs between W17 (5–6) and MS (more than 3 may be overly complex). No source gives a repetition threshold.
- **Implement checkpointing for long jobs.** N7009H lists it only as something to consider during requirements and design; no source requires implementing it, and none covers HPC job operations (scaling measurements, output volume estimates).
- **Optimization follows a fixed order, and "premature optimization is the root of all evil".** No source gives an order, quotes Knuth, or uses the term "premature optimization"; the support is for "get it working correctly first, then optimize with a profiler if worthwhile" (W14 (Practices 6, 6a), W17 (What we left out, Profiling)).
- **Comment performance tricks and non-obvious numerical tricks with the rationale, measurements, and references.** W14 is often cited for this but does not say it; the closest is GEP (reviewer guide, Comments), which treats explaining "what" the code does as a useful exception for complex algorithms.
- **Every TODO must carry an issue number.** In GEP (pushback, Cleaning It Up Later), a TODO referencing an issue is "optional", and RSQ (technical debt) asks for context but not a number.
- **Log through a logging framework, not print.** MS asks for logs to go to both the console and external infrastructure, but does not reject print.
- **Long names are cheaper than short ones / single-letter variables taken from equations are fine.** Neither is supported; GEP (Naming) also asks that names not be too long, and W17 (2f) allows single-letter names only for loop counters.
- **Never swallow exceptions; distinguish exceptions from error codes ("fail fast", "YAGNI").** No source addresses exception handling explicitly; fail fast, YAGNI, and dead code have partial support in substance, but the terms themselves are not used in a code-design context.
- **Keep the public API minimal.** DLR is cited for this but does not say it; only TW (reviewing checklist, Interfaces) speaks of minimizing, while SemVer and CP only ask that the API be declared and defined.
- **Rewrite legacy code.** No source supports this; RSQ (technical debt) calls large rewrites risky and recommends incremental improvement, and RSQ (static_analysis) introduces quality tools to existing code gradually, using a baseline.

### Testing and CI

- **Put unit tests, CI, coverage, and code review into all research code from the start.** W17 deliberately leaves these out for beginners and small projects, TW17 excludes one-off analysis code, and DLR has no recommendations at AC0; MS's CI/CD in Sprint 0 assumes enterprise client engagements, and RSQ requires CI at tier 3. At the higher levels, every source supports these practices.
- **The test suite must finish within N minutes.** No source sets a time limit; the support is for fast unit tests (BSSw, RSQ (testing_software)), timeouts on long verifications (MS (end-to-end tests)), and running slow tests nightly (TW (testing guidance), BSSw).
- **One assertion per test.** No source limits the number; RSQ's "verify one condition per test" and GEP (Tests) are not rules about counts.
- **Aim for 80% (or 100%) coverage.** The 80% figure is a parenthetical example in MS (code coverage checks), and the MS checklist says ">90% if possible" with no modal verb; RSQ warns against fixating on 100%, and W17 (What we left out, Coverage) does not recommend that beginners measure coverage at all.
- **TDD is mandatory.** No source makes it an obligation; MS and RSQ mention it only at the may level.
- **The test pyramid follows a ratio such as 70% unit, 20% integration, 10% E2E.** DLR (Software Test) names the concept without ratios, and TW goes only as far as "many fast tests, few slow ones".
- **Make flaky tests pass with automatic retries.** No source supports this; DLR (EST.6) asks that the cause be found and fixed.
- **Run the whole test suite in a pre-commit hook.** The support covers only fast checks such as formatters and linters (RSQ, NL).
- **Use Make.** It is may in TW (make), and W17 (What we left out, Build tools) leaves build tools out of its beginner recommendations, noting that shell scripts can substitute; the support is for regenerating results with a single command.
- **Never use mocks / always mock external dependencies.** Neither has support as a general rule, though replacing external dependencies in unit tests is supported (TW, MS, RSQ); "avoid mocks" appears only in CC, as an example of specifying a test policy in a prompt.
- **Use property-based testing, mutation testing, and fuzzing.** These appear in none of the sources in this corpus.

### Documentation and release

- **Run code examples in documentation as automated tests (doctest, etc.).** "doctest" appears zero times in the originals, and the nearest statements (CP, DLR (EDI.3), DX, RSQ) do not say to run examples as tests.
- **Require a docstring on every public function and type, and track docstring coverage numerically.** Every source, including W14 and W17, is at should or below; an earlier draft cited W14 and W17 for a Required level, but neither makes this mandatory. No source gives a numeric target; JOSS (review criteria, API documentation) only grades "all functions/methods" as good and "core API only" as acceptable.
- **Deprecation periods are "two minor versions" or "six months".** The only minimum rule is SEMVER (FAQ on deprecation): at least one minor release containing the deprecation before removal. No source sets a period.
- **Record design decisions as ADRs and use a specific documentation tool (e.g., Sphinx, Read the Docs).** "Architecture decision record" appears in no original, and the support is for recording design decisions and their reasons; all documentation tools are examples (W14 (Practice 7c), RSQ).
- **SemVer is the only correct scheme.** RSQ also lists calendar versioning, and DLR's X.Y.Z (ERM.1) means something different from SemVer; the support is for choosing and declaring a scheme.
- **Maintain LTS branches and backport fixes.** CP mentions possible backports only in the context of Julia version compatibility; no source recommends maintaining LTS branches.
- **Do not release 1.0.0 until the software is complete.** SEMVER (FAQ on 1.0.0) says the opposite: if the software is used in production or users depend on it, it should already be 1.0.0.

### Review and collaboration

- **All research code, including solo work, goes through PRs and review.** CP and W17 (What we left out, Code reviews) state explicitly that this does not apply to solo developers or beginners, DLR starts it at class 3 (EDI.11), and RSQ at tier 2. JOSS accepts single authors given evidence of community engagement.
- **PRs under N lines (e.g., 400), always two approvers.** No source sets a strict limit; GEP (small CLs) gives 100 and 1000 lines as rough figures and leaves the call to the reviewer, and MS says to use common sense. Approver counts differ: one approval in CP, usually two in MS, one or two in DLR (EDI.11).
- **Complete reviews within 24 hours.** GEP's "one business day at most" is a target for the first response and assumes an in-house full-time team; MS lets each team set its own SLA and OSG gives 7 days only as an example, and no source sets a completion deadline.
- **Always pair program.** W14 (Practice 8b) does not recommend constant use and limits it to onboarding and hard problems, and W17 leaves it out.
- **Code review finds 60–90% of defects.** RSQ's figure is background about rigorous inspections (Fagan 1976), and W14's "most cost-effective" claim is also background.
- **Issue and PR templates are mandatory.** No source requires them; OSG suggests trying them or using them against low-quality contributions, and MS says to follow a template where one exists.
- **AI review can replace human review.** No source says so; CC treats it as an additional independent check before completion, and CX as a way to align review standards between humans and agents.
- **ColPrac sets response deadlines for reviews and rules for writing issues.** It does not; ColPrac sets rules for approval and merging, for preparing PRs, and for releases.

### Security and AI

- **Revoke and reissue leaked secrets immediately and purge them from history.** No source states this; MS only links to a separate page on accidental commits, which was not collected.
- **Do not let AI-generated tests verify AI-generated code.** No source supports this; CC and CX recommend having the agent write and run tests, and CC (Writer/Reviewer; may) shows splitting tests and code across separate sessions. The closest statements, not letting the worker grade itself (CC; may) and review in a fresh context (CC), separate the grader from the worker.
- **Do not delegate judgments of scientific correctness to AI.** No source says this for developers; the closest are that JOSS authors make the core design decisions and are responsible for accuracy (JOSS), a rule for reviewers only (JOSS), and assessing the credibility impact of using AI in M&S (N7009H (§4.5.3)). The supportable statement is only that responsibility stays with humans.
- **Keep agents away from secrets (.env, keys, tokens) and defend against prompt injection.** Neither has a supporting statement; related are sandbox restrictions (CC (Configure permissions)), starting with strict permissions (CX), and not putting unpublished data into external AI services that train on it (N7009H (§4.5.3)). Prompt injection appears only in CC (example) about the product's classifier model, with no recommended countermeasures for users.
- **Mark all AI-generated code in commits or files, and disclose AI use in papers generally.** No source makes this an obligation; MS allows attribution through commit trailers (may), and JOSS's disclosure requirement applies only to JOSS papers.
- **Require approval for, or prohibit, AI use per lab or project.** No source supports this; JOSS (AI Usage Policy) permits AI use in most aspects of a submission.

### Verification and numerical computing

- **Comparing floating-point values with a tolerance is enough.** GB91 (Systems Aspects) says tolerance comparison is not a cure-all, pointing to the choice of tolerance, values of opposite sign, and the loss of transitivity. CP, TW17 (Rule 10), and N7009H (Table 7) assume tolerances, but no source covers choosing relative versus absolute error, ulp-based comparison, or deriving tolerances from condition numbers or discretization error.
- **Numerical results must match bit for bit, and regression tests should compare bitwise.** No source requires bitwise agreement across platforms or code changes; BSSw warns that binary-identity tests can hinder refactoring, CP does not treat changes in specific floating-point values as breaking, and GB91 (Sun addendum) shows that IEEE does not guarantee identical results. The support is for identical results with the same version and environment (TW17 (Rules 4, 10), S13 (Rule 6), RSQ).
- **Verify with the method of manufactured solutions, Richardson extrapolation, order-of-accuracy checks, and conservation tests.** None of these terms appears in the originals, and N7009H (§4.7) states that it prescribes no specific V&V techniques; the closest, N7009H (§5.5.2.2 b), describes checking convergence to the asymptotic limit but gives no extrapolation or order formulas.
- **Comparison with analytic solutions constitutes verification.** The comparison itself is supported (DLR (EST.4), TW (limitations of regression testing)), but no source says how to choose the problems or set the tolerances.
- **Pass random seeds as function arguments.** Passing them as CLI input parameters is supported (TW17 (Rules 3, 10)), but no source has a design rule for passing random number generators or seeds into functions or classes.
- **Use independent random streams in parallel computations.** No source supports this; TW (background) only notes the difficulty of getting identical results in parallel computing, with no recommendation on splitting streams or deriving seeds.
- **Record the source of physical constants (e.g., the CODATA version) and carry units in types (dimensional-analysis libraries).** "CODATA" appears zero times, and W14 (Practice 4a) only recommends defining constants once. For units, the sources ask only for recording and documentation (N7009 (4.2.1.3), N7009H (§5.2.1.3.4; §5.3.2), TW) and for separating units from values in data files (W17 (1d)); the means of implementation is not specified.
- **Document coordinate systems.** Only the NASA documents support this (N7009 (4.2.1.3), must for the M&S in scope; N7009H (§5.2.1.3.4; §5.3.2), should); the research-software sources do not mention coordinate systems.
- **Avoid value-changing optimizations such as -ffast-math and always compute in double (the highest) precision.** "fast-math" appears zero times, and GB91's statements on optimizers are addressed to compiler writers. On precision, GB91 (Ambiguity) says "compute every expression in the highest available precision" is not a good rule, and GB91 (Sun addendum) calls the belief that extended precision is always more accurate naive at best.
- **Manage nondeterminism from thread counts, BLAS, and MPI reduction order.** There is no specific support; DLR (EST.6, class 3 and up) only says that nondeterministic test results from thread interactions can indicate errors.
- **Test stochastic code with statistical tests.** No source supports this; the t-test in N7009H (§5.5.3.2.1) concerns comparing model results with reference data in validation, and CP does not address testing methods.
- **If it reproduces, it is correct.** TW calls reproducibility necessary but not sufficient for high-quality research, TW also says regression tests do not confirm correctness, and BSSw asks for independent verification of the reference output of no-change tests.
- **Validation always requires experimental data.** BSSw says experimental data is "usually" used; N7009H (App. B.2) lists experiments, test articles, and prototypes as referents, and DLR (EST.4) says comparison with known solutions or discussion with colleagues also works.

### Reproducibility and data

- **3-2-1 backups (three copies, two media, one off-site).** No source specifies the number of copies or media types; the sources call for backups to multiple locations (W17 (1b), TW), off-site storage when using external disks (W17 (1b)), syncing at least daily (W17 (5e)), and regular backups (RSQ).
- **Notebooks cannot be a unit of reproducibility.** The sources point the other way: RSQ treats notebooks as one packaging option and as one component of a research compendium. JOSS's exclusion of notebooks defines the journal's scope.
- **Monte Carlo results must always carry statistical errors.** No source says this in this form; N7009 (4.3.8.2; must, reporting to decision makers) requires uncertainty estimates but accepts a qualitative description or an explicit statement that no estimate exists, and N7009H (§5.6.1.3.4) only says to apply statistical analysis.

### Publication and citation

- **The CITATION.cff format is mandatory, and version and date-released are updated with every release.** No source mandates the CFF format; in CFF these keys are optional and no update timing is given (CFF (Valid keys)), and RSQ only describes cffinit as a tool for updating an existing file.
- **Document the authorship criteria for the software (who is listed as an author).** No source supports this; CFF and RSQ ask for authors to be listed but give no criteria, JOSS's criteria cover co-authorship of JOSS papers, and F4RS and OSG have no authorship criteria.
- **Archive both the library and the analysis repository when submitting a paper.** No source says this in this form; there are only pieces: deposit at submission (W17 (2j)), publication and archiving by tier (RSQ), a fictional example (TW), and research compendia (TW).
- **Cite dependencies in papers.** No source says this in this form; RSQ calls acknowledging software use by citation good practice but does not ask for completeness, CFF (Referencing other work) concerns references in metadata, and JOSS concerns citing prior similar work.
- **Putting the code on GitHub ensures long-term preservation.** RSQ (life_cycle, Archiving) explicitly denies this.
- **FAIR means open, and making software FAIR guarantees its quality.** TW (FAIR) denies the first, and F4RS (A1.2) allows authenticated access; RSQ says FAIR says nothing about the software's functionality.
- **All sources require a DOI for every release.** Only F4RS (as a goal) and RSQ ask for this; W17 addresses the time of submission and JOSS the accepted version, and NL and TW are silent.
- **JOSS requires CI and a code of conduct.** JOSS (review criteria, Tests) strongly encourages automated tests, rating CI integration as good and documented manual steps as acceptable, and CI is only one indicator in the pre-review screening of single-author submissions. There is no code-of-conduct requirement; JOSS asks for contribution guidelines and support channels (JOSS (Good practices; Community guidelines)).

### Principles and operations

- **There is an overall ranking of values (e.g., correctness > reproducibility > readability > performance).** No source has one; the only explicit contrasts are optimize after it works (W14, W17), write for people, not computers (W14), and technical facts override opinions (GEP), and N7009 scales rigor to the importance of the decision.
- **Classify software by lines of code and fix the required items per class in a mapping table.** DLR rejects lines of code as only partly correlated with size and uses total effort instead; RSQKit's tier table is a first proposal from an expert vote and is explicitly marked as lacking consensus (contested), and DLR's decision tree is likewise only a recommendation.
- **Fix detailed requirements before starting, and adopt a specific process (e.g., Scrum).** W14 says requirements for scientific software are not fixed, so methods that depend on upfront specification do not fit, and the documents DLR and N7009 require are brief and can be revised later. The research-oriented sources require no specific process, and N7009H only says that it does not exclude agile methods.
- **Devote a fixed share of time (a "20% rule", etc.) to technical debt.** RSQ only lists a fixed-share approach as an option, without any figure.
- **Set a numeric bus-factor target and follow an offboarding checklist.** The sources go only as far as sharing ownership with at least one other person (RSQ, if possible) and having at least two people with admin rights (OSG, pre-launch checklist). No source gives concrete offboarding steps; the related statements are only OSG (Share the workload), W14 (Document Design and Purpose; Collaborate), and RSQ (Three-Tier Model).
- **Following the procedure guarantees quality.** N7009H states that compliance with the standard does not correlate with the level of credibility achieved, and RSQ says metrics do not fully capture quality, which depends on context.
- **W14 also recommends CI, publication, and citation.** W14 does not mention CI; W17 (What we left out, Continuous integration) is the source that addresses CI explicitly, as something left out. W14 states that publishing and citing code and data are outside its scope.

## Appendix C. Candidate organizational policies

The clauses in the main text do not go beyond the strength of the sources. An organization may nevertheless want to make mandatory an item that the sources state only as should, or to decide a point on which the sources are silent. The items below were judged during synthesis as matters "to be handled as organizational policy" and are listed as candidates. **These items are not decisions of JuliaPackageFactory; they are a list of issues that need a decision.** If an item is adopted, write it as an organizational policy and state explicitly that the sources do not support it.

| Issue | What the sources say | Related clauses |
|---|---|---|
| Make mandatory the items on which the sources agree without dissent but none says must (writing tests, smoke tests, rerunning regression tests on every change, putting tests under version control, CI as a merge condition, version numbers and tags for releases, immutability of released versions, review of AI output, excluding secrets and restricted-share data, recording random seeds, immutability of raw data, each of Sandve's "minimum" items) | Agreement only up to should. | G6.2, G6.4, G6.21, G7.4, G11.1, S6.1, S7.1, S7.4, S7.10, S7.12, S8.1, S8.3, S8.4, S8.9 |
| Make it mandatory to keep secrets out of repositories | Four sources say should; none says must. | G3.3, G11 |
| Require pull requests in shared repositories | Only MS (enterprise client engagements) and CP (community packages) say must. W17 does not recommend them to beginners. | G3.11 |
| Set a default license | Most sources favor a permissive license as the first choice, but there is no basis for ruling out GPL. NL makes Apache-2.0 the default as an organizational policy. | G4.5 |
| Precedence when the style guide and existing code disagree | GEP treats the guide as absolute; RSQ and CP give priority to consistency with existing code. | G5.16 |
| Procedure and contact point for export-control checks | DLR says must. The procedure is specific to each organization. | G4.19 |
| Codify a ranking of values (correctness, reproducibility, readability, performance, in that order) | No source has an overall ranking. The explicit contrasts are three: "optimize after it works", "write for people", and "facts override opinions". | S1.6, S1 |
| Offboarding procedure | No source gives concrete steps. | G13 |
| Ranking of values and a mapping table for classes (what is mandatory in which class) | The sources' classes are not mapped onto one another, and RSQ states that there is no consensus. | Appendix A |
| How to determine tolerances | No source gives a method. | S4 |
| Rule for separating a paper's analysis code from libraries | Support is weak (S9). | S9 |
| Rule for updating CITATION.cff with every release | CFF states no update rule. | S11 |

---

## Appendix D. Sources

All sources were retrieved on 2026-10-19. Web sources change; when revising this document, retrieve them again. For Git-hosted sources the commit used is given.

### Scientific software practice

- **W14** — Wilson G, et al. "Best Practices for Scientific Computing." *PLOS Biology* 12(1): e1001745 (2014). https://doi.org/10.1371/journal.pbio.1001745
- **W17** — Wilson G, et al. "Good Enough Practices in Scientific Computing." *PLOS Computational Biology* 13(6): e1005510 (2017). https://doi.org/10.1371/journal.pcbi.1005510 — a revision of W14 for beginners; shared authors.
- **S13** — Sandve GK, et al. "Ten Simple Rules for Reproducible Computational Research." *PLOS Computational Biology* 9(10): e1003285 (2013). https://doi.org/10.1371/journal.pcbi.1003285
- **TW17** — Taschuk M, Wilson G. "Ten simple rules for making research software more robust." *PLOS Computational Biology* 13(4): e1005412 (2017). https://doi.org/10.1371/journal.pcbi.1005412
- **TW** — The Turing Way Community. *The Turing Way.* 27 chapters under reproducible-research (overview, vcs, testing, code-reuse, licensing, rdm, renv, reviewing, open, make, compendia). Commit c25d0befe9ce3a21ac46fa47b685db82ad169909 (2026-10-16). https://github.com/the-turing-way/the-turing-way
- **F4RS** — Barker M, et al. "Introducing the FAIR Principles for research software." *Scientific Data* 9: 622 (2022). https://doi.org/10.1038/s41597-022-01710-x
- **BSSw** — Bartlett R, et al. "Definition and Categorization of Tests for CSE Software." Better Scientific Software (2018). https://bssw.io/blog_posts/definition-and-categorization-of-tests-for-cse-software
- **RSQ** — EVERSE. *Research Software Quality Kit (RSQKit).* 25 pages. Commit cf49a090ce145e16130ad1985be641f38be0af72 (2026-10-09). https://everse.software/RSQKit/
- **JOSS** — Journal of Open Source Software. *Review criteria* and *Submitting a paper.* https://joss.readthedocs.io/

### Verification, validation and numerical computation

- **N7009** — NASA. *Standard for Models and Simulations.* NASA-STD-7009B (2024-03-05). https://standards.nasa.gov/standard/NASA/NASA-STD-7009
- **N7009H** — NASA. *NASA Handbook for Models and Simulations: An Implementation Guide for NASA-STD-7009B.* NASA-HDBK-7009B (2026-02-03). https://standards.nasa.gov/standard/NASA/NASA-HDBK-7009 — the implementation guide for N7009; treated as the same family. Page headers in the retrieved file still read DRAFT.
- **GB91** — Goldberg D. "What Every Computer Scientist Should Know About Floating-Point Arithmetic." *ACM Computing Surveys* 23(1) (1991). Reprint in Oracle's *Numerical Computation Guide*, Appendix D, used. https://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html — the reprint includes an addendum by Sun, "Differences Among IEEE 754 Implementations", which this document cites separately from Goldberg's own text.

### Institutional software engineering guidelines

- **DLR** — DLR (German Aerospace Center). *Software Engineering Guidelines.* English text and application-class checklists v1.0. https://rse.dlr.de/guidelines/00_dlr-se-guidelines_en.html
- **NL** — Netherlands eScience Center. *Guide.* Nine files excluding language guides. Commit 977965e18d983b363e88c9f2025250124032434a. https://github.com/NLeSC/guide

### General software development

- **GEP** — Google. *Engineering Practices Documentation* (Code Review Developer Guide: all reviewer and developer pages). Commit 3bb3ec2. https://github.com/google/eng-practices
- **MS** — Microsoft. *Engineering Fundamentals Playbook.* 17 pages (fundamentals checklist, source control, automated testing, CI, code reviews, security, documentation, observability). Commit 016770e. https://github.com/microsoft/code-with-engineering-playbook
- **CP** — SciML. *ColPrac: Contributor's Guide on Collaborative Practices for Community Packages.* Commit 48746d9. https://github.com/SciML/ColPrac
- **SEMVER** — *Semantic Versioning 2.0.0.* Commit f99d548. https://semver.org/
- **KC** — *Keep a Changelog 1.1.0.* https://keepachangelog.com/en/1.1.0/
- **CCOM** — *Conventional Commits 1.0.0.* https://www.conventionalcommits.org/en/v1.0.0/
- **DX** — Procida D. *Diátaxis.* start-here, tutorials, how-to-guides, reference, explanation, compass. Commit 957c09c. https://diataxis.fr/
- **CFF** — *Citation File Format.* README and schema guide. Commit dd51afe. https://citation-file-format.github.io/
- **OSG** — GitHub. *Open Source Guides.* legal, code-of-conduct, starting-a-project, best-practices. Commit e7ac98e. https://opensource.guide/
- **COC** — *Contributor Covenant 2.1.* https://www.contributor-covenant.org/version/2/1/code_of_conduct/

### AI coding agents (vendor documents; revised often)

- **CC** — Anthropic. *Claude Code: Best practices.* https://code.claude.com/docs/en/best-practices
- **CX** — OpenAI. *Codex: Best practices.* https://developers.openai.com/codex/learn/best-practices (redirected to learn.chatgpt.com at retrieval)

### Excluded

| Source | Reason |
|---|---|
| ISO/IEC/IEEE 12207 | Paid standard; the text could not be retained. |
| IEEE CS SWEBOK Guide v4 | Requires registration; not retrieved. |
| Oberkampf & Roy, *Verification and Validation in Scientific Computing* (2010) | A book; the text could not be retained. V&V concepts are taken from N7009 and BSSw instead. |
| obra/Superpowers | A personal prompt collection; judged unsuitable as a normative source for a public guideline. |

---

## Appendix E. How this document was made

1. **Collection.** The 26 sources above were retrieved and stored locally with URL, retrieval date, size, SHA-256, and commit hash. PDFs were converted with pdftotext and HTML with a standard-library script.
2. **Extraction.** For each source, every recommendation (and every explicit "do not") was tabulated with the source's own strength (must / should / may / example / background / contested / unspecified), its location, and a short identifying phrase: 1,789 rows in all. "Mentions" were distinguished from "recommends", and nothing absent from the source was added.
3. **Synthesis.** The rows were classified into 25 topics and consolidated across sources in ten groups into 415 draft clauses, each with its supporting rows, number of independent sources, agreement level (strong = 3 or more sources, medium = 2, weak = 1), scope conditions, conflicts between sources, and a proposed level. Each group also listed commonly held beliefs that the sources do not support.
4. **Drafting.** Draft clauses with strong or medium agreement, and weak ones that scientific computing needs, were rewritten as the clauses of this document (343 in the full edition). Levels never exceed the synthesis proposals, and all supporting rows were carried over.
5. **Audit.** Auditors separate from the drafters checked every cited row against the clause text, downgraded clauses whose only *must* was out of scope, removed wrong citations, and corrected source counts.
6. **This edition.** Clauses supported by three or more sources, all MUST clauses, and Part II clauses supported by two sources were kept (250 clauses). Row identifiers were replaced by source keys and locators so that the document is self-contained.

Extraction, synthesis, translation and audit were carried out by AI agents (Claude Opus) under a human editor who designed the procedure and made the final checks. Extraction by AI can still err; when a clause looks doubtful, follow the Sources line to the original.

---

*Published by [JuliaPackageFactory](https://github.com/JuliaPackageFactory). Text licensed under CC BY 4.0. Issues and pull requests are welcome; a proposed clause should name the source and the passage that supports it.*
