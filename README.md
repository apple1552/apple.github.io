<!DOCTYPE html>
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
        .subject-selector, .grade-selector {
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
            text-align: center; /* 텍스트 중앙 정렬 */
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
        <!-- 학년, 과목 선택 드롭다운 그룹 -->
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
    <!-- 시험 종료 5분전 메시지 추가 -->
    <div class="message" id="examFiveMinLeftMessage">
        시험 종료 5분전
    </div>
    <div class="message" id="examEndMessage">
        시험 종료
    </div>

    <script>
        // 학년, 과목 선택 드롭다운 관련 요소
        const gradeSelector = document.getElementById('gradeSelector');
        const subjectSelector = document.getElementById('subjectSelector');
        const selectedSubjectDisplay = document.getElementById('selectedSubject');
        const selectedPeriodDisplay = document.getElementById('selectedPeriodDisplay');
        const examFiveMinLeftMessage = document.getElementById('examFiveMinLeftMessage'); // 5분전 메시지 요소
        const examEndMessage = document.getElementById('examEndMessage'); // 시험 종료 메시지 요소

        // 학년별 과목 코드 매핑 (간호학과 자료만 유지)
        const subjectsByGrade = {
            "none": {}, // 초기 상태
            "grade_nursing1": { "55": "성인간호학", "56": "성인간호학2" },
            "grade_nursing2": { "13": "간호윤리", "18": "간호과정과비판적사고" },
            "grade_nursing3": { "75": "다문화간호" },
            "grade_nursing4": { "42": "아동간호학" }
        };

        /**
         * 현재 시간에 따라 교시 정보를 반환합니다.
         * 9시부터 50분 수업, 10분 쉬는 시간 기준으로 계산됩니다.
         * @param {number} hours 현재 시 (24시간 형식)
         * @param {number} minutes 현재 분
         * @returns {string} 현재 교시 정보 (예: "1교시 (09:00~09:50)", "쉬는 시간", "수업 전", "수업 종료")
         */
        function getPeriodByTime(hours, minutes) {
            const periodData = [
                { period: 1, startHour: 9, startMin: 0, endHour: 9, endMin: 50 },
                { period: 2, startHour: 10, startMin: 0, endHour: 10, endMin: 50 },
                { period: 3, startHour: 11, startMin: 0, endHour: 11, endMin: 50 },
                { period: 4, startHour: 12, startMin: 0, endHour: 12, endMin: 50 },
                { period: 5, startHour: 13, startMin: 0, endHour: 13, endMin: 50 },
                { period: 6, startHour: 14, startMin: 0, endHour: 14, endMin: 50 },
                { period: 7, startHour: 15, startMin: 0, endHour: 15, endMin: 50 },
                { period: 8, startHour: 16, startMin: 0, endHour: 16, endMin: 50 },
                { period: 9, startHour: 17, startMin: 0, endHour: 17, endMin: 50 }
            ];

            const currentTimeInMinutes = hours * 60 + minutes;

            for (let i = 0; i < periodData.length; i++) {
                const p = periodData[i];
                const periodStartTimeInMinutes = p.startHour * 60 + p.startMin;
                const periodEndTimeInMinutes = p.endHour * 60 + p.endMin;

                // 현재 시간이 수업 시간 내에 있을 경우
                if (currentTimeInMinutes >= periodStartTimeInMinutes && currentTimeInMinutes <= periodEndTimeInMinutes) {
                    const startTimeStr = `${String(p.startHour).padStart(2, '0')}:${String(p.startMin).padStart(2, '0')}`;
                    const endTimeStr = `${String(p.endHour).padStart(2, '0')}:${String(p.endMin).padStart(2, '0')}`;
                    return `${p.period}교시 (${startTimeStr}~${endTimeStr})`;
                }

                // 현재 시간이 쉬는 시간 내에 있을 경우 (다음 교시 시작 전)
                if (i < periodData.length - 1) {
                    const nextP = periodData[i + 1];
                    const breakStartTimeInMinutes = p.endHour * 60 + p.endMin; // 현재 교시 종료 시간
                    const breakEndTimeInMinutes = nextP.startHour * 60 + nextP.startMin; // 다음 교시 시작 시간

                    if (currentTimeInMinutes > breakStartTimeInMinutes && currentTimeInMinutes < breakEndTimeInMinutes) {
                        return "쉬는 시간";
                    }
                }
            }

            // 모든 교시 및 쉬는 시간 외의 경우
            const firstPeriodStartMin = periodData[0].startHour * 60 + periodData[0].startMin;
            const lastPeriodEndMin = periodData[periodData.length - 1].endHour * 60 + periodData[periodData.length - 1].endMin;

            if (currentTimeInMinutes < firstPeriodStartMin) {
                return "수업 전"; // 9시 이전
            } else if (currentTimeInMinutes > lastPeriodEndMin) {
                return "수업 종료"; // 17시 50분 이후
            }

            return ""; // 예외 처리 (도달하지 않아야 함)
        }


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

        function updateTime() {
            const now = new Date();
            const hours = now.getHours();
            const minutes = now.getMinutes();
            const seconds = String(now.getSeconds()).padStart(2, '0');

            const currentTimeDisplay = document.getElementById('currentTime');

            currentTimeDisplay.textContent = `${String(hours).padStart(2, '0')}:${String(minutes).padStart(2, '0')}:${seconds}`;

            // 현재 교시 정보 업데이트
            selectedPeriodDisplay.textContent = getPeriodByTime(hours, minutes);

            // 모든 메시지를 기본적으로 숨김
            examFiveMinLeftMessage.style.display = 'none';
            examEndMessage.style.display = 'none';
            currentTimeDisplay.classList.remove('red-text');

            // 시험 종료 메시지 및 시계 색상 로직 (수정)
            if (minutes >= 40 && minutes < 45) {
                // 40분 ~ 44분 사이: 시계만 빨간색
                currentTimeDisplay.classList.add('red-text');
            } else if (minutes >= 45 && minutes < 50) {
                // 45분 ~ 49분 사이: 시계 빨간색, "시험 종료 5분전" 메시지 표시
                currentTimeDisplay.classList.add('red-text');
                examFiveMinLeftMessage.style.display = 'block';
            } else if (minutes >= 50) {
                // 50분 이상: 시계 빨간색, "시험 종료" 메시지 표시
                currentTimeDisplay.classList.add('red-text');
                examEndMessage.style.display = 'block';
            }
        }

        // 1초마다 시간 업데이트
        setInterval(updateTime, 1000);

        // 페이지 로드 시 바로 시간 업데이트
        updateTime();
    </script>
</body>
</html>
