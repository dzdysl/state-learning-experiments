# Hypotheses and conclusions

## Confirmed conclusion — why the final step succeeds

The user's proposed intermediate state is substantially correct: after Security Mode Reject, UERANSIM retains current KSI 0; during the next authentication it builds pending/non-current KSI 1; and the subsequent Authentication Response still uses current KSI 0.  The correction is that this does **not** create an observed KSI-0-versus-KSI-0 conflict at the second AMF authentication.  Open5GS sees the GUTI Registration Request's declared KSI 0, then increments its new authentication KSI to 1 before it sends the second Authentication Request.

The stale KSI-0-protected Authentication Response is accepted because the replacement AMF-UE has no established NAS security context at that point.  Open5GS deliberately disables integrity processing in `nas_5gs_security_decode()` in this state; no MAC comparison is made, hence no MAC failure.  RES* is separately correct for the new authentication vector, so the AMF advances to KSI-1 Security Mode Command.  UERANSIM promotes its pending KSI 1 on receipt of that SMC; final Security Mode Complete then uses matching KSI 1 and succeeds.

## H1 — the earlier failing word crosses a different boundary

**Confidence: high.**

The prior follow-up adds a further registration/authentication re-entry after an AMF security context already exists, and its core log contains two explicit NAS MAC verification failures.  That state sets the sticky `mac_failed` flag and blocks final SMC processing.  This successful word enters a new AMF context, performs GUTI/identity recovery, and completes the new KSI-1 security exchange before another stale protected message can be verified against an active AMF security context.

The exact divergence should be tested with an incremental comparison that adds one symbol at a time after this word, with AMF NAS-security debug traces retained.

## H2 — UERANSIM's disabled duplicate-ngKSI check is a test-harness semantic choice

**Confidence: high for the source change; medium for its protocol impact.**

The historical 5G-AKA handler explicitly comments out its normal check for an ngKSI already held by current or non-current context.  This allows the harness to proceed through unusual re-entry cases by replacing the pending context.  In this captured word the second value is 1 while current is 0, so the disabled check is not required to resolve a literal same-KSI collision; it still documents that the harness intentionally permits these context combinations.

## H3 — KSI alone is not a NAS-MAC identity

**Confidence: high.**

KSI is a three-bit context selector, not the NAS integrity key itself.  A MAC needs the derived `KNASint` and NAS COUNT as well.  The second response's old KSI 0 would only produce an AMF MAC warning if the AMF attempted verification with an established, incompatible context.  The source-confirmed no-security-context branch prevents that verification here.  A future test should log KSI, selected algorithms, COUNT, and a non-secret key-context identifier on both sides rather than infer a MAC result from KSI equality alone.

## Current StateLearner/UERANSIM check — `ngKSI already in use` is not an emitted 5G-AKA response

**Confidence: high for the current working tree; not a byte-exact claim about the dirty runtime bundle.**

The current `open5gs-state-learning` working tree at commit `72af668f8363ae6e42bb5d7fcfea01f930eb8dfd` retains the ordinary duplicate-ngKSI guard only in its EAP-AKA' handler (`src/ue/nas/mm/auth.cpp:157-165`): a received Authentication Request is considered conflicting when its KSI equals either `m_currentNsCtx->ngKsi` or `m_nonCurrentNsCtx->ngKsi`.  That branch calls `sendAuthFailure(NGKSI_ALREADY_IN_USE)`, but the helper's actual `sendNasMessage(resp)` call is commented out (`:63-81`), so it does not emit an uplink Authentication Failure PDU in this StateLearner build.

For the 5G-AKA handler used by the captured Open5GS exchange, the equivalent guard is explicitly commented out with `State Learner: remove ngKSI check` (`auth.cpp:342-352`).  The handler instead continues and creates/replaces `m_nonCurrentNsCtx` from the received Authentication Request (`:382-389`).  Therefore, for the current Open5GS + StateLearner 5G-AKA flow, `ngKSI already in use` is neither an actual UE Registration Reject nor an actual emitted UE Authentication Failure; it is retained only as commented original logic.  This source check is recorded separately because the run provenance declares a dirty runtime build and does not provide its dirty diff.
