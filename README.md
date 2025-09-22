# AI-Crop-Disease
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>🌱 AI Crop Disease Detector</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700;800&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background: linear-gradient(135deg, #e0f2fe 0%, #d1fae5 100%);
            min-height: 100vh;
        }
        .container-card {
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }
        .container-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 15px 40px rgba(0, 0, 0, 0.15);
        }
        .prediction-card {
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            transition: transform 0.3s ease;
        }
        .prediction-card:hover {
            transform: translateY(-5px);
        }
        .custom-file-label {
            display: flex;
            align-items: center;
            justify-content: center;
            background: linear-gradient(45deg, #6366f1, #4f46e5);
            color: white;
            padding: 12px 24px;
            border-radius: 9999px;
            cursor: pointer;
            font-weight: 600;
            transition: transform 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
        }
        .custom-file-label:hover {
            transform: scale(1.05);
            box-shadow: 0 5px 15px rgba(79, 70, 229, 0.4);
        }
        .camera-label {
            background: linear-gradient(45deg, #10b981, #059669);
        }
        .camera-label:hover {
            box-shadow: 0 5px 15px rgba(5, 150, 105, 0.4);
        }
        .hidden-file-input {
            display: none;
        }
        #chatbot-container {
            position: fixed;
            bottom: 20px;
            right: 20px;
            z-index: 1000;
        }
        #chat-toggle-btn {
            background: #4f46e5;
            color: white;
            width: 60px;
            height: 60px;
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            cursor: pointer;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
            transition: transform 0.2s ease-in-out;
            font-size: 24px;
        }
        #chat-toggle-btn:hover {
            transform: scale(1.1);
        }
        #chat-window {
            background: white;
            border-radius: 12px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
            width: 350px;
            height: 450px;
            display: flex;
            flex-direction: column;
            overflow: hidden;
            transform: scale(0);
            transform-origin: bottom right;
            transition: transform 0.3s ease-in-out;
            position: absolute;
            bottom: 70px;
            right: 0;
        }
        #chat-window.open {
            transform: scale(1);
        }
        #chat-header {
            background: linear-gradient(90deg, #4f46e5, #818cf8);
            color: white;
            padding: 1rem;
            font-weight: 600;
            text-align: center;
        }
        #chat-history {
            flex-grow: 1;
            padding: 1rem;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            gap: 10px;
        }
        .chat-message {
            max-width: 80%;
            padding: 8px 12px;
            border-radius: 10px;
        }
        .user-message {
            background-color: #e2e8f0;
            align-self: flex-end;
            word-wrap: break-word;
        }
        .bot-message {
            background-color: #4f46e5;
            color: white;
            align-self: flex-start;
            word-wrap: break-word;
        }
        #chat-input-container {
            display: flex;
            padding: 1rem;
            border-top: 1px solid #e2e8f0;
        }
        #chat-input {
            flex-grow: 1;
            padding: 8px;
            border-radius: 20px;
            border: 1px solid #cbd5e1;
            outline: none;
            transition: border-color 0.2s;
        }
        #chat-input:focus {
            border-color: #4f46e5;
        }
        #chat-input-btn {
            background: #4f46e5;
            color: white;
            border: none;
            border-radius: 50%;
            width: 36px;
            height: 36px;
            display: flex;
            justify-content: center;
            align-items: center;
            margin-left: 8px;
            cursor: pointer;
        }
        #scan-history-container {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
            gap: 16px;
        }
        .scan-history-item {
            border-radius: 12px;
            overflow: hidden;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
            transition: transform 0.2s;
            cursor: pointer;
        }
        .scan-history-item:hover {
            transform: translateY(-3px);
        }
        .scan-history-item img {
            width: 100%;
            height: 120px;
            object-fit: cover;
        }
        .scan-history-item p {
            padding: 8px;
            font-size: 0.8rem;
            text-align: center;
            font-weight: 600;
        }
        #message-box {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: #fff;
            padding: 2rem;
            border-radius: 1rem;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
            z-index: 2000;
            max-width: 400px;
            text-align: center;
            display: none;
        }
        #message-box-backdrop {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.5);
            z-index: 1999;
            display: none;
        }
    </style>
</head>
<body class="p-4 md:p-8">
    <main class="max-w-4xl mx-auto bg-white p-6 md:p-10 rounded-3xl container-card">
        <!-- User ID display -->
        <div class="flex justify-end mb-4 text-xs sm:text-sm text-gray-500">
            User ID: <span id="user-id" class="ml-2 font-mono">Loading...</span>
        </div>
        
        <!-- Language Toggle -->
        <div class="flex justify-end mb-4">
            <button id="lang-toggle" class="bg-gray-200 text-gray-800 px-4 py-2 rounded-full text-sm font-semibold hover:bg-gray-300 transition-colors duration-200">
                Switch to Hindi
            </button>
        </div>

        <!-- Title and Description -->
        <header class="text-center mb-8">
            <h1 id="main-title" class="text-3xl sm:text-4xl md:text-5xl font-extrabold text-gray-800 tracking-tight">
                🌱 AI Crop Disease Detector
            </h1>
            <p id="description" class="mt-4 text-base sm:text-lg md:text-xl text-gray-600 font-medium leading-relaxed">
                Detect crop diseases, nutrient deficiencies, and pests from leaf images instantly!
            </p>
        </header>

        <!-- Image Input Section -->
        <section class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-4">
            <!-- Plant Selection Dropdown -->
            <div class="md:col-span-2 flex flex-col items-center">
                <label for="plant-select" id="plant-select-label" class="mb-2 text-gray-700 font-semibold text-lg">
                    1. Select Your Crop
                </label>
                <select id="plant-select" class="w-full max-w-sm p-3 rounded-xl border-2 border-gray-300 focus:border-indigo-500 focus:ring-2 focus:ring-indigo-500 transition-colors duration-200 cursor-pointer">
                    <option value="">-- Please Select a Crop --</option>
                    <option value="Tomato">Tomato</option>
                    <option value="Potato">Potato</option>
                    <option value="Corn">Corn</option>
                    <option value="Grape">Grape</option>
                    <option value="Apple">Apple</option>
                </select>
            </div>
            
            <div class="flex flex-col items-center">
                <label for="file-upload" class="custom-file-label">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-8l-4-4m0 0L8 8m4-4v12" />
                    </svg>
                    <span>Upload an Image</span>
                </label>
                <input type="file" id="file-upload" accept="image/*" class="hidden-file-input">
            </div>
            <div class="flex flex-col items-center">
                <label for="camera-upload" class="custom-file-label camera-label">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 9a2 2 0 012-2h.93a2 2 0 001.664-.89l.812-1.218A2 2 0 0110.866 4h2.268a2 2 0 011.664.89l.812 1.218A2 2 0 0018.07 7H19a2 2 0 012 2v9a2 2 0 01-2 2H5a2 2 0 01-2-2V9z" />
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 13a3 3 0 11-6 0 3 3 0 016 0z" />
                    </svg>
                    <span>Take a Live Photo</span>
                </label>
                <input type="file" id="camera-upload" accept="image/*" capture="camera" class="hidden-file-input">
            </div>
        </section>
        
        <!-- Status Message -->
        <p id="status-message" class="text-center font-semibold text-gray-600 my-4 text-lg"></p>

        <!-- Image and Prediction Section -->
        <section id="result-container" class="hidden">
            <div class="text-center mb-6">
                <img id="leaf-image" class="w-full h-auto max-h-96 object-contain rounded-2xl border-4 border-gray-200 shadow-lg" alt="Leaf Image">
                <p id="image-caption" class="text-sm text-gray-500 mt-2">Your uploaded leaf image</p>
            </div>
            
            <div id="loading-spinner" class="hidden text-center mt-6">
                <div class="animate-spin inline-block w-10 h-10 border-4 border-blue-500 border-t-transparent rounded-full"></div>
                <p id="loading-text" class="mt-2 text-gray-600 text-lg">Analyzing image...</p>
            </div>

            <!-- Prediction Card -->
            <div id="prediction-card" class="prediction-card p-6 rounded-2xl mt-6 hidden border-l-4">
                <h2 id="problem-title" class="text-2xl font-bold"></h2>
                <div id="remedy-details" class="mt-4 text-base md:text-lg font-normal space-y-4"></div>
            </div>
        </section>

        <!-- Scan History Section -->
        <hr class="my-10 border-gray-300">
        <section class="scan-history-section">
            <h2 id="history-heading" class="text-2xl font-bold text-gray-800 mb-4 text-center">⏱ Your Scan History</h2>
            <div id="scan-history-container" class="mt-4">
                <!-- Scan history items will be dynamically added here -->
            </div>
        </section>

        <!-- Tips Section -->
        <hr class="my-10 border-gray-300">
        <section class="tips-section text-center">
            <h2 id="tips-heading" class="text-2xl font-bold text-gray-800 mb-4">🌿 Tips for Healthy Crops</h2>
            <ul class="text-left max-w-lg mx-auto list-disc list-inside space-y-3 text-gray-700 text-sm sm:text-base">
                <li id="tip-1"><span class="font-bold text-green-700">💧 Proper watering</span> is key to preventing stress and disease.</li>
                <li id="tip-2"><span class="font-bold text-green-700">🧹 Timely removal of infected leaves</span> stops the spread of issues.</li>
                <li id="tip-3"><span class="font-bold text-green-700">🔬 Regularly inspect crops</span> for early signs of problems.</li>
                <li id="tip-4"><span class="font-bold text-green-700">🧪 Maintain balanced soil nutrients</span> to boost plant immunity.</li>
            </ul>
        </section>
    </main>
    
    <!-- Chatbot Container -->
    <div id="chatbot-container">
        <div id="chat-window">
            <div id="chat-header">Chat with the Crop Bot</div>
            <div id="chat-history"></div>
            <div id="chat-input-container">
                <input type="text" id="chat-input" placeholder="Type your message..." class="bg-gray-100">
                <button id="chat-input-btn">➤</button>
            </div>
        </div>
        <button id="chat-toggle-btn">💬</button>
    </div>

    <!-- Custom Message Box -->
    <div id="message-box-backdrop"></div>
    <div id="message-box" class="flex flex-col items-center">
        <p id="message-box-text" class="text-lg font-medium mb-4">Please select a plant first!</p>
        <button id="message-box-ok" class="bg-indigo-500 text-white px-6 py-2 rounded-full font-semibold hover:bg-indigo-600 transition-colors duration-200">OK</button>
    </div>

    <script type="module">
        // Firebase Libraries
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, getDoc, addDoc, setDoc, updateDoc, deleteDoc, onSnapshot, collection, query, where, getDocs } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Global variables provided by the Canvas environment.
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : {};
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        let db, auth, userId;
        
        // =========================================================================
        // IMPORTANT: TO FIX THE API ERROR, PASTE YOUR API KEY HERE:
        // =========================================================================
        const API_KEY = "AIzaSyD50RiEfoDpN2EUuHuZg5dsIVY-qg8GUJE";
        // =========================================================================

        document.addEventListener('DOMContentLoaded', async () => {
            try {
                // Initialize Firebase
                const app = initializeApp(firebaseConfig);
                db = getFirestore(app);
                auth = getAuth(app);
                
                // Authenticate the user
                if (initialAuthToken) {
                    await signInWithCustomToken(auth, initialAuthToken);
                } else {
                    await signInAnonymously(auth);
                }

                // Set up auth state listener
                onAuthStateChanged(auth, async (user) => {
                    if (user) {
                        userId = user.uid;
                        document.getElementById('user-id').textContent = userId;
                        console.log("User authenticated, loading scan history...");
                        loadScanHistory();
                    } else {
                        console.log("User is signed out or not authenticated.");
                    }
                });

            } catch (error) {
                console.error("Firebase initialization failed:", error);
                document.getElementById('user-id').textContent = "Auth Error";
            }

            const fileUpload = document.getElementById('file-upload');
            const cameraUpload = document.getElementById('camera-upload');
            const leafImage = document.getElementById('leaf-image');
            const resultContainer = document.getElementById('result-container');
            const loadingSpinner = document.getElementById('loading-spinner');
            const predictionCard = document.getElementById('prediction-card');
            const problemTitle = document.getElementById('problem-title');
            const remedyDetails = document.getElementById('remedy-details');
            const langToggle = document.getElementById('lang-toggle');
            const scanHistoryContainer = document.getElementById('scan-history-container');
            const statusMessage = document.getElementById('status-message');
            const plantSelect = document.getElementById('plant-select');
            
            // Chatbot elements
            const chatToggleBtn = document.getElementById('chat-toggle-btn');
            const chatWindow = document.getElementById('chat-window');
            const chatHistory = document.getElementById('chat-history');
            const chatInput = document.getElementById('chat-input');
            const chatInputBtn = document.getElementById('chat-input-btn');

            // Message box elements
            const messageBox = document.getElementById('message-box');
            const messageBoxBackdrop = document.getElementById('message-box-backdrop');
            const messageBoxText = document.getElementById('message-box-text');
            const messageBoxOk = document.getElementById('message-box-ok');

            let currentLang = 'en';

            const translations = {
                en: {
                    title: "🌱 AI Crop Disease Detector",
                    description: "Detect crop diseases, nutrient deficiencies, and pests from leaf images instantly!",
                    selectPlant: "1. Select Your Crop",
                    placeholderPlant: "-- Please Select a Crop --",
                    uploadBtn: "Upload an Image",
                    cameraBtn: "Take a Live Photo",
                    imageCaption: "Your uploaded leaf image",
                    loadingText: "Analyzing image...",
                    tipsHeading: "🌿 Tips for Healthy Crops",
                    historyHeading: "⏱ Your Scan History",
                    tip1: "💧 Proper watering is key to preventing stress and disease.",
                    tip2: "🧹 Timely removal of infected leaves stops the spread of issues.",
                    tip3: "🔬 Regularly inspect crops for early signs of problems.",
                    tip4: "🧪 Maintain balanced soil nutrients to boost plant immunity.",
                    confidence: "Confidence:",
                    toggleBtn: "Switch to Hindi",
                    symptoms: "Symptoms:",
                    causes: "Causes:",
                    actions: "Actionable Insights:",
                    messageBoxTitle: "Please select a plant first!",
                    noImage: "No image selected. Please try again.",
                    apiError: "An error occurred. Please try a different image or check your internet connection.",
                    apiKeyMissing: "API key is missing. Please enter your key from Google AI Studio to use this feature.",
                    thinking: "Crop Bot is thinking...",
                    noSources: "No sources found."
                },
                hi: {
                    title: "🌱 एआई फसल रोग डिटेक्टर",
                    description: "पत्ती की तस्वीरों से तुरंत फसल के रोग, पोषक तत्वों की कमी और कीटों का पता लगाएं!",
                    selectPlant: "1. अपनी फसल चुनें",
                    placeholderPlant: "-- कृपया एक फसल चुनें --",
                    uploadBtn: "एक तस्वीर अपलोड करें",
                    cameraBtn: "एक लाइव फोटो लें",
                    imageCaption: "आपकी अपलोड की गई पत्ती की तस्वीर",
                    loadingText: "तस्वीर का विश्लेषण हो रहा है...",
                    tipsHeading: "🌿 स्वस्थ फसलों के लिए सुझाव",
                    historyHeading: "⏱ आपकी स्कैन हिस्ट्री",
                    tip1: "💧 पानी का उचित प्रबंधन तनाव और बीमारी से बचाव के लिए महत्वपूर्ण है।",
                    tip2: "🧹 संक्रमित पत्तियों को समय पर हटाने से समस्याओं का प्रसार रुकता है।",
                    tip3: "🔬 समस्याओं के शुरुआती लक्षणों के लिए फसलों का नियमित निरीक्षण करें।",
                    tip4: "🧪 पौधों की रोग प्रतिरोधक क्षमता बढ़ाने के लिए मिट्टी में संतुलित पोषक तत्व बनाए रखें।",
                    confidence: "कॉन्फिडेंस:",
                    toggleBtn: "Switch to English",
                    symptoms: "लक्षण:",
                    causes: "कारण:",
                    actions: "उपयोगी सलाह:",
                    messageBoxTitle: "कृपया पहले एक फसल चुनें!",
                    noImage: "कोई तस्वीर नहीं चुनी गई है। कृपया पुनः प्रयास करें।",
                    apiError: "एक त्रुटि हुई। कृपया एक अलग तस्वीर का प्रयास करें या अपना इंटरनेट कनेक्शन जांचें।",
                    apiKeyMissing: "एपीआई कुंजी गुम है। इस सुविधा का उपयोग करने के लिए कृपया Google AI Studio से अपनी कुंजी दर्ज करें।",
                    thinking: "क्रॉप बॉट सोच रहा है...",
                    noSources: "कोई स्रोत नहीं मिला।"
                }
            };

            function showMessage(message) {
                messageBoxText.textContent = message;
                messageBox.style.display = 'flex';
                messageBoxBackdrop.style.display = 'block';
            }

            function hideMessage() {
                messageBox.style.display = 'none';
                messageBoxBackdrop.style.display = 'none';
            }

            messageBoxOk.addEventListener('click', hideMessage);

            function updateUI(lang) {
                document.getElementById('main-title').textContent = translations[lang].title;
                document.getElementById('description').textContent = translations[lang].description;
                document.getElementById('plant-select-label').textContent = translations[lang].selectPlant;
                document.querySelector('#plant-select option[value=""]').textContent = translations[lang].placeholderPlant;
                document.querySelector('#file-upload + .custom-file-label span').textContent = translations[lang].uploadBtn;
                document.querySelector('#camera-upload + .custom-file-label span').textContent = translations[lang].cameraBtn;
                document.getElementById('image-caption').textContent = translations[lang].imageCaption;
                document.getElementById('loading-text').textContent = translations[lang].loadingText;
                document.getElementById('tips-heading').textContent = translations[lang].tipsHeading;
                document.getElementById('history-heading').textContent = translations[lang].historyHeading;
                document.getElementById('tip-1').innerHTML = `<span class="font-bold text-green-700">💧</span> ${translations[lang].tip1}`;
                document.getElementById('tip-2').innerHTML = `<span class="font-bold text-green-700">🧹</span> ${translations[lang].tip2}`;
                document.getElementById('tip-3').innerHTML = `<span class="font-bold text-green-700">🔬</span> ${translations[lang].tip3}`;
                document.getElementById('tip-4').innerHTML = `<span class="font-bold text-green-700">🧪</span> ${translations[lang].tip4}`;
                langToggle.textContent = translations[lang].toggleBtn;
                document.getElementById('message-box-text').textContent = translations[lang].messageBoxTitle;
            }

            langToggle.addEventListener('click', () => {
                currentLang = currentLang === 'en' ? 'hi' : 'en';
                updateUI(currentLang);
            });

            function getBase64Image(file) {
                return new Promise((resolve, reject) => {
                    const reader = new FileReader();
                    reader.onload = () => resolve(reader.result.split(',')[1]);
                    reader.onerror = error => reject(error);
                    reader.readAsDataURL(file);
                });
            }

            async function handleFile(file) {
                const selectedPlant = plantSelect.value;
                if (!selectedPlant) {
                    showMessage(translations[currentLang].messageBoxTitle);
                    return;
                }
                const statusMessage = document.getElementById('status-message');
                if (!file) {
                    statusMessage.textContent = translations[currentLang].noImage;
                    return;
                }
                
                statusMessage.textContent = 'File selected. Analyzing...';
                
                try {
                    const imageDataUrl = URL.createObjectURL(file);
                    leafImage.src = imageDataUrl;
                    resultContainer.classList.remove('hidden');
                    predictionCard.classList.add('hidden');
                    loadingSpinner.classList.remove('hidden');

                    const base64Image = await getBase64Image(file);
                    const prediction = await predictProblem(base64Image, selectedPlant);

                    loadingSpinner.classList.add('hidden');
                    statusMessage.textContent = '';

                    if (prediction.error) {
                        showMessage(prediction.error);
                    } else {
                        predictionCard.classList.remove('hidden');
                        displayPrediction(prediction);
                        if (userId) {
                            await saveScanToFirestore(imageDataUrl, prediction, selectedPlant);
                        } else {
                            console.warn("User not authenticated. Scan will not be saved.");
                        }
                    }
                } catch (error) {
                    console.error("Error during file processing or prediction:", error);
                    statusMessage.textContent = translations[currentLang].apiError;
                    loadingSpinner.classList.add('hidden');
                }
            }

            fileUpload.addEventListener('change', (event) => handleFile(event.target.files[0]));
            cameraUpload.addEventListener('change', (event) => handleFile(event.target.files[0]));
            
            async function predictProblem(base64Image, plantType, retries = 0) {
                if (!API_KEY || API_KEY === "YOUR_API_KEY_HERE") {
                    return { error: translations[currentLang].apiKeyMissing };
                }

                const prompt = `Analyze the provided image of a ${plantType} plant leaf and identify any diseases, nutrient deficiencies, or pests.
                Provide a JSON response with the following structure:
                {
                    "diagnosis": "Name of the problem (e.g., Early Blight, Healthy, Nitrogen Deficiency)",
                    "symptoms": "A detailed, comma-separated list of symptoms visible on the leaf.",
                    "causes": "A detailed, comma-separated list of the primary causes of the problem.",
                    "remedy": "A detailed, comma-separated list of specific, actionable steps to treat the problem."
                }
                Make sure the response is a single, valid JSON object and nothing else. If you cannot make a clear diagnosis, state "Unknown" in all fields.`;

                const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${API_KEY}`;

                const payload = {
                    contents: [
                        {
                            parts: [
                                { text: prompt },
                                { inlineData: { mimeType: "image/png", data: base64Image } }
                            ]
                        }
                    ],
                    generationConfig: {
                        responseMimeType: "application/json"
                    }
                };
                
                try {
                    const response = await fetch(apiUrl, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(payload)
                    });
                    
                    if (!response.ok) {
                        if (response.status === 429 && retries < 5) {
                            const delay = Math.pow(2, retries) * 1000;
                            console.warn(`Rate limit exceeded. Retrying in ${delay}ms...`);
                            await new Promise(resolve => setTimeout(resolve, delay));
                            return predictProblem(base64Image, plantType, retries + 1);
                        } else {
                            throw new Error(`API call failed with status: ${response.status}`);
                        }
                    }

                    const result = await response.json();
                    const jsonString = result.candidates?.[0]?.content?.parts?.[0]?.text;
                    if (!jsonString) {
                        throw new Error("Invalid API response format or no content.");
                    }
                    const parsedJson = JSON.parse(jsonString);
                    return parsedJson;

                } catch (error) {
                    console.error("Error calling Gemini API:", error);
                    return { diagnosis: "Error", symptoms: "An error occurred during analysis.", causes: "API communication error.", remedy: "Please try again later." };
                }
            }

            function displayPrediction(prediction) {
                let cardColor, titleColor, borderColor;
                const diagnosisLower = prediction.diagnosis.toLowerCase();
                
                if (diagnosisLower.includes("healthy")) {
                    cardColor = "#d4edda"; 
                    titleColor = "#155724";
                    borderColor = "#28a745";
                } else if (diagnosisLower.includes("deficiency") || diagnosisLower.includes("nutrition")) {
                    cardColor = "#fff3cd"; 
                    titleColor = "#856404";
                    borderColor = "#ffc107";
                } else if (diagnosisLower.includes("pest") || diagnosisLower.includes("insect")) {
                    cardColor = "#d1ecf1"; 
                    titleColor = "#0c5460";
                    borderColor = "#17a2b8";
                } else {
                    cardColor = "#f8d7da"; 
                    titleColor = "#721c24";
                    borderColor = "#dc3545";
                }

                predictionCard.style.backgroundColor = cardColor;
                predictionCard.style.borderColor = borderColor;
                problemTitle.style.color = titleColor;
                problemTitle.textContent = prediction.diagnosis;
                
                // Helper function to create bullet points from a comma-separated string
                const createBulletPoints = (text) => {
                    if (!text || text.toLowerCase().includes('unknown')) {
                        return `<p>${text}</p>`;
                    }
                    const items = text.split(',').map(item => item.trim()).filter(item => item.length > 0);
                    return `<ul class="list-disc list-inside space-y-1">${items.map(item => `<li>${item}</li>`).join('')}</ul>`;
                };

                const detailsHTML = `
                    <div class="mb-4">
                        <p class="font-bold text-gray-800">${translations[currentLang].symptoms}</p>
                        ${createBulletPoints(prediction.symptoms)}
                    </div>
                    <div class="mb-4">
                        <p class="font-bold text-gray-800">${translations[currentLang].causes}</p>
                        ${createBulletPoints(prediction.causes)}
                    </div>
                    <div>
                        <p class="font-bold text-gray-800">${translations[currentLang].actions}</p>
                        ${createBulletPoints(prediction.remedy)}
                    </div>
                `;
                remedyDetails.innerHTML = detailsHTML;
            }

            async function saveScanToFirestore(imageDataUrl, prediction, plantType) {
                if (!userId) return;
                try {
                    const scansRef = collection(db, `artifacts/${appId}/users/${userId}/scans`);
                    const newScan = {
                        imageDataUrl: imageDataUrl,
                        prediction: prediction,
                        plantType: plantType,
                        timestamp: new Date().toISOString()
                    };
                    await addDoc(scansRef, newScan);
                    console.log("Scan saved successfully!");
                } catch (e) {
                    console.error("Error adding document: ", e);
                }
            }

            function loadScanHistory() {
                if (!userId) {
                    console.log("UserID is not available yet. Skipping history load.");
                    return;
                }
                const scansRef = collection(db, `artifacts/${appId}/users/${userId}/scans`);
                const q = query(scansRef);

                onSnapshot(q, (snapshot) => {
                    console.log("Received a new snapshot for scan history.");
                    scanHistoryContainer.innerHTML = '';
                    snapshot.forEach((doc) => {
                        const scan = doc.data();
                        const scanItem = document.createElement('div');
                        scanItem.classList.add('scan-history-item', 'bg-white', 'rounded-xl', 'shadow');
                        scanItem.innerHTML = `
                            <img src="${scan.imageDataUrl}" alt="Scanned Leaf">
                            <p>${scan.plantType}: ${scan.prediction.diagnosis}</p>
                        `;
                        scanItem.addEventListener('click', () => {
                            leafImage.src = scan.imageDataUrl;
                            resultContainer.classList.remove('hidden');
                            displayPrediction(scan.prediction);
                        });
                        scanHistoryContainer.appendChild(scanItem);
                    });
                });
            }
            
            // Chatbot functionality
            chatToggleBtn.addEventListener('click', () => {
                chatWindow.classList.toggle('open');
                if (chatWindow.classList.contains('open')) {
                    addMessageToChat('Hello! I am the Crop Bot. I can help answer some of your questions about the app and crop health.', false);
                    chatInput.focus();
                }
            });

            function addMessageToChat(message, isUser) {
                const messageDiv = document.createElement('div');
                messageDiv.classList.add('chat-message');
                messageDiv.classList.add(isUser ? 'user-message' : 'bot-message');
                messageDiv.textContent = message;
                chatHistory.appendChild(messageDiv);
                chatHistory.scrollTop = chatHistory.scrollHeight;
            }
            
            // Basic exponential backoff for API calls
            function sleep(ms) {
                return new Promise(resolve => setTimeout(resolve, ms));
            }
            
            async function callGeminiApi(prompt, retries = 0) {
                if (!API_KEY || API_KEY === "YOUR_API_KEY_HERE") {
                    return { text: translations[currentLang].apiKeyMissing, sources: [] };
                }
                
                const systemPrompt = `You are a helpful chatbot designed to provide information about the "AI Crop Disease Detector" application. Provide concise and helpful answers. If the user asks about crop diseases, nutrient deficiencies, or remedies, use the latest information available to you.`;
                const userQuery = prompt;
                const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${API_KEY}`;

                const payload = {
                    contents: [{ parts: [{ text: userQuery }] }],
                    tools: [{ "google_search": {} }],
                    systemInstruction: {
                        parts: [{ text: systemPrompt }]
                    },
                };
                
                try {
                    const response = await fetch(apiUrl, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(payload)
                    });
                    
                    if (!response.ok) {
                        if (response.status === 429 && retries < 5) {
                            const delay = Math.pow(2, retries) * 1000;
                            console.warn(`Rate limit exceeded. Retrying in ${delay}ms...`);
                            await sleep(delay);
                            return callGeminiApi(prompt, retries + 1);
                        } else {
                            throw new Error(`API call failed with status: ${response.status}`);
                        }
                    }

                    const result = await response.json();
                    const candidate = result.candidates?.[0];

                    if (candidate && candidate.content?.parts?.[0]?.text) {
                        const text = candidate.content.parts[0].text;
                        const groundingMetadata = candidate.groundingMetadata;
                        let sources = [];
                        if (groundingMetadata && groundingMetadata.groundingAttributions) {
                            sources = groundingMetadata.groundingAttributions
                                .map(attribution => ({
                                    uri: attribution.web?.uri,
                                    title: attribution.web?.title,
                                }))
                                .filter(source => source.uri && source.title);
                        }
                        return { text, sources };
                    } else {
                        throw new Error("Invalid API response structure.");
                    }
                } catch (error) {
                    console.error("Error calling Gemini API:", error);
                    return { text: "I'm sorry, I'm having trouble connecting to my service right now. Please try again later.", sources: [] };
                }
            }

            async function handleChatInput() {
                const userMessage = chatInput.value.trim();
                if (userMessage === '') return;
                
                addMessageToChat(userMessage, true);
                chatInput.value = '';
                
                const thinkingMessage = document.createElement('div');
                thinkingMessage.id = 'bot-thinking';
                thinkingMessage.classList.add('chat-message', 'bot-message', 'italic', 'text-sm');
                thinkingMessage.textContent = translations[currentLang].thinking;
                chatHistory.appendChild(thinkingMessage);
                chatHistory.scrollTop = chatHistory.scrollHeight;

                const response = await callGeminiApi(userMessage);

                const thinkingEl = document.getElementById('bot-thinking');
                if (thinkingEl) {
                    thinkingEl.remove();
                }

                addMessageToChat(response.text, false);
                if (response.sources.length > 0) {
                    let sourcesHtml = '<strong>Sources:</strong><ul>';
                    response.sources.forEach(source => {
                        sourcesHtml += `<li><a href="${source.uri}" target="_blank" class="text-white underline">${source.title}</a></li>`;
                    });
                    sourcesHtml += '</ul>';
                    const sourcesDiv = document.createElement('div');
                    sourcesDiv.classList.add('chat-message', 'bot-message', 'text-xs');
                    sourcesDiv.innerHTML = sourcesHtml;
                    chatHistory.appendChild(sourcesDiv);
                    chatHistory.scrollTop = chatHistory.scrollHeight;
                } else {
                    const noSourcesDiv = document.createElement('div');
                    noSourcesDiv.classList.add('chat-message', 'bot-message', 'text-xs', 'italic');
                    noSourcesDiv.textContent = translations[currentLang].noSources;
                    chatHistory.appendChild(noSourcesDiv);
                    chatHistory.scrollTop = chatHistory.scrollHeight;
                }
            }

            chatInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    handleChatInput();
                }
            });

            chatInputBtn.addEventListener('click', () => {
                handleChatInput();
            });
        });
    </script>
</body>
</html>
