import React, { useState, useEffect } from "react";
import "./App.css";

const TaskForm = ({ onSubmit, task = {} }) => {
  const [name, setName] = useState(task.name || "");
  const [description, setDescription] = useState(task.description || "");
  const [error, setError] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!name.trim() || !description.trim()) {
      setError("Both fields are required.");
      return;
    }
    onSubmit({ name, description });
    setName("");
    setDescription("");
    setError("");
  };

  return (
    <form onSubmit={handleSubmit} className="task-form">
      <input
        type="text"
        placeholder="Task Name"
        value={name}
        onChange={(e) => setName(e.target.value)}
      />
      <textarea
        placeholder="Task Description"
        value={description}
        onChange={(e) => setDescription(e.target.value)}
      ></textarea>
      {error && <p className="error">{error}</p>}
      <button type="submit">{task.name ? "Update" : "Add"} Task</button>
    </form>
     );
    };

    const TaskItem = ({ task, onEdit, onDelete, onToggle }) => (
        <div className={`task-item ${task.completed ? "completed" : ""}`}>
          <h3>{task.name}</h3>
          <p>{task.description}</p>
          <div className="task-actions">
            <button onClick={() => onToggle(task.id)}>
              {task.completed ? "Undo" : "Complete"}
            </button>
            <button onClick={() => onEdit(task)}>Edit</button>
            <button onClick={() => onDelete(task.id)}>Delete</button>
          </div>
        </div>
      );
      
      const TaskList = ({ tasks, onEdit, onDelete, onToggle }) => (
        <div className="task-list">
          {tasks.map((task) => (
            <TaskItem
              key={task.id}
              task={task}
              onEdit={onEdit}
              onDelete={onDelete}
              onToggle={onToggle}
            />
        ))}
        </div>
      );
      
      const App = () => {
        const [tasks, setTasks] = useState(() => {
          const savedTasks = localStorage.getItem("tasks");
          return savedTasks ? JSON.parse(savedTasks) : [];
        });
        const [editingTask, setEditingTask] = useState(null);
      
        useEffect(() => {
          localStorage.setItem("tasks", JSON.stringify(tasks));
        }, [tasks]);
      
        const addTask = (task) => {
          setTasks([...tasks, { id: Date.now(), ...task, completed: false }]);
        };
      
        const updateTask = (updatedTask) => {
          setTasks(tasks.map((task) => (task.id === editingTask.id ? { ...task, ...updatedTask } : task)));
          setEditingTask(null);
        };

        const deleteTask = (id) => {
            if (window.confirm("Are you sure you want to delete this task?")) {
              setTasks(tasks.filter((task) => task.id !== id));
            }
          };
        
          const toggleTaskCompletion = (id) => {
            setTasks(tasks.map((task) => (task.id === id ? { ...task, completed: !task.completed } : task)));
          };
        
          return (
            <div className="app">
              <h1>To-Do List</h1>
              <TaskForm
                onSubmit={editingTask ? updateTask : addTask}
                task={editingTask}
              />
              <TaskList
                tasks={tasks}
                onEdit={(task) => setEditingTask(task)}
                onDelete={deleteTask}
                onToggle={toggleTaskCompletion}
              />
            </div>
          );
        };
        
        export default App;
        
