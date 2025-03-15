# Django LocalLibrary

A comprehensive Django web application that creates an online catalog for a small local library, where users can browse available books and manage their accounts.

## Table of Contents

- [Features](#features)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Usage](#usage)
- [Application Components](#application-components)
  - [Models](#models)
  - [Views](#views)
  - [Templates](#templates)
  - [Forms](#forms)
  - [URLs](#urls)
- [Authentication System](#authentication-system)
- [Book Management](#book-management)
- [Administrative Functions](#administrative-functions)
- [Development Process](#development-process)

## Features

- Browse books and authors with pagination
- View detailed information about books and authors
- User authentication system with login/logout
- Book borrowing system for authenticated users
- Personal bookshelf showing borrowed books
- Librarian interface for managing book circulation
- Book renewal functionality for librarians
- Create, update, and delete books and authors (with proper permissions)
- Session-based visit counter
- Responsive design using Bootstrap
- Full admin interface for database management

## Project Structure

```
locallibrary/                  # Project root directory
│
├── locallibrary/              # Project configuration directory
│   ├── __init__.py
│   ├── settings.py            # Project settings 
│   ├── urls.py                # Main URL configuration
│   ├── asgi.py                # ASGI configuration
│   └── wsgi.py                # WSGI configuration
│
├── catalog/                   # Main application directory
│   ├── __init__.py
│   ├── admin.py               # Admin site configuration
│   ├── apps.py                # App configuration
│   ├── forms.py               # Form definitions
│   ├── models.py              # Database models
│   ├── tests.py               # Test cases
│   ├── urls.py                # App URL configuration
│   ├── views.py               # View functions and classes
│   ├── migrations/            # Database migrations
│   ├── static/                # Static files (CSS, JavaScript)
│   │   └── css/
│   │       └── styles.css     # Custom CSS
│   └── templates/             # HTML templates
│       ├── catalog/           # App-specific templates
│       │   ├── author_confirm_delete.html
│       │   ├── author_detail.html
│       │   ├── author_form.html
│       │   ├── author_list.html
│       │   ├── book_confirm_delete.html
│       │   ├── book_detail.html
│       │   ├── book_form.html
│       │   ├── book_list.html
│       │   ├── book_renew_librarian.html
│       │   ├── bookinstance_list_borrowed_all.html
│       │   └── bookinstance_list_borrowed_user.html
│       ├── base_generic.html  # Base template
│       └── index.html         # Home page template
│
├── templates/                 # Project-level templates
│   └── registration/          # Authentication templates
│       ├── login.html
│       ├── logged_out.html
│       └── other auth templates...
│
├── db.sqlite3                 # SQLite database
├── manage.py                  # Django management script
└── README.md                  # This file
```

## Installation

1. Clone this repository:
   ```bash
   git clone https://github.com/yourusername/django-locallibrary.git
   cd django-locallibrary
   ```

2. Create and activate a virtual environment:
   ```bash
   # For Windows
   python -m venv venv
   venv\Scripts\activate
   
   # For macOS/Linux
   python3 -m venv venv
   source venv/bin/activate
   ```

3. Install dependencies:
   ```bash
   pip install django
   ```

4. Apply migrations:
   ```bash
   python manage.py makemigrations
   python manage.py migrate
   ```

5. Create a superuser (for admin access):
   ```bash
   python manage.py createsuperuser
   ```

6. Run the development server:
   ```bash
   python manage.py runserver
   ```

7. Access the site at http://127.0.0.1:8000/

## Usage

### User Roles

The application supports three types of users:

1. **Anonymous Users**: Can browse books and authors
2. **Authenticated Users**: Can borrow books and view their borrowed books
3. **Librarians**: Can manage book circulation and author/book information

### Browsing the Library

- **Home Page**: Visit the root URL (/) to see library statistics
- **Book List**: Click "All books" to see a list of all available books
- **Author List**: Click "All authors" to see a list of all authors

### User Authentication

- **Login**: Click the "Login" link in the sidebar to access your account
- **Logout**: Click "Logout" to end your session (only for authenticated users)
- **My Borrowed**: View books you have currently borrowed (only for authenticated users)

### Librarian Functions

- **All Borrowed**: View all books that are currently on loan (staff only)
- **Renew Book**: Extend the due date for borrowed books (staff only)
- **Add/Edit/Delete Authors and Books**: Manage library content (with proper permissions)

## Application Components

### Models

The application uses the following models (defined in `catalog/models.py`):

1. **Book**: Represents a book title in the library (not a specific copy)
   - Fields: title, author (ForeignKey), summary, ISBN, genre (ManyToManyField)
   - Methods: `__str__()`, `get_absolute_url()`

2. **Author**: Represents a book author
   - Fields: first_name, last_name, date_of_birth, date_of_death
   - Methods: `__str__()`, `get_absolute_url()`

3. **BookInstance**: Represents a specific copy of a book that can be borrowed
   - Fields: id (UUID), book (ForeignKey), imprint, due_back, borrower, status
   - Methods: `__str__()`, `is_overdue` (property)
   - Permissions: can_mark_returned

4. **Genre**: Represents a book genre
   - Fields: name
   - Methods: `__str__()`

### Views

The application uses both function-based and class-based views (defined in `catalog/views.py`):

1. **Function-based Views**:
   - `index`: Home page showing library statistics
   - `renew_book_librarian`: Form for renewing borrowed books

2. **Class-based Views**:
   - List Views: `BookListView`, `AuthorListView`
   - Detail Views: `BookDetailView`, `AuthorDetailView`
   - Borrowed Books Views: `LoanedBooksByUserListView`, `AllBorrowedBooksListView`
   - Create/Update/Delete Views: `AuthorCreate`, `AuthorUpdate`, `AuthorDelete`, `BookCreate`, `BookUpdate`, `BookDelete`

### Templates

The application uses Django's template system to render HTML pages:

1. **Base Template**: `base_generic.html` contains the common layout for all pages
2. **List Templates**: Display lists of books and authors
3. **Detail Templates**: Show detailed information about a book or author
4. **Form Templates**: Allow creating, updating, and deleting records
5. **Authentication Templates**: Handle user login/logout

### Forms

The application uses forms for data input (defined in `catalog/forms.py`):

1. **RenewBookForm**: Custom form for renewing books with date validation

Additionally, the application uses ModelForms created by generic editing views for creating/updating books and authors.

### URLs

URL patterns are defined in two files:

1. **Project URLs** (`locallibrary/urls.py`): Main URL configuration that includes app URLs and authentication URLs
2. **App URLs** (`catalog/urls.py`): Specific URL patterns for the catalog application

## Authentication System

The authentication system uses Django's built-in authentication framework:

1. **Login/Logout**: Standard Django auth views
2. **Permission Control**: Used to restrict access to certain functions
3. **User Association**: BookInstance objects can be linked to users via the borrower field

## Book Management

1. **Adding Books**:
   - Go to Admin site (/admin/) or use the "Create book" link (with proper permissions)
   - Fill in details: title, author, summary, ISBN, and genre(s)
   - Submit the form

2. **Adding Book Copies**:
   - Create a BookInstance linked to a Book
   - Set status, due date, and other details

3. **Borrowing Process**:
   - Librarian assigns a user to a book's "borrower" field
   - Sets status to "On loan"
   - Sets a due date

4. **Returning Process**:
   - Librarian removes the borrower
   - Changes status back to "Available"

## Administrative Functions

### Admin Interface

Django provides a powerful admin interface at `/admin/`:

1. **Accessing Admin**:
   ```bash
   # Create superuser if you haven't already
   python manage.py createsuperuser
   
   # Run server and visit http://127.0.0.1:8000/admin/
   python manage.py runserver
   ```

2. **Managing Users and Permissions**:
   - Create users
   - Assign them to groups
   - Grant specific permissions

3. **Data Management**:
   - Add/Edit/Delete Authors
   - Add/Edit/Delete Books
   - Add/Edit/Delete BookInstances
   - Add/Edit/Delete Genres

### Permissions

The application uses Django's permission system to control access:

1. **Model Permissions**: Automatically created for all models (add, change, delete)
2. **Custom Permissions**: `can_mark_returned` for librarians
3. **Permission Checks**: Implemented in views using decorators and mixins

## Development Process

This project was developed following these steps:

1. **Project Setup**:
   ```bash
   # Create project
   django-admin startproject locallibrary
   
   # Create app
   python manage.py startapp catalog
   ```

2. **Define Models**:
   - Create models in models.py
   - Register models in admin.py
   - Create and apply migrations
   ```bash
   python manage.py makemigrations
   python manage.py migrate
   ```

3. **Create Views and Templates**:
   - Implement basic views for listing and displaying data
   - Create templates for rendering HTML

4. **Add Authentication**:
   - Configure authentication URLs
   - Create templates for login/logout
   - Implement permission controls

5. **Implement Forms**:
   - Create forms for data input
   - Implement form handling in views

6. **Advanced Features**:
   - Add sessions for visit counting
   - Implement CRUD operations for models
   - Add book renewal functionality

7. **Testing and Deployment**:
   - Write tests for models and views
   - Configure settings for production
   - Deploy the application