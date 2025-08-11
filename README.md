<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Citizen Policy Portal</title>
<style>
    body {
        font-family: Arial, sans-serif;
        background-color: #ffffff;
        color: #004d26;
        margin: 0;
        padding: 0;
    }
    header {
        background-color: #006633;
        color: white;
        padding: 20px;
        text-align: center;
        font-size: 1.8em;
        font-weight: bold;
    }
    nav {
        background-color: #004d26;
        padding: 10px;
        text-align: center;
    }
    nav a {
        color: white;
        margin: 0 15px;
        text-decoration: none;
        font-weight: bold;
    }
    nav a:hover {
        text-decoration: underline;
    }
    .container {
        max-width: 800px;
        margin: 20px auto;
        padding: 15px;
    }
    select, textarea, button {
        width: 100%;
        padding: 10px;
        margin-top: 10px;
        font-size: 1em;
        border: 1px solid #ccc;
        border-radius: 5px;
    }
    button {
        background-color: #006633;
        color: white;
        cursor: pointer;
        border: none;
    }
    button:hover {
        background-color: #004d26;
    }
    .suggestions {
        margin-top: 20px;
    }
    .suggestion {
        border: 1px solid #ccc;
        border-left: 5px solid #006633;
        padding: 10px;
        margin-bottom: 10px;
        background: #f9f9f9;
    }
    .stars {
        color: gold;
        cursor: pointer;
    }
</style>
</head>
<body>

<header>Shape Pakistan’s Future — Your Voice, Your Power</header>
<nav>
    <a href="index.html">Home</a>
    <a href="about.html">About. html">About Us<a>
</nav>

<div class="container">
    <h2>Submit Your Suggestion</h2>
    <select id="department">
        <option value="">Select Department</option>
        <option>Education</option>
        <option>Health</option>
        <option>Infrastructure</option>
        <option>Security</option>
        <option>Transport</option>
        <option>Energy</option>
        <option>Environment</option>
        <option>Economy</option>
    </select>
    <textarea id="opinion" placeholder="Write your policy suggestion here..." rows="4"></textarea>
    <button onclick="submitOpinion()">Submit</button>
    <h2>View Suggestions</h2>
    <select id="viewDept" onchange="viewSuggestions()">
        <option value="">Select Department</option>
        <option>Education</option>
        <option>Health</option>
        <option>Infrastructure</option>
        <option>Security</option>
        <option>Transport</option>
        <option>Energy</option>
        <option>Environment</option>
        <option>Economy</option>
    </select>    
    <div class="suggestions" id="suggestionsList"></div>
</div>

<script>
    let data = JSON.parse(localStorage.getItem("suggestions")) || {};

    function submitOpinion() {
        const dept = document.getElementById('department').value;
        const opinion = document.getElementById('opinion').value.trim();
        
        if (!dept || !opinion) {
            alert("Please select a department and write your suggestion.");
            return;
        }
        
        if (!data[dept]) data[dept] = [];
        data[dept].push({ text: opinion, rating: 0, votes: 0 });
        
        localStorage.setItem("suggestions", JSON.stringify(data));
        document.getElementById('opinion').value = "";
        alert("Your suggestion has been added!");
    }

    function viewSuggestions() {
        const dept = document.getElementById('viewDept').value;
        const listDiv = document.getElementById('suggestionsList');
        listDiv.innerHTML = "";
        
        if (!dept || !data[dept] || data[dept].length === 0) {
            listDiv.innerHTML = "<p>No suggestions yet for this department.</p>";
            return;
        }
        
        data[dept].forEach((op, index) => {
            const div = document.createElement('div');
            div.className = 'suggestion';
            div.innerHTML = `
                <p>${op.text}</p>
                <p>Rating: ${op.rating.toFixed(1)} ⭐ (${op.votes} votes)</p>
                <div>
                    ${[1,2,3,4,5].map(star => `
                        <span class="stars" onclick="rateSuggestion('${dept}', ${index}, ${star})">&#9733;</span>
                    `).join('')}
                </div>
            `;
            listDiv.appendChild(div);
        });
    }

    function rateSuggestion(dept, index, stars) {
        const suggestion = data[dept][index];
        suggestion.rating = ((suggestion.rating * suggestion.votes) + stars) / (suggestion.votes + 1);
        suggestion.votes += 1;
        localStorage.setItem("suggestions", JSON.stringify(data));
        viewSuggestions();
    }
</script>

</body>
</html>    
        
