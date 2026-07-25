# Tinig Bayan — Design

**Status: design exploration. Nothing here is built.** The repository is currently an
empty Angular 19 scaffold. This document records the reasoning behind the platform so
the decisions are legible before code hardens around them.

Companion document: [THREAT-MODEL.md](./THREAT-MODEL.md).

---

## 1. The problem, stated correctly

The goal is a national civic platform for the Philippines that resists polarization —
in contrast to Facebook, where political division is manufactured and monetized.

The tempting framing is "a platform without fake news." We reject that framing.

Misinformation is not the disease. It is what fills a machine built for something else.
The polarization engine has four parts, none of which are about truth:

1. **Engagement ranking.** Moral outrage is the most reliably shared emotion, so any
   ranker optimizing engagement converges on outrage. Nobody chooses this; the
   optimizer finds it.
2. **Identity signaling.** A political post is not a claim, it is a jersey. Jerseys
   cannot be fact-checked.
3. **Context collapse.** Broadcasting to family, employer, and strangers simultaneously
   turns every utterance into performance and makes "I'm not sure" unsayable.
4. **No accountability loop.** Speech is costless and being wrong is costless. Nothing
   ever returns to the person who was wrong.

A platform with a perfect fact-checker bolted onto an engagement-ranked feed would still
polarize. People would fight about true things.

Two further constraints shape everything below:

- **Exposure to opposing views, without relationship, backfires.** Bail et al. (PNAS,
  2018) paid Twitter users to follow bots retweeting opposing views; those users became
  *more* extreme. "Show people both sides" is not a solution — it is a known failure
  mode. Cross-cutting exposure must be embedded in a structure that makes it
  cooperative, not adversarial.
- **The platform must never adjudicate truth.** Any platform positioning itself as the
  arbiter of what is true in Philippine politics will be destroyed as partisan within a
  week, and will deserve it. The platform reports *where agreement exists*. It never
  rules on who is right.

---

## 2. The core primitive

Everything in this design reduces to one rule:

> **Cross-cluster agreement is the only currency.**

Not popularity. Not volume. Not approval. The only thing that earns rank, reputation,
or agenda position is endorsement from people who *disagree with each other*.

This applies uniformly:

| Mechanism | Conventional design | Tinig Bayan |
|---|---|---|
| Ranking | most upvoted | endorsed across opposing clusters |
| Reputation | karma from anyone | credit only from the out-group |
| Agenda-setting | most signatures | support spanning multiple clusters |
| Question wording | moderator judgment | both clusters rate it as fair |

The design intent is to take the thing people actually want — status, attention, agenda
power — and route the only path to it through the people they disagree with.

This primitive is borrowed from two working systems: **Polis** (opinion clustering with
consensus-statement surfacing, used in Taiwan's vTaiwan process) and **Community Notes**
(bridging-based ranking, algorithm published). Neither is speculative; both run at
scale.

---

## 3. Identity — verify, then forget

### 3.1 Decision: authenticity is mandatory

Every account is a verified unique human. Display is pseudonymous. This follows the
Dcard model: a hard gate, a soft interior — Dcard borrowed Taiwan's university email
registry as proof of personhood and showed only a coarse badge (school, year) inside.

The registry does not transfer. There is no equivalently clean national registry in the
Philippines. Candidate verification paths, ranked:

| Path | Assurance | Reach | Verdict |
|---|---|---|---|
| **Phone / SIM** | Medium-high | Near-universal | **Primary.** RA 11934 (SIM Registration Act, 2022) ties every SIM to a verified identity — the telco already did the KYC. Known weakness: documented bulk fraudulent registration; numbers churn. |
| **PhilSys national ID** | Highest | High | **Rejected for now.** Government-linked identity on a political speech platform is a chilling instrument by construction, in a country where activists are red-tagged. Accreditation as a relying party is also a bureaucratic mountain. |
| **e-wallet KYC** | High | Very high | **Not viable.** Real eKYC exists behind GCash/Maya, but no general third-party identity assertion product to build on. |
| **Vouching** | Low-medium | Universal | **Secondary path only.** Existing verified users vouch; penalties propagate to the voucher. Serves the undocumented. Never primary. |

**Plan for multiple paths with different accessibility profiles, not one.**

### 3.2 The equity problem — stated, not solved

Mandatory hard identity systematically excludes the poorest and least documented — the
exact people a platform named *Tinig Bayan* claims to carry. If the verification bar is
high, we will have built a platform for the middle class and named it after the people.

This compounds with sortition (§4.3): if the documented are overrepresented among the
verified, a "representative" panel is representative of the *documented* Philippines.

Mitigations, all partial:
- Multiple verification paths at different cost/documentation levels.
- The vouching path, specifically for people no registry reaches.
- Assisted or offline enrollment through partner organizations.
- **Measure and publish the demographic skew of the verified population.** Correct
  panel draws against known population statistics where possible, and state the
  residual bias on every published result.

This is an open problem. It should never be presented as solved.

### 3.3 Requirement: the platform must not possess the identity mapping

Dcard holds the link between the real student and the anonymous account. For a campus
board, acceptable. For a Philippine national political platform, **that database is a
kill list** — reachable by subpoena, by breach, or by one bought insider. Under RA 10175
(cyberlibel) the platform is a permanent subpoena target; under RA 10173 (Data Privacy
Act) it would be a controller holding sensitive personal data about political
expression.

The requirement is therefore not "protect the mapping." It is **never possess it.**

**Verify-then-forget:**

1. User proves uniqueness to a verification service (phone OTP, etc.).
2. The service issues a **blindly signed credential** — it signs a token it cannot read.
3. The user redeems the token at the platform. The platform verifies "this is a unique,
   verified human" and **cannot determine which one**.
4. A **nullifier** derived from the identity prevents a second redemption. One human,
   one account, enforced without linkage.

This is Privacy Pass / anonymous-credential territory. Semaphore implements this shape
with zero-knowledge proofs if we want it off the shelf, but **zk is not required to
start** — a blind RSA signature scheme is a few hundred lines and delivers most of the
guarantee.

Equally important and much cheaper: **run verification and the application as separate
services, with separate keys and ideally separate operators.** Then the join does not
exist anywhere to be compelled.

When a court or a general asks who posted something, the honest and only answer must be:
*we cannot know.* Build so that this is true.

### 3.4 Display identity

Coarse attribute badges only — no names. Dcard can safely show school and year because
there are thousands of students per school. Region plus age plus one or two attributes
deanonymizes quickly in a small municipality.

**Enforce a k-anonymity threshold on every displayed attribute combination.** Coarsen
buckets until the threshold holds. This is a hard gate on rendering, not a guideline.

---

## 4. Deliberation

### 4.1 No feed

The infinite scroll of everything is the polarization machine. There is no feed.

The unit is an **issue-scoped room**. Removed by design:

- **No reshare, no quote-post.** Quote-post is the dunk mechanic — it exists to show
  your side someone else's stupidity. Nothing can go viral, which removes most of a
  troll farm's leverage.
- **No like counts affecting rank.**
- Rate limits, deliberately.

Under consideration: requiring a participant to summarize the position they are
opposing, to that person's satisfaction, before replying against it. Slow is the point.

### 4.2 The unit is a claim, not a post

A post can only be cheered. A claim can carry evidence, be forked, and be *conditionally*
agreed with — "I accept the fact, not the conclusion." That single affordance breaks a
large amount of tribal bundling, because it lets people decompose a package they would
otherwise have to accept or reject whole.

Inside a room, the mechanic is Polis-style: participants submit short statements and
vote **agree / disagree / pass** on others' statements. The system clusters participants
by voting pattern and surfaces the statements that **bridge** the clusters.

### 4.3 National scale via sortition

A hundred million people cannot deliberate. Polis-style processes work at hundreds to
low thousands. "National" therefore cannot mean one room.

**Mandatory identity unlocks the mechanism that makes national deliberation tractable:
sortition.** Because every account is a verified unique human with coarse demographic
attributes, the platform can draw a **statistically representative random sample of the
Philippines**. Facebook structurally cannot do this.

*This is the real reason to require authenticity.* Not "no fake accounts" — that is a
side benefit. The reason is the ability to construct representative micro-publics.

This is established practice, not invention: Fishkin's Deliberative Polling, Ireland's
Citizens' Assembly (which broke a decades-frozen abortion debate and fed the 2018
referendum), and Mongolia, where deliberative polling is required by law for
constitutional amendments.

**How a national question runs:**

1. **Open with the raw question.** No decomposition step — see §4.4.
2. **Draw rooms.** Many parallel rooms of roughly 200, each **stratified to be
   internally diverse** — deliberately mixed, never homogeneous.
3. **Round one — discover the axes.** Participants submit statements and vote
   agree/disagree/pass. The dimensions of disagreement are computed from the vote matrix.
4. **Round two — deliberate the discovered fault lines.** The statements that split the
   clusters in round one become the substance of round two.
5. **Aggregate across rooms**, weighting by cross-room replication.
6. **Publish.** "A representative sample of Filipinos, after real deliberation,
   converged on this." Citable by media and legislators — which is also the growth loop.

**Cross-room replication is the manipulation detector.** Run one question through 40
independent representative rooms and a coordinated campaign appears as an outlier room.
This is a *statistical* defense against troll farms rather than a moderation one: much
harder to defeat, and it never requires the platform to judge anyone's sincerity.

### 4.4 Decomposition is emergent, not authored

"Federalism: yes or no" is pure identity and cannot be deliberated — answering it is a
declaration of which camp you belong to, not an act of reasoning. It has to become
something with content: *should the internal revenue allotment formula weight population
or land area? which powers devolve? what happens to regions with a small tax base?*

The obvious way to get there is to appoint someone to draft the sub-questions. **We
reject that.** Whoever decomposes controls the framing, and framing largely determines
the outcome — decomposing "the war on drugs" into questions about enforcement
effectiveness produces a different national conversation than decomposing it into
questions about extrajudicial killings. Neither is dishonest; they are simply not the
same question. An appointed decomposer is the push-poll surface of the entire platform,
one level upstream of the wording-neutrality check, and the most valuable thing on it to
capture.

**Instead, the statements are the decomposition.** A participant statement is implicitly
a sub-question with a position attached. The axes of division are then **computed, not
chosen** — dimensionality reduction over the vote matrix, as Polis does. Statements with
high cross-cluster agreement are the consensus; statements that split the clusters are
the real fault lines, and those fault lines *are* the sub-questions. Decomposition is
therefore not a prior step performed by anyone. It is an output of round one.

This subjects framing to the same cross-cluster test as everything else, rather than
appointing a framer and then trying to keep them honest.

**Second consequence: the platform never has to decline a question.** "Was Marcos Sr. a
hero?" simply runs, and produces two clusters with almost no bridging statements. That
null result is honest, publishable output — *this question has no deliberable structure,
and here is the evidence.* Far better than a committee refusing to run it, which is
indistinguishable from censorship and would rightly be attacked as such.

**What does not dissolve.** Three residual functions remain. All three are *sorting, not
framing*: they do not choose the axes, their errors are visible to anyone reading the
statement pool, and they are partly automatable. That makes them categorically less
capturable than the role we rejected.

1. **A seed frame.** Something has to be at the top of the page, and even "what should we
   do about federalism?" presumes federalism is the object of concern rather than, say,
   local government funding. Zero framing is impossible. The proposer supplies minimal
   framing; participants override it by submitting statements that reframe. Weak framing
   that can be overridden beats strong framing that cannot.
2. **Empirical claims must not be voted on.** "The death penalty deters crime" has an
   answer; "I would accept slower growth for lower inequality" is a preference.
   Participants will not separate these. If a factual claim goes to a vote, the platform
   launders an opinion into a finding. Factual claims need routing to evidence, not to
   agreement.
3. **Statement hygiene.** Compound statements — *"we should federalize AND fix
   corruption"* — make agree/disagree meaningless and corrupt the clustering. Splitting
   compounds, deduplication, and rejecting incoherent submissions is a janitorial
   function the statement pool cannot go without.

**The failure mode this buys.** Emergent framing is not neutral either: it is biased
toward whoever writes statements early and fluently. Early statements anchor the opinion
space, so a motivated group that floods the pool in the first hour shapes the axes
everyone else votes along. An appointed role has been traded for a race condition.
Mitigations, all cheap: seed the initial pool from a stratified random sample rather than
volunteers, throttle statements per person, serve statements to voters in randomized
order, and keep submission open throughout rather than front-loading it, so late
entrants can still introduce a missing dimension. See THREAT-MODEL §2.5.

### 4.5 Consequence: proposers are not participants

Under sortition, the person who proposed a question generally will **not** be in the
room that deliberates it. This is the point — it prevents proposers from stacking their
own panel — but it will feel bad and must be designed for.

Citizens' assemblies solve this by letting the public submit evidence and testimony to a
panel drawn by lot. Same here: proposers get full visibility and a written channel in,
but not a seat.

---

## 5. Reputation and agenda-setting

### 5.1 Decision: no Reddit-style karma

Rejected. Not because karma is bad in general — on r/woodworking, majority approval
genuinely approximates quality. It fails here because on political content, **majority
approval approximates faction size.**

The mechanical failure loop: dissent is downvoted → dissenters are hidden and
rate-limited → dissenters leave → remaining consensus tightens → the score now measures
agreement with an even narrower majority. This is the loop that homogenizes subreddits
over time. Shipping it would mean shipping an echo-chamber generator as a core feature.

It also directly contradicts the ranking model. Bridging says *surface what opponents
both accept*; karma says *surface what the most people liked*. Ship both and karma wins
— it is simpler, more visible, more addictive, and everyone already knows how to play it.
A leaderboard number always beats a subtle statistical property.

The identity layer already provides what karma is genuinely good at — spam and sockpuppet
resistance. One verified human, one account, no transferable score means no farming
market. That protection is already paid for; do not buy it again with a mechanism that
costs the mission.

### 5.2 Never conflate disagreement with punishment

"Downvote is not a disagree button" has never been obeyed on any platform, because
nothing enforces it.

On Tinig Bayan, **"I disagree" is the single most valuable signal collected** — it is
what makes clustering work at all. If disagreeing is also how you punish someone, people
stop doing it honestly and the clustering degrades into noise.

Therefore two entirely separate mechanisms:

- **Disagree** — free, unlimited, no penalty to anyone, feeds clustering.
- **Norm violation** — a report. Costly, reviewed, rare.

### 5.3 Reputation, if any, has different physics

The legitimate needs are spam resistance, a read-worthiness signal, and accountability
over time. Any reputation system must satisfy all six:

1. **Only opponents can credit you.** Your score moves when someone in a *different
   opinion cluster* marks your statement as fair, or as the strongest version of its
   argument. Same-cluster approval is worth exactly zero.
2. **Many reputations, none fungible.** Per-domain and non-transferable. Excellence on
   flood control confers no authority on the Bangsamoro. Fungibility is what creates
   farming markets and the "high karma = credible" fallacy.
3. **Not a public scoreboard.** Visible only to the holder, or invisible and used purely
   as a ranking input. The instant a number sits beside a name, people optimize the
   number.
4. **It decays.** Reputation reflects current behavior, not a hot streak from years ago.
   Otherwise an old guard entrenches — a form of capture.
5. **It buys responsibility, not status.** It unlocks statement-pool hygiene (§4.4),
   serving on a synthesis panel, moderating. Notably it does *not* unlock framing.
   Spendable, not displayable.
6. **It cannot be transferred, sold, or delegated.**

### 5.4 Agenda-setting: propose freely, promote carefully

**Decision: any authenticated user may propose a national question.**

Proposal is not the same as running one. Promotion is a separate gated step, for two
reasons: capacity (only so many real deliberations can run at once) and abuse — *"Should
[politician] be arrested?"* opened as a national question is a smear that borrows the
platform's legitimacy at zero cost to the attacker.

Promotion rules:

- **Never gate on signatures or popularity.** That hands the national agenda to the
  largest bloc — the karma failure one level up.
- **Gate on bridging support.** A proposal is promoted when it draws backing from
  *multiple opinion clusters*. A question only one faction wants asked is not a national
  question; it is a campaign.
- **A light framing check, not a drafting stage.** Earlier drafts of this design had an
  editorial step that converted raw proposals into sub-questions. That is exactly the
  role §4.4 rejects — it hands someone the framing pen. What remains is narrow: confirm
  the proposal states a topic rather than smuggling a verdict, and leave the rest to the
  statement pool.
- **Test wording for neutrality, and measure it.** Loaded framing is the classic
  push-poll attack. Ask both clusters "is this fairly worded?" and require cross-cluster
  agreement before the question can run. An objective check on a problem everyone else
  handles with vibes.
- **Throttle proposals.** Each account holds a small number of open proposals at a time.
  Scarcity, not moderation.

---

## 6. Explicitly not building

Recorded so these do not creep back in:

- A feed.
- Reshare or quote-post.
- Public vote counts as a ranking input.
- Reddit-style karma or any single public reputation number.
- A fact-checking authority, or any mechanism where the platform rules on truth.
- An appointed question-drafter, or any role that holds the framing pen (§4.4).
- Votes on empirical claims — that is laundering opinion into finding (§4.4).
- Growth-optimized virality mechanics of any kind.
- A "show both sides" balanced feed — a measured failure mode, not a fix.

---

## 7. Open questions

- **Distribution.** A deliberately slow, non-viral platform has no growth loop by
  construction, and Facebook is effectively the internet for much of the country. The
  current best answer is to *parasitize rather than compete*: make Tinig Bayan an
  embeddable, shareable deliberation instrument whose links get posted on Facebook, so
  Facebook is the distribution channel and Tinig Bayan is where the conversation
  actually happens. Unresolved.
- **Language.** Tagalog, Bisaya, Ilocano, and Taglish code-switching break most
  off-the-shelf clustering and moderation tooling. Needs budgeting early, not late.
- **Devices.** Low-end Android on metered data means a small-bundle PWA. This should
  constrain framework decisions before they harden.
- **Verified-population skew** (§3.2). Unsolved.
- **Room size and count** — 200 and 40 are placeholders, not derived numbers.
- **The residual sorting roles** (§4.4) — who separates empirical claims from
  preferences, and who performs statement-pool hygiene. Much narrower and more checkable
  than the framing role this replaced, and plausibly partly automatable, but not yet
  specified.
- **Statement-pool anchoring** (§4.4) — the mitigations are cheap and plausible but
  untested. How strongly early statements shape the discovered axes is an empirical
  question that should be measured in the first rooms, not assumed.
- **Legal review.** RA 10173, RA 10175, RA 11934, RA 11055, and COMELEC rules on online
  campaign material during election periods all bear on this. None of the above
  substitutes for actual Philippine counsel.

---

## 8. Build order

The room is the risky hypothesis; the identity layer is expensive but well-understood.
Build the risky thing first.

1. **A single deliberation room.** Statement submission, agree/disagree/pass, clustering,
   bridging surface. Throwaway auth. This is the falsifiable core — if a room does not
   produce bridging statements that participants recognize as fair, nothing else matters.
2. **Multi-room + aggregation**, without sortition. Validate cross-room replication.
3. **Identity: verify-then-forget.** Phone path first, separate service, blind signatures.
4. **Sortition**, once the verified population is large enough to draw from meaningfully.
5. **Agenda-setting and reputation.** Last — these only matter at a scale that does not
   yet exist.

---

## 9. References

- Bail et al., "Exposure to opposing views on social media can increase political
  polarization," *PNAS* (2018).
- Polis — The Computational Democracy Project; vTaiwan.
- Community Notes bridging-based ranking (published algorithm).
- Fishkin, Deliberative Polling — Stanford Deliberative Democracy Lab.
- Citizens' Assembly (Ireland), 2016–2018.
- Mongolia, Law on Deliberative Polling (2017).
- Privacy Pass; blind signature schemes; Semaphore (Privacy & Scaling Explorations).
- RA 10173 (Data Privacy Act), RA 10175 (Cybercrime Prevention Act), RA 11055 (PhilSys),
  RA 11934 (SIM Registration Act).
