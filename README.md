# DOCX 데이터 복원 및 추출기

손상되거나 깨진 MS Word(`.docx`) 파일, 혹은 잘못 저장된 텍스트 파일에서 **본문 텍스트**를 추출하고, 필요 시 최소 구조의 DOCX로 다시 저장하는 **브라우저 전용** 도구입니다.

> **상세 가이드:** [docs/DOCX_복구_가이드.md](docs/DOCX_복구_가이드.md) — DOCX 내부 구조, 깨짐 유형, 단계별 복구 절차, 수동 복구 방법

---

## 빠른 시작

1. `index.html`을 브라우저에서 엽니다.
2. 손상된 `.docx`(또는 `.zip`, XML이 보이는 `.txt`)를 드래그하거나 선택합니다.
3. **복원된 텍스트** 탭에서 내용을 확인합니다.
4. **텍스트 복사** 또는 **DOCX 다운로드**로 저장합니다.

```powershell
# (선택) 로컬 서버
cd c:\CusorApps\msword_restore
python -m http.server 8080
# http://localhost:8080
```

---

## DOCX 구조 요약

`.docx` = **ZIP** + XML. 본문은 항상 `word/document.xml`에 있습니다.

```
문서.docx (ZIP)
├── [Content_Types].xml
├── _rels/.rels
└── word/document.xml   ← <w:p> 단락, <w:t> 글자
```

| 태그 | 의미 |
|------|------|
| `w:p` | 단락 |
| `w:r` | 텍스트 런 |
| `w:t` | 실제 문자 |

---

## 복구 절차 (요약)

| 단계 | 내용 |
|------|------|
| 1 | 원본 **백업** 복사 |
| 2 | `index.html`에서 파일 업로드 |
| 3 | ZIP 정상 → 구조 탭에서 `word/document.xml` 확인 / ZIP 손상 → 강제 추출 모드 자동 전환 |
| 4 | **복원된 텍스트**로 본문 검증 |
| 5 | 복사 또는 **DOCX 다운로드** → Word에서 열어 편집 |

자세한 설명·한계·수동 복구: **[docs/DOCX_복구_가이드.md](docs/DOCX_복구_가이드.md)**

---

## 동작 방식

- **정상 DOCX:** JSZip으로 압축 해제 → `word/document.xml` 파싱 → `<w:p>` / `<w:t>`에서 텍스트 추출
- **손상 파일:** ZIP 파싱 실패 시 파일을 텍스트로 읽고 `<w:t>...</w:t>` 정규식으로 강제 추출
- **DOCX 생성:** 추출 텍스트로 `[Content_Types].xml`, `_rels/.rels`, `word/document.xml`만 포함한 최소 docx 생성

---

## 지원 형식

| 형식 | 지원 |
|------|------|
| `.docx` | ✅ |
| `.zip` (DOCX와 동일 구조) | ✅ |
| XML 노출 `.txt` | ✅ |
| `.doc` (구 Word 바이너리) | ❌ |

---

## 프로젝트 구조

```
msword_restore/
├── index.html              # 앱 전체 (UI + JSZip 복구 로직)
├── README.md               # 이 파일
└── docs/
    └── DOCX_복구_가이드.md  # 구조·절차·고급 복구 상세 문서
```

---

## 기술 스택

- HTML5, JavaScript (Vanilla)
- [Tailwind CSS](https://tailwindcss.com/) (CDN)
- [JSZip](https://stuk.github.io/jszip/) 3.10.1 (CDN)

서버 업로드 없이 **클라이언트에서만** 처리됩니다.

---

## 라이선스

저장소에 별도 라이선스 파일이 없으면, 사용·배포 전 저장소 소유자의 정책을 확인하세요.
