# CLAUDE.md

이 파일은 Claude Code(claude.ai/code)가 이 저장소에서 작업할 때 참고하는 가이드입니다.

## 이 프로젝트는

이연진 ♥ 김나현의 싱글 페이지 한국어 모바일 청첩장입니다 (결혼식 날짜 2026-11-07). GitHub Pages가 저장소 루트를 https://nhkim-sudo.github.io/ 로 서빙하는 정적 사이트입니다. **빌드 단계, 프레임워크, 패키지 매니저, 테스트가 전혀 없습니다** — 모든 것이 [index.html](index.html) 하나에 들어 있고(HTML + 인라인 `<style>` + 인라인 `<script>`), 정적 미디어는 [assets/](assets/) 아래에 있습니다.

## 작업 흐름

- [index.html](index.html)을 직접 **수정**합니다. 모든 CSS는 하나의 `<style>` 블록(약 17행부터 시작, `:root` 디자인 토큰은 18행)에, 모든 JS는 하단의 하나의 `<script>` 블록(약 1225행부터)에 있습니다.
- **로컬 미리보기**는 저장소 루트에서 아래 명령을 실행한 뒤 http://localhost:8000/ 을 엽니다:
  ```bash
  python -m http.server 8000
  ```
- **배포**는 자동입니다: `main`에 푸시하면 GitHub Pages가 루트를 게시합니다. 커밋이 곧 배포입니다 — 별도의 릴리스 과정이 없습니다.
- [.claude/settings.local.json](.claude/settings.local.json)의 권한 허용 목록에 `git add` / `git commit` / `git push`가 사전 승인되어 있습니다.
- 이 저장소의 커밋 메시지는 한국어로 작성하며, 사용자에게 보이는 변경 사항을 설명합니다 (`git log` 참고).

## 레이아웃 / 섹션 (index.html 위에서 아래 순서)

헤더(이름/날짜) → 메인 이미지 → 초대 문구 → WEDDING DAY(하드코딩된 달력 + 실시간 D-day 카운터) → 오시는 길(지도) → GALLERY → 마음 전하실 곳(계좌 정보) → 공유 버튼. 각 섹션은 스크롤 시 페이드인되는 `scroll-reveal` 클래스를 사용합니다.

## 수정 시 함께 맞춰야 하는 것들

날짜, 예식장, 이름, 좌표가 마크업, 메타 태그, JS에 **중복**되어 있습니다. 이 중 하나라도 바꿀 때는 모든 사본을 함께 업데이트하세요:

- **결혼식 날짜/시간**: `og:description` 메타(약 8행), 헤더 날짜(약 869행), `calendar-title`(약 902행), 하드코딩된 11월 달력 그리드(약 917행 이후, 강조된 날짜 셀), 그리고 `updateDDay()` 안의 목표 날짜(약 1394행). D-day 카운터는 이 날짜까지 카운트다운하며, 당일에는 "오늘은 결혼식입니다" 메시지로 전환됩니다.
- **예식장 + 좌표**: `openTmap()`(`placeName`, `latitude`, `longitude`, 약 1289행)과 함께 오시는 길 섹션(약 1005행 이후)의 Google Maps 임베드 iframe 및 Tmap/카카오/네이버/구글 지도 링크 버튼도 수정해야 합니다. 그 섹션의 대중교통 안내는 자유 텍스트입니다.
- **계좌 정보**: 각 `copyAccount('...')` onclick 인자가 옆에 표시된 계좌번호와 일치해야 합니다 (신랑측/신부측, 각각 본인/아버지/어머니).

## 연동 기능 및 주의사항

- **카카오 공유**: Kakao JS SDK(CDN, `<head>`에서 로드)를 사용합니다. `KAKAO_API_KEY`는 인라인으로 초기화되는 JS 키입니다(약 1274행) — 설계상 공개 클라이언트 키이며, 카카오가 등록된 도메인으로 제한하므로 배포된 GitHub Pages 오리진에서만 동작하고 `localhost`나 `file://`로 열면 동작하지 않습니다.
- **배경 음악**: `<audio id="bgm-audio">`가 로드 시 자동 재생됩니다. 브라우저가 자동 재생을 차단하므로, 첫 사용자 클릭 시 재생을 시작하는 폴백이 있습니다. 소스는 `assets/audio/` 아래의 `.m4a` 파일입니다.
- **갤러리**: 이미지는 [assets/gallery/](assets/gallery/) 아래에 있습니다. 미리보기는 일부만 보여주고 "+ 더보기" 버튼(`expandGallery()`)이 있으며, 사진을 클릭하면 이전/다음 탐색이 가능한 전체 화면 모달이 열립니다(`openGalleryModal` / `changeGalleryPhoto`).
- 레이아웃은 모바일 우선이며 `max-width: 480px`(`.container`)로 제한됩니다. 휴대폰 화면 너비에서 테스트하세요.

## 참고

- [files/청첩장_커스터마이징_가이드.md](files/청첩장_커스터마이징_가이드.md)는 웹 개발자가 아닌 최종 사용자를 위해 작성된 커스터마이징 가이드입니다. 현재 파일보다 먼저 작성되어 `wedding-invitation.html`이라는 파일명과 자리표시자 콘텐츠를 언급하는데, 이는 더 이상 `index.html`과 일치하지 않습니다 — 배경 자료로만 참고하고 코드의 정확한 지도로 취급하지 마세요.
- `.crossnote/`는 VS Code Markdown Preview Enhanced 확장이 생성한 것으로, 사이트와 무관합니다.
