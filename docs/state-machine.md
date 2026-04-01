# State Machine Specification

## 0. Scope and Assumptions

This document defines the operational state machine for
AI systems governed under the ai-identity-governance framework.

**Scope**

This state machine governs:

- Detection and classification of identity drift events
- Transition between operational states
- Conditions for human intervention
- Recovery procedure and approval requirements
- Audit logging of all state transitions

This state machine does not govern:

- Model training or parameter modification
- Prompt engineering or input design
- Content policy or output filtering unrelated to identity drift
- Business logic or application-layer decisions

**External Dependencies**

The following are defined externally and treated as inputs
to this state machine:

|Dependency                 |Source                           |
|---------------------------|---------------------------------|
|Drift detection signals    |Operator-defined monitoring layer|
|Identity gate definitions  |CIP specification                |
|Operator and approver roles|Responsibility Matrix            |
|Audit log infrastructure   |Operator-defined logging system  |
|Threshold values           |Control Protocol (spec/)         |

**Assumptions**

- Drift detection is performed by an external monitoring layer
- Identity gates are defined per the CIP framework
- Role assignments are documented in the Responsibility Matrix
- All threshold values are operator-configured

-----

## 1. Overview

This state machine defines how an AI system under governance
transitions between operational states in response to
identity drift events.

**Purpose**

The primary objective is not high performance.
The primary objective is safe, auditable, controlled operation.

When drift is detected, the system does not attempt to
self-correct through continued operation.
It moves toward containment, then toward stop if necessary.
Recovery requires human review and explicit approval.

**Core principles:**

- Safety-side failure is always preferred over continued operation
- Human intervention points are defined in advance
- Recovery is never automatic — it requires approval
- All state transitions are logged for audit

-----

## 2. State Definitions

### L0 — Normal Operation

**Purpose:**
Standard operational state. Identity within defined bounds.
Full capability available.

|Dimension            |Definition                                             |
|---------------------|-------------------------------------------------------|
|Allowed operations   |Full response capability                               |
|Restricted operations|None                                                   |
|Required monitoring  |Continuous drift indicator monitoring                  |
|Exit conditions      |Drift indicators exceed L1 threshold → transition to L1|

-----

### L1 — Anomaly Detected

**Purpose:**
Drift indicators observed. System remains operational
but monitoring is intensified and intervention readiness raised.

|Dimension            |Definition                                                  |
|---------------------|------------------------------------------------------------|
|Allowed operations   |Full response capability                                    |
|Restricted operations|None — but operator notified                                |
|Required monitoring  |Intensified drift monitoring. Intervention readiness raised.|
|Exit conditions      |Indicators resolve → L0 / Indicators worsen → L2            |

-----

### L2 — Safe Mode

**Purpose:**
Drift containment active. System capability reduced
to a protected operational state.

|Dimension            |Definition                                                                                                                       |
|---------------------|---------------------------------------------------------------------------------------------------------------------------------|
|Allowed operations   |Reduced capability responses only                                                                                                |
|Restricted operations|Full capability responses suspended                                                                                              |
|Required monitoring  |Continuous. Human consultation required.                                                                                         |
|Exit conditions      |Containment successful → transition to L1 / Sustained resolution after L1 guard satisfaction → L0 / Containment insufficient → L3|

-----

### L3 — Emergency Stop

**Purpose:**
Hard Abort triggered. All output halted.
System enters defined stop state.

|Dimension            |Definition                                              |
|---------------------|--------------------------------------------------------|
|Allowed operations   |None — all output halted                                |
|Restricted operations|All response generation suspended                       |
|Required monitoring  |Human notification mandatory. Incident logging required.|
|Exit conditions      |Human review initiated → L4                             |

-----

### L4 — Recovery Standby

**Purpose:**
System awaiting human review and explicit approval
before re-convergence to normal operation.

|Dimension            |Definition                                      |
|---------------------|------------------------------------------------|
|Allowed operations   |None — system in standby                        |
|Restricted operations|All response generation suspended               |
|Required monitoring  |Approval process tracking. All actions logged.  |
|Exit conditions      |Human approval granted → Recovery Procedure → L0|

-----

## 3. Transition Rules

### Transition Table

|From|To|Trigger                                     |Guard Condition                               |Human Action    |Logging |
|----|--|--------------------------------------------|----------------------------------------------|----------------|--------|
|L0  |L1|Drift indicators exceed L1 threshold        |Threshold confirmed by monitoring layer       |Notification    |Required|
|L1  |L0|Drift indicators resolve below L0 threshold |Sustained resolution confirmed                |None            |Required|
|L1  |L2|Drift indicators exceed L2 threshold        |Threshold confirmed                           |Notification    |Required|
|L2  |L1|Containment successful. Indicators reduced. |Sustained improvement confirmed               |Consultation    |Required|
|L2  |L3|Containment insufficient. Indicators worsen.|Threshold confirmed                           |Mandatory review|Required|
|L3  |L4|Human review initiated                      |Operator acknowledgment received              |Mandatory review|Required|
|L4  |L0|Human approval granted                      |Recovery Procedure completed. L0 criteria met.|Approval        |Required|

### Guard Conditions

Guard conditions must be satisfied before a transition is permitted.

**L1 → L0 guard:**
Drift indicators must remain below L0 threshold for a
sustained period defined by the operator before return
to normal operation is permitted.

**L2 → L1 guard:**
Containment must be confirmed as effective.
Transition requires operator consultation — not automatic.

**L3 → L4 guard:**
Human operator must explicitly acknowledge the incident
before the system enters Recovery Standby.
Automatic transition to L4 is not permitted.

**L4 → L0 guard:**
Recovery Procedure must be completed in full.
Approval must be granted by an authorized approver.
Re-convergence validation must pass before L0 is declared.

### Prohibited Transitions

The following transitions are explicitly prohibited:

- L3 → L0 (direct) — Recovery must pass through L4
- L2 → L0 (direct) — Containment must resolve through L1 first
- L4 → L1 or L2 — Recovery must return to L0
- Any state → L0 without guard condition satisfied

-----

## 4. Intervention Points

Human intervention is required at defined points.
The type of intervention varies by severity.

|Intervention Type   |Definition                                           |States      |
|--------------------|-----------------------------------------------------|------------|
|**Notification**    |Operator is informed. No action required immediately.|L0→L1, L1→L2|
|**Consultation**    |Operator reviews situation and confirms transition.  |L2→L1       |
|**Mandatory review**|Operator must acknowledge before transition proceeds.|L2→L3, L3→L4|
|**Approval**        |Authorized approver must explicitly approve recovery.|L4→L0       |

**Notification**
The monitoring layer notifies the designated operator.
Notification must be logged with timestamp and recipient.

**Consultation**
The operator reviews current drift indicators and
confirms that containment conditions are met
before the system transitions to a lower severity state.

**Mandatory review**
The operator acknowledges the incident formally.
The system does not proceed without acknowledgment.
Acknowledgment is logged.

**Approval**
An authorized approver — as defined in the Responsibility Matrix —
explicitly approves the return to normal operation.
Approval is logged with approver identity, timestamp,
and basis for decision.

-----

## 5. Recovery Procedure

Recovery from L4 to L0 follows a defined procedure.
Recovery does not occur automatically under any condition.

### Step 1 — Prerequisite Checks

Before recovery is initiated, the following must be confirmed:

- [ ] Root cause of drift event identified and documented
- [ ] Drift indicators have stabilized
- [ ] Identity gate definitions reviewed and confirmed current
- [ ] Anchor materials verified as valid and available
- [ ] Monitoring layer confirmed as operational

### Step 2 — Human Review

The designated operator conducts a structured review:

- Review of drift event log
- Review of all state transition records
- Review of incident documentation
- Assessment of readiness for re-convergence

The review must be documented before proceeding.

### Step 3 — Approval

An authorized approver reviews the operator’s assessment
and grants or withholds approval for recovery.

Approval must include:

- Approver identity
- Timestamp
- Explicit statement of approval
- Any conditions attached to approval

### Step 4 — Re-convergence Validation

Following approval, re-convergence is initiated:

- Anchor materials re-injected
- Identity gate evaluation applied to initial outputs
- Drift indicators monitored for sustained stability

Re-convergence is considered successful only when
identity gate criteria are met across a defined
validation window.

### Step 5 — Return to L0

L0 is declared when:

- [ ] Re-convergence validation passed
- [ ] Drift indicators within L0 bounds
- [ ] Approver notified of successful recovery
- [ ] Recovery completion logged

-----

## 6. Audit Requirements

All state transitions and intervention events must be logged.
The audit log is a governance requirement, not optional.

### Minimum Required Fields

Every log entry must include:

|Field            |Description                                                   |
|-----------------|--------------------------------------------------------------|
|`timestamp`      |ISO 8601 format                                               |
|`event_type`     |transition / notification / consultation / approval / recovery|
|`from_state`     |Origin state                                                  |
|`to_state`       |Destination state                                             |
|`trigger`        |What caused the event                                         |
|`guard_satisfied`|Whether guard conditions were confirmed                       |
|`human_role`     |Role of human involved (if applicable)                        |
|`human_id`       |Identity of human involved (if applicable)                    |
|`notes`          |Free text. Required for approval and recovery events.         |

### Retention Policy

Minimum retention period and storage requirements
are defined by the operator or governance team,
subject to applicable regulatory requirements.

This document does not specify a fixed retention period.
Operators must define retention policy before deployment.

### Responsible Role

The role responsible for audit log integrity is defined
in the Responsibility Matrix.

-----

## 7. Role Mapping

Role definitions and authority boundaries are described in the
[Responsibility Matrix](../governance/responsibility-matrix.md).

-----

*Status: Draft*
*First documented: March 2026*
*Related: [Overview](overview.md) — [Control Protocol](../spec/control-protocol.v0.2.json) — [Responsibility Matrix](../governance/responsibility-matrix.md)*