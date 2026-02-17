<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ENHYPEN TICKET SIMULATOR</title>
    <link rel="stylesheet" as="style" crossorigin href="https://cdn.jsdelivr.net/gh/orioncactus/pretendard@v1.3.9/dist/web/static/pretendard.min.css" />
    <style>
        :root {
            --en-red: #ff0000;
            --en-deep-red: #8b0000;
            --en-black: #050505;
            --en-card: #121212;
            --en-gray: #1e1e1e;
            --podo: #b326ff; /* 좀 더 선명한 포도색 */
            --glass: rgba(255, 255, 255, 0.05);
        }

        body { 
            font-family: 'Pretendard', sans-serif; 
            margin: 0; 
            background: #000; 
            display: flex; 
            justify-content: center; 
            align-items: center;
            height: 100vh;
            color: #ffffff; 
        }

        /* 모바일 프레임 강화 */
        .mobile-view { 
            width: 100%; 
            max-width: 400px; 
            height: 850px; 
            background: var(--en-black); 
            position: relative; 
            overflow: hidden; 
            border-radius: 40px; 
            box-shadow: 0 0 50px rgba(255, 0, 0, 0.15); 
            border: 8px solid #1a1a1a;
        }

        .screen { display: none; width: 100%; height: 100%; position: absolute; top: 0; left: 0; overflow-y: auto; }
        .active { display: block; animation: fadeIn 0.4s ease-out; }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* 헤더: 시크한 블랙 */
        header { 
            background: rgba(5, 5, 5, 0.8);
            backdrop-filter: blur(10px);
            height: 70px; 
            display: flex; 
            align-items: center; 
            justify-content: space-between; 
            padding: 0 25px; 
            font-weight: 700; 
            letter-spacing: 1px;
            position: sticky;
            top: 0;
            z-index: 10;
        }

        /* 메인 카드 섹션 */
        .card {
            background: var(--en-card);
            margin: 20px;
            padding: 20px;
            border-radius: 24px;
            border: 1px solid rgba(255,255,255,0.05);
        }

        .concert-poster { 
            width: 100%; 
            border-radius: 18px; 
            box-shadow: 0 10px 20px rgba(0,0,0,0.5);
            margin-bottom: 15px;
        }

        /* 타이머: 강렬한 레드 네온 */
        .timer-section { text-align: center; margin: 30px 0; }
        #timer { 
            font-size: 36px; 
            font-weight: 900; 
            color: var(--en-red); 
            text-shadow: 0 0 15px rgba(255, 0, 0, 0.6);
            font-variant-numeric: tabular-nums;
        }

        /* 버튼: 둥글고 볼륨감 있게 */
        .btn-main { 
            width: calc(100% - 50px); 
            height: 60px; 
            background: #222; 
            border: none; 
            color: #555; 
            font-size: 17px; 
            font-weight: 700; 
            border-radius: 20px; 
            margin: 0 25px;
            transition: all 0.3s;
            cursor: not-allowed;
            position: absolute;
            bottom: 40px;
        }
        .btn-main.active-btn { 
            background: var(--en-red); 
            color: white; 
            cursor: pointer;
            box-shadow: 0 10px 20px rgba(226, 20, 45, 0.3);
        }
        .btn-main.active-btn:active { transform: scale(0.96); }

        /* 대기열: 프로그레스 바 커스텀 */
        .progress-container { 
            width: 85%; 
            height: 8px; 
            background: #1a1a1a; 
            border-radius: 10px; 
            margin: 30px auto; 
            overflow: hidden; 
        }
        .progress-bar { width: 0%; height: 100%; background: linear-gradient(90deg, var(--en-deep-red), var(--en-red)); border-radius: 10px; }

        /* 구역 선택: 스타일리시한 그리드 */
        .area-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; padding: 20px; }
        .area-box { 
            padding: 20px 10px; 
            border-radius: 18px; 
            text-align: center; 
            font-weight: 600; 
            font-size: 14px;
            transition: 0.2s;
            border: 1px solid rgba(255,255,255,0.1);
        }
        .sc-zone { background: linear-gradient(135deg, #E2142D, #8b0000); color: white; border: none; }
        .mg-zone { background: #2a2a2a; color: #ff7586; border: 1px solid #ff7586; }
        .gen-zone { background: #1a1a1a; color: #ccc; }

        /* 포도알: 보석처럼 */
        .podo-grid { display: grid; grid-template-columns: repeat(8, 1fr); gap: 8px; padding: 20px; justify-items: center;}
        .podo { width: 32px; height: 32px; border-radius: 10px; transition: 0.2s; }
        .podo.purple { 
            background: var(--podo); 
            box-shadow: inset -2px -2px 6px rgba(0,0,0,0.3), 0 0 10px rgba(179, 38, 255, 0.4); 
            cursor: pointer; 
        }
        .podo.gray { background: #222; opacity: 0.5; }

        /* 입력창 */
        .en-input { 
            width: 80%; 
            height: 55px; 
            background: #111; 
            border: 2px solid #333; 
            border-radius: 15px; 
            color: white; 
            text-align: center; 
            font-size: 20px; 
            margin-bottom: 20px;
            outline: none;
        }
        .en-input:focus { border-color: var(--en-red); }

        .captcha-box { 
            background: #fff; 
            color: #000; 
            padding: 20px 40px; 
            border-radius: 15px; 
            font-weight: 900; 
            font-size: 32px; 
            letter-spacing: 8px; 
            margin: 20px 0;
            transform: rotate(-1deg);
        }

        /* 커스텀 스크롤바 */
        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-thumb { background: #333; border-radius: 10px; }
    </style>
</head>
<body>

<div class="mobile-view">
    <div id="step1" class="screen active">
        <header><span>ENHYPEN</span> <span style="font-size:20px;">≡</span></header>
        <div class="card">
            <img src="https://i.ibb.co/Lz0YmYq/poster.jpg" onerror="this.src='https://via.placeholder.com/400x500/111/fff?text=ENHYPEN+WALK+THE+LINE'" class="concert-poster">
            <h2 style="font-size: 22px; margin: 10px 0 5px 0; letter-spacing: -0.5px;">WALK THE LINE : FINAL</h2>
            <p style="font-size: 13px; color: #aaa; margin: 0;">2025.10.24 - 10.26 | KSPO DOME</p>
        </div>
        
        <div class="timer-section">
            <p style="font-size: 12px; color: #888; margin-bottom: 8px; letter-spacing: 1px;">SERVER TIME</p>
            <div id="timer">00:00:00:0</div>
        </div>

        <button id="mainBtn" class="btn-main" onclick="goWait()">티켓 예매하기</button>
    </div>

    <div id="stepWait" class="screen">
        <div style="text-align: center; margin-top: 150px;">
            <div style="font-size: 50px; margin-bottom: 20px;">⏳</div>
            <h3 style="color:var(--en-red); font-size: 22px; margin-bottom: 10px;">접속 대기 중입니다</h3>
            <p style="font-size:14px; color: #888;">잠시만 기다려 주시면 예매 페이지로 이동합니다.</p>
            <div class="progress-container"><div id="waitBar" class="progress-bar"></div></div>
            <div style="background: #111; display: inline-block; padding: 15px 30px; border-radius: 20px;">
                <p style="font-size:14px; color:#666; margin:0 0 5px 0;">대기 순서</p>
                <span id="waitCount" style="color:white; font-size: 24px; font-weight:bold;">124,502</span>
            </div>
        </div>
    </div>

    <div id="stepDate" class="screen">
        <header>날짜 선택</header>
        <div style="padding: 20px;">
            <div class="card" style="margin: 0 0 15px 0; border-left: 6px solid var(--en-red);" onclick="selectDate('10.24(금) 7PM')">
                <p style="color:#888; margin:0; font-size:12px;">2025.10.24 FRIDAY</p>
                <b style="font-size:18px;">PM 19:00 (KST)</b>
                <div style="margin-top:10px; font-size:13px; color:var(--en-red);">잔여 좌석: 1,204석</div>
            </div>
            <div class="card" style="margin: 0 0 15px 0; border-left: 6px solid var(--en-red);" onclick="selectDate('10.25(토) 6PM')">
                <p style="color:#888; margin:0; font-size:12px;">2025.10.25 SATURDAY</p>
                <b style="font-size:18px;">PM 18:00 (KST)</b>
                <div style="margin-top:10px; font-size:13px; color:var(--en-red);">잔여 좌석: 852석</div>
            </div>
        </div>
    </div>

    <div id="stepCaptcha" class="screen">
        <header>보안 인증</header>
        <div style="text-align: center; padding: 40px 20px;">
            <div class="captcha-box" id="captchaText">EN7HYP</div>
            <p style="font-size: 14px; color: #aaa; margin-bottom: 30px;">상단의 보안 문자를 입력해 주세요.</p>
            <input type="text" id="captchaInput" class="en-input" placeholder="대문자로 입력">
            <button class="btn-main active-btn" onclick="checkCaptcha()" style="position: relative; width: 100%; margin: 0;">인증 완료</button>
        </div>
    </div>

    <div id="stepMap" class="screen">
        <header>구역 선택 <span onclick="go('step1')" style="font-size:24px; cursor:pointer;">×</span></header>
        <div style="padding: 20px;">
            <div style="width: 100%; height: 40px; background: #333; border-radius: 10px; text-align: center; line-height: 40px; font-size: 12px; letter-spacing: 5px; color: #888; margin-bottom: 30px;">STAGE</div>
            <div class="area-grid">
                <div class="area-box sc-zone" onclick="goPodo('F1 (Sound Check)', 220000)">F1</div>
                <div class="area-box sc-zone" onclick="goPodo('F2 (Sound Check)', 220000)">F2</div>
                <div class="area-box mg-zone" onclick="goPodo('102 (VIP)', 253000)">102 (VIP)</div>
                <div class="area-box mg-zone" onclick="goPodo('114 (VIP)', 253000)">114 (VIP)</div>
                <div class="area-box gen-zone" onclick="goPodo('205 (일반)', 165000)">205</div>
                <div class="area-box gen-zone" onclick="goPodo('211 (일반)', 165000)">211</div>
            </div>
        </div>
    </div>

    <div id="stepPodo" class="screen">
        <header><span id="podoTitle">구역</span> <span onclick="go('stepMap')" style="font-size:24px;">×</span></header>
        <div style="background:#111; padding:15px; font-size:13px; display:flex; justify-content:space-between; border-bottom:1px solid #222;">
            <span>선택 구역 가격: <b id="podoPrice">0</b>원</span>
            <span style="color:var(--podo);">● 남은 좌석 5</span>
        </div>
        <div class="podo-grid" id="podoGrid"></div>
        <p style="text-align:center; font-size:12px; color:#444; margin-top:20px;">좌석을 선택하면 결제 단계로 이동합니다.</p>
    </div>

    <div id="stepPay" class="screen">
        <header>최종 확인</header>
        <div style="padding: 20px;">
            <div class="card" style="margin:0;">
                <p style="color:#888; font-size:13px;">RESERVATION INFO</p>
                <div style="margin: 20px 0;">
                    <p style="font-size:14px; color:#aaa; margin:5px 0;">일시</p>
                    <b id="finalDate" style="font-size:18px;">-</b>
                </div>
                <div style="margin: 20px 0;">
                    <p style="font-size:14px; color:#aaa; margin:5px 0;">좌석</p>
                    <b id="finalSeat" style="font-size:18px;">-</b>
                </div>
                <hr style="border:0; border-top:1px solid #333; margin:20px 0;">
                <div style="display:flex; justify-content:space-between; align-items:center;">
                    <span>총 결제 금액</span>
                    <span style="color:var(--en-red); font-size:24px; font-weight:900;" id="finalPrice">0원</span>
                </div>
            </div>
            <button class="btn-main active-btn" style="position:relative; width:100%; margin:30px 0;" onclick="finish()">무통장 입금으로 결제</button>
        </div>
    </div>
</div>

<script>
    /* 타이머 로직 (서버시간 느낌 극대화) */
    setInterval(() => {
        const now = new Date();
        const h = String(now.getHours()).padStart(2, '0');
        const m = String(now.getMinutes()).padStart(2, '0');
        const s = String(now.getSeconds()).padStart(2, '0');
        const ms = Math.floor(now.getMilliseconds()/100);
        document.getElementById('timer').innerText = `${h}:${m}:${s}:${ms}`;
        
        const btn = document.getElementById('mainBtn');
        // 매 분 00초에만 예매 버튼 활성화 (연습용)
        if(s === "00") { 
            btn.classList.add('active-btn'); 
            btn.disabled = false;
            btn.innerText = "지금 예매하기";
        } else { 
            btn.classList.remove('active-btn'); 
            btn.disabled = true; 
            btn.innerText = (60 - parseInt(s)) + "초 후 오픈";
        }
    }, 100);

    function go(id) {
        document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
    }

    function goWait() {
        go('stepWait');
        let count = 124502;
        let prog = 0;
        const iv = setInterval(() => {
            count -= Math.floor(Math.random() * 2500) + 1000;
            prog += 2.5;
            document.getElementById('waitCount').innerText = Math.max(0, count).toLocaleString();
            document.getElementById('waitBar').style.width = prog + "%";
            if(prog >= 100) { clearInterval(iv); go('stepDate'); }
        }, 100);
    }

    let selDate = "";
    function selectDate(d) {
        selDate = d;
        go('stepCaptcha');
    }

    function checkCaptcha() {
        if(document.getElementById('captchaInput').value.toUpperCase() === "EN7HYP") go('stepMap');
        else {
            alert("보안 문자가 틀렸습니다!");
            document.getElementById('captchaInput').value = "";
        }
    }

    function goPodo(name, price) {
        document.getElementById('podoTitle').innerText = name;
        document.getElementById('podoPrice').innerText = price.toLocaleString();
        const grid = document.getElementById('podoGrid');
        grid.innerHTML = '';
        go('stepPodo');
        
        for(let i=0; i<64; i++) {
            const p = document.createElement('div');
            const isPodo = Math.random() < 0.08; 
            p.className = 'podo ' + (isPodo ? 'purple' : 'gray');
            p.onclick = function() {
                if(this.classList.contains('purple')) {
                    if(Math.random() < 0.6) { 
                        alert("이미 선택된 좌석입니다!");
                        this.className = 'podo gray';
                    } else {
                        document.getElementById('finalDate').innerText = selDate;
                        document.getElementById('finalSeat').innerText = name + "구역 좌석";
                        document.getElementById('finalPrice').innerText = price.toLocaleString() + "원";
                        go('stepPay');
                    }
                }
            };
            grid.appendChild(p);
        }
    }

    function finish() {
        alert("🎉 축하합니다!\nENHYPEN 월드투어 티켓팅에 성공하셨습니다!\n엔진 님, 공연장에서 만나요!");
        location.reload();
    }
</script>
</body
