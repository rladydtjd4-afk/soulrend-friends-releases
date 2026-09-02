# MCC 서버 기능 추가 패키지

**대상: MineColonies - Cobblemon Conquest 2.3.0** (마인크래프트 1.21.1 · NeoForge 21.1.218 · 코블몬 1.7.x)

이 폴더(`mods/`, `config/`)를 **서버 폴더에 그대로 덮어쓰기**하면 아래 3가지 기능이 추가된다.
모든 jar는 NeoForge 1.21.1 + 코블몬 1.7.0+ 호환을 jar 메타데이터로 확인했고,
의존 라이브러리(Architectury 13.0.8 / Cloth Config 15.0.140 / Kotlin for Forge 5.11.0)는 모드팩에 이미 포함된 버전으로 요구 조건을 전부 충족한다.

| 기능 | 모드 파일 | 클라이언트에도 필요? |
|---|---|---|
| 교배 | `Cobbreeding-neoforge-2.2.2.jar` | ✅ 필요 |
| 전설 키 아이템 소환 | `MythsAndLegends-neoforge-1.9.0.jar` + 데이터팩 2개 | ✅ 모드만 필요 (데이터팩은 서버만) |
| 서버 편의 명령어 | `CobblemonExtras-neoforge-1.5.0-mcc.jar` | ❌ 불필요 (서버 전용) |

> ⚠️ **클라이언트 배포**: `Cobbreeding`과 `MythsAndLegends` jar 2개는 친구들 모드팩(클라이언트) `mods/`에도 넣어야 접속된다.
>
> ℹ️ CobblemonExtras의 `-mcc` 접미사: 원본 1.5.0의 minecraft 의존성 선언이 `[1.21]`(정확히 1.21만)로 되어 있어
> 1.21.1에서 로드가 거부될 수 있는 문제를 발견, `[1.21,1.22)`로 수정한 패치본이다 (MIT 라이선스, 수정 허용). 그 외 변경 없음.

---

## 1. 설치 순서

1. **서버 백업** (월드 + mods + config)
2. `mods/` 안의 jar 3개 → 서버 `mods/` 폴더에 복사
3. `config/` 폴더 → 서버 `config/` 폴더에 병합
   - `config/cobbreeding/main.json` — 교배 설정 (권장값 적용됨)
   - `config/openloader/packs/MythsAndLegends-Datapack-v1.0.5.zip` — 전설 키 아이템 스폰 데이터팩 (원본 그대로, 수정 금지)
   - `config/openloader/packs/zz_MCC_MnL_Compat.zip` — **중복 방지 데이터팩** (아래 2번)
4. 클라이언트(친구들 모드팩)에 jar 2개(Cobbreeding, MythsAndLegends) 추가
5. 서버 시작 → 아래 3번의 "첫 실행 후 설정" 1가지 적용

## 2. Mega Showdown(ATM x MSD)과의 전설 스폰 중복 조정

모드팩 내장 `ATM x MSD [3.2.1]` 데이터팩이 전설 81종을 **자연 스폰**시키고 있다.
Myths and Legends를 그냥 얹으면 전설이 키 아이템 없이도 계속 나와서 의미가 없어진다.

그래서 `zz_MCC_MnL_Compat.zip`이 **양쪽에 겹치는 74종의 자연 스폰 파일을 비활성화**한다
(`data/legendary_spawns_atm/spawn_pool_world/*.json`을 `"enabled": false`로 덮어씀). 결과:

- **겹치는 74종** (아르세우스, 삼신조, 창세신 트리오, 레지 시리즈, 카푸 시리즈 등): **키 아이템으로만** 스폰
- **M&L 전용 7종** (뮤츠, 솔가레오, 루나아라, 게노세크트, 타입:널, 비리디온, 테라파고스): 키 아이템으로 스폰
- **나머지 98종** (패러독스, 울트라비스트 등): 기존대로 자연 스폰 유지

### 우선순위 확인 (설치 후 1회 필수)

데이터팩은 나중에 로드된 팩이 이긴다. 서버 접속 후:

```
/datapack list
```

목록에서 `zz_MCC_MnL_Compat`이 `ATM x MSD`보다 **뒤(오른쪽/아래)**에 있으면 정상.
만약 앞에 있다면 (팩 이름은 탭 자동완성으로 선택):

```
/datapack disable "open_loader/data/zz_MCC_MnL_Compat.zip"
/datapack enable "open_loader/data/zz_MCC_MnL_Compat.zip" last
```

### 동작 검증

1. `/checkspawn ultra-rare` — 키 아이템 없이 실행 → 전설이 후보에 안 뜨면 성공
2. 크리에이티브로 키 아이템 지급(예: `/give @s mythsandlegends:azure_flute`) 후
   `/mythsandlegends checkinventory <닉네임>` → `/checkspawn ultra-rare` → 해당 전설이 후보에 뜨면 성공
3. M&L 데이터팩은 pack_format 34(구버전 표기)라 로드 시 "호환되지 않는 팩" 경고가 뜰 수 있으나 **정상 동작** (제작자가 1.21.1용으로 배포한 버전)

## 3. 설정 상태

### 교배 — `config/cobbreeding/main.json` (적용됨)
- `eggHatchMultiplier: 0.5` — 알 부화 시간 절반 (에그 사이클 20 기준 약 **5분**)
- `maxNumberOfActivatedPasturePerPlayer: 3` — 1인당 교배 목장 3개 (서버 부하 방지)
- 나머지는 기본값. 모든 키는 2.2.2 jar 내부 문자열과 대조해 유효성 확인됨
- 알은 목장 블록에서 생성 → **인벤토리에 들고 있어야** 부화 진행

### 전설 소환 — Myths and Legends (기본값 사용, config 파일 미포함)
- 1.9.0에서 config 형식이 공식 문서와 달라져(모드 번호식 `item_consumption_mode` 등), 잘못된 파일을 넣지 않도록 **첫 실행 시 자동 생성되는 기본값**을 쓴다
- 기본값이 이미 서버에 적합함:
  - `item_consumption_mode = 1` — **일반 스폰 시 키 아이템 소모** (1아이템 = 1전설)
  - 인벤토리 검사 주기 2분 30초, 우클릭 강제 소환 꺼짐
- **첫 실행 후 1가지만 수정 권장**: `config/MythsAndLegends/config.toml`에서
  `isBroadcastEnabled = false` → `true` (전설 스폰 서버 알림 켜기).
  알림에 좌표 노출이 싫으면 같은 파일의 broadcast 설정에서 `Location`을 `false`로
- 키 아이템 드롭 확률은 첫 실행 시 생성되는 `config/MythsAndLegends/loot_tables_config.json`에서 상자 종류별 조정 가능

### 편의 명령어 — CobblemonExtras (기본값 사용)
- config는 서버 첫 실행 시 자동 생성됨
- 주요 명령어: `/pc`(어디서나 PC), `/pokesee`(파티 요약), `/poketrade <닉>`(1:1 교환), `/battle <닉>`(PVP),
  `/pokeivs`(전투 중 IV), `/pokeodds`(이로치 확률)
- ⚠️ 기본은 **OP(레벨 2) 전용**. 일반 유저에게 풀려면: 생성된 config의 `PermissionLevels`에서 명령어별 레벨을 0으로 낮추거나, FTB Ranks로 `cobblemonextras.command.*` 권한 부여
- `/pokeheal`은 이 모드에 없음 — 회복은 포켓센터(CobbleTowns) 힐러 머신 또는 OP용 `/healpokemon`

## 4. 검증 내역 (2026-09-02)

- [x] jar 3개 모두 `neoforge.mods.toml`에서 minecraft 1.21.1 / cobblemon ≥1.7.0 범위 확인
- [x] 의존 라이브러리 버전: Architectury 13.0.8 (M&L 요구 ≥13.0.8 충족), Cloth Config 15.0.140 (≥15), Kotlin for Forge 5.11.0 (≥5) — 모드팩 manifest 대조
- [x] CobblemonExtras minecraft 범위 `[1.21]` 문제 발견 → `-mcc` 패치본으로 해결
- [x] `cobbreeding/main.json`의 전체 키를 jar 내부 문자열과 대조 (문서에만 있고 2.2.2에 없는 `customColors` 제거)
- [x] M&L 문서의 config 키가 1.9.0 실제 키와 불일치함을 확인 → config 미동봉으로 전환
- [x] zz_MCC_MnL_Compat: 74개 전 파일 JSON 유효성 + ATM x MSD 실제 경로(`data/legendary_spawns_atm/...`)와 1:1 대조
- [x] 데이터팩 zip 2개 무결성 검사 통과
- [ ] 실서버 기동 테스트 — 로컬에서 불가, 서버에 올린 뒤 위 2번 검증 절차로 확인할 것

## 5. 문제 해결

- **config가 안 먹거나 로그에 오류가 날 때**: 해당 config를 지우고 서버 1회 실행 → 자동 생성 파일에 값만 옮기기
- **코블몬 데이터팩은 `/reload`가 안 됨** — 수정 후 반드시 서버 재시작
- **전설이 여전히 자연 스폰될 때**: 2번의 `/datapack list` 우선순위 확인
- **서버가 CobblemonExtras에서 안 뜰 때**: `-mcc` 패치본이 맞는지 확인 (원본 1.5.0은 1.21.1에서 거부될 수 있음)

## 6. 출처·버전·라이선스

| 파일 | 버전 | 출처 | 라이선스/재배포 |
|---|---|---|---|
| Cobbreeding | 2.2.2 (neoforge) | [Modrinth](https://modrinth.com/mod/cobbreeding) | MIT |
| CobblemonExtras | 1.5.0-mcc (패치본) | [Modrinth](https://modrinth.com/mod/cobblemonextras) | MIT (수정·재배포 허용) |
| Myths and Legends | 1.9.0 (neoforge) | [Modrinth](https://modrinth.com/mod/cobblemon-myths-and-legends-sidemod) | 커스텀(수익화 금지) — 비상업 친구 서버 사용 |
| M&L Datapack | 1.0.5 | [Modrinth](https://modrinth.com/datapack/myths-and-legends-datapack) | CC BY-NC-ND 4.0 — **원본 그대로** 동봉 (제작: D0ctorLeon) |
| zz_MCC_MnL_Compat | 1.0 | 자체 제작 | 자유 |

원본 재다운로드 (2026-09 기준 CDN 직링크):

```
https://cdn.modrinth.com/data/ItmVb4zY/versions/9bPk2DC3/Cobbreeding-neoforge-2.2.2.jar
https://cdn.modrinth.com/data/TXoSDUCh/versions/ZYtnELom/CobblemonExtras-neoforge-1.5.0.jar
https://cdn.modrinth.com/data/CaOWby9K/versions/IsIw5iAW/MythsAndLegends-neoforge-1.9.0.jar
https://cdn.modrinth.com/data/BUn2XhG1/versions/QGUUECu0/MythsAndLegends-Datapack-v1.0.5.zip
```
