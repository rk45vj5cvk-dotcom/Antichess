# Antichess Opening Trainer

An early, browser-local opening rehearsal app for Lichess antichess PGN studies. Each chapter's validated main line becomes one exercise; variations remain in the original PGN and are not answers. No account or backend is required for importing or practicing. The student plays White; Black follows the selected chapter. Pieces can be moved by tapping a source and destination or by dragging with a mouse, finger, or stylus.

## Run locally

Requires Node.js 18+ and Python 3. From this folder:

```sh
npm test
npm start
```

Open http://localhost:8000. The service worker caches the app for offline use after the first visit. On iPhone, open the deployed HTTPS URL in Safari and use Share → Add to Home Screen. Courses and progress are kept in the browser's local storage; clearing site data removes them, and they do not sync between devices.

## Import

Export the study from Lichess as PGN with variations and comments, then select Import a study. Missing variant tags require explicit confirmation; other variant tags are rejected. Check the preview for invalid, empty, and unsupported chapters. A sample course is available on the empty home screen.

## Scope and limitations

This is an initial vertical slice, not a finished commercial release. It includes a small in-house antichess move generator and a focused PGN parser rather than a third-party rules engine. Core moves, compulsory captures, en passant, promotion including king, capturable kings, no castling, and no-move victory have unit coverage. Additional independent verification with real Lichess exports and exhaustive variant edge cases is needed before relying on it. Repetition and draw adjudication are deliberately not implemented. The parser is intended for common Lichess PGN, not every possible PGN dialect. Author comments are shown in Learn mode, but complex annotation placement is not fully supported. It currently trains White only; practice and drill are basic, and partial-depth learning and advanced spaced repetition are not included. Progress is keyed by chapter content: editing a chapter changes its identity. A course with the same derived identity is replaced on re-import, while unrelated progress is retained. No third-party packages are bundled.

## Artwork credits

The classic chess pieces are by Colin M. L. Burnett (Cburnett) and contributors, from the [Wikimedia Commons standard transparent chess piece set](https://commons.wikimedia.org/wiki/Category:SVG_chess_pieces/Standard_transparent). Each file is offered under multiple licenses; this project uses them under the 3-clause BSD license. See [the bundled artwork license](dist/assets/cburnett/LICENSE.txt). The square colors are drawn by the app and inspired by Lichess's brown board. Lichess's own distribution of its cburnett files is listed under GPLv2+; these files are sourced separately from Wikimedia Commons under the BSD option.
