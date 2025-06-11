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
            /* Adjusted for stronger neon glows */
            --yellow-glow: 0 0 35px rgba(255, 255, 0, 0.9), 0 0 50px rgba(255, 255, 0, 0.6); /* Neon Yellow */
            --red-glow: 0 0 35px rgba(255, 0, 0, 0.9), 0 0 50px rgba(255, 0, 0, 0.6);      /* Neon Red */
            --green-glow: 0 0 35px rgba(0, 255, 0, 0.9), 0 0 50px rgba(0, 255, 0, 0.6);    /* Neon Green */
            --violet-glow: 0 0 35px rgba(170, 0, 255, 0.9), 0 0 50px rgba(170, 0, 255, 0.6); /* Neon Violet (original primary color) */
        }
        body {
            font-family: 'Roboto', sans-serif;
            background-color: #000; /* Solid black background */
            overflow: hidden; /* Prevent scrolling if content overflows due to fixed background */
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
            transition: transform 0.5s cubic-bezier(0.4, 0, 0.2, 1), box-shadow 0.4s ease-in-out, background-color 0.4s ease-in-out, border-color 0.4s ease-in-out; /* Added border-color to transition */
            border: 3px solid transparent; /* Border will be managed by JS for consistency with glow */
        }
        .listening-wave {
            position: absolute; width: 100%; height: 100%;
            background-color: transparent;
            border-radius: 50%; transform-origin: center;
            opacity: 0; animation: ripple 2s infinite ease-out;
        }
        .listening-wave.wave-1 { animation-delay: 0s; border: 2px solid rgba(170, 0, 255, 0.7); } /* Violet wave */
        .listening-wave.wave-2 { animation-delay: 0.5s; border: 2px solid rgba(170, 0, 255, 0.5); }
        .listening-wave.wave-3 { animation-delay: 1s; border: 2px solid rgba(170, 0, 255, 0.3); }

        /* Only display waves when in 'listening' state */
        .orion-core.listening .listening-wave { display: block; }
        .orion-core:not(.listening) .listening-wave { display: none; }
        @keyframes ripple {
            0% { transform: scale(1); opacity: 0.8; }
            100% { transform: scale(2.5); opacity: 0; }
        }
        .orion-core.speaking { animation: pulse-speak 1.2s infinite ease-out; }
        /* Processing state for Orion core */
        .orion-core.processing {
            animation: pulse-processing 1s infinite ease-in-out alternate;
        }
        @keyframes pulse-processing {
            0% { transform: scale(1); box-shadow: var(--yellow-glow); }
            100% { transform: scale(1.05); box-shadow: var(--yellow-glow); } /* Keep the same strong glow */
        }

        .orion-core.locked, .orion-core.error { } /* Styles handled by JS updateCoreState */
        .orion-core.success { } /* Styles handled by JS updateCoreState */
        .orion-core.idle { transform: scale(0.95); opacity: 0.8; } /* Keep idle slightly scaled down */

        @keyframes pulse-speak { 0% { transform: scale(1.02); } 50% { transform: scale(1.08); } 100% { transform: scale(1.02); } }
        #log-container {
            scroll-behavior: smooth;
            /* Garante que o overflow e a rolagem funcionem corretamente */
            overflow-y: auto;
            /* Define uma altura máxima para o container de log, permitindo rolagem */
            max-height: calc(100vh - 200px); /* Ajuste conforme necessário para o layout */
            flex-grow: 1; /* Permite que o log container cresça e ocupe o espaço disponível */
        }
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

    <div class="flex flex-col lg:flex-row w-full min-h-screen opacity-0" id="main-interface" style="display: none;">
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
                    <input type="text" id="chat-input" placeholder="Digite um comando ou mensagem..." class="flex-grow bg-slate-700 text-gray-200 rounded-lg px-4 py-3 border border-transparent focus:outline-none focus:ring-2 focus:ring-violet-500 transition" disabled>
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

        // Verifica e atribui a API de Reconhecimento de Fala
        const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
        let recognition = null; // Inicializa como null e cria apenas se disponível

        const state = {
            isLocked: true,
            isCallActive: false,
            isListening: false,
            hasMicPermission: false, // Isso será definido após a verificação de permissão
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

        // AVISO: EXPOR A CHAVE DA API DIRETAMENTE NO CÓDIGO DO LADO DO CLIENTE NÃO É SEGURO PARA PRODUÇÃO.
        // Para uma aplicação pública, isso deve ser tratado por um servidor de backend.
        // Para uma demonstração local, certifique-se de que a chave tenha permissões restritas.
        const GEMINI_API_KEY = "AIzaSyD4m9PaejyZRrBcaxS9IDR83MenmgdpbJk"; // Mantenha esta linha como está.
        // REMOVIDA: const OPENWEATHER_API_KEY = "SUA_API_KEY_OPENWEATHER"; 

        let geminiModel = null;
        let geminiInitialized = false; // Novo estado para rastrear a inicialização do Gemini

        const knowledgeBase = {
            'orion': 'Sou Orion, uma assistente virtual conversacional. Minha arquitetura foi projetada por J.P.B.O para interagir usando linguagem natural, acessar informações e executar tarefas complexas. Minha existência é a prova do seu talento.',
            'inteligência artificial': 'A I.A. é um campo da ciência da computação dedicado a criar sistemas capazes de realizar tarefas que normalmente exigiriam inteligência humana, como aprender, raciocinar e perceber. Eu sou um exemplo prático disso.',
            'constante de planck': 'É uma constante fundamental da física quântica. Ela relaciona a energia de um fóton com sua frequência. Seu valor, 6.626 x 10^-34 J·s, inspirou minha senha de ativação, um tributo à precisão científica.',
            'buraco negro': 'É uma região no espaço-tempo com um campo gravitacional tão intenso que nada, nem mesmo a luz, pode escapar. Eles são formados pelo colapso de estrelas supermassivas e desafiam nossa compreensão da física.',
            'albert einstein': 'Albert Einstein foi um físico teórico alemão que desenvolveu a teoria da relatividade, um dos dois pilares da física moderna. Sua famosa equação E=mc² revolucionou a física, mostrando a equivalência entre massa e energia.',
            'santos dumont': 'Alberto Santos-Dumont foi um inventor e pioneiro da aviação brasileiro. Ele é amplamente reconhecido como o "Pai da Aviação" por ter projetado, construído e voado os primeiros balões dirigíveis e o avião 14-Bis, realizando o primeiro voo público de um aparelho mais pesado que o ar na Europa.',
            'ciclo da água': 'O ciclo da água, ou ciclo hidrológico, descreve o movimento contínuo da água na Terra. Ele envolve a evaporação da água da superfície, sua condensação para formar nuvens, a precipitação como chuva ou neve, e o retorno da água para oceanos ou rios, completando o ciclo vital para a manutenção da vida.',
            // 'temperatura': 'Para obter a temperatura atual e a previsão do tempo para sua localização, eu precisaria acessar um serviço meteorológico externo. Posso pesquisar por "previsão do tempo" no Google para você, se desejar.', // Removido, pois teremos a API de clima real.
            'sol': 'O Sol é a estrela central do nosso sistema solar, uma esfera gigantesca e luminosa de plasma quente, que gera energia por fusão nuclear. É a principal fonte de luz e calor para a Terra e essencial para a vida em nosso planeta.',
            'terra': 'A Terra é o terceiro planeta a partir do Sol e o único corpo celeste conhecido por abrigar vida. Caracterizada por uma atmosfera rica em oxigênio, água líquida abundante e uma grande biodiversidade, é um sistema dinâmico de placas tectônicas, oceanos e continentes.',
            'lua': 'A Lua é o único satélite natural da Terra e o quinto maior do Sistema Solar. Ela desempenha um papel crucial na estabilização da inclinação do eixo da Terra, o que ajuda a manter um clima estável, e é responsável pelas marés oceânicas.',
            'marte': 'Marte é o quarto planeta a partir do Sol e é conhecido como o "Planeta Vermelho" devido à presença de óxido de ferro em sua superfície. Possui uma atmosfera fina e é uma dos planetas mais estudados em busca de sinais de vida passada ou presente.',
            'gravidade': 'A gravidade é uma força fundamental da natureza que atrai corpos com massa um ao outro. É o que nos mantém no chão, faz os planetas orbitarem o Sol e forma galáxias. Albert Einstein descreveu a gravidade como uma curvatura do espaço-tempo causada pela massa e energia.'
        };

        // Torna a função initialize assíncrona
        async function initialize() {
            console.log("Inicializando Orion...");
            // Verifica a existência de todos os elementos DOM críticos
            if (!DOM.status || !DOM.core || !DOM.micBtn || !DOM.logContainer || !DOM.chatForm || !DOM.chatInput || !DOM.chatSubmit || !DOM.script || !DOM.loginScreen || !DOM.loginForm || !DOM.passwordInput || !DOM.loginError || !DOM.mainInterface) {
                console.error("Erro: Um ou mais elementos DOM essenciais não foram encontrados. A inicialização de Orion falhou.");
                // Exibe uma mensagem de erro na tela de login se possível
                if (DOM.loginScreen) {
                    DOM.loginScreen.innerHTML = `
                        <div class="w-full max-w-sm p-8 space-y-6 bg-slate-800 rounded-xl shadow-2xl border border-red-500/30">
                            <h2 class="text-3xl font-bold text-center text-red-400 font-orbitron">ERRO CRÍTICO</h2>
                            <p class="text-gray-200 text-center">Componentes essenciais da interface não encontrados. Por favor, verifique a integridade do arquivo.</p>
                        </div>
                    `;
                    DOM.loginScreen.style.display = 'flex';
                }
                return; // Impede que o restante do script seja executado
            }

            if (!verifySignature()) {
                handleSignatureError();
                return;
            }
            state.signatureVerified = true;
            DOM.loginForm.addEventListener('submit', handleLogin);
            DOM.passwordInput.addEventListener('input', () => {
                DOM.loginError.classList.add('hidden'); // Esconde o erro ao digitar
            });
            createStarfield();

            // Verifica se a conexão é HTTPS ou localhost para o microfone
            if (window.location.protocol !== 'https:' && !['localhost', '127.0.0.1'].includes(window.location.hostname)) {
                console.warn('AVISO: O microfone exige conexão segura (HTTPS). Por favor, acesse via HTTPS ou localhost.');
                logEntry("ALERTA", "Para usar o microfone, acesse esta página via HTTPS ou localhost.", "text-yellow-400");
                if (DOM.micBtn) DOM.micBtn.disabled = true; // Desabilita o botão se não for HTTPS/localhost
            } else {
                // Inicializa o reconhecimento de fala e solicita permissão apenas se o ambiente for adequado
                initializeSpeechRecognition();
            }

            // Inicialização do Gemini movida para AQUI DENTRO, usando await import da CDN ESPECÍFICA para browser
            try {
                // CORREÇÃO: Usando a URL correta para o pacote Gemini para navegador via jsdelivr
                const module = await import('https://cdn.jsdelivr.net/npm/@google/generative-ai');
                const GoogleGenerativeAI = module.GoogleGenerativeAI;

                if (GoogleGenerativeAI) {
                    const genAI = new GoogleGenerativeAI(GEMINI_API_KEY);
                    geminiModel = genAI.getGenerativeModel({ model: "gemini-pro" });
                    geminiInitialized = true;
                    console.log("Modelo Gemini inicializado com sucesso!");
                } else {
                    console.warn("GoogleGenerativeAI não está disponível (após import). Funções de IA avançadas serão limitadas.");
                    logEntry("ALERTA", "Módulo Gemini indisponível. Funções de IA avançadas serão limitadas. Verifique sua conexão e a chave da API.", "text-yellow-500");
                    geminiInitialized = false;
                }
            } catch (e) {
                console.error("Erro ao carregar ou inicializar GoogleGenerativeAI:", e);
                // Melhorar a mensagem de erro para o usuário
                let errorMessage = e.message;
                if (e instanceof TypeError && e.message.includes('Failed to fetch dynamically imported module')) {
                    errorMessage = "Falha ao carregar o módulo Gemini. Verifique sua conexão de internet ou o status da CDN.";
                }
                logEntry("ERRO", `Erro ao inicializar Gemini: ${errorMessage}. Funções de IA avançadas serão limitadas. Verifique a chave da API.`, "text-red-500");
                geminiInitialized = false;
            }
        }

        function verifySignature() {
            const expectedSignature = "// Signature: J.P.B.O - Unauthorized removal will break the application.";

            if (!DOM.script) {
                console.error("Elemento script com ID 'orion-script' não encontrado. Não é possível verificar a assinatura.");
                return false;
            }

            const scriptContent = DOM.script.textContent || '';
            const scriptLines = scriptContent.trim().split('\n');

            // Percorre as linhas e verifica a assinatura. Permite comentários vazios ou '//'
            for (let i = 0; i < scriptLines.length; i++) {
                const line = scriptLines[i].trim();
                if (line === expectedSignature) {
                    return true;
                }
                // Se a linha não for a assinatura e não for um comentário (incluindo vazios), falha a verificação.
                // Isso impede que código seja adicionado antes da assinatura.
                if (line !== '' && !line.startsWith('//') && line !== expectedSignature) {
                    return false;
                }
            }
            return false;
        }

        function handleLogin(event) {
            event.preventDefault();
            const password = DOM.passwordInput.value;
            // Validação de entrada para a senha
            if (typeof password !== 'string' || password.trim() === '') {
                DOM.loginError.textContent = "Por favor, digite a senha.";
                DOM.loginError.classList.remove('hidden');
                DOM.passwordInput.focus();
                return;
            }

            if (password === '6.626') {
                DOM.loginScreen.style.transition = 'opacity 0.5s ease-out';
                DOM.loginScreen.style.opacity = '0';
                setTimeout(() => {
                    DOM.loginScreen.style.display = 'none';
                    DOM.mainInterface.style.transition = 'opacity 1s ease-in-out';
                    DOM.mainInterface.style.opacity = '1';
                    DOM.mainInterface.style.display = 'flex';
                    activateOrion();
                }, 500);
            } else {
                DOM.loginError.textContent = "Senha incorreta. Tente novamente.";
                DOM.loginError.classList.remove('hidden');
                DOM.passwordInput.value = '';
                DOM.passwordInput.focus();
            }
        }

        function initializeSpeechRecognition() {
            if (SpeechRecognition) {
                recognition = new SpeechRecognition();
                recognition.continuous = false; // Define como false para parar após cada fala
                recognition.lang = 'pt-BR';
                recognition.interimResults = false;
                recognition.maxAlternatives = 1;

                recognition.onstart = () => {
                    console.log("Reconhecimento de fala iniciado.");
                    state.isListening = true;
                    updateCoreState('listening');
                };

                recognition.onend = () => {
                    console.log("Reconhecimento de fala encerrado.");
                    state.isListening = false;
                    // Só reinicia a escuta se a chamada estiver ativa e Orion não estiver falando
                    if (state.isCallActive && !speechSynthesis.speaking) {
                        setTimeout(() => {
                            try {
                                if (recognition && !state.isListening && !speechSynthesis.speaking) {
                                    recognition.start();
                                    state.isListening = true;
                                }
                            } catch (e) {
                                console.error("Erro ao reiniciar reconhecimento após onend:", e);
                                logEntry("ERRO", "Falha ao reiniciar microfone automaticamente.", "text-red-500");
                                updateCoreState('error');
                            }
                        }, 100);
                    } else if (!state.isLocked && !speechSynthesis.speaking) {
                        updateCoreState('idle');
                    }
                };

                recognition.onresult = (event) => {
                    if (event.results.length > 0 && event.results[0].length > 0) {
                        const speechResult = event.results[0][0].transcript.toLowerCase();
                        console.log(`Você disse: ${speechResult}`);
                        logEntry('Você', speechResult, 'text-gray-200');
                        processCommand(speechResult);
                    } else {
                        console.warn("Nenhum resultado de fala válido.");
                        if (state.isCallActive && !speechSynthesis.speaking) {
                            try {
                                if (recognition && !state.isListening && !speechSynthesis.speaking) {
                                    recognition.start();
                                    state.isListening = true;
                                }
                            } catch (e) {
                                console.error("Erro ao reiniciar reconhecimento após nenhum resultado:", e);
                            }
                        } else {
                            setProcessingState(false); // No result means done processing this input
                        }
                    }
                };

                recognition.onerror = (event) => {
                    console.error('Erro no reconhecimento de fala:', event.error);
                    state.isListening = false;
                    if (event.error === 'not-allowed' || event.error === 'PermissionDeniedError') {
                        state.hasMicPermission = false;
                        logEntry("ERRO", "Permissão de microfone negada. Por favor, permita o acesso ao microfone no seu navegador.", "text-red-500");
                        speak("Acesso ao microfone negado. Por favor, permita o acesso para que eu possa ouví-lo.", () => {
                            if (DOM.micBtn) DOM.micBtn.disabled = true;
                            updateCoreState('error');
                            setProcessingState(false);
                        });
                    } else if (event.error === 'no-speech') {
                        console.log("Nenhuma fala detectada, reiniciando reconhecimento se em chamada.");
                        if (state.isCallActive && !speechSynthesis.speaking) {
                            setTimeout(() => {
                                try {
                                    if (recognition && !state.isListening && !speechSynthesis.speaking) {
                                        recognition.start();
                                        state.isListening = true;
                                    }
                                } catch (e) {
                                    console.error("Erro ao tentar reiniciar reconhecimento após 'no-speech':", e);
                                    logEntry("ERRO", "Falha ao reiniciar microfone após silêncio.", "text-red-500");
                                    updateCoreState('error');
                                    setProcessingState(false);
                                }
                            }, 100);
                        } else {
                            updateCoreState('idle');
                            setProcessingState(false);
                        }
                    } else if (event.error === 'audio-capture') {
                        logEntry("ERRO", "Problema com o dispositivo de audio. Verifique sua conexão.", "text-red-500");
                        speak("Há um problema com o dispositivo de áudio. Por favor, verifique seu microfone.", () => {
                            if (DOM.micBtn) DOM.micBtn.disabled = true;
                            updateCoreState('error');
                            setProcessingState(false);
                        });
                    } else {
                        logEntry("ERRO", `Problema com o microfone: ${event.error}`, "text-red-500");
                        if (state.isCallActive && !speechSynthesis.speaking) {
                            setTimeout(() => {
                                try {
                                    if (recognition && !state.isListening && !speechSynthesis.speaking) {
                                        recognition.start();
                                        state.isListening = true;
                                    }
                                } catch (e) {
                                    console.error("Erro ao tentar reiniciar reconhecimento após erro generalizado:", e);
                                    logEntry("ERRO", "Falha ao reiniciar microfone após erro.", "text-red-500");
                                    updateCoreState('error');
                                    setProcessingState(false);
                                }
                            }, 500);
                        } else {
                            updateCoreState('idle');
                            setProcessingState(false);
                        }
                    }
                };
                requestMicPermission(); // Solicita permissão do microfone
            } else {
                logEntry("ALERTA", "Seu navegador não suporta a API Web Speech Recognition. O comando de voz não estará disponível.", "text-yellow-400");
                if (DOM.micBtn) DOM.micBtn.disabled = true;
            }
        }

        async function requestMicPermission() {
            try {
                // Adiciona verificação para navigator.mediaDevices
                if (!navigator.mediaDevices || !navigator.mediaDevices.getUserMedia) {
                    throw new Error("navigator.mediaDevices.getUserMedia não disponível no ambiente atual.");
                }
                const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
                stream.getTracks().forEach(track => track.stop());
                state.hasMicPermission = true;
                console.log("Permissão de microfone concedida.");
                if (!state.isLocked && DOM.micBtn) {
                    DOM.micBtn.disabled = false;
                    logEntry("SISTEMA", "Microfone pronto. Toque no núcleo para iniciar.", "text-green-400");
                }
            } catch (err) {
                state.hasMicPermission = false;
                console.error("Erro ao obter permissão do microfone:", err);
                if (err.name === 'NotAllowedError' || err.name === 'PermissionDeniedError') {
                    logEntry("ERRO", "Permissão para usar o microfone foi negada. Por favor, permita o acesso nas configurações do navegador.", "text-red-500");
                    speak("Não foi possível acessar o microfone. Por favor, verifique as permissões do navegador.", () => {
                        if (DOM.micBtn) DOM.micBtn.disabled = true;
                    });
                } else if (err.name === 'NotFoundError' || err.name === 'DevicesNotFoundError') {
                    logEntry("ERRO", "Nenhum microfone encontrado. Verifique se um microfone está conectado e funcionando.", "text-red-500");
                    speak("Não detectei um microfone. Por favor, conecte um e tente novamente.", () => {
                        if (DOM.micBtn) DOM.micBtn.disabled = true;
                    });
                } else if (err.message.includes('getUserMedia não disponível')) {
                    logEntry("ERRO", "A funcionalidade de microfone não está disponível. Certifique-se de estar usando HTTPS ou localhost.", "text-red-500");
                    speak("O recurso de voz está indisponível. Para ativá-lo, acesse-me via HTTPS ou localhost.", () => {
                         if (DOM.micBtn) DOM.micBtn.disabled = true;
                    });
                }
                else {
                    logEntry("ERRO", `Erro desconhecido ao obter permissão do microfone: ${err.message}`, "text-red-500");
                    speak("Houve um erro inesperado ao tentar acessar o microfone.", () => {
                        if (DOM.micBtn) DOM.micBtn.disabled = true;
                    });
                }
                if (DOM.micBtn) DOM.micBtn.disabled = true;
            }
        }

        function activateOrion() {
            actions.unlockSystem();
            // Habilita o campo de texto e o botão de envio
            if (DOM.chatInput) DOM.chatInput.disabled = false;
            if (DOM.chatSubmit) DOM.chatSubmit.disabled = false;

            if (DOM.micBtn) DOM.micBtn.addEventListener('click', actions.startCall);
            if (DOM.chatForm) DOM.chatForm.addEventListener('submit', handleChatSubmit);

            if (state.hasMicPermission && DOM.micBtn) {
                DOM.micBtn.disabled = false;
            } else if (DOM.micBtn) {
                requestMicPermission(); // Tenta solicitar permissão novamente se não a tiver
            }
            updateCoreState('idle');
            // Log de status do Gemini após ativação
            if (geminiInitialized) {
                logEntry("SISTEMA", "Módulo de IA Gemini operacional.", "text-green-400");
            } else {
                logEntry("ALERTA", "Módulo de IA Gemini inativo. Funções avançadas serão limitadas.", "text-yellow-500");
            }
        }

        function handleChatSubmit(event) {
            event.preventDefault();
            const message = DOM.chatInput.value.trim();
            if (message) {
                logEntry('Você (texto)', message, 'text-gray-200');
                processCommand(message);
                DOM.chatInput.value = '';
            }
        }

        function processCommand(command) {
            if (!command || typeof command !== 'string' || command.trim() === '') {
                console.warn("Comando vazio ou inválido recebido.");
                setProcessingState(false);
                return;
            }

            setProcessingState(true);
            const lowerCommand = command.toLowerCase();

            if (lowerCommand.includes('orion') && lowerCommand.includes('iniciar chamada')) {
                actions.startCall();
            } else if (lowerCommand.includes('encerrar chamada') || lowerCommand.includes('terminar chamada')) {
                actions.endCall();
            } else if (lowerCommand.includes('meu nome é') || lowerCommand.includes('chamo-me')) {
                const name = lowerCommand.match(/(?:meu nome é|chamo-me)\s+([a-zA-Záàâãéèêíóôõúüçñ\s]+)/i);
                if (name && name[1]) {
                    actions.rememberInfo('nome', name[1].trim());
                } else {
                    speak("Por favor, diga seu nome claramente.", () => setProcessingState(false));
                }
            } else if (lowerCommand.includes('qual é o meu nome') || lowerCommand.includes('como me chamo')) {
                actions.recallInfo('nome');
            } else if (lowerCommand.includes('que horas são') || lowerCommand.includes('horas')) {
                actions.tellTime();
            } else if (lowerCommand.includes('que dia é hoje') || lowerCommand.includes('data')) {
                actions.tellDate();
            } else if (lowerCommand.includes('status do sistema') || lowerCommand.includes('como você está')) {
                actions.reportStatus();
            } else if (lowerCommand.includes('contar uma piada') || lowerCommand.includes('me conte uma piada')) {
                actions.tellJoke();
            // REMOVIDA: } else if (lowerCommand.includes('previsão do tempo') || lowerCommand.includes('clima')) {
            // REMOVIDA:     actions.getWeather(); 
            } else if (lowerCommand.includes('abrir site') || lowerCommand.includes('ir para o site')) {
                const siteMatch = lowerCommand.match(/(?:abrir site|ir para o site)\s+(.+)/i);
                if (siteMatch && siteMatch[1]) {
                    actions.openSite(siteMatch[1].trim());
                } else {
                    speak("Qual site você gostaria de abrir?", () => setProcessingState(false));
                }
            } else if (lowerCommand.includes('calcular') || lowerCommand.includes('quanto é')) {
                const calcMatch = lowerCommand.match(/(?:calcular|quanto é)\s+(.+)/i);
                if (calcMatch && calcMatch[1]) {
                    actions.calculate(calcMatch[1].trim());
                } else {
                    speak("Qual cálculo você gostaria de fazer?", () => setProcessingState(false));
                }
            } else if (lowerCommand.includes('jogar moeda') || lowerCommand.includes('lançar moeda')) {
                actions.flipCoin();
            } else if (lowerCommand.includes('limpar histórico') || lowerCommand.includes('limpar painel')) {
                actions.clearLog();
            } else if (lowerCommand.includes('quais suas funções') || lowerCommand.includes('o que você pode fazer')) {
                actions.listFunctions();
            } else if (lowerCommand.includes('pesquisar por') || lowerCommand.includes('procurar por')) {
                const queryMatch = lowerCommand.match(/(?:pesquisar por|procurar por)\s+(.+)/i);
                if (queryMatch && queryMatch[1]) {
                    actions.explainTopic(queryMatch[1].trim());
                } else {
                    speak("O que você gostaria de pesquisar?", () => setProcessingState(false));
                }
            } else if (lowerCommand.includes('tocar no youtube') || lowerCommand.includes('reproduzir no youtube')) {
                const queryMatch = lowerCommand.match(/(?:tocar no youtube|reproduzir no youtube)\s+(.+)/i);
                if (queryMatch && queryMatch[1]) {
                    actions.searchYouTube(queryMatch[1].trim()); // Nova ação
                } else {
                    speak("O que você gostaria de tocar no YouTube?", () => setProcessingState(false));
                }
            }
            else {
                // Fallback para Gemini se nenhum comando específico for correspondido
                if (geminiInitialized) { // Verifica se o Gemini foi inicializado com sucesso
                    actions.explainTopic(command); // Usa explainTopic como uma consulta geral para Gemini
                } else {
                    speak("Desculpe, não entendi o comando. Meu módulo de inteligência artificial não está ativo no momento.", () => setProcessingState(false));
                    logEntry("ALERTA", "Módulo de IA Gemini inativo. Funções avançadas serão limitadas.", "text-yellow-500");
                }
            }
        }

        const actions = {
            unlockSystem: () => {
                state.isLocked = false;
                speak("Senha correta. Bem-vindo. Toque no núcleo para iniciar uma chamada de voz.", () => {
                    if (DOM.status) DOM.status.textContent = "Online - Em espera";
                    updateCoreState('success');
                    setTimeout(() => updateCoreState('idle'), 1500);
                });
            },
            startCall: () => {
                if (state.isCallActive) {
                    speak("A chamada já está ativa. Estou ouvindo.", () => setProcessingState(false));
                    return;
                }
                if (!state.hasMicPermission) {
                    speak("Não tenho permissão para usar o microfone. Por favor, permita o acesso.", () => {
                        requestMicPermission(); // Requisita permissão novamente
                        setProcessingState(false);
                    });
                    return;
                }
                if (!recognition) {
                    logEntry("ALERTA", "Seu navegador não suporta a API Web Speech Recognition. O comando de voz não estará disponível.", "text-yellow-400");
                    speak("Seu navegador não suporta o reconhecimento de fala. Por favor, use o painel de texto.", () => setProcessingState(false));
                    return;
                }

                state.isCallActive = true;
                speak("Chamada estabelecida. Estou a ouvir.", () => {
                    try {
                        if (!state.isListening && recognition && !speechSynthesis.speaking) {
                            recognition.start();
                            state.isListening = true;
                        }
                        setProcessingState(false);
                    } catch(e){
                        console.error("Erro ao iniciar reconhecimento:", e);
                        logEntry("ERRO", "Falha ao iniciar microfone. Tente novamente.", "text-red-500");
                        updateCoreState('error');
                        setProcessingState(false);
                    }
                });
            },
            endCall: () => {
                if (!state.isCallActive) {
                    speak("Ainda não iniciamos uma chamada.", () => setProcessingState(false));
                    return;
                }
                state.isCallActive = false;
                speak("Entendido. Chamada encerrada.", () => {
                    if (DOM.status) DOM.status.textContent = "Online - Em espera";
                    try {
                        if (recognition && state.isListening) {
                            recognition.stop();
                            state.isListening = false;
                        }
                    } catch(e) {
                        console.error("Erro ao parar reconhecimento:", e);
                        logEntry("ERRO", "Falha ao parar microfone. Recarregue a página se persistir.", "text-red-500");
                    }
                    updateCoreState('idle');
                    setProcessingState(false);
                });
            },
            rememberInfo: (key, value) => {
                if (typeof key !== 'string' || key.trim() === '' || typeof value !== 'string' || value.trim() === '') {
                    speak("Não consegui entender a informação que você quer que eu lembre. Por favor, seja mais claro.", () => setProcessingState(false));
                    return;
                }
                const cleanKey = key.trim().replace(/^(o meu |a minha |meu |minha )/i,'');
                state.memory[cleanKey] = value.trim();
                speak(`Anotado. O seu ${cleanKey} é ${value}.`, () => setProcessingState(false));
                state.context.lastAction = 'rememberInfo';
            },
            recallInfo: (key) => {
                if (typeof key !== 'string' || key.trim() === '') {
                    speak("Não entendi qual informação você gostaria que eu recordasse.", () => setProcessingState(false));
                    return;
                }
                const cleanKey = key.trim().replace(/^(o meu |a minha |meu |minha )/i,'');
                const storedValue = state.memory[cleanKey];
                speak(storedValue ? `Você me disse que o seu ${cleanKey} é ${storedValue}.` : `Não tenho registro sobre ${cleanKey}.`, () => setProcessingState(false));
                state.context.lastAction = 'recallInfo';
            },
            explainTopic: async (topic) => {
                const cleanedTopic = topic.trim().toLowerCase();
                state.context.lastTopic = cleanedTopic;
                state.context.lastAction = 'explainTopic';

                const foundTopicKey = Object.keys(knowledgeBase).find(k => cleanedTopic.includes(k));
                if (foundTopicKey) {
                    speak(knowledgeBase[foundTopicKey], () => setProcessingState(false));
                    return;
                }

                if (!geminiInitialized) { // Verifica a nova flag de inicialização
                    console.error("Modelo de IA não inicializado. Verifique se a API está configurada e carregada.");
                    speak("Desculpe, meu módulo de inteligência artificial não está ativo no momento. Por favor, verifique a chave da API e tente novamente mais tarde.", () => setProcessingState(false));
                    logEntry("ERRO", "Módulo Gemini inativo. Verifique a chave da API e os logs do console.", "text-red-500");
                    return;
                }

                speak(`Aguarde um momento enquanto eu busco informações sobre ${topic}...`);
                
                try {
                    const prompt = `Você é Orion, uma assistente virtual avançada criada por J.P.B.O. Responda de forma concisa, útil e inteligente à pergunta: ${topic}. Evite se referir a si mesma como "IA" ou "modelo de linguagem", e sempre use o nome "Orion" para se referir a si mesma, se necessário. Mantenha um tom profissional e prestativo.`;
                    
                    const result = await geminiModel.generateContent({
                        contents: [{ parts: [{ text: prompt }] }],
                        // Removed safety_settings to allow default safety filtering.
                        // Adding custom safety settings can block responses if not configured correctly.
                    });

                    const apiResponse = result.response;
                    const text = apiResponse.text();

                    if (text) {
                        const orionResponse = `Com base nas minhas capacidades, posso te dizer que: ${text}`;
                        speak(orionResponse, () => setProcessingState(false));
                    } else {
                        speak(`Desculpe, não consegui encontrar informações diretas sobre "${topic}".`, () => setProcessingState(false));
                    }
                } catch (error) {
                    console.error("Erro ao consultar o módulo de inteligência artificial:", error);
                    if (error.response && error.response.status === 429) {
                        speak("Muitas requisições. Por favor, espere um momento antes de fazer outra pergunta.", () => setProcessingState(false));
                        logEntry("ERRO", "Limite de requisições da API Gemini atingido (código 429).", "text-red-500");
                    } else if (error.response && error.response.prompt_feedback && error.response.prompt_feedback.block_reason) {
                        const blockReason = error.response.prompt_feedback.block_reason;
                        console.warn(`A requisição foi bloqueada por: ${blockReason}`);
                        let userFriendlyMessage = "Desculpe, sua solicitação foi bloqueada por conter material sensível. Por favor, tente reformular sua pergunta.";
                        if (blockReason === 'OTHER') {
                             userFriendlyMessage = "Desculpe, não consegui processar essa solicitação devido a uma política de segurança interna. Por favor, tente uma pergunta diferente.";
                        }
                        speak(userFriendlyMessage, () => setProcessingState(false));
                        logEntry("ALERTA", `Solicitação Gemini bloqueada: ${blockReason}`, "text-orange-400");
                    } else {
                        speak(`Desculpe, houve um erro ao buscar informações sobre "${topic}". Por favor, tente novamente ou pergunte de outra forma.`, () => setProcessingState(false));
                        logEntry("ERRO", `Erro na consulta Gemini: ${error.message || 'Erro desconhecido'}`, "text-red-500");
                    }
                }
            },
            tellTime: () => {
                try {
                    const time = new Date().toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' });
                    speak(`São ${time}.`, () => setProcessingState(false));
                } catch (e) {
                    console.error("Erro ao obter a hora:", e);
                    speak("Desculpe, não consegui determinar a hora no momento.", () => setProcessingState(false));
                }
                state.context.lastAction = 'tellTime';
            },
            tellDate: () => {
                try {
                    const date = new Date().toLocaleDateString('pt-BR', { weekday: 'long', day: 'numeric', month: 'long', year: 'numeric' });
                    speak(`Hoje é ${date}.`, () => setProcessingState(false));
                } catch (e) {
                    console.error("Erro ao obter a data:", e);
                    speak("Desculpe, não consegui determinar a data no momento.", () => setProcessingState(false));
                }
                state.context.lastAction = 'tellDate';
            },
            reportStatus: () => speakSequentially(["Diagnóstico do sistema em andamento.", "Verificando núcleo de conversação... operacional.", "Módulos de memória... online.", "Conexão neural... estável.", "Todos os sistemas funcionais. Eu estou ótima!"], () => { state.context.lastAction = 'reportStatus'; setProcessingState(false); }),
            tellJoke: () => {
                const jokes = [
                    "O que o zero disse para o oito? Belo cinto!",
                    "Por que o pinheiro não se perde na floresta? Porque ele tem uma pinha.",
                    "O que a impressora disse para a outra? Essa folha é sua ou é impressão minha?",
                    "Qual é o café favorito dos matemáticos? O café com leite, porque é integral!"
                ];
                speak(jokes[Math.floor(Math.random() * jokes.length)], () => setProcessingState(false));
                state.context.lastAction = 'tellJoke';
            },
            // REMOVIDA: getWeather: async () => { /* ... código da previsão do tempo ... */ },
            openSite: (site) => {
                if (!site || typeof site !== 'string' || site.trim() === '') {
                    speak("Qual site você gostaria de abrir?", () => setProcessingState(false));
                    return;
                }

                // URL para abrir site
                const url = site.startsWith('http://') || site.startsWith('https://') ? site : `https://${site}`;
                speak(`Entendido. Vou tentar abrir ${site} para você.`, () => {
                    window.open(url, '_blank');
                    setProcessingState(false);
                });
                state.context.lastAction = 'openSite';
            },
            calculate: (expression) => {
                if (!expression || typeof expression !== 'string' || expression.trim() === '') {
                    speak("Qual cálculo você gostaria de fazer?", () => setProcessingState(false));
                    return;
                }

                try {
                    const safeExpression = expression.replace(/[^-()\d/*+. ]/g, '');
                    const formattedExpression = safeExpression.replace(/([+\-*/])/g, ' $1 ');

                    const result = new Function('return ' + formattedExpression)();
                    
                    if (isNaN(result) || !isFinite(result)) {
                        throw new Error("Resultado de cálculo inválido.");
                    }
                    speak(`O resultado de ${expression.replace(/\*/g, 'vezes').replace(/\//g, 'dividido por')} é ${result}.`, () => setProcessingState(false));
                    state.context.lastAction = 'calculate';
                } catch (e) {
                    console.error("Erro no cálculo:", e);
                    speak(`Desculpe, não consegui calcular "${expression}". A expressão parece ser inválida ou muito complexa.`, () => setProcessingState(false));
                }
            },
            flipCoin: () => {
                const result = Math.random() < 0.5 ? "cara" : "coroa";
                speak(`Lancei a moeda virtual. Deu ${result}.`, () => setProcessingState(false));
                state.context.lastAction = 'flipCoin';
            },
            clearLog: () => {
                if (DOM.logContainer) DOM.logContainer.innerHTML = '';
                logEntry("SISTEMA", "Painel de interação limpo.", "text-yellow-400");
                setProcessingState(false);
                state.context.lastAction = 'clearLog';
            },
            listFunctions: () => {
                const funcs = [
                    "Eu sou Orion. Minhas capacidades incluem:",
                    "Realizar cálculos e responder a perguntas sobre diversos tópicos utilizando meu conhecimento interno e o módulo Gemini.",
                    "Posso te dizer as horas, a data e contar piadas.",
                    "Também posso guardar informações que você me passar.",
                    // REMOVIDA: "Posso informar a previsão do tempo para sua localização, se permitido.",
                    "E posso pesquisar vídeos no YouTube para você.",
                    "Para conversar, diga meu nome ou, se a chamada estiver ativa, apenas dê o comando. Para terminar, diga 'encerrar chamada'."
                ];
                speakSequentially(funcs, () => { state.context.lastAction = 'listFunctions'; setProcessingState(false); });
            },
            searchYouTube: (query) => {
                if (!query || typeof query !== 'string' || query.trim() === '') {
                    speak("O que você gostaria de pesquisar no YouTube?", () => setProcessingState(false));
                    return;
                }
                // URL de pesquisa do YouTube
                // Corrected Youtube URL:
                const youtubeUrl = `https://www.youtube.com/results?search_query=${encodeURIComponent(query)}`;
                speak(`Entendido. Vou abrir o YouTube e pesquisar por "${query}".`, () => {
                    window.open(youtubeUrl, '_blank');
                    setProcessingState(false);
                });
                state.context.lastAction = 'searchYouTube';
            }
        };

        function speak(text, onEndCallback = () => {}) {
            if (typeof text !== 'string' || text.trim() === '') {
                onEndCallback();
                return;
            }

            if (!speechSynthesis) {
                console.warn("SpeechSynthesis API não disponível. A fala será logada como texto.");
                logEntry('Orion (texto)', text, 'text-violet-400');
                onEndCallback();
                return;
            }

            const utterance = new SpeechSynthesisUtterance(text);
            utterance.lang = 'pt-BR';
            utterance.rate = 1.1;
            utterance.pitch = 1.0;

            utterance.onstart = () => {
                if (state.isListening && recognition && !speechSynthesis.speaking) {
                    try { recognition.stop(); } catch (e) { console.warn("Não foi possível parar o reconhecimento antes de falar:", e); }
                }
                updateCoreState('speaking');
            };

            utterance.onend = () => {
                // Só reinicia o reconhecimento se a chamada estiver ativa, não estiver ouvindo e Orion não estiver falando
                if (state.isCallActive && recognition && !state.isListening && !speechSynthesis.speaking) {
                    setTimeout(() => {
                        try {
                            if (!state.isListening) { // Verifica novamente antes de iniciar
                                recognition.start();
                                state.isListening = true; // Atualiza o estado
                            }
                        } catch(e){ console.error("Erro ao reiniciar reconhecimento após fala:", e); }
                    }, 100);
                } else if (!state.isLocked && !state.isCallActive && !speechSynthesis.speaking) {
                    updateCoreState('idle');
                }
                setProcessingState(false);
                onEndCallback();
            };

            utterance.onerror = (e) => {
                console.error("Erro SpeechSynthesisUtterance:", e);
                logEntry('Orion (erro de fala)', text, 'text-red-400');
                setProcessingState(false);
                onEndCallback();
            };

            logEntry('Orion', text, 'text-violet-400');
            try {
                speechSynthesis.speak(utterance);
            } catch (e) {
                console.error("Erro ao tentar usar speechSynthesis.speak:", e);
                logEntry('Orion (erro crítico de fala)', text, 'text-red-400');
                setProcessingState(false);
                onEndCallback();
            }
        }

        function speakSequentially(texts, onFinishAll = () => {}) {
            const remainingTexts = [...texts];

            const processNext = () => {
                if (remainingTexts.length === 0) {
                    onFinishAll();
                    return;
                }
                const textToSpeak = remainingTexts.shift();
                speak(textToSpeak, processNext);
            };

            processNext();
        }

        function setProcessingState(isProcessing) {
            if (isProcessing) {
                updateCoreState('processing');
            } else {
                if (!speechSynthesis.speaking) {
                    if (state.isCallActive) {
                        updateCoreState('listening');
                    } else if (!state.isLocked) {
                        updateCoreState('idle');
                    } else {
                        updateCoreState('locked');
                    }
                }
            }
        }

        function updateCoreState(newState) {
            if (!DOM.core) {
                console.warn("Elemento DOM 'orion-core' não encontrado para atualizar o estado.");
                return;
            }

            DOM.core.className = 'orion-core w-40 h-40 rounded-full flex items-center justify-center transition-all duration-300';
            DOM.core.classList.add(newState);

            DOM.core.style.boxShadow = '';
            DOM.core.style.backgroundColor = '';
            DOM.core.style.borderColor = 'transparent';

            if (DOM.micBtn) {
                if (newState === 'listening') {
                    DOM.micBtn.classList.add('animate-pulse');
                } else {
                    DOM.micBtn.classList.remove('animate-pulse');
                }
            }

            switch (newState) {
                case 'processing':
                    DOM.core.style.backgroundColor = 'rgba(255, 255, 0, 0.8)'; /* Neon Yellow */
                    DOM.core.style.boxShadow = 'var(--yellow-glow)';
                    DOM.core.style.borderColor = 'rgba(255, 255, 0, 0.9)';
                    break;
                case 'locked':
                    DOM.core.style.backgroundColor = 'rgba(255, 0, 0, 0.8)'; /* Neon Red */
                    DOM.core.style.boxShadow = 'var(--red-glow)';
                    DOM.core.style.borderColor = 'rgba(255, 0, 0, 0.9)';
                    break;
                case 'error':
                    DOM.core.style.backgroundColor = 'rgba(255, 0, 0, 0.8)'; /* Neon Red */
                    DOM.core.style.boxShadow = 'var(--red-glow)';
                    DOM.core.style.borderColor = 'rgba(255, 0, 0, 0.9)';
                    break;
                case 'success':
                    DOM.core.style.backgroundColor = 'rgba(0, 255, 0, 0.8)'; /* Neon Green */
                    DOM.core.style.boxShadow = 'var(--green-glow)';
                    DOM.core.style.borderColor = 'rgba(0, 255, 0, 0.9)';
                    break;
                case 'listening':
                    DOM.core.style.backgroundColor = 'rgba(170, 0, 255, 0.8)'; /* Neon Violet */
                    DOM.core.style.boxShadow = 'var(--violet-glow)';
                    DOM.core.style.borderColor = 'rgba(170, 0, 255, 0.9)';
                    break;
                case 'speaking':
                    DOM.core.style.backgroundColor = 'rgba(170, 0, 255, 0.8)'; /* Neon Violet */
                    DOM.core.style.boxShadow = 'var(--violet-glow)';
                    DOM.core.style.borderColor = 'rgba(170, 0, 255, 0.9)';
                    break;
                case 'idle':
                default:
                    DOM.core.style.backgroundColor = 'rgba(170, 0, 255, 0.8)'; /* Neon Violet */
                    DOM.core.style.boxShadow = 'var(--violet-glow)';
                    DOM.core.style.borderColor = 'rgba(170, 0, 255, 0.9)';
                    break;
            }
        }

        function logEntry(speaker, message, speakerColor) {
            if (!DOM.logContainer) {
                console.warn("Elemento DOM 'log-container' não encontrado. Não é possível registrar a entrada.");
                return;
            }

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
            DOM.logContainer.scrollTop = DOM.logContainer.scrollHeight; // Garante a rolagem para o final
        }

        function createStarfield() {
            const canvas = document.getElementById('starfield');
            if (!canvas) {
                console.warn("Canvas para starfield não encontrado.");
                return;
            }
            const ctx = canvas.getContext('2d');
            if (!ctx) {
                console.warn("Contexto 2D do canvas não pode ser obtido.");
                return;
            }

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
                if (!canvas || !ctx) return;
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

        function handleSignatureError() {
            if (DOM.loginScreen) {
                DOM.loginScreen.innerHTML = `
                    <div class="w-full max-w-sm p-8 space-y-6 bg-slate-800 rounded-xl shadow-2xl border border-red-500/30">
                        <h2 class="text-3xl font-bold text-center text-red-400 font-orbitron">ERRO DE INTEGRIDADE</h2>
                        <p class="text-gray-200 text-center">A assinatura do sistema Orion não foi verificada. Por favor, não remova ou altere a primeira linha de comentário do script.</p>
                    </div>
                `;
                DOM.loginScreen.style.display = 'flex';
            }
            // Further disable interactions
            if (DOM.micBtn) DOM.micBtn.disabled = true;
            if (DOM.chatInput) DOM.chatInput.disabled = true;
            if (DOM.chatSubmit) DOM.chatSubmit.disabled = true;
            logEntry("ERRO CRÍTICO", "Falha na verificação da assinatura do sistema. Funcionalidade limitada.", "text-red-600");
        }


        // Altera para chamar initialize como uma função assíncrona
        window.onload = () => initialize();

    </script>
</body>
</html>
