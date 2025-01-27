# citeproc changelog

## 0.4.1

  * Change Pandoc `inNote` so it creates a `Span` with class `csl-note`
    rather than a `Note`.  This should make it easier to integrate
    citations with ordinary notes in pandoc.
  * Do not hyperlink author-only citations (#77).  If we do this we get
    two consecutive hyperlinks for author-in-text forms.
  * `movePunctuationInsideQuotes`: only move `,` and `.`, not `?` and `!`,
    as per the CSL spec.

## 0.4.0.1

  * Fix bug introduced by the fix to #61 (#74).
    In certain circumstances, we could get doubled "et al.".
  * Depend on unicode-collation unconditionally (#71).  It is necessary
    even when text-icu is used, because of Text.Collate.Lang.
  * Rename tests in extra/ so they fall into categories.

## 0.4

  * We now use Lang from unicode-collation rather than defining our own.
    The type constructor has changed, as has the signature of
    parseLang.
  * Use unicode-collation by default for more accurate sorting.
    - text-icu will still be used if the icu flag is set.  This may
      give better performance, at the cost of depending on a large
      C library.
    - Change type of SortKeyValue so it doesn't embed Lang. [API change]
      Instead, we now store a language-specific collator in the Eval Context.
    - Move compSortKeyValues from Types to Eval.
  * Add curly open quote to word splitters in normalizeSortKey.
  * Improve date sorting: use the format YYYY0000 if no month, day,
    and YYYYMM00 if no day when generating sort keys.
  * Special treatment of literal "others" as last name in a list (#61).
    When we convert bibtex/biblatex bibliographies, the form "and others"
    yields a last name with nameLiteral = "others".  We detect this and
    generate a localized "and others" (et al).
  * Make abbreviations case-insensitive (#45).

## 0.3.0.9

 * Implement `et-al-subsequent-min` and `et-al-subsequent-use-first` (#60).

## 0.3.0.8

 * In parsing abbreviations JSON, ignore top-level fields
   besides "default" (#57), e.g. "info" which is used in Zotero's
   default abbreviations file.

## 0.3.0.7

  * Remove check for ASCII in case transform code.
    Previously we weren't doing case transform on words
    containing non-ASCII characters.

## 0.3.0.6

  * Fix infinite loop in `fixPunct` (#49).  In a few rare cases
    `fixPunct` would hang.

## 0.3.0.5

  * Add a space between "no date" term and disambiguator
    if the long form is used (#47).

## 0.3.0.4

  * Improve disambiguation code.  Add type signatures,
    move some functions to the top-level, and make the
    logic clearer and more efficient.
  * Re-render after each stage of ambiguity resolution
    instead of relying on analysis of names and dates.
    This is necessary especially for styles like
    chicago-note-bibliography which use titles in
    citations.  Closes #44.  No measurable
    performance impact.
  * Update test suite from upstream.
  * Update `it-IT` locale.

## 0.3.0.3

  * Fix author-only citations (#43).  We got bad results with some
    styles when a reference had both an author and a translator.

## 0.3.0.2

  * Don't use cite-group delimiter if ANY citation in group has
    locator (#38).  This seems to be citeproc.js's behavior and it gives
    better results for chicago-author-date:  we want both
    `[@foo20; @foo21, p. 3]` and `[@foo20, p. 3; @foo21]` to produce
    a semicolon separator, rather than a comma.

## 0.3.0.1

  * Better handle `initialize-with` that ends in a nonbreaking space.
    In this case, citeproc should not add an additional space
    or strip the nonbreaking space.  Closes #37.


## 0.3

  * Change `makeReferenceMap` to return a cleaned-up list of
    references as well as a reference map.  The cleanup-up list
    removes references with duplicate ids.  When there are multiple
    references with the same id, the last one is included and
    the others discarded.  [API change]

## 0.2.0.1

  * FromJSON for Name: make straight quotes curly.
    Otherwise nothing will do this, when we are decoding
    JSON to (Reference a), a /= CslJson Text.
  * Remove redundant pragmas and imports (Albert Krewinkel).
  * Use custom prelude with GHC 8.6.* and older (Albert
    Krewinkel).  This adds support for GHC 8.0.x.

## 0.2

  * Remove `AfterOtherPunctuation` constructor from
    `CaseTransformState` [API change].
    This gave bad results with things like parentheses (#27).
  * Change `SortKeyValue` to include `Maybe Lang` [API change].
    This allows us to do locale-sensitive sorting (though this
    won't matter much unless the `icu` flag is used).
  * Add `Maybe Lang` parameter on `initialize` (since
    capitalization can be locale-dependent).
  * Add cabal.project.icu for building with icu lib.
  * Add (unexported) Citeproc.Unicode compatibility module.
    This allows us to use the same functions whether or not
    the `icu` flag is used.

## 0.1.1.1

  * Pay attention to citationNoteNumber in computing position.
    In calculating whether an item is alone in its citation,
    we need to take into account citationNoteNumber, since
    two citations may occur in the same note and they should
    not be ranked "alone." See jgm/pandoc#6813,
    citation-style-language/documentation#121

## 0.1.1

  * Ensure that uncited references are sorted last
    when it comes to assigning citation numbers (#22).
  * Remove "capitalize initial term" feature.  This is required by
    the test suite but not the spec.  It makes more sense for us to do
    this capitalization in the calling program, e.g. pandoc.  For some
    citations in note styles may already be in notes and thus not
    trigger separate footnotes.  If initial terms had been capitalized,
    we'd need to uncapitalize, and that is hard to do reliably.
  * Treat empty `FancyVal` as an empty value.
  * Derive Functor, Traversable, Foldable for Result [API change].

## 0.1.0.3

  * Better handling of author-only/suppress-author.
    Previously all results of "names" elements were treated
    as authors.  But only the first should be (generally this
    is the author, but it could be the editor of an edited
    volume with no author).  See jgm/pandoc#6765.

## 0.1.0.2

  * Don't enclose contents of e:choose in a Formatted element (#19).
    The e:choose element is "transparent" and the delimiter
    controlling its formatting should be inserted between
    the items it returns.

## 0.1.0.1

  * Fix sorting when no `<sorting>` element given. The spec says:
    "In the absence of cs:sort, cites and bibliographic entries appear in
    the order in which they are cited." This affects IEEE in particular.  See
    jgm/pandoc#6741.

  * Improve `sameNames` and citation grouping.  Preivously if a citation
    item had a prefix, it would not be grouped with following citations.
    See jgm/pandoc#6722 for discussion.

  * Remove unneeded `hasNoSuffix` check in `sameNames`.

  * Remove unneeded import

  * `citeproc` executable: strip BOM before parsing style (#18).

## 0.1

  * Initial release.

