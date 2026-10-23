---
name: contract-review
description: Review a contract in Wordsmith against one of the organisation's playbooks or against checks built from the user's instructions, read the findings, and produce a tracked-changes redline. Use when the user asks to review, check or mark up a contract, NDA, agreement or other legal document with Wordsmith.
---

# Contract review with Wordsmith

This workflow takes a contract from the conversation to a reviewed set of findings and a Word redline. A
review runs in Wordsmith for a few minutes, so it starts and finishes in separate tool calls.

## 1. Get the contract into Wordsmith

Every Wordsmith tool works on a Wordsmith file id.

- **An attachment in the conversation, a public https link, or text:** call `add_file`. Set exactly one of
  `file`, `url` or `text`. It returns the file's `id`.
- **A file on the user's machine, when the host can run shell commands:** call `create_file_upload` with
  `file_name`, `content_type` and `content_length`. Run the `curl_command` it returns, with `<path>`
  replaced by the file's path. Then call `complete_file_upload` with the `upload_id` to get the file's `id`.

A review accepts DOCX and PDF files, up to 10 reviewed together.

## 2. Choose what to review against

- **A playbook:** the organisation's saved positions for a contract type. Call
  `find_resources` with `kind="playbook"` and a `query` such as `"NDA"` to find its id.
- **Instructions:** if no playbook fits, the review builds its checks from written instructions, such as
  "check the limitation of liability, governing law and termination for convenience".

Note which side the user acts for, as the document names it (for example "the Supplier"). The review
reads the contract from that party's position.

## 3. Start the review

Call `review_document` with:

- `type`: `"playbook"` with `playbook_id`, or `"instructions"` with `instructions`
- `file_ids`: the ids from step 1
- `party`: the side the user acts for

The call waits up to `wait_seconds` (at most 20). If the result has an `operation_id` and a running status,
call `get_operation` with it until the status is final. The finished result carries the `review_id`.

## 4. Read the findings

Call `get_review` with the `review_id`. It returns the review's status, its finding counts, the
`redline_file_id` of the full redline, and a page of findings. Each finding has an explanation, the cited
contract text and any proposed amendment.

- `finding_status` defaults to `"issue"`. Use `"all"` to include passed checks.
- `severity` filters issues to `"high"`, `"medium"` or `"low"`.
- Pass the returned `cursor` to read the next page.

## 5. Produce a redline

The review's own redline, with every proposed amendment, is the `redline_file_id` from `get_review`.

For a redline with only some amendments, call `update_review` with `action="generate_redline"`, the
`review_id` and the chosen `finding_ids`. It returns a new `file_id`: a tracked-changes DOCX, or a ZIP of
DOCX files when the review covered several documents. If it returns an `operation_id`, finish it with
`get_operation`.

To give the user the document, call `read_file` with the file id and `include=["download_url"]`. The link
works without sign-in and expires after 5 minutes.

## Example

A user attaches a supplier's NDA and asks for a review against the company NDA playbook, acting for the
customer:

1. `add_file(file=<attachment>)` returns `id: "f_123"`.
2. `find_resources(kind="playbook", query="NDA")` returns the playbook `pb_456`.
3. `review_document(type="playbook", playbook_id="pb_456", file_ids=["f_123"], party="the Customer")`
   returns an `operation_id`; `get_operation` returns `review_id: "rv_789"`.
4. `get_review(review_id="rv_789", severity="high")` lists the high-severity issues.
5. `update_review(review_id="rv_789", action="generate_redline", finding_ids=[...])` returns the
   redline's `file_id`.
