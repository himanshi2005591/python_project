# python_project
from abc import ABC, abstractmethod

# Abstract class for Book Categories
class BookCategory(ABC):
    def __init__(self, title, author, book_id):
        self.title = title
        self.author = author
        self.book_id = book_id
        self.is_issued = False

    @abstractmethod
    def category_name(self):
        pass

    def __str__(self):
        return f"[{self.category_name()}] {self.title} by {self.author} (ID: {self.book_id})"

# Concrete Book Categories
class Fiction(BookCategory):
    def category_name(self):
        return "Fiction"

class NonFiction(BookCategory):
    def category_name(self):
        return "Non-Fiction"

class Science(BookCategory):
    def category_name(self):
        return "Science"

# Abstract class for the Library System
class LibrarySystem(ABC):
    @abstractmethod
    def add_book(self, book: BookCategory):
        pass

    @abstractmethod
    def issue_book(self, book_id: str, user: str):
        pass

    @abstractmethod
    def return_book(self, book_id: str, user: str):
        pass

# Concrete implementation of Library
class Library(LibrarySystem):
    def __init__(self):
        self.books = {}
        self.issued_books = {}

    def add_book(self, book: BookCategory):
        self.books[book.book_id] = book
        print(f"Book added: {book}")

    def issue_book(self, book_id: str, user: str):
        book = self.books.get(book_id)
        if book and not book.is_issued:
            book.is_issued = True
            self.issued_books[book_id] = user
            print(f"Issued: {book} to {user}")
        elif book:
            print(f"Book '{book.title}' is already issued.")
        else:
            print("Book ID not found.")

    def return_book(self, book_id: str, user: str):
        if book_id in self.issued_books:
            if self.issued_books[book_id] == user:
                book = self.books[book_id]
                book.is_issued = False
                del self.issued_books[book_id]
                print(f"Returned: {book} by {user}")
            else:
                print("Error: This book was not issued to you.")
        else:
            print("Error: Book not issued or invalid ID.")

# CLI interaction
def run_library():
    library = Library()

    while True:
        print("\n==== Library Menu ====")
        print("1. Add Book")
        print("2. Issue Book")
        print("3. Return Book")
        print("4. Show All Books")
        print("5. Exit")

        choice = input("Enter your choice: ")

        if choice == "1":
            title = input("Enter book title: ")
            author = input("Enter author name: ")
            book_id = input("Enter book ID: ")
            print("Choose category: 1. Fiction 2. Non-Fiction 3. Science")
            cat_choice = input("Enter category number: ")

            if cat_choice == "1":
                book = Fiction(title, author, book_id)
            elif cat_choice == "2":
                book = NonFiction(title, author, book_id)
            elif cat_choice == "3":
                book = Science(title, author, book_id)
            else:
                print("Invalid category.")
                continue

            library.add_book(book)

        elif choice == "2":
            book_id = input("Enter book ID to issue: ")
            user = input("Enter your name: ")
            library.issue_book(book_id, user)

        elif choice == "3":
            book_id = input("Enter book ID to return: ")
            user = input("Enter your name: ")
            library.return_book(book_id, user)

        elif choice == "4":
            print("\n--- Book List ---")
            for book in library.books.values():
                status = "Issued" if book.is_issued else "Available"
                print(f"{book} - Status: {status}")

        elif choice == "5":
            print("Exiting Library System.")
            break

        else:
            print("Invalid choice. Try again.")

# Run the interactive system
if __name__ == "__main__":
    run_library()
