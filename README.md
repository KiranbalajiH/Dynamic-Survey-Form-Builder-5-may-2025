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

### PROGRAM:

## Survey.jsx:
'''

    import React, { useState } from "react";
    import "./Survey.css";
    
    function Survey({ questions }) {
      const [answers, setAnswers] = useState({});
      const [submitted, setSubmitted] = useState(false);
    
      const handleChange = (id, value) => {
        setAnswers((prev) => ({ ...prev, [id]: value }));
      };
    
      const handleSubmit = (e) => {
        e.preventDefault();
        setSubmitted(true);
      };
    
      return (
        <div className="survey-container">
          {!submitted ? (
            <form onSubmit={handleSubmit}>
              {questions.map((q) => (
                <div key={q.id} className="question-block">
                  <p>{q.text}</p>
                  {q.options.map((opt, i) => (
                    <label key={i}>
                      <input
                        type="radio"
                        name={`radio-${q.id}`}
                        value={opt}
                        checked={answers[q.id] === opt}
                        onChange={() => handleChange(q.id, opt)}
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
              <h3>Your Responses</h3>
              <ul>
                {questions.map((q) => (
                  <li key={q.id}>
                    <strong>{q.text}</strong>: {answers[q.id]}
                  </li>
                ))}
              </ul>
              <button onClick={() => setSubmitted(false)}>Edit Answers</button>
            </div>
          )}
        </div>
      );
    }
    
    export default Survey;

'''
## APP.JSX:
'''

    import React, { useState } from "react";
    import Survey from "./Survey";
    
    function App() {
      const [questions, setQuestions] = useState([
        {
          id: 1,
          text: "What is your favorite color?",
          type: "radio",
          options: ["Red", "Blue", "Green", "Yellow"],
        },
        {
          id: 2,
          text: "Choose your preferred pet:",
          type: "radio",
          options: ["Dog", "Cat", "Bird", "Fish"],
        },
      ]);
    
      return (
        <div className="container">
          <h1>Radio Survey App</h1>
          <Survey questions={questions} />
        </div>
      );
    }
    
    export default App;

'''
## SURVEY.CSS:
'''


  .survey-container {
      margin-top: 20px;
      font-family: Arial, sans-serif;
    }
    
    .question-block {
      margin-bottom: 20px;
      padding: 12px;
      background: #f5f5f5;
      border-radius: 8px;
    }
    
    label {
      display: block;
      margin-top: 6px;
    }
    
    button {
      padding: 10px 20px;
      margin-top: 15px;
      background-color: #007bff;
      color: #fff;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }
    
    button:hover {
      background-color: #0056b3;
    }
    
'''

### OUTPUT

![image](https://github.com/user-attachments/assets/7aade021-4515-492a-af8e-e17241586d06)
