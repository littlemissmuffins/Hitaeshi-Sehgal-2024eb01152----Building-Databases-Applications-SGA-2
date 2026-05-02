# Library Management System

A Spring Boot web application for managing a library's book catalogue. Supports adding, editing, and searching books, with each book linked to an author.

## Tech Stack

- **Java 21**
- **Spring Boot 4.0.6** — Web MVC, Data JPA, Validation
- **H2** — In-memory database (auto-configured, no setup needed)
- **JSP + JSTL** — Server-side rendered views
- **Maven** — Build tool (packaged as WAR)

## Project Structure

```
src/main/java/com/example/libraryapp/
├── entity/
│   ├── Author.java          # Author entity (one-to-many with Book)
│   └── Book.java            # Book entity (title, genre, author)
├── repository/
│   ├── AuthorRepository.java
│   └── BookRepository.java  # Includes custom JPQL search query
├── service/
│   └── LibraryService.java  # Business logic layer
├── controller/
│   └── LibraryController.java  # Handles all HTTP routes
├── DataLoader.java          # Seeds sample data on startup
└── LibraryAppApplication.java

src/main/webapp/WEB-INF/jsp/
├── index.jsp        # Home page — lists all books
├── new_book.jsp     # Add a new book
└── update_book.jsp  # Edit an existing book
```

## Getting Started

**Prerequisites:** Java 21, Maven 3.x

```bash
# Clone the repo
git clone <repo-url>
cd library-app

# Run the app
./mvnw spring-boot:run
```

Then open [http://localhost:8080](http://localhost:8080) in your browser.

> **Note:** The app must be run via `spring-boot:run` (not as a plain JAR) because JSP rendering requires the embedded Tomcat Jasper compiler, which is included only in WAR mode.

## Features & Routes

| Method | Route | Description |
|--------|-------|-------------|
| GET | `/` | View all books |
| GET | `/showNewBookForm` | Show add-book form |
| POST | `/saveBook` | Save a new or updated book |
| GET | `/showFormForUpdate/{id}` | Show edit form for a book |
| GET | `/search?authorName=` | Search books by author name |

## Sample Data

On first startup, `DataLoader` seeds 10 books across 3 authors:

- **George Orwell** — 1984, Animal Farm
- **J.K. Rowling** — Harry Potter books (3)
- **J.R.R. Tolkien** — The Hobbit, Lord of the Rings trilogy, The Silmarillion

## Data Model

```
Author                    Book
──────────────────        ──────────────────────
id (PK)                   id (PK)
name                      title
                          genre
                          author_id (FK → Author)
```

An `Author` can have many `Books`; a `Book` belongs to one `Author`.

## Search

The search feature uses a custom JPQL query with a case-insensitive partial match on author name:

```sql
SELECT b FROM Book b JOIN b.author a
WHERE LOWER(a.name) LIKE LOWER(CONCAT('%', :authorName, '%'))
```

So searching `"rowl"` will return all J.K. Rowling books.
