<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Navi AI - Secure Interface</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { background-color: #131314; color: #e3e3e3; }
        .sidebar { background-color: #1e1f20; }
        .input-box { background-color: #1e1f20; border-radius: 28px; transition: 0.3s; }
        .bank-mode { border: 2px solid #ea4335 !important; box-shadow: 0 0 10px #ea4335; }
        ::-webkit-scrollbar { width: 5px; }
        ::-webkit-scrollbar-thumb { background: #3c4043; border-radius: 10px; }
    </style>
</head>
<body class="flex h-screen overflow-hidden">

    <aside class="w-64 sidebar flex flex-col hidden md:flex border-r border-gray-800">
        <div class="p-4">
            <button onclick="window.location.reload()" class="flex items-center gap-3 bg-[#28292a] hover:bg-[#333537] px-4 py-3 rounded-full text-sm font-medium w-full">
                <span class="text-xl">+</span> New Chat
            </button>
        </div>
        <div class="flex-1 overflow-y-auto px-4 mt-4">
            <p class="text-[10px] text-gray-500 uppercase tracking-widest font-bold mb-4">Memory Vault</p>
            <div id="history-list" class="space-y-1">
                </div>
        </div>
        <div class="p-4 border-t border-gray-800">
            <p class="text-[10px] text-cyan-500 font-mono tracking-tighter">🛡️ NAVI_CORE_SECURE</p>
        </div>
    </aside>

    <main class="flex-1 flex flex-col relative">
        <header class="p-4 flex justify-between items-center">
            <div class="text-xl font-google text-gray-400">Navi Assistant</div>
            <div id="bank-guard" class="hidden text-red-500 text-xs font-bold animate-pulse">
                🚫 BANKING PRIVACY: NO DATA LOGS
            </div>
            <button onclick="toggleSettings()" class="hover:bg-gray-800 p-2 rounded-full">⚙️</button>
        </header>

        <div id="chat-container" class="flex-1 overflow-y-auto px-4 md:px-32 py-10 space-y-8">
            <div id="welcome" class="mt-12">
                <h1 class="text-4xl font-semibold mb-2 bg-gradient-to-r from-blue-400 via-purple-400 to-red-400 bg-clip-text text-transparent">Hello, Siddhesar/Chandni</h1>
                <p class="text-gray-500 text-lg">Main aapka personal Navi Assistant hoon. Main aapki har baat ka honest jawab doonga aur security ka dhyan rakhunga.</p>
            </div>
        </div>

        <div class="p-4 md:px-32 bg-[#131314]">
            <div id="input-ui" class="input-box p-4 border border-transparent focus-within:border-gray-600 max-w-4xl mx-auto">
                <textarea id="userInput" rows="1" class="w-full bg-transparent border-none outline-none text-lg resize-none placeholder-gray-500" placeholder="Yahan poochein ya screen text dalein..."></textarea>
                <div class="flex justify-between items-center mt-3">
                    <div class="flex gap-4 text-gray-400">
                        <button onclick="writerMode()" id="writer-btn" title="Writer Mode">✍️</button>
                        <button title="All Languages">🌐</button>
                    </div>
                    <button onclick="sendToNavi()" class="bg-blue-600 hover:bg-blue-500 text-white px-6 py-2 rounded-full font-bold transition-all">Analyze</button>
                </div>
            </div>
            <p class="text-[10px] text-center text-gray-600 mt-2 italic font-mono">STRICT PRIVACY RULE: NAVI DOES NOT MANIPULATE DATA.</p>
        </div>
    </main>

    <div id="settings" class="hidden fixed inset-0 bg-black/80 flex items-center justify-center p-4 z-50">
        <div class="bg-[#1e1f20] p-6 rounded-3xl w-full max-w-md border border-gray-700 shadow-2xl">
            <h2 class="text-xl font-bold mb-4">Navi Configuration</h2>
            <input type="password" id="api-key" class="w-full bg-[#131314] border border-gray-700 p-3 rounded-xl mb-4 text-white" placeholder="OpenRouter API Key">
            <input type="text" id="model-id" class="w-full bg-[#131314] border border-gray-700 p-3 rounded-xl mb-6 text-white" placeholder="Model ID" value="google/gemini-pro">
            <div class="flex justify-end gap-3">
                <button onclick="toggleSettings()" class="px-4 py-2 text-gray-400">Cancel</button>
                <button onclick="saveSettings()" class="bg-blue-600 px-6 py-2 rounded-xl font-bold">Save Navi Details</button>
            </div>
        </div>
    </div>

    <script>
        let isWriterActive = false;

        function toggleSettings() { document.getElementById('settings').classList.toggle('hidden'); }
        
        function saveSettings() {
            localStorage.setItem('navi_key', document.getElementById('api-key').value);
            localStorage.setItem('navi_model', document.getElementById('model-id').value);
            toggleSettings();
        }

        function writerMode() {
            isWriterActive = !isWriterActive;
            document.getElementById('writer-btn').style.color = isWriterActive ? "#60a5fa" : "#9ca3af";
            alert(isWriterActive ? "Writer Mode Active: Main ab aapko likhne mein honest madad karunga." : "Writer Mode OFF.");
        }

        async function sendToNavi() {
            const input = document.getElementById('userInput').value.trim();
            if(!input) return;

            document.getElementById('welcome').classList.add('hidden');
            const chatBox = document.getElementById('chat-container');

            // 1. Display User Message
            chatBox.innerHTML += `<div class="flex justify-end"><div class="max-w-[80%] bg-[#2b2c2d] p-4 rounded-2xl text-gray-200">${input}</div></div>`;
            document.getElementById('userInput').value = "";

            // 2. Strict Privacy/Bank Check
            if(input.toLowerCase().includes("bank") || input.toLowerCase().includes("password")) {
                document.getElementById('bank-guard').classList.remove('hidden');
                document.getElementById('input-ui').classList.add('bank-mode');
                chatBox.innerHTML += `<div class="flex justify-start"><div class="max-w-[80%] p-4 text-red-400 font-bold border border-red-900 bg-red-900/10 rounded-2xl">Navi Alert: Yeh sensitive bank data hai. Main ise yaad nahi rakhunga aur privacy maintain karunga.</div></div>`;
                return;
            }

            // 3. API Connection (OpenRouter)
            const key = localStorage.getItem('navi_key');
            if(!key) {
                chatBox.innerHTML += `<div class="text-orange-400 text-sm italic mt-4">Pehle settings mein API key daalein...</div>`;
                toggleSettings();
                return;
            }

            try {
                const model = localStorage.getItem('navi_model') || 'google/gemini-pro';
                const response = await fetch("https://openrouter.ai/api/v1/chat/completions", {
                    method: "POST",
                    headers: { "Authorization": `Bearer ${key}`, "Content-Type": "application/json" },
                    body: JSON.stringify({ model: model, messages: [{role: "user", content: input}] })
                });
                const data = await response.json();
                const reply = data.choices[0].message.content;

                // 4. Honest/Direct Response Display
                chatBox.innerHTML += `<div class="flex justify-start"><div class="max-w-[80%] p-4 text-gray-300 leading-relaxed">${reply.replace(/\n/g, '<br>')}</div></div>`;
                
                // 5. Memory Update
                saveToVault(input);
            } catch (err) {
                chatBox.innerHTML += `<div class="text-red-500">Error: Connection failed. Key ya internet check karein.</div>`;
            }
            chatBox.scrollTop = chatBox.scrollHeight;
        }

        function saveToVault(txt) {
            let hist = JSON.parse(localStorage.getItem('vault_hist')) || [];
            hist.unshift(txt.substring(0, 20) + "...");
            localStorage.setItem('vault_hist', JSON.stringify(hist.slice(0, 10)));
            renderVault();
        }

        function renderVault() {
            const list = document.getElementById('history-list');
            list.innerHTML = "";
            let hist = JSON.parse(localStorage.getItem('vault_hist')) || [];
            hist.forEach(h => {
                list.innerHTML += `<div class="p-2 text-xs text-gray-500 hover:bg-[#2b2c2d] rounded-lg cursor-pointer truncate">${h}</div>`;
            });
        }
        renderVault();
    </script>
</body>
</html>
