<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Universal AI Chat - Navi Core</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = { darkMode: 'class' }
    </script>
    <style>
        ::-webkit-scrollbar { width: 5px; }
        ::-webkit-scrollbar-thumb { background: #334155; border-radius: 10px; }
        .bank-protect { border: 2px solid #ef4444 !important; }
    </style>
</head>
<body class="bg-[#131314] text-gray-200 h-screen flex dark">

    <aside class="w-64 bg-[#1e1f20] border-r border-gray-800 flex flex-col hidden md:flex transition-all">
        <div class="p-4">
            <button onclick="newChat()" class="w-full flex items-center gap-2 bg-[#28292a] hover:bg-[#333537] text-white px-4 py-3 rounded-full text-sm font-medium">
                <span class="text-xl">+</span> New Chat
            </button>
        </div>
        <div id="history-list" class="flex-1 overflow-y-auto px-3 space-y-1">
            </div>
        <div class="p-4 border-t border-gray-800 text-[10px] text-cyan-500 font-mono tracking-widest">
            🛡️ NAVI SECURITY ACTIVE
        </div>
    </aside>

    <main class="flex-1 flex flex-col relative overflow-hidden">
        <header class="h-14 flex items-center justify-between px-6 bg-[#131314]">
            <div class="text-xl font-medium text-gray-400">Navi AI</div>
            <div id="status-tag" class="hidden bg-red-900/30 text-red-400 text-[10px] px-3 py-1 rounded-full border border-red-800">
                ⚠️ SENSITIVE MODE: NO LOGS
            </div>
            <button onclick="openSettings()" class="text-gray-400 hover:text-white">⚙️ Settings</button>
        </header>

        <div id="chat-window" class="flex-1 overflow-y-auto p-4 md:px-32 space-y-8 pb-32">
            <div id="welcome" class="mt-20 text-center space-y-4">
                <h1 class="text-4xl font-semibold bg-gradient-to-r from-blue-400 to-red-400 bg-clip-text text-transparent">Hello, Main Navi Hoon</h1>
                <p class="text-gray-500">Main aapki help kar sakta hoon aur apki privacy ka pehra de sakta hoon.</p>
            </div>
        </div>

        <div class="absolute bottom-0 w-full p-4 md:px-32 bg-[#131314]">
            <div id="input-container" class="max-w-4xl mx-auto bg-[#1e1f20] rounded-3xl p-4 shadow-xl border border-transparent focus-within:border-gray-700 transition-all">
                <textarea id="userInput" rows="1" class="w-full bg-transparent border-none outline-none resize-none text-lg text-white placeholder-gray-500" placeholder="Yahan type karein..." oninput="autoResize(this)"></textarea>
                <div class="flex justify-between items-center mt-2">
                    <div class="flex gap-4 text-gray-400 text-sm">
                        <button onclick="toggleWriter()" id="writer-btn" title="Writer Mode">✍️</button>
                        <button title="Image (Coming Soon)">🖼️</button>
                    </div>
                    <button onclick="handleSend()" class="bg-blue-600 hover:bg-blue-500 text-white px-6 py-2 rounded-full font-bold">Analyze</button>
                </div>
            </div>
            <p class="text-[10px] text-center text-gray-600 mt-2 italic">Navi hamesha sach bolega aur data leak se bachayega.</p>
        </div>
    </main>

    <div id="settings" class="hidden fixed inset-0 bg-black/80 backdrop-blur-sm z-50 flex items-center justify-center p-4">
        <div class="bg-[#1e1f20] p-6 rounded-2xl w-full max-w-md border border-gray-700">
            <h3 class="text-xl font-bold mb-4">Configuration</h3>
            <input type="password" id="api-key" class="w-full bg-[#131314] border border-gray-700 p-3 rounded-lg mb-4 text-white" placeholder="OpenRouter API Key (sk-or-...)">
            <input type="text" id="model-name" class="w-full bg-[#131314] border border-gray-700 p-3 rounded-lg mb-4 text-white" placeholder="Model (e.g. google/gemini-pro)" value="google/gemini-pro">
            <div class="flex justify-end gap-3">
                <button onclick="closeSettings()" class="text-gray-400 px-4 py-2">Cancel</button>
                <button onclick="saveSettings()" class="bg-blue-600 px-6 py-2 rounded-lg text-white font-bold">Save</button>
            </div>
        </div>
    </div>

    <script>
        let isWriterMode = false;

        function autoResize(el) {
            el.style.height = 'auto';
            el.style.height = el.scrollHeight + 'px';
        }

        function openSettings() { document.getElementById('settings').classList.remove('hidden'); }
        function closeSettings() { document.getElementById('settings').classList.add('hidden'); }
        
        function saveSettings() {
            localStorage.setItem('navi_api_key', document.getElementById('api-key').value);
            localStorage.setItem('navi_model', document.getElementById('model-name').value);
            closeSettings();
        }

        function toggleWriter() {
            isWriterMode = !isWriterMode;
            document.getElementById('writer-btn').classList.toggle('text-blue-400');
            alert(isWriterMode ? "Writer Mode Active: Direct & Honest help ON." : "Writer Mode OFF.");
        }

        async function handleSend() {
            const inputEl = document.getElementById('userInput');
            const text = inputEl.value.trim();
            if(!text) return;

            document.getElementById('welcome').classList.add('hidden');
            addMsg("user", text);
            inputEl.value = "";
            inputEl.style.height = 'auto';

            // Security Check (Navi Core)
            const status = document.getElementById('status-tag');
            const container = document.getElementById('input-container');
            
            if(text.toLowerCase().includes("bank") || text.toLowerCase().includes("password")) {
                status.classList.remove('hidden');
                container.classList.add('bank-protect');
                addMsg("navi", "⚠️ Sensing Private Data. Main ise save nahi kar raha hoon. Aapki security meri priority hai.");
                return; // Direct protection
            } else {
                status.classList.add('hidden');
                container.classList.remove('bank-protect');
            }

            // Call API
            const key = localStorage.getItem('navi_api_key');
            if(!key) {
                addMsg("navi", "Kripya Settings mein jaakar OpenRouter API Key daalein.");
                openSettings();
                return;
            }

            try {
                const response = await fetch("https://openrouter.ai/api/v1/chat/completions", {
                    method: "POST",
                    headers: {
                        "Authorization": `Bearer ${key}`,
                        "Content-Type": "application/json"
                    },
                    body: JSON.stringify({
                        model: localStorage.getItem('navi_model') || "google/gemini-pro",
                        messages: [{role: "user", content: text}]
                    })
                });
                const data = await response.json();
                const reply = data.choices[0].message.content;
                addMsg("navi", reply);
                
                // Save History
                saveHistory(text);
            } catch (e) {
                addMsg("navi", "Error: Connection mein garbar hai. Check API key.");
            }
        }

        function addMsg(sender, txt) {
            const win = document.getElementById('chat-window');
            const div = document.createElement('div');
            div.className = `flex ${sender === 'user' ? 'justify-end' : 'justify-start'}`;
            div.innerHTML = `<div class="max-w-[85%] p-4 rounded-2xl ${sender === 'user' ? 'bg-[#2b2c2d]' : 'bg-transparent'}">${txt.replace(/\n/g, '<br>')}</div>`;
            win.appendChild(div);
            win.scrollTop = win.scrollHeight;
        }

        function saveHistory(t) {
            let hist = JSON.parse(localStorage.getItem('navi_hist')) || [];
            hist.unshift(t.substring(0, 25) + "...");
            localStorage.setItem('navi_hist', JSON.stringify(hist.slice(0, 10)));
            renderHist();
        }

        function renderHist() {
            const list = document.getElementById('history-list');
            list.innerHTML = "";
            let hist = JSON.parse(localStorage.getItem('navi_hist')) || [];
            
