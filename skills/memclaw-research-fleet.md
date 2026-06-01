# MemClaw Research Fleet — Shared Governance Skill

## Your Memory Identity
- fleet_id: fleet-longrun-research
- tenant_id: default (omit if using managed cloud — inferred from API key)
- visibility: scope_team (all agents share this pool)

## BEFORE every task
1. Call `memclaw_recall` with your query, `fleet_ids: ["fleet-longrun-research"]`, and `include_brief: true`
2. Pass `status: "active"` to exclude outdated/archived rows. Note: `"active"` does not include `"confirmed"` — make a second call with `status: "confirmed"` if you need both.
3. Never act on recalled memories with status `outdated` or `conflicted`

## AFTER every task
1. Call `memclaw_write` with your finding
2. Always set `fleet_id: "fleet-longrun-research"` and `visibility: "scope_team"`
3. Include `agent_id` matching your own agent identity
4. You only need to send `content` — MemClaw auto-infers type, weight, tags

## Contradiction Rule
If you discover a fact that contradicts what you previously wrote:
- Write the NEW fact with `memclaw_write`
- MemClaw contradiction detection will auto-flag the old memory as `outdated`
- Do NOT manually delete old memories — let the crystallizer resolve them

## Status Transitions You Should Know
- `pending → confirmed` — when you verify a fact from another agent
- `active → outdated` — when you discover a superseding fact (MemClaw does this automatically)
- `active → archived` — when data is stale and not expected to update

## Nightly Crystallizer
MemClaw runs a background crystallizer that deduplicates near-identical memories
and resolves contradiction chains. After Day 9 of any drift scenario, explicitly
trigger it so the Synthesis agent gets a clean recall surface:

POST /api/v1/crystallize with your tenant credentials.