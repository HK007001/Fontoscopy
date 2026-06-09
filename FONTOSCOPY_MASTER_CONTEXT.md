FONTOSCOPY — MASTER PROJECT CONTEXT
(Read This First Before Doing Any Development Work)

This document is intended for future ChatGPT sessions.

Treat this as the authoritative design philosophy and project direction.

Do not blindly follow existing code if it conflicts with the principles below.

Project Identity

Project Name:

Fontoscopy

Current slogan:

Ultimate Font Tester

Potential future positioning:

Writing-System QA Platform

or

Language-Aware Font Validation Platform
Original Goal

Originally Fontoscopy was envisioned as a powerful font testing and validation application.

However, during development, a major realization emerged:

Most font validators focus on:

Font internals

while users actually care about:

Writing correctness

This changes everything.

Most Important Insight

The single most important discovery of the project:

A font should be judged by what it can successfully write.

NOT by:

number of Unicode characters
number of OpenType features
number of tables

BUT by:

Can the language be written correctly?

This must become the core philosophy of Fontoscopy.

Current Problem

The current application behaves too much like:

OpenType Table Inspector

instead of:

Writing-System Validator

This causes serious scoring inaccuracies.

Major Problems Discovered
Problem 1

Professional fonts receive F grades.

Observed with fonts from:

Google
Adobe
Linotype
other major foundries

These fonts work perfectly in real-world software.

Yet Fontoscopy often grades them poorly.

This means:

Current score ≠ Actual quality

This is unacceptable.

Problem 2

Feature Presence Bias

Current logic appears to be:

Feature missing
↓
Penalty

Examples:

No GSUB
No GPOS
No blwf
No haln

↓

FAIL

This is flawed.

Because:

The font may still render perfectly.

Required New Logic

Instead of:

Feature missing
→ FAIL

Use:

Feature missing
↓
Test behavior
↓
Behavior works?
↓
PASS

Behavior is more important than implementation details.

Philosophy Rule #1
Behavior > Feature Presence

Always.

Example:

Font lacks:

blwf

But:

ক্ষ
জ্ঞ
স্ত্র

render correctly.

Then:

No penalty

or

Tiny engineering penalty only
Philosophy Rule #2
Functional Quality > Engineering Quality

Separate scores.

Never combine them blindly.

Functional Score

Measures:

Does the font work?

Examples:

conjuncts
marks
shaping
reph
ya-phala
Arabic joining
Engineering Score

Measures:

How well was the font built?

Examples:

table structure
OpenType organization
metadata
optimization

Example:

Functional: 98

Engineering: 62

Overall: A

Perfectly valid.

Philosophy Rule #3
Writing Capability > Unicode Coverage

This is critical.

Current coverage logic is flawed.

Example

Current approach:

Bangla Unicode Block
128 codepoints

Font:
92 codepoints

Coverage:
72%

This is not meaningful.

Instead ask:

Can Bangla be written correctly?

Coverage must be language-aware.

Philosophy Rule #4
Language Awareness > Font Awareness

Fontoscopy must understand:

Bangla
Arabic
Devanagari
Sinhala
Khmer
Myanmar

Not merely:

GSUB
GPOS
cmap
Essential Glyph Problem

Current "Essential Glyph" concept is likely wrong.

Why

Many validators define:

Essential Glyphs
=
Unicode characters

This is incorrect for complex scripts.

Example

Bangla:

ক্ষ

has no Unicode codepoint.

Yet it is more important than many rare encoded characters.

Therefore:

Replace:

Essential Glyphs

with:

Script Essentials
Bangla Script Essentials

Must include:

Base Letters
অ আ ই ঈ উ ঊ
Consonants
ক খ গ ঘ ঙ ...
Vowel Signs
া ি ী ু ূ ৃ ে ৈ ো ৌ
Virama
্
Numerals
০১২৩৪৫৬৭৮৯
Reph
Ya-phala
Common Conjuncts
Rare Conjuncts
ZWJ
ZWNJ
Coverage Redesign

Remove:

Unicode Coverage Score

as primary metric.

Replace with:

Character Coverage

AND

Writing Coverage

Example:

Character Coverage:
100%

Conjunct Coverage:
98%

Shaping Coverage:
97%

Bangla Capability:
98%
Major Discovery

Unicode coverage alone is almost useless for complex scripts.

Example

Font A:

96 Unicode chars
0 conjunct support

Current system:

100%

Reality:

Terrible font

Font B:

96 Unicode chars
285 conjuncts

Current system:

100%

Reality:

Excellent font

Fontoscopy must detect this difference.

Unencoded Glyph Awareness

Current system may ignore:

conjunct glyphs
ligatures
alternates

that lack Unicode assignments.

This is wrong.

These glyphs are often the reason complex scripts work.

Fontoscopy must account for them.

New Primary Metric

Replace:

Coverage Score

with:

Script Capability Score
Bangla Capability Categories
Base Letters
Marks
Numerals
Reph
Ya-phala
Common Conjuncts

Target:

≈285 common conjuncts
Rare Conjuncts

Lower weight.

ZWJ
ZWNJ
Mark Positioning

Example output:

Bangla Capability

Base Letters ......... PASS
Marks ................ PASS
Reph ................. PASS
Ya-phala ............. PASS
Common Conjuncts ..... 282/285
Rare Conjuncts ....... 910/960

Capability Score ..... 97%
Long-Term Vision

Fontoscopy should evolve from:

Font Validator

into:

Writing-System QA Platform
Core Competitive Idea

Most validators ask:

Does the font contain feature X?

Fontoscopy should ask:

Can the language actually be written?

This is the key differentiator.

What NOT To Focus On

Until validation quality improves:

Avoid prioritizing:

new themes
animations
visual effects
dashboards
cosmetic UI

The project already has enough UI.

Focus on validation accuracy.

V9 Mission

Highest priority.

Goals
Fix scoring philosophy
Fix coverage philosophy
Fix essential glyph philosophy
Separate engineering and functional scores
Reduce feature-presence penalties
Add Bangla Capability framework
HarfBuzz

Future major milestone.

Current validator only sees browser output.

Need:

Browser
vs
HarfBuzz

comparison.

Future:

Browser
vs
HarfBuzz
vs
DirectWrite
vs
CoreText
Corpus Testing

Future major feature.

Instead of isolated glyph testing:

Use real language.

Sources:

Newspapers
Books
Wikipedia
Government documents

Example:

100,000 Bangla words

Measure:

Pass
Fail
Coverage
Visual Regression

Future feature.

Compare:

Version A
Version B

Detect:

shaping changes
glyph changes
spacing changes
Explainability

Never show:

FAIL

without explanation.

Always explain:

Reason

Affected words

Expected

Actual
Production Readiness

Future score.

Example:

Web ........ PASS
Android .... PASS
Windows .... PASS
Office ..... PASS
EPUB ....... PASS

Production Readiness:
98%
Expansion Order

After Bangla:

Arabic
Devanagari
Sinhala
Khmer
Myanmar
Thai
Lao

Bangla remains first priority.

Architecture Strategy

Current phase:

Single-file development.

Examples:

fontoscopy-v8.html
fontoscopy-v9.html
fontoscopy-v10.html

This is preferred right now.

Future:

Modular architecture.

Example:

/js
/data
/tests
Development Workflow

Recommended process:

Upload latest version.
Identify one target improvement.
Implement.
Save new version.

Example:

v8
↓
v9
↓
v10

Never modify old versions.

Always create new versions.

What ChatGPT Must Remember

When helping with Fontoscopy:

Never assume:

More Unicode
=
Better font

Never assume:

Missing feature
=
Bad font

Never assume:

Table presence
=
Functionality

Always prioritize:

Can the writing system actually function?
Final Mission Statement

Fontoscopy is not trying to become the largest font dashboard.

Fontoscopy is trying to answer a much harder question:

Can this font correctly support the writing system it claims to support?

Every feature, score, report, and future design decision should be evaluated against that mission.

If a metric does not help answer that question, it is secondary.