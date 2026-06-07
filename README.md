# 3D Rubik's Cube Simulator & Solver

A fully interactive, browser-based 3D Rubik's Cube simulator with a built-in dual-solver engine. Implemented as a single standalone `cubesol.html` file using Three.js and TWEEN.js — no installation or local server required.

---

## Key Features

- **Viewport Mode (Single/Dual View)**: Supports both **Single View** (1 cube, 100% width) and **Dual View** (2 cubes side-by-side showing opposite sides). Automatically defaults to Single View on screens $\le$ 768px (mobile devices) for a cleaner, less cramped experience.
- **Intuitive 4-Way Controls**: Drag any cubie (1-9) vertically or horizontally. Edge and center dragging across axes smoothly rotates the middle layers (M, E, S). Drag the empty background to adjust camera angles.
- **Functional Undo**: Tracks manual drag-rotations in a history stack and lets you sequentially reverse them in animated steps.
- **Hamburger Menu (☰)**: Accessible bottom-left pop-up menu housing less frequent options (Default Angle, Single/Dual View toggle, Save Result, and Help Guide) to keep the main screen clean.
- **Dual Solver Engine**: Features two independent solvers:
  - **Kociemba**: Generates optimal solutions (~20 moves) for minimal turns.
  - **LBL (Layer-by-Layer)**: Recreates a beginner-friendly solving path for step-by-step learning.
- **Real-time Progress Display**: Displays current solve step info (`Move N/Total`) at the top-left during auto-solving.
- **Save Result**: Exports the scramble sequence and solver steps as a compact JSON file.

---

## How to Use

1. **Run**: Double-click `cubesol.html` to open it in any modern browser.
2. **Main Buttons**:
   - **⚡**: Scrambles the cube randomly with 20 moves.
   - **↪️**: Sequentially reverses manual drag rotations.
   - **🔴**: Halts automated solves immediately to allow manual control.
   - **☰ (Hamburger Menu)**: Opens options to reset angles, toggle views, save result, or view the help guide.
3. **Solver Buttons**:
   - **LBL - 2-step**: Executes the next 2 moves of the LBL solver (beginner method hint).
   - **LBL - Solve**: Fully solves the cube using the LBL solver (~80-130 moves).
   - **Kociemba - 1-step**: Executes the next 1 move of the Kociemba solver.
   - **Kociemba - Solve**: Fully solves the cube optimally using the Kociemba solver (~18-22 moves).

---

## Solver Architecture

### 1. Two-Phase Solver (Kociemba-style) — Solve / 1-Step
The primary solver for **Solve** and **1-Step** (Kociemba). Produces near-optimal solutions averaging **~20 moves** — roughly 4–6× fewer than LBL.

**How it works:**
1. **Phase 1 — Reduce to subgroup G1** `<U, D, R², L², F², B²>`: Uses IDA\* search with precomputed pruning tables for corner orientation, edge orientation, and UD-slice position. Terminates once all three coordinates reach their solved state.
2. **Phase 2 — Solve within G1**: Searches for the shortest sequence of G1-legal moves (`U`, `D`, `R²`, `L²`, `F²`, `B²`) that solves corner permutation, UD-edge permutation, and UD-slice edge permutation simultaneously.
3. **Post-processing**: Adjacent-move cancellation and commutator merging trim remaining redundancy.

**Performance** (verified over 30 random scrambles, 0 failures):
- **Average moves**: ~20.5 (range: 18-22)
- **Solve time**: ~0.8 s (first call has a ~2s table-building overhead)

### 2. LBL Solver (Layer-by-Layer) — Solve / 2-Step
Used for **2-Step (LBL)** and **Solve (LBL)**. Recreates beginner-friendly algorithms suitable for learning. Average ~80–130 moves.

**Stages:**
1. **White Cross**: Align 4 white edges on the top face.
2. **White Corners**: Insert 4 white corners.
3. **Second Layer**: Solve the 4 middle-layer edges.
4. **Yellow Cross (EOLL)**: Orient bottom edges to form a yellow cross.
5. **Yellow Corners Orientation (OCLL)**: Orient bottom corners using Sune/Anti-Sune.
6. **Yellow Corners Permutation (CPLL)**: Permute corners using T-perm/Y-perm.
7. **Yellow Edges Permutation (EPLL)**: Permute edges using Ua-perm/Ub-perm/H-perm.

**Post-processing**: Cancels adjacent redundant moves, merges commutators, and applies a BFS window-replacement pass to optimize the move count.

---

## Save Result JSON Format

Saves scramble and solution metadata:
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

***

# 3D 루빅스 큐브 시뮬레이터 & 솔버 (Korean)

웹 브라우저에서 실행 가능한 인터랙티브 3D 루빅스 큐브 시뮬레이터 및 듀얼 솔버 엔진입니다. Three.js와 TWEEN.js를 기반으로 별도 설치 없이 단일 `cubesol.html` 파일로 실행됩니다.

---

## 주요 기능

- **뷰포트 모드 (Single/Dual View)**: 큐브 1개만 화면 가득 보여주는 **싱글 뷰(Single View)**와 양면을 한눈에 보여주는 **듀얼 뷰(Dual View)**를 모두 지원합니다. 화면 폭이 768px 이하(모바일 기기)일 때는 더 쾌적하게 큐브를 조작할 수 있도록 자동으로 싱글 뷰가 적용됩니다.
- **직관적인 4방향 드래그 조작**: 큐브의 모든 조각(1~9번)을 가로나 세로 어느 방향으로든 자유롭게 조작할 수 있습니다. 엣지나 센터 조각을 축을 가로질러 드래그하면 가운데 축 레이어(M, E, S 레이어)가 부드럽게 회전합니다. 빈 배경을 드래그하면 카메라 시점을 회전할 수 있습니다.
- **이력 기반 실행 취소 (Undo)**: 사용자가 직접 조작한 레이어 이동 이력을 스택에 기록하며, `Undo` 버튼 클릭 시 역방향으로 동작하여 이전 상태로 차례차례 안전하게 복원합니다.
- **슬라이드 햄버거 메뉴(☰)**: 좌측 하단에 위치한 ☰ 버튼을 통해 옵션 메뉴(기본 각도 정렬, 싱글/듀얼 뷰 전환, 결과 저장, 도움말 가이드)를 열 수 있어, 모바일이나 좁은 화면에서 UI가 깔끔하게 정돈됩니다.
- **듀얼 솔버 엔진 내장**:
  - **Kociemba**: 최소 회전수(평균 약 20수)에 최적화된 해법을 제시합니다.
  - **LBL (Layer-by-Layer)**: 초보자도 한 단계씩 보고 쉽게 따라 할 수 있도록 레이어 단위의 인간 친화적 단계별 힌트를 제공합니다.
- **실시간 솔브 진행률 표시**: 자동 솔빙 진행 시 좌상단 UI에 현재 회전 번호와 전체 번호(`Move N/전체`)를 실시간으로 확인 가능합니다.
- **결과 저장**: 섞은 공식(Scramble) 및 솔버 해결 공식(Solution) 데이터를 가벼운 JSON 파일로 저장합니다.

---

## 사용 방법

1. **실행**: 지원하는 웹 브라우저에서 `cubesol.html` 파일을 더블 클릭하여 바로 실행합니다.
2. **메인 버튼**:
   - **⚡**: 무작위 20수로 큐브를 임의로 섞습니다.
   - **↪️**: 사용자가 직접 조작한 큐브 회전을 역순으로 되돌립니다.
   - **🔴**: 자동 풀이 동작을 현재 단계에서 멈추고 사용자의 수동 조작을 가능하게 합니다.
   - **☰ (햄버거 메뉴)**: 카메라 각도 리셋, 싱글/듀얼 뷰 모드 전환, 솔브 결과 저장, 조작법 도움말을 열 수 있습니다.
3. **솔버 제어 버튼**:
   - **LBL - 2-step**: LBL 공식의 다음 2수를 실행합니다 (사람이 따라 하기 쉬운 힌트).
   - **LBL - Solve**: LBL 공식을 이용하여 전체 풀이를 완료합니다 (~80-130수).
   - **Kociemba - 1-step**: Kociemba 최적화 솔루션의 다음 1수를 실행합니다.
   - **Kociemba - Solve**: Kociemba 공식을 이용하여 최적화된 풀이를 완료합니다 (~18-22수).

---

## 솔버 작동 구조

### 1. Two-Phase Solver (Kociemba 해법) — Solve / 1-Step
**Solve** 및 **1-Step (Kociemba)** 에 사용됩니다. 평균 **약 20수**의 거의 최적에 가까운 해를 생성하며, LBL 대비 약 4–6배 적은 이동수를 달성합니다.

**동작 원리:**
1. **1단계 — 부분군 G1으로 환원** `<U, D, R², L², F², B²>`: 코너 오리엔테이션, 엣지 오리엔테이션, UD-슬라이스 위치에 대한 사전 계산된 가지치기 테이블로 IDA\* 탐색을 수행합니다. 세 좌표가 모두 풀린 상태가 되면 1단계가 완료됩니다.
2. **2단계 — G1 내부 풀이**: G1 허용 수(`U`, `D`, `R²`, `L²`, `F²`, `B²`)만으로 코너/엣지 퍼뮤테이션을 동시에 해결합니다.
3. **후처리**: 인접 수 상쇄 및 교환 가능 수 병합으로 잔여 중복을 제거합니다.

**성능** (무작위 스크램블 30회 검증, 실패 0):
- **평균 이동수**: ~20.5수 (범위: 18–22수)
- **풀이 시간**: ~0.8초 (첫 실행 시 가지치기 테이블 생성에 약 2초 오버헤드 발생)

### 2. LBL Solver (LBL 해법) — Solve / 2-Step
**2-Step (LBL)** 및 **Solve (LBL)** 에 사용됩니다. 초보자 방식의 직관적 공식 순서를 따르므로 학습 목적에 적합합니다. 평균 약 80–130수.

**단계:**
1. **White Cross**: 흰색 엣지 4개를 윗면에 맞추어 배치합니다.
2. **White Corners**: 흰색 코너 4개를 알맞은 제자리에 맞춰 넣습니다.
3. **Second Layer**: 2층 중간층 엣지 4개를 해결합니다.
4. **Yellow Cross (EOLL)**: 아랫면 엣지를 오리엔테이션해 노란 십자를 만듭니다.
5. **Yellow Corners Orientation (OCLL)**: Sune/Anti-Sune 공식을 사용하여 노란색 면 전체를 오리엔테이션합니다.
6. **Yellow Corners Permutation (CPLL)**: T-perm/Y-perm 공식을 사용하여 코너 위치를 맞춥니다.
7. **Yellow Edges Permutation (EPLL)**: Ua-perm/Ub-perm/H-perm 공식을 사용하여 최종 엣지를 맞추어 완성합니다.

**후처리**: 인접 공식 상쇄, 중복 교환자 병합, BFS 윈도우 치환 알고리즘을 적용하여 회전수를 최종 단축합니다.

---

## 솔브 결과 저장 JSON 포맷

**Save Result** 클릭 시 저장되는 JSON 예시:
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
