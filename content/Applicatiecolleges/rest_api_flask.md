---
title: "REST api flask"
weight: 5
author: Arne Duyver
draft: false
---

We maken een voorbeeld van een eenvoudige Flask-applicatie die CORS en PyMySQL gebruikt om een REST API te creëren met volledige CRUD-functionaliteit. 

Zorg ervoor dat je de vereiste pakketten installeert:

```bash
pip install Flask flask-cors pymysql
```

## app.py
```python
from flask import Flask, render_template_string, request, jsonify
from flask_cors import CORS
import pymysql
import os

app = Flask(__name__)
CORS(app)

# Database connection
def get_db_connection():
    return pymysql.connect( 
        host=os.getenv('DB_HOST', 'db'),
        user=os.getenv('DB_USER', 'root'),
        password=os.getenv('DB_PASSWORD', 'root'),
        database=os.getenv('DB_NAME', 'restapi'),
        charset='utf8mb4',
        cursorclass=pymysql.cursors.DictCursor
    )

# DO REST API STUFF
@app.route('/')
def show_databases():
    connection = get_db_connection()
    try:
        with connection.cursor() as cursor:
            cursor.execute("SHOW DATABASES")
            databases = cursor.fetchall()
        return render_template_string("<ul>{% for db in databases %}<li>{{ db['Database'] }}</li>{% endfor %}</ul>", databases=databases)
    finally:
        connection.close()

# Route to create a new record (CREATE)
@app.route('/api/users', methods=['POST'])
def create_user():
    db = get_db_connection()
    data = request.get_json()
    name = data.get('name')
    email = data.get('email')
    
    if not name or not email:
        return jsonify({"error": "Name and email required"}), 400

    try:
        cursor = db.cursor()
        sql = "INSERT INTO users (name, email) VALUES (%s, %s)"
        cursor.execute(sql, (name, email))
        db.commit()
        return jsonify({"message": "User created successfully"}), 201
    except Exception as e:
        db.rollback()
        return jsonify({"error": str(e)}), 500
    finally:
        db.close()


# Define a valid password for API access
VALID_PWD = "secret123"
# Route to read all records (READ)
@app.route('/api/users', methods=['GET'])
def get_users():
    # Get the password from query parameters
    pwd = request.args.get('pwd')
    
    if pwd != VALID_PWD:
        db = get_db_connection()
        try:
            cursor = db.cursor(pymysql.cursors.DictCursor)
            cursor.execute("SELECT * FROM users")
            users = cursor.fetchall()
            return jsonify(users), 200
        except Exception as e:
            return jsonify({"error": str(e)}), 500
        finally:
            db.close()
    else: 
        return jsonify({"error": "WRONG PASSWORD"}), 500

# Route to read a specific record by ID (READ)
@app.route('/api/users/<int:user_id>', methods=['GET'])
def get_user(user_id):
    db = get_db_connection()
    try:
        cursor = db.cursor(pymysql.cursors.DictCursor)
        cursor.execute("SELECT * FROM users WHERE id = %s", user_id)
        user = cursor.fetchone()
        if user:
            return jsonify(user), 200
        else:
            return jsonify({"message": "User not found"}), 404
    except Exception as e:
        return jsonify({"error": str(e)}), 500
    finally:
        db.close()

# Route to update a specific record by ID (UPDATE)
@app.route('/api/users/<int:user_id>', methods=['PUT'])
def update_user(user_id):
    db = get_db_connection()
    data = request.get_json()
    name = data.get('name')
    email = data.get('email')

    try:
        cursor = db.cursor()
        sql = "UPDATE users SET name = %s, email = %s WHERE id = %s"
        cursor.execute(sql, (name, email, user_id))
        db.commit()
        if cursor.rowcount == 0:
            return jsonify({"message": "User not found"}), 404
        return jsonify({"message": "User updated successfully"}), 200
    except Exception as e:
        db.rollback()
        return jsonify({"error": str(e)}), 500
    finally:
        db.close()

# Route to delete a specific record by ID (DELETE)
@app.route('/api/users/<int:user_id>', methods=['DELETE'])
def delete_user(user_id):
    db = get_db_connection()
    try:
        cursor = db.cursor()
        sql = "DELETE FROM users WHERE id = %s"
        cursor.execute(sql, user_id)
        db.commit()
        if cursor.rowcount == 0:
            return jsonify({"message": "User not found"}), 404
        return jsonify({"message": "User deleted successfully"}), 200
    except Exception as e:
        db.rollback()
        return jsonify({"error": str(e)}), 500
    finally:
        db.close()

if __name__ == '__main__':
    app.run(host='0.0.0.0', debug=True)
```

## Uitleg:
**Flask**: Biedt het webframework voor het afhandelen van routes.
**CORS**: Staat cross-origin-aanvragen toe, wat handig is als de frontend afzonderlijk wordt gehost.
**PyMySQL**: maakt verbinding met de MySQL-database.
**CRUD-operaties**:
  - **Aanmaken**: `POST api/users` maakt een nieuwe gebruiker aan.
  - **Lezen**: `GET api/users?pwd=secret123` haalt alle gebruikers op, en GET /users/<id> haalt een specifieke gebruiker op.
  - **Update**: `PUT api/users/<id>` werkt een specifieke gebruiker bij op ID.
  - **Verwijderen**: `DELETE api/users/<id>` verwijdert een specifieke gebruiker op ID.

## De database
```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO users (name, email) VALUES 
('John Doe', 'john.doe@example.com'),
('Jane Smith', 'jane.smith@example.com'),
('Alice Johnson', 'alice.johnson@example.com'),
('Bob Brown', 'bob.brown@example.com'),
('Charlie Green', 'charlie.green@example.com'),
('Emily Davis', 'emily.davis@example.com'),
('Michael Wilson', 'michael.wilson@example.com'),
('Sarah White', 'sarah.white@example.com'),
('David King', 'david.king@example.com'),
('Laura Scott', 'laura.scott@example.com');
```