# Hackathon
VAST TC hackathon repo

# fork this repo 
from flask import Flask, request, jsonify from flask_sqlalchemy import SQLAlchemy from datetime import datetime

Initialize app and database

app = Flask(name) app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///complaints.db' db = SQLAlchemy(app)

Database model

class Complaint(db.Model): id = db.Column(db.Integer, primary_key=True) customer_name = db.Column(db.String(100), nullable=False) email = db.Column(db.String(120), nullable=False) category = db.Column(db.String(50), nullable=False) description = db.Column(db.Text, nullable=False) status = db.Column(db.String(20), default='Pending') created_at = db.Column(db.DateTime, default=datetime.utcnow)

Routes

@app.route('/complaints', methods=['POST']) def create_complaint(): data = request.json new_complaint = Complaint( customer_name=data['customer_name'], email=data['email'], category=data['category'], description=data['description'] ) db.session.add(new_complaint) db.session.commit() return jsonify({'message': 'Complaint created successfully!'}), 201

@app.route('/complaints', methods=['GET']) def get_complaints(): complaints = Complaint.query.all() output = [] for complaint in complaints: output.append({ 'id': complaint.id, 'customer_name': complaint.customer_name, 'email': complaint.email, 'category': complaint.category, 'description': complaint.description, 'status': complaint.status, 'created_at': complaint.created_at }) return jsonify(output)

@app.route('/complaints/int:id', methods=['PUT']) def update_complaint_status(id): data = request.json complaint = Complaint.query.get_or_404(id) complaint.status = data['status'] db.session.commit() return jsonify({'message': 'Complaint status updated successfully!'})

@app.route('/complaints/int:id', methods=['DELETE']) def delete_complaint(id): complaint = Complaint.query.get_or_404(id) db.session.delete(complaint) db.session.commit() return jsonify({'message': 'Complaint deleted successfully!'})

if name == 'main': db.create_all() app.run(debug=True)

