# Tinig Bayan — Threat Model

**Status: design exploration.** Companion to [DESIGN.md](./DESIGN.md).

A platform that convenes national political deliberation in the Philippines is a target
from day one. This document names the adversaries, what each one wants, and what in the
design actually stops them — including where nothing does.

**Assets worth attacking, in order of severity:**

1. **The identity mapping** (verified human ↔ account). If it exists, it is the most
   dangerous database in the country.
2. **The legitimacy of published results.** The product is the claim "a representative
   sample of Filipinos concluded X." Corrupt that and the platform is worse than useless
   — it becomes a laundering service for manufactured consensus.
3. **The national agenda queue.** Control of which questions get asked.
4. **Availability**, during election periods especially.

---

## 1. Adversary: the state (or a faction within it)

**Wants:** to identify a specific speaker; to compel takedowns; to shut the platform
down during an election period.

**Capabilities:** subpoena, warrant, RA 10175 cyberlibel proceedings, NPC or COMELEC
regulatory pressure, seizure of infrastructure, pressure on individual staff.

**Mitigations:**

| Attack | Defense | Residual risk |
|---|---|---|
| Compelled disclosure of who posted X | **Verify-then-forget** (DESIGN §3.3). Blind-signed credentials mean the platform cannot answer. | Metadata: IP logs, timing correlation, session fingerprints. See below. |
| Seizure of the verification service | Separate service, separate keys, separate operator. It holds phone numbers but no account linkage. | If both services are seized *and* both retained logs, correlation becomes possible. |
| Coercion of staff | Nothing to reveal, by construction. This is the point of designing for absence rather than protection. | Future code changes could silently reintroduce linkage. Needs a standing review gate. |
| Takedown / blocking | None technical. | Real and unmitigated. |

**Metadata is the live gap.** Blind signatures protect the credential, not the network
layer. If the app logs IPs, or if verification and account creation are correlatable by
timestamp, the cryptography is decorative. Required: no IP retention, deliberate delay
and batching between verification and redemption, and no analytics SDK that reintroduces
a device identifier. **This is the most likely way the design fails in practice** — not
by a broken crypto scheme, but by an ordinary logging default nobody reviewed.

**Non-technical defenses** (equally load-bearing): open-source ranking, public
moderation logs, and a published policy that the platform never adjudicates truth. These
are what make "we are not partisan" verifiable rather than a claim.

---

## 2. Adversary: coordinated political operations (troll farms)

Industrial and paid in the Philippine context. Assume budget, staff, and patience.

**Wants:** to make a faction's position appear to be the deliberated national consensus.

### 2.1 Mass fake accounts

**Defense:** one verified human, one account, enforced by nullifier. Removes the cheap
path entirely.

**Residual:** RA 11934 SIM registration has documented bulk-fraud weaknesses. An
operation can buy fraudulently registered SIMs at some cost per identity. The defense is
economic, not absolute — it raises unit cost from ~zero to something real. **Track the
prevailing black-market price of a registered SIM; that number is the actual security
parameter of the whole system.**

### 2.2 Real paid humans

Verification cannot detect a real person who is paid to participate. This is the hardest
attack and the design's answer is structural rather than detective.

**Defenses:**

- **Sortition.** Rooms are drawn by lot from the verified population. An operator cannot
  choose to be in a room. To place N operatives in a given room they must control a
  large fraction of the *entire* verified population — the cost scales with the platform,
  not with the attack.
- **Cross-room replication** (DESIGN §4.3). The same question runs through many
  independent representative rooms. Captured rooms appear as statistical outliers. This
  never requires judging anyone's sincerity, which is why it is robust.
- **Bridging.** Even a captured room produces nothing unless the operatives can win
  endorsement from participants in the *opposing* cluster. Volume alone buys nothing.

**Residual:** an operation that is genuinely large relative to the verified population
during the early period, when that population is small. **The platform is most
vulnerable to capture when it is smallest** — the reverse of the usual assumption. Do
not publish results as representative until the verified base can support a real draw,
and say so publicly.

### 2.3 Cluster spoofing

The subtlest attack on the whole design. Bridging rewards cross-cluster endorsement, so
an operation infiltrates the *opposing* cluster — voting sincerely on unrelated topics to
establish position — then uses that standing to endorse its own side's statements, which
now read as bridging.

**Defenses:** clusters are computed per-question from voting patterns, not stored as
persistent identity, so standing must be rebuilt each time. Reputation is per-domain and
non-fungible (DESIGN §5.3), so cross-topic standing does not transfer. Cross-room
replication still applies.

**Residual: partially unmitigated, and this is the deepest open problem in the design.**
It is the same class of attack Community Notes faces. Detection likely requires
monitoring for coordinated voting *sequences* rather than positions. Needs dedicated
work before any high-stakes question runs.

### 2.4 Agenda flooding and smear-by-question

Opening *"Should [politician] be arrested?"* as a national question is a smear that
borrows the platform's legitimacy at zero cost.

**Defenses:** proposal throttling per account; promotion gated on **bridging support**
rather than signature count, so a single faction cannot promote alone; a drafting stage;
and cross-cluster agreement that wording is neutral before a question may run.

**Residual:** the drafting-and-promotion layer is now the highest-value target on the
platform, since it decides what the nation deliberates. Whoever earns those privileges
must be selected in a capture-resistant way — plausibly by lot from qualified
contributors rather than by accumulation. Unresolved.

---

## 3. Adversary: ordinary users behaving badly

Not malicious, but the failure mode most likely to actually occur.

| Behavior | Defense |
|---|---|
| Brigading a room | Sortition — you cannot choose your room. |
| Downvoting dissent into silence | No downvote exists. Disagree is free and carries no penalty (DESIGN §5.2). |
| Dunking and screenshot wars | No reshare, no quote-post. Nothing can go viral. |
| Status-farming | No public score. Reputation is invisible, non-fungible, decaying, and spendable only on responsibility. |
| Sincere but polarizing pile-on | Bridging ranking means volume alone changes nothing. |

---

## 4. Adversary: the platform itself

The most under-modeled threat in projects like this. Tinig Bayan claims to report what
the nation concluded. That is a power, and it needs constraints pointed at its own
operators.

**Attacks available to the operator:** tuning cluster parameters to shape outcomes;
biased panel draws; quiet suppression of a question; selective publication.

**Required defenses:**

- Ranking, clustering, and draw algorithms **open source**.
- Panel draws **seeded verifiably** — a published commitment before the draw and a
  reproducible seed after, so anyone can recompute the sample.
- **Full raw vote data published** (subject to k-anonymity) so third parties can
  independently recompute results.
- **Every proposed question publicly visible**, including those not promoted and the
  reason. Suppression must be detectable.
- Moderation logs public.

Without these, "trust us" is the entire security model, and the platform becomes exactly
the manufactured-consensus machine it was built to replace.

---

## 5. Adversary: the design's own failure modes

Not attacks — ways the thing fails while working as specified.

- **Exclusion presented as representation** (DESIGN §3.2). If the verified population
  skews toward the documented middle class, every published result is a middle-class
  result wearing a national label. Mandatory mitigation: measure the skew, publish it,
  and attach the residual bias to every result.
- **Deanonymization by attribute** (DESIGN §3.4). Region plus age plus one more field
  identifies people in small municipalities. k-anonymity must be a hard gate on
  rendering, not a guideline.
- **Legitimacy laundering.** A well-run process produces a citable output. That output
  will be quoted selectively by whoever it favors. The platform cannot prevent this, but
  should publish confidence intervals, dissenting cluster positions, and non-consensus
  areas with equal prominence — never a single headline number.
- **Karma by another name.** Any future feature that surfaces a public approval count
  reintroduces the failure DESIGN §5.1 exists to prevent. This deserves a standing
  review question on every feature: *does this create a number people can farm?*

---

## 6. Priority order

1. **Metadata linkability** (§1) — most likely real-world failure of the identity
   guarantee, and it fails silently.
2. **Cluster spoofing** (§2.3) — deepest unsolved attack on the core mechanism.
3. **Early-stage capture** (§2.2) — highest risk precisely when the platform looks
   harmless.
4. **Operator trust** (§4) — cheap to fix now, near-impossible to retrofit credibly.
5. **Exclusion skew** (§5) — an ethical failure that no amount of good engineering
   downstream repairs.
