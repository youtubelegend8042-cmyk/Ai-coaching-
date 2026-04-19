<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Abhishek AI Smart Coaching v2.0</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        :root { --primary: #1a73e8; --success: #28a745; --danger: #e74c3c; --warning: #ff9800; --dark: #2c3e50; }
        body { font-family: 'Poppins', sans-serif; background: linear-gradient(45deg, #6a11cb 0%, #2575fc 100%); margin: 0; padding: 10px; display: flex; flex-direction: column; align-items: center; min-height: 100vh; color: #333; }
        
        .app-card { width: 100%; max-width: 500px; background: rgba(255, 255, 255, 0.95); backdrop-filter: blur(10px); padding: 20px; border-radius: 30px; box-shadow: 0 20px 40px rgba(0,0,0,0.2); margin-bottom: 20px; border: 1px solid rgba(255,255,255,0.3); }
        h2 { text-align: center; color: var(--primary); margin-top: 0; font-size: 24px; font-weight: 800; text-transform: uppercase; }
        
        #chat-box { height: 280px; overflow-y: auto; background: #ffffff; border: 1px solid #eee; margin-top: 10px; padding: 15px; border-radius: 20px; font-size: 15px; line-height: 1.6; box-shadow: inset 0 2px 5px rgba(0,0,0,0.05); }
        
        /* Subject Tags */
        .tags-container { display: flex; gap: 8px; overflow-x: auto; margin-top: 10px; padding-bottom: 5px; }
        .tag { background: #e8f0fe; color: var(--primary); padding: 6px 15px; border-radius: 20px; font-size: 12px; cursor: pointer; white-space: nowrap; border: 1px solid var(--primary); font-weight: bold; }
        .tag:hover { background: var(--primary); color: white; }

        .util-btns { display: flex; gap: 8px; margin-top: 12px; }
        .util-btn { flex: 1; padding: 10px; border: none; border-radius: 12px; cursor: pointer; color: white; font-weight: bold; font-size: 13px; display: none; align-items: center; justify-content: center; gap: 5px; transition: 0.3s; }
        #speak-btn { background: var(--warning); box-shadow: 0 4px 10px rgba(255, 152, 0, 0.3); }
        #copy-btn { background: var(--primary); box-shadow: 0 4px 10px rgba(26, 115, 232, 0.3); }

        .search-container { display: flex; align-items: center; background: #fff; border: 2px solid var(--primary); border-radius: 50px; padding: 5px 15px; margin-top: 15px; box-shadow: 0 5px 15px rgba(0,0,0,0.05); }
        #user-question { flex: 1; border: none; padding: 12px; font-size: 16px; outline: none; background: transparent; }
        
        /* Calculator Upgrade */
        .calc-container { background: var(--dark); padding: 20px; border-radius: 30px; width: 100%; max-width: 500px; box-shadow: 0 15px 30px rgba(0,0,0,0.3); }
        .calc-display { background: #1c2833; color: #00ffcc; text-align: right; padding: 20px; font-size: 30px; border-radius: 15px; margin-bottom: 15px; font-family: 'Courier New', monospace; box-shadow: inset 0 2px 10px rgba(0,0,0,0.5); min-height: 40px; }
        .calc-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 12px; }
        .calc-btn { padding: 18px; font-size: 20px; border: none; border-radius: 15px; cursor: pointer; background: #3e5871; color: white; font-weight: bold; transition: 0.2s; }
        .calc-btn:active { transform: translateY(3px); }
        .calc-btn.op { background: #e67e22; }
        .calc-btn.eq { background: var(--success); grid-column: span 2; }
        .calc-btn.clr { background: var(--danger); }
        .calc-btn.del { background: #7f8c8d; }

        .loader { display: none; text-align: center; color: var(--primary); font-weight: bold; margin: 10px 0; }
        .recording-active { color: var(--danger) !important; animation: pulse 1s infinite; }
        @keyframes pulse { 0% { transform: scale(1); } 50% { transform: scale(1.2); } 100% { transform: scale(1); } }
    </style>
</head>
<body onload="checkMemory()">

<div class="app-card">
    <h2><i class="fas fa-robot"></i> Abhishek AI Guru</h2>
    <div id="user-status" style="text-align: center; font-size: 13px; color: var(--success); font-weight: bold; background: #e8f5e9; padding: 5px; border-radius: 10px; margin-bottom: 10px;"></div>
    
    <select id="class-select" style="width: 100%; padding: 12px; border-radius: 15px; border: 1px solid #ddd; font-weight: bold; color: var(--dark);">
        <option value="1-8">Class 1st - 8th</option>
        <option value="9-10">Class 9th & 10th</option>
        <option value="11-12">Class 11th & 12th</option>
    </select>

    <div id="chat-box">Taiyari ho rahi hai...</div>
    
    <div class="tags-container">
        <div class="tag" onclick="quickAsk('Mujhe Vigyan ka ek mazedaar fact batao')">🧪 Science Fact</div>
        <div class="tag" onclick="quickAsk('Maths yaad karne ki koi trick batao')">🔢 Math Trick</div>
        <div class="tag" onclick="quickAsk('Ek achha vichar (Quote) sunao')">💡 Motivation</div>
        <div class="tag" onclick="quickAsk('English bolna kaise seekhein?')">🗣️ English</div>
    </div>

    <div id="loading" class="loader"><i class="fas fa-brain fa-spin"></i> Guru Ji soch rahe hain...</div>

    <div class="util-btns">
        <button id="speak-btn" class="util-btn" onclick="speakAnswer()"><i class="fas fa-play-circle"></i> Suniye</button>
        <button id="copy-btn" class="util-btn" onclick="copyText()"><i class="fas fa-copy"></i> Copy</button>
        <button id="forget-btn" class="util-btn" style="background:#555; display:flex;" onclick="forgetMe()"><i class="fas fa-user-slash"></i> Reset</button>
    </div>

    <div class="search-container">
        <input type="text" id="user-question" placeholder="Kuch naya seekhein..." onkeypress="if(event.key==='Enter')askAI()">
        <button class="icon-btn" onclick="startRecording()"><i class="fas fa-microphone" id="mic-icon"></i></button>
        <button class="icon-btn send-btn" onclick="askAI()" style="color:var(--success)"><i class="fas fa-paper-plane"></i></button>
    </div>
</div>

<div class="calc-container">
    <div class="calc-display" id="display">0</div>
    <div class="calc-grid">
        <button class="calc-btn clr" onclick="clearCalc()">AC</button>
        <button class="calc-btn del" onclick="delLast()"><i class="fas fa-backspace"></i></button>
        <button class="calc-btn op" onclick="press('/')">÷</button>
        <button class="calc-btn op" onclick="press('*')">×</button>
        <button class="calc-btn" onclick="press('7')">7</button>
        <button class="calc-btn" onclick="press('8')">8</button>
        <button class="calc-btn" onclick="press('9')">9</button>
        <button class="calc-btn op" onclick="press('-')">-</button>
        <button class="calc-btn" onclick="press('4')">4</button>
        <button class="calc-btn" onclick="press('5')">5</button>
        <button class="calc-btn" onclick="press('6')">6</button>
        <button class="calc-btn op" onclick="press('+')">+</button>
        <button class="calc-btn" onclick="press('1')">1</button>
        <button class="calc-btn" onclick="press('2')">2</button>
        <button class="calc-btn" onclick="press('3')">3</button>
        <button class="calc-btn op" onclick="press('.')">.</button>
        <button class="calc-btn" onclick="press('0')">0</button>
        <button class="calc-btn" onclick="press('**')">xʸ</button>
        <button class="calc-btn eq" onclick="calculate()">=</button>
    </div>
</div>

<script>
    let lastResponse = "";
    let userName = "";

    function checkMemory() {
        userName = localStorage.getItem("studentName");
        if (!userName) {
            let n = prompt("Namaste! Aapka naam kya hai?");
            if (n) { localStorage.setItem("studentName", n); userName = n; }
            else { userName = "Student"; }
        }
        document.getElementById('user-status').innerText = "👤 Swagat hai, " + userName + "!";
        document.getElementById('chat-box').innerHTML = `<b>Namaste ${userName}!</b> Main aapka digital Guru hoon. Aaj aap mujhse kya seekhna chahte hain?`;
    }

    function forgetMe() { if(confirm("Kya aap apni pehchaan mitana chahte hain?")) { localStorage.removeItem("studentName"); location.reload(); } }

    function quickAsk(txt) {
        document.getElementById('user-question').value = txt;
        askAI();
    }

    async function askAI() {
        const groqKey = "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";
        const input = document.getElementById('user-question');
        const promptText = input.value;
        const cls = document.getElementById('class-select').value;
        const chatBox = document.getElementById('chat-box');
        const loader = document.getElementById('loading');
        
        if(!promptText) return;
        loader.style.display = "block";
        chatBox.style.opacity = "0.5";
        input.value = "";

        try {
            const res = await fetch("https://api.groq.com/openai/v1/chat/completions", {
                method: "POST",
                headers: { "Authorization": `Bearer ${groqKey}`, "Content-Type": "application/json" },
                body: JSON.stringify({
                    model: "llama-3.3-70b-versatile",
                    messages: [
                        {role: "system", content: `You are a helpful, wise Indian teacher. Name of student is ${userName} in class ${cls}. Answer in Hindi mixed with simple English where needed. Be encouraging.`},
                        {role: "user", content: promptText}
                    ]
                })
            });
            const data = await res.json();
            lastResponse = data.choices[0].message.content;
            chatBox.innerHTML = `<b>Aapka Sawal:</b> ${promptText}<br><hr><b>Guru Ji:</b><br>${lastResponse.replace(/\n/g, "<br>")}`;
            document.getElementById('speak-btn').style.display = 'flex';
            document.getElementById('copy-btn').style.display = 'flex';
            chatBox.scrollTop = chatBox.scrollHeight;
        } catch (e) { chatBox.innerText = "Kshama karein, internet check karein!"; }
        finally { loader.style.display = "none"; chatBox.style.opacity = "1"; }
    }

    // --- Calculator & Others ---
    function press(v) { let d = document.getElementById('display'); if(d.innerText === "0") d.innerText = v; else d.innerText += v; }
    function clearCalc() { document.getElementById('display').innerText = "0"; }
    function delLast() { let d = document.getElementById('display'); d.innerText = d.innerText.slice(0, -1) || "0"; }
    function calculate() { try { document.getElementById('display').innerText = eval(document.getElementById('display').innerText); } catch { document.getElementById('display').innerText = "Error"; } }
    function speakAnswer() { const msg = new SpeechSynthesisUtterance(lastResponse); msg.lang = 'hi-IN'; window.speechSynthesis.speak(msg); }
    function copyText() { navigator.clipboard.writeText(lastResponse); alert("Jawab copy ho gaya!"); }
    function startRecording() {
        const recognition = new (window.SpeechRecognition || window.webkitSpeechRecognition)();
        recognition.lang = 'hi-IN';
        recognition.onstart = () => document.getElementById('mic-icon').classList.add('recording-active');
        recognition.onresult = (e) => { document.getElementById('user-question').value = e.results[0][0].transcript; document.getElementById('mic-icon').classList.remove('recording-active'); askAI(); };
        recognition.start();
    }
</script>

</body>
</html>
