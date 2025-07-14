<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MoodTunes - AI Music for Your Mood</title>
    <style>
        /* ALL YOUR CSS GOES HERE */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        /* Important for iframe: make body allow scrolling for content */
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            min-height: 100vh; /* Ensure it takes full height of iframe */
            display: flex;
            justify-content: center;
            align-items: center;
            color: #00ff00;
            background: #1a1a1a; /* Simple dark background for the iframe body */
            overflow-x: hidden; /* Prevent horizontal scroll */
            overflow-y: auto; /* Allow vertical scroll within the iframe */
        }

        .container {
            max-width: 500px;
            width: 90%;
            background: rgba(0, 0, 0, 0.9);
            backdrop-filter: blur(20px);
            border-radius: 25px;
            padding: 30px;
            box-shadow: 0 20px 60px rgba(0, 255, 0, 0.3), 0 0 0 1px rgba(0, 255, 0, 0.1);
            text-align: center;
            border: 2px solid #00ff00;
            position: relative;
            overflow: hidden;
            /* Added margin-top/bottom for spacing within GoDaddy iframe */
            margin: 20px auto;
        }

        .container::before {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: radial-gradient(circle, rgba(0, 255, 0, 0.1) 0%, transparent 70%);
            animation: rotate 20s linear infinite;
            z-index: -1;
        }

        @keyframes rotate {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        h1 {
            font-size: 2.8em;
            margin-bottom: 10px;
            background: linear-gradient(45deg, #00ff00, #39ff14, #00ff88);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            text-shadow: 0 0 30px rgba(0, 255, 0, 0.5);
            animation: glow 2s ease-in-out infinite alternate;
        }

        @keyframes glow {
            from { text-shadow: 0 0 20px rgba(0, 255, 0, 0.5); }
            to { text-shadow: 0 0 30px rgba(0, 255, 0, 0.8), 0 0 40px rgba(0, 255, 0, 0.3); }
        }

        .subtitle {
            margin-bottom: 30px;
            opacity: 0.9;
            font-size: 1.1em;
            color: #ccffcc;
        }

        .screen {
            display: none;
            animation: fadeIn 0.6s ease-out;
        }

        .screen.active {
            display: block;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(30px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .form-group {
            margin-bottom: 20px;
        }

        label {
            display: block;
            margin-bottom: 8px;
            font-weight: bold;
            text-align: left;
            color: #ccffcc;
        }

        textarea, select {
            width: 100%;
            padding: 18px;
            border: none;
            border-radius: 15px;
            background: rgba(255, 255, 255, 0.95);
            color: #333;
            font-size: 16px;
            resize: vertical;
            min-height: 80px;
            transition: all 0.3s ease;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
        }

        textarea:focus, select:focus {
            outline: none;
            box-shadow: 0 0 0 3px rgba(0, 255, 0, 0.3), 0 8px 25px rgba(0, 0, 0, 0.15);
            transform: translateY(-2px);
        }

        button {
            background: linear-gradient(45deg, #00ff00, #39ff14);
            color: #000000;
            border: none;
            padding: 18px 35px;
            border-radius: 30px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s ease;
            margin: 10px 5px;
            min-width: 140px;
            text-transform: uppercase;
            letter-spacing: 1px;
            box-shadow: 0 8px 25px rgba(0, 255, 0, 0.3);
            position: relative;
            overflow: hidden;
        }

        button::before {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.2), transparent);
            transition: left 0.5s;
        }

        button:hover::before {
            left: 100%;
        }

        button:hover {
            transform: translateY(-3px);
            box-shadow: 0 12px 35px rgba(0, 255, 0, 0.4);
            background: linear-gradient(45deg, #39ff14, #00ff88);
        }

        button:active {
            transform: translateY(-1px);
            box-shadow: 0 4px 15px rgba(0, 255, 0, 0.3);
        }

        button:disabled {
            opacity: 0.6;
            cursor: not-allowed;
            transform: none;
        }

        .mood-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(130px, 1fr));
            gap: 15px;
            margin: 20px 0;
        }

        .mood-btn {
            padding: 25px 15px;
            background: rgba(0, 0, 0, 0.8);
            border: 2px solid #00ff00;
            border-radius: 20px;
            cursor: pointer;
            transition: all 0.4s ease;
            font-size: 14px;
            min-width: auto;
            color: #00ff00;
            position: relative;
            overflow: hidden;
        }

        .mood-btn::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: linear-gradient(45deg, rgba(0, 255, 0, 0.1), rgba(57, 255, 20, 0.1));
            opacity: 0;
            transition: opacity 0.3s ease;
        }

        .mood-btn:hover::before {
            opacity: 1;
        }

        .mood-btn:hover {
            background: rgba(0, 255, 0, 0.15);
            transform: translateY(-5px) scale(1.05);
            box-shadow: 0 10px 30px rgba(0, 255, 0, 0.4);
            border-color: #39ff14;
        }

        .mood-btn.selected {
            background: linear-gradient(45deg, #00ff00, #39ff14);
            border-color: #39ff14;
            color: #000000;
            transform: translateY(-5px) scale(1.1);
            box-shadow: 0 15px 40px rgba(0, 255, 0, 0.6);
        }

        .music-player {
            background: rgba(0, 0, 0, 0.9);
            border: 2px solid #00ff00;
            border-radius: 20px;
            padding: 25px;
            margin: 20px 0;
            box-shadow: 0 10px 30px rgba(0, 255, 0, 0.2);
            position: relative;
        }

        .music-player::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: linear-gradient(45deg, rgba(0, 255, 0, 0.05), rgba(57, 255, 20, 0.05));
            border-radius: 18px;
            pointer-events: none;
        }

        .current-track {
            font-size: 20px;
            font-weight: bold;
            margin-bottom: 10px;
            color: #39ff14;
            text-shadow: 0 0 15px rgba(57, 255, 20, 0.5);
            position: relative;
        }

        .track-info {
            opacity: 0.8;
            margin-bottom: 20px;
            color: #ccffcc;
            font-size: 14px;
        }

        .progress-bar {
            width: 100%;
            height: 8px;
            background: rgba(0, 0, 0, 0.8);
            border: 1px solid #00ff00;
            border-radius: 4px;
            overflow: hidden;
            margin: 20px 0;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .progress-bar:hover {
            height: 10px;
            box-shadow: 0 0 15px rgba(0, 255, 0, 0.3);
        }

        .progress-fill {
            height: 100%;
            background: linear-gradient(90deg, #00ff00, #39ff14, #00ff88);
            width: 0%;
            transition: width 0.1s ease;
            position: relative;
        }

        .progress-fill::after {
            content: '';
            position: absolute;
            top: 0;
            right: 0;
            width: 20px;
            height: 100%;
            background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.3));
            animation: shimmer 2s infinite;
        }

        @keyframes shimmer {
            0% { opacity: 0; }
            50% { opacity: 1; }
            100% { opacity: 0; }
        }

        .controls {
            display: flex;
            justify-content: center;
            gap: 20px;
            margin-top: 25px;
        }

        .control-btn {
            background: rgba(0, 0, 0, 0.8);
            border: 2px solid #00ff00;
            color: #00ff00;
            padding: 15px 20px;
            border-radius: 50px;
            cursor: pointer;
            font-size: 14px;
            min-width: 100px;
            transition: all 0.3s ease;
            position: relative;
            overflow: hidden;
        }

        .control-btn::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: linear-gradient(45deg, rgba(0, 255, 0, 0.1), rgba(57, 255, 20, 0.1));
            opacity: 0;
            transition: opacity 0.3s ease;
        }

        .control-btn:hover::before {
            opacity: 1;
        }

        .control-btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 25px rgba(0, 255, 0, 0.3);
            border-color: #39ff14;
        }

        .loading {
            display: inline-block;
            width: 20px;
            height: 20px;
            border: 2px solid rgba(0, 255, 0, 0.3);
            border-top: 2px solid #00ff00;
            border-radius: 50%;
            animation: spin 1s linear infinite;
            margin-right: 10px;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .mood-analysis {
            background: rgba(0, 0, 0, 0.6);
            border: 1px solid rgba(0, 255, 0, 0.3);
            border-radius: 15px;
            padding: 20px;
            margin: 20px 0;
            text-align: left;
            box-shadow: 0 5px 20px rgba(0, 255, 0, 0.1);
        }

        .mood-analysis h3 {
            color: #39ff14;
            margin-bottom: 10px;
            text-shadow: 0 0 10px rgba(57, 255, 20, 0.3);
        }

        .volume-control {
            display: flex;
            align-items: center;
            gap: 10px;
            margin-top: 15px;
            color: #ccffcc;
        }

        .volume-slider {
            flex: 1;
            height: 5px;
            background: rgba(0, 0, 0, 0.8);
            border: 1px solid #00ff00;
            border-radius: 3px;
            outline: none;
            cursor: pointer;
        }

        .volume-slider::-webkit-slider-thumb {
            appearance: none;
            width: 15px;
            height: 15px;
            background: #00ff00;
            border-radius: 50%;
            cursor: pointer;
            box-shadow: 0 0 10px rgba(0, 255, 0, 0.5);
        }

        .time-display {
            display: flex;
            justify-content: space-between;
            font-size: 12px;
            color: #ccffcc;
            margin-top: 5px;
        }

        @media (max-width: 600px) {
            .container {
                padding: 20px;
                margin: 10px auto; /* Centering on mobile */
            }

            h1 {
                font-size: 2.2em;
            }

            .mood-grid {
                grid-template-columns: repeat(2, 1fr);
            }

            .controls {
                flex-direction: column;
                gap: 10px;
            }

            .control-btn {
                min-width: 80px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🎵 MoodTunes</h1>
        <p class="subtitle">AI-powered music that matches your emotions</p>

        <div class="screen active" id="dayScreen">
            <h2>How was your day?</h2>
            <div class="form-group">
                <label for="dayInput">Tell me about your day, your feelings, or what's on your mind:</label>
                <textarea id="dayInput" placeholder="I had a rough day at work, feeling stressed but hoping to relax tonight..."></textarea>
            </div>
            <button onclick="analyzeMood(event)">Analyze My Mood</button>
        </div>

        <div class="screen" id="moodScreen">
            <h2>Select Your Mood</h2>
            <div class="mood-analysis" id="moodAnalysis"></div>
            <div class="mood-grid">
                <button class="mood-btn" onclick="selectMood('happy', event)">😊 Happy</button>
                <button class="mood-btn" onclick="selectMood('sad', event)">😢 Sad</button>
                <button class="mood-btn" onclick="selectMood('energetic', event)">⚡ Energetic</button>
                <button class="mood-btn" onclick="selectMood('calm', event)">😌 Calm</button>
                <button class="mood-btn" onclick="selectMood('romantic', event)">💕 Romantic</button>
                <button class="mood-btn" onclick="selectMood('focused', event)">🎯 Focused</button>
                <button class="mood-btn" onclick="selectMood('angry', event)">😠 Angry</button>
                <button class="mood-btn" onclick="selectMood('nostalgic', event)">🌅 Nostalgic</button>
            </div>
            <button onclick="showScreen('dayScreen')">Back</button>
        </div>

        <div class="screen" id="musicScreen">
            <h2>Now Playing</h2>
            <div class="music-player">
                <div class="current-track" id="currentTrack">Loading...</div>
                <div class="track-info" id="trackInfo"></div>
                <div class="progress-bar" onclick="seekTo(event)">
                    <div class="progress-fill" id="progressFill"></div>
                </div>
                <div class="time-display">
                    <span id="currentTime">0:00</span>
                    <span id="totalTime">0:00</span>
                </div>
                <div class="volume-control">
                    <span>🔊</span>
                    <input type="range" class="volume-slider" id="volumeSlider" min="0" max="100" value="70" oninput="changeVolume(this.value)">
                    <span id="volumeValue">70%</span>
                </div>
                <div class="controls">
                    <button class="control-btn" onclick="previousTrack()">⏮️ Previous</button>
                    <button class="control-btn" id="playPauseBtn" onclick="togglePlayPause()">▶️ Play</button>
                    <button class="control-btn" onclick="nextTrack()">⏭️ Next</button>
                    <button class="control-btn" onclick="shufflePlaylist()">🔀 Shuffle</button>
                </div>
            </div>
            <button onclick="showScreen('moodScreen')">Change Mood</button>
            <button onclick="showScreen('dayScreen')">New Session</button>
        </div>
    </div>

    <script>
        // Encapsulate all JavaScript in an IIFE to prevent global conflicts
        (function() {
            // App state with audio
            let currentMood = '';
            let currentPlaylist = [];
            let currentTrackIndex = 0;
            let isPlaying = false;
            let progressInterval;
            let currentAudio = null;
            let isShuffled = false;
            let originalPlaylistOrder = []; // Store the original order for unshuffling

            // Enhanced music database with Web Audio API compatible tracks
            // NOTE: These are royalty-free tracks from Bensound.com
            // For "real" or new music, you'll need to license and host your own MP3s.
            const musicDatabase = {
                happy: [
                    { title: "Sunny Day", artist: "Bensound", genre: "Pop", duration: "2:20", url: "https://www.bensound.com/bensound-music/bensound-sunny.mp3" },
                    { title: "Happy Rock", artist: "Bensound", genre: "Rock", duration: "1:45", url: "https://www.bensound.com/bensound-music/bensound-happyrock.mp3" },
                    { title: "Ukulele", artist: "Bensound", genre: "Folk", duration: "2:26", url: "https://www.bensound.com/bensound-music/bensound-ukulele.mp3" },
                    { title: "Pop Dance", artist: "Bensound", genre: "Dance", duration: "2:13", url: "https://www.bensound.com/bensound-music/bensound-popdance.mp3" },
                    { title: "Summer", artist: "Bensound", genre: "Pop", duration: "2:37", url: "https://www.bensound.com/bensound-music/bensound-summer.mp3" }
                ],
                sad: [
                    { title: "Sad Day", artist: "Bensound", genre: "Indie", duration: "2:28", url: "https://www.bensound.com/bensound-music/bensound-sadday.mp3" },
                    { title: "Slow Motion", artist: "Bensound", genre: "Alternative", duration: "3:26", url: "https://www.bensound.com/bensound-music/bensound-slowmotion.mp3" },
                    { title: "Memories", artist: "Bensound", genre: "Ballad", duration: "3:50", url: "https://www.bensound.com/bensound-music/bensound-memories.mp3" },
                    { title: "Piano Moment", artist: "Bensound", genre: "Classical", duration: "2:17", url: "https://www.bensound.com/bensound-music/bensound-pianomoment.mp3" },
                    { title: "The Lounge", artist: "Bensound", genre: "Jazz", duration: "4:16", url: "https://www.bensound.com/bensound-music/bensound-thelounge.mp3" }
                ],
                energetic: [
                    { title: "Energy", artist: "Bensound", genre: "Rock", duration: "2:59", url: "https://www.bensound.com/bensound-music/bensound-energy.mp3" },
                    { title: "Punky", artist: "Bensound", genre: "Punk", duration: "1:30", url: "https://www.bensound.com/bensound-music/bensound-punky.mp3" },
                    { title: "Rock Angel", artist: "Bensound", genre: "Rock", duration: "1:53", url: "https://www.bensound.com/bensound-music/bensound-rockangel.mp3" },
                    { title: "Better Days", artist: "Bensound", genre: "Pop Rock", duration: "2:33", url: "https://www.bensound.com/bensound-music/bensound-betterdays.mp3" },
                    { title: "Extreme Action", artist: "Bensound", genre: "Electronic", duration: "8:03", url: "https://www.bensound.com/bensound-music/bensound-extremeaction.mp3" }
                ],
                calm: [
                    { title: "Relaxing", artist: "Bensound", genre: "Ambient", duration: "2:15", url: "https://www.bensound.com/bensound-music/bensound-relaxing.mp3" },
                    { title: "Cute", artist: "Bensound", genre: "New Age", duration: "3:14", url: "https://www.bensound.com/bensound-music/bensound-cute.mp3" },
                    { title: "Tenderness", artist: "Bensound", genre: "Instrumental", duration: "2:03", url: "https://www.bensound.com/bensound-music/bensound-tenderness.mp3" },
                    { title: "Acoustic Breeze", artist: "Bensound", genre: "Folk", duration: "2:37", url: "https://www.bensound.com/bensound-music/bensound-acousticbreeze.mp3" },
                    { title: "A New Beginning"
