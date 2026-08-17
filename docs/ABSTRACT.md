# Tinig Bayan — Abstract

Three lengths. Use whichever fits the venue.

---

## Full (≈280 words)

Political polarization on mainstream social platforms is commonly attributed to
misinformation. We argue the causation runs the other way. Engagement ranking rewards
moral outrage because outrage is what spreads; identity signaling displaces claim-making,
so posts function as declarations of allegiance rather than assertions that could be
evaluated; and costless speech removes any accountability loop. Misinformation fills that
machine rather than driving it — a platform with a perfect fact-checker attached to an
engagement-ranked feed would still polarize, because people would fight about true things.
Content-level interventions therefore treat a symptom, and any platform positioning itself
as an arbiter of truth in a contested political environment forfeits the legitimacy it
depends on.

Tinig Bayan is a design for a national civic deliberation platform for the Philippines,
built on a single inversion: **cross-cluster agreement is the only currency.** Ranking,
reputation, and agenda-setting reward endorsement from participants who disagree with one
another, rather than aggregate approval. The system has no feed, no resharing, no public
score, and no mechanism for adjudicating truth; it reports only where agreement exists.
Question decomposition is emergent rather than authored — participants' statements are the
sub-questions, and the axes of disagreement are computed from the vote matrix rather than
chosen by a moderator, so no role holds the framing pen.

Participation requires verified unique personhood with pseudonymous display, implemented
so the platform cannot possess the identity mapping: blind-signed credentials,
nullifier-based deduplication, and separated services. The purpose of verification is not
sybil resistance but **sortition** — the capacity to draw statistically representative
panels, and to use replication across independently drawn panels as a statistical detector
of coordinated manipulation.

The design is unimplemented. Its principal unresolved problems — cluster spoofing,
verification-driven exclusion, and facilitation at scale — are documented rather than
claimed solved.

---

## Short (≈90 words)

Tinig Bayan is a design for a national civic deliberation platform for the Philippines
that treats polarization as a product of what platforms reward rather than of the
misinformation they carry. Its single primitive is that cross-cluster agreement is the
only currency: ranking, reputation, and agenda-setting reward endorsement from people who
disagree with each other, never aggregate approval. There is no feed, no resharing, no
public score, and the platform never adjudicates truth. Verified unique personhood exists
to enable sortition — representative panels drawn by lot — not to deter fake accounts.

---

## One line

A civic deliberation platform where the only thing that earns rank, reputation, or agenda
position is agreement from the people you disagree with.

---

## Notes on framing

- The claim that misinformation is downstream is the load-bearing one. It is what
  justifies not building a fact-checker, and it will be the first thing challenged.
- "Sortition, not sybil resistance" is the most distinctive claim and the least obvious.
  Lead with it for a technical or academic audience.
- State that the design is unimplemented. The work is a design and a threat model; saying
  so directly is more credible than letting a reader discover it.
- For a Philippine audience, the exclusion problem (§3.2) deserves a sentence — a platform
  named for the voice of the people that requires documentation to enter has a tension it
  should name before a critic names it.
