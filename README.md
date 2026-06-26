# 차량 제어 데이터셋 검수 시스템

차량 음성 제어 sLLM 학습용 데이터셋(`combined_dataset_ko_stt_2x`)을 여러 검수자가
온라인으로 검수·편집하기 위한 단일 페이지 도구입니다. 관리자: **KuriosKat**

브라우저만 있으면 동작하며 별도 서버가 필요 없습니다.

## 폴더 구조

```
.
├── index.html                         # 검수 도구 (이 파일을 GitHub Pages로 배포)
├── data/
│   └── combined_dataset_ko_stt_2x.jsonl   # 데이터셋 (4,736행)
├── reviews/
│   └── review.json                    # 검수 결과 누적 파일 (검수자들이 PR/커밋)
└── README.md
```

## 주요 기능

- **행 뷰어/편집기**: 발화 / ACTION(JSON) / SAY / intent / slots를 분리해 보고 수정
- **트리 시각화**: action 계층과 intent 계층을 탭으로 전환, 노드 클릭 시 해당 행 필터
- **class(인자) 추가**: target 등 인자를 행에 추가 (빠른 추가 버튼 포함)
- **AI 변환**: 검수자 본인 Claude API 키로 행을 일괄 변환·증강(선택 기능)
- **검수 시스템**: 검수자 닉네임 입력 → 행별 "검수 완료" 기록(누가·언제)
- **IVIS 검수모드**: 인포테인먼트(미디어·오디오·비디오) 1,039행만 추려 집중 검수
- **검수 결과 Export / 병합**: review.json으로 내보내고, 여러 검수자 결과를 병합

## 검수자 사용법

1. 배포된 페이지(아래 GitHub Pages 주소)를 연다.
2. 상단 REVIEW 바에 **본인 GitHub 닉네임**을 입력한다.
3. **⎇ GitHub** 버튼을 눌러 저장소(`KuriosKat/cardata-review`), 브랜치, 검수파일 경로,
   그리고 **본인 Personal Access Token**을 입력하고 "연결 확인"을 누른다.
4. **검수 시 자동 커밋**을 체크한다. (이제 검수할 때마다 저장소에 자동 반영됨)
5. **◈ IVIS 검수모드** 버튼을 눌러 인포테인먼트 행만 표시한다.
6. 각 행을 검토하고 상세 화면에서 **✓ 검수 완료**를 누른다 → 자동으로 커밋된다.

> 자동 커밋은 매번 원격 `reviews/review.json`을 읽어 다른 검수자 결과와 병합한 뒤 다시
> 커밋하므로, 여러 명이 동시에 검수해도 서로의 결과를 덮어쓰지 않는다.
> 자동 커밋을 쓰지 않으려면 대신 **⤴ 검수결과 Export**로 파일을 받아 PR로 올려도 된다.

### Personal Access Token 발급 (검수자 각자)

1. GitHub → Settings → Developer settings → **Fine-grained tokens** → Generate new token
2. **Repository access**: `KuriosKat/cardata-review`만 선택
3. **Permissions → Repository permissions → Contents: Read and write**
4. 생성된 토큰(`github_pat_...`)을 복사해 도구의 GitHub 패널에 붙여넣는다.
   - 토큰은 저장·전송되지 않고 그 브라우저 세션에만 보관된다.
   - 저장소에 쓰기 권한을 주려면 KuriosKat가 각 검수자를 **Collaborator**로 추가해야 한다
     (Settings → Collaborators). 또는 검수자가 fork 후 PR을 보내는 방식을 쓴다.

관리자는 자동 커밋으로 누적된 `reviews/review.json`을 그대로 쓰거나, 검수자가 보낸
개별 파일을 **⤓ 검수 불러오기/병합**으로 합칠 수 있다.

## GitHub에 올리는 방법 (KuriosKat 계정)

### A. 웹에서 올리기 (가장 간단)

1. https://github.com/new 에서 새 저장소를 만든다.
   - Owner: **KuriosKat**, Repository name: 예) `cardata-review`
   - Public 또는 Private 선택 (Private면 Pages는 유료 플랜 필요)
2. 저장소 페이지에서 **Add file → Upload files**를 누른다.
3. 이 폴더의 `index.html`, `data/`, `reviews/`, `README.md`를 그대로 드래그해 올린다.
4. **Commit changes**를 누른다.

### B. 명령줄로 올리기

```bash
cd deploy
git init
git add .
git commit -m "검수 시스템 초기 배포"
git branch -M main
git remote add origin https://github.com/KuriosKat/cardata-review.git
git push -u origin main
```

## GitHub Pages로 공개 배포

1. 저장소 → **Settings → Pages**로 이동한다.
2. **Source**를 `Deploy from a branch`로 두고, Branch를 `main` / 폴더를 `/ (root)`로 선택한다.
3. **Save**를 누르고 1~2분 기다린다.
4. 배포 주소가 표시된다:
   `https://KuriosKat.github.io/cardata-review/`
5. 이 주소를 검수자들에게 공유하면 각자 브라우저에서 검수할 수 있다.

> Private 저장소를 그대로 두면 데이터셋이 외부에 노출되지 않는다. 이 경우 Pages 공개
> 대신, 각 검수자가 `index.html`을 내려받아 로컬에서 더블클릭으로 열어 사용하고
> review 파일만 PR로 공유하는 방식을 쓴다.

## 안내

- 이 도구는 데이터를 브라우저 안에서만 다루며, 편집·검수 결과는 사용자가 Export(또는 PR)
  하기 전까지 외부로 전송되지 않는다.
- AI 변환 기능을 쓸 경우 검수자 본인의 Claude API 키가 필요하며, 키는 해당 브라우저
  세션에만 보관되고 저장·전송되지 않는다. 공개 저장소에 키가 포함된 파일을 올리지 않는다.
- 액션 정의/라벨 스펙(예: 열선 액션명 통일, 도어 target 구조)은 실제 ClusterApp 동작과
  직결되므로, 확정 전에는 IVIS와 합의된 기준을 따른다.
