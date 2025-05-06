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

       import React, { useState, useEffect } from 'react';
    import './survey.css';
    
    function Survey() {
      const [mode, setMode] = useState('build');
      const [questions, setQuestions] = useState([]);
      const [newQuestionText, setNewQuestionText] = useState('');
      const [newQuestionType, setNewQuestionType] = useState('text');
      const [newOptions, setNewOptions] = useState(['']);
      const [editingQuestionId, setEditingQuestionId] = useState(null);
      const [responses, setResponses] = useState({});
    
      useEffect(() => {
        const storedQuestions = localStorage.getItem('surveyQuestions');
        if (storedQuestions) {
          setQuestions(JSON.parse(storedQuestions));
        }
      }, []);
    
      useEffect(() => {
        localStorage.setItem('surveyQuestions', JSON.stringify(questions));
      }, [questions]);
    
      const handleModeChange = (newMode) => {
        setMode(newMode);
        setResponses({}); // Clear responses when switching to fill mode
        setEditingQuestionId(null); // Clear editing state
      };
    
      const handleAddQuestion = () => {
        if (!newQuestionText.trim()) {
          alert('Question text cannot be empty.');
          return;
        }
    
        const newQuestion = {
          id: Date.now(),
          text: newQuestionText,
          type: newQuestionType,
          options: newQuestionType === 'radio' || newQuestionType === 'checkbox' ? newOptions.filter(opt => opt.trim() !== '') : [],
        };
    
        setQuestions([...questions, newQuestion]);
        setNewQuestionText('');
        setNewQuestionType('text');
        setNewOptions(['']);
      };
    
      const handleEditQuestion = (id) => {
        const questionToEdit = questions.find((q) => q.id === id);
        if (questionToEdit) {
          setEditingQuestionId(id);
          setNewQuestionText(questionToEdit.text);
          setNewQuestionType(questionToEdit.type);
          setNewOptions(questionToEdit.options ? [...questionToEdit.options, ''] : ['']);
          setMode('build'); // Automatically switch to build mode for editing
        }
      };
    
      const handleUpdateQuestion = () => {
        if (!newQuestionText.trim()) {
          alert('Question text cannot be empty.');
          return;
        }
    
        const updatedQuestions = questions.map((q) =>
          q.id === editingQuestionId
            ? {
                id: editingQuestionId,
                text: newQuestionText,
                type: newQuestionType,
                options: newQuestionType === 'radio' || newQuestionType === 'checkbox' ? newOptions.filter(opt => opt.trim() !== '') : [],
              }
            : q
        );
    
        setQuestions(updatedQuestions);
        setNewQuestionText('');
        setNewQuestionType('text');
        setNewOptions(['']);
        setEditingQuestionId(null);
        setMode('build');
      };
    
      const handleRemoveQuestion = (id) => {
        setQuestions(questions.filter((q) => q.id !== id));
      };
    
      const handleOptionChange = (index, value) => {
        const updatedOptions = [...newOptions];
        updatedOptions[index] = value;
        setNewOptions(updatedOptions);
      };
    
      const handleAddOption = () => {
        setNewOptions([...newOptions, '']);
      };
    
      const handleRemoveOption = (index) => {
        const updatedOptions = newOptions.filter((_, i) => i !== index);
        setNewOptions(updatedOptions);
      };
    
      const handleResponseChange = (questionId, value) => {
        setResponses({ ...responses, [questionId]: value });
      };
    
      const handleSubmit = (e) => {
        e.preventDefault();
        alert(JSON.stringify(responses, null, 2));
      };
    
      const renderBuildMode = () => (
        <div className="build-mode">
          <h2>Build Your Survey</h2>
          <ul className="questions-list">
            {questions.map((question) => (
              <li key={question.id} className="question-item">
                <strong>{question.text}</strong> ({question.type})
                {question.options.length > 0 && (
                  <div className="question-options">Options: {question.options.join(', ')}</div>
                )}
                <div className="question-actions">
                  <button onClick={() => handleEditQuestion(question.id)}>Edit</button>
                  <button onClick={() => handleRemoveQuestion(question.id)}>Remove</button>
                </div>
              </li>
            ))}
          </ul>

      <h3>{editingQuestionId ? 'Edit Question' : 'Add New Question'}</h3>
      <div className="add-question-form">
        <label htmlFor="question-text">Question Text:</label>
        <input
          type="text"
          id="question-text"
          value={newQuestionText}
          onChange={(e) => setNewQuestionText(e.target.value)}
        />

        <label htmlFor="question-type">Question Type:</label>
        <select id="question-type" value={newQuestionType} onChange={(e) => setNewQuestionType(e.target.value)}>
          <option value="text">Text</option>
          <option value="radio">Radio</option>
          <option value="checkbox">Checkbox</option>
        </select>

        {(newQuestionType === 'radio' || newQuestionType === 'checkbox') && (
          <div className="options-container">
            <label>Options:</label>
            {newOptions.map((option, index) => (
              <div key={index} className="option-input">
                <input
                  type="text"
                  value={option}
                  onChange={(e) => handleOptionChange(index, e.target.value)}
                  placeholder={`Option ${index + 1}`}
                />
                {newOptions.length > 1 && (
                  <button type="button" onClick={() => handleRemoveOption(index)}>
                    Remove
                  </button>
                )}
              </div>
            ))}
            <button type="button" onClick={handleAddOption}>
              Add Option
            </button>
          </div>
        )}

        <button onClick={editingQuestionId ? handleUpdateQuestion : handleAddQuestion}>
          {editingQuestionId ? 'Update Question' : 'Add Question'}
        </button>
      </div>
    </div>
  );
  
    const renderFillMode = () => (
      <div className="fill-mode">
        <h2>Fill the Survey</h2>
        {questions.length === 0 ? (
          <p>No questions to display. Switch to Build Mode to create a survey.</p>
        ) : (
          <form onSubmit={handleSubmit}>
            {questions.map((question) => (
              <div key={question.id} className="fill-question">
                <label>{question.text}</label>
                {question.type === 'text' && (
                  <input type="text" name={question.id} onChange={(e) => handleResponseChange(question.id, e.target.value)} />
                )}
                {question.type === 'radio' &&
                  question.options.map((option, index) => (
                    <div key={`${question.id}-${index}`}>
                      <input
                        type="radio"
                        name={question.id}
                        value={option}
                        onChange={(e) => handleResponseChange(question.id, e.target.value)}
                      />
                      <label>{option}</label>
                    </div>
                  ))}
                {question.type === 'checkbox' &&
                  question.options.map((option, index) => (
                    <div key={`${question.id}-${index}`}>
                      <input
                        type="checkbox"
                        name={question.id}
                        value={option}
                        onChange={(e) => {
                          const currentResponses = responses[question.id] || [];
                          const newValue = e.target.value;
                          if (e.target.checked) {
                            handleResponseChange(question.id, [...currentResponses, newValue]);
                          } else {
                            handleResponseChange(question.id, currentResponses.filter((res) => res !== newValue));
                          }
                        }}
                      />
                      <label>{option}</label>
                    </div>
                  ))}
              </div>
            ))}
            <button type="submit">Submit</button>
          </form>
        )}
      </div>
    );
  
    return (
      <div className="survey-container">
        <div className="mode-buttons">
          <button className={mode === 'build' ? 'active' : ''} onClick={() => handleModeChange('build')}>
            Build Mode
          </button>
          <button className={mode === 'fill' ? 'active' : ''} onClick={() => handleModeChange('fill')}>
            Fill Mode
          </button>
        </div>
        {mode === 'build' ? renderBuildMode() : renderFillMode()}
      </div>
    );
  }
  
  export default Survey;

'''
## APP.JSX:
'''

       import React from 'react';
    import Survey from './survey';
    import './survey.css'; // Import the CSS here as well if needed globally
    
    function App() {
      return (
        <div className="app">
          <h1>Survey Builder</h1>
          <Survey />
        </div>
      );
    }
    
    export default App;

'''
## SURVEY.CSS:
'''


     .survey-container {
      font-family: sans-serif;
      padding: 20px;
    }
    
    .mode-buttons {
      margin-bottom: 20px;
    }
    
    .mode-buttons button {
      padding: 10px 20px;
      margin-right: 10px;
      cursor: pointer;
      border: 1px solid #ccc;
      border-radius: 5px;
    }
    
    .mode-buttons button.active {
      background-color: #007bff;
      color: white;
      border-color: #007bff;
    }
    
    .build-mode h2,
    .fill-mode h2,
    .add-question-form h3 {
      margin-top: 0;
    }
    
    .questions-list {
      list-style: none;
      padding: 0;
    }
    
    .question-item {
      border: 1px solid #eee;
      padding: 10px;
      margin-bottom: 10px;
      display: flex;
      align-items: center;
      justify-content: space-between;
    }
    
    .question-options {
      font-size: 0.9em;
      color: #777;
      margin-left: 10px;
    }
    
    .question-actions button {
      margin-left: 10px;
      padding: 5px 10px;
      cursor: pointer;
    }
    
    .add-question-form label {
      display: block;
      margin-bottom: 5px;
      font-weight: bold;
    }
    
    .add-question-form input[type='text'],
    .add-question-form select {
      width: 100%;
      padding: 8px;
      margin-bottom: 15px;
      border: 1px solid #ccc;
      border-radius: 4px;
      box-sizing: border-box;
    }
    
    .options-container {
      margin-top: 15px;
      margin-bottom: 15px;
      padding-left: 20px;
      border-left: 2px solid #ddd;
    }
    
    .option-input {
      display: flex;
      align-items: center;
      margin-bottom: 8px;
    }
    
    .option-input input[type='text'] {
      flex-grow: 1;
      padding: 6px;
      border: 1px solid #ccc;
      border-radius: 4px;
      margin-right: 10px;
    }
    
    .option-input button {
      padding: 5px 10px;
      cursor: pointer;
    }
    
    .fill-question {
      margin-bottom: 20px;
      padding: 15px;
      border: 1px solid #f0f0f0;
      border-radius: 5px;
      background-color: #fafafa;
    }
    
    .fill-question label {
      display: block;
      margin-bottom: 8px;
      font-weight: bold;
    }
    
    .fill-question input[type='text'] {
      width: 100%;
      padding: 8px;
      border: 1px solid #ccc;
      border-radius: 4px;
      box-sizing: border-box;
      margin-bottom: 10px;
    }
    
    .fill-question input[type='radio'],
    .fill-question input[type='checkbox'] {
      margin-right: 5px;
    }
    
    .fill-question button[type='submit'] {
      padding: 10px 20px;
      background-color: #28a745;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      font-size: 1rem;
    }
    
    .fill-question button[type='submit']:hover {
      background-color: #218838;
    }   
'''

### OUTPUT


![image](https://github.com/user-attachments/assets/5ec443ba-391b-468f-80eb-88e300bebf18)
