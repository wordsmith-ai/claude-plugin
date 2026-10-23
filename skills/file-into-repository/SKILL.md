---
name: file-into-repository
description: Create a Wordsmith repository, organise it into folders, add documents to it so they become searchable, and remove a document. Use when the user asks to file, store or organise contracts and other documents in Wordsmith, or to set up a repository for a matter or contract type.
---

# Filing documents into a Wordsmith repository

A repository is a searchable collection of an organisation's documents in Wordsmith, such as executed
contracts or precedents. Wordsmith's `manage_repository` tool creates repositories and folders and adds and
removes documents. Wordsmith's `search_knowledge` tool then searches what is filed.

## 1. Find or create the repository

- **An existing one:** call `find_resources` with `kind="repository"` and a `query`. `get_resource` with
  `kind="repository"` and `include=["folders"]` or `include=["documents"]` lists its contents.
- **A new one:** call `manage_repository` with `action="create"`, a `name` and optionally a `description`.
  It returns the `repository_id`.

To organise documents, call `manage_repository` with `action="create_folder"`, the `repository_id`, a
`name`, and a `parent_id` for a folder inside another folder.

## 2. Get the documents into Wordsmith

Each document needs a Wordsmith file id. Call `add_file` for an attachment, a public https link or text.
For a file on the user's machine, call `create_file_upload`, run its `curl_command`, then call
`complete_file_upload`.

## 3. Add them to the repository

Call `manage_repository` with `action="add_files"`, the `repository_id`, up to 100 `file_ids`, and an
optional `folder_id`. Each file moves into the repository and becomes a searchable document.

Indexing runs in the background. If the result has an `operation_id`, call `get_operation` until the
`repository_indexing` operation finishes. The documents are searchable after that.

## 4. Search what is filed

Call `search_knowledge` with a `query` and the `repository_ids` to search. Each passage it returns carries
the `document_id`, the `file_id` and a relevance score. `read_file` with the `file_id` and
`include=["text"]` returns the document's text a page at a time.

## Removing a document

`manage_repository` with `action="remove_document"` takes the repository's `document_id`, not the
`file_id`, and deletes both the document and its file. This tool cannot delete a whole
repository.

## Example

A user attaches three signed supply agreements and asks to file them under a new "Supply agreements"
repository, in a "2026" folder:

1. `manage_repository(action="create", name="Supply agreements")` returns `repo_1`.
2. `manage_repository(action="create_folder", repository_id="repo_1", name="2026")` returns `fld_2`.
3. `add_file` for each attachment returns `f_a`, `f_b` and `f_c`.
4. `manage_repository(action="add_files", repository_id="repo_1", folder_id="fld_2", file_ids=["f_a", "f_b", "f_c"])`
   returns an `operation_id`; `get_operation` reports when indexing is done.
