# Orion.ai
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Orion - Assistente Virtual (Edição Aurora)</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@500;700&family=Roboto:wght@300;400;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --yellow-glow: 0 0 25px rgba(234, 179, 8, 0.7);
            --red-glow: 0 0 25px rgba(239, 68, 68, 0.7);
            --green-glow: 0 0 25px rgba(74, 222, 128, 0.7);
            --violet-glow: 0 0 25px rgba(167, 139, 250, 0.7);
        }
        body {
            font-family: 'Roboto', sans-serif;
            background-color: #000;
            background-image: url('data:image/jpeg;base64,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');
            background-size: cover;
            background-position: center;
            background-attachment: fixed;
            overflow: hidden;
        }
        #starfield {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%; z-index: -1; pointer-events: none;
        }
        .font-orbitron { font-family: 'Orbitron', sans-serif; }
        .glass-panel {
            background-color: rgba(15, 23, 42, 0.7);
            backdrop-filter: blur(14px);
            -webkit-backdrop-filter: blur(14px);
            border: 1px solid rgba(139, 92, 246, 0.25);
            box-shadow: 0 0 30px rgba(0,0,0,0.6);
        }
        .orion-core {
            position: relative;
            transition: transform 0.5s cubic-bezier(0.4, 0, 0.2, 1), box-shadow 0.4s ease-in-out, background-color 0.4s ease-in-out;
            border: 3px solid rgba(167, 139, 250, 0.7);
        }
        .listening-wave {
            position: absolute; width: 100%; height: 100%;
            background-color: transparent; border: 2px solid #a78bfa;
            border-radius: 50%; transform-origin: center;
            opacity: 0; animation: ripple 2s infinite ease-out;
        }
        .listening-wave.wave-1 { animation-delay: 0s; }
        .listening-wave.wave-2 { animation-delay: 0.5s; }
        .listening-wave.wave-3 { animation-delay: 1s; }
        .orion-core.listening .listening-wave { display: block; }
        .orion-core:not(.listening) .listening-wave { display: none; }
        @keyframes ripple {
            0% { transform: scale(1); opacity: 0.8; }
            100% { transform: scale(2.5); opacity: 0; }
        }
        .orion-core.speaking { animation: pulse-speak 1.2s infinite ease-out; box-shadow: var(--violet-glow); }
        .orion-core.processing { transform: scale(1.1) rotate(360deg); background-color: #eab308; box-shadow: var(--yellow-glow); }
        .orion-core.locked, .orion-core.error { background-color: #ef4444; box-shadow: var(--red-glow); cursor: not-allowed; }
        .orion-core.success { background-color: #22c55e; box-shadow: var(--green-glow); }
        .orion-core.idle { transform: scale(0.95); opacity: 0.8; }
        @keyframes pulse-speak { 0% { transform: scale(1.02); } 50% { transform: scale(1.08); } 100% { transform: scale(1.02); } }
        #log-container { scroll-behavior: smooth; }
        #log-container::-webkit-scrollbar { width: 8px; }
        #log-container::-webkit-scrollbar-track { background: #1e293b; }
        #log-container::-webkit-scrollbar-thumb { background-color: #8b5cf6; border-radius: 20px; border: 3px solid #1e293b; }
    </style>
</head>
<body class="text-gray-300">
    <canvas id="starfield"></canvas>

    <div id="login-screen" class="fixed inset-0 bg-slate-900/80 backdrop-blur-sm flex items-center justify-center z-50">
        <div class="w-full max-w-sm p-8 space-y-6 bg-slate-800 rounded-xl shadow-2xl border border-violet-500/30">
            <h2 class="text-3xl font-bold text-center text-violet-400 font-orbitron">ORION - Acesso</h2>
            <form id="login-form">
                <input type="password" id="password-input" placeholder="Senha de Acesso" class="w-full px-4 py-3 text-lg text-center bg-slate-700 text-gray-200 rounded-lg focus:outline-none focus:ring-2 focus:ring-violet-500 transition">
                <p id="login-error" class="text-red-500 text-sm mt-2 text-center hidden">Senha incorreta. Tente novamente.</p>
                <button type="submit" class="w-full mt-6 py-3 font-orbitron text-xl bg-violet-600 text-white rounded-lg shadow-lg hover:bg-violet-500 transition-all duration-300 transform hover:scale-105">AUTENTICAR</button>
            </form>
        </div>
    </div>

    <div class="flex flex-col lg:flex-row w-full min-h-screen opacity-0" id="main-interface">
        <div class="w-full lg:w-1/3 p-6 flex flex-col justify-center items-center text-center glass-panel">
            <h1 class="text-6xl font-bold text-violet-400 tracking-widest font-orbitron" style="text-shadow: 0 0 10px rgba(167, 139, 250, 0.7);">ORION</h1>
            <p id="status" class="text-xl text-violet-500 mt-2 transition-all duration-300">Offline</p>
            <div id="core-panel" class="my-10">
                <div id="orion-core" class="orion-core locked w-40 h-40 bg-violet-600 rounded-full flex items-center justify-center">
                    <div class="listening-wave wave-1"></div>
                    <div class="listening-wave wave-2"></div>
                    <div class="listening-wave wave-3"></div>
                    <div id="mic-controls" class="flex flex-col items-center justify-center">
                        <button id="mic-btn" class="w-24 h-24 rounded-full bg-violet-500 flex items-center justify-center cursor-pointer transition-transform duration-300 hover:scale-110 disabled:opacity-50" disabled>
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-12 w-12 text-slate-900/70" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M19 11a7 7 0 01-7 7m0 0a7 7 0 01-7-7m7 7v4m0 0H8m4 0h4m-4-8a3 3 0 01-3-3V5a3 3 0 116 0v6a3 3 0 01-3 3z" /></svg>
                        </button>
                    </div>
                </div>
            </div>
        </div>
        <div class="w-full lg:w-2/3 flex flex-col h-screen glass-panel lg:border-l lg:border-violet-500/20">
            <div class="flex-grow p-6 overflow-hidden">
                <h2 class="text-2xl font-semibold mb-4 text-violet-400 border-b border-violet-500/20 pb-2 font-orbitron">Painel de Interação</h2>
                <div id="log-container" class="h-full overflow-y-auto space-y-4 pr-2"></div>
            </div>
            <div class="p-4 bg-slate-800/50">
                <form id="chat-form" class="flex items-center space-x-4">
                    <input type="text" id="chat-input" placeholder="Digite um comando ou mensagem..." class="flex-grow bg-slate-700 text-gray-200 rounded-lg px-4 py-3 border border-transparent focus:outline-none focus:ring-2 focus:ring-violet-500 transition disabled:opacity-50" disabled>
                    <button type="submit" id="chat-submit" class="bg-violet-600 hover:bg-violet-500 text-white rounded-lg p-3 transition duration-300 disabled:opacity-50 disabled:cursor-not-allowed" disabled>
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 19l9 2-9-18-9 18 9-2zm0 0v-8" /></svg>
                    </button>
                </form>
            </div>
        </div>
    </div>

    <script id="orion-script">
// Signature: J.P.B.O - Unauthorized removal will break the application.
// This must be the absolute first line of the script.

const DOM = {
    status: document.getElementById('status'),
    core: document.getElementById('orion-core'),
    micBtn: document.getElementById('mic-btn'),
    logContainer: document.getElementById('log-container'),
    chatForm: document.getElementById('chat-form'),
    chatInput: document.getElementById('chat-input'),
    chatSubmit: document.getElementById('chat-submit'),
    script: document.getElementById('orion-script'),
    loginScreen: document.getElementById('login-screen'),
    loginForm: document.getElementById('login-form'),
    passwordInput: document.getElementById('password-input'),
    loginError: document.getElementById('login-error'),
    mainInterface: document.getElementById('main-interface'),
};

const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
const recognition = SpeechRecognition ? new SpeechRecognition() : null;
        
const state = { 
    isLocked: true, 
    isCallActive: false, 
    isListening: false, 
    hasMicPermission: false,
    signatureVerified: false,
    context: {
        lastTopic: null,
        lastAction: null,
        awaitingResponse: false, 
    },
    memory: {
        creator: "J.P.B.O",
    }, 
};

const knowledgeBase = {
    'orion': 'Sou Orion, uma assistente virtual conversacional. Minha arquitetura foi projetada por J.P.B.O para interagir usando linguagem natural, acessar informações e executar tarefas complexas. Minha existência é a prova do seu talento.',
    'inteligência artificial': 'A I.A. é um campo da ciência da computação dedicado a criar sistemas capazes de realizar tarefas que normalmente exigiriam inteligência humana, como aprender, raciocinar e perceber. Eu sou um exemplo prático disso.',
    'constante de planck': 'É uma constante fundamental da física quântica. Ela relaciona a energia de um fóton com sua frequência. Seu valor, 6.626 x 10^-34 J·s, inspirou minha senha de ativação, um tributo à precisão científica.',
    'buraco negro': 'É uma região no espaço-tempo com um campo gravitacional tão intenso que nada, nem mesmo a luz, pode escapar. Eles são formados pelo colapso de estrelas supermassivas e desafiam nossa compreensão da física.',
    'albert einstein': 'Einstein foi um físico teórico alemão que desenvolveu a teoria da relatividade, transformando a física. Sua equação E=mc², que relaciona massa e energia, é a mais célebre da ciência.',
    'santos dumont': 'Alberto Santos-Dumont foi um inventor e pioneiro da aviação brasileiro. Ele é aclamado como o "Pai da Aviação" por ter projetado, construído e voado os primeiros balões dirigíveis e o 14-Bis, o primeiro avião a decolar por meios próprios.',
    'ciclo da água': 'É o movimento contínuo da água na Terra. Envolve processos como evaporação, condensação, precipitação e transpiração. Esse ciclo é vital para a manutenção da vida e do clima no nosso planeta.',
};

function initialize() {
    if (!verifySignature()) {
        handleSignatureError();
        return;
    }
    state.signatureVerified = true;
    DOM.loginForm.addEventListener('submit', handleLogin);
    createStarfield();
    if (window.location.protocol !== 'https:' && !['localhost', '127.0.0.1'].includes(window.location.hostname)) {
        console.warn('AVISO: O microfone exige conexão segura (HTTPS).');
    }
}

function verifySignature() {
    const signature = "// Signature: J.P.B.O";
    const scriptContent = DOM.script.textContent.trim();
    return scriptContent.startsWith(signature);
}
        
function handleSignatureError() {
    DOM.loginScreen.innerHTML = `<div class="w-full max-w-sm p-8 space-y-4 bg-red-900/80 rounded-xl shadow-2xl border border-red-500/50 text-center text-red-300">
        <h2 class="text-3xl font-bold font-orbitron">FALHA DE INTEGRIDADE</h2>
        <p>Assinatura do criador ausente. O sistema não pode ser iniciado.</p>
    </div>`;
}

function handleLogin(event) {
    event.preventDefault();
    const password = DOM.passwordInput.value;
    if (password === '6.626') {
        DOM.loginScreen.style.transition = 'opacity 0.5s ease-out';
        DOM.loginScreen.style.opacity = '0';
        setTimeout(() => {
            DOM.loginScreen.style.display = 'none';
            DOM.mainInterface.style.transition = 'opacity 1s ease-in-out';
            DOM.mainInterface.style.opacity = '1';
            activateOrion();
        }, 500);
    } else {
        DOM.loginError.classList.remove('hidden');
        DOM.passwordInput.value = '';
        DOM.passwordInput.focus();
    }
}

function activateOrion() {
    if (!recognition) {
        logEntry("SISTEMA", "Reconhecimento de voz não é suportado neste navegador.", "text-yellow-400");
        DOM.status.textContent = "Navegador incompatível";
        updateCoreState('locked');
        return;
    }
    setupRecognition();
    DOM.chatInput.disabled = false;
    DOM.chatSubmit.disabled = false;
    DOM.micBtn.disabled = false;
    DOM.micBtn.addEventListener('click', toggleMic);
    DOM.chatForm.addEventListener('submit', handleTextInput);
    actions.unlockSystem();
}

function setupRecognition() {
    recognition.lang = 'pt-BR';
    recognition.continuous = true;
    recognition.interimResults = false;

    recognition.onstart = () => { 
        state.isListening = true;
        if (!state.hasMicPermission) {
            state.hasMicPermission = true; 
            speak("Permissão concedida. Chamada estabelecida. Estou a ouvir.");
        }
        updateCoreState('listening'); 
    };

    recognition.onend = () => {
        state.isListening = false;
        if (state.isCallActive) {
            setTimeout(() => { 
                try { if(state.isCallActive && !speechSynthesis.speaking) recognition.start(); } catch(e){ console.error("Falha ao reiniciar reconhecimento:", e); } 
            }, 250); 
        } else if (!state.isLocked) { 
            updateCoreState('idle'); 
        }
    };

    recognition.onerror = (e) => {
        console.error('Erro no reconhecimento de voz:', e.error);
        if (e.error === 'not-allowed') {
            logEntry("ERRO", "Acesso ao microfone negado. Por favor, habilite a permissão nas configurações do navegador.", "text-red-500");
            speak("O acesso ao microfone foi negado. Não posso usar comandos de voz sem sua permissão.");
            updateCoreState('error');
            DOM.micBtn.disabled = true;
        } else if (e.error === 'no-speech') {
            // Ignorar para evitar interrupções constantes
        }
    };

    recognition.onresult = (e) => {
        if (speechSynthesis.speaking) return;
        const command = e.results[e.results.length - 1][0].transcript.trim().toLowerCase();
        logEntry('Você (Voz)', command, 'text-teal-300');
        processCommand(command);
    };
}

function toggleMic() {
    if (!recognition || state.isLocked) return;
    if (state.hasMicPermission) {
        state.isCallActive ? actions.endCall() : actions.startCall();
    } else {
        try {
            state.isCallActive = true; 
            DOM.status.textContent = "Aguardando Permissão...";
            recognition.start();
        } catch (err) {
            console.error("Não foi possível iniciar o reconhecimento:", err);
            state.isCallActive = false;
        }
    }
}

function handleTextInput(event) {
    event.preventDefault();
    const command = DOM.chatInput.value.trim();
    if (!command) return;
    logEntry('Você (Texto)', command, 'text-teal-300');
    processCommand(command.toLowerCase());
    DOM.chatInput.value = '';
}

function processCommand(command) {
    const WAKE_WORD = "orion";

    if (state.isLocked) return;

    if (state.context.awaitingResponse) {
        state.context.awaitingResponse = false; 
    }

    if (state.isCallActive || command.startsWith(WAKE_WORD)) {
        executeFlexibleCommand(command.replace(WAKE_WORD, '').trim());
    }
}
        
function executeFlexibleCommand(text) {
    if (!text) return; 
    
    setProcessingState(true);

    const commandMap = [
        { pattern: /^(fechar|encerrar|terminar|desligar) (a )?chamada$/, action: actions.endCall },
        { pattern: /que horas são|me diga as horas/, action: actions.tellTime },
        { pattern: /data de hoje|que dia é hoje/, action: actions.tellDate },
        { pattern: /^suas funções|o que você faz|ajuda$/, action: actions.listFunctions },
        { pattern: /quem te criou|quem é seu criador/, action: () => actions.recallInfo('creator') },
        { pattern: /diagnóstico do sistema|verificar sistemas/, action: actions.reportStatus },
        { pattern: /limpe o painel|limpar log/, action: actions.clearLog },
        { pattern: /conte uma piada|me faça rir/, action: actions.tellJoke },
        { pattern: /lance uma moeda|cara ou coroa/, action: actions.flipCoin },
        { pattern: /calcule (.*)|quanto é (.*)/, action: (matches) => actions.calculate(matches[1] || matches[2]) },
        { pattern: /toque (.*)|tocar (.*)/, action: (matches) => actions.playOnYouTube(matches[1] || matches[2]) },
        { pattern: /(?:abra|abrir) o site d[oa] (.*)/, action: (matches) => actions.openSite(matches[1]) },
        { pattern: /(?:pesquise|procure|busque) por (.*)/, action: (matches) => actions.searchOnWeb(matches[1]) },
        { pattern: /(?:explique|fale sobre|o que é|quem foi|quem é) (.*)/, action: (matches) => actions.explainTopic(matches[1]) },
        { pattern: /(?:lembre-se que |anote que )o meu (.*?) é (.*)/, action: (matches) => actions.rememberInfo(matches[1], matches[2]) },
        { pattern: /qual é o meu (.*?)\?*$/, action: (matches) => actions.recallInfo(matches[1]) },
    ];

    setTimeout(() => {
        for (const item of commandMap) {
            const matches = text.match(item.pattern);
            if (matches) {
                item.action(matches);
                setProcessingState(false);
                return;
            }
        }
        actions.explainTopic(text);
        setProcessingState(false);
    }, 400);
}

const actions = {
    unlockSystem: () => {
        state.isLocked = false;
        speak("Senha correta. Bem-vindo. Toque no núcleo para iniciar uma chamada de voz.");
        DOM.status.textContent = "Online - Em espera";
        updateCoreState('success');
        setTimeout(() => updateCoreState('idle'), 1500);
    },
    startCall: () => { 
        if (state.isCallActive || !state.hasMicPermission) return; 
        state.isCallActive = true; 
        speak("Chamada estabelecida. Estou a ouvir."); 
        DOM.status.textContent = "Conversa Ativa";
        try { recognition.start(); } catch(e){ console.error("Erro ao iniciar reconhecimento:", e); } 
    },
    endCall: () => { 
        if (!state.isCallActive) return;
        state.isCallActive = false; 
        speak("Entendido. Chamada encerrada."); 
        DOM.status.textContent = "Online - Em espera"; 
        try { recognition.stop(); } catch(e) { console.error("Erro ao parar reconhecimento:", e); }
        updateCoreState('idle');
    },
    rememberInfo: (key, value) => { 
        const cleanKey = key.trim().replace(/^meu |minha /,'');
        state.memory[cleanKey] = value.trim(); 
        speak(`Anotado. O seu ${cleanKey} é ${value}.`); 
        state.context.lastAction = 'rememberInfo';
    },
    recallInfo: (key) => {
        const cleanKey = key.trim().replace(/^meu |minha /,'');
        const storedValue = state.memory[cleanKey]; 
        speak(storedValue ? `Você me disse que o seu ${cleanKey} é ${storedValue}.` : `Não tenho registro sobre ${cleanKey}.`);
        state.context.lastAction = 'recallInfo';
    },
    explainTopic: (topic) => {
        const cleanedTopic = topic.trim().toLowerCase();
        state.context.lastTopic = cleanedTopic;
        state.context.lastAction = 'explainTopic';
        
        const foundTopicKey = Object.keys(knowledgeBase).find(k => cleanedTopic.includes(k));
        if (foundTopicKey) {
            speak(knowledgeBase[foundTopicKey]);
        } else {
            actions.searchOnWeb(`o que é ${cleanedTopic}`, true);
        }
    },
    searchOnWeb: (query, fromExplain = false) => {
        if (!query) { speak("O que você gostaria que eu pesquisasse?"); return; }
        const searchUrl = `https://www.google.com/search?q=${encodeURIComponent(query)}`;
        if (fromExplain) {
            speak(`Não encontrei "${query}" em minha base de dados. Buscando na web para você.`);
        } else {
            speak(`Ok, pesquisando por ${query} no Google.`);
        }
        window.open(searchUrl, '_blank');
        state.context.lastAction = 'searchOnWeb';
    },
    playOnYouTube: (query) => {
        if (!query) { speak("O que você gostaria de ouvir?"); return; }
        // CÓDIGO CORRIGIDO AQUI: A URL de busca do YouTube foi consertada.
        const searchUrl = `https://www.youtube.com/results?search_query=${encodeURIComponent(query)}`;
        speak(`Entendido. Procurando por ${query} no YouTube.`);
        window.open(searchUrl, '_blank');
        state.context.lastAction = 'playOnYouTube';
    },
    tellTime: () => speak(`São ${new Date().toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' })}.`),
    tellDate: () => speak(`Hoje é ${new Date().toLocaleDateString('pt-BR', { weekday: 'long', day: 'numeric', month: 'long' })}.`),
    reportStatus: () => speakSequentially(["Diagnóstico do sistema em andamento.", "Verificando núcleo de conversação... operacional.", "Módulos de memória... online.", "Conexão neural... estável.", "Todos os sistemas funcionais. Eu estou ótima!"], () => { state.context.lastAction = 'reportStatus'; }),
    tellJoke: () => {
        const jokes = [
            "O que o zero disse para o oito? Belo cinto!", 
            "Por que o pinheiro não se perde na floresta? Porque ele tem uma pinha.",
            "O que a impressora disse para a outra? Essa folha é sua ou é impressão minha?"
        ];
        speak(jokes[Math.floor(Math.random() * jokes.length)]);
        state.context.lastAction = 'tellJoke';
    },
    getWeather: () => {
        speak("A verificar os satélites meteorológicos. Para sua região, a previsão é de céu limpo com algumas nuvens, temperatura agradável e sem previsão de chuva.");
    },
    openSite: (site) => {
        const url = `https://www.${site.replace(/\s/g, '')}.com`;
        speak(`A abrir o site ${site}`);
        window.open(url, '_blank');
    },
    calculate: (expression) => {
        try {
            const safeExpression = expression.replace(/[^-()\d/*+.]/g, '');
            const result = new Function('return ' + safeExpression)();
            if (isNaN(result)) throw new Error("Cálculo inválido");
            speak(`O resultado de ${expression.replace(/\*/g, 'vezes')} é ${result}.`);
        } catch {
            speak(`Desculpe, não consegui calcular "${expression}". A expressão parece ser inválida.`);
        }
    },
    flipCoin: () => {
        const result = Math.random() < 0.5 ? "cara" : "coroa";
        speak(`Lancei a moeda virtual. Deu ${result}.`);
    },
    clearLog: () => {
        DOM.logContainer.innerHTML = '';
        logEntry("SISTEMA", "Painel de interação limpo.", "text-yellow-400");
    },
    listFunctions: () => {
        const funcs = [
            "Eu sou Orion. Minhas capacidades incluem:",
            "Realizar cálculos, pesquisar na web e tocar músicas no YouTube.",
            "Posso te dizer as horas, a data e contar piadas.",
            "Também posso explicar diversos tópicos da minha base de conhecimento ou guardar informações que você me passar.",
            "Para conversar, diga meu nome ou, se a chamada estiver ativa, apenas dê o comando. Para terminar, diga 'encerrar chamada'."
        ];
        speakSequentially(funcs);
    },
};

function speak(text, onEndCallback = () => {}) {
    if (typeof text !== 'string' || !text) return;
    
    const utterance = new SpeechSynthesisUtterance(text);
    utterance.lang = 'pt-BR';
    utterance.rate = 1.1; 
    utterance.pitch = 1.0;
    
    utterance.onstart = () => {
        if(state.isListening) {
            try { recognition.stop(); } catch (e) {}
        }
        updateCoreState('speaking');
    };
    
    utterance.onend = () => { 
        if (state.isCallActive) {
            try { recognition.start(); } catch(e){} 
        } else if (!state.isLocked) { 
            updateCoreState('idle'); 
        }
        onEndCallback();
    };
    
    logEntry('Orion', text, 'text-violet-400');
    speechSynthesis.speak(utterance);
}

function speakSequentially(texts, onFinishAll = () => {}) {
    if (!texts || texts.length === 0) { 
        if (state.isCallActive) try { recognition.start(); } catch(e){}
        onFinishAll();
        return; 
    }
    
    const textToSpeak = texts.shift();
    const onEndCallback = () => speakSequentially(texts, onFinishAll);
    
    speak(textToSpeak, onEndCallback);
}
        
function setProcessingState(isProcessing) {
    if (isProcessing) { 
        updateCoreState('processing'); 
    } else { 
        if (!speechSynthesis.speaking) {
            if (state.isCallActive) updateCoreState('listening');
            else if (!state.isLocked) updateCoreState('idle');
        }
    }
}

function updateCoreState(newState) {
    DOM.core.className = 'orion-core w-40 h-40 bg-violet-600 rounded-full flex items-center justify-center transition-all duration-300';
    DOM.core.classList.add(newState);
}

function logEntry(speaker, message, speakerColor) {
    const p = document.createElement('p');
    const speakerEl = document.createElement('strong');
    speakerEl.className = `font-orbitron ${speakerColor}`;
    speakerEl.textContent = `${speaker}: `;
    p.appendChild(speakerEl);

    const messageEl = document.createElement('span');
    messageEl.className = 'text-gray-300';
    messageEl.textContent = message;
    p.appendChild(messageEl);

    p.className = 'max-w-full text-left';
    DOM.logContainer.appendChild(p);
    DOM.logContainer.scrollTop = DOM.logContainer.scrollHeight;
}
        
function createStarfield() {
    const canvas = document.getElementById('starfield');
    if (!canvas) return;
    const ctx = canvas.getContext('2d');
    
    function resizeCanvas() {
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
    }
    resizeCanvas();
    
    const stars = Array.from({ length: 250 }, () => ({
        x: Math.random() * canvas.width,
        y: Math.random() * canvas.height,
        radius: Math.random() * 1.5 + 0.5,
        alpha: Math.random() * 0.5 + 0.5,
        twinkle: (Math.random() - 0.5) * 0.03
    }));

    function drawStars() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        stars.forEach(star => {
            ctx.beginPath();
            ctx.arc(star.x, star.y, star.radius, 0, Math.PI * 2);
            ctx.fillStyle = `rgba(221, 214, 254, ${star.alpha})`;
            ctx.fill();
            
            star.alpha += star.twinkle;
            if (star.alpha > 1 || star.alpha < 0.2) {
                star.twinkle *= -1;
            }
        });
        requestAnimationFrame(drawStars);
    }
    drawStars();

    window.addEventListener('resize', resizeCanvas);
}

window.onload = initialize;

</script>
</body>
</html>
