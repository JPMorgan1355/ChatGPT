<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ChatGPT Clone</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        :root {
            --sidebar-width: 260px;
            --header-height: 60px;
            --input-area-height: 120px;
            --primary-color: #10a37f;
            --primary-hover: #0d8c6d;
            --bg-primary: #ffffff;
            --bg-secondary: #f7f7f8;
            --bg-sidebar: #202123;
            --text-primary: #343541;
            --text-secondary: #6e6e80;
            --border-color: #d9d9e3;
            --hover-bg: #f5f5f5;
            --message-bg-user: #f7f7f8;
            --message-bg-assistant: #ffffff;
            --code-bg: #f6f8fa;
        }

        [data-theme="dark"] {
            --bg-primary: #343541;
            --bg-secondary: #444654;
            --bg-sidebar: #202123;
            --text-primary: #ececf1;
            --text-secondary: #8e8ea0;
            --border-color: #4d4d4f;
            --hover-bg: #40414f;
            --message-bg-user: #343541;
            --message-bg-assistant: #444654;
            --code-bg: #2d2d2d;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
            background: var(--bg-primary);
            color: var(--text-primary);
            height: 100vh;
            overflow: hidden;
            transition: background 0.3s, color 0.3s;
        }

        .app-container {
            display: flex;
            height: 100vh;
        }

        /* Sidebar */
        .sidebar {
            width: var(--sidebar-width);
            background: var(--bg-sidebar);
            color: white;
            display: flex;
            flex-direction: column;
            transition: transform 0.3s;
            z-index: 100;
        }

        .sidebar-header {
            padding: 12px;
            border-bottom: 1px solid rgba(255,255,255,0.1);
        }

        .new-chat-btn {
            width: 100%;
            padding: 12px;
            background: transparent;
            border: 1px solid rgba(255,255,255,0.2);
            color: white;
            border-radius: 6px;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 12px;
            font-size: 14px;
            transition: background 0.2s;
        }

        .new-chat-btn:hover {
            background: rgba(255,255,255,0.1);
        }

        .sidebar-content {
            flex: 1;
            overflow-y: auto;
            padding: 8px;
        }

        .chat-history-item {
            padding: 12px;
            margin: 2px 0;
            border-radius: 6px;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 12px;
            font-size: 14px;
            color: #ececf1;
            transition: background 0.2s;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }

        .chat-history-item:hover {
            background: rgba(255,255,255,0.1);
        }

        .chat-history-item.active {
            background: rgba(255,255,255,0.15);
        }

        .sidebar-footer {
            padding: 12px;
            border-top: 1px solid rgba(255,255,255,0.1);
        }

        .sidebar-footer-item {
            padding: 10px;
            border-radius: 6px;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 12px;
            font-size: 14px;
            color: #ececf1;
            transition: background 0.2s;
        }

        .sidebar-footer-item:hover {
            background: rgba(255,255,255,0.1);
        }

        /* Main Content */
        .main-content {
            flex: 1;
            display: flex;
            flex-direction: column;
            background: var(--bg-primary);
            position: relative;
        }

        .header {
            height: var(--header-height);
            border-bottom: 1px solid var(--border-color);
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 0 20px;
            position: relative;
            background: var(--bg-primary);
        }

        .header-title {
            font-weight: 600;
            font-size: 16px;
        }

        .header-actions {
            position: absolute;
            right: 20px;
            display: flex;
            gap: 10px;
        }

        .icon-btn {
            width: 36px;
            height: 36px;
            border-radius: 6px;
            border: none;
            background: transparent;
            color: var(--text-secondary);
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: background 0.2s;
        }

        .icon-btn:hover {
            background: var(--hover-bg);
        }

        .menu-toggle {
            position: absolute;
            left: 20px;
            display: none;
        }

        /* Chat Area */
        .chat-container {
            flex: 1;
            overflow-y: auto;
            padding-bottom: 20px;
        }

        .welcome-screen {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100%;
            text-align: center;
            padding: 20px;
        }

        .welcome-title {
            font-size: 32px;
            font-weight: 600;
            margin-bottom: 40px;
            background: linear-gradient(90deg, #10a37f, #1a7f64);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }

        .examples-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 16px;
            max-width: 800px;
            width: 100%;
        }

        .example-card {
            padding: 16px;
            border: 1px solid var(--border-color);
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.2s;
            background: var(--bg-primary);
        }

        .example-card:hover {
            background: var(--hover-bg);
            border-color: var(--primary-color);
            transform: translateY(-2px);
            box-shadow: 0 4px 12px rgba(0,0,0,0.1);
        }

        .example-icon {
            font-size: 24px;
            margin-bottom: 8px;
        }

        .example-title {
            font-weight: 600;
            margin-bottom: 4px;
            font-size: 14px;
        }

        .example-desc {
            font-size: 13px;
            color: var(--text-secondary);
        }

        .messages-container {
            display: none;
        }

        .message {
            padding: 24px 20px;
            border-bottom: 1px solid var(--border-color);
            animation: fadeIn 0.3s ease;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .message-content {
            max-width: 800px;
            margin: 0 auto;
            display: flex;
            gap: 20px;
            align-items: flex-start;
        }

        .message-avatar {
            width: 30px;
            height: 30px;
            border-radius: 4px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: 600;
            font-size: 14px;
            flex-shrink: 0;
        }

        .user-avatar {
            background: #5436da;
            color: white;
        }

        .assistant-avatar {
            background: var(--primary-color);
            color: white;
        }

        .message-body {
            flex: 1;
            line-height: 1.6;
            font-size: 15px;
            white-space: pre-wrap;
        }

        .message-body p {
            margin-bottom: 12px;
        }

        .message-body code {
            background: var(--code-bg);
            padding: 2px 6px;
            border-radius: 4px;
            font-family: 'Courier New', monospace;
            font-size: 14px;
        }

        .message-body pre {
            background: var(--code-bg);
            padding: 16px;
            border-radius: 8px;
            overflow-x: auto;
            margin: 12px 0;
        }

        .message-actions {
            display: flex;
            gap: 8px;
            margin-top: 8px;
            opacity: 0;
            transition: opacity 0.2s;
        }

        .message:hover .message-actions {
            opacity: 1;
        }

        .action-btn {
            padding: 4px 8px;
            border: 1px solid var(--border-color);
            background: transparent;
            border-radius: 4px;
            cursor: pointer;
            font-size: 12px;
            color: var(--text-secondary);
            transition: all 0.2s;
        }

        .action-btn:hover {
            background: var(--hover-bg);
            color: var(--text-primary);
        }

        /* Input Area */
        .input-area {
            border-top: 1px solid var(--border-color);
            padding: 20px;
            background: var(--bg-primary);
        }

        .input-container {
            max-width: 800px;
            margin: 0 auto;
            position: relative;
        }

        .input-wrapper {
            display: flex;
            align-items: flex-end;
            gap: 12px;
            border: 1px solid var(--border-color);
            border-radius: 12px;
            padding: 12px 16px;
            background: var(--bg-primary);
            box-shadow: 0 2px 6px rgba(0,0,0,0.05);
            transition: border-color 0.2s, box-shadow 0.2s;
        }

        .input-wrapper:focus-within {
            border-color: var(--primary-color);
            box-shadow: 0 2px 12px rgba(16, 163, 127, 0.15);
        }

        .message-input {
            flex: 1;
            border: none;
            outline: none;
            background: transparent;
            color: var(--text-primary);
            font-size: 15px;
            resize: none;
            max-height: 200px;
            font-family: inherit;
        }

        .message-input::placeholder {
            color: var(--text-secondary);
        }

        .send-btn {
            width: 32px;
            height: 32px;
            border-radius: 6px;
            border: none;
            background: var(--primary-color);
            color: white;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: background 0.2s;
            flex-shrink: 0;
        }

        .send-btn:hover {
            background: var(--primary-hover);
        }

        .send-btn:disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }

        .input-footer {
            text-align: center;
            margin-top: 8px;
            font-size: 12px;
            color: var(--text-secondary);
        }

        /* Typing Indicator */
        .typing-indicator {
            display: flex;
            gap: 4px;
            padding: 4px 0;
        }

        .typing-dot {
            width: 8px;
            height: 8px;
            background: var(--text-secondary);
            border-radius: 50%;
            animation: typing 1.4s infinite;
        }

        .typing-dot:nth-child(2) { animation-delay: 0.2s; }
        .typing-dot:nth-child(3) { animation-delay: 0.4s; }

        @keyframes typing {
            0%, 60%, 100% { transform: translateY(0); }
            30% { transform: translateY(-10px); }
        }

        /* Scrollbar */
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }

        ::-webkit-scrollbar-track {
            background: transparent;
        }

        ::-webkit-scrollbar-thumb {
            background: #c1c1c1;
            border-radius: 4px;
        }

        ::-webkit-scrollbar-thumb:hover {
            background: #a8a8a8;
        }

        [data-theme="dark"] ::-webkit-scrollbar-thumb {
            background: #4d4d4f;
        }

        /* Mobile Responsive */
        @media (max-width: 768px) {
            .sidebar {
                position: fixed;
                left: 0;
                top: 0;
                bottom: 0;
                transform: translateX(-100%);
            }

            .sidebar.open {
                transform: translateX(0);
            }

            .menu-toggle {
                display: flex;
            }

            .examples-grid {
                grid-template-columns: 1fr;
            }

            .message-content {
                gap: 12px;
            }
        }

        /* Overlay for mobile sidebar */
        .overlay {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: rgba(0,0,0,0.5);
            z-index: 99;
        }

        .overlay.active {
            display: block;
        }

        /* Markdown-like styling */
        .message-body strong {
            font-weight: 600;
        }

        .message-body ul, .message-body ol {
            margin-left: 20px;
            margin-bottom: 12px;
        }

        .message-body li {
            margin-bottom: 4px;
        }
    </style>
</head>
<body>
    <div class="app-container">
        <!-- Sidebar -->
        <aside class="sidebar" id="sidebar">
            <div class="sidebar-header">
                <button class="new-chat-btn" onclick="startNewChat()">
                    <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                        <line x1="12" y1="5" x2="12" y2="19"></line>
                        <line x1="5" y1="12" x2="19" y2="12"></line>
                    </svg>
                    New chat
                </button>
            </div>
            <div class="sidebar-content" id="chatHistory">
                <!-- Chat history items will be inserted here -->
            </div>
            <div class="sidebar-footer">
                <div class="sidebar-footer-item" onclick="toggleTheme()">
                    <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                        <circle cx="12" cy="12" r="5"></circle>
                        <line x1="12" y1="1" x2="12" y2="3"></line>
                        <line x1="12" y1="21" x2="12" y2="23"></line>
                        <line x1="4.22" y1="4.22" x2="5.64" y2="5.64"></line>
                        <line x1="18.36" y1="18.36" x2="19.78" y2="19.78"></line>
                        <line x1="1" y1="12" x2="3" y2="12"></line>
                        <line x1="21" y1="12" x2="23" y2="12"></line>
                        <line x1="4.22" y1="19.78" x2="5.64" y2="18.36"></line>
                        <line x1="18.36" y1="5.64" x2="19.78" y2="4.22"></line>
                    </svg>
                    <span id="themeText">Dark mode</span>
                </div>
                <div class="sidebar-footer-item">
                    <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                        <path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"></path>
                        <circle cx="12" cy="7" r="4"></circle>
                    </svg>
                    User
                </div>
            </div>
        </aside>

        <!-- Overlay for mobile -->
        <div class="overlay" id="overlay" onclick="toggleSidebar()"></div>

        <!-- Main Content -->
        <main class="main-content">
            <header class="header">
                <button class="icon-btn menu-toggle" onclick="toggleSidebar()">
                    <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                        <line x1="3" y1="12" x2="21" y2="12"></line>
                        <line x1="3" y1="6" x2="21" y2="6"></line>
                        <line x1="3" y1="18" x2="21" y2="18"></line>
                    </svg>
                </button>
                <div class="header-title" id="headerTitle">ChatGPT Clone</div>
                <div class="header-actions">
                    <button class="icon-btn" onclick="shareChat()" title="Share">
                        <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                            <path d="M4 12v8a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2v-8"></path>
                            <polyline points="16 6 12 2 8 6"></polyline>
                            <line x1="12" y1="2" x2="12" y2="15"></line>
                        </svg>
                    </button>
                </div>
            </header>

            <div class="chat-container" id="chatContainer">
                <!-- Welcome Screen -->
                <div class="welcome-screen" id="welcomeScreen">
                    <h1 class="welcome-title">ChatGPT Clone</h1>
                    <div class="examples-grid">
                        <div class="example-card" onclick="sendExample('Explain quantum computing in simple terms')">
                            <div class="example-icon">💡</div>
                            <div class="example-title">Explain concepts</div>
                            <div class="example-desc">"Explain quantum computing in simple terms"</div>
                        </div>
                        <div class="example-card" onclick="sendExample('Write a Python function to sort a list')">
                            <div class="example-icon">💻</div>
                            <div class="example-title">Write code</div>
                            <div class="example-desc">"Write a Python function to sort a list"</div>
                        </div>
                        <div class="example-card" onclick="sendExample('Help me write a professional email')">
                            <div class="example-icon">✉️</div>
                            <div class="example-title">Draft emails</div>
                            <div class="example-desc">"Help me write a professional email"</div>
                        </div>
                        <div class="example-card" onclick="sendExample('Give me ideas for a weekend project')">
                            <div class="example-icon">🎨</div>
                            <div class="example-title">Brainstorm ideas</div>
                            <div class="example-desc">"Give me ideas for a weekend project"</div>
                        </div>
                    </div>
                </div>

                <!-- Messages Container -->
                <div class="messages-container" id="messagesContainer"></div>
            </div>

            <!-- Input Area -->
            <div class="input-area">
                <div class="input-container">
                    <div class="input-wrapper">
                        <textarea 
                            class="message-input" 
                            id="messageInput" 
                            placeholder="Message ChatGPT Clone..." 
                            rows="1"
                            onkeydown="handleKeyDown(event)"
                            oninput="autoResize(this)"
                        ></textarea>
                        <button class="send-btn" id="sendBtn" onclick="sendMessage()" disabled>
                            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                                <line x1="22" y1="2" x2="11" y2="13"></line>
                                <polygon points="22 2 15 22 11 13 2 9 22 2"></polygon>
                            </svg>
                        </button>
                    </div>
                    <div class="input-footer">
                        ChatGPT Clone can make mistakes. Consider checking important information.
                    </div>
                </div>
            </div>
        </main>
    </div>

    <script>
        // State
        let currentChatId = null;
        let chats = JSON.parse(localStorage.getItem('chats')) || {};
        let isGenerating = false;
        let currentTheme = localStorage.getItem('theme') || 'light';

        // Initialize
        document.addEventListener('DOMContentLoaded', () => {
            applyTheme(currentTheme);
            renderChatHistory();
            if (Object.keys(chats).length === 0) {
                startNewChat();
            } else {
                loadChat(Object.keys(chats)[0]);
            }
        });

        // Theme Management
        function applyTheme(theme) {
            currentTheme = theme;
            document.documentElement.setAttribute('data-theme', theme);
            localStorage.setItem('theme', theme);
            document.getElementById('themeText').textContent = theme === 'dark' ? 'Light mode' : 'Dark mode';
        }

        function toggleTheme() {
            applyTheme(currentTheme === 'dark' ? 'light' : 'dark');
        }

        // Chat Management
        function startNewChat() {
            const id = Date.now().toString();
            const chat = {
                id: id,
                title: 'New conversation',
                messages: [],
                createdAt: new Date().toISOString()
            };
            chats[id] = chat;
            currentChatId = id;
            saveChats();
            renderChatHistory();
            showWelcomeScreen();
            updateHeaderTitle('New conversation');
        }

        function loadChat(id) {
            currentChatId = id;
            const chat = chats[id];
            if (!chat) return;

            renderChatHistory();
            
            if (chat.messages.length === 0) {
                showWelcomeScreen();
            } else {
                showMessages();
                renderMessages(chat.messages);
            }
            
            updateHeaderTitle(chat.title);
        }

        function updateHeaderTitle(title) {
            document.getElementById('headerTitle').textContent = title;
        }

        function saveChats() {
            localStorage.setItem('chats', JSON.stringify(chats));
        }

        function renderChatHistory() {
            const container = document.getElementById('chatHistory');
            container.innerHTML = '';
            
            Object.values(chats)
                .sort((a, b) => new Date(b.createdAt) - new Date(a.createdAt))
                .forEach(chat => {
                    const div = document.createElement('div');
                    div.className = `chat-history-item ${chat.id === currentChatId ? 'active' : ''}`;
                    div.onclick = () => {
                        loadChat(chat.id);
                        if (window.innerWidth <= 768) toggleSidebar();
                    };
                    
                    const title = chat.messages.length > 0 
                        ? chat.messages[0].content.substring(0, 30) + '...' 
                        : 'New conversation';
                    
                    div.innerHTML = `
                        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                            <path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z"></path>
                        </svg>
                        <span style="overflow: hidden; text-overflow: ellipsis;">${title}</span>
                    `;
                    container.appendChild(div);
                });
        }

        // UI State
        function showWelcomeScreen() {
            document.getElementById('welcomeScreen').style.display = 'flex';
            document.getElementById('messagesContainer').style.display = 'none';
        }

        function showMessages() {
            document.getElementById('welcomeScreen').style.display = 'none';
            document.getElementById('messagesContainer').style.display = 'block';
        }

        // Message Handling
        function handleKeyDown(e) {
            if (e.key === 'Enter' && !e.shiftKey) {
                e.preventDefault();
                sendMessage();
            }
        }

        function autoResize(textarea) {
            textarea.style.height = 'auto';
            textarea.style.height = Math.min(textarea.scrollHeight, 200) + 'px';
            
            const sendBtn = document.getElementById('sendBtn');
            sendBtn.disabled = textarea.value.trim().length === 0 || isGenerating;
        }

        function sendExample(text) {
            document.getElementById('messageInput').value = text;
            autoResize(document.getElementById('messageInput'));
            sendMessage();
        }

        async function sendMessage() {
            const input = document.getElementById('messageInput');
            const content = input.value.trim();
            
            if (!content || isGenerating) return;
            
            // Add user message
            const userMessage = {
                role: 'user',
                content: content,
                timestamp: new Date().toISOString()
            };
            
            addMessageToChat(userMessage);
            input.value = '';
            input.style.height = 'auto';
            document.getElementById('sendBtn').disabled = true;
            
            // Generate response
            await generateResponse(content);
        }

        function addMessageToChat(message) {
            if (!currentChatId) startNewChat();
            
            const chat = chats[currentChatId];
            chat.messages.push(message);
            
            // Update title based on first message
            if (chat.messages.length === 1) {
                chat.title = message.content.substring(0, 30) + (message.content.length > 30 ? '...' : '');
                updateHeaderTitle(chat.title);
                renderChatHistory();
            }
            
            saveChats();
            showMessages();
            renderMessages(chat.messages);
        }

        function renderMessages(messages) {
            const container = document.getElementById('messagesContainer');
            container.innerHTML = '';
            
            messages.forEach((msg, index) => {
                const messageDiv = document.createElement('div');
                messageDiv.className = 'message';
                messageDiv.style.background = msg.role === 'user' ? 'var(--message-bg-user)' : 'var(--message-bg-assistant)';
                
                const isUser = msg.role === 'user';
                const avatar = isUser ? 'U' : 'AI';
                const avatarClass = isUser ? 'user-avatar' : 'assistant-avatar';
                
                messageDiv.innerHTML = `
                    <div class="message-content">
                        <div class="message-avatar ${avatarClass}">${avatar}</div>
                        <div style="flex: 1; min-width: 0;">
                            <div class="message-body" id="msg-${index}">${formatMessage(msg.content)}</div>
                            ${!isUser ? `
                                <div class="message-actions">
                                    <button class="action-btn" onclick="copyMessage(${index})">
                                        <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="vertical-align: middle; margin-right: 4px;">
                                            <rect x="9" y="9" width="13" height="13" rx="2" ry="2"></rect>
                                            <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"></path>
                                        </svg>
                                        Copy
                                    </button>
                                    <button class="action-btn" onclick="regenerateResponse(${index})">
                                        <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="vertical-align: middle; margin-right: 4px;">
                                            <polyline points="1 4 1 10 7 10"></polyline>
                                            <polyline points="23 20 23 14 17 14"></polyline>
                                            <path d="M20.49 9A9 9 0 0 0 5.64 5.64L1 10m22 4l-4.64 4.36A9 9 0 0 1 3.51 15"></path>
                                        </svg>
                                        Regenerate
                                    </button>
                                </div>
                            ` : ''}
                        </div>
                    </div>
                `;
                
                container.appendChild(messageDiv);
            });
            
            scrollToBottom();
        }

        function formatMessage(content) {
            // Simple markdown-like formatting
            let formatted = content
                .replace(/&/g, '&amp;')
                .replace(/</g, '&lt;')
                .replace(/>/g, '&gt;');
            
            // Bold
            formatted = formatted.replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>');
            
            // Code blocks
            formatted = formatted.replace(/```([\s\S]*?)```/g, '<pre><code>$1</code></pre>');
            
            // Inline code
            formatted = formatted.replace(/`([^`]+)`/g, '<code>$1</code>');
            
            // Line breaks
            formatted = formatted.replace(/\n/g, '<br>');
            
            return formatted;
        }

        async function generateResponse(userContent) {
            isGenerating = true;
            document.getElementById('sendBtn').disabled = true;
            
            // Show typing indicator
            const typingId = Date.now();
            const typingMessage = {
                role: 'assistant',
                content: '',
                isTyping: true,
                id: typingId
            };
            
            const chat = chats[currentChatId];
            chat.messages.push(typingMessage);
            renderMessages(chat.messages);
            
            // Simulate thinking time
            await new Promise(resolve => setTimeout(resolve, 1000));
            
            // Generate contextual response
            const response = generateContextualResponse(userContent);
            
            // Remove typing indicator and add real response
            chat.messages = chat.messages.filter(m => m.id !== typingId);
            
            const assistantMessage = {
                role: 'assistant',
                content: '',
                timestamp: new Date().toISOString()
            };
            
            chat.messages.push(assistantMessage);
            saveChats();
            
            // Stream the response
            await streamResponse(assistantMessage, response);
            
            isGenerating = false;
            document.getElementById('sendBtn').disabled = document.getElementById('messageInput').value.trim() === '';
        }

        function generateContextualResponse(input) {
            const lower = input.toLowerCase();
            
            if (lower.includes('hello') || lower.includes('hi')) {
                return "Hello! How can I help you today?";
            }
            if (lower.includes('code') || lower.includes('python') || lower.includes('javascript') || lower.includes('function')) {
                return "I'd be happy to help with code! Here's an example:\n\n```python\ndef greet(name):\n    return f\"Hello, {name}!\"\n\n# Usage\nprint(greet(\"World\"))\n```\n\nWould you like me to explain this or help with something more specific?";
            }
            if (lower.includes('explain') || lower.includes('what is') || lower.includes('how does')) {
                return "That's a great question! Let me break it down for you:\n\n**Key Points:**\n• This is a fundamental concept in the field\n• It builds upon several underlying principles\n• Understanding this will help you grasp more advanced topics\n\n**Simple Explanation:**\nThink of it like a system where inputs are processed through specific rules to produce outputs. The beauty lies in how these rules interact.\n\nWould you like me to dive deeper into any specific aspect?";
            }
            if (lower.includes('write') || lower.includes('draft') || lower.includes('email')) {
                return "Here's a draft for you:\n\n**Subject:** Follow-up on Our Discussion\n\nDear [Name],\n\nI hope this email finds you well. I wanted to follow up on our conversation regarding [topic].\n\n**Key Points:**\n• Point 1: [Detail]\n• Point 2: [Detail]\n• Point 3: [Detail]\n\nPlease let me know if you have any questions or need clarification on any of these points.\n\nBest regards,\n[Your Name]\n\nFeel free to customize this template to fit your specific needs!";
            }
            if (lower.includes('idea') || lower.includes('project') || lower.includes('suggestion')) {
                return "Here are some creative ideas for you:\n\n**1. Personal Dashboard**\nBuild a web dashboard that aggregates your daily information - weather, tasks, calendar, and news.\n\n**2. AI Photo Organizer**\nCreate a tool that automatically tags and categorizes your photos using image recognition.\n\n**3. Habit Tracker with Gamification**\nDevelop an app that turns habit formation into a game with levels, rewards, and streaks.\n\n**4. Smart Recipe Generator**\nBuild an app that suggests recipes based on ingredients you have at home.\n\nWhich one interests you most? I can help you plan the implementation!";
            }
            
            return "Thank you for your message! I'm a demo clone, so I provide simulated responses based on your input patterns.\n\n**What I can help with:**\n• General questions and explanations\n• Code examples and programming help\n• Writing assistance and drafting\n• Creative brainstorming\n\nFor real AI capabilities, you would need to integrate with an API like OpenAI's GPT-4, Claude, or similar services.\n\nIs there anything specific you'd like to explore?";
        }

        async function streamResponse(message, fullText) {
            const chat = chats[currentChatId];
            const messageIndex = chat.messages.indexOf(message);
            
            for (let i = 0; i < fullText.length; i++) {
                message.content += fullText[i];
                saveChats();
                
                // Update only the content
                const msgElement = document.getElementById(`msg-${messageIndex}`);
                if (msgElement) {
                    msgElement.innerHTML = formatMessage(message.content);
                }
                
                // Random delay for realistic typing effect
                await new Promise(resolve => setTimeout(resolve, 20 + Math.random() * 30));
                
                // Scroll if near bottom
                if (isNearBottom()) {
                    scrollToBottom();
                }
            }
        }

        function isNearBottom() {
            const container = document.getElementById('chatContainer');
            return container.scrollHeight - container.scrollTop - container.clientHeight < 100;
        }

        function scrollToBottom() {
            const container = document.getElementById('chatContainer');
            container.scrollTop = container.scrollHeight;
        }

        // Actions
        function copyMessage(index) {
            const chat = chats[currentChatId];
            const message = chat.messages[index];
            if (message) {
                navigator.clipboard.writeText(message.content).then(() => {
                    // Visual feedback could be added here
                });
            }
        }

        async function regenerateResponse(index) {
            const chat = chats[currentChatId];
            // Remove the assistant message and the user message before it
            const userMessage = chat.messages[index - 1];
            if (userMessage && userMessage.role === 'user') {
                chat.messages.splice(index - 1, 2);
                saveChats();
                renderMessages(chat.messages);
                await generateResponse(userMessage.content);
            }
        }

        function shareChat() {
            const chat = chats[currentChatId];
            if (!chat) return;
            
            const shareData = {
                title: chat.title,
                text: chat.messages.map(m => `${m.role}: ${m.content}`).join('\n\n')
            };
            
            if (navigator.share) {
                navigator.share(shareData);
            } else {
                navigator.clipboard.writeText(shareData.text);
                alert('Chat copied to clipboard!');
            }
        }

        // Mobile Sidebar
        function toggleSidebar() {
            const sidebar = document.getElementById('sidebar');
            const overlay = document.getElementById('overlay');
            sidebar.classList.toggle('open');
            overlay.classList.toggle('active');
        }
    </script>
</body>
</html>
