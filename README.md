# pybb-ledger — evidence repository and ledger mirror

Durable store and offsite mirror for the pybb attestation ledger
(see pybb `docs/ledger.md`, design in `docs/ledger_plan.md` section H).

Layout (content-addressed, append-only by convention):

    blobs/<sha256>.json.gz           evidence packages, appraisal packages, component lists, provision bundles
    ledger/journal.jsonl             one line per ledger transaction: {tx, ts, db, keys: {key: content_sha256}}
    ledger/records/<nid>/...         the ledger's records, canonical JSON (nonce, evidence, verdict, appraisal-<seq>, locator-<seq>)
    ledger/bundles/<id>.json         bundle records
    ledger/state/<tx>.json           the ledger head at each push
    ledger/backups/                  immuadmin hot-backup segments (operator-produced)
    runs/<archive_dir>/manifest.json push-time grouping: protocol -> nid -> blobs

Nothing under blobs/ or ledger/records/ is ever modified; a correction is a
new record. Every record file re-hashes to its journal entry, every blob to
its name. Restore: `python examples/ledger_restore.py verify|replay|exact` in pybb.
Private: provision bundles contain raw model bytes.
