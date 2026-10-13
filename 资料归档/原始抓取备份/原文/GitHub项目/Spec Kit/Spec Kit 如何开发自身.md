How Spec Kit Develops Spec Kit: An Agentic SDLC | Spec Kit Documentation 
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      

      

      

  

  
    
      
        
          
            
            Spec Kit
          
          
            
          
          
            
              
                
                
              
            
          
        
      
    

    

      
        

          
        

        

How Spec Kit Develops Spec Kit: An Agentic SDLC

Spec Kit's own development combines agentic work with conventional software
delivery. Agents assess feature requests, help develop substantial changes,
and investigate reported bugs. The project also uses Spec Kit itself where
its processes fit, while deterministic tests and releases remain ordinary
GitHub Actions and maintainers make the decisions to proceed or merge.

Public workflows and a historical feature provide the evidence for this case
study. They show different paths through the project's SDLC, including where
the project uses Spec Kit itself.

The project's software development life cycle

The traditional stages locate the project's practices across its SDLC.

flowchart LR
    P["Planning"] --> Q["Requirements"] --> D["Design"] --> I["Development"]
    I --> T["Testing"] --> R["Deployment"] --> M["Maintenance"]

These stages are a map, not a required sequence. Work can begin wherever
its starting material and goal call for it: a feature request in planning,
an existing change in testing, or a bug report in maintenance. Stages can
overlap, repeat, or be skipped; one feature issue can feed an assessment,
record requirements, and carry design discussion. The arrows show one
familiar path, not mandatory transitions between SDLC stages.
Feedback from maintenance can inform the next planning cycle.

Who does the work

These modes illustrate how work gets done here; they are not an exhaustive
taxonomy or inventory of activities. A stage can combine them.

Regular automation. Scripts and bots follow predefined rules to run
checks, propose dependency updates, and publish releases. They do not
interpret feature requests or choose designs.

Agent automation. A coding agent interprets context and produces an
assessment, SDD artifacts, a proposed change, or a test report. It may run
through a contributor-invoked Spec Kit command, a contributor's own agent,
or a label-triggered repository workflow. Running an agent on GitHub
Actions does not make its reasoning a deterministic script.

A star (★) marks agentic work: before prose paragraphs and after
timeline items. A person (👤) highlights human contributions in the
stage narratives, including work done with an agent's help.

Human work. People can frame issues, discuss approaches, write or revise
changes, and review evidence, with or without an agent's help. Maintainers
apply labels to start repository workflows and decide whether to proceed
or merge. Those handoffs are human decisions, not evidence that people
authored every assessment, specification, fix, or test report.

How the project works at each stage

1. Planning: assess feature requests

A feature can enter planning through the
feature request form.
It asks for the problem, proposed solution, alternatives, component, and use
cases. The resulting issue starts with needs-triage; filing it does not
automatically launch an agent.

★ When a feature request is labeled for assessment, the
feature-assess agentic workflow
provisions the Specify CLI from the checkout, installs Spec Kit's bundled
assess extension, and follows its intake, research, define,
shape, and decide stages. The agent posts its evidence and a go,
needs-clarification, or kill verdict to the issue. This is an agentic
workflow using Spec Kit itself.

👤 Contributors frame the request; maintainers decide whether to
initiate assessment and how to act on its verdict. A go finding informs
that planning decision; it does not automatically open a feature PR or
start SDD.

2. Requirements: record the intent in issues or specs

👤 Contributors can record early requirements in the same feature issue
through its problem statement, use cases, and acceptance criteria, then
clarify or revise the intent during discussion. For a bounded change, that
may be enough; it does not have to become an SDD spec.md.

★ For substantial changes, contributors can instead expand the requirements
with Spec Kit's core SDD commands against the project's constitution. Spec Kit
used this process on itself in the
historical bundler work:
the commit contains a constitution and feature specification for the
specify bundle command. This is evidence of SDD dogfooding for that
feature; the bundler work is distinct from the current feature-assess
workflow. The
contribution guide
asks contributors to test relevant changes with SDD while allowing small fixes
to use the normal issue and PR process. Generated specs/ artifacts are
normally gitignored; the linked commit preserves a historical snapshot.

3. Design: choose an approach at the right scale

👤 The feature form's proposed solution and alternatives can seed a
design discussion; contributors and maintainers can refine the approach
during PR review. A separate SDD plan is not required for every issue.
Larger changes need prior discussion and agreement with maintainers, as the
contribution guide
explains. When decisions should remain useful across changes, the repository
keeps CLI,
integration,
and workflow-step
design documents.

★ The bundler SDD snapshot
illustrates the deeper path: its plan, research, data model, contracts, and
tasks made the design actionable through /speckit.plan and
/speckit.tasks. This shows where Spec Kit itself was used without
presenting that level of detail as the default for every change.

4. Development: make reviewable changes

The bundler implementation
added specify bundle after the recorded SDD work. This is a concrete
specification-led feature in the project. Other bounded changes use the
ordinary issue, PR, review, and test process.

👤 Contributors can implement and revise changes directly or with an
agent's help. Maintainers review the resulting PR and its evidence, even
when an agent produced the proposed change.

★ Contributors can use their own agents, independently of the repository's
workflows. Because that work may not be visible in a diff, the
contribution policy
requires disclosure of the tool, model, settings or mode, and extent of AI
assistance. Agent-authored commits and comments need their own attribution;
the known bug-fix and community-catalog workflows are exempt because their
agent identity is inherent. Disclosure provides provenance, not a lower
evidence or review bar.

5. Testing: verify both intent and behavior

★ The bundler work includes a
convergence pass
that appended a missing task. This is one example of Spec Kit's SDD process
checking implementation against intent rather than treating the first pass
as complete. The
bug-test workflow
uses an agent to select relevant tests, but the test commands themselves
still run deterministically.

Separately, conventional GitHub Actions run
Python tests and Ruff,
Markdown linting for documentation and ShellCheck for shell scripts,
and CodeQL
on PRs and pushes to main. Agentic convergence complements those
independent checks.

👤 Contributors supply tests and reproduction evidence for changes;
maintainers assess whether the patch and evidence address the stated need,
not just whether the checks passed.

6. Deployment: publish without an agent

The Spec Kit repository uses regular GitHub Actions for delivery. The
manually dispatched release trigger
sets the version, creates a tag, and opens a release PR. The tag triggers a
conventional
GitHub Release.
A separately dispatched workflow
builds and publishes to PyPI;
a docs/ change on main triggers
DocFX deployment.
These conventional Actions handle deterministic delivery without an agent
or a Spec Kit command.

👤 Maintainers choose when to start a release and whether to specify
a version rather than use the automatic patch increment. They later dispatch
PyPI publishing for that tag and review the release PR.

7. Maintenance: investigate, repair, and learn

👤 Reporters supply observed and expected behavior and reproduction
steps through the
bug report form.
Maintainers triage the report, choose when to request each agentic step,
and review the proposed fix and test evidence before merging. Reports can
arise before or after release; a newly discovered need can feed the next
planning cycle instead of being folded into a repair.

★ The repository uses separate
bug-assess,
bug-fix,
and bug-test
agentic workflows on issues. They separate diagnosis, remediation, and
verification with human-controlled handoffs; the fix is proposed as a draft
PR for maintainer review. Today these project
workflows do not consume Spec Kit's bundled
bug extension, even though it offers a corresponding
assess, fix, test process for users. Having the workflows use that extension
is a direction for future work, not a current capability or a promised
release.

Agentic and conventional workflows both run on GitHub Actions. What changes
is whether an agent interprets evidence or proposes a change.
Dependabot
also proposes weekly pip and GitHub Actions dependency-update PRs for
maintainer review; it is conventional automation, not an agentic workflow.

Extensibility and community practice

Extensibility cuts across this SDLC: the project builds and distributes
processes as well as using them. The team ships the
assess bundle
and bugfix bundle,
each combining an extension with a resumable Spec Kit workflow and a human
review gate. It also ships the
lean preset
to demonstrate an alternative way of shaping core SDD guidance. These are
Spec Kit's own extensions, presets, workflows, and bundles,
not the repository's GitHub Actions workflows.

★ Beyond core feature delivery, agentic community-submission workflows for
extensions,
presets,
and bundles
validate submission metadata and propose catalog changes in draft PRs for
maintainer review.

Catalog discovery does not audit or endorse community code; users must
review third-party components before use.

How we went from SDLC to an agentic SDLC

Agentic practices were layered into an existing SDLC, not substituted for
conventional checks and releases. The commit history shows how that mix
emerged over time. These are selected milestones, not an exhaustive
changelog. The same star marks agentic milestones, including use of Spec
Kit's SDD process; conventional automation and policies are unmarked.

Month
What changed

August 2025

GitHub Release automation was added.

September 2025

Documentation deployment was added.
Contribution guidelines began requiring disclosure of AI assistance, including contributor-run agents.

October 2025

Markdown linting was added to PR and main checks.

February 2026

Dependabot began proposing weekly pip and GitHub Actions dependency-update PRs.
A CodeQL workflow was added for code scanning.
Pytest CI began running tests on PRs and pushes to main.
Ruff linting was added to the Python CI workflow.

May 2026

Extension submissions gained catalog validation and draft PRs. ★
Preset submissions gained catalog validation and draft PRs. ★

June 2026

An agentic bug-assess workflow was added. ★
The bundler feature used Spec Kit's SDD process to produce its specification, plan, and tasks. ★
PyPI publishing gained a conventional workflow.
ShellCheck began checking shell scripts in CI.

July 2026

Bug-fix extended the agentic issue workflow with a human handoff. ★
Bug-test added a separate verification stage. ★
Bundle submissions gained catalog automation. ★

August 2026

The feature-assess workflow began using Spec Kit's assess extension on feature requests. ★

September 2026

AI disclosure began requiring agent, model, settings, and extent.
Contribution guidance added attribution for agent-authored commits and comments.

This was not a handoff from conventional automation to agents. Tests, lint,
scanning, and publishing kept repeatable work in GitHub Actions, while
disclosure made contributor-run agents visible. The project added agentic
catalog and bug workflows for bounded tasks, used SDD on a substantial
feature, and later made feature assessment consume Spec Kit's assess
extension. These were separate additions, not steps every issue must follow.

Together they give the project choices. For features, assess can inform a
human decision about a request; substantial implementation work can use SDD
to specify and plan it, as the bundler did. There is no automatic handoff
between those processes. A small change can stay in an issue and PR, while
a bug can move through human-gated assessment, repair, and verification.
Code changes still face conventional checks and maintainer review; issue
verdicts and catalog proposals have their own human gates.

A production practice, not a prescribed recipe

For this public open-source project, a new issue is untrusted input, not
permission to run an agent. A maintainer activates each repository-owned
agentic workflow with its label and decides when work advances; contributors
can also bring their own agents, subject to disclosure and review. These are
deliberate boundaries for this project, not inherent limits of Spec Kit.

A closed project with a different trust model could automate more handoffs
between assessment, specification, implementation, and testing, while
keeping permissions, evidence, and review appropriate to the risk. Spec Kit's
production mix is not a prescribed recipe: teams can adopt agents in
different places, with or without Spec Kit's processes. That mix can change
with the project's needs and practices.

Here, agentic does not mean human-free: contributors still bring problems,
requirements, and changes; maintainers steer product direction, guide
delivery, and review evidence. Conventional automation handles repeatable
checks and publishing, while agents help with bounded work that requires
interpretation. This division aims to focus human time on judgment, though
the timeline shows adoption rather than measured time savings.

        
          Edit this page
        

        

      

      
        
      
    

    

    
      
        
          Spec Kit - A specification-driven development toolkit