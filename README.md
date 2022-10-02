![CI logo](https://codeinstitute.s3.amazonaws.com/fullstack/ci_logo_small.png)

# Task Manager App - CRUD - with Python, Flask, SQLAlchemy, psycopg2 and  Materialize.

## Setting up a basic Flask Application

<details>
<summary>Basic Flask Setup</summary>

- pip3 install Flask-SQLAlchemy psycopg2

- Create a new file - touch env.py

- Set env.py by:

        import os   # so our hidden environment variables are visible within our system

        os.eviron.setdefault("IP","")
        os.eviron.setdefault("PORT","")
        os.eviron.setdefault("SECRET_KEY","")
        os.eviron.setdefault("DEBUG","")
        os.eviron.setdefault("DEVELOPMENT","")
        os.eviron.setdefault("DB_URL","")

- Create a package by:

  - create a new directory- mkdir taskmanager

  - create a new file - touch taskmanager/\_\_init\_\_.py

- Set up \_\_init\_\_.py file by:

        import os
        from flask import Flask
        from flask_sqlalchemy import SQLAlchemy
        if os.path.exists("env.py"):
            import env # noqa (noqa => no quality assurance)

        app = Flask(__name__)
        app.config["SECRET_KEY"] = os.environ.get("SECRET_KEY")
        app.config["SQLALCHEMY_DATABASE_URI"] = os.environ.get("DB_URL")

        db = SQLAlchemy(app)

        from taskmanager import routes # noqa

- Create a new file - touch taskmanager/routes.py
- Set routes.py by:

        from flask import render_template
        from taskmanager import app, db

        @app.route("/")
        def home():
            return render_template("base.html")
- Create new file that will run our App - touch run.py
- Set run.py by:

        import os
        from taskmanager import app

        if __name__ == "__main__":
            app.run(
            host=os.environ.get("IP"),
            port=int(os.environ.get("PORT")),
            debug=os.environ.get("DEBUG"),
            )

- Create a new directory - mkdir taskmanager/templates
- Create a new file - touch taskmanager/templates/base.html
- Create a Html5 boilerplate.
- Run the file - python3 run.py

</details>

## Creating the ORM Database

<details>
<summary>Basic Database setup</summary>

- Create a new file within our taskmanager package - touch taskmanager/models.py
- Setup models.py by importing db from taskmanager
- Create new class-based tables by:

        class Category(db.Model):
            # schema for the Category model
            id = db.Column(db.Integer, primary_key=True)
            category_name = db.Column(db.String(25), unique=True, nullable=False)
            tasks = db.relationship("Task", backref="category", cascade="all, delete", lazy=True)

            def __repr__(self):
            # __repr__ to represent themselves as a String
            return self.category_name
        

        class Task(db.Model):
            # schema for the Task model
            id = db.Column(db.Integer, primary_key=True)
            task_name = db.Column(db.String(50), unique=True, nullable=False)
            task_description = db.Column(db.Text, nullable=False)
            is_urgent = db.Column(db.Boolean, default=False, nullable=False)
            due_data = db.Column(db.Date, nullable=False)
            category_id = db.Column(db.Integer, db.ForeignKey("category.id", ondelete="CASCADE"), nullable=False)

            def __repr__(self):
            # __repr__ to represent themselves as a String
            return "#{0} - Task: {1} | Urgent: {2}".format(
                self.id, self.task_name, self.is_urgent
                )
- Return to  the routes file and at the top import

        from taskmanager.models import Category, Task

- Create the taskmanager database in the postgres CLI

        gitpod /workspace/Flask-SQLAlchemy-Task-Manager (main) $ set_pg
        gitpod /workspace/Flask-SQLAlchemy-Task-Manager (main) $ psql
        psql (12.12 (Ubuntu 12.12-1.pgdg20.04+1))
        Type "help" for help.

        postgres=# CREATE DATABASE taskmanager;
        CREATE DATABASE

- Connect to taskmanager database

        postgres=# \c taskmanager;
        You are now connected to database "taskmanager" as user "gitpod".
        taskmanager=# 

- Migrate and generate our new tables using python. (!IMPORTANT: This have to be performed every time we make changes to our models, ie: when adding a new column, so our database knows about the changes made)
  - Access the python3 interpreter
  - Import db from our taskmanager package
  - Perform db.create_all() method, now our models have been created.
  - Exit the interpreter - exit()
  
        gitpod /workspace/Flask-SQLAlchemy-Task-Manager (main) $ python3
        Python 3.8.11 (default, Sep 28 2022, 18:47:08) 
        [GCC 9.4.0] on linux
        Type "help", "copyright", "credits" or "license" for more information.
        >>> from taskmanager import db
        /workspace/.pip-modules/lib/python3.8/site-packages/flask_sqlalchemy/__init__.py:872: FSADeprecationWarning: SQLALCHEMY_TRACK_MODIFICATIONS adds significant overhead and will be disabled by default in the future.  Set it to True or False to suppress this warning.
        warnings.warn(FSADeprecationWarning(
        >>> db.create_all()
        >>> exit()

- To check if our table/s exists within our database:

        gitpod /workspace/Flask-SQLAlchemy-Task-Manager (main) $ psql -d taskmanager
        psql (12.12 (Ubuntu 12.12-1.pgdg20.04+1))
        Type "help" for help.

        taskmanager=# \dt
            List of relations
        Schema |   Name   | Type  | Owner  
        --------+----------+-------+--------
        public | category | table | gitpod
        public | task     | table | gitpod
        (2 rows)

        taskmanager=# \q

</details>

## Creating a Basic FrontEnd User Interface template using Template Inheritance

<details>
<summary>Basic FrontEnd HTML/CSS</summary>

- Add Materialize CDN (css and javascript) links to our base.html page
- Create a new directory called static within our taskmanager package 
and create our css and js directories and files.
Also, it might require a hard reload to see the changes made in this files.

        mkdir taskmanager/static

        mkdir taskmanager/static/css
        mkdir taskmanager/static/js

        touch taskmanager/static/css/style.css
        touch taskmanager/static/js/script.js

- Link our static files using url_for{{}} method.(paying attention to quotation marks)

        <link
        rel="stylesheet"
        href="{{ url_for('static', filename='css/style.css') }}"
        />

        <script src="{{ url_for('static', filename='js/script.js') }}"></script>

- Add a Mobile Collapse Navbar from Materialize Components

  - Add a \<header>...\</header> tags and paste navbar code within
  - Change Logo Name
  - Use url_for{{}} method to link our pages to routes file
  - Add font-awesome CDN link above the Materialize

  - Add the Sidenav initialization code to script.js

        document.addEventListener("DOMContentLoaded", function () {
        // sidenav initialization
        let sidenav = document.querySelectorAll(".sidenav");
        M.Sidenav.init(sidenav);
        });

- Add \<main>...\</main> tags and Jinja content blocks  within

        {% block content %} {% endblock %}

- Add Footer and customize
- Add sticky footer code to style.css file
- Create new template html file 

        touch taskmanager/templates/tasks.html

- Add Jinja templates to tasks.html to extend our base.html

        {% extends "base.html" %} 
        {% block content %} 

        {% endblock %}

</details>

## [C]reate new Categories for our Database

<details>
<summary>Basic setup for adding new category</summary>

- Add two new template html files

        touch taskmanager/templates/categories.html
        touch taskmanager/templates/add_category.html

- Extend the files from the base.html

        {% extends "base.html" %}
        {% block content %}

        {% endblock %}

- Add a button to categories.html that invokes the add_category() function

        <a
        href="{{ url_for('add_category') }}"
        class="btn-large light-blue darken-2"
        >

- Create a new route that populates categories.html and link it to our navbar

        @app.route("/categories")
        def categories():
        return render_template("categories.html")

        <li><a href="{{ url_for('categories') }}">Categories</a></li>

- Create a new route that render the template to add a new category, using the methods "GET" and "POST"

        @app.route("/add_category", methods=["GET", "POST"])
        def add_category():
        return render_template("add_category.html")

- Add a form to the add_category.html template using the "POST" method

        <form class="col s12" method="POST" action="{{ url_for('add_category') }}">
        <!-- category name -->
        <div class="row">
        <div class="input-field col s12">
            <i class="fas fa-folder-open prefix light-blue-text text-darken-4"></i>
            <input
            id="category_name"
            name="category_name"
            minlength="3"
            maxlength="25"
            type="text"
            class="validate"
            required
            />
            <label for="category_name">Category Name</label>
        </div>
        </div>
        <!-- ./category name -->

        <!-- submit button -->
        <div class="row">
        <div class="col s12 center-align">
            <button type="submit" class="btn-large light-blue darken-1">
            Add Category <i class="fas fa-plus-square right"></i>
            </button>
        </div>
        </div>
        <!-- ./submit button -->
        </form>

- Import request, redirect and url_for from flask

- Add the request method functionality to our add_category function

        if request.method == "POST":
            category = Category(category_name=request.form.get("category_name"))
            db.session.add(category)
            db.session.commit()
            return redirect(url_for("categories"))

</details>

## [R]ead Categories from our Database

<details>
<summary>Basics setup for viewing categories</summary>

- Add a card-panel to the categories.html template

        <div class="row">
        <div class="col s12 m6 l3">
            <div class="card light-blue darken-4 center-align">
            <div class="card-content white-text">
                <span class="card-title">Card Title</span>
            </div>
            <div class="card-action">
                <a href="#" class="btn green accent-4">Edit</a>
                <a href="#" class="btn red">Delete</a>
            </div>
            </div>
        </div>
        </div>

- Add a query for all categories sorted by name, to the routes categories function

        @app.route("/categories")
        def categories():
            categories = list(Category.query.order_by(Category.category_name).all())
            return render_template("categories.html", categories=categories)

- Add a loop using Jinja template format, to the categories card

        {% for category in categories %}
        <div class="col s12 m6 l3">
            <div class="card light-blue darken-4 center-align">
            <div class="card-content white-text">
                <span class="card-title">{{ category.category_name }}</span>
            </div>
            <div class="card-action">
                <a href="#" class="btn green accent-4">Edit</a>
                <a href="#" class="btn red">Delete</a>
            </div>
            </div>
        </div>
        {% endfor %}

</details>

## [U]pdate the Database records

<details>
<summary>Basic setup for editing our Database</summary>

- Add a new file to the templates directory

        By making a copy of the add_category.html file and changing all necessary wording to edit.

        ie: edit_category.html, action="{{ url_for('edit_category') }}", etc...

- Create a new function in the routes file called edit_category

        @app.route("/edit_category/<int:category_id>", methods=["GET", "POST"])
        def edit_category(category_id):
            category = Category.query.get_or_404(category_id)
            if request.method == "POST":
                category.category_name = request.form.get("category_name")
                db.session.commit()
                return redirect(url_for("categories"))
            return render_template("edit_category.html", category=category)

</details>

## [D]elete categories 

<details>
<summary>Basic setup for deleting records from the database</summary>

- Create a new function in the routes file

        @app.route("/delete_category/<int:category_id>")
        def delete_category(category_id):
        category = Category.query.get_or_404(category_id)
        db.session.delete(category)
        db.session.commit()
        return redirect(url_for("categories"))

- Update delete link in categories.html

        href="{{ url_for('delete_category', category_id=category.id) }}"

</details>

## Addding Tasks
<details>
<summary>Basic setup for adding tasks to our database</summary>

- Add a link to tasks.html that invokes the add_task function

        <a href="{{ url_for('add_task') }}" class="btn-large light-blue darken-2">
        Add Task <i class="fas fa-plus-square right"></i>
        </a>

- Add the add_task function to routes file

        @app.route("/add_task", methods=["GET", "POST"])
        def add_task():

                db.session.add(task)
                db.session.commit()
                return redirect(url_for("home"))
            return render_template("add_task.html", categories=categories)

- Add a new file to templates directory called add_task.html

        By making a copy of the add_category.html file and changing all necessary wording to task.

- Add more form fields for:

        Task description
        Due date
        Is urgent
        Category Id

- Initialize form with Javascript

        // select initialization
        let selects = document.querySelectorAll("select");
        M.FormSelect.init(selects);

</details>

## Viewing New Tasks

<details>
<summary>Basic setup for displaying the  new tasks created</summary>

- Add a query all our task to the routes home function

        tasks = list(Task.query.order_by(Task.id).all())

- Add a materialize collapsible to task.html
- Initialize the collapsible with Javascript

</details>

## Edit  Tasks

<details>
<summary>Basic setup for editing the tasks created</summary>

- Create a new function edit_task from coping edit_categories  function
- Create a new template for edit_task.html by coping edit_category.html

</details>

## Delete task

<details>
<summary>Basic setup for deleting a task record</summary>

- Create a new function delete_task in the routes file from delete_category

- Add a delete link in task.html

        href="{{ url_for('delete_task', task_id=task.id) }}"

</details>

## Deployment to Heroku

<details>
<summary>Basic deployment instructions</summary>

- Check install packages

         $ pip3 list

        Package          Version
        ---------------- -------
        click            8.1.3
        Flask            2.2.2
        Flask-SQLAlchemy 2.5.1
        greenlet         1.1.3
        itsdangerous     2.1.2
        psycopg2         2.9.3
        SQLAlchemy       1.4.41
        Werkzeug         2.2.2

- Freeze and install packages to requirements.txt file

        $ pip3 freeze --local > requirements.txt

- Create a Procfile to tell Heroku which file runs the app

        $ echo web: python run.py > Procfile
        ### delete this blank line after code line to avoid problems when running the app ###


</details>

----

Happy Coding!