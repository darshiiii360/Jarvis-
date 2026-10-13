<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>JARVIS - AI Voice Assistant</title>
    <style>
        :root {
            --bg-color: #050b14;
            --panel-bg: rgba(10, 25, 47, 0.7);
            --neon-blue: #00f3ff;
            --neon-glow: rgba(0, 243, 255, 0.4);
            --text-color: #e2e8f0;
            --accent-color: #38bdf8;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-color);
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: space-between;
            padding: 15px;
            overflow-x: hidden;
        }

        header {
            width: 100%;
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px 20px;
            background: var(--panel-bg);
            border: 1px solid rgba(0, 243, 255, 0.2);
            border-radius: 12px;
            backdrop-filter: blur(10px);
        }

        h1 {
            font-size: 1.2rem;
            letter-spacing: 2px;
            color: var(--neon-blue);
            text-shadow: 0 0 10px var(--neon-glow);
        }

        .btn {
            background: transparent;
            border: 1px solid var(--neon-blue);
            color: var(--neon-blue);
            padding: 6px 12px;
            border-radius: 6px;
            cursor: pointer;
            font-size: 0.9rem;
            transition: 0.3s;
        }

        .btn:active {
            background: var(--neon-blue);
            color: var(--bg-color);
        }

        /* Main Container */
        .main-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            flex-grow: 1;
            width: 100%;
            max-width: 600px;
            margin: 20px 0;
        }

        /* Glowing Orb */
        .orb-container {
            position: relative;
            width: 150px;
            height: 150px;
            display: flex;
            align-items: center;
            justify-content: center;
            margin-bottom: 20px;
        }

        .orb {
            width: 100px;
            height: 100px;
            border-radius: 50%;
            background: radial-gradient(circle, var(--neon-blue) 0%, #0066ff 70%, #000 100%);
            box-shadow: 0 0 30px var(--neon-blue), inset 0 0 20px #fff;
            transition: 0.5s;
        }

        .orb.listening {
            animation: pulse 1.5s infinite alternate;
            background: radial-gradient(circle, #00ff66 0%, #009933 70%, #000 100%);
            box-shadow: 0 0 40px #00ff66, inset 0 0 20px #fff;
        }

        .orb.thinking {
            animation: spin 2s linear infinite;
            background: radial-gradient(circle, #ffcc00 0%, #ff6600 70%, #000 100%);
            box-shadow: 0 0 40px #ffcc00, inset 0 0 20px #fff;
        }

        .orb.speaking {
            animation: wave 0.8s infinite alternate;
            background: radial-gradient(circle, #ff00ff 0%, #9900ff 70%, #000 100%);
            box-shadow: 0 0 50px #ff00ff, inset 0 0 20px #fff;
        }

        @keyframes pulse {
            0% { transform: scale(0.9); box-shadow: 0 0 20px #00ff66; }
            100% { transform: scale(1.15); box-shadow: 0 0 50px #00ff66; }
        }

        @keyframes spin {
            0% { transform: rotate(0deg) scale(1); }
            50% { transform: rotate(180deg) scale(1.1); }
            100% { transform: rotate(360deg) scale(1); }
        }

        @keyframes wave {
            0% { transform: scale(0.95); }
            100% { transform: scale(1.25); }
        }

        /* Status & Transcript Text */
        #status-text {
            font-size: 1rem;
            color: var(--accent-color);
            margin-bottom: 10px;
            text-align: center;
            height: 25px;
        }

        #output-box {
            width: 100%;
            background: var(--panel-bg);
            border: 1px solid rgba(0, 243, 255, 0.2);
            border-radius: 12px;
            padding: 15px;
            min-height: 80px;
            max-height: 150px;
            overflow-y: auto;
            font-size: 0.95rem;
            margin-bottom: 15px;
            backdrop-filter: blur(10px);
        }

        /* Controls / Input Area */
        .controls-area {
            width: 100%;
            display: flex;
            gap: 10px;
            margin-bottom: 15px;
        }

        input[type="text"] {
            flex-grow: 1;
            background: rgba(10, 25, 47, 0.9);
            border: 1px solid var(--neon-blue);
            border-radius: 8px;
            padding: 10px 15px;
            color: var(--text-color);
            font-size: 1rem;
            outline: none;
        }

        .action-buttons {
            display: flex;
            gap: 10px;
            width: 100%;
            justify-content: center;
        }

        .action-buttons button {
            flex: 1;
            padding: 10px;
            border-radius: 8px;
            font-weight: bold;
        }

        #btn-mic { background: rgba(0, 243, 255, 0.1); border-color: var(--neon-blue); color: var(--neon-blue); }
        #btn-stop { background: rgba(255, 0, 0, 0.1); border-color: #ff4d4d; color: #ff4d4d; }

        /* Tabs / Mini Features (Notes, To-Do, Tools) */
        .tools-panel {
            width: 100%;
            background: var(--panel-bg);
            border: 1px solid rgba(0, 243, 255, 0.2);
            border-radius: 12px;
            padding: 12px;
            font-size: 0.85rem;
            max-height: 120px;
            overflow-y: auto;
        }

        .tools-panel h3 {
            font-size: 0.9rem;
            color: var(--neon-blue);
            margin-bottom: 5px;
        }

        /* Settings Modal */
        .modal {
            display: none;
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.8);
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }

        .modal-content {
            background: #0a192f;
            border: 1px solid var(--neon-blue);
            padding: 20px;
            border-radius: 12px;
            width: 90%;
            max-width: 400px;
            display: flex;
            flex-direction: column;
            gap: 12px;
        }

        .modal-content label {
            font-size: 0.85rem;
            color: var(--accent-color);
        }

        .modal-content input, .modal-content select {
            width: 100%;
            padding: 8px;
            background: #050b14;
            border: 1px solid var(--neon-blue);
            border-radius: 6px;
            color: white;
        }
    </style>
</head>
<body>

    <header>
        <h1>J.A.R.V.I.S.</h1>
        <button class="btn" onclick="openSettings()">Settings</button>
    </header>

    <div class="main-container">
        <div class="orb-container">
            <div id="orb" class="orb"></div>
        </div>
        <div id="status-text">System Idle. Say "Jarvis" or tap Mic.</div>
        <div id="output-box">J.A.R.V.I.S. online. Aapka hukm kya hai?</div>

        <div class="controls-area">
            <input type="text" id="text-input" placeholder="Kuch type karein ya mic dabayein..." onkeypress="handleKeyPress(event)">
            <button class="btn" onclick="sendTextMessage()">Bhejo</button>
        </div>

        <div class="action-buttons">
            <button class="btn" id="btn-mic" onclick="toggleListening()">Mic On/Off</button>
            <button class="btn" id="btn-stop" onclick="stopEverything()">Roko</button>
        </div>
    </div>

    <div class="tools-panel">
        <h3>Quick Utilities & Notes</h3>
        <div id="utility-display">Timer/Alarm aur Notes yahan dikhenge.</div>
    </div>

    <!-- Settings Modal -->
    <div id="settings-modal" class="modal">
        <div class="modal-content">
            <h3>Configuration</h3>
            <label>Google Gemini API Key:</label>
            <input type="password" id="api-key-input" placeholder="Apni API key yahan daalein">
            
            <label>Gemini Model:</label>
            let selectedModel = localStorage.getItem("jarvis_model") || "gemini-1.5-flash";


                <option value="gemini-1.5-flash">gemini-1.5-flash</option>
            </select>

            <button class="btn" onclick="saveSettings()" style="margin-top: 10px;">Save karein</button>
        </div>
    </div>

    <script>
        // Global Variables
        let apiKey = localStorage.getItem("jarvis_api_key") || "";
        let selectedModel = localStorage.getItem("jarvis_model") || "gemini-2.5-flash";
        let isListening = false;
        let isSpeaking = false;
        let wakeWordActiveTimer = null;
        let activeTimerInterval = null;
        let notesList = JSON.parse(localStorage.getItem("jarvis_notes")) || [];
        let conversationHistory = [];

        // Speech Recognition Setup
        const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
        let recognition = null;

        if (SpeechRecognition) {
            recognition = new SpeechRecognition();
            recognition.lang = 'hi-IN';
            recognition.continuous = false;
            recognition.interimResults = false;

            recognition.onresult = (event) => {
                const speechResult = event.results[0][0].transcript.trim();
                document.getElementById('output-box').innerText = "Suna gaya: " + speechResult;
                processInput(speechResult);
            };

            recognition.onerror = (event) => {
                console.error("Speech recognition error", event.error);
                setOrbState('idle');
                if(event.error === 'not-allowed') {
                    showError("Microphone ki permission nahi di gayi hai.");
                }
            };

            recognition.onend = () => {
                if (isListening && !isSpeaking) {
                    try { recognition.start(); } catch(e) {}
                } else {
                    setOrbState('idle');
                }
            };
        } else {
            showError("Aapka browser SpeechRecognition support nahi karta. Chrome use karein.");
        }

        // Modal Functions
        function openSettings() {
            document.getElementById('api-key-input').value = apiKey;
            document.getElementById('model-select').value = selectedModel;
            document.getElementById('settings-modal').style.display = 'flex';
        }

        function saveSettings() {
            apiKey = document.getElementById('api-key-input').value.trim();
            selectedModel = document.getElementById('model-select').value;
            localStorage.setItem("jarvis_api_key", apiKey);
            localStorage.setItem("jarvis_model", selectedModel);
            document.getElementById('settings-modal').style.display = 'none';
            speakText("Settings save ho gayi hain.");
        }

        // Orb & Status Control
        function setOrbState(state) {
            const orb = document.getElementById('orb');
            orb.className = 'orb ' + state;
            const statusText = document.getElementById('status-text');
            if(state === 'listening') statusText.innerText = "Sun raha hoon...";
            else if(state === 'thinking') statusText.innerText = "Soch raha hoon...";
            else if(state === 'speaking') statusText.innerText = "Bol raha hoon...";
            else statusText.innerText = "System Idle. Say 'Jarvis' or tap Mic.";
        }

        function toggleListening() {
            if(!recognition) {
                showError("Speech recognition available nahi hai.");
                return;
            }
            if(isListening) {
                isListening = false;
                recognition.stop();
                setOrbState('idle');
            } else {
                if(!apiKey) {
                    showError("Pehle Settings mein apni Gemini API Key daalein.");
                    openSettings();
                    return;
                }
                isListening = true;
                setOrbState('listening');
                try { recognition.start(); } catch(e) {}
            }
        }

        function stopEverything() {
            window.speechSynthesis.cancel();
            isSpeaking = false;
            if(recognition) recognition.stop();
            isListening = false;
            setOrbState('idle');
            document.getElementById('output-box').innerText = "Rok diya gaya hai.";
        }

        // Input Processing & Wake Word Logic
        function handleKeyPress(e) {
            if(e.key === 'Enter') sendTextMessage();
        }

        function sendTextMessage() {
            const input = document.getElementById('text-input');
            const text = input.value.trim();
            if(!text) return;
            input.value = "";
            document.getElementById('output-box').innerText = "Aapne kaha: " + text;
            processInput(text);
        }

        async function processInput(text) {
            if(!apiKey) {
                showError("Pehle Settings mein API key daalein.");
                openSettings();
                return;
            }

            // Local commands check (Time, Date, Notes, Timer)
            const lowerText = text.toLowerCase();
            if(lowerText.includes('kitne baje hain') || lowerText.includes('samay kya hai')) {
                const timeStr = new Date().toLocaleTimeString('hi-IN');
                respondWithVoice("Abhi samay " + timeStr + " ho raha hai.");
                return;
            }
            if(lowerText.includes('tareekh kya hai') || lowerText.includes('aaj ka din')) {
                const dateStr = new Date().toLocaleDateString('hi-IN', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' });
                respondWithVoice("Aaj " + dateStr + " hai.");
                return;
            }
            if(lowerText.startsWith('note likho') || lowerText.startsWith('save note')) {
                const noteContent = text.replace(/note likho|save note/gi, "").trim();
                notesList.push(noteContent);
                localStorage.setItem("jarvis_notes", JSON.stringify(notesList));
                updateToolsPanel();
                respondWithVoice("Note save kar liya hai.");
                return;
            }
            if(lowerText.includes('timer lagao')) {
                const numbers = text.match(/\d+/);
                if(numbers) {
                    const minutes = parseInt(numbers[0]);
                    startTimer(minutes * 60);
                    respondWithVoice(minutes + " minute ka timer shuru kar diya hai.");
                    return;
                }
            }

            // Send to Gemini API
            setOrbState('thinking');
            
            // System prompt for concise, conversational Hinglish without markdown/emojis
            const systemInstruction = "Tum JARVIS ho, ek smart, friendly aur chust AI assistant. Tumhe Hinglish ya Hindi mein bilkul chhote, bolchaal wale aur seedhe jawaab dene hain. Koi markdown, asterisks, bullet points, ya emojis use mat karna. Sirf plain text mein bolna.";

            conversationHistory.push({ role: "user", parts: [{ text: text }] });

            try {
                const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/${selectedModel}:generateContent?key=${apiKey}`, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        contents: conversationHistory,
                        systemInstruction: { parts: [{ text: systemInstruction }] }
                    })
                });

                const data = await response.json();
                if(data.candidates && data.candidates[0].content) {
                    let reply = data.candidates[0].content.parts[0].text;
                    // Clean up any markdown if model accidentally outputs it
                    reply = reply.replace(/[*#_`]/g, '');
                    conversationHistory.push({ role: "model", parts: [{ text: reply }] });
                    respondWithVoice(reply);
                } else if(data.error) {
                    showError("API Error: " + data.error.message);
                    setOrbState('idle');
                } else {
                    showError("Gemini se koi jawab nahi mila.");
                    setOrbState('idle');
                }
            } catch (err) {
                showError("Network error. Internet check karein.");
                setOrbState('idle');
            }
        }

        // Text to Speech
        function respondWithVoice(text) {
            document.getElementById('output-box').innerText = text;
            
            // Temporarily pause mic while speaking so it doesn't hear itself
            if(recognition && isListening) {
                try { recognition.stop(); } catch(e) {}
            }

            isSpeaking = true;
            setOrbState('speaking');

            const utterance = new SpeechSynthesisUtterance(text);
            utterance.lang = 'hi-IN';
            utterance.rate = 1.0;

            utterance.onend = () => {
                isSpeaking = false;
                if(isListening) {
                    setOrbState('listening');
                    try { recognition.start(); } catch(e) {}
                } else {
                    setOrbState('idle');
                }
            };

            utterance.onerror = () => {
                isSpeaking = false;
                setOrbState('idle');
            };

            window.speechSynthesis.speak(utterance);
        }

        // Timer & Utilities UI
        function startTimer(seconds) {
            let timeLeft = seconds;
            updateToolsPanel("Timer: " + Math.floor(timeLeft/60) + " min bache hain");
            if(activeTimerInterval) clearInterval(activeTimerInterval);
            
            activeTimerInterval = setInterval(() => {
                timeLeft--;
                if(timeLeft <= 0) {
                    clearInterval(activeTimerInterval);
                    respondWithVoice("Timer khatam ho gaya hai!");
                    updateToolsPanel("Timer khatam!");
                } else {
                    updateToolsPanel("Timer: " + Math.floor(timeLeft/60) + " min " + (timeLeft%60) + " sec bache hain");
                }
            }, 1000);
        }

        function updateToolsPanel(customMsg = "") {
            let html = "<h3>Notes</h3><ul>";
            notesList.forEach((n, idx) => { html += `<li>${idx+1}. ${n}</li>`; });
            html += "</ul>";
            if(customMsg) html += `<p style="color:var(--neon-blue); margin-top:5px;">${customMsg}</p>`;
            document.getElementById('utility-display').innerHTML = html;
        }

        function showError(msg) {
            document.getElementById('output-box').innerText = "⚠️ " + msg;
            speakText("Error: " + msg);
        }

        function speakText(txt) {
            const u = new SpeechSynthesisUtterance(txt);
            u.lang = 'hi-IN';
            window.speechSynthesis.speak(u);
        }

        // Initial load
        updateToolsPanel();
    </script>
</body>
</html>
<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>JARVIS - AI Voice Assistant</title>
    <style>
        :root {
            --bg-color: #050b14;
            --panel-bg: rgba(10, 25, 47, 0.7);
            --neon-blue: #00f3ff;
            --neon-glow: rgba(0, 243, 255, 0.4);
            --text-color: #e2e8f0;
            --accent-color: #38bdf8;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-color);
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: space-between;
            padding: 15px;
            overflow-x: hidden;
        }

        header {
            width: 100%;
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px 20px;
            background: var(--panel-bg);
            border: 1px solid rgba(0, 243, 255, 0.2);
            border-radius: 12px;
            backdrop-filter: blur(10px);
        }

        h1 {
            font-size: 1.2rem;
            letter-spacing: 2px;
            color: var(--neon-blue);
            text-shadow: 0 0 10px var(--neon-glow);
        }

        .btn {
            background: transparent;
            border: 1px solid var(--neon-blue);
            color: var(--neon-blue);
            padding: 6px 12px;
            border-radius: 6px;
            cursor: pointer;
            font-size: 0.9rem;
            transition: 0.3s;
        }

        .btn:active {
            background: var(--neon-blue);
            color: var(--bg-color);
        }

        .main-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            flex-grow: 1;
            width: 100%;
            max-width: 600px;
            margin: 20px 0;
        }

        .orb-container {
            position: relative;
            width: 150px;
            height: 150px;
            display: flex;
            align-items: center;
            justify-content: center;
            margin-bottom: 20px;
        }

        .orb {
            width: 100px;
            height: 100px;
            border-radius: 50%;
            background: radial-gradient(circle, var(--neon-blue) 0%, #0066ff 70%, #000 100%);
            box-shadow: 0 0 30px var(--neon-blue), inset 0 0 20px #fff;
            transition: 0.5s;
        }

        .orb.listening {
            animation: pulse 1.5s infinite alternate;
            background: radial-gradient(circle, #00ff66 0%, #009933 70%, #000 100%);
            box-shadow: 0 0 40px #00ff66, inset 0 0 20px #fff;
        }

        .orb.thinking {
            animation: spin 2s linear infinite;
            background: radial-gradient(circle, #ffcc00 0%, #ff6600 70%, #000 100%);
            box-shadow: 0 0 40px #ffcc00, inset 0 0 20px #fff;
        }

        .orb.speaking {
            animation: wave 0.8s infinite alternate;
            background: radial-gradient(circle, #ff00ff 0%, #9900ff 70%, #000 100%);
            box-shadow: 0 0 50px #ff00ff, inset 0 0 20px #fff;
        }

        @keyframes pulse {
            0% { transform: scale(0.9); box-shadow: 0 0 20px #00ff66; }
            100% { transform: scale(1.15); box-shadow: 0 0 50px #00ff66; }
        }

        @keyframes spin {
            0% { transform: rotate(0deg) scale(1); }
            50% { transform: rotate(180deg) scale(1.1); }
            100% { transform: rotate(360deg) scale(1); }
        }

        @keyframes wave {
            0% { transform: scale(0.95); }
            100% { transform: scale(1.25); }
        }

        #status-text {
            font-size: 1rem;
            color: var(--accent-color);
            margin-bottom: 10px;
            text-align: center;
            height: 25px;
        }

        #output-box {
            width: 100%;
            background: var(--panel-bg);
            border: 1px solid rgba(0, 243, 255, 0.2);
            border-radius: 12px;
            padding: 15px;
            min-height: 80px;
            max-height: 150px;
            overflow-y: auto;
            font-size: 0.95rem;
            margin-bottom: 15px;
            backdrop-filter: blur(10px);
        }

        .controls-area {
            width: 100%;
            display: flex;
            gap: 10px;
            margin-bottom: 15px;
        }

        input[type="text"] {
            flex-grow: 1;
            background: rgba(10, 25, 47, 0.9);
            border: 1px solid var(--neon-blue);
            border-radius: 8px;
            padding: 10px 15px;
            color: var(--text-color);
            font-size: 1rem;
            outline: none;
        }

        .action-buttons {
            display: flex;
            gap: 10px;
            width: 100%;
            justify-content: center;
        }

        .action-buttons button {
            flex: 1;
            padding: 10px;
            border-radius: 8px;
            font-weight: bold;
        }

        #btn-mic { background: rgba(0, 243, 255, 0.1); border-color: var(--neon-blue); color: var(--neon-blue); }
        #btn-stop { background: rgba(255, 0, 0, 0.1); border-color: #ff4d4d; color: #ff4d4d; }

        .tools-panel {
            width: 100%;
            background: var(--panel-bg);
            border: 1px solid rgba(0, 243, 255, 0.2);
            border-radius: 12px;
            padding: 12px;
            font-size: 0.85rem;
            max-height: 120px;
            overflow-y: auto;
        }

        .tools-panel h3 {
            font-size: 0.9rem;
            color: var(--neon-blue);
            margin-bottom: 5px;
        }

        .modal {
            display: none;
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.8);
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }

        .modal-content {
            background: #0a192f;
            border: 1px solid var(--neon-blue);
            padding: 20px;
            border-radius: 12px;
            width: 90%;
            max-width: 400px;
            display: flex;
            flex-direction: column;
            gap: 12px;
        }

        .modal-content label {
            font-size: 0.85rem;
            color: var(--accent-color);
        }

        .modal-content input, .modal-content select {
            width: 100%;
            padding: 8px;
            background: #050b14;
            border: 1px solid var(--neon-blue);
            border-radius: 6px;
            color: white;
        }
    </style>
</head>
<body>

    <header>
        <h1>J.A.R.V.I.S.</h1>
        <button class="btn" onclick="openSettings()">Settings</button>
    </header>

    <div class="main-container">
        <div class="orb-container">
            <div id="orb" class="orb"></div>
        </div>
        <div id="status-text">System Idle. Say "Jarvis" or tap Mic.</div>
        <div id="output-box">J.A.R.V.I.S. online. Aapka hukm kya hai?</div>

        <div class="controls-area">
            <input type="text" id="text-input" placeholder="Kuch type karein ya mic dabayein..." onkeypress="handleKeyPress(event)">
            <button class="btn" onclick="sendTextMessage()">Bhejo</button>
        </div>

        <div class="action-buttons">
            <button class="btn" id="btn-mic" onclick="toggleListening()">Mic On/Off</button>
            <button class="btn" id="btn-stop" onclick="stopEverything()">Roko</button>
        </div>
    </div>

    <div class="tools-panel">
        <h3>Quick Utilities & Notes</h3>
        <div id="utility-display">Timer/Alarm aur Notes yahan dikhenge.</div>
    </div>

    <div id="settings-modal" class="modal">
        <div class="modal-content">
            <h3>Configuration</h3>
            <label>Google Gemini API Key:</label>
            <input type="password" id="api-key-input" placeholder="Apni API key yahan daalein">
            
            <label>Gemini Model:</label>
            <select id="model-select">
                <option value="gemini-3.8-flash">gemini-3.8-flash (Recommended)</option>
                <option value="gemini-1.5-flash">gemini-1.5-flash</option>
            </select>

            <button class="btn" onclick="saveSettings()" style="margin-top: 10px;">Save karein</button>
        </div>
    </div>

    <script>
        let apiKey = localStorage.getItem("jarvis_api_key") || "";
        let selectedModel = localStorage.getItem("jarvis_model") || "gemini-3.8-flash";
        let isListening = false;
        let isSpeaking = false;
        let activeTimerInterval = null;
        let notesList = JSON.parse(localStorage.getItem("jarvis_notes")) || [];
        let conversationHistory = [];

        const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
        let recognition = null;

        if (SpeechRecognition) {
            recognition = new SpeechRecognition();
            recognition.lang = 'hi-IN';
            recognition.continuous = false;
            recognition.interimResults = false;

            recognition.onresult = (event) => {
                const speechResult = event.results[0][0].transcript.trim();
                document.getElementById('output-box').innerText = "Suna gaya: " + speechResult;
                processInput(speechResult);
            };

            recognition.onerror = (event) => {
                console.error("Speech recognition error", event.error);
                setOrbState('idle');
                if(event.error === 'not-allowed') {
                    showError("Microphone ki permission nahi di gayi hai.");
                }
            };

            recognition.onend = () => {
                if (isListening && !isSpeaking) {
                    try { recognition.start(); } catch(e) {}
                } else {
                    setOrbState('idle');
                }
            };
        } else {
            showError("Aapka browser SpeechRecognition support nahi karta. Chrome use karein.");
        }

        function openSettings() {
            document.getElementById('api-key-input').value = apiKey;
            document.getElementById('model-select').value = selectedModel;
            document.getElementById('settings-modal').style.display = 'flex';
        }

        function saveSettings() {
            apiKey = document.getElementById('api-key-input').value.trim();
            selectedModel = document.getElementById('model-select').value;
            localStorage.setItem("jarvis_api_key", apiKey);
            localStorage.setItem("jarvis_model", selectedModel);
            document.getElementById('settings-modal').style.display = 'none';
            speakText("Settings save ho gayi hain.");
        }

        function setOrbState(state) {
            const orb = document.getElementById('orb');
            orb.className = 'orb ' + state;
            const statusText = document.getElementById('status-text');
            if(state === 'listening') statusText.innerText = "Sun raha hoon...";
            else if(state === 'thinking') statusText.innerText = "Soch raha hoon...";
            else if(state === 'speaking') statusText.innerText = "Bol raha hoon...";
            else statusText.innerText = "System Idle. Say 'Jarvis' or tap Mic.";
        }

        function toggleListening() {
            if(!recognition) {
                showError("Speech recognition available nahi hai.");
                return;
            }
            if(isListening) {
                isListening = false;
                recognition.stop();
                setOrbState('idle');
            } else {
                if(!apiKey) {
                    showError("Pehle Settings mein apni Gemini API Key daalein.");
                    openSettings();
                    return;
                }
                isListening = true;
                setOrbState('listening');
                try { recognition.start(); } catch(e) {}
            }
        }

        function stopEverything() {
            window.speechSynthesis.cancel();
            isSpeaking = false;
            if(recognition) recognition.stop();
            isListening = false;
            setOrbState('idle');
            document.getElementById('output-box').innerText = "Rok diya gaya hai.";
        }

        function handleKeyPress(e) {
            if(e.key === 'Enter') sendTextMessage();
        }

        function sendTextMessage() {
            const input = document.getElementById('text-input');
            const text = input.value.trim();
            if(!text) return;
            input.value = "";
            document.getElementById('output-box').innerText = "Aapne kaha: " + text;
            processInput(text);
        }

        async function processInput(text) {
            if(!apiKey) {
                showError("Pehle Settings mein API key daalein.");
                openSettings();
                return;
            }

            const lowerText = text.toLowerCase();
            if(lowerText.includes('kitne baje hain') || lowerText.includes('samay kya hai')) {
                const timeStr = new Date().toLocaleTimeString('hi-IN');
                respondWithVoice("Abhi samay " + timeStr + " ho raha hai.");
                return;
            }
            if(lowerText.includes('tareekh kya hai') || lowerText.includes('aaj ka din')) {
                const dateStr = new Date().toLocaleDateString('hi-IN', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' });
                respondWithVoice("Aaj " + dateStr + " hai.");
                return;
            }
            if(lowerText.startsWith('note likho') || lowerText.startsWith('save note')) {
                const noteContent = text.replace(/note likho|save note/gi, "").trim();
                notesList.push(noteContent);
                localStorage.setItem("jarvis_notes", JSON.stringify(notesList));
                updateToolsPanel();
                respondWithVoice("Note save kar liya hai.");
                return;
            }
            if(lowerText.includes('timer lagao')) {
                const numbers = text.match(/\d+/);
                if(numbers) {
                    const minutes = parseInt(numbers[0]);
                    startTimer(minutes * 60);
                    respondWithVoice(minutes + " minute ka timer shuru kar diya hai.");
                    return;
                }
            }

            setOrbState('thinking');
            const systemInstruction = "Tum JARVIS ho, ek smart, friendly aur chust AI assistant. Tumhe Hinglish ya Hindi mein bilkul chhote, bolchaal wale aur seedhe jawaab dene hain. Koi markdown, asterisks, bullet points, ya emojis use mat karna. Sirf plain text mein bolna.";

            conversationHistory.push({ role: "user", parts: [{ text: text }] });

            try {
                const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/${selectedModel}:generateContent?key=${apiKey}`, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        contents: conversationHistory,
                        systemInstruction: { parts: [{ text: systemInstruction }] }
                    })
                });

                const data = await response.json();
                if(data.candidates && data.candidates[0].content) {
                    let reply = data.candidates[0].content.parts[0].text;
                    reply = reply.replace(/[*#_`]/g, '');
                    conversationHistory.push({ role: "model", parts: [{ text: reply }] });
                    respondWithVoice(reply);
                } else if(data.error) {
                    showError("API Error: " + data.error.message);
                    setOrbState('idle');
                } else {
                    showError("Gemini se koi jawab nahi mila.");
                    setOrbState('idle');
                }
            } catch (err) {
                showError("Network error. Internet check karein.");
                setOrbState('idle');
            }
        }

        function respondWithVoice(text) {
            document.getElementById('output-box').innerText = text;
            if(recognition && isListening) {
                try { recognition.stop(); } catch(e) {}
            }

            isSpeaking = true;
            setOrbState('speaking');

            const utterance = new SpeechSynthesisUtterance(text);
            utterance.lang = 'hi-IN';
            utterance.rate = 1.0;

            utterance.onend = () => {
                isSpeaking = false;
                if(isListening) {
                    setOrbState('listening');
                    try { recognition.start(); } catch(e) {}
                } else {
                    setOrbState('idle');
                }
            };

            utterance.onerror = () => {
                isSpeaking = false;
                setOrbState('idle');
            };

            window.speechSynthesis.speak(utterance);
        }

        function startTimer(seconds) {
            let timeLeft = seconds;
            updateToolsPanel("Timer: " + Math.floor(timeLeft/60) + " min bache hain");
            if(activeTimerInterval) clearInterval(activeTimerInterval);
            
            activeTimerInterval = setInterval(() => {
                timeLeft--;
                if(timeLeft <= 0) {
                    clearInterval(activeTimerInterval);
                    respondWithVoice("Timer khatam ho gaya hai!");
                    updateToolsPanel("Timer khatam!");
                } else {
                    updateToolsPanel("Timer: " + Math.floor(timeLeft/60) + " min " + (timeLeft%60) + " sec bache hain");
                }
            }, 1000);
        }

        function updateToolsPanel(customMsg = "") {
            let html = "<h3>Notes</h3><ul>";
            notesList.forEach((n, idx) => { html += `<li>${idx+1}. ${n}</li>`; });
            html += "</ul>";
            if(customMsg) html += `<p style="color:var(--neon-blue); margin-top:5px;">${customMsg}</p>`;
            document.getElementById('utility-display').innerHTML = html;
        }

        function showError(msg) {
            document.getElementById('output-box').innerText = "⚠️ " + msg;
            speakText("Error: " + msg);
        }

        function speakText(txt) {
            const u = new SpeechSynthesisUtterance(txt);
            u.lang = 'hi-IN';
            window.speechSynthesis.speak(u);
        }

        updateToolsPanel();
    </script>
</body>
</html>
<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>JARVIS - AI Voice Assistant</title>
    <style>
        :root {
            --bg-color: #050b14;
            --panel-bg: rgba(10, 25, 47, 0.7);
            --neon-blue: #00f3ff;
            --neon-glow: rgba(0, 243, 255, 0.4);
            --text-color: #e2e8f0;
            --accent-color: #38bdf8;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-color);
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: space-between;
            padding: 15px;
            overflow-x: hidden;
        }

        header {
            width: 100%;
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px 20px;
            background: var(--panel-bg);
            border: 1px solid rgba(0, 243, 255, 0.2);
            border-radius: 12px;
            backdrop-filter: blur(10px);
        }

        h1 {
            font-size: 1.2rem;
            letter-spacing: 2px;
            color: var(--neon-blue);
            text-shadow: 0 0 10px var(--neon-glow);
        }

        .btn {
            background: transparent;
            border: 1px solid var(--neon-blue);
            color: var(--neon-blue);
            padding: 6px 12px;
            border-radius: 6px;
            cursor: pointer;
            font-size: 0.9rem;
            transition: 0.3s;
        }

        .btn:active {
            background: var(--neon-blue);
            color: var(--bg-color);
        }

        .main-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            flex-grow: 1;
            width: 100%;
            max-width: 600px;
            margin: 20px 0;
        }

        .orb-container {
            position: relative;
            width: 150px;
            height: 150px;
            display: flex;
            align-items: center;
            justify-content: center;
            margin-bottom: 20px;
        }

        .orb {
            width: 100px;
            height: 100px;
            border-radius: 50%;
            background: radial-gradient(circle, var(--neon-blue) 0%, #0066ff 70%, #000 100%);
            box-shadow: 0 0 30px var(--neon-blue), inset 0 0 20px #fff;
            transition: 0.5s;
        }

        .orb.listening {
            animation: pulse 1.5s infinite alternate;
            background: radial-gradient(circle, #00ff66 0%, #009933 70%, #000 100%);
            box-shadow: 0 0 40px #00ff66, inset 0 0 20px #fff;
        }

        .orb.thinking {
            animation: spin 2s linear infinite;
            background: radial-gradient(circle, #ffcc00 0%, #ff6600 70%, #000 100%);
            box-shadow: 0 0 40px #ffcc00, inset 0 0 20px #fff;
        }

        .orb.speaking {
            animation: wave 0.8s infinite alternate;
            background: radial-gradient(circle, #ff00ff 0%, #9900ff 70%, #000 100%);
            box-shadow: 0 0 50px #ff00ff, inset 0 0 20px #fff;
        }

        @keyframes pulse {
            0% { transform: scale(0.9); box-shadow: 0 0 20px #00ff66; }
            100% { transform: scale(1.15); box-shadow: 0 0 50px #00ff66; }
        }

        @keyframes spin {
            0% { transform: rotate(0deg) scale(1); }
            50% { transform: rotate(180deg) scale(1.1); }
            100% { transform: rotate(360deg) scale(1); }
        }

        @keyframes wave {
            0% { transform: scale(0.95); }
            100% { transform: scale(1.25); }
        }

        #status-text {
            font-size: 1rem;
            color: var(--accent-color);
            margin-bottom: 10px;
            text-align: center;
            height: 25px;
        }

        #output-box {
            width: 100%;
            background: var(--panel-bg);
            border: 1px solid rgba(0, 243, 255, 0.2);
            border-radius: 12px;
            padding: 15px;
            min-height: 80px;
            max-height: 150px;
            overflow-y: auto;
            font-size: 0.95rem;
            margin-bottom: 15px;
            backdrop-filter: blur(10px);
        }

        .controls-area {
            width: 100%;
            display: flex;
            gap: 10px;
            margin-bottom: 15px;
        }

        input[type="text"] {
            flex-grow: 1;
            background: rgba(10, 25, 47, 0.9);
            border: 1px solid var(--neon-blue);
            border-radius: 8px;
            padding: 10px 15px;
            color: var(--text-color);
            font-size: 1rem;
            outline: none;
        }

        .action-buttons {
            display: flex;
            gap: 10px;
            width: 100%;
            justify-content: center;
        }

        .action-buttons button {
            flex: 1;
            padding: 10px;
            border-radius: 8px;
            font-weight: bold;
        }

        #btn-mic { background: rgba(0, 243, 255, 0.1); border-color: var(--neon-blue); color: var(--neon-blue); }
        #btn-stop { background: rgba(255, 0, 0, 0.1); border-color: #ff4d4d; color: #ff4d4d; }

        .tools-panel {
            width: 100%;
            background: var(--panel-bg);
            border: 1px solid rgba(0, 243, 255, 0.2);
            border-radius: 12px;
            padding: 12px;
            font-size: 0.85rem;
            max-height: 120px;
            overflow-y: auto;
        }

        .tools-panel h3 {
            font-size: 0.9rem;
            color: var(--neon-blue);
            margin-bottom: 5px;
        }

        .modal {
            display: none;
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.8);
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }

        .modal-content {
            background: #0a192f;
            border: 1px solid var(--neon-blue);
            padding: 20px;
            border-radius: 12px;
            width: 90%;
            max-width: 400px;
            display: flex;
            flex-direction: column;
            gap: 12px;
        }

        .modal-content label {
            font-size: 0.85rem;
            color: var(--accent-color);
        }

        .modal-content input, .modal-content select {
            width: 100%;
            padding: 8px;
            background: #050b14;
            border: 1px solid var(--neon-blue);
            border-radius: 6px;
            color: white;
        }
    </style>
</head>
<body>

    <header>
        <h1>J.A.R.V.I.S.</h1>
        <button class="btn" onclick="openSettings()">Settings</button>
    </header>

    <div class="main-container">
        <div class="orb-container">
            <div id="orb" class="orb"></div>
        </div>
        <div id="status-text">System Idle. Say "Jarvis" or tap Mic.</div>
        <div id="output-box">J.A.R.V.I.S. online. Aapka hukm kya hai?</div>

        <div class="controls-area">
            <input type="text" id="text-input" placeholder="Kuch type karein ya mic dabayein..." onkeypress="handleKeyPress(event)">
            <button class="btn" onclick="sendTextMessage()">Bhejo</button>
        </div>

        <div class="action-buttons">
            <button class="btn" id="btn-mic" onclick="toggleListening()">Mic On/Off</button>
            <button class="btn" id="btn-stop" onclick="stopEverything()">Roko</button>
        </div>
    </div>

    <div class="tools-panel">
        <h3>Quick Utilities & Notes</h3>
        <div id="utility-display">Timer/Alarm aur Notes yahan dikhenge.</div>
    </div>

    <div id="settings-modal" class="modal">
        <div class="modal-content">
            <h3>Configuration</h3>
            <label>Google Gemini API Key:</label>
            <input type="password" id="api-key-input" placeholder="Apni API key yahan daalein">
            
            <label>Gemini Model:</label>
            <select id="model-select">
                <option value="gemini-3.8-flash">gemini-3.8-flash (Recommended)</option>
                <option value="gemini-1.5-flash">gemini-1.5-flash</option>
            </select>

            <button class="btn" onclick="saveSettings()" style="margin-top: 10px;">Save karein</button>
        </div>
    </div>

    <script>
        let apiKey = localStorage.getItem("jarvis_api_key") || "";
        let selectedModel = localStorage.getItem("jarvis_model") || "gemini-3.8-flash";
        let isListening = false;
        let isSpeaking = false;
        let activeTimerInterval = null;
        let notesList = JSON.parse(localStorage.getItem("jarvis_notes")) || [];
        let conversationHistory = [];

        const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
        let recognition = null;

        if (SpeechRecognition) {
            recognition = new SpeechRecognition();
            recognition.lang = 'hi-IN';
            recognition.continuous = false;
            recognition.interimResults = false;

            recognition.onresult = (event) => {
                const speechResult = event.results[0][0].transcript.trim();
                document.getElementById('output-box').innerText = "Suna gaya: " + speechResult;
                processInput(speechResult);
            };

            recognition.onerror = (event) => {
                console.error("Speech recognition error", event.error);
                setOrbState('idle');
                if(event.error === 'not-allowed') {
                    showError("Microphone ki permission nahi di gayi hai.");
                }
            };

            recognition.onend = () => {
                if (isListening && !isSpeaking) {
                    try { recognition.start(); } catch(e) {}
                } else {
                    setOrbState('idle');
                }
            };
        } else {
            showError("Aapka browser SpeechRecognition support nahi karta. Chrome use karein.");
        }

        function openSettings() {
            document.getElementById('api-key-input').value = apiKey;
            document.getElementById('model-select').value = selectedModel;
            document.getElementById('settings-modal').style.display = 'flex';
        }

        function saveSettings() {
            apiKey = document.getElementById('api-key-input').value.trim();
            selectedModel = document.getElementById('model-select').value;
            localStorage.setItem("jarvis_api_key", apiKey);
            localStorage.setItem("jarvis_model", selectedModel);
            document.getElementById('settings-modal').style.display = 'none';
            speakText("Settings save ho gayi hain.");
        }

        function setOrbState(state) {
            const orb = document.getElementById('orb');
            orb.className = 'orb ' + state;
            const statusText = document.getElementById('status-text');
            if(state === 'listening') statusText.innerText = "Sun raha hoon...";
            else if(state === 'thinking') statusText.innerText = "Soch raha hoon...";
            else if(state === 'speaking') statusText.innerText = "Bol raha hoon...";
            else statusText.innerText = "System Idle. Say 'Jarvis' or tap Mic.";
        }

        function toggleListening() {
            if(!recognition) {
                showError("Speech recognition available nahi hai.");
                return;
            }
            if(isListening) {
                isListening = false;
                recognition.stop();
                setOrbState('idle');
            } else {
                if(!apiKey) {
                    showError("Pehle Settings mein apni Gemini API Key daalein.");
                    openSettings();
                    return;
                }
                isListening = true;
                setOrbState('listening');
                try { recognition.start(); } catch(e) {}
            }
        }

        function stopEverything() {
            window.speechSynthesis.cancel();
            isSpeaking = false;
            if(recognition) recognition.stop();
            isListening = false;
            setOrbState('idle');
            document.getElementById('output-box').innerText = "Rok diya gaya hai.";
        }

        function handleKeyPress(e) {
            if(e.key === 'Enter') sendTextMessage();
        }

        function sendTextMessage() {
            const input = document.getElementById('text-input');
            const text = input.value.trim();
            if(!text) return;
            input.value = "";
            document.getElementById('output-box').innerText = "Aapne kaha: " + text;
            processInput(text);
        }

        async function processInput(text) {
            if(!apiKey) {
                showError("Pehle Settings mein API key daalein.");
                openSettings();
                return;
            }

            const lowerText = text.toLowerCase();
            if(lowerText.includes('kitne baje hain') || lowerText.includes('samay kya hai')) {
                const timeStr = new Date().toLocaleTimeString('hi-IN');
                respondWithVoice("Abhi samay " + timeStr + " ho raha hai.");
                return;
            }
            if(lowerText.includes('tareekh kya hai') || lowerText.includes('aaj ka din')) {
                const dateStr = new Date().toLocaleDateString('hi-IN', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' });
                respondWithVoice("Aaj " + dateStr + " hai.");
                return;
            }
            if(lowerText.startsWith('note likho') || lowerText.startsWith('save note')) {
                const noteContent = text.replace(/note likho|save note/gi, "").trim();
                notesList.push(noteContent);
                localStorage.setItem("jarvis_notes", JSON.stringify(notesList));
                updateToolsPanel();
                respondWithVoice("Note save kar liya hai.");
                return;
            }
            if(lowerText.includes('timer lagao')) {
                const numbers = text.match(/\d+/);
                if(numbers) {
                    const minutes = parseInt(numbers[0]);
                    startTimer(minutes * 60);
                    respondWithVoice(minutes + " minute ka timer shuru kar diya hai.");
                    return;
                }
            }

            setOrbState('thinking');
            const systemInstruction = "Tum JARVIS ho, ek smart, friendly aur chust AI assistant. Tumhe Hinglish ya Hindi mein bilkul chhote, bolchaal wale aur seedhe jawaab dene hain. Koi markdown, asterisks, bullet points, ya emojis use mat karna. Sirf plain text mein bolna.";

            conversationHistory.push({ role: "user", parts: [{ text: text }] });

            try {
                const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/${selectedModel}:generateContent?key=${apiKey}`, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        contents: conversationHistory,
                        systemInstruction: { parts: [{ text: systemInstruction }] }
                    })
                });

                const data = await response.json();
                if(data.candidates && data.candidates[0].content) {
                    let reply = data.candidates[0].content.parts[0].text;
                    reply = reply.replace(/[*#_`]/g, '');
                    conversationHistory.push({ role: "model", parts: [{ text: reply }] });
                    respondWithVoice(reply);
                } else if(data.error) {
                    showError("API Error: " + data.error.message);
                    setOrbState('idle');
                } else {
                    showError("Gemini se koi jawab nahi mila.");
                    setOrbState('idle');
                }
            } catch (err) {
                showError("Network error. Internet check karein.");
                setOrbState('idle');
            }
        }

        function respondWithVoice(text) {
            document.getElementById('output-box').innerText = text;
            if(recognition && isListening) {
                try { recognition.stop(); } catch(e) {}
            }

            isSpeaking = true;
            setOrbState('speaking');

            const utterance = new SpeechSynthesisUtterance(text);
            utterance.lang = 'hi-IN';
            utterance.rate = 1.0;

            utterance.onend = () => {
                isSpeaking = false;
                if(isListening) {
                    setOrbState('listening');
                    try { recognition.start(); } catch(e) {}
                } else {
                    setOrbState('idle');
                }
            };

            utterance.onerror = () => {
                isSpeaking = false;
                setOrbState('idle');
            };

            window.speechSynthesis.speak(utterance);
        }

        function startTimer(seconds) {
            let timeLeft = seconds;
            updateToolsPanel("Timer: " + Math.floor(timeLeft/60) + " min bache hain");
            if(activeTimerInterval) clearInterval(activeTimerInterval);
            
            activeTimerInterval = setInterval(() => {
                timeLeft--;
                if(timeLeft <= 0) {
                    clearInterval(activeTimerInterval);
                    respondWithVoice("Timer khatam ho gaya hai!");
                    updateToolsPanel("Timer khatam!");
                } else {
                    updateToolsPanel("Timer: " + Math.floor(timeLeft/60) + " min " + (timeLeft%60) + " sec bache hain");
                }
            }, 1000);
        }

        function updateToolsPanel(customMsg = "") {
            let html = "<h3>Notes</h3><ul>";
            notesList.forEach((n, idx) => { html += `<li>${idx+1}. ${n}</li>`; });
            html += "</ul>";
            if(customMsg) html += `<p style="color:var(--neon-blue); margin-top:5px;">${customMsg}</p>`;
            document.getElementById('utility-display').innerHTML = html;
        }

        function showError(msg) {
            document.getElementById('output-box').innerText = "⚠️ " + msg;
            speakText("Error: " + msg);
        }

        function speakText(txt) {
            const u = new SpeechSynthesisUtterance(txt);
            u.lang = 'hi-IN';
            window.speechSynthesis.speak(u);
        }

        updateToolsPanel();
    </script>
</body>
</html>
