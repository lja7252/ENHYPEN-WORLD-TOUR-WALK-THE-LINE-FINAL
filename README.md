<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ENHYPEN 티켓팅 연습기</title>
    <style>
        :root {
            --en-red: #E2142D;
            --en-dark: #0A0A0A;
            --en-gray: #2A2A2A;
            --podo: #8a2be2;
        }

        body { font-family: 'Pretendard', sans-serif; margin: 0; background: #1a1a1a; display: flex; justify-content: center; overflow: hidden; color: white; }
        .mobile-view { width: 100%; max-width: 430px; height: 100vh; background: var(--en-dark); position: relative; overflow: hidden; border-radius: 30px; box-shadow: 0 10px 30px rgba(0,0,0,0.5); border: 4px solid #333; margin-top: 10px; }
        .screen { display: none; width: 100%; height: 100%; position: absolute; top: 0; left: 0; overflow-y: auto; }
        .active { display: block; }

        /* 헤더 디자인 */
        header { background: #000; color: white; height: 60px; display: flex; align-items: center; justify-content: space-between; padding: 0 20px; font-weight: bold; border-bottom: 1px solid #333; }
        
        /* 공지사항 섹션 */
        .notice-area { padding: 20px; background: #111; }
        .concert-poster { width: 100%; border-radius: 10px; margin-bottom: 15px; }
        .info-box { font-size: 12px; color: #ccc; line-height: 1.6; border-top: 1px solid #333; padding-top: 15px; }
        
        /* 타이머 & 버튼 */
        .timer-section { text-align: center; padding: 20px; background: #000; }
        #timer { font-size: 28px; font-weight: 800; color: var(--en-red); margin-bottom: 10px; }
        
        .btn-main { width: 90%; height: 55px; background: #555; border: none; color: white; font-size: 18px; font-weight: bold; border-radius: 5px; margin: 10px 5%; cursor: not-allowed; }
        .btn-main.active-btn { background: var(--en-red); cursor: pointer; }

        /* 대기열 */
        .waiting-box { text-align: center; padding-top: 120px; }
        .progress-container { width: 80%; height: 10px; background: #333; border-radius: 5px; margin: 25px auto; overflow: hidden; }
        .progress-bar { width: 0%; height: 100%; background: var(--en-red); transition: 0.5s linear; }

        /* 좌석 선택 커스텀 (제공된 이미지 반영) */
        .stage { width: 150px; height: 35px; background: #333; margin: 10px auto; text-align: center; line-height: 35px; font-size: 12px; color: #888; border-radius: 3px; }
        .seat-map-container { padding: 20px; text-align: center; }
        .area-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 10px; justify-content: center; margin: 20px auto; max-width: 200px; }
        
        .area-box { padding: 15px; border: 1px solid #444; border-radius: 5px; font-weight: bold; cursor: pointer; font-size: 13px; }
        .sc-zone { background: #E2142D; color: white; border: none; } /* Sound Check */
        .mg-zone { background: #FF7586; color: white; border: none; } /* M&G */
        .gen-zone { background: #444; color: white; } /* 일반석 */

        /* 포도알(좌석) */
        .podo-grid { display: grid; grid-template-columns: repeat(10, 1fr); gap: 5px; padding: 15px; }
        .podo { width: 25px; height: 25px; border-radius: 4px; cursor: pointer; }
        .podo.purple { background: var(--podo); border: 1px solid #721cb8; }
        .podo.gray { background: #333; cursor: default; }

        /* 보안문자 */
        .captcha-box { background: #eee; color: #000; font-size: 35px; font-weight: 900; letter-spacing: 5px; padding: 20px; border-radius: 5px; margin: 20px 0; display: inline-block; font-family: serif; font-style: italic; }
        .en-input { width: 80%; height: 45px; background: #222; border: 1px solid #444; color: white; text-align: center; font-size: 18px; margin-bottom: 20px; }

        /* 결제 */
        .price-tag { color: var(--en-red); font-size: 20px; font-weight: bold; }
    </style>
</head>
<body>

<div class="mobile-view">
    <div id="step1" class="screen active">
        <header><span>〈</span> ENHYPEN WORLD TOUR <span>🔍</span></header>
        <div class="notice-area">
            <h2 style="font-size: 18px; margin-bottom: 5px;">WALK THE LINE : FINAL</h2>
            <p style="font-size: 12px; color: #888;">KSPO DOME | 2025.10.24 ~ 10.26</p>
            <div class="info-box">
                ※ 본 공연은 온/오프라인 동시에 진행되는 공연입니다.<br>
                ※ 본인 인증된 계정에 한하여 예매 가능합니다.<br>
                ※ 1인 2매까지 예매 가능 (회차별)
            </div>
        </div>
        <div class="timer-section">
            <p style="font-size: 12px; color: #666; margin: 0;">현재 서버 시간</p>
            <div id="timer">00:00:00:0</div>
        </div>
        <button id="mainBtn" class="btn-main" onclick="goWait()" style="position:absolute; bottom:20px;">예매하기</button>
    </div>

    <div id="stepWait" class="screen">
        <div class="waiting-box">
            <h3 style="color:var(--en-red);">서비스 접속 대기 중</h3>
            <p style="font-size:14px; color: #aaa;">예매를 위해 순서대로 접속 중입니다.</p>
            <div class="progress-container"><div id="waitBar" class="progress-bar"></div></div>
            <p style="font-size:16px;">대기 순서: <span id="waitCount" style="color:var(--en-red); font-weight:bold;">124,502</span>번</p>
        </div>
    </div>

    <div id="stepDate" class="screen">
        <header>날짜 선택</header>
        <div style="padding: 20px;">
            <div style="background:#222; padding:15px; border-radius:10px; margin-bottom:10px; border-left: 5px solid var(--en-red);" onclick="selectDate('10.24(금) 7PM')">
                <b>2025.10.24 (금) 19:00</b><br><small>잔여: 1,204석</small>
            </div>
            <div style="background:#222; padding:15px; border-radius:10px; border-left: 5px solid var(--en-red);" onclick="selectDate('10.25(토) 6PM')">
                <b>2025.10.25 (토) 18:00</b><br><small>잔여: 852석</small>
            </div>
            <p style="font-size:12px; color:#666; margin-top:20px;">* 10월 26일 회차는 매진되었습니다.</p>
        </div>
    </div>

    <div id="stepCaptcha" class="screen">
        <header>안심예매</header>
        <div style="text-align: center; padding: 20px;">
            <div class="captcha-box" id="captchaText">EN7HYP</div>
            <p style="font-size: 13px; color: #888;">부정 예매 방지를 위해 문자를 입력하세요.</p>
            <input type="text" id="captchaInput" class="en-input" placeholder="대문자 입력">
            <button class="btn-main active-btn" onclick="checkCaptcha()">입력 완료</button>
        </div>
    </div>

    <div id="stepMap" class="screen">
        <header>구역 선택 <span onclick="go('step1')">✕</span></header>
        <div class="stage">STAGE</div>
        <div class="seat-map-container">
            <p style="font-size:11px; color:#888;">좌측: F1/F3 | 우측: F2/F4</p>
            <div class="area-grid">
                <div class="area-box sc-zone" onclick="goPodo('F1 (Sound Check)', 220000)">F1</div>
                <div class="area-box sc-zone" onclick="goPodo('F2 (Sound Check)', 220000)">F2</div>
                <div class="area-box mg-zone" onclick="goPodo('102 (M&G)', 253000)">102</div>
                <div class="area-box mg-zone" onclick="goPodo('114 (M&G)', 253000)">114</div>
                <div class="area-box gen-zone" onclick="goPodo('205 (일반)', 165000)">205</div>
                <div class="area-box gen-zone" onclick="goPodo('211 (일반)', 165000)">211</div>
            </div>
        </div>
    </div>

    <div id="stepPodo" class="screen">
        <header><span id="podoTitle">구역</span> <span onclick="go('stepMap')">✕</span></header>
        <div style="background:#222; padding:10px; font-size:12px; display:flex; justify-content:space-between;">
            <span>가격: <span id="podoPrice">0</span>원</span>
            <span>잔여: <b style="color:var(--podo);">5석</b></span>
        </div>
        <div class="podo-grid" id="podoGrid"></div>
        <p style="text-align:center; font-size:12px; color:#555;">이미 선택된 좌석은 회색으로 표시됩니다.</p>
    </div>

    <div id="stepPay" class="screen">
        <header>결제하기</header>
        <div style="padding: 20px;">
            <div style="background:#222; padding:20px; border-radius:10px;">
                <p>선택공연: <br><b id="finalDate">날짜</b></p>
                <p>선택좌석: <br><b id="finalSeat">좌석정보</b></p>
                <hr style="border:0; border-top:1px solid #444;">
                <p>티켓금액: <span class="price-tag" id="finalPrice">0원</span></p>
            </div>
            <button class="btn-main active-btn" style="margin-top:20px;" onclick="finish()">무통장 입금하기</button>
        </div>
    </div>
</div>

<script>
    /* 타이머 로직 */
    setInterval(() => {
        const now = new Date();
        const s = now.getSeconds();
        const ms = Math.floor(now.getMilliseconds()/100);
        document.getElementById('timer').innerText = now.toTimeString().split(' ')[0] + ":" + ms;
        const btn = document.getElementById('mainBtn');
        if(s === 0) { btn.classList.add('active-btn'); btn.disabled = false; }
        else { btn.classList.remove('active-btn'); btn.disabled = true; }
    }, 100);

    function go(id) {
        document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
    }

    /* 대기열 로직 */
    function goWait() {
        go('stepWait');
        let count = 124502;
        let prog = 0;
        const iv = setInterval(() => {
            count -= Math.floor(Math.random() * 1500) + 500;
            prog += 1.5;
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
        else alert("문자가 일치하지 않습니다.");
    }

    /* 좌석 생성 및 이선좌 로직 */
    let currentPrice = 0;
    function goPodo(name, price) {
        document.getElementById('podoTitle').innerText = name;
        currentPrice = price;
        const grid = document.getElementById('podoGrid');
        grid.innerHTML = '';
        go('stepPodo');
        
        for(let i=0; i<80; i++) {
            const p = document.createElement('div');
            const isPodo = Math.random() < 0.05; // 5% 확률로 포도알 등장
            p.className = 'podo ' + (isPodo ? 'purple' : 'gray');
            p.onclick = function() {
                if(this.classList.contains('purple')) {
                    if(Math.random() < 0.7) { // 70% 확률로 이선좌 발생
                        alert("이미 선택된 좌석입니다.");
                        this.className = 'podo gray';
                    } else {
                        document.getElementById('finalDate').innerText = selDate;
                        document.getElementById('finalSeat').innerText = name + "구역 " + (i+1) + "번";
                        document.getElementById('finalPrice').innerText = price.toLocaleString() + "원";
                        go('stepPay');
                    }
                }
            };
            grid.appendChild(p);
        }
    }

    function finish() {
        alert("🎉 축하합니다! ENHYPEN 콘서트 티켓팅에 성공하셨습니다!\n입금 기한을 확인해주세요.");
        location.reload();
    }
</script>
</body>
