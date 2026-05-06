# 학생 점수 확인 페이지

생년월일 6자리만 입력하면 본인 점수를 확인할 수 있는 정적 웹페이지입니다.

## 보안 방식

- 학생 이름은 코드에 **전혀 포함되지 않습니다**
- 생년월일은 **SHA-256 해시값**으로만 저장 → 원본 생년월일 복구 불가
- 매칭 시: 학생 입력 → 브라우저에서 해시 → 해시 비교 → 점수 표시
- View Source로 코드를 봐도 누가 누구인지 알 수 없습니다

## GitHub Pages 배포 (5분 소요)

### 1단계: GitHub 저장소 만들기

1. [github.com](https://github.com) 로그인 (계정 없으면 무료 가입)
2. 우측 상단 `+` → **New repository** 클릭
3. Repository name: `score-check` (원하는 이름)
4. **Public** 선택 (Pages 무료 사용)
5. **Create repository** 클릭

### 2단계: 파일 업로드

1. 새로 만든 저장소 페이지에서 **uploading an existing file** 링크 클릭
   - 또는 **Add file → Upload files**
2. `index.html` 파일을 드래그 앤 드롭
3. 하단의 **Commit changes** 클릭

### 3단계: GitHub Pages 활성화

1. 저장소 상단 **Settings** 탭 클릭
2. 좌측 메뉴 **Pages** 클릭
3. **Source** 섹션에서:
   - Branch: `main` 선택
   - 폴더: `/ (root)` 선택
   - **Save** 클릭
4. 1~2분 후 페이지 상단에 URL 표시됨
   - 형식: `https://[사용자이름].github.io/score-check/`

### 4단계: 학생들에게 공유

생성된 URL을 카카오톡/문자/QR 코드로 공유하면 됩니다.

## 데이터 수정 방법

학생 명단이 바뀌거나 점수를 수정해야 할 때:

1. `index.html` 파일을 텍스트 에디터로 열기
2. `const SCORE_DB = {...}` 부분을 찾기
3. 새 데이터로 해시 생성 필요 → 아래 Python 스크립트 사용

```python
import hashlib, json

# 형식: ("YYMMDD", 점수)
data = [
    ("010302", 70),
    ("980121", 62.5),
    # ... 학생 데이터 입력
]

result = {}
for bd, score in data:
    h = hashlib.sha256(bd.encode()).hexdigest()
    result[h] = score

print(json.dumps(result, separators=(',', ':')))
```

출력된 JSON을 `index.html`의 `SCORE_DB` 값에 붙여넣고 저장한 후 GitHub에 다시 업로드하면 됩니다.

## 보안 한계 (참고)

- 누군가 마음먹고 가능한 모든 6자리 숫자(약 100만 개)를 brute-force 해싱하면 (생년월일 → 점수) 매칭 표는 만들 수 있습니다
- 하지만 **이름은 코드에 없으므로**, 누구의 점수인지는 알 수 없습니다
- 본인 점수만 확인하는 용도로는 충분히 안전합니다

## 학생 안내 문구 예시

> 📚 시험 점수 확인 페이지: [URL]
>
> 본인 생년월일 6자리(YYMMDD)를 입력하면 점수를 확인할 수 있어요.
> 예: 2001년 3월 2일생 → `010302` 입력
