# 코블몬 서버 기능 조사 — MineColonies · Cobblemon Conquest 모드팩용

> 기준: **MineColonies - Cobblemon Conquest 2.3.0** (마인크래프트 1.21.1, NeoForge 21.1.218, 모드 283개)
> 모드팩 zip의 manifest/modlist와 내장 데이터팩(Open Loader)을 직접 확인해서, **이미 팩에 들어있는 기능은 제외**하고 정리했다.

---

## 0. 이미 모드팩에 들어있어서 제외한 것들

| 기능 | 팩에 이미 있는 것 |
|---|---|
| GTS(거래소) | cobble-gts |
| 경제/화폐 | CobbleDollars |
| 트레이너/체육관 | Radical Cobblemon Trainers(+API, 텍스처팩, RCT Trainers+ 데이터팩) |
| 레이드 | Cobblemon Raid Dens |
| 배틀타워 | Cobblemon Battle Tower |
| 메가진화/Z기술/다이맥스 | Cobblemon: Mega Showdown (+MegaShowdownTweaks, MSDLT 등 조정 데이터팩) |
| 퀘스트 | FTB Quests + Cobblemon Quests Reloaded |
| 대량발생 | Cobblemon Mass Outbreaks + Revamped Outbreaks |
| 기술머신 | SimpleTMs |
| 필드 루트(볼) | Cobbleloots |
| 마을/포켓센터 구조물 | CobbleTowns 데이터팩 |
| 스폰 확장 | ATM x MSD 데이터팩 (스폰 172종 + 종 추가 219건) |
| 스타터킷/서버 관리 | Starter Kit, FTB Essentials/Ranks/Teams/Chunks, spark, Simple Voice Chat |
| 도감/QoL | Pokenav, Living Dex, Cobblepedia, Box Link, Capture XP, Pokerus, Spawn Notification, Berry Pouch 등 |

---

## 1. 팩에 **없는** 서버 기능 — 추가 후보

### 1-1. 교배 시스템 ⭐ 가장 큰 공백
- **Cobbreeding**: 코블몬 본체(1.7.x)에는 교배가 없어서 서버들은 이 사이드모드를 쓴다. 알(20 에그 사이클 기준)이 인벤토리 소지 약 10분 후 부화. 개체값 유전 등 설정 가능.
- 팩에 교배 관련 모드가 전혀 없으므로 서버라면 1순위 추가 후보.

### 1-2. 원더트레이드 (랜덤 교환)
- GTS는 이미 있지만 원더트레이드는 없음.
- **Cobblemon Wonder Trade** 또는 **zonary123 Cobble WonderTrade**. 서버 전용(server-only) 빌드가 있어 플레이어들이 클라이언트에 따로 설치하지 않아도 되는 구현도 있음.

### 1-3. 전설 포켓몬 키 아이템 소환
- **Myths and Legends** (사이드모드 + 필수 데이터팩): 조수의 방울·하늘피리 같은 **키 아이템을 들고 있으면 해당 전설/환상이 그 플레이어 주변에만 스폰**. 조건(바이옴·추가 아이템·파티 구성)은 데이터팩으로 정의. 키 아이템은 루트 상자 드롭(확률 설정 가능).
- 팩에는 없음. 단, Mega Showdown이 이미 전설 관련 콘텐츠를 다루므로 스폰 중복이 없게 데이터팩 조정 필요.

### 1-4. 편의 명령어
- **CobblemonExtras**: `/pokeheal`, `/pc`, `/ivs`, `/evs`, 배틀 신청 등 서버 필수 명령어 모음. 팩에 없음.

### 1-5. 운영자 배치형 체육관 NPC
- 팩의 RCT는 트레이너가 **자연 스폰**되는 방식. 서버에서 흔히 하는 "직접 지은 체육관에 관장 NPC 고정 배치 + 배틀 룰 지정"은 **Brecher's Trainers**가 담당. RCT와 병행 사용 가능.
- 참고: RCT 자체도 데이터팩으로 커스텀 트레이너/팀 추가를 지원하므로, 모드를 안 늘리고 RCT 데이터팩으로 서버 전용 관장을 만드는 방법도 있음.

### 1-6. 자작 데이터팩 (모드 추가 없이 가능한 것)
이 팩은 **Open Loader**를 쓰고 있어서 `config/openloader/packs/`에 zip만 넣으면 데이터팩이 적용된다 (CobbleTowns, ATM x MSD가 이미 이 방식). 직접 만들 수 있는 것:

- **커스텀 스폰** (`data/cobblemon/spawn_pool_world/`): 종별 스폰 바이옴·레벨·희귀도(bucket/weight)·시간대·달 위상·발밑 블록·구조물 조건 제어. 콜로니 주변 테마 스폰, 기간 한정 스폰 이벤트 등.
- **종 데이터 패치** (`species_addition`): 종족값·타입·기술·진화·드롭만 부분 수정. 예전에 만든 한계돌파식 밸런스 조정을 이 방식으로 데이터팩화 가능. (팩의 OA_Arsenal 데이터팩이 정확히 이 구조)
- **드롭/루트 테이블** 조정, **species_features**로 커스텀 폼·색 변형(리소스팩 병행).
- ⚠️ 코블몬 데이터팩은 `/reload`로 갱신 안 됨 — 월드 재접속/서버 재시작 필요.

### 1-7. 추가하지 않는 게 나은 것
- **Cobblemon Economy**: 화폐+상점+퀘스트 올인원이지만 CobbleDollars·FTB Quests와 역할이 겹침.
- **Cobblespawn / AllTheMons 계열 스폰팩**: ATM x MSD가 이미 스폰 확장을 담당 — 중복 스폰 위험.
- **Impactor**: 범용 경제 API인데 이 팩 화폐는 CobbleDollars로 일원화되어 있어 불필요.

---

## 2. 적용 순서 제안

1. **Cobbreeding + CobblemonExtras** — 모드 2개로 서버 필수 기능(교배·편의 명령어) 확보
2. **원더트레이드** — 멀티 콘텐츠 보강 (GTS와 세트)
3. **Myths and Legends** — 전설 사냥 엔드콘텐츠 (MSD와 스폰 조정 필요)
4. **자작 데이터팩** — openloader/packs에 서버 전용 스폰/밸런스 팩 추가 (모드 추가 없음, 리스크 최소)
5. (선택) **Brecher's Trainers** — 플레이어/운영자 체육관 운영 시

⚠️ 공통 주의: 팩이 **NeoForge 1.21.1**이므로 사이드모드는 NeoForge 1.21.1 빌드를 우선 선택. 팩에 Sinytra Connector(+Forgified Fabric API)가 있어 Fabric 전용 사이드모드도 구동될 수 있지만(cobble-gts가 그 예) 개별 테스트 필요.

---

## 출처

- [Cobblemon Wiki — Creating Custom Spawns](https://wiki.cobblemon.com/index.php/Tutorials/Creating_Custom_Spawns)
- [Cobblemon Wiki — Spawn Pool World](https://wiki.cobblemon.com/index.php/Spawn_Pool_World)
- [Cobblemon Wiki — Species](https://wiki.cobblemon.com/index.php/Species) / [Species Additions](https://wiki.cobblemon.com/index.php/Species_Additions)
- [MineColonies - Cobblemon Conquest (CurseForge)](https://www.curseforge.com/minecraft/modpacks/minecolonies-cobblemon-conquest) — 2.3.0 zip의 manifest.json/modlist.html/내장 데이터팩 직접 확인
- [Cobbreeding 관련 — PokeTools 교배 가이드](https://www.poketools.com/cobblemon-breeding)
- [Cobblemon Wonder Trade (Modrinth)](https://modrinth.com/mod/cobblemon-wonder-trade) / [zonary123 Cobble WonderTrade](https://modrinth.com/mod/zonary123-cobble-wondertrade)
- [Myths and Legends 사이드모드 (Modrinth)](https://modrinth.com/mod/cobblemon-myths-and-legends-sidemod) / [데이터팩](https://modrinth.com/datapack/myths-and-legends-datapack)
- [CobblemonExtras (Modrinth)](https://modrinth.com/mod/cobblemonextras)
- [Brecher's Trainers (CurseForge)](https://www.curseforge.com/minecraft/mc-mods/brechers-cobblemon-trainers)
- [Radical Cobblemon Trainers 공식 문서](https://srcmc.gitlab.io/rct/docs/0.13/gameplay/trainers/)
