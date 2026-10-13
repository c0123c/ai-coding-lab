# How to bring your software delivery workflow into GitHub with agent apps

How many tabs do you have open alongside your pull request?

Imagine picking up a new issue in your product’s free-trial onboarding flow: make the “invite your teammates” step optional. Support keeps flagging the step as a friction point as signups increase. Quick win, right?

From scoping to deployment, you need answers to these four questions:

* Is this even the right change?
* Are the dependencies I’m touching clean?
* How do I roll it out safely?
* Is it safe to deploy right now?

Each answer lives in a different tool, so working through the pull request means carrying the same context across four places.

[GitHub agent apps](https://docs.github.com/copilot/concepts/agents/agent-apps) bring the tools you need to answer those questions to where you’re already working, powered by the same platform and harness as our own [Copilot cloud agent](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent). The illustrative walkthrough below shows how you can use services you already depend on, such as Amplitude, Endor Labs, LaunchDarkly, and PagerDuty to answer these questions and complete this request, without ever leaving GitHub.

## 1. Before you build it

Support says the “invite your teammates” step is annoying for customers who are onboarding with your product, but they haven’t given an indication of who has complained or whether those complaints lead to churn. You’d be right to be skeptical. So instead of opening Amplitude and building a query to confirm your hunch, you ask the [Amplitude](https://github.com/apps/amplitude) agent right from the **Agents** tab:

[![](https://github.blog/wp-content/uploads/2026/08/Screenshot-2026-08-12-at-4.03.38-PM.png)](https://github.blog/wp-content/uploads/2026/08/Amp.mp4)

```
@amplitude[agent] is completing the team invite step correlated with success later in the funnel? Break it down by segments we're measuring.
```

The split comes back clear: team users who finish the step are more likely to retain later, while solo users don’t have that correlation. A rescope is now justified: defer the step for solo signups and keep it for teams.

Access to product insights is now within GitHub, enabling course correction before any code is written.

## 2. As you build it

Copilot opens a draft pull request for the change. The implementation also updates dependencies used by the onboarding flow. Instead of waiting for a CI scan to fail later, you ask the [Endor Labs](https://github.com/marketplace/endor-labs-agenthq-plugin) agent in a comment:

[![](https://github.blog/wp-content/uploads/2026/08/Screenshot-2026-08-12-at-4.03.21-PM.png)](https://github.blog/wp-content/uploads/2026/08/endor.mp4)

```
@endor-labs-github-agenthq[agent] is there anything I need to watch out for in the dependencies being touched by this pull request?
```

The agent identifies the changed dependencies, checks them for known vulnerabilities and broader package risk, then reports back in the pull request. This time, everything looks clean. Nothing to remediate.

Dependency review becomes a proactive check while the change is still in front of you. Much better than remediating a CI scan after it fails.

## 3. Rolling it out

The previous finding now gets carried through to implementation: solo signups get the optional path, while teams keep the existing one. Because these segments are set at signup, a feature flag can target them directly. Ask the [LaunchDarkly](https://github.com/marketplace/launchdarkly-agent) agent to set it up for you, the same way you’d ask a team member:

[![](https://github.blog/wp-content/uploads/2026/08/Screenshot-2026-08-12-at-4.03.15-PM.png)](https://github.blog/wp-content/uploads/2026/08/darkly.mp4)

```
@launchdarkly-agent[agent] please create a feature flag for this pull request and wire it into the code. 
   - key: defer-team-invite 
   - type: boolean 
   - default: false 
   - target: solo-intent signups 
   - rollout: internal > 5% > 25% > 100%
```

The agent creates the flag in LaunchDarkly and adds the code implementation as a commit you review. If the target environment requires approval, it creates an approval request instead of applying the targeting change directly. A human still decides whether the rollout moves forward.

Flag setup goes from a second tool, a manual code handoff, and Slack coordination to one pull request comment and a commit you review.

## 4. Before you ship

Review tells you the code is correct, but whether the service is in a good state for a deployment is a different question. Before merging, you ask the [PagerDuty](https://github.com/apps/pagerduty-agent-app) agent:

[![](https://github.blog/wp-content/uploads/2026/08/Screenshot-2026-08-12-at-4.03.02-PM.png)](https://github.blog/wp-content/uploads/2026/08/pagerduty.mp4)

```
@pagerduty-agent-app[agent] assess the deployment risk for this pull request against the onboarding service. Check active incidents and recent incident history, then recommend whether to proceed.
```

The agent maps the repository to its PagerDuty service, checks for active incidents, reviews the previous 90 days, and compares the files in the pull request with areas involved in past incidents.

This time, the risk is low. There are no active incidents and no meaningful correlation with the current changes. The recommendation is to proceed.

Nothing dramatic happens, but that’s the point. Checking deploy risk becomes a routine step for your pull requests instead of something you do only when a release already feels dangerous.

## What changes

You still use Amplitude, LaunchDarkly, Endor Labs, and PagerDuty. But now, you no longer need to carry the context between them, and they’ll all work directly in your GitHub workflows.

As work moves from idea to production, developers can bring each service into GitHub when its context or capabilities matter. With agent apps, GitHub becomes the place where developers and agents coordinate what happens next, without developers switching contexts.

## Try it

[Agent apps](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/use-agent-apps) are available from the [GitHub Marketplace](https://github.com/marketplace?type=apps&category=agent-apps). Install one, enable it for your organization, and take it for a spin:

* Assign it to an issue to kick off a task.
* @mention it in a pull request comment for analysis or action.
* Select it from the **Agents** tab in your repository.

Your tools are still your tools. Now, they show up where you are already working: on GitHub. Explore the other inaugural agent apps and start bringing your stack directly into your workflow:

* [Packfiles](https://github.com/marketplace/packfiles-agent)’s agent reads your backlog and builds a migration strategy. reads your backlog and builds a migration strategy.
* [Miro](https://github.com/marketplace/miro-agent-app)‘s agent connects visual collaboration with code workflows.
* [Bright Security](https://github.com/marketplace/bright-security-agent)‘s agent autonomously handles end-to-end dynamic security testing inside GitHub.
* [SonarQube](https://github.com/marketplace/sonarqube-agent)‘s agent brings analysis, quality gates, and remediation into GitHub agent sessions.
* [Octopus Deploy](https://github.com/marketplace/octopus-deploy-intelligence-agent)‘s agent can identify, diagnose, and resolve deployment failures.

[Discover agent apps in the GitHub Marketplace >](https://github.com/marketplace?type=apps&category=agent-apps)

---

## Tags:

* [Agent Apps](https://github.blog/tag/agent-apps/)
* [GitHub Copilot](https://github.blog/tag/github-copilot/)
* [GitHub Marketplace](https://github.blog/tag/github-marketplace/)

## Written by

![Sam Zhang](https://avatars.githubusercontent.com/u/38818012?v=4&s=200)

### [Sam Zhang](https://github.blog/author/imsamz/)

[@im-samz](https://github.com/im-samz)

Product Manager, GitHub Copilot
