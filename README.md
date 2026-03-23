// ============================================================
// (https://script.google.com/macros/s/AKfycbyddrtLO1f5Pn3ANOBe8sp_fW91vZtnksv2eRrB2QmnHeqGEc1A4jxMEF33t1Qh4i8w/exec)
// ============================================================
const SHEET_URL = '여기에_복사한_웹앱_URL_붙여넣기';
let startTime = Date.now();

function sendToSheet(typeIdx, scores) {
  const typeNames = ['콘텐츠형', '판매형', '지식전달형', '모임형'];
  const elapsed = Math.round((Date.now() - startTime) / 1000);

  const payload = {
    timestamp : new Date().toLocaleString('ko-KR', {timeZone:'Asia/Seoul'}),
    resultType: typeNames[typeIdx],
    scores    : scores,
    elapsed   : elapsed
  };

  // fetch (비동기, 실패해도 결과 화면엔 영향 없음)
  fetch(SHEET_URL, {
    method : 'POST',
    mode   : 'no-cors',
    headers: { 'Content-Type': 'application/json' },
    body   : JSON.stringify(payload)
  }).catch(() => {}); // 에러 무시 — UX에 영향 없도록
}
function calcResult() {
  const sc = [0, 0, 0, 0];

  QUESTIONS.forEach((q, qi) => {
    const ans = answers[qi];
    if (ans === null) return;
    if (q.type === 'choice') {
      sc[q.options[ans].type] += 3;
    } else {
      q.types.forEach(t => { sc[t] += ans; });
    }
  });

  let maxIdx = 0;
  sc.forEach((v, i) => { if (v > sc[maxIdx]) maxIdx = i; });

  sendToSheet(maxIdx, sc); // (https://script.google.com/macros/s/AKfycbyddrtLO1f5Pn3ANOBe8sp_fW91vZtnksv2eRrB2QmnHeqGEc1A4jxMEF33t1Qh4i8w/exec)

  return maxIdx;
}
