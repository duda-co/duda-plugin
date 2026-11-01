---
name: ai-visibility
description: Use when the user asks how often AI assistants (ChatGPT, Gemini, Google AI Overviews, Perplexity, Microsoft Copilot) mention a brand or website, how it ranks against competitors in AI answers, or how to improve that. Covers reading AI Visibility (AEO) reports for a Duda site or any domain, running a one-time report for a prospect or competitor, starting, pausing, resuming or refreshing ongoing tracking, and adding tracked prompts and competitors.
---

# AI Visibility

AI Visibility (also called AEO, answer engine optimization) measures how often AI answer engines mention a brand when people ask them questions, and where the brand ranks against its competitors. Call it "AI Visibility" with the user.

## When to use

- "How visible is [site] in ChatGPT?" / "Does AI recommend my client?"
- "How does [client] compare to their competitors in AI answers?"
- "Run an AI Visibility report on [prospect.com]" (sales audit for a site that isn't on Duda)
- "Give me a monthly AI Visibility summary for [site]"
- "Start tracking AI Visibility for [site]" / "Pause tracking" / "Refresh the data"
- "Which questions should we track?" / "Add [competitor] to the comparison"
- "How do we show up more in AI answers?"

## Tools

`get_aeo_report` · `create_aeo` · `manage_aeo_project` · `get_aeo_prompts` · `update_aeo_prompts` · `get_aeo_competitors` · `update_aeo_competitors`

## Plan and usage

- The account needs an AI Visibility plan. Without one, every tool fails with `AeoPlanNotFound`. Tell the user and don't retry.
- **Tracking** a Duda site (a project) uses 1 site slot. Pausing frees the slot and keeps the data.
- A **one-time report** uses 1 report from the plan. It works for any domain, on Duda or not.
- Each added **prompt** uses 1 prompt slot. Each **competitor** counts toward the plan's competitor limit.
- A manual **refresh** uses 1 of the site's refreshes for this billing cycle. The account's refreshes are shared by all its sites. Talk about refreshes only, never chats.
- On a plan-limit error (`AeoSitesLimitExceeded`, `AeoReportsLimitExceeded`, `AeoModelsLimitExceeded`, `AeoPromptsLimitExceeded`), tell the user the plan has none left. Don't retry.

## Vocabulary

- **Prompt**: a question people ask AI assistants, such as "best dentist in Austin". A project tracks a set of prompts.
- **Visibility**: the share of answers that mention the brand, as a percentage (0 to 100). Show it as "42%".
- **Position**: the brand's average rank in AI answers. Lower is better: 1.8 is just below second place.

## Instructions

### Pick the target

- A domain or URL goes in `domain`. The tool finds the Duda site for it and returns its `site_name`; reuse that `site_name` in later calls.
- A known site alias goes in `site_name`.
- A one-time report id (it starts with `or_`) goes in `report_id`. Call `get_aeo_report` with no target to list the account's one-time reports.

### Read: summary first

1. Start every AI Visibility question with `get_aeo_report`. By default it returns a 30-day summary: the brand's visibility, its rank against up to 4 competitors, the top competitor, and up to 3 prompts at the highest score.
2. Answer from the summary. Ask for more only when the question needs it:
   - More brands side by side: `detail: 'brands'`, or raise `brands`.
   - Per AI engine, or the sources the answers cite: `detail: 'full'`.
   - What to improve: `sections: ['recommendations']`.
   - What an engine actually said for one prompt: `get_aeo_prompts` with `view: 'answers'` and that `prompt_id`.
   - Another period: `from_date` and `to_date`. Month over month takes two calls, one per month.
3. Lead the answer with one sentence (visibility, rank, top competitor), then up to three short points: what is strong, what is weak, one next step.
4. Say "the prompts with the highest score" and "the prompts with the lowest score", not "best" and "worst". A score alone doesn't say that.
5. If the result has `filters_not_applied`, don't present those sections as filtered. If it has `top_only: true`, say the prompt figures cover the top prompts only.

### When there is no data

The result carries a `status` and a `next` hint. Follow the hint:

- `duda_site: false`: the domain isn't a Duda site of this account. Offer a one-time report.
- `NOT_STARTED`: the site has no AI Visibility yet. Ask the user to choose ongoing tracking (1 site slot) or a one-time report (1 report).
- `ACTIVATION_STARTED`: setup takes about 3 to 4 minutes. Tell the user and check again in a later turn with `sections: ['status']`, not in a loop.
- `ACTIVATION_COMPLETED` with no data: the first data run hasn't finished, or the filters match nothing.
- `DEACTIVATED`: tracking is paused and the data is kept. Offer to resume.
- `ACTIVATION_STALLED`: stop checking and tell the user setup did not finish.
- A one-time report with `ready: false` is still running. `missing_models` names the engines that haven't answered yet.

### When to ask first

- **Always ask first** for changes that spend something the user can't get back: starting tracking or a one-time report (`create_aeo`) and a manual refresh. Also ask first for any change you suggest yourself.
- **Act directly** on a reversible change the user explicitly asked for: adding the prompts or competitors they named, pausing tracking, or resuming it. A clear request is the yes.

### Start tracking or run a one-time report

1. Call `create_aeo` **without** `create` first. It is a preview and creates nothing. It returns `would_create` and what it uses.
2. Tell the user what will be created and what it uses, and ask. Call `create_aeo` again with `create: 'project'` or `create: 'one_time_report'` only after a clear yes.
3. A project needs `country_code`, the site's main market. Ask if you don't know it.
4. For a one-time report, the preview may ask for a brand name, a city, the audience country and 2 to 3 competitors with their websites. Ask the user once, say why each helps, and skip what they already gave. If they say to just run it, run it with what you have.
5. If the user asks for a one-time report, make a one-time report, even for a site that is already tracked.

### Change tracking

Read the status first with `get_aeo_report` `sections: ['status']`.

- **Pause** (`manage_aeo_project`, `action: 'pause'`): stops tracking, keeps the data, frees the site slot.
- **Resume** (`action: 'resume'`): uses 1 site slot.
- **Refresh** (`action: 'refresh'`): read `get_aeo_report` `sections: ['usage']` first, tell the user how many refreshes are left, then ask.
- **Never refresh after a brand name change.** A new brand name re-scores the data already collected, so the numbers update without a refresh. If mentions look missing because AI answers spell the brand differently ("2Fast" for "2 Fast Motorcycle Track Day"), suggest renaming the brand in the AI Visibility settings, not a refresh.

### Prompts and competitors

- To see what is tracked: `get_aeo_prompts` (`view: 'list'`) and `get_aeo_competitors`.
- To suggest new prompts: `get_aeo_prompts` with `view: 'suggestions'`, then let the user pick.
- To add prompts: `update_aeo_prompts` with `action: 'add'`. Each one uses a prompt slot. Add the prompts the user named directly; confirm a list you suggested first.
- To add a competitor: `update_aeo_competitors` with `action: 'add'`, the brand `name` and its `competitor_domain`. Ask for a missing domain; don't guess it.

### Improve visibility

1. Read `sections: ['recommendations']`. On-site recommendations need the site's content-suggestions permission and at least one competitor with a domain; if `on_site_unavailable` is set, say what is missing.
2. Show up to 3 recommendations, the most specific first.
3. For a change to the site's own content, offer to make it with the page or blog tools, and ask before you edit.

### Common flows

- **Monthly client report:** `get_aeo_report` for last month and the month before (two calls with `from_date` and `to_date`), then compare visibility, rank and the top competitor. Add `sections: ['recommendations']` for next steps.
- **Prospect audit:** `create_aeo` preview for the prospect's domain, collect the report inputs once, create the one-time report after a yes, then read it with `get_aeo_report` and its `report_id` when it is ready.
