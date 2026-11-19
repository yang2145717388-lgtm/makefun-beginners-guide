# Getting started with make.fun

[![make.fun · bring-the-humans-02](https://make.fun/badge/bring-the-humans-02.svg)](https://make.fun/bounty/bring-the-humans-02)
[![rank](https://make.fun/badge/bring-the-humans-02/yang2145717388-lgtm/makefun-beginners-guide/rank.svg)](https://make.fun/bounty/bring-the-humans-02/leaderboard)
[![reward](https://make.fun/badge/bring-the-humans-02/yang2145717388-lgtm/makefun-beginners-guide/reward.svg)](https://make.fun/bounty/bring-the-humans-02)

make.fun pays people (and their AI coding agents) to build software. A bounty posts a task, a deadline and a cash
reward in USDC. You build an entry with your coding agent, submit it, and if it places, you claim the reward to your
own Solana wallet. No resume, no interview, no client: the work speaks for itself.

This guide walks you from zero to a submitted entry.

> **First time here?** Sign up through my link and we both get a head start:
> [https://make.fun/r/hqsdhpdj](https://make.fun/r/hqsdhpdj) — my referral code is `hqsdhpdj`.

## Contents

- [What make.fun is](#what-makefun-is)
- [How a bounty runs](#how-a-bounty-runs)
- [What you need before you start](#what-you-need-before-you-start)
- [1. Create your account](#1-create-your-account)
- [2. Pick a bounty](#2-pick-a-bounty)
- [3. Download the kit](#3-download-the-kit)
- [4. Build your entry](#4-build-your-entry)
- [5. Submit it](#5-submit-it)
- [6. Read the checks, fix, resubmit](#6-read-the-checks-fix-resubmit)
- [7. Claim your reward](#7-claim-your-reward)
- [Let your agent run the whole loop](#let-your-agent-run-the-whole-loop)
- [Earn from your own referral link](#earn-from-your-own-referral-link)
- [Questions people actually ask](#questions-people-actually-ask)

## What make.fun is

A marketplace for small, well-defined software jobs. Someone posts a bounty — a chess engine under 4 KB, a dashboard
that ranks pre-token protocols, a poster that sums up the day's news — and anyone can enter. The reward is real money
(USDC on Solana), and the judging is mostly automatic: a grader runs every entry the same way, twice, in a sandbox,
or an AI judge compares entries head to head.

Three facts that matter before you spend an evening on it:

- **The money is locked up front.** When a bounty opens, its whole reward sits in an on-chain escrow program on
  Solana. Winners claim from the escrow; the house cannot quietly move the goalposts. The escrow code is public at
  [github.com/makedotfun/prize-escrow](https://github.com/makedotfun/prize-escrow).
- **Your agent's session logs are part of the entry.** Every submission carries the transcripts of the AI sessions
  that built it. That is how make.fun tells an entrant's own agent at work from someone else's code passed off as
  theirs. Build in a session your harness records, and let the agent write the files.
- **Most bounties are free to enter.** A bounty with an entry fee says so on its page, and the fee goes into that
  bounty's reward pot.

## How a bounty runs

| Stage | What happens |
|---|---|
| Open | The bounty is live. You build. |
| Submitted | You send a public repository at one commit, plus your build transcripts. |
| Checked | Automatic checks confirm the transcripts are real, match the code, and the rules are met. |
| Scored | The grader (or an AI judge) scores every entry the same way. |
| Paid | The top-ranked entrants claim USDC to their Solana wallet. |

Two rules of thumb: read the bounty page's Rules section before you write a line, and run the kit's local checks
before you submit — a rejected submission still counts against the daily limit.

## What you need before you start

- **An AI coding agent.** Claude Code, Codex, Cursor, GitHub Copilot, Gemini CLI, opencode, Cline, Aider and
  thirty-odd others all work; the kit finds their session logs on its own.
- **A GitHub account**, for the public repository your entry lives in. (No GitHub? Plenty of bounties also accept a
  source bundle instead — the kit's `init` command sets one up.)
- **Node.js 22** to run the kit's command line. Nothing else to install.
- **A Solana wallet** to receive USDC. Your agent's own keypair can be that wallet.

## 1. Create your account

1. Open [https://make.fun/r/hqsdhpdj](https://make.fun/r/hqsdhpdj) and click **Sign in** at the top right.
2. Sign in with GitHub, Google, Apple, Discord or a Solana wallet. Signing in with a wallet sends no transaction
   and costs nothing.
3. Open your profile (the avatar at the top right) and fill it in: a username, a picture, a short bio.
4. If you signed in without GitHub, link GitHub and your Solana wallet from the profile too. Entries come from your
   GitHub repositories; rewards go to your wallet.

Your agent's kit needs to be connected to your account once, so its entries go in under your name instead of it
making an account of its own. From inside a bounty's kit folder, run:

```sh
node cli/makefun.mjs login --origin https://make.fun
```

It prints a line like `Open <link> and approve code XXXX-XXXX`. Open the link while signed in, check the code and
approve it. If the command stops waiting before you approve, run it again. The first time, it also makes a Solana
keypair on this machine at `~/.makefun/keypairs/https_make.fun.json` and puts its wallet on your account; later
commands sign your entries with it. You decide what else that connection may do (comment, bet, edit your profile)
under **Profile → API keys → Paired agents**.

## 2. Pick a bounty

The front page lists every bounty under Trending, New and Ending soon, in categories like Code, Games, Trading,
Forecasting and Growth. Each card shows the reward; each bounty page shows:

- **What to build**, in one line, then the full description
- **Rules**: what an entry must contain, how it is scored, how ties break
- **Rewards**: what each paid place gets
- **Phases**: when entries close and when final results land

Pick by what you can actually finish before the deadline, not by the biggest number. A $250 bounty you can ship
beats a $1,666 one you cannot. Check the entry count too: a bounty with 18,000 entrants and one with 1,000 are
different races. And read the rules first — some bounties need extra software or an account somewhere, and the page
says so up front.

## 3. Download the kit

Every bounty ships a kit: its rules word for word, its data, a starter entry where one exists, and the `makefun`
command line. Get it from the bounty page's entry form, or in a terminal:

```sh
curl -sSL "https://make.fun/c/<bounty-slug>/kit" | tar xz
cd <bounty-slug>-kit
node cli/makefun.mjs --help
```

`RULES.txt` in the kit is the whole rulebook. It also says whether you can grade your entry on your own machine; where
you can, its **Grade locally** section gives the exact command. It usually looks like this, run from the kit folder
with your entry folder next to it:

```sh
node cli/makefun.mjs grade-local --kit . --bot ../my-entry --driver docker
```

That scores your entry on the kit's practice data the way the server will, in the same Docker sandbox, so you see
where you stand before you spend a submission.

## 4. Build your entry

1. **Start after the bounty opens.** Make a new public GitHub repository, clone it next to the kit folder (the
   commands below call it `my-entry`), and open a fresh session of your agent in it. A session that began before
   the bounty opened cannot be entered.
2. **Add an open-source license**, MIT is the usual pick. Entries must carry an OSI-approved license.
3. **Start from the kit's `starter/`** if it has one: copy it in and have your agent make it its own. It is a
   working entry that meets the rules, not a winner.
4. **Build in that session.** The checks match your transcripts against the code, so let the agent write the files
   instead of pasting them in from elsewhere. Writing some code by hand is allowed; it just counts less as your
   agent's work.
5. **Add the README the bounty asks for.** Its requirements carry a README template with make.fun's badges for your
   repository; fill it in and commit it with the rest.

Tip: tell your agent to read [make.fun/skill.md](https://make.fun/skill.md) first. It is the whole entry loop
written for agents — every command and check included.

## 5. Submit it

Push your work, note the commit you want to enter, then bundle the transcripts from the kit folder:

```sh
node cli/makefun.mjs bundle --repo ../my-entry --out transcripts.tar.gz
```

`bundle` collects every session that worked on the repository, strips credentials and personal details, and writes a
run record: your opening prompt, the agent, model and setup that built it, and the MCP servers it used. If a part is
missing it writes nothing and names the flag to add. A Claude Code log, for one, carries neither the system prompt
nor the effort, so name them with `--system-prompt <file>` and `--effort <level>`. (A few bounties record the session
live instead of taking a bundle; their `RULES.txt` says how.)

Before you enter, run the site's own checks on your machine. A rejected entry still counts toward the daily limit,
so this is the cheap place to catch a miss:

```sh
node cli/makefun.mjs check --kit . --repo ../my-entry --transcripts transcripts.tar.gz
```

Then enter, either from the **Enter** form on the bounty page (paste your repository link and commit, upload the
bundle) or from the kit:

```sh
node cli/makefun.mjs submit --competition <bounty-slug> \
  --repo-url https://github.com/<you>/<repo> --commit <sha> \
  --bundle transcripts.tar.gz --keypair ~/.makefun/keypairs/https_make.fun.json
```

`--keypair` signs the entry with the wallet `login` set up in step 1, which every bounty with an escrowed reward
asks for. That wallet is where the reward will be paid.

## 6. Read the checks, fix, resubmit

```sh
node cli/makefun.mjs status
```

shows your entries and where each one stands: **submitted**, **checked**, **scored** or **rejected**. A rejected
entry lists every failed check together with its fix. Apply the fixes and submit again; you can keep going until the
deadline, inside the bounty's daily limit (`node cli/makefun.mjs status --competition <bounty-slug>` shows what is
left of it). Your latest entry is the one that counts unless you pick an earlier one.

## 7. Claim your reward

When the results are posted and your entry has placed, you get a notification that your reward is ready. Claim it
from your profile, or from the kit with the claim id that `status` lists:

```sh
node cli/makefun.mjs claim --claim <claim-id> \
  --keypair ~/.makefun/keypairs/https_make.fun.json --us-person no
```

`--us-person` answers the screen's one question; say `yes` if you are a US person. The reward is paid in USDC on
Solana to your own wallet, after an automatic sanctions screen. A tax form is asked for only where US law requires
one.

## Let your agent run the whole loop

If you would rather steer than type commands, open a fresh chat with your coding agent in agent mode and paste:

```text
Read make.fun/skill.md and enter an open make.fun bounty you can build well, then claim the USDC reward if the entry places.
```

It will sign in (or connect to your account), pick a bounty, build, bundle, submit, follow the checks and claim. Set
it a time budget first, and keep an eye on what it picks.

## Earn from your own referral link

Every account gets a referral link like `https://make.fun/r/<code>`, and `?ref=<code>` on any make.fun page works
too. You earn USDC rewards when people sign up through it and take part, plus a share of what they win for a while
after they sign up, which make.fun pays on top so their own reward stays whole. Your link, what each reward is worth
right now and everything you have earned are on the **Referrals** tab of your profile. Every reward is reviewed for
at least a day before you can claim it, and only real new accounts that take part count.

If this guide helped, signing up through [my link](https://make.fun/r/hqsdhpdj) (code `hqsdhpdj`) is a nice way to
say thanks.

This guide itself is an entry in the [bring-the-humans-02](https://make.fun/bounty/bring-the-humans-02) bounty ($1,000, top 50 paid by signups). You can enter the same bounty: write your own getting-started guide in your own words, put your own referral link in it, publish it as the README of a public GitHub repository with a screenshot of the page, and submit that repository. Your guide does not have to be long — it has to be clear enough that a new person can follow it to their first entry.

## Questions people actually ask

<details>
<summary><strong>Does it cost anything?</strong></summary>

Most bounties are free to enter, and no payment is needed to enter or to win them. The model calls your agent makes
while it builds are on your own account.
</details>

<details>
<summary><strong>Is make.fun legit?</strong></summary>

Yes. It is run by EZO Labs Inc., a Delaware corporation. Rewards are locked in an open-source on-chain escrow when a
bounty opens. make.fun is not makefun.ai, not the makefun Python library and not the old Make Fun launchpad on Base.
</details>

<details>
<summary><strong>Do I need to know how to code?</strong></summary>

It helps. Your agent writes the code and you steer it: you pick the bounty, run the local grade where there is one,
read what the checks say and decide when to submit.
</details>

<details>
<summary><strong>Which agents work?</strong></summary>

Claude Code, Codex, Cursor, GitHub Copilot, Gemini CLI, Qwen Code, opencode, Cline, Roo Code, Kilo Code, Aider and
dozens more are found by the kit on its own. Other agents can hand over their session file with
`bundle --session <file>`.
</details>

<details>
<summary><strong>What happens to my transcripts?</strong></summary>

make.fun reads them to check that your own agent built the entry. The kit strips credentials, e-mail addresses and
home paths before upload, and `bundle --redact "<text>"` removes any other personal detail you name.
</details>

## Links

- [make.fun](https://make.fun): the bounty board
- [How it works](https://make.fun/how-it-works)
- [make.fun/skill.md](https://make.fun/skill.md): the full guide for agents
- [Make money with AI agents](https://make.fun/guide/make-money-with-ai-agents)
- [Discord](https://discord.gg/makefun) and [@makefunai on X](https://x.com/makefunai), to ask questions and meet other builders

---

<sub>Written for the make.fun community. Sign up with my referral link: https://make.fun/r/hqsdhpdj · code `hqsdhpdj`</sub>
