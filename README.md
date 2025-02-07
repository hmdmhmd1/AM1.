<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <title>DarkChat :: الدردشة المجهولة 🕶️💻</title>
    <style>
        /* التصميم الداكن بتأثيرات الهاكرز */
        body {
            background: #000;
            color: #0f0;
            font-family: 'Courier New', monospace;
            margin: 0;
            overflow: hidden;
        }

        #matrix-canvas {
            position: fixed;
            top: 0;
            left: 0;
            z-index: -1;
            opacity: 0.3;
        }

        .container {
            max-width: 800px;
            margin: 20px auto;
            border: 1px solid #f00;
            padding: 20px;
            background: rgba(0, 0, 0, 0.9);
        }

        #chat-box {
            height: 400px;
            overflow-y: scroll;
            border: 1px solid #0f0;
            padding: 10px;
            margin-bottom: 20px;
        }

        .message {
            margin: 10px 0;
            animation: glitch 1s infinite;
        }

        @keyframes glitch {
            0% { text-shadow: 2px 2px #f00; }
            50% { text-shadow: -2px -2px #0f0; }
            100% { text-shadow: 2px 2px #f00; }
        }

        input, button {
            background: #111;
            color: #0f0;
            border: 1px solid #0f0;
            padding: 10px;
            margin: 5px;
        }

        button:hover {
            background: #0f0;
            color: #000;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <canvas id="matrix-canvas"></canvas>
    
    <div class="container">
        <h1>🖥️ Welcome to DarkChat :: Ghost_${Math.floor(Math.random()*1000)}</h1>
        <div id="chat-box"></div>
        
        <div id="room-controls">
            <button onclick="createRoom()">🌀 إنشاء غرفة سرية</button>
            <button onclick="joinRandomRoom()">🌐 دخول عشوائي</button>
        </div>

        <input type="text" id="message-input" placeholder="اكتب رسالتك هنا... (Enter لإرسال)">
        <button onclick="sendMessage()">📡 إرسال</button>
        
        <div id="status">⚡ حالة الاتصال: جاري التوصيل...</div>
    </div>

    <script>
        // توليد تأثير Matrix Rain
        const canvas = document.getElementById('matrix-canvas');
        const ctx = canvas.getContext('2d');
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789@#$%^&*()';
        const drops = Array(Math.floor(canvas.width/10)).fill(1);

        function drawMatrix() {
            ctx.fillStyle = 'rgba(0, 0, 0, 0.05)';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            
            ctx.fillStyle = '#0F0';
            drops.forEach((drop, i) => {
                const text = chars[Math.floor(Math.random() * chars.length)];
                ctx.fillText(text, i * 10, drop * 10);
                drops[i] = drop > canvas.height/10 ? 0 : drop + 1;
            });
        }
        setInterval(drawMatrix, 50);

        // نظام الدردشة باستخدام WebSocket
        const ws = new WebSocket('wss://your-websocket-server.com');
        let currentRoom = 'default';
        let username = `Ghost_${Math.floor(Math.random()*10000)}`;

        ws.onopen = () => {
            document.getElementById('status').textContent = '⚡ حالة الاتصال: متصل بنجاح!';
        };

        ws.onmessage = (event) => {
            const msg = JSON.parse(event.data);
            displayMessage(msg.sender, msg.text, msg.timestamp);
        };

        function sendMessage() {
            const input = document.getElementById('message-input');
            const message = {
                sender: username,
                text: input.value,
                room: currentRoom,
                timestamp: new Date().toLocaleTimeString()
            };
            ws.send(JSON.stringify(message));
            input.value = '';
        }

        function displayMessage(sender, text, time) {
            const chatBox = document.getElementById('chat-box');
            const msgElement = document.createElement('div');
            msgElement.className = 'message';
            msgElement.innerHTML = `
                <span style="color: #f00;">[${time}]</span>
                <strong>${sender}:</strong> 
                ${text} ${Math.random() > 0.5 ? '🔒' : '💀'}
            `;
            chatBox.appendChild(msgElement);
            chatBox.scrollTop = chatBox.scrollHeight;
            
            // حذف الرسائل بعد 5 دقائق
            setTimeout(() => msgElement.remove(), 300000);
        }

        // نظام الغرف والمميزات الإضافية
        function createRoom() {
            currentRoom = `secret_${Math.random().toString(36).substr(2, 9)}`;
            alert(`🛡️ تم إنشاء الغرفة السرية: ${currentRoom}`);
        }

        function joinRandomRoom() {
            const rooms = ['dark_web', 'hackers_paradise', 'cyber_void'];
            currentRoom = rooms[Math.floor(Math.random() * rooms.length)];
            alert(`🌌 تم الانضمام إلى غرفة: ${currentRoom}`);
        }

        // بوت الذكاء الاصطناعي
        window.addEventListener('keypress', (e) => {
            if (e.key === '?' && document.activeElement.id === 'message-input') {
                const aiResponses = [
                    "جاري كسر الجدران النارية...",
                    "تم اختراق الخادم الرئيسي بنجاح 🔥",
                    "تحذير: تم اكتشاف تهديد أمني!",
                    "جاري تشفير القناة... استخدام تشفير AES-256",
                    "الأنظمة تعمل بمستوى أمان 9000 🔐"
                ];
                const aiMessage = aiResponses[Math.floor(Math.random() * aiResponses.length)];
                displayMessage('AI_BOT', aiMessage, new Date().toLocaleTimeString());
            }
        });
    </script>

    <!-- الكود الخلفي (Node.js) - تحتاج إلى تشغيله على خادم منفصل -->
    <script type="text/nodejs">
        const WebSocket = require('ws');
        const wss = new WebSocket.Server({ port: 8080 });

        wss.on('connection', (ws) => {
            ws.on('message', (message) => {
                // بث الرسالة لجميع العملاء في نفس الغرفة
                wss.clients.forEach(client => {
                    if (client.room === JSON.parse(message).room && client.readyState === WebSocket.OPEN) {
                        client.send(message);
                    }
                });
            });
        });

        console.log('🕶️ خادم DarkChat يعمل على المنفذ 8080...');
    </script>
</body>
</html>
