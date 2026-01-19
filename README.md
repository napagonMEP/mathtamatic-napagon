<html lang="th" class="h-full">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>คณิตศาสตร์การเงิน ป.5</title>
  <script src="/_sdk/element_sdk.js"></script>
  <script src="https://cdn.tailwindcss.com"></script>
  <link href="https://fonts.googleapis.com/css2?family=Sarabun:wght@400;600;700&amp;display=swap" rel="stylesheet">
  <style>
    body {
      box-sizing: border-box;
      font-family: 'Sarabun', sans-serif;
    }
    
    .coin-animation {
      animation: coinFloat 2s ease-in-out infinite;
    }
    
    @keyframes coinFloat {
      0%, 100% { transform: translateY(0px); }
      50% { transform: translateY(-10px); }
    }
    
    .correct-answer {
      animation: correctPulse 0.5s ease-in-out;
    }
    
    @keyframes correctPulse {
      0%, 100% { transform: scale(1); }
      50% { transform: scale(1.05); }
    }
    
    .wrong-shake {
      animation: wrongShake 0.5s ease-in-out;
    }
    
    @keyframes wrongShake {
      0%, 100% { transform: translateX(0); }
      25% { transform: translateX(-10px); }
      75% { transform: translateX(10px); }
    }
  </style>
  <style>@view-transition { navigation: auto; }</style>
  <script src="/_sdk/data_sdk.js" type="text/javascript"></script>
 </head>
 <body class="h-full">
  <div id="app" class="h-full w-full overflow-auto"></div>
  <script>
    const defaultConfig = {
      background_color: "#FFF9E6",
      card_color: "#FFFFFF",
      primary_color: "#FF6B35",
      text_color: "#2C3E50",
      accent_color: "#4ECDC4",
      font_family: "Sarabun",
      font_size: 16,
      main_title: "คณิตศาสตร์การเงิน ป.5",
      subtitle: "ฝึกทักษะการคิดคำนวณเงิน",
      score_label: "คะแนน",
      check_button: "ตรวจคำตอบ",
      next_button: "ข้อถัดไป",
      reset_button: "เริ่มใหม่",
      creator_name: "เด็กชาย นปกรณ์ เข็มทอง",
      creator_class: "ชั้นประถมศึกษาปีที่ 5/5 สาย MEP"
    };

    // Quiz questions about money
    const questions = [
      {
        question: "ซื้อขนมปัง 3 ถุง ถุงละ 25 บาท ต้องจ่ายเงินทั้���หมดเท่าไร?",
        answer: 75,
        unit: "บาท",
        emoji: "🍞"
      },
      {
        question: "มีเงิน 500 บาท ซื้อของ 235 บาท เหลือเงินเท่าไร?",
        answer: 265,
        unit: "บาท",
        emoji: "💰"
      },
      {
        question: "ออมเงินวันละ 20 บาท ออมครบ 1 สัปดาห์ (7 วัน) จะได้เงินเท่าไร?",
        answer: 140,
        unit: "บาท",
        emoji: "🏦"
      },
      {
        question: "ซื้อดินสอ 12 แท่ง ราคารวม 60 บาท ดินสอ 1 แท่งราคาเท่าไร?",
        answer: 5,
        unit: "บาท",
        emoji: "✏️"
      },
      {
        question: "มีเหรียญ 10 บาท 8 เหรียญ มีเงินทั้งหมดเ����่าไร?",
        answer: 80,
        unit: "บาท",
        emoji: "🪙"
      },
      {
        question: "ซื้อน้ำ 4 ขวด ขวดละ 15 บาท ��ห้เงิน 100 บาท ได้เงินทอนเท่าไร?",
        answer: 40,
        unit: "บาท",
        emoji: "🧃"
      },
      {
        question: "ขายของเล่นได้ 180 บาท แบ่งให้เพื่อน 3 คน เท่าๆ กัน คนละเท่าไร?",
        answer: 60,
        unit: "บาท",
        emoji: "🎮"
      },
      {
        question: "มีธนบัตร 50 บาท 3 ใบ และเหรียญ 10 บาท 5 เหรียญ มีเงินรวมเท่าไร?",
        answer: 200,
        unit: "บาท",
        emoji: "💵"
      }
    ];

    let currentQuestion = 0;
    let score = 0;
    let answered = false;

    function shuffleQuestions() {
      for (let i = questions.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [questions[i], questions[j]] = [questions[j], questions[i]];
      }
    }

    async function onConfigChange(config) {
      const customFont = config.font_family || defaultConfig.font_family;
      const baseFontStack = 'Sarabun, sans-serif';
      const baseSize = config.font_size || defaultConfig.font_size;
      
      const backgroundColor = config.background_color || defaultConfig.background_color;
      const cardColor = config.card_color || defaultConfig.card_color;
      const primaryColor = config.primary_color || defaultConfig.primary_color;
      const textColor = config.text_color || defaultConfig.text_color;
      const accentColor = config.accent_color || defaultConfig.accent_color;
      
      const mainTitle = config.main_title || defaultConfig.main_title;
      const subtitle = config.subtitle || defaultConfig.subtitle;
      const scoreLabel = config.score_label || defaultConfig.score_label;
      const checkButton = config.check_button || defaultConfig.check_button;
      const nextButton = config.next_button || defaultConfig.next_button;
      const resetButton = config.reset_button || defaultConfig.reset_button;
      const creatorName = config.creator_name || defaultConfig.creator_name;
      const creatorClass = config.creator_class || defaultConfig.creator_class;

      const app = document.getElementById('app');
      app.style.background = backgroundColor;
      app.style.fontFamily = `${customFont}, ${baseFontStack}`;
      
      app.innerHTML = `
        <div class="min-h-full w-full flex items-center justify-center p-6">
          <div class="w-full max-w-2xl">
            <!-- Header -->
            <div class="text-center mb-8">
              <div class="coin-animation inline-block text-6xl mb-4">💰</div>
              <h1 id="main-title" class="mb-2" style="font-size: ${baseSize * 2}px; color: ${primaryColor}; font-weight: 700;">${mainTitle}</h1>
              <p id="subtitle" style="font-size: ${baseSize * 1.1}px; color: ${textColor};">${subtitle}</p>
            </div>

            <!-- Score Card -->
            <div class="mb-6 p-4 rounded-2xl text-center" style="background: ${cardColor}; box-shadow: 0 4px 6px rgba(0,0,0,0.1);">
              <div style="font-size: ${baseSize}px; color: ${textColor};">
                <span id="score-label">${scoreLabel}:</span> <span id="score" style="color: ${accentColor}; font-weight: 700; font-size: ${baseSize * 1.5}px;">${score}</span> / ${questions.length}
              </div>
            </div>

            <!-- Question Card -->
            <div class="p-8 rounded-3xl mb-6" style="background: ${cardColor}; box-shadow: 0 10px 30px rgba(0,0,0,0.15);">
              <div class="text-center mb-6">
                <span style="font-size: ${baseSize * 4}px;">${questions[currentQuestion].emoji}</span>
              </div>
              
              <div class="mb-6">
                <label class="block mb-3" style="font-size: ${baseSize * 1.3}px; color: ${textColor}; font-weight: 600;">
                  ข้อ���ี่ ${currentQuestion + 1}: ${questions[currentQuestion].question}
                </label>
                <div class="flex gap-3">
                  <input 
                    type="number" 
                    id="answer-input" 
                    class="flex-1 px-6 py-4 rounded-xl border-2 focus:outline-none focus:ring-2 transition-all"
                    style="font-size: ${baseSize * 1.2}px; border-color: ${accentColor}; color: ${textColor};"
                    placeholder="พิมพ์คำตอบ..."
                  >
                  <div class="px-6 py-4 rounded-xl flex items-center" style="background: ${backgroundColor}; font-size: ${baseSize * 1.2}px; color: ${textColor}; font-weight: 600;">
                    ${questions[currentQuestion].unit}
                  </div>
                </div>
              </div>

              <div id="feedback" class="mb-6 text-center" style="min-height: 40px; font-size: ${baseSize * 1.1}px;"></div>

              <div class="flex gap-4">
                <button 
                  id="check-btn"
                  class="flex-1 py-4 rounded-xl font-semibold transition-all transform hover:scale-105 active:scale-95"
                  style="background: ${primaryColor}; color: white; font-size: ${baseSize * 1.1}px;"
                >
                  ${checkButton}
                </button>
                <button 
                  id="next-btn"
                  class="flex-1 py-4 rounded-xl font-semibold transition-all transform hover:scale-105 active:scale-95 hidden"
                  style="background: ${accentColor}; color: white; font-size: ${baseSize * 1.1}px;"
                >
                  ${nextButton}
                </button>
                <button 
                  id="reset-btn"
                  class="flex-1 py-4 rounded-xl font-semibold transition-all transform hover:scale-105 active:scale-95 hidden"
                  style="background: ${accentColor}; color: white; font-size: ${baseSize * 1.1}px;"
                >
                  ${resetButton}
                </button>
              </div>
            </div>

            <!-- Progress -->
            <div class="text-center" style="font-size: ${baseSize * 0.9}px; color: ${textColor};">
              ข้อที่ ${currentQuestion + 1} จาก ${questions.length}
            </div>

            <!-- Creator Info -->
            <div class="mt-8 text-center p-6 rounded-2xl" style="background: ${cardColor}; box-shadow: 0 4px 6px rgba(0,0,0,0.1);">
              <div style="font-size: ${baseSize * 0.85}px; color: ${textColor}; opacity: 0.8;">
                <div class="mb-2" style="font-weight: 600; font-size: ${baseSize * 1}px;">
                  🎓 ผู้สร้างเกม
                </div>
                <div id="creator-name" style="font-size: ${baseSize * 1.1}px; font-weight: 700; color: ${primaryColor};">
                  ${creatorName}
                </div>
                <div id="creator-class" style="font-size: ${baseSize * 0.9}px; margin-top: 4px;">
                  ${creatorClass}
                </div>
              </div>
            </div>
          </div>
        </div>
      `;

      setupEventListeners();
    }

    function setupEventListeners() {
      const checkBtn = document.getElementById('check-btn');
      const nextBtn = document.getElementById('next-btn');
      const resetBtn = document.getElementById('reset-btn');
      const input = document.getElementById('answer-input');
      const feedback = document.getElementById('feedback');

      input.addEventListener('keypress', (e) => {
        if (e.key === 'Enter' && !answered) {
          checkAnswer();
        }
      });

      checkBtn.addEventListener('click', checkAnswer);
      nextBtn.addEventListener('click', nextQuestion);
      resetBtn.addEventListener('click', resetQuiz);

      input.focus();
    }

    function checkAnswer() {
      if (answered) return;

      const input = document.getElementById('answer-input');
      const feedback = document.getElementById('feedback');
      const checkBtn = document.getElementById('check-btn');
      const nextBtn = document.getElementById('next-btn');
      const resetBtn = document.getElementById('reset-btn');
      const userAnswer = parseInt(input.value);
      const correctAnswer = questions[currentQuestion].answer;

      const config = window.elementSdk.config;
      const primaryColor = config.primary_color || defaultConfig.primary_color;
      const accentColor = config.accent_color || defaultConfig.accent_color;
      const baseSize = config.font_size || defaultConfig.font_size;

      if (isNaN(userAnswer)) {
        feedback.innerHTML = `<span style="color: ${primaryColor};">⚠️ กรุณาใส่ตัวเลขคำ���อบ</span>`;
        input.classList.add('wrong-shake');
        setTimeout(() => input.classList.remove('wrong-shake'), 500);
        return;
      }

      answered = true;
      input.disabled = true;

      if (userAnswer === correctAnswer) {
        score++;
        feedback.innerHTML = `<span style="color: #27AE60; font-weight: 700; font-size: ${baseSize * 1.2}px;">✅ ถูกต้อง! เก่งมาก!</span>`;
        feedback.classList.add('correct-answer');
        document.getElementById('score').textContent = score;
      } else {
        feedback.innerHTML = `<span style="color: #E74C3C; font-weight: 700; font-size: ${baseSize * 1.2}px;">❌ ไม่ถูกต้อง คำตอบที่ถูกคือ ${correctAnswer} ${questions[currentQuestion].unit}</span>`;
        input.classList.add('wrong-shake');
      }

      checkBtn.classList.add('hidden');
      
      if (currentQuestion < questions.length - 1) {
        nextBtn.classList.remove('hidden');
      } else {
        resetBtn.classList.remove('hidden');
        setTimeout(() => showFinalScore(), 1000);
      }
    }

    function nextQuestion() {
      currentQuestion++;
      answered = false;
      onConfigChange(window.elementSdk.config);
    }

    function resetQuiz() {
      currentQuestion = 0;
      score = 0;
      answered = false;
      shuffleQuestions();
      onConfigChange(window.elementSdk.config);
    }

    function showFinalScore() {
      const feedback = document.getElementById('feedback');
      const config = window.elementSdk.config;
      const baseSize = config.font_size || defaultConfig.font_size;
      const accentColor = config.accent_color || defaultConfig.accent_color;
      
      let message = "";
      let emoji = "";
      
      if (score === questions.length) {
        message = "สุดยอด! คุณได้คะแนนเต็ม! 🏆";
        emoji = "🎉";
      } else if (score >= questions.length * 0.7) {
        message = "เก่งมาก! ทำได้ดีเลย! 🌟";
        emoji = "👏";
      } else {
        message = "ลองใหม่อีกครั้งนะ! 💪";
        emoji = "📚";
      }
      
      feedback.innerHTML = `<div style="font-size: ${baseSize * 1.3}px; color: ${accentColor}; font-weight: 700;">${emoji} ${message}</div>`;
    }

    // Initialize
    shuffleQuestions();

    if (window.elementSdk) {
      window.elementSdk.init({
        defaultConfig,
        onConfigChange,
        mapToCapabilities: (config) => ({
          recolorables: [
            {
              get: () => config.background_color || defaultConfig.background_color,
              set: (value) => {
                config.background_color = value;
                window.elementSdk.setConfig({ background_color: value });
              }
            },
            {
              get: () => config.card_color || defaultConfig.card_color,
              set: (value) => {
                config.card_color = value;
                window.elementSdk.setConfig({ card_color: value });
              }
            },
            {
              get: () => config.primary_color || defaultConfig.primary_color,
              set: (value) => {
                config.primary_color = value;
                window.elementSdk.setConfig({ primary_color: value });
              }
            },
            {
              get: () => config.text_color || defaultConfig.text_color,
              set: (value) => {
                config.text_color = value;
                window.elementSdk.setConfig({ text_color: value });
              }
            },
            {
              get: () => config.accent_color || defaultConfig.accent_color,
              set: (value) => {
                config.accent_color = value;
                window.elementSdk.setConfig({ accent_color: value });
              }
            }
          ],
          borderables: [],
          fontEditable: {
            get: () => config.font_family || defaultConfig.font_family,
            set: (value) => {
              config.font_family = value;
              window.elementSdk.setConfig({ font_family: value });
            }
          },
          fontSizeable: {
            get: () => config.font_size || defaultConfig.font_size,
            set: (value) => {
              config.font_size = value;
              window.elementSdk.setConfig({ font_size: value });
            }
          }
        }),
        mapToEditPanelValues: (config) => new Map([
          ["main_title", config.main_title || defaultConfig.main_title],
          ["subtitle", config.subtitle || defaultConfig.subtitle],
          ["score_label", config.score_label || defaultConfig.score_label],
          ["check_button", config.check_button || defaultConfig.check_button],
          ["next_button", config.next_button || defaultConfig.next_button],
          ["reset_button", config.reset_button || defaultConfig.reset_button],
          ["creator_name", config.creator_name || defaultConfig.creator_name],
          ["creator_class", config.creator_class || defaultConfig.creator_class]
        ])
      });
    }
  </script>
 <script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'9c031a26d4ad895b',t:'MTc2ODc5MTg0MC4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
