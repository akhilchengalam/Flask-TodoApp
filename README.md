1. Create virtualenv and install the requirements
    virtualenv -p python3 venv
    .\venv\Scipts\activate
    pip install -r requirements.txt


2. Create project directories and files
    - TodoApp/
        - app/
            - __init__.py
            - models.py
            - views.py
        - run.py

3. Copy the code to __init__.py file

`
    from flask import Flask
    from flask_sqlalchemy import SQLAlchemy
    
    app = Flask(__name__)
    
    app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///db.sqlite'
    app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
    db = SQLAlchemy(app)
    
    from app.views import *      
`    
4. Copy the code to models.py file

`
    from app import db

    class Todo(db.Model):
        id = db.Column(db.Integer, primary_key=True)
        title = db.Column(db.String(100))
        complete = db.Column(db.Boolean)`
        
5. Copy the code to run.py

`
    from app import app, db
    
    if __name__ == "__main__":
        db.create_all()
        app.run(debug=True)
`        
6. Create first view for the home page
    _Create views.py file and copy the code_
    
    
    `from flask import render_template, request, redirect, url_for
    from app import app, db
    from app.models import Todo
    
    
    @app.route("/")
    def home():
        todo_list = Todo.query.all()
        return render_template("base.html", todo_list=todo_list)

    Create base.html file inside templates folder
    # Copy the code from templates/base.html`
    
7. Run the application 
    **_python run.py_**
    
8. Create view for adding Todo notes
    _Add this to views.py_
    
    
    `@app.route("/add", methods=["POST"])
    def add():
        title = request.form.get("title")
        new_todo = Todo(title=title, complete=False)
        db.session.add(new_todo)
        db.session.commit()
        return redirect(url_for("home"))`
        
9. Create the view for updating a note
    _add this to views.py_
    

    `@app.route("/update/<int:todo_id>")
    def update(todo_id):
        todo = Todo.query.filter_by(id=todo_id).first()
        todo.complete = not todo.complete
        db.session.commit()
        return redirect(url_for("home"))`

        
10. Create view for deleting an item
    _add this to views.py_
    

    `@app.route("/delete/<int:todo_id>")
    def delete(todo_id):
        todo = Todo.query.filter_by(id=todo_id).first()
        db.session.delete(todo)
        db.session.commit()
        return redirect(url_for("home"))`