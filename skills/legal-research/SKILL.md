---
name: legal-research
description: Search case law and legislation in public legal databases through Wordsmith, and read the full text of a judgment, statute or regulation. Use when the user asks for cases, statutes, regulations or EU law on a legal point, or for the text of a specific judgment or Act.
---

# Legal research with Wordsmith

Wordsmith's `legal_research` tool searches several public legal sources at once and reads documents from
them. It finds and reads primary sources. For a researched answer or memo, Wordsmith's `ask_wordsmith`
tool hands the question to a Wordsmith agent instead.

## Sources

| Source | Covers |
| --- | --- |
| `moonlit` | Legislation in 30+ jurisdictions, strongest in civil-law Europe |
| `courtlistener` | US case law |
| `uk_case_law` | UK judgments |
| `uk_legislation` | UK Acts and statutory instruments |
| `nz_legislation` | New Zealand legislation |
| `eur_lex` | EU law and CJEU judgments |
| `federal_register`, `regulations_gov` | US federal rules and rulemaking |
| `sec_edgar` | US SEC filings (search only) |

## 1. Search

Call `legal_research` with `action="search"` and a `query` in plain language or as a citation, such as
`"[2015] UKSC 72"`. Narrow it with:

- `jurisdictions`: lower-case ISO country codes plus `"eu"`, for example `["gb"]`
- `sources`: a subset of the sources above. The default searches every source the user can use
- `date_from` and `date_to`: `YYYY-MM-DD`
- `limit`: up to 50 results

The result lists matches from every source, each with a `source` and `document_id`. It also reports each
source's status. `timeout` means that source was slow and returned nothing. `partial` means some of its
matches may be missing, so a repeat search with only that source can fill the gap.

## 2. Read a document

Call `legal_research` with `action="read"`, the result's `source` and `document_id`. `max_chars` sets how
much text comes back (default 20,000). A non-null `next_offset` means there is more: call again with
`offset=next_offset`.

`sec_edgar` results cannot be read. Their `url` opens the filing on the SEC's site.

## Example

A user asks for the leading UK authority on the penalty rule in liquidated damages clauses:

1. `legal_research(action="search", query="penalty rule liquidated damages legitimate interest", jurisdictions=["gb"], sources=["uk_case_law"])`
2. The results include *Cavendish Square Holding BV v Makdessi* `[2015] UKSC 67`.
3. `legal_research(action="read", source="uk_case_law", document_id=<its document_id>)` returns the
   judgment text, with `next_offset` for the next part.
