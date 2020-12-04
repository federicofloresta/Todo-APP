# Todo-APP
"use strict"

//Fetch existing todos from localStorage
const getSavedTodos = () => {
    const todosJSON = localStorage.getItem("todos")

    try {
        return todosJSON ? JSON.parse(todosJSON) : []
    } catch (e) {
        return []
    }
}

//Save todos to local storage
const saveTodos = (todos) => {
    localStorage.setItem("todos", JSON.stringify(todos))
}

// Remove todo by id --- Similar to do this where we had an id
const removeTodo = (id) => {
    const todoIndex = todos.findIndex((todo) => todo.id === id)
    if (todoIndex > -1) {
        todos.splice(todoIndex, 1)
    }
}

// Toggle the completed the completed value for a given todo
const toggleTodo = (id) => {
    const todo = todos.find((todo) => todo.id === id)
    if (todo)
        todo.completed = !todo.completed
}

//Render application todos based on filters
const renderTodos = (todos, filters) => {
    const todoEl = document.querySelector("#todos")
    const filteredTodos = todos.filter((todo) => {
        const searchTextMatch = todo.text.toLowerCase().includes(filters.searchText.toLowerCase())
        const hideCompletedMatch = !filters.hideCompleted || !todo.completed

        return searchTextMatch && hideCompletedMatch
    })

    const incompleteTodos = filteredTodos.filter((todo) => !todo.completed);

    todoEl.innerHTML = " ";
    todoEl.appendChild(generateSummaryDOM(incompleteTodos));

    if (filteredTodos.length > 0) {
        filteredTodos.forEach((todo) => {
            todoEl.appendChild(generateTodoDOM(todo))
        })
    } else {
        const emptyMessage = document.createElement("p")
        emptyMessage.classList.add("empty-message")
        emptyMessage.textContent = "No to-dos to show"
        todoEl.appendChild(emptyMessage)
    }
    //Get the DOM element for an individual todo
    const generateTodoDOM = (todo) => {
        const todoEl = document.createElement("label");
        const containerEl = document.createElement("div");
        const checkbox = document.createElement("input");
        const todoText = document.createElement("span");
        const removeButton = document.createElement("button");

        //Setup todo checkbox
        checkbox.setAttribute("type", "checkbox");
        checkbox.checked = todo.completed;
        containerEl.appendChild(checkbox);
        checkbox.addEventListener("change", () => {
            toggleTodo(todo.id)
            saveTodos(todos)
            renderTodos(todos, filters)
        })

        //Setup the todo text
        todoText.textContent = todo.text;
        containerEl.appendChild(todoText);

        //Setup container
        todoEl.classList.add("list-item");
        containerEl.classList.add("list-item__container");
        todoEl.appendChild(containerEl);

        //Create a button to delete
        removeButton.textContent = "Remove";
        removeButton.classList.add("button", "button--text");
        todoEl.appendChild(removeButton);
        removeButton.addEventListener("click", () => {
            removeTodo(todo.id);
            saveTodos(todos);
            renderTodos(todos, filters);
        })

        return todoEl;
    }
    /*Depending on the number of todos that you have it will display as a msg the number of to-dos. 
    If you have 0 To-do it will display "You have 0 todos" etc */
    function generateSummaryDOM(incompleteTodos) {
        const summary = document.createElement("h2");
        const plural = incompleteTodos.length === 1 ? " " : "s";
        summary.classList.add("list-title");
        summary.textContent = `You have ${incompleteTodos.length} to-do${plural} left`;
        return summary;
    };
}

Todo-app.js
"use strict"

let todos = [];

const filters = {
    searchText: " ",
    hideCompleted: false
}

renderTodos(todos, filters);

document.querySelector("#search-text").addEventListener("input", (e) => {
    filters.searchText = e.target.value;
    renderTodos(todos, filters);
})

// create a form with a single input for todo text
// setup an submit handler and cancel the default action
// add a new item to the todos array with that text data
// rerender the app
// clear the input field value

document.querySelector("#new-todo").addEventListener("submit", (e) => {
    const text = e.target.elements.text.value.trim();
    if (text.length > 0) {
        e.preventDefault()
        todos.push({
            id: uuidv4(),
            text,
            completed: false
        })
        saveTodos(todos);

        renderTodos(todos, filters);
        e.target.elements.text.value = " ";
    }

})

//checkbox to see what is true or false


//Challenge 
// Create a new checkbox and setup a eventlistener "HideCompleted"
// Create a New HideCompleted filter (default false)
// Update HideCompleted on rerendering the checkbox change 
// Setup renderTodos to remove completed items

document.querySelector("#hide-completed").addEventListener("change", (e) => {
    filters.hideCompleted = e.target.checked
    renderTodos(todos, filters);
})

HTML file
<!DOCTYPE html>

 <html>

<head>
    <title>Todo App</title>
    <link rel="stylesheet" href="/styles/styles.css">
</head>

<body>
    <div class="header">
        <div class="container">
            <h1 class="header__title"> To-do App</h1>
            <div class="header__subtitle">What do you want to do?</div>
        </div>
    </div>

    <div class="actions">
        <div class="actions__container">

            <input id="search-text" class="input" placeholder="Filter todos">
            <label class="checkbox">
                <input type="checkbox" id="hide-completed"> Hide Completed
            </label>
        </div>
    </div>

    <div class="container">
        <div id="todos"></div>
        <form id="new-todo">
            <input type="text" class="input" placeholder="Something to do" name="text">
            <button class="button">Add Todo</button>
        </form>
    </div>


    <script src="/scripts/uuidv4.js"></script>
    <script src="/scripts/todo-functions.js"></script>
    <script src="/scripts/todo-app.js"></script>
</body>

</html>
