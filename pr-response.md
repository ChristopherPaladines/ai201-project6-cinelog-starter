PR Response Doc — CineLog Watchlist Feature

Comment 1 — Rename

What I did: Renamed save_to_watchlist() to add_to_watchlist() in services/watchlist_service.py, and updated the one call site in routes/watchlist/watchlist.py (both the import statement and the function call inside add_film()).
How I verified: Ran a project-wide search (Cmd+Shift+F in VS Code) for save_to_watchlist after making the changes — zero results remained, confirming no call sites were missed. Ran pytest tests/ -v to confirm the existing test suite still passed.

Comment 2 — Deduplication

What I did: Added a new AlreadyInWatchlistError exception class (mirroring AlreadyInCollectionError in collection_service.py), and added a dedup check inside add_to_watchlist(): before creating a new WatchlistEntry, query for an existing entry matching the same user_id and film_id, and raise AlreadyInWatchlistError if one is found.
How I verified: Modeled the check directly on add_to_collection()'s existing dedup logic in collection_service.py. Ran pytest tests/ -v to confirm nothing broke, then added a dedicated test (test_add_to_watchlist_duplicate_raises) to confirm the new behavior — adding the same film twice raises the error and only one row exists in the database afterward.

Comment 3 — Missing test

What I did: Created tests/test_watchlist.py, mirroring the fixture and test structure from tests/test_collection.py. Added test_add_to_watchlist_nonexistent_film_raises, modeled directly on test_add_to_collection_nonexistent_film_raises — asserts that calling add_to_watchlist() with a fake film ID raises FilmNotFoundError. Also added test_add_to_watchlist_creates_entry and test_add_to_watchlist_duplicate_raises to cover the happy path and dedup case, following the brief's guidance that new service functions should have tests for the happy path, duplicate/conflict handling, and a nonexistent ID.
How I verified: Ran pytest tests/test_watchlist.py -v — all 3 tests passed. Ran the full suite (pytest tests/ -v) to confirm no regressions in the collection tests.

Comment 4 — Default visibility

My position:
Reasoning:
Tradeoff acknowledged:

Comment 5 — Sort order

My position:
Reasoning:
Engagement with reviewer's point:

Comment 6 — Rebase

What conflicted:
How I resolved it:
How I verified no conflict remains: