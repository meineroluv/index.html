# index.html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Clean Coast AI | 제주 연안 해안쓰레기 예측 및 인력 배치 시스템</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      font-family: 'Malgun Gothic', 'Apple SD Gothic Neo', sans-serif;
      background-color: #f0f6fa;
      color: #1e293b;
      line-height: 1.6;
      padding: 24px 16px;
    }
    .container { max-width: 920px; margin: 0 auto; }
    header {
      background: linear-gradient(135deg, #0077b6, #00b4d8);
      color: #ffffff;
      padding: 28px 24px;
      border-radius: 14px;
      margin-bottom: 20px;
      box-shadow: 0 4px 12px rgba(0, 119, 182, 0.18);
    }
    header h1 { font-size: 1.65rem; margin-bottom: 6px; }
    header p { font-size: 0.95rem; opacity: 0.92; }

    .card {
      background: #ffffff;
      border-radius: 12px;
      padding: 22px;
      margin-bottom: 20px;
      box-shadow: 0 2px 8px rgba(0, 0, 0, 0.06);
    }
    .card h2 {
      font-size: 1.15rem;
      color: #0077b6;
      margin-bottom: 14px;
      border-left: 4px solid #0077b6;
      padding-left: 10px;
    }

    .form-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(190px, 1fr));
      gap: 14px;
      margin-bottom: 16px;
    }
    label { display: block; font-size: 0.88rem; font-weight: bold; margin-bottom: 6px; color: #334155; }
    select, input {
      width: 100%;
      padding: 10px 12px;
      border: 1px solid #cbd5e1;
      border-radius: 8px;
      font-size: 0.95rem;
    }
    input[readonly] { background-color: #f8fafc; color: #64748b; }

    button {
      width: 100%;
      padding: 12px;
      background-color: #0077b6;
      color: #ffffff;
      border: none;
      border-radius: 8px;
      font-size: 1rem;
      font-weight: bold;
      cursor: pointer;
    }
    button:hover { background-color: #023e8a; }

    /* 결과 요약 박스 4개 */
    .result-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
      gap: 12px;
      margin-top: 10px;
    }
    .stat-box {
      background: #f8fafc;
      border: 1px solid #e2e8f0;
      border-radius: 10px;
      padding: 14px;
      text-align: center;
    }
    .stat-box.highlight {
      background: #e0f2fe;
      border-color: #38bdf8;
    }
    .stat-label { font-size: 0.84rem; color: #475569; margin-bottom: 4px; }
    .stat-value { font-size: 1.35rem; font-weight: bold; color: #0f172a; }
    .stat-sub { font-size: 0.78rem; color: #0369a1; margin-top: 4px; }

    /* 비교 테이블 및 막대 그래프 */
    table { width: 100%; border-collapse: collapse; margin-top: 10px; font-size: 0.9rem; }
    th, td { padding: 10px 8px; border-bottom: 1px solid #e2e8f0; text-align: center; }
    th { background-color: #f1f5f9; color: #334155; font-weight: bold; }
    .bar-bg { background: #e2e8f0; border-radius: 6px; height: 14px; width: 100%; overflow: hidden; }
    .bar-fill { background: #0096c7; height: 100%; border-radius: 6px; }
    .badge {
      display: inline-block;
      padding: 2px 8px;
      border-radius: 12px;
      font-size: 0.78rem;
      font-weight: bold;
    }
    .badge-high { background: #fee2e2; color: #b91c1c; }
    .badge-mid { background: #fef3c7; color: #b45309; }
    .badge-low { background: #dcfce7; color: #15803d; }

    .formula-box {
      background: #f8fafc;
      border-left: 4px solid #64748b;
      padding: 12px 14px;
      font-size: 0.86rem;
      color: #334155;
      margin-top: 10px;
    }
  </style>
</head>
<body>
  <div class="container">
    <header>
      <h1>🌊 Clean Coast AI</h1>
      <p>제주시 주요 5개 해수욕장 다중 선형 회귀 기반 해안쓰레기 예측 및 적정 인력 배치 시스템</p>
    </header>

    <!-- 1. 입력 영역 -->
    <section class="card">
      <h2>1. 해수욕장 및 환경 데이터 입력</h2>
      <div class="form-grid">
        <div>
          <label for="beachSelect">해수욕장 선택 (제주시 5개소)</label>
          <select id="beachSelect" onchange="loadBeachPreset()">
            <option value="곽지">곽지해수욕장 (애월읍)</option>
            <option value="금능">금능해수욕장 (한림읍)</option>
            <option value="김녕">김녕해수욕장 (구좌읍)</option>
            <option value="삼양">삼양해수욕장 (삼양동)</option>
            <option value="이호테우" selected>이호테우해수욕장 (이호동)</option>
          </select>
        </div>
        <div>
          <label for="areaInput">백사장 실측면적 (㎡)</label>
          <input type="number" id="areaInput" readonly />
        </div>
        <div>
          <label for="visitorInput">예상 이용객 수 (명)</label>
          <input type="number" id="visitorInput" min="0" step="100" />
        </div>
        <div>
          <label for="windInput">평균 풍속 (m/s)</label>
          <input type="number" id="windInput" min="0" max="20" step="0.1" />
        </div>
      </div>
      <button type="button" onclick="analyzeSingleBeach()">예측 쓰레기 수거량 및 적정 인력 계산하기</button>
    </section>

    <!-- 2. 개별 해수욕장 예측 결과 -->
    <section class="card">
      <h2>2. AI 예측 및 파생 속성 분석 결과 (<span id="selectedTitle">이호테우</span>)</h2>
      <div class="result-grid">
        <div class="stat-box highlight">
          <div class="stat-label">예측 쓰레기 수거량</div>
          <div class="stat-value" id="predWaste">- kg</div>
          <div class="stat-sub">다중 선형 회귀 산출</div>
        </div>
        <div class="stat-box highlight">
          <div class="stat-label">추천 배치인원 (가공 3)</div>
          <div class="stat-value" id="recStaff">- 명</div>
          <div class="stat-sub">1인 한계 300kg 기준 올림</div>
        </div>
        <div class="stat-box">
          <div class="stat-label">방문객 밀도 (가공 1)</div>
          <div class="stat-value" id="visitorDensity">- 명/㎡</div>
          <div class="stat-sub" id="densityNote">-</div>
        </div>
        <div class="stat-box">
          <div class="stat-label">기상 가중치 (가공 2)</div>
          <div class="stat-value" id="weatherWeight">-</div>
          <div class="stat-sub" id="windNote">-</div>
        </div>
      </div>
      <div class="formula-box">
        <strong>적용 회귀식 (결정계수 R² = 0.92):</strong><br />
        예측 수거량(kg) = (0.01110 × 이용객 수) + (0.00255 × 실측면적) + (206.6133 × 평균풍속) - 581.80
      </div>
    </section>

    <!-- 3. 2025년 5개 해수욕장 전체 우선순위 비교 대시보드 -->
    <section class="card">
      <h2>3. 제주시 5개 해수욕장 청소 우선순위 및 인력 배분 현황 (2025년 기준)</h2>
      <table>
        <thead>
          <tr>
            <th>우선순위</th>
            <th>해수욕장</th>
            <th>실측면적(㎡)</th>
            <th>방문객 밀도</th>
            <th>기상 가중치</th>
            <th>예측 수거량(kg)</th>
            <th style="width: 18%;">발생량 비교</th>
            <th>추천 인원</th>
          </tr>
        </thead>
        <tbody id="summaryTableBody"></tbody>
      </table>
    </section>
  </div>

  <script>
    // 1. 연구자가 직접 수집한 제주시 5개 해수욕장 기초 데이터 (2025년 기준)
    const beachData = {
      "곽지": { area: 27931, visitors: 66880, wind: 3.2 },
      "금능": { area: 309008, visitors: 110343, wind: 3.1 },
      "김녕": { area: 89138, visitors: 36026, wind: 4.5 },
      "삼양": { area: 25161, visitors: 67960, wind: 2.8 },
      "이호테우": { area: 29186, visitors: 87087, wind: 2.8 }
    };

    // 2. 다중 선형 회귀식 및 파생 속성 계산 함수
    function predictMetrics(visitors, area, wind) {
      // 회귀식 계산 (음수 방지 처리)
      const rawWaste = (0.01110 * visitors) + (0.00255 * area) + (206.6133 * wind) - 581.80;
      const waste = Math.max(0, rawWaste);

      // 가공 속성 1: 방문객 밀도 (명/㎡)
      const density = visitors / area;

      // 가공 속성 2: 기상 가중치 (풍속 기준 3단계)
      let weatherIdx = 1.0;
      if (wind >= 4.5) weatherIdx = 1.4;
      else if (wind >= 3.0) weatherIdx = 1.2;

      // 가공 속성 3: 추천 배치인원 (1인당 300kg 한계 기준 올림 처리)
      const staff = waste > 0 ? Math.ceil(waste / 300) : 0;

      return { waste, density, weatherIdx, staff };
    }

    // 3. 해수욕장 선택 시 해당 면적과 2025년 기본값 자동 입력
    function loadBeachPreset() {
      const name = document.getElementById("beachSelect").value;
      const info = beachData[name];
      document.getElementById("areaInput").value = info.area;
      document.getElementById("visitorInput").value = info.visitors;
      document.getElementById("windInput").value = info.wind;
      analyzeSingleBeach();
    }

    // 4. 단일 해수욕장 분석 결과 화면 업데이트
    function analyzeSingleBeach() {
      const name = document.getElementById("beachSelect").value;
      const area = Number(document.getElementById("areaInput").value);
      const visitors = Number(document.getElementById("visitorInput").value);
      const wind = Number(document.getElementById("windInput").value);

      const res = predictMetrics(visitors, area, wind);

      document.getElementById("selectedTitle").textContent = name + "해수욕장";
      document.getElementById("predWaste").textContent = res.waste.toFixed(1) + " kg";
      document.getElementById("recStaff").textContent = res.staff + " 명";
      document.getElementById("visitorDensity").textContent = res.density.toFixed(2) + " 명/㎡";
      document.getElementById("densityNote").textContent = res.density >= 2.0 ? "고밀집 주의 구역" : "일반 밀집 구역";
      document.getElementById("weatherWeight").textContent = res.weatherIdx.toFixed(1);
      document.getElementById("windNote").textContent = wind >= 4.5 ? "강풍 유입 주의" : (wind >= 3.0 ? "보통 풍속" : "약풍");
    }

    // 5. 5개 해수욕장 전체 우선순위 비교표 생성
    function renderComparisonTable() {
      const list = Object.keys(beachData).map(name => {
        const b = beachData[name];
        const m = predictMetrics(b.visitors, b.area, b.wind);
        return { name, area: b.area, ...m };
      });

      // 예측 수거량이 많은 순서대로 정렬
      list.sort((a, b) => b.waste - a.waste);
      const maxWaste = list[0].waste;

      const tbody = document.getElementById("summaryTableBody");
      tbody.innerHTML = "";

      list.forEach((item, index) => {
        const ratio = Math.round((item.waste / maxWaste) * 100);
        const badgeClass = index === 0 ? "badge-high" : (index <= 2 ? "badge-mid" : "badge-low");

        const row = `
          <tr>
            <td><span class="badge ${badgeClass}">${index + 1}순위</span></td>
            <td><strong>${item.name}</strong></td>
            <td>${item.area.toLocaleString()}</td>
            <td>${item.density.toFixed(2)} 명/㎡</td>
            <td>${item.weatherIdx.toFixed(1)}</td>
            <td><strong>${item.waste.toFixed(1)} kg</strong></td>
            <td>
              <div class="bar-bg"><div class="bar-fill" style="width: ${ratio}%;"></div></div>
            </td>
            <td><strong>${item.staff}명</strong></td>
          </tr>
        `;
        tbody.innerHTML += row;
      });
    }

    // 페이지 열릴 때 초기 실행
    loadBeachPreset();
    renderComparisonTable();
  </script>
</body>
</html>
