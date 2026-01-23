# User Dashboard Project
# ======================

"""
A user dashboard application that provides a simple and intuitive interface
for users to manage their account information and settings.

Features:
- User authentication and authorization
- Account profile management
- Settings and preferences management
- Data visualization and analytics

Requirements:
- Python 3.8+
- Flask 2.0+
- SQLite 3.0+
"""

import os
from flask import Flask, render_template, request, redirect, url_for
from flask_sqlalchemy import SQLAlchemy

# Create a new Flask application instance
app = Flask(__name__)

# Configure the application settings
app.config['SECRET_KEY'] = 'secret_key_here'
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///user_dashboard.db'

# Create a new SQLAlchemy instance
db = SQLAlchemy(app)

# Define the User model
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(64), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)

    def __repr__(self):
        return f"User('{self.username}', '{self.email}')"

# Define the Settings model
class Settings(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)
    theme = db.Column(db.String(64), nullable=False, default='light')
    language = db.Column(db.String(64), nullable=False, default='en')

    def __repr__(self):
        return f"Settings('{self.user_id}', '{self.theme}', '{self.language}')"

# Create the database tables
with app.app_context():
    db.create_all()

# Define the routes for the application
@app.route('/')
def index():
    return render_template('index.html')

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        username = request.form['username']
        password = request.form['password']
        user = User.query.filter_by(username=username).first()
        if user and user.password == password:
            return redirect(url_for('dashboard'))
    return render_template('login.html')

@app.route('/dashboard')
def dashboard():
    user = User.query.filter_by(id=1).first()
    settings = Settings.query.filter_by(user_id=1).first()
    return render_template('dashboard.html', user=user, settings=settings)

# Run the application
if __name__ == '__main__':
    app.run(debug=True)