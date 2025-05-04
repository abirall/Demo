
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Employee Directory</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f7f9fc;
      margin: 0;
      padding: 20px;
    }

    h1 {
      text-align: center;
      color: #333;
    }

    .form-container, .list-container {
      max-width: 600px;
      margin: 20px auto;
      background: #fff;
      padding: 20px;
      box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
      border-radius: 8px;
    }

    input {
      width: 100%;
      padding: 10px;
      margin: 6px 0;
      border: 1px solid #ccc;
      border-radius: 5px;
    }

    button {
      padding: 10px 15px;
      background-color: #007bff;
      border: none;
      color: white;
      border-radius: 5px;
      cursor: pointer;
    }

    button:hover {
      background-color: #0056b3;
    }

    ul {
      list-style: none;
      padding: 0;
    }

    li {
      background: #f1f1f1;
      margin: 8px 0;
      padding: 10px;
      border-radius: 5px;
    }

    .emp-title {
      font-weight: bold;
    }
  </style>
</head>
<body>
  <h1>Employee Directory</h1>

  <div class="form-container">
    <input type="text" id="name" placeholder="Name" />
    <input type="email" id="email" placeholder="Email" />
    <input type="text" id="position" placeholder="Position" />
    <button onclick="addEmployee()">Add Employee</button>
  </div>

  <div class="list-container">
    <h2>Employee List</h2>
    <ul id="employeeList"></ul>
  </div>

  <script>
    const employees = [];

    function addEmployee() {
      const name = document.getElementById('name').value.trim();
      const email = document.getElementById('email').value.trim();
      const position = document.getElementById('position').value.trim();

      if (name && email && position) {
        employees.push({ name, email, position });
        displayEmployees();
        document.getElementById('name').value = '';
        document.getElementById('email').value = '';
        document.getElementById('position').value = '';
      } else {
        alert('Please fill in all fields');
      }
    }

    function displayEmployees() {
      const list = document.getElementById('employeeList');
      list.innerHTML = '';
      employees.forEach((emp, index) => {
        const item = document.createElement('li');
        item.innerHTML = `<span class="emp-title">${emp.name}</span> – ${emp.email} – ${emp.position}`;
        list.appendChild(item);
      });
    }
  </script>
</body>
</html>

