# Task Management API Assignment

## Overview
In this assignment, you'll build a RESTful API for a task management application using Node.js, Express, MongoDB, and dotenv.

### Objectives
- Understand RESTful services.
- Learn Express.js and MongoDB.
- Manage environment variables with dotenv.
- Implement CRUD operations.

## Requirements

### 1. Setup and Configuration
1. **Initialize a new Node.js Project**
   - In your project directory, run:
     ```bash
     npm init -y
     ```
     This will create a `package.json` file.

2. **Install Required Packages**
   - Install the necessary packages using:
     ```bash
     npm install express mongoose dotenv nodemon
     ```
     - `express`: Framework for handling HTTP requests.
     - `mongoose`: ORM for MongoDB.
     - `dotenv`: Manages environment variables.
     - `nodemon`: Automatically restarts the server on code changes.

3. **Configure `.env` File**
   - Create a `.env` file in the root directory and add the following:
     ```plaintext
     MONGO_URI=mongodb+srv://<username>:<password>@cluster.mongodb.net/taskdb
     PORT=3000
     ```
     - Replace `<username>` and `<password>` with your MongoDB Atlas credentials.
     - `PORT` is the port number where the server will run.

### 2. Database Setup
1. **Connect to MongoDB**xcvxcv
   - Create a `db.js` file to manage MongoDB connection:
     ```javascript
     const mongoose = require('mongoose');
     const dotenv = require('dotenv');

     dotenv.config();

     mongoose.connect(process.env.MONGO_URI, {
       useNewUrlParser: true,
       useUnifiedTopology: true
     })
     .then(() => console.log('MongoDB connected'))
     .catch(err => console.error('MongoDB connection error:', err));
     ```

2. **Design the Task Schema**
   - Create a `taskModel.js` file:
     ```javascript
     const mongoose = require('mongoose');

     const taskSchema = new mongoose.Schema({
       title: { type: String, required: true },
       description: String,
       status: { type: String, default: 'pending' },
       created_at: { type: Date, default: Date.now },
       updated_at: { type: Date, default: Date.now }
     });

     module.exports = mongoose.model('Task', taskSchema);
     ```

### 3. API Endpoints
1. **Implement Endpoints**
   - Create `app.js`:
     ```javascript
     const express = require('express');
     const mongoose = require('mongoose');
     const dotenv = require('dotenv');
     const Task = require('./taskModel');
     require('./db'); // Connects to MongoDB

     dotenv.config();
     const app = express();
     const port = process.env.PORT || 3000;

     app.use(express.json());

     // GET all tasks
     app.get('/tasks', async (req, res) => {
       try {
         const tasks = await Task.find();
         res.status(200).json(tasks);
       } catch (err) {
         res.status(500).json({ error: err.message });
       }
     });

     // GET a task by ID
     app.get('/tasks/:id', async (req, res) => {
       try {
         const task = await Task.findById(req.params.id);
         if (!task) return res.status(404).json({ error: 'Task not found' });
         res.status(200).json(task);
       } catch (err) {
         res.status(500).json({ error: err.message });
       }
     });

     // POST create a task
     app.post('/tasks', async (req, res) => {
       try {
         const task = new Task(req.body);
         const savedTask = await task.save();
         res.status(201).json(savedTask);
       } catch (err) {
         res.status(400).json({ error: err.message });
       }
     });

     // PUT update a task by ID
     app.put('/tasks/:id', async (req, res) => {
       try {
         const updatedTask = await Task.findByIdAndUpdate(req.params.id, req.body, { new: true, runValidators: true });
         if (!updatedTask) return res.status(404).json({ error: 'Task not found' });
         res.status(200).json(updatedTask);
       } catch (err) {
         res.status(400).json({ error: err.message });
       }
     });

     // DELETE a task by ID
     app.delete('/tasks/:id', async (req, res) => {
       try {
         const deletedTask = await Task.findByIdAndDelete(req.params.id);
         if (!deletedTask) return res.status(404).json({ error: 'Task not found' });
         res.status(200).json(deletedTask);
       } catch (err) {
         res.status(500).json({ error: err.message });
       }
     });

     // Error-handling middleware
     app.use((err, req, res, next) => {
       res.status(500).json({ error: err.message });
     });

     app.listen(port, () => console.log(`Server running on http://localhost:${port}`));
     ```

### 4. Testing
**1. Manually Test the API**
- Use Postman or another API testing tool to verify the functionality of the endpoints.

### 5. Submission Guidelines
**1. Push Code to GitHub**
- Create a new GitHub repository and push the project.

**2. Create a README.md File**
- Include:
  - Project description.
  - Setup instructions.
  - API endpoints with examples:
    ```markdown
    # Task Management API

    ## Project Description
    A simple RESTful API for managing tasks, built with Node.js, Express, and MongoDB.

    ## Setup Instructions
    1. Clone the repository.
    2. Run `npm install` to install dependencies.
    3. Create a `.env` file with the following contents:
       ```
       MONGO_URI=your-mongodb-uri
       PORT=3000
       ```
    4. Use `nodemon` to run the app:
       ```
       npx nodemon app.js
       ```
    5. Alternatively, you can also use `node app.js` to start the server.

    ## API Endpoints
    - `GET /tasks`: Fetch all tasks.
    - `GET /tasks/:id`: Fetch a task by ID.
    - `POST /tasks`: Create a new task.
    - `PUT /tasks/:id`: Update a task by ID.
    - `DELETE /tasks/:id`: Delete a task by ID.
    ```
