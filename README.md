<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>HabitFlow</title>

<style>
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

body {
    font-family: Arial, sans-serif;
    background: linear-gradient(135deg, #f5f3ff, #eef2ff);
    color: #202033;
    min-height: 100vh;
}

.app {
    width: 92%;
    max-width: 650px;
    margin: auto;
    padding: 30px 0 50px;
}

header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 25px;
}

.small {
    color: #777;
    font-size: 12px;
    letter-spacing: 2px;
}

h1 {
    font-size: 34px;
    margin-top: 5px;
}

.date {
    color: #777;
    font-size: 13px;
}

.progress {
    background: linear-gradient(135deg, #6657e8, #887cff);
    color: white;
    border-radius: 24px;
    padding: 25px;
    margin-bottom: 20px;
    box-shadow: 0 15px 35px #6657e844;
}

.progress p {
    opacity: .85;
}

.progress h2 {
    font-size: 42px;
    margin-top: 8px;
}

.add {
    display: flex;
    gap: 10px;
    margin-bottom: 28px;
}

.add input {
    flex: 1;
    padding: 16px;
    border: 1px solid #ddd;
    border-radius: 15px;
    outline: none;
    font-size: 15px;
}

.add button {
    width: 55px;
    border: none;
    border-radius: 15px;
    background: #6657e8;
    color: white;
    font-size: 28px;
    cursor: pointer;
}

.title {
    display: flex;
    justify-content: space-between;
    margin-bottom: 15px;
}

.title span {
    color: #777;
    font-size: 13px;
}

.habit {
    background: white;
    padding: 18px;
    border-radius: 18px;
    margin-bottom: 12px;
    display: flex;
    align-items: center;
    gap: 14px;
    box-shadow: 0 6px 20px #0000000b;
}

.check {
    width: 30px;
    height: 30px;
    border: 2px solid #ccc;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    flex-shrink: 0;
}

.check.done {
    background: #6657e8;
    color: white;
    border-color: #6657e8;
}

.info {
    flex: 1;
}

.name {
    font-weight: bold;
}

.streak {
    color: #777;
    font-size: 12px;
    margin-top: 5px;
}

.delete {
    border: none;
    background: transparent;
    cursor: pointer;
    font-size: 17px;
}

.stats {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 10px;
    margin-top: 25px;
}

.stat {
    background: white;
    padding: 18px 8px;
    text-align: center;
    border-radius: 18px;
    box-shadow: 0 6px 20px #0000000b;
}

.stat b {
    display: block;
    font-size: 24px;
    margin: 6px;
}

.stat small {
    color: #777;
}

.empty {
    text-align: center;
    color: #888;
    padding: 30px;
}

@media(max-width:500px) {
    .app {
        width: 94%;
        padding-top: 20px;
    }

    h1 {
        font-size: 29px;
    }
}
</style>
</head>

<body>

<div class="app">

<header>
    <div>
        <div class="small">WELCOME 👋</div>
        <h1>HabitFlow</h1>
    </div>

    <div class="date" id="date"></div>
</header>


<div class="progress">
    <p>Today's Progress</p>
    <h2 id="progress">0%</h2>
</div>


<div class="add">
    <input
        id="habitInput"
        placeholder="Add a new habit..."
        onkeydown="if(event.key==='Enter') addHabit()"
    >

    <button onclick="addHabit()">+</button>
</div>


<div class="title">
    <h2>My Habits</h2>
    <span id="count">0 habits</span>
</div>


<div id="habits"></div>


<div class="stats">

    <div class="stat">
        🔥
        <b id="best">0</b>
        <small>Best Streak</small>
    </div>

    <div class="stat">
        ✅
        <b id="completed">0</b>
        <small>Completed</small>
    </div>

    <div class="stat">
        🎯
        <b id="total">0</b>
        <small>Total</small>
    </div>

</div>

</div>


<script>

let habits = JSON.parse(localStorage.getItem("habitflow")) || [];


function save() {
    localStorage.setItem(
        "habitflow",
        JSON.stringify(habits)
    );
}


function addHabit() {

    const input =
        document.getElementById("habitInput");

    const name = input.value.trim();

    if (!name) {
        return;
    }

    habits.push({
        id: Date.now(),
        name: name,
        completed: false,
        streak: 0
    });

    input.value = "";

    save();
    render();
}


function toggle(id) {

    const habit =
        habits.find(h => h.id === id);

    if (!habit) return;

    habit.completed = !habit.completed;

    if (habit.completed) {
        habit.streak++;
    } else if (habit.streak > 0) {
        habit.streak--;
    }

    save();
    render();
}


function removeHabit(id) {

    habits =
        habits.filter(h => h.id !== id);

    save();
    render();
}


function render() {

    const container =
        document.getElementById("habits");

    container.innerHTML = "";

    if (habits.length === 0) {

        container.innerHTML = `
            <div class="empty">
                🌱<br><br>
                No habits yet.<br>
                Add your first habit above!
            </div>
        `;

    }

    habits.forEach(habit => {

        container.innerHTML += `

        <div class="habit">

            <div
                class="check ${habit.completed ? "done" : ""}"
                onclick="toggle(${habit.id})"
            >
                ${habit.completed ? "✓" : ""}
            </div>

            <div class="info">

                <div class="name">
                    ${habit.name}
                </div>

                <div class="streak">
                    🔥 ${habit.streak} day streak
                </div>

            </div>

            <button
                class="delete"
                onclick="removeHabit(${habit.id})"
            >
                🗑️
            </button>

        </div>

        `;
    });

    updateStats();
}


function updateStats() {

    const total = habits.length;

    const completed =
        habits.filter(h => h.completed).length;

    const progress =
        total === 0
        ? 0
        : Math.round((completed / total) * 100);

    document.getElementById("progress")
        .textContent = progress + "%";

    document.getElementById("completed")
        .textContent = completed;

    document.getElementById("total")
        .textContent = total;

    document.getElementById("count")
        .textContent =
        total + (total === 1 ? " habit" : " habits");

    const best =
        habits.length
        ? Math.max(...habits.map(h => h.streak))
        : 0;

    document.getElementById("best")
        .textContent = best;
}


document.getElementById("date")
    .textContent =
    new Date().toLocaleDateString(
        "en-US",
        {
            weekday: "short",
            month: "short",
            day: "numeric"
        }
    );


render();

</script>

</body>
</html>
