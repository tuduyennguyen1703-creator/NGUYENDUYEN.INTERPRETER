<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Interpreting Flashcards (Full IPA + Audio + Settings)</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>

    <style>
        /* CẤU HÌNH 3D LẬT THẺ */
        .perspective-1000 {
            perspective: 1000px;
        }
        .transform-style-3d {
            transform-style: preserve-3d;
            -webkit-transform-style: preserve-3d;
        }
        .backface-hidden {
            -webkit-backface-visibility: hidden;
            backface-visibility: hidden;
            /* Fix lỗi hiển thị trên một số màn hình */
            -webkit-transform: translate3d(0,0,0);
            transform: translate3d(0,0,0);
        }
        .rotate-y-180 {
            transform: rotateY(180deg);
        }
        
        .card-inner {
            transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1);
        }

        /* Thanh cuộn mượt */
        .custom-scrollbar::-webkit-scrollbar {
            width: 6px;
        }
        .custom-scrollbar::-webkit-scrollbar-track {
            background: rgba(0,0,0,0.05);
            border-radius: 4px;
        }
        .custom-scrollbar::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 4px;
        }
        
        .font-ipa {
            font-family: "Lucida Sans Unicode", "Arial Unicode MS", "Times New Roman", serif;
        }

        /* Loại bỏ overscroll-behavior chặn cuộn trên body để tránh kẹt trên mobile */
        body {
            -webkit-tap-highlight-color: transparent;
        }

        /* Hiệu ứng nút */
        .btn-active:active {
            transform: scale(0.95);
        }
        
        /* Modal Animation */
        .modal-enter {
            opacity: 0;
            transform: scale(0.9);
        }
        .modal-enter-active {
            opacity: 1;
            transform: scale(1);
            transition: opacity 0.2s, transform 0.2s;
        }
    </style>
</head>
<body class="bg-slate-100 text-slate-800 font-sans min-h-screen flex flex-col items-center py-4 px-3 sm:py-6 sm:px-4 relative">

    <!-- Header & Settings Button -->
    <header class="mb-4 sm:mb-6 text-center w-full max-w-2xl relative">
        <div class="flex items-center justify-center gap-2 mb-2">
            <svg xmlns="http://www.w3.org/2000/svg" class="w-6 h-6 sm:w-8 sm:h-8 text-blue-600" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M22 10v6M2 10l10-5 10 5-10 5z"/><path d="M6 12v5c3 0 6-3 6-3s-3-3-6-3"/></svg>
            <h1 class="text-xl sm:text-3xl font-bold text-gray-800">Interpreting Review</h1>
        </div>
        <p class="text-xs sm:text-base text-gray-500">Subject: Interpreting & Translation Theory</p>
        
        <!-- Nút Cài đặt (Gear Icon) -->
        <button onclick="toggleSettings()" class="absolute right-0 top-0 p-2 text-gray-400 hover:text-blue-600 hover:bg-blue-50 rounded-full transition-colors btn-active" title="Cài đặt giọng đọc">
            <svg xmlns="http://www.w3.org/2000/svg" class="w-6 h-6" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M12.22 2h-.44a2 2 0 0 0-2 2v.18a2 2 0 0 1-1 1.73l-.43.25a2 2 0 0 1-2 0l-.15-.08a2 2 0 0 0-2.73.73l-.22.38a2 2 0 0 0 .73 2.73l.15.1a2 2 0 0 1 1 1.72v.51a2 2 0 0 1-1 1.74l-.15.09a2 2 0 0 0-.73 2.73l.22.38a2 2 0 0 0 2.73.73l.15-.08a2 2 0 0 1 2 0l.43.25a2 2 0 0 1 1 1.73V20a2 2 0 0 0 2 2h.44a2 2 0 0 0 2-2v-.18a2 2 0 0 1 1-1.73l.43-.25a2 2 0 0 1 2 0l.15.08a2 2 0 0 0 2.73-.73l.22-.39a2 2 0 0 0-.73-2.73l-.15-.1a2 2 0 0 1-1-1.74v-.47a2 2 0 0 1 1-1.74l.15-.09a2 2 0 0 0 .73-2.73l-.22-.35a2 2 0 0 0-2.73-.73l-.15.08a2 2 0 0 1-2 0l-.43-.25a2 2 0 0 1-1-1.73V4a2 2 0 0 0-2-2z"/><circle cx="12" cy="12" r="3"/></svg>
        </button>
    </header>

    <!-- Progress Info -->
    <div class="w-full max-w-2xl mb-2 flex justify-between items-center text-xs sm:text-sm font-medium text-gray-600 px-1">
        <span id="progress-text">Question 1 of 10</span>
        <div class="flex items-center gap-1">
            <svg xmlns="http://www.w3.org/2000/svg" class="w-4 h-4 text-blue-500" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M2 3h6a4 4 0 0 1 4 4v14a3 3 0 0 0-3-3H2z"/><path d="M22 3h-6a4 4 0 0 0-4 4v14a3 3 0 0 1 3-3h7z"/></svg>
            <span>Study Mode</span>
        </div>
    </div>
    
    <!-- Progress Bar -->
    <div class="w-full max-w-2xl bg-gray-200 h-2 rounded-full mb-4 sm:mb-8 overflow-hidden shadow-inner">
        <div id="progress-bar" class="bg-blue-600 h-full transition-all duration-300 ease-out rounded-full" style="width: 10%"></div>
    </div>

    <!-- Main Card Container -->
    <div class="relative w-full max-w-2xl h-[70vh] sm:h-[600px] perspective-1000 group">
        <!-- Card Inner -->
        <div id="card-inner" class="card-inner relative w-full h-full transform-style-3d shadow-xl rounded-2xl bg-white">
            
            <!-- FRONT FACE (Mặt trước - Câu hỏi) -->
            <!-- Thêm id="front-face" để JS điều khiển pointer-events -->
            <div id="front-face" class="absolute w-full h-full backface-hidden bg-white rounded-2xl p-6 sm:p-10 flex flex-col items-center justify-center border border-gray-200 text-center z-20 cursor-pointer" onclick="flipCard()">
                <span class="absolute top-4 left-4 sm:top-6 sm:left-6 text-xs font-bold uppercase tracking-wider text-blue-600 bg-blue-100 px-3 py-1 rounded-full shadow-sm">Question</span>
                
                <div class="flex flex-col items-center w-full">
                    <div id="question-number-display" class="text-5xl sm:text-7xl font-black text-blue-50 mb-4 sm:mb-6 select-none transition-all">1</div>
                    <h2 id="question-text" class="text-lg sm:text-2xl font-bold text-gray-800 leading-snug px-2 select-none">
                        How do you define interpreting?
                    </h2>
                </div>
                
                <div class="absolute bottom-6 sm:bottom-8 text-gray-400 text-xs sm:text-sm flex items-center gap-2 animate-bounce font-medium select-none">
                    <svg xmlns="http://www.w3.org/2000/svg" class="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 12a9 9 0 1 1-9-9c2.52 0 4.93 1 6.74 2.74L21 8"/><path d="M21 3v5h-5"/></svg>
                    Tap to Flip
                </div>
                
                <div class="absolute inset-0 bg-gradient-to-br from-transparent to-blue-50 opacity-50 rounded-2xl pointer-events-none"></div>
            </div>

            <!-- BACK FACE (Mặt sau - Câu trả lời) -->
            <!-- Thêm id="back-face" để JS điều khiển pointer-events -->
            <div id="back-face" class="absolute w-full h-full backface-hidden rotate-y-180 bg-slate-50 rounded-2xl p-0 overflow-hidden border border-slate-200 text-left flex flex-col pointer-events-none">
                <!-- Sticky Header -->
                <div class="bg-white px-4 py-3 sm:px-6 sm:py-4 border-b border-slate-100 flex justify-between items-center shadow-sm z-30 shrink-0 cursor-pointer" onclick="flipCard()">
                    <span class="text-xs font-bold uppercase tracking-wider text-white bg-green-500 px-3 py-1 rounded-full shadow-sm">Answer</span>
                    <div class="flex items-center gap-1 text-[10px] sm:text-xs text-gray-400 italic">
                        <span>Tap header to flip back</span>
                        <svg xmlns="http://www.w3.org/2000/svg" class="w-3 h-3" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M4 14.899A7 7 0 1 1 15.71 8h1.79a4.5 4.5 0 0 1 2.5 8.242"/><path d="M12 12v9"/><path d="m16 16-4-4-4 4"/></svg>
                    </div>
                </div>

                <!-- Nội dung cuộn -->
                <!-- Thêm touch-action: pan-y để đảm bảo cuộn mượt trên mobile -->
                <div id="answer-content" class="flex-1 overflow-y-auto p-4 sm:p-8 custom-scrollbar space-y-4 bg-slate-50" style="touch-action: pan-y;">
                    <!-- Nội dung JS -->
                </div>
            </div>
        </div>
    </div>

    <!-- Controls -->
    <div class="flex items-center gap-3 sm:gap-4 mt-4 sm:mt-8 w-full justify-center pb-4 sm:pb-8">
        <button onclick="prevCard()" class="p-3 sm:p-4 rounded-full bg-white shadow-lg hover:bg-blue-50 active:bg-blue-100 text-gray-600 hover:text-blue-600 transition-all border border-gray-100 hover:scale-105 btn-active">
            <svg xmlns="http://www.w3.org/2000/svg" class="w-5 h-5 sm:w-6 sm:h-6" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m15 18-6-6 6-6"/></svg>
        </button>

        <button onclick="flipCard()" id="flip-btn" class="flex-1 max-w-[180px] sm:max-w-[200px] px-4 py-3 sm:px-6 sm:py-4 rounded-full bg-blue-600 text-white text-sm sm:text-base font-bold shadow-xl hover:bg-blue-700 active:scale-95 transition-all hover:shadow-2xl ring-offset-2 focus:ring-2 ring-blue-500 btn-active">
            Show Answer
        </button>

        <button onclick="nextCard()" class="p-3 sm:p-4 rounded-full bg-white shadow-lg hover:bg-blue-50 active:bg-blue-100 text-gray-600 hover:text-blue-600 transition-all border border-gray-100 hover:scale-105 btn-active">
            <svg xmlns="http://www.w3.org/2000/svg" class="w-5 h-5 sm:w-6 sm:h-6" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m9 18 6-6-6-6"/></svg>
        </button>
    </div>

    <!-- SETTINGS MODAL -->
    <div id="settings-modal" class="fixed inset-0 bg-black/50 z-50 hidden flex items-center justify-center p-4 backdrop-blur-sm">
        <div class="bg-white rounded-2xl shadow-2xl w-full max-w-sm p-6 transform transition-all scale-95 opacity-0" id="modal-content">
            <div class="flex justify-between items-center mb-4 border-b pb-2">
                <h3 class="text-lg font-bold text-gray-800 flex items-center gap-2">
                    <svg xmlns="http://www.w3.org/2000/svg" class="w-5 h-5 text-blue-600" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M12.22 2h-.44a2 2 0 0 0-2 2v.18a2 2 0 0 1-1 1.73l-.43.25a2 2 0 0 1-2 0l-.15-.08a2 2 0 0 0-2.73.73l-.22.38a2 2 0 0 0 .73 2.73l.15.1a2 2 0 0 1 1 1.72v.51a2 2 0 0 1-1 1.74l-.15.09a2 2 0 0 0-.73 2.73l.22.38a2 2 0 0 0 2.73.73l.15-.08a2 2 0 0 1 2 0l.43.25a2 2 0 0 1 1 1.73V20a2 2 0 0 0 2 2h.44a2 2 0 0 0 2-2v-.18a2 2 0 0 1 1-1.73l.43-.25a2 2 0 0 1 2 0l.15.08a2 2 0 0 0 2.73-.73l.22-.39a2 2 0 0 0-.73-2.73l-.15-.1a2 2 0 0 1-1-1.74v-.47a2 2 0 0 1 1-1.74l.15-.09a2 2 0 0 0 .73-2.73l-.22-.35a2 2 0 0 0-2.73-.73l-.15.08a2 2 0 0 1-2 0l-.43-.25a2 2 0 0 1-1-1.73V4a2 2 0 0 0-2-2z"/><circle cx="12" cy="12" r="3"/></svg>
                    Cài đặt Giọng đọc
                </h3>
                <button onclick="toggleSettings()" class="text-gray-400 hover:text-red-500 p-1">
                    <svg xmlns="http://www.w3.org/2000/svg" class="w-6 h-6" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
                </button>
            </div>
            
            <div class="space-y-4">
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-1">Chọn Giọng (Voice)</label>
                    <select id="voice-select" class="w-full p-2 border border-gray-300 rounded-lg text-sm bg-gray-50 focus:ring-2 focus:ring-blue-500 outline-none">
                        <option value="">Đang tải danh sách giọng...</option>
                    </select>
                </div>

                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-1">Tốc độ (Speed)</label>
                    <div class="flex items-center gap-3">
                        <span class="text-xs text-gray-500 w-8">0.5x</span>
                        <input type="range" id="rate-range" min="0.5" max="1.5" step="0.1" value="0.9" class="w-full h-2 bg-gray-200 rounded-lg appearance-none cursor-pointer accent-blue-600">
                        <span class="text-xs text-gray-500 w-8">1.5x</span>
                    </div>
                    <div class="text-center text-xs text-blue-600 font-bold mt-1" id="rate-value">0.9x</div>
                </div>
            </div>

            <div class="mt-6 flex gap-2">
                <button onclick="testVoice()" class="flex-1 py-2 px-4 bg-gray-100 hover:bg-gray-200 text-gray-700 rounded-lg text-sm font-medium transition-colors">Test</button>
                <button onclick="saveSettings()" class="flex-1 py-2 px-4 bg-blue-600 hover:bg-blue-700 text-white rounded-lg text-sm font-medium transition-colors shadow-md">Lưu & Đóng</button>
            </div>
        </div>
    </div>

    <!-- JAVASCRIPT LOGIC -->
    <script>
        // Dữ liệu câu hỏi với Full IPA từng câu
        const cards = [
            {
                id: 1,
                question: "Question 1. How do you define interpreting?",
                sections: [
                    {
                        title: "1. Definition (Định nghĩa)",
                        lines: [
                            { en: "To begin with, I would like to define interpreting.", ipa: "/tuː bɪˈɡɪn wɪð, aɪ wʊd laɪk tuː dɪˈfaɪn ɪnˈtɜːprɪtɪŋ/" },
                            { en: "Interpreting is the oral transfer of a spoken message from one language into another.", ipa: "/ɪnˈtɜːprɪtɪŋ ɪz ði ˈɔːrəl ˈtrænsfɜːr ɒv ə ˈspəʊkən ˈmɛsɪdʒ frɒm wʌn ˈlæŋɡwɪdʒ ˈɪntuː əˈnʌðə/" },
                            { en: "The most important thing is maintaining its meaning fully and accurately.", ipa: "/ðə məʊst ɪmˈpɔːtənt θɪŋ ɪz meɪnˈteɪnɪŋ ɪts ˈmiːnɪŋ ˈfʊli ənd ˈækjərətli/" },
                            { en: "It typically occurs during cross-cultural communication when two parties do not share the same language.", ipa: "/ɪt ˈtɪpɪkli əˈkɜːz ˈdjʊərɪŋ ˌkrɒsˈkʌltʃərəl kəˌmjuːnɪˈkeɪʃn wɛn tuː ˈpɑːtiz duː nɒt ʃeə ðə seɪm ˈlæŋɡwɪdʒ/" },
                            { en: "Therefore, an interpreter is a professional who acts as a communication bridge between people speaking different languages.", ipa: "/ðeəfɔː, ən ɪnˈtɜːprɪtər ɪz ə prəˈfɛʃənl huː ækts æz ə kəˌmjuːnɪˈkeɪʃn brɪdʒ bɪˈtwiːn ˈpiːpl ˈspiːkɪŋ ˈdɪfrənt ˈlæŋɡwɪdʒɪz/" },
                            { en: "They are trained to listen and express messages in real time.", ipa: "/ðeɪ ɑː treɪnd tuː ˈlɪsn ənd ɪkˈsprɛs ˈmɛsɪdʒɪz ɪn rɪəl taɪm/" }
                        ]
                    },
                    {
                        title: "2. Procedure (Quy trình)",
                        lines: [
                            { en: "Moving on to the procedure of interpreting.", ipa: "/ˈmuːvɪŋ ɒn tuː ðə prəˈsiːdʒər ɒv ɪnˈtɜːprɪtɪŋ/" },
                            { en: "The process generally consists of 4 steps:", ipa: "/ðə ˈprəʊsɛs ˈdʒɛnərəli kənˈsɪsts ɒv fɔː stɛps/" },
                            { en: "Step 1: The interpreter listens to the Source Language (SL).", ipa: "/stɛp wʌn: ði ɪnˈtɜːprɪtə ˈlɪsnz tuː ðə sɔːs ˈlæŋɡwɪdʒ/" },
                            { en: "Step 2: They need to comprehend or understand the message.", ipa: "/stɛp tuː: ðeɪ niːd tuː ˌkɒmprɪˈhɛnd ɔːr ˌʌndəˈstænd ðə ˈmɛsɪdʒ/" },
                            { en: "At this stage, the understanding is still general.", ipa: "/æt ðɪs steɪdʒ, ði ˌʌndəˈstændɪŋ ɪz stɪl ˈdʒɛnərəl/" },
                            { en: "Step 3: The interpreter grasps the core meaning or the main idea of the speech.", ipa: "/stɛp θriː: ði ɪnˈtɜːprɪtə ɡrɑːsps ðə kɔː ˈmiːnɪŋ ɔː ðə meɪn aɪˈdɪə ɒv ðə spiːtʃ/" },
                            { en: "Step 4: Finally, they re-express the meaning in the Target Language (TL).", ipa: "/stɛp fɔː: ˈfaɪnəli, ðeɪ riː-ɪkˈsprɛs ðə ˈmiːnɪŋ ɪn ðə ˈtɑːɡɪt ˈlæŋɡwɪdʒ/" },
                            { en: "This output must be full-meaning and exactly convey what the speaker wants to say.", ipa: "/ðɪs ˈaʊtpʊt mʌst biː fʊl-ˈmiːnɪŋ ənd ɪɡˈzæktli kənˈveɪ wɒt ðə ˈspiːkə wɒnts tuː seɪ/" }
                        ]
                    }
                ]
            },
            {
                id: 2,
                question: "Question 2. What are the differences between consecutive interpreters and simultaneous interpreters?",
                sections: [
                    {
                        title: "1. Consecutive Interpreting (Phiên dịch đuổi)",
                        lines: [
                            { en: "Now, I would like to distinguish between the two main types of interpreting.", ipa: "/naʊ, aɪ wʊd laɪk tuː dɪˈstɪŋɡwɪʃ bɪˈtwiːn ðə tuː meɪn taɪps ɒv ɪnˈtɜːprɪtɪŋ/" },
                            { en: "First is Consecutive Interpreting.", ipa: "/fɜːst ɪz kənˈsɛkjʊtɪv ɪnˈtɜːprɪtɪŋ/" },
                            { en: "In this mode, the speaker delivers a short segment of speech and then pauses.", ipa: "/ɪn ðɪs məʊd, ðə ˈspiːkə dɪˈlɪvəz ə ʃɔːt ˈsɛɡmənt ɒv spiːtʃ ənd ðɛn ˈpɔːzɪz/" },
                            { en: "During this pause, the interpreter renders the message into the target language.", ipa: "/ˈdjʊərɪŋ ðɪs pɔːz, ði ɪnˈtɜːprɪtə ˈrɛndəz ðə ˈmɛsɪdʒ ˈɪntuː ðə ˈtɑːɡɪt ˈlæŋɡwɪdʒ/" },
                            { en: "This process continues until the end of the session.", ipa: "/ðɪs ˈprəʊsɛs kənˈtɪnjuːz ʌnˈtɪl ði ɛnd ɒv ðə ˈsɛʃən/" },
                            { en: "There are 3 common kinds of consecutive interpreting:", ipa: "/ðeər ɑː θriː ˈkɒmən kaɪnz ɒv kənˈsɛkjʊtɪv ɪnˈtɜːprɪtɪŋ/" },
                            { en: "1. Whole speech interpreting.", ipa: "/wʌn. həʊl spiːtʃ ɪnˈtɜːprɪtɪŋ/" },
                            { en: "2. Community interpreting.", ipa: "/tuː. kəˈmjuːnɪti ɪnˈtɜːprɪtɪŋ/" },
                            { en: "3. Escort interpreting.", ipa: "/θriː. ˈɛskɔːt ɪnˈtɜːprɪtɪŋ/" }
                        ]
                    },
                    {
                        title: "2. Simultaneous Interpreting (Phiên dịch song song)",
                        lines: [
                            { en: "On the other hand, we have Simultaneous Interpreting.", ipa: "/ɒn ði ˈʌðə hænd, wiː hæv ˌsɪməlˈteɪnɪəs ɪnˈtɜːprɪtɪŋ/" },
                            { en: "Unlike consecutive interpreting, when the speaker starts to speak, the interpreter also starts to convey the message.", ipa: "/ʌnˈlaɪk kənˈsɛkjʊtɪv ɪnˈtɜːprɪtɪŋ, wɛn ðə ˈspiːkə stɑːts tuː spiːk, ði ɪnˈtɜːprɪtə ˈɔːlsəʊ stɑːts tuː kənˈveɪ ðə ˈmɛsɪdʒ/" },
                            { en: "The interpretation must be completed as soon as the speaker finishes, without any pause.", ipa: "/ði ɪnˌtɜːprɪˈteɪʃn mʌst biː kəmˈpliːtɪd æz suːn æz ðə ˈspiːkə ˈfɪnɪʃɪz, wɪˈðaʊt ˈɛni pɔːz/" },
                            { en: "Therefore, the key challenge here is the ability to maintain a close, manageable gap between the interpretation and the speaker's pace.", ipa: "/ðeəfɔː, ðə kiː ˈtʃælɪndʒ hɪər ɪz ði əˈbɪlɪti tuː meɪnˈteɪn ə kləʊs, ˈmænɪdʒəbl ɡæp bɪˈtwiːn ði ɪnˌtɜːprɪˈteɪʃn ənd ðə ˈspiːkəz peɪs/" }
                        ]
                    }
                ]
            },
            {
                id: 3,
                question: "Question 3. Which form of interpreting is the most challenging to you: sight interpreting, consecutive interpreting, or simultaneous interpreting? Why?",
                sections: [
                    {
                        title: "Answer: Simultaneous Interpreting",
                        lines: [
                            { en: "In my opinion, Simultaneous Interpreting is the most challenging form.", ipa: "/ɪn maɪ əˈpɪnjən, ˌsɪməlˈteɪnɪəs ɪnˈtɜːprɪtɪŋ ɪz ðə məʊst ˈtʃælɪndʒɪŋ fɔːm/" },
                            { en: "For me, it is the most difficult because it is extremely mentally demanding.", ipa: "/fɔː miː, ɪt ɪz ðə məʊst ˈdɪfɪkəlt bɪˈkɒz ɪt ɪz ɪkˈstriːmli ˈmɛntəli dɪˈmɑːndɪŋ/" },
                            { en: "There are three main reasons for this:", ipa: "/ðeər ɑː θriː meɪn ˈriːznz fɔː ðɪs/" },
                            { en: "First is Multitasking.", ipa: "/fɜːst ɪz ˈmʌltiˌtɑːskɪŋ/" },
                            { en: "The core challenge is that you have to listen and speak at nearly the same time.", ipa: "/ðə kɔː ˈtʃælɪndʒ ɪz ðæt juː hæv tuː ˈlɪsn ənd spiːk æt ˈnɪəli ðə seɪm taɪm/" },
                            { en: "This task requires intense focus and mental multitasking.", ipa: "/ðɪs tɑːsk rɪˈkwaɪəz ɪnˈtɛns ˈfəʊkəs ənd ˈmɛntəl ˈmʌltiˌtɑːskɪŋ/" },
                            { en: "Second is Real-time Processing.", ipa: "/ˈsɛkənd ɪz rɪəl-taɪm ˈprəʊsɛsɪŋ/" },
                            { en: "Although interpreters usually work from a booth and don't face the audience, they must process a massive amount of information in real time.", ipa: "/ɔːlˈðəʊ ɪnˈtɜːprɪtəz ˈjuːʒʊəli wɜːk frɒm ə buːð ənd dəʊnt feɪs ði ˈɔːdɪəns, ðeɪ mʌst ˈprəʊsɛs ə ˈmæsɪv əˈmaʊnt ɒv ˌɪnfəˈmeɪʃn ɪn rɪəl taɪm/" },
                            { en: "And finally, it requires quick Reflexes.", ipa: "/ənd ˈfaɪnəli, ɪt rɪˈkwaɪəz kwɪk ˈriːflɛksɪz/" },
                            { en: "This job demands not only outstanding language fluency but also extremely quick reflexes to deliver the interpretation almost instantly.", ipa: "/ðɪs dʒɒb dɪˈmɑːndz nɒt ˈəʊnli aʊtˈstændɪŋ ˈlæŋɡwɪdʒ ˈfluːənsi bʌt ˈɔːlsəʊ ɪkˈstriːmli kwɪk ˈriːflɛksɪz tuː dɪˈlɪvə ði ɪnˌtɜːprɪˈteɪʃn ˈɔːlməʊst ˈɪnstəntli/" }
                        ]
                    }
                ]
            },
            {
                id: 4,
                question: "Question 4. Besides linguistic knowledge, what other kinds of knowledge does an interpreter need to have?",
                sections: [
                    {
                        title: "Two main categories of knowledge",
                        lines: [
                            { en: "Besides linguistic knowledge, an interpreter needs to have two other main kinds of knowledge.", ipa: "/bɪˈsaɪdz lɪŋˈɡwɪstɪk ˈnɒlɪdʒ, ən ɪnˈtɜːprɪtə niːdz tuː hæv tuː ˈʌðə meɪn kaɪnz ɒv ˈnɒlɪdʒ/" },
                            { en: "There are two main categories for this:", ipa: "/ðeər ɑː tuː meɪn ˈkætɪɡəriz fɔː ðɪs/" },
                            { en: "First is Socio-cultural and General Knowledge.", ipa: "/fɜːst ɪz ˌsəʊsɪəʊ-ˈkʌltʃərəl ənd ˈdʒɛnərəl ˈnɒlɪdʒ/" },
                            { en: "The core requirement is that you must understand daily life vocabulary.", ipa: "/ðə kɔː rɪˈkwaɪəmənt ɪz ðæt juː mʌst ˌʌndəˈstænd ˈdeɪli laɪf vəʊˈkæbjʊləri/" },
                            { en: "For example, even if you are not an expert, you should still know basic terms like voltage or circuit breakers.", ipa: "/fɔːr ɪɡˈzɑːmpl, ˈiːvən ɪf juː ɑː nɒt ən ˈɛkspɜːt, juː ʃʊd stɪl nəʊ ˈbeɪsɪk tɜːmz laɪk ˈvəʊltɪdʒ ɔː ˈsɜːkɪt ˈbreɪkəz/" },
                            { en: "Moreover, you need to be sensitive to cultural nuances because an action considered positive in one culture might be perceived as negative in another.", ipa: "/mɔːˈrəʊvə, juː niːd tuː biː ˈsɛnsɪtɪv tuː ˈkʌltʃərəl ˈnjuːɑːnsɪz bɪˈkɒz ən ˈækʃn kənˈsɪdəd ˈpɒzətɪv ɪn wʌn ˈkʌltʃə maɪt biː pəˈsiːvd æz ˈnɛɡətɪv ɪn əˈnʌðə/" },
                            { en: "Second is Interpreting Sub-skills.", ipa: "/ˈsɛkənd ɪz ɪnˈtɜːprɪtɪŋ sʌb-skɪlz/" },
                            { en: "This job demands not only knowledge but also specific skills to perform well.", ipa: "/ðɪs dʒɒb dɪˈmɑːndz nɒt ˈəʊnli ˈnɒlɪdʒ bʌt ˈɔːlsəʊ spəˈsɪfɪk skɪlz tuː pəˈfɔːm wɛl/" },
                            { en: "A professional interpreter needs a good memory (both short and long-term), quick reflexes, and confidence.", ipa: "/ə prəˈfɛʃənl ɪnˈtɜːprɪtə niːdz ə ɡʊd ˈmɛməri (bəʊθ ʃɔːt ənd lɒŋ-tɜːm), kwɪk ˈriːflɛksɪz, ənd ˈkɒnfɪdəns/" },
                            { en: "And finally, having a good voice (clarity, pronunciation) and a high sense of responsibility is also extremely important.", ipa: "/ənd ˈfaɪnəli, ˈhævɪŋ ə ɡʊd vɔɪs (ˈklærɪti, prəˌnʌnsɪˈeɪʃn) ənd ə haɪ sɛns ɒv rɪˌspɒnsəˈbɪlɪti ɪz ˈɔːlsəʊ ɪkˈstriːmli ɪmˈpɔːtənt/" }
                        ]
                    }
                ]
            },
            {
                id: 5,
                question: "Question 5. Do you think that any person who is good at a foreign language can become a good interpreter?",
                sections: [
                    {
                        title: "Answer: No",
                        lines: [
                            { en: "No, I do not agree with this idea.", ipa: "/nəʊ, aɪ duː nɒt əˈɡriː wɪð ðɪs aɪˈdɪə/" },
                            { en: "Being good at a foreign language does not guarantee that a person will become a good interpreter.", ipa: "/ˈbiːɪŋ ɡʊd æt ə ˈfɒrɪn ˈlæŋɡwɪdʒ dʌz nɒt ˌɡærənˈtiː ðæt ə ˈpɜːsn wɪl bɪˈkʌm ə ɡʊd ɪnˈtɜːprɪtə/" },
                            { en: "There are two main reasons for this:", ipa: "/ðeər ɑː tuː meɪn ˈriːznz fɔː ðɪs/" },
                            { en: "First, a good interpreter requires much more than just fluency.", ipa: "/fɜːst, ə ɡʊd ɪnˈtɜːprɪtə rɪˈkwaɪəz mʌtʃ mɔː ðæn dʒʌst ˈfluːənsi/" },
                            { en: "They need a clear voice, broad background knowledge, and knowledge of specialized terms.", ipa: "/ðeɪ niːd ə klɪə vɔɪs, brɔːd ˈbækɡraʊnd ˈnɒlɪdʒ, ənd ˈnɒlɪdʒ ɒv ˈspɛʃəlaɪzd tɜːmz/" },
                            { en: "They also need psychological traits like reflexes and confidence.", ipa: "/ðeɪ ˈɔːlsəʊ niːd ˌsaɪkəˈlɒdʒɪkəl treɪts laɪk ˈriːflɛksɪz ənd ˈkɒnfɪdəns/" },
                            { en: "Second, look at a practical example like a negotiation.", ipa: "/ˈsɛkənd, lʊk æt ə ˈpræktɪkəl ɪɡˈzɑːmpl laɪk ə nɪˌɡəʊʃɪˈeɪʃn/" },
                            { en: "The success depends heavily on the interpreter.", ipa: "/ðə səkˈsɛs dɪˈpɛndz ˈhɛvɪli ɒn ði ɪnˈtɜːprɪtə/" },
                            { en: "If the interpreting is clear and accurate, the parties will understand each other and decide whether to sign the contract or not.", ipa: "/ɪf ði ɪnˈtɜːprɪtɪŋ ɪz klɪər ənd ˈækjərət, ðə ˈpɑːtiz wɪl ˌʌndəˈstænd iːtʃ ˈʌðər ənd dɪˈsaɪd ˈwɛðə tuː saɪn ðə ˈkɒntrækt ɔː nɒt/" },
                            { en: "Therefore, language skills alone are not enough.", ipa: "/ðeəfɔː, ˈlæŋɡwɪdʒ skɪlz əˈləʊn ɑː nɒt ɪˈnʌf/" }
                        ]
                    }
                ]
            },
            {
                id: 6,
                question: "Question 6. Do interpreters need to know about the target audience? Why?",
                sections: [
                    {
                        title: "Answer: Yes, it is crucial",
                        lines: [
                            { en: "Yes, it is crucial for interpreters to know their audience.", ipa: "/jɛs, ɪt ɪz ˈkruːʃəl fɔːr ɪnˈtɜːprɪtəz tuː nəʊ ðeər ˈɔːdɪəns/" },
                            { en: "The main purpose is that understanding the audience helps the interpreter decide the most effective way to interpret.", ipa: "/ðə meɪn ˈpɜːpəs ɪz ðæt ˌʌndəˈstændɪŋ ði ˈɔːdɪəns hɛlps ði ɪnˈtɜːprɪtə dɪˈsaɪd ðə məʊst ɪˈfɛktɪv weɪ tuː ɪnˈtɜːprɪt/" },
                            { en: "By analyzing the audience's profile, the interpreter can make three key adjustments:", ipa: "/baɪ ˈænəlaɪzɪŋ ði ˈɔːdɪənsɪz ˈprəʊfaɪl, ði ɪnˈtɜːprɪtə kæn meɪk θriː kiː əˈdʒʌstmənts/" },
                            { en: "First is Vocabulary. You must decide whether to use specialized terms or simple words.", ipa: "/fɜːst ɪz vəʊˈkæbjʊləri. juː mʌst dɪˈsaɪd ˈwɛðə tuː juːz ˈspɛʃəlaɪzd tɜːmz ɔː ˈsɪmpl wɜːdz/" },
                            { en: "Second is Style. You need to choose between a formal/academic tone or a more friendly/literary style.", ipa: "/ˈsɛkənd ɪz staɪl. juː niːd tuː tʃuːz bɪˈtwiːn ə ˈfɔːməl/ˌækəˈdɛmɪk təʊn ɔːr ə mɔː ˈfrɛndli/ˈlɪtərəri staɪl/" },
                            { en: "And finally is Technique. You must decide whether to explain more details or simplify the concepts.", ipa: "/ənd ˈfaɪnəli ɪz tɛkˈniːk. juː mʌst dɪˈsaɪd ˈwɛðə tuː ɪkˈspleɪn mɔː ˈdiːteɪlz ɔː ˈsɪmplɪfaɪ ðə ˈkɒnsɛpts/" },
                            { en: "In conclusion, the biggest mistake is failing to adjust the language.", ipa: "/ɪn kənˈkluːʒən, ðə ˈbɪɡɪst mɪsˈteɪk ɪz ˈfeɪlɪŋ tuː əˈdʒʌst ðə ˈlæŋɡwɪdʒ/" },
                            { en: "Therefore, professionals always research the audience beforehand.", ipa: "/ðeəfɔː, prəˈfɛʃənlz ˈɔːlweɪz rɪˈsɜːtʃ ði ˈɔːdɪəns bɪˈfɔːhænd/" }
                        ]
                    }
                ]
            },
            {
                id: 7,
                question: "Question 7. The two basic criteria to evaluate an interpreter's performance are comprehension and delivery. Explain them.",
                sections: [
                    {
                        title: "Two Basic Criteria",
                        lines: [
                            { en: "Generally, a good interpreter is evaluated based on two basic criteria:", ipa: "/ˈdʒɛnərəli, ə ɡʊd ɪnˈtɜːprɪtər ɪz ɪˈvæljʊeɪtɪd beɪst ɒn tuː ˈbeɪsɪk kraɪˈtɪərɪə/" },
                            { en: "First is Comprehension.", ipa: "/fɜːst ɪz ˌkɒmprɪˈhɛnʃn/" },
                            { en: "This refers to the ability to listen, understand, and grasp the main ideas or core meanings of the speaker.", ipa: "/ðɪs rɪˈfɜːz tuː ði əˈbɪlɪti tuː ˈlɪsn, ˌʌndəˈstænd, ənd ɡrɑːsp ðə meɪn aɪˈdɪəz ɔː kɔː ˈmiːnɪŋz ɒv ðə ˈspiːkə/" },
                            { en: "Second is Delivery.", ipa: "/ˈsɛkənd ɪz dɪˈlɪvəri/" },
                            { en: "This refers to the ability to re-express the message and interpret it accurately in the target language.", ipa: "/ðɪs rɪˈfɜːz tuː ði əˈbɪlɪti tuː riː-ɪkˈsprɛs ðə ˈmɛsɪdʒ ənd ɪnˈtɜːprɪt ɪt ˈækjərətli ɪn ðə ˈtɑːɡɪt ˈlæŋɡwɪdʒ/" }
                        ]
                    }
                ]
            },
            {
                id: 8,
                question: "Question 8. Do you agree that the faster an interpreter interprets, the more competent he/she is?",
                sections: [
                    {
                        title: "Answer: No, speed is not competence",
                        lines: [
                            { en: "No, that is wrong.", ipa: "/nəʊ, ðæt ɪz rɒŋ/" },
                            { en: "Speed is just one factor and not the true measure of competence.", ipa: "/spiːd ɪz dʒʌst wʌn ˈfæktər ənd nɒt ðə truː ˈmɛʒər ɒv ˈkɒmpɪtəns/" },
                            { en: "To me, there is a clear difference:", ipa: "/tuː miː, ðeər ɪz ə klɪə ˈdɪfrəns/" },
                            { en: "Fluent interpreting means clear, accurate, and smooth delivery.", ipa: "/ˈfluːənt ɪnˈtɜːprɪtɪŋ miːnz klɪər, ˈækjərət, ənd smuːð dɪˈlɪvəri/" },
                            { en: "Competent interpreting means faithfully conveying meaning, tone, and nuance while maintaining the flow.", ipa: "/ˈkɒmpɪtənt ɪnˈtɜːprɪtɪŋ miːnz ˈfeɪθfʊli kənˈveɪɪŋ ˈmiːnɪŋ, təʊn, ənd ˈnjuːɑːns waɪl meɪnˈteɪnɪŋ ðə fləʊ/" },
                            { en: "Therefore, the best interpreters know when to go fast and when to pause.", ipa: "/ðeəfɔː, ðə bɛst ɪnˈtɜːprɪtəz nəʊ wɛn tuː ɡəʊ fɑːst ənd wɛn tuː pɔːz/" },
                            { en: "They know how to prioritize clarity over simply racing against time.", ipa: "/ðeɪ nəʊ haʊ tuː praɪˈɒrɪtaɪz ˈklærɪti ˈəʊvə ˈsɪmpli ˈreɪsɪŋ əˈɡɛnst taɪm/" }
                        ]
                    }
                ]
            },
            {
                id: 9,
                question: "Question 9. There are \"3 No's\" for interpreters: No addition - No omission - No alteration. Explain them.",
                sections: [
                    {
                        title: "The \"3 No's\" Rule",
                        lines: [
                            { en: "To maintain accuracy, an interpreter must strictly follow the '3 No's':", ipa: "/tuː meɪnˈteɪn ˈækjərəsi, ən ɪnˈtɜːprɪtə mʌst ˈstrɪktli ˈfɒləʊ ðə θriː nəʊz/" },
                            { en: "Firstly, No Addition.", ipa: "/ˈfɜːstli, nəʊ əˈdɪʃn/" },
                            { en: "You must not add information based on your subjective experience.", ipa: "/juː mʌst nɒt æd ˌɪnfəˈmeɪʃn beɪst ɒn jɔː səbˈdʒɛktɪv ɪkˈspɪərɪəns/" },
                            { en: "Adding information can make the audience misunderstand, especially since the interpreter is not the expert.", ipa: "/ˈædɪŋ ˌɪnfəˈmeɪʃn kæn meɪk ði ˈɔːdɪəns ˌmɪsʌndəˈstænd, ɪˈspɛʃəli sɪns ði ɪnˈtɜːprɪtər ɪz nɒt ði ˈɛkspɜːt/" },
                            { en: "Secondly, No Omission.", ipa: "/ˈsɛkəndli, nəʊ əˈmɪʃn/" },
                            { en: "You must not skip any information, especially main ideas.", ipa: "/juː mʌst nɒt skɪp ˈɛni ˌɪnfəˈmeɪʃn, ɪˈspɛʃəli meɪn aɪˈdɪəz/" },
                            { en: "This is crucial in high-stakes situations like contract negotiations.", ipa: "/ðɪs ɪz ˈkruːʃəl ɪn haɪ-steɪks ˌsɪtjʊˈeɪʃnz laɪk ˈkɒntrækt nɪˌɡəʊʃɪˈeɪʃnz/" },
                            { en: "Last but not least, No Alteration.", ipa: "/lɑːst bʌt nɒt liːst, nəʊ ˌɔːltəˈreɪʃn/" },
                            { en: "You are not allowed to change the information given by the speaker.", ipa: "/juː ɑː nɒt əˈlaʊd tuː tʃeɪndʒ ði ˌɪnfəˈmeɪʃn ˈɡɪvn baɪ ðə ˈspiːkə/" },
                            { en: "Changing ideas leads to misunderstanding and affects your reliability.", ipa: "/ˈtʃeɪndʒɪŋ aɪˈdɪəz liːdz tuː ˌmɪsʌndəˈstændɪŋ ənd əˈfɛkts jɔː rɪˌlaɪəˈbɪlɪti/" }
                        ]
                    }
                ]
            },
            {
                id: 10,
                question: "Question 10. What are some main ethical considerations for interpreters?",
                sections: [
                    {
                        title: "Three Main Ethical Pillars",
                        lines: [
                            { en: "There are three main ethical pillars for interpreters:", ipa: "/ðeər ɑː θriː meɪn ˈɛθɪkəl ˈpɪləz fɔːr ɪnˈtɜːprɪtəz/" },
                            { en: "First is Confidentiality.", ipa: "/fɜːst ɪz ˌkɒnfɪˌdɛnʃɪˈælɪti/" },
                            { en: "Interpreters must not reveal any information to a third party.", ipa: "/ɪnˈtɜːprɪtəz mʌst nɒt rɪˈviːl ˈɛni ˌɪnfəˈmeɪʃn tuː ə θɜːd ˈpɑːti/" },
                            { en: "Second is Impartiality.", ipa: "/ˈsɛkənd ɪz ˌɪmpɑːʃɪˈælɪti/" },
                            { en: "They must remain neutral, show no bias, and act as a faithful bridge (especially in legal contexts).", ipa: "/ðeɪ mʌst rɪˈmeɪn ˈnjuːtrəl, ʃəʊ nəʊ ˈbaɪəs, ənd ækt æz ə ˈfeɪθfʊl brɪdʒ (ɪˈspɛʃəli ɪn ˈliːɡəl ˈkɒntɛksts)/" },
                            { en: "And finally, Accuracy.", ipa: "/ənd ˈfaɪnəli, ˈækjərəsi/" },
                            { en: "They must ensure no addition, no omission, and no alteration during the process.", ipa: "/ðeɪ mʌst ɪnˈʃʊə nəʊ əˈdɪʃn, nəʊ əˈmɪʃn, ənd nəʊ ˌɔːltəˈreɪʃn ˈdjʊərɪŋ ðə ˈprəʊsɛs/" }
                        ]
                    }
                ]
            }
        ];

        let currentIndex = 0;
        let isFlipped = false;
        let selectedVoiceURI = localStorage.getItem('preferredVoice') || '';
        let speechRate = parseFloat(localStorage.getItem('speechRate')) || 0.9;

        // Elements
        const cardInner = document.getElementById('card-inner');
        const frontFace = document.getElementById('front-face');
        const backFace = document.getElementById('back-face');
        const questionText = document.getElementById('question-text');
        const questionNumDisplay = document.getElementById('question-number-display');
        const answerContent = document.getElementById('answer-content');
        const progressBar = document.getElementById('progress-bar');
        const progressText = document.getElementById('progress-text');
        const flipBtn = document.getElementById('flip-btn');
        const settingsModal = document.getElementById('settings-modal');
        const modalContent = document.getElementById('modal-content');
        const voiceSelect = document.getElementById('voice-select');
        const rateRange = document.getElementById('rate-range');
        const rateValue = document.getElementById('rate-value');

        // ==== VOICE SETTINGS LOGIC ====
        
        function loadVoices() {
            if (!window.speechSynthesis) return;
            
            let voices = window.speechSynthesis.getVoices();
            if (voices.length === 0) {
                // Retry if voices aren't loaded yet
                setTimeout(loadVoices, 100);
                return;
            }

            voiceSelect.innerHTML = '';
            
            // Prioritize English voices
            voices.sort((a, b) => {
                const aName = a.name.toLowerCase();
                const bName = b.name.toLowerCase();
                // UK English top priority
                if (a.lang === 'en-GB' && b.lang !== 'en-GB') return -1;
                if (b.lang === 'en-GB' && a.lang !== 'en-GB') return 1;
                // Other English next
                if (a.lang.startsWith('en') && !b.lang.startsWith('en')) return -1;
                if (b.lang.startsWith('en') && !a.lang.startsWith('en')) return 1;
                return aName.localeCompare(bName);
            });

            voices.forEach(voice => {
                // Only show English voices to keep list clean, or all if preferred
                if(voice.lang.startsWith('en')) {
                    const option = document.createElement('option');
                    option.value = voice.voiceURI;
                    option.textContent = `${voice.name} (${voice.lang})`;
                    if (voice.voiceURI === selectedVoiceURI) {
                        option.selected = true;
                    }
                    voiceSelect.appendChild(option);
                }
            });

            // If no voice selected yet, try to auto-select a UK voice
            if (!selectedVoiceURI) {
                const ukVoice = voices.find(v => v.lang === 'en-GB');
                if (ukVoice) {
                    voiceSelect.value = ukVoice.voiceURI;
                    selectedVoiceURI = ukVoice.voiceURI;
                }
            }
        }

        // Load voices when they are ready
        if (window.speechSynthesis) {
            window.speechSynthesis.onvoiceschanged = loadVoices;
            loadVoices(); // Init call
        }

        function toggleSettings() {
            if (settingsModal.classList.contains('hidden')) {
                // Open
                settingsModal.classList.remove('hidden');
                setTimeout(() => {
                    modalContent.classList.remove('scale-95', 'opacity-0');
                    modalContent.classList.add('scale-100', 'opacity-100');
                }, 10);
                loadVoices(); // Refresh list just in case
                rateRange.value = speechRate;
                rateValue.textContent = speechRate + 'x';
            } else {
                // Close
                modalContent.classList.remove('scale-100', 'opacity-100');
                modalContent.classList.add('scale-95', 'opacity-0');
                setTimeout(() => {
                    settingsModal.classList.add('hidden');
                }, 200);
            }
        }

        rateRange.addEventListener('input', (e) => {
            speechRate = e.target.value;
            rateValue.textContent = speechRate + 'x';
        });

        function saveSettings() {
            selectedVoiceURI = voiceSelect.value;
            speechRate = rateRange.value;
            
            localStorage.setItem('preferredVoice', selectedVoiceURI);
            localStorage.setItem('speechRate', speechRate);
            
            toggleSettings();
        }

        function testVoice() {
            speakText("Hello, this is a test of the selected voice.");
        }

        function speakText(text) {
            if (!window.speechSynthesis) {
                alert("Trình duyệt không hỗ trợ đọc văn bản.");
                return;
            }
            
            window.speechSynthesis.cancel();

            const utterance = new SpeechSynthesisUtterance(text);
            
            // Apply settings
            const voices = window.speechSynthesis.getVoices();
            const selectedVoice = voices.find(v => v.voiceURI === selectedVoiceURI);
            
            if (selectedVoice) {
                utterance.voice = selectedVoice;
            } else {
                // Fallback to any en-GB if specific one not found
                const ukVoice = voices.find(v => v.lang === 'en-GB');
                if (ukVoice) utterance.voice = ukVoice;
            }

            utterance.rate = parseFloat(speechRate);
            window.speechSynthesis.speak(utterance);
        }

        // ==== CARD LOGIC ====

        function updateCard() {
            const card = cards[currentIndex];
            
            // Reset Flip State
            isFlipped = false;
            cardInner.classList.remove('rotate-y-180');
            flipBtn.textContent = "Show Answer";
            
            // Reset interaction states
            frontFace.style.pointerEvents = 'auto';
            backFace.style.pointerEvents = 'none';

            // Update Text
            const qNum = card.question.match(/Question (\d+)/);
            questionNumDisplay.textContent = qNum ? qNum[1] : "?";
            questionText.textContent = card.question.replace(/Question \d+\.\s*/, '');

            // Update Progress
            progressText.textContent = `Question ${currentIndex + 1} of ${cards.length}`;
            progressBar.style.width = `${((currentIndex + 1) / cards.length) * 100}%`;

            // Scroll to top of answer
            if(answerContent) answerContent.scrollTop = 0;

            // Render Answer
            answerContent.innerHTML = card.sections.map(section => `
                <div class="bg-white p-4 sm:p-5 rounded-xl shadow-sm border border-slate-100 hover:shadow-md transition-shadow">
                    <h3 class="font-bold text-blue-800 text-base sm:text-lg mb-4 border-b border-blue-50 pb-2 flex items-center">
                        ${section.title}
                    </h3>
                    <div class="space-y-4">
                        ${section.lines.map(line => `
                            <div class="group mb-3">
                                <div class="flex items-start gap-3">
                                    <button 
                                        onclick="event.stopPropagation(); speakText('${line.en.replace(/'/g, "\\\\'").replace(/"/g, "&quot;")}')" 
                                        class="btn-audio shrink-0 mt-0.5 w-8 h-8 flex items-center justify-center rounded-full bg-blue-100 text-blue-600 hover:bg-blue-200 transition-colors shadow-sm"
                                        aria-label="Listen"
                                        title="Nghe"
                                    >
                                        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polygon points="11 5 6 9 2 9 2 15 6 15 11 19 11 5"></polygon><path d="M15.54 8.46a5 5 0 0 1 0 7.07"></path></svg>
                                    </button>
                                    <div>
                                        <p class="text-gray-800 text-sm sm:text-base leading-relaxed font-medium mb-1">${line.en}</p>
                                        <p class="font-ipa text-slate-500 text-xs sm:text-sm bg-slate-50 px-2 py-1 rounded inline-block border border-slate-100 select-all">${line.ipa}</p>
                                    </div>
                                </div>
                            </div>
                        `).join('')}
                    </div>
                </div>
            `).join('');
        }

        function flipCard() {
            isFlipped = !isFlipped;
            if (isFlipped) {
                // FLIPPED TO BACK
                cardInner.classList.add('rotate-y-180');
                flipBtn.textContent = "Show Question";
                
                // IMPORTANT: Disable pointer events on front so back is clickable/scrollable
                // Timeout matches transition to prevent accidental clicks during flip
                setTimeout(() => {
                    frontFace.style.pointerEvents = 'none';
                    backFace.style.pointerEvents = 'auto';
                }, 150);
                
            } else {
                // FLIPPED TO FRONT
                cardInner.classList.remove('rotate-y-180');
                flipBtn.textContent = "Show Answer";
                
                setTimeout(() => {
                    frontFace.style.pointerEvents = 'auto';
                    backFace.style.pointerEvents = 'none';
                }, 150);
            }
        }

        function nextCard() {
            cardInner.style.opacity = '0.5';
            setTimeout(() => {
                currentIndex = (currentIndex + 1) % cards.length;
                updateCard();
                cardInner.style.opacity = '1';
            }, 150);
        }

        function prevCard() {
            cardInner.style.opacity = '0.5';
            setTimeout(() => {
                currentIndex = (currentIndex - 1 + cards.length) % cards.length;
                updateCard();
                cardInner.style.opacity = '1';
            }, 150);
        }

        // Keyboard Navigation
        document.addEventListener('keydown', (e) => {
            if (e.key === 'ArrowRight') nextCard();
            if (e.key === 'ArrowLeft') prevCard();
            if (e.key === ' ' || e.key === 'Enter') {
                e.preventDefault();
                flipCard();
            }
        });

        // Close modal when clicking outside
        settingsModal.addEventListener('click', (e) => {
            if (e.target === settingsModal) {
                toggleSettings();
            }
        });

        // Init
        updateCard();

    </script>
</body>
</html>
