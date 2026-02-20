# CAPTCHA Glossary

<link-summary>Definitions of terms used in the CAPTCHA articles.</link-summary>
<card-summary>Quick-reference glossary for non-developer specialists.</card-summary>
<web-summary>Shared vocabulary for incident handling, reporting, and stakeholder communication.</web-summary>

<primary-label ref="audience-nondev"/>
<secondary-label ref="stable"/>

## Terms

| Term | Definition |
|---|---|
| CAPTCHA challenge | A user verification step shown when request risk is above policy threshold. |
| Decision rule | A condition that can require a CAPTCHA (IP burst, blacklist, hourly spike, repeated payload, manual override). |
| Rule trigger | Evidence that a specific decision rule matched (for example, a log entry showing the observed value exceeded a threshold). |
| Effective client IP | The client IP address as determined by the service after traffic passes through CDN / reverse proxy / load balancer. |
| NAT / corporate proxy | A network setup where many users share one outbound IP address. This can make legitimate traffic look like a burst from “one IP”. |
| Blacklist | A maintained list of IP addresses with known abuse history. |
| Hour bucket | Aggregated request volume for the current one-hour period. |
| Baseline | Historical average used for anomaly comparison (in this doc set, the last %traffic_baseline_period_days% days). |
| Traffic spike (anomaly) | A sudden increase in requests compared to the baseline for the current hour bucket. |
| Repeated payload | Identical request body sent many times within a short time window. Legitimate causes include aggressive retries. |
| Manual override | Admin action that forces CAPTCHA for a configured scope regardless of automatic rules. |
| False positive | Legitimate traffic incorrectly challenged as suspicious. |
| Retention impact | Negative effect on users completing their intended flow after seeing a challenge. |

<seealso>
    <category ref="operations">
        <a href="CAPTCHA-Operations-Runbook.md" summary="Triage steps and actions that reference these terms."/>
    </category>
    <category ref="security">
        <a href="CAPTCHA-Decisioning-Overview.md" summary="Rule logic referenced by glossary entries."/>
    </category>
</seealso>
