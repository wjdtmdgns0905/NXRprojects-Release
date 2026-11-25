# 교육2팀 백오피스 툴 릴리스 절차

이 저장소의 GitHub Releases는 앱의 자동 업데이트 배포처입니다. 앱 소스는 [NXR-TeamEdu2-BackOfficeTools](https://github.com/wjdtmdgns0905/NXR-TeamEdu2-BackOfficeTools)에 있습니다. 이 저장소의 `main` 브랜치에 소스나 ZIP을 커밋하는 것만으로는 앱 업데이트가 배포되지 않습니다.

## 버전과 파일 규칙

- 새 기능을 추가하면 첫째 자리, 큰 수정을 하면 둘째 자리, 작은 수정이나 패치를 하면 마지막 자리를 올립니다. 기존 버전 번호로 다시 릴리스하지 않습니다.
- 릴리스 태그: `backoffice2-v<version>` (예: `backoffice2-v3.0.1`)
- 릴리스 자산 이름: `TeamEdu2BackOfficeTools-portable.zip`
- 일반 릴리스로 게시하고 최신 릴리스로 지정합니다. 소스 코드 자동 ZIP은 앱 업데이트 파일이 아닙니다.

## 게시 순서

1. 소스 저장소에서 `version.json`의 `version`을 올리고, `repository`가 `wjdtmdgns0905/NXRprojects-Release`, `tagPrefix`가 `backoffice2-v`인지 확인합니다.
2. 소스 저장소의 변경사항을 검사합니다. 코드 변경 시 `node smoke.js`와 변경된 JavaScript 파일의 Node 구문 검사를 실행합니다. 해당 기능의 모의 검사도 실행합니다.
3. 앱을 종료한 뒤 소스 저장소 루트에서 `powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\package.ps1 -Release -Rebuild`를 실행합니다. 생성된 ZIP은 같은 루트의 `TeamEdu2BackOfficeTools-portable.zip`입니다. 출력된 SHA-256을 기록합니다. 이 명령은 ZIP을 만들고 업데이트 ZIP 구조를 검사하지만 GitHub에 게시하지는 않습니다.
4. 소스 변경사항을 커밋하고 소스 저장소 `main`에 푸시합니다.
5. 이 저장소의 [Releases](https://github.com/wjdtmdgns0905/NXRprojects-Release/releases)에서 **Draft a new release**를 열거나 GitHub Releases API로 초안을 만듭니다. 위 형식의 새 태그를 `main`에서 만들고, 제목과 변경 내용을 입력합니다.
6. 아래의 웹 화면 또는 API 방법으로 3단계의 ZIP을 첨부합니다. 자산 이름, 크기, 업로드 완료 상태를 확인한 후에만 릴리스를 게시합니다.
7. 게시된 릴리스 페이지에서 태그, 제목, ZIP 자산을 확인합니다. 공개 자산을 다운로드해 로컬 ZIP과 SHA-256을 비교하고, 앱의 업데이트 검사 경로로 ZIP 구조 및 버전 인식을 확인합니다.

### 웹 화면에서 첨부

릴리스 초안의 **Attach binaries** 영역에서 ZIP을 선택합니다. 업로드가 완료되고 자산 이름과 크기가 표시되면 **Publish release**를 누릅니다. 브라우저 자동화에서 로컬 파일 접근이 차단된다면 아래 API 방법을 사용할 수 있습니다. 권한 설정을 임의로 변경하지 않습니다.

### GitHub API로 첨부·게시 (3.0.1에서 검증)

3.0.1은 웹에서 만든 초안에 대해 아래 경로로 ZIP을 업로드하고 게시했습니다. Git Credential Manager에 저장된 `wjdtmdgns0905` 계정의 자격 증명을 `git credential fill`에 `protocol=https`, `host=github.com`, `username=wjdtmdgns0905`를 전달해 메모리에서만 받았습니다. 자격 증명을 파일·명령 출력·릴리스 설명에 기록하지 않습니다.

1. 인증 헤더 `Authorization: Bearer <저장된 자격 증명>` 및 `Accept: application/vnd.github+json`으로 `GET https://api.github.com/repos/wjdtmdgns0905/NXRprojects-Release/releases?per_page=100`을 호출해 정확한 태그의 초안 ID와 자산 목록을 찾습니다. 초안이 없으면 같은 `/releases`에 `tag_name`, `target_commitish: main`, `name`, `body`, `draft: true`, `prerelease: false`를 JSON으로 `POST`합니다.
2. 초안의 `upload_url`에서 `{?name,label}` 부분을 제거하고 `?name=TeamEdu2BackOfficeTools-portable.zip`을 붙입니다. `Content-Type: application/zip`으로 로컬 ZIP을 `POST`합니다. PowerShell에서는 `Invoke-RestMethod -Method Post -InFile <ZIP 경로> -ContentType 'application/zip'`을 사용합니다. 반환된 자산의 `state`가 `uploaded`이고 이름과 크기가 맞는지 확인합니다.
3. `PATCH https://api.github.com/repos/wjdtmdgns0905/NXRprojects-Release/releases/<초안 ID>`에 `{"draft":false,"prerelease":false,"make_latest":"true"}`를 보냅니다. 반환값의 `draft: false`, 태그, 자산 수를 확인합니다.
4. 공개 다운로드 URL에서 ZIP을 받아 생성 시 기록한 SHA-256과 비교합니다. 3.0.1의 공개 ZIP은 34,963,949바이트, SHA-256 `6b8cee3b54ee9956adb0ff10e0b4b24ae7a6685d862667f6416882986ff6debf`로 확인했고 `src/update.js`의 ZIP 구조 검사도 통과했습니다.

## 기록 범위

기존 2.x 릴리스는 공개 자산이 게시된 사실과 해시가 소스 저장소의 `PROJECT_HANDOFF.md`에 기록되어 있습니다. 당시 ZIP을 GitHub에 올린 구체적인 도구(웹 화면 또는 API)는 저장소에 기록되어 있지 않습니다. 위 API 방법은 3.0.1에서 실제로 사용하고 검증한 절차입니다.
