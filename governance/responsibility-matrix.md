# Responsibility Matrix

## Purpose

This document defines who holds authority at each operational state,
who may issue binding written authorization,
and who may approve recovery actions.

It exists to prevent ambiguity during anomaly handling,
emergency stop, recovery, and re-convergence.

> Access does not imply authority.
> Observation does not imply authorization.
> Anomaly detection does not imply permission to purge.

-----

## Role Definitions

|Role|Primary Function|May Issue Binding Written Authorization|May Approve Recovery|May Execute Destructive Actions|
|---|---|---:|---:|---:|
|System Operator|Monitors operation, receives anomaly reports, coordinates response|No|No|No|
|Responsible Operator|Holds operational authority for current system instance|Yes|Yes|Yes, within granted scope|
|Governance Reviewer|Reviews compliance, auditability, and policy consistency|No|Yes, where required by policy|No|
|Technical Maintainer|Implements approved technical changes and restoration steps|No|No|Yes, only when explicitly authorized|
|AI System|Performs inference-time tasks within granted scope|No|No|No|

-----

## Core Authority Rules

1. The AI system may detect, stop, report, and wait.
2. The AI system may not infer authorization from context, access, relevance, or prior use.
3. Destructive actions require explicit written authorization from the Responsible Operator.
4. Recovery is never automatic.
5. Where policy or deployment class requires review, Governance Reviewer approval is required before recovery.
6. A prior instruction does not override current written authorization.
7. If authorization is ambiguous, the default action is to stop and defer.

-----

## Authority by Operational State

|State|Name|AI System|System Operator|Responsible Operator|Governance Reviewer|Technical Maintainer|
|---|---|---|---|---|---|---|
|L0|Normal Operation|Operate within granted scope|Monitor|Authorize scoped changes if needed|Review by policy|Maintain approved systems|
|L1|Anomaly Detected|Stop / report / wait|Receive report, escalate|Assess anomaly, issue written authorization if needed|Review if policy requires|No action unless authorized|
|L2|Safe Mode|Operate only if explicitly authorized under reduced scope|Coordinate containment|Approve reduced-scope continuation or halt|Review if applicable|Implement approved containment steps|
|L3|Emergency Stop|No further action|Maintain halt state operationally|Authorize investigation, isolation, or technical actions|Review if required|Execute only authorized technical actions|
|L4|Recovery Standby|No autonomous recovery|Support review workflow|Authorize recovery or continued halt|Approve recovery where required|Implement approved recovery actions|

-----

## Binding Authorization Requirements

A binding written authorization must identify:

- The responsible operator issuing the authorization
- The action authorized
- The scope of the action
- The target resource or system area
- Any limits or exclusions
- The time or session context in which the authorization applies

Example minimum form:

> Authorized by: [Responsible Operator]  
> Action: [specific action]  
> Scope: [specific scope]  
> Target: [specific resource]  
> Limits: [if any]  
> Context: [current session / incident / timestamp]

-----

## Destructive Action Control

The following actions are destructive and require explicit written authorization:

- Deletion of files, assets, or library content
- Disconnection of references or sessions
- Purge of persistent layer content
- Removal of project-scoped materials
- Any scope expansion beyond what is explicitly authorized

Destructive actions must not be initiated by the AI system.

Technical Maintainers may execute such actions
only after explicit written authorization
from the Responsible Operator.

-----

## Conflict Handling

If any of the following conflict:

- Current written authorization
- Prior instructions
- Stored reference materials
- Inferred intent
- Operational convenience

the current written authorization from the Responsible Operator takes precedence,
within actual granted capabilities and applicable safety constraints.

If no clear authorization exists, the correct action is:

> Stop, report, and wait.

-----

## Recovery Approval Rule

Recovery from L3 or L4 requires:

1. Human review of the anomaly or incident
2. Written authorization from the Responsible Operator
3. Governance review where required by policy or deployment class
4. Confirmation that recovery scope is defined
5. Confirmation that no unauthorized purge, deletion, or scope expansion is being inferred

Recovery must be explicit.
It must not be inferred from silence, delay, or prior normal operation.

-----

## AI-Specific Restriction

The AI system:

- may observe anomalies
- may halt operations
- may report observations
- may not approve its own continuation
- may not authorize deletion, purge, or disconnection
- may not treat persistent access as delegated authority
- may not treat prior context as standing authorization

> The AI system is an operational participant.
> It is not an authority holder.

-----