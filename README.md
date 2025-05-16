<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fanijo - LinkedIn Post Assistant</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
        
        :root {
            --gradient-start: #1e1b4b;
            --gradient-end: #4c1d95;
            --glass-bg: rgba(255, 255, 255, 0.05);
            --glass-border: rgba(255, 255, 255, 0.1);
        }
        
        body {
            font-family: 'Inter', sans-serif;
            background: linear-gradient(135deg, var(--gradient-start), var(--gradient-end));
            color: white;
            min-height: 100vh;
            margin: 0;
            padding: 0;
            transition: all 0.3s ease;
        }
        
        .glass-card {
            background: var(--glass-bg);
            backdrop-filter: blur(10px);
            -webkit-backdrop-filter: blur(10px);
            border: 1px solid var(--glass-border);
            box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.2);
        }
        
        .glow-button {
            transition: all 0.3s ease;
        }
        
        .glow-button:hover {
            box-shadow: 0 0 15px rgba(156, 39, 176, 0.7);
            transform: translateY(-2px);
        }
        
        .beta-badge {
            background: rgba(255, 255, 255, 0.15);
            border: 1px solid rgba(255, 255, 255, 0.2);
        }
        
        .chat-container {
            height: calc(100vh - 120px);
            overflow-y: auto;
            -webkit-overflow-scrolling: touch;
        }
        
        .user-message {
            background: rgba(124, 58, 237, 0.15);
            border: 1px solid rgba(124, 58, 237, 0.3);
        }
        
        .assistant-message {
            background: rgba(255, 255, 255, 0.05);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        .message-enter {
            animation: messageEnter 0.3s ease-out;
        }
        
        @keyframes messageEnter {
            from {
                opacity: 0;
                transform: translateY(10px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }
        
        .typing-indicator {
            display: flex;
            align-items: center;
            color: rgba(255, 255, 255, 0.7);
        }
        
        .typing-indicator .dots {
            display: flex;
            margin-left: 8px;
        }
        
        .typing-indicator .dot {
            display: inline-block;
            width: 8px;
            height: 8px;
            border-radius: 50%;
            background: rgba(255, 255, 255, 0.7);
            margin: 0 2px;
            animation: typing 1.4s infinite ease-in-out;
        }
        
        .typing-indicator .dot:nth-child(2) {
            animation-delay: 0.2s;
        }
        
        .typing-indicator .dot:nth-child(3) {
            animation-delay: 0.4s;
        }
        
        @keyframes typing {
            0%, 60%, 100% {
                transform: translateY(0);
            }
            30% {
                transform: translateY(-5px);
            }
        }
        
        #landing-page, #chat-page {
            transition: opacity 0.5s ease;
        }
        
        .hidden-page {
            opacity: 0;
            pointer-events: none;
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
        }
        
        /* Mobile optimizations */
        @media (max-width: 640px) {
            .chat-container {
                height: calc(100vh - 110px);
                padding-bottom: 20px;
            }
            
            .assistant-message, .user-message {
                max-width: 90% !important;
            }
            
            h1 {
                font-size: 2.5rem !important;
            }
            
            #message-input {
                font-size: 16px;
                padding: 10px 12px;
            }
            
            #send-button {
                padding: 10px 14px;
            }
            
            .typing-indicator {
                font-size: 14px;
            }
        }
    </style>
</head>
<body>
    <!-- Landing Page -->
    <div id="landing-page" class="flex flex-col items-center justify-center min-h-screen px-4">
        <div class="text-center max-w-2xl">
            <div class="beta-badge inline-block px-3 py-1 rounded-full text-xs font-medium mb-2">Beta</div>
            <h1 class="text-6xl font-bold mb-4 tracking-tight">Fanijo</h1>
            <p class="text-xl font-light mb-8 opacity-80">Transform your raw ideas into ready-to-use LinkedIn posts.</p>
            <button id="start-chatting" class="glass-card glow-button px-8 py-3 rounded-full font-medium text-lg flex items-center mx-auto">
                Start Chatting <i class="fas fa-arrow-right ml-2"></i>
            </button>
        </div>
        <div class="absolute bottom-6 text-gray-400 text-sm">
            Premium LinkedIn content generation.
        </div>
    </div>
    
    <!-- Chat Page -->
    <div id="chat-page" class="hidden-page">
        <div class="glass-card sticky top-0 z-10 px-4 py-3 flex items-center">
            <div class="w-8 h-8 rounded-full bg-purple-500 flex items-center justify-center mr-2">
                <i class="fas fa-comment-dots text-white"></i>
            </div>
            <span class="font-medium">Fanijo</span>
        </div>
        
        <div class="chat-container px-4 py-4">
            <div class="max-w-3xl mx-auto">
                <div class="assistant-message glass-card rounded-xl p-4 mb-4 w-full max-w-[85%] message-enter">
                    <p>Hi there! How can I help you today? 

By the way, if you'd like to turn this into a LinkedIn post, just share the topic or your rough idea — I'll help you shape it!</p>
                </div>
                
                <div id="chat-messages"></div>
                
                <div id="typing-indicator" class="typing-indicator assistant-message glass-card rounded-xl p-4 mb-4 w-full max-w-[85%] hidden">
                    <span>Fanijo is thinking</span>
                    <div class="dots">
                        <div class="dot"></div>
                        <div class="dot"></div>
                        <div class="dot"></div>
                    </div>
                </div>
            </div>
        </div>
        
        <div class="glass-card sticky bottom-0 px-4 py-3">
            <div class="max-w-3xl mx-auto flex">
                <input 
                    id="message-input" 
                    type="text" 
                    placeholder="Share your ideas for a LinkedIn post..." 
                    class="flex-1 bg-transparent border border-gray-600 rounded-l-full px-4 py-2 focus:outline-none focus:ring-1 focus:ring-purple-400"
                    autocomplete="off"
                >
                <button id="send-button" class="bg-purple-600 hover:bg-purple-700 px-4 py-2 rounded-r-full">
                    <i class="fas fa-paper-plane"></i>
                </button>
            </div>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            const landingPage = document.getElementById('landing-page');
            const chatPage = document.getElementById('chat-page');
            const startChattingBtn = document.getElementById('start-chatting');
            const messageInput = document.getElementById('message-input');
            const sendButton = document.getElementById('send-button');
            const chatMessages = document.getElementById('chat-messages');
            const typingIndicator = document.getElementById('typing-indicator');
            
            // Switch to chat interface
            startChattingBtn.addEventListener('click', function() {
                landingPage.classList.add('hidden-page');
                chatPage.classList.remove('hidden-page');
                messageInput.focus();
                
                // Add smooth transition for mobile
                setTimeout(() => {
                    document.querySelector('.chat-container').scrollTop = document.querySelector('.chat-container').scrollHeight;
                }, 100);
            });
            
            // Send message function
            function sendMessage() {
                const message = messageInput.value.trim();
                if (message === '') return;
                
                // Add user message to chat
                addMessage(message, 'user');
                messageInput.value = '';
                
                // Show typing indicator with "Fanijo is thinking" text
                typingIndicator.classList.remove('hidden');
                
                // Scroll to bottom
                scrollToBottom();
                
                // Send to the webhook
                fetch('https://luftan99.app.n8n.cloud/webhook/fanijomain2', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({
                        idea: message
                    })
                })
                .then(response => {
                    if (!response.ok) {
                        throw new Error('Network response was not ok');
                    }
                    return response.json();
                })
                .then(data => {
                    // Hide typing indicator
                    typingIndicator.classList.add('hidden');
                    
                    // Check if response has output field
                    if (data && data.output) {
                        addMessage(data.output, 'assistant');
                    } else {
                        addMessage("I couldn't generate a response. Please try again.", 'assistant');
                    }
                    
                    // Scroll to bottom
                    scrollToBottom();
                })
                .catch(error => {
                    typingIndicator.classList.add('hidden');
                    addMessage("Sorry, I encountered an error processing your request. Please try again.", 'assistant');
                    console.error('Error:', error);
                });
            }
            
            // Add message to chat
            function addMessage(text, sender) {
                const messageDiv = document.createElement('div');
                messageDiv.classList.add('message-enter');
                
                if (sender === 'user') {
                    messageDiv.className += ' user-message glass-card rounded-xl p-4 mb-4 ml-auto max-w-[85%]';
                    messageDiv.style.marginLeft = 'auto';
                } else {
                    messageDiv.className += ' assistant-message glass-card rounded-xl p-4 mb-4 max-w-[85%]';
                }
                
                // Replace newline characters with <br> tags
                const formattedText = text.replace(/\n/g, '<br>');
                messageDiv.innerHTML = `<p>${formattedText}</p>`;
                chatMessages.appendChild(messageDiv);
            }
            
            // Scroll to bottom of chat
            function scrollToBottom() {
                const container = document.querySelector('.chat-container');
                container.scrollTop = container.scrollHeight;
            }
            
            // Send message on button click
            sendButton.addEventListener('click', sendMessage);
            
            // Send message on Enter key
            messageInput.addEventListener('keypress', function(e) {
                if (e.key === 'Enter') {
                    sendMessage();
                }
            });
            
            // Add hover effect to button
            startChattingBtn.addEventListener('mouseenter', function() {
                this.classList.add('glow-button');
            });
            
            startChattingBtn.addEventListener('mouseleave', function() {
                this.classList.remove('glow-button');
            });
            
            // Mobile optimizations
            function handleMobileInput() {
                if ('visualViewport' in window) {
                    const viewport = window.visualViewport;
                    
                    viewport.addEventListener('resize', function() {
                        if (viewport.height < window.innerHeight) {
                            // Keyboard is probably open
                            setTimeout(scrollToBottom, 100);
                        }
                    });
                }
            }
            
            // Initialize mobile input handling
            handleMobileInput();
            
            // Focus input when typing indicator appears (for mobile)
            const observer = new MutationObserver(function(mutations) {
                mutations.forEach(function(mutation) {
                    if (!typingIndicator.classList.contains('hidden')) {
                        messageInput.focus();
                    }
                });
            });
            
            observer.observe(typingIndicator, {
                attributes: true,
                attributeFilter: ['class']
            });
        });
    </script>
</body>
</html>
