<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Citizen Opinion Portal</title>
<style>
    body {
        font-family: Arial, sans-serif;
        background-color: white;
        margin: 0;
        padding: 0;
    }

    /* Header - Reduced height */
    header {
        background-color: green;
        color: white;
        text-align: center;
        padding: 60px 20px; /* ~2.5 inches on most screens */
        font-size: 1.6rem;
        font-weight: bold;
        line-height: 1.3;
    }
    header span {
        display: block;
        font-size: 1.2rem;
        font-weight: normal;
    }

    main {
        padding: 20px;
        max-width: 900px;
        margin: auto;
    }

    .form-section {
        background: #f9f9f9;
        padding: 20px;
        margin-bottom: 20px;
        border-radius: 8px;
        box-shadow: 0 2px 5px rgba(0,0,0,0.1);
    }

    select, textarea, button {
        width: 100%;
        padding: 10px;
        margin-top: 10px;
        font-size: 1rem;
        border: 1px solid #ccc;
        border-radius: 5px;
    }

    button {
        background-color: green;
        color: white;
        border: none;
        cursor: pointer;
    }

    button:hover {
        background-color: darkgreen;
    }

    .suggestions {
        margin-top: 20px;
    }

    .suggestion-card {
        border: 1px solid #ddd;
        padding: 15px;
        margin-bottom: 10px;
        border-radius: 5px;
        background: white;
    }

    .stars {
        display: inline-block;
        cursor: pointer;
    }
    .star {
        font-size: 20px;
        color: white;
        -webkit-text-stroke: 1px black;
    }
    .star.rated {
        color: yellow;
        -webkit-text-stroke: 1px black;
    }

    /* About Us Section - Left 4-5 inches */
    .about-section {
        display: flex;
        background-color: green;
        color: white;
        padding: 20px;
        border-radius: 8px;
        margin-top: 40px;
        flex-wrap: wrap;
    }
    .about-left {
        font-weight: bold;
        font-size: 1.5rem;
        flex: 0 0 200px; /* ~4-5 inches */
    }
    .about-right {
        flex: 1;
        text-align: justify;
        padding-left: 20px;
        font-size: 1rem;
    }

    @media (max-width: 600px) {
        .about-section {
            flex-direction: column;
        }
        .about-left {
            flex: none;
            margin-bottom: 10px;
        }
    }
</style>
</head>
<body>

<header>
    YOUR VOICE MATTERS  
    <span>Shape the future with your opinion</span>
</header>

<main>
    <div class="form-section">
        <h2>Give Your Opinion</h2>
        <select id="department">
            <option value="">Select Department</option>
            <option>Education</option>
            <option>Health</option>
            <option>Infrastructure</option>
            <option>Security</option>
            <option>Transport</option>
            <option>Environment</option>
            <option>Economy</option>
        </select>
        <textarea id="suggestionText" placeholder="Write your suggestion here..."></textarea>
        <button onclick="submitSuggestion()">Submit Suggestion</button>
    </div>

    <div class="form-section">
        <h2>View Suggestions</h2>
        <select id="viewDepartment" onchange="loadSuggestions()">
            <option value="">Select Department</option>
            <option>Education</option>
            <option>Health</option>
            <option>Infrastructure</option>
            <option>Security</option>
            <option>Transport</option>
            <option>Environment</option>
            <option>Economy</option>
        </select>
        <div class="suggestions" id="suggestionsList"></div>
    </div>

    <div class="about-section">
        <div class="about-left">About Us</div>
        <div class="about-right">
            Your opinion is the cornerstone of change.  
            We believe that every voice matters, and through collective ideas,  
            we can build policies that reflect the needs of our community.  
            Together, let's shape a better tomorrow.
        </div>
    </div>
</main>

<script>
    let suggestions = JSON.parse(localStorage.getItem('suggestions')) || [];

    function submitSuggestion() {
        const department = document.getElementById('department').value;
        const text = document.getElementById('suggestionText').value.trim();
        if (!department || !text) {
            alert("Please select a department and write a suggestion.");
            return;
        }
        const suggestion = {
            id: Date.now(),
            department,
            text,
            ratings: [],
            avgRating: 0
        };
        suggestions.push(suggestion);
        localStorage.setItem('suggestions', JSON.stringify(suggestions));
        document.getElementById('suggestionText').value = '';
        alert("Suggestion submitted!");
    }

    function loadSuggestions() {
        const dept = document.getElementById('viewDepartment').value;
        const listDiv = document.getElementById('suggestionsList');
        listDiv.innerHTML = '';
        if (!dept) return;

        const filtered = suggestions.filter(s => s.department === dept);
        filtered.forEach(s => {
            const div = document.createElement('div');
            div.classList.add('suggestion-card');
            div.innerHTML = `
                <p>${s.text}</p>
                <div class="stars">${renderStars(s)}</div>
                <p>Average Rating: ${s.avgRating.toFixed(1)} / 5</p>
                <button onclick="deleteSuggestion(${s.id})">Remove Suggestion</button>
                <button onclick="removeRating(${s.id})">Remove My Review</button>
            `;
            listDiv.appendChild(div);

            div.querySelectorAll('.star').forEach((star, index) => {
                star.addEventListener('click', () => rateSuggestion(s.id, index + 1));
            });
        });
    }

    function renderStars(suggestion) {
        let starsHtml = '';
        const rounded = Math.round(suggestion.avgRating);
        for (let i = 1; i <= 5; i++) {
            const rated = i <= rounded ? 'rated' : '';
            starsHtml += `<span class="star ${rated}">&#9733;</span>`;
        }
        return starsHtml;
    }

    function rateSuggestion(id, rating) {
        let userKey = `rated-${id}`;
        if (localStorage.getItem(userKey)) {
            alert("You have already rated this suggestion.");
            return;
        }
        const suggestion = suggestions.find(s => s.id === id);
        suggestion.ratings.push(rating);
        suggestion.avgRating = suggestion.ratings.reduce((a, b) => a + b, 0) / suggestion.ratings.length;
        localStorage.setItem('suggestions', JSON.stringify(suggestions));
        localStorage.setItem(userKey, rating);
        loadSuggestions();
    }

    function removeRating(id) {
        let userKey = `rated-${id}`;
        if (!localStorage.getItem(userKey)) {
            alert("You have not rated this suggestion yet.");
            return;
        }
        const rating = parseInt(localStorage.getItem(userKey), 10);
        const suggestion = suggestions.find(s => s.id === id);
        const index = suggestion.ratings.indexOf(rating);
        if (index > -1) {
            suggestion.ratings.splice(index, 1);
        }
        suggestion.avgRating = suggestion.ratings.length > 0 
            ? suggestion.ratings.reduce((a, b) => a + b, 0) / suggestion.ratings.length
            : 0;
        localStorage.setItem('suggestions', JSON.stringify(suggestions));
        localStorage.removeItem(userKey);
        loadSuggestions();
    }

    function deleteSuggestion(id) {
        if (!confirm("Are you sure you want to remove this suggestion?")) return;
        suggestions = suggestions.filter(s => s.id !== id);
        localStorage.setItem('suggestions', JSON.stringify(suggestions));
        loadSuggestions();
    }
</script>

</body>
</html>
