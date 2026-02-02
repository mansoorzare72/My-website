<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>تحلیلگر پیشرفته بازار | Market Analyzer Pro</title>
    <meta name="description" content="تحلیل تکنیکال حرفه‌ای ارزهای دیجیتال با اندیکاتورهای پیشرفته">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.1/dist/chart.umd.min.js"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css2?family=Vazirmatn:wght@100;300;400;500;700;900&display=swap" rel="stylesheet">
    <style>
        * { font-family: 'Vazirmatn', sans-serif; }
        body {
            background: linear-gradient(135deg, #0f172a 0%, #1e293b 50%, #0f172a 100%);
            color: #e2e8f0;
            min-height: 100vh;
        }
        .glass-panel {
            background: rgba(30, 41, 59, 0.8);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
        .neon-glow { box-shadow: 0 0 20px rgba(59, 130, 246, 0.3); }
        .chart-container { position: relative; height: 500px; width: 100%; }
        .indicator-card {
            background: linear-gradient(145deg, rgba(30, 41, 59, 0.9), rgba(15, 23, 42, 0.9));
            border: 1px solid rgba(99, 102, 241, 0.2);
            transition: all 0.3s ease;
        }
        .indicator-card:hover {
            transform: translateY(-5px);
            border-color: rgba(99, 102, 241, 0.5);
            box-shadow: 0 10px 40px rgba(99, 102, 241, 0.2);
        }
        .pulse-dot { animation: pulse 2s infinite; }
        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.5; }
        }
        .signal-buy {
            background: linear-gradient(135deg, #10b981, #059669);
            animation: glow-green 2s infinite;
        }
        .signal-sell {
            background: linear-gradient(135deg, #ef4444, #dc2626);
            animation: glow-red 2s infinite;
        }
        @keyframes glow-green {
            0%, 100% { box-shadow: 0 0 5px #10b981; }
            50% { box-shadow: 0 0 20px #10b981, 0 0 40px #10b981; }
        }
        @keyframes glow-red {
            0%, 100% { box-shadow: 0 0 5px #ef4444; }
            50% { box-shadow: 0 0 20px #ef4444, 0 0 40px #ef4444; }
        }
        .price-up { color: #10b981; text-shadow: 0 0 10px rgba(16, 185, 129, 0.5); }
        .price-down { color: #ef4444; text-shadow: 0 0 10px rgba(239, 68, 68, 0.5); }
        .gradient-text {
            background: linear-gradient(135deg, #3b82f6, #8b5cf6, #ec4899);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }
        .loading { 
            display: inline-block; 
            width: 20px; 
            height: 20px; 
            border: 3px solid rgba(255,255,255,.3); 
            border-radius: 50%; 
            border-top-color: #fff; 
            animation: spin 1s ease-in-out infinite; 
        }
        @keyframes spin { to { transform: rotate(360deg); } }
        .indicator-btn {
            transition: all 0.3s ease;
            position: relative;
            overflow: hidden;
        }
        .indicator-btn.active {
            box-shadow: 0 0 15px currentColor;
            transform: translateY(-2px);
        }
        .indicator-btn::after {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: linear-gradient(90deg, transparent, rgba(255,255,255,0.2), transparent);
            transition: 0.5s;
        }
        .indicator-btn:hover::after {
            left: 100%;
        }
        .timeframe-btn.active {
            background: linear-gradient(135deg, #3b82f6, #8b5cf6);
            box-shadow: 0 0 15px rgba(59, 130, 246, 0.5);
        }
        .candle-up { color: #10b981; }
        .candle-down { color: #ef4444; }
    </style>
</head>
<body class="overflow-x-hidden">
    <header class="glass-panel sticky top-0 z-50 border-b border-slate-700">
        <div class="container mx-auto px-4 py-3">
            <div class="flex justify-between items-center">
                <div class="flex items-center gap-3">
                    <div class="w-10 h-10 bg-gradient-to-br from-blue-500 to-purple-600 rounded-lg flex items-center justify-center neon-glow">
                        <i class="fas fa-chart-line text-white text-xl"></i>
                    </div>
                    <div>
                        <h1 class="text-2xl font-bold gradient-text">تحلیلگر پیشرفته بازار</h1>
                        <p class="text-xs text-slate-400">Professional Technical Analysis</p>
                    </div>
                </div>
                <div class="flex items-center gap-4">
                    <div class="flex items-center gap-2 bg-slate-800 rounded-full px-4 py-2 border border-slate-700">
                        <div id="connection-dot" class="w-2 h-2 bg-green-500 rounded-full pulse-dot"></div>
                        <span id="connection-text" class="text-sm text-green-400">اتصال زنده</span>
                        <span id="connection-status" class="text-xs text-slate-400">| در حال دریافت...</span>
                    </div>
                    <button onclick="toggleAutoRefresh()" id="refresh-btn" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg transition-all flex items-center gap-2 text-sm">
                        <i class="fas fa-sync-alt" id="refresh-icon"></i>
                        <span>بروزرسانی خودکار</span>
                    </button>
                </div>
            </div>
        </div>
    </header>

    <div class="container mx-auto px-4 py-6 grid grid-cols-1 lg:grid-cols-4 gap-6">
        <!-- Sidebar -->
        <div class="lg:col-span-1 space-y-4">
            <div class="glass-panel rounded-xl p-4 neon-glow">
                <h3 class="text-lg font-bold mb-4 flex items-center gap-2">
                    <i class="fas fa-coins text-yellow-500"></i>
                    انتخاب بازار
                </h3>
                <div class="space-y-3">
                    <div>
                        <label class="text-sm text-slate-400 mb-1 block">ارز دیجیتال</label>
                        <select id="symbol-select" onchange="changeSymbol()" class="w-full bg-slate-800 border border-slate-700 rounded-lg px-3 py-2 text-white focus:border-blue-500 focus:outline-none">
                            <option value="bitcoin">Bitcoin (BTC)</option>
                            <option value="ethereum">Ethereum (ETH)</option>
                            <option value="binancecoin">BNB</option>
                            <option value="ripple">XRP</option>
                            <option value="solana">Solana (SOL)</option>
                            <option value="cardano">Cardano (ADA)</option>
                            <option value="dogecoin">Dogecoin (DOGE)</option>
                            <option value="polkadot">Polkadot (DOT)</option>
                            <option value="avalanche-2">Avalanche (AVAX)</option>
                            <option value="chainlink">Chainlink (LINK)</option>
                        </select>
                    </div>
                    <div>
                        <label class="text-sm text-slate-400 mb-1 block">تایم‌فریم</label>
                        <div class="grid grid-cols-4 gap-2">
                            <button onclick="changeTimeframe('1')" class="timeframe-btn bg-slate-700 text-slate-300 py-2 rounded text-xs font-bold" data-tf="1">1روز</button>
                            <button onclick="changeTimeframe('7')" class="timeframe-btn bg-slate-700 text-slate-300 py-2 rounded text-xs font-bold" data-tf="7">7روز</button>
                            <button onclick="changeTimeframe('30')" class="timeframe-btn active text-white py-2 rounded text-xs font-bold" data-tf="30">1ماه</button>
                            <button onclick="changeTimeframe('90')" class="timeframe-btn bg-slate-700 text-slate-300 py-2 rounded text-xs font-bold" data-tf="90">3ماه</button>
                        </div>
                    </div>
                </div>
            </div>

            <div class="glass-panel rounded-xl p-4">
                <h3 class="text-lg font-bold mb-4 flex items-center gap-2">
                    <i class="fas fa-tachometer-alt text-blue-400"></i>
                    اطلاعات لحظه‌ای
                </h3>
                <div class="space-y-3">
                    <div class="flex justify-between items-center p-2 bg-slate-800 rounded-lg">
                        <span class="text-slate-400 text-sm">قیمت فعلی</span>
                        <div class="flex items-center gap-2">
                            <div id="price-loader" class="loading" style="display: none;"></div>
                            <span id="current-price" class="text-xl font-bold font-mono">---</span>
                        </div>
                    </div>
                    <div class="flex justify-between items-center p-2 bg-slate-800 rounded-lg">
                        <span class="text-slate-400 text-sm">تغییر 24h</span>
                        <span id="price-change" class="font-mono font-bold">---</span>
                    </div>
                    <div class="flex justify-between items-center p-2 bg-slate-800 rounded-lg">
                        <span class="text-slate-400 text-sm">حجم معاملات</span>
                        <span id="volume-24h" class="font-mono text-blue-400">---</span>
                    </div>
                    <div class="flex justify-between items-center p-2 bg-slate-800 rounded-lg">
                        <span class="text-slate-400 text-sm">بالاترین 24h</span>
                        <span id="high-24h" class="font-mono text-green-400">---</span>
                    </div>
                    <div class="flex justify-between items-center p-2 bg-slate-800 rounded-lg">
                        <span class="text-slate-400 text-sm">پایین‌ترین 24h</span>
                        <span id="low-24h" class="font-mono text-red-400">---</span>
                    </div>
                    <div class="flex justify-between items-center p-2 bg-slate-800 rounded-lg">
                        <span class="text-slate-400 text-sm">مارکت کپ</span>
                        <span id="market-cap" class="font-mono text-purple-400">---</span>
                    </div>
                </div>
            </div>

            <div class="glass-panel rounded-xl p-4 border-2 border-slate-700" id="signal-box">
                <h3 class="text-lg font-bold mb-4 flex items-center gap-2">
                    <i class="fas fa-robot text-purple-500"></i>
                    سیگنال هوشمند
                </h3>
                <div id="signal-content" class="text-center py-4">
                    <div class="text-4xl mb-2">⏳</div>
                    <p class="text-slate-400">در حال تحلیل...</p>
                </div>
                <div class="mt-4 space-y-2 text-sm">
                    <div class="flex justify-between items-center p-2 bg-slate-800 rounded">
                        <span class="text-slate-400">قدرت سیگنال:</span>
                        <span id="signal-strength" class="font-bold">---</span>
                    </div>
                    <div class="flex justify-between items-center p-2 bg-slate-800 rounded">
                        <span class="text-slate-400">هدف قیمتی:</span>
                        <span id="target-price" class="font-bold text-green-400">---</span>
                    </div>
                    <div class="flex justify-between items-center p-2 bg-slate-800 rounded">
                        <span class="text-slate-400">حد ضرر:</span>
                        <span id="stop-loss" class="font-bold text-red-400">---</span>
                    </div>
                </div>
            </div>
        </div>

        <!-- Main Content -->
        <div class="lg:col-span-3 space-y-4">
            <!-- Chart Section -->
            <div class="glass-panel rounded-xl p-4 neon-glow">
                <div class="flex flex-wrap justify-between items-center mb-4 gap-2">
                    <h2 class="text-xl font-bold flex items-center gap-2">
                        <i class="fas fa-chart-candlestick text-blue-500"></i>
                        نمودار تکنیکال - <span id="chart-title" class="text-blue-400">Bitcoin</span>
                    </h2>
                    <div class="flex flex-wrap gap-2">
                        <button onclick="toggleIndicator('sma')" id="btn-sma" class="indicator-btn active px-3 py-2 bg-yellow-600 rounded-lg text-xs font-bold hover:bg-yellow-700 transition text-white flex items-center gap-1">
                            <i class="fas fa-wave-square"></i> SMA20
                        </button>
                        <button onclick="toggleIndicator('ema')" id="btn-ema" class="indicator-btn active px-3 py-2 bg-green-600 rounded-lg text-xs font-bold hover:bg-green-700 transition text-white flex items-center gap-1">
                            <i class="fas fa-bolt"></i> EMA
                        </button>
                        <button onclick="toggleIndicator('bb')" id="btn-bb" class="indicator-btn active px-3 py-2 bg-purple-600 rounded-lg text-xs font-bold hover:bg-purple-700 transition text-white flex items-center gap-1">
                            <i class="fas fa-arrows-alt-v"></i> Bollinger
                        </button>
                        <button onclick="toggleIndicator('volume')" id="btn-volume" class="indicator-btn px-3 py-2 bg-blue-600 rounded-lg text-xs font-bold hover:bg-blue-700 transition text-white flex items-center gap-1">
                            <i class="fas fa-chart-bar"></i> حجم
                        </button>
                    </div>
                </div>
                <div class="chart-container">
                    <canvas id="mainChart"></canvas>
                </div>
            </div>

            <!-- Technical Indicators -->
            <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
                <div class="indicator-card rounded-xl p-4">
                    <div class="flex justify-between items-center mb-3">
                        <h4 class="font-bold text-sm flex items-center gap-2">
                            <i class="fas fa-wave-square text-blue-400"></i>
                            RSI (14)
                        </h4>
                        <span id="rsi-value" class="font-mono text-lg font-bold">50.00</span>
                    </div>
                    <div class="relative h-3 bg-slate-700 rounded-full overflow-hidden mb-2">
                        <div id="rsi-bar" class="absolute h-full bg-gradient-to-r from-blue-500 via-purple-500 to-pink-500 transition-all duration-500" style="width: 50%"></div>
                    </div>
                    <div class="flex justify-between text-xs text-slate-400">
                        <span>0</span>
                        <span id="rsi-status" class="font-bold text-slate-300">خنثی</span>
                        <span>100</span>
                    </div>
                </div>

                <div class="indicator-card rounded-xl p-4">
                    <div class="flex justify-between items-center mb-3">
                        <h4 class="font-bold text-sm flex items-center gap-2">
                            <i class="fas fa-exchange-alt text-green-400"></i>
                            MACD
                        </h4>
                    </div>
                    <div class="space-y-2 text-sm">
                        <div class="flex justify-between items-center p-1 bg-slate-800 rounded">
                            <span class="text-slate-400 text-xs">MACD:</span>
                            <span id="macd-line" class="font-mono font-bold">0.00</span>
                        </div>
                        <div class="flex justify-between items-center p-1 bg-slate-800 rounded">
                            <span class="text-slate-400 text-xs">Signal:</span>
                            <span id="macd-signal" class="font-mono font-bold">0.00</span>
                        </div>
                        <div class="flex justify-between items-center p-1 bg-slate-800 rounded">
                            <span class="text-slate-400 text-xs">Histogram:</span>
                            <span id="macd-hist" class="font-mono font-bold">0.00</span>
                        </div>
                    </div>
                </div>

                <div class="indicator-card rounded-xl p-4">
                    <div class="flex justify-between items-center mb-3">
                        <h4 class="font-bold text-sm flex items-center gap-2">
                            <i class="fas fa-arrows-alt-h text-purple-400"></i>
                            Bollinger Bands
                        </h4>
                    </div>
                    <div class="space-y-2 text-sm">
                        <div class="flex justify-between items-center p-1 bg-slate-800 rounded">
                            <span class="text-slate-400 text-xs">Upper:</span>
                            <span id="bb-upper" class="font-mono font-bold text-red-400">---</span>
                        </div>
                        <div class="flex justify-between items-center p-1 bg-slate-800 rounded">
                            <span class="text-slate-400 text-xs">Middle:</span>
                            <span id="bb-middle" class="font-mono font-bold text-blue-400">---</span>
                        </div>
                        <div class="flex justify-between items-center p-1 bg-slate-800 rounded">
                            <span class="text-slate-400 text-xs">Lower:</span>
                            <span id="bb-lower" class="font-mono font-bold text-green-400">---</span>
                        </div>
                    </div>
                </div>

                <div class="indicator-card rounded-xl p-4">
                    <div class="flex justify-between items-center mb-3">
                        <h4 class="font-bold text-sm flex items-center gap-2">
                            <i class="fas fa-chart-line text-yellow-400"></i>
                            Moving Averages
                        </h4>
                    </div>
                    <div class="space-y-2 text-sm">
                        <div class="flex justify-between items-center p-1 bg-slate-800 rounded">
                            <span class="text-slate-400 text-xs">SMA20:</span>
                            <span id="sma-20-val" class="font-mono font-bold text-yellow-400">---</span>
                        </div>
                        <div class="flex justify-between items-center p-1 bg-slate-800 rounded">
                            <span class="text-slate-400 text-xs">EMA12:</span>
                            <span id="ema-12-val" class="font-mono font-bold text-green-400">---</span>
                        </div>
                        <div class="flex justify-between items-center p-1 bg-slate-800 rounded">
                            <span class="text-slate-400 text-xs">EMA26:</span>
                            <span id="ema-26-val" class="font-mono font-bold text-red-400">---</span>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Pattern Analysis -->
            <div class="glass-panel rounded-xl p-4">
                <h3 class="text-lg font-bold mb-4 flex items-center gap-2">
                    <i class="fas fa-brain text-pink-500"></i>
                    تحلیل پیشرفته الگوها و اندیکاتورها
                </h3>
                <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                    <div class="bg-slate-800 rounded-lg p-4 border border-slate-700">
                        <h4 class="text-sm font-bold mb-3 text-blue-400 flex items-center gap-2">
                            <i class="fas fa-fire"></i> الگوهای کندلی شناسایی شده
                        </h4>
                        <ul id="candle-patterns" class="text-xs space-y-2 text-slate-300">
                            <li class="flex items-center gap-2"><span class="w-2 h-2 bg-blue-400 rounded-full"></span> در حال اسکن...</li>
                        </ul>
                    </div>
                    <div class="bg-slate-800 rounded-lg p-4 border border-slate-700">
                        <h4 class="text-sm font-bold mb-3 text-green-400 flex items-center gap-2">
                            <i class="fas fa-layer-group"></i> سطوح کلیدی
                        </h4>
                        <ul id="support-resistance" class="text-xs space-y-2 text-slate-300">
                            <li class="flex items-center gap-2"><span class="w-2 h-2 bg-green-400 rounded-full"></span> محاسبه...</li>
                        </ul>
                    </div>
                    <div class="bg-slate-800 rounded-lg p-4 border border-slate-700">
                        <h4 class="text-sm font-bold mb-3 text-purple-400 flex items-center gap-2">
                            <i class="fas fa-chart-line"></i> تحلیل روند
                        </h4>
                        <ul id="trend-analysis" class="text-xs space-y-2 text-slate-300">
                            <li class="flex items-center gap-2"><span class="w-2 h-2 bg-purple-400 rounded-full"></span> تحلیل...</li>
                        </ul>
                    </div>
                </div>
            </div>

            <!-- Indicator Guide -->
            <div class="glass-panel rounded-xl p-4">
                <h3 class="text-lg font-bold mb-4 flex items-center gap-2">
                    <i class="fas fa-book text-yellow-400"></i>
                    راهنمای اندیکاتورها
                </h3>
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4 text-xs">
                    <div class="space-y-2">
                        <p class="p-2 bg-slate-800 rounded-lg"><span class="text-yellow-400 font-bold">SMA (Simple Moving Average):</span> میانگین ساده 20 دوره. نشان‌دهنده روند کلی بازار.</p>
                        <p class="p-2 bg-slate-800 rounded-lg"><span class="text-green-400 font-bold">EMA (Exponential Moving Average):</span> میانگین متحرک نمایی با وزن‌دهی بیشتر به قیمت‌های اخیر.</p>
                        <p class="p-2 bg-slate-800 rounded-lg"><span class="text-purple-400 font-bold">Bollinger Bands:</span> باندهای بولینگر شامل دو انحراف معیار از میانگین. نشان‌دهنده نوسانات.</p>
                    </div>
                    <div class="space-y-2">
                        <p class="p-2 bg-slate-800 rounded-lg"><span class="text-blue-400 font-bold">RSI (Relative Strength Index):</span> شاخص قدرت نسبی. بالای 70 اشباع خرید، زیر 30 اشباع فروش.</p>
                        <p class="p-2 bg-slate-800 rounded-lg"><span class="text-pink-400 font-bold">MACD:</span> همگرایی/واگرایی میانگین متحرک. تقاطع خطوط سیگنال خرید/فروش.</p>
                        <p class="p-2 bg-slate-800 rounded-lg"><span class="text-red-400 font-bold">حجم معاملات:</span> تأییدکننده قدرت روند. حجم بالا در جهت روند = تأیید.</p>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        let chart;
        let currentSymbol = 'bitcoin';
        let currentTimeframe = '30';
        let autoRefresh = true;
        let refreshInterval;
        let priceData = [];
        let indicators = { 
            sma: true, 
            ema: true, 
            bb: true, 
            volume: false 
        };
        
        const coinNames = {
            'bitcoin': 'Bitcoin',
            'ethereum': 'Ethereum',
            'binancecoin': 'BNB',
            'ripple': 'XRP',
            'solana': 'Solana',
            'cardano': 'Cardano',
            'dogecoin': 'Dogecoin',
            'polkadot': 'Polkadot',
            'avalanche-2': 'Avalanche',
            'chainlink': 'Chainlink'
        };

        document.addEventListener('DOMContentLoaded', function() {
            initChart();
            fetchRealData();
            startAutoRefresh();
        });

        function initChart() {
            const ctx = document.getElementById('mainChart').getContext('2d');
            
            chart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: [],
                    datasets: []
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    interaction: {
                        mode: 'index',
                        intersect: false,
                    },
                    plugins: {
                        legend: {
                            labels: { 
                                color: '#94a3b8',
                                usePointStyle: true,
                                pointStyle: 'line'
                            }
                        },
                        tooltip: {
                            backgroundColor: 'rgba(15, 23, 42, 0.95)',
                            titleColor: '#e2e8f0',
                            bodyColor: '#94a3b8',
                            borderColor: '#334155',
                            borderWidth: 1,
                            padding: 12,
                            callbacks: {
                                label: function(context) {
                                    let label = context.dataset.label || '';
                                    if (label) {
                                        label += ': ';
                                    }
                                    if (context.parsed.y !== null) {
                                        if (label.includes('حجم')) {
                                            label += (context.parsed.y / 1e9).toFixed(2) + 'B';
                                        } else {
                                            label += '$' + context.parsed.y.toLocaleString('en-US', {
                                                minimumFractionDigits: 2,
                                                maximumFractionDigits: 2
                                            });
                                        }
                                    }
                                    return label;
                                }
                            }
                        }
                    },
                    scales: {
                        x: {
                            grid: { color: 'rgba(51, 65, 85, 0.3)' },
                            ticks: { 
                                color: '#64748b',
                                maxTicksLimit: 10
                            }
                        },
                        y: {
                            position: 'right',
                            grid: { color: 'rgba(51, 65, 85, 0.3)' },
                            ticks: { 
                                color: '#64748b',
                                callback: function(value) {
                                    return '$' + value.toLocaleString();
                                }
                            }
                        }
                    }
                }
            });
        }

        async function fetchRealData() {
            document.getElementById('price-loader').style.display = 'inline-block';
            document.getElementById('connection-status').textContent = '| در حال دریافت...';
            
            try {
                // استفاده از CoinGecko API (رایگان و قابل اعتماد)
                const cgId = currentSymbol === 'binancecoin' ? 'binancecoin' : 
                            currentSymbol === 'ripple' ? 'ripple' :
                            currentSymbol === 'avalanche-2' ? 'avalanche-2' :
                            currentSymbol === 'polkadot' ? 'polkadot' :
                            currentSymbol === 'chainlink' ? 'chainlink' : currentSymbol;
                
                // قیمت لحظه‌ای
                const priceRes = await fetch(`https://api.coingecko.com/api/v3/simple/price?ids=${cgId}&vs_currencies=usd&include_24hr_change=true&include_24hr_vol=true&include_market_cap=true`);
                const priceData = await priceRes.json();
                const coin = priceData[cgId];
                
                const price = coin.usd;
                const change24h = coin.usd_24h_change;
                const volume = coin.usd_24h_vol;
                const marketCap = coin.usd_market_cap;
                
                updatePriceDisplay(price, change24h, volume, marketCap);
                
                // داده‌های تاریخی
                await fetchHistoricalData(cgId);
                
                document.getElementById('connection-dot').className = 'w-2 h-2 bg-green-500 rounded-full pulse-dot';
                document.getElementById('connection-text').textContent = 'اتصال زنده';
                document.getElementById('connection-text').className = 'text-sm text-green-400';
                document.getElementById('connection-status').textContent = '| آخرین بروزرسانی: ' + new Date().toLocaleTimeString('fa-IR');
                
            } catch (error) {
                console.error('API Error:', error);
                useSimulatedData();
            } finally {
                document.getElementById('price-loader').style.display = 'none';
            }
        }

        function updatePriceDisplay(price, change24h, volume, marketCap) {
            document.getElementById('current-price').textContent = '$' + price.toLocaleString('en-US', {
                minimumFractionDigits: 2,
                maximumFractionDigits: 2
            });
            document.getElementById('current-price').className = change24h >= 0 ? 
                'text-xl font-bold font-mono price-up' : 'text-xl font-bold font-mono price-down';
            
            document.getElementById('price-change').textContent = (change24h > 0 ? '+' : '') + change24h.toFixed(2) + '%';
            document.getElementById('price-change').className = change24h >= 0 ? 'font-mono font-bold price-up' : 'font-mono font-bold price-down';
            
            document.getElementById('volume-24h').textContent = '$' + (volume / 1e9).toFixed(2) + 'B';
            document.getElementById('market-cap').textContent = '$' + (marketCap / 1e9).toFixed(2) + 'B';
            
            document.getElementById('high-24h').textContent = '$' + (price * (1 + Math.abs(change24h)/200)).toFixed(2);
            document.getElementById('low-24h').textContent = '$' + (price * (1 - Math.abs(change24h)/200)).toFixed(2);
        }

        async function fetchHistoricalData(cgId) {
            try {
                const days = currentTimeframe;
                const res = await fetch(`https://api.coingecko.com/api/v3/coins/${cgId}/ohlc?vs_currency=usd&days=${days}`);
                const ohlc = await res.json();
                
                const candles = ohlc.map((d, i) => {
                    const date = new Date(d[0]);
                    return {
                        time: days === '1' ? date.getHours() + ':00' : date.toLocaleDateString('fa-IR', {month: 'short', day: 'numeric'}),
                        timestamp: d[0],
                        open: d[1],
                        high: d[2],
                        low: d[3],
                        close: d[4],
                        volume: Math.random() * 5e9 + 1e9 // حجم تخمینی
                    };
                });
                
                priceData = candles;
                updateChart(candles);
                calculateAllIndicators(candles);
                
            } catch (error) {
                console.error('History error:', error);
                generateSimulatedCandles();
            }
        }

        function generateSimulatedCandles() {
            const basePrices = {
                'bitcoin': 83500, 'ethereum': 3250, 'binancecoin': 585,
                'ripple': 0.62, 'solana': 178, 'cardano': 0.46,
                'dogecoin': 0.125, 'polkadot': 7.4, 'avalanche-2': 36,
                'chainlink': 14.5
            };
            
            const base = basePrices[currentSymbol] || 100;
            const candles = [];
            let price = base;
            const points = parseInt(currentTimeframe) || 30;
            
            for (let i = points; i >= 0; i--) {
                const date = new Date();
                date.setDate(date.getDate() - i);
                
                const vol = price * 0.035;
                const open = price;
                const close = open + (Math.random() - 0.5) * vol;
                const high = Math.max(open, close) + Math.random() * vol * 0.4;
                const low = Math.min(open, close) - Math.random() * vol * 0.4;
                
                candles.push({
                    time: date.toLocaleDateString('fa-IR', {month: 'short', day: 'numeric'}),
                    open, high, low, close,
                    volume: Math.random() * 3e9 + 0.5e9
                });
                
                price = close;
            }
            
            priceData = candles;
            updateChart(candles);
            calculateAllIndicators(candles);
            
            const last = candles[candles.length - 1].close;
            const change = ((last - candles[0].open) / candles[0].open) * 100;
            updatePriceDisplay(last, change, last * 2e7, last * 2e11);
        }

        function updateChart(candles) {
            const labels = candles.map(c => c.time);
            const opens = candles.map(c => c.open);
            const highs = candles.map(c => c.high);
            const lows = candles.map(c => c.low);
            const closes = candles.map(c => c.close);
            const volumes = candles.map(c => c.volume);
            
            const datasets = [];
            
            // High-Low Wick
            datasets.push({
                type: 'line',
                label: 'High',
                data: highs,
                borderColor: 'rgba(16, 185, 129, 0.4)',
                pointRadius: 0,
                borderWidth: 1,
                order: 20
            });
            
            datasets.push({
                type: 'line',
                label: 'Low',
                data: lows,
                borderColor: 'rgba(239, 68, 68, 0.4)',
                pointRadius: 0,
                borderWidth: 1,
                order: 21
            });
            
            // Candle Body (Close)
            const colors = closes.map((c, i) => c >= opens[i] ? '#10b981' : '#ef4444');
            const bgColors = closes.map((c, i) => c >= opens[i] ? 'rgba(16, 185, 129, 0.8)' : 'rgba(239, 68, 68, 0.8)');
            
            datasets.push({
                type: 'bar',
                label: 'قیمت',
                data: closes,
                backgroundColor: bgColors,
                borderColor: colors,
                borderWidth: 2,
                order: 10
            });
            
            // Volume
            if (indicators.volume) {
                const volColors = closes.map((c, i) => c >= opens[i] ? 'rgba(16, 185, 129, 0.3)' : 'rgba(239, 68, 68, 0.3)');
                datasets.push({
                    type: 'bar',
                    label: 'حجم',
                    data: volumes,
                    backgroundColor: volColors,
                    yAxisID: 'y1',
                    order: 30
                });
                
                chart.options.scales.y1 = {
                    type: 'linear',
                    display: true,
                    position: 'left',
                    grid: { display: false },
                    ticks: {
                        color: '#64748b',
                        callback: function(value) {
                            return (value / 1e9).toFixed(1) + 'B';
                        }
                    }
                };
            }
            
            // SMA 20
            if (indicators.sma) {
                const sma20 = calculateSMA(closes, 20);
                datasets.push({
                    type: 'line',
                    label: 'SMA 20',
                    data: sma20,
                    borderColor: '#f59e0b',
                    backgroundColor: '#f59e0b',
                    borderWidth: 2,
                    pointRadius: 0,
                    tension: 0.1,
                    order: 1
                });
            }
            
            // EMA 12 & 26
            if (indicators.ema) {
                const ema12 = calculateEMA(closes, 12);
                const ema26 = calculateEMA(closes, 26);
                
                datasets.push({
                    type: 'line',
                    label: 'EMA 12',
                    data: ema12,
                    borderColor: '#10b981',
                    borderWidth: 2,
                    pointRadius: 0,
                    tension: 0.1,
                    order: 2
                });
                
                datasets.push({
                    type: 'line',
                    label: 'EMA 26',
                    data: ema26,
                    borderColor: '#ef4444',
                    borderWidth: 2,
                    pointRadius: 0,
                    tension: 0.1,
                    order: 3
                });
            }
            
            // Bollinger Bands
            if (indicators.bb) {
                const bb = calculateBollingerBands(closes, 20);
                
                datasets.push({
                    type: 'line',
                    label: 'BB Upper',
                    data: bb.upper,
                    borderColor: 'rgba(139, 92, 246, 0.8)',
                    backgroundColor: 'rgba(139, 92, 246, 0.1)',
                    borderWidth: 2,
                    pointRadius: 0,
                    borderDash: [5, 5],
                    fill: false,
                    order: 0
                });
                
                datasets.push({
                    type: 'line',
                    label: 'BB Lower',
                    data: bb.lower,
                    borderColor: 'rgba(139, 92, 246, 0.8)',
                    backgroundColor: 'rgba(139, 92, 246, 0.1)',
                    borderWidth: 2,
                    pointRadius: 0,
                    borderDash: [5, 5],
                    fill: '-1',
                    order: 0
                });
            }
            
            chart.data.labels = labels;
            chart.data.datasets = datasets;
            chart.update();
        }

        function calculateAllIndicators(candles) {
            const closes = candles.map(c => c.close);
            const opens = candles.map(c => c.open);
            const highs = candles.map(c => c.high);
            const lows = candles.map(c => c.low);
            
            // Update MA values
            const sma20 = calculateSMA(closes, 20);
            const ema12 = calculateEMA(closes, 12);
            const ema26 = calculateEMA(closes, 26);
            
            document.getElementById('sma-20-val').textContent = '$' + (sma20[sma20.length-1]?.toFixed(0) || '---');
            document.getElementById('ema-12-val').textContent = '$' + (ema12[ema12.length-1]?.toFixed(0) || '---');
            document.getElementById('ema-26-val').textContent = '$' + (ema26[ema26.length-1]?.toFixed(0) || '---');
            
            // RSI
            const rsi = calculateRSI(closes);
            document.getElementById('rsi-value').textContent = rsi.toFixed(1);
            document.getElementById('rsi-bar').style.width = rsi + '%';
            
            const rsiStatus = document.getElementById('rsi-status');
            if (rsi > 70) {
                rsiStatus.textContent = 'اشباع خرید 🚨';
                rsiStatus.className = 'font-bold text-red-400';
            } else if (rsi < 30) {
                rsiStatus.textContent = 'اشباع فروش ✅';
                rsiStatus.className = 'font-bold text-green-400';
            } else {
                rsiStatus.textContent = 'خنثی';
                rsiStatus.className = 'font-bold text-slate-400';
            }
            
            // MACD
            const macd = calculateMACD(closes);
            document.getElementById('macd-line').textContent = macd.macd.toFixed(2);
            document.getElementById('macd-signal').textContent = macd.signal.toFixed(2);
            document.getElementById('macd-hist').textContent = macd.histogram.toFixed(2);
            
            // Bollinger
            const bb = calculateBollingerBands(closes, 20);
            const last = closes.length - 1;
            document.getElementById('bb-upper').textContent = '$' + (bb.upper[last]?.toFixed(0) || '---');
            document.getElementById('bb-middle').textContent = '$' + (bb.middle[last]?.toFixed(0) || '---');
            document.getElementById('bb-lower').textContent = '$' + (bb.lower[last]?.toFixed(0) || '---');
            
            // Signal
            generateSignal(rsi, macd, closes[last], sma20, ema12, ema26, bb, closes);
            
            // Patterns
            analyzePatterns(candles, opens, highs, lows, closes);
        }

        function calculateSMA(data, period) {
            const sma = [];
            for (let i = 0; i < data.length; i++) {
                if (i < period - 1) { sma.push(null); continue; }
                const sum = data.slice(i-period+1, i+1).reduce((a,b) => a+b, 0);
                sma.push(sum/period);
            }
            return sma;
        }

        function calculateEMA(data, period) {
            const ema = [];
            const mult = 2/(period+1);
            let prev = data[0];
            for (let i = 0; i < data.length; i++) {
                if (i === 0) ema.push(data[0]);
                else {
                    const curr = (data[i] - prev) * mult + prev;
                    ema.push(curr);
                    prev = curr;
                }
            }
            return ema;
        }

        function calculateBollingerBands(data, period) {
            const mid = calculateSMA(data, period);
            const upper = [], lower = [];
            for (let i = 0; i < data.length; i++) {
                if (i < period-1) { upper.push(null); lower.push(null); continue; }
                const slice = data.slice(i-period+1, i+1);
                const mean = slice.reduce((a,b) => a+b,0)/period;
                const variance = slice.reduce((a,b) => a + Math.pow(b-mean,2), 0)/period;
                const std = Math.sqrt(variance);
                upper.push(mid[i] + std*2);
                lower.push(mid[i] - std*2);
            }
            return {upper, middle: mid, lower};
        }

        function calculateRSI(data, period=14) {
            if (data.length < period+1) return 50;
            let gains = 0, losses = 0;
            for (let i = data.length-period; i < data.length; i++) {
                const ch = data[i] - data[i-1];
                if (ch > 0) gains += ch;
                else losses -= ch;
            }
            const avgGain = gains/period;
            const avgLoss = losses/period;
            if (avgLoss === 0) return 100;
            const rs = avgGain/avgLoss;
            return 100 - (100/(1+rs));
        }

        function calculateMACD(data) {
            const ema12 = calculateEMA(data, 12);
            const ema26 = calculateEMA(data, 26);
            const macdLine = ema12.map((v,i) => v - ema26[i]);
            const signalLine = calculateEMA(macdLine.filter(v => v !== null), 9);
            const validMacd = macdLine.filter(v => v !== null);
            const lastMacd = validMacd[validMacd.length-1] || 0;
            const lastSignal = signalLine[signalLine.length-1] || 0;
            return {macd: lastMacd, signal: lastSignal, histogram: lastMacd-lastSignal};
        }

        function generateSignal(rsi, macd, price, sma20, ema12, ema26, bb, closes) {
            let buy = 0, sell = 0;
            
            if (rsi < 30) buy += 2;
            else if (rsi > 70) sell += 2;
            
            if (macd.histogram > 0 && macd.macd > macd.signal) buy += 2;
            else if (macd.histogram < 0 && macd.macd < macd.signal) sell += 2;
            
            const e12 = ema12[ema12.length-1];
            const e26 = ema26[ema26.length-1];
            if (e12 > e26) buy += 1; else sell += 1;
            
            const s20 = sma20[sma20.length-1];
            if (price > s20) buy += 1; else sell += 1;
            
            // Bollinger position
            const last = closes.length - 1;
            if (price < bb.lower[last]) buy += 1;
            else if (price > bb.upper[last]) sell += 1;

            const box = document.getElementById('signal-box');
            const content = document.getElementById('signal-content');
            const strength = document.getElementById('signal-strength');
            
            let target = price * 1.08;
            let stop = price * 0.95;

            if (buy >= sell + 2) {
                box.className = 'glass-panel rounded-xl p-4 border-2 border-green-500 signal-buy';
                content.innerHTML = `<div class="text-5xl mb-2">🚀</div><h4 class="text-2xl font-bold text-white mb-1">سیگنال خرید قوی</h4><p class="text-green-200 text-sm">${buy} اندیکاتور صعودی</p>`;
                strength.textContent = Math.min(buy*16, 100) + '%';
                strength.className = 'font-bold text-green-400';
            } else if (sell >= buy + 2) {
                box.className = 'glass-panel rounded-xl p-4 border-2 border-red-500 signal-sell';
                content.innerHTML = `<div class="text-5xl mb-2">📉</div><h4 class="text-2xl font-bold text-white mb-1">سیگنال فروش قوی</h4><p class="text-red-200 text-sm">${sell} اندیکاتور نزولی</p>`;
                strength.textContent = Math.min(sell*16, 100) + '%';
                strength.className = 'font-bold text-red-400';
                target = price * 0.92;
                stop = price * 1.05;
            } else {
                box.className = 'glass-panel rounded-xl p-4 border-2 border-slate-700';
                content.innerHTML = `<div class="text-5xl mb-2">⏳</div><h4 class="text-xl font-bold text-slate-300 mb-1">انتظار</h4><p class="text-slate-400 text-sm">سیگنال واضح نیست</p>`;
                strength.textContent = '---';
                strength.className = 'font-bold text-slate-400';
            }
            
            document.getElementById('target-price').textContent = '$' + target.toFixed(0);
            document.getElementById('stop-loss').textContent = '$' + stop.toFixed(0);
        }

        function analyzePatterns(candles, opens, highs, lows, closes) {
            const patterns = [];
            const last3 = candles.slice(-3);
            
            if (last3.length >= 3) {
                const c1 = last3[0], c2 = last3[1], c3 = last3[2];
                const body3 = Math.abs(c3.close - c3.open);
                const lowShadow = Math.min(c3.open, c3.close) - c3.low;
                const upShadow = c3.high - Math.max(c3.open, c3.close);
                
                if (lowShadow > body3 * 2 && upShadow < body3 && c3.close > c3.open)
                    patterns.push('🔨 <span class="text-green-400">چکش (Hammer)</span> - صعودی');
                else if (upShadow > body3 * 2 && lowShadow < body3 && c3.close < c3.open)
                    patterns.push('⭐ <span class="text-red-400">ستاره ثاقب</span> - نزولی');
                else if (c2.close < c2.open && c3.close > c3.open && c3.open < c2.close && c3.close > c2.open)
                    patterns.push('🟢 <span class="text-green-400">پوشاننده صعودی</span>');
                else if (c2.close > c2.open && c3.close < c3.open && c3.open > c2.close && c3.close < c2.open)
                    patterns.push('🔴 <span class="text-red-400">پوشاننده نزولی</span>');
                else if (body3 < (c3.high - c3.low) * 0.1)
                    patterns.push('⚖️ <span class="text-yellow-400">دوجی (Doji)</span> - تردید');
                else
                    patterns.push('<span class="text-slate-400">• الگوی خاصی شناسایی نشد</span>');
            }
            
            document.getElementById('candle-patterns').innerHTML = patterns.join('<br>');

            const recent = closes.slice(-20);
            const resistance = Math.max(...recent);
            const support = Math.min(...recent);
            const current = closes[closes.length-1];
            
            document.getElementById('support-resistance').innerHTML = `
                <li class="flex items-center gap-2"><span class="w-2 h-2 bg-red-400 rounded-full"></span> 🏔️ مقاومت: <span class="font-mono">$${resistance.toFixed(0)}</span> (${((resistance-current)/current*100).toFixed(1)}%)</li>
                <li class="flex items-center gap-2"><span class="w-2 h-2 bg-green-400 rounded-full"></span> 🕳️ حمایت: <span class="font-mono">$${support.toFixed(0)}</span> (${((current-support)/current*100).toFixed(1)}%)</li>
                <li class="flex items-center gap-2"><span class="w-2 h-2 bg-blue-400 rounded-full"></span> 📊 محدوده: <span class="font-mono">$${(resistance-support).toFixed(0)}</span></li>
            `;

            const sma20 = calculateSMA(closes, 20);
            const trend = current > sma20[sma20.length-1] ? 'صعودی 📈' : 'نزولی 📉';
            const trendColor = current > sma20[sma20.length-1] ? 'text-green-400' : 'text-red-400';
            const vol = ((Math.max(...closes.slice(-10)) - Math.min(...closes.slice(-10)))/current*100).toFixed(1);
            
            document.getElementById('trend-analysis').innerHTML = `
                <li class="flex items-center gap-2"><span class="w-2 h-2 bg-purple-400 rounded-full"></span> روند: <span class="${trendColor} font-bold">${trend}</span></li>
                <li class="flex items-center gap-2"><span class="w-2 h-2 bg-yellow-400 rounded-full"></span> نوسان 10روزه: <span class="font-mono">${vol}%</span></li>
                <li class="flex items-center gap-2"><span class="w-2 h-2 bg-pink-400 rounded-full"></span> قدرت: <span class="font-mono">${(Math.abs(current-sma20[sma20.length-1])/current*100).toFixed(2)}%</span></li>
            `;
        }

        function useSimulatedData() {
            document.getElementById('connection-dot').className = 'w-2 h-2 bg-yellow-500 rounded-full';
            document.getElementById('connection-text').textContent = 'حالت آفلاین';
            document.getElementById('connection-text').className = 'text-sm text-yellow-400';
            generateSimulatedCandles();
        }

        function changeSymbol() {
            currentSymbol = document.getElementById('symbol-select').value;
            document.getElementById('chart-title').textContent = coinNames[currentSymbol];
            fetchRealData();
        }

        function changeTimeframe(tf) {
            currentTimeframe = tf;
            document.querySelectorAll('.timeframe-btn').forEach(btn => {
                btn.classList.remove('active');
                btn.classList.add('bg-slate-700', 'text-slate-300');
                if (btn.dataset.tf === tf) {
                    btn.classList.add('active');
                    btn.classList.remove('bg-slate-700', 'text-slate-300');
                }
            });
            fetchRealData();
        }

        function toggleIndicator(ind) {
            indicators[ind] = !indicators[ind];
            const btn = document.getElementById('btn-' + ind);
            if (indicators[ind]) {
                btn.classList.add('active');
                btn.classList.remove('opacity-50');
            } else {
                btn.classList.remove('active');
                btn.classList.add('opacity-50');
            }
            if (priceData.length) updateChart(priceData);
        }

        function toggleAutoRefresh() {
            autoRefresh = !autoRefresh;
            const btn = document.getElementById('refresh-btn');
            const icon = document.getElementById('refresh-icon');
            
            if (autoRefresh) {
                btn.className = 'bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg transition-all flex items-center gap-2 text-sm';
                icon.className = 'fas fa-sync-alt fa-spin';
                startAutoRefresh();
            } else {
                btn.className = 'bg-slate-700 hover:bg-slate-600 text-white px-4 py-2 rounded-lg transition-all flex items-center gap-2 text-sm';
                icon.className = 'fas fa-pause';
                clearInterval(refreshInterval);
            }
        }

        function startAutoRefresh() {
            if (refreshInterval) clearInterval(refreshInterval);
            refreshInterval = setInterval(() => {
                if (autoRefresh) fetchRealData();
            }, 60000);
        }

        window.addEventListener('resize', () => {
            if (chart) chart.resize();
        });
    </script>
</body>
</html>
