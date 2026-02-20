# CAPTCHA Operations & Troubleshooting

<link-summary>Operational playbook for triaging CAPTCHA spikes, identifying the trigger, and applying safe mitigations.</link-summary>
<card-summary>Use this page when customers report unexpected CAPTCHA prompts or when traffic anomalies are detected.</card-summary>
<web-summary>Guidance for support and operations teams during abuse spikes and false-positive reports.</web-summary>

<primary-label ref="audience-ops"/>
<secondary-label ref="security"/>
<secondary-label ref="stable"/>

## When to use this page
<secondary-label ref="impact"/>

Use this runbook when one of the following happens:

- Customers report a sudden increase in CAPTCHA prompts.
- Monitoring detects a request spike or unusual traffic shape.
- Security flags abuse from a specific source.
- A temporary manual CAPTCHA override is requested.

## What to collect from a report

Ask the reporter (or collect internally) before taking action:

- **Timestamp** and timezone (or specify UTC).
- **Request ID / trace ID**.
- **Endpoint / operation** being called.
- **Effective client IP** (and whether the user is on VPN/proxy).
- **Frequency**: roughly how many attempts per minute.
- **Screenshot** of the prompt (if this is a UI flow).

This information is usually enough to confirm the trigger and estimate scope.

## Quick triage checklist

1. **Confirm the trigger** (which rule matched).
2. **Estimate impact** (how many users / which region / how long).
3. **Decide if expected or false positive** (NAT, partner integration, retry storm).
4. **Pick an action** (wait, advise user, or apply a tightly-scoped manual override).
5. **Escalate** if the case involves high-value customers, widespread impact, or unclear behavior.

## How to identify the trigger

Start with the most recent rule-evaluation logs around the reported timestamp.

<code-block lang="json" src="../codeSnippets/logs/rule-evaluation-event.json"/>

Look for:

- **decision**: `CAPTCHA_REQUIRED` means the request was challenged.
- **manualOverride**: `true` indicates enforcement was configured by an admin.
- **matchedRules**: the rule(s) that caused the decision. The `id` field explains the trigger.

If multiple rules match, treat the **highest-priority rule** as the primary explanation:
manual override → blacklist → IP burst → hourly spike → repeated payload.

## Trigger-to-action matrix

| What you see | Likely cause | First action | Escalate when |
|---|---|---|---|
| **manualOverride=true** | Admin forced CAPTCHA for a scope | Confirm scope and planned end time; ensure it is not broader than necessary | No end time set, or large portion of traffic affected |
| Rule indicates **blacklist** | Known-bad IP source | Confirm the IP is still considered risky (recency, source) | IP belongs to a trusted partner or a verified customer network |
| Rule indicates **IP burst** | High request rate from one IP | Check if this is NAT/proxy concentration or automation; advise customer if needed | High-value customers affected, or the same IP appears across many unrelated accounts |
| Rule indicates **hourly spike** | Traffic anomaly | Validate against business events (campaign, batch jobs); check for retry storm | Spike persists across multiple hour buckets or correlates with elevated error rates |
| Rule indicates **repeated payload** | Many identical requests in a short window | Check whether client retries are happening; ask for client logs/retry settings | Payload belongs to a known integration path or a partner use case |

## Manual override guardrails

<include from="lib.md" element-id="manual-override-warning"/>

When applying a manual override (or requesting one):

- Keep the **scope as narrow as possible** (specific endpoint, partner, or traffic segment).
- Always set an **explicit end time** and a **reason** in the incident ticket.
- Prefer **short time windows** and re-evaluate before extending.

## Escalation checklist

Escalate to Security / Infra when any of the following is true:

- CAPTCHA appears for **many unrelated users** at once.
- A **trusted partner** or **high-value customer** is blocked.
- You suspect a **retry storm** (errors causing clients to hammer the service).
- You cannot clearly map the behavior to a rule trigger.

Include in your escalation message:

- Incident/ticket ID
- Time range (UTC)
- Top IPs / endpoints involved
- Example request IDs
- The matched rule ID(s) and observed values (from logs)

## FAQ (for support)

**Why do many users in one company see CAPTCHA at once?**
Often they share one outbound IP (NAT / corporate proxy). High combined traffic can trigger the IP burst rule.

**Can we disable CAPTCHA for a single user?**
Usually no. CAPTCHA is applied based on rules and scopes (IP, endpoint, traffic segment). If needed, request a **narrow** manual override.

**What can we suggest to a customer using a VPN/proxy?**
Ask them to retry without VPN/proxy or from another network. Shared exit nodes can be blacklisted or can trigger IP burst thresholds.

**How long will a CAPTCHA spike last?**
It depends on the trigger: IP burst uses a ~20-minute window, repeated payload uses ~30 seconds, and hourly spikes are evaluated per hour bucket. Manual overrides last until disabled.

**What should we say externally?**
Keep it simple: “We use adaptive verification to protect the service. It can trigger during unusual traffic patterns. We’re investigating the specific cause.”

<seealso style="cards" title="Related">
    <category ref="security">
        <a href="CAPTCHA-Decisioning-Overview.md" summary="Rule definitions, thresholds, and decision priority."/>
        <a href="CAPTCHA-Glossary.md" summary="Shared vocabulary used in tickets and incident reports."/>
    </category>
</seealso>
