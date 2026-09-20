---
name: skill-scout
description: Search local skills, marketplaces, GitHub, and the web before you create, build, find, fork, or extend a Claude Code skill.
origin: community
---

# Skill Scout

> Community work by `redminwang`, reused from old PR #1232. Full credit goes to the original author.

Use this skill before you make a new skill. It helps you find work that may already exist. It also helps you check outside code before you trust or use it.

## When to Use

Use this skill when:

- The user asks to create, build, or make a skill.
- The user asks if a skill exists for a task.
- The user shares a work flow that may fit a skill.
- The user wants to fork or extend a skill.
- You need to find the closest skill for a task.

Do not run a search if the user clearly asks you to skip it. Confirm that choice, then start the requested build work.

## Step 1: Define the Need

Write down:

- The task the skill must do.
- The words or events that should trigger it.
- The tools, apps, file types, or data it may use.
- Three to five search words.
- Common names or spellings for the same task.

Keep the scope clear. Split the request if it has two tasks that do not belong in one skill.

## Step 2: Search Local Sources

Search installed skills first. Then search local marketplace files. Local skills are easier to check and may already be ready to use.

Use safe, short search terms. Escape any term that has shell symbols.

```bash
find ~/.claude/skills -maxdepth 2 -name SKILL.md 2>/dev/null | grep -iE "keyword|synonym"
find ~/.claude/plugins/marketplaces -path '*/skills/*/SKILL.md' 2>/dev/null | grep -iE "keyword|synonym"
```

Search skill text and frontmatter too:

```bash
grep -RilE "keyword|synonym" ~/.claude/skills ~/.claude/plugins/marketplaces 2>/dev/null
```

If a folder does not exist, skip it. Do not treat that as an error.

If the search finds too many files, use a more exact term. If it finds none, try a wider term or a common synonym.

## Step 3: Search Remote Sources

Use GitHub and web search tools only when they are already available and the user has not asked for local-only work.

Keep each search short:

```bash
gh search repos "claude code skill keyword" --limit 10 --sort stars
gh search code "name: keyword" --filename SKILL.md --limit 10
```

Use no more than three web searches:

```text
"claude code skill" keyword
"SKILL.md" keyword
"everything-claude-code" keyword
```

If GitHub or web search is not available, say which search was skipped. Continue with the sources you can check.

Do not sign in, add a token, install a tool, or change network settings just to search.

## Step 4: Check Each Outside Skill

Do not suggest an outside skill until you inspect it.

Check:

- The `name` and `description` in its frontmatter.
- All steps in its `SKILL.md`.
- Any script, template, or file that the skill tells users to run.
- Shell commands and file writes.
- Network calls and package installs.
- Use of keys, passwords, cookies, or login data.
- The license and any credit rules.
- Recent work, open issues, and signs that the project is still kept up.

Reject or warn about a skill if:

- Its source cannot be checked.
- Its license is missing or does not allow reuse.
- It hides code in a short link or unknown download.
- It asks for more access than its task needs.
- It can delete or replace files without a clear warning.
- Its steps do not match its stated job.

Do not run outside code as part of the review.

For a fork, copy the skill into a new local folder or branch. Keep its license and credit. Review the diff. Do not edit the marketplace copy in place.

## Step 5: Rank the Results

Rank matches in this order:

1. Exact match in the skill name.
2. Match in the description or a clear synonym.
3. Installed or local marketplace skill.
4. GitHub skill with a clear license and recent work.
5. Web mention with no source files.

Also weigh safety and task fit. A safe close match should rank above an exact match with risky steps.

Remove duplicate copies of the same skill. Keep no more than ten results. If there is no good match, say so clearly.

## Step 6: Show the Choice

Show a short table:

| Choice | What it means |
| --- | --- |
| Use it | Use or install a skill that fits the task. |
| Fork it | Copy the closest skill and make small changes. |
| Make new | Build a new skill because no close match exists. |

For each result, state:

- Where it came from.
- Why it fits.
- What is missing.
- Any safety or license concern.
- Whether it was fully checked.

Do not install, fork, or create anything until the user picks a path. You may move on without asking only when the user already chose a path or no close match exists.

## Concrete Example

A user says:

```text
Make a skill that writes release notes from merged pull requests.
```

First, list the need:

```text
Task: Write release notes from merged pull requests.
Triggers: "write release notes", "make a changelog".
Tools: GitHub data and Markdown files.
Search words: release notes, changelog, pull request, PR summary.
```

Then search local sources. If allowed and available, search GitHub and the web. Read each close match before you rank it.

Report the result like this:

| # | Skill | Source | Why it fits | Gap | Check |
| --- | --- | --- | --- | --- | --- |
| 1 | article-writing | Local | Writes and edits clear text | No pull request steps | Safe local files |
| 2 | content-engine | Local marketplace | Has a full writing flow | Too large for this task | Safe, but broad |
| 3 | blog-writer | GitHub | Turns source text into a draft | Needs license and script review | Not fully checked |

Then give a short choice:

```text
I found two close local skills and one outside skill. `article-writing`
is the best fit, but it has no pull request checklist. You can use it,
fork it for release notes, or make a new skill.
```

## Edge Cases

- If the user gives an exact skill name, search for that name first.
- If two skills share a name, compare their source, license, and update date.
- If a skill is only a web post, label it as a lead, not a trusted source.
- If a repo is old but the skill is small and safe, report that fact. Do not reject it only for age.
- If the best match covers only half the task, suggest a fork or a small add-on.
- If several small skills can do the job, explain that choice. Do not force one large skill.
- If the user wants private or offline work, search local sources only.
- If search tools fail, report the failed parts and use the results you already have.
- If the license or author is unclear, do not copy the skill.
- If no close match exists, say what you searched before you suggest a new skill.

## Avoid These Mistakes

- Do not start a new skill before a useful search.
- Do not install an outside skill before reading it.
- Do not run outside code during review.
- Do not show a long list of weak, unranked matches.
- Do not treat a web mention as trusted source code.
- Do not edit a marketplace skill in place.
- Do not hide license, safety, or review gaps.
- Do not drop the original author's credit when you fork a skill.

## Related Skills

- `search-first`: A general search-before-build flow.
- `skill-stocktake`: Checks installed skills for health, overlap, and gaps.
- `agent-sort`: Sorts and groups agents and skills.