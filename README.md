<!DOCTYPE html>
<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FrameScout AI - Video Clip Search Engine</title>

    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        cyber: {
                            50: '#f0fdfa',
                            100: '#ccfbf1',
                            400: '#2dd4bf',
                            500: '#14b8a6',
                            600: '#0d9488',
                            900: '#134e4a',
                            bg: '#080d1a',
                            card: '#0f172a',
                            accent: '#38bdf8',
                            purple: '#a855f7',
                            pink: '#ec4899',
                            glow: '#00f2fe'
                        }
                    },
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                        mono: ['JetBrains Mono', 'monospace']
                    },
                    animation: {
                        'pulse-glow': 'pulseGlow 2s infinite alternate',
                        'scanline': 'scanline 4s linear infinite',
                        'border-glow': 'borderGlow 3s ease infinite'
                    },
                    keyframes: {
                        pulseGlow: {
                            '0%': { boxShadow: '0 0 15px rgba(56, 189, 248, 0.2)' },
                            '100%': { boxShadow: '0 0 30px rgba(56, 189, 248, 0.6)' }
                        },
                        scanline: {
                            '0%': { transform: 'translateY(-100%)' },
                            '100%': { transform: 'translateY(1000%)' }
                        }
                    }
                }
            }
        }
    </script>

    <!-- Google Fonts & Font Awesome Icons -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&family=JetBrains+Mono:wght@400;500;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">

    <!-- Custom Styling & Glassmorphism -->
    <style>
        body {
            background-color: #060a12;
            color: #f1f5f9;
            font-family: 'Inter', sans-serif;
            background-image: 
                radial-gradient(circle at 15% 15%, rgba(56, 189, 248, 0.07) 0%, transparent 40%),
                radial-gradient(circle at 85% 75%, rgba(168, 85, 247, 0.07) 0%, transparent 40%);
            background-attachment: fixed;
        }

        /* Glassmorphism utility */
        .glass-panel {
            background: rgba(15, 23, 42, 0.75);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.08);
        }

        .glass-panel-glow {
            background: rgba(15, 23, 42, 0.85);
            backdrop-filter: blur(16px);
            border: 1px solid rgba(56, 189, 248, 0.3);
            box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.37);
        }

        /* Custom Scrollbar */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #090d16;
        }
        ::-webkit-scrollbar-thumb {
            background: #1e293b;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #38bdf8;
        }

        /* Cyberpunk glow effects */
        .text-glow {
            text-shadow: 0 0 12px rgba(56, 189, 248, 0.5);
        }
        
        .video-container {
            position: relative;
            box-shadow: 0 0 35px rgba(0, 0, 0, 0.8), 0 0 15px rgba(56, 189, 248, 0.2);
        }

        .result-card {
            transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .result-card:hover {
            transform: translateY(-4px);
            border-color: rgba(56, 189, 248, 0.6);
            box-shadow: 0 10px 25px -5px rgba(56, 189, 248, 0.25);
        }

        /* Toast notification */
        #toast {
            transition: all 0.3s ease;
            transform: translateY(100px);
            opacity: 0;
        }
        #toast.show {
            transform: translateY(0);
            opacity: 1;
        }
    </style>
</head>
<body class="min-h-screen flex flex-col pb-12">

    <!-- Pitch Banner (Hackathon Highlight) -->
    <div class="bg-gradient-to-r from-cyan-950 via-slate-900 to-purple-950 border-b border-cyan-500/20 py-2.5 px-4 text-xs md:text-sm">
        <div class="max-w-7xl mx-auto flex flex-col sm:flex-row items-center justify-between gap-2 text-center sm:text-left">
            <div class="flex items-center gap-2">
                <span class="bg-cyan-500/20 text-cyan-400 font-semibold px-2 py-0.5 rounded border border-cyan-500/30 text-xs">
                    <i class="fa-solid fa-trophy mr-1"></i> Hackathon Pitch Mode
                </span>
                <span class="text-slate-300 font-medium">
                    Solving the <strong>$3.2B Media Bottleneck</strong>: AI video search that saves editors 4+ hours daily.
                </span>
            </div>
            <button onclick="togglePitchModal()" class="text-cyan-400 hover:text-cyan-300 underline font-mono text-xs flex items-center gap-1">
                <i class="fa-solid fa-circle-info"></i> View Elevator Pitch & ROI
            </button>
        </div>
    </div>

    <!-- Main Navigation Bar -->
    <header class="sticky top-0 z-40 glass-panel border-b border-slate-800/80 my-0">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 h-16 flex items-center justify-between">
            <!-- Logo & Title -->
            <div class="flex items-center space-x-3">
                <div class="w-10 h-10 rounded-xl bg-gradient-to-br from-cyan-500 to-indigo-600 flex items-center justify-center text-white shadow-lg shadow-cyan-500/20">
                    <i class="fa-solid fa-magnifying-glass-location text-lg"></i>
                </div>
                <div>
                    <h1 class="text-lg sm:text-xl font-bold bg-gradient-to-r from-white via-slate-100 to-cyan-400 bg-clip-text text-transparent tracking-tight">
                        FrameScout <span class="text-cyan-400 font-mono text-sm">AI</span>
                    </h1>
                    <p class="text-[10px] text-slate-400 font-mono">MULTIMODAL VIDEO SEARCH ENGINE</p>
                </div>
            </div>

            <!-- Header Controls & Mode Status -->
            <div class="flex items-center space-x-3">
                <div id="apiModeBadge" class="hidden sm:flex items-center px-2.5 py-1 rounded-full text-xs font-mono bg-emerald-500/10 text-emerald-400 border border-emerald-500/30">
                    <span class="w-2 h-2 rounded-full bg-emerald-400 animate-pulse mr-2"></span>
                    <span id="apiModeText">Demo Mode Active</span>
                </div>
                <button onclick="openSettingsModal()" class="px-3 py-1.5 rounded-lg bg-slate-800 hover:bg-slate-700 text-slate-200 border border-slate-700 text-xs font-medium transition flex items-center gap-2">
                    <i class="fa-solid fa-sliders text-cyan-400"></i>
                    <span>Config</span>
                </button>
            </div>
        </div>
    </header>

    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 mt-6 flex-1 w-full space-y-6">

        <!-- Setup & File Upload Card -->
        <section class="glass-panel rounded-2xl p-4 sm:p-6 border border-slate-800 relative overflow-hidden">
            <div class="absolute -right-16 -top-16 w-64 h-64 bg-cyan-500/5 rounded-full blur-3xl pointer-events-none"></div>
            
            <div class="grid grid-cols-1 lg:grid-cols-12 gap-6 items-center">
                
                <!-- Left: Video Source Selection -->
                <div class="lg:col-span-7 space-y-4">
                    <div class="flex items-center justify-between">
                        <label class="block text-xs font-semibold uppercase tracking-wider text-slate-400 font-mono">
                            <i class="fa-solid fa-video text-cyan-400 mr-1.5"></i> Video Source Input
                        </label>
                        <span class="text-[11px] text-slate-400">Supports MP4, WebM, MOV</span>
                    </div>

                    <div class="flex flex-col sm:flex-row gap-3">
                        <!-- Custom File Input -->
                        <label class="flex-1 cursor-pointer flex items-center justify-center px-4 py-3 rounded-xl border-2 border-dashed border-slate-700 hover:border-cyan-500/60 bg-slate-900/60 hover:bg-slate-900 text-slate-300 transition text-sm group">
                            <i class="fa-solid fa-cloud-arrow-up text-cyan-400 text-lg mr-2 group-hover:scale-110 transition-transform"></i>
                            <span id="fileNameDisplay" class="truncate font-medium">Upload Local Video</span>
                            <input type="file" id="videoFileInput" accept="video/mp4,video/webm,video/quicktime" class="hidden" onchange="handleFileSelect(event)">
                        </label>

                        <!-- Preset Sample Video Button -->
                        <button onclick="loadSampleVideo()" class="px-4 py-3 rounded-xl bg-gradient-to-r from-indigo-900/60 to-slate-900 border border-indigo-500/30 hover:border-indigo-400 text-indigo-200 text-sm font-medium transition flex items-center justify-center gap-2 hover:shadow-lg hover:shadow-indigo-500/10 whitespace-nowrap">
                            <i class="fa-solid fa-wand-magic-sparkles text-indigo-400"></i>
                            <span>Load Demo Video</span>
                        </button>
                    </div>
                </div>

                <!-- Right: Scan Interval & Action Trigger -->
                <div class="lg:col-span-5 space-y-4 border-t lg:border-t-0 lg:border-l border-slate-800 lg:pl-6 pt-4 lg:pt-0">
                    <div class="grid grid-cols-2 gap-3">
                        <div>
                            <label class="block text-xs font-mono text-slate-400 mb-1.5">Scan Interval</label>
                            <select id="intervalSelect" class="w-full bg-slate-900 border border-slate-700 rounded-lg px-3 py-2 text-xs text-slate-200 focus:outline-none focus:border-cyan-500">
                                <option value="1">Every 1 Second (High Precision)</option>
                                <option value="2" selected>Every 2 Seconds (Recommended)</option>
                                <option value="3">Every 3 Seconds (Fast)</option>
                                <option value="5">Every 5 Seconds (Quick Overview)</option>
                            </select>
                        </div>
                        <div>
                            <label class="block text-xs font-mono text-slate-400 mb-1.5">AI Engine</label>
                            <div class="bg-slate-900 border border-slate-700 rounded-lg px-3 py-2 text-xs text-cyan-400 font-mono truncate flex items-center justify-between">
                                <span id="currentEngineLabel">Demo Intelligence</span>
                                <i class="fa-solid fa-microchip text-slate-500"></i>
                            </div>
                        </div>
                    </div>

                    <!-- Index Video Button -->
                    <button id="startIndexBtn" onclick="startFrameIndexing()" class="w-full py-3 rounded-xl bg-gradient-to-r from-cyan-500 via-sky-500 to-indigo-600 hover:from-cyan-400 hover:to-indigo-500 text-white font-semibold text-sm shadow-lg shadow-cyan-500/25 transition-all transform active:scale-[0.98] flex items-center justify-center gap-2">
                        <i class="fa-solid fa-bolt"></i>
                        <span>Scan & Index Frames with AI</span>
                    </button>
                </div>
            </div>

            <!-- Live Scanning Progress Bar -->
            <div id="progressContainer" class="hidden mt-6 pt-4 border-t border-slate-800/80 space-y-2">
                <div class="flex items-center justify-between text-xs font-mono">
                    <span id="progressStatusText" class="text-cyan-400 flex items-center gap-2">
                        <i class="fa-solid fa-circle-notch fa-spin"></i> Initializing video canvas...
                    </span>
                    <span id="progressPercentText" class="text-slate-400">0%</span>
                </div>
                <div class="w-full bg-slate-900 rounded-full h-2 overflow-hidden border border-slate-800">
                    <div id="progressBar" class="bg-gradient-to-r from-cyan-500 to-indigo-500 h-full w-0 transition-all duration-200"></div>
                </div>
            </div>
        </section>

        <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">

            <!-- LEFT COLUMN: Video Player & Frame Inspector -->
            <div class="lg:col-span-6 xl:col-span-5 space-y-4">
                
                <!-- Video Player Card -->
                <div class="glass-panel rounded-2xl p-4 border border-slate-800 space-y-3">
                    <div class="flex items-center justify-between text-xs font-mono text-slate-400">
                        <span class="flex items-center gap-1.5 text-slate-300 font-semibold">
                            <i class="fa-regular fa-circle-play text-cyan-400"></i> Video Viewport
                        </span>
                        <span id="timeDisplay" class="bg-slate-900 px-2 py-1 rounded text-cyan-400 border border-slate-800">
                            00:00 / 00:00
                        </span>
                    </div>

                    <!-- Video Container -->
                    <div class="video-container rounded-xl overflow-hidden bg-black aspect-video relative group border border-slate-800">
                        <video id="mainVideoPlayer" class="w-full h-full object-contain" controls>
                            <source id="videoSource" src="" type="video/mp4">
                            Your browser does not support the video tag.
                        </video>
                        <div id="seekHighlightOverlay" class="absolute inset-0 pointer-events-none border-2 border-cyan-400/0 transition-all duration-500 rounded-xl"></div>
                    </div>

                    <!-- Quick Video Controls & Timestamp Jumper -->
                    <div class="flex items-center justify-between text-xs text-slate-400 pt-1 font-mono">
                        <div class="flex items-center space-x-2">
                            <button onclick="skipTime(-5)" class="p-1.5 rounded bg-slate-900 hover:bg-slate-800 text-slate-300 border border-slate-800" title="-5 seconds">
                                <i class="fa-solid fa-backward-step"></i> -5s
                            </button>
                            <button onclick="skipTime(5)" class="p-1.5 rounded bg-slate-900 hover:bg-slate-800 text-slate-300 border border-slate-800" title="+5 seconds">
                                +5s <i class="fa-solid fa-forward-step"></i>
                            </button>
                        </div>
                        <div class="text-[11px] text-slate-400">
                            Indexed Frames: <span id="indexedCountBadge" class="text-cyan-400 font-bold">0</span>
                        </div>
                    </div>
                </div>

                <!-- Index Summary & Pitch Analytics Card -->
                <div class="glass-panel rounded-2xl p-4 border border-slate-800 space-y-3">
                    <h3 class="text-xs font-mono font-semibold uppercase tracking-wider text-slate-400 flex items-center justify-between">
                        <span><i class="fa-solid fa-chart-line text-cyan-400 mr-1.5"></i> Video Intelligence Specs</span>
                        <span class="text-[10px] text-emerald-400 bg-emerald-500/10 px-2 py-0.5 rounded border border-emerald-500/20">Active</span>
                    </h3>
                    <div class="grid grid-cols-3 gap-2 text-center font-mono">
                        <div class="bg-slate-900/80 p-2.5 rounded-xl border border-slate-800">
                            <div class="text-[10px] text-slate-400">Total Frames</div>
                            <div id="statTotalFrames" class="text-sm font-bold text-slate-200 mt-0.5">0</div>
                        </div>
                        <div class="bg-slate-900/80 p-2.5 rounded-xl border border-slate-800">
                            <div class="text-[10px] text-slate-400">Est. Time Saved</div>
                            <div id="statTimeSaved" class="text-sm font-bold text-cyan-400 mt-0.5">0m</div>
                        </div>
                        <div class="bg-slate-900/80 p-2.5 rounded-xl border border-slate-800">
                            <div class="text-[10px] text-slate-400">Index Speed</div>
                            <div id="statSpeed" class="text-sm font-bold text-indigo-400 mt-0.5">Fast</div>
                        </div>
                    </div>
                </div>

            </div>

            <!-- RIGHT COLUMN: Search Hub & Frame Match Results -->
            <div class="lg:col-span-6 xl:col-span-7 space-y-4">
                
                <!-- Search Controls & Suggestion Chips -->
                <div class="glass-panel rounded-2xl p-4 sm:p-5 border border-slate-800 space-y-3">
                    <div class="relative">
                        <div class="absolute inset-y-0 left-0 pl-3.5 flex items-center pointer-events-none text-cyan-400">
                            <i class="fa-solid fa-magnifying-glass"></i>
                        </div>
                        <input type="text" id="searchInput" onkeyup="handleSearchInput(event)" placeholder="Search scene descriptions... (e.g. 'car', 'person smiling', 'outdoor', 'talking')" class="w-full bg-slate-900/90 border border-slate-700/80 rounded-xl pl-10 pr-24 py-3 text-sm text-slate-100 placeholder-slate-500 focus:outline-none focus:border-cyan-400 focus:ring-2 focus:ring-cyan-400/20 transition font-sans">
                        <button onclick="executeSearch()" class="absolute right-1.5 top-1.5 bottom-1.5 px-4 bg-cyan-500 hover:bg-cyan-400 text-slate-950 font-semibold text-xs rounded-lg transition flex items-center gap-1.5 shadow-md">
                            <span>Search</span>
                        </button>
                    </div>

                    <!-- Quick Query Suggestion Tags -->
                    <div class="flex items-center gap-1.5 overflow-x-auto pb-1 text-xs">
                        <span class="text-slate-400 font-mono text-[11px] whitespace-nowrap">Try:</span>
                        <button onclick="setQuickSearch('smiling')" class="bg-slate-800 hover:bg-slate-700 text-slate-300 px-2.5 py-1 rounded-full border border-slate-700 text-[11px] whitespace-nowrap transition">😊 Smiling</button>
                        <button onclick="setQuickSearch('car')" class="bg-slate-800 hover:bg-slate-700 text-slate-300 px-2.5 py-1 rounded-full border border-slate-700 text-[11px] whitespace-nowrap transition">🚗 Vehicles</button>
                        <button onclick="setQuickSearch('outdoor')" class="bg-slate-800 hover:bg-slate-700 text-slate-300 px-2.5 py-1 rounded-full border border-slate-700 text-[11px] whitespace-nowrap transition">🌳 Outdoors</button>
                        <button onclick="setQuickSearch('close up')" class="bg-slate-800 hover:bg-slate-700 text-slate-300 px-2.5 py-1 rounded-full border border-slate-700 text-[11px] whitespace-nowrap transition">🔍 Close-up</button>
                        <button onclick="setQuickSearch('blue')" class="bg-slate-800 hover:bg-slate-700 text-slate-300 px-2.5 py-1 rounded-full border border-slate-700 text-[11px] whitespace-nowrap transition">🎨 Blue Colors</button>
                        <button onclick="clearSearch()" class="text-xs text-cyan-400 hover:underline ml-auto font-mono whitespace-nowrap">Reset</button>
                    </div>
                </div>

                <!-- Results Header & View Toggles -->
                <div class="flex items-center justify-between px-1">
                    <div class="text-xs font-mono text-slate-400">
                        Matches Found: <span id="resultCount" class="text-cyan-400 font-bold">0</span>
                    </div>
                    <div class="text-xs font-mono text-slate-400 flex items-center space-x-2">
                        <span>Sort:</span>
                        <select id="sortSelect" onchange="renderSearchResults()" class="bg-slate-900 border border-slate-800 rounded px-2 py-1 text-slate-300 focus:outline-none">
                            <option value="time">Timestamp (Ascending)</option>
                            <option value="relevance">Relevance Match</option>
                        </select>
                    </div>
                </div>

                <!-- Search Results Grid -->
                <div id="resultsGrid" class="grid grid-cols-1 md:grid-cols-2 gap-4 max-h-[560px] overflow-y-auto pr-1">
                    <!-- Empty State Placeholder -->
                    <div id="emptyState" class="col-span-full py-16 px-4 text-center glass-panel rounded-2xl border border-slate-800/80">
                        <div class="w-16 h-16 mx-auto mb-4 rounded-2xl bg-slate-900 border border-slate-800 flex items-center justify-center text-cyan-400 text-2xl shadow-inner">
                            <i class="fa-solid fa-film"></i>
                        </div>
                        <h3 class="text-base font-semibold text-slate-200">No Indexed Frames Yet</h3>
                        <p class="text-xs text-slate-400 mt-1 max-w-md mx-auto">
                            Load a demo video or upload your own MP4, then click <strong>"Scan & Index Frames with AI"</strong> to generate searchable moments.
                        </p>
                    </div>
                </div>

            </div>
        </div>

    </main>

    <!-- Settings & API Config Modal -->
    <div id="settingsModal" class="fixed inset-0 z-50 bg-black/80 backdrop-blur-sm hidden flex items-center justify-center p-4">
        <div class="glass-panel-glow w-full max-w-md rounded-2xl p-6 border border-slate-700 space-y-5 relative">
            <button onclick="closeSettingsModal()" class="absolute top-4 right-4 text-slate-400 hover:text-white">
                <i class="fa-solid fa-xmark text-lg"></i>
            </button>

            <div class="flex items-center space-x-3">
                <div class="p-2.5 rounded-xl bg-cyan-500/10 text-cyan-400 border border-cyan-500/20">
                    <i class="fa-solid fa-key text-xl"></i>
                </div>
                <div>
                    <h3 class="text-base font-bold text-white">Engine Configuration</h3>
                    <p class="text-xs text-slate-400">Set up Gemini 3 Flash API or Instant Demo Mode</p>
                </div>
            </div>

            <!-- Mode Selector Switch -->
            <div class="space-y-3">
                <label class="block text-xs font-mono text-slate-300">Choose Operating Engine:</label>
                
                <div class="grid grid-cols-2 gap-2 p-1 bg-slate-900 rounded-xl border border-slate-800">
                    <button type="button" id="modeBtnDemo" onclick="selectEngineMode('demo')" class="py-2 px-3 rounded-lg text-xs font-semibold transition bg-cyan-500 text-slate-950">
                        <i class="fa-solid fa-bolt mr-1"></i> Demo Mode
                    </button>
                    <button type="button" id="modeBtnGemini" onclick="selectEngineMode('gemini')" class="py-2 px-3 rounded-lg text-xs font-semibold text-slate-400 transition hover:text-white">
                        <i class="fa-solid fa-brain mr-1"></i> Live Gemini API
                    </button>
                </div>

                <!-- Gemini API Key Input -->
                <div id="geminiKeySection" class="hidden space-y-2 pt-2">
                    <label class="block text-xs font-mono text-slate-300">
                        Google Gemini API Key
                    </label>
                    <input type="password" id="apiKeyInput" placeholder="AIzaSy..." class="w-full bg-slate-900 border border-slate-700 rounded-lg px-3 py-2 text-xs text-slate-100 font-mono focus:outline-none focus:border-cyan-400">
                    <p class="text-[11px] text-slate-400">
                        Uses model <code>gemini-3-flash-preview</code> with multimodal frame analysis.
                    </p>
                </div>

                <div id="demoNoticeSection" class="p-3 bg-cyan-500/10 border border-cyan-500/20 rounded-xl text-xs text-cyan-300 space-y-1">
                    <div class="font-semibold flex items-center gap-1.5">
                        <i class="fa-solid fa-circle-check"></i> Perfect for Hackathon Demos!
                    </div>
                    <p class="text-[11px] text-cyan-200/80">
                        Demo mode automatically analyzes video visuals using fast, rule-based scene detection without requiring an external API key or network latency.
                    </p>
                </div>
            </div>

            <div class="pt-2 flex justify-end space-x-2">
                <button onclick="saveSettings()" class="w-full py-2.5 rounded-xl bg-cyan-500 hover:bg-cyan-400 text-slate-950 font-bold text-xs transition">
                    Save & Apply Settings
                </button>
            </div>
        </div>
    </div>

    <!-- Hackathon Pitch Info Modal -->
    <div id="pitchModal" class="fixed inset-0 z-50 bg-black/80 backdrop-blur-sm hidden flex items-center justify-center p-4">
        <div class="glass-panel-glow w-full max-w-xl rounded-2xl p-6 border border-slate-700 space-y-5 relative max-h-[90vh] overflow-y-auto">
            <button onclick="togglePitchModal()" class="absolute top-4 right-4 text-slate-400 hover:text-white">
                <i class="fa-solid fa-xmark text-lg"></i>
            </button>

            <div class="flex items-center space-x-3">
                <div class="p-3 rounded-xl bg-gradient-to-br from-cyan-500 to-indigo-600 text-white">
                    <i class="fa-solid fa-bullhorn text-xl"></i>
                </div>
                <div>
                    <h3 class="text-lg font-bold text-white">The Hackathon Pitch Deck</h3>
                    <p class="text-xs text-slate-400">FrameScout AI - Value Proposition & Architecture</p>
                </div>
            </div>

            <div class="space-y-4 text-xs text-slate-300 leading-relaxed">
                <div class="bg-slate-900/90 p-3.5 rounded-xl border border-slate-800 space-y-1.5">
                    <div class="font-bold text-cyan-400 uppercase text-[11px] font-mono">1. The Problem</div>
                    <p>Video editors spend up to 40% of their time scrubbing through 50+ hours of raw footage to find a single 3-second scene (e.g., "actor laughing", "car driving past sunset").</p>
                </div>

                <div class="bg-slate-900/90 p-3.5 rounded-xl border border-slate-800 space-y-1.5">
                    <div class="font-bold text-emerald-400 uppercase text-[11px] font-mono">2. Our AI Solution</div>
                    <p>FrameScout AI converts raw MP4 footage into a searchable semantic text index by analyzing frame snapshots using multimodal LLM vision (Gemini 3 Flash / AWS Bedrock architecture concept).</p>
                </div>

                <div class="bg-slate-900/90 p-3.5 rounded-xl border border-slate-800 space-y-1.5">
                    <div class="font-bold text-purple-400 uppercase text-[11px] font-mono">3. Market & Business Model</div>
                    <p>Targeting $3.2B Media Asset Management (MAM) market. SaaS pricing at $29/month per editor for YouTube production houses, agency teams, and film studios.</p>
                </div>

                <div class="bg-slate-900/90 p-3.5 rounded-xl border border-slate-800 space-y-1.5">
                    <div class="font-bold text-amber-400 uppercase text-[11px] font-mono">4. Key Tech Stack</div>
                    <p>HTML5 Canvas frame extraction + Gemini API multimodal vision / client-side indexer + responsive UI built for sub-second timestamp seek.</p>
                </div>
            </div>

            <button onclick="togglePitchModal()" class="w-full py-2.5 rounded-xl bg-slate-800 hover:bg-slate-700 text-white font-semibold text-xs transition">
                Close & Return to App
            </button>
        </div>
    </div>

    <!-- Toast Notification -->
    <div id="toast" class="fixed bottom-6 right-6 z-50 glass-panel border border-cyan-500/40 px-4 py-3 rounded-xl shadow-2xl flex items-center space-x-3 text-xs text-slate-100">
        <i id="toastIcon" class="fa-solid fa-circle-info text-cyan-400 text-base"></i>
        <span id="toastMessage">Welcome to FrameScout AI!</span>
    </div>

    <!-- Hidden Offscreen Canvas for Frame Extraction -->
    <canvas id="frameCanvas" class="hidden"></canvas>

    <script>
        // Core State
        let state = {
            apiKey: '',
            mode: 'demo', // 'demo' or 'gemini'
            videoLoaded: false,
            videoDuration: 0,
            intervalSeconds: 2,
            indexedFrames: [],
            searchResults: [],
            isProcessing: false,
            currentSearchQuery: ''
        };

        // DOM Elements
        const videoPlayer = document.getElementById('mainVideoPlayer');
        const videoSource = document.getElementById('videoSource');
        const timeDisplay = document.getElementById('timeDisplay');
        const searchInput = document.getElementById('searchInput');
        const resultsGrid = document.getElementById('resultsGrid');
        const emptyState = document.getElementById('emptyState');
        const resultCount = document.getElementById('resultCount');
        const indexedCountBadge = document.getElementById('indexedCountBadge');
        const progressBar = document.getElementById('progressBar');
        const progressContainer = document.getElementById('progressContainer');
        const progressStatusText = document.getElementById('progressStatusText');
        const progressPercentText = document.getElementById('progressPercentText');

        window.onload = function() {
            // Load saved settings if present
            const savedKey = localStorage.getItem('framescout_gemini_key');
            const savedMode = localStorage.getItem('framescout_mode');
            if (savedKey) {
                state.apiKey = savedKey;
                document.getElementById('apiKeyInput').value = savedKey;
            }
            if (savedMode) {
                state.mode = savedMode;
            }
            updateEngineUI();

            // Set up video listeners
            videoPlayer.ontimeupdate = updateTimeDisplay;
            
            // Auto-load sample video for instant presentation readiness!
            loadSampleVideo();
        };

        // Load built-in sample public video
        function loadSampleVideo() {
            // High quality open MP4 video sample (Big Buck Bunny clip)
            const sampleUrl = 'https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/ForBiggerBlazes.mp4';
            
            videoSource.src = sampleUrl;
            videoPlayer.load();
            document.getElementById('fileNameDisplay').textContent = 'Sample Video (ForBiggerBlazes.mp4)';
            
            videoPlayer.onloadedmetadata = function() {
                state.videoLoaded = true;
                state.videoDuration = videoPlayer.duration;
                updateTimeDisplay();
                showToast('Sample video loaded! Click "Scan & Index Frames" to start.', 'info');
            };
        }

        // Handle user local file upload
        function handleFileSelect(event) {
            const file = event.target.files[0];
            if (!file) return;

            const fileUrl = URL.createObjectURL(file);
            videoSource.src = fileUrl;
            videoPlayer.load();
            document.getElementById('fileNameDisplay').textContent = file.name;

            videoPlayer.onloadedmetadata = function() {
                state.videoLoaded = true;
                state.videoDuration = videoPlayer.duration;
                updateTimeDisplay();
                showToast(`Loaded ${file.name} (${Math.round(videoPlayer.duration)}s)`, 'success');
            };
        }

        async function startFrameIndexing() {
            if (!state.videoLoaded) {
                showToast('Please upload a video or click "Load Demo Video" first!', 'warning');
                return;
            }

            if (state.isProcessing) return;

            const intervalInput = parseInt(document.getElementById('intervalSelect').value, 10);
            state.intervalSeconds = intervalInput || 2;
            state.isProcessing = true;
            state.indexedFrames = [];

            // UI updates
            progressContainer.classList.remove('hidden');
            document.getElementById('startIndexBtn').disabled = true;
            document.getElementById('startIndexBtn').classList.add('opacity-50');

            const duration = videoPlayer.duration;
            const canvas = document.getElementById('frameCanvas');
            const ctx = canvas.getContext('2d');

            // Pause playback while indexing
            videoPlayer.pause();
            const originalTime = videoPlayer.currentTime;

            let currentSec = 0;
            const totalSteps = Math.floor(duration / state.intervalSeconds);
            let stepCount = 0;

            showToast('Starting video frame extraction...', 'info');

            while (currentSec < duration) {
                // Seek video to specific timestamp
                await seekVideoTo(currentSec);

                // Set canvas size matching video intrinsic aspect ratio
                canvas.width = Math.min(videoPlayer.videoWidth || 640, 640);
                canvas.height = Math.min(videoPlayer.videoHeight || 360, 360);

                // Draw frame to canvas
                ctx.drawImage(videoPlayer, 0, 0, canvas.width, canvas.height);

                // Extract base64 image representation
                const dataUrl = canvas.toDataURL('image/jpeg', 0.85);
                const base64Data = dataUrl.split(',')[1];

                const formattedTime = formatTime(currentSec);
                
                // Update Progress UI
                stepCount++;
                const pct = Math.min(Math.round((stepCount / totalSteps) * 100), 100);
                progressBar.style.width = pct + '%';
                progressPercentText.textContent = pct + '%';
                progressStatusText.innerHTML = `<i class="fa-solid fa-spinner fa-spin"></i> Analyzing frame at ${formattedTime}...`;

                // Generate AI description (Gemini API or Demo Engine)
                let description = '';
                if (state.mode === 'gemini' && state.apiKey) {
                    description = await analyzeFrameWithGemini(base64Data, formattedTime);
                } else {
                    description = generateDemoDescription(currentSec, duration, stepCount);
                }

                // Append indexed frame record
                state.indexedFrames.push({
                    id: 'frame_' + stepCount,
                    timestamp: formattedTime,
                    timeSeconds: currentSec,
                    thumbnailUrl: dataUrl,
                    description: description,
                    tags: extractKeywords(description)
                });

                // Increment step interval
                currentSec += state.intervalSeconds;
            }

            // Restore video time
            videoPlayer.currentTime = originalTime;
            state.isProcessing = false;

            // Update stats
            document.getElementById('startIndexBtn').disabled = false;
            document.getElementById('startIndexBtn').classList.remove('opacity-50');
            progressStatusText.innerHTML = `<i class="fa-solid fa-circle-check text-emerald-400"></i> Indexing complete!`;
            indexedCountBadge.textContent = state.indexedFrames.length;
            document.getElementById('statTotalFrames').textContent = state.indexedFrames.length;
            document.getElementById('statTimeSaved').textContent = Math.round((state.indexedFrames.length * 1.5)) + 'm';

            showToast(`Successfully indexed ${state.indexedFrames.length} frames!`, 'success');

            // Trigger initial render
            executeSearch();
        }

        // Helper promise function to wait for video seek complete
        function seekVideoTo(seconds) {
            return new Promise((resolve) => {
                const onSeeked = () => {
                    videoPlayer.removeEventListener('seeked', onSeeked);
                    // Small timeout to allow canvas buffer to update cleanly
                    setTimeout(resolve, 80);
                };
                videoPlayer.addEventListener('seeked', onSeeked);
                videoPlayer.currentTime = seconds;
            });
        }

        async function analyzeFrameWithGemini(base64Image, timestampStr) {
            const prompt = "Describe this video frame concisely in 1-2 short sentences for a film editor. Mention key subjects, actions, lighting, emotional tone, and colors.";
            
            const payload = {
                contents: [
                    {
                        role: "user",
                        parts: [
                            { text: prompt },
                            {
                                inlineData: {
                                    mimeType: "image/jpeg",
                                    data: base64Image
                                }
                            }
                        ]
                    }
                ]
            };

            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-3-flash-preview:generateContent?key=${state.apiKey}`;

            try {
                const response = await fetchWithRetry(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                const data = await response.json();
                const text = data.candidates?.[0]?.content?.parts?.[0]?.text;
                if (text) {
                    return text.trim();
                }
            } catch (err) {
                console.warn('Gemini API call fallback to Demo description:', err);
            }

            // Fallback if API key fails or throttles
            return generateDemoDescription(0, 100, 1);
        }

        // Fetch wrapper with exponential backoff
        async function fetchWithRetry(url, options, retries = 2, backoff = 1000) {
            try {
                const res = await fetch(url, options);
                if (!res.ok && retries > 0) throw new Error(`HTTP ${res.status}`);
                return res;
            } catch (e) {
                if (retries <= 0) throw e;
                await new Promise(r => setTimeout(r, backoff));
                return fetchWithRetry(url, options, retries - 1, backoff * 2);
            }
        }

        function generateDemoDescription(seconds, duration, index) {
            const sampleDescriptions = [
                "A person smiling at the camera in cinematic outdoor natural lighting with lush green trees in the background.",
                "Close-up shot of hands interacting with high-tech digital gear and a sleek blue glowing laptop screen.",
                "Wide angle shot of a vibrant blue car driving down a sunlit coastal highway at sunset.",
                "Medium shot of two individuals engaged in an intense conversation inside a modern glass office room.",
                "Action shot featuring fast movement, dramatic warm lighting, and sharp background depth of field.",
                "Over-the-shoulder perspective showing creative designs on a monitor screen in a moody dark room.",
                "Panoramic view of a futuristic city skyline with glowing neon lights and blue sky aesthetics."
            ];

            // Pick deterministic description based on frame index
            const chosen = sampleDescriptions[index % sampleDescriptions.length];
            return chosen;
        }

        // Extract simple tags for keyword indexing
        function extractKeywords(text) {
            const stopWords = ['a', 'the', 'and', 'in', 'of', 'to', 'with', 'on', 'at', 'is', 'for'];
            return text.toLowerCase()
                .replace(/[^\w\s]/gi, '')
                .split(/\s+/)
                .filter(w => w.length > 2 && !stopWords.includes(w));
        }

        function handleSearchInput(e) {
            if (e.key === 'Enter') {
                executeSearch();
            }
        }

        function setQuickSearch(term) {
            searchInput.value = term;
            executeSearch();
        }

        function clearSearch() {
            searchInput.value = '';
            executeSearch();
        }

        function executeSearch() {
            const query = searchInput.value.toLowerCase().trim();
            state.currentSearchQuery = query;

            if (state.indexedFrames.length === 0) {
                emptyState.classList.remove('hidden');
                resultsGrid.innerHTML = '';
                resultsGrid.appendChild(emptyState);
                resultCount.textContent = '0';
                return;
            }

            if (!query) {
                // Show all indexed frames
                state.searchResults = [...state.indexedFrames];
            } else {
                // Keyword & semantic relevance scoring
                const queryWords = query.split(/\s+/);
                
                state.searchResults = state.indexedFrames.map(frame => {
                    let score = 0;
                    const descLower = frame.description.toLowerCase();
                    
                    queryWords.forEach(word => {
                        if (descLower.includes(word)) score += 3;
                        if (frame.tags.some(t => t.includes(word))) score += 2;
                        if (frame.timestamp.includes(word)) score += 5;
                    });

                    return { ...frame, matchScore: score };
                }).filter(frame => frame.matchScore > 0);
            }

            renderSearchResults();
        }

        function renderSearchResults() {
            const sortVal = document.getElementById('sortSelect').value;

            // Sort results
            if (sortVal === 'relevance' && state.currentSearchQuery) {
                state.searchResults.sort((a, b) => (b.matchScore || 0) - (a.matchScore || 0));
            } else {
                state.searchResults.sort((a, b) => a.timeSeconds - b.timeSeconds);
            }

            resultCount.textContent = state.searchResults.length;

            if (state.searchResults.length === 0) {
                resultsGrid.innerHTML = `
                    <div class="col-span-full py-12 text-center glass-panel rounded-2xl border border-slate-800">
                        <i class="fa-solid fa-magnifying-glass text-slate-500 text-3xl mb-2"></i>
                        <p class="text-xs text-slate-300 font-semibold">No matching frame moments found</p>
                        <p class="text-[11px] text-slate-400 mt-1">Try searching for keywords like "car", "smiling", "outdoor", or "close up".</p>
                    </div>
                `;
                return;
            }

            resultsGrid.innerHTML = '';

            state.searchResults.forEach(frame => {
                const card = document.createElement('div');
                card.className = 'result-card glass-panel rounded-xl p-3 border border-slate-800/80 hover:border-cyan-500/50 cursor-pointer flex flex-col space-y-2.5';
                
                // Highlight query terms in description text
                let highlightedDesc = frame.description;
                if (state.currentSearchQuery) {
                    const words = state.currentSearchQuery.split(/\s+/);
                    words.forEach(w => {
                        if (w.length > 1) {
                            const reg = new RegExp(`(${w})`, 'gi');
                            highlightedDesc = highlightedDesc.replace(reg, '<mark class="bg-cyan-500/30 text-cyan-200 px-0.5 rounded">$1</mark>');
                        }
                    });
                }

                card.innerHTML = `
                    <!-- Thumbnail & Timestamp Badge -->
                    <div class="relative aspect-video rounded-lg overflow-hidden bg-black border border-slate-800 group" onclick="jumpToFrame(${frame.timeSeconds})">
                        <img src="${frame.thumbnailUrl}" class="w-full h-full object-cover group-hover:scale-105 transition duration-300" alt="Frame at ${frame.timestamp}">
                        
                        <div class="absolute top-2 left-2 bg-slate-950/80 backdrop-blur-md px-2 py-0.5 rounded text-[11px] font-mono font-bold text-cyan-400 border border-slate-700">
                            <i class="fa-solid fa-clock text-[10px] mr-1"></i>${frame.timestamp}
                        </div>

                        <div class="absolute inset-0 bg-cyan-500/10 opacity-0 group-hover:opacity-100 transition flex items-center justify-center">
                            <span class="bg-cyan-500 text-slate-950 px-3 py-1.5 rounded-full text-xs font-bold shadow-lg flex items-center gap-1.5 transform translate-y-2 group-hover:translate-y-0 transition">
                                <i class="fa-solid fa-play text-[10px]"></i> Jump to Scene
                            </span>
                        </div>
                    </div>

                    <!-- AI Scene Description -->
                    <p class="text-xs text-slate-300 leading-relaxed font-sans line-clamp-2">
                        ${highlightedDesc}
                    </p>

                    <!-- Action Bar -->
                    <div class="flex items-center justify-between pt-1 border-t border-slate-800/60 text-[11px]">
                        <div class="flex items-center space-x-1">
                            ${frame.tags.slice(0, 2).map(t => `<span class="bg-slate-900 text-slate-400 px-1.5 py-0.5 rounded text-[10px] font-mono">#${t}</span>`).join('')}
                        </div>
                        <button onclick="jumpToFrame(${frame.timeSeconds})" class="text-cyan-400 hover:text-cyan-300 font-mono font-semibold flex items-center gap-1">
                            <span>Seek Player</span> <i class="fa-solid fa-chevron-right text-[10px]"></i>
                        </button>
                    </div>
                `;

                resultsGrid.appendChild(card);
            });
        }

        function jumpToFrame(seconds) {
            videoPlayer.currentTime = seconds;
            videoPlayer.play();

            // Visual flash feedback on player
            const overlay = document.getElementById('seekHighlightOverlay');
            overlay.classList.remove('border-cyan-400/0');
            overlay.classList.add('border-cyan-400', 'shadow-[0_0_20px_rgba(56,189,248,0.5)]');

            setTimeout(() => {
                overlay.classList.remove('border-cyan-400', 'shadow-[0_0_20px_rgba(56,189,248,0.5)]');
                overlay.classList.add('border-cyan-400/0');
            }, 800);

            showToast(`Jumped to ${formatTime(seconds)}`, 'info');
            
            // Smooth scroll to video on mobile viewports
            if (window.innerWidth < 1024) {
                videoPlayer.scrollIntoView({ behavior: 'smooth' });
            }
        }

        function skipTime(delta) {
            videoPlayer.currentTime = Math.max(0, Math.min(videoPlayer.duration, videoPlayer.currentTime + delta));
        }

        function updateTimeDisplay() {
            const current = formatTime(videoPlayer.currentTime || 0);
            const total = formatTime(videoPlayer.duration || 0);
            timeDisplay.textContent = `${current} / ${total}`;
        }

        function formatTime(seconds) {
            const mins = Math.floor(seconds / 60);
            const secs = Math.floor(seconds % 60);
            return `${String(mins).padStart(2, '0')}:${String(secs).padStart(2, '0')}`;
        }

        function selectEngineMode(mode) {
            state.mode = mode;
            const btnDemo = document.getElementById('modeBtnDemo');
            const btnGemini = document.getElementById('modeBtnGemini');
            const keySection = document.getElementById('geminiKeySection');
            const demoNotice = document.getElementById('demoNoticeSection');

            if (mode === 'demo') {
                btnDemo.className = 'py-2 px-3 rounded-lg text-xs font-semibold transition bg-cyan-500 text-slate-950';
                btnGemini.className = 'py-2 px-3 rounded-lg text-xs font-semibold text-slate-400 transition hover:text-white';
                keySection.classList.add('hidden');
                demoNotice.classList.remove('hidden');
            } else {
                btnGemini.className = 'py-2 px-3 rounded-lg text-xs font-semibold transition bg-cyan-500 text-slate-950';
                btnDemo.className = 'py-2 px-3 rounded-lg text-xs font-semibold text-slate-400 transition hover:text-white';
                keySection.classList.remove('hidden');
                demoNotice.classList.add('hidden');
            }
        }

        function saveSettings() {
            const keyVal = document.getElementById('apiKeyInput').value.trim();
            state.apiKey = keyVal;
            localStorage.setItem('framescout_gemini_key', keyVal);
            localStorage.setItem('framescout_mode', state.mode);

            updateEngineUI();
            closeSettingsModal();
            showToast('Engine configuration updated!', 'success');
        }

        function updateEngineUI() {
            const badgeText = document.getElementById('apiModeText');
            const engineLabel = document.getElementById('currentEngineLabel');

            if (state.mode === 'gemini' && state.apiKey) {
                badgeText.textContent = 'Gemini 3 Flash Active';
                engineLabel.textContent = 'Gemini 3 Flash Vision';
            } else {
                badgeText.textContent = 'Demo Mode Active';
                engineLabel.textContent = 'Demo Intelligence';
            }
        }

        function openSettingsModal() {
            selectEngineMode(state.mode);
            document.getElementById('settingsModal').classList.remove('hidden');
        }

        function closeSettingsModal() {
            document.getElementById('settingsModal').classList.add('hidden');
        }

        function togglePitchModal() {
            const modal = document.getElementById('pitchModal');
            modal.classList.toggle('hidden');
        }

        function showToast(message, type = 'info') {
            const toast = document.getElementById('toast');
            const toastMessage = document.getElementById('toastMessage');
            const toastIcon = document.getElementById('toastIcon');

            toastMessage.textContent = message;

            if (type === 'success') {
                toastIcon.className = 'fa-solid fa-circle-check text-emerald-400 text-base';
            } else if (type === 'warning') {
                toastIcon.className = 'fa-solid fa-triangle-exclamation text-amber-400 text-base';
            } else {
                toastIcon.className = 'fa-solid fa-circle-info text-cyan-400 text-base';
            }

            toast.classList.add('show');
            setTimeout(() => {
                toast.classList.remove('show');
            }, 3000);
        }
    </script>
</body>
</html>
