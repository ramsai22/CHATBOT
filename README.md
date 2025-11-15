# CHATBOT-IMPLEMENTATION
# Intelligent-Enterprise-Assistant - Hackthon
An AI-powered virtual assistant designed to help the public sector work smarter. It automates tasks like document search, policy answering, leave requests, and helpdesk support — all through natural conversation.

# Overview
The Intelligent Enterprise Assistant acts as a smart FAQ chatbot for organizational use. It uses predefined knowledge to respond to queries and can be easily extended to support more workflows. This prototype demonstrates how AI-powered chat support can: 1)Answer repeated employee/citizen queries instantly 2)Reduce helpdesk workload 3)Improve access to organizational knowledge 4)Serve as a foundation for integrating automation and data lookup

# Problem Statement
Public sector organizations handle large volumes of repetitive queries related to rules, HR policies, procedures, and internal services. Traditional systems require manual lookup, causing delays and inefficiency.

# Features
Conversational Chat Interface • Predefined knowledge responses • Web-based UI • Accessible using any browser • Easy to Customize • Responds to common policy and helpdesk queries • Users can ask questions naturally

# Project Structure
<img width="393" height="283" alt="image" src="https://github.com/user-attachments/assets/4b870d1c-00ac-4287-8ec7-03b506602eb3" />

# Implementation Code:
app.py
```py
from flask import Flask, render_template, request, jsonify
from thefuzz import process, fuzz

app = Flask(__name__)

# Knowledge Base: Same as before, but the matching will be fuzzy
responses = {
    "hi": "Hello! How can I assist you today?",
    "hello": "Hi there! How can I help?",
    "how are you": "I'm working perfectly! 😊",
    "bye": "Goodbye! Have a great day!",
    "what is an intelligent enterprise": "An Intelligent Enterprise uses AI, automation, and data to improve efficiency and decision making.",
    "what is your purpose": "My purpose is to assist employees and citizens by providing quick and accurate information.",
    "how do you help employees": "I help employees by answering questions instantly and reducing manual work.",
    "how to apply for leave": "You can apply leave using the HR Portal under Employee Self Service.",
    "what are office working hours": "Office hours are Monday to Friday, 9 AM to 5 PM.",
    "how to register a complaint": "You can register a complaint through the Citizen Grievance Portal.",
    "how to reset my password": "You can reset your password through the IT Helpdesk Support System.",
    "where to get service forms": "Service forms are available on the official e-Governance portal.",
    "what documents are needed for id card": "You need Aadhaar Card, Employee ID, and a passport-size photograph."
}

# Threshold for similarity score (e.g., 70% match is considered acceptable)
FUZZY_MATCH_THRESHOLD = 70


@app.route("/")
def index():
    return render_template("index.html")

@app.route("/get", methods=["POST"])
def chatbot_response():
    user_msg = request.form["msg"].lower()
    
    # Use fuzzy matching to find the best key in the responses dictionary
    # extractOne returns (best_match_key, score)
    best_match = process.extractOne(
        query=user_msg, 
        choices=responses.keys(), 
        scorer=fuzz.ratio
    )
    
    best_match_key, score = best_match
    
    if score >= FUZZY_MATCH_THRESHOLD:
        reply = responses[best_match_key]
    else:
        # Default response if no key meets the similarity threshold
        reply = "Sorry, I didn't understand. Can you try again or check your spelling?"
        
    return jsonify({"response": reply})

if __name__ == "__main__":
    # Use 0.0.0.0 for broader access, especially in certain deployment environments
    app.run(host='0.0.0.0', debug=True)
```
index.html
```html
<!DOCTYPE html>
<html>
<head>
    <title>Simple Chatbot</title>
    <link rel="stylesheet" href="/static/style.css">
</head>
<body>

<h2>💬 Simple AI Chatbot</h2>

<div id="chatbox"></div>

<div class="input-area">
    <input type="text" id="userInput" placeholder="Type your message...">
    <button onclick="sendMessage()">Send</button>
</div>

<script src="/static/script.js"></script>
</body>
</html>
```
style.css
```css
body {
    background: #f2f2f2;
    text-align: center;
    font-family: Arial, sans-serif;
}

h2 {
    color: #333;
}

#chatbox {
    width: 60%;
    height: 350px;
    background: white;
    border-radius: 6px;
    padding: 10px;
    margin: auto;
    margin-bottom: 10px;
    overflow-y: auto;
    border: 1px solid #ccc;
    /* Styles for the chat messages within the box */
    text-align: left; 
}

/* Styling for You and Bot messages */
#chatbox p {
    margin: 5px 0;
    padding: 5px;
    border-radius: 4px;
}
#chatbox p b {
    font-weight: bold;
    margin-right: 5px;
}


.input-area {
    display: flex;
    justify-content: center;
    gap: 10px;
}

#userInput {
    width: 50%;
    padding: 10px;
    border-radius: 4px;
    border: 1px solid #555;
}

button {
    padding: 10px 20px;
    border: none;
    background: #007bff;
    color: white;
    cursor: pointer;
    border-radius: 4px;
}

button:hover {
    background: #0056b3;
}
```
script.js
```js
function sendMessage() {
    let msg = document.getElementById("userInput").value;
    // Don't send empty messages
    if (msg.trim() === "") return;

    // Display user message in the chatbox
    document.getElementById("chatbox").innerHTML += `<p><b>You:</b> ${msg}</p>`;

    // Use Fetch API to send the message to the Flask /get endpoint
    fetch("/get", {
        method: "POST",
        // The message is sent as form data
        headers: {"Content-Type": "application/x-www-form-urlencoded"},
        body: `msg=${msg}`
    })
    .then(response => response.json()) // Parse the JSON response from Flask
    .then(data => {
        // Display bot response in the chatbox
        document.getElementById("chatbox").innerHTML += `<p><b>Bot:</b> ${data.response}</p>`;
        
        // Scroll to the bottom of the chatbox to show the latest message
        let chatbox = document.getElementById("chatbox");
        chatbox.scrollTop = chatbox.scrollHeight;
    })
    .catch(error => {
        console.error('Error fetching chatbot response:', error);
        document.getElementById("chatbox").innerHTML += `<p><b>Bot:</b> Sorry, there was an error connecting to the server.</p>`;
    });

    // Clear the input field
    document.getElementById("userInput").value = "";
}

// Optional: Add functionality to send message by pressing Enter key
document.getElementById("userInput").addEventListener("keypress", function(event) {
    // Number 13 is the "Enter" key on the keyboard
    if (event.key === 'Enter') {
        event.preventDefault(); // Prevent default Enter action (like form submission)
        sendMessage();
    }
});
```
# How to Run Everything
Step 1: Open Terminal in your project folder Navigate to the intelligent-enterprise-assistant-v2 directory.

Step 2: Install Dependencies You need to install Flask (if not already) and the fuzzy matching library thefuzz.

Bash

pip install flask thefuzz

Step 3: Start the Flask Server Run the Python application:

Bash

python app.py You will see output indicating the server is running, likely on:

Running on http://0.0.0.0:5000/ Step 4: Open in Browser Open your browser and navigate to the address: http://127.0.0.1:5000/

# OUTPUT
<img width="1919" height="1017" alt="image" src="https://github.com/user-attachments/assets/4fb443cf-b5b0-4043-9ef3-2076c730bef0" />


# RESULT:
Thus the Chatbot is executed Successfully.
