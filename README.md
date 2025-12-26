# Club-136-Financials
Club 136 Financials
from flask import Flask, request, jsonify
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)

# Configuration for a local SQLite database
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///club136_finance.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
db = SQLAlchemy(app)

# Database Model for Members
class Member(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100), nullable=False)
    balance = db.Column(db.Float, default=0.0)

# Create the database
with app.app_context():
    db.create_all()

@app.route('/add_member', methods=['POST'])
def add_member():
    data = request.get_json()
    new_member = Member(name=data['name'], balance=data['amount'])
    db.session.add(new_member)
    db.session.commit()
    return jsonify({"message": f"Member {data['name']} added successfully!"})

@app.route('/members', methods=['GET'])
def get_members():
    members = Member.query.all()
    output = []
    for m in members:
        output.append({"id": m.id, "name": m.name, "balance": m.balance})
    return jsonify(output)

if __name__ == '__main__':
    app.run(debug=True)
