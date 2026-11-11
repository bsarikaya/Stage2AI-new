---
title: "AI Architecture in 6G Mobile Network"
abbrev: "TODO - Abbreviation"
category: info

docname: draft-sascch-6gai-agent-arch-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
# area: AREA
# workgroup: WG Working Group
keyword:
 - next generation
 - unicorn
 - AI-native
venue:
#  group: WG
#  type: Working Group
#  mail: WG@example.com
#  arch: https://example.com/WG
  github: "bsarikaya/Stage2AI-new"
  latest: "https://bsarikaya.github.io/Stage2AI-new/draft-sascch-6gai-agent-arch.html"

author:
- ins: B. Sarikaya
  org: Unaffiliated
  name: Behcet Sarikaya
  city: Dallas, TX
  code: 75252
  country: USA
  email: sarikaya@ieee.org
- ins: R. Schott
  name: Roland Schott
  org: Deutsche Telekom
  abbrev: Deutsche Telekom
  street: Deutsche-Telekom-Allee 9
  city: Darmstadt
  code: 64295
  country: Germany
  email: Roland.Schott@telekom.de
- ins: V. Chintha
  name: Venkat Chintha
  city: Dallas
  code:
  country: USA
  email:

normative:

informative:
  TR23.801-01:
    title: "Study on Architecture for 6G System; Stage 2 (Release 20)"
    author:
      org: 3GPP
    seriesinfo:
      3GPP TR: 23.801-01 V0.10.0
    date: 2026-10
    target: https://www.3gpp.org/ftp/Specs/archive/23_series/23.801-01/
  TR33.801-01:
    title: "Study on Security for the 6G System (Release 20)"
    author:
      org: 3GPP
    seriesinfo:
      3GPP TR: 33.801-01 V0.7.0
    date: 2026-10
    target: https://www.3gpp.org/ftp/Specs/archive/33_series/33.801-01/
  TR32.801-01:
    title: "Study on 6G Management and Orchestration (Release 20)"
    author:
      org: 3GPP
    seriesinfo:
      3GPP TR: 32.801-01 V0.5.0
    date: 2026-10
    target: https://www.3gpp.org/ftp/Specs/archive/32_series/32.801-01/
  TS33.501:
    title: "Security architecture and procedures for 5G system (Release 20)"
    author:
      org: 3GPP
    seriesinfo:
      3GPP TS: 33.501 V20.3.0
    date: 2026-09
    target: https://www.3gpp.org/ftp/Specs/archive/33_series/33.501/
  TS28.312:
    title: "Management and orchestration; Intent driven management services for mobile networks (Release 20)"
    author:
      org: 3GPP
    seriesinfo:
      3GPP TS: 28.312 V20.1.0
    date: 2026-09
    target: https://www.3gpp.org/ftp/Specs/archive/28_series/28.312/
  TS28.628:
    title: "Telecommunication management; Self-Organizing Networks (SON) Policy Network Resource Model (NRM) Integration Reference Point (IRP); Information Service (IS) (Release 19)"
    author:
      org: 3GPP
    seriesinfo:
      3GPP TS: 28.628 V19.0.0
    date: 2025-09
    target: https://www.3gpp.org/ftp/Specs/archive/28_series/28.628/
  RFC6241:
  RFC6470:
  RFC8040:
  RFC8341:
  RFC8342:
  RFC8693:
  RFC9144:
  RFC9396:
  RFC9943:
  I-D.ietf-wimse-aims:
  I-D.ietf-oauth-transaction-tokens:
  I-D.ietf-netconf-trace-ctx-extension:
  I-D.ietf-opsawg-yang-provenance:
  I-D.irtf-nmrg-network-digital-twin-arch:
  I-D.mcw-opsawg-icon-requirements:
  I-D.kuehlewind-audit-architecture:
  I-D.cui-dns-native-agent-naming-resolution:

...

--- abstract

TODO Abstract


--- middle

# Introduction {#intro}

TODO Introduction

Intents are what is new to 3GPP in 6G. Intent Serving Function (ISF) is defined to handle intents. We explain the intent processing in 6G.
# Conventions and Definitions

{::boilerplate bcp14-tagged}

# NW-Agent and Intent Serving Function

Intents are declative service requests coming UE or other Application Functions (AF). Execution of intents takes several steps.

# Operating AI Agents Safely {#safe-operation}

The solution variants (SVs) for Key Issue (KI) #18 in {{TR23.801-01}} let an AI agent turn an intent into actions on network functions (NFs). This section looks at what an operator needs before allowing that on a live network: a bound on what the agent may change, a check before a change is applied, a way to undo a change and to stop the agent, and a record of who changed what. For each of the four, it describes where 3GPP stands, what is still open, and which IETF work can be reused.

## Status in 3GPP and Approach {#safe-status}

At the time of writing, the only interim agreement on operator control under KI#18 is clause 7.1.18.1 of {{TR23.801-01}}. It states that the 6G core network supports authorising or controlling the sending of intent requests per requested service or per User Equipment (UE) or Application Function (AF), and supports activating and deactivating AI capabilities per NF. Whether and how the operator controls AI observability and traceability is for further study, and the details of control through operations, administration and maintenance (OAM) are left to 3GPP SA WG5. Everything else cited below from clause 6.18 is a proposal in an SV, not an agreement. Agreed text for KI#19 (clauses 7.1.19.1 and 7.1.19.2) is cited where it is relevant and is marked as KI#19.

The choice between NW-Agent functions and an ISF (clause 6.18.1), intent handling embedded in existing NFs, with or without AI (clause 6.18.2), and a separate AI domain (clause 6.18.3) is also open. This section therefore uses three role names that do not depend on that choice:

agent:
: The entity that plans and invokes actions to fulfil an intent, for example the NW-Agent functions or the intent fulfilment logic of the ISF. The term names a role. An NW-Agent function can play it, but the two terms are not the same.

enforcement point:
: An entity under operator control that decides whether an action proposed by an agent is applied. Examples are the intermediary that SV#18.1 places under operator control in the NW-Agent option, the gateway function of the ISF, and the target itself.

target:
: The NF, or the management service of an NF, on which an action is performed.

Actions reach a target on two paths: at run time, as service operations over the service-based interface or as tool invocations over a tool-based interface, and through the management plane, as configuration changes made through OAM. The requirements below apply to both. Several of the IETF mechanisms cited come from NETCONF and RESTCONF. They are cited to show that a property is already specified for network management, not to suggest that 3GPP NFs are configured with these protocols. Whether and how any of them is used with 3GPP interfaces is for the groups concerned to decide.

None of these requirements is specific to AI. They apply to any automation that changes a network. They become necessary with agents because the actions are selected at run time instead of being written and reviewed in advance, and because an agent can act faster than a person can review. Where {{I-D.mcw-opsawg-icon-requirements}} or {{I-D.ietf-wimse-aims}} already states a requirement, it is repeated here only to relate it to the 3GPP entities and to the 3GPP status. The analysis of the gap in 3GPP is what this section adds.

The requirements are numbered for discussion. The key words in them describe what an operator needs from a solution, whether it is built from 3GPP procedures, IETF protocols or both. They do not place conformance obligations on implementations of this document.

## Scope of Authority {#safe-authority}

In the 5G service-based architecture, access to NF services is authorised with OAuth 2.0 access tokens issued by the Network Repository Function (NRF) (clause 13.4.1 of {{TS33.501}}). A token names the consumer NF instance, the producer NF type or producer NF instances, the services, optionally the operations and resources, optionally a list of slices or NF sets, and an expiry time. The claims defined in that clause do not include the task or intent being served, the UEs or parameter values affected, or the size of a change. Annex X of {{TS33.501}} adds a claim that names the source NF when an intermediary NF acts on its behalf, but only for the network automation services of that annex, such as access to data, analytics and AI/ML models. Token revocation is not supported.

For agents, SV#18.22 proposes a boundary of allowed NFs, NF services, slices, UEs and services that OAM configures statically (clause 6.18.22.1.1.1 of {{TR23.801-01}}), and autonomy levels that can be raised or lowered (clause 6.18.22.0). Neither the boundary nor the autonomy levels are agreed, and the SV itself notes that configuration through OAM cannot guarantee control in real time. The agreed UE AI agent identifier (KI#19, clause 7.1.19.1) applies to operator-controlled agents on UEs. No agreed text defines an identifier or a credential for an agent in the network. Some SVs propose that agents register with an agent identifier (for example clause 6.18.17.0), but they do not say how it is authenticated or tied to authority. {{TR33.801-01}} has no key issue dedicated to KI#18. Its key issue on agentic exposure (clause 5.6.3.3) covers the exposure aspects of KI#18 and KI#19, and in its clause on UE AI agent security (clause 5.8.1) the aspects related to KI#18 are marked for further study.

The agreed controls decide who may send an intent and whether an NF may use AI. They do not state what a particular agent may change while it fulfils a particular intent.

AUTH-1:
: Authority MUST be given to an agent as an explicit grant issued by a function under operator control. An agent MUST NOT be able to issue or widen its own grant.

AUTH-2:
: A grant MUST identify the agent and the intent or task it serves, and MUST list the permitted operations and targets, for example NF instances, slices and UE groups. It SHOULD be able to carry limits on the size and rate of change. Anything that is not listed is not permitted.

AUTH-3:
: A grant MUST expire. Its lifetime SHOULD follow the task, not a general token lifetime set for the whole network.

AUTH-4:
: The operator MUST be able to revoke a grant before it expires. Operator policy sets an upper bound on the time until a revocation takes effect at the enforcement point, and the operator MUST be able to observe that the bound is met.

AUTH-5:
: When an agent delegates part of a task to another agent, the derived grant MUST NOT exceed the original in any respect, and the chain of delegation MUST be recorded.

AUTH-6:
: Grants MUST be checked at an enforcement point that the agent cannot modify or bypass. A request outside the grant MUST be refused, and the refusal MUST be reported to the operator.

Several related IETF specifications exist. {{I-D.ietf-wimse-aims}} gives each agent a unique identifier, uses OAuth for delegation, states that the scope of a token obtained by exchange never exceeds that of the token presented, and relies on short-lived credentials and on revocation and risk signals. Transaction tokens {{I-D.ietf-oauth-transaction-tokens}} bind authorisation context to a single transaction within a trust domain. Rich Authorization Requests {{RFC9396}} can carry typed, structured permissions; a type that expresses slices, NF instances, UE groups and numeric limits would have to be defined, and the authors are not aware of one. Token exchange {{RFC8693}} records the acting party and earlier actors in the "act" claim. For configuration data, the NETCONF Access Control Model (NACM) {{RFC8341}} shows enforcement at the managed server down to individual data nodes, but its rules are per user or group and have no notion of task, lifetime or delegation. A grant also needs a stable identifier for the agent. DNS-based agent naming such as {{I-D.cui-dns-native-agent-naming-resolution}} is one way to provide a name that stays the same across versions of an agent. {{I-D.mcw-opsawg-icon-requirements}} has requirements on restricting the scope of a network management agent to given domains, devices, protocols and tools, and on raising and lowering its level of authorisation. It does not require a grant that is tied to a task, that expires, or that cannot be widened on delegation.

## Pre-Action Verification {#safe-verification}

SV#18.22 proposes that operator control may evaluate selected AI-derived candidate actions, namely those that change network state or service behaviour that is visible from outside, before they are enforced, where operator policy so provides. The evaluation has five possible dispositions: it can allow the action, allow it within a narrower scope, ask for more explanation or context, redirect it to a defined or default procedure, or deny it (clause 6.18.22.0 of {{TR23.801-01}}). A note adds that the evaluation of an explanation does not authorise an action that is prohibited on other grounds, such as operator policy or security requirements. SV#18.1 describes an intermediary under operator control that verifies intents, plans and candidate actions before enforcement (clause 6.18.1.1.1.1). SV#18.6 has an optional step in which, if digital twin simulation is supported and enabled by operator policy, the executing function may invoke the digital twin for validation before the live invocation (clause 6.18.6.2.5); the text does not say what is validated or how the result is used. None of this is agreed. In the ISF option, the gateway function is listed both with translating invocation requests into service requests and with authorising, auditing and verifying intent actions; whether it is deployed separately is for further study (clause 6.18.1.1.1.2). The intermediary of SV#18.1 may likewise be co-located with the NW-Agent function. If the gateway function or the intermediary is also part of the agent, it cannot satisfy VER-1 by itself.

Two precedents exist on the management side. In coordination of self-organising network (SON) functions (clause 4.7.4.2 of {{TS28.628}}), a SON function can request permission from a coordination function before it changes certain parameters. The coordination function typically bases its answer on inputs such as the current and the proposed values and the likely impact on other objects and on performance indicators. Intent-driven management (clause 5.3.3 of {{TS28.312}}) has a feasibility check of an intent before fulfilment, which can use network digital twin functions to simulate the resulting management tasks. It does not define a check that is applied to each configuration change made while the intent is fulfilled.

The proposed evaluation is defined mainly by its results. The input it may consider is listed only at a high level (clause 6.18.22.1.1.0 of {{TR23.801-01}}): the action and its category, the affected scope and expected impact, explainability-related information, confidence, operator policy and validity information. The details are for further study. The list does not include whether the state the agent acted on is still current, whether the action can be undone, or what the state was before.

VER-1:
: The check MUST be performed by an entity other than the agent that proposes the action. Its result MUST NOT rest only on information supplied by that agent.

VER-2:
: A request to change a target MUST identify the version of the target's state on which the agent based its decision. The change MUST be refused if the part of that state on which the decision relied has changed since.

VER-3:
: The check MUST detect other changes that are pending or in progress on the same target, whether they come from another agent, an automated control loop or a person.

VER-4:
: Each action MUST be marked as reversible or not reversible. An action that cannot be undone MUST require a stricter level of approval, set by operator policy.

VER-5:
: Before a change is applied, the state of each target MUST be recorded.

VER-6:
: A request MUST state its expected effect as one or more observable measurements with thresholds.

VER-7:
: The check MUST return one of a closed, documented set of results and MUST record the information on which the result was based. A request that fails VER-2 MUST be refused.

The recorded state (VER-5) and the expected effect (VER-6) are used later for rollback and for attribution. The five dispositions proposed in SV#18.22 are one candidate for the set of results in VER-7.

The IETF and the IRTF have parts of this for configuration management. RESTCONF ({{Section 3.4.1.2 of RFC8040}}) lets a client send the entity-tag it received when it read configuration data in an "If-Match" header, and the server rejects the edit if the tag no longer matches. The entity-tag reflects configuration data only, not operational state. The NETCONF validate capability ({{Section 8.6 of RFC6241}}) checks a proposed configuration at the server without applying it. The Network Management Datastore Architecture (NMDA) {{RFC8342}} separates intended from operational state. Datastore comparison {{RFC9144}} reports the differences between two datastores, which can show whether a change has taken effect; it does not record the state before a change. {{I-D.irtf-nmrg-network-digital-twin-arch}} describes, as a use case, verifying the impact of a change in a network digital twin before it is applied to the production network. None of these defines the check as a whole, and validating a configuration against its data model is not a check of its effect on the network.

## Rollback and Containment {#safe-rollback}

SV#18.22 proposes, for some of the architecture options, that the network can always switch to default procedures that do not use AI, in a forced or a graceful way, that default handling may include a return to a previously known safe state, and that an intermediary can stop forwarding tool invocations from agents (clauses 6.18.22.0 and 6.18.22.1.1.2.3 of {{TR23.801-01}}). Whether both forced and graceful switching are needed, and how service continuity is kept during the switch, are open. SV#18.15 has a participating NF stop or revert an exploratory action of reinforcement learning when a safeguard in the exploration policy is breached (clause 6.18.15.2.1.2). For KI#18, the agreed text provides only activation and deactivation of AI capabilities per NF (clause 7.1.18.1). There is no agreed text on stopping an agent or on undoing its changes. For KI#19, the network and the consumer can terminate an AI training or inference service (clause 7.1.19.2), which is termination of a service and not the reversal of actions on NFs.

Switching AI off for future requests is different from undoing what an agent has already done. A plan that fulfils one intent can change several NFs. SV#18.22 mentions a return to the last known configuration of an NF, but if a plan fails or is stopped halfway, the SVs do not say how the completed steps of a plan that spans several NFs are identified and reversed.

RBK-1:
: A plan that changes more than one target MUST have one identifier, carried in every change made under it, so that the changes can be found and reversed as a unit.

RBK-2:
: Reverting a plan, in whole or from a given step, MUST be possible without the cooperation of the agent that created it. The order of reversal MUST be defined.

RBK-3:
: A change SHOULD be provisional until it is confirmed. A provisional change MUST be reverted without waiting for the agent if the expected effect (VER-6) is not observed, or a health condition defined by the operator is breached, within a time defined by the operator. Actions marked as not reversible (VER-4) are outside this requirement.

RBK-4:
: The operator MUST be able to suspend an agent and revoke its grants (AUTH-4) on a path that does not pass through the agent. The response MUST distinguish a stop that has been accepted from one that has taken effect.

RBK-5:
: Operator policy MUST be able to limit, per plan and per period of time, how many targets, slices and UEs a plan may affect.

RBK-6:
: The specification of each kind of action MUST state what happens to established sessions when the action is reverted or the agent is stopped.

NETCONF confirmed commit ({{Section 8.4 of RFC6241}}) is the closest existing model. A change is reverted automatically unless it is confirmed within a timeout, and with a persist identifier a session other than the one that made the change can confirm or cancel it. Confirmed commit applies to one device only. Appendix E.2 of {{RFC6241}}, which is not normative, describes how a manager can coordinate changes on several devices by locking, validating, taking checkpoints and restoring them on failure. The authors are not aware of an IETF standard for a confirmed commit across several devices, so the entity that coordinates the plan has to provide RBK-1 to RBK-3. {{I-D.mcw-opsawg-icon-requirements}} has requirements on interrupting an agent, including a stop that does not depend on the agent and the distinction between accepted and applied, and on rollback. It does not define an identifier, a protocol or a mapping to 3GPP entities.

## Attribution {#safe-attribution}

SV#18.6 proposes a correlation identifier for each intent being fulfilled. It is associated with the fulfilment plan and may be used by the participating entities to tie their actions and events together. The actions, the entities involved, the configuration values and the outcomes are stored as traceability information (clause 6.18.6.1.2.3 of {{TR23.801-01}}). The Intent Correlation ID of clause 6.18.7 identifies a set of related intents of one requester, and whether it is needed is for further study. The TR uses similar names for the two identifiers, so the relation between them is not settled either. In SV#18.22, entities record their own reasoning and decision history. In SV#18.1, the access control intermediary produces audit logs for monitoring at request level and sends them to an Audit Function that clause 6.18.1 does not define. SV#18.19 proposes monitoring in which a monitoring function may also check whether execution matched the request; whether this applies is for further study. In the management study, a potential solution for the observability of management agents (clause 9.3.1.4.2.1 of {{TR32.801-01}}) has the management agent report its own operation log, including who invoked the operation, to a consumer that subscribed to it. As noted in {{safe-status}}, operator control of observability and traceability is not yet agreed.

Several of these proposals have the entity that acted record its own history. Others store or collect records in a separate registry or monitoring function. None requires that the records are kept independently of the acting entity, that they are protected against alteration or loss, or that they carry a time reference with a stated accuracy. Agent identifiers exist for registration and discovery. SV#18.6 lists the agent involved among its traceability information, and SV#18.22 says that traceability information may correlate an action with the decision of operator control and with the result. No proposal states that the record identifies the grant of the requesting agent, or the chain of delegation under which the change was made.

ATT-1:
: Each change made for an agent MUST carry one or more identifiers that together allow the agent, its grant, the intent and the plan to be retrieved.

ATT-2:
: The enforcement point MUST produce its own record of each change. The agent's record can explain why a change was made. It MUST NOT be the only evidence of what was changed.

ATT-3:
: The record MUST show the requester of the intent and every agent in the chain of delegation, not only the identity used on the last hop.

ATT-4:
: A party other than the producer of a record MUST be able to detect that the record has been altered, removed or cut short.

ATT-5:
: Records MUST carry a time from a known source with a stated accuracy, so that a change can be related to performance measurements taken before and after it.

ATT-6:
: Identifiers used for correlation SHOULD NOT contain subscriber identifiers.

ATT-7:
: The operator MUST be able to set the retention period of records and the rules for access to them.

NETCONF configuration change notifications {{RFC6470}} are produced by the server. They identify either the server itself or the user name, session and source host of the management session that made a change, but they have no field for the party on whose behalf it was made. Trace context propagation for NETCONF {{I-D.ietf-netconf-trace-ctx-extension}} lets a client attach a W3C trace context to a request, and lets the server add a trace context to the reply and to notifications. It correlates records; it does not authenticate the party that set the value. YANG data provenance {{I-D.ietf-opsawg-yang-provenance}} lets the producer of YANG data sign it, and later parties can add countersignatures. The signatures cover the origin and integrity of the data; nothing in them says who asked for the change. The SCITT architecture {{RFC9943}} provides append-only logs of signed statements with receipts, which could serve ATT-4. The architecture in {{I-D.kuehlewind-audit-architecture}}, written for the proposed AUDIT work in the IETF, has the services record what they observe at their own boundary. It states that an agent that records itself cannot by itself deliver non-repudiation to a third party, which is consistent with ATT-2. {{I-D.ietf-wimse-aims}} lists the minimum content of an audit event for authorisation decisions and requires audit records to be tamper-evident. {{I-D.mcw-opsawg-icon-requirements}} has requirements on accountability, tamper-evident telemetry and an authoritative clock source.

## Summary {#safe-summary}

{{safe-summary-table}} summarises the status of the four topics. This document does not ask 3GPP to adopt any of the requirements. They are offered as input for discussion.

| Topic | Agreed in 3GPP | Proposed, not agreed | Open |
|---|---|---|---|
| Scope of authority | Intent sending per service or per UE/AF; AI on or off per NF | Static boundary and autonomy levels (SV#18.22) | Grant per agent and task; revocation; delegation |
| Pre-action verification | Nothing | Evaluation with five dispositions (SV#18.22); intermediary under operator control (SV#18.1); digital twin step (SV#18.6) | State on which the check relies; independence from the agent |
| Rollback and containment | AI on or off per NF; nothing on rollback or on stopping an agent | Switch to default procedures and stop forwarding (SV#18.22); revert of exploratory actions (SV#18.15) | Rollback of a plan that spans several NFs; revert triggers; sessions |
| Attribution | Nothing; control of observability and traceability is for further study | Correlation identifier and traceability information (SV#18.6); local decision records (SV#18.22); audit and monitoring functions (SV#18.1, SV#18.19) | Independent, protected records; agent identity and grant carried in the record of a change |
{: #safe-summary-table title="Status of the four topics for KI#18 in TR 23.801-01 V0.10.0"}

# Security Considerations {#security}

TODO Security


# IANA Considerations {#iana}

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
