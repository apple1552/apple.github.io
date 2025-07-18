#간호학과 시험시계
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>시험 시계</title>
    <style>
        body {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background-color: #f0f0f0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            color: #333;
        }
        .exam-subject-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            margin-bottom: 20px;
        }
        .exam-subject {
            font-size: 4em; /* 시험 과목 글자 크기를 더 크게 */
            font-weight: bold;
            margin-bottom: 10px; /* 드롭다운과의 간격 */
            color: black; /* 글자색을 검은색으로 변경 */
        }
        .selector-group {
            display: flex;
            gap: 10px; /* 드롭다운 간 간격 */
            margin-bottom: 20px; /* 시험 과목 표시와의 간격 */
        }
        .subject-selector, .grade-selector, .period-selector {
            padding: 8px 12px; /* 패딩을 줄여 드롭다운 크기 작게 */
            font-size: 1em; /* 폰트 크기를 줄여 드롭다운 크기 작게 */
            border-radius: 8px;
            border: 1px solid #ccc;
        }
        .selected-period-display {
            font-size: 1.5em; /* 교시 표시 글자 크기 */
            color: #555;
            margin-bottom: 5px; /* 시간과의 간격 */
            height: 1.5em; /* 공간 확보 */
        }
        .current-time {
            font-size: 6em; /* 시간 글자 크기를 더 크게 */
            font-weight: bold;
            color: #333;
            transition: color 0.5s ease-in-out; /* 색상 변화 애니메이션 */
        }
        .message {
            font-size: 2.5em; /* 메시지 글자 크기 */
            font-weight: bold;
            color: #d9534f; /* 기본적으로 빨간색 */
            margin-top: 20px;
            display: none; /* 초기에는 숨김 */
        }
        .red-text {
            color: #d9534f; /* 빨간색 텍스트 */
        }
    </style>
</head>
<body>
    <div class="exam-subject-container">
        <!-- 학년, 과목, 교시 선택 드롭다운 그룹 -->
        <div class="selector-group">
            <select id="gradeSelector" class="grade-selector">
                <option value="">학년 선택</option>
                <!-- 간호학과 학년 옵션만 유지 -->
                <option value="grade_nursing1">간호학과 1학년</option>
                <option value="grade_nursing2">간호학과 2학년</option>
                <option value="grade_nursing3">간호학과 3학년</option>
                <option value="grade_nursing4">간호학과 4학년</option>
            </select>
            <select id="subjectSelector" class="subject-selector" disabled>
                <option value="">과목 선택</option>
            </select>
            <select id="periodSelector" class="period-selector">
                <option value="">교시 선택</option>
                <option value="1">1교시</option>
                <option value="2">2교시</option>
                <option value="3">3교시</option>
                <option value="4">4교시</option>
                <option value="5">5교시</option>
                <option value="6">6교시</option>
                <option value="7">7교시</option>
                <option value="8">8교시</option>
                <option value="9">9교시</option>
            </select>
        </div>

        <!-- 선택된 시험 과목명과 코드를 함께 표시 -->
        <div class="exam-subject" id="selectedSubject">
            학년을 선택하세요
        </div>
    </div>

    <!-- 선택된 교시를 시간 위에 작게 표시 -->
    <div class="selected-period-display" id="selectedPeriodDisplay"></div>

    <div class="current-time" id="currentTime">
        00:00:00
    </div>
    <div class="message" id="examEndMessage">
        시험 종료
    </div>

    <script>
        // 학년, 과목, 교시 선택 드롭다운 관련 요소
        const gradeSelector = document.getElementById('gradeSelector');
        const subjectSelector = document.getElementById('subjectSelector');
        const periodSelector = document.getElementById('periodSelector');
        const selectedSubjectDisplay = document.getElementById('selectedSubject');
        const selectedPeriodDisplay = document.getElementById('selectedPeriodDisplay');

        // 학년별 과목 코드 매핑 (간호학과 자료만 유지)
        const subjectsByGrade = {
            "none": {}, // 초기 상태
            "grade_nursing1": { "55": "성인간호학", "56": "성인간호학2" },
            "grade_nursing2": { "13": "간호윤리", "18": "간호과정과비판적사고" },
            "grade_nursing3": { "75": "다문화간호" },
            "grade_nursing4": { "42": "아동간호학" }
        };

        // 교시별 시간 정보 매핑
        const periodTimes = {
            "1": "09:00~09:50",
            "2": "10:00~10:50",
            "3": "11:00~11:50",
            "4": "12:00~12:50",
            "5": "13:00~13:50",
            "6": "14:00~14:50",
            "7": "15:00~15:50",
            "8": "16:00~16:50",
            "9": "17:00~17:50"
        };

        // 학년 드롭다운 변경 이벤트 리스너
        gradeSelector.addEventListener('change', function() {
            const selectedGrade = this.value;
            subjectSelector.innerHTML = '<option value="">과목 선택</option>'; // 과목 드롭다운 초기화
            selectedSubjectDisplay.textContent = "과목을 선택하세요"; // 과목 표시 초기화

            if (selectedGrade && subjectsByGrade[selectedGrade]) {
                // 선택된 학년에 맞는 과목으로 과목 드롭다운 채우기
                for (const code in subjectsByGrade[selectedGrade]) {
                    const option = document.createElement('option');
                    option.value = code;
                    option.textContent = subjectsByGrade[selectedGrade][code];
                    subjectSelector.appendChild(option);
                }
                subjectSelector.disabled = false; // 과목 드롭다운 활성화
            } else {
                subjectSelector.disabled = true; // 학년이 선택되지 않으면 과목 드롭다운 비활성화
                selectedSubjectDisplay.textContent = "학년을 선택하세요";
            }
        });

        // 과목 드롭다운 변경 이벤트 리스너
        subjectSelector.addEventListener('change', function() {
            const selectedValue = this.value; // 선택된 옵션의 value (과목 코드)
            const selectedGrade = gradeSelector.value;

            if (selectedValue && selectedGrade) {
                // 과목명과 코드를 괄호 안에 함께 표시
                selectedSubjectDisplay.textContent = `${subjectsByGrade[selectedGrade][selectedValue]} (${selectedValue})`;
            } else if (selectedGrade) {
                selectedSubjectDisplay.textContent = "과목을 선택하세요";
            } else {
                selectedSubjectDisplay.textContent = "학년을 선택하세요";
            }
        });

        // 교시 드롭다운 변경 이벤트 리스너
        periodSelector.addEventListener('change', function() {
            const selectedPeriod = this.value;
            if (selectedPeriod && periodTimes[selectedPeriod]) {
                // 선택된 교시와 해당 시간 정보를 함께 표시
                selectedPeriodDisplay.textContent = `${selectedPeriod}교시 (${periodTimes[selectedPeriod]})`;
            } else {
                selectedPeriodDisplay.textContent = ''; // 선택 없으면 비워둠
            }
        });

        function updateTime() {
            const now = new Date();
            const hours = String(now.getHours()).padStart(2, '0');
            const minutes = String(now.getMinutes()).padStart(2, '0');
            const seconds = String(now.getSeconds()).padStart(2, '0');

            const currentTimeDisplay = document.getElementById('currentTime');
            const examEndMessage = document.getElementById('examEndMessage');

            currentTimeDisplay.textContent = `${hours}:${minutes}:${seconds}`;

            // 40분 ~ 41분 사이에는 시계만 빨간색
            if (minutes >= 40 && minutes < 42) {
                currentTimeDisplay.classList.add('red-text');
                examEndMessage.style.display = 'none'; // 시험 종료 메시지 숨김
            }
            // 42분 이상이 되면 '시험 종료' 메시지 표시, 시계 색상 유지
            else if (minutes >= 42) {
                currentTimeDisplay.classList.add('red-text'); // 시계 색상 유지
                examEndMessage.style.display = 'block'; // 시험 종료 메시지 표시
            }
            // 40분 미만일 때는 기본 색상
            else {
                currentTimeDisplay.classList.remove('red-text');
                examEndMessage.style.display = 'none'; // 시험 종료 메시지 숨김
            }
        }

        // 1초마다 시간 업데이트
        setInterval(updateTime, 1000);

        // 페이지 로드 시 바로 시간 업데이트
        updateTime();
    </script>
</body>
</html>
