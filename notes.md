# models desriptions

## User

Fields: id, username, email, created_at, lists, added_items
Non-Nullable Fields: username, email
Primary key: id

# Grocery List

Fields: id, name, created_by, created_at, is_shared
Primary key: id
Non-nullable Fields: name, created_by

# Item

Fields: id, list_id, name, quantity, unit, category, added_by, added_at, is_purchased, purchased_by, purchased_at
Non-nullable Fields: id, list_id, name, added_by, is_purchased
Nullable Fields: quantity, unit, category, purchased_at
Primary key: id

## Other Notes

- Item and user help each other to decide who purchased an item. 
- added_by is when item added to list and purhased_by is when that said item is purchased and shows by who (could be different by added_by)

# routes/list.py

- There seems to be a list_service function for every route function in routes/list.py.

- Each does a try/except and when it failes returns a 404 and error message (ValueError) except get_lists().

- Each result is jsonify (ied) and returned. (even if error)

- What does request.json() do? (returns JSOn body from incoming HTTP request)(empty directionary if else) (only used for GET/PATCH methods)

- 200 if success

- for patch/get, check for needed fields to call service to make those changes

# serives/list_service.py

## mark_purchased()

- There are checks for if the item exists in the given list, and if the item has not been purchased already (if so, for any, there is a ValueError).

- It returns the item that was purchased (so caller could know the item was purchased)

# PR Descriptions

# First PR

- Wants to make all items in list marked as purchased

# Second PR

- Wants to get stats of the grocery list (how many purchased, total, left)