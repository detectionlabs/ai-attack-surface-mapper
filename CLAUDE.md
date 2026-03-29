cat > /mnt/c/Users/josem/Projects/ai-attack-surface-mapper/CLAUDE.md << 'EOF'
# Argus — AI Attack Surface Mapper

All-seeing visibility into Shadow AI across the enterprise.
SIEM-native, no new agents, no new sensors.

## Tagline
"The rise of AI tooling has introduced a decentralization of corporate data."

## Architecture — Three Layers

### 1. Collection Layer (KQL queries in Sentinel)
| Source | Query File | Signals |
|--------|-----------|---------|
| DNS | collection/dns/ai_dns_signals.kql | AI TLDs, MCP patterns, model download infra, streaming subdomains |
| Endpoint | collection/endpoint/ai_endpoint_signals.kql | Known AI processes, package installs, local inference ports |
| Identity | collection/identity/ai_oauth_signals.kql | OAuth consent grants, SSO sign-ins, Okta token grants |

### 2. Processing Pipeline
- Normalize → Enrich → LLM Classifier (for unknown services)
- LLM classifier determines if unmatched asset is AI-related and categorizes it
- This is the novel contribution — pattern matching + LLM classification

### 3. Registry
- Phase 1: Alerting — high-confidence Shadow AI signals to incident responders
- Phase 2: Posture Dashboard — full AI asset inventory and Shadow AI posture scoring

## Sentinel Data Connector Requirements
- DNS: DnsEvents (DNS Analytics)
- Endpoint: DeviceProcessEvents, DeviceNetworkEvents (MDE)
- Identity: AuditLogs, SigninLogs (Azure AD), OktaSystemLog_CL (Okta)

## Repo Structure
/collection
  /dns/ai_dns_signals.kql
  /endpoint/ai_endpoint_signals.kql
  /identity/ai_oauth_signals.kql
/processing
  /normalize
  /enrich
  /classifier
/registry
  /alerting
  /dashboard
/data
  ai-services.json
  mcp-signatures.json
  shadow-ai-indicators.json
/docs

## Known Coverage Gaps
- DNS over HTTPS defeats DNS-based discovery
- API key auth invisible to OAuth signals
- Self-hosted local AI models generate no telemetry
- MCP detection limited to presence not behavior

## DEF CON 34 CFP
Deadline: May 1, 2026
Novel contributions:
- MCP server detection via KQL (no published tooling exists)
- LLM classifier for unknown AI service categorization
- Agentless architecture using existing Sentinel telemetry

## Immediate Tasks
1. Populate /collection KQL queries
2. Build ai-services.json classification data
3. Design LLM classifier prompt and schema
4. Write DEF CON CFP abstracty
