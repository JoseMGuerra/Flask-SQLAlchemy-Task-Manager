![CI logo](https://codeinstitute.s3.amazonaws.com/fullstack/ci_logo_small.png)

# Task Manager App - CRUD - with Python, Flask, SQLAlchemy, psycopg2 and  Materialize.

## Setting up a basic Flask Application

<details>
<summary>Basic Setup</summary>

- pip3 install Flask-SQLAlchemy psycopg2

- Create a new file - touch env.py

- Set env.py by:

        import os

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
            import env # noqa

        app = Flask(__name__)
        app.config["SECRET_KEY"] = os.environ.get("SECRET_KEY")
        app.config["SQLALCHEMY_DATABASE_URI"] = os.environ.get("DB_URL")

        db = SQLAlchemy(app)

        from taskmanager import routes # noq

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
---

Happy coding!
