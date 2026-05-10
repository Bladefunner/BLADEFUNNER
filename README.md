<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BLADEFUNNER</title>
    <style>
        /* ================= 核心重置与 Apple 风格设计变量 ================= */
        :root {
            --bg-color: #fbfbfd;
            --text-main: #1d1d1f;
            --text-muted: #86868b;
            --apple-blue: #0066cc;
            --apple-blue-hover: #0077ed;
            --nav-bg: rgba(251, 251, 253, 0.8);
            --card-bg: #ffffff;
            --border-radius: 18px;
            --shadow: 0 4px 24px rgba(0,0,0,0.06);
        }

        * { margin: 0; padding: 0; box-sizing: border-box; }

        body {
            font-family: -apple-system, BlinkMacSystemFont, "SF Pro Text", "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-main);
            line-height: 1.5;
            -webkit-font-smoothing: antialiased;
        }

        /* ================= 导航栏 ================= */
        nav {
            position: fixed;
            top: 0; width: 100%; height: 50px;
            background: var(--nav-bg);
            backdrop-filter: blur(12px); /* 毛玻璃效果 */
            -webkit-backdrop-filter: blur(12px);
            display: flex; justify-content: center; align-items: center;
            border-bottom: 1px solid rgba(0,0,0,0.05);
            z-index: 1000;
        }

        .nav-container {
            width: 100%; max-width: 980px;
            display: flex; justify-content: space-between; padding: 0 20px;
        }

        .logo { font-weight: 700; letter-spacing: 1px; cursor: pointer; }
        
        .nav-links { display: flex; gap: 30px; }
        .nav-links a {
            text-decoration: none; color: var(--text-main); font-size: 12px;
            letter-spacing: -0.01em; opacity: 0.8; transition: opacity 0.3s;
            cursor: pointer;
        }
        .nav-links a:hover { opacity: 1; color: var(--apple-blue); }

        /* ================= 页面视图容器 ================= */
        .view-section {
            display: none; /* 默认隐藏所有，JS控制显示 */
            padding-top: 80px;
            min-height: 100vh;
            max-width: 980px;
            margin: 0 auto;
            padding-bottom: 100px;
            animation: fadeIn 0.4s ease;
        }
        .view-section.active { display: block; }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* ================= 首页 (Home) ================= */
        .hero { text-align: center; margin-top: 100px; }
        .hero h1 { font-size: 56px; font-weight: 600; letter-spacing: -0.005em; margin-bottom: 20px; }
        .hero p { font-size: 24px; color: var(--text-muted); font-weight: 400; }

        /* ================= 探索页 (Explore) ================= */
        .section-title { font-size: 40px; font-weight: 600; margin: 40px 20px 30px; text-align: center; }
        .grid-container {
            display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 20px; padding: 0 20px;
        }
        .card {
            background: var(--card-bg); border-radius: var(--border-radius); padding: 40px;
            text-align: center; box-shadow: var(--shadow); cursor: pointer;
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }
        .card:hover { transform: scale(1.02); box-shadow: 0 10px 40px rgba(0,0,0,0.1); }
        .card h3 { font-size: 24px; margin-bottom: 10px; }
        .card p { color: var(--text-muted); }

        /* ================= 学习主界面 (阅读器 + 音频) ================= */
        .learning-container {
            display: flex; gap: 30px; padding: 0 20px; margin-top: 20px; flex-wrap: wrap;
        }
        
        .script-area {
            flex: 2; background: var(--card-bg); border-radius: var(--border-radius);
            padding: 40px; box-shadow: var(--shadow); min-width: 300px;
            position: relative; font-size: 18px; line-height: 1.8;
        }

        .script-area h2 { font-size: 24px; margin-bottom: 20px; border-bottom: 1px solid #eee; padding-bottom: 10px;}
        .script-text p { margin-bottom: 15px; cursor: text; }

        .sidebar {
            flex: 1; display: flex; flex-direction: column; gap: 20px; min-width: 300px;
        }

        /* 音频与练习模块 */
        .audio-player, .vocab-book {
            background: var(--card-bg); border-radius: var(--border-radius);
            padding: 30px; box-shadow: var(--shadow);
        }

        .audio-player h3, .vocab-book h3 { margin-bottom: 15px; font-size: 18px; }
        
        .btn-group { display: flex; gap: 10px; margin-top: 15px; flex-wrap: wrap; }
        .btn {
            background: var(--apple-blue); color: white; border: none;
            padding: 10px 16px; border-radius: 20px; font-size: 14px;
            cursor: pointer; transition: background 0.2s;
        }
        .btn:hover { background: var(--apple-blue-hover); }
        .btn-outline {
            background: transparent; color: var(--apple-blue); border: 1px solid var(--apple-blue);
        }
        .btn-outline:hover { background: var(--apple-blue); color: white; }

        /* 模拟上传按钮 */
        .upload-btn-wrapper { position: relative; overflow: hidden; display: inline-block; margin-bottom: 15px; }
        .upload-btn-wrapper input[type=file] {
            font-size: 100px; position: absolute; left: 0; top: 0; opacity: 0; cursor: pointer;
        }

        /* 划词翻译弹窗 (Tooltip) */
        #tooltip {
            position: absolute; display: none; background: #333; color: white;
            padding: 10px 15px; border-radius: 8px; font-size: 14px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.15); z-index: 100;
            max-width: 200px; pointer-events: auto;
        }
        #tooltip .add-vocab-btn {
            display: block; margin-top: 8px; background: var(--apple-blue);
            color: white; border: none; padding: 4px 8px; border-radius: 4px;
            font-size: 12px; cursor: pointer; width: 100%;
        }

        /* 生词本列表 */
        #vocab-list { list-style: none; margin-top: 15px; max-height: 200px; overflow-y: auto; }
        #vocab-list li {
            padding: 8px 0; border-bottom: 1px solid #eee; display: flex; justify-content: space-between; font-size: 14px;
        }
        #vocab-list li:last-child { border-bottom: none; }
        .vocab-word { font-weight: 600; color: var(--apple-blue); }

    </style>
</head>
<body>

    <!-- 顶部导航栏 -->
    <nav>
        <div class="nav-container">
            <div class="logo" onclick="navigateTo('home')">BLADEFUNNER</div>
            <div class="nav-links">
                <a onclick="navigateTo('home')">WHATFUN</a>
                <a onclick="navigateTo('explore')">EXPLORE</a>
            </div>
        </div>
    </nav>

    <!-- 首页视图 -->
    <main id="home-view" class="view-section active">
        <div class="hero">
            <h1>Learn differently.</h1>
            <p>Welcome to BLADEFUNNER. 探索语言的乐趣。</p>
        </div>
    </main>

    <!-- 探索页视图 -->
    <main id="explore-view" class="view-section">
        <h2 class="section-title">Explore Categories</h2>
        <div class="grid-container">
            <div class="card" onclick="navigateTo('english-scripts')">
                <h3>ENGLISH</h3>
                <p>经典剧本跟读与词汇精讲</p>
            </div>
            <div class="card">
                <h3>JAPANESE</h3>
                <p>即将推出 / Coming Soon</p>
            </div>
        </div>
    </main>

    <!-- 剧本选择页 -->
    <main id="english-scripts-view" class="view-section">
        <h2 class="section-title">Scripts Library</h2>
        <div class="grid-container">
            <div class="card" onclick="navigateTo('learning-app')">
                <h3>FRIENDS (老友记)</h3>
                <p>Season 1 - Episode 1: The One Where Monica Gets a Roommate</p>
            </div>
            <div class="card">
                <h3>THE BIG BANG THEORY</h3>
                <p>生活大爆炸经典台词库</p>
            </div>
        </div>
    </main>

    <!-- 核心学习界面 (阅读、听力、口语、生词本) -->
    <main id="learning-app-view" class="view-section">
        <div class="learning-container">
            <!-- 左侧：剧本交互阅读区 -->
            <div class="script-area" id="script-container">
                <h2>FRIENDS - S01E01</h2>
                <div class="script-text">
                    <p><strong>Monica:</strong> There's nothing to tell! He's just some guy I work with!</p>
                    <p><strong>Joey:</strong> C'mon, you're going out with the guy! There's gotta be something wrong with him!</p>
                    <p><strong>Chandler:</strong> All right Joey, be nice. So does he have a hump? A hump and a hairpiece?</p>
                    <p><strong>Phoebe:</strong> Wait, does he eat chalk?</p>
                    <p><strong>Ross:</strong> (entering) Hi.</p>
                    <p><em>(Try selecting any English word above with your mouse to see the dictionary popup!)</em></p>
                </div>
                
                <!-- 划词弹窗 -->
                <div id="tooltip">
                    <span id="tooltip-word"></span><br>
                    <span id="tooltip-def" style="font-size: 12px; color: #ccc;"></span>
                    <button class="add-vocab-btn" onclick="addToVocab()">加入生词本</button>
                </div>
            </div>

            <!-- 右侧：音频工具与生词本 -->
            <div class="sidebar">
                
                <!-- 音频与口语模块 -->
                <div class="audio-player">
                    <h3>Listening & Speaking</h3>
                    <div class="upload-btn-wrapper">
                        <button class="btn btn-outline">上传本地音频</button>
                        <input type="file" id="audio-upload" accept="audio/*" />
                    </div>
                    <audio id="player" controls style="width: 100%; margin-bottom: 15px; display:none;"></audio>
                    
                    <div class="btn-group">
                        <button class="btn">单句练习</button>
                        <button class="btn">模仿跟读 (录音)</button>
                        <button class="btn btn-outline">盲听测试</button>
                    </div>
                </div>

                <!-- 类似“奶酪单词”的生词本模块 -->
                <div class="vocab-book">
                    <h3>My Vocab (生词本)</h3>
                    <div class="btn-group" style="margin-bottom: 10px;">
                        <button class="btn" style="font-size: 12px; padding: 6px 10px;">巩固练习</button>
                        <button class="btn btn-outline" style="font-size: 12px; padding: 6px 10px;" onclick="clearVocab()">清空</button>
                    </div>
                    <ul id="vocab-list">
                        <!-- JS 动态填充 -->
                    </ul>
                </div>

            </div>
        </div>
    </main>

    <!-- 交互逻辑脚本 -->
    <script>
        // === 1. 简单的 SPA 路由逻辑 ===
        function navigateTo(viewId) {
            // 隐藏所有视图
            document.querySelectorAll('.view-section').forEach(el => {
                el.classList.remove('active');
            });
            // 显示目标视图
            document.getElementById(viewId + '-view').classList.add('active');
            window.scrollTo(0, 0);
        }

        // === 2. 本地音频上传与播放逻辑 ===
        const audioUpload = document.getElementById('audio-upload');
        const player = document.getElementById('player');
        
        audioUpload.addEventListener('change', function(e) {
            const file = e.target.files[0];
            if (file) {
                const objectUrl = URL.createObjectURL(file);
                player.src = objectUrl;
                player.style.display = 'block';
            }
        });

        // === 3. 划词翻译与生词本逻辑 ===
        const scriptContainer = document.getElementById('script-container');
        const tooltip = document.getElementById('tooltip');
        const tooltipWord = document.getElementById('tooltip-word');
        const tooltipDef = document.getElementById('tooltip-def');
        const vocabList = document.getElementById('vocab-list');
        
        let currentSelectedWord = '';
        let currentDefinition = '';

        // 模拟一个极其简单的本地词库 (实际应用中这里会调用API)
        const mockDictionary = {
            "hump": "n. 驼峰；隆起",
            "hairpiece": "n. 假发",
            "chalk": "n. 粉笔",
            "roommate": "n. 室友"
        };

        // 监听鼠标抬起事件，获取选中的文本
        scriptContainer.addEventListener('mouseup', function(e) {
            const selection = window.getSelection();
            const text = selection.toString().trim().toLowerCase().replace(/[^a-z]/g, ''); // 简单去标点

            if (text.length > 1) {
                currentSelectedWord = text;
                // 获取释义（模拟）
                currentDefinition = mockDictionary[text] || "暂无精确释义，请点击添加后自行完善。";
                
                // 显示弹窗
                tooltipWord.textContent = text.toUpperCase();
                tooltipDef.textContent = currentDefinition;
                
                // 计算弹窗位置
                const range = selection.getRangeAt(0);
                const rect = range.getBoundingClientRect();
                
                tooltip.style.display = 'block';
                tooltip.style.left = `${rect.left + window.scrollX}px`;
                tooltip.style.top = `${rect.top + window.scrollY - tooltip.offsetHeight - 10}px`;
            } else {
                tooltip.style.display = 'none';
            }
        });

        // 点击空白处隐藏弹窗
        document.addEventListener('mousedown', function(e) {
            if (!tooltip.contains(e.target) && e.target.id !== 'script-container') {
                tooltip.style.display = 'none';
            }
        });

        // 生词本管理 (利用 LocalStorage 保存数据，刷新不丢失)
        function loadVocab() {
            const saved = JSON.parse(localStorage.getItem('bladefunner_vocab')) || [];
            vocabList.innerHTML = '';
            if(saved.length === 0) {
                vocabList.innerHTML = '<li style="color:#888;">生词本空空如也，去划词添加吧！</li>';
            }
            saved.forEach(item => {
                const li = document.createElement('li');
                li.innerHTML = `<span class="vocab-word">${item.word}</span> <span>${item.def.substring(0, 8)}...</span>`;
                vocabList.appendChild(li);
            });
        }

        function addToVocab() {
            let saved = JSON.parse(localStorage.getItem('bladefunner_vocab')) || [];
            // 查重
            if (!saved.some(item => item.word === currentSelectedWord)) {
                saved.push({ word: currentSelectedWord, def: currentDefinition });
                localStorage.setItem('bladefunner_vocab', JSON.stringify(saved));
                loadVocab();
                // 提供反馈
                const btn = tooltip.querySelector('.add-vocab-btn');
                btn.textContent = "已添加 ✓";
                setTimeout(() => { 
                    btn.textContent = "加入生词本"; 
                    tooltip.style.display = 'none';
                    window.getSelection().removeAllRanges();
                }, 1000);
            } else {
                alert('该单词已经在生词本中啦！');
            }
        }

        function clearVocab() {
            if(confirm("确定要清空所有生词吗？")) {
                localStorage.removeItem('bladefunner_vocab');
                loadVocab();
            }
        }

        // 页面加载时初始化生词本
        window.onload = loadVocab;

    </script>
</body>
</html>
