+++
title = "Building a production MCP server (and, incidentally, $45M/mo in ad spend)"
published = true
description = "An engineer’s story of turning a scrappy helper into a production Model Context Protocol server—stack, tradeoffs, and what I shipped. Side note: it now runs a lot of Meta ads."
tags = "ai, python, marketing, mcp"
cover_image = "https://pipeboard.co/content/journey-mcp-dev-to.png"
+++
## I built an MCP server to automate my ads

If you want to peek at the code, here’s the GitHub repo: [pipeboard-co/meta-ads-mcp](https://github.com/pipeboard-co/meta-ads-mcp).

### TL;DR

* **Problem**: I needed a programmable, repeatable workflow around Meta ads; the Ads Manager UI slowed me down.
* **Approach**: I prototyped scripts, then rebuilt everything as a Model Context Protocol (MCP) server so any AI client or dev tool could call it.
* **Stack**: MCP SDKs; Vercel + DigitalOcean; Supabase; OpenAI + Anthropic; Discord; PostHog; Senja (details below).
* **Growth**: Friends asked for access; hosting removed setup friction; usage grew quickly.
* **Today**: Side note—10,000+ businesses use it and ~$45M/mo in ad spend flows through the MCP. I launched paid plans while keeping a free tier.

### The spark: my own Instagram campaigns

I built this for myself while running Instagram ads for a product I was validating. The Facebook Ads Manager UI felt strange, and after a week of spend I wasn’t confident about what to improve: creatives, copy, targeting… or something else entirely.

### Getting help from AI

I asked ChatGPT and Claude for feedback on my campaign setup and metrics. The suggestions were surprisingly actionable. One example that paid off fast: because I had a small custom audience, people were seeing the same ad too often. Adding a frequency cap reduced fatigue and improved results.

Example JSON to set a frequency cap on an ad set:

Ad set update (PATCH `/{adset_id}`):

```json
{
  "frequency_control_specs": [
    { "event": "IMPRESSIONS", "interval_days": 7, "max_frequency": 2 }
  ]
}
```

Ad set creation (POST `/act_{ad_account_id}/adsets`) — excerpt:

```json
{
  "name": "My Ad Set with Frequency Cap",
  "campaign_id": "120000000000000",
  "daily_budget": "5000",
  "billing_event": "IMPRESSIONS",
  "optimization_goal": "REACH",
  "targeting": { "geo_locations": { "countries": ["US"] } },
  "frequency_control_specs": [
    { "event": "IMPRESSIONS", "interval_days": 7, "max_frequency": 2 }
  ],
  "status": "PAUSED"
}
```

### Turning tips into tools

I wanted to keep iterating, so I wrote scripts to analyze performance and suggest improvements. They turned into a loop: check metrics, propose next changes, track impact. Friends tried it and said they wanted to use the same workflow.

### Rebuilt as an MCP server

Right then, Anthropic announced MCP. It clicked: if my tooling spoke MCP, any compatible AI (and many dev tools) could use it. I rewrote the project as an MCP server and posted about it on LinkedIn. People picked it up and word spread organically.

### Making setup simple

Local setup was too hard. Installing Python, configuring environment variables, and connecting to Meta’s APIs was a lot to ask. So I launched it as [Pipeboard.co](https://pipeboard.co)—a hosted version that made onboarding much easier. It became the brand I use to promote the project. Adoption accelerated immediately once setup friction dropped.

### Making it reliable

For the past few months I’ve focused on fixing bugs, smoothing sharp edges, and making the system resilient—auth, rate limits, idempotency, retries, and observability. I’m also steadily improving the optimization logic so the campaigns it manages get better over time—lightweight heuristics, data-informed defaults, and safe, incremental changes. Feedback has been consistently positive: it’s easy to get running and it makes day-to-day Meta ad management less painful.

### Tech stack (for builders)

I built and operate this with a pragmatic stack: [Model Context Protocol](https://modelcontextprotocol.org) SDKs (define tools/resources so AIs can call real data and actions); [Vercel](https://vercel.com) and [DigitalOcean](https://www.digitalocean.com) (frontend/edge deploys; background workers, long-running jobs, and cron); [Supabase](https://supabase.com) (Postgres, row-level auth, and file storage); LLMs: [OpenAI](https://openai.com) and [Anthropic](https://www.anthropic.com) (reasoning, summarization, suggestions); [Discord](https://discord.com) (community support and fast feedback loops); [PostHog](https://posthog.com) (product analytics); and [Senja](https://senja.io) (collect and showcase user feedback/testimonials).

### Launching paid plans (free stays)

I launched paid plans this Monday on [Pipeboard.co](https://pipeboard.co) while keeping a free service available. I’m happy to say there are already many paying customers. That’s both validating and motivating—the roadmap is packed with ways to manage ads more efficiently that will be fun to build.

### Lessons for builders

* **Ship one tight loop first**: implement a single end-to-end tool that delivers value before generalizing. It clarifies schemas, auth, and error paths.
* **Align with MCP clients**: design clear tool schemas (names, params, returns), implement Discovery so clients can auto-load tools/resources/prompts, return deterministic JSON and structured errors, make tools idempotent and safe for retries, and verify with the MCP Inspector. See [MCP docs](https://modelcontextprotocol.io/llms-full.txt).
* **Design for retries**: make tools idempotent, handle rate limits, and include safe dry-run/preview modes.
* **Invest early in observability**: add request IDs, tracing, and product analytics to spot friction and failures fast.
* **Remove setup friction**: offer a zero-config hosted path while keeping a simple local dev story.

### What it does

* **Analyze performance**: surfaces insights like frequency issues, fatigued creatives, and under-delivering ad sets.
* **Suggest changes**: proposes tweaks to budget, targeting, placements, and creative rotation.
* **Automate safely**: gives you a human-in-the-loop workflow—review recommendations, then apply.
* **Plug into AI**: expose your ad account context directly to AI tools via MCP, so your assistant can reason with real data.

### Who it’s for

* **Technical builders**: who want a programmable, automatable way to run Meta ads with MCP-native tooling.
* **Marketers and agencies**: who want practical insights and simpler workflows without wrestling with the Ads Manager UI.

### Why it took off

* **Engineering depth → quality and outcomes**: I’ve been building software for [20 years](https://www.linkedin.com/in/yvesjunqueira/), including 10 at Google. That background let me relentlessly optimize reliability and overall quality. It just works—and that helps ads perform better and more efficiently.
* **Word of mouth from a “feels like magic” experience**: When a product consistently delivers results and removes friction, people share it with friends and colleagues. Most growth came from organic recommendations.

### What’s next

Although this is a solo project for now, the pace of feature requests and usage means I’ll likely bring on help to keep shipping quickly. The focus remains the same: better insight, smoother workflows, and safer automation.

### Try it

* **Code**: see the repo on GitHub: [Meta Ads MCP](https://github.com/pipeboard-co/meta-ads-mcp)
* **Community**: join the Discord (link in the README)

### Final thoughts

To my fellow builders, keep shipping: turn insight → action → learning, and ship products you’re proud of. It’s incredibly rewarding.

If this story helped you in any way, send me a note—I'd love to hear what resonated.