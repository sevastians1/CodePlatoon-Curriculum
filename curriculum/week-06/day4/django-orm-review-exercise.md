## Django ORM Review
We are good to build a library program! It will allow patrons to check books out of the library, and librarians to add new books.

We will use Django, and possibly a little Javascript (just axios) on the frontend. 

We will not worry about the prettiness of the user interface and focus on functionality.

### Features

- View a list of books: 
	- View all books written by a particular author
	- View all books belonging to a particular genre

- A patron can check and return a book.
	- Ability to check out a copy of a book, if it is available
	- Ability to return the copy of a book they checked out
	- A "book" could be:
		- A paperback
		- A hardcover
		- An ebook
		- An audio book
	- The system will track when a book is checked out
	, and when a book is returned

- A patron can view all the books they've checked out

- A patron can view their history - what books they've previously checked out & returned

- Librarians can add new copies of books & book-related data to the system. NOTE: Our library is very liberal -- anyone can be a librarian! No authentication necessary.
	- Create/Delete a genre
	- Create/Delete an author
	- Create/Delete information about a book
	- Add another copy of a book (paperback, hardcover, ebook, or audiobook) to the system
	

### Implementation Plan

1. First we will talk through the program as a group. We will discuss our data model and create a diagram to document it.

2. We will develop a list of tasks to build our site. We will develop *iteratively*. We will pick the smallest possible feature we can build end-to-end (both the user interface). *We may even break a feature down into smaller pieces to implement*. 

3. We will work through our list of tasks, group-coding style. The instructor will "drive", and the class will do most of the navigation.
