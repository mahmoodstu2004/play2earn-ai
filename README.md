import subprocess
import os

# Step 1: Create and activate a virtual environment
subprocess.run(["python3", "-m", "venv", "venv"])

# Activate virtual environment (Windows)
if os.name == 'nt':
    activate_env = os.path.join("venv", "Scripts", "activate.bat")
else:  # Activate virtual environment (Unix or MacOS)
    activate_env = os.path.join("venv", "bin", "activate")

# Step 2: Install necessary libraries
subprocess.run([activate_env + " && pip install Flask Flask-SQLAlchemy Flask-Bcrypt Flask-JWT-Extended"], shell=True)

# Step 3: Create requirements.txt
subprocess.run([activate_env + " && pip freeze > requirements.txt"], shell=True)





project_specifications = """
# Play2Earn.ai Platform Development Guide

## Introduction

**Mission Statement:**
Play2Earn.ai aims to revolutionize the way individuals interact with AI by creating a gamified platform where users can earn cryptocurrency tokens through valuable contributions to AI model training and development. Our mission is to democratize AI development, making it accessible and rewarding for everyone.

## Project Overview
This development guide provides a comprehensive week-by-week plan to build the Play2Earn.ai platform using Python and its libraries. The project includes frontend development with React.js, backend development with Flask, smart contract integration with Ethereum, and database management with SQLAlchemy.

## Agile Development Plan
### Week 1: Project Setup and Initial Development

**Objectives:**
- Define project scope and requirements.
- Set up development environment.
- Establish version control with Git.
- Begin basic backend setup.

**Tasks:**
- Create a project repository on GitHub.
- Set up virtual environments and install necessary libraries.
- Outline detailed project specifications and requirements.
- Implement basic Flask app setup with SQLAlchemy.

**Deliverables:**
- Project repository.
- Development environment setup.
- Detailed project specification document.
- Basic Flask app with SQLAlchemy setup.
"""

with open("project_specifications.md", "w") as f:
    f.write(project_specifications)




import os

# Create necessary directories and files
os.makedirs("app", exist_ok=True)

files_content = {
    "app/__init__.py": """
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_bcrypt import Bcrypt
from flask_jwt_extended import JWTManager

db = SQLAlchemy()
bcrypt = Bcrypt()
jwt = JWTManager()

def create_app():
    app = Flask(__name__)
    app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///play2earn.db'
    app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
    app.config['JWT_SECRET_KEY'] = 'your_secret_key'
    db.init_app(app)
    bcrypt.init_app(app)
    jwt.init_app(app)

    from .routes import main
    app.register_blueprint(main)

    return app
""",
    "app/models.py": """
from . import db

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    password = db.Column(db.String(120), nullable=False)

class Task(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(200), nullable=False)
    description = db.Column(db.Text, nullable=False)
    reward = db.Column(db.Float, nullable=False)

class Reward(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)
    task_id = db.Column(db.Integer, db.ForeignKey('task.id'), nullable=False)
    amount = db.Column(db.Float, nullable=False)
""",
    "app/routes.py": """
from flask import Blueprint, jsonify

main = Blueprint('main', __name__)

@main.route('/')
def index():
    return jsonify({"message": "Welcome to Play2Earn.ai"})
""",
    "run.py": """
from app import create_app, db

app = create_app()

with app.app_context():
    db.create_all()

if __name__ == '__main__':
    app.run(debug=True)
"""
}

for file_path, content in files_content.items():
    with open(file_path, "w") as f:
        f.write(content)



import subprocess

try:
    # Add the changes to Git and make an initial commit
    subprocess.run(["git", "add", "."], check=True)
    subprocess.run(["git", "commit", "-m", "Initial commit with basic Flask setup and database models"], check=True)
    
    # Push the changes to GitHub
    subprocess.run(["git", "push", "origin", "main"], check=True)
except subprocess.CalledProcessError as e:
    print(f"Error occurred: {e}")
except FileNotFoundError as e:
    print(f"Git not found: {e}")
