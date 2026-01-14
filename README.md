<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Note AI Final</title>
    <style>
        :root { --bg: #000; --sidebar: #111; --border: #333; --text: #fff; }
        body { margin: 0; display: flex; height: 100vh; background: var(--bg); color: var(--text); font-family: sans-serif; overflow: hidden; }

        /* Sidebar */
        .sidebar { width: 260px; background: var(--sidebar); display: flex; flex-direction: column; border-right: 1px solid var(--border); position: absolute; height: 100%; left: -260px; transition: 0.3s; z-index: 100; }
        .sidebar.active { left: 0; }
        .btn-menu { position: absolute; top: 15px; left: 15px; background: #222; border: 1px solid var(--border); color: white; padding: 8px 12px; border-radius: 12px; z-index: 110; cursor: pointer; }
        .history { flex: 1; padding: 80px 15px 15px 15px; }
        .history-item { padding: 12px; border-radius: 12px; border: 1px solid #222; margin-bottom: 8px; font-size: 13px; color: #888; }

        /* Main Area */
        .main { flex: 1; display: flex; flex-direction: column; width: 100%; }
        header { padding: 20px; border-bottom: 1px solid var(--border); text-align: center; font-weight: bold; }
        #chat-win { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 15px; }

        /* Burbujas Redondeadas */
        .msg { max-width: 80%; padding: 15px; border-radius: 25px; font-size: 15px; line-height: 1.5; border: 1px solid var(--border); }
        .user { align-self: flex-end; background: #222; border-bottom-right-radius: 5px; }
        .bot { align-self: flex-start; background: #000; border-bottom-left-radius: 5px; }

        /* Input Píldora */
        .input-area { padding: 20px; background: #000; }
        .input-box { max-width: 600px; margin: auto; display: flex; background: #111; border: 1px solid var(--border); border-radius: 30px; padding: 5px 15px; align-items: center; }
        input { flex: 1; background: transparent; border: none; color: white; padding: 12px; outline: none; }
        button.send { background: white; color: black; border: none; border-radius: 50%; width: 40px; height: 40px; cursor: pointer; font-weight: bold; }
    </style>
</head>
<body>

    <button class="btn-menu" onclick="document.getElementById('side').classList.toggle('active')">☰</button>

    <div class="sidebar" id="side">
        <div class="history" id="hist">
            <div class="history-item">Historial vacío</div>
        </div>
    </div>

    <div class="main">
        <header>NOTE AI</header>
        <div id="chat-win"></div>
        <div class="input-area">
            <div class="input-box">
                <input type="text" id="userInput" placeholder="Escribe un mensaje...">
                <button class="send" onclick="send()">➤</button>
            </div>
        </div>
    </div>

    <script>
        // PEGA AQUÍ TU LLAVE DE GROQ QUE EMPIEZA CON gsk_
        const API_KEY = "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"; 

        async function send() {
            const input = document.getElementById('userInput');
            const prompt = input.value.trim();
            if(!prompt) return;

            addMsg(prompt, 'user');
            input.value = "";
            document.getElementById('side').classList.remove('active');
            
            const botId = "bot-" + Date.now();
            addMsg("Pensando...", 'bot', botId);

            try {
                const response = await fetch("https://api.groq.com/openai/v1/chat/completions", {
                    method: "POST",
                    headers: {
                        "Authorization": `Bearer ${API_KEY}`,
                        "Content-Type": "application/json"
                    },
                    body: JSON.stringify({
                        model: "llama-3.3-70b-versatile",
                        messages: [{role: "user", content: prompt}]
                    })
                });

                const data = await response.json();
                if(data.error) throw new Error(data.error.message);
                
                document.getElementById(botId).innerText = data.choices[0].message.content;
            } catch (e) {
                document.getElementById(botId).innerText = "Error: " + e.message;
            }
        }

        function addMsg(text, type, id = null) {
            const div = document.createElement('div');
            div.className = `msg ${type}`;
            if(id) div.id = id;
            div.innerText = text;
            const win = document.getElementById('chat-win');
            win.appendChild(div);
            win.scrollTop = win.scrollHeight;
        }
    </script>
</body>
</html>
