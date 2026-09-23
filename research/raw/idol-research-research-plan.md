# Idol Research Plan

## Purpose

`idol-research` is the main Data Science / Research case for building a longitudinal, source-backed dataset of the idol ecosystem.

Goal: collect broadly first, preserve provenance, resolve entities, then analyze and generalize.

## Research Loop

```text
SEED SOURCES
    ↓
COLLECT
    ↓
RAW OBSERVATION
    ↓
NORMALIZE
    ↓
ENTITY RESOLUTION
    ↓
RELATION / EVENT GRAPH
    ↓
ANALYSIS
    ↓
HYPOTHESIS
    ↓
EVALUATION
    ↓
NEXT RESEARCH
    ↺
```

## Scope

Collect the ecosystem rather than only famous idols.

### 1. People

- active members
- former members / graduates
- producers
- managers / organizers when publicly documented
- songwriters / composers / choreographers

### 2. Groups

- idol groups
- units
- solo idols
- temporary / project groups
- local and underground groups

### 3. Organizations

- agencies
- labels
- management companies
- production companies
- event organizers
- media companies

### 4. Events

- live shows
- festivals
- joint events / taibans
- tours
- release events
- fan meetings
- cheki / meet-and-greet events
- auditions

### 5. Places

- live houses
- theaters
- halls
- clubs
- festivals venues
- recurring event locations

### 6. Music

- songs
- albums / singles
- releases
- MV / video
- streaming records when observable
- credits

### 7. Media / Web

- official websites
- news articles
- interviews
- magazines
- YouTube
- streaming pages
- event listings

### 8. Social

- X
- Instagram
- TikTok
- YouTube
- other publicly observable social accounts

Record platform, URL, handle, observed_at and source where possible.

### 9. Commerce

- tickets
- CDs / downloads
- merchandise
- cheki / photo sessions
- membership / subscription when publicly documented

### 10. Relations

- member_of
- managed_by
- performed_at
- appeared_in
- co_appeared_with
- released
- produced_by
- written_by
- composed_by
- choreographed_by
- held_at
- participated_in
- graduated_from
- transferred_to
- collaborated_with

## Time Dimension

Every observation should preserve time whenever available.

```text
observed_at
published_at
started_at
ended_at
accessed_at
retrieved_at
```

Do not silently turn a current observation into historical fact.

## Source Policy

Prioritize primary sources, then high-quality secondary sources.

```text
official source
    > official social account
    > event / ticket platform
    > reputable media
    > database / directory
    > search result / community source
```

Keep the original URL and provenance.

## Data Layers

```text
raw/
  source observations

data/
  normalized records

entities/
  resolved people / groups / agencies / venues

relations/
  graph edges

events/
  time-based observations

analysis/
  derived metrics and notebooks

research/
  questions / hypotheses / results
```

The exact directory layout can evolve; the conceptual separation should remain.

## Core Entity Model

```text
Person
Group
Agency
Event
Venue
Song
Release
Media
SocialAccount
Product
Source
Observation
```

## Minimum Observation Record

Every collected fact should aim to retain:

```text
subject
predicate
object
source
url
published_at
observed_at
accessed_at
retrieved_at
confidence
notes
```

Separate:

- Fact
- Inference
- Hypothesis

Never overwrite a fact with an inference.

## Research Seeds

Start from existing seed material and expand recursively.

```text
seed group
  → official site
  → members
  → agency
  → events
  → venues
  → co-appearances
  → music
  → media
  → social
  → related groups
  → related organizers
  → next seed
```

Seeds should be maintained as a queue, not treated as a one-time list.

## Collection Strategy

### Phase 0 — Inventory

- inspect existing `idol-lab` assets
- inspect existing `idol-research` data
- identify duplicate entities
- identify missing schemas
- list known seed sources

### Phase 1 — Broad Crawl

Collect breadth before deep analysis.

Priority:

1. groups
2. members
3. agencies
4. events
5. venues
6. official URLs / social accounts
7. music
8. media
9. relations

### Phase 2 — Entity Resolution

Resolve aliases and duplicates.

Examples:

```text
same person / different spelling
same group / former name
same venue / abbreviated name
same agency / corporate name
same event / multiple listing pages
```

Keep aliases instead of deleting them.

### Phase 3 — Longitudinal Dataset

Build snapshots over time.

```text
2024
2025
2026
...
```

Track additions, removals, membership changes, event activity and observable audience signals.

### Phase 4 — Graph

Construct the ecosystem graph.

```text
person ──member_of──> group
 group ──managed_by──> agency
 group ──performed_at──> venue
 group ──participated_in──> event
 person ──appeared_with──> person
 group ──released──> song
 song ──written_by──> person
 event ──organized_by──> organization
```

### Phase 5 — Data Science

Potential analyses:

- event network centrality
- venue network
- agency network
- co-appearance network
- member mobility
- group survival / activity patterns
- event frequency
- geographic concentration
- music / event relationships
- social growth vs event exposure
- pathway from local / underground activity to larger scenes

Do not assume causal relationships without evidence.

### Phase 6 — Research Loop

Turn observations into explicit research questions.

```text
Observation
→ RQ
→ Hypothesis
→ Dataset
→ Analysis
→ Evaluation
→ Insight
→ New RQ
```

## AW / Agent Workflow

The collection system should eventually run as an Agent Workflow.

```text
Seed Queue
   ↓
Research Agent
   ↓
Source Discovery
   ↓
Fetcher / Crawler
   ↓
Evidence Extractor
   ↓
Entity Resolver
   ↓
Validator
   ↓
DB Commit
   ↓
Research Queue
```

Agents must not invent facts. Every factual record needs evidence or an explicit unresolved state.

## What We Do Not Do

- do not scrape indiscriminately without respecting site terms / robots / access limits
- do not collect private personal information
- do not treat social follower counts as permanent truth
- do not infer identity from weak evidence
- do not merge entities only because names look similar
- do not present hypotheses as facts
- do not optimize for maximum row count at the expense of provenance

## Definition of Done

The project becomes a strong DS case when it can demonstrate:

1. a broad source inventory
2. reproducible collection
3. provenance-preserving observations
4. entity resolution
5. temporal snapshots
6. an ecosystem graph
7. at least one meaningful analysis
8. explicit hypotheses and evaluation
9. an automated research loop
10. a public visualization / research result

## Portfolio Story

```text
I wanted to understand an entire living cultural ecosystem.

So I treated idols, groups, agencies, events, venues,
music and media as observable entities and relations.

I collected evidence over time, resolved entities,
built the graph, and used Data Science to ask questions
about how the ecosystem actually behaves.

The research system then turns each finding into the next question.
```
