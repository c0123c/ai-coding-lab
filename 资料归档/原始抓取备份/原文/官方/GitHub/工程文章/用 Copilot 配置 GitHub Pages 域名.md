# How GitHub Copilot enables zero DNS configuration for GitHub Pages

Custom domains make a project feel real. But for many developers, DNS, the last mile, is also the most frustrating: A records, CNAME entries, TTLs, and that long wait where you’re never quite sure if the internet is broken or you are.

In this post, I’ll walk through how I took a project from an empty repository to a live website on a custom domain, secured with HTTPS, in about 14 minutes without manually editing a single DNS record. The trick is to let [GitHub Copilot CLI](https://github.com/features/copilot/cli) drive the work, with a community [Namecheap skill](https://github.com/brunoborges/namecheap-skill) handling the DNS automation through the registrar’s API.

**Here’s what you’ll learn how to do:**

* Publish a site with [GitHub Pages](https://docs.github.com/pages)
* Register an inexpensive domain
* Enable your registrar’s API and connect it to Copilot CLI
* Point the domain at GitHub Pages and verify it end to end

**What you’ll need**

* A GitHub account (the free tier works)
* [GitHub Copilot CLI](https://github.com/features/copilot/cli), installed and authenticated with GitHub Copilot
* A Namecheap account, for buying the domain and using its API

No prior DNS expertise required. That’s the whole point. Let’s get started. ⤵

## Step 1: Publish a site with GitHub Pages

Every deployment needs something to deploy, so start with a home for the site: a new public repository.

![Screenshot of Copilot CLI screen that says 'create a public repository for this folder with the same name'](https://github.blog/wp-content/uploads/2026/07/Screenshot-2026-07-07-at-3.42.14-PM.png?resize=721%2C266)
![Screenshot showing the public repository has been created.](https://github.blog/wp-content/uploads/2026/07/Screenshot-2026-07-07-at-3.42.20-PM.png?resize=935%2C68)

With the repository in place, you don’t have to hand-write an `index.html`, commit it, and then click through the pages settings yourself. Instead, describe the outcome you want to Copilot CLI and let it create the landing page and enable GitHub Pages for you.

![Screenshot showing a prompt to enable GitHub Pages for this repo and create a website landing page about 'GitHub Pages and Custom Domains.'](https://github.blog/wp-content/uploads/2026/07/Screenshot-2026-07-07-at-3.42.33-PM.png?resize=932%2C563)

The site is now live on a github.io URL. That’s a solid start. Now let’s give it a proper address.

## Step 2: Register an inexpensive domain

You don’t need a premium .com to ship a side project. For this walkthrough I chose one of the cheapest top-level domains available, .click, and searched for an available name.

[ghpagesblog.click](https://ghpagesblog.click) was available, so I moved to checkout.

The total came to **USD $2.00**, or about **CAD $2.46**. That’s a low-risk price for trying a custom domain on a side project.

## Step 3: Connect the domain to GitHub Pages

This is the step developers tend to dread. Here, an AI assistant does the repetitive work while you stay in control of the decisions.

### Enable Namecheap API access

Before Copilot CLI can update your DNS, you need to turn on Namecheap’s API. In your Namecheap account, go to **Profile → Tools**, scroll to **Business & Dev Tools**, and select **Manage** under *Namecheap API Access*.

![Screenshot showing Profile > Tools highlighted.](https://github.blog/wp-content/uploads/2026/07/Screenshot-2026-07-07-at-3.43.09-PM.png?resize=899%2C277)

You can also navigate directly to the [API access settings page](https://ap.www.namecheap.com/settings/tools/apiaccess/) (note that this URL may change over time).

On that page, complete three steps:

1. Toggle the API to **ON**.
2. Add the public IP of the machine that will call the API to the IP allowlist (Namecheap labels this field **Whitelisted IPs**).
3. Copy the **API Key** and store it somewhere safe. You’ll need it shortly.

For more detail on what the API offers, see [Namecheap’s API introduction](https://www.namecheap.com/support/api/intro/).

### Install the Namecheap skill

Next, give Copilot CLI the ability to talk to Namecheap by installing the [Namecheap skill](https://awesome-copilot.github.com/skills/#file=skills%2Fnamecheap%2FSKILL.md). It’s a single command:

```
gh skill install github/awesome-copilot namecheap --scope user
```

The first time you ask Copilot to do something like *“list my Namecheap domains,* it confirms the skill is configured and prompts you for your username.

![Screenshot showing Copilot CLI prompt 'list my namecheap domains.'](https://github.blog/wp-content/uploads/2026/07/Screenshot-2026-07-07-at-3.43.35-PM.png?resize=928%2C519)

Then it asks for the API key you copied earlier.

![Screenshot of Copilot asking the user 'What is your namecheap API key? It will be saved locally...'](https://github.blog/wp-content/uploads/2026/07/Screenshot-2026-07-07-at-3.43.46-PM.png?resize=934%2C194)

With credentials in place, Copilot returns the list of domains in your account. It’s a quick way to confirm everything is wired up correctly before making any changes.

![Screenshot of domains: brunoborges.io and toml-schema.org.](https://github.blog/wp-content/uploads/2026/07/Screenshot-2026-07-07-at-3.43.52-PM.png?resize=506%2C210)

### Point the domain at GitHub Pages

Now connect the domain to the site. Ask Copilot to configure the custom domain using the skill.

![Screenshot of a prompt asking Copilot to 'Enable this GitHub Pages site with the custom domain ghpagesblog.click registered with namecheap.'](https://github.blog/wp-content/uploads/2026/07/Screenshot-2026-07-07-at-3.44.01-PM.png?resize=934%2C98)

A good automation asks before it acts. The skill pauses to confirm the change before touching any records.

![Screenshot of Copilot asking: 'The domain is using Namecheap DNS, but it currently points to Namecheap parking/redirect records. To make the apex domain work on GitHub Pages, those need to be replaced with GitHub Pages DNS records. Asking user: Replace the current Namecheap parking DNS records for ghp...'](https://github.blog/wp-content/uploads/2026/07/Screenshot-2026-07-07-at-3.44.16-PM.png?resize=931%2C228)

Once you approve, it replaces the existing parking records with the GitHub Pages A records and a CNAME for the WWW subdomain, which is the exact configuration GitHub Pages expects. This matches GitHub’s documented steps for [configuring a custom domain for your GitHub Pages site](https://docs.github.com/pages/configuring-a-custom-domain-for-your-github-pages-site).

![Screenshot showing 'Replace Namecheap DNS records.'](https://github.blog/wp-content/uploads/2026/07/Screenshot-2026-07-07-at-3.44.25-PM.png?resize=933%2C351)

It also handles the repository side, committing a CNAME file that tells GitHub Pages which custom domain the site should answer to.

## Step 4: Verify the deployment

Rather than assuming success, Copilot CLI checks its own work. First, it confirms the domain resolves.

![Screenshot showing 'Verify custom domain publication (shell).'](https://github.blog/wp-content/uploads/2026/07/Screenshot-2026-07-07-at-3.44.43-PM.png?resize=928%2C144)

Then it confirms that the site returns a healthy HTTP 200 response.

If you’d like to review every prompt and response, the [full Copilot CLI session is available as a gist](https://gist.github.com/brunoborges/167c988a0c4c16b8ccffca995ae98ce2).

Now for the timeline. The domain was purchased at **11:21:27 a.m. ET**.

The site was live on the custom domain, served over HTTPS, at around **11:35 a.m. ET**. That’s roughly **14 minutes** from owning nothing to a fully deployed site, including API setup, skill installation, DNS configuration, propagation, and verification.

## Wrapping up

DNS isn’t hard, exactly, but it’s fiddly, easy to get wrong, and slow to give feedback. By pairing GitHub Pages with GitHub Copilot CLI and the Namecheap skill, the repetitive parts of a custom-domain deployment fade into a short conversation: you make the decisions and approve the changes, and the tooling handles the plumbing.

If you’ve been putting off a custom domain because the DNS step feels like a chore, this workflow removes the friction. To go further, explore the [GitHub Pages documentation](https://docs.github.com/pages) and the guide to [configuring a custom domain for your GitHub Pages site](https://docs.github.com/pages/configuring-a-custom-domain-for-your-github-pages-site), then try it on your next project.

---

## Tags:

* [GitHub Copilot](https://github.blog/tag/github-copilot/)
* [GitHub Copilot CLI](https://github.blog/tag/github-copilot-cli/)
* [GitHub Pages](https://github.blog/tag/github-pages/)

## Written by

![Bruno Borges](https://avatars.githubusercontent.com/u/129743?v=4&s=200)

### [Bruno Borges](https://github.blog/author/brunoborges/)

[@brunoborges](https://github.com/brunoborges)

Principal Product Manager
