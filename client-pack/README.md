# 클라이언트용 파일 (친구들 배포)

## 1. 사이드모드 jar 2개 (필수)
서버팩의 `server-pack/mods/`에서 아래 2개를 각자 모드팩 `mods/` 폴더에 추가:
- `Cobbreeding-neoforge-2.2.2.jar`
- `MythsAndLegends-neoforge-1.9.0.jar`

(`CobblemonExtras-neoforge-1.5.0-mcc.jar`는 서버 전용 — 클라이언트에 넣지 말 것)

## 2. 사이드모드 한글패치 (선택, 권장)
`MCC-사이드모드-한글패치-v1.0.zip`을 `resourcepacks/` 폴더에 넣고
게임 설정 → 리소스 팩에서 활성화.

번역 범위:
- **Myths and Legends**: 키 아이템 85종 이름·설명(공식 한글 명칭), 전설 스폰 알림, 합체/지가르데 UI, 명령어 메시지 — 전체 254줄
- **Cobbreeding**: 포켓몬 알, 목장 교배 버튼/메시지, 설정 화면 — 전체 395줄

`src-*.json`은 번역 원본(수정용). 수정 후 zip 안의 `assets/<모드ID>/lang/ko_kr.json`을 교체하면 된다.
