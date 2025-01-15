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
- [Conclusion](#Conclusion)
- [References](#References)


<a class="anchor" id="Abstract"></a>
##  Abstract




[Back to top](#Index)

<a class="anchor" id="Introduction"></a>
## 1. Introduction




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
     * index.html
     * register.html
     * books.html
     * addimage.html
     * addbook.html

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
```python
@app.route("/", methods=["GET"])
def firstRoute():
    return render_template("register.html")
```
* Renders the registration/login page when accessing the root URL.
  
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

```python
@app.route("/logout")
def logout():
    # invalidate the JWT token
    
    return "Logged Out of Books"
```
* Placeholder route to handle logout functionality.

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








