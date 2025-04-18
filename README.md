 <!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <title>사주 궁합 계산기 (정밀)</title>
  <style>
    body { font-family: sans-serif; padding: 2rem; }
    label, input { display: block; margin-top: 0.5rem; }
    .person { margin-bottom: 2rem; }
    .result { margin-top: 2rem; padding: 1rem; border: 1px solid #ccc; border-radius: 8px; }
  </style>
</head>
<body>

  <h1>사주 궁합 계산기 (정밀)</h1>

  <div class="person">
    <h2>A의 생년월일시</h2>
    <label>년: <input type="number" id="yearA" placeholder="예: 1996"></label>
    <label>월: <input type="number" id="monthA" placeholder="1~12"></label>
    <label>일: <input type="number" id="dayA" placeholder="1~31"></label>
    <label>시 (0~23): <input type="number" id="hourA" placeholder="예: 14"></label>
  </div>

  <div class="person">
    <h2>B의 생년월일시</h2>
    <label>년: <input type="number" id="yearB" placeholder="예: 1991"></label>
    <label>월: <input type="number" id="monthB" placeholder="1~12"></label>
    <label>일: <input type="number" id="dayB" placeholder="1~31"></label>
    <label>시 (0~23): <input type="number" id="hourB" placeholder="예: 10"></label>
  </div>

  <button onclick="calculate()">궁합 보기</button>

  <div class="result" id="result"></div>

  <script>
    const stems = ["갑", "을", "병", "정", "무", "기", "경", "신", "임", "계"];
    const branches = ["자", "축", "인", "묘", "진", "사", "오", "미", "신", "유", "술", "해"];
    const stemToElement = {
      "갑": "목", "을": "목", "병": "화", "정": "화",
      "무": "토", "기": "토", "경": "금", "신": "금",
      "임": "수", "계": "수"
    };
    const branchToElement = {
      "자": "수", "축": "토", "인": "목", "묘": "목",
      "진": "토", "사": "화", "오": "화", "미": "토",
      "신": "금", "유": "금", "술": "토", "해": "수"
    };
    const elementCompatibility = {
      "목": { "화": 10, "금": -10, "수": 5 },
      "화": { "토": 10, "수": -10, "목": 5 },
      "토": { "금": 10, "목": -10, "화": 5 },
      "금": { "수": 10, "화": -10, "토": 5 },
      "수": { "목": 10, "토": -10, "금": 5 }
    };

    function getYearStemBranch(year) {
      const stem = stems[(year - 4) % 10];
      const branch = branches[(year - 4) % 12];
      return { stem, branch };
    }

    function getHourBranch(hour) {
      const hourBranches = [
        "자", "축", "축", "인", "인", "묘", "묘", "진", "진", "사", "사", "오",
        "오", "미", "미", "신", "신", "유", "유", "술", "술", "해", "해", "자"
      ];
      return hourBranches[hour];
    }

    function getElementsFromBirth(year, hour) {
      const y = getYearStemBranch(year);
      const h = getHourBranch(hour);
      return [
        stemToElement[y.stem],
        branchToElement[y.branch],
        branchToElement[h]
      ];
    }

    function getElementScore(elementsA, elementsB) {
      let score = 0;
      for (const a of elementsA) {
        for (const b of elementsB) {
          const compat = elementCompatibility[a]?.[b] ?? 0;
          score += compat;
        }
      }
      return Math.round((score / (elementsA.length * elementsB.length)) * 10 + 50);
    }

    function getMessage(score) {
      if (score >= 80) return "두 분은 서로를 잘 이해하고 보완해주는 환상의 궁합이에요.";
      if (score >= 60) return "조화롭게 어울릴 수 있지만, 작은 배려가 필요해요.";
      if (score >= 40) return "성향 차이가 있지만 노력하면 충분히 맞춰갈 수 있어요.";
      return "가치관이나 기질이 많이 다를 수 있어요. 신중한 접근이 필요해요.";
    }

    function calculate() {
      const yearA = parseInt(document.getElementById("yearA").value);
      const monthA = parseInt(document.getElementById("monthA").value);
      const dayA = parseInt(document.getElementById("dayA").value);
      const hourA = parseInt(document.getElementById("hourA").value);

      const yearB = parseInt(document.getElementById("yearB").value);
      const monthB = parseInt(document.getElementById("monthB").value);
      const dayB = parseInt(document.getElementById("dayB").value);
      const hourB = parseInt(document.getElementById("hourB").value);

      if (
        isNaN(yearA) || isNaN(monthA) || isNaN(dayA) || isNaN(hourA) ||
        isNaN(yearB) || isNaN(monthB) || isNaN(dayB) || isNaN(hourB)
      ) {
        document.getElementById("result").innerText = "모든 정보를 입력해주세요.";
        return;
      }

      const elementsA = getElementsFromBirth(yearA, hourA);
      const elementsB = getElementsFromBirth(yearB, hourB);

      const score = getElementScore(elementsA, elementsB);
      const message = getMessage(score);

      document.getElementById("result").innerHTML = `
        <strong>궁합 점수:</strong> ${score}점<br>
        <strong>결과:</strong> ${message}<br><br>
        <strong>A의 오행:</strong> ${elementsA.join(", ")}<br>
        <strong>B의 오행:</strong> ${elementsB.join(", ")}
      `;
    }
  </script>
</body>
</html>
