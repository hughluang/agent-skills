# Prototype HTML

The parent pastes this file into the prototype subagent's prompt. That subagent does not see the conversation. The parent also sends the round packet: the HTML path, the open decision, the skeleton and its open versions or the one already chosen, the logic parts and relations still open or the one already chosen, the settled facts that change how the decision is read, the language of the conversation, and on a later round the answers just given plus the facts to draw.

The parent writes no HTML. The prototype subagent starts no server.

## Result

A person who has not read the spec can point at the open decision on either region and say what the choice is. The words printed on that place are the words of the decision. The file is one self-contained HTML page beside the spec, with UI copy in the language of the conversation, marked throwaway, and hostable by a static server. Product routes stay untouched.

The page shows two regions at once. Each region has a heading in the language of the conversation, one for the implementation skeleton and one for the logic relations. Hiding either region behind a switch fails.

**Skeleton.** Draw the screen the implementation would add or change, at skeleton fidelity: structure, hierarchy, controls, and the words a person would read there. Use the product's chrome only so a person can tell where this screen lives. When the packet lists versions, put two or three in this region. They differ in structure, hierarchy, or primary action. The switcher sits inside the region and names each version by that difference. When the packet names one version, draw that version. When the implementation adds no screen, draw the existing place a person already uses, and print on it what the implementation does there.

**Logic.** Draw the named parts and the relations among them. A part is a region whose visible text is its domain name. A relation is a connector whose visible text is the domain name of that relation. An unsettled relation stays as an open place, and the question is the text of that place. When the packet names one relation, draw that relation.

Draw a settled fact only when it changes how the open decision is read. The page is ordinary HTML: headings, regions, lists, tables, forms, and buttons. A connector is a line with its relation text on it.

When the decision is what a person sees after they act, operating the control changes the skeleton. When that action changes a relation, the logic region marks the relation that changed.

## Tests

**Both.** The skeleton region and the logic region are visible together. Removing either region fails.

**Read.** Cover any legend, essay, and status line. The words still printed on the places state the choice in the words of the decision. A place whose text is a nickname for a symbol fails. A connector without its relation's own words fails. A page that repeats the spec and has no distinct place for the decision fails. The parent fails the file when the choice is readable only by using knowledge that is not printed on the page.

**Place.** Removing an element either removes a place the user can point at, or the element does not belong. The page uses the system font stack, inline CSS, and no external asset or embedded font.

**Action.** When the decision is what a person sees after they act, operating the control changes that place on the skeleton and, where the relation depends on the action, marks it on the logic region. When the decision is which version or which relation, choosing it marks that one in its region. A control that only changes a sentence fails.

**Round.** On a later round, the same tests hold for the new packet. A place that still shows the previous answer fails.

When a test does not decide, it fails. The parent sends the file back to the same subagent.
