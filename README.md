pip install Flask
from flask import Flask, render_template, request, jsonify

app = Flask(__name__)

# This will hold the tasks for our To-Do List
tasks = []

@app.route('/')
def index():
    return render_template('index.html', tasks=tasks)

@app.route('/add', methods=['POST'])
def add_task():
    task = request.form['task']
    tasks.append(task)
    return jsonify({'task': task})

@app.route('/delete', methods=['POST'])
def delete_task():
    task = request.form['task']
    if task in tasks:
        tasks.remove(task)
    return jsonify({'task': task})

if __name__ == '__main__':
    app.run(debug=True)
python app.py
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>To-Do List</title>
    <link rel="stylesheet" href="/static/styles.css">
</head>
<body>
    <div class="container">
        <h1>To-Do List</h1>
        <input type="text" id="taskInput" placeholder="Enter a new task">
        <button id="addTaskBtn">Add Task</button>
        
        <ul id="taskList">
            {% for task in tasks %}
                <li>{{ task }} <button class="deleteBtn">Delete</button></li>
            {% endfor %}
        </ul>
    </div>
    <script src="/static/script.js"></script>
</body>
</html>
body {
    font-family: Arial, sans-serif;
    background-color: #f4f4f4;
    margin: 0;
    padding: 0;
}

.container {
    max-width: 600px;
    margin: 0 auto;
    padding: 20px;
    background-color: white;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

h1 {
    text-align: center;
}

input {
    width: 80%;
    padding: 10px;
    font-size: 16px;
    margin: 10px 0;
    border: 1px solid #ccc;
}

button {
    padding: 10px 20px;
    font-size: 16px;
    background-color: #4CAF50;
    color: white;
    border: none;
    cursor: pointer;
}

ul {
    list-style-type: none;
    padding: 0;
}

li {
    background-color: #f4f4f4;
    padding: 10px;
    margin: 5px 0;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.deleteBtn {
    background-color: red;
    color: white;
    border: none;
    padding: 5px 10px;
    cursor: pointer;
}
document.getElementById("addTaskBtn").addEventListener("click", function() {
    const taskInput = document.getElementById("taskInput");
    const task = taskInput.value;
    if (task) {
        fetch('/add', {
            method: 'POST',
            body: new URLSearchParams({ task }),
            headers: { 'Content-Type': 'application/x-www-form-urlencoded' }
        })
        .then(response => response.json())
        .then(data => {
            const taskList = document.getElementById("taskList");
            const li = document.createElement("li");
            li.textContent = data.task;
            const deleteBtn = document.createElement("button");
            deleteBtn.textContent = "Delete";
            deleteBtn.classList.add("deleteBtn");
            deleteBtn.addEventListener("click", function() {
                deleteTask(data.task, li);
            });
            li.appendChild(deleteBtn);
            taskList.appendChild(li);
            taskInput.value = ""; // Clear input
        });
    }
});

function deleteTask(task, li) {
    fetch('/delete', {
        method: 'POST',
        body: new URLSearchParams({ task }),
        headers: { 'Content-Type': 'application/x-www-form-urlencoded' }
    })
    .then(() => {
        li.remove();
    });
}
python app.py
