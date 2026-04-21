<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <title>DeepSeek 鸿蒙助手</title>
    <style>
        /* 适配鸿蒙的简洁样式 */
        body { font-family: system-ui, -apple-system, 'HarmonyOS Sans', sans-serif; }
        #chat { height: 70vh; overflow-y: auto; }
        .user-msg { background: #e9ecef; }
        .ai-msg { background: #007aff; color: white; }
        input, button { font-size: 16px; padding: 12px; }
    </style>
</head>
<body>
    <div id="chat"></div>
    <div style="display: flex; gap: 8px;">
        <input id="input" placeholder="问DeepSeek..." style="flex:1">
        <button id="send">发送</button>
    </div>
    <script>
        const API_KEY = '你的DeepSeek API Key'; // ⚠️ 注意：前端暴露风险，建议后续加后端代理
        const API_URL = 'https://api.deepseek.com/v1/chat/completions';
        
        async function callDeepSeek(userMessage) {
            const response = await fetch(API_URL, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                    'Authorization': `Bearer ${API_KEY}`
                },
                body: JSON.stringify({
                    model: 'deepseek-chat',
                    messages: [{ role: 'user', content: userMessage }],
                    stream: false
                })
            });
            const data = await response.json();
            return data.choices[0].message.content;
        }
        
        // 界面交互逻辑
        document.getElementById('send').onclick = async () => {
            const input = document.getElementById('input');
            const msg = input.value.trim();
            if (!msg) return;
            appendMessage('user', msg);
            input.value = '';
            const reply = await callDeepSeek(msg);
            appendMessage('ai', reply);
        };
        
        function appendMessage(role, text) {
            const chat = document.getElementById('chat');
            const div = document.createElement('div');
            div.className = role === 'user' ? 'user-msg' : 'ai-msg';
            div.textContent = text;
            chat.appendChild(div);
            chat.scrollTop = chat.scrollHeight;
        }
    </script>
</body>
</html>
