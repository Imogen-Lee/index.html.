<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes, viewport-fit=cover">
    <title>绿野未来 | 向内求索，向外生长</title>
    <!-- 引入 Leaflet 地图库 (轻量、现代、满足中国地图足迹) -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <!-- 引入 Google Fonts 提升质感 -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:opsz,wght@14..32,300;14..32,400;14..32,500;14..32,600;14..32,700&family=Playfair+Display:ital@0;1&display=swap" rel="stylesheet">
    <!-- fontawesome 6 (免费图标库) -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            cursor: none; /* 隐藏默认光标，配合自定义跟随光效 */
        }

        /* 自定义鼠标跟随光晕 */
        .custom-cursor {
            width: 32px;
            height: 32px;
            background: radial-gradient(circle, rgba(34,197,94,0.5) 0%, rgba(16,185,129,0.2) 70%, transparent 100%);
            border-radius: 50%;
            position: fixed;
            pointer-events: none;
            z-index: 9999;
            transform: translate(-50%, -50%);
            transition: transform 0.08s ease-out;
            mix-blend-mode: screen;
            backdrop-filter: blur(2px);
        }
        
        /* 大屏时圆环辅助 */
        .cursor-ring {
            width: 8px;
            height: 8px;
            background-color: #22c55e;
            border-radius: 50%;
            position: fixed;
            pointer-events: none;
            z-index: 10000;
            transform: translate(-50%, -50%);
            transition: width 0.2s, height 0.2s;
            box-shadow: 0 0 12px #10b981;
        }

        body {
            background: #FEFBF5;  /* 奶白色基调 */
            font-family: 'Inter', sans-serif;
            color: #2D3E2F;
            line-height: 1.5;
            scroll-behavior: smooth;
            overflow-x: hidden;
        }

        /* 森系绿色主调 + 呼吸/渐变效果 */
        h1, h2, h3 {
            font-family: 'Playfair Display', serif;
            font-weight: 600;
            letter-spacing: -0.01em;
        }

        h2 {
            font-size: 2.2rem;
            margin-bottom: 2rem;
            position: relative;
            display: inline-block;
            background: linear-gradient(120deg, #22c55e, #10b981);
            background-clip: text;
            -webkit-background-clip: text;
            color: transparent;
        }
        h2:after {
            content: '';
            position: absolute;
            bottom: -10px;
            left: 0;
            width: 60px;
            height: 3px;
            background: linear-gradient(90deg, #22c55e, #a7f3d0);
            border-radius: 4px;
        }
        
        .section {
            padding: 80px 5%;
            max-width: 1280px;
            margin: 0 auto;
            border-bottom: 1px solid rgba(34,197,94,0.1);
        }
        .container {
            width: 100%;
        }

        /* 卡片悬浮微动效 + 柔和边框 */
        .card-hover {
            background: rgba(255,255,240,0.7);
            backdrop-filter: blur(2px);
            border-radius: 32px;
            padding: 1.8rem;
            transition: all 0.35s cubic-bezier(0.2, 0, 0, 1);
            box-shadow: 0 10px 25px -5px rgba(0,0,0,0.03), 0 8px 10px -6px rgba(0,0,0,0.02);
            border: 1px solid rgba(34,197,94,0.25);
        }
        .card-hover:hover {
            transform: translateY(-8px);
            box-shadow: 0 25px 35px -12px rgba(34,197,94,0.2);
            border-color: #22c55e80;
            background: #ffffffdd;
        }

        /* 按钮呼吸动画 */
        .btn {
            display: inline-block;
            padding: 12px 32px;
            background: linear-gradient(135deg, #22c55e, #10b981);
            border: none;
            border-radius: 60px;
            color: white;
            font-weight: 600;
            font-size: 1rem;
            transition: all 0.3s;
            box-shadow: 0 8px 14px rgba(34,197,94,0.2);
            text-decoration: none;
        }
        .btn:hover {
            transform: scale(1.02);
            box-shadow: 0 12px 20px rgba(34,197,94,0.4);
            background: linear-gradient(135deg, #2bde6e, #16c999);
        }
        /* 呼吸动画 */
        @keyframes softBreathing {
            0% { opacity: 0.9; transform: scale(1);}
            100% { opacity: 1; transform: scale(1.02);}
        }
        .breathing-icon {
            animation: softBreathing 3s infinite alternate ease-in-out;
        }

        /* hero区域 */
        .hero {
            min-height: 90vh;
            display: flex;
            align-items: center;
            justify-content: center;
            text-align: center;
            background: radial-gradient(circle at 10% 30%, rgba(167,243,208,0.2), #FEFBF5);
            position: relative;
        }
        .hero-content h1 {
            font-size: 4rem;
            background: linear-gradient(to right, #1f6e43, #22c55e);
            background-clip: text;
            -webkit-background-clip: text;
            color: transparent;
            margin-bottom: 1rem;
        }
        .hero-tag {
            background: #e9f9ef;
            display: inline-block;
            padding: 6px 18px;
            border-radius: 40px;
            color: #15803d;
            font-weight: 500;
            margin: 1rem 0;
        }

        /* 地图容器 */
        .map-container {
            border-radius: 32px;
            overflow: hidden;
            box-shadow: 0 20px 35px -15px rgba(0,0,0,0.1);
            height: 380px;
            border: 1px solid #dcfce7;
        }
        #chinaMap {
            height: 100%;
            width: 100%;
            z-index: 1;
        }

        /* 两列布局 */
        .grid-2 {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 2rem;
        }
        .grid-3 {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 1.8rem;
        }
        .badge-container {
            display: flex;
            flex-wrap: wrap;
            gap: 12px;
            margin-top: 1rem;
        }
        .skill-badge {
            background: #ecfdf5;
            padding: 8px 20px;
            border-radius: 50px;
            font-weight: 500;
            color: #0b5e42;
            border: 1px solid #a7f3d0;
            transition: all 0.2s;
        }
        .skill-badge:hover {
            background: #22c55e20;
            transform: translateY(-2px);
        }

        /* 过往经历 timeline 简雅 */
        .timeline-item {
            border-left: 3px solid #22c55e;
            padding-left: 1.5rem;
            margin-bottom: 2rem;
        }

        /* 联系区块 */
        .contact-block {
            background: linear-gradient(115deg, #ffffff, #f7fee7);
            border-radius: 48px;
            padding: 2.5rem;
            text-align: center;
            border: 1px solid #bef264;
        }

        footer {
            text-align: center;
            padding: 40px 20px;
            background: #FEF4E8;
            color: #3f5e3a;
            font-size: 0.9rem;
        }

        @media (max-width: 768px) {
            .hero-content h1 { font-size: 2.5rem; }
            h2 { font-size: 1.8rem; }
            .section { padding: 60px 5%; }
            .custom-cursor, .cursor-ring { display: none; } /* 移动端隐藏自定义光标，恢复默认 */
            body { cursor: auto; }
            * { cursor: auto; }
        }
    </style>
</head>
<body>

<!-- 鼠标跟随元素 -->
<div class="custom-cursor"></div>
<div class="cursor-ring"></div>

<!-- Hero 板块 -->
<section class="hero">
    <div class="container hero-content">
        <div class="breathing-icon">
            <i class="fas fa-seedling" style="font-size: 3rem; color: #22c55e; opacity: 0.7;"></i>
        </div>
        <h1>绿野未来科技</h1>
        <p style="font-size: 1.3rem; max-width: 600px; margin: 0 auto;">向内求索，向外生长</p>
        <div class="hero-tag">🌱 可持续发展 · 绿色智慧解决方案</div>
        <p style="margin: 1rem 0 2rem;">【让技术扎根土壤，生长出有温度的数字生态】</p>
        <a href="#contact" class="btn">探索合作 <i class="fas fa-arrow-right"></i></a>
    </div>
</section>

<!-- 关于我们 (公司版) -->
<section class="section" id="about">
    <div class="container">
        <h2>关于我们</h2>
        <div class="grid-2">
            <div class="card-hover">
                <i class="fas fa-leaf" style="font-size: 2rem; color: #22c55e; margin-bottom: 1rem;"></i>
                <p style="font-size: 1.05rem;">绿野未来成立于2022年，由一群热爱自然与科技创新的跨领域伙伴组成。我们相信真正高级的科技是“隐形的”，如同森林的呼吸。深耕智慧环保、碳中和互动媒体与乡村数字振兴，为客户提供兼具美感与生命力的数字产品。</p>
                <p style="margin-top: 1rem;"><strong>职业路径：</strong> 环境信息学 · 创意技术 · 绿色金融科技</p>
                <p><strong>擅长：</strong> 交互式数据叙事 / 低碳社区构建 / 智能感知UI</p>
                <p><strong>正在学习：</strong> 生态AI代理 / 再生设计系统</p>
            </div>
            <div class="card-hover">
                <i class="fas fa-users" style="font-size: 2rem; color: #10b981; margin-bottom: 1rem;"></i>
                <p>从代码到土壤，我们持续探索技术背后的生态责任。团队核心成员曾参与联合国开发计划署可持续项目、国家级湿地公园数智化改造，坚持用“柔韧创新”解决真实问题。</p>
                <div style="margin-top: 1rem;">
                    <span class="skill-badge">#生态设计</span>
                    <span class="skill-badge">#数据向善</span>
                    <span class="skill-badge">#未来农场</span>
                </div>
            </div>
        </div>
    </div>
</section>

<!-- 我的足迹 (带中国地图) 使用 Leaflet 交互地图 + 城市标记 -->
<section class="section" id="footprint">
    <div class="container">
        <h2>🌿 我的足迹 · 绿野中国</h2>
        <p style="margin-bottom: 1.5rem;">扎根大地，在每一个充满生命力的城市播撒绿色种子</p>
        <div class="map-container">
            <div id="chinaMap"></div>
        </div>
        <div class="badge-container" style="justify-content: center; margin-top: 20px;">
            <span class="skill-badge"><i class="fas fa-map-marker-alt"></i> 北京 · 研发总部</span>
            <span class="skill-badge"><i class="fas fa-map-marker-alt"></i> 上海 · 创意中心</span>
            <span class="skill-badge"><i class="fas fa-map-marker-alt"></i> 成都 · 智慧农业实验室</span>
            <span class="skill-badge"><i class="fas fa-map-marker-alt"></i> 深圳 · 硬件协同基地</span>
            <span class="skill-badge"><i class="fas fa-map-marker-alt"></i> 杭州 · 数字乡村实践地</span>
        </div>
        <p style="text-align: center; margin-top: 1rem;">海外出差 · 新加坡 | 柏林 | 京都 · 国际生态设计交流</p>
    </div>
</section>

<!-- 我正在做的事 (两个卡片) -->
<section class="section" id="doing">
    <div class="container">
        <h2>🌱 我正在做的事</h2>
        <div class="grid-2">
            <div class="card-hover">
                <i class="fas fa-code-branch" style="font-size: 2rem; color: #22c55e;"></i>
                <h3 style="margin: 0.8rem 0 0.5rem;">碳迹·光影互动装置</h3>
                <p>联合上海自然博物馆打造沉浸式碳足迹可视化装置，结合实时传感器与生成艺术，让用户“看见”日常选择对生态环境的涟漪。</p>
                <ul style="margin-top: 12px; list-style: none; padding-left: 0;">
                    <li>✓ 已完成硬件原型 + 低代码交互框架</li>
                    <li>✓ 落地测试阶段，2025年末首展</li>
                </ul>
            </div>
            <div class="card-hover">
                <i class="fas fa-graduation-cap" style="font-size: 2rem; color: #10b981;"></i>
                <h3 style="margin: 0.8rem 0 0.5rem;">自我成长 · 再生设计学社</h3>
                <p>创立内部“绿野学院”，每周共学前沿生态设计、Bloomburrow 树莓派生态监测，同时探索人工智能辅助生态修复决策。</p>
                <ul style="margin-top: 12px; list-style: none; padding-left: 0;">
                    <li>📚 在读《地球系统与再生设计》</li>
                    <li>🧪 实验开源林地微气象站项目</li>
                </ul>
            </div>
        </div>
    </div>
</section>

<!-- 过往经历 (详细版) 时间线风格 -->
<section class="section" id="history">
    <div class="container">
        <h2>📌 过往经历 · 重要印记</h2>
        <div class="grid-2">
            <div>
                <div class="timeline-item">
                    <h3>🌾 2023 - 2024 | 智慧乡村碳中和平台</h3>
                    <p>为云南那崩村落地国内首个“零碳火把节”数字碳账本，联合村民碳积分体系，获SDGs创新案例提名。</p>
                </div>
                <div class="timeline-item">
                    <h3>💧 2022 - 2023 | 长江流域生态数据库可视化</h3>
                    <p>受世界自然基金会(WWF)委托，开发长江江豚栖息地交互地图，帮助公众科学参与保护行动。</p>
                </div>
                <div class="timeline-item">
                    <h3>🎓 2021 | 米兰理工 · 数字环境艺术驻留</h3>
                    <p>参与跨学科驻地项目，作品《空气的棱镜》在绿色设计周展出。</p>
                </div>
            </div>
            <div>
                <div class="timeline-item">
                    <h3>🌟 2024 | 发布“林栖”开源生态UI组件库</h3>
                    <p>为低碳应用提供可爱的森系设计语言，累计被300+开发者和设计团队引用。</p>
                </div>
                <div class="timeline-item">
                    <h3>🏆 2023 哈佛中国论坛 eco-tech 分论坛讲者</h3>
                    <p>分享技术驱动下的社区气候行动，并展示旗下“碳笔记”轻应用原型。</p>
                </div>
                <div class="timeline-item">
                    <h3>📱 2022-2023 硅谷绿色加速器成员</h3>
                    <p>完成国际碳中和产品设计冲刺，与拉丁美洲环保组织建立合作网络。</p>
                </div>
            </div>
        </div>
    </div>
</section>

<!-- 我关注与学习的事 + 技能与爱好标签 融合为 "关注 & 核心能力" -->
<section class="section" id="focus">
    <div class="container">
        <h2>📖 我关注与学习的事</h2>
        <div class="grid-3" style="margin-bottom: 2rem;">
            <div class="card-hover">
                <i class="fas fa-chart-line"></i>
                <h3>再生型经济模型</h3>
                <p>追踪 Doughnut Economics、生态阈值框架，探索企业如何超越净零实现“净正值”。</p>
            </div>
            <div class="card-hover">
                <i class="fas fa-microchip"></i>
                <h3>边缘AI & 野生动植物保护</h3>
                <p>研究低功耗边缘推理用于东北虎豹国家公园物种识别，开源轻量模型部署。</p>
            </div>
            <div class="card-hover">
                <i class="fas fa-palette"></i>
                <h3>生成式生态艺术(GxEco)</h3>
                <p>结合LLM与气候数据的诗意化表达，让科技富有情感的叙事力量。</p>
            </div>
        </div>
        
        <h2 style="margin-top: 3rem;">🍃 技能与爱好标签</h2>
        <div class="badge-container" style="margin-top: 0.5rem;">
            <span class="skill-badge">交互原型设计</span>
            <span class="skill-badge">Python·生态建模</span>
            <span class="skill-badge">Figma 森系组件</span>
            <span class="skill-badge">JavaScript/Three.js</span>
            <span class="skill-badge">GIS 空间分析</span>
            <span class="skill-badge">自然教育引导</span>
            <span class="skill-badge">手绘植物图谱</span>
            <span class="skill-badge">园艺疗法</span>
            <span class="skill-badge">即兴自然写作</span>
        </div>
        <p style="margin-top: 28px; font-style: italic; background: #ecfdf5; padding: 1rem; border-radius: 28px;">
        💚 爱好：森林徒步、生态摄影、阅读 & 冥想、永续农业实践 — 不断学习尝试新东西，保持积极和新鲜感。
        </p>
    </div>
</section>

<!-- 联系与合作 (最后一个板块) -->
<section class="section" id="contact">
    <div class="container contact-block">
        <i class="fas fa-paper-plane" style="font-size: 2.5rem; color: #22c55e;"></i>
        <h2 style="color: #2c5e2a;">联系与合作</h2>
        <p style="font-size: 1.15rem; max-width: 550px; margin: 1rem auto;">无论是共创绿色项目、演讲邀请，还是单纯想聊聊生态与设计，都欢迎来信。</p>
        <div style="margin: 2rem 0;">
            <a href="mailto:hello@greenvista.com" style="font-size: 1.3rem; font-weight: 600; color: #0b5e42; text-decoration: none; background: #dcfce7; padding: 10px 24px; border-radius: 60px;">
                <i class="fas fa-envelope"></i> hello@greenvista.com
            </a>
        </div>
        <div class="badge-container" style="justify-content: center;">
            <span class="skill-badge"><i class="fab fa-weixin"></i> 绿野未来_小助手</span>
            <span class="skill-badge"><i class="fab fa-linkedin"></i> GreenVista Lab</span>
            <span class="skill-badge"><i class="fab fa-github"></i> green-ecosphere</span>
        </div>
        <p style="margin-top: 1.8rem;">📞 合作专线: +86 (010) 6502 8833</p>
    </div>
</section>

<!-- 底部版权 -->
<footer>
    <p>© 2026 绿野未来科技 · 向内求索，向外生长</p>
    <p style="margin-top: 8px;">🌿 森系清新 · 永续设计 | 基于生命力的数字体验</p>
    <p style="font-size: 0.7rem; margin-top: 12px;">#lovable #AI #零代码开发精神 单文件自适应网页 电脑&手机版</p>
</footer>

<script>
    // 自定义鼠标跟随光效 (桌面端)
    const cursor = document.querySelector('.custom-cursor');
    const ring = document.querySelector('.cursor-ring');
    if (window.innerWidth > 768) {
        document.addEventListener('mousemove', (e) => {
            cursor.style.left = e.clientX + 'px';
            cursor.style.top = e.clientY + 'px';
            ring.style.left = e.clientX + 'px';
            ring.style.top = e.clientY + 'px';
        });
        // 悬浮在可交互元素上时放大光晕
        const interactive = document.querySelectorAll('a, button, .btn, .card-hover, .skill-badge');
        interactive.forEach(el => {
            el.addEventListener('mouseenter', () => {
                ring.style.width = '16px';
                ring.style.height = '16px';
                ring.style.backgroundColor = '#90e0b0';
                cursor.style.width = '48px';
                cursor.style.height = '48px';
            });
            el.addEventListener('mouseleave', () => {
                ring.style.width = '8px';
                ring.style.height = '8px';
                ring.style.backgroundColor = '#22c55e';
                cursor.style.width = '32px';
                cursor.style.height = '32px';
            });
        });
    } else {
        // 移动端隐藏自定义光标元素
        if(cursor) cursor.style.display = 'none';
        if(ring) ring.style.display = 'none';
    }

    // 初始化中国地图足迹 (Leaflet 开源地图，展现城市标记)
    const map = L.map('chinaMap').setView([35.0, 108.0], 5); // 中国居中适当缩放
    // 加载优雅的瓦片图层 (CartoDB 浅色风格, 符合清新调性)
    L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
        attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OSM</a> &copy; <a href="https://carto.com/">CARTO</a>',
        subdomains: 'abcd',
        maxZoom: 10,
        minZoom: 4
    }).addTo(map);
    
    // 足迹城市(坐标)
    const cities = [
        { name: "北京 · 研发总部", coords: [39.9042, 116.4074], desc: "可持续算法 & 生态交互实验室" },
        { name: "上海 · 创意中心", coords: [31.2304, 121.4737], desc: "数字叙事与体验设计" },
        { name: "成都 · 智慧农业实验室", coords: [30.5728, 104.0668], desc: "AIoT + 永续农田" },
        { name: "深圳 · 硬件协同基地", coords: [22.5431, 114.0579], desc: "低碳硬件开发" },
        { name: "杭州 · 数字乡村实践地", coords: [30.2741, 120.1551], desc: "共富生态村计划" },
        { name: "昆明·西南生物多样性据点", coords: [25.0443, 102.7097], desc: "高黎贡山物种监测" }
    ];
    
    // 添加自定义绿色图标 (简单优雅)
    const greenIcon = L.divIcon({
        className: 'custom-map-icon',
        html: '<i class="fas fa-leaf" style="color: #22c55e; font-size: 22px; text-shadow: 0 2px 5px rgba(0,0,0,0.1);"></i>',
        iconSize: [24, 28],
        popupAnchor: [0, -12]
    });
    
    cities.forEach(city => {
        const marker = L.marker(city.coords, { icon: greenIcon }).addTo(map);
        marker.bindPopup(`
            <b style="color: #1f6e43;">🌱 ${city.name}</b><br>
            <span style="font-size: 0.85rem;">${city.desc}</span><br>
            <span style="font-size: 0.75rem;">✨ 绿野足迹 · 生态协作</span>
        `);
    });
    
    // 添加微小圆圈范围表示生态影响力 (装饰)
    const boundsGroup = L.layerGroup().addTo(map);
    cities.forEach(city => {
        L.circle(city.coords, {
            color: '#86efac',
            weight: 1,
            fillColor: '#86efac66',
            fillOpacity: 0.2,
            radius: 80000
        }).addTo(boundsGroup);
    });
    
    // 确保地图自适应重绘
    window.addEventListener('resize', () => {
        setTimeout(() => map.invalidateSize(), 100);
    });
    
    // 轻微的呼吸动画应用到 hero 中的图标元素 (二次确认)
    const breathingEl = document.querySelector('.breathing-icon');
    if(breathingEl) {
        setInterval(() => {
            breathingEl.style.transform = 'scale(1.02)';
            setTimeout(() => breathingEl.style.transform = 'scale(1)', 2000);
        }, 3000);
    }
    
    // 平滑滚动导航 (如果有内部锚点)
    document.querySelectorAll('a[href^="#"]').forEach(anchor => {
        anchor.addEventListener('click', function(e) {
            const targetId = this.getAttribute('href');
            if(targetId !== "#" && targetId !== "") {
                const target = document.querySelector(targetId);
                if(target) {
                    e.preventDefault();
                    target.scrollIntoView({ behavior: 'smooth', block: 'start' });
                }
            }
        });
    });
    
    // 同时给卡片增加微悬浮动画(已在css完成) & 添加温柔的入场渐变
    const sections = document.querySelectorAll('.section');
    const observer = new IntersectionObserver((entries) => {
        entries.forEach(entry => {
            if(entry.isIntersecting) {
                entry.target.style.opacity = '1';
                entry.target.style.transform = 'translateY(0)';
            }
        });
    }, { threshold: 0.1 });
    sections.forEach(section => {
        section.style.opacity = '0';
        section.style.transform = 'translateY(25px)';
        section.style.transition = 'opacity 0.7s ease, transform 0.5s ease';
        observer.observe(section);
    });
    // 强制触发第一屏 hero 不透明度？让hero本身直接可见
    document.querySelector('.hero').style.opacity = '1';
    document.querySelector('.hero').style.transform = 'translateY(0)';
    
    // 添加demo 文案适配（防止地图因为资源额外报错，无影响）
    console.log("绿野未来 | 森系清新公司展示页已启动 | 基于LoveLedger卡片风格 + 中国地图足迹");
</script>
</body>
</html>
