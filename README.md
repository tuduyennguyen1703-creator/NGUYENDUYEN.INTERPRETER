<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Interpreting Flashcards</title>
    
    <!-- 1. Tải Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- 2. Tải React và ReactDOM (Dùng unpkg để ổn định hơn cho file local) -->
    <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    
    <!-- 3. Tải Babel để xử lý JSX -->
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

    <style>
        /* Hiệu ứng 3D */
        .perspective-1000 {
            perspective: 1000px;
        }
        .transform-style-3d {
            transform-style: preserve-3d;
        }
        .backface-hidden {
            backface-visibility: hidden;
        }
        .rotate-y-180 {
            transform: rotateY(180deg);
        }
        /* Thanh cuộn đẹp hơn */
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
        .custom-scrollbar::-webkit-scrollbar-thumb:hover {
            background: #94a3b8;
        }
        body {
            overscroll-behavior-y: none;
            -webkit-tap-highlight-color: transparent;
        }
        /* Loading Error State */
        #error-message {
            display: none;
        }
    </style>
</head>
<body class="bg-gray-100 text-slate-800">
    <!-- Container chính để React render vào -->
    <div id="root" class="min-h-screen flex items-center justify-center">
        <div id="loading-state" class="text-center p-4">
            <div class="animate-spin rounded-full h-12 w-12 border-b-2 border-blue-600 mx-auto mb-4"></div>
            <p class="text-gray-600">Đang tải Flashcards...</p>
            <p class="text-xs text-gray-400 mt-2">(Đang kết nối thư viện React...)</p>
        </div>
        <div id="error-message" class="text-center p-4 text-red-600 bg-red-50 rounded-lg border border-red-200">
            <h3 class="font-bold text-lg mb-2">Không thể tải ứng dụng</h3>
            <p>Vui lòng kiểm tra kết nối internet của bạn.</p>
            <p class="text-xs mt-2">React hoặc Babel không tải được từ CDN.</p>
        </div>
    </div>

    <!-- Script kiểm tra lỗi tải thư viện -->
    <script>
        window.onload = function() {
            setTimeout(function() {
                // Nếu sau 3 giây mà React chưa chạy (root chưa có nội dung thay đổi), hiện thông báo lỗi
                var root = document.getElementById('root');
                if (root && root.innerHTML.includes('Đang tải Flashcards')) {
                    if (typeof React === 'undefined' || typeof ReactDOM === 'undefined') {
                        document.getElementById('loading-state').style.display = 'none';
                        document.getElementById('error-message').style.display = 'block';
                    }
                }
            }, 5000);
        };
    </script>

    <!-- Script chính chứa mã React -->
    <script type="text/babel" data-presets="env,react">
        const { useState, useEffect } = React;

        // --- Các biểu tượng (Icons) ---
        const ChevronLeft = ({ className }) => (
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="m15 18-6-6 6-6"/></svg>
        );
        const ChevronRight = ({ className }) => (
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="m9 18 6-6-6-6"/></svg>
        );
        const RotateCw = ({ className }) => (
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="M21 12a9 9 0 1 1-9-9c2.52 0 4.93 1 6.74 2.74L21 8"/><path d="M21 3v5h-5"/></svg>
        );
        const BookOpen = ({ className }) => (
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="M2 3h6a4 4 0 0 1 4 4v14a3 3 0 0 0-3-3H2z"/><path d="M22 3h-6a4 4 0 0 0-4 4v14a3 3 0 0 1 3-3h7z"/></svg>
        );
        const GraduationCap = ({ className }) => (
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="M22 10v6M2 10l10-5 10 5-10 5z"/><path d="M6 12v5c3 0 6-3 6-3s-3-3-6-3"/></svg>
        );

        // --- Component Chính ---
        const InterpretingFlashcards = () => {
            const [currentIndex, setCurrentIndex] = useState(0);
            const [isFlipped, setIsFlipped] = useState(false);

            // Dữ liệu câu hỏi và trả lời
            const cards = [
                {
                    id: 1,
                    question: "Question 1. How do you define interpreting?",
                    sections: [
                        {
                            title: "1. Definition (Định nghĩa)",
                            content: "To begin with, I would like to define interpreting.\nInterpreting is the oral transfer /ˈɔːrəl trænsˈfɜːr/ of a spoken message from one language into another. The most important thing is maintaining its meaning fully and accurately /ˈækjərətli/.\nIt typically occurs during cross-cultural /ˌkrɒs ˈkʌltʃərəl/ communication when two parties do not share the same language. Therefore, an interpreter is a professional who acts as a communication bridge /brɪdʒ/ between people speaking different languages. They are trained to listen and express messages in real time /riːl taɪm/."
                        },
                        {
                            title: "2. Procedure (Quy trình)",
                            content: "Moving on to the procedure /prəˈsiːdʒər/ of interpreting. The process generally /ˈdʒenrəli/ consists of 4 steps:\n• Step 1: The interpreter listens to the Source Language (SL).\n• Step 2: They need to comprehend /ˌkɒmprɪˈhend/ or understand the message. At this stage, the understanding is still general.\n• Step 3: The interpreter grasps the core meaning /kɔːr ˈmiːnɪŋ/ or the main idea of the speech.\n• Step 4: Finally, they re-express /ˌriː ɪkˈspres/ the meaning in the Target Language (TL). This output must be full-meaning and exactly convey /kənˈveɪ/ what the speaker wants to say."
                        }
                    ]
                },
                {
                    id: 2,
                    question: "Question 2. What are the differences between consecutive interpreters and simultaneous interpreters?",
                    sections: [
                        {
                            title: "1. Consecutive Interpreting (Phiên dịch đuổi)",
                            content: "Now, I would like to distinguish between the two main types of interpreting.\nFirst is Consecutive Interpreting /kənˈsekjətɪv/. In this mode, the speaker delivers a short segment /ˈseɡmənt/ of speech and then pauses /pɔːz/. During this pause, the interpreter renders /ˈrendər/ the message into the target language. This process continues until the end of the session.\nThere are 3 common kinds of consecutive interpreting:\n1. Whole speech interpreting.\n2. Community interpreting.\n3. Escort /ˈeskɔːt/ interpreting."
                        },
                        {
                            title: "2. Simultaneous Interpreting (Phiên dịch song song)",
                            content: "On the other hand, we have Simultaneous Interpreting /ˌsɪmlˈteɪniəs/. Unlike consecutive interpreting, when the speaker starts to speak, the interpreter also starts to convey the message. The interpretation /ɪnˌtɜːprəˈteɪʃn/ must be completed as soon as the speaker finishes, without any pause.\nTherefore, the key challenge here is the ability to maintain a close, manageable gap /ˈmænɪdʒəbl ɡæp/ between the interpretation and the speaker's pace */peɪs/."
                        }
                    ]
                },
                {
                    id: 3,
                    question: "Question 3. Which form of interpreting is the most challenging to you: sight interpreting, consecutive interpreting, or simultaneous interpreting? Why?",
                    sections: [
                        {
                            title: "Answer: Simultaneous Interpreting",
                            content: "In my opinion, Simultaneous Interpreting is the most challenging form. For me, it is the most difficult because it is extremely mentally demanding.\n\nThere are three main reasons for this:\n\n1. Multitasking: The core challenge is that you have to listen and speak at nearly the same time. This task requires intense focus and mental multitasking.\n\n2. Real-time Processing: Although interpreters usually work from a booth and don't face the audience, they must process a massive amount of information in real time.\n\n3. Quick Reflexes: This job demands not only outstanding language fluency but also extremely quick reflexes to deliver the interpretation almost instantly."
                        }
                    ]
                },
                {
                    id: 4,
                    question: "Question 4. Besides linguistic knowledge, what other kinds of knowledge does an interpreter need to have?",
                    sections: [
                        {
                            title: "Two main categories of knowledge",
                            content: "Besides linguistic knowledge, an interpreter needs to have two other main kinds of knowledge.\n\n1. Socio-cultural and General Knowledge: The core requirement is that you must understand daily life vocabulary. For example, even if you are not an expert, you should still know basic terms like voltage /ˈvəʊltɪdʒ/ or circuit breakers /ˈsɜːkɪt ˈbreɪkəz/. Moreover, you need to be sensitive to cultural nuances /ˈnjuːɑːnsɪz/ because an action considered positive in one culture might be perceived as negative in another.\n\n2. Interpreting Sub-skills: This job demands not only knowledge but also specific skills to perform well. A professional interpreter needs a good memory (both short and long-term), quick reflexes /ˈriːfleksɪz/, and confidence. And finally, having a good voice (clarity, pronunciation) and a high sense of responsibility /rɪˌspɒnsəˈbɪləti/ is also extremely important."
                        }
                    ]
                },
                {
                    id: 5,
                    question: "Question 5. Do you think that any person who is good at a foreign language can become a good interpreter?",
                    sections: [
                        {
                            title: "Answer: No",
                            content: "No, I do not agree with this idea. Being good at a foreign language does not guarantee /ˌɡærənˈtiː/ that a person will become a good interpreter.\n\nThere are two main reasons for this:\n\n1. Skills beyond fluency: A good interpreter requires much more than just fluency. They need a clear voice, broad background knowledge, and knowledge of specialized terms /ˈspeʃəlaɪzd tɜːmz/. They also need psychological traits like reflexes and confidence.\n\n2. Practical impact: Look at a practical example like a negotiation /nɪˌɡəʊʃiˈeɪʃn/. The success depends heavily on the interpreter. If the interpreting is clear and accurate, the parties will understand each other and decide whether to sign the contract or not. Therefore, language skills alone are not enough."
                        }
                    ]
                },
                {
                    id: 6,
                    question: "Question 6. Do interpreters need to know about the target audience? Why?",
                    sections: [
                        {
                            title: "Answer: Yes, it is crucial",
                            content: "Yes, it is crucial /ˈkruːʃl/ for interpreters to know their audience. The main purpose is that understanding the audience helps the interpreter decide the most effective way to interpret.\n\nBy analyzing the audience's profile /ˈprəʊfaɪl/, the interpreter can make three key adjustments:\n• Vocabulary: You must decide whether to use specialized terms or simple words.\n• Style: You need to choose between a formal/academic /ˌækəˈdemɪk/ tone or a more friendly/literary /ˈlɪtərəri/ style.\n• Technique: You must decide whether to explain more details or simplify the concepts.\n\nIn conclusion, the biggest mistake is failing to adjust the language. Therefore, professionals always research the audience beforehand."
                        }
                    ]
                },
                {
                    id: 7,
                    question: "Question 7. The two basic criteria to evaluate an interpreter's performance.",
                    sections: [
                        {
                            title: "Two Basic Criteria",
                            content: "Generally, a good interpreter is evaluated based on two basic criteria /kraɪˈtɪəriə/:\n\n1. Comprehension /ˌkɒmprɪˈhenʃn/: This refers to the ability to listen, understand, and grasp /ɡrɑːsp/ the main ideas or core meanings of the speaker.\n\n2. Delivery /dɪˈlɪvəri/: This refers to the ability to re-express the message and interpret it accurately /ˈækjərətli/ in the target language."
                        }
                    ]
                },
                {
                    id: 8,
                    question: "Question 8. Do you agree that the faster an interpreter interprets, the more competent he/she is?",
                    sections: [
                        {
                            title: "Answer: No, speed is not competence",
                            content: "No, that is wrong. Speed is just one factor and not the true measure of competence /ˈkɒmpɪtəns/.\n\nTo me, there is a clear difference:\n• Fluent interpreting means clear, accurate, and smooth delivery.\n• Competent interpreting means faithfully conveying meaning, tone, and nuance while maintaining the flow.\n\nTherefore, the best interpreters know when to go fast and when to pause. They know how to prioritize /praɪˈɒrətaɪz/ clarity over simply racing against time."
                        }
                    ]
                },
                {
                    id: 9,
                    question: "Question 9. There are \"3 No's\" for interpreters: No addition - No omission - No alteration. Explain them.",
                    sections: [
                        {
                            title: "The \"3 No's\" Rule",
                            content: "To maintain accuracy, an interpreter must strictly follow the '3 No's':\n\n1. No Addition /əˈdɪʃn/: You must not add information based on your subjective experience /səbˈdʒektɪv/. Adding information can make the audience misunderstand, especially since the interpreter is not the expert.\n\n2. No Omission /əˈmɪʃn/: You must not skip any information, especially main ideas. This is crucial in high-stakes situations like contract negotiations.\n\n3. No Alteration /ˌɔːltəˈreɪʃn/: You are not allowed to change the information given by the speaker. Changing ideas leads to misunderstanding and affects your reliability /rɪˌlaɪəˈbɪləti/."
                        }
                    ]
                },
                {
                    id: 10,
                    question: "Question 10. What are some main ethical considerations for interpreters?",
                    sections: [
                        {
                            title: "Three Main Ethical Pillars",
                            content: "There are three main ethical pillars for interpreters:\n\n1. Confidentiality /ˌkɒnfɪˌdenʃiˈæləti/: Interpreters must not reveal any information to a third party.\n\n2. Impartiality /ˌɪmˌpɑːʃiˈæləti/: They must remain neutral, show no bias /ˈbaɪəs/, and act as a faithful bridge (especially in legal contexts).\n\n3. Accuracy: They must ensure no addition, no omission, and no alteration during the process."
                        }
                    ]
                }
            ];

            const handleNext = () => {
                setIsFlipped(false);
                setTimeout(() => {
                    setCurrentIndex((prev) => (prev + 1) % cards.length);
                }, 200);
            };

            const handlePrev = () => {
                setIsFlipped(false);
                setTimeout(() => {
                    setCurrentIndex((prev) => (prev - 1 + cards.length) % cards.length);
                }, 200);
            };

            const handleFlip = () => {
                setIsFlipped(!isFlipped);
            };

            // Keyboard navigation
            useEffect(() => {
                const handleKeyDown = (e) => {
                    if (e.key === 'ArrowRight') handleNext();
                    if (e.key === 'ArrowLeft') handlePrev();
                    if (e.key === ' ' || e.key === 'Enter') {
                        e.preventDefault(); 
                        handleFlip();
                    }
                };
                window.addEventListener('keydown', handleKeyDown);
                return () => window.removeEventListener('keydown', handleKeyDown);
            }, [isFlipped]);

            const currentCard = cards[currentIndex];

            // Helper to get question number safely
            const getQuestionNumber = (text) => {
                const match = text.match(/Question (\d+)/);
                return match ? match[1] : "?";
            };

            return (
                <div className="min-h-screen flex flex-col items-center py-4 sm:py-8 px-4 font-sans text-slate-800 bg-gray-100">
                    
                    {/* Header */}
                    <header className="mb-4 sm:mb-6 text-center">
                        <div className="flex items-center justify-center gap-2 mb-2">
                            <GraduationCap className="w-6 h-6 sm:w-8 sm:h-8 text-blue-600" />
                            <h1 className="text-2xl sm:text-3xl font-bold text-gray-800">Interpreting Review</h1>
                        </div>
                        <p className="text-sm sm:text-base text-gray-500">Subject: Interpreting & Translation Theory</p>
                    </header>

                    {/* Progress Bar */}
                    <div className="w-full max-w-2xl mb-4 flex justify-between items-center text-sm font-medium text-gray-600 px-1">
                        <span>Question {currentIndex + 1} of {cards.length}</span>
                        <div className="flex items-center gap-1">
                            <BookOpen className="w-4 h-4" />
                            <span>Study Mode</span>
                        </div>
                    </div>
                    
                    {/* Progress Line */}
                    <div className="w-full max-w-2xl bg-gray-300 h-2 rounded-full mb-6 sm:mb-8 overflow-hidden">
                        <div 
                            className="bg-blue-600 h-full transition-all duration-300 ease-out"
                            style={{ width: `${((currentIndex + 1) / cards.length) * 100}%` }}
                        />
                    </div>

                    {/* 3D Card Container */}
                    <div 
                        className="relative w-full max-w-2xl h-[60vh] sm:h-[500px] cursor-pointer group perspective-1000"
                        onClick={handleFlip}
                    >
                        <div 
                            className={`relative w-full h-full transition-all duration-500 transform-style-3d shadow-xl rounded-2xl ${isFlipped ? 'rotate-y-180' : ''}`}
                        >
                            
                            {/* FRONT FACE (Question) */}
                            <div className="absolute w-full h-full backface-hidden bg-white rounded-2xl p-6 sm:p-8 flex flex-col items-center justify-center border border-gray-200 text-center">
                                <span className="absolute top-4 left-4 sm:top-6 sm:left-6 text-xs font-bold uppercase tracking-wider text-blue-500 bg-blue-50 px-2 sm:px-3 py-1 rounded-full">
                                    Question
                                </span>
                                <div className="flex flex-col items-center w-full">
                                    <div className="text-5xl sm:text-6xl font-black text-blue-50 mb-4 select-none">
                                        {getQuestionNumber(currentCard.question)}
                                    </div>
                                    <h2 className="text-lg sm:text-2xl font-bold text-gray-800 leading-relaxed px-2">
                                        {currentCard.question.replace(/Question \d+\.\s*/, '')}
                                    </h2>
                                </div>
                                <div className="absolute bottom-6 text-gray-400 text-xs sm:text-sm flex items-center gap-2 animate-bounce">
                                    <RotateCw className="w-4 h-4" />
                                    Click or Press Space to Flip
                                </div>
                            </div>

                            {/* BACK FACE (Answer) */}
                            <div className="absolute w-full h-full backface-hidden rotate-y-180 bg-blue-50 rounded-2xl p-4 sm:p-8 overflow-y-auto border border-blue-200 custom-scrollbar text-left">
                                <span className="sticky top-0 left-0 text-xs font-bold uppercase tracking-wider text-white bg-green-500 px-3 py-1 rounded-full inline-block mb-4 shadow-sm z-10">
                                    Answer
                                </span>
                                
                                <div className="space-y-4 sm:space-y-6 pb-2">
                                    {currentCard.sections.map((section, idx) => (
                                        <div key={idx} className="bg-white p-4 sm:p-5 rounded-xl shadow-sm border border-blue-100">
                                            <h3 className="font-bold text-blue-800 text-base sm:text-lg mb-2 sm:mb-3 border-b border-blue-100 pb-2">
                                                {section.title}
                                            </h3>
                                            <div className="text-gray-700 text-sm sm:text-base leading-relaxed whitespace-pre-line">
                                                {section.content}
                                            </div>
                                        </div>
                                    ))}
                                </div>
                            </div>

                        </div>
                    </div>

                    {/* Controls */}
                    <div className="flex items-center gap-4 mt-6 sm:mt-8 w-full justify-center pb-8">
                        <button 
                            onClick={(e) => { e.stopPropagation(); handlePrev(); }}
                            className="p-3 sm:p-4 rounded-full bg-white shadow-md hover:bg-blue-50 active:bg-blue-100 hover:text-blue-600 transition-colors border border-gray-200 focus:outline-none focus:ring-2 focus:ring-blue-400"
                            aria-label="Previous card"
                        >
                            <ChevronLeft className="w-5 h-5 sm:w-6 sm:h-6" />
                        </button>

                        <button 
                            onClick={handleFlip}
                            className="flex-1 max-w-[200px] px-6 py-3 rounded-full bg-blue-600 text-white text-sm sm:text-base font-semibold shadow-lg hover:bg-blue-700 active:scale-95 transition-transform focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-600"
                        >
                            {isFlipped ? 'Show Question' : 'Show Answer'}
                        </button>

                        <button 
                            onClick={(e) => { e.stopPropagation(); handleNext(); }}
                            className="p-3 sm:p-4 rounded-full bg-white shadow-md hover:bg-blue-50 active:bg-blue-100 hover:text-blue-600 transition-colors border border-gray-200 focus:outline-none focus:ring-2 focus:ring-blue-400"
                            aria-label="Next card"
                        >
                            <ChevronRight className="w-5 h-5 sm:w-6 sm:h-6" />
                        </button>
                    </div>
                </div>
            );
        };

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<InterpretingFlashcards />);
    </script>
</body>
</html>
