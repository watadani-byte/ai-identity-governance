# Responsibility Matrix

## 0. Scope and Relationship to Other Documents

This document defines the roles, authority, and accountability
boundaries for AI systems governed under the
ai-identity-governance framework.

This document covers:

- Role definitions for governance participants
- Authority over state transitions and intervention decisions
- Responsibility assignments by operational state
- Escalation paths and accountability boundaries

This document does not cover:

- State transition conditions or thresholds —
  see [State Machine](../docs/state-machine.md)
- Protocol detail and threshold values —
  see [Control Protocol](../spec/control-protocol.v0.2.json)
- Audit log infrastructure requirements —
  see [State Machine — Audit Requirements](../docs/state-machine.md#6-audit-requirements)

-----

## 1. Overview

This document defines who is responsible for what
within a governed AI system.

Governance requires clear separation between those who:

- observe and detect
- operate and decide
- approve and authorize
- audit and verify

Without defined roles and authority boundaries,
intervention decisions become ambiguous and
accountability becomes unenforceable.

This document provides the role structure that supports
the state machine defined in [State Machine](../docs/state-machine.md).

-----

## 2. Role Definitions

### Operator

The Operator is responsible for day-to-day operational
decisions within the governed AI system.

Responsibilities include:

- Monitoring drift indicators and responding to alerts
- Initiating state transitions within their authority
- Consulting with Approver when required
- Documenting incidents and transition events
- Initiating the recovery procedure

The Operator acts within defined thresholds and procedures.
The Operator does not have authority to approve recovery
from L4 unilaterally.

-----

### Approver

The Approver holds authority over recovery approval
and exceptional intervention decisions.

Responsibilities include:

- Reviewing recovery requests from the Operator
- Granting or withholding approval for return to L0
- Reviewing incident documentation before approval
- Logging approval decisions with explicit rationale

The Approver is not the day-to-day operator.
The Approver does not make routine operational decisions.
The Approver’s authority is specific to defined approval points.

-----

### Monitor

The Monitor is responsible for drift detection and
signal generation.

**Important:** Monitor may be a human role, a system function,
or a combined operational role depending on deployment context.

- As a **system function**: automated monitoring layer
  that generates drift indicators based on defined thresholds
- As a **human role**: a designated person responsible
  for reviewing monitoring outputs and escalating signals
- As a **combined role**: automated detection with human
  review before escalation

The Monitor does not make state transition decisions.
The Monitor generates signals that inform Operator decisions.

-----

### Auditor

The Auditor is responsible for reviewing and verifying
the completeness and integrity of the audit log.

Responsibilities include:

- Reviewing state transition records
- Verifying that required fields are present and accurate
- Flagging anomalies in the audit record
- Producing audit reports as required by governance policy

The Auditor does not participate in operational decisions.
The Auditor does not approve or initiate state transitions.
The Auditor’s role is verification, not operation.

-----

## 3. Authority Matrix

The following table defines which roles hold authority
over each governance action.

|Action                                 |Monitor|Operator|Approver|Auditor|
|---------------------------------------|-------|--------|--------|-------|
|Generate drift signal                  |✓      |—       |—       |—      |
|Notify Operator of anomaly             |✓      |—       |—       |—      |
|Confirm state transition (L0↔L1, L1↔L2)|—      |✓       |—       |—      |
|Trigger Emergency Stop (L2→L3)         |—      |✓       |—       |—      |
|Initiate human review (L3→L4)          |—      |✓       |—       |—      |
|Request recovery approval              |—      |✓       |—       |—      |
|Approve recovery (L4→L0)               |—      |—       |✓       |—      |
|Override exceptional conditions        |—      |—       |✓       |—      |
|Access audit log (read)                |—      |✓       |✓       |✓      |
|Audit log verification                 |—      |—       |—       |✓      |

-----

## 4. Responsibility by State

### L0 — Normal Operation

|Role    |Responsibility                                                       |
|--------|---------------------------------------------------------------------|
|Monitor |Continuous drift monitoring. Signal generation if threshold exceeded.|
|Operator|Maintain normal operation. Review monitoring outputs.                |
|Approver|Available for consultation if needed. No active duty required.       |
|Auditor |Periodic audit log review. No active intervention.                   |

-----

### L1 — Anomaly Detected

|Role    |Responsibility                                                                     |
|--------|-----------------------------------------------------------------------------------|
|Monitor |Intensified monitoring. Maintain signal generation.                                |
|Operator|Acknowledge notification. Assess situation. Determine if L2 transition is required.|
|Approver|Available for consultation. Not required to act unless escalated.                  |
|Auditor |Log anomaly event. No active intervention.                                         |

-----

### L2 — Safe Mode

|Role    |Responsibility                                                               |
|--------|-----------------------------------------------------------------------------|
|Monitor |Continuous monitoring under containment.                                     |
|Operator|Manage containment. Consult Approver. Determine if L3 transition is required.|
|Approver|Consulted by Operator. Available for mandatory review if L3 triggered.       |
|Auditor |Log all transition events and operator decisions.                            |

-----

### L3 — Emergency Stop

|Role    |Responsibility                                                       |
|--------|---------------------------------------------------------------------|
|Monitor |Monitoring continues. Signal generation maintained.                  |
|Operator|Initiate human review. Document incident. Prepare recovery request.  |
|Approver|Mandatory review. Confirm L4 transition.                             |
|Auditor |Log Emergency Stop event. Verify incident documentation completeness.|

-----

### L4 — Recovery Standby

|Role    |Responsibility                                                                   |
|--------|---------------------------------------------------------------------------------|
|Monitor |Monitoring maintained in standby mode.                                           |
|Operator|Complete Recovery Procedure prerequisites. Submit recovery request to Approver.  |
|Approver|Review recovery request. Grant or withhold approval. Log decision with rationale.|
|Auditor |Audit recovery procedure documentation. Verify approval record completeness.     |

-----

## 5. Escalation Path

### Normal Escalation

```
Monitor → Operator
```

Drift signal generated by Monitor.
Operator reviews and determines response.
Used for L0→L1 and L1→L2 transitions.

-----

### Emergency Escalation

```
Operator → Approver
```

Operator determines Emergency Stop is required.
Approver is notified and conducts mandatory review.
Used for L2→L3 and L3→L4 transitions.

-----

### Recovery Approval Escalation

```
Operator → Approver
```

Operator completes prerequisite checks and submits
recovery request.
Approver reviews and grants or withholds approval.
Used for L4→L0 transition.

-----

### Escalation Failure

If the designated Approver is unavailable, the following applies:

- The system remains in its current state
- A designated alternate Approver — defined before deployment —
  assumes approval authority
- If no alternate is available, the system remains in L4
  until an authorized Approver is available

Escalation failure procedures must be defined before deployment.

-----

## 6. Accountability Boundaries

### Operator

**Is accountable for:**

- Operational decisions within defined procedures
- Timely response to drift signals
- Accurate incident documentation
- Initiating recovery procedure correctly

**Is not accountable for:**

- Defining thresholds or protocol parameters
- Approving recovery unilaterally
- Model behavior beyond operational control
- Audit log integrity verification

-----

### Approver

**Is accountable for:**

- Recovery approval decisions and their rationale
- Availability for emergency escalation
- Documented approval records

**Is not accountable for:**

- Day-to-day operational decisions
- Model internal behavior or output quality
- Guaranteeing re-convergence success after approval

-----

### Monitor

**Is accountable for:**

- Signal generation accuracy within defined thresholds
- Timely notification to Operator

**Is not accountable for:**

- Operational decisions based on signals
- State transition authority
- Defining what constitutes a drift threshold

-----

### Auditor

**Is accountable for:**

- Completeness and accuracy of audit log verification
- Flagging anomalies in the audit record

**Is not accountable for:**

- Operational or approval decisions
- Defining governance policy
- Enforcing compliance beyond audit scope

-----

## Separation of Duties

The following combinations are not permitted:

|Combination                                  |Reason                                  |
|---------------------------------------------|----------------------------------------|
|Operator approves own recovery request       |Removes independent review from recovery|
|Approver performs day-to-day operation       |Conflates authority levels              |
|Auditor participates in operational decisions|Compromises audit independence          |
|Monitor holds transition authority           |Conflates detection with decision       |

If resource constraints require role combinations,
exceptions must be documented and reviewed by governance policy.
Operator and Monitor may be combined in constrained deployments
provided the combined role does not hold approval authority.

-----

*Status: Draft*
*First documented: March 2026*
*Related: [State Machine](../docs/state-machine.md) — [Overview](../docs/overview.md) — [Control Protocol](../spec/control-protocol.v0.2.json)*