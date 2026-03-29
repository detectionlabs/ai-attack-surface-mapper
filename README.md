# Argus

> *All-seeing visibility into Shadow AI across your enterprise.*

## Purpose

The rise of AI tooling has introduced a decentralization of corporate data. Employees are connecting sanctioned and unsanctioned AI applications to organizational accounts, installing local inference engines, and routing sensitive data through third-party APIs — often with no visibility from security teams.

**Argus** surfaces these Shadow AI assets by mining existing telemetry sources you already have: DNS logs, OAuth grants, endpoint events, and more. No new agents. No new sensors.

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                  Collection Layer                   │
│  DNS/Proxy · OAuth Grants · Email · Endpoint        │
└──────────────────────┬──────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────┐
│               Processing Pipeline                   │
│  Normalize → Enrich → LLM Classifier (unknown svcs) │
└──────────────────────┬──────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────┐
│                    Registry                         │
│  Alerting (Phase 1) · Posture Dashboard (Phase 2)   │
└─────────────────────────────────────────────────────┘
```

### Collection Layer

KQL queries that extract AI-related signals from existing data sources in Microsoft Sentinel:

| Source | Query | Signals |
|---|---|---|
| DNS | `collection/dns/ai_dns_signals.kql` | AI TLDs, MCP patterns, model download infra, streaming subdomains |
| Endpoint | `collection/endpoint/ai_endpoint_signals.kql` | Known AI processes, package installs, local inference ports |
| Identity | `collection/identity/ai_oauth_signals.kql` | OAuth consent grants, SSO sign-ins, Okta token grants |

### Processing Pipeline

Normalizes signals from disparate sources into a common schema. For services that don't match a known AI vendor, an LLM classifier determines whether the asset is AI-related and categorizes it.

### Registry

- **Phase 1 — Alerting:** Surface high-confidence Shadow AI signals to incident responders for remediation.
- **Phase 2 — Posture Dashboard:** A registry and frontend for tracking the organization's full AI asset inventory and assessing overall Shadow AI posture.

## Requirements

The collection queries target Microsoft Sentinel and assume the following data connectors are enabled:

- **DNS**: `DnsEvents` (DNS Analytics)
- **Endpoint**: `DeviceProcessEvents`, `DeviceNetworkEvents` (Microsoft Defender for Endpoint)
- **Identity**: `AuditLogs`, `SigninLogs` (Azure AD), `OktaSystemLog_CL` (Okta)
