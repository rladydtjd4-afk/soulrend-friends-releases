# MCC 서버 기능 추가 패키지

**대상: MineColonies - Cobblemon Conquest 2.3.0** (마인크래프트 1.21.1 · NeoForge 21.1.218 · 코블몬 1.7.x)

이 폴더(`mods/`, `config/`)를 **서버 폴더에 그대로 덮어쓰기**하면 아래 4가지 기능이 추가된다.
모든 jar는 NeoForge 1.21.1 + 코블몬 1.7.0+ 호환을 jar 메타데이터로 확인했다.

| 기능 | 모드 파일 | 설치 위치 | 클라이언트에도 필요? |
|---|---|---|---|
| 교배 | `Cobbreeding-neoforge-2.2.2.jar` | 서버+클라 | ✅ 필요 |
| 원더트레이드 | `cobblemon_wonder_trade-neoforge-1.2.2.jar` | 서버+클라 | ✅ 필요 (거래소 블록 GUI) |
| 전설 키 아이템 소환 | `MythsAndLegends-neoforge-1.9.0.jar` + 데이터팩 2개 | 서버+클라 | ✅ 모드만 필요 (데이터팩은 서버만) |
| 서버 편의 명령어 | `CobblemonExtras-neoforge-1.5.0.jar` | **서버만** | ❌ 불필요 |

> ⚠️ **클라이언트 배포**: CobblemonExtras를 뺀 3개 jar는 친구들 모드팩(클라이언트) `mods/` 폴더에도 넣어야 접속된다.
> 릴리즈 zip 만들 때 클라용/서버용을 구분할 것.

---

## 1. 설치 순서

1. **서버 백업** (월드 + mods + config)
2. `mods/` 안의 jar 4개 → 서버 `mods/` 폴더에 복사
3. `config/` 폴더 → 서버 `config/` 폴더에 병합 (덮어쓰기 OK, 아래 파일들이 들어감)
   - `config/cobbreeding/main.json` — 교배 설정
   - `config/cobblemon_wonder_trade.json` — 원더트레이드 설정
   - `config/MythsAndLegends/config.toml` — 전설 소환 설정
   - `config/openloader/packs/MythsAndLegends-Datapack-v1.0.5.zip` — 전설 키 아이템 스폰 데이터팩 (원본 그대로, 수정 금지)
   - `config/openloader/packs/zz_MCC_MnL_Compat.zip` — **중복 방지 데이터팩** (아래 2번 설명)
4. 클라이언트(친구들 모드팩)에 CobblemonExtras 제외 jar 3개 추가
5. 서버 시작

의존성(Architectury API, Cloth Config)은 모드팩에 이미 포함되어 있어 추가 설치 불필요.

## 2. Mega Showdown(ATM x MSD)과의 전설 스폰 중복 조정

모드팩 내장 `ATM x MSD [3.2.1]` 데이터팩이 전설 81종을 **자연 스폰**시키고 있다.
Myths and Legends를 그냥 얹으면 전설이 "키 아이템 없이도" 계속 나와서 의미가 없어진다.

그래서 `zz_MCC_MnL_Compat.zip`이 **양쪽에 겹치는 74종의 자연 스폰 파일을 비활성화**한다
(`data/legendary_spawns_atm/spawn_pool_world/*.json`을 `"enabled": false`로 덮어씀).
결과:

- **겹치는 74종** (아르세우스, 삼신조, 창세신 트리오, 레지 시리즈, 카푸 시리즈 등): 이제 **키 아이템으로만** 스폰
- **M&L 전용 7종** (뮤츠, 솔가레오, 루나아라, 게노세크트, 타입:널, 비리디온, 테라파고스): 키 아이템으로 스폰
- **ATM 전용** (패러독스 일부, 울트라비스트 일부 등 나머지 98종): 기존대로 자연 스폰 유지

### 우선순위 확인 (설치 후 1회 필수)

데이터팩은 나중에 로드된 팩이 이긴다. 서버 접속 후:

```
/datapack list
```

목록에서 `zz_MCC_MnL_Compat`이 `ATM x MSD`보다 **뒤(오른쪽/아래)**에 있으면 정상.
만약 앞에 있다면:

```
/datapack disable "open_loader/data/zz_MCC_MnL_Compat.zip"
/datapack enable "open_loader/data/zz_MCC_MnL_Compat.zip" last
```

(팩 이름은 `/datapack list`에 표시되는 실제 이름을 탭 자동완성으로 선택)

### 동작 검증

1. `/checkspawn ultra-rare` — 키 아이템 없이 실행 → 전설이 후보에 안 뜨면 성공
2. 크리에이티브로 키 아이템 지급 후 `/mythsandlegends checkinventory <닉네임>` → `/checkspawn ultra-rare` → 해당 전설이 후보에 뜨면 성공
3. 키 아이템 목록·조건: [M&L 위키](https://github.com/D0ctorLeon/mythsandlegends-datapack) 참고

## 3. 서버 권장 설정 (이미 적용됨)

### 교배 — `config/cobbreeding/main.json`
- `eggHatchMultiplier: 0.5` — 알 부화 시간 절반 (에그 사이클 20 기준 약 **5분**; 기본 10분)
- `maxNumberOfActivatedPasturePerPlayer: 3` — 1인당 교배 목장 3개 제한 (서버 부하 방지)
- 알은 목장 블록(Pasture)에서 생성 → 인벤토리에 들고 있어야 부화가 진행됨
- 숨겨진 특성 유전 확률 있음, 이로치 확률은 국제결혼(Masuda)식 부스트 적용

### 원더트레이드 — `config/cobblemon_wonder_trade.json`
- 쿨타임 10분, **레벨 매칭 켬**(±10) — 저렙 몬 넣고 고렙 몬 뽑는 악용 방지
- 전설/환상/UB/패러독스는 같은 티어끼리만 교환됨 (모드 자체 기능)
- 사용법: 원더트레이드 스테이션 블록 설치 → 우클릭 → 포켓몬 선택

### 전설 소환 — `config/MythsAndLegends/config.toml`
- **스폰 성공 시 키 아이템 소모** (`consume_items_on_successful_spawn = true`) — 1아이템 1전설
- 전설/환상 스폰 시 **서버 전체 알림** 켬 (좌표는 비공개 — 발견자 보호)
- 인벤토리 검사 주기 2분 30초 (기본값)

### 편의 명령어 — CobblemonExtras
- config는 서버 첫 실행 시 `config/cobblemonextras/`에 자동 생성됨 (기본값 사용)
- 주요 명령어: `/pc`(어디서나 PC), `/pokesee`(파티 요약), `/poketrade <닉>`(1:1 교환), `/battle <닉>`(PVP),
  `/pokeivs`(전투 중 IV 확인), `/pokeodds`(이로치 확률 확인)
- ⚠️ 기본적으로 **OP 권한(레벨 2)이 필요**하다. 일반 유저에게 풀려면 둘 중 하나:
  - 자동 생성된 config의 `PermissionLevels` 섹션에서 명령어별 요구 레벨을 0으로 낮추기 (간단, 권장)
  - FTB Ranks로 `cobblemonextras.command.*` 권한 부여
- `/pokeheal`은 이 모드에 없음 — 회복은 포켓센터(CobbleTowns) 힐러 머신 또는 OP용 `/healpokemon` 사용

## 4. 문제 해결

- **config가 안 먹거나 모드가 로그에 오류를 남길 때**: 해당 config 파일을 지우고 서버를 1회 실행해 자동 생성된 파일을 받은 뒤, 이 폴더의 값만 옮겨 적기 (모드 업데이트로 형식이 바뀌었을 수 있음)
- **코블몬 데이터팩은 `/reload`가 안 됨** — 데이터팩 수정 후엔 반드시 서버 재시작
- **전설이 여전히 자연 스폰될 때**: 위 2번의 `/datapack list` 우선순위 확인
- **원더트레이드 GUI가 안 열릴 때**: 클라이언트에도 jar가 설치됐는지 확인

## 5. 출처·버전·라이선스

| 파일 | 버전 | 출처 | 라이선스/재배포 |
|---|---|---|---|
| Cobbreeding | 2.2.2 (neoforge) | [Modrinth](https://modrinth.com/mod/cobbreeding) | MIT |
| CobblemonExtras | 1.5.0 (neoforge) | [Modrinth](https://modrinth.com/mod/cobblemonextras) | MIT |
| Myths and Legends | 1.9.0 (neoforge) | [Modrinth](https://modrinth.com/mod/cobblemon-myths-and-legends-sidemod) | 커스텀(수익화 금지) — 비상업 친구 서버 사용 |
| M&L Datapack | 1.0.5 | [Modrinth](https://modrinth.com/datapack/myths-and-legends-datapack) | CC BY-NC-ND 4.0 — **원본 그대로** 동봉 (제작: D0ctorLeon) |
| Cobblemon Wonder Trade | 1.2.2 (neoforge) | [Modrinth](https://modrinth.com/mod/cobblemon-wonder-trade) | ARR — 제작자(Raguto)가 모드팩 포함을 명시 허용 |
| zz_MCC_MnL_Compat | 1.0 | 자체 제작 | 자유 |

재다운로드가 필요하면 (2026-09 기준 CDN 직링크):

```
https://cdn.modrinth.com/data/ItmVb4zY/versions/9bPk2DC3/Cobbreeding-neoforge-2.2.2.jar
https://cdn.modrinth.com/data/TXoSDUCh/versions/ZYtnELom/CobblemonExtras-neoforge-1.5.0.jar
https://cdn.modrinth.com/data/CaOWby9K/versions/IsIw5iAW/MythsAndLegends-neoforge-1.9.0.jar
https://cdn.modrinth.com/data/bfndVmco/versions/fGFCcolu/cobblemon_wonder_trade-neoforge-1.2.2.jar
https://cdn.modrinth.com/data/BUn2XhG1/versions/QGUUECu0/MythsAndLegends-Datapack-v1.0.5.zip
```
