#  Introduction

A naming convention is a set of unwritten rules you should use if you want to increase the readability of the whole data model. You can use this convention to name anything inside the database, whether that's tables, columns, stored procedures, functions, views and so on. Alternatively you may wish to set the convention only for tables and columns, but it is a good idea to arrange something before beginning to create your data model.

# Why use a naming convention?

When working with databases, the likelihood is that you'll have a multitude of tables. Use a naming convention because:

* You want a simple life. You will spend less time finding what you need. If you know how table names are structured, it is easier to make an educated guess as to what table might hold the data you're looking for.
* It is easier to search for specific patterns. If you wish to ensure all tables have a primary key variable (e.g. ID), you can set up QA procedures (automated or manual) to check for that.
* The database will live for a long time. Whilst the app it's connected to or even the code used to create the database may change, the likelihood is that the database will stay very similar in structure to its 'initial production version'. By applying best practice from the start and when adding new objects, you maintain a well organised database structure that is easily readable.
* You won't be the only person working with the database. This makes it easier for anyone to jump straight into using the data, and creating standard templates for new objects based on your naming convention.
* Well chosen identifiers make it significantly easier for developers and analysts to understand and read easily what a table or variable is doing e.g. "CurrentTrustGrouping"" is easier than "currenttrustgrouping".


# Naming conventions

Within the Joint Risk Tool we have stuck with the 'PascalCase' naming convention. This essentially consists of capitalizing the first letter in a compound word e.g. "SingleList". This is commonly used for variable fields within tables. Some common alternatives are:

* camelCase: First letter isn't capitalized, second/third/fourth all are e.g. twoWords.
* flat case: No capitalization e.g. twowords. Alternatively is TWOWORDS i.e. all caps.
* snake_case: Introduction of an underscore between words e.g. two_words.
* Pascal_Snake_Case: Retains capitalization but for greater clarity also introduces underscores e.g. Two_Words
* Train-Case: Similar to Pascal_Snake_Case, but a hyphen replaces the underscore.

# Additional Tips

You should use the above to aid in setting consistent column and table naming conventions. Some other things to bear in mind are:

* Avoid quotes: If you have to quote an identifier in SQL or R, rename it, as quotes are annoying.
* Avoid using object names/column names that are just data types: For example, 'text' or 'timestamp' are bad because they give zero context about what the variable relates to. Database object names and particularly column names should be a noun describing the field or object.
* Full words: Object names should be full english words, avoid abbreviations, especially if just the type that removes vowels.
* Avoid using reserved words: Depending on the programming/database language you are using, avoid using any word that the system might confuse for an existing function. Things like 'user', 'group' or 'file' should all be avoided. Here are the list of reserved words for [Microsoft SQL Server](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/reserved-keywords-transact-sql?redirectedfrom=MSDN&view=sql-server-ver15).