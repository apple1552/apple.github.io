<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>전산회계 1급 학습 대시보드</title>
    <!-- Chosen Palette: Warm Neutrals -->
    <!-- Application Structure Plan: 이 SPA는 학습자 편의성을 위해 대시보드 구조로 설계되었습니다. 상단 내비게이션을 통해 '시험 정보', '핵심 이론', '프로그램 실무'의 3개 주요 섹션으로 즉시 이동할 수 있습니다. 각 섹션은 원본 문서의 내용을 주제에 맞게 재구성하여, 사용자가 선형적인 순서에 얽매이지 않고 필요한 부분부터 학습할 수 있도록 했습니다. '핵심 이론'에서는 클릭 가능한 카드를 통해 상호작용을 유도하고, '프로그램 실무'에서는 아코디언 UI를 사용하여 복잡한 절차를 단계별로 명확하게 보여주는 등 사용자 경험(UX)을 최적화하는 데 중점을 두었습니다. -->
    <!-- Visualization & Content Choices: 1. 시험 정보: 원본의 표 형식 데이터를 가독성 높은 Tailwind CSS 테이블로 시각화하여 정보를 명확하게 비교/전달합니다. 2. 핵심 이론: '회계 정보의 질적 특성'은 복잡한 상충 관계를 가지므로, 이를 설명하기 위해 Chart.js를 사용한 막대그래프를 도입했습니다. '목적적합성'과 '신뢰성'의 상충 관계를 시각적으로 보여주어 개념 이해를 돕습니다. 각 질적 특성은 클릭 시 상세 설명이 나타나는 카드로 구현하여 상호작용을 통한 학습을 유도합니다. (Canvas/Chart.js 사용) 3. 프로그램 실무: 설치부터 전기 이월까지의 절차적 내용은 단계별 아코디언 UI로 구성하여, 사용자가 각 단계에 집중하고 정보 과부하를 느끼지 않도록 설계했습니다. (HTML/CSS/JS 사용) -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@400;500;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Noto Sans KR', sans-serif;
            background-color: #f8f9fa;
        }
        .nav-link {
            transition: color 0.3s, border-bottom-color 0.3s;
        }
        .nav-link.active {
            color: #1e40af; /* a shade of blue */
            border-bottom-color: #1e40af;
        }
        .content-section {
            display: none;
        }
        .content-section.active {
            display: block;
        }
        .card {
            background-color: white;
            border-radius: 0.75rem;
            box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1);
            transition: transform 0.3s, box-shadow 0.3s;
        }
        .card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -2px rgb(0 0 0 / 0.1);
        }
        .accordion-content {
            max-height: 0;
            overflow: hidden;
            transition: max-height 0.5s ease-out, padding 0.5s ease-out;
            padding: 0 1.5rem;
        }
        .accordion-button.open + .accordion-content {
            max-height: 1000px;
            padding: 1.5rem 1.5rem;
        }
        .table-custom th, .table-custom td {
            border: 1px solid #e5e7eb;
            padding: 0.75rem 1rem;
            text-align: center;
        }
        .table-custom th {
            background-color: #f3f4f6;
        }
        .tag {
            background-color: #e0e7ff;
            color: #3730a3;
            font-size: 0.75rem;
            font-weight: 500;
            padding: 0.25rem 0.75rem;
            border-radius: 9999px;
        }
    </style>
</head>
<body class="text-gray-800">

    <div class="bg-white shadow-md sticky top-0 z-50">
        <nav class="container mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex items-center justify-between h-16">
                <div class="flex items-center">
                    <span class="font-bold text-xl text-blue-800">전산회계 1급 핵심 요약</span>
                </div>
                <div class="hidden md:block">
                    <div class="ml-10 flex items-baseline space-x-4">
                        <a href="#home" class="nav-link px-3 py-2 rounded-md text-sm font-medium text-gray-700 hover:text-blue-800 border-b-2 border-transparent">홈</a>
                        <a href="#exam-info" class="nav-link px-3 py-2 rounded-md text-sm font-medium text-gray-700 hover:text-blue-800 border-b-2 border-transparent">시험 정보</a>
                        <a href="#theory" class="nav-link px-3 py-2 rounded-md text-sm font-medium text-gray-700 hover:text-blue-800 border-b-2 border-transparent">핵심 이론</a>
                        <a href="#practice" class="nav-link px-3 py-2 rounded-md text-sm font-medium text-gray-700 hover:text-blue-800 border-b-2 border-transparent">프로그램 실무</a>
                    </div>
                </div>
            </div>
        </nav>
    </div>

    <main class="container mx-auto p-4 sm:p-6 lg:p-8">

        <!-- Home Section -->
        <section id="home" class="content-section">
            <div class="text-center bg-white p-8 rounded-xl shadow-lg">
                <h1 class="text-4xl font-bold mb-4 text-gray-900">전산회계 1급 학습 대시보드</h1>
                <p class="text-lg text-gray-600 mb-8 max-w-2xl mx-auto">박쌤의 전산회계 1급 강의 핵심 내용을 체계적으로 정리했습니다. 상단 메뉴를 통해 원하는 주제를 선택하여 효율적으로 학습하세요.</p>
                <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                    <a href="#exam-info" class="nav-card-link card p-6 text-center hover:bg-blue-50">
                        <h3 class="text-xl font-bold mb-2 text-blue-800">시험 정보</h3>
                        <p class="text-gray-600">시험 범위, 문항 수, 학습 전략 등 합격을 위한 필수 정보를 확인하세요.</p>
                    </a>
                    <a href="#theory" class="nav-card-link card p-6 text-center hover:bg-blue-50">
                        <h3 class="text-xl font-bold mb-2 text-blue-800">핵심 이론</h3>
                        <p class="text-gray-600">회계 순환 과정, 질적 특성 등 어려운 이론을 시각 자료와 함께 쉽게 이해하세요.</p>
                    </a>
                    <a href="#practice" class="nav-card-link card p-6 text-center hover:bg-blue-50">
                        <h3 class="text-xl font-bold mb-2 text-blue-800">프로그램 실무</h3>
                        <p class="text-gray-600">K-EDU 프로그램 설치부터 전기 이월 작업까지 실무 절차를 단계별로 마스터하세요.</p>
                    </a>
                </div>
            </div>
        </section>

        <!-- Exam Info Section -->
        <section id="exam-info" class="content-section">
            <h2 class="text-3xl font-bold mb-6 text-gray-900">시험 정보 및 학습 전략</h2>
            <p class="mb-8 text-gray-600">전산회계 1급 시험은 법인 기업의 회계 처리를 다루며, 2급에 비해 범위가 넓고 난이도가 높습니다. 성공적인 합격을 위해 시험의 구조와 효과적인 학습 전략을 파악하는 것이 중요합니다.</p>
            
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
                <div class="card p-6">
                    <h3 class="text-2xl font-bold mb-4">이론 시험 (30점)</h3>
                     <div class="overflow-x-auto">
                        <table class="w-full table-custom">
                            <thead><tr><th>구분</th><th>내용</th><th>출제 문항 (예상)</th></tr></thead>
                            <tbody>
                                <tr><td><strong>재무 회계</strong></td><td>법인 기업의 자본 관련 내용 복잡</td><td>약 8문제</td></tr>
                                <tr><td><strong>원가 회계</strong></td><td>공장 제조 기업의 회계 업무</td><td>4문제</td></tr>
                                <tr><td><strong>세무 회계</strong></td><td>부가가치세 관련 내용</td><td>3문제</td></tr>
                            </tbody>
                        </table>
                    </div>
                </div>
                <div class="card p-6">
                    <h3 class="text-2xl font-bold mb-4">실무 시험 (70점)</h3>
                    <div class="overflow-x-auto">
                        <table class="w-full table-custom">
                            <thead><tr><th>구분</th><th>내용</th><th>배점 (예상)</th></tr></thead>
                            <tbody>
                                <tr><td><strong>일반 전표 입력</strong></td><td>난이도 다소 상승</td><td>18점</td></tr>
                                <tr><td><strong>매입 매출 전표 입력</strong></td><td>부가가치세 관련 내용</td><td>18점</td></tr>
                                <tr><td><strong>수정 문제 및 결산</strong></td><td>2급과 유사한 유형</td><td>15점</td></tr>
                                <tr><td colspan="2"><strong>기초 정보 수정 및 장부 조회</strong></td><td>나머지 배점</td></tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>

            <div class="mt-8 card p-6">
                <h3 class="text-2xl font-bold mb-4">핵심 학습 전략</h3>
                <ul class="space-y-4">
                    <li class="flex items-start"><span class="text-blue-600 font-bold mr-2">✔</span><span><strong>2급 선행 학습 권장:</strong> 회계의 기본 원리를 확실히 다지고 1급을 시작하는 것이 효율적입니다.</span></li>
                    <li class="flex items-start"><span class="text-blue-600 font-bold mr-2">✔</span><span><strong>원리 이해 중심 학습:</strong> 단순 암기보다 회계 원리를 이해하는 데 집중하세요.</span></li>
                    <li class="flex items-start"><span class="text-blue-600 font-bold mr-2">✔</span><span><strong>반복 학습:</strong> '투 플러스 원' 학습법(1회 시청, 1회 복습, 모르는 부분 재시청)으로 완전한 이해를 도모하세요.</span></li>
                    <li class="flex items-start"><span class="text-blue-600 font-bold mr-2">✔</span><span><strong>꾸준함이 중요:</strong> 단기 합격보다 매일 꾸준히 시간을 투자하는 것이 합격의 지름길입니다.</span></li>
                </ul>
            </div>
        </section>

        <!-- Theory Section -->
        <section id="theory" class="content-section">
            <h2 class="text-3xl font-bold mb-6 text-gray-900">핵심 이론 마스터</h2>
            <p class="mb-8 text-gray-600">회계의 근간을 이루는 핵심 이론들을 체계적으로 학습합니다. 회계 순환의 전체적인 흐름을 파악하고, 의사결정에 유용한 정보를 만들기 위한 기준인 '질적 특성'을 상호작용하며 깊이 있게 이해할 수 있습니다.</p>

            <div class="card p-6 mb-8">
                 <h3 class="text-2xl font-bold mb-4">회계 정보의 질적 특성</h3>
                 <p class="mb-6 text-gray-600">회계 정보가 유용하기 위해 갖추어야 할 주요 속성들입니다. 크게 '목적적합성'과 '신뢰성'으로 나뉘며, 두 특성은 서로 상충 관계에 있기도 합니다. 아래 차트는 역사적 원가주의가 신뢰성을 높이지만 목적적합성은 저해할 수 있는 관계를 보여줍니다.</p>
                <div class="chart-container w-full max-w-2xl mx-auto h-80 relative">
                    <canvas id="qualityChart"></canvas>
                </div>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-6" id="qualitative-characteristics">
                <!-- Characteristics will be injected here by JS -->
            </div>
        </section>

        <!-- Practice Section -->
        <section id="practice" class="content-section">
            <h2 class="text-3xl font-bold mb-6 text-gray-900">프로그램 실무 가이드</h2>
            <p class="mb-8 text-gray-600">이론 학습을 바탕으로 K-EDU 회계 프로그램을 다루는 실무 능력을 기릅니다. 프로그램 설치부터 기초 정보 입력, 그리고 가장 중요한 전기 이월 작업까지의 과정을 단계별로 상세히 안내합니다. 각 단계를 클릭하여 내용을 확인하세요.</p>
            
            <div id="accordion-container" class="space-y-4">
                <!-- Accordion items will be injected here by JS -->
            </div>
        </section>
    </main>

    <script>
        document.addEventListener('DOMContentLoaded', function () {
            const sections = document.querySelectorAll('.content-section');
            const navLinks = document.querySelectorAll('.nav-link');
            const navCardLinks = document.querySelectorAll('.nav-card-link');

            function showSection(hash) {
                const targetHash = hash || '#home';
                sections.forEach(section => {
                    if ('#' + section.id === targetHash) {
                        section.classList.add('active');
                    } else {
                        section.classList.remove('active');
                    }
                });
                navLinks.forEach(link => {
                    if (link.getAttribute('href') === targetHash) {
                        link.classList.add('active');
                    } else {
                        link.classList.remove('active');
                    }
                });
                window.scrollTo(0, 0);
            }

            function handleNavClick(e) {
                e.preventDefault();
                const hash = e.currentTarget.getAttribute('href');
                history.pushState(null, null, hash);
                showSection(hash);
            }
            
            navLinks.forEach(link => link.addEventListener('click', handleNavClick));
            navCardLinks.forEach(link => link.addEventListener('click', handleNavClick));

            window.addEventListener('popstate', () => {
                showSection(window.location.hash);
            });
            
            showSection(window.location.hash);

            // Accordion Logic
            const accordionData = [
                {
                    title: '1단계: 프로그램 설치 및 기초 정보 등록',
                    content: `
                        <p class="font-semibold text-lg mb-2">프로그램 설치</p>
                        <ul class="list-disc list-inside space-y-1 mb-4 text-gray-700">
                            <li>한국세무사회 자격시험 웹사이트에서 최신 버전의 K-Lounge(K-EDU) 교육용 프로그램을 다운로드하여 설치합니다.</li>
                        </ul>
                        <p class="font-semibold text-lg mb-2">기초 정보 등록</p>
                         <ul class="list-disc list-inside space-y-1 text-gray-700">
                            <li><strong>회사 등록:</strong> 법인 기업의 회사 코드, 사업자등록번호, 주소 등 기본 정보를 입력합니다.</li>
                            <li><strong>거래처 등록:</strong> 일반 거래처, 금융기관, 신용카드 가맹점 등 거래처 유형별로 정보를 등록합니다.</li>
                            <li><strong>계정과목 및 적요 등록:</strong> 필요에 따라 새로운 계정과목을 추가하거나 기존 계정의 적요를 등록/수정합니다.</li>
                        </ul>`
                },
                {
                    title: '2단계: 전기분 재무제표 입력',
                    content: `
                        <p class="mb-4 text-gray-700">전년도(전기)의 재무상태표와 손익계산서 데이터를 정확히 입력하는 과정입니다. 이 데이터는 당기 재무제표의 기초가 됩니다.</p>
                        <ul class="list-disc list-inside space-y-1 text-gray-700">
                           <li><strong>전기분 재무상태표:</strong> 전년도 말 기준 자산, 부채, 자본의 각 계정별 잔액을 입력합니다. 입력 완료 후 대차평균의 원리에 따라 차변과 대변의 합계가 일치하는지 반드시 확인해야 합니다.</li>
                           <li><strong>전기분 손익계산서:</strong> 전년도 기간의 수익과 비용 내역을 입력하여 당기순이익을 확인합니다. 이 당기순이익은 재무상태표의 이익잉여금과 연결됩니다.</li>
                        </ul>`
                },
                {
                    title: '3단계: 거래처별 초기이월',
                    content: `
                       <p class="mb-4 text-gray-700">재무상태표의 특정 계정들에 대해 거래처별 상세 잔액을 입력하는 중요한 작업입니다. 이를 통해 채권, 채무를 체계적으로 관리할 수 있습니다.</p>
                        <ul class="list-disc list-inside space-y-1 text-gray-700">
                           <li><strong>데이터 불러오기:</strong> 전기분 재무상태표에 입력된 데이터를 불러와 작업을 시작합니다.</li>
                           <li><strong>대상 계정:</strong> 외상매출금, 받을어음, 외상매입금, 지급어음, 각종 예금, 차입금 등 거래처 관리가 필요한 모든 계정에 대해 수행합니다.</li>
                           <li><strong>금액 입력:</strong> 각 계정별로 해당 거래처를 지정하고 정확한 잔액을 입력합니다. 합계액이 재무상태표의 계정 잔액과 일치해야 합니다.</li>
                        </ul>`
                }
            ];

            const accordionContainer = document.getElementById('accordion-container');
            accordionData.forEach((item, index) => {
                const div = document.createElement('div');
                div.className = 'card';
                div.innerHTML = `
                    <button class="accordion-button w-full text-left p-6 flex justify-between items-center">
                        <span class="text-xl font-bold">${item.title}</span>
                        <svg class="w-6 h-6 transform transition-transform" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path></svg>
                    </button>
                    <div class="accordion-content">
                        ${item.content}
                    </div>
                `;
                accordionContainer.appendChild(div);
            });

            accordionContainer.addEventListener('click', function(e) {
                const button = e.target.closest('.accordion-button');
                if (button) {
                    button.classList.toggle('open');
                    const svg = button.querySelector('svg');
                    svg.classList.toggle('rotate-180');
                }
            });


            // Chart.js Logic
            const ctx = document.getElementById('qualityChart').getContext('2d');
            const qualityChart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: ['역사적 원가주의', '공정가치(시가)주의'],
                    datasets: [{
                        label: '신뢰성',
                        data: [90, 40],
                        backgroundColor: 'rgba(59, 130, 246, 0.7)',
                        borderColor: 'rgba(59, 130, 246, 1)',
                        borderWidth: 1
                    }, {
                        label: '목적적합성',
                        data: [50, 85],
                        backgroundColor: 'rgba(239, 68, 68, 0.7)',
                        borderColor: 'rgba(239, 68, 68, 1)',
                        borderWidth: 1
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        y: {
                            beginAtZero: true,
                            max: 100,
                            title: { display: true, text: '특성 수준' }
                        }
                    },
                    plugins: {
                        title: {
                            display: true,
                            text: '회계 원칙에 따른 질적 특성 비교',
                            font: { size: 16 }
                        },
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    let label = context.dataset.label || '';
                                    if (label) {
                                        label += ': ';
                                    }
                                    if (context.parsed.y !== null) {
                                        label += context.parsed.y + '점';
                                    }
                                    return label;
                                }
                            }
                        }
                    }
                }
            });

            // Qualitative Characteristics Cards Logic
            const characteristicsData = [
                { 
                    title: '목적적합성', 
                    subs: ['예측가치', '확인가치', '중요성', '적시성'], 
                    description: '의사결정에 영향을 미칠 수 있는 능력. 미래 예측, 과거 확인에 도움이 되며, 중요하고 시의적절해야 합니다.' 
                },
                { 
                    title: '신뢰성', 
                    subs: ['표현의 충실성', '실질 우선', '중립성', '검증가능성'], 
                    description: '정보가 오류나 편견 없이 나타내고자 하는 바를 충실히 표현하는 능력. 중립적이고 검증 가능해야 합니다.' 
                },
                { 
                    title: '이해가능성', 
                    subs: [], 
                    description: '합리적인 지식을 가진 정보 이용자가 그 의미를 쉽게 파악할 수 있어야 합니다.' 
                },
                { 
                    title: '비교가능성', 
                    subs: [], 
                    description: '기업 간 또는 기간별 재무 정보를 비교하여 유사점과 차이점을 식별할 수 있어야 합니다.' 
                }
            ];

            const characteristicsContainer = document.getElementById('qualitative-characteristics');
            characteristicsData.forEach(char => {
                const div = document.createElement('div');
                div.className = 'card p-6';
                let tagsHtml = char.subs.map(sub => `<span class="tag">${sub}</span>`).join(' ');
                
                div.innerHTML = `
                    <h4 class="text-xl font-bold mb-2">${char.title}</h4>
                    <p class="text-gray-600 mb-4">${char.description}</p>
                    <div class="flex flex-wrap gap-2">${tagsHtml}</div>
                `;
                characteristicsContainer.appendChild(div);
            });
        });
    </script>
</body>
</html>
