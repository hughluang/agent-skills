---
name: grill-on-prototype
description: Grill an open decision on one HTML page that shows the implementation skeleton and the logic diagram, update that page every round, and write the spec only after the user confirms it.
license: MIT
compatibility: "Needs a host with subagents and a browser. Depends on the research skill and the grilling skill. Invoke explicitly."
metadata:
  author: hughluang
  version: "1.0.0"
disable-model-invocation: true
---

# Grill on a prototype

Use this when an open decision should become a spec and the user cannot yet point at what the words mean. The picture is the shared object. Words name a place already on the picture.

The run is background, picture, rounds, spec. Product code is a later run. The picture is one HTML file beside the spec. It is not a route in the product.

Read and follow the research skill and the grilling skill. Drawing and tests are [prototype-html.md](prototype-html.md). Opening the file is [open.md](open.md).

## 1. Name the open decision

Read the spec and the domain docs it cites. Separate facts already settled from the decision still open. Settled facts stay settled.

Done when one sentence names the decision the user would accept or reject.

## 2. Background

When the decision depends on practice outside the repo, follow the research skill.

Split the question into distinct source paths. One background agent per path. A claim enters the notes only when its page opened. A page that did not open is absent from the notes.

Write one synthesis in the feature directory. It points at the path notes and does not replace them.

Done when the synthesis is on disk and every claim in it names an opened source.

Skip this step when the open decision is already inside the repo's docs and code. Say that you skipped it.

## 3. What the picture holds

The file has two regions, both visible at once.

**Skeleton.** The screen the implementation would add or change: its structure, hierarchy, controls, and the words a person would read there. When that structure is still open, this region holds two or three versions that differ in structure, hierarchy, or primary action. The control that switches versions stays inside this region.

**Logic.** The named parts and the relations among them that the decision turns on. A relation's visible text is the domain name of that relation. Each open relation is a place on this diagram. Show the relations still open, and no extra relations to fill the page.

When the implementation adds no screen, the skeleton is the existing place a person already uses, and the words on it say what the implementation does there.

Done when the packet names the skeleton and the logic in one line each.

## 4. Draw

Delegate the picture to one subagent. Paste prototype-html.md with the round packet: the HTML path, the open decision, the skeleton and its open versions or the one the user already pointed at, the logic parts and relations still open or the one the user already pointed at, the settled facts that change how that decision is read, the language of the conversation, and on a later round the answers just given. A settled fact that does not change the reading stays out of the packet. The parent writes no HTML.

Wait until the subagent finishes. Apply the tests in prototype-html.md. A failed or undecided test goes back to the same subagent. Wait again.

Then follow open.md. Done when the file is on disk and open.md has loaded it.

## 5. Rounds

Follow the grilling skill. One round is the whole frontier: numbered questions, one recommended answer each, then wait.

A question names a place on the skeleton or on the logic diagram. When skeleton versions are on the page, choosing among them is on this frontier, and a question that assumes one version waits. The same holds for an open relation.

A fact you can read in the repo goes into the round packet and is stated in the question. A decision is asked. Ask a settled decision again only when the user reopens it.

After each answer that changes what the picture should show, resume the same subagent. Paste prototype-html.md again with the new round packet. The parent does not edit the HTML. Wait until it finishes. Apply the tests in prototype-html.md, and send one correction when a test fails or does not decide. Then ask the next frontier.

Done when the user has answered and the picture matches those answers.

## 6. Spec

When no decision remains, ask the user to confirm the picture. Write the spec only after they confirm.

Follow the repo agent guide for the spec's path, status, and language. Record the confirmed skeleton, the confirmed relations, the reads, the copy that was locked, and what stays out. Leave product code unchanged.

Done when the spec's status is the tracker's ready label and a reader can implement it without reopening a settled decision.
