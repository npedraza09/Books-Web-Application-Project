<a href="https://npedraza09.github.io">Back to My Portfolio</a>

<a class="anchor" id="Index"></a>
# Index

- [Abstract](#Abstract)
- [1. Introduction](#Introduction)
- [2. Structure](#Structure)
    - [2.1 Folders](#Folders)
    - [2.2 Files](#Files)
- [3. Script](#Script)
    - [3.1 Application](#App)
    - [3.2 Templates](#Templates)
        -  [3.2.1 Web App Index](#Web_App_Index)
        -  [3.2.2 Registration](#Registration)
        -  [3.2.3 Book Display](#Book_Display)
        -  [3.2.4 Adding Books](#Adding_Books)
        -  [3.2.5 Adding Images](#Adding_Images)
- [4. Conclusion](#Conclusion)


<a class="anchor" id="Abstract"></a>
##  Abstract

The Books Web Application project is a dynamic platform designed for managing a collection of books, offering users the ability to browse, add, and organize books alongside their corresponding images. Built using Flask as the backend framework and Bootstrap for the frontend, the application incorporates authentication and authorization protocols via JSON Web Tokens (JWTs) to ensure secure and role-based access. The modular structure includes a Python-based core application, HTML templates for dynamic rendering, and a static folder for image storage, providing a user-friendly and extendable solution for book collection management.


[Back to top](#Index)

<a class="anchor" id="Introduction"></a>
## 1. Introduction

Managing and organizing book collections can be a challenging task without an intuitive and secure digital platform. The Books Web Application addresses this need by providing a streamlined web-based solution for users to browse, add, and manage books. Designed with both functionality and user experience in mind, this application ensures ease of access while maintaining secure, role-based access control.

The project leverages Flask, a lightweight and powerful Python framework, to handle backend logic and routing. HTML templates dynamically render content using Jinja2, ensuring efficient and consistent design, while Bootstrap enhances the frontend with responsive and visually appealing components. Security is a key focus, with JWTs providing robust authentication and authorization mechanisms. By allowing users to upload book-related images, the application further enriches its functionality and personalization.

This application’s architecture is designed for scalability and maintainability, featuring distinct folders for application logic, templates, and static files. By following a modular design pattern, it facilitates future enhancements and simplifies debugging. The Books Web Application stands as a testament to how modern web technologies can be seamlessly integrated to deliver a robust and user-friendly solution for managing book collections.


[Back to top](#Index)

<a class="anchor" id="Structure"></a>
## 2. Structure

<a class="anchor" id="Folders"></a>
### 2.1 Folders

#### 1. Application

Although this is not a folder, the application file is on its own, separate from the other folders but under the project folder that encapsulates everything. This file is written with Python and is the main component of the web app. It integrates everything to successfully create the streamlined platform.

#### 2. Templates Folder

This folder includes all HTML files that have templates for different parts of the web app. These templates are referenced on the app.py to integrate and build the web app. 

#### 3. Static Folder

The static folder does not have much to it other than the images in png format for the books. This folder is created for simplicity when listing and referencing each book to its corresponding image, especially when a book and image are added by a user on their account.


<a class="anchor" id="Files"></a>
### 2.2 Files

* Application:
    * app.py (python file for integration and construction of web application)

 * Templates:
     * index.html (parent template for main interface of the web app)
     * register.html (child template inside index.html for registration purposes)
     * books.html (child template for book description and image display)
     * addimage.html (child template to add an image to the database)
     * addbook.html (child template to add a book to the database)

* Static:
    * imageN.png (image file number N in png format)


[Back to top](#Index)

<a class="anchor" id="Script"></a>
## 3. Script

<a class="anchor" id="Application"></a>
### 3.1 Application

#### Imports
```python
from flask import Flask, request, render_template, session
from flask import redirect, make_response, jsonify
from functools import wraps
import os

from flask_restful import Resource, Api
from flask_jwt_extended import create_access_token
from flask_jwt_extended import jwt_required, verify_jwt_in_request
from flask_jwt_extended import JWTManager, get_jwt_identity, get_jwt
from flask_jwt_extended import set_access_cookies
```
* Flask and its utilities: Import core Flask modules for creating a web app, handling requests, rendering templates, managing sessions, and redirecting users.
* functools.wraps: Allows creation of decorators that retain the metadata of the original function.
* os: Used for interacting with the operating system, like file path manipulations.
* Flask-RESTful: Adds support for creating REST APIs.
* Flask-JWT-Extended: Manages authentication and authorization using JSON Web Tokens (JWTs).

#### App Initialization
```python
app = Flask(__name__)
app.config["JWT_SECRET_KEY"] = "secretkey"
app.config["JWT_TOKEN_LOCATION"] = ["cookies"]
app.config["JWT_COOKIE_SECURE"] = False
jwt = JWTManager(app)
jwt.init_app(app)
app = Flask(__name__)
app.secret_key = "secretkey"
app.config["UPLOADED_PHOTOS_DEST"] = "static"
app.config["JWT_SECRET_KEY"] = "secretkey"
app.config["JWT_TOKEN_LOCATION"] = ["cookies"]
app.config["JWT_COOKIE_SECURE"] = False
app.config["JWT_COOKIE_CSRF_PROTECT"] = False

jwt = JWTManager(app)
jwt.init_app(app)
```
Flask app setup: The Flask app is instantiated, and various configurations are applied.
* JWT_SECRET_KEY: The secret key used for signing JWT tokens.
* JWT_TOKEN_LOCATION: Specifies that tokens are stored in cookies.
* JWT_COOKIE_SECURE: Allows insecure HTTP connections (useful for local testing).
* UPLOADED_PHOTOS_DEST: Directory for saving uploaded files.
* JWT_COOKIE_CSRF_PROTECT: Disables CSRF protection for JWT cookies.

#### Data Structure
```python
books = [
    {
        "id": 1,
        "author": "Eric Reis",
        "country": "USA",
        "language": "English",
        "title": "Lean Startup",
        "year": 2011
    },
    {
        "id": 2,
        "author": "Mark Schwartz",
        "country": "USA",
        "language": "English",
        "title": "A Seat at the Table",
        "year": 2017
    },
    {
        "id": 3,
        "author": "James Womak",
        "country": "USA",
        "language": "English",
        "title": "Lean Thinking",
        "year": 1996
    },
        {
        "id": 4,
        "author": "Viktor Frankl",
        "country": "USA",
        "language": "English",
        "title": "Man's search for meaning",
        "year": 1963
    },
        {
        "id": 5,
        "author": "Hermann Hesse",
        "country": "USA",
        "language": "English",
        "title": "Siddhartha",
        "year": 1951
    }
]

users = [
    {"username": "testuser", "password": "testuser", "role": "admin"},
    {"username": "John", "password": "John", "role": "reader"},
    {"username": "Anne", "password": "Anne", "role": "admin"},
    {"username": "Nico", "password": "Nico", "role": "admin"},
    {"username": "Isa", "password": "Isa", "role": "reader"}
]
```
* Books: A list of dictionaries representing a collection of books.
* Users: A list of dictionaries with username, password, and role fields for authentication and role-based access control.

#### Utility Functions
```python
def admin_required(fn):
    @wraps(fn)
    def wrapper(*args, **kwargs):
        verify_jwt_in_request()
        claim = get_jwt()
        if (claim["role"] != "admin"):
            return jsonify(msg = "Admins only!"), 403
        else:
            return fn(*args, **kwargs) 
      
    return wrapper
```
* Verifies JWT tokens.
* Checks if the user has the admin role.
* Returns a 403 error if the user lacks admin privileges.

```python
def checkUser(username, password):
    for user in users:
        if username in user["username"] and password in user["password"]:
            return {"username": user["username"], "role": user["role"]}
    return None
```
* Iterates through the users list to validate credentials.
* Returns the user's username and role if valid, otherwise None.

#### Routes

##### Home Route
```python
@app.route("/", methods=["GET"])
def firstRoute():
    return render_template("register.html")
```
* Renders the registration/login page when accessing the root URL.

##### Login Route
```python
@app.route("/login", methods=["GET", "POST"])
def login():
    if request.method == "POST":
        username = request.form["username"]
        password = request.form["password"]
        validUser = checkUser(username, password)
        if validUser != None:
            # set JWT token
            access_token = create_access_token(
            identity=username, additional_claims={"role": validUser["role"]}
)

            response = make_response(
                render_template(
                    "index.html", title="books", username=username, books=books
                )
            )
            response.status_code = 200
            # add jwt-token to response headers
            response.headers.extend({"jwt-token": access_token})
            set_access_cookies(response, access_token)
            return response

    return render_template("register.html")
```
* Handles login via POST:
    * Validates the user using checkUser.
    * Creates a JWT token with the username and role.
    * Stores the token in cookies.
* Renders the registration page for GET requests or invalid login attempts.

##### Logout Route
```python
@app.route("/logout")
def logout():
    # invalidate the JWT token
    
    return "Logged Out of Books"
```
* Placeholder route to handle logout functionality.

##### Books Route
```python
@app.route("/books", methods=["GET"])
@jwt_required()
def getBooks():
    try:
        username = get_jwt_identity()
        return render_template('books.html', username=username, books=books)
    except:
        return render_template("register.html")
```
* Requires a valid JWT token.
* Displays the list of books for authenticated users.

##### Add Book Route
```python
@app.route("/addbook", methods=["GET", "POST"])
@jwt_required()
@admin_required
def addBook():
    username = get_jwt_identity()
    if request.method == "GET":
        return render_template("addBook.html", username=username)
    if request.method == "POST":
        # expects pure json with quotes everywheree
        author = request.form.get("author")
        title = request.form.get("title")
        newbook = {"author": author, "title": title}
        books.append(newbook)
        return render_template(
            "books.html", books=books, username=username, title="books"
        )
    else:
        return 400
```
* Requires both a valid JWT token and admin privileges.
* GET: Renders a form to add a book.
* POST: Adds a new book to the books list.

##### Add Image Route
```python
@app.route("/addimage", methods=["GET", "POST"])
@jwt_required()
@admin_required
def addimage():
    if request.method == "GET":
        return render_template("addimage.html")
    elif request.method == "POST":
        image = request.files["image"]
        id = request.form.get("number")  # use id to number the image
        imagename = "image" + id + ".png"
        image.save(os.path.join(app.config["UPLOADED_PHOTOS_DEST"], imagename))
        print(image.filename)
        return "image loaded"

    return "all done"
```
* Allows admin users to upload an image.
* Saves the uploaded file in the static directory with a unique name based on an ID.

#### Main Execution Block
```python
if __name__ == "__main__":
    app.run(debug=True, host="0.0.0.0", port=5000)
```
* Runs the app in debug mode, listening on all network interfaces on port 5000.


[Back to top](#Index)

<a class="anchor" id="Templates"></a>
### 3.2 Templates

<a class="anchor" id="Web_App_Index"></a>
#### 3.2.1 Web App Index

##### HTML Boilerplate and Bootstrap Integration
```html
<!doctype html>
<html>
    <!-- Bootstrap CSS -->
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css" integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">
```
* The <!doctype html> specifies that the document is an HTML5 document.
* The <link> element imports Bootstrap 4 for responsive design and styling.
* The href specifies the Bootstrap CDN URL.
* The integrity attribute ensures the file's integrity and security.
* The crossorigin attribute enables secure cross-origin resource sharing.

##### Head Section
```html
    <head>
        <nav class="navbar navbar-expand-lg navbar-light bg-light">
            <a class="navbar-brand" href="#">My Books Site</a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
              <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNav">
              <ul class="navbar-nav">
                <li class="nav-item active">
                  <a class="nav-link" href="{{'/'}}">Home <span class="sr-only">(current)</span></a>
                </li>
                <li class="nav-item">
                  <a class="nav-link" href="{{'/books' }}">Books</a>
                </li>
                <li class="nav-item">
                  <a class="nav-link" href="{{'/addbook'}}">Add Book</a>
                </li>
                <li class="nav-item">
                  <a class="nav-link" href="{{'/addimage'}}">Add Image</a>
                </li>
                <li class="nav-item">
                  <a class="nav-link disabled" href="#">Delete Book(Disabled)</a>
                </li>
              </ul>
            </div>
          </nav>          
    </head>
```
* Navigation Bar:
    * The <nav> element uses Bootstrap's navbar component for a responsive menu bar.
    * Navbar Brand: <a class="navbar-brand"> specifies the brand name or logo (here, "My Books Site").
    * Toggle Button: Used in smaller screens to show/hide the menu items. The data-target links to the menu's ID (#navbarNav).
* Menu Items:
    * <ul class="navbar-nav"> creates a list of menu items.
    * Each <li> represents a menu item.
    * The href attributes use Jinja2 template syntax ({{}}) to generate dynamic links for Flask routes.

##### Body Section
```html
    <body>
        <div class="p-3 border bg-light">
            {%if username %}
                <h1> Welcome {{username}}</h1>
            {%endif%}
            <div id="content">
            {%block content %}{%endblock%}
            </div>
        </div>
    </body>
</html>
```
* Main Container:
    * <div class="p-3 border bg-light">: A light-colored box with padding and a border, styled using Bootstrap utility classes.
* Dynamic Welcome Message:
    * {% if username %}: Jinja2 syntax checks if a username variable is available (passed from Flask).
    * If a user is logged in, it displays Welcome {{username}}.
* Content Block:
    * <div id="content">: Placeholder for page-specific content.
    * {% block content %}{% endblock %}: Template block for extending or overriding content in child templates.


[Back to top](#Index)

<a class="anchor" id="Registration"></a>
#### 3.2.2 Registration

##### Template Inheritance
```html
{% extends "index.html"%}
```
* This line indicates that the current HTML file extends (inherits) the index.html template.
* The index.html file serves as a base layout with shared structure (like the navigation bar and main container). This child template focuses only on defining the specific content for the content block.

##### Block Content
```html
{% block content %}
<h1> Use "testuser" for username and password</h1>
<form method="POST" action="/login">
    UserName <input type="text" name="username" />
    <br>
    Password <input type="text" name="password" />
    <br>
    <input type="submit">
  </form>
{% endblock %}
```
* Overrides the content block placeholder defined in index.html.
* Everything between {% block content %} and {% endblock %} is injected into the content section of the base template.
* Creates a form for user authentication.
* method="POST": Specifies the HTTP POST method, used for sending login data securely.
* action="/login": Specifies the Flask route /login (defined in app.py) to handle form submissions.

<img width="800" height="400" alt="Screenshot 2025-01-15 at 4 09 14 PM" src="https://github.com/user-attachments/assets/4bf43b14-732a-4da5-8f8a-5626ea6223fc" />
Home screen/ Registration.

<img width="800" height="400" alt="Screenshot 2025-01-15 at 4 09 21 PM" src="https://github.com/user-attachments/assets/2d72c702-d3d8-43fb-811b-540c663a463f" />
Login successful.

[Back to top](#Index)

<a class="anchor" id="Book_Display"></a>
#### 3.2.3 Book Display

```html
{% extends "index.html"%}
{%block content %}
    <ul class="list-group">
    {% for book in books %}
        <li class="list-group-item">
            <img src="static/image{{book['id']}}.png" width="50"> 
            {{book['author']}},	
            {{book['title']}},
            {{book['country']}},
            {{book['language']}},
            {{book['year']}}
        </li>
    {% endfor %}
    </ul>
```
* The script loops through the books data and renders each book's details dynamically.
* Uses Bootstrap classes (list-group and list-group-item) for clean and responsive styling.
* Relies on the index.html structure and Flask's data-passing mechanisms for functionality.

<img width="800" height="400" alt="Screenshot 2025-01-15 at 4 09 39 PM" src="https://github.com/user-attachments/assets/a604f32b-3740-4b05-aeed-5f3a17fe1d3a" />
Books list display.

[Back to top](#Index)

<a class="anchor" id="Adding_Books"></a>
#### 3.2.4 Adding Books 

```html
{% extends "index.html"%}

{% block content %}
<h1> Add a book</h1>
<form method="POST" action="/addbook">
    author <input type="text" name="author" />
    <br>
    title <input type="text" name="title" />
    <br>
    <input type="submit">
  </form>
{% endblock %}
```
* Tightly integrated with the Flask backend.
* The /addbook route in app.py:
    * Processes the submitted data.
    * Checks for valid user roles (e.g., admin).
    * Adds the book to the books list.
* Dynamic and Reusable:
    * By extending index.html, the script minimizes redundancy.
    * Any updates to index.html (e.g., styling or layout changes) are automatically reflected here.
* Ease of Maintenance:
    * The script is simple and modular, focusing solely on the "Add a Book" functionality.
    * Backend changes to the /addbook route do not require modifications to the template.

<img width="800" height="400" alt="Screenshot 2025-01-15 at 4 10 01 PM" src="https://github.com/user-attachments/assets/46d25adb-ff8f-4ed0-9ed3-890719d0d9d3" />
Add a book based on user role.

<img width="800" height="400" alt="image" src="https://github.com/user-attachments/assets/bfe16531-0b8f-475f-beb5-582974f77cc6" />
User without a valid role to add books.

[Back to top](#Index)

<a class="anchor" id="Adding_Images"></a>
#### 3.2.5 Adding Images

```html
{% extends "index.html"%}
{%block content %}
    <div class='container'>
        <div class='row'>
            <div class='col'>
                <h1>Upload Image</h1> <br>

                <form action="/addimage" method="POST" enctype="multipart/form-data">
                    <div class="form-group">
                        <label>Select image</label>
                        <div class="custom-file">
                            <input type="file" class = "custom-file-input" name="image" id="image">
                            <label class="custom-file-label" for="image">Select image...</label>
                            <input type="number" class="form-control" aria-label="Book Number" name="number" id="number">
                            <label  for="number">Enter Book Number...</label>
                        </div>
                    </div>
                    <button type="submit" class="btn-lg btn-primary">Submit</button>
                </form>
            </div>
        </div>
    </div>
{%endblock%}
```
* The backend processes the uploaded image and saves it in the static directory with a unique name based on the provided book number.
* Responsive Design:
    * The script uses Bootstrap classes (container, row, col, form-group) to ensure a user-friendly, responsive layout.
* File Upload Functionality:
    * Allows users to upload an image file with ease, ensuring the backend can associate it with the correct book using the number input.
*Dynamic and Reusable:
    * By inheriting from index.html, the script focuses solely on the upload functionality while maintaining a consistent look and feel.
* User Guidance:
    * Labels and placeholders guide users in selecting an image and associating it with a book.
* Security and Validity:
    * Using enctype="multipart/form-data" ensures proper handling of uploaded files.
    * The /addimage route requires JWT authentication and admin privileges, as defined in app.py.

<img width="800" height="400" alt="Screenshot 2025-01-15 at 4 10 16 PM" src="https://github.com/user-attachments/assets/9f9cfe8c-a346-4fcf-b450-81760f76e006" />
Add an image for a book based on user role.

[Back to top](#Index)

<a class="anchor" id="Conclusion"></a>
## 4. Conclusion

The Books Web Application successfully integrates cutting-edge web technologies to create a secure, user-friendly platform for managing book collections. Its modular design ensures scalability and adaptability, while features like JWT-based authentication and image uploads demonstrate its comprehensive functionality. By prioritizing security, responsiveness, and maintainability, this project highlights the potential of Flask and Bootstrap to deliver high-quality web solutions. It serves as a foundation for future enhancements, offering opportunities to expand into more advanced features like search capabilities, display, real-time updates, and personalized recommendations.
