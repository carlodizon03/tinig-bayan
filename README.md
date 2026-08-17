# Tinig Bayan

A civic deliberation platform for the Philippines, built so that national political
conversation resists polarization instead of manufacturing it.

**The only thing that earns rank, reputation, or agenda position is agreement from the
people you disagree with.**

Most attempts to fix political social media go after misinformation. We think that's the
symptom. Engagement ranking rewards outrage because outrage spreads; posts become
declarations of allegiance rather than claims anyone could evaluate; and nothing ever
comes back to the person who was wrong. Bolt a perfect fact-checker onto a feed like that
and it still polarizes — people just fight about true things instead. So this platform
changes what gets rewarded rather than policing what gets said, and it never rules on who
is right. It reports only where agreement exists.

What that means in practice:

- **No feed, no resharing, no public score.** Nothing can go viral, so volume buys
  nothing.
- **Content surfaces when opposing sides both endorse it** — not when it's popular.
- **National questions are deliberated by representative panels drawn by lot**, the way a
  jury is. Running the same question through many independent panels also makes
  coordinated manipulation show up as a statistical outlier.
- **Everyone is a verified unique human, shown pseudonymously** — but the platform is
  built so it *cannot* know who you are. Verification issues a blindly signed credential
  it can't link back to you. Asked who posted something, the honest answer is: we can't
  know.
- **Verification exists to enable the lottery, not to stop trolls.** Drawing a genuinely
  representative sample of a country is something no other platform can do, and it's the
  whole reason for requiring authenticity.

**Status: design exploration.** No product code exists yet — the repository is an
Angular 19 scaffold. What's here is a design and a threat model, including the problems
it does not solve: resistance to infiltration of opposing clusters, the risk of excluding
the least documented people in a platform named for the voice of the people, and how
small-group deliberation could ever be facilitated at national scale.

- [docs/DESIGN.md](docs/DESIGN.md) — problem framing, identity architecture
  (verify-then-forget), deliberation mechanics, what a participant actually sees,
  reputation and agenda-setting, and build order.
- [docs/THREAT-MODEL.md](docs/THREAT-MODEL.md) — the state, troll farms, ordinary users,
  and the platform operator itself.
- [docs/ABSTRACT.md](docs/ABSTRACT.md) — summaries at three lengths for use elsewhere.

---

## Development

This project was generated using [Angular CLI](https://github.com/angular/angular-cli) version 19.1.8.

### Development server

To start a local development server, run:

```bash
ng serve
```

Once the server is running, open your browser and navigate to `http://localhost:4200/`. The application will automatically reload whenever you modify any of the source files.

### Code scaffolding

Angular CLI includes powerful code scaffolding tools. To generate a new component, run:

```bash
ng generate component component-name
```

For a complete list of available schematics (such as `components`, `directives`, or `pipes`), run:

```bash
ng generate --help
```

### Building

To build the project run:

```bash
ng build
```

This will compile your project and store the build artifacts in the `dist/` directory. By default, the production build optimizes your application for performance and speed.

### Running unit tests

To execute unit tests with the [Karma](https://karma-runner.github.io) test runner, use the following command:

```bash
ng test
```

### Running end-to-end tests

For end-to-end (e2e) testing, run:

```bash
ng e2e
```

Angular CLI does not come with an end-to-end testing framework by default. You can choose one that suits your needs.

### Additional Resources

For more information on using the Angular CLI, including detailed command references, visit the [Angular CLI Overview and Command Reference](https://angular.dev/tools/cli) page.
