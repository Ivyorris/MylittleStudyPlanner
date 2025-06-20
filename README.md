<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
   <link href="https://fonts.googleapis.com/css2?family=Roboto&family=Fredoka&display=swap" rel="stylesheet">
  <title>My little Study Planner</title>
</head>
<style>
    body {
      font-family: 'Fredoka';
      margin: 0;
      padding: 0;
      background-color: #aadaf0; 
    }
    header, nav {
      background-color: #594192;
      color: white;
      padding: 10px 20px;
    }
    nav a {
      margin: 0 10px;
      color: 	#e4e7ec;
      text-decoration: none;
    }
    section {
      padding: 20px;
      display: none;
    }
    section.active {
      display: block;
    }
    .todo-item.done {
      text-decoration: line-through;
      color: gray;
    }
    .calendar-grid {
      display: grid;
      grid-template-columns: repeat(7, 1fr);
      gap: 7px;
      margin-top: 20px;
    }
    .calendar-day {
      border: 1px solid #ccc;
      padding: 10px;
      background-color: white;
      min-height: 80px;
      position: relative;
    }
    .calendar-day span {
      font-weight: bold;
    }
    .reminder {
      font-size: 0.8em;
      background: 	#5fbbe7;
      padding: 2px 4px;
      margin-top: 10px;
      display: circle;
    }
    .reminder-form {
      margin-top: 20px;
    }
    #home.active {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 70vh;
      text-align: center;
    }
    #home h2 {
      font-size: 2em;
    }
    #home p {
      font-size: 1.2em;
      margin: 20px 0;
    }
    #home button {
      background-color: #594192;
      color: white;
      border: none;
      padding: 10px 20px;
      border-radius: 20px;
      font-size: 1em;
      cursor: pointer;
      transition: background-color 0.3s ease;
      font-family: 'Fredoka', cursive;
    }
    #home button:hover {
      background-color: #473574;
    }
  </style>
<body>
  <header>
    <h1>My little Study Planner</h1>
  </header>
  <nav>
    <a href="#" onclick="showSection('home')">Home</a>
    <a href="#" onclick="showSection('subjects')">Subjects</a>
    <a href="#" onclick="showSection('todo')">To-Do List</a>
    <a href="#" onclick="showSection('calendar')">Calendar</a>
    <a href="#" onclick="showSection('timer')">Study Timer</a>
  </nav>

  <section id="home" class="active">
    <div id="homeContent">
      <h2>Welcome!</h2>
      <p id="quote">"Education is the passport to the future."</p>
      <button onclick="changeQuote()">Change Quote</button>
    </div>
  </section>


 <section id="subjects">
    <h2>Your Subjects and Grades</h2>
    <div class="subject-form">
      <input type="text" id="subjectName" placeholder="Subject name" />
      <input type="text" id="subjectGrade" placeholder="Grade" />
      <button onclick="addSubject()">Add Subject</button>
    </div>
    <ul id="subjectList"></ul>
  </section>



  <section id="todo">
    <h2>To-Do List</h2>
    <input type="text" id="taskInput" placeholder="New task..." />
    <button onclick="addTask()">Add</button>
    <ul id="taskList"></ul>
  </section>

  <section id="calendar">
    <h2>Calendar View</h2>
    <div class="reminder-form">
      <input type="number" id="reminderDay" min="1" max="30" placeholder="Day (1-30)" />
      <input type="text" id="reminderText" placeholder="Reminder" />
      <button onclick="addReminder()">Add Reminder</button>
    </div>
    <div class="calendar-grid" id="calendarGrid"></div>
  </section>

  <section id="timer">
    <h2>Pomodoro Timer</h2>
    <p id="timerDisplay">25:00</p>
    <button onclick="startTimer()">Start</button>
    <button onclick="resetTimer()">Reset</button>
    <button onclick="pauseTimer()">pause</button>
  </section>

  <script defer>
    // Section toggling
    function showSection(id) {
      document.querySelectorAll('section').forEach(sec => sec.classList.remove('active'));
      document.getElementById(id).classList.add('active');
    }

    // Motivational Quotes
    const quotes = [
      "Education is the passport to the future.",
      "The future belongs to those who prepare for it today.",
      "Success is no accident. It is hard work.",
      "Don't watch the clock; do what it does. Keep going."
    ];
    function changeQuote() {
      const randomIndex = Math.floor(Math.random() * quotes.length);
      document.getElementById('quote').innerText = quotes[randomIndex];
    }

    // Subjects from JSON
let subjects = [
      { name: "Mathematics", grade: "A" },
      { name: "English", grade: "B+" },
      { name: "Science", grade: "A-" },
      { name: "History", grade: "B" }
    ];

    function renderSubjects() {
      const subjectList = document.getElementById('subjectList');
      subjectList.innerHTML = '';
      subjects.forEach(sub => {
        const li = document.createElement('li');
        li.textContent = `${sub.name}: ${sub.grade}`;
        subjectList.appendChild(li);
      });
    }

    function addSubject() {
      const name = document.getElementById('subjectName').value.trim();
      const grade = document.getElementById('subjectGrade').value.trim();
      if (!name || !grade) return;

      subjects.push({ name, grade });
      renderSubjects();

      document.getElementById('subjectName').value = '';
      document.getElementById('subjectGrade').value = '';
    }

    renderSubjects();

    // To-Do List
    function addTask() {
      const taskInput = document.getElementById('taskInput');
      if (taskInput.value.trim() !== "") {
        const li = document.createElement('li');
        li.className = 'todo-item';
        li.textContent = taskInput.value;
        li.onclick = () => li.classList.toggle('done');
        const removeBtn = document.createElement('button');
        removeBtn.textContent = '❌';
        removeBtn.onclick = (e) => {
          e.stopPropagation();
          li.remove();
        };
        li.appendChild(removeBtn);
        document.getElementById('taskList').appendChild(li);
        taskInput.value = "";
      }
    }

   let timer;
    let time = 25 * 60;
    function startTimer() {
      if (timer) return;
      timer = setInterval(() => {
        if (time <= 0) {
          clearInterval(timer);
          timer = null;
          alert("Time's up!");
          return;
        }
        time--;
        updateTimerDisplay();
      }, 1000);
    }
    function pauseTimer() {
      clearInterval(timer);
      timer = null;
    }
    function resetTimer() {
      clearInterval(timer);
      timer = null;
      time = 25 * 60;
      updateTimerDisplay();
    }
    function updateTimerDisplay() {
      const minutes = Math.floor(time / 60);
      const seconds = time % 60;
      document.getElementById('timerDisplay').innerText = `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
    }
    updateTimerDisplay();
    // Calendar with Reminders
    const calendarGrid = document.getElementById('calendarGrid');
    for (let day = 1; day <= 30; day++) {
      const dayBox = document.createElement('div');
      dayBox.className = 'calendar-day';
      dayBox.dataset.day = day;
      dayBox.innerHTML = `<span>${day}</span><div class="reminders"></div>`;
      calendarGrid.appendChild(dayBox);
    }

    function addReminder() {
      const day = parseInt(document.getElementById('reminderDay').value);
      const text = document.getElementById('reminderText').value;
      if (!day || day < 1 || day > 30 || !text.trim()) return;

      const dayBox = document.querySelector(`.calendar-day[data-day='${day}'] .reminders`);
      const reminder = document.createElement('div');
      reminder.className = 'reminder';
      reminder.innerText = text;
      dayBox.appendChild(reminder);

      document.getElementById('reminderDay').value = '';
      document.getElementById('reminderText').value = '';
    }
  </script>
</body>
</html>
