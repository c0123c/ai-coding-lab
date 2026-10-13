# Headless mode

Run Antigravity CLI non-interactively to script agent tasks, integrate with CI pipelines, and capture machine-readable output.

Headless mode (also called print mode) sends a single prompt to the agent, streams or returns the response, and exits. Use it whenever you need the agent’s output in a program instead of a terminal UI.

## Run a single prompt

Pass a prompt with -p (or its aliases --print and --prompt ) to run once and exit:

```
agy\n -p\n "In one sentence, what is a git rebase?"
```

```
A git rebase rewrites the commit history by transplanting a sequence of commits onto a new base commit, imposing a strictly linear progression of changes that eliminates arbitrary merge artifacts.
```

The response goes to stdout . Diagnostics—errors, authentication prompts, progress, and permission notices—go to stderr . This split keeps the captured response clean:

```
# Capture only the model response; diagnostics still print to the terminal.\n
\nanswer\n=\n$(\nagy\n -p\n "Name three popular version control systems, comma-separated."\n)
```

Note: Headless mode uses your cached credentials. Authenticate once with
an interactive agy session first. In a non-interactive environment with no
terminal (for example, CI), a run that isn’t already authenticated exits with
an authentication required error instead of hanging.

Note: Headless mode uses your cached credentials. Authenticate once with
an interactive agy session first. In a non-interactive environment with no
terminal (for example, CI), a run that isn’t already authenticated exits with
an authentication required error instead of hanging.

## Output formats

The --output-format flag controls the shape of stdout . It accepts three values:

Format stdout shape Use it for text The response text (default) Human-readable output, quick scripts json One JSON object printed on completion Capturing a result plus metadata stream-json Newline-delimited JSON (NDJSON) events Monitoring progress, tools, and token usage

### Text

This is the default format. The response text goes straight to stdout with no wrapping:

```
agy\n -p\n "In one sentence, what does the command git bisect do?"
```

```
Git bisect executes a binary search algorithm across a project's commit history to rapidly isolate the precise commit responsible for introducing a defect.
```

### JSON

Set --output-format json to get a single JSON envelope after the run completes. The CLI emits it on one line; pipe through jq to pretty-print:

```
agy\n -p\n "In one sentence, what is a git rebase?"\n --output-format\n json\n |\n jq
```

```
{\n
\n  "conversation_id"\n: \n"055a398f-db14-4c5f-abbb-1bf03f8120a7"\n,\n
\n  "status"\n: \n"SUCCESS"\n,\n
\n  "response"\n: \n"A git rebase rewrites the commit history by transplanting a sequence of commits onto a new base commit, imposing a strictly linear progression of changes that eliminates arbitrary merge artifacts.\n\n\n"\n,\n
\n  "duration_seconds"\n: \n7.16\n,\n
\n  "num_turns"\n: \n1\n,\n
\n  "usage"\n: {\n
\n    "input_tokens"\n: \n10415\n,\n
\n    "output_tokens"\n: \n657\n,\n
\n    "thinking_tokens"\n: \n616\n,\n
\n    "cache_read_tokens"\n: \n8113\n,\n
\n    "total_tokens"\n: \n11072\n
\n  }\n
\n}
```

The envelope contains these fields:

Field Type Description conversation_id string ID of the conversation, for resuming later status string Terminal status (refer to Status values ) response string The agent’s free-text response error string Error message; present only on failure duration_seconds number Wall-clock duration of the run num_turns number Number of user turns in the conversation structured_output object Parsed schema output; present only with --json-schema json_schema object The schema that was enforced; present only with --json-schema usage object Token counts: input_tokens , output_tokens , thinking_tokens , cache_read_tokens , total_tokens

#### Structured output with a schema

Pass --json-schema to constrain the answer to a schema. The parsed object appears in structured_output , and response holds the same payload serialized as a string:

```
agy\n -p\n "Parse the semantic version string v2.14.3 into an object with integer fields major, minor, and patch."\n \\n
\n  --output-format\n json\n \\n
\n  --json-schema\n '{"type":"object","properties":{"major":{"type":"integer"},"minor":{"type":"integer"},"patch":{"type":"integer"}},"required":["major","minor","patch"]}'\n |\n jq
```

```
{\n
\n  "conversation_id"\n: \n"4e502687-290c-4030-b908-5ed6c68fa5dc"\n,\n
\n  "status"\n: \n"SUCCESS"\n,\n
\n  "response"\n: \n"{\n\"\nmajor\n\"\n:2,\n\"\nminor\n\"\n:14,\n\"\npatch\n\"\n:3}\n\n\n"\n,\n
\n  "duration_seconds"\n: \n4.45\n,\n
\n  "num_turns"\n: \n1\n,\n
\n  "structured_output"\n: { \n"major"\n: \n2\n, \n"minor"\n: \n14\n, \n"patch"\n: \n3\n },\n
\n  "json_schema"\n: {\n
\n    "type"\n: \n"object"\n,\n
\n    "properties"\n: {\n
\n      "major"\n: { \n"type"\n: \n"integer"\n },\n
\n      "minor"\n: { \n"type"\n: \n"integer"\n },\n
\n      "patch"\n: { \n"type"\n: \n"integer"\n }\n
\n    },\n
\n    "required"\n: [\n"major"\n, \n"minor"\n, \n"patch"\n]\n
\n  },\n
\n  "usage"\n: { \n"input_tokens"\n: \n10522\n, \n"output_tokens"\n: \n354\n, \n"thinking_tokens"\n: \n329\n, \n"cache_read_tokens"\n: \n8112\n, \n"total_tokens"\n: \n10876\n }\n
\n}
```

The flag accepts a schema string, a path to a .json schema file, or a primitive type name ( string , number , integer , boolean ). Read the parsed value from structured_output :

```
agy\n -p\n "Parse the semantic version string v2.14.3 into an object with integer fields major, minor, and patch."\n \\n
\n  --output-format\n json\n \\n
\n  --json-schema\n '{"type":"object","properties":{"major":{"type":"integer"},"minor":{"type":"integer"},"patch":{"type":"integer"}},"required":["major","minor","patch"]}'\n \\n
\n  |\n jq\n '.structured_output'
```

### Streaming JSON

Set --output-format stream-json to emit one JSON object per line (NDJSON) as the run progresses. Use this format to observe tool calls and token usage in real time.

```
agy\n -p\n "In one sentence, what is a git rebase?"\n --output-format\n stream-json
```

The stream begins with one init event, followed by any number of step_update events, and ends with one result event (the cwd and tools array are abbreviated in the following example):

```
{\n"event"\n:\n"init"\n,\n"conversation_id"\n:\n"c3b66b04-872b-4fbe-a3a4-058a026ef20a"\n,\n"init"\n:{\n"cwd"\n:\n"/home/user/project"\n,\n"tools"\n:[\n"ask_permission"\n,\n"run_command"\n,\n"write_to_file"\n,\n"..."\n],\n"permission_mode"\n:\n"request-review"\n}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"c3b66b04-872b-4fbe-a3a4-058a026ef20a"\n,\n"step_index"\n:\n0\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"user_input"\n}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"c3b66b04-872b-4fbe-a3a4-058a026ef20a"\n,\n"step_index"\n:\n3\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"agent_response"\n,\n"text_delta"\n:\n"Git rebase destructively rewrites a branch's commit history by systematically detaching its unique commits and sequentially reapplying them onto a new base commit.\n\n\n"\n,\n"duration_seconds"\n:\n6.28\n,\n"usage"\n:{\n"input_tokens"\n:\n10302\n,\n"output_tokens"\n:\n582\n,\n"thinking_tokens"\n:\n551\n,\n"cache_read_tokens"\n:\n8113\n,\n"total_tokens"\n:\n10884\n}}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"c3b66b04-872b-4fbe-a3a4-058a026ef20a"\n,\n"step_index"\n:\n4\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"checkpoint"\n,\n"duration_seconds"\n:\n0.53\n,\n"usage"\n:{\n"input_tokens"\n:\n116\n,\n"output_tokens"\n:\n7\n,\n"thinking_tokens"\n:\n0\n,\n"cache_read_tokens"\n:\n0\n,\n"total_tokens"\n:\n123\n}}}\n
\n{\n"event"\n:\n"result"\n,\n"result"\n:{\n"conversation_id"\n:\n"c3b66b04-872b-4fbe-a3a4-058a026ef20a"\n,\n"status"\n:\n"SUCCESS"\n,\n"response"\n:\n"Git rebase destructively rewrites a branch's commit history by systematically detaching its unique commits and sequentially reapplying them onto a new base commit.\n\n\n"\n,\n"duration_seconds"\n:\n6.88\n,\n"num_turns"\n:\n1\n,\n"usage"\n:{\n"input_tokens"\n:\n10418\n,\n"output_tokens"\n:\n589\n,\n"thinking_tokens"\n:\n551\n,\n"cache_read_tokens"\n:\n8113\n,\n"total_tokens"\n:\n11007\n}}}
```

When the response streams in chunks, the agent_response step emits one or more ACTIVE events carrying partial text_delta fragments before its final DONE ; short responses like this one arrive in a single DONE .

Every line is an event object whose event field names its type:

event Payload key Emitted init init Once, at stream start step_update step_update For each step transition or text delta result result Once, at the end (same shape as json )

The init payload records the run configuration. model and agent appear only when set with --model or --agent ; permission_mode is request-review by default (and always-proceed under --dangerously-skip-permissions ):

Field Type Description cwd string Working directory tools string[] Names of all available tools permission_mode string Effective permission mode model string Model in use, when overridden agent string Active agent, when overridden json_schema object Enforced schema, when set with --json-schema

Each step_update payload describes one step. Observed step_type values include user_input , agent_response , tool , and checkpoint ; state is ACTIVE while a step runs and DONE when it finishes:

Field Type Description conversation_id string ID of the conversation step_index number Zero-based index of the step state string ACTIVE or DONE step_type string Step category, for example agent_response or tool tool_name string Canonical tool name, on tool steps text_delta string Incremental response text duration_seconds number Step duration, when known usage object Per-step token usage, when known tool_info object Tool invocation details subagent_info object Subagent invocation details

#### Tool calls in the stream

On tool steps, tool_info carries the call and its result. This is a real tool step from a run that executed echo hello_headless_demo :

```
{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"edb1c8c1-50ba-4f3f-87eb-412d0e9d47c3"\n,\n"step_index"\n:\n4\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"tool"\n,\n"tool_name"\n:\n"run_command"\n,\n"duration_seconds"\n:\n0.07\n,\n"tool_info"\n:{\n"name"\n:\n"run_command"\n,\n"parameters"\n:{\n"CommandLine"\n:\n"echo hello_headless_demo"\n},\n"output"\n:\n"hello_headless_demo\n\r\n\n"\n}}}
```

tool_info holds name , parameters , output , and—when the tool fails—an error object with type and message . Steps that spawn subagents carry subagent_info instead, listing each subagent under subagents (with type_name , role , conversation_id , log_uri , and workspace_uris ).

#### Structured output in the stream

With --json-schema , the schema applies to the terminal result event, which carries the same structured_output and json_schema fields as the json envelope.

## Parse output with jq

stdout is machine-readable, so jq extracts what you need.

Get the response text from a JSON run:

```
agy\n -p\n "Name three popular version control systems, comma-separated."\n --output-format\n json\n |\n jq\n -r\n '.response'
```

```
Git, Subversion, Mercurial.
```

Concatenate streaming text as it arrives:

```
agy\n -p\n "Explain what a merge conflict is in two sentences."\n --output-format\n stream-json\n \\n
\n  |\n jq\n -j\n 'select(.event=="step_update") | .step_update.text_delta // empty'
```

Read token usage from the terminal result event:

```
agy\n -p\n "In one sentence, what is a git rebase?"\n --output-format\n stream-json\n \\n
\n  |\n jq\n 'select(.event=="result") | .result.usage'
```

Tip: Use jq -j (join output) when concatenating text_delta fragments
so jq doesn’t insert newlines between them.

Tip: Use jq -j (join output) when concatenating text_delta fragments
so jq doesn’t insert newlines between them.

## Continue a conversation

Headless runs are stateless by default. Resume prior context with --continue ( -c ) for the most recent conversation, or --conversation with an ID from a previous run’s conversation_id :

```
# Continue the most recent conversation.\n
\nagy\n -p\n "Now explain your previous answer in more detail"\n --continue\n
\n
\n# Resume a specific conversation by ID.\n
\nagy\n -p\n "Summarize what we discussed"\n --conversation\n 055a398f-db14-4c5f-abbb-1bf03f8120a7
```

Each of these starts a new process. To run multiple turns inside one process, refer to Stream prompts from stdin .

## Stream prompts from stdin

Use --input-format stream-json to maintain a single, continuous conversation process, feeding it prompts one by one on standard input (stdin). Each prompt executes a full turn and emits its own result event.

This approach is ideal for applications that dynamically determine the next prompt based on the previous answer. Because the process only starts once, subsequent turns skip startup overhead and reuse the warmed-up conversation. This makes it significantly faster than running repeated commands with --continue .

Note: --input-format stream-json requires --output-format stream-json .
In a streaming session, the CLI emits one result event per turn.

Note: --input-format stream-json requires --output-format stream-json .
In a streaming session, the CLI emits one result event per turn.

### Send a prompt

Write one JSON object per line to stdin . The event key specifies the message type (matching the output stream format). A prompt is represented as a user event with a message :

```
{ \n"event"\n: \n"user"\n, \n"message"\n: { \n"content"\n: \n"Reply with exactly the word: apple. Nothing else."\n } }
```

You can pipe multiple prompts into a single session:

```
printf\n '%s\n'\n \\n
\n  '{"event":"user","message":{"content":"Reply with exactly the word: apple. Nothing else."}}'\n \\n
\n  '{"event":"user","message":{"content":"What word did I ask you to reply with in my previous message? Answer with just that word."}}'\n \\n
\n  |\n agy\n --input-format\n stream-json\n --output-format\n stream-json
```

The content field accepts either a standard string or a list of text blocks. The following two formats are equivalent:

```
{ \n"event"\n: \n"user"\n, \n"message"\n: { \n"content"\n: \n"Reply with exactly: banana"\n } }\n
\n{ \n"event"\n: \n"user"\n, \n"message"\n: { \n"content"\n: [{ \n"type"\n: \n"text"\n, \n"text"\n: \n"Reply with exactly: banana"\n }] } }
```

text is the only supported block type. Submitting any other block type ends the session with an error message rather than silently dropping the block. This ensures the agent never answers a prompt you didn’t explicitly send.

### Read the results

The output stream works as follows:

- Opens with a single init event.

- Emits a series of step_update events for the active turn.

- Concludes the turn with a final result event.

This example shows the output from the two-prompt Bash command in the previous section (with the init payload abbreviated):

```
{\n"event"\n:\n"init"\n,\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"init"\n:{\n"cwd"\n:\n"/home/user/project"\n,\n"tools"\n:[\n"ask_permission"\n,\n"run_command"\n,\n"write_to_file"\n,\n"..."\n],\n"permission_mode"\n:\n"request-review"\n}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"step_index"\n:\n0\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"user_input"\n}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"step_index"\n:\n2\n,\n"state"\n:\n"ACTIVE"\n,\n"step_type"\n:\n"agent_response"\n,\n"text_delta"\n:\n"apple"\n}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"step_index"\n:\n2\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"agent_response"\n,\n"text_delta"\n:\n"\n\n\n"\n,\n"duration_seconds"\n:\n1.169607627\n,\n"usage"\n:{\n"input_tokens"\n:\n30384\n,\n"output_tokens"\n:\n4\n,\n"thinking_tokens"\n:\n0\n,\n"cache_read_tokens"\n:\n0\n,\n"total_tokens"\n:\n30388\n}}}\n
\n{\n"event"\n:\n"result"\n,\n"result"\n:{\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"status"\n:\n"SUCCESS"\n,\n"response"\n:\n"apple\n\n\n"\n,\n"duration_seconds"\n:\n1.427806958\n,\n"num_turns"\n:\n1\n,\n"usage"\n:{\n"input_tokens"\n:\n30384\n,\n"output_tokens"\n:\n4\n,\n"thinking_tokens"\n:\n0\n,\n"cache_read_tokens"\n:\n0\n,\n"total_tokens"\n:\n30388\n}}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"step_index"\n:\n3\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"user_input"\n}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"step_index"\n:\n4\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"agent_response"\n,\n"text_delta"\n:\n"apple\n\n\n"\n,\n"duration_seconds"\n:\n0.895679386\n,\n"usage"\n:{\n"input_tokens"\n:\n278\n,\n"output_tokens"\n:\n4\n,\n"thinking_tokens"\n:\n0\n,\n"cache_read_tokens"\n:\n30214\n,\n"total_tokens"\n:\n282\n}}}\n
\n{\n"event"\n:\n"result"\n,\n"result"\n:{\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"status"\n:\n"SUCCESS"\n,\n"response"\n:\n"apple\n\n\n"\n,\n"duration_seconds"\n:\n2.548755756\n,\n"num_turns"\n:\n2\n,\n"usage"\n:{\n"input_tokens"\n:\n30662\n,\n"output_tokens"\n:\n8\n,\n"thinking_tokens"\n:\n0\n,\n"cache_read_tokens"\n:\n30214\n,\n"total_tokens"\n:\n30670\n}}}
```

The second turn answers apple from the first turn’s context. Notice that a single conversation_id tracks the entire session, and init is only sent once.

When parsing the result object, keep in mind that the response text applies only to the current turn, while metadata counters track the cumulative session:

Field Scope response The turn that emitted it num_turns Cumulative over the session usage Cumulative over the session duration_seconds Cumulative over the session

To filter and only view the final responses, you can pipe the output through jq :

```
printf\n '%s\n'\n \\n
\n  '{"event":"user","message":{"content":"Reply with exactly: one"}}'\n \\n
\n  '{"event":"user","message":{"content":"Reply with exactly: two"}}'\n \\n
\n  |\n agy\n --input-format\n stream-json\n --output-format\n stream-json\n \\n
\n  |\n jq\n -r\n 'select(.event=="result") | "\(.result.num_turns): \(.result.response)"'
```

```
1: one\n
\n
\n2: two
```

### Drive a session programmatically

Instead of passing all prompts up front, you can hold the stdin pipe open in a script. This allows your application to evaluate the model’s answer before submitting the next prompt.

Tip: You can read stdout line by line and dispatch logic based on the event field. Wait until you receive the result event for the current
prompt before writing the next one.

Tip: You can read stdout line by line and dispatch logic based on the event field. Wait until you receive the result event for the current
prompt before writing the next one.

For example:

```
import\n json\n
\nimport\n subprocess\n
\n
\nproc \n=\n subprocess.Popen(\n
\n    [\n"agy"\n, \n"--input-format"\n, \n"stream-json"\n, \n"--output-format"\n, \n"stream-json"\n],\n
\n    stdin\n=\nsubprocess.\nPIPE\n,\n
\n    stdout\n=\nsubprocess.\nPIPE\n,\n
\n    text\n=\nTrue\n,\n
\n    bufsize\n=\n1\n,\n
\n)\n
\n
\n
\ndef\n ask\n(prompt):\n
\n    """Send one prompt and return the response for that turn."""\n
\n    message \n=\n {\n"event"\n: \n"user"\n, \n"message"\n: {\n"content"\n: prompt}}\n
\n    proc.stdin.write(json.dumps(message) \n+\n "\n\n\n"\n)\n
\n    proc.stdin.flush()\n
\n    for\n line \nin\n proc.stdout:\n
\n        event \n=\n json.loads(line)\n
\n        if\n event[\n"event"\n] \n==\n "result"\n:\n
\n            return\n event[\n"result"\n][\n"response"\n]\n
\n
\n
\nfirst \n=\n ask(\n"Name one popular version control system. Answer with one word."\n)\n
\nprint\n(ask(\nf\n"Name a competitor to \n{\nfirst.strip()\n}\n. Answer with one word."\n))\n
\n
\nproc.stdin.close()\n
\nproc.wait()
```

### End a session

To close a session gracefully, close stdin . The process exits after the input pipe is closed and the current turn completes. If an application writes a final prompt and immediately closes the pipe, it still receives the final result before the process terminates.

Clean sessions exit with 0 , which matches the standard headless mode behavior.

### Unsupported messages

To prevent unpredictable behavior, the CLI validates inputs. If it encounters a malformed or unsupported message, it responds as described in the following table:

Input Result Exit Unrecognized event name Skipped: warning logged to stderr — control_request or control_response events ERROR result, session ends 2 Slash command handled by the CLI such as /model ERROR result, session ends 2 Message missing the event field ERROR result, session ends 1 Invalid JSON line ERROR result, session ends 1 Content block type other than text ERROR result, session ends 1

Unrecognized event names are safely skipped with a warning. This ensures that applications built against newer versions of the streaming protocol don’t crash when running on older CLI versions:

```
warning: ignoring unsupported stream input message event "future_thing"
```

For all other errors, the session terminates immediately. Any turns that previously completed retain their result events, but the malformed input line aborts the remainder of the session.

Slash commands that the CLI can respond to directly (such as /model and /usage ) produce a text report rather than a standard event stream. A streaming session can’t use these types of slash commands. For example:

```
{ \n"event"\n: \n"result"\n, \n"result"\n: { \n"conversation_id"\n: \n"4fae3a70-409d-42a4-86ea-9de206a49ff4"\n, \n"status"\n: \n"ERROR"\n, \n"response"\n: \n""\n, \n"error"\n: \n"/model is answered by the CLI itself and is unavailable with --input-format stream-json; run it as its own --print /model invocation"\n, \n"duration_seconds"\n: \n0\n, \n"num_turns"\n: \n0\n, \n"usage"\n: { \n"input_tokens"\n: \n0\n, \n"output_tokens"\n: \n0\n, \n"thinking_tokens"\n: \n0\n, \n"cache_read_tokens"\n: \n0\n, \n"total_tokens"\n: \n0\n } } }
```

### Common mistakes

Mistake Why it fails How to fix it Pairing with --output-format json or text Those formats only emit a single output envelope when the process exits, causing every turn except the last one to be lost. Always use --output-format stream-json with this input mode. Passing a prompt with the -p flag Streaming mode exclusively listens for prompts on stdin . Any prompt passed through a command-line flag is dropped. Send the prompt into stdin as a user message instead. Sending /model or /usage into the stream The CLI handles these commands internally outside of the event stream, breaking the JSON flow. Run agy -p /model as an entirely separate, standalone command. Treating num_turns as a per-turn count Metadata counters (like turns, duration, and usage) track the entire cumulative session, not just the active turn. Use the response field to get the text for the current turn. Waiting for the process to exit before reading stdout The session stays open indefinitely until stdin is closed. If your script waits for an exit signal, it hangs. Read the events line-by-line as they arrive, and manually close stdin when finished.

## Select a model, effort, or agent

List the available model slugs, then pin one for the run:

```
agy\n models
```

```
gemini-3.8-flash-high     Gemini 3.8 Flash (High)
gemini-3.8-flash-medium   Gemini 3.8 Flash (Medium)
gemini-3.7-flash-high     Gemini 3.7 Flash (High)
gemini-3.7-flash-medium   Gemini 3.7 Flash (Medium)
gemini-3.6-flash-high     Gemini 3.6 Flash (High)
gemini-3.6-flash-medium   Gemini 3.6 Flash (Medium)
gemini-3.1-pro-high       Gemini 3.1 Pro (High)
...
```

```
# Pin a model by slug.\n
\nagy\n -p\n "Reverse the string antigravity."\n --model\n gemini-3.8-flash-medium\n
\n
\n# Set reasoning effort (low, medium, or high).\n
\nagy\n -p\n "Outline a plan to add caching to this service."\n --effort\n high\n
\n
\n# Select an agent (list them with `agy agents`).\n
\nagy\n -p\n "Review this function for edge cases."\n --agent\n <\nagent-nam\ne\n>
```

Unlike the interactive UI, headless mode doesn’t silently fall back when --model names an unknown model. It exits non-zero with an ERROR status so a pinned pipeline fails loudly instead of running the wrong model.

## Permissions in headless mode

There is no interactive prompt in headless mode, so tools that would normally ask for confirmation are handled by policy.

By default, the CLI respects the permission mode in your settings. A tool that requires approval it can’t obtain is soft-denied: the run continues, exits 0 , and prints a notice to stderr naming the tool and how to allow it. Reading and writing files inside your active workspace is auto-allowed; actions such as shell commands default to Ask and are soft-denied in headless mode unless you grant them.

Grant a tool ahead of time by adding an action(target) rule under permissions.allow in ~/.gemini/antigravity-cli/settings.json :

```
{\n
\n  "permissions"\n: {\n
\n    "allow"\n: [\n
\n      "command(git)"\n,\n
\n      "command(regex:npm run (build|lint|test))"\n,\n
\n      "write_file(src/)"\n
\n    ]\n
\n  }\n
\n}
```

To auto-approve every tool for a run, pass --dangerously-skip-permissions :

```
agy\n -p\n "Run the test suite and report failures"\n --dangerously-skip-permissions
```

Warning: --dangerously-skip-permissions approves all tool calls,
including file writes and command execution. Prefer scoped permissions.allow rules unless you fully trust the prompt and environment.
Refer to Permissions for the full rule syntax.

Warning: --dangerously-skip-permissions approves all tool calls,
including file writes and command execution. Prefer scoped permissions.allow rules unless you fully trust the prompt and environment.
Refer to Permissions for the full rule syntax.

## Handle exit codes and errors

A successful run exits 0 . A run that fails to produce a response exits non-zero and writes the reason to stderr . In json and stream-json modes, the failure also appears in the status and error fields.

For example, pinning an unknown model exits 1 and returns an error envelope:

```
agy\n -p\n "hi"\n --model\n does-not-exist-model\n --output-format\n json\n; \necho\n "exit=\n$?\n"
```

```
{\n"conversation_id"\n:\n""\n,\n"status"\n:\n"ERROR"\n,\n"response"\n:\n""\n,\n"error"\n:\n"invalid model selection (--model \n\"\ndoes-not-exist-model\n\"\n --effort \n\"\"\n): model does-not-exist-model is not recognized as a known model or custom model in settings\n\n\nAvailable models:\n\n\n  Gemini 3.6 Flash (High)\n\n\n  ..."\n,\n"duration_seconds"\n:\n0\n,\n"num_turns"\n:\n0\n,\n"usage"\n:{\n"input_tokens"\n:\n0\n,\n"output_tokens"\n:\n0\n,\n"thinking_tokens"\n:\n0\n,\n"cache_read_tokens"\n:\n0\n,\n"total_tokens"\n:\n0\n}}
```

```
exit=1
```

The status field reports the terminal state of the run:

Status Meaning SUCCESS The run completed and produced a response ERROR The run ended with an error CANCELED The run was canceled INTERRUPTED The run was interrupted (for example, SIGINT ) INVALID The run reached an invalid state WAITING The run ended while waiting on input RUNNING The run did not reach a terminal state

By default, a run waits up to five minutes for a response. Adjust the ceiling with --print-timeout :

```
agy\n -p\n "Summarize the design tradeoffs of optimistic locking."\n --print-timeout\n 15m
```

## Flag reference

Flag Default Description -p , --print , --prompt — Run a single prompt non-interactively and print the response --output-format text Output format: text , json , or stream-json --input-format text Input format: text or stream-json ; reads prompts on stdin --json-schema — Schema string or file path to enforce structured output --model — Model slug for this run (refer to agy models ) --effort — Reasoning effort: low , medium , or high --agent — Agent for this run (refer to agy agents ) --continue , -c false Continue the most recent conversation --conversation — Resume a conversation by ID --dangerously-skip-permissions false Auto-approve all tool permission requests --print-timeout 5m Maximum time to wait for a response --sandbox false Run with terminal sandbox restrictions enabled

## Example: run the agent in CI

Fail the job on error and save the response:

```
#!/usr/bin/env bash\n
\nset\n -euo\n pipefail\n
\n
\nresult\n=\n$(\nagy\n -p\n "Name three popular version control systems, comma-separated."\n \\n
\n  --output-format\n json\n \\n
\n  --print-timeout\n 10m\n)\n
\n
\nstatus\n=\n$(\necho\n "\n$result\n"\n |\n jq\n -r\n '.status'\n)\n
\nif\n [[ \n"\n$status\n"\n !=\n "SUCCESS"\n ]]; \nthen\n
\n  echo\n "Agent run failed: $(\necho\n "\n$result\n" \n|\n jq\n -r\n '.error')"\n >&2\n
\n  exit\n 1\n
\nfi\n
\n
\necho\n "\n$result\n"\n |\n jq\n -r\n '.response'\n >\n result.txt
```

## Next steps

Explore the following guides to learn more about CLI capabilities:

- Prompting and interaction : Write effective prompts for the agent.

- Permissions : Configure allow, deny, and ask rules.

- Background tasks and subagents : Delegate work to specialized agents.

- Reference : Full command and flag reference.
