# Novation, architecture write-up

Agentic trademark research for law firms. Co-founded and built August 2025 to January 2026.

**The source is not public.** This is a description of what I built and what I learned, with no code and no client data.

## The problem

Before a law firm files a trademark, someone has to clear it: search the USPTO register for conflicting marks, then search common-law usage that never got registered at all, then form a judgment about whether the new mark is likely to be refused or challenged. Registered marks are the easy half. The hard half is the unregistered ones scattered across business filings, product pages and directories, where nothing is indexed for this purpose.

Firms did this manually. It took hours per mark, and the quality varied with how tired the associate was.

## What I built

A multi-tenant web application that ran the clearance search with agent swarms and returned a structured report with sources attached to every claim.

| Layer | What I used |
|---|---|
| Agents | GPT-5.2 swarms on ECS, one agent per search surface, results merged and deduplicated |
| Front end | React and Next.js, deployed on AWS Amplify |
| Data | PostgreSQL on RDS, multi-tenant schema, one isolated tenant per firm |
| Network | Private subnets with bastion-host access over EC2 and Ubuntu |

## What I learned that I would not have learned otherwise

**Trust is the product, not accuracy.** The first version returned good answers with no provenance. Attorneys would not use it, because a clearance opinion they cannot defend is worthless to them no matter how right it is. Attaching a source to every single claim mattered more than any improvement to the underlying search.

**Fan-out needs a merge strategy before it needs more agents.** Running one agent per search surface was easy. Reconciling five overlapping result sets, where the same mark appears under three spellings, was the actual engineering problem.

**Rate limits are a product constraint, not an infrastructure detail.** The USPTO API throttled us mid-demo more than once. Backoff, caching and a UI that degrades honestly instead of hanging were things I built because a partner watched it fail in front of me.

**Multi-tenancy from day one was the right call.** Ten firms meant ten sets of privileged client data that could never touch each other. Retrofitting isolation would have been far worse than building it in.

## Outcome

Deployed across 10 firms over 16 demos. Received a formal acquisition offer from two investors, and won $2,500 and $5,000 non-dilutive grants, including a top-12 finish out of 100+ startups in the "Building a Better Future through Business and AI" challenge.
