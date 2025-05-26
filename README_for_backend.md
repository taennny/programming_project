# Flask 일정관리 템플릿 - 확장 버전 설명서

## 주요 기능

1. **할 일 등록** (날짜 선택 후):

   - 제목, 내용, 마감시간(datetime-local) 입력 가능

2. **할 일 완료 체크**:

   - 완료/미완료 상태를 버튼으로 토글 가능

3. **시간 기반 알림**:
   - 마감 시간 1분 이내일 경우 JS 팝업 알림 출력

---

## 백엔드가 맡아야 할 기능 정리

| 기능                          | 설명                                                                 |
|-------------------------------|----------------------------------------------------------------------|
| 달력 렌더링                | `calendar` 모듈로 연/월별 달력 계산 후 2차원 리스트로 템플릿에 전달 |
| 할 일 추가 (`/add_todo`)   | form에서 전달된 `title`, `content`, `deadline`, `date`로 새로운 할 일을 추가 |
| 할 일 완료 토글 (`/toggle_done`) | form에서 받은 `date`와 `index`로 `done` 상태를 반전시킴 |
| 할 일 삭제 (`/delete_todo`) | form에서 받은 `date`와 `index`로 해당 할 일을 삭제함 |
| 선택 날짜 유지 리디렉션     | 완료 토글/삭제 후에도 `year`, `month`를 유지해 같은 달력 상태로 돌아감 |
| JSON 데이터 변환 및 전달   | `todos_json = json.dumps(todos)` → JavaScript에서 사용할 수 있도록 넘김 |

---

## 백엔드에서 넘겨야 할 변수

- `calendar`: 2차원 리스트 - 주 단위로 날짜 배열 (빈 날은 None)
- `todos`: 날짜별 할 일 목록 (예시 아래 참고)
- `year`: 현재 연도
- `month`: 현재 월
- `todos_json`: JavaScript에서 사용할 수 있도록 JSON 형식으로 인코딩된 todos

### 예시:

```python
todos = {
  "2025-05-25": [
    {
      "title": "팀플 발표준비",
      "content": "자료 정리 및 발표 연습",
      "deadline": "2025-05-25T16:30",
      "done": False
    }
  ]
}
todos_json = json.dumps(todos)  # Flask 템플릿에 넘길 때는 safe 필터 사용
```

---

## 구성

```
Frontend_todo/
├── templates/
│   └── index.html
├── README_for_backend.md
└── FRONTEND_BACKEND_FUNTIONALITY_GUIDE.md
```
