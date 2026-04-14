<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Книга пам'яті Героїв України</title>
    <style>
        :root {
            --gold: #d4af37;
            --accent-blue: #0057b7; 
            --page-width: 480px;
            --page-height: 680px;
            --animation-speed: 1s;
        }

        * {
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
        }

        body {
            margin: 0;
            padding: 10px;
            min-height: 100vh;
            background: #f4f4f4;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            font-family: 'Times New Roman', serif;
            perspective: 2500px;
        }

        .header-title {
            color: var(--accent-blue);
            margin-bottom: 25px;
            text-align: left;
            text-transform: uppercase;
            font-size: 1.6rem;
            font-weight: 900;
            letter-spacing: 1px;
            width: calc(var(--page-width) * 2);
            max-width: 95vw;
            border-bottom: 3px solid var(--gold);
            padding-bottom: 10px;
        }

        .book-viewport {
            position: relative;
            width: calc(var(--page-width) * 2);
            height: var(--page-height);
            display: flex;
            transition: transform 0.8s ease;
        }

        .book-viewport.at-start { transform: translateX(-25%); }
        .book-viewport.at-end { transform: translateX(25%); }

        .book {
            position: relative;
            width: 100%;
            height: 100%;
            transform-style: preserve-3d;
        }

        .page {
            position: absolute;
            width: 50%;
            height: 100%;
            top: 0;
            right: 0;
            transform-origin: left;
            transform-style: preserve-3d;
            transition: transform var(--animation-speed) cubic-bezier(0.645, 0.045, 0.355, 1);
            cursor: pointer;
        }

        .page.flipped { transform: rotateY(-180deg); }

        .front, .back {
            position: absolute;
            width: 100%;
            height: 100%;
            top: 0;
            left: 0;
            backface-visibility: hidden;
            background: #fdfcf7;
            padding: 35px;
            border: 1px solid #ddd;
            display: flex;
            flex-direction: column;
            box-shadow: inset 0 0 50px rgba(0,0,0,0.03), 10px 0 20px rgba(0,0,0,0.1);
        }

        .back { border-radius: 8px 0 0 8px; transform: rotateY(180deg); }
        .front { border-radius: 0 8px 8px 0; }

        .cover-front { background-image: url('assets/cover.png'); background-size: cover; background-position: center; border: none; padding: 0; }
        .cover-back { background-image: url('assets/end.png'); background-size: cover; background-position: center; border: none; padding: 0; }

        .hero-profile { display: flex; flex-direction: column; align-items: center; text-align: center; height: 100%; }
        .hero-photo-frame {
            width: 260px; height: 340px; border: 1px solid #ccc; padding: 8px; background: #f0f0f0;
            margin-bottom: 20px; box-shadow: 0 5px 15px rgba(0,0,0,0.1); display: flex; align-items: center;
            justify-content: center; overflow: hidden; position: relative;
        }
        .hero-photo { width: 100%; height: 100%; background-size: cover; background-position: center; z-index: 2; }
        .photo-placeholder { position: absolute; width: 60px; height: 60px; opacity: 0.2; z-index: 1; }

        .hero-name-label {
            font-size: 1.3rem; font-weight: 800; color: #000; text-transform: uppercase;
            margin-bottom: 10px; width: 100%; border-bottom: 2px solid var(--accent-blue);
            padding-bottom: 10px; letter-spacing: 0.5px;
        }

        .info-group { width: 100%; margin-top: 5px; }
        .info-item { margin-bottom: 12px; border-bottom: 1px dotted #ccc; padding-bottom: 4px; }
        .info-label { color: var(--accent-blue); font-weight: bold; font-size: 0.8rem; text-transform: uppercase; display: block; }
        .info-value { font-size: 1rem; color: #111; }

        .page-title { color: var(--accent-blue); border-bottom: 2px solid var(--gold); padding-bottom: 10px; margin-top: 0; text-transform: uppercase; font-size: 1.5rem; }
        .bio-text { font-size: 0.95rem; line-height: 1.6; text-align: justify; color: #333; }

        .controls { margin-top: 40px; display: flex; gap: 12px; align-items: center; }
        .nav-btn { padding: 12px 30px; border: none; cursor: pointer; font-weight: bold; color: white; text-transform: uppercase; font-size: 0.8rem; transition: 0.3s; }
        #prevBtn { background: #c2c2c2; border-radius: 20px 5px 5px 20px; }
        #nextBtn { background: var(--accent-blue); border-radius: 5px 20px 20px 5px; }
        .nav-btn:disabled { opacity: 0.3; cursor: not-allowed; }
        .page-counter { font-weight: bold; color: #666; font-family: sans-serif; }

        .memorial-candle { margin-top: auto; align-self: center; width: 60px; display: block; }

        @media (max-width: 950px) {
            :root { --page-width: 90vw; --page-height: 80vh; }
            .book-viewport { width: var(--page-width); }
            .book-viewport.at-start, .book-viewport.at-end { transform: translateX(0); }
            .page { width: 100%; right: 0; }
        }
    </style>
</head>
<body>

    <h1 class="header-title">Книга пам'яті Героїв України</h1>

    <div class="book-viewport at-start" id="viewport">
        <div class="book" id="bookContainer">
            <!-- Сторінки генеруються скриптом -->
        </div>
    </div>

    <div class="controls">
        <button id="prevBtn" class="nav-btn">← Назад</button>
        <span class="page-counter" id="counter">1 / 1</span>
        <button id="nextBtn" class="nav-btn">Вперед →</button>
    </div>

    <script>
        // ДАНІ ГЕРОЇВ - додавайте нових героїв за цим зразком
        const heroes = [
{
                name: "Святозар Чопей",
                birth: "25.02.1989",
                death: "01.04.2026",
                rank: "Захисник України",
                unit: "46-та окрема аеромобільна Подільська бригада ДШВ ЗСУ",
                photo: "assets/hero1.svg", 
                bio: "Випускник ВСП «Фаховий коледж інфокомунікацій НУ «Львівська політехніка». Був учасником АТО та ООС. З початком повномасштабного вторгнення став на захист Батьківщини. Боронив територіальну цілісність на Луганському та Запорізькому напрямках. Загинув під час виконання бойового завдання. Педагогічний колектив та студентська спільнота висловлюють щирі співчуття рідним. Світла пам'ять Герою!"
            },
            {
                                name: "Прізвище Ім'я По батькові",
                birth: "ХХ.ХХ.ХХХХ",
                death: "ХХ.ХХ.ХХХХ",
                rank: "Звання",
                unit: "Підрозділ",
                photo: "assets/hero2.svg", 
                bio: "Біографія героя"
            },
                        {
                name: "Прізвище Ім'я По батькові",
                birth: "ХХ.ХХ.ХХХХ",
                death: "ХХ.ХХ.ХХХХ",
                rank: "Звання",
                unit: "Підрозділ",
                photo: "assets/hero3.svg", 
                bio: "Біографія героя"
            },
            {
                name: "Прізвище Ім'я По батькові",
                birth: "ХХ.ХХ.ХХХХ",
                death: "ХХ.ХХ.ХХХХ",
                rank: "Звання",
                unit: "Підрозділ",
                photo: "assets/hero4.svg", 
                bio: "Біографія героя"
            }
        ];

        const bookContainer = document.getElementById('bookContainer');
        const viewport = document.getElementById('viewport');
        const prevBtn = document.getElementById('prevBtn');
        const nextBtn = document.getElementById('nextBtn');
        const counter = document.getElementById('counter');

        function initBook() {
            let html = `
                <!-- ПЕРША ПАРА: ОБКЛАДИНКА ТА ПЕРЕДМОВА -->
                <div class="page" style="z-index: ${heroes.length * 2 + 10}">
                    <div class="front cover-front"></div>
                    <div class="back">
                        <h2 class="page-title">Передмова</h2>
                        <p class="bio-text">Ця книга — живий літопис мужності. Вона зберігає імена тих, хто став щитом для України. Кожна сторінка — це ціле життя, віддане за наш мир і свободу. Ми пам'ятаємо кожного.</p>
                        <img src="assets/candle.svg" class="memorial-candle" onerror="this.style.display='none'">
                    </div>
                </div>
            `;

            // Додаємо першого героя з цитатою
            if (heroes.length > 0) {
                const firstHero = heroes[0];
                const zIndex = heroes.length * 2;
                html += `
                    <!-- ПЕРШИЙ ГЕРОЙ: ЦИТАТА ТА ПРОФІЛЬ -->
                    <div class="page" style="z-index: ${zIndex}">
                        <div class="front">
                            <div style="height:100%; display:flex; align-items:center; justify-content:center; padding: 40px; font-style: italic; color: #555; text-align: center;">
                                "Герої не вмирають, доки ми про них пам'ятаємо."
                            </div>
                        </div>
                        <div class="back">
                            <div class="hero-profile">
                                <div class="hero-photo-frame">
                                    <svg class="photo-placeholder" viewBox="0 0 24 24" fill="currentColor"><path d="M12 12c2.21 0 4-1.79 4-4s-1.79-4-4-4-4 1.79-4 4 1.79 4 4 4zm0 2c-2.67 0-8 1.34-8 4v2h16v-2c0-2.66-5.33-4-8-4z"/></svg>
                                    <div class="hero-photo" style="background-image: url('${firstHero.photo}')"></div>
                                </div>
                                <div class="hero-name-label">${firstHero.name}</div>
                                <div class="info-group">
                                    <div class="info-item"><span class="info-label">Дата народження</span><span class="info-value">${firstHero.birth}</span></div>
                                    <div class="info-item"><span class="info-label">Дата загибелі</span><span class="info-value">${firstHero.death}</span></div>
                                    <div class="info-item"><span class="info-label">Звання</span><span class="info-value">${firstHero.rank}</span></div>
                                    <div class="info-item"><span class="info-label">Підрозділ</span><span class="info-value">${firstHero.unit}</span></div>
                                </div>
                            </div>
                        </div>
                    </div>
                `;
            }

            // ОСНОВНИЙ ЦИКЛ: для кожного героя додаємо його біографію + профіль наступного героя (або обкладинку)
            heroes.forEach((hero, index) => {
                const zIndex = (heroes.length - index) * 2 - (index === 0 ? 1 : 0);
                html += `
                    <!-- ГЕРОЙ ${index + 1}: БІОГРАФІЯ + ${index < heroes.length - 1 ? 'ГЕРОЙ ' + (index + 2) : 'ОБКЛАДИНКА'} -->
                    <div class="page" style="z-index: ${zIndex}">
                        <div class="front">
                            <h2 class="page-title">Біографія</h2>
                            <div class="bio-text"><p>${hero.bio}</p></div>
                        </div>
                        <div class="back ${index === heroes.length - 1 ? 'cover-back' : ''}">
                            ${index < heroes.length - 1 ? `
                                <div class="hero-profile">
                                    <div class="hero-photo-frame">
                                        <svg class="photo-placeholder" viewBox="0 0 24 24" fill="currentColor"><path d="M12 12c2.21 0 4-1.79 4-4s-1.79-4-4-4-4 1.79-4 4 1.79 4 4 4zm0 2c-2.67 0-8 1.34-8 4v2h16v-2c0-2.66-5.33-4-8-4z"/></svg>
                                        <div class="hero-photo" style="background-image: url('${heroes[index + 1].photo}')"></div>
                                    </div>
                                    <div class="hero-name-label">${heroes[index + 1].name}</div>
                                    <div class="info-group">
                                        <div class="info-item"><span class="info-label">Дата народження</span><span class="info-value">${heroes[index + 1].birth}</span></div>
                                        <div class="info-item"><span class="info-label">Дата загибелі</span><span class="info-value">${heroes[index + 1].death}</span></div>
                                        <div class="info-item"><span class="info-label">Звання</span><span class="info-value">${heroes[index + 1].rank}</span></div>
                                        <div class="info-item"><span class="info-label">Підрозділ</span><span class="info-value">${heroes[index + 1].unit}</span></div>
                                    </div>
                                </div>
                            ` : ''}
                        </div>
                    </div>
                `;
            });

            bookContainer.innerHTML = html;
        }

        initBook();

        const pages = document.querySelectorAll('.page');
        let currentLoc = 0;
        let totalPages = pages.length;

        function updateUI() {
            const isMobile = window.innerWidth <= 950;
            if (!isMobile) {
                viewport.classList.remove('at-start', 'at-end');
                if (currentLoc === 0) viewport.classList.add('at-start');
                else if (currentLoc === totalPages) viewport.classList.add('at-end');
            }
            prevBtn.disabled = (currentLoc === 0);
            nextBtn.disabled = (currentLoc === totalPages);
            
            // Оновлення лічильника (відображаємо номер розвороту)
            counter.innerText = `${Math.ceil((currentLoc + 1) / 2)} / ${Math.ceil((totalPages + 1) / 2)}`;

            pages.forEach((page, index) => {
                page.style.zIndex = (index < currentLoc) ? index + 1 : totalPages - index + 1;
            });
        }

        nextBtn.addEventListener('click', () => {
            if (currentLoc < totalPages) {
                pages[currentLoc].classList.add('flipped');
                currentLoc++;
                updateUI();
            }
        });

        prevBtn.addEventListener('click', () => {
            if (currentLoc > 0) {
                currentLoc--;
                pages[currentLoc].classList.remove('flipped');
                updateUI();
            }
        });

        window.addEventListener('resize', updateUI);
        updateUI();
        
    </script>
</body>
</html>
