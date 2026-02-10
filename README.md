<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>제60회 ET전 항해 - 이화여자대학교 교육공학과</title>
    <link href="https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;700&family=Nanum+Myeongjo:wght@400;700&display=swap" rel="stylesheet">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Outfit', -apple-system, BlinkMacSystemFont, sans-serif;
            overflow: hidden;
            background: linear-gradient(135deg, #4A90A4 0%, #87CEEB 25%, #E6E6AA 75%, #F0E68C 100%);
            height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .leaflet-container {
            width: 100%;
            max-width: 900px;
            height: 90vh;
            max-height: 700px;
            position: relative;
            perspective: 2000px;
            margin: 0 auto;
        }

        .page {
            position: absolute;
            width: 100%;
            height: 100%;
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border-radius: 16px;
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            padding: 60px;
            transition: all 0.8s cubic-bezier(0.4, 0, 0.2, 1);
            opacity: 0;
            transform: translateX(100px) scale(0.9);
            pointer-events: none;
            overflow-y: auto;
        }

        .page.cover {
            background: transparent;
            backdrop-filter: none;
            box-shadow: none;
        }

        .page.active {
            opacity: 1;
            transform: translateX(0) scale(1);
            pointer-events: auto;
        }

        .page.prev {
            opacity: 0;
            transform: translateX(-100px) scale(0.9);
        }

        /* Page 1 - Cover */
        .cover {
            text-align: center;
        }

        .cover .main-title {
            font-family: 'Nanum Myeongjo', serif;
            font-size: 140px;
            font-weight: 700;
            letter-spacing: -4px;
            margin-bottom: 40px;
            color: #4A90A4;
            text-shadow: 0 4px 20px rgba(74, 144, 164, 0.3);
        }

        .cover .university {
            font-size: 16px;
            letter-spacing: 1px;
            color: #2C5F6F;
            margin-bottom: 8px;
            font-weight: 400;
        }

        .cover .event-title {
            font-size: 24px;
            font-weight: 600;
            color: #2C5F6F;
            margin-bottom: 16px;
            letter-spacing: -0.5px;
        }

        .cover .subtitle {
            font-size: 14px;
            letter-spacing: 4px;
            color: rgba(44, 95, 111, 0.7);
            margin-bottom: 60px;
            font-weight: 300;
        }

        /* Page 2 - About */
        .about {
            text-align: left;
            max-width: 700px;
        }

        .about h2 {
            font-size: 36px;
            font-weight: 600;
            margin-bottom: 30px;
            color: #2C5F6F;
            letter-spacing: -1px;
            text-align: center;
        }

        .about .description {
            font-size: 15px;
            line-height: 1.9;
            color: #555;
            font-weight: 300;
            text-align: left;
        }

        /* Page 3 - Floor Plan */
        .floorplan {
            padding: 40px;
            width: 100%;
            height: 100%;
            display: flex;
            flex-direction: column;
        }

        .floorplan h2 {
            font-size: 36px;
            font-weight: 600;
            margin-bottom: 30px;
            color: #2C5F6F;
            letter-spacing: -1px;
            text-align: center;
        }

        .floor-container {
            flex: 1;
            display: flex;
            align-items: center;
            justify-content: center;
            position: relative;
        }

        .floor-map {
            width: 600px;
            height: 400px;
            position: relative;
            border: 2px solid #4A90A4;
            background: rgba(255, 255, 255, 0.7);
        }

        .booth {
            position: absolute;
            border: 2px solid #4A90A4;
            background: rgba(255, 255, 255, 0.8);
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 12px;
            font-weight: 600;
            color: #2C5F6F;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .booth.lid-unlocked {
            background: #286E7E;
            color: white;
        }

        .booth.rnd-unlocked {
            background: #453171;
            color: white;
        }

        .booth.hrd-unlocked {
            background: #C70757;
            color: white;
        }

        .booth:hover {
            transform: scale(1.05);
            box-shadow: 0 4px 12px rgba(74, 144, 164, 0.3);
        }

        .special-booth {
            background: linear-gradient(135deg, rgba(74, 144, 164, 0.2) 0%, rgba(240, 230, 140, 0.2) 100%);
            border-color: #F0E68C;
            font-size: 11px;
        }

        .entrance {
            background: linear-gradient(135deg, rgba(74, 144, 164, 0.15) 0%, rgba(240, 230, 140, 0.15) 100%);
            border-color: #4A90A4;
            border-style: dashed;
        }

        /* Navigation */
        .navigation {
            position: absolute;
            bottom: 30px;
            left: 50%;
            transform: translateX(-50%);
            display: flex;
            gap: 40px;
            align-items: center;
            z-index: 100;
        }

        .nav-button {
            width: 48px;
            height: 48px;
            border: 2px solid rgba(74, 144, 164, 0.3);
            background: rgba(255, 255, 255, 0.8);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.3s ease;
            font-size: 20px;
            color: #4A90A4;
        }

        .nav-button:hover:not(.disabled) {
            background: #4A90A4;
            color: white;
            border-color: #4A90A4;
            transform: scale(1.1);
        }

        .nav-button.disabled {
            opacity: 0.3;
            cursor: not-allowed;
        }

        .page-indicator {
            display: flex;
            gap: 8px;
        }

        .dot {
            width: 8px;
            height: 8px;
            border-radius: 50%;
            background: rgba(74, 144, 164, 0.3);
            transition: all 0.3s ease;
        }

        .dot.active {
            background: #4A90A4;
            width: 24px;
            border-radius: 4px;
        }

        /* Modal */
        .modal {
            display: none;
            position: fixed;
            z-index: 1000;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.5);
            align-items: center;
            justify-content: center;
        }

        .modal.show {
            display: flex;
        }

        .modal-content {
            background: white;
            padding: 40px;
            border-radius: 16px;
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
            text-align: center;
        }

        .modal-content h3 {
            color: #2C5F6F;
            margin-bottom: 20px;
            font-size: 24px;
        }

        .modal-content input {
            width: 200px;
            padding: 12px;
            border: 2px solid #4A90A4;
            border-radius: 8px;
            font-size: 18px;
            text-align: center;
            margin-bottom: 20px;
        }

        .modal-buttons {
            display: flex;
            gap: 10px;
            justify-content: center;
        }

        .modal-button {
            padding: 10px 24px;
            border: none;
            border-radius: 8px;
            font-size: 14px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .modal-button.confirm {
            background: #4A90A4;
            color: white;
        }

        .modal-button.cancel {
            background: #ddd;
            color: #666;
        }

        .modal-button:hover {
            transform: translateY(-2px);
        }

        @media (max-width: 768px) {
            .leaflet-container {
                height: 95vh;
                max-width: 95%;
            }

            .page {
                padding: 30px 20px;
            }

            .cover .main-title {
                font-size: 80px;
            }

            .about h2, .floorplan h2 {
                font-size: 28px;
            }

            .floor-map {
                width: 100%;
                height: 300px;
            }

            .booth {
                font-size: 10px;
            }
        }
    </style>
</head>
<body>
    <div class="leaflet-container">
        <!-- Page 1: Cover -->
        <div class="page active cover">
            <div class="main-title">항해</div>
            <div class="university">이화여자대학교 교육공학과</div>
            <div class="event-title">제60회 ET전</div>
            <div class="subtitle">EDUCATIONAL TECHNOLOGY EXHIBITION</div>
        </div>

        <!-- Page 2: About -->
        <div class="page about">
            <h2>주요 상징 요소</h2>
            <div class="description">
                제60회 ET전 《항해》의 주요 상징 요소는 배, 파도, 그리고 나침반입니다.<br><br>

                이 세 요소는 전환의 시대를 마주한 우리와, 우리가 서 있는 위치, 그리고 교육공학의 역할을 상징합니다.<br><br>

                전시장의 중심에 새겨진 파도는 단순한 자연물이 아니라,
                사회적인 사건, 기술의 변화, 애플리케이션이나 기업의 로고 등 
                전환의 시대를 만들어낸 수많은 요소들을 모아 구성된 이미지입니다.<br><br>

                그 파도 위에 놓인 배는 변화의 흐름 속에서 방향을 고민하는 
                학습자와 교수자, 그리고 조직의 모습을 담고 있습니다.<br><br>

                이러한 혼란 속에서 나침반은 방향을 읽고, 어디로 나아갈지를 설계하는 교육공학의 역할로 비유하였습니다.
            </div>
        </div>

        <!-- Page 3: Floor Plan -->
        <div class="page floorplan">
            <h2>전시장 배치도</h2>
            <div class="floor-container">
                <div class="floor-map" id="floorMap">
                    <!-- Will be populated by JavaScript -->
                </div>
            </div>
        </div>

        <!-- Navigation -->
        <div class="navigation">
            <button class="nav-button prev-btn disabled" id="prevBtn">←</button>
            <div class="page-indicator">
                <div class="dot active"></div>
                <div class="dot"></div>
                <div class="dot"></div>
            </div>
            <button class="nav-button next-btn" id="nextBtn">→</button>
        </div>
    </div>

    <!-- Password Modal -->
    <div class="modal" id="passwordModal">
        <div class="modal-content">
            <h3 id="modalTitle">비밀번호를 입력하세요</h3>
            <input type="password" id="passwordInput" maxlength="4" placeholder="****">
            <div class="modal-buttons">
                <button class="modal-button confirm" id="confirmBtn">확인</button>
                <button class="modal-button cancel" id="cancelBtn">취소</button>
            </div>
        </div>
    </div>

    <script>
        let currentPage = 0;
        const totalPages = 3;
        const pages = document.querySelectorAll('.page');
        const dots = document.querySelectorAll('.dot');
        const prevBtn = document.getElementById('prevBtn');
        const nextBtn = document.getElementById('nextBtn');
        
        const modal = document.getElementById('passwordModal');
        const passwordInput = document.getElementById('passwordInput');
        const confirmBtn = document.getElementById('confirmBtn');
        const cancelBtn = document.getElementById('cancelBtn');
        let currentBooth = null;

        // Unlocked booths state
        const unlockedBooths = new Set();

        function updatePage() {
            pages.forEach((page, index) => {
                page.classList.remove('active', 'prev');
                if (index === currentPage) {
                    page.classList.add('active');
                } else if (index < currentPage) {
                    page.classList.add('prev');
                }
            });

            dots.forEach((dot, index) => {
                dot.classList.toggle('active', index === currentPage);
            });

            prevBtn.classList.toggle('disabled', currentPage === 0);
            nextBtn.classList.toggle('disabled', currentPage === totalPages - 1);
        }

        prevBtn.addEventListener('click', () => {
            if (currentPage > 0) {
                currentPage--;
                updatePage();
            }
        });

        nextBtn.addEventListener('click', () => {
            if (currentPage < totalPages - 1) {
                currentPage++;
                updatePage();
            }
        });

        // Keyboard navigation
        document.addEventListener('keydown', (e) => {
            if (e.key === 'ArrowLeft' && currentPage > 0) {
                currentPage--;
                updatePage();
            } else if (e.key === 'ArrowRight' && currentPage < totalPages - 1) {
                currentPage++;
                updatePage();
            }
        });

        // Floor map creation
        function createFloorMap() {
            const floorMap = document.getElementById('floorMap');
            
            const booths = [
                // 왼쪽 면 (아래→위): LID1~LID4
                { name: 'LID1', left: 10, top: 320, width: 60, height: 60, type: 'lid' },
                { name: 'LID2', left: 10, top: 240, width: 60, height: 60, type: 'lid' },
                { name: 'LID3', left: 10, top: 160, width: 60, height: 60, type: 'lid' },
                { name: 'LID4', left: 10, top: 80, width: 60, height: 60, type: 'lid' },
                
                // 위 면 (왼쪽→오른쪽): RND1~RND4
                { name: 'RND1', left: 90, top: 10, width: 60, height: 60, type: 'rnd' },
                { name: 'RND2', left: 170, top: 10, width: 60, height: 60, type: 'rnd' },
                { name: 'RND3', left: 250, top: 10, width: 60, height: 60, type: 'rnd' },
                { name: 'RND4', left: 330, top: 10, width: 60, height: 60, type: 'rnd' },
                
                // 오른쪽 면 (위→아래): HRD1~HRD3
                { name: 'HRD1', left: 530, top: 150, width: 60, height: 60, type: 'hrd' },
                { name: 'HRD2', left: 530, top: 230, width: 60, height: 60, type: 'hrd' },
                { name: 'HRD3', left: 530, top: 310, width: 60, height: 60, type: 'hrd' },
            ];

            const specialBooths = [
                // 중앙: 배움을 항해
                { name: '배움을<br>항해', left: 250, top: 180, width: 100, height: 100, special: true },
                // ㄱ자 책상: 가능성을 항해 - 수평 부분
                { name: '가능성을 항해', left: 400, top: 10, width: 120, height: 40, special: true, isLShape: true },
                // ㄱ자 책상: 가능성을 항해 - 수직 부분
                { name: '', left: 480, top: 50, width: 40, height: 100, special: true, isLShape: true },
                // 입구 오른쪽: 전환을 항해
                { name: '전환을<br>항해', left: 120, top: 320, width: 300, height: 60, special: true }
            ];

            const entrance = { left: 80, top: 390, width: 80, height: 10 };

            // Create regular booths
            booths.forEach(booth => {
                const div = document.createElement('div');
                div.className = 'booth';
                div.innerHTML = booth.name;
                div.style.left = booth.left + 'px';
                div.style.top = booth.top + 'px';
                div.style.width = booth.width + 'px';
                div.style.height = booth.height + 'px';
                div.dataset.name = booth.name;
                div.dataset.type = booth.type;
                
                div.addEventListener('click', () => openPasswordModal(booth.name, div));
                floorMap.appendChild(div);
            });

            // Create special booths
            specialBooths.forEach(booth => {
                const div = document.createElement('div');
                div.className = 'booth special-booth';
                div.innerHTML = booth.name;
                div.style.left = booth.left + 'px';
                div.style.top = booth.top + 'px';
                div.style.width = booth.width + 'px';
                div.style.height = booth.height + 'px';
                if (booth.isLShape) {
                    div.style.fontSize = '10px';
                    div.style.display = 'flex';
                    div.style.alignItems = 'center';
                    div.style.justifyContent = 'center';
                }
                floorMap.appendChild(div);
            });

            // Create entrance
            const entranceDiv = document.createElement('div');
            entranceDiv.className = 'booth entrance';
            entranceDiv.innerHTML = '입구';
            entranceDiv.style.left = entrance.left + 'px';
            entranceDiv.style.top = entrance.top + 'px';
            entranceDiv.style.width = entrance.width + 'px';
            entranceDiv.style.height = entrance.height + 'px';
            floorMap.appendChild(entranceDiv);
        }

        function openPasswordModal(boothName, boothElement) {
            if (unlockedBooths.has(boothName)) {
                return; // Already unlocked
            }
            
            currentBooth = { name: boothName, element: boothElement };
            modal.classList.add('show');
            passwordInput.value = '';
            passwordInput.focus();
        }

        function closeModal() {
            modal.classList.remove('show');
            currentBooth = null;
            passwordInput.value = '';
        }

        confirmBtn.addEventListener('click', () => {
            const password = passwordInput.value;
            if (password === '1963' && currentBooth) {
                unlockedBooths.add(currentBooth.name);
                const teamType = currentBooth.element.dataset.type;
                currentBooth.element.classList.add(`${teamType}-unlocked`);
                closeModal();
            } else if (password !== '' && password !== '1963') {
                // 비밀번호가 틀렸을 때
                passwordInput.value = '';
                passwordInput.style.borderColor = 'red';
                setTimeout(() => {
                    passwordInput.style.borderColor = '#4A90A4';
                }, 500);
            }
        });

        cancelBtn.addEventListener('click', closeModal);

        passwordInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') {
                confirmBtn.click();
            }
        });

        modal.addEventListener('click', (e) => {
            if (e.target === modal) {
                closeModal();
            }
        });

        // Initialize floor map
        createFloorMap();
    </script>
</body>
</html>
