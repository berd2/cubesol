# 3D Rubik's Cube Simulator & Solver

A fully interactive, browser-based 3D Rubik's Cube simulator with a built-in dual-solver engine. Implemented as a single standalone `index.html` file using Three.js and TWEEN.js — no installation or local server required.

완전한 인터랙티브 브라우저 기반 3D 루빅스 큐브 시뮬레이터로, 듀얼 솔버 엔진을 내장하고 있습니다. Three.js와 TWEEN.js를 사용한 단일 `index.html` 파일로 구현되어 있으며, 별도 설치나 로컬 서버 없이 실행됩니다.

---

## Key Features / 주요 기능

- **Dual-View Rendering**: Two simultaneous camera perspectives show all 6 faces at once (Left: Blue/Orange/Yellow side, Right: White/Red/Green side).
  두 개의 고정 카메라 시점을 동시에 제공해 큐브의 모든 6면을 한눈에 확인할 수 있습니다.

- **Intuitive Mouse Controls**:
  직관적인 마우스 조작:
  - **Layer Rotation**: Drag any cubie to smoothly rotate its layer. Corner cubies allow two-axis movement; edge cubies are axis-restricted to prevent mis-rotations.
    드래그로 개별 레이어를 부드럽게 회전합니다. 코너 조각은 양방향, 엣지 조각은 오조작 방지를 위해 축 제한 회전이 적용됩니다.
  - **Camera Rotation**: Drag the empty background to independently adjust each viewport's angle.
    빈 배경을 드래그해 각 시점의 카메라 각도를 독립적으로 변경합니다.

- **Dual Solver Engine**: Two separate solvers handle different goals — Kociemba for minimal moves, LBL for human-readable step-by-step guidance.
  두 가지 솔버를 탑재합니다. Kociemba는 이동수 최소화, LBL은 사람이 따라하기 쉬운 단계별 힌트에 특화됩니다.

- **Real-time Progress Display**: During automated solving, the current move index is shown live (`Move N/Total`).
  자동 솔빙 실행 시 좌상단 UI에 현재 진행 단계를 실시간으로 표시합니다 (`Move N/전체`).

- **Save Result**: Exports the scramble sequence and solution moves as a compact JSON file.
  스크램블 시퀀스와 솔루션 이동 목록을 간결한 JSON 파일로 내보냅니다.

---

## How to Use / 사용 방법

1. **Run**: Open `index.html` in any modern browser (Chrome, Edge, Safari, etc.) — just double-click.
   **실행**: Chrome, Edge, Safari 등 최신 브라우저에서 `index.html`을 더블 클릭해 실행합니다.

2. **Buttons**:
   **버튼 설명**:

   | Button | Description |
   |--------|-------------|
   | **Scramble** | Randomly scrambles the cube with 20 moves / 무작위 20수로 큐브를 섞습니다 |
   | **Undo** | Placeholder (solver mode: use step buttons instead) / 솔버 모드에서는 스텝 버튼으로 진행 |
   | **Default View** | Resets both camera angles to the default positions / 카메라 각도를 기본값으로 리셋 |
   | **Save Result** | Downloads the last Solve All result as a JSON file / 마지막 Solve All 결과를 JSON으로 저장 |
   | **LBL — 2-Step** | Executes the next 2 moves of the LBL solution (human-readable hint) / LBL 해법의 다음 2수를 실행 (직관적 힌트) |
   | **LBL — Solve All** | Runs the full LBL solution (~80–130 moves) / LBL 전체 솔루션 실행 (~80–130수) |
   | **Kociemba — 1-Step** | Executes the next 1 move of the Kociemba optimal solution / Kociemba 최적해의 다음 1수를 실행 |
   | **Kociemba — Solve All** | Runs the full optimal solution (~18–22 moves) / 최적 솔루션 전체 실행 (~18–22수) |

3. **Help (?)**: Click the `?` button (bottom-left) for a detailed drag-control guide.
   화면 좌측 하단의 `?` 버튼을 클릭해 드래그 조작 가이드를 확인합니다.

---

## Solver Architecture / 솔버 구조

Two solvers coexist, each suited to a different use case.
두 가지 솔버가 공존하며, 각각 다른 목적에 최적화되어 있습니다.

### 1. Two-Phase Solver (Kociemba-style) — Solve All / 1-Step

The primary solver for **Solve All** and **1-Step** (Kociemba). Produces near-optimal solutions averaging **~20 moves** — roughly 4–6× fewer than LBL.

**Solve All** 및 **1-Step (Kociemba)** 에 사용되는 주 솔버입니다. 평균 **약 20수**의 거의 최적에 가까운 해를 생성하며, LBL 대비 약 4–6배 적은 이동수를 달성합니다.

**How it works:**
1. **Phase 1 — Reduce to subgroup G1** `<U, D, R², L², F², B²>`: Uses IDA\* search with precomputed pruning tables for corner orientation, edge orientation, and UD-slice position. Terminates once all three coordinates reach their solved state.
2. **Phase 2 — Solve within G1**: Searches for the shortest sequence of G1-legal moves (`U`, `D`, `R²`, `L²`, `F²`, `B²`) that solves corner permutation, UD-edge permutation, and slice-edge permutation simultaneously.
3. **Post-processing**: Adjacent-move cancellation and commutator merging trim any remaining redundancy.

**동작 원리:**
1. **1단계 — 부분군 G1으로 환원** `<U, D, R², L², F², B²>`: 코너 오리엔테이션, 엣지 오리엔테이션, UD-슬라이스 위치에 대한 사전 계산된 가지치기 테이블로 IDA\* 탐색을 수행합니다.
2. **2단계 — G1 내부 풀이**: G1 허용 수(`U`, `D`, `R²`, `L²`, `F²`, `B²`)만으로 코너/엣지 퍼뮤테이션을 동시에 해결합니다.
3. **후처리**: 인접 수 상쇄 및 교환 가능 수 병합으로 잔여 중복을 제거합니다.

**Performance** (verified over 30 random scrambles, 0 failures):

| Metric | Value |
|--------|-------|
| Average moves | ~20.5 |
| Range | 18–22 |
| Solve time (after first call) | ~0.8 s |
| First-call overhead (table build) | ~2 s |

**성능** (무작위 스크램블 30회 검증, 실패 0):

| 항목 | 값 |
|---|---|
| 평균 이동수 | ~20.5수 |
| 범위 | 18–22수 |
| 풀이 시간 (첫 호출 이후) | ~0.8초 |
| 첫 호출 오버헤드 (테이블 생성) | ~2초 |

---

### 2. LBL Solver (Layer-by-Layer) — Solve All / 2-Step

Used for **2-Step (LBL)** and **Solve All (LBL)**. Produces human-readable move sequences that follow the classic beginner's method, making it suitable for learning. Average ~80–130 moves.

**2-Step (LBL)** 및 **Solve All (LBL)** 에 사용됩니다. 초보자 방식의 직관적 공식 순서를 따르므로 학습 목적에 적합합니다. 평균 약 80–130수.

**Stages:**
1. **White Cross** — Place the 4 white edges on the top face.
   흰색 엣지 4개를 윗면에 배치합니다.
2. **White Corners** — Insert the 4 white corners into position.
   흰색 코너 4개를 삽입합니다.
3. **Second Layer** — Solve the 4 middle-layer edges.
   중간층 엣지 4개를 해결합니다.
4. **Yellow Cross (EOLL)** — Orient the bottom edges to form a yellow cross.
   아랫면 엣지를 오리엔테이션해 노란 십자를 만듭니다.
5. **Yellow Corners Orientation (OCLL)** — Orient all 4 bottom corners using Sune/Anti-Sune.
   Sune/Anti-Sune 공식으로 아랫면 코너를 오리엔테이션합니다.
6. **Yellow Corners Permutation (CPLL)** — Permute corners with T-perm/Y-perm.
   T-perm/Y-perm으로 코너 퍼뮤테이션을 해결합니다.
7. **Yellow Edges Permutation (EPLL)** — Cycle edges with Ua-perm/Ub-perm/H-perm.
   Ua/Ub/H-perm으로 엣지 퍼뮤테이션을 완성합니다.

**Post-processing**: Adjacent-move cancellation, commutator merging, and a BFS window-replacement pass (depth 4, windows of 5–7 moves) reduce the raw move count before execution.

**후처리**: 인접 수 병합, 교환자 병합, BFS 윈도우 치환(깊이 4, 5–7수 윈도우)으로 실행 전 이동수를 줄입니다.

---

## Save Result JSON Format / 저장 결과 JSON 형식

Clicking **Save Result** downloads a compact JSON containing only the scramble and solution sequences.

**Save Result** 클릭 시 스크램블과 솔루션 시퀀스만을 담은 간결한 JSON 파일을 다운로드합니다.

```json
{
  "timestamp": "2026-06-07T06:44:54.356Z",
  "scramble": ["L'", "B", "R'", "F'", "..."],
  "solution": {
    "solver": "Kociemba",
    "moves": ["L", "U", "F'", "D'", "..."],
    "count": 21
  }
}
```

- `timestamp` — ISO 8601 datetime of the solve initiation / 솔브 시작 시각
- `scramble` — The exact move sequence used to scramble / 스크램블에 사용된 이동 순서
- `solution.solver` — `"Kociemba"` or `"LBL"` depending on which button was pressed / 사용된 솔버 종류
- `solution.moves` — The computed solution sequence / 계산된 솔루션 이동 순서
- `solution.count` — Total number of moves / 총 이동수
