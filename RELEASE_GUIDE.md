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
5. 이 저장소의 [Releases](https://github.com/wjdtmdgns0905/NXRprojects-Release/releases)에서 **Draft a new release**를 엽니다. 위 형식의 새 태그를 `main`에서 만들고, 제목과 변경 내용을 입력합니다.
6. **Attach binaries** 영역에 3단계에서 만든 ZIP을 업로드합니다. 업로드 목록에 `TeamEdu2BackOfficeTools-portable.zip`이 완전히 표시되고 파일 크기가 0이 아닌지 확인한 후 **Publish release**를 누릅니다. 자산 업로드가 실패했다면 게시하지 않습니다.
7. 게시된 릴리스 페이지에서 태그, 제목, ZIP 자산을 확인합니다. 공개 자산을 다운로드해 로컬 ZIP과 SHA-256을 비교하고, 앱의 업데이트 검사 경로로 ZIP 구조 및 버전 인식을 확인합니다.

브라우저 자동화로 ZIP을 첨부할 때 Microsoft Edge의 ChatGPT 확장 프로그램이 로컬 파일 접근을 허용하지 않으면 업로드가 실패할 수 있습니다. 이 경우 `edge://extensions`에서 ChatGPT 확장 프로그램의 **Details → Allow access to file URLs** 설정을 사용자가 켠 뒤 다시 업로드하거나, 사용자가 GitHub 웹 화면에서 직접 ZIP을 선택해야 합니다. 이 권한은 ZIP 생성이나 Git 푸시에는 필요하지 않습니다.

## 기록 범위

기존 2.x 릴리스는 공개 자산이 게시된 사실과 해시가 소스 저장소의 `PROJECT_HANDOFF.md`에 기록되어 있습니다. 당시 ZIP을 GitHub에 올린 구체적인 도구(웹 화면 또는 API)는 저장소에 기록되어 있지 않으므로 이 문서는 앞으로 사용할 수 있는 재현 가능한 웹 게시 절차를 명시합니다.
