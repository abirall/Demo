employee-directory/
├── backend/
│   ├── server.js
│   ├── .env
│   ├── models/
│   │   └── Employee.js
│   └── routes/
│       └── employeeRoutes.js
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── App.js
│   │   └── index.js
│   ├── package.json
│   └── .gitignore
├── jenkins/
│   └── Jenkinsfile
├── .gitignore
└── README.md

// backend/server.js
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');
require('dotenv').config();

const employeeRoutes = require('./routes/employeeRoutes');

const app = express();
app.use(cors());
app.use(express.json());
app.use('/api/employees', employeeRoutes);

mongoose.connect(process.env.MONGO_URI)
    .then(() => {
        console.log('MongoDB connected');
        app.listen(5000, () => console.log('Server running on port 5000'));
    })
    .catch((err) => console.error(err));

// backend/.env
MONGO_URI=mongodb://localhost:27017/employeeDB

// backend/models/Employee.js
const mongoose = require('mongoose');

const employeeSchema = new mongoose.Schema({
    name: String,
    email: String,
    position: String,
});

module.exports = mongoose.model('Employee', employeeSchema);

// backend/routes/employeeRoutes.js
const express = require('express');
const router = express.Router();
const Employee = require('../models/Employee');

router.post('/', async (req, res) => {
    try {
        const employee = new Employee(req.body);
        await employee.save();
        res.status(201).json(employee);
    } catch (err) {
        res.status(400).json({ message: err.message });
    }
});

router.get('/', async (req, res) => {
    try {
        const employees = await Employee.find();
        res.json(employees);
    } catch (err) {
        res.status(500).json({ message: err.message });
    }
});

module.exports = router;

// frontend/src/App.js
import React, { useEffect, useState } from 'react';
import axios from 'axios';

function App() {
  const [employees, setEmployees] = useState([]);
  const [form, setForm] = useState({ name: '', email: '', position: '' });

  const fetchEmployees = async () => {
    const res = await axios.get('http://localhost:5000/api/employees');
    setEmployees(res.data);
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    await axios.post('http://localhost:5000/api/employees', form);
    setForm({ name: '', email: '', position: '' });
    fetchEmployees();
  };

  useEffect(() => {
    fetchEmployees();
  }, []);

  return (
    <div>
      <h1>Employee Directory</h1>
      <form onSubmit={handleSubmit}>
        <input placeholder="Name" value={form.name} onChange={e => setForm({...form, name: e.target.value})} />
        <input placeholder="Email" value={form.email} onChange={e => setForm({...form, email: e.target.value})} />
        <input placeholder="Position" value={form.position} onChange={e => setForm({...form, position: e.target.value})} />
        <button type="submit">Add</button>
      </form>
      <ul>
        {employees.map(emp => (
          <li key={emp._id}>{emp.name} - {emp.email} - {emp.position}</li>
        ))}
      </ul>
    </div>
  );
}

export default App;

// frontend/src/index.js
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);

// frontend/.gitignore
node_modules
build

// frontend/package.json (minimal setup)
{
  "name": "frontend",
  "version": "1.0.0",
  "private": true,
  "dependencies": {
    "axios": "^1.6.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-scripts": "5.0.1"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build"
  }
}

// jenkins/Jenkinsfile
pipeline {
    agent any
    environment {
        NODE_ENV = 'production'
    }
    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/YOUR_USERNAME/employee-directory.git'
            }
        }

        stage('Build Backend') {
            steps {
                dir('backend') {
                    sh 'npm install'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying to EC2...'
                // Add SSH or SCP deploy logic here
            }
        }
    }
}

// .gitignore
node_modules
.env
frontend/build

// README.md
# Employee Directory Web App

A full-stack web application where employees can be added and viewed. Built with React, Express, MongoDB, and Jenkins for CI/CD.

## How to Run

### Backend
```bash
cd backend
npm install
node server.js
```

### Frontend
```bash
cd frontend
npm install
npm start
```

Make sure MongoDB is running locally or replace `MONGO_URI` with your cloud MongoDB URI.

