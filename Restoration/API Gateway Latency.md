# Incident Scenario – ProdEase AI Application

### Incident Title:
ProdEase AI Application Slowness Due to API Gateway Latency

### Date/Time:
August 25, 2025 – 2:40 PM (GMT+8)

### Detected By:
User Reports + Monitoring Alert (New Relic APM)

### Severity:
Medium (P2 – Performance Degradation)

### Description:
At 2:40 PM, multiple users reported slow response times (20–30 seconds delay) while accessing ProdEase AI dashboards. Monitoring confirmed high latency in API requests passing through the application’s API Gateway.

### Business Impact:
- Users experienced delayed responses when fetching AI-generated reports.
- Automation tasks were significantly slowed down.
- Partial degradation for ~70% of users (no total outage).

## Investigation Steps
1. Check Application Monitoring Dashboards (APM / New Relic):
    - Observed high response time spikes (avg 25s per request).
2. Validate API Gateway Logs:
    - Found increased 504 Gateway Timeout errors for outbound API calls to a third-party ML service.
3. Check Third-Party Dependency (ML Service):
    - Contacted vendor status page → confirmed partial outage affecting requests in APAC region.
4. Examine ProdEase AI Backend Services:
    - Services were up but pending on responses from the external API.
    - Queue backlog growing due to retry attempts.

## Restoration Steps
1. Containment:
    - Disabled automatic retries on failing API calls to prevent further backlog.
    - Updated incident channel with ongoing issue details.
2. Mitigation:
    - Applied circuit breaker configuration in API Gateway to route failed third-party calls to a fallback cache mechanism.
    - Activated cached responses for commonly accessed AI models to keep dashboards functional.
3. Validation:
    - Re-tested dashboard performance → average response time dropped back to 2–3 seconds.
    - Confirmed no new timeouts observed in logs.
4. Resolution:
    - Full functionality restored at 3:25 PM (45 minutes partial service degradation).
    - Business users confirmed improved response times.

## Post-Incident Follow-Up
- **Root Cause:** Third-party ML service latency caused bottleneck at API Gateway.
- **Preventive Actions:**
  1. Implement permanent circuit breaker + fallback caching for all external API calls.
  2. Negotiate SLA monitoring integration with third-party provider.
  3. Set up alerting thresholds to detect rising latency before it impacts users.
  4.  Explore building in-house ML inference for critical workloads to reduce vendor dependency.
