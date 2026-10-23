---
name: draft-from-blueprint
description: Draft a Word or PDF document in Wordsmith from one of the organisation's blueprints (document templates with placeholders), filling the placeholders from known values, source documents or a past conversation. Also drafts a new document from instructions when no blueprint fits. Use when the user asks to draft, prepare or generate an agreement, letter or other document with Wordsmith.
---

# Drafting from a blueprint with Wordsmith

A blueprint is a Wordsmith document template with placeholders, such as the party names, the effective date
or the governing law. Wordsmith's `draft_document` tool fills a blueprint, or drafts a new document from
instructions when there is no blueprint.

## 1. Find the blueprint

Call `find_resources` with `kind="blueprint"` and a `query`, such as `"mutual NDA"`. To see a blueprint's
placeholders and their keys, call `get_resource` with `kind="blueprint"` and its id.

## 2. Gather the inputs

`draft_document` fills placeholders from any mix of these:

- `values`: the placeholder values already known, keyed by placeholder key
- `context`: facts to draft from, up to 20,000 characters
- `file_ids`: source documents, such as a term sheet. Add them with `add_file` first
- `session_id`: a past Wordsmith conversation to draft from. Find one with `find_resources(kind="session")`

When `values` covers every required placeholder, Wordsmith fills the blueprint from them alone. Otherwise
it drafts the missing values from the context, files or session.

## 3. Draft

Call `draft_document` with `blueprint_id` and the inputs above. Optional: `file_name` (without extension)
and `output_format` (`"docx"` by default, or `"pdf"`).

Without a blueprint, pass `instructions` describing the document, plus any `context` or `file_ids`.

The call waits up to `wait_seconds` (at most 20). If it returns an `operation_id`, call `get_operation`
until the status is final.

## 4. Check and share the draft

The result holds:

- `file_id`: the new document. `read_file` with `include=["text"]` returns its text, and with
  `include=["download_url"]` a link that expires after 5 minutes
- the `values` Wordsmith used for each placeholder, including the ones it drafted, for the user to check
- `url`: opens the draft in Wordsmith

## Example

A user asks for a mutual NDA with a named counterparty, starting today, from the term sheet they attached:

1. `find_resources(kind="blueprint", query="mutual NDA")` returns `bp_12`.
2. `add_file(file=<term sheet>)` returns `f_34`.
3. `draft_document(blueprint_id="bp_12", values={"counterparty_name": "Example Holdings Ltd"}, file_ids=["f_34"])`
   returns an `operation_id`; `get_operation` returns the `file_id`, the `values` used and the `url`.
