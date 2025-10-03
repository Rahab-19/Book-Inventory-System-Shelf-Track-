"""
Shelf Track - Bookstore Inventory System
This program helps bookstore clerks manage book records using an SQLite database.
Clerks can add, update, delete, search, and view books and their authors.
"""

import sqlite3  # This is a built-in Python library that helps us work with SQLite databases
from sqlite3 import Error  # Used for handling any errors that may occur

#  Function to connect to the database 
def create_connection(db_file):
    """
    Connect to the SQLite database and return the connection object.
    If the file doesn't exist, it creates a new one.
    """
    try:
        conn = sqlite3.connect(db_file)  # Try connecting to the database
        return conn
    except Error as e:
        print(f"Database connection error: {e}")
    return None

# Function to create tables if they don't exist 
def create_tables(conn):
    """
    Creates 'author' and 'book' tables if they do not already exist.
    """
    try:
        with conn:
            # Create the author table
            conn.execute('''
                CREATE TABLE IF NOT EXISTS author (
                    id INTEGER PRIMARY KEY,
                    name TEXT NOT NULL,
                    country TEXT NOT NULL
                )
            ''')
            # Create the book table
            conn.execute('''
                CREATE TABLE IF NOT EXISTS book (
                    id INTEGER PRIMARY KEY,
                    title TEXT NOT NULL,
                    authorID INTEGER,
                    qty INTEGER,
                    FOREIGN KEY(authorID) REFERENCES author(id)
                )
            ''')
    except Error as e:
        print(f"Error creating tables: {e}")

# Insert sample data into the tables 
def insert_sample_data(conn):
    """
    Populates the tables with initial sample data if they are empty.
    """
    authors = [
        (1290, 'Charles Dickens', 'England'),
        (8937, 'J.K. Rowling', 'England'),
        (2356, 'C.S. Lewis', 'Ireland'),
        (6380, 'J.R.R. Tolkien', 'South Africa'),
        (5620, 'Lewis Carroll', 'England')
    ]
    books = [
        (3001, 'A Tale of Two Cities', 1290, 30),
        (3002, "Harry Potter and the Philosopher's Stone", 8937, 40),
        (3003, 'The Lion, the Witch and the Wardrobe', 2356, 25),
        (3004, 'The Lord of the Rings', 6380, 37),
        (3005, 'Alice’s Adventures in Wonderland', 5620, 12)
    ]
    try:
        with conn:
            cur = conn.cursor()
            # Only insert data if tables are empty
            cur.execute("SELECT COUNT(*) FROM author")
            if cur.fetchone()[0] == 0:
                cur.executemany("INSERT INTO author VALUES (?, ?, ?)", authors)
            cur.execute("SELECT COUNT(*) FROM book")
            if cur.fetchone()[0] == 0:
                cur.executemany("INSERT INTO book VALUES (?, ?, ?, ?)", books)
    except Error as e:
        print(f"Error inserting sample data: {e}")

# Ask for a number and check it is valid 
def get_valid_int(prompt, length=None):
    """
    Get a valid integer input from the user. Optionally checks for a fixed length.
    """
    while True:
        try:
            value = int(input(prompt))
            if length and len(str(value)) != length:
                raise ValueError(f"Input must be {length} digits long.")
            return value
        except ValueError as e:
            print(f"Invalid input: {e}")

# Function to add a new book 
def enter_book(conn):
    print("\n--- Enter New Book ---")
    book_id = get_valid_int("Book ID (4 digits): ", 4)
    title = input("Title: ")
    author_id = get_valid_int("Author ID (4 digits): ", 4)
    qty = get_valid_int("Quantity: ")
    try:
        with conn:
            conn.execute("INSERT INTO book VALUES (?, ?, ?, ?)", (book_id, title, author_id, qty))
            print("Book added successfully.")
    except Error as e:
        print(f"Error adding book: {e}")

# Function to update a book’s details 
def update_book(conn):
    print("\n--- Update Book ---")
    book_id = get_valid_int("Enter Book ID to update: ", 4)
    try:
        cur = conn.cursor()
        # Join book and author tables to fetch full info
        cur.execute('''
            SELECT book.title, author.id, author.name, author.country
            FROM book
            INNER JOIN author ON book.authorID = author.id
            WHERE book.id = ?
        ''', (book_id,))
        book = cur.fetchone()

        if not book:
            print("Book not found.")
            return

        print(f"\nTitle: {book[0]}\nAuthor: {book[2]}\nCountry: {book[3]}")
        print("\n1. Update Quantity\n2. Update Title\n3. Update Author Info")
        choice = input("Choose option (default is 1): ")

        # Update title, author, or quantity
        if choice == "2":
            new_title = input("New Title: ")
            conn.execute("UPDATE book SET title = ? WHERE id = ?", (new_title, book_id))
        elif choice == "3":
            new_name = input("New Author Name: ")
            new_country = input("New Country: ")
            conn.execute("UPDATE author SET name = ?, country = ? WHERE id = ?", (new_name, new_country, book[1]))
        else:
            new_qty = get_valid_int("New Quantity: ")
            conn.execute("UPDATE book SET qty = ? WHERE id = ?", (new_qty, book_id))

        print("Update successful.")
    except Error as e:
        print(f"Update error: {e}")

# Function to delete a book 
def delete_book(conn):
    print("\n--- Delete Book ---")
    book_id = get_valid_int("Book ID to delete: ", 4)
    try:
        with conn:
            conn.execute("DELETE FROM book WHERE id = ?", (book_id,))
            print("Book deleted.")
    except Error as e:
        print(f"Delete error: {e}")

# Function to search for books by title
def search_book(conn):
    print("\n--- Search Books ---")
    keyword = input("Enter title or part of title: ")
    try:
        cur = conn.cursor()
        cur.execute("SELECT * FROM book WHERE title LIKE ?", ('%' + keyword + '%',))
        results = cur.fetchall()
        if results:
            for row in results:
                print(row)
        else:
            print("No books found.")
    except Error as e:
        print(f"Search error: {e}")

# Function to view all book details 
def view_book_details(conn):
    print("\n--- Book Details ---")
    try:
        cur = conn.cursor()
        cur.execute('''
            SELECT book.title, author.name, author.country
            FROM book
            INNER JOIN author ON book.authorID = author.id
        ''')
        # Display books nicely
        for title, name, country in cur.fetchall():
            print("-" * 50)
            print(f"Title: {title}\nAuthor's Name: {name}\nAuthor's Country: {country}")
    except Error as e:
        print(f"Error fetching book details: {e}")

# Main function to show the menu and run the program 
def main():
    database = "ebookstore.db"  # This is the name of the database file

    # Use 'with' to automatically close the connection later
    with create_connection(database) as conn:
        if conn:
            create_tables(conn)        # Set up tables if they don’t exist
            insert_sample_data(conn)   # Add default books and authors

            while True:
                # Display options to the user
                print("\n=== Bookstore Menu ===")
                print("1. Enter book")
                print("2. Update book")
                print("3. Delete book")
                print("4. Search books")
                print("5. View details of all books")
                print("0. Exit")
                choice = input("Select option: ")

                # Run the appropriate function based on the user’s choice
                if choice == "1":
                    enter_book(conn)
                elif choice == "2":
                    update_book(conn)
                elif choice == "3":
                    delete_book(conn)
                elif choice == "4":
                    search_book(conn)
                elif choice == "5":
                    view_book_details(conn)
                elif choice == "0":
                    print("Goodbye!")
                    break
                else:
                    print("Invalid option. Try again.")
        else:
            print("Unable to connect to database.")

#  Start the program 
if __name__ == "__main__":
    main()
