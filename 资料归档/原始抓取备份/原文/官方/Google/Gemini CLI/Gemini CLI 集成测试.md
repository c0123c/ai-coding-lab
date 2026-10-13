Integration tests | Gemini CLI
  Skip to content   
Unpaid tier and Google One users: Gemini CLI was replaced by Antigravity
    CLI on June 18th, 2026. To learn more, see our blog post.
                    Gemini CLI      
  Home  
  Plans  
   Extensions    
  Browse extensions  
  About Extensions     
  Docs  
  Reference  
  Resources  
  Changelog              Search  CtrlK       Cancel                 Feedback  GitHub  GitHub       Select theme   DarkLightAuto                      
  Home   
  Plans   
    Extensions       
  Browse extensions   
  About Extensions       
    Docs       
    Get started       
  Overview   
  Quickstart   
  Installation   
  Authentication   
  CLI cheatsheet   
  Gemini 3 on Gemini CLI       
    Use Gemini CLI       
  File management   
  Get started with Agent Skills   
  Manage context and memory   
  Execute shell commands   
  Manage sessions and history   
  Plan tasks with todos   
  Use Plan Mode with model steering 🔬  
  Web search and fetch   
  Set up an MCP server   
  Automate tasks       
    Features       
    Extensions       
  Overview   
  User guide: Install and manage   
  Developer guide: Build extensions   
  Developer guide: Best practices   
  Developer guide: Releasing   
  Developer guide: Reference       
    Agent Skills       
  Overview   
  Get started with Agent Skills   
  Creating Agent Skills   
  Using Agent Skills   
  Developer guide: Best practices       
  Auto Memory 🔬  
  Checkpointing   
  Headless mode   
  Git worktrees 🔬  
    Hooks       
  Overview   
  Reference       
    IDE integration       
  Overview   
  Developer guide: ACP mode       
    MCP servers       
  Overview   
  Resource tools       
  Model routing   
  Model selection   
  Model steering 🔬  
  Notifications 🔬  
  Plan mode   
  Subagents   
  Remote subagents   
  Rewind   
  Sandboxing   
  Settings   
  Telemetry   
  Token caching       
    Configuration       
  Custom commands   
  Enterprise configuration   
  Ignore files (.geminiignore)   
  Model configuration   
  Project context (GEMINI.md)   
  Settings   
  System prompt override   
  Themes   
  Trusted folders       
    Development       
  Behavioral evaluations   
  Contribution guide   
  Integration testing   
  Issue and PR automation   
  Local development   
  NPM package structure           
    Reference       
  Command reference   
  Configuration reference   
  Keyboard shortcuts   
  Memory import processor   
  Policy engine   
  Tools reference       
    Resources       
  FAQ   
  Quota and pricing   
  Terms and privacy   
  Troubleshooting   
  Uninstall       
    Changelog       
  Release notes   
  Stable release   
  Preview release                
    Get started       
  Overview   
  Quickstart   
  Installation   
  Authentication   
  CLI cheatsheet   
  Gemini 3 on Gemini CLI       
    Use Gemini CLI       
  File management   
  Get started with Agent Skills   
  Manage context and memory   
  Execute shell commands   
  Manage sessions and history   
  Plan tasks with todos   
  Use Plan Mode with model steering 🔬  
  Web search and fetch   
  Set up an MCP server   
  Automate tasks       
    Features       
    Extensions       
  Overview   
  User guide: Install and manage   
  Developer guide: Build extensions   
  Developer guide: Best practices   
  Developer guide: Releasing   
  Developer guide: Reference       
    Agent Skills       
  Overview   
  Get started with Agent Skills   
  Creating Agent Skills   
  Using Agent Skills   
  Developer guide: Best practices       
  Auto Memory 🔬  
  Checkpointing   
  Headless mode   
  Git worktrees 🔬  
    Hooks       
  Overview   
  Reference       
    IDE integration       
  Overview   
  Developer guide: ACP mode       
    MCP servers       
  Overview   
  Resource tools       
  Model routing   
  Model selection   
  Model steering 🔬  
  Notifications 🔬  
  Plan mode   
  Subagents   
  Remote subagents   
  Rewind   
  Sandboxing   
  Settings   
  Telemetry   
  Token caching       
    Configuration       
  Custom commands   
  Enterprise configuration   
  Ignore files (.geminiignore)   
  Model configuration   
  Project context (GEMINI.md)   
  Settings   
  System prompt override   
  Themes   
  Trusted folders       
    Development       
  Behavioral evaluations   
  Contribution guide   
  Integration testing   
  Issue and PR automation   
  Local development   
  NPM package structure                 GitHub     Select theme   DarkLightAuto                
    Reference       
  Command reference   
  Configuration reference   
  Keyboard shortcuts   
  Memory import processor   
  Policy engine   
  Tools reference                 GitHub     Select theme   DarkLightAuto                
    Resources       
  FAQ   
  Quota and pricing   
  Terms and privacy   
  Troubleshooting   
  Uninstall                 GitHub     Select theme   DarkLightAuto                
    Releases       
  Release notes   
  Stable release   
  Preview release                 GitHub     Select theme   DarkLightAuto                         On this page 
  Introduction   
  Overview   
  Building the tests   
  Running the tests   
  Running a specific set of tests   
  Running a single test by name   
  Regenerating model responses   
  Deflaking a test   
  Deflake script   
  Running all tests   
  Sandbox matrix      
  Memory regression tests   
  Running memory tests   
  Updating baselines   
  How it works      
  Performance regression tests   
  Running performance tests   
  Updating baselines   
  How it works      
  Diagnostics   
  Keeping test output   
  Verbose output      
  Linting and formatting   
  Running the linter      
  Directory structure   
  Continuous integration     
On this page 
  Introduction   
  Overview   
  Building the tests   
  Running the tests   
  Running a specific set of tests   
  Running a single test by name   
  Regenerating model responses   
  Deflaking a test   
  Deflake script   
  Running all tests   
  Sandbox matrix      
  Memory regression tests   
  Running memory tests   
  Updating baselines   
  How it works      
  Performance regression tests   
  Running performance tests   
  Updating baselines   
  How it works      
  Diagnostics   
  Keeping test output   
  Verbose output      
  Linting and formatting   
  Running the linter      
  Directory structure   
  Continuous integration                 
 Integration tests     Copy as Markdown Copied!             
This document provides information about the integration testing framework used
in this project.

OverviewSection titled “Overview”

The integration tests are designed to validate the end-to-end functionality of
Gemini CLI. They execute the built binary in a controlled environment and verify
that it behaves as expected when interacting with the file system.

These tests are located in the integration-tests directory and are run using a
custom test runner.

Building the testsSection titled “Building the tests”

Prior to running any integration tests, you need to create a release bundle that
you want to actually test:
Terminal window
npm run bundle

You must re-run this command after making any changes to the CLI source code,
but not after making changes to tests.

Running the testsSection titled “Running the tests”

The integration tests are not run as part of the default npm run test command.
They must be run explicitly using the npm run test:integration:all script.

The integration tests can also be run using the following shortcut:
Terminal window
npm run test:e2e

Running a specific set of testsSection titled “Running a specific set of tests”

To run a subset of test files, you can use
npm run <integration test command> <file_name1> .... where <integration
test command> is either test:e2e or test:integration* and <file_name>
is any of the .test.js files in the integration-tests/ directory. For
example, the following command runs list_directory.test.js and
write_file.test.js:
Terminal window
npm run test:e2e list_directory write_file

Running a single test by nameSection titled “Running a single test by name”

To run a single test by its name, use the --test-name-pattern flag:
Terminal window
npm run test:e2e -- --test-name-pattern "reads a file"

Regenerating model responsesSection titled “Regenerating model responses”

Some integration tests use faked out model responses, which may need to be
regenerated from time to time as the implementations change.

To regenerate these golden files, set the REGENERATE_MODEL_GOLDENS environment
variable to “true” when running the tests, for example:

WARNING: If running locally you should review these updated responses for
any information about yourself or your system that gemini may have included in
these responses.
Terminal window
REGENERATE_MODEL_GOLDENS="true" npm run test:e2e

WARNING: Make sure you run await rig.cleanup() at the end of your test,
else the golden files will not be updated.

Deflaking a testSection titled “Deflaking a test”

Before adding a new integration test, you should test it at least 5 times
with the deflake script or workflow to make sure that it is not flaky.

Deflake scriptSection titled “Deflake script”
Terminal window
npm run deflake -- --runs=5 --command="npm run test:e2e -- -- --test-name-pattern '<your-new-test-name>'"

Deflake workflowSection titled “Deflake workflow”
Terminal window
gh workflow run deflake.yml --ref <your-branch> -f test_name_pattern="<your-test-name-pattern>"

Running all testsSection titled “Running all tests”

To run the entire suite of integration tests, use the following command:
Terminal window
npm run test:integration:all

Sandbox matrixSection titled “Sandbox matrix”

The all command will run tests for no sandboxing, docker and podman.
Each individual type can be run using the following commands:
Terminal window
npm run test:integration:sandbox:none
Terminal window
npm run test:integration:sandbox:docker
Terminal window
npm run test:integration:sandbox:podman

Memory regression testsSection titled “Memory regression tests”

Memory regression tests are designed to detect heap growth and leaks across key
CLI scenarios. They are located in the memory-tests directory.

These tests are distinct from standard integration tests because they measure
memory usage and compare it against committed baselines.

Running memory testsSection titled “Running memory tests”

Memory tests are not run as part of the default npm run test or
npm run test:e2e commands. They are run nightly in CI but can be run manually:
Terminal window
npm run test:memory

Updating baselinesSection titled “Updating baselines”

If you intentionally change behavior that affects memory usage, you may need to
update the baselines. Set the UPDATE_MEMORY_BASELINES environment variable to
true:
Terminal window
UPDATE_MEMORY_BASELINES=true npm run test:memory

This will run the tests, take median snapshots, and overwrite
memory-tests/baselines.json. You should review the changes and commit the
updated baseline file.

How it worksSection titled “How it works”

The harness (MemoryTestHarness in packages/test-utils):

Forces garbage collection multiple times to reduce noise.

Takes median snapshots to filter spikes.

Compares against baselines with a 10% tolerance.

Can analyze sustained leaks across 3 snapshots using analyzeSnapshots().

Performance regression testsSection titled “Performance regression tests”

Performance regression tests are designed to detect wall-clock time, CPU usage,
and event loop delay regressions across key CLI scenarios. They are located in
the perf-tests directory.

These tests are distinct from standard integration tests because they measure
performance metrics and compare it against committed baselines.

Running performance testsSection titled “Running performance tests”

Performance tests are not run as part of the default npm run test or
npm run test:e2e commands. They are run nightly in CI but can be run manually:
Terminal window
npm run test:perf

Updating baselinesSection titled “Updating baselines”

If you intentionally change behavior that affects performance, you may need to
update the baselines. Set the UPDATE_PERF_BASELINES environment variable to
true:
Terminal window
UPDATE_PERF_BASELINES=true npm run test:perf

This will run the tests multiple times (with warmup), apply IQR outlier
filtering, and overwrite perf-tests/baselines.json. You should review the
changes and commit the updated baseline file.

How it worksSection titled “How it works”

The harness (PerfTestHarness in packages/test-utils):

Measures wall-clock time using performance.now().

Measures CPU usage using process.cpuUsage().

Monitors event loop delay using perf_hooks.monitorEventLoopDelay().

Applies IQR (Interquartile Range) filtering to remove outlier samples.

Compares against baselines with a 15% tolerance.

DiagnosticsSection titled “Diagnostics”

The integration test runner provides several options for diagnostics to help
track down test failures.

Keeping test outputSection titled “Keeping test output”

You can preserve the temporary files created during a test run for inspection.
This is useful for debugging issues with file system operations.

To keep the test output set the KEEP_OUTPUT environment variable to true.
Terminal window
KEEP_OUTPUT=true npm run test:integration:sandbox:none

When output is kept, the test runner will print the path to the unique directory
for the test run.

Verbose outputSection titled “Verbose output”

For more detailed debugging, set the VERBOSE environment variable to true.
Terminal window
VERBOSE=true npm run test:integration:sandbox:none

When using VERBOSE=true and KEEP_OUTPUT=true in the same command, the output
is streamed to the console and also saved to a log file within the test’s
temporary directory.

The verbose output is formatted to clearly identify the source of the logs:

--- TEST: <log dir>:<test-name> ---... output from the gemini command ...--- END TEST: <log dir>:<test-name> ---

Linting and formattingSection titled “Linting and formatting”

To ensure code quality and consistency, the integration test files are linted as
part of the main build process. You can also manually run the linter and
auto-fixer.

Running the linterSection titled “Running the linter”

To check for linting errors, run the following command:
Terminal window
npm run lint

You can include the :fix flag in the command to automatically fix any fixable
linting errors:
Terminal window
npm run lint:fix

Directory structureSection titled “Directory structure”

The integration tests create a unique directory for each test run inside the
.integration-tests directory. Within this directory, a subdirectory is created
for each test file, and within that, a subdirectory is created for each
individual test case.

This structure makes it easy to locate the artifacts for a specific test run,
file, or case.

.integration-tests/└── <run-id>/    └── <test-file-name>.test.js/        └── <test-case-name>/            ├── output.log            └── ...other test artifacts...

Continuous integrationSection titled “Continuous integration”

To ensure the integration tests are always run, a GitHub Actions workflow is
defined in .github/workflows/chained_e2e.yml. This workflow automatically runs
the integrations tests for pull requests against the main branch, or when a
pull request is added to a merge queue.

The workflow runs the tests in different sandboxing environments to ensure
Gemini CLI is tested across each:

sandbox:none: Runs the tests without any sandboxing.

sandbox:docker: Runs the tests in a Docker container.

sandbox:podman: Runs the tests in a Podman container.
   Last updated: Apr 10, 2026                
This website uses cookies from Google to deliver and enhance the quality of its services and to analyze
        traffic.
 
I understand.
                Terms | Privacy