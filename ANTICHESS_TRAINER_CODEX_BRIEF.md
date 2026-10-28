# Antichess Opening Trainer — Project Brief for Codex

**Status:** Initial product specification (v1.0)  
**Product:** Working title: Antichess Opening Trainer  
**Goal:** Build a commercially viable, mobile-first application for memorizing antichess opening repertoires through progressive, repeated drilling.  
**Primary platform:** Installable Progressive Web App (PWA), optimized for iPhone, usable on desktop.  
**Primary user:** Antichess players who want to memorize long, branching opening lines.

## 1. Instructions to Codex

Treat this document as the authoritative specification. Build an actual, maintainable application in a repository, not a static mockup. Start by examining the repository and proposing a concise implementation plan, then implement the first working vertical slice. Make reasonable technology choices and document them. Ask for clarification only when a decision would materially affect correctness or require an external credential.

**First milestone:** A user can import a Lichess-exported antichess PGN study, see its ordered chapters as drillable lines, learn and practice those lines on a mobile-friendly board with correct antichess rules, and have progress persist locally. Include automated tests for rules, import, and drill behavior. Do not build payments, social features, an arena, or a full repertoire editor in the first milestone.

**Non-negotiable:** Never silently substitute standard chess rules for antichess. Never claim an imported move tree is valid without variant-aware validation. Never claim an opening is theoretically winning merely because it is the author's recommended line.

## 2. Product vision and differentiation

The user has studied openings with Chessreps and wants a similar training experience dedicated exclusively to **antichess** (also called losing chess / giveaway chess in some contexts). Antichess rewards extensive opening memorization, sometimes 20–30 moves deep or longer. Existing general-purpose chess repertoire tools often lack correct antichess move rules and a dedicated course catalog.

The core value proposition is a focused, polished drill trainer, not a general chess-playing platform or an interactive textbook. Expert-authored courses are an eventual differentiator; users may also want to import their own material.

The creator is an experienced antichess player and course author. Their source material is maintained in Lichess studies and includes recommended moves, explanatory comments, variations, and some standalone quiz positions. The agreed **new authoring standard** deliberately simplifies that material into ordered drillable lines. Existing studies are useful test fixtures but are not guaranteed to conform to the new standard.

Commercial aspiration: a modest niche subscription business, with roughly $5,000/year in revenue a satisfactory outcome. Monetization is **not** part of the first milestone. A possible future price to test is about $4.99/month, but it is not a settled requirement.

## 3. Definitive product decisions

- **Antichess only.** No standard chess, Chess960, or other variants in scope.
- **One Lichess study = one course/opening.**
- **One Lichess chapter = one drillable line.** A course typically contains **15–40 ordered lines**; the count is guidance, not a hard limit.
- **Line 1 is short**, generally the first 2–3 full moves; subsequent lines introduce branches and generally grow longer. A course may include lines 20–30+ moves deep.
- **Author controls chapter/line order.** Do not auto-sort by length: a short but counterintuitive variation may be harder than a long forced sequence.
- **Main line of each chapter = exact recommended drill sequence.** Variations and annotations are secondary reference material in v1, not separate drills unless represented by their own chapters.
- **No separate quiz system in v1.** A chapter with only a FEN and no moves is not an automatically gradable line. Flag it as a draft/untrainable chapter.
- **Focus on Learn, Practice, and Drill.** Time trials, puzzles, ratings, arena, and advanced gamification are later possibilities.
- **Mobile-first PWA**, installable on iPhone, avoiding native-app distribution costs at launch.
- **No backend/account requirement for the first milestone.** Save imported courses and learning progress locally; design storage so accounts/sync can be added later.
- **No custom full repertoire editor initially.** Author courses in Lichess, export PGN, import into trainer. A simple import preview and warnings are required.
- **Never force authors to duplicate a line merely to teach it at different depths.** Chapter sequences are authored once; the trainer can later support adjustable rehearsal depth or partial-line practice.

## 4. Lichess course authoring standard v1.0

### 4.1 Study-level rules

1. Create a Lichess study in the **Antichess** variant.
2. Give it a clear course title and short description.
3. Export as PGN with comments and variations retained.
4. The study's ordered chapters are the ordered training lines.
5. Do not rely on a study URL alone for initial import; support PGN file upload/paste first. Direct URL import can follow after API/access considerations are resolved.

### 4.2 Chapter-level rules

1. **Each chapter represents one training line.**
2. Name chapters with a two-digit ordinal and descriptive title, e.g. `01 - Wizard Basics`, `02 - Qa4 Continuation`, `03 - Qxa2 / Qxa1`.
3. Place the exact sequence to memorize on the **chapter's main line**. White and Black moves both belong in the sequence.
4. Order chapters pedagogically: basic line first, then common variations, then unusual and/or deep variations. Lines can overlap substantially.
5. Comments are optional and should be attached to relevant positions/moves. Import them as learning explanations.
6. Variations may illustrate alternatives, but in v1 only the main line is tested. Preserve side variations as metadata if practical; never silently promote them into required answers.
7. A chapter may start from the normal starting position or an explicit **FEN**. For full-line drilling, prefer starting from the initial position so users rehearse the entire sequence. For a mid-position chapter, display and drill from its explicit starting FEN, not from an invented prefix.
8. Avoid standalone quiz chapters in new training studies. A chapter with no main-line moves should be flagged and skipped as untrainable rather than inventing a solution.
9. Standard Lichess annotations (comments, arrows, colored squares, NAGs) should be preserved where feasible. Custom tags are **not required** in v1.

### 4.3 Example course layout (illustrative only)

| Chapter | Topic | Approximate depth |
|---|---|---|
| 01 | Wizard Basics | 2–3 moves |
| 02 | Qa4 Continuation | 4–5 moves |
| 03 | 5...Qxa2 Introduction | 6–8 moves |
| 04 | Qxa2 / Qxa1 | 8–12 moves |
| 05 | Qxa2 / Qxb1 | 8–12 moves |
| 06 | Qxa2 / Qxb2 | 8–12 moves |
| ... | Other common branches and deviations | 10–20 moves |
| 15–40 | Deep and difficult continuations | 15–30+ moves |

These are illustrative depths, not instructions to fabricate or extend moves. Actual lines come exclusively from author-supplied PGN.

### 4.4 Legacy-study support

An existing reference study is **Let's Learn the Wizard Opening - antichess**, https://lichess.org/study/f3NuTHGo. It contains many chapters, explanatory comments, mainlines, side variations, FEN-start chapters, and some quiz-only chapters. It was authored **before** the simplified standard. Use it as a parser/validation stress test when a PGN export is supplied; do not assume all its chapters are drillable. The user's new course will follow the chapter-per-line convention.

## 5. Antichess rules — critical engineering requirements

Use a reputable, actively maintained **variant-aware** rules implementation, or implement and rigorously test the variant rules. Investigate licensing before choosing a library or bundling an engine in a commercial app. Fairy-Stockfish is one potential engine for later evaluation/analysis, but an engine is **not required** to play back author-defined repertoire lines.

Correct rules must include:

- **Captures are compulsory.** If any legal capture exists, all noncapturing moves are illegal. A player may choose among available captures.
- **En passant** is supported and counts as a capture when legally available; mandatory-capture enforcement includes it.
- **No check or checkmate restriction.** Kings are ordinary capturable pieces; they may move into attack and be captured. There is no castling.
- **Pawn promotion may be to queen, rook, bishop, knight, or king**, consistent with the supported Lichess antichess variant. The promotion chooser must expose all five.
- **Victory condition:** the player who has no pieces remaining **or** has no legal move on their turn wins. Do not use ordinary-chess stalemate, checkmate, or insufficient-material outcomes.
- Validate variant-specific game-ending and draw/repetition behavior against authoritative Lichess antichess rules before implementing any draw adjudication. Do not inherit standard chess draw rules blindly.
- **PGN/FEN:** parse positions that may have no king, multiple kings due to promotion, or otherwise be illegal under orthodox chess assumptions. Do not require one king of each color.
- Move notation must be parsed and generated consistently with variant rules. Some Lichess antichess PGNs use `#` notation for terminal positions; don't interpret it as orthodox checkmate.
- When a legal move is **not** the repertoire's prescribed move, give repertoire feedback; do not confuse it with a rules-illegal move.
- Test captures, forced-capture priorities, en passant, promotions (including king), captures of kings, no-castling, no-legal-move victory, FEN-start lines, and import of long annotated variations.

## 6. User experience and modes

### 6.1 Course catalog

Display imported courses as cards with title, number of drillable lines, number learned/mastered, and progress. A user selects a course and sees its ordered lines. Provide an import action for PGN files and optionally pasted PGN text.

### 6.2 Learn mode

- Present the next not-yet-learned line, beginning with line 1.
- Show a nearly full-width board on a phone, White at bottom by default; later support Black-perspective courses/board flipping.
- The student plays **the repertoire side**; the trainer automatically responds with the authored opponent moves.
- Show optional author comments/explanations at the relevant move.
- Provide Hint, Restart, and navigation controls.
- On a legal but non-repertoire move, stop advancement, explain that it is not the target move, reveal or hint at the intended move, and allow retry without losing the current line. On a rules-illegal move, do not execute it.
- Mark a line learned after a successful complete rehearsal; precise thresholds can be tuned during testing.
- Show line progress and a clear completion state with Next Line.

### 6.3 Practice mode

- Rehearse previously introduced lines with reduced/no automatic hints.
- Record mistakes and successful attempts per line.
- Prioritize weak and recently failed lines over well-mastered ones.
- Let the user retry the same line or advance to another.

### 6.4 Drill mode

- Provide repeated recall practice of selected learned lines, potentially shuffled.
- Offer a useful streak/combo or mastery metric, but don't prioritize gamification over accurate training.
- The opponent follows the selected authored line, rather than choosing an unmodeled move.
- Make the active line/variation identifiable to avoid ambiguity when multiple course lines share a prefix.

### 6.5 Depth and progression

The central training progression is **breadth and depth together**: the user learns a few opening moves, then more opponent branches, then longer continuations. Course chapters encode the default sequence. The trainer should not require a user to memorize a 25-move line in one exposure.

For v1, implement chapter-by-chapter progression. If straightforward, support practice of an initial prefix or a configurable stopping depth (e.g. 3, 5, 10, full moves) without duplicating source chapters. Do **not** automatically truncate a chapter in a way that misrepresents a forced tactical sequence as a completed win. Later enhancements may include progressive unlocking within a line, partial-line drills beginning near a branching position, and position-level spaced repetition.

### 6.6 Move and line ambiguity

A course may have multiple lines sharing the same prefix. In v1, each drill selects a specific line and the opponent plays its authored moves deterministically. The student's correct move is the selected line's authored move. A later repertoire-graph system may support alternative acceptable continuations and transpositions. Do not introduce unrequested random opponent responses that strand the student outside the source line.

## 7. Data model and import pipeline

Suggested entities (adapt as needed):

- `Course`: id, title, description, source metadata, variant, ordered lines, import version.
- `TrainingLine`: id, courseId, order, chapter title, chapter URL/id, starting FEN, normalized main-line moves, annotations, source PGN metadata, validation status.
- `MoveStep`: ply index, UCI move, SAN label, FEN before/after, comment/annotation, side to move.
- `LineProgress`: lineId, introduced/learned/mastered state, attempts, correct attempts, mistakes, last practiced, next due (if implemented).
- `ImportWarning`: chapter, move index, severity, human-readable explanation.

Import pipeline:

1. Accept `.pgn` upload or pasted PGN.
2. Split multi-game PGN into Lichess chapters, preserving chapter order and metadata.
3. Verify `Variant "Antichess"` or explicitly ask for confirmation if missing; reject contradictory variants.
4. Parse initial FEN/SetUp, main line, comments, and variations as feasible.
5. Validate every main-line move under antichess rules, generating canonical move identities (prefer UCI internally) and position history.
6. Detect empty mainlines, invalid moves, unsupported annotation features, and duplicate/ambiguous chapters; report warnings without crashing the whole import.
7. Preview imported lines with chapter titles, starting positions, move counts, and errors.
8. Save valid lines locally with stable IDs and preserve progress on re-import where source chapter IDs and content identity permit. Do not silently overwrite unrelated progress.

**Important:** Chapter PGNs may begin from FENs representing a midgame position. Move numbering in the source may start at `5...`, `6...`, etc. Import must respect the FEN's active color and fullmove counter.

## 8. Technical architecture (suggested, not mandated)

- TypeScript web app, e.g. React + Vite or Next.js, with responsive UI and PWA support.
- A well-tested antichess rules adapter with clear interface (`loadFen`, `legalMoves`, `makeMove`, `gameStatus`, `parsePgn`). Separate variant logic from UI.
- PGN parsing/normalization layer that converts source chapters into validated training lines.
- Local persistence with IndexedDB (preferred for imported PGN/course data) or another robust browser storage mechanism; schema versioning and migrations.
- A clear domain layer for course progression, attempts, and mastery, independent of rendering.
- Accessible touch-friendly board interactions; support tap-to-move and ideally drag-and-drop later. Clear selected-square and legal-target highlights.
- Offline-capable installed PWA after initial installation; avoid assuming network availability during drills.
- Unit tests for variant rules and import, integration tests for training progression, and at least one end-to-end mobile viewport flow.
- Keep third-party library attribution and licensing documented for commercial use.

Choose the smallest architecture that reliably supports the first milestone. Avoid premature infrastructure and unnecessary cloud services.

## 9. Design reference

The inspiration is the **workflow** of Chessreps, not its proprietary implementation, branding, assets, or exact visual design. Relevant observed behaviors:

- Course catalog cards show title, author, description, line count, and progress.
- Learn screen has instructional text above a large board, progress indicator, navigation, and hint/mode controls.
- Incorrect move triggers explanatory feedback and Try Again.
- Completed line offers a clear Next Line action.
- Learn, Practice, and Drill are distinct modes.
- Course content unlocks progressively.

Use original visual design and code. Favor a polished, uncluttered iPhone interface with readable typography, large touch targets, and an obvious sense of progress.

## 10. Scope boundaries

**Build now:** variant-correct board and move engine; multi-chapter PGN import; ordered line catalog; Learn; basic Practice and Drill; local progress; responsive PWA; tests.

**Consider later:** Lichess study URL import; course publishing/admin dashboard; user accounts and sync; premium subscription; advanced spaced repetition by position; transposition-aware repertoire graph; personalized repertoire editor; time trials; puzzles; ratings; arena; analytics; native iOS app.

**Explicitly out of scope for v1:** separate standalone quiz builder, general-purpose chess play, engine analysis, arbitrary opponent move generation, competitive matchmaking, payments.

## 11. Acceptance criteria for the first milestone

1. App loads as a mobile-friendly PWA and can be installed on iPhone.
2. User imports a valid multi-chapter Lichess antichess PGN; valid chapters appear in original order with titles and move counts.
3. A short 2–3-move chapter and a 20+-move chapter both drill correctly.
4. The student controls the repertoire side while opponent moves are automatically replayed from the selected line.
5. Legal wrong moves produce feedback and retry; illegal moves never execute.
6. All moves are checked against **antichess**, including forced captures and king promotion.
7. Chapters starting from explicit FEN positions work correctly.
8. Empty or invalid chapters are clearly flagged, not treated as successful drills.
9. Comments display in Learn mode at the correct point.
10. Learned status, attempts, and mistakes survive page reload and offline use.
11. Automated tests cover critical variant rules, representative PGN imports, and basic progression.
12. A developer can run the app and tests using documented commands in `README.md`.

## 12. First implementation sequence

1. Initialize project, README, test runner, and PWA shell.
2. Select and verify a commercially suitable antichess rules library or implement a tested rules adapter.
3. Build antichess PGN importer and import-preview UI.
4. Build interactive board and deterministic line playback.
5. Add Learn flow, mistake feedback, comments, and Next Line.
6. Add Practice/Drill selection, local persistence, and basic progress.
7. Test on narrow mobile viewports and iPhone Safari/PWA constraints.
8. Import the author's new standardized Wizard Opening study and refine based on real usage.

## 13. Questions intentionally left open

Do not block the first milestone on these; choose sensible defaults and expose configurable settings where inexpensive:

- Course title/brand and visual identity.
- Whether the first public release permits user imports or only curator-published courses.
- Mastery thresholds, repetition schedule, and when a line counts as perfected.
- Whether comments are shown automatically or on demand in Learn mode.
- How to handle multiple acceptable winning moves beyond the author's mainline (v1 drills the authored move).
- Exact freemium limits, subscription price, and launch channel.

## 14. Immediate next action for Codex

Create the project and deliver a **working vertical slice**: import a small multi-chapter antichess PGN, select an ordered line, and complete an interactive drill on a mobile-width board with correct compulsory-capture rules. Include tests and clear run instructions. Keep the implementation modular enough to extend to Learn, Practice, Drill, and progress storage. Report any unverified variant-rule or PGN assumptions explicitly instead of papering over them.
