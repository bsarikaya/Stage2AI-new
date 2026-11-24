# Outline: Operating AI Agents Safely in the 6G System

Venkat Chintha · outline for co-author discussion · baseline TR 23.801-01 V0.10.0 (incl. SA2#177 agreements), plus SA2#178 results (Nov 2026) cited by Tdoc; architecture-neutral (SV#18.1/18.2/18.3). Details in the appendix.

## 1. Proposed skeleton (for Behcet and Roland to decide)
Venkat attention: 
https://datatracker.ietf.org/meeting/126/materials/slides-126-dmsc-6g-progress-on-multi-agent-collaboration-01
has good slides

| § | Section | Content | Owner |
|---|---|---|---|
| 1 | Introduction | Link to Stage 1 (-01); scope; "Stage 2" at the IETF | Behcet |
| 2 | Terminology | AI agent (TR 22.870), intent (KI#18 NOTE 3) | Shared |
| 3 | AI in TR 23.801-01 | KI#18, KI#19, plus KI#7, KI#21, KI#22 | Behcet |
| 4 | Architecture, interfaces | SV#18.1–18.3, 18.16, 18.17; SV#19.1, 19.3 | Behcet |
| 5 | Operating agents safely | Sections 2–5 below | Venkat |
| 6 | RAN considerations | AI/ML closed loops in the RAN | TBD |
| 7 | IETF protocol requirements | NETCONF/YANG, WIMSE, AUDIT, AgentProto, DAWN | Shared |
| 8 | Security | -01 §5; TR 33.801-01 KI#8.2, KI#6.3 | Shared |
| 9 | IANA | None | — |
| A | Appendix | Solution variants mapped to IETF work | Shared |

## 2. Scope of authority

- **3GPP (KI#18 b6, SV#18.22, S2-2611398):** intents authorised per service/UE/AF; no per-agent authority.
- **SA2#178:** AI optional and subject to operator policy; 6GC can receive 3GPP intents only from UEs and AFs (S2-2613128, approved). Operator control and autonomy levels postponed (S2-2613041).
- **Contribution:** scoped, time-limited, revocable grant per task (WIMSE AIMS, Transaction Tokens, NACM).

## 3. Pre-action verification

https://datatracker.ietf.org/doc/draft-cui-dns-native-agent-naming-resolution/
good draft on ai agent naming


- **3GPP (SV#18.1, SV#18.22, SV#18.6):** five-outcome action evaluation proposed, not agreed; no live-state inputs.
- **SA2#178:** intent fulfilment should comply with network policies (S2-2613128, approved); verifying the fulfilment decision against operator policy postponed (S2-2613041).
- **Contribution:** state the management plane must expose for the check (RFC 6241, RFC 8342, digital twin).

## 4. Rollback and containment

- **3GPP (SV#18.22, SV#18.15):** fallback to default/last-known config proposed; no multi-NF plan rollback.
- **SA2#178:** an optional closed loop may adjust the fulfilment plan (S2-2613092, approved); nothing on rollback or on stopping an agent.
- **Contribution:** multi-target confirmed commit (RFC 6241 §8.4); suspend/revoke independent of the agent.

## 5. Attribution

- **3GPP (SV#18.6, 18.22, 18.1, 18.19):** correlation ID proposed; self-reported, not tamper-evident.
- **SA2#178:** nothing approved on traceability; performance monitoring conclusions postponed (S2-2613040).
- **Contribution:** signed YANG provenance, AUDIT records, common time reference linking change to effect.

**Questions for co-authors:** (1) §5 into `draft-sascch-6gai-agent-arch` now, or skeleton first; (2) how per-author files merge before -00; (3) -00 by early Jan (AINETOPS BoF cut-off 12 Jan 2027) or by 22 Feb 2027?

---

## Appendix: details

### A.0 Baseline and skeleton notes

- Baseline: 3GPP TR 23.801-01 V0.10.0 (2026-10), which includes the interim agreements approved at SA2#177 (S2-2611398 as cl. 7.1.18.1, S2-2611400 as cl. 7.1.19.1). Results of SA2#178 (16–20 Nov 2026) are not yet in a published version of the TR; they are cited by Tdoc number with their status (approved, postponed or not handled). 3GPP text is paraphrased and cited by clause; no option among SV#18.1, SV#18.2 and SV#18.3 is assumed.
- §1 Introduction: relation to draft-sarischo-6gip-aiagent-requirements-01 (Stage 1); scope is AI in the 6G system only; what "Stage 2" means in an IETF document.
- §2 Terminology: AI agent (TR 22.870), intent (TR 23.801-01 KI#18 NOTE 3), aligned with DAWN terms.
- §3: KI#18 "AI for 6G architecture", KI#19 "6G Network for AI".
- §4: tool access (SV#18.16); registration and discovery (SV#18.17, SV#19.1); exposure (SV#19.3).
- §6 RAN: consistent with -01's "currently unknown" statement on RAN agents.
- §7: consolidated asks of NETCONF/YANG, WIMSE/OAuth, AUDIT, AgentProto, DAWN.
- §8: builds on -01 §5 and SA3 TR 33.801-01 KI#8.2, KI#6.3.
- Draft name: `draft-sascch-6gai-agent-arch` (set by Behcet, 27 Oct 2026). Editor's Copy: https://bsarikaya.github.io/Stage2AI-new/draft-sascch-6gai-agent-arch.html

Each subsection in §5 of the draft follows the same pattern: the problem in 6G terms; what TR 23.801-01 proposes and what is agreed; the gap; and what the management plane and IETF protocols must expose or enforce.

### A.1 Scope of authority — what an agent is allowed to change, and how that is bounded

- **TR 23.801-01:** KI#18 bullet 6 (operator-configurable autonomy levels, including no AI). SV#18.22: operator control decides whether AI-driven requests and actions are allowed; autonomy levels per slice/DNN, NF, UE group or application; an OAM-configured "boundary allowed" listing permitted NFs, NF service APIs, slices, UEs and services (cl. 6.18.22). SV#18.15: exploration bounded by operator policy. Agreed (S2-2611398): authorisation of intent requests per service or per UE/AF, and per-NF (de)activation of AI. For UE agents (S2-2611400): a preconfigured agent ID, used for authorisation. SA2#178, approved (S2-2613128): AI capabilities and technologies are optional and subject to operator policies and configuration; the 6GC can receive 3GPP intents only from UEs and AFs; whether to introduce an NF for intent handling is FFS. SA2#178, postponed (S2-2613041): in notes, OAM provides the operator policy and the NF enforces it at runtime (OAM details FFS, SA5 scope); the 6G CN may support multiple autonomy levels (details FFS). For UE agents, approved (S2-2612866): the agent ID is related to the UE subscription and allocated by the HPLMN; one UE AI agent per UE in this release; an LS asks SA3 about the trust assumption for configuring the ID (S2-2613121).
- **Gap:** network-side agents have no per-agent credential or authority object. How autonomy is "unlocked" is not defined. The Stage 1 draft (§2.7) covers access to resources, not what an authorised agent may change.
- **IETF side:** express authority as a scoped, time-limited, revocable grant bound to one task (WIMSE AIMS, OAuth Transaction Tokens), enforced on the managed side (NACM, RFC 8341). Cite ICON (-02) CTL-7/8/9 for the requirements rather than restating them.

### A.2 Pre-action verification — checks before an action reaches the live network

- **TR 23.801-01:** SV#18.1 operator control intermediary as a deterministic enforcement point. SV#18.22 evaluation of candidate network actions with five outcomes: allow, allow within a restricted scope, request more information, redirect to a default procedure, deny. Its NOTE 7 says explainability never authorises an otherwise prohibited action. SV#18.6: optional Digital Twin check before execution. Nothing is agreed yet: Turkcell S2-2610275 and China Mobile S2-2610037 were merged into S2-2610784, which was not handled at SA2#177. SA2#178 did not add a check: S2-2613128 (approved) says intent fulfilment should comply with network policies, subscription constraints and available network capabilities and resources, in a clause that depends on agreement of the intent structure, and does not say which entity checks this or when. S2-2613041 (the 6G CN can verify whether the fulfilment decision adheres to operator-configured policies and constraints) was postponed. Turkcell's SV#18.22 update on evaluation before enforcement (S2-2612233) was not handled.
- **Gap:** no agreed requirement for a check before enforcement. The listed evaluation inputs do not include the runtime state a check needs: freshness of the state the agent acted on, concurrent changes to the same resources, reversibility, and a recorded pre-change baseline.
- **IETF side:** define what the management plane must expose for such a gate to run against live state: validate and candidate datastores (RFC 6241), intended vs. operational state (RFC 8342), the NMRG digital-twin architecture as an optional what-if step. The Stage 1 hook is -01 §4.1 (tool chaining moving from read to write operations).

### A.3 Rollback and containment — when an action goes wrong

- **TR 23.801-01:** SV#18.22: the network shall always be able to switch to default procedures without AI; forced or graceful switch (FFS whether both are needed); rollback to a previously known-safe state; forcing an NF back to its last known configuration; the intermediary can stop forwarding tool calls from agents. SV#18.15: stop or revert exploratory actions. (SV#18.9 is 6G-to-5G interworking fallback, not rollback.) Agreed: only for KI#19, where the network or the consumer can terminate an AI service. SA2#178, approved for KI#18 (S2-2613092): closed-loop operation is optional; where it is used, an entity that supports intent fulfilment determines the fulfilment status from performance monitoring and adjusts the fulfilment plan when needed. The text does not say whether adjusting the plan includes undoing steps already applied. The postponed operator control text (S2-2613041) has no principle on fallback or rollback, only that deactivating AI capabilities does not prevent standardised 6G CN procedures from operating; its discussion part says a common understanding of what to fall back to is still needed.
- **Gap:** no rollback for a plan that has partly executed across several NFs. Service continuity during the switch is FFS. None of this is in the KI#18 interim agreements.
- **IETF side:** confirmed commit with automatic revert on timeout (RFC 6241 §8.4) is the single-device model; the draft should state what a multi-target equivalent needs (a shared rollback handle, ordering, health conditions that trigger the revert). A suspend or revoke signal on a path independent of the agent. Cite ICON INT-1/INT-2. The Stage 1 hook is -01 §4.1 ("blast radius").

### A.4 Attribution — which agent changed what, and why

- **TR 23.801-01:** SV#18.6: an intent correlation identifier carried across multi-agent delegation and NF/tool calls, with traceability records of actions, entities and configuration values (cl. 6.18.6.1.2.3). This is not the Intent Correlation ID of cl. 6.18.7, which groups related intents of one requester and whose need is FFS. SV#18.22: correlation of each candidate action with its explanation, the operator control decision and the result. SV#18.1: audit logs streamed to an Audit Function. SV#18.19: a Monitoring and Adaptation Function that audits execution. Agreed: nothing. S2-2611398 leaves operator control of observability and traceability FFS, and the SA2/SA5 split is open. SA2#178: no change. The conclusions on performance monitoring (S2-2613040) and on operator control (S2-2613041) were postponed, and the SV#18.7 and SV#18.19 updates on intent identifiers and monitoring (S2-2611787, S2-2612616) were not handled.
- **Gap:** records are self-reported by the acting entity; they are not tamper-evident; there is no common time reference linking a change to its effect on performance counters.
- **IETF side:** signed provenance of YANG data (draft-ietf-opsawg-yang-provenance), cross-domain audit of delegation chains (AUDIT, if chartered), incident linkage (draft-ietf-nmop-network-incident-yang). The open question for the co-authors: whether attribution may rely on the agent's own record or must come from independent observation.

### A.5 Optional: arbitration between closed loops

SV#18.22 covers conflicts between fulfilment plans of different intent handlers (FFS whether needed); SV#18.21 covers closed-loop operation, for which SA2#178 approved principles (S2-2613092: optional; governance by the entity responsible for intent handling, which entity is FFS; nothing on conflicts between loops); SA5 TR 32.801-01 KI#1.11 covers conflict handling. Proposed as a cross-reference only, since it was not one of the four agreed points.
