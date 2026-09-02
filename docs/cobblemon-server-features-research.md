# 코블몬 서버 기능 조사 — MineColonies · Cobblemon Conquest 모드팩용

포켓몬(코블몬) 서버들이 실제로 쓰는 기능을 두 축으로 정리했다:

1. **코블몬 순정 데이터팩만으로 되는 것** — 스폰, 종(種) 데이터, 드롭, 진화 등
2. **사이드모드(+전용 데이터팩)가 필요한 것** — 체육관/트레이너, 레이드, 경제/상점, GTS/원더트레이드, 교배, 전설 포켓몬 소환 등

---

## 1. 코블몬 자체 데이터팩 기능 (모드 추가 없이 가능)

### 1-1. 커스텀 스폰 (`spawn_pool_world`)

`data/<네임스페이스>/spawn_pool_world/` 아래 JSON 파일로 포켓몬별 자연 스폰을 완전히 제어할 수 있다.

주요 필드:

| 필드 | 역할 |
|---|---|
| `pokemon` | 종 이름 (예: `pikachu`, 패러독스는 `iron valiant`처럼 공백 유지) |
| `level` | 스폰 레벨 범위 |
| `bucket` | 희귀도 풀: `common` / `uncommon` / `rare` / `ultra-rare` |
| `weight` | 같은 희귀도 풀 안에서의 가중치 (대략 0.1~10) |
| `context` | 스폰 위치 유형: `grounded` / `submerged` / `surface` |
| `biomes` | 바이옴 ID 또는 태그 |
| `condition` / `anticondition` | 시간대, 달 위상, 발밑 블록, 구조물 등 세부 조건 / 금지 조건 |
| `presets` | 미리 정의된 스폰 조건 템플릿 |

- 조건 조합이 매우 정밀함 — 예: "새달(초승달) 밤 2:30~3:00, 바다 바이옴의 점토 블록 위에서만 스폰" 같은 것도 가능.
- `pack.mcmeta`의 filter 기능으로 **기본 모드 스폰을 통째로 무효화**하고 서버만의 스폰 테이블로 갈아엎을 수 있다 (지역 리메이크 서버들이 쓰는 방식).
- ⚠️ **`/reload`가 코블몬 데이터팩에는 안 먹힌다** — 월드 재접속/서버 재시작으로 갱신해야 함.

### 1-2. 종 데이터 수정 (`species` / `species_additions`)

- **species 파일**: 종족값(base stats), 타입, 특성, 배울 기술, 진화 조건, 드롭 테이블까지 종의 모든 데이터를 JSON으로 정의.
- **species_additions**: 기존 종 파일 전체를 덮어쓰지 않고 **바꾸고 싶은 항목만 패치**하는 방식. 다른 데이터팩과의 호환을 위해 기존 포켓몬을 수정할 때는 이쪽이 권장된다. (전에 만든 한계돌파류 밸런스 조정도 이 방식으로 데이터팩화 가능)
- **드롭**: species 파일의 `drops` 항목(`amount` + `entries`)으로 포켓몬 처치/포획 드롭 아이템을 지정.
- **species_features**: "Aspect"를 만들어 리전 폼·색 변형 같은 **커스텀 폼/비주얼 변형**을 추가 (리소스팩과 조합).
- 완전한 **커스텀 포켓몬 추가**도 데이터팩+리소스팩 조합으로 가능 (공식 튜토리얼 있음).

### 1-3. 스폰 확장 데이터팩 사례 (그대로 가져다 쓸 수 있음)

- **Cobblespawn**: 비전설 전 포켓몬을 자연 스폰시키는 데이터팩 (패러독스 포함).
- **AllTheMons**: 코블몬에 없는 포켓몬을 채워주는 대형 데이터팩. 스폰 JSON 작성 시 종 이름 참조용으로도 쓰임.

---

## 2. 서버들이 쓰는 사이드모드 + 전용 데이터팩

### 2-1. 트레이너 / 체육관 (진행도 시스템의 핵심)

- **Radical Cobblemon Trainers (RCT)**: 1500명 이상의 트레이너(라디컬 레드/언바운드/BDSP 기반)가 자연 스폰. 체육관 관장 전투와 진행도(관장은 재도전 횟수 3회) 포함. **데이터팩으로 커스텀 트레이너/팀을 추가**할 수 있어 서버 전용 관장을 만들 수 있음. 다운로드 850만+로 사실상 표준.
  - 부속: **Radical Gyms & Structures** (관장용 체육관 구조물 자연 생성), **Radical Trainers Structures** 데이터팩, **More Radical Trainers: SV** 같은 트레이너 추가 데이터팩.
- **Brecher's Trainers**: 서버 운영자가 배치형 상설 트레이너를 직접 만들어 두는 방식. 전투 룰/제한 지정 가능 — 플레이어가 지은 체육관에 관장 NPC를 세우는 용도로 적합.

### 2-2. 레이드

- **Cobblemon Raid Dens**: 레이드 둥지 추가. **데이터팩으로 커스텀 레이드 보스 제작/기존 보스 수정** 가능. CobbleDollars 연동으로 레이드 보상 화폐 지급.
- **Matyah's Overhauled Cobblemon Raids** 데이터팩: 레이드 보상 테이블 전면 리밸런스 사례 (티어별 화폐 보상 등).

### 2-3. 경제 / 상점

- **CobbleDollars**: 코블몬 서버 표준 화폐 모드. 상인 NPC(Cobblemerchant) 제공, 상점 목록은 설정/데이터팩으로 구성.
  - **CobbleDollar Shops** 데이터팩: 주민 상점을 Cobblemerchant로 교체해 서버 화폐를 일원화한 사례.
- **Cobblemon Economy**: NPC 상점 + 이중 화폐 + **퀘스트 NPC 진행 시스템**(포획/전투/레이드/배틀타워 보상, 순환 퀘스트 보드)까지 묶은 올인원 경제 모드. CobbleDollars·Impactor 브릿지 지원.
- **Impactor**: 범용 서버 경제 API (다른 모드들과 화폐 연동용).

### 2-4. 거래 (GTS / 원더트레이드)

- **Cobblemon GTS**: 포켓몬·아이템을 등록해 파는 글로벌 거래소. Fabric/NeoForge 모두 지원.
- **Cobblemon Wonder Trade** / **zonary123 Cobble WonderTrade**: 랜덤 포켓몬 교환. 서버 전용(server-only) 빌드도 있어 클라이언트 설치 없이 운영 가능한 것도 있음.

### 2-5. 교배

- **Cobbreeding**: 코블몬 본체(1.7.x 기준)에는 교배가 없어서 서버들은 이 사이드모드를 사용. 기본 설정에서 알(20 에그 사이클 기준)이 인벤토리 소지 약 10분 후 부화.

### 2-6. 전설 포켓몬 소환 (키 아이템 방식)

- **Myths and Legends** (사이드모드 + 필수 데이터팩): 조수의 방울, 하늘피리 같은 **키 아이템을 들고 있으면 해당 전설/환상 포켓몬이 그 플레이어 주변에만 스폰**. 조건(바이옴·추가 아이템·파티 구성)은 데이터팩으로 정의.
  - 키 아이템은 루트 상자에서 획득(확률·루트 테이블 설정 가능), 인벤토리 검사 주기는 config로 조정(기본 2분 30초).
  - v1.3 이후로는 키 아이템 스폰 조건을 쓰려면 전용 데이터팩이 필수.

### 2-7. 편의 명령어

- **CobblemonExtras**: `/pokeheal`, `/pc`, `/ivs`, `/evs`, 배틀 신청 등 서버 필수 편의 명령어 모음.

---

## 3. 이 모드팩에 적용할 때 제안

MineColonies + Cobblemon 조합이라는 특성을 살리면:

1. **1단계 (데이터팩만, 모드 추가 없음)** — 리스크 최소
   - `spawn_pool_world` 커스텀 스폰 팩: 콜로니 주변/특정 바이옴에 테마 스폰 배치, 희귀 스폰 이벤트(달 위상·시간 조건).
   - `species_additions`: 드롭 테이블 조정(서버 경제용 아이템), 한계돌파 밸런스의 데이터팩화.
2. **2단계 (핵심 사이드모드)** — 서버 느낌을 내는 최소 세트
   - RCT(+Gyms & Structures) 또는 Brecher's Trainers → 체육관/배지 진행도
   - CobbleDollars(+상점 데이터팩) → 경제
   - CobblemonExtras → 편의 명령어
3. **3단계 (멀티플레이 콘텐츠)**
   - Cobblemon GTS + Wonder Trade, Cobbreeding, Cobblemon Raid Dens, Myths and Legends

⚠️ 공통 주의: 모드팩의 마인크래프트 버전(1.20.1/1.21.1 여부)과 로더(Forge/NeoForge/Fabric), 코블몬 버전에 맞는 사이드모드 빌드를 골라야 함. 코블몬 데이터팩은 `/reload`로 갱신되지 않으므로 테스트 시 재접속 필요.

---

## 출처

- [Cobblemon Wiki — Creating Custom Spawns](https://wiki.cobblemon.com/index.php/Tutorials/Creating_Custom_Spawns)
- [Cobblemon Wiki — Spawn Pool World](https://wiki.cobblemon.com/index.php/Spawn_Pool_World)
- [Cobblemon Wiki — Species](https://wiki.cobblemon.com/index.php/Species)
- [Cobblemon Wiki — Species Additions](https://wiki.cobblemon.com/index.php/Species_Additions)
- [Cobblemon Wiki — Creating A Custom Pokemon](https://wiki.cobblemon.com/index.php/Tutorials/Creating_A_Custom_Pokemon)
- [Cobblespawn (CurseForge)](https://www.curseforge.com/minecraft/data-packs/cobblespawn)
- [Radical Cobblemon Trainers (Modrinth)](https://modrinth.com/mod/rctmod) / [공식 문서](https://srcmc.gitlab.io/rct/docs/0.13/gameplay/trainers/)
- [Radical Gyms & Structures (CurseForge)](https://www.curseforge.com/minecraft/mc-mods/radical-gyms-structures-cobblemon)
- [Brecher's Trainers (CurseForge)](https://www.curseforge.com/minecraft/mc-mods/brechers-cobblemon-trainers)
- [Cobblemon Raid Dens (Modrinth)](https://modrinth.com/mod/cobblemonraiddens)
- [Matyah's Raid Dens 데이터팩 (CurseForge)](https://www.curseforge.com/minecraft/data-packs/matyahs-configs-cobblemon-raid-dens-datapack)
- [Cobblemon Economy (Modrinth)](https://modrinth.com/mod/cobblemon-economy)
- [CobbleDollar Shops 데이터팩 (Modrinth)](https://modrinth.com/datapack/cobbledollar-shops)
- [Cobblemon GTS (Modrinth)](https://modrinth.com/mod/cobblemon-gts)
- [Cobblemon Wonder Trade (Modrinth)](https://modrinth.com/mod/cobblemon-wonder-trade)
- [Myths and Legends 사이드모드 (Modrinth)](https://modrinth.com/mod/cobblemon-myths-and-legends-sidemod) / [데이터팩](https://modrinth.com/datapack/myths-and-legends-datapack)
- [CobblemonExtras (Modrinth)](https://modrinth.com/mod/cobblemonextras)
- [Cobbreeding 관련 — PokeTools 교배 가이드](https://www.poketools.com/cobblemon-breeding)
