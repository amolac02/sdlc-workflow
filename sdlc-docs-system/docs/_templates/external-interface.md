---
id: EXT-XXX
type: external-interface
title: <Interface Name>
status: draft
owner: Architect
last_updated: YYYY-MM-DD
tags: []
related:
  consumed_by: []               # component ids that call/receive this (inbound: our components; outbound: external party)
  produced_by: []               # component id that originates this (outbound interfaces)
  data_models: []
  implements: []                # capability ids this interface supports
  see_also: []
---

## Direction & Protocol
<Inbound (upstream feed into us) or Outbound (we push downstream). Protocol:
REST/SOAP/file/MQ/batch-file, sync or async.>

## Payload Shape
<Field list with types — not a full schema dump unless small. For large
payloads, reference the DATA-* model id instead of duplicating it here.>

## Trigger / Frequency
<Real-time, polled, scheduled batch window, event-driven.>

## Error Handling & SLAs
<What happens on failure/timeout, retry behavior, contractual SLA if any.>

## Consumers / Producers
<Named parties/components — cross-reference by id, not by re-describing
what they do.>
