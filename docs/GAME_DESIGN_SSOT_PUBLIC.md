# Slime — Public Game Design SSOT

> 채용 포트폴리오용 공개 기획 정본입니다. 현재 README에 공개되어 있던 **Design SSOT v3.64 기반 설계 지도**를 상위 기획 문서로 분리해, 플레이어 경험·성장 구조·핵심 의사결정·현재 구현 범위를 한 번에 읽을 수 있도록 정리했습니다. 세부 내부 수치와 작업 로그, 비공개 구현 자료는 포함하지 않습니다.

## 1. Game Identity

**슬라임은 먹고싶어**는 직접 이동하는 2D 액션 전투 위에 포식·변이·연구·Form·환생을 얹은 **Action Incremental RPG**입니다.

게임의 중심은 숫자만 커지는 방치형 성장보다:

> **전투 결과가 포식으로 이어지고, 포식이 이번 Run의 선택과 다음 Run의 영구 성장으로 연결되는 구조**

를 만드는 데 있습니다.

## 2. Player Fantasy

플레이어가 느끼길 원하는 핵심 경험은 다음과 같습니다.

- 적을 처치하고 실제로 **먹는 행동**으로 보상을 완성하는 슬라임의 정체성
- 한 Run 안에서 변이 카드를 선택해 전투 방식을 즉시 바꾸는 빌드 변화
- Run 밖에서 Research와 Form을 준비해 다음 도전을 설계하는 장기 성장
- 같은 Form도 Mastery 전문화에 따라 다른 전투 리듬으로 완성하는 선택
- Rank가 오를수록 Elite·Boss·대량 Spawn이 겹치며 높아지는 핵앤슬래시 밀도

## 3. Core Loop

```text
직접 이동 + 자동 기본 공격
→ 적 처치
→ 시체 포식
→ Feeding Level Up
→ 변이 카드 선택
→ 현재 Run의 전투 방식 변화
→ Gate Rank 돌파 또는 실패
→ 보상 정산
→ Research / Form / 영구 성장
→ 다음 Run 출정
```

장기적으로는:

```text
Gate R1 → R5 → Core
→ 첫 Prestige
→ 인간계
→ Form Mastery / Relic / World 성장
→ 더 높은 난도와 빌드 검증
```

으로 확장하는 방향을 갖습니다.

## 4. Core Design Pillars

### 4.1 Combat → Devour → Mutation

적의 HP가 0이 되었다고 즉시 보상을 지급하지 않습니다.

```text
적 처치
→ 시체 생성
→ 슬라임에게 끌려감
→ 포식 완료
→ Gold / Essence / Feeding 보상 확정
```

이 구조는 처치를 단순 삭제가 아니라 **슬라임이 먹는 행동**으로 마무리하기 위한 것입니다.

### 4.2 Run Mutation — 짧은 선택이 전투를 바꾼다

포식으로 Feeding Level에 도달하면 변이 카드 제안이 생성되고 고정됩니다.

```text
Feeding Level Up
→ 카드 제안 생성
→ 전투 일시 정지
→ 1개 선택
→ 이번 Run의 전투 방식에 반영
```

창을 닫았다 다시 열어 무료로 reroll하는 방식은 허용하지 않습니다. 별도의 Reroll 자원을 사용해야 다시 선택할 수 있습니다.

현재 공개 설계에서 Mutation은 공격력·공격 속도·최대 HP·이동·포식 범위 등 실제 조작과 전투 리듬에 영향을 주는 축으로 구성됩니다.

### 4.3 Growth Layers — 성장 시간을 분리한다

성장을 하나의 공격력 수치로 합치지 않고 플레이 시간과 선택의 성격이 다른 층으로 분리합니다.

| 성장 층 | 플레이어 행동 | 설계 목적 |
| --- | --- | --- |
| Run Mutation | 포식 후 카드 선택 | 현재 Run의 전투 방식을 즉시 변경 |
| Common Research | Gate 보상으로 연구 | 모든 Form이 공유하는 기반 성장 |
| Form / Form Aux | Form 선택과 전용 보조기관 | 전투 판타지와 약점 변경 |
| Form Mastery | Form 귀속 자원으로 전문화 | 같은 Form 안에서 장기 빌드 결정 |
| Prestige / Relic / World | 장기 진행 재구축 | 반복 도전과 후반 목표 제공 |

### 4.4 Form — 스킨이 아니라 전투 패키지

Form은 색상만 다른 스킨이 아니라 **전투 언어와 약점을 바꾸는 패키지**입니다.

한 Run에서는 하나의 Form을 고정하고, Run 중 상황마다 교체해 약점을 지우는 플레이는 허용하지 않습니다.

현재 공개 Production 범위:

- **BLUE** — 포식과 난전 Momentum
- **RED** — 직접 공격과 공격적 압박
- **GREEN** — 생존력을 공격 자원으로 전환

장기 설계 범위에는 GOLD, PINK, CRYSTAL, BLACK, PURPLE이 있으며, 일부는 아직 Runtime 미구현 또는 DESIGN PENDING 상태입니다.

### 4.5 Form Mastery — 하나의 Form도 한 방향으로만 끝나지 않는다

첫 Prestige 이후 Form별 Mastery는 A/B/C 중 하나의 전문화를 선택하는 구조를 목표로 합니다.

전문화는 작은 수치 차이보다 다음 다섯 축에서 충분히 달라야 합니다.

- **Driver** — 무엇이 공격을 발생시키는가
- **Geometry** — 어떤 위치와 범위를 공격하는가
- **Resource** — 무엇을 모으고 소비하는가
- **Rhythm** — 지속·Burst·반격·모드 전환 중 어떤 박자인가
- **Specialty** — 어떤 Encounter에서 강한가

목표는 모든 장점을 동시에 갖는 만능 Form을 피하고, 실제로 플레이 방식이 달라지는 선택을 만드는 것입니다.

### 4.6 Gate Rank — 목표를 단계적으로 읽게 한다

모든 새 Run은 R1부터 시작합니다.

```text
R1 / R2
→ 단일 Gate

R3 / R4
→ Gate 1 파괴
→ 반대 위치 Gate 2 등장
→ Gate 2 파괴

R5
→ Boss 구간
```

R3·R4의 Twin Gate는 동시에 두 목표를 보여주는 것이 아니라 순차적으로 등장시켜, 현재 집중해야 할 목표와 압박 전환이 명확하게 읽히도록 설계합니다.

## 5. Key Design Decisions

### 왜 숫자만 커지는 방치형을 피하는가

영구 성장만 강하면 매 Run이 같은 행동을 반복하는 자동 진행으로 수렴하기 쉽습니다. 그래서 Run Mutation, Form, Mastery, Research가 서로 다른 시간축에서 다른 선택을 만들도록 분리합니다.

### 왜 처치와 보상 지급을 분리하는가

적 삭제 순간에 바로 Gold와 XP를 지급하면 슬라임이라는 캐릭터의 포식 행동이 장식이 됩니다. 보상 확정을 포식 완료에 두어 **전투 결과와 캐릭터 행동을 같은 사건으로 연결**했습니다.

### 왜 Form 전문화를 하나만 선택하는가

서로 상충하는 엔진을 한 번에 모두 가져가면 Form 선택과 빌드 선택의 의미가 약해집니다. Mastery Lv1에서 하나의 전문화를 확정하고 이후에는 그 전문화 내부에서 진화하도록 설계합니다.

### 왜 Mid-Run Resume를 억지로 지원하지 않는가

전투 중 HP·투사체·시체·Feeding·Mutation 상태를 불완전하게 복원하는 대신, **이미 확정된 영구 보상은 안전하게 보존하고 진행 중 Run은 복원하지 않는 경계**를 택했습니다.

확정 보상은 TransactionId를 가진 Reward Journal로 중복 지급을 방지합니다.

## 6. Current Implementation Scope

### Runtime / Public Evidence

현재 공개 README의 실제 Play Mode GIF에서 다음을 확인할 수 있습니다.

- 플레이어 이동과 기본 공격
- 적 접근·공격·피격
- 적 처치와 시체 흡수
- 포식 진행
- Mutation 카드 선택
- 카드 적용 후 전투 복귀
- R1 Gate Expedition HUD와 전투 흐름

### Implemented / Validation Record

공개 저장소는 전체 내부 테스트를 배포하지 않지만, 다음 구현·검증 기록을 공개 범위로 둡니다.

- 전투 기반: 플레이어·적·투사체·피해·처치 루프
- 플레이어 전투 표현: 기본 공격·피격 반응·시체 흡수
- 일반 적 프레젠테이션
- 전투 판정과 시각 연출의 책임 분리
- 확정 보상 / Save 경계

### In Progress

- World / Map Background와 전투 공간의 시각 정체성
- HUD 가독성·장식·타이포그래피
- 장기 Form / Mastery의 실제 Runtime 확장
- Public Playable용 WebGL 마감

### Planned / Not Claimed as Complete

- 잠긴 Form 전체의 Production 구현
- 장기 Prestige / Relic / World 콘텐츠
- 최종 Boss / Final Raid
- 약 10~20분 분량의 WebGL Public Playable

구현되지 않은 항목은 설계 목표와 현재 구현 범위를 구분해 표시합니다.

## 7. Public Evidence

- README의 **Combat Preview** — 실제 R1 전투 GIF
- README의 **포식 → 변이 카드 선택** — 실제 Play Mode 흐름 GIF
- 이 문서 — 상위 시스템 기획 의도와 성장 구조
- 추후 itch.io — WebGL Public Playable

---

이 문서는 **공개용 상위 게임 기획 기준**입니다. 내부 Design SSOT 전체, 상세 밸런스 수치, 비공개 구현 자료와 작업 로그는 공개하지 않습니다.
