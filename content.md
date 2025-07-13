<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Jonah Ortiz's Assistant</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f7f7f9;
      padding: 2rem;
    }

    #chatbox {
      max-width: 600px;
      margin: auto;
      background: #ffffff;
      border-radius: 10px;
      padding: 1.5rem;
      box-shadow: 0 0 10px rgba(0,0,0,0.05);
    }

    h2 {
      text-align: center;
      margin-bottom: 1rem;
      color: #333;
    }

    #messages {
      max-height: 400px;
      overflow-y: auto;
      padding-bottom: 1rem;
    }

    .message {
      margin: 0.75rem 0;
      line-height: 1.4;
    }

    .user {
      text-align: right;
      color: #0d6efd;
    }

    .bot {
      text-align: left;
      color: #198754;
    }

    #input-area {
      display: flex;
      gap: 0.5rem;
      margin-top: 1rem;
    }

    input[type="text"] {
      flex: 1;
      padding: 0.75rem;
      font-size: 1rem;
      border: 1px solid #ccc;
      border-radius: 8px;
    }

    button {
      padding: 0.75rem 1rem;
      border: none;
      background: #0d6efd;
      color: white;
      border-radius: 8px;
      font-weight: bold;
      cursor: pointer;
    }

    button:hover {
      background: #0b5ed7;
    }

    #interview-button {
      margin-top: 1rem;
      width: 100%;
      background: #198754;
    }

    #interview-button:hover {
      background: #157347;
    }
  </style>
</head>
<body>
  <div id="chatbox">
    <h2>Ask Jonah’s Assistant</h2>
    <div id="messages"></div>

    <div id="input-area">
      <input type="text" id="userInput" placeholder="Ask about Jonah's skills, experience, etc." />
      <button onclick="sendMessage()">Send</button>
    </div>

    <button id="interview-button" onclick="requestInterview()">📅 Request Interview</button>
  </div>

  <script>
    const webhookUrl = "https://your-n8n-instance.com/webhook/resume-chatbot"; // Replace with your n8n webhook
    const interviewWebhook = "https://your-n8n-instance.com/webhook/request-interview"; // Optional second webhook

    async function sendMessage() {
      const input = document.getElementById("userInput");
      const msg = input.value.trim();
      if (!msg) return;

      const messages = document.getElementById("messages");
      messages.innerHTML += `<div class="message user"><strong>You:</strong> ${msg}</div>`;
      input.value = "";

      try {
        const response = await fetch(webhookUrl, {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({ message: msg })
        });

        const data = await response.json();
        messages.innerHTML += `<div class="message bot"><strong>Assistant:</strong> ${data.response}</div>`;
        messages.scrollTop = messages.scrollHeight;
      } catch (err) {
        messages.innerHTML += `<div class="message bot">❌ Error: Could not contact assistant.</div>`;
      }
    }

    async function requestInterview() {
      const messages = document.getElementById("messages");
      messages.innerHTML += `<div class="message user"><strong>You:</strong> I'd like to request an interview.</div>`;

      try {
        const response = await fetch(interviewWebhook, {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({ action: "request_interview" })
        });

        const data = await response.json();
        messages.innerHTML += `<div class="message bot"><strong>Assistant:</strong> ${data.response || "Interview request has been submitted. Jonah will follow up shortly."}</div>`;
        messages.scrollTop = messages.scrollHeight;
      } catch (err) {
        messages.innerHTML += `<div class="message bot">❌ Error: Could not send interview request.</div>`;
      }
    }
  </script>
</body>
</html>
