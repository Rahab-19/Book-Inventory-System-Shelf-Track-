# Shelf Track – Bookstore Inventory System  

![Python](https://img.shields.io/badge/Python-3.9%2B-blue?logo=python&logoColor=white)  
![SQLite](https://img.shields.io/badge/Database-SQLite-green?logo=sqlite&logoColor=white)  
![License](https://img.shields.io/badge/License-MIT-yellow)  
![Status](https://img.shields.io/badge/Status-Active-brightgreen)  

Shelf Track is a command-line bookstore management system built with Python and SQLite.  
It allows bookstore clerks to manage books and authors by adding, updating, deleting, searching, and viewing inventory.  

---

## Features  
- Manage authors and books  
- Add new books with unique IDs  
- Update book details (title, author information, quantity)  
- Delete books from the database  
- Search books by title (case-insensitive)  
- View detailed inventory including title, author, country, and stock quantity  
- Includes sample data for quick setup  

---

## Tech Stack  
- **Python** (standard library: `sqlite3`)  
- **SQLite Database** (`ebookstore.db`)  

No additional dependencies required.  

---

## Project Structure  
```

ShelfTrack/
│── ebookstore.db        # Database (auto-created if missing)
│── shelf_track.py       # Main program file
│── README.md            # Project documentation

````

---

## How to Run  

1. Clone the repository:  
   ```bash
   git clone https://github.com/yourusername/shelf-track.git
   cd shelf-track
````

2. Run the program:

   ```bash
   python shelf_track.py
   ```

3. Use the menu to manage the bookstore:

   ```
   === Bookstore Menu ===
   1. Enter book
   2. Update book
   3. Delete book
   4. Search books
   5. View details of all books
   0. Exit
   ```

---

## Example Usage

**Search Example**

```
--- Search Books ---
Enter title or part of title: Harry Potter
(3002, "Harry Potter and the Philosopher's Stone", 8937, 40)
```

**View Book Details Example**

```
--------------------------------------------------
Title: The Lord of the Rings
Author: J.R.R. Tolkien (South Africa)
Quantity: 37
```

---

## Future Improvements

* Add a menu to manage authors
* Export inventory to CSV/Excel
* Track sales and update stock automatically
* Add login system for clerks and managers

---

## Contributing

Pull requests are welcome.
If you would like to improve Shelf Track, feel free to fork the repository and submit a PR.

---

## License

This project is licensed under the MIT License.

```

Would you like me to also prepare a **1-line GitHub description + keywords (tags)** you can paste into your repo settings, so it looks professional in search results?
```
