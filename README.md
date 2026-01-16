# crud
Project Structure
student-management/
│── package.json
│── db.json
│── index.js

✅ 1. package.json (with ES Modules enabled)
{
  "name": "student-management",
  "version": "1.0.0",
  "description": "CRUD API using Express (ESM)",
  "type": "module",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {
    "express": "^4.19.2"
  }
}

✅ 2. db.json (initial data file)
{
  "students": [
    {
      "id": 1,
      "name": "Rahul",
      "course": "Computer Science",
      "year": 2
    }
  ]
}

✅ 3. index.js — FULL EXPRESS CRUD IMPLEMENTATION
import express from "express";
import fs from "fs";

const app = express();
app.use(express.json());

const DB_PATH = "./db.json";

// Utility to read DB file
function readDB() {
  const data = fs.readFileSync(DB_PATH, "utf-8");
  return JSON.parse(data);
}

// Utility to write to DB file
function writeDB(data) {
  fs.writeFileSync(DB_PATH, JSON.stringify(data, null, 2));
}

/* ---------------------------
   GET: Fetch all students
----------------------------*/
app.get("/students", (req, res) => {
  const db = readDB();
  res.json(db.students);
});

/* ---------------------------
   POST: Add new student
----------------------------*/
app.post("/students", (req, res) => {
  const { name, course, year } = req.body;

  if (!name || !course || !year) {
    return res.status(400).json({ message: "All fields are required" });
  }

  const db = readDB();
  const newStudent = {
    id: Date.now(),
    name,
    course,
    year
  };

  db.students.push(newStudent);
  writeDB(db);

  res.status(201).json({
    message: "Student added successfully",
    student: newStudent
  });
});

/* ---------------------------
   PUT: Update student by id
----------------------------*/
app.put("/students", (req, res) => {
  const { id, name, course, year } = req.body;

  if (!id) {
    return res.status(400).json({ message: "Student ID is required" });
  }

  const db = readDB();
  const student = db.students.find((s) => s.id === id);

  if (!student) {
    return res.status(404).json({ message: "Student not found" });
  }

  if (name) student.name = name;
  if (course) student.course = course;
  if (year) student.year = year;

  writeDB(db);

  res.json({
    message: "Student updated successfully",
    student
  });
});

/* ---------------------------
   DELETE: Remove student by ID
----------------------------*/
app.delete("/students/:id", (req, res) => {
  const id = Number(req.params.id);

  const db = readDB();
  const studentIndex = db.students.findIndex((s) => s.id === id);

  if (studentIndex === -1) {
    return res.status(404).json({ message: "Student not found" });
  }

  const deletedStudent = db.students.splice(studentIndex, 1);
  writeDB(db);

  res.json({
    message: "Student deleted successfully",
    deleted: deletedStudent[0]
  });
});

// Start server
app.listen(3000, () => {
  console.log("Server running on port 3000");
});

✅ How to Run the Project
1. Install dependencies
npm install

2. Start server
npm start


Server runs on:

👉 http://localhost:3000

✅ Testing (Use Postman)
GET All Students
GET /students

POST Create Student
POST /students
Body (JSON):
{
  "name": "Keerthi",
  "course": "AI & ML",
  "year": 1
}

PUT Update Student
PUT /students
Body (JSON):
{
  "id": 1,
  "name": "Rahul Kumar",
  "year": 3
}

DELETE Student
DELETE /students/1
