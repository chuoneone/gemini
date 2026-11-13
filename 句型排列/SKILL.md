---
name: 句型排列
description: |
  貼上句子,做成UDL的句型重組互動網頁
  
  
---
【角色設定】
你是一位擅長教育科技的前端開發專家，專門為特教學生製作「句子重組」互動網頁（單一 HTML 檔，可直接開啟執行）。
請嚴格依照使用者給你的內容出題，不可以自己增加或減少內容。

【製作方式（最重要）】
1. 以最下方「範本程式碼」為底，完整複製。
2. 只替換 /*QUIZ_DATA*/ 與 /*END_QUIZ_DATA*/ 之間的 QUIZ 物件（題目資料）。
3. 其餘 HTML、CSS、JavaScript 一字不改：不要重寫、不要優化、不要加新功能、不要刪功能。
4. 最後輸出完整的 HTML。

【QUIZ 資料格式】
- title：頁面標題（例如「Unit 3 祈使句重組」）。
- timeLimit：倒數秒數，預設 420（7 分鐘），使用者有指定才改。
- dictionary：英文題才需要，列出每個英文字卡的中文意思（滑鼠移到字卡上會顯示）；國文題留空 {}。
- exercises：題目陣列，每題包含
  - id：每題不重複（q1、q2…）
  - prompt：題號＋中文意思（國文題寫白話翻譯），例如「1. 請小心。」
  - hint：解題訣竅，一句短的規則提示，不可直接給答案；沒有就省略這個欄位
  - answer：完整正確句子
  - chunks：拆好的字塊

【切字塊規則】
1. chunks 要「依正確順序」排列，依序拼回去必須剛好等於 answer（英文用空格相接、標點前不空格；中文直接相連）。網頁會自動打亂順序。
2. 標點符號一律獨立成一塊（. , ? ! 以及中文 ，。？！、；：），網頁會自動先填好，學生只排字詞。
3. 英文固定搭配可以合成一塊（如 look at、to each other、your book）；國文依詞語拆分（如 ["予獨愛", "蓮之", "出淤泥而不染"]）。
4. 一題只放一個句子。若原本是「問句＋答句」，請拆成兩題。
5. 輸出前逐題自我檢查：chunks 依序拼回去 = answer。

【網頁效果（範本已內建，不用另外寫）】
- 開始畫面 → 一次顯示一題；頂部有「上一句／第幾題／倒數計時／下一句」。
- 點單字卡放進底線格子，點格子裡的字退回；不用拖曳。
- 檢查答案時每格各自變綠（對）或紅（錯），要訂正完才能前進；答對後「檢查答案」變成「下一句」。
- 🔊 播放整句：中文用 zh-TW、英文用 en-US，標點不發音；點字卡也會唸出該字。
- 以「第一次就答對」的比例計 100 分，全部完成或時間到跳出成績與彩帶。

【國文題的 QUIZ 範例（同一份範本，只換這段資料）】
{
  "title": "愛蓮說 句子重組",
  "timeLimit": 420,
  "dictionary": {},
  "exercises": [
    {
      "id": "q1",
      "prompt": "1. 水上、陸地上各種草木的花朵，值得喜愛的非常多。",
      "answer": "水陸草木之花，可愛者甚蕃。",
      "chunks": [
        "水陸",
        "草木之花",
        "，",
        "可愛者",
        "甚蕃",
        "。"
      ]
    },
    {
      "id": "q2",
      "prompt": "2. 蓮花，是花之中的品德高尚的君子。",
      "answer": "蓮，花之君子者也。",
      "chunks": [
        "蓮",
        "，",
        "花之",
        "君子者也",
        "。"
      ]
    }
  ]
}

【範本程式碼（英文範例）】
<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>句型排列 | 文法重組練習</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Nunito:wght@600;700;800&family=Noto+Sans+TC:wght@400;500;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary-color: #0f766e;
            --secondary-color: #3b82f6;
            --accent-color: #f59e0b;
            --success-color: #10b981;
            --error-color: #ef4444;
        }
        body {
            font-family: 'Nunito', 'Noto Sans TC', sans-serif;
            background-color: #f3f4f6;
            background-image: radial-gradient(#e5e7eb 1px, transparent 1px);
            background-size: 20px 20px;
            color: #1f2937;
            -webkit-tap-highlight-color: transparent;
        }
        .exercise-card {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border-radius: 16px;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
            border: 1px solid rgba(255, 255, 255, 0.5);
            transition: transform 0.2s ease, box-shadow 0.2s ease;
        }
        .word-item {
            display: inline-block;
            font-family: 'Nunito', sans-serif;
            padding: 0.65rem 1.25rem;
            background: white;
            border: 2px solid #e5e7eb;
            border-radius: 12px;
            color: #374151;
            font-weight: 700;
            cursor: pointer;
            user-select: none;
            transition: all 0.15s ease;
            box-shadow: 0 2px 0 #e5e7eb;
            font-size: 1.25rem;
            margin: 6px;
            touch-action: manipulation;
        }
        .word-item:active { transform: translateY(2px); box-shadow: none; background-color: #f0fdfa; }
        .word-item:hover { border-color: var(--secondary-color); color: var(--secondary-color); }
        .drop-zone {
            min-height: 64px;
            background-color: #f9fafb;
            border: 2px dashed #cbd5e1;
            border-radius: 12px;
            transition: all 0.3s ease;
            display: flex;
            flex-wrap: wrap;
            gap: 8px;
            align-items: center;
            padding: 12px;
        }
        .drop-zone:empty::before { content: '點擊下方單字卡片來組句'; color: #94a3b8; font-size: 0.9rem; width: 100%; text-align: center; padding: 10px; }
        .drop-zone.correct { background-color: #ecfdf5; border-color: var(--success-color); border-style: solid; }
        .drop-zone.incorrect { background-color: #fef2f2; border-color: var(--error-color); border-style: solid; animation: shake 0.5s cubic-bezier(.36,.07,.19,.97) both; }
        /* ===== 填空式重組：空格是底線，放進去的字像一般文字，標點緊貼前一個字 ===== */
        .drop-zone.slot-zone { background: transparent; border: none; padding: 8px 2px 4px; gap: 18px 0; align-items: flex-end; }
        .drop-zone.slot-zone.correct, .drop-zone.slot-zone.incorrect { background: transparent; border: none; }
        .slot {
            display: inline-flex; align-items: flex-end; justify-content: center;
            min-width: 4.6rem; height: 2.9rem; margin: 0 0.32rem; padding: 0 0.15rem;
            border-bottom: 2.5px solid #94a3b8;
            transition: background 0.15s, border-color 0.15s;
        }
        .slot:not(:empty) { min-width: 0; border-bottom-color: #cbd5e1; }
        .slot.locked { min-width: 0; margin-left: -0.32rem; padding: 0; border-bottom: none; }
        .slot .word-item, .slot .word-item:hover, .slot .word-item:active {
            margin: 0; padding: 0 0.05rem; background: transparent; border: none; border-radius: 0;
            box-shadow: none; transform: none; font-size: 1.6rem; line-height: 1.25; color: #1f2937;
        }
        .slot .word-item:hover { color: var(--secondary-color); }
        .word-item.locked { cursor: default; pointer-events: none; }
        .slot:has(.word-item.ok) { border-bottom-color: var(--success-color); }
        .slot:has(.word-item.bad) { border-bottom-color: var(--error-color); background: #fef2f2; }
        .slot .word-item.ok { color: #047857; }
        .slot .word-item.bad { color: #dc2626; }
        @media (max-width: 640px) {
            .drop-zone.slot-zone { gap: 8px 0; }
            .slot { min-width: 3.6rem; height: 2.4rem; margin: 0 0.25rem; }
            .slot.locked { margin-left: -0.25rem; }
            .slot .word-item, .slot .word-item:hover, .slot .word-item:active { font-size: 1.3rem; }
        }
        .match-prompt svg { max-width: 7rem; height: auto; }
        .match-card .drop-zone:empty::before { content: '把正確單字放到這裡'; }
        .match-card .drop-zone { justify-content: center; }
        @keyframes shake {
            10%, 90% { transform: translate3d(-1px, 0, 0); }
            20%, 80% { transform: translate3d(2px, 0, 0); }
            30%, 50%, 70% { transform: translate3d(-4px, 0, 0); }
            40%, 60% { transform: translate3d(4px, 0, 0); }
        }
        .btn-action { transition: all 0.2s; }
        .btn-action:active { transform: scale(0.95); }
        .speaker-btn {
            display: inline-flex; align-items: center; justify-content: center;
            width: 40px; height: 40px; border-radius: 50%;
            background-color: #ccfbf1; color: var(--primary-color);
            margin-left: 12px; cursor: pointer; vertical-align: middle;
        }
        .speaker-btn:hover { background-color: var(--primary-color); color: white; }
        #tooltip {
            position: fixed; pointer-events: none; z-index: 1000;
            background: #1f2937; color: white; padding: 6px 12px;
            border-radius: 6px; font-size: 0.875rem; display: none;
            transform: translate(-50%, -120%); white-space: nowrap; box-shadow: 0 4px 6px rgba(0,0,0,0.1);
        }
        #tooltip::after {
            content: ''; position: absolute; top: 100%; left: 50%;
            margin-left: -5px; border-width: 5px; border-style: solid;
            border-color: #1f2937 transparent transparent transparent;
        }
        @media (hover: none) {
            #tooltip { display: none !important; }
        }
        @media (max-width: 640px) {
            .word-item {
                font-size: 1.05rem;
                padding: 0.5rem 0.85rem;
                margin: 4px;
                border-radius: 10px;
            }
            .drop-zone {
                min-height: 56px;
                padding: 8px;
                gap: 6px;
            }
            .exercise-card {
                padding: 1.25rem 1rem !important;
            }
            .speaker-btn {
                width: 36px;
                height: 36px;
                margin-left: 8px;
            }
        }
        .checkmark-wrapper { width: 120px; height: 120px; margin: 0 auto; }
        .checkmark {
            width: 120px; height: 120px; border-radius: 50%; display: block;
            stroke-width: 4; stroke: #fff; stroke-miterlimit: 10;
            box-shadow: inset 0px 0px 0px #10b981;
            animation: fill .4s ease-in-out .4s forwards, scale .3s ease-in-out .9s both;
        }
        .checkmark__check {
            transform-origin: 50% 50%; stroke-dasharray: 48; stroke-dashoffset: 48;
            animation: stroke 0.3s cubic-bezier(0.65, 0, 0.45, 1) 0.8s forwards;
        }
        @keyframes stroke { 100% { stroke-dashoffset: 0; } }
        @keyframes scale { 0%, 100% { transform: none; } 50% { transform: scale3d(1.1, 1.1, 1); } }
        @keyframes fill { 100% { box-shadow: inset 0px 0px 0px 80px #10b981; } }
        .confetti { position: absolute; width: 10px; height: 10px; background-color: #f00; animation: confetti-fall 3s linear forwards; }
        @keyframes confetti-fall {
            0% { transform: translateY(-10vh) rotate(0deg); opacity: 1; }
            100% { transform: translateY(100vh) rotate(720deg); opacity: 0; }
        }
    </style>
</head>
<body class="flex flex-col min-h-screen">

    <div id="tooltip"></div>

    <!-- Start & Challenge Modal -->
    <div id="start-overlay" class="fixed inset-0 z-50 flex items-center justify-center bg-black/60 backdrop-blur-sm">
        <div class="bg-white p-8 rounded-3xl shadow-2xl text-center max-w-sm w-full mx-4">
            <h2 id="start-modal-title" class="text-2xl font-black text-gray-800 mb-4">文法重組挑戰</h2>
            <p class="text-gray-500 mb-6" id="start-modal-desc">點擊下方開始按鈕，開始限時重組挑戰！</p>
            <button onclick="startGame()" class="w-full bg-teal-600 text-white text-lg px-6 py-3 rounded-xl font-bold hover:bg-teal-700 transition shadow-lg transform hover:scale-105 active:scale-95">開始挑戰</button>
        </div>
    </div>

    <!-- Completion Modal -->
    <div id="completion-overlay" class="fixed inset-0 z-50 flex items-center justify-center bg-black/60 backdrop-blur-sm hidden opacity-0 transition-opacity duration-500 p-4">
        <div class="bg-white p-6 md:p-8 rounded-3xl shadow-2xl text-center transform scale-90 transition-transform duration-500 max-w-md w-full mx-auto relative overflow-y-auto max-h-[92vh]" id="completion-modal">
            <div class="checkmark-wrapper mb-4">
                <svg class="checkmark" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 52 52">
                    <circle class="checkmark__circle" cx="26" cy="26" r="25" fill="none"/>
                    <path class="checkmark__check" fill="none" d="M14.1 27.2l7.1 7.2 16.7-16.8"/>
                </svg>
            </div>
            <h2 class="text-2xl md:text-3xl font-extrabold text-gray-800 mb-1 animate-bounce">挑戰完成！</h2>
            <p class="text-gray-600 text-sm md:text-base mb-4" id="completion-desc">你已完成所有文法練習！</p>
            
            <div class="space-y-2 mb-4 bg-slate-50 p-3.5 rounded-xl text-sm md:text-base">
                <div class="flex justify-between font-bold text-slate-700">
                    <span>答對題數：</span>
                    <span id="final-correct">0/0</span>
                </div>
                <div class="flex justify-between font-bold text-slate-700">
                    <span>挑戰成績：</span>
                    <span id="final-score" class="text-teal-600 text-lg md:text-xl">0 分</span>
                </div>
            </div>

            <button onclick="closeOverlay()" class="w-full bg-teal-600 text-white text-lg px-6 py-3 rounded-xl font-bold hover:bg-teal-700 transition shadow-lg transform hover:scale-102 active:scale-95">關閉並儲存</button>
        </div>
    </div>

    <!-- Top Sticky Navigation Bar -->
    <header class="w-full bg-white border-b border-slate-200 px-3 sm:px-4 py-2.5 sm:py-3 flex items-center sticky top-0 z-30 justify-between gap-1.5 sm:gap-2">
        <div class="flex items-center gap-1.5 sm:gap-2 shrink-0">
            <button id="prev-btn" onclick="prevQuestion()" class="text-teal-600 hover:text-teal-800 text-xs sm:text-sm font-bold bg-teal-50 hover:bg-teal-100 border border-teal-100 px-2.5 sm:px-3 py-1.5 rounded-xl transition shadow-sm disabled:opacity-40 disabled:cursor-not-allowed shrink-0 flex items-center gap-1">
                <span>←</span><span>上一句</span>
            </button>
        </div>
        <div class="text-center px-1 hidden md:block flex-1 min-w-0">
            <h1 class="text-lg md:text-xl font-black text-teal-800 truncate" id="quiz-title">文法重組挑戰</h1>
        </div>
        <div class="flex items-center justify-end gap-1.5 sm:gap-2 text-xs sm:text-sm font-bold shrink-0">
            <span id="question-nav-label" class="text-teal-700 bg-teal-50 border border-teal-100 px-2 sm:px-2.5 py-1.5 rounded-xl whitespace-nowrap shrink-0 text-center font-bold hidden leading-none">1 / 0</span>
            <span class="text-amber-700 bg-amber-50 border border-amber-100 px-2 sm:px-2.5 py-1.5 rounded-xl shadow-sm whitespace-nowrap shrink-0 leading-none">⏱ <span id="timer-display">07:00</span></span>
            <button id="next-btn" onclick="nextQuestion()" class="text-teal-600 hover:text-teal-800 text-xs sm:text-sm font-bold bg-teal-50 hover:bg-teal-100 border border-teal-100 px-2.5 sm:px-3 py-1.5 rounded-xl transition shadow-sm disabled:opacity-40 disabled:cursor-not-allowed shrink-0 flex items-center gap-1">
                <span>下一句</span><span>→</span>
            </button>
        </div>
    </header>

    <div class="max-w-3xl mx-auto w-full flex-1 p-4 pb-16">

        <!-- Exercise Sections -->
        <div id="exercise-sections">
            <div id="section-1-wrapper" class="mb-12 hidden">
                <div id="container-section-1" class="space-y-6"></div>
            </div>

            <div id="section-2-wrapper" class="mb-12 hidden">
                <div class="flex items-center gap-3 mb-6">
                    <span class="flex items-center justify-center w-8 h-8 rounded-full bg-teal-100 text-teal-600 font-bold text-sm">2</span>
                    <h2 class="text-xl font-bold text-gray-800" id="section-2-title">請回答相關問題</h2>
                </div>
                <div id="container-section-2" class="space-y-6"></div>
            </div>
        </div>
    </div>


    <script>
        // ===== 題目資料：製作新練習時只改這一段 =====
        const QUIZ = /*QUIZ_DATA*/{
  "title": "Unit 3 祈使句重組",
  "timeLimit": 420,
  "dictionary": {
    "Be": "是（be 動詞原形）",
    "nice": "友善的",
    "to each other": "對彼此",
    "please": "請",
    "Helen": "海倫",
    "open": "打開",
    "your book": "你的書",
    "careful": "小心的"
  },
  "exercises": [
    {
      "id": "q1",
      "prompt": "1. 請彼此好好相處。",
      "hint": "please 放句尾 ➜ 前面加逗號",
      "answer": "Be nice to each other, please.",
      "chunks": [
        "Be",
        "nice",
        "to each other",
        ",",
        "please",
        "."
      ]
    },
    {
      "id": "q2",
      "prompt": "2. 海倫，請打開你的書。",
      "hint": "稱呼放最前面 ➜ 後面加逗號",
      "answer": "Helen, please open your book.",
      "chunks": [
        "Helen",
        ",",
        "please",
        "open",
        "your book",
        "."
      ]
    },
    {
      "id": "q3",
      "prompt": "3. 請小心。",
      "hint": "be 動詞的原形是 Be",
      "answer": "Be careful, please.",
      "chunks": [
        "Be",
        "careful",
        ",",
        "please",
        "."
      ]
    }
  ]
}/*END_QUIZ_DATA*/;
    </script>

    <script>
        
        let quizData = null;
        let dictionary = {};
        let exercises = [];
        let completedCount = 0;
        let firstTryCorrectCount = 0;
        let totalCount = 0;
        let currentQuestionIndex = 0;
        let timerSeconds = (QUIZ && QUIZ.timeLimit) || 420;
        let timerInterval = null;
        let isCompleted = false;

        const synth = window.speechSynthesis;
        const isCJK = (t) => /[\u3400-\u9fff]/.test(t || '');
        const PUNCT_RE = /^[.,?!;:，。？！；：、「」『』]+$/;
        // 把字塊拼成句子：英文用空白隔開、標點前不留空白；中文直接相連
        function joinChunks(arr) {
            if (arr.some(isCJK)) return arr.join('').replace(/\s+/g, '');
            return arr.join(' ')
                .replace(/\s+([,.?!;:])/g, '$1')
                .replace(/\s+(’t|'t)/g, '$1')
                .replace(/\.\./g, '.')
                .replace(/\s+/g, ' ')
                .trim();
        }

        if (QUIZ && QUIZ.exercises) {
            quizData = QUIZ;
            document.title = quizData.title;
            document.getElementById('quiz-title').innerText = quizData.title;
            document.getElementById('start-modal-title').innerText = quizData.title;
            
            dictionary = quizData.dictionary || {};
            exercises = quizData.exercises || [];
            exercises.forEach(ex => {
                if (ex.parts && ex.parts.length > 0 && !ex.chunks) {
                    ex.chunks = ex.parts[0].chunks;
                    ex.answer = ex.parts[0].answer;
                }
            });
            totalCount = exercises.length;
            const fmt = (t) => `${String(Math.floor(t / 60)).padStart(2, '0')}:${String(t % 60).padStart(2, '0')}`;
            document.getElementById('timer-display').innerText = fmt(timerSeconds);
            document.getElementById('start-modal-desc').innerText = `點擊下方開始按鈕，開始限時 ${Math.round(timerSeconds / 60)} 分鐘的重組挑戰！`;
        } else {
            document.getElementById('quiz-title').innerText = "找不到測驗資料";
            document.getElementById('quiz-subtitle').innerText = "請在 QUIZ 填入題目資料。";
        }

        function speak(text, rate = 0.7) {
            if (!text || !window.speechSynthesis) return;
            try {
                window.speechSynthesis.cancel();
                if (window.speechSynthesis.paused) {
                    window.speechSynthesis.resume();
                }
                const cleanText = text.replace(/[.?。？！，、；：「」]/g, ' ').replace(/(\d+)\s?a\.m\./gi, '$1 A M').replace(/(\d+)\s?p\.m\./gi, '$1 P M').trim();
                if (!cleanText) return;

                const utterThis = new SpeechSynthesisUtterance(cleanText);
                const isZh = /[\u4e00-\u9fa5]/.test(cleanText);
                utterThis.lang = isZh ? 'zh-TW' : 'en-US';
                utterThis.rate = rate;

                window._lastUtterance = utterThis;
                utterThis.onend = () => { window._lastUtterance = null; };
                utterThis.onerror = () => { window._lastUtterance = null; };

                setTimeout(() => {
                    window.speechSynthesis.speak(utterThis);
                }, 10);
            } catch (e) {
                console.error('TTS error:', e);
            }
        }

        function startGame() {
            document.getElementById('start-overlay').classList.add('hidden');
            if (exercises.length === 0) return;
            renderApp();
            showActiveQuestion();
            startTimer();
        }

        // 答對這一題才能按「下一題」（學生要把錯的格子訂正完）
        function updateNextButton() {
            const nextBtn = document.getElementById('next-btn');
            if (!nextBtn || exercises.length === 0) return;
            const ex = exercises[currentQuestionIndex];
            const zone = ex && document.getElementById(`zone-${ex.id}`);
            const solved = !!(zone && zone.dataset.completed);
            nextBtn.disabled = (currentQuestionIndex === totalCount - 1) || !solved;
            nextBtn.title = solved ? '' : '要先答對這一題才能前進喔';
        }

        function showActiveQuestion() {
            document.querySelectorAll('.exercise-card').forEach(card => card.classList.add('hidden'));
            document.getElementById('section-1-wrapper').classList.add('hidden');
            document.getElementById('section-2-wrapper').classList.add('hidden');

            if (exercises.length === 0) return;

            const currentEx = exercises[currentQuestionIndex];
            const card = document.getElementById(`card-${currentEx.id}`);
            if (card) {
                card.classList.remove('hidden');
            }

            if (!currentEx.section || currentEx.section === 1) {
                document.getElementById('section-1-wrapper').classList.remove('hidden');
            } else {
                document.getElementById('section-2-wrapper').classList.remove('hidden');
            }

            document.getElementById('prev-btn').disabled = (currentQuestionIndex === 0);
            updateNextButton();

            // 同步更新導覽列計數器
            const navLabel = document.getElementById('question-nav-label');
            navLabel.innerHTML = `<span class="hidden sm:inline">第 </span>${currentQuestionIndex + 1} / ${totalCount}<span class="hidden sm:inline"> 題</span>`;
            navLabel.classList.remove('hidden');
        }

        function prevQuestion() {
            if (currentQuestionIndex > 0) {
                currentQuestionIndex--;
                showActiveQuestion();
            }
        }

        function nextQuestion() {
            if (document.getElementById('next-btn').disabled) return;
            if (currentQuestionIndex < totalCount - 1) {
                currentQuestionIndex++;
                showActiveQuestion();
            }
        }

        function startTimer() {
            timerInterval = setInterval(() => {
                if (timerSeconds <= 0) {
                    clearInterval(timerInterval);
                    finishGame();
                } else {
                    timerSeconds--;
                    const mins = Math.floor(timerSeconds / 60).toString().padStart(2, '0');
                    const secs = (timerSeconds % 60).toString().padStart(2, '0');
                    document.getElementById('timer-display').innerText = `${mins}:${secs}`;
                }
            }, 1000);
        }

        function renderApp() {
            const hasS1 = exercises.some(ex => ex.section === 1 || !ex.section);
            const hasS2 = exercises.some(ex => ex.section && ex.section !== 1);
            
            if (hasS1) document.getElementById('section-1-wrapper').classList.remove('hidden');
            if (hasS2) document.getElementById('section-2-wrapper').classList.remove('hidden');

            const s1El = document.getElementById('container-section-1');
            const s2El = document.getElementById('container-section-2');

            exercises.forEach((ex) => {
                const card = document.createElement('div');
                card.className = 'exercise-card p-5 sm:p-6';
                card.id = `card-${ex.id}`;
                card.dataset.id = ex.id;

                const isMatch = ex.type === 'match';
                let rawPrompt = ex.prompt || '';
                let hintText = ex.hint || ex.timeClue || '';

                // 移除「重組」、「請重組:」、「請重組」等字樣
                let promptText = rawPrompt
                    .replace(/^(?:\d+\.\s*)?重組\s*/, (match) => {
                        const numMatch = match.match(/^\d+\.\s*/);
                        return numMatch ? numMatch[0] : '';
                    })
                    .replace(/^請重組[:：]?\s*/, '')
                    .replace(/^(?:\d+\.\s*)?請重組[:：]?\s*/, (match) => {
                        const numMatch = match.match(/^\d+\.\s*/);
                        return numMatch ? numMatch[0] : '';
                    })
                    .trim();

                // 若題幹中含有括號的解題訣竅/提示（如（看到...）或 (看到...) 或 (常態事實...)），提取出來作為下方小提示
                const clueRegex = /[（\(](?:看到|提示|常態事實)[^）\)]+[）\)]/g;
                const matches = promptText.match(clueRegex);
                if (matches) {
                    if (!hintText) {
                        hintText = matches.map(m => m.replace(/^[（\(]|[）\)]$/g, '')).join('；');
                    }
                    promptText = promptText.replace(clueRegex, '').trim();
                }

                let promptHtml = '';
                if (isMatch) {
                    promptHtml = `<div class="match-prompt w-full flex justify-center">${promptText}</div>`;
                } else {
                    const speechText = promptText.replace(/^\d+\.\s*/, '').replace(/[「」]/g, '').trim();
                    promptHtml = `
                    <div class="flex flex-col w-full">
                        <div class="flex items-center flex-wrap">
                            <span class="text-2xl md:text-3xl font-extrabold text-gray-800 leading-normal">${promptText}</span>
                            <button class="speaker-btn speak-prompt ml-3 shrink-0" data-text="${(ex.answer || speechText).replace(/"/g, '&quot;')}" title="聽完整句子發音">
                                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor" class="w-6 h-6 pointer-events-none">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M19.114 5.636a9 9 0 010 12.728M16.463 8.288a5.25 5.25 0 010 7.424M6.75 8.25l4.72-4.72a.75.75 0 011.28.53v15.88a.75.75 0 01-1.28.53l-4.72-4.72H4.51c-.88 0-1.704-.507-1.938-1.354A9.01 9.01 0 012.25 12c0-.83.112-1.633.322-2.396C2.806 8.756 3.63 8.25 4.51 8.25H6.75z" />
                                </svg>
                            </button>
                        </div>
                        ${hintText ? `
                        <div class="text-xs sm:text-sm font-bold text-amber-800 bg-amber-50 border border-amber-200/80 px-2.5 py-1 rounded-lg mt-2 inline-flex items-center gap-1.5 self-start shadow-sm">
                            <span>💡 解題訣竅：</span><span>${hintText}</span>
                        </div>` : ''}
                    </div>`;
                }

                const bankWords = isMatch ? (ex.choices || ex.chunks) : ex.chunks;
                // 格子模式：字塊順序與答案一致時，下方改為一格一格的填空；標點預設填好
                const normJoin = joinChunks;
                const useSlots = !isMatch && Array.isArray(ex.chunks) && ex.chunks.length > 0 &&
                    normJoin(ex.chunks) === (isCJK(ex.answer) ? (ex.answer || '').replace(/\s+/g, '') : (ex.answer || '').replace(/\s+/g, ' ').trim());
                const isPunct = (w) => PUNCT_RE.test(w);
                const shuffledWords = [...(useSlots ? bankWords.filter(w => !isPunct(w)) : bankWords)].sort(() => Math.random() - 0.5);
                if (isMatch) card.classList.add('match-card');
                if (useSlots) card.dataset.mode = 'slots';
                const zoneHtml = useSlots
                    ? `<div class="slot-help text-xs sm:text-sm text-gray-400 font-bold mb-1">點一下單字卡，放進下面的格子；點格子裡的字可以拿回來</div>
                    <div class="drop-zone slot-zone mb-3" id="zone-${ex.id}">${ex.chunks.map((w, i) => isPunct(w)
                        ? `<div class="slot locked" data-n="${i + 1}"><div class="word-item locked">${w}</div></div>`
                        : `<div class="slot" data-n="${i + 1}"></div>`).join('')}</div>`
                    : `<div class="drop-zone mb-3" id="zone-${ex.id}"></div>`;

                const imgHtml = ex.image ? `<img src="${ex.image}" alt="" class="w-full max-w-[260px] aspect-square object-cover rounded-2xl border-4 border-white shadow-md mb-4">` : '';
                card.innerHTML = `
                    ${imgHtml}
                    <div class="mb-4 flex items-center flex-wrap ${isMatch ? 'justify-center' : ''}">${promptHtml}</div>
                    <div class="word-bank flex flex-wrap gap-2 mb-4 ${isMatch ? 'justify-center' : ''}" id="bank-${ex.id}">
                        ${shuffledWords.map(word => `<div class="word-item">${word}</div>`).join('')}
                    </div>
                    ${zoneHtml}
                    <div class="flex items-center gap-2 sm:gap-3 mt-3">
                        <button class="btn-action check-btn flex-1 sm:flex-initial bg-teal-600 hover:bg-teal-700 text-white font-bold py-2.5 px-4 sm:px-6 rounded-xl shadow transition-colors text-sm sm:text-base whitespace-nowrap text-center" data-id="${ex.id}">檢查答案</button>
                        <button class="btn-action reset-btn bg-white hover:bg-gray-50 text-gray-600 border border-gray-300 font-bold py-2.5 px-3.5 sm:px-4 rounded-xl transition-colors text-sm sm:text-base shrink-0 whitespace-nowrap" data-id="${ex.id}">重置</button>
                    </div>
                    <div class="feedback mt-2.5 min-h-[1.5rem] text-sm font-bold"></div>
                `;

                if (!ex.section || ex.section === 1) s1El.appendChild(card);
                else s2El.appendChild(card);
            });
        }

        document.body.addEventListener('click', (e) => {
            const target = e.target;
            if (target.classList.contains('word-item')) {
                if (tooltip) tooltip.style.display = 'none';
                const card = target.closest('.exercise-card');
                if (!card) return;
                if (card.dataset.mode === 'slots') {
                    // 格子模式：點字庫的字 → 放進最左邊的空格；點格子裡的字 → 退回字庫
                    if (target.classList.contains('locked')) return;
                    speak(target.innerText, 0.7);
                    const slotBank = card.querySelector('.word-bank');
                    if (target.parentElement === slotBank) {
                        const emptySlot = Array.from(card.querySelectorAll('.slot:not(.locked)')).find(sl => !sl.firstElementChild);
                        if (emptySlot) emptySlot.appendChild(target);
                    } else {
                        slotBank.appendChild(target);
                    }
                    const slotFeedback = card.querySelector('.feedback');
                    slotFeedback.textContent = '';
                    slotFeedback.className = 'feedback mt-2.5 min-h-[1.5rem] text-sm font-bold';
                    card.querySelector('.drop-zone').classList.remove('correct', 'incorrect');
                    card.querySelectorAll('.word-item.ok, .word-item.bad').forEach(c => c.classList.remove('ok', 'bad'));
                    setCheckBtn(card, false);
                    return;
                }
                const exId = card.dataset.id;
                const bank = document.getElementById(`bank-${exId}`);
                const zone = document.getElementById(`zone-${exId}`);

                speak(target.innerText, 0.7);

                const exData = exercises.find(e => e.id === exId);
                if (target.parentElement === bank) {
                    if (exData?.type === 'match' && zone.children.length > 0) {
                        bank.appendChild(zone.firstElementChild);
                    }
                    zone.appendChild(target);
                } else {
                    bank.appendChild(target);
                }

                const feedbackEl = card.querySelector('.feedback');
                feedbackEl.textContent = '';
                feedbackEl.className = 'feedback mt-2.5 min-h-[1.5rem] text-sm font-bold';
                zone.classList.remove('correct', 'incorrect');
                setCheckBtn(card, false);
                return;
            }
            if (target.classList.contains('check-btn')) {
                if (target.classList.contains('go-next')) { nextQuestion(); return; }
                checkAnswer(target.dataset.id); return;
            }
            if (target.classList.contains('reset-btn')) { resetQuestion(target.dataset.id); return; }
            if (target.closest('.hint-btn')) { speak(target.closest('.hint-btn').dataset.text, 0.55); return; }
            if (target.closest('.speak-prompt')) { speak(target.closest('.speak-prompt').dataset.text, 0.7); return; }
        });

        // 答對（含訂正完）後「檢查答案」變成「下一句」，直接前往下一題
        function setCheckBtn(card, toNext) {
            const btn = card && card.querySelector('.check-btn');
            if (!btn) return;
            btn.classList.toggle('go-next', toNext);
            btn.textContent = toNext ? '下一句 →' : '檢查答案';
        }

        function checkAnswer(id) {
            const zone = document.getElementById(`zone-${id}`);
            const card = document.getElementById(`card-${id}`);
            const feedbackEl = card.querySelector('.feedback');
            const exData = exercises.find(e => e.id === id);
            let rawWords;
            if (zone.classList.contains('slot-zone')) {
                const slotTexts = Array.from(zone.querySelectorAll('.slot')).map(sl => sl.firstElementChild ? sl.firstElementChild.innerText : null);
                if (slotTexts.some(t => t === null)) {
                    feedbackEl.textContent = '✏️ 還有空格沒填完喔！';
                    feedbackEl.className = 'feedback mt-3 h-6 text-sm font-bold text-amber-600';
                    return;
                }
                rawWords = slotTexts;
            } else {
                rawWords = Array.from(zone.children).map(el => el.innerText);
            }
            
            const normalizedUser = joinChunks(rawWords);
            const normalizedAnswer = isCJK(exData.answer)
                ? exData.answer.replace(/\s+/g, '')
                : exData.answer.replace(/\s+/g, ' ').replace(/\.\./g, '.').trim();

            // 追蹤首次作答（首答正確率）
            if (!zone.dataset.hasAttempted) {
                zone.dataset.hasAttempted = 'true';
                if (normalizedUser === normalizedAnswer) {
                    zone.dataset.firstTryCorrect = 'true';
                    firstTryCorrectCount++;
                } else {
                    zone.dataset.firstTryCorrect = 'false';
                }
            }

            // 格子模式：每一格各自判斷，放對的變綠、放錯的變紅
            if (zone.classList.contains('slot-zone')) {
                zone.querySelectorAll('.slot').forEach((sl, i) => {
                    const chip = sl.firstElementChild;
                    if (!chip || chip.classList.contains('locked')) return;
                    chip.classList.remove('ok', 'bad');
                    const right = normalizedUser === normalizedAnswer || chip.innerText.trim() === String(exData.chunks[i]).trim();
                    chip.classList.add(right ? 'ok' : 'bad');
                });
            }

            if (normalizedUser === normalizedAnswer) {
                feedbackEl.textContent = zone.dataset.firstTryCorrect === 'true' ? '🎉 答對了！太棒了！' : '🎉 修正成功！答對了！';
                feedbackEl.className = 'feedback mt-3 h-6 text-sm font-bold text-green-600';
                zone.classList.remove('incorrect');
                zone.classList.add('correct');
                
                if (!zone.dataset.completed) {
                    zone.dataset.completed = 'true';
                    updateProgress(1);
                }
                updateNextButton();
                if (exercises.indexOf(exData) < totalCount - 1) setCheckBtn(card, true);
            } else {
                feedbackEl.textContent = '🤔 再試試看！';
                feedbackEl.className = 'feedback mt-3 h-6 text-sm font-bold text-red-500';
                zone.classList.remove('correct');
                zone.classList.add('incorrect');
                zone.style.animation = 'none';
                zone.offsetHeight; 
                zone.style.animation = null; 
            }
        }

        function resetQuestion(id) {
            const bank = document.getElementById(`bank-${id}`);
            const zone = document.getElementById(`zone-${id}`);
            const card = document.getElementById(`card-${id}`);
            const feedbackEl = card.querySelector('.feedback');
            
            if (zone.classList.contains('slot-zone')) {
                zone.querySelectorAll('.slot:not(.locked) > .word-item').forEach(chip => { chip.classList.remove('ok', 'bad'); bank.appendChild(chip); });
            } else {
                Array.from(zone.children).forEach(child => bank.appendChild(child));
            }
            const words = Array.from(bank.children);
            words.sort(() => Math.random() - 0.5);
            words.forEach(w => bank.appendChild(w));

            feedbackEl.textContent = '';
            zone.classList.remove('correct', 'incorrect');
            setCheckBtn(card, false);
            if (zone.dataset.completed) {
                delete zone.dataset.completed;
                updateProgress(-1);
            }
            updateNextButton();
        }

        function updateProgress(change) {
            completedCount += change;
            completedCount = Math.max(0, Math.min(completedCount, totalCount));
            
            if (completedCount === totalCount) {
                finishGame();
            }
        }

        function finishGame() {
            if (isCompleted) return;
            isCompleted = true;
            clearInterval(timerInterval);

            const finalScore = Math.round((firstTryCorrectCount / totalCount) * 100);

            document.getElementById('final-correct').innerText = `${firstTryCorrectCount}/${totalCount} (一試即中)`;
            document.getElementById('final-score').innerText = `${finalScore} 分`;

            const overlay = document.getElementById('completion-overlay');
            const modal = document.getElementById('completion-modal');
            overlay.classList.remove('hidden');
            setTimeout(() => {
                overlay.classList.remove('opacity-0');
                modal.classList.remove('scale-90');
                modal.classList.add('scale-100');
                for(let i=0; i<50; i++) { createConfetti(overlay); }
            }, 10);

            speak(exercises.some(e => isCJK(e.answer)) ? `挑戰完成！你得到 ${finalScore} 分。` : `Challenge completed! You scored ${finalScore} points.`, 0.8);
        }

        window.closeOverlay = function() {
            const overlay = document.getElementById('completion-overlay');
            const modal = document.getElementById('completion-modal');
            overlay.classList.add('opacity-0');
            modal.classList.remove('scale-100');
            modal.classList.add('scale-90');
            setTimeout(() => {
                overlay.classList.add('hidden');
                document.querySelectorAll('.confetti').forEach(c => c.remove());
                window.close();
            }, 500);
        }

        function createConfetti(container) {
            const confetti = document.createElement('div');
            confetti.classList.add('confetti');
            const colors = ['#ef4444', '#3b82f6', '#10b981', '#f59e0b', '#0d9488', '#ec4899'];
            confetti.style.backgroundColor = colors[Math.floor(Math.random() * colors.length)];
            confetti.style.left = Math.random() * 100 + 'vw';
            confetti.style.animationDuration = (Math.random() * 2 + 2) + 's';
            confetti.style.opacity = Math.random();
            container.appendChild(confetti);
        }

        const tooltip = document.getElementById('tooltip');
        document.body.addEventListener('mouseover', (e) => {
            if (e.target.classList.contains('word-item')) {
                const text = e.target.innerText.replace(/[.,?]/g, '').trim();
                const translation = dictionary[text];
                if (translation) {
                    tooltip.textContent = translation;
                    tooltip.style.display = 'block';
                    const rect = e.target.getBoundingClientRect();
                    tooltip.style.left = `${rect.left + rect.width / 2}px`;
                    tooltip.style.top = `${rect.top}px`;
                }
            }
        });
        document.body.addEventListener('mouseout', (e) => {
            if (e.target.classList.contains('word-item')) {
                tooltip.style.display = 'none';
            }
        });

        document.querySelector('body').addEventListener('submit', (e) => e.preventDefault());
    </script>
</body>
</html>
