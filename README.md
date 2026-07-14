<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>⚡ Escape Room: 동명사 미스터리 하우스 ⚡</title>
    <style>
        :root {
            --primary: #6366f1;
            --bg: #090d16;
            --card-bg: #111827;
            --text: #f3f4f6;
            --text-muted: #6b7280;
            --success: #10b981;
            --error: #ef4444;
            --accent: #f59e0b;
            --room-color: #f59e0b;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Malgun Gothic', sans-serif; }
        body { background-color: var(--bg); color: var(--text); padding: 20px; display: flex; flex-direction: column; align-items: center; min-height: 100vh; overflow-x: hidden; position: relative; background-image: radial-gradient(circle at 50% 50%, #1e1b4b 0%, #090d16 100%); }
        
        header { text-align: center; margin-bottom: 20px; width: 100%; max-width: 650px; }
        header h1 { font-size: 28px; color: var(--accent); margin-bottom: 8px; text-shadow: 0 0 15px rgba(245, 158, 11, 0.6); letter-spacing: 2px; }
        
        .stage-map { display: none; justify-content: space-between; width: 100%; max-width: 650px; margin-bottom: 20px; background: rgba(15, 23, 42, 0.6); padding: 10px; border-radius: 12px; border: 1px solid #1f2937; gap: 5px; backdrop-filter: blur(5px); }
        .stage-dot { flex: 1; text-align: center; font-size: 11px; padding: 6px 2px; color: var(--text-muted); border-bottom: 3px solid #374151; transition: all 0.3s; white-space: nowrap; }
        .stage-dot.active { color: var(--accent); border-color: var(--accent); font-weight: bold; text-shadow: 0 0 8px rgba(245, 158, 11, 0.5); }
        .stage-dot.cleared { color: var(--success); border-color: var(--success); }

        .screen-container { background: var(--card-bg); border-radius: 16px; padding: 25px; width: 100%; max-width: 650px; box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.7); border: 1px solid #1f2937; position: relative; transition: all 0.5s ease; }
        
        .room-visual-zone { width: 100%; height: 120px; background: #0b0f19; border-radius: 10px; margin-bottom: 20px; display: flex; justify-content: center; align-items: center; border: 1px solid #1f2937; position: relative; overflow: hidden; }
        .room-visual-zone::before { content: ''; position: absolute; inset: 0; background: linear-gradient(180deg, rgba(255,255,255,0.03) 0%, transparent 100%); }
        
        .intro-screen { text-align: center; padding: 20px 10px; }
        .intro-title { font-size: 28px; color: var(--accent); font-weight: bold; margin-bottom: 15px; text-shadow: 0 0 10px rgba(245, 158, 11, 0.4); }
        .intro-desc { color: #9ca3af; font-size: 15px; line-height: 1.6; margin-bottom: 30px; }

        .room-title { font-size: 15px; color: var(--room-color); font-weight: bold; margin-bottom: 5px; text-transform: uppercase; letter-spacing: 2px; display: flex; align-items: center; gap: 8px; transition: color 0.3s; }
        .question-num { font-size: 13px; color: var(--text-muted); margin-bottom: 15px; }
        .question-text { font-size: 18px; font-weight: bold; margin-bottom: 20px; line-height: 1.6; white-space: pre-wrap; }
        
        .options-container { display: flex; flex-direction: column; gap: 12px; }
        .option-btn { background: #1f2937; border: 2px solid #374151; padding: 15px; border-radius: 10px; text-align: left; font-size: 16px; color: var(--text); cursor: pointer; transition: all 0.2s; font-weight: 500; }
        .option-btn:hover { border-color: var(--primary); background: #2563eb; box-shadow: 0 0 10px rgba(37, 99, 235, 0.4); }
        .option-btn.correct { background-color: #064e3b !important; border-color: var(--success) !important; color: #a7f3d0 !important; }
        .option-btn.incorrect { background-color: #7f1d1d !important; border-color: var(--error) !important; color: #fca5a5 !important; }

        .input-container { display: flex; flex-direction: column; gap: 12px; }
        .text-input { width: 100%; padding: 15px; border: 2px solid #374151; border-radius: 10px; font-size: 16px; font-weight: bold; text-align: center; background: #0b0f19; color: white; }
        .text-input:focus { outline: none; border-color: var(--accent); box-shadow: 0 0 10px rgba(245, 158, 11, 0.4); }

        .explanation-box { margin-top: 20px; padding: 15px; border-radius: 10px; display: none; line-height: 1.6; background: #0b0f19; border-left: 4px solid var(--primary); }
        .explanation-box.correct { border-left-color: var(--success); }
        .explanation-box.incorrect { border-left-color: var(--error); animation: shake 0.4s ease-in-out; }
        .explanation-title { font-weight: bold; margin-bottom: 5px; font-size: 15px; }
        .explanation-text { font-size: 14px; color: #9ca3af; }

        .sad-illustration { font-size: 28px; text-align: center; margin-bottom: 10px; color: #fca5a5; font-weight: bold; animation: blink 1s infinite alternate; }

        .nav-container { margin-top: 25px; display: flex; justify-content: flex-end; width: 100%; max-width: 650px; }
        .btn { background: var(--primary); color: white; border: none; padding: 12px 24px; border-radius: 8px; font-size: 16px; font-weight: bold; cursor: pointer; display: none; box-shadow: 0 4px 14px rgba(99, 102, 241, 0.4); transition: all 0.2s; }
        .btn:hover { background: #4f46e5; transform: translateY(-1px); }
        .btn.escape-btn { background: var(--accent); color: #000; box-shadow: 0 4px 14px rgba(245, 158, 11, 0.4); }
        .btn.escape-btn:hover { background: #d97706; }

        .result-screen, .teacher-dashboard { display: none; }
        .result-title { font-size: 30px; color: var(--accent); font-weight: bold; margin-bottom: 15px; text-align: center; text-shadow: 0 0 15px rgba(245, 158, 11, 0.5); }
        
        .rank-table { width: 100%; border-collapse: collapse; margin-top: 15px; background: #0b0f19; border-radius: 8px; overflow: hidden; }
        .rank-table th, .rank-table td { padding: 12px; text-align: center; border-bottom: 1px solid #1f2937; }
        .rank-table th { background: #1e1b4b; color: var(--accent); font-weight: bold; }
        .rank-table tr:hover { background: #111827; }

        .confetti { position: absolute; width: 8px; height: 8px; border-radius: 50%; z-index: 10; pointer-events: none; animation: fly 1s ease-out forwards; }

        @keyframes shake {
            0%, 100% { transform: translateX(0); }
            20%, 60% { transform: translateX(-8px); }
            40%, 80% { transform: translateX(8px); }
        }
        @keyframes fly {
            0% { transform: translate(0, 0) scale(1); opacity: 1; }
            100% { transform: translate(var(--x), var(--y)) scale(0.5); opacity: 0; }
        }
        @keyframes blink {
            0% { opacity: 0.4; text-shadow: 0 0 5px rgba(239, 68, 68, 0.2); }
            100% { opacity: 1; text-shadow: 0 0 15px rgba(239, 68, 68, 0.8); }
        }
        @keyframes pulse-light {
            0% { box-shadow: inset 0 0 15px rgba(0,0,0,0.8); }
            100% { box-shadow: inset 0 0 30px rgba(245, 158, 11, 0.2); }
        }

        .room-clear-overlay { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(5, 5, 10, 0.95); z-index: 100; justify-content: center; align-items: center; flex-direction: column; text-align: center; color: var(--accent); }
        .overlay-title { font-size: 36px; font-weight: bold; margin-bottom: 20px; animation: pulse 1s infinite alternate; }
        .overlay-desc { font-size: 18px; color: var(--text); max-width: 80%; line-height: 1.6; margin-bottom: 20px; }

        @keyframes pulse {
            0% { transform: scale(1); text-shadow: 0 0 10px var(--accent); }
            100% { transform: scale(1.05); text-shadow: 0 0 25px var(--accent); }
        }
        .admin-toggle-btn { background: none; border: 1px solid #374151; color: var(--text-muted); padding: 5px 10px; border-radius: 5px; cursor: pointer; font-size: 12px; margin-top: 20px; }
        .admin-toggle-btn:hover { color: white; border-color: var(--text-muted); }
    </style>
</head>
<body>

    <header>
        <h1>🔑 ESCAPE ROOM 🔑</h1>
        <p style="color: var(--text-muted); font-size: 14px; margin-top: 5px;">동명사의 비밀을 풀고 미스터리 하우스를 탈출하라!</p>
    </header>

    <div class="stage-map" id="stageMap">
        <div class="stage-dot active" id="dot0">R1. 시작의 방</div>
        <div class="stage-dot" id="dot1">R2. 전치사의 감옥</div>
        <div class="stage-dot" id="dot2">R3. 분사의 미로</div>
        <div class="stage-dot" id="dot3">R4. 관용구의 비밀방</div>
        <div class="stage-dot" id="dot4">R5. 마스터의 시련</div>
    </div>

    <div class="screen-container" id="introScreen">
        <div class="intro-screen">
            <div class="room-visual-zone" style="height: 140px;">
                <svg width="100" height="100" viewBox="0 0 100 100" style="filter: drop-shadow(0 0 8px #f59e0b);">
                    <path d="M10,10 L90,90 M90,10 L10,90 M50,10 L50,90 M10,50 L90,50" stroke="#4b5563" stroke-width="1"/>
                    <path d="M30,30 Q50,40 70,30 Q60,50 70,70 Q50,60 30,70 Q40,50 30,30 Z" fill="none" stroke="#f59e0b" stroke-width="2"/>
                    <path d="M20,20 Q50,32 80,20 Q68,50 80,80 Q50,68 20,80 Q32,50 20,20 Z" fill="none" stroke="#d97706" stroke-width="1.5"/>
                    <circle cx="50" cy="50" r="4" fill="#ef4444"/>
                </svg>
            </div>
            <div class="intro-title"> Castle of Gerund : 미스터리 하우스</div>
            <div class="intro-desc">
                동명사의 저주가 깃든 컴컴한 서재와 지하 감옥에 갇혔습니다.<br>
                각 방의 자물쇠 암호를 해독해야만 살아서 이곳을 빠져나갈 수 있습니다.<br>
                <span style="color: #f87171; font-weight: bold;">[패널티 활성화] 각 방에서 80% 이상(4문제 이상) 맞추지 못하면 자물쇠가 다시 잠깁니다!</span>
            </div>
            <div style="max-width: 400px; margin: 0 auto; display: flex; flex-direction: column; gap: 15px;">
                <input type="text" id="startStudentInfo" class="text-input" placeholder="학번 이름을 입력하세요 (예: 30101 홍길동)">
                <button class="btn" style="display: block; width: 100%; background: var(--accent); color: #000; padding: 15px; font-size: 18px;" onclick="startGame()">🏰 조사 착수 (진입)</button>
            </div>
        </div>
    </div>

    <div class="screen-container" id="quizContainer" style="display: none;">
        <div class="room-visual-zone" id="roomVisualContainer" style="animation: pulse-light 2s infinite alternate;">
            <div id="roomGraphicContent"></div>
        </div>

        <div class="room-title" id="roomTitle">ROOM 1. 시작의 방</div>
        <div class="question-num" id="questionNumText">자물쇠 해제 현황: 1 / 5</div>
        <div class="question-text" id="questionText">질문 내용</div>
        
        <div id="interactionArea"></div>

        <div class="explanation-box" id="explanationBox">
            <div id="sadIllustration" class="sad-illustration" style="display:none;">⚠️ 함정 발동! 날카로운 해골 창이 솟아올랐습니다!</div>
            <div class="explanation-title" id="expTitle">결과</div>
            <div class="explanation-text" id="expText">해설 내용</div>
        </div>
    </div>

    <div class="nav-container">
        <button class="btn" id="nextBtn" onclick="nextQuestion()">다음 암호 풀기 ➡️</button>
    </div>

    <div class="room-clear-overlay" id="clearOverlay">
        <div id="overlayIcon" style="font-size: 60px; margin-bottom: 10px;">🔓</div>
        <div class="overlay-title" id="overlayTitle">ROOM CLEARED!</div>
        <div class="overlay-desc" id="overlayDesc">다음 방의 육중한 철문이 열리는 소리가 들립니다...</div>
        <button class="btn" id="overlayActionBtn" style="display:inline-block; margin-top: 20px; background: var(--accent); color:#000;" onclick="closeOverlay()">다음 방 진입하기 🚪</button>
    </div>

    <div class="screen-container result-screen" id="resultScreen">
        <div class="result-title">🏰 HOUSE ESCAPED! 🏰</div>
        <div class="room-visual-zone" style="height: 130px; background: #070a12;">
            <svg width="100" height="100" viewBox="0 0 100 100" style="filter: drop-shadow(0 0 15px #ffd700);">
                <path d="M20,50 L80,50 L80,80 L20,80 Z" fill="#b45309" stroke="#f59e0b" stroke-width="2"/>
                <path d="M20,50 Q50,20 80,50 Z" fill="#d97706" stroke="#ffd700" stroke-width="2"/>
                <rect x="45" y="45" width="10" height="15" rx="3" fill="#f59e0b" stroke="#ffd700"/>
                <circle cx="50" cy="52" r="2" fill="#000"/>
                <path d="M10,35 L25,20 M90,35 L75,20 M50,15 L50,5" stroke="#ffd700" stroke-width="3" stroke-linecap="round"/>
            </svg>
        </div>
        <div id="scoreText" style="font-size: 18px; line-height: 1.8; margin-bottom: 30px; text-align: center;">점수 계산 중...</div>
        
        <div style="display: flex; gap: 10px; justify-content: center;">
            <button class="btn" id="retryWrongBtn" style="display:inline-block; background:#ef4444;" onclick="startWrongQuestions()">함정(오답) 재탈출하기 🔄</button>
            <button class="btn" style="display:inline-block;" onclick="restartFullQuiz()">새로운 플레이어 도전 🔁</button>
        </div>
    </div>

    <div class="screen-container teacher-dashboard" id="teacherDashboard">
        <div class="result-title" style="color: var(--success);">📊 실시간 랭킹 대시보드 (교사 화면)</div>
        <table class="rank-table">
            <thead>
                <tr>
                    <th>순위</th>
                    <th>학번/이름</th>
                    <th>최종 성공률 (개수)</th>
                    <th>탈출 시간</th>
                </tr>
            </thead>
            <tbody id="rankTableBody">
                <tr>
                    <td colspan="4" style="color: var(--text-muted);">등록된 탈출 기록이 없습니다.</td>
                </tr>
            </tbody>
        </table>
        <div style="text-align: center; margin-top: 20px;">
            <button class="btn" style="display: inline-block; background: #374151;" onclick="clearLeaderboard()">순위표 초기화 🗑️</button>
            <button class="btn" style="display: inline-block;" onclick="closeDashboard()">대시보드 닫기 ❌</button>
        </div>
    </div>

    <button class="admin-toggle-btn" onclick="tryAccessDashboard()">📋 교사 전용 순위 대시보드 전환 (Password)</button>

    <script>
        // 백틱(`)을 사용하여 여러 줄 문자열 오류를 근본적으로 방지했습니다.
        const roomGraphics = [
            `<svg width="100" height="100" viewBox="0 0 100 100" style="filter: drop-shadow(0 0 10px rgba(245, 158, 11, 0.6));"><path d="M20,25 Q50,15 80,25 L80,75 Q50,65 20,75 Z" fill="#1e1b4b" stroke="#4338ca" stroke-width="2"/><path d="M25,28 Q50,19 75,28 L75,72 Q50,63 25,72 Z" fill="#b45309" stroke="#f59e0b" stroke-width="1.5"/><path d="M50,22 L50,68" stroke="#78350f" stroke-width="2" stroke-dasharray="1,2"/><path d="M30,35 H45 M30,45 H45 M30,55 H45 M55,35 H70 M55,45 H70 M55,55 H70" stroke="#fef3c7" stroke-width="1.5" opacity="0.8"/><path d="M75,20 Q65,45 45,65 L40,70 L45,65 Q65,45 75,20" fill="#fef3c7" stroke="#d97706" stroke-width="1"/><path d="M42,68 L35,75" stroke="#fef3c7" stroke-width="1.5" stroke-linecap="round"/></svg>`,
            `<svg width="100" height="100" viewBox="0 0 100 100" style="filter: drop-shadow(0 0 12px rgba(239, 68, 68, 0.6));"><rect x="15" y="15" width="70" height="70" rx="8" fill="#030712" stroke="#4b5563" stroke-width="3"/><line x1="15" y1="35" x2="30" y2="35" stroke="#374151" stroke-width="2"/><line x1="70" y1="65" x2="85" y2="65" stroke="#374151" stroke-width="2"/><g stroke="#9ca3af" stroke-width="3" stroke-linecap="round"><line x1="32" y1="15" x2="32" y2="85"/><line x1="50" y1="15" x2="50" y2="85"/><line x1="68" y1="15" x2="68" y2="85"/></g><path d="M20,30 Q50,55 80,30" fill="none" stroke="#ef4444" stroke-width="3" stroke-dasharray="2,5"/><path d="M20,60 Q50,85 80,60" fill="none" stroke="#b91c1c" stroke-width="3" stroke-dasharray="2,5"/></svg>`,
            `<svg width="100" height="100" viewBox="0 0 100 100" style="filter: drop-shadow(0 0 10px rgba(16, 185, 129, 0.6));"><circle cx="50" cy="50" r="42" fill="none" stroke="#065f46" stroke-width="2" stroke-dasharray="5,3"/><path d="M25,25 H75 V75 H25 Z M35,35 H65 V65 H50 V50 H35 Z" fill="none" stroke="#10b981" stroke-width="2.5" stroke-linejoin="round"/><path d="M25,50 H35 M65,50 H75 M50,25 V35 M50,65 V75" stroke="#34d399" stroke-width="2"/><circle cx="50" cy="50" r="4" fill="#fbbf24"/><line x1="50" y1="50" x2="50" y2="40" stroke="#fbbf24" stroke-width="1.5" stroke-linecap="round"/></svg>`,
            `<svg width="100" height="100" viewBox="0 0 100 100" style="filter: drop-shadow(0 0 14px rgba(139, 92, 246, 0.7));"><circle cx="50" cy="50" r="40" fill="none" stroke="#4c1d95" stroke-width="1.5"/><polygon points="50,12 83,70 17,70" fill="none" stroke="#8b5cf6" stroke-width="2"/><polygon points="50,88 83,30 17,30" fill="none" stroke="#a78bfa" stroke-width="1.5"/><path d="M25,50 Q50,28 75,50 Q50,72 25,50 Z" fill="#1e1b4b" stroke="#c084fc" stroke-width="2"/><circle cx="50" cy="50" r="10" fill="none" stroke="#ddd6fe" stroke-width="1.5"/><circle cx="50" cy="50" r="5" fill="#a78bfa"/></svg>`,
            `<svg width="100" height="100" viewBox="0 0 100 100" style="filter: drop-shadow(0 0 15px rgba(220, 38, 38, 0.8));"><path d="M30,80 Q50,30 50,15 Q50,30 70,80 Z" fill="none" stroke="#7f1d1d" stroke-width="1"/><path d="M32,35 Q50,18 68,35 Q76,50 68,68 L60,68 L62,78 L55,78 L55,72 L45,72 L45,78 L38,78 L40,68 L32,68 Q24,50 32,35 Z" fill="#374151" stroke="#9ca3af" stroke-width="2.5" stroke-linejoin="round"/><circle cx="43" cy="45" r="5" fill="#ef4444"/><circle cx="57" cy="45" r="5" fill="#ef4444"/><path d="M38,38 L48,42 M62,38 L52,42" stroke="#1f2937" stroke-width="2" stroke-linecap="round"/><polygon points="50,50 47,56 53,56" fill="#111827"/><line x1="45" y1="64" x2="45" y2="68" stroke="#111827" stroke-width="2"/><line x1="50" y1="63" x2="50" y2="68" stroke="#111827" stroke-width="2"/><line x1="55" y1="64" x2="55" y2="68" stroke="#111827" stroke-width="2"/></svg>`
        ];

        const masterDataset = [
            // ROOM 1
            { id: 1, room: 0, roomName: "ROOM 1. 시작의 방", type: "choice", q: "I enjoy ______ soccer.", options: ["① play", "② playing", "③ played", "④ to played"], ans: 1, exp: "enjoy는 동명사만을 목적으로 취하는 대표적인 동사입니다." },
            { id: 2, room: 0, roomName: "ROOM 1. 시작의 방", type: "choice", q: "다음 중 문장이 옳은 것은?", options: ["① She likes read books.", "② She likes reading books.", "③ She likes reads books.", "④ She likes to reading books."], ans: 1, exp: "like는 목적어로 동명사(-ing)와 to부정사를 모두 가질 수 있습니다." },
            { id: 3, room: 0, roomName: "ROOM 1. 시작의 방", type: "short", q: "My hobby is ______ (cook).", ans: "cooking", exp: "is 뒤에서 보어 역할을 수행하는 동명사 형태가 알맞습니다." },
            { id: 4, room: 0, roomName: "ROOM 1. 시작의 방", type: "choice", q: "다음 중 밑줄 친 부분이 동명사인 것은?", options: ["① I want to sleep.", "② I finished cleaning my room.", "③ I will visit my uncle.", "④ I can swim well."], ans: 1, exp: "finished 뒤의 cleaning은 명사적 목적어로 쓰인 명백한 동명사입니다." },
            { id: 16, room: 0, roomName: "ROOM 1. 시작의 방", type: "choice", q: "Reading books ______ fun.", options: ["① am", "② is", "③ are", "④ were"], ans: 1, exp: "동명사구 주어는 단수 취급하므로 단수 동사 is가 정답입니다." },
            // ROOM 2
            { id: 5, room: 1, roomName: "ROOM 2. 전치사의 감옥", type: "choice", q: "He is good at ______ English.", options: ["① speak", "② speaking", "③ spoke", "④ speaks"], ans: 1, exp: "전치사 at 뒤에는 명사 역할을 하는 동명사(-ing)가 와야 합니다." },
            { id: 6, room: 1, roomName: "ROOM 2. 전치사의 감옥", type: "choice", q: "Thank you for ______ me.", options: ["① help", "② helping", "③ helped", "④ to help"], ans: 1, exp: "전치사 for 뒤에 동명사 목적어인 helping이 알맞게 결합합니다." },
            { id: 10, room: 1, roomName: "ROOM 2. 전치사의 감옥", type: "choice", q: "I'm interested in ______ pictures.", options: ["① draw", "② drawing", "③ drew", "④ draws"], ans: 1, exp: "전치사 in 뒤의 목적어로 동명사 drawing이 적절합니다." },
            { id: 14, room: 1, roomName: "ROOM 2. 전치사의 감옥", type: "choice", q: "다음 중 전치사 뒤에 올 수 있는 형태는?", options: ["① play", "② played", "③ playing", "④ to play"], ans: 2, exp: "전치사 뒤의 목적어 자리에는 명사 역할을 담당하는 동명사만 올 수 있습니다." },
            { id: 15, room: 1, roomName: "ROOM 2. 전치사의 감옥", type: "choice", q: "She is afraid of ______ mistakes.", options: ["① make", "② making", "③ made", "④ makes"], ans: 1, exp: "전치사 of 뒤에 동명사 목적어인 making이 연결되어야 합니다." },
            // ROOM 3
            { id: 8, room: 2, roomName: "ROOM 3. 분사의 미로", type: "choice", q: "다음 중 밑줄 친 부분이 동명사인 것은?", options: ["① I like swimming.", "② The swimming boy is my brother.", "③ I am swimming now.", "④ She is swimming fast."], ans: 0, exp: "①번만 명사적 목적으로 쓰인 동명사이고, 나머지는 분사입니다." },
            { id: 9, room: 2, roomName: "ROOM 3. 분사의 미로", type: "choice", q: "다음 중 어법상 틀린 것은?", options: ["① I enjoy reading books.", "② He finished doing his homework.", "③ She is interested in painting.", "④ They want going home."], ans: 3, exp: "want는 to부정사만을 목적으로 가지므로 want to go로 교정해야 합니다." },
            { id: 11, room: 2, roomName: "ROOM 3. 분사의 미로", type: "short", q: "그는 축구하는 것을 좋아한다.\\nHe likes ______ soccer.", ans: "playing", exp: "likes 뒤에서 목적어 역할을 수행하는 명사형 변환 동명사 구문입니다." },
            { id: 17, room: 2, roomName: "ROOM 3. 분사의 미로", type: "choice", q: "다음 중 동명사를 목적어로 취하는 동사는?", options: ["① enjoy", "② want", "③ decide", "④ hope"], ans: 0, exp: "enjoy는 동명사를, 나머지 세 동사는 모두 to부정사를 목적으로 가집니다." },
            { id: 20, room: 2, roomName: "ROOM 3. 분사의 미로", type: "choice", q: "다음 중 밑줄 친 부분의 쓰임이 다른 하나는?", options: ["① I enjoy reading books.", "② Reading books is fun.", "③ She is good at reading.", "④ She is reading a book now."], ans: 3, exp: "④번만 시제 진행 형태를 나타내는 현재분사로 쓰였습니다." },
            // ROOM 4
            { id: 7, room: 3, roomName: "ROOM 4. 관용구의 비밀방", type: "short", q: "She stopped ______ (talk) when the teacher came in.", ans: "talking", exp: "이야기하던 기존 행동을 멈춘 맥락이므로 목적어 명사 역할을 하는 talking이 알맞습니다." },
            { id: 12, room: 3, roomName: "ROOM 4. 관용구의 비밀방", type: "choice", q: "다음 중 평소 개인의 '취미 활동'을 나타내는 구문은?", options: ["① I enjoy playing baseball.", "② I can play baseball.", "③ I will play baseball tomorrow.", "④ I played baseball yesterday."], ans: 0, exp: "지속성을 내포한 enjoy 구문이 취미 진술에 가장 어울립니다." },
            { id: 13, room: 3, roomName: "ROOM 4. 관용구의 비밀방", type: "short", q: "They finished ______ (eat) dinner.", ans: "eating", exp: "finish의 고유 목적어 제어 인자는 동명사인 eating입니다." },
            { id: 18, room: 3, roomName: "ROOM 4. 관용구의 비밀방", type: "short", q: "다음 문장을 바르게 고쳐 쓰시오.\\nHe enjoys play basketball.", ans: "He enjoys playing basketball.", exp: "일반동사 뒤에 또 다른 원형 동사가 올 수 없으므로 playing으로 바꿉니다." },
            { id: 19, room: 3, roomName: "ROOM 4. 관용구의 비밀방", type: "short", q: "I am busy ______ (study) English.", ans: "studying", exp: "be busy -ing (~하느라 바쁘다) 구문 패턴입니다." },
            // ROOM 5
            { id: 21, room: 4, roomName: "ROOM 5. 마스터의 시련", type: "choice", q: "다음 두 문장의 빈칸에 공통으로 들어갈 수 있는 단어 형태는?\\n\\n- He forgot ______ the window, so it's still open now.\\n- They decided ______ their project tomorrow.", options: ["① close / submit", "② closing / submitting", "③ to close / to submit", "④ to close / submitting"], ans: 2, exp: "forgot 뒤에 미래에 열어야 할 당위적 사실이 오고, decide는 to부정사만을 취하므로 to형이 공통분모입니다." },
            { id: 22, room: 4, roomName: "ROOM 5. 마스터의 시련", type: "short", q: "It is no use ______ (cry) over spilt milk.", ans: "crying", exp: "It is no use -ing (~해도 소용없다) 서술형 고난도 숙어 표현입니다." },
            { id: 23, room: 4, roomName: "ROOM 5. 마스터의 시련", type: "choice", q: "He went to school without eating breakfast.\\n= He went to school and ______ breakfast.", options: ["① didn't stop eating", "② did mind eating", "③ didn't eat", "④ was used to eating"], ans: 2, exp: "without -ing(~하지 않은 채)는 절 단위 전환 시 'and didn't 동사원형' 문맥과 완전 일치합니다." },
            { id: 24, room: 4, roomName: "ROOM 5. 마스터의 시련", type: "short", q: "I look forward to ______ (meet) you soon.", ans: "meeting", exp: "look forward to의 to는 전치사이므로 명사 성격인 동명사 meeting이 결합해야 합니다." },
            { id: 25, room: 4, roomName: "ROOM 5. 마스터의 시련", type: "choice", q: "다음 중 의미하는 바가 나머지 셋과 다른 구조를 가진 문장은?", options: ["① She remembers locking the door.", "② She doesn't forget locking the door.", "③ She locked the door and she knows it.", "④ She remembers to lock the door."], ans: 3, exp: "④번만 앞으로 잠가야 할 미래 의무를 지칭하며, 다른 선택지는 과거 행동의 기억을 뜻합니다." },
            { id: 26, room: 4, roomName: "ROOM 5. 마스터의 시련", type: "short", q: "의미상 주어를 포함한 동명사구를 완성하시오.\\nI don't like ______ ______ (he / make) a noise.", ans: "his making", exp: "동명사 행위의 주체를 명시하는 의미상 주어는 소유격(his)을 배치합니다." },
            { id: 27, room: 4, roomName: "ROOM 5. 마스터의 시련", type: "choice", q: "밑줄 친 부분의 성격이 문장 성분상 '동명사'로 사용되지 않은 것은?", options: ["① My father's job is collecting old coins.", "② He is busy planning his next vacation.", "③ Keeping a diary everyday is a good habit.", "④ I cannot help laughing at his funny face."], ans: 1, exp: "②번의 busy 구문 뒤 분사는 상태를 나타내는 형용사 분사 계열입니다." },
            { id: 28, room: 4, roomName: "ROOM 5. 마스터의 시련", type: "short", q: "As soon as she heard the news, she wept.\\n= ______ hearing the news, she wept.", ans: "on", exp: "On -ing 구문은 '~하자마자'를 나타내는 고난도 대치 전치사 코드입니다." },
            { id: 29, room: 4, roomName: "ROOM 5. 마스터의 시련", type: "choice", q: "동사 뒤에 동명사와 to부정사가 모두 올 수 있으며, 의미 변화가 거의 없는 동사 쌍은?", options: ["① stop, forget", "② try, remember", "③ start, begin", "④ regret, mean"], ans: 2, exp: "start, begin은 목적어 종류 변환에 따른 의미 변화가 수반되지 않습니다." },
            { id: 30, room: 4, roomName: "ROOM 5. 마스터의 시련", type: "short", q: "He denied ______ (steal) the wallet yesterday.", ans: "stealing", exp: "부인하다(deny)의 대상 목적어는 과거 귀속 성격을 가진 동명사(stealing)만 수용합니다." }
        ];

        let roomData = [[], [], [], [], []];
        let activeQuestions = [];
        let wrongQuestions = [];
        let currentIdx = 0;
        let totalScore = 0;
        let roomCorrectCount = 0;
        let isAnswered = false;
        let currentRoomIdx = 0;
        
        let startTime = 0;
        let currentStudentName = "";
        let isRoomPassed = false;

        masterDataset.forEach(item => { roomData[item.room].push(item); });

        function startGame() {
            const inputField = document.getElementById('startStudentInfo');
            const info = inputField.value.trim();
            if(!info) { alert("❗ 학번과 이름을 입력하셔야 하우스 문이 열립니다!"); return; }
            
            currentStudentName = info;
            wrongQuestions = [];
            currentRoomIdx = 0;
            totalScore = 0;
            startTime = Date.now();
            
            document.getElementById('introScreen').style.display = 'none';
            document.getElementById('stageMap').style.display = 'flex';
            document.getElementById('quizContainer').style.display = 'block';
            
            setupRoom(0);
        }

        function setupRoom(roomIdx) {
            currentRoomIdx = roomIdx;
            currentIdx = 0;
            roomCorrectCount = 0;
            
            activeQuestions = [...roomData[roomIdx]];
            activeQuestions.sort(() => Math.random() - 0.5);

            const colors = ["#f59e0b", "#ef4444", "#10b981", "#8b5cf6", "#ec4899"];
            document.documentElement.style.setProperty('--room-color', colors[roomIdx]);
            document.getElementById('roomGraphicContent').innerHTML = roomGraphics[roomIdx];

            for (let i = 0; i < 5; i++) {
                const dot = document.getElementById(`dot${i}`);
                dot.className = "stage-dot";
                if (i < roomIdx) dot.classList.add('cleared');
                if (i === roomIdx) dot.classList.add('active');
            }

            loadQuestion();
        }

        function loadQuestion() {
            isAnswered = false;
            document.getElementById('nextBtn').style.display = 'none';
            document.getElementById('explanationBox').style.display = 'none';
            document.getElementById('sadIllustration').style.display = 'none';
            
            const currentQuiz = activeQuestions[currentIdx];
            document.getElementById('roomTitle').innerHTML = `<span>🔑</span> ${currentQuiz.roomName}`;
            document.getElementById('questionNumText').innerText = `현재 방 자물쇠 해제 현황: ${currentIdx + 1} / ${activeQuestions.length}`;
            document.getElementById('questionText').innerHTML = currentQuiz.q;

            const area = document.getElementById('interactionArea');
            area.innerHTML = '';

            if (currentQuiz.type === "choice") {
                const container = document.createElement('div');
                container.classList.add('options-container');
                currentQuiz.options.forEach((opt, idx) => {
                    const btn = document.createElement('button');
                    btn.classList.add('option-btn');
                    btn.innerHTML = opt;
                    btn.onclick = () => checkAnswer(idx, btn);
                    container.appendChild(btn);
                });
                area.appendChild(container);
            } else {
                const container = document.createElement('div');
                container.classList.add('input-container');
                const input = document.createElement('input');
                input.type = 'text';
                input.classList.add('text-input');
                input.placeholder = '잠금해제 암호 입력 후 Enter';
                input.id = 'shortAnswerInput';
                input.onkeyup = (e) => { if(e.key === 'Enter') checkAnswer(input.value, input); };
                container.appendChild(input);
                area.appendChild(container);
                input.focus();
            }
        }

        function checkAnswer(userInput, element) {
            if (isAnswered) return;
            isAnswered = true;

            const currentQuiz = activeQuestions[currentIdx];
            const expBox = document.getElementById('explanationBox');
            const expTitle = document.getElementById('expTitle');
            const expText = document.getElementById('expText');
            const sadImg = document.getElementById('sadIllustration');
            
            let isCorrect = false;

            if (currentQuiz.type === "choice") {
                const btns = document.querySelectorAll('.option-btn');
                if (userInput === currentQuiz.ans) {
                    isCorrect = true;
                    element.classList.add('correct');
                } else {
                    element.classList.add('incorrect');
                    btns[currentQuiz.ans].classList.add('correct');
                }
            } else {
                element.disabled = true;
                if (userInput.trim().toLowerCase() === currentQuiz.ans.toLowerCase()) {
                    isCorrect = true;
                    element.style.borderColor = "var(--success)";
                    element.style.backgroundColor = "#064e3b";
                } else {
                    element.style.borderColor = "var(--error)";
                    element.style.backgroundColor = "#7f1d1d";
                }
            }

            if (isCorrect) {
                roomCorrectCount++;
                totalScore++;
                triggerConfetti();
                expBox.className = "explanation-box correct";
                expTitle.innerText = "🔓 자물쇠 해제 성공! 단서를 발견했습니다.";
                sadImg.style.display = 'none';
            } else {
                wrongQuestions.push(currentQuiz);
                expBox.className = "explanation-box incorrect";
                expTitle.innerText = "💥 함정 발동!";
                sadImg.style.display = 'block';
            }

            expText.innerText = currentQuiz.exp;
            expBox.style.display = 'block';

            const nextBtn = document.getElementById('nextBtn');
            if (currentIdx === activeQuestions.length - 1) {
                nextBtn.innerText = "방문 검증하기 🔍";
            } else {
                nextBtn.innerText = "다음 암호 풀기 ➡️";
            }
            nextBtn.style.display = 'block';
        }

        function triggerConfetti() {
            const colors = ['#ff007f', '#ffd700', '#00f5ff', '#7fff00', '#6366f1'];
            for (let i = 0; i < 35; i++) {
                const confetti = document.createElement('div');
                confetti.classList.add('confetti');
                confetti.style.backgroundColor = colors[Math.floor(Math.random() * colors.length)];
                confetti.style.left = '50%';
                confetti.style.top = '40%';
                const angle = Math.random() * Math.PI * 2;
                const distance = Math.random() * 160 + 40;
                confetti.style.setProperty('--x', `${Math.cos(angle) * distance}px`);
                confetti.style.setProperty('--y', `${Math.sin(angle) * distance}px`);
                document.body.appendChild(confetti);
                setTimeout(() => confetti.remove(), 1000);
            }
        }

        function nextQuestion() {
            currentIdx++;
            if (currentIdx < activeQuestions.length) {
                loadQuestion();
            } else {
                const successRate = (roomCorrectCount / activeQuestions.length) * 100;
                
                if (successRate >= 80) {
                    isRoomPassed = true;
                    if (currentRoomIdx < 4) {
                        showRoomClearOverlay(true);
                    } else {
                        showFinalResult();
                    }
                } else {
                    isRoomPassed = false;
                    showRoomClearOverlay(false);
                }
            }
        }

        function showRoomClearOverlay(passed) {
            const overlay = document.getElementById('clearOverlay');
            const icon = document.getElementById('overlayIcon');
            const title = document.getElementById('overlayTitle');
            const desc = document.getElementById('overlayDesc');
            const actionBtn = document.getElementById('overlayActionBtn');

            if (passed) {
                icon.innerText = "🔓";
                icon.style.filter = "drop-shadow(0 0 10px #10b981)";
                title.innerText = `ROOM ${currentRoomIdx + 1} 탈출 성공!`;
                title.style.color = "var(--success)";
                
                const descs = [
                    "기초 자물쇠들을 모두 무력화했습니다. 다음 구역 '전치사의 감옥'문이 열립니다.",
                    "전치사 결합 규칙을 격파했습니다. '분사의 미로' 지형이 눈앞에 나타납니다.",
                    "동명사와 분사의 함정을 간파했습니다. '관용구의 비밀방'으로 진입합니다.",
                    "거의 다 왔습니다! 이제 최종 시험 관문인 '마스터의 시련'이 시작됩니다!"
                ];
                desc.innerText = descs[currentRoomIdx];
                actionBtn.innerText = "다음 방 진입하기 🚪";
                actionBtn.className = "btn";
                actionBtn.style.background = "var(--accent)";
            } else {
                icon.innerText = "🔒";
                icon.style.filter = "drop-shadow(0 0 10px #ef4444)";
                title.innerText = "탈출 실패: 자물쇠 복구";
                title.style.color = "var(--error)";
                desc.innerHTML = `현재 방 정답 개수: <b style="color:#ef4444">${roomCorrectCount}개</b> (80% 미만)<br>자물쇠의 암호가 전부 초기화되고 역전류 함정이 가동되었습니다.<br>이 방의 문제를 처음부터 다시 풀어야 합니다!`;
                actionBtn.innerText = "방 재도전하기 🔄";
                actionBtn.className = "btn escape-btn";
            }
            overlay.style.display = 'flex';
        }

        function closeOverlay() {
            document.getElementById('clearOverlay').style.display = 'none';
            if (isRoomPassed) {
                setupRoom(currentRoomIdx + 1);
            } else {
                totalScore -= roomCorrectCount;
                setupRoom(currentRoomIdx);
            }
        }

        function showFinalResult() {
            const endTime = Date.now();
            const diffSec = Math.floor((endTime - startTime) / 1000);
            const min = Math.floor(diffSec / 60);
            const sec = diffSec % 60;
            const totalElapsedTime = `${min}분 ${sec}초`;

            document.getElementById('quizContainer').style.display = 'none';
            document.getElementById('nextBtn').style.display = 'none';
            document.getElementById('stageMap').style.display = 'none';
            
            const totalQuestions = masterDataset.length;
            const finalPercent = Math.round((totalScore / totalQuestions) * 100);
            
            document.getElementById('scoreText').innerHTML = `
                플레이어 정보: <b style="color:var(--accent);">${currentStudentName}</b><br>
                최종 탈출 소요 시간: <span style="color:var(--accent);"><b>${totalElapsedTime}</b></span><br>
                제출된 최종 정답률: <span style="color:var(--accent); font-size:24px;"><b>${finalPercent}%</b></span><br>
                <span style="color:var(--text-muted); font-size:13px;">(모든 방을 80% 이상으로 클리어하여 하우스를 정복했습니다!)</span>
            `;
            
            autoRegisterScore(finalPercent, totalQuestions, totalElapsedTime, diffSec);

            const retryWrongBtn = document.getElementById('retryWrongBtn');
            if (wrongQuestions.length === 0) {
                retryWrongBtn.style.display = 'none';
            } else {
                retryWrongBtn.style.display = 'inline-block';
                retryWrongBtn.innerText = `함정(오답) 단독 파괴하기 (${wrongQuestions.length}문항) 🔄`;
            }
            document.getElementById('resultScreen').style.display = 'block';
        }

        function autoRegisterScore(finalPercent, totalQuestions, totalElapsedTime, diffSec) {
            let records = JSON.parse(localStorage.getItem('escapeRoomRatings')) || [];
            records.push({
                name: currentStudentName,
                percent: finalPercent,
                countStr: `${totalScore} / ${totalQuestions}`,
                timeStr: totalElapsedTime,
                seconds: diffSec
            });
            records.sort((a, b) => {
                if (b.percent !== a.percent) return b.percent - a.percent;
                return a.seconds - b.seconds;
            });
            localStorage.setItem('escapeRoomRatings', JSON.stringify(records));
        }

        function tryAccessDashboard() {
            const pw = prompt("🔒 교사 인증 비밀번호를 입력하십시오.");
            if (pw === "1111") {
                renderLeaderboard();
                document.getElementById('introScreen').style.display = 'none';
                document.getElementById('quizContainer').style.display = 'none';
                document.getElementById('resultScreen').style.display = 'none';
                document.getElementById('stageMap').style.display = 'none';
                document.getElementById('teacherDashboard').style.display = 'block';
            } else if (pw !== null) {
                alert("❌ 비밀번호가 올바르지 않습니다.");
            }
        }

        function renderLeaderboard() {
            const records = JSON.parse(localStorage.getItem('escapeRoomRatings')) || [];
            const tbody = document.getElementById('rankTableBody');
            tbody.innerHTML = '';

            if (records.length === 0) {
                tbody.innerHTML = `<tr><td colspan="4" style="color: var(--text-muted);">등록된 탈출 기록이 없습니다.</td></tr>`;
                return;
            }

            records.forEach((rec, idx) => {
                let medal = idx + 1;
                if (idx === 0) medal = "🥇";
                else if (idx === 1) medal = "🥈";
                else if (idx === 2) medal = "🥉";

                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td><b>${medal}</b></td>
                    <td>${rec.name}</td>
                    <td style="color:var(--accent); font-weight:bold;">${rec.percent}% (${rec.countStr})</td>
                    <td>${rec.timeStr}</td>
                `;
                tbody.appendChild(tr);
            });
        }

        function clearLeaderboard() {
            if (confirm("⚠️ 정말로 모든 학생 랭킹 데이터를 초기화하시겠습니까?")) {
                localStorage.removeItem('escapeRoomRatings');
                renderLeaderboard();
            }
        }

        function closeDashboard() {
            document.getElementById('teacherDashboard').style.display = 'none';
            if (!currentStudentName) {
                document.getElementById('introScreen').style.display = 'block';
            } else {
                document.getElementById('resultScreen').style.display = 'block';
            }
        }

        function startWrongQuestions() {
            activeQuestions = [...wrongQuestions];
            activeQuestions.sort(() => Math.random() - 0.5);
            wrongQuestions = [];
            currentIdx = 0;
            roomCorrectCount = 0;

            document.getElementById('roomTitle').innerText = "🔥 패자부활: 비밀 지하 감옥";
            document.getElementById('questionNumText').innerText = `남은 정복 문항: 1 / ${activeQuestions.length}`;
            document.getElementById('roomGraphicContent').innerHTML = roomGraphics[4];
            
            document.getElementById('stageMap').style.display = 'none';
            document.getElementById('quizContainer').style.display = 'block';
            document.getElementById('resultScreen').style.display = 'none';
            loadQuestion();
        }

        function restartFullQuiz() {
            currentStudentName = "";
            document.getElementById('startStudentInfo').value = "";
            document.getElementById('stageMap').style.display = 'none';
            document.getElementById('resultScreen').style.display = 'none';
            document.getElementById('introScreen').style.display = 'block';
        }
    </script>
</body>
</html>
