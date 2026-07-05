# Code Review Notes

Fill this in as you work through the milestones. Each section mirrors the structure of a real GitHub pull request review.

---

## PR #1 — Bulk Purchase (`pr1_bulk_purchase.py`)

### Summary
*What does this PR do? (1–2 sentences in your own words)*

> The PR adds a function that marks every item in the grocery list as purchased. This is a POST request.

### Issues

For each issue you find, note: where it is (file + function), what's wrong, and why it matters in production.

**Issue 1**
- Location: "items = Item.query.filter_by(list_id=list_id).all()" ( services/list_service.py)
- What's wrong: Code returns ALL items in the list (not just unpurchased).
- Why it matters: Purchased items have their set history of when it was bought and who bought the item. If that is changed, that data is lost.
- Suggested fix: Add "is_purchased=False" as a parameter in filter_by()

**Issue 2**
- Location: routes/lists.py
- What's wrong: Code doesn't check if user_id is valid.
- Why it matters: Routes should always validate required fields before sending it to the service. 
- Suggested fix: add a check to see if user_id was added ( if not user_id:
        return jsonify({"error": "Missing required field: user_id"}), 400)

**Issue 3** *(if found)*
- Location: services/list_service.py (return len(items))
- What's wrong: returns all the items in the list rather than just the ones that were purchased with the request.
- Why it matters: description says to return only the ones that were currently purchased, so need to reflect that.
- Suggested fix: The fix in issue #1 should fix this.


### Questions for the Author
*Things you're uncertain about — design choices that could be intentional or bugs depending on intent.*

>Do you only want to return purchased items from request or all items that are purchased?

### Verdict
- [ ] Approve — ship it
- [X] Request Changes — needs fixes before merging
- [ ] Comment — needs discussion before a verdict

**Rationale** *(1–2 sentences)*:

>The changes introduce a real correctness issue around marking items as purchased and do not fully validate the request payload. These problems could cause data loss or unexpected behavior in production, so the PR should be revised before merging.

---

## PR #2 — List Stats (`pr2_list_stats.py`)

### Summary
*What does this PR do? (1–2 sentences in your own words)*

> Adds a function that gets the stats of a grocery list (how many purchased, total, left to be purchased, items per category). 

### Issues

**Issue 1**
- Location: services/list_service.py
- What's wrong: for the by_category stat, it counts the amount of total items per category (not just the ones needed to be purchased)
- Why it matters: The frontend team asked for how much needed to be purchased for each category.
- Suggested fix: check if is_purchased is false, and if so, then update the dictionary. 

**Issue 2**
- Location: routes/lists.py
- What's wrong: There isn't a check if the list_id is valid or not.
- Why it matters: This needs to return an error since if the list_id doesn't exist, it leads to no meaningful results. It could be a hidden bug that a user may not know about it since it would just return a successful code.
- Suggested fix: Add a check in get_list_stats() in services/list_service.py for the items variable. If it is a None type, return a ValueError. Also, in the corresponding function in routes/list.py, use a try and except block to catch the error and return the 404 error.

**Issue 3** *(if found)*
- Location:
- What's wrong:
- Why it matters:
- Suggested fix:

### Questions for the Author
*A good code review often surfaces design questions, not just bugs. What would you want to clarify before approving?*

>

### Verdict
- [ ] Approve — ship it
- [X] Request Changes — needs fixes before merging
- [ ] Comment — needs discussion before a verdict

**Rationale** *(1–2 sentences)*:

> The PR adds useful list statistics functionality, but the current implementation still needs refinement before it is ready to merge. I would want the issues addressed so the behavior is accurate and reliable in production.

---

## Reflection

*Answer after completing both reviews.*

**1.** Which issue was hardest to spot, and why?

> The most difficult was the user_id check because I had forgotten how the POST request works. 

**2.** Which issues do you think an LLM reviewer (like Claude reviewing its own code) would most likely miss? Why?

> It would most likely miss the by_category error because it is more context-driven while the checks for the variables are usually things that Claude and ChatGPT usually find and warn the user about (at least from my experience).

**3.** One thing you'd add to a code review checklist for AI-generated backend code:

> Check that the code handles edge cases like invalid IDs, missing fields, and business-rule logic such as counting only unfinished items. These are the kinds of issues that can slip through AI-generated code but matter a lot in production.
