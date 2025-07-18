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
        .exam-subject {
            font-size: 3em; /* 시험 과목 글자 크기 */
            font-weight: bold;
            margin-bottom: 20px;
            color: #0056b3;
        }
        .current-time {
            font-size: 5em; /* 시간 글자 크기 */
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
    <div class="exam-subject" contenteditable="true" spellcheck="false">
        시험 과목 입력
    </div>
    <div class="current-time" id="currentTime">
        00:00:00
    </div>
    <div class="message" id="examEndMessage">
        시험 종료
    </div>

    <script>
        function updateTime() {
            const now = new Date();
            const hours = String(now.getHours()).padStart(2, '0');
            const minutes = String(now.getMinutes()).padStart(2, '0');
            const seconds = String(now.getSeconds()).padStart(2, '0');

            const currentTimeDisplay = document.getElementById('currentTime');
            const examEndMessage = document.getElementById('examEndMessage');

            currentTimeDisplay.textContent = `${hours}:${minutes}:${seconds}`;

            // 40분 ~ 50분 사이에는 시계 빨간색
            if (minutes >= 40 && minutes < 50) {
                currentTimeDisplay.classList.add('red-text');
                examEndMessage.style.display = 'none'; // 시험 종료 메시지 숨김
            }
            // 50분이 되면 '시험 종료' 메시지 표시, 시계 색상 유지
            else if (minutes >= 50) {
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
