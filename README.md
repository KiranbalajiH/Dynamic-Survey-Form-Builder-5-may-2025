# React Survey App

A simple React application with two modes:

- **Build Mode**: Create a custom survey.
- **Fill Mode**: Fill out the survey and view a summary of responses.

Built using only `useState`, `useEffect`, and conditional rendering. No external routing or libraries.

---

## Features

###  Build Mode
- Add questions with:
  - Text input
  - Radio buttons
  - Checkboxes
- Specify options (comma-separated) for radio and checkbox types
- Remove questions

### 📝 Fill Mode
- Render form dynamically based on questions
- Input fields match the type of question
- On submit, shows summary of answers

---

## 🛠️ Tech Stack

- React (with Hooks)
- Vite (optional)
- No external routing libraries
- Styling via CSS
- Axios (optional, not used here)

---

## 📂 File Structure

src/
├── App.jsx
├── main.jsx
├── index.css
public/
├── index.html

yaml
Copy
Edit

---
```
🔹 main.jsx
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App.jsx";
import "./index.css";

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
🔹 App.jsx
jsx
Copy
Edit
import React, { useState } from "react";

function App() {
  const [mode, setMode] = useState("build");
  const [questions, setQuestions] = useState([]);
  const [newQuestion, setNewQuestion] = useState({ text: "", type: "text", options: "" });
  const [responses, setResponses] = useState({});
  const [submitted, setSubmitted] = useState(false);

  const addQuestion = () => {
    if (!newQuestion.text.trim()) return;
    const question = {
      id: Date.now(),
      text: newQuestion.text,
      type: newQuestion.type,
      options: newQuestion.type !== "text" ? newQuestion.options.split(",").map(opt => opt.trim()) : [],
    };
    setQuestions([...questions, question]);
    setNewQuestion({ text: "", type: "text", options: "" });
  };

  const removeQuestion = (id) => {
    setQuestions(questions.filter(q => q.id !== id));
  };

  const handleChange = (id, value) => {
    setResponses(prev => ({ ...prev, [id]: value }));
  };

  const handleCheckboxChange = (id, option) => {
    const current = responses[id] || [];
    const updated = current.includes(option)
      ? current.filter(o => o !== option)
      : [...current, option];
    handleChange(id, updated);
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    setSubmitted(true);
  };

  return (
    <div className="container">
      <h1>Survey App</h1>
      <div className="mode-toggle">
        <button onClick={() => setMode("build")}>Build Mode</button>
        <button onClick={() => setMode("fill")}>Fill Mode</button>
      </div>

      {mode === "build" && (
        <div className="build-mode">
          <h2>Create a Question</h2>
          <input
            placeholder="Question text"
            value={newQuestion.text}
            onChange={(e) => setNewQuestion({ ...newQuestion, text: e.target.value })}
          />
          <select
            value={newQuestion.type}
            onChange={(e) => setNewQuestion({ ...newQuestion, type: e.target.value })}
          >
            <option value="text">Text</option>
            <option value="radio">Radio</option>
            <option value="checkbox">Checkbox</option>
          </select>
          {(newQuestion.type === "radio" || newQuestion.type === "checkbox") && (
            <input
              placeholder="Options (comma separated)"
              value={newQuestion.options}
              onChange={(e) => setNewQuestion({ ...newQuestion, options: e.target.value })}
            />
          )}
          <button onClick={addQuestion}>Add Question</button>

          <ul className="question-list">
            {questions.map((q) => (
              <li key={q.id}>
                <strong>{q.text}</strong> ({q.type})
                <button onClick={() => removeQuestion(q.id)}>Remove</button>
              </li>
            ))}
          </ul>
        </div>
      )}

      {mode === "fill" && (
        <div className="fill-mode">
          <h2>Fill Out the Survey</h2>
          {!submitted ? (
            <form onSubmit={handleSubmit}>
              {questions.map((q) => (
                <div key={q.id} className="question">
                  <label>{q.text}</label>
                  {q.type === "text" && (
                    <input
                      type="text"
                      value={responses[q.id] || ""}
                      onChange={(e) => handleChange(q.id, e.target.value)}
                    />
                  )}
                  {q.type === "radio" &&
                    q.options.map((opt, i) => (
                      <label key={i}>
                        <input
                          type="radio"
                          name={`radio-${q.id}`}
                          value={opt}
                          checked={responses[q.id] === opt}
                          onChange={() => handleChange(q.id, opt)}
                        />
                        {opt}
                      </label>
                    ))}
                  {q.type === "checkbox" &&
                    q.options.map((opt, i) => (
                      <label key={i}>
                        <input
                          type="checkbox"
                          value={opt}
                          checked={(responses[q.id] || []).includes(opt)}
                          onChange={() => handleCheckboxChange(q.id, opt)}
                        />
                        {opt}
                      </label>
                    ))}
                </div>
              ))}
              <button type="submit">Submit</button>
            </form>
          ) : (
            <div className="summary">
              <h3>Summary of Responses</h3>
              <ul>
                {questions.map((q) => (
                  <li key={q.id}>
                    <strong>{q.text}</strong>:{" "}
                    {Array.isArray(responses[q.id])
                      ? responses[q.id].join(", ")
                      : responses[q.id]}
                  </li>
                ))}
              </ul>
              <button onClick={() => setSubmitted(false)}>Edit Answers</button>
            </div>
          )}
        </div>
      )}
    </div>
  );
}

export default App;

```
 index.css

```
body {
  font-family: Arial, sans-serif;
  background: #f4f4f4;
  margin: 0;
  padding: 0;
}

.container {
  max-width: 800px;
  margin: auto;
  padding: 20px;
  background: white;
  margin-top: 40px;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.mode-toggle button {
  margin-right: 10px;
  padding: 10px 20px;
  font-weight: bold;
  cursor: pointer;
}

.build-mode input,
.fill-mode input,
select {
  display: block;
  margin: 10px 0;
  padding: 8px;
  width: 100%;
  box-sizing: border-box;
}

.question-list li {
  margin-top: 10px;
}

.question {
  margin-bottom: 20px;
}

.summary ul {
  list-style-type: none;
  padding: 0;
}
