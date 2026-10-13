# How Copilot helps build the GitHub platform

As engineers at GitHub, “dogfooding” is core to our culture. We build GitHub on GitHub, using the same tools we ship to developers. But as [GitHub Copilot](https://github.com/features/copilot?utm_source=blog-copilot-feature&utm_medium=blog&utm_campaign=universe25post) has evolved from an autocomplete suggestion to a sophisticated AI assistant, our own use of it has evolved, too.

It’s no longer just a tool in our editors. We’ve integrated Copilot directly into our development lifecycle.

Inside our core repository, the one we use to build github.com, `@Copilot` isn’t just suggesting code; it’s an active contributor. It gets assigned issues by human engineers, opens pull requests, and does the work assigned.

Copilot is a prolific engineer within GitHub, and it’s taking on some of our most time-consuming and tedious tasks. For this article, we analyzed a month of pull requests created within our core repo by Copilot. Here’s what we found.

### Copilot does simple work that saves time

Before we get to the complex, architectural work, it’s worth noting how much of Copilot’s day-to-day work is about accelerating the small tasks that add up. These are the quick fixes that save engineers from constant context-switching.

* **UI and copy tweaks:** It gets tasked with fixing minor UI bugs, like realigning a misaligned icon, or updating placeholder text in a filter bar to be more accurate.
* **Documentation and cleanup:** It also handles straightforward cleanup. In one pull request, `@Copilot` was assigned to fix **161 typos** in comments and documentation strings across 100 different files. It’s a simple task, but one that no human engineer wants to spend an afternoon doing.

### Critical cleanup and maintenance

A healthy codebase requires constant care. This is where `@Copilot` shines. A significant portion of the pull requests were focused on code maintenance, modernization, and large-scale refactors that would have been time-consuming for human engineers, including:

* **Removing feature flags:** This is a huge one. `@Copilot` is constantly assigned to clean up deprecated feature flags, removing the conditional logic, stale code, and old tests across the entire codebase.
* **Large-scale refactoring:** When we need to rename a class used across the application, `@Copilot` can take it on. It’s even authored massive, repository-wide pull requests to rename core internal classes, a task that would be tedious and time-consuming for any developer.
* **Performance optimization:** It finds and fixes common performance anti-patterns, replacing inefficient code with more optimized alternatives in high-traffic areas.

### Fixing bugs and flaky tests

`@Copilot` also helped us solve a number of bugs. It’s patched production issues and improved the stability of our CI/CD pipeline.

* **Production errors:** It’s not just fixing simple bugs; it’s resolving tricky `NoMethodError` issues in core logic and even fixing complex error masking issues in our caching infrastructure.
* **Performance bottlenecks:** One of its most impactful contributions was a pull request that fixed a severe performance issue where git push was taking **~15 minutes** for engineers in Codespaces.
* **Fixing flaky tests:** We all know the pain of a test breaking a build. `@Copilot` was regularly assigned to investigate and fix them.

### Building new features

This is where it gets really interesting. `@Copilot` isn’t just maintaining old code; it’s actively building new functionality. Human engineers spec out the work in an issue, assign it to `@Copilot`, and it gets to work.

It’s adding:

* **New API endpoints:** One pull request added a new REST API endpoint to list repository security advisory comments.
* **New internal tools:** It’s not just adding production features. Copilot has been extensively used to improve our internal tools. Our internal intranet, training sites, onboarding, etc. All of these have been maintained significantly by Copilot.

### Migrations and security

Beyond daily tasks, we’re also assigning `@Copilot` to high-stakes, complex projects that are critical for the platform’s future.

* **Security gating:** `@Copilot` has been tasked with adding security gates to prevent our own internal integrations from performing sensitive actions, such as modifying releases or release assets.
* **Database migrations:** It’s even handling database schema migrations. We’ve assigned it critical, high-precision tasks like migrating column types to support new standards.
* **Documentation creation:** It’s used to keep documentation in sync with fast-moving code. For example, it has authored numerous pull requests to add comments to our rate-limiting code to ensure developers easily remember to keep it synchronized with another service.

### Codebase-wide audits and analysis: Discerning problems, then solving them

In some of its most advanced tasks, `@Copilot` acts as a researcher. We can assign it an ambiguous task, and it will analyze the codebase and report back with its findings in a pull request.

In one pull request, it was tasked with auditing all our Codespaces feature flags. It returned a pull request with a comprehensive report categorizing all the flags and their references throughout the code.

In another, it performed a “comprehensive analysis of authorization queries” to identify opportunities for performance and safety improvements. This moves Copilot’s role from code generation to systems-level architectural analysis, helping developers to jump straight to solving a complex problem rather than spending days just identifying it.

### A new way to collaborate

If you check Copilot’s merged pull request rate in your repo, you’ll notice it’s lower than human contributors. That’s expected—and useful.

Here’s the pattern we discovered:

* You assign `@Copilot` an issue
* It opens a pull request with a first-pass solution
* You review it like any other pull request
* **Then you choose** to merge it, iterate on the branch, or close it and take a different approach.

The value isn’t in blindly merging. *It’s in not starting from zero.* You’ll get a concrete implementation to critique right away. All the boilerplate and the basic scaffolding is already handled.

This shifts our focus away from writing *all* the code. Instead, you get to jump straight to the most critical parts of engineering: solving the core problem, refining Copilot’s suggestions, working in areas of the codebase not fit for AI, and owning the architecture, security, and user experience. It’s not about automating our jobs; it’s about letting Copilot handle the tedious 80% of the work. This frees us up to dedicate our expertise to the critical 20% that truly matters.

Start using [GitHub Copilot >](https://github.com/features/copilot?utm_source=blog-copilot-feature&utm_medium=blog&utm_campaign=universe25post)

---

## Tags:

* [GitHub Copilot](https://github.blog/tag/github-copilot/)

## Written by

![Matt Nigh](https://avatars.githubusercontent.com/u/5862416?v=4&s=200)

### [Matt Nigh](https://github.blog/author/mattnigh/)

[@mattnigh](https://github.com/mattnigh)

Program Manager Director, I lead the AI for Everyone program at GitHub.
