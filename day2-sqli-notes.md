	•	URL param: category
	•	Payload: category=Gifts'+OR+1=1--
	•	What it did: Closed the string with ', used OR 1=1 to make the WHERE clause always true, and -- commented out the rest of the query.
	•	Result: Returned all products regardless of category. Lab solved.
	•	Lesson: Double comment markers (-- twice) break the payload — only one -- belongs at the very end.

Lab 2: SQLi vulnerability allowing login bypass

	•	Field: Username
	•	Payload: administrator'--
	•	What it did: Commented out the password check entirely after matching the username.
	•	Gotcha: Password field still needed any character typed in, since the browser blocks empty-field submission before the payload even reaches the server.
	•	Result: Logged in as administrator without knowing the real password. Lab solved.

Lab 3: SQLi UNION attack — determining number of columns

	•	Method: Incremented ORDER BY on the category filter until the page errored.
	•	Payloads tested: ORDER BY 1--, ORDER BY 2--, ORDER BY 3--, ORDER BY 4--
	•	Result: ORDER BY 4-- returned Internal Server Error → confirmed table has 3 columns.
	•	Lesson: The last number that does NOT error is the column count.

Lab 4: SQLi UNION attack — retrieving data from other tables

	•	Column count for this lab: 2 (re-checked via ORDER BY, different from Lab 3 — don’t assume counts carry over between labs)
	•	Payload: category=gifts' UNION SELECT username, password--
	•	Gotcha hit: Accidentally typed a double apostrophe ('') instead of one, which broke the query into plain text instead of SQL. Fixed by removing the extra quote.
	•	Note: FROM users wasn’t required for this particular lab variant — UNION SELECT alone pulled valid matching data without explicitly specifying the table.
	•	Result: Usernames and passwords displayed in the product listing. Lab solved.

Lab 5 (in progress, not completed): SQLi UNION attack — combining data in a single column

	•	Goal: Concatenate username and password into one display column using a separator.
	•	Attempted payload (had errors): category=Corporate+gifts' UNION SELECT NULL username,||~||passwords--
	•	Issues identified:
	1.	Missing comma between NULL and username
	2.	Missing username before the concatenation operator
	3.	passwords should be password (singular — matches real column name)