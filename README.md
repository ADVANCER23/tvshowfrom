<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FROM - Все серии</title>
    <!-- Подключаем официальный плеер MUX -->
    <script src="https://cdn.jsdelivr.net/npm/@mux/mux-player@latest"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: linear-gradient(135deg, #0f0c29 0%, #302b63 50%, #24243e 100%);
            min-height: 100vh;
            color: #fff;
            padding: 20px;
        }
        .header {
            text-align: center;
            margin-bottom: 40px;
            padding-top: 20px;
        }
        .header h1 {
            font-size: 3.5em;
            margin-bottom: 10px;
            letter-spacing: 5px;
            text-shadow: 0 0 20px rgba(255,255,255,0.3);
        }
        .header p {
            font-size: 1.2em;
            opacity: 0.8;
            letter-spacing: 2px;
        }
        .container {
            max-width: 1200px;
            margin: 0 auto;
        }
        .episodes-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 20px;
        }
        .episode-card {
            background-color: rgba(255, 255, 255, 0.05);
            border-radius: 12px;
            overflow: hidden;
            cursor: pointer;
            transition: all 0.3s ease;
            border: 1px solid rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
        }
        .episode-card:hover {
            transform: translateY(-5px);
            background-color: rgba(255, 255, 255, 0.1);
            border-color: rgba(255, 255, 255, 0.3);
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
        }
        .episode-thumbnail {
            width: 100%;
            aspect-ratio: 16/9;
            position: relative;
            overflow: hidden;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .episode-thumbnail img {
            width: 100%;
            height: 100%;
            object-fit: cover;
            transition: transform 0.3s ease;
        }
        .episode-card:hover .episode-thumbnail img {
            transform: scale(1.1);
        }
        .thumbnail-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(to bottom, rgba(0,0,0,0) 0%, rgba(0,0,0,0.7) 100%);
            display: flex;
            align-items: center;
            justify-content: center;
            transition: background 0.3s ease;
        }
        .episode-card:hover .thumbnail-overlay {
            background: linear-gradient(to bottom, rgba(0,0,0,0.2) 0%, rgba(0,0,0,0.9) 100%);
        }
        .play-icon {
            font-size: 4em;
            opacity: 0.9;
            transition: all 0.3s ease;
            text-shadow: 0 4px 20px rgba(0,0,0,0.8);
        }
        .episode-card:hover .play-icon {
            opacity: 1;
            transform: scale(1.2);
        }
        .episode-info {
            padding: 15px 20px;
        }
        .episode-number {
            font-size: 0.85em;
            color: #a0a0a0;
            margin-bottom: 5px;
            text-transform: uppercase;
            letter-spacing: 1px;
        }
        .episode-title {
            font-size: 1.1em;
            font-weight: 600;
            line-height: 1.3;
        }
        .placeholder-card {
            background-color: rgba(255, 255, 255, 0.03);
            border-radius: 12px;
            padding: 40px 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            border: 2px dashed rgba(255, 255, 255, 0.2);
            min-height: 200px;
            opacity: 0.6;
        }
        .question-mark {
            font-size: 4em;
            font-weight: bold;
            margin-bottom: 10px;
            color: #666;
        }
        .placeholder-text {
            font-size: 1.1em;
            color: #888;
            text-align: center;
        }
        .video-modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.95);
            z-index: 1000;
            justify-content: center;
            align-items: center;
            padding: 20px;
            backdrop-filter: blur(5px);
        }
        .video-modal.active {
            display: flex;
            animation: fadeIn 0.3s ease;
        }
        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }
        .video-container {
            position: relative;
            width: 100%;
            max-width: 1000px;
            background-color: #000;
            border-radius: 16px;
            overflow: hidden;
            box-shadow: 0 20px 60px rgba(0,0,0,0.8);
        }
        .video-container.fullscreen {
            max-width: 100%;
            border-radius: 0;
        }
        .modal-controls {
            position: absolute;
            top: 15px;
            right: 15px;
            display: flex;
            gap: 10px;
            z-index: 10;
        }
        .control-btn {
            background: rgba(255, 255, 255, 0.15);
            backdrop-filter: blur(10px);
            border: none;
            color: #fff;
            width: 45px;
            height: 45px;
            border-radius: 50%;
            font-size: 20px;
            cursor: pointer;
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .control-btn:hover {
            background: rgba(255, 255, 255, 0.3);
            transform: scale(1.1);
        }
        .control-btn.close-btn:hover {
            background: rgba(255, 68, 68, 0.9);
        }
        mux-player {
            width: 100%;
            aspect-ratio: 16/9;
            --controls: auto;
            --accent-color: #e50914;
        }
        .video-info-modal {
            padding: 20px;
            background: #111;
        }
        .video-info-modal h2 {
            font-size: 1.5em;
            margin-bottom: 5px;
        }
        .video-info-modal p {
            color: #888;
            font-size: 0.9em;
        }
        @media (max-width: 768px) {
            .episodes-grid {
                grid-template-columns: 1fr;
            }
            .header h1 {
                font-size: 2.5em;
            }
            .play-icon {
                font-size: 3em;
            }
            .modal-controls {
                top: 10px;
                right: 10px;
            }
            .control-btn {
                width: 40px;
                height: 40px;
                font-size: 18px;
            }
        }
    </style>
</head>
<body>
    <div class="header">
        <h1>FROM</h1>
        <p>SEASON 1 • ALL EPISODES</p>
    </div>
    <div class="container">
        <div class="episodes-grid">
            <!-- СЕРИЯ 1 -->
            <div class="episode-card" onclick="openVideo(1)">
                <div class="episode-thumbnail">
                    <img src="https://i.ibb.co/b50pkRN7/20260420144001d2050e9d65.jpg" alt="Episode 1" onerror="this.style.display='none'">
                    <div class="thumbnail-overlay">
                        <div class="play-icon">▶</div>
                    </div>
                </div>
                <div class="episode-info">
                    <div class="episode-number">Episode 01</div>
                    <div class="episode-title">The Arrival</div>
                </div>
            </div>
            <!-- СЕРИЯ 2 -->
            <div class="episode-card" onclick="openVideo(2)">
                <div class="episode-thumbnail">
                    <img src="https://i.ibb.co/bRr8zJ1L/51267580-8590651.jpg" alt="Episode 2" onerror="this.style.display='none'">
                    <div class="thumbnail-overlay">
                        <div class="play-icon">▶</div>
                    </div>
                </div>
                <div class="episode-info">
                    <div class="episode-number">Episode 02</div>
                    <div class="episode-title">Fray</div>
                </div>
            </div>
            <!-- СЕРИЯ 3 -->
            <div class="episode-card" onclick="openVideo(3)">
                <div class="episode-thumbnail">
                    <img src="https://i.ibb.co/xSGx64LX/cda11-17772896140489-1920.jpg" alt="Episode 3" onerror="this.style.display='none'">
                    <div class="thumbnail-overlay">
                        <div class="play-icon">▶</div>
                    </div>
                </div>
                <div class="episode-info">
                    <div class="episode-number">Episode 03</div>
                    <div class="episode-title">Merrily We Go</div>
                </div>
            </div>
            <!-- СЕРИЯ 4 -->
            <div class="episode-card" onclick="openVideo(4)">
                <div class="episode-thumbnail">
                    <img src="https://i.ibb.co/Y7SwvL0f/51267586-8832416.jpg" alt="Episode 4" onerror="this.style.display='none'">
                    <div class="thumbnail-overlay">
                        <div class="play-icon">▶</div>
                    </div>
                </div>
                <div class="episode-info">
                    <div class="episode-number">Episode 04</div>
                    <div class="episode-title">Of Myths and Monsters</div>
                </div>
            </div>
            <!-- СЕРИЯ 5 -->
            <div class="episode-card" onclick="openVideo(5)">
                <div class="episode-thumbnail">
                    <img src="https://i.ibb.co/jZf5SKs9/51312361-9086066.jpg" alt="Episode 5" onerror="this.style.display='none'">
                    <div class="thumbnail-overlay">
                        <div class="play-icon">▶</div>
                    </div>
                </div>
                <div class="episode-info">
                    <div class="episode-number">Episode 05</div>
                    <div class="episode-title">What A Long Strange Trip It's Been</div>
                </div>
            </div>
            <!-- СЕРИЯ 6 -->
            <div class="episode-card" onclick="openVideo(6)">
                <div class="episode-thumbnail">
                    <img src="https://i.ibb.co/HDPN3yCD/f497789b74536b3db4d9a8ddab016c3559e90180-1600x900.jpg" alt="Episode 6" onerror="this.style.display='none'">
                    <div class="thumbnail-overlay">
                        <div class="play-icon">▶</div>
                    </div>
                </div>
                <div class="episode-info">
                    <div class="episode-number">Episode 06</div>
                    <div class="episode-title">The Heart Is A Lonely Hunter</div>
                </div>
            </div>
            <!-- СЕРИЯ 7 -->
            <div class="episode-card" onclick="openVideo(7)">
                <div class="episode-thumbnail">
                    <img src="https://i.ibb.co/ksw69qw8/01kv5hygn5bvcn1e2y1g.jpg" alt="Episode 7" onerror="this.style.display='none'">
                    <div class="thumbnail-overlay">
                        <div class="play-icon">▶</div>
                    </div>
                </div>
                <div class="episode-info">
                    <div class="episode-number">Episode 07</div>
                    <div class="episode-title">Best Laid Plans</div>
                </div>
            </div>
            <!-- СЕРИЯ 8 -->
            <div class="episode-card" onclick="openVideo(8)">
                <div class="episode-thumbnail">
                    <img src="https://i.ibb.co/TxmsYvTR/FROM-S4-UT-409-251022-REACHR-05800-R-3000.jpg" alt="Episode 8" onerror="this.style.display='none'">
                    <div class="thumbnail-overlay">
                        <div class="play-icon">▶</div>
                    </div>
                </div>
                <div class="episode-info">
                    <div class="episode-number">Episode 08</div>
                    <div class="episode-title">Heavy Is The Head</div>
                </div>
            </div>
            <!-- СЕРИЯ 9 -->
            <div class="episode-card" onclick="openVideo(9)">
                <div class="episode-thumbnail">
                    <img src="https://i.ibb.co/KjwgXK7M/FROM-Season-4-Episode-9.jpg" alt="Episode 9" onerror="this.style.display='none'">
                    <div class="thumbnail-overlay">
                        <div class="play-icon">▶</div>
                    </div>
                </div>
                <div class="episode-info">
                    <div class="episode-number">Episode 09</div>
                    <div class="episode-title">The Calm Before</div>
                </div>
            </div>
            <!-- СЕРИЯ 10 (ЗАГЛУШКА) -->
            <div class="placeholder-card">
                <div class="question-mark">?</div>
                <div class="placeholder-text">Episode 10<br>Coming Soon</div>
            </div>
        </div>
    </div>
    <!-- МОДАЛЬНОЕ ОКНО С ПЛЕЕРОМ -->
    <div class="video-modal" id="videoModal">
        <div class="video-container" id="videoContainer">
            <div class="modal-controls">
                <button class="control-btn" onclick="toggleFullscreen()" title="Полный экран">⛶</button>
                <button class="control-btn close-btn" onclick="closeVideo()" title="Закрыть">✕</button>
            </div>
            <mux-player 
                id="muxPlayer" 
                stream-type="on-demand"
            ></mux-player>
            <div class="video-info-modal">
                <h2 id="videoTitle">Название серии</h2>
                <p id="videoNumber">Episode 01</p>
            </div>
        </div>
    </div>
    <script>
        const episodes = {
            1: { 
                title: "The Arrival", 
                playbackId: "se202kIsL7rzoF02jH7AZPGVvP7SEU00vJ5PBk2VE01IaAM",
                thumbnail: "https://image.tmdb.org/t/p/w500/from_s01e01.jpg"
            },
            2: { 
                title: "Fray", 
                playbackId: "dCIiF1J2wcTLWLOxAvxxwnMIj0099aub38ZMY2SP00FSI",
                thumbnail: "https://image.tmdb.org/t/p/w500/from_s01e02.jpg"
            },
            3: { 
                title: "Merrily We Go", 
                playbackId: "EqlWU2QmVteLmW021e00TKHYuyjrNufZ9E3qR0000H2phrg",
                thumbnail: "https://image.tmdb.org/t/p/w500/from_s01e03.jpg"
            },
            4: { 
                title: "Of Myths and Monsters", 
                playbackId: "00fvCAMg00iFwkxF6mbzxYs6HYS8Zn3sQa75CyrkNqO2Y",
                thumbnail: "https://image.tmdb.org/t/p/w500/from_s01e04.jpg"
            },
            5: { 
                title: "What A Long Strange Trip It's Been", 
                playbackId: "OdqctZEyf7gmarYjZvOJwNIFuSrx1502BcP67YOlsBxI",
                thumbnail: "https://image.tmdb.org/t/p/w500/from_s01e05.jpg"
            },
            6: { 
                title: "The Heart Is A Lonely Hunter", 
                playbackId: "qJ9aGSv32rdPgu4eCq02M02VloodtTs3stDUU01k3NUwo8",
                thumbnail: "https://image.tmdb.org/t/p/w500/from_s01e06.jpg"
            },
            7: { 
                title: "Best Laid Plans", 
                playbackId: "VX1X9ds4PJ028ADWZR7L7S74qWSVrEylEMqEpH01kGQjg",
                thumbnail: "https://image.tmdb.org/t/p/w500/from_s01e07.jpg"
            },
            8: { 
                title: "Heavy Is The Head", 
                playbackId: "1Tz58KSr02spPGpQ2PBHadFdlV01OSRkq01Q2dOn1eGGjM",
                thumbnail: "https://image.tmdb.org/t/p/w500/from_s01e08.jpg"
            },
            9: { 
                title: "The Calm Before", 
                playbackId: "yOJ2RQkUOw5ovEglvGg87GxOLOZYh6N4SDSu01Q9k4PI",
                thumbnail: "https://image.tmdb.org/t/p/w500/from_s01e09.jpg"
            }
        };
        const modal = document.getElementById('videoModal');
        const videoContainer = document.getElementById('videoContainer');
        const muxPlayer = document.getElementById('muxPlayer');
        const videoTitle = document.getElementById('videoTitle');
        const videoNumber = document.getElementById('videoNumber');
        function openVideo(id) {
            const episode = episodes[id];
            if (!episode || episode.playbackId.startsWith("ВСТАВЬТЕ")) {
                alert("Для этой серии ещё не добавлен Playback ID!");
                return;
            }
            muxPlayer.playbackId = episode.playbackId;
            videoTitle.textContent = episode.title;
            videoNumber.textContent = `Episode 0${id}`;
            modal.classList.add('active');
            document.body.style.overflow = 'hidden';
        }
        function closeVideo() {
            // Выход из полноэкранного режима если активен
            if (document.fullscreenElement) {
                document.exitFullscreen().catch(err => {
                    console.log('Fullscreen exit error:', err);
                });
            }
            muxPlayer.playbackId = '';
            videoContainer.classList.remove('fullscreen');
            modal.classList.remove('active');
            document.body.style.overflow = '';
        }
        function toggleFullscreen() {
            if (!document.fullscreenElement) {
                // Вход в полноэкранный режим
                if (videoContainer.requestFullscreen) {
                    videoContainer.requestFullscreen();
                } else if (videoContainer.webkitRequestFullscreen) {
                    videoContainer.webkitRequestFullscreen();
                } else if (videoContainer.msRequestFullscreen) {
                    videoContainer.msRequestFullscreen();
                }
                videoContainer.classList.add('fullscreen');
            } else {
                // Выход из полноэкранного режима
                if (document.exitFullscreen) {
                    document.exitFullscreen();
                } else if (document.webkitExitFullscreen) {
                    document.webkitExitFullscreen();
                } else if (document.msExitFullscreen) {
                    document.msExitFullscreen();
                }
                videoContainer.classList.remove('fullscreen');
            }
        }
        // Отслеживание изменения полноэкранного режима
        document.addEventListener('fullscreenchange', handleFullscreenChange);
        document.addEventListener('webkitfullscreenchange', handleFullscreenChange);
        document.addEventListener('msfullscreenchange', handleFullscreenChange);
        function handleFullscreenChange() {
            if (!document.fullscreenElement) {
                videoContainer.classList.remove('fullscreen');
            }
        }
        // Двойной клик по плееру для полноэкранного режима
        muxPlayer.addEventListener('dblclick', toggleFullscreen);
        modal.addEventListener('click', function(e) {
            if (e.target === modal) {
                closeVideo();
            }
        });
        document.addEventListener('keydown', function(e) {
            if (e.key === 'Escape' && modal.classList.contains('active')) {
                if (document.fullscreenElement) {
                    document.exitFullscreen();
                } else {
                closeVideo();
                }
            }
        });
    </script>
</body>
</html>
       
