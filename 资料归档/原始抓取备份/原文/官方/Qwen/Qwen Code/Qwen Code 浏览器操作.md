---
vendor: "Qwen"
repository: "QwenLM/qwen-code"
path: "docs/users/features/browser-use.md"
source_url: "https://github.com/QwenLM/qwen-code/blob/main/docs/users/features/browser-use.md"
raw_url: "https://raw.githubusercontent.com/QwenLM/qwen-code/main/docs/users/features/browser-use.md"
resolved_url: "https://raw.githubusercontent.com/QwenLM/qwen-code/main/docs/users/features/browser-use.md"
tree_snapshot_sha: "7e3de59d3aba5cab80767ac5b121c0b3b4e35267"
sha256_body: "47a2c751d514b450aaf62769ef737f999ec1c806824125b821d9ce59ad47d36b"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0001.jsonl.gz:23"
---

# Browser Use

Browser Use lets Qwen Code work with pages in your Chrome browser, using your
existing tabs and signed-in sessions.

## Use

Use macOS or Linux with Chrome 125 or later and Qwen Code 0.24.2 or later
(check with `qwen --version`). **Install the
[Qwen Code extension from the Chrome Web Store](https://chromewebstore.google.com/detail/qwen-code/hdhmmjclhibojdddmancfgbkleahfaph)
in the Chrome profile you want to use.** The extension is required and is not
installed by the Qwen Code package. Chrome keeps it up to date once installed.

**Use one copy of the extension per profile.** If you previously loaded it
unpacked, remove or disable that copy in `chrome://extensions` before using the
store version. With both enabled, Qwen sees two browsers for the same profile
and may connect to either.

If the Chrome Web Store says the extension is not available in your region,
build it from source instead: follow the
[README](https://github.com/QwenLM/qwen-code/tree/main/packages/chrome-extension#readme)
of the `packages/chrome-extension` directory in the Qwen Code repository, then
open `chrome://extensions`, enable Developer mode, choose **Load unpacked**, and
pick the built `dist/extension` directory.

Describe your browser task directly, for example:

> Read my open dashboard and summarize today's orders.

Qwen selects the Browser Use skill when appropriate. The first browser task
automatically registers a small local connection program in your user directory;
later tasks reuse it. Qwen confirms the connection with the extension before
operating pages. If it cannot connect, open Chrome and check that Qwen Code is
0.24.2 or later and that the extension is enabled in the intended profile, then
retry. If a runtime dependency needs configuration on first use, Qwen will guide
you and may ask you to restart. No separate Browser Use Qwen extension or
`qwen serve` process is needed.

## Disable

Use `/skills` to disable **browser-use**. This hides the skill from the model
but does not disconnect an existing browser session or remove instructions
already loaded in a conversation.
