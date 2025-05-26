# TO-DO 프론트엔드 기능 설명 및 백엔드 연동 지점 요약

이 문서는 현재 HTML + JS 기반으로 구현된 프론트엔드에서 제공하는 주요 기능과  
Flask 백엔드가 연동해야 하는 지점을 요약한 자료입니다.

---

## 프론트엔드 주요 기능 설명

| 기능               | 설명                                                                  |
| ------------------ | --------------------------------------------------------------------- |
| 달력 렌더링        | 월/연도 선택 시 해당 월의 달력이 표시됨 (`calendar` 변수 기반)        |
| 날짜 선택          | 사용자가 날짜를 클릭하면 선택된 날짜가 강조되고, 해당 할 일 목록 표시 |
| 할 일 추가         | 제목, 내용, 마감시간을 입력하고 추가 → `/add_todo`로 POST 요청 전송   |
| 완료 체크 토글     | 체크박스를 클릭하면 `/toggle_done`로 POST 전송하여 완료 상태 토글     |
| 할 일 삭제         | 삭제 버튼 클릭 시 `/delete_todo`로 POST 요청                          |
| 할 일 개수 표시    | 각 날짜 셀에는 미완료 할 일의 개수만 표시됨                           |
| 마감시간 형식 정리 | `T`가 포함된 ISO 시간 → 사용자에겐 `YYYY-MM-DD HH:mm` 형식으로 표시   |

---

## 백엔드 연동 지점 요약

### `render_template("index.html", ...)` 시 전달 변수

```python
render_template(
  "index.html",
  calendar=calendar_rows,           # 2차원 리스트: 날짜들 주 단위로 나눔
  todos=todos,                      # 날짜별 할 일 딕셔너리
  todos_json=json.dumps(todos),     # JS에서 사용할 JSON 문자열 (할 일 목록)
  year=selected_year,
  month=selected_month
)
```

---

## POST 요청 처리 라우트

### `/add_todo`

- form 데이터: `title`, `content`, `deadline`, `date`
- 처리: 해당 날짜의 리스트에 새 할 일 추가

---

### `/toggle_done`

- form 데이터: `date`, `index`
- 처리: 해당 날짜의 인덱스의 `done` 상태 반전
- redirect: 변경된 `date`의 연/월 유지
  ```python
  year, month = date.split('-')[:2]
  return redirect(f"/?year={year}&month={int(month)}")
  ```

---

### `/delete_todo`

- form 데이터: `date`, `index`
- 처리: 해당 날짜 리스트에서 해당 인덱스 제거
- redirect 동일

---

## 기타 UX 관련 사항

- 날짜 선택 상태 유지 및 강조 (`.selected-day`)
- JS로 할 일 목록 우측 동적 표시 (날짜 클릭 시 갱신)
- 완료 체크 시에도 선택 날짜 유지 (`redirect`에 연도/월 포함)
- 삭제/추가 모두 서버 처리 후 전체 페이지 리렌더링 (전통적인 방식)
