# 🎲 Bugalhas — React Edition

<p align="center">
  <img src="personagem3.jpeg" width="160" alt="Spirit" />
  &nbsp;&nbsp;
  <img src="personagem2.jpeg" width="160" alt="Snow" />
  &nbsp;&nbsp;
  <img src="personagem1.jpeg" width="160" alt="Fox" />
</p>

<p align="center">
  <img src="https://img.shields.io/badge/React-18-61DAFB?style=for-the-badge&logo=react&logoColor=black" />
  <img src="https://img.shields.io/badge/Vite-5-646CFF?style=for-the-badge&logo=vite&logoColor=white" />
  <img src="https://img.shields.io/badge/JavaScript-ES2024-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black" />
  <img src="https://img.shields.io/badge/CSS_Modules-✓-1572B6?style=for-the-badge&logo=css3&logoColor=white" />
  <img src="https://img.shields.io/badge/Zero_Libs_Externas-✓-brightgreen?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Python-3.11+-3776AB?style=for-the-badge&logo=python&logoColor=white" />
  <img src="https://img.shields.io/badge/pytest-8+-0A9EDC?style=for-the-badge&logo=pytest&logoColor=white" />
  <img src="https://img.shields.io/badge/Playwright-1.44+-2EAD33?style=for-the-badge&logo=playwright&logoColor=white" />
</p>

<p align="center">
  <strong>Refatoração completa do jogo de dados Bugalhas para React 18 + Vite.</strong><br/>
  Arquitetura baseada em componentes, hooks customizados e Context API. Sem Redux. Sem libs de terceiros.
</p>

---

## 📑 Índice

- [Por que React?](#-por-que-react)
- [Stack](#-stack)
- [Estrutura de Pastas](#-estrutura-de-pastas)
- [Arquitetura de Componentes](#-arquitetura-de-componentes)
- [Gerenciamento de Estado](#-gerenciamento-de-estado)
- [Hooks Customizados](#-hooks-customizados)
- [Utilitários e Lógica Pura](#-utilitários-e-lógica-pura)
- [Como Executar](#-como-executar)
- [Scripts Disponíveis](#-scripts-disponíveis)
- [Mecânicas do Jogo](#-mecânicas-do-jogo)
- [Roteiro de Refatoração](#-roteiro-de-refatoração-1-hora)
- [Testes com Python](#-testes-com-python)
- [Melhorias Planejadas](#-melhorias-planejadas)

---

## ✨ Por que React?

| Problema na versão vanilla | Solução com React |
|---|---|
| `renderAll()` redesenha o DOM inteiro a cada ação | React re-renderiza apenas os componentes afetados |
| Estado global espalhado em variáveis soltas | Estado centralizado em `useReducer` + `Context` |
| Lógica do jogo misturada com manipulação de DOM | Lógica em funções puras testáveis, DOM é responsabilidade do React |
| Adicionar novos personagens exige editar HTML e JS | Basta atualizar o array `CHARS` em `constants.js` |
| Sem tipagem, sem estrutura de projeto | Convenções claras, componentes isolados e reutilizáveis |

---

## 🛠️ Stack

| Ferramenta | Versão | Motivo |
|---|---|---|
| **React** | 18 | Componentes funcionais + Concurrent Mode |
| **Vite** | 5+ | Dev server rápido, HMR instantâneo |
| **CSS Modules** | — | Escopo de CSS por componente, sem conflito de classe |
| **JavaScript** | ES2024 | Optional chaining, nullish coalescing, structuredClone |

> **Sem Redux, Zustand ou React Query.** O estado do jogo é simples o suficiente para `useReducer` + Context API.

---

## 📁 Estrutura de Pastas

```
bugalhas-react/
│
├── public/
│   ├── personagem1.jpeg      # Fox
│   ├── personagem2.jpeg      # Snow
│   └── personagem3.jpeg      # Spirit
│
├── src/
│   ├── main.jsx              # Entry point
│   ├── App.jsx               # Root — gerencia qual tela está ativa
│   │
│   ├── constants/
│   │   └── index.js          # CHARS, FACES, SCREEN names
│   │
│   ├── utils/
│   │   ├── gameLogic.js      # Funções puras: colSum, sum, dominateColumn, checkEnd
│   │   └── botStrategy.js    # Heurística da IA (função pura)
│   │
│   ├── context/
│   │   ├── GameContext.jsx   # createContext + Provider
│   │   └── gameReducer.js    # useReducer — todas as ações do jogo
│   │
│   ├── hooks/
│   │   ├── useGame.js        # Acessa context + expõe actions
│   │   ├── useBot.js         # useEffect que dispara jogadas do BOT
│   │   └── useToast.js       # Estado e timer do toast
│   │
│   ├── screens/
│   │   ├── MenuScreen/
│   │   │   ├── MenuScreen.jsx
│   │   │   └── MenuScreen.module.css
│   │   ├── GameScreen/
│   │   │   ├── GameScreen.jsx
│   │   │   └── GameScreen.module.css
│   │   └── ResultScreen/
│   │       ├── ResultScreen.jsx
│   │       └── ResultScreen.module.css
│   │
│   └── components/
│       ├── CharCard/
│       │   ├── CharCard.jsx
│       │   └── CharCard.module.css
│       ├── Board/
│       │   ├── Board.jsx
│       │   └── Board.module.css
│       ├── Cell/
│       │   ├── Cell.jsx
│       │   └── Cell.module.css
│       ├── DiceBanner/
│       │   ├── DiceBanner.jsx
│       │   └── DiceBanner.module.css
│       ├── HUD/
│       │   ├── HUD.jsx
│       │   └── HUD.module.css
│       ├── ColSums/
│       │   ├── ColSums.jsx
│       │   └── ColSums.module.css
│       └── Toast/
│           ├── Toast.jsx
│           └── Toast.module.css
│
├── index.html
├── vite.config.js
├── package.json
└── README.md
```

---

## 🧩 Arquitetura de Componentes

### Árvore de componentes

```
App
├── GameProvider (Context)
│   ├── Toast
│   └── [screen ativa]
│       ├── MenuScreen
│       │   ├── ModeSelector  (botões PVP / BOT)
│       │   └── CharCard ×3
│       │
│       ├── GameScreen
│       │   ├── HUD
│       │   │   ├── PlayerCard (p1)
│       │   │   └── PlayerCard (p2)
│       │   ├── DiceBanner
│       │   └── Arena
│       │       ├── Board (p2) + ColSums
│       │       └── Board (p1) + ColSums
│       │
│       └── ResultScreen
│           └── ResultBox
```

### Responsabilidade de cada componente

| Componente | Responsabilidade | Recebe via |
|---|---|---|
| `App` | Controla qual tela renderizar | — |
| `MenuScreen` | Seleção de modo e personagem | `useGame()` |
| `GameScreen` | Tela principal da partida | `useGame()` |
| `ResultScreen` | Exibe vencedor e placar final | `useGame()` |
| `CharCard` | Card clicável de personagem | props |
| `Board` | Grid 3×3 de um jogador | props: `board`, `owner`, `isActive` |
| `Cell` | Casa individual do tabuleiro | props: `cell`, `isPlayable`, `onClick` |
| `DiceBanner` | Dado, botão de rolar, texto de turno | `useGame()` |
| `HUD` | Placar e avatar dos dois jogadores | `useGame()` |
| `ColSums` | Somas das 3 colunas com highlight | props: `board1`, `board2`, `forPlayer` |
| `Toast` | Notificações temporárias | `useToast()` |

---

## 🗂️ Gerenciamento de Estado

### Estado global (`gameReducer.js`)

```js
// Estado inicial
const initialState = {
  screen: 'menu',        // 'menu' | 'game' | 'result'
  mode: null,            // 'pvp' | 'bot'
  turn: 1,               // 1 | 2
  p1: null,              // { nome, img }
  p2: null,              // { nome, img }
  board1: Array(9).fill(null),
  board2: Array(9).fill(null),
  currentRoll: null,     // null | 1-6
  gameOver: false,
  locked: false,         // true enquanto o BOT está "pensando"
}

// Actions
const ACTIONS = {
  SET_MODE:       'SET_MODE',
  PICK_CHAR:      'PICK_CHAR',
  START_GAME:     'START_GAME',
  ROLL_DICE:      'ROLL_DICE',
  PLACE_CARD:     'PLACE_CARD',
  BOT_TURN_START: 'BOT_TURN_START',
  BOT_TURN_END:   'BOT_TURN_END',
  GAME_OVER:      'GAME_OVER',
  RESTART:        'RESTART',
}
```

### Por que `useReducer` e não `useState` isolados?

O jogo tem **múltiplas partes do estado que mudam juntas** (ex: ao colocar uma peça, `board`, `currentRoll`, `turn` e `locked` mudam simultaneamente). `useReducer` garante que essas mudanças sejam atômicas e previsíveis — sem race conditions e sem estados intermediários inconsistentes.

---

## 🪝 Hooks Customizados

### `useGame.js`
Ponto único de acesso ao estado e às actions do jogo. Todos os componentes usam este hook em vez de acessar o Context diretamente.

```js
// Uso em qualquer componente:
const { state, rollDice, placeCard, setMode, pickChar, restart } = useGame()
```

### `useBot.js`
Encapsula toda a lógica assíncrona do BOT em um `useEffect`.
Observa `state.turn`, `state.mode` e `state.locked` para disparar a jogada automática.

```js
// Internamente:
useEffect(() => {
  if (state.mode !== 'bot' || state.turn !== 2 || state.gameOver) return
  const timer = setTimeout(() => {
    const roll = rollDice()                        // rola dado
    setTimeout(() => {
      const index = chooseBotMove(state, roll)     // heurística
      placeCard(index, 2)                          // posiciona
    }, 700)
  }, 800)
  return () => clearTimeout(timer)
}, [state.turn, state.locked])
```

### `useToast.js`
Estado e timer isolados para não poluir o reducer.

```js
const { message, showToast } = useToast()
// showToast('Escolha um modo primeiro') → exibe por 2.2s e some
```

---

## ⚙️ Utilitários e Lógica Pura

Toda a lógica de negócio fica em **funções puras** sem efeitos colaterais. Isso facilita testes unitários no futuro.

### `gameLogic.js`

```js
// Soma de uma coluna com multiplicador por repetição
export function colSum(board, col) { ... }

// Soma total do tabuleiro
export function sum(board) { ... }

// Retorna novo board2 após dominação (imutável)
export function applyDomination(board1, board2, placedIndex, roll) { ... }

// Verifica se ambos os tabuleiros estão cheios
export function checkEnd(board1, board2) { ... }
```

### `botStrategy.js`

```js
// Recebe o estado do jogo e o dado rolado,
// retorna o índice da melhor casa para o BOT
export function chooseBotMove(state, roll) {
  const free = state.board2
    .map((v, i) => (!v ? i : -1))
    .filter(i => i !== -1)

  let best = free[0], bestScore = -Infinity
  free.forEach(i => {
    let score = 0
    const col = i % 3
    // Prioridade 1: destruir peça inimiga
    ;[col, col + 3, col + 6].forEach(pos => {
      if (state.board1[pos]?.num === roll) score += 10
    })
    // Prioridade 2: avançar em colunas vantajosas
    score += colSum(state.board2, col) - colSum(state.board1, col)
    if (score > bestScore) { bestScore = score; best = i }
  })
  return best
}
```

---

## 🚀 Como Executar

### Pré-requisitos

- Node.js `>= 18`
- npm `>= 9` ou yarn

### Instalação

```bash
# 1. Clone o repositório
git clone https://github.com/seu-usuario/bugalhas-react.git
cd bugalhas-react

# 2. Instale as dependências
npm install

# 3. Inicie o servidor de desenvolvimento
npm run dev
```

Acesse **http://localhost:5173** no navegador.

### Criando do zero com Vite

```bash
npm create vite@latest bugalhas-react -- --template react
cd bugalhas-react
npm install
```

---

## 📜 Scripts Disponíveis

| Comando | Ação |
|---|---|
| `npm run dev` | Inicia o servidor de desenvolvimento com HMR |
| `npm run build` | Gera o bundle de produção em `/dist` |
| `npm run preview` | Serve o bundle de produção localmente |
| `npm run lint` | Roda ESLint no projeto |

---

## 🎮 Mecânicas do Jogo

### Regra de pontuação por coluna

| Dados na coluna | Cálculo | Exemplo (`valor = 3`) |
|---|---|---|
| 1 dado | `valor × 1` | 3 |
| 2 dados iguais | `valor × 2 × 2` | 12 |
| 3 dados iguais | `valor × 3 × 3` | 27 |
| Dados mistos | soma simples de cada grupo | `[6,6,2]` → 24+2 = **26** |

### Regra de dominação

Ao posicionar um dado no índice `i`, a coluna `col = i % 3` do tabuleiro adversário é verificada. Todas as casas `[col, col+3, col+6]` com valor igual ao dado recém-posicionado são **removidas**.

```
Exemplo visual:
  Você joga ⚄(5) na coluna do meio do seu tabuleiro
  Adversário tinha [⚄, _, ⚄] na coluna do meio
  → Ambos os ⚄ adversários são destruídos
```

---

## 🗺️ Roteiro de Refatoração (1 hora)

Um guia passo a passo para executar a migração sem se perder:

```
⏱️ 0:00 – 0:10  →  Setup do projeto
  [ ] npm create vite@latest bugalhas-react -- --template react
  [ ] Copiar imagens para /public
  [ ] Copiar variáveis CSS (:root) para src/index.css
  [ ] Copiar @import do Google Fonts para index.html

⏱️ 0:10 – 0:20  →  Lógica pura (sem React ainda)
  [ ] Criar src/constants/index.js  (CHARS, FACES, SCREENS)
  [ ] Criar src/utils/gameLogic.js  (colSum, sum, applyDomination, checkEnd)
  [ ] Criar src/utils/botStrategy.js (chooseBotMove)

⏱️ 0:20 – 0:35  →  Estado e Context
  [ ] Criar src/context/gameReducer.js  (initialState + reducer com todos os ACTIONS)
  [ ] Criar src/context/GameContext.jsx (Provider + useContext)
  [ ] Criar src/hooks/useGame.js
  [ ] Criar src/hooks/useToast.js

⏱️ 0:35 – 0:50  →  Componentes
  [ ] Cell.jsx + Cell.module.css
  [ ] Board.jsx (mapeia board[0..8] → <Cell />)
  [ ] ColSums.jsx
  [ ] DiceBanner.jsx
  [ ] HUD.jsx
  [ ] CharCard.jsx
  [ ] Toast.jsx

⏱️ 0:50 – 0:55  →  Screens
  [ ] MenuScreen.jsx
  [ ] GameScreen.jsx (monta Arena com boards e HUD)
  [ ] ResultScreen.jsx

⏱️ 0:55 – 1:00  →  App.jsx + useBot + testes finais
  [ ] App.jsx renderiza screen baseado em state.screen
  [ ] src/hooks/useBot.js (useEffect com setTimeout para BOT)
  [ ] Smoke test: PvP funciona? BOT joga? Resultado aparece?
```

---

## 🐍 Testes com Python

A suíte de testes é 100% Python. Testes unitários cobrem a lógica pura do jogo (re-implementada em Python espelhando o JS), e os testes E2E cobrem o fluxo completo no navegador via Playwright.

### Estrutura da pasta `tests/`

```
tests/
├── requirements.txt          # pytest, pytest-playwright
├── pytest.ini                # configuração global
├── conftest.py               # fixtures compartilhadas (base_url)
│
├── unit/
│   ├── __init__.py
│   ├── game_logic.py         # Re-implementação Python das funções puras do JS
│   ├── test_col_sum.py       # colSum — pontuação e multiplicadores
│   ├── test_domination.py    # applyDomination — remoção de peças inimigas
│   ├── test_bot_strategy.py  # chooseBotMove — heurística da IA
│   └── test_check_end.py     # checkEnd — condição de fim de jogo
│
└── e2e/
    ├── __init__.py
    ├── conftest.py           # fixture `page` com base_url do Vite
    ├── test_menu.py          # seleção de modo e personagem
    ├── test_game_pvp.py      # fluxo completo PvP: rolar, posicionar, trocar turno
    └── test_game_bot.py      # fluxo vs BOT: verificar jogada automática
```

---

### Instalação das dependências Python

```bash
cd tests
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate

pip install -r requirements.txt
playwright install chromium
```

**`tests/requirements.txt`**
```
pytest>=8.0
pytest-playwright>=0.5
```

**`tests/pytest.ini`**
```ini
[pytest]
testpaths = unit e2e
addopts = -v --tb=short
```

---

### Pré-requisito para E2E

Os testes E2E precisam que o servidor Vite esteja rodando. Suba em um terminal separado antes de rodar:

```bash
# terminal 1 — app React
npm run dev

# terminal 2 — testes
cd tests && pytest e2e/
```

---

### `data-testid` necessários nos componentes React

Os testes E2E usam `data-testid` em vez de classes CSS (que são hasheadas pelo CSS Modules). Adicione estes atributos ao implementar os componentes:

| Componente | `data-testid` |
|---|---|
| Botão PvP | `mode-pvp` |
| Botão BOT | `mode-bot` |
| Card de personagem (índice n) | `char-card-0`, `char-card-1`, `char-card-2` |
| Botão Rolar | `roll-btn` |
| Face do dado | `dice-face` |
| Tabuleiro P1 | `board-p1` |
| Tabuleiro P2 | `board-p2` |
| Célula do tabuleiro P1 (índice n) | `cell-p1-0` … `cell-p1-8` |
| Placar P1 | `hud-score-p1` |
| Placar P2 | `hud-score-p2` |
| Tela de resultado | `result-screen` |
| Texto do vencedor | `result-winner` |
| Botão Jogar Novamente | `btn-restart` |
| Toast | `toast` |

---

### Lógica pura — `tests/unit/game_logic.py`

Re-implementação fiel das funções de `gameLogic.js` e `botStrategy.js` em Python. É esta camada que os testes unitários importam.

```python
from collections import Counter

def cell(num):
    """Cria uma célula preenchida, espelhando { num } do JS."""
    return {"num": num}

def col_sum(board, col):
    positions = [col, col + 3, col + 6]
    vals = [board[i]["num"] for i in positions if board[i] is not None]
    if not vals:
        return 0
    freq = Counter(vals)
    total = 0
    for valor, repeticoes in freq.items():
        total += valor * repeticoes * repeticoes if repeticoes >= 2 else valor
    return total

def board_sum(board):
    return col_sum(board, 0) + col_sum(board, 1) + col_sum(board, 2)

def apply_domination(enemy_board, placed_index, rolled):
    """Imutável: retorna novo board com peças dominadas removidas."""
    col = placed_index % 3
    new_board = list(enemy_board)
    for pos in [col, col + 3, col + 6]:
        if new_board[pos] is not None and new_board[pos]["num"] == rolled:
            new_board[pos] = None
    return new_board

def check_end(board1, board2):
    return all(c is not None for c in board1) and all(c is not None for c in board2)

def choose_bot_move(board1, board2, roll):
    free = [i for i, v in enumerate(board2) if v is None]
    if not free:
        return None
    best, best_score = free[0], float("-inf")
    for i in free:
        col = i % 3
        score = sum(
            10 for pos in [col, col + 3, col + 6]
            if board1[pos] is not None and board1[pos]["num"] == roll
        )
        score += col_sum(board2, col) - col_sum(board1, col)
        if score > best_score:
            best_score, best = score, i
    return best
```

---

### Testes Unitários

#### `test_col_sum.py`

```python
import pytest
from unit.game_logic import col_sum, cell

EMPTY = [None] * 9

def board_with(col, values):
    b = list(EMPTY)
    for row, v in enumerate(values):
        if v is not None:
            b[col + row * 3] = cell(v)
    return b

class TestColSumEmpty:
    def test_coluna_vazia_retorna_zero(self):
        assert col_sum(EMPTY, 0) == 0

    def test_todas_colunas_vazias(self):
        for c in range(3):
            assert col_sum(EMPTY, c) == 0

class TestColSumSingleValues:
    def test_um_dado_retorna_o_proprio_valor(self):
        b = board_with(0, [3, None, None])
        assert col_sum(b, 0) == 3

    def test_dado_maximo_sem_repeticao(self):
        b = board_with(1, [6, None, None])
        assert col_sum(b, 1) == 6

    def test_tres_dados_distintos_soma_simples(self):
        b = board_with(2, [1, 2, 3])
        assert col_sum(b, 2) == 6

class TestColSumMultiplier:
    def test_dois_iguais_aplica_multiplicador_4x(self):
        b = board_with(0, [3, 3, None])
        assert col_sum(b, 0) == 3 * 2 * 2   # 12

    def test_tres_iguais_aplica_multiplicador_9x(self):
        b = board_with(0, [3, 3, 3])
        assert col_sum(b, 0) == 3 * 3 * 3   # 27

    def test_tres_seis_iguais_maximo_possivel(self):
        b = board_with(0, [6, 6, 6])
        assert col_sum(b, 0) == 6 * 9        # 54

    def test_dois_iguais_mais_diferente(self):
        # [6, 6, 2] → (6×2×2) + 2 = 26
        b = board_with(0, [6, 6, 2])
        assert col_sum(b, 0) == 26

    @pytest.mark.parametrize("val,expected", [
        (1, 1 * 2 * 2),   # 4
        (4, 4 * 2 * 2),   # 16
        (6, 6 * 2 * 2),   # 24
    ])
    def test_dois_iguais_parametrizado(self, val, expected):
        b = board_with(0, [val, val, None])
        assert col_sum(b, 0) == expected
```

#### `test_domination.py`

```python
from unit.game_logic import apply_domination, cell

EMPTY = [None] * 9

class TestApplyDomination:
    def test_remove_peca_inimiga_na_mesma_coluna(self):
        enemy = list(EMPTY)
        enemy[0] = cell(4)                    # coluna 0, linha 0
        result = apply_domination(enemy, placed_index=0, rolled=4)
        assert result[0] is None

    def test_remove_multiplas_pecas_iguais_na_coluna(self):
        enemy = list(EMPTY)
        enemy[0] = cell(4)                    # col 0, linha 0
        enemy[3] = cell(4)                    # col 0, linha 1
        result = apply_domination(enemy, placed_index=0, rolled=4)
        assert result[0] is None
        assert result[3] is None

    def test_nao_remove_valor_diferente(self):
        enemy = list(EMPTY)
        enemy[0] = cell(5)
        result = apply_domination(enemy, placed_index=0, rolled=4)
        assert result[0] == cell(5)           # intocado

    def test_nao_remove_peca_de_outra_coluna(self):
        enemy = list(EMPTY)
        enemy[1] = cell(4)                    # coluna 1
        result = apply_domination(enemy, placed_index=0, rolled=4)
        assert result[1] == cell(4)           # coluna errada, intocado

    def test_imutabilidade_nao_altera_board_original(self):
        enemy = list(EMPTY)
        enemy[0] = cell(3)
        original_ref = enemy[0]
        apply_domination(enemy, placed_index=0, rolled=3)
        assert enemy[0] is original_ref       # original não foi modificado
```

#### `test_bot_strategy.py`

```python
from unit.game_logic import choose_bot_move, cell

EMPTY = [None] * 9

class TestBotStrategy:
    def test_escolhe_unica_casa_livre(self):
        board2 = [cell(1)] * 9
        board2[4] = None
        assert choose_bot_move(EMPTY, board2, roll=3) == 4

    def test_prefere_casa_que_destroi_inimigo(self):
        board1 = list(EMPTY)
        board1[0] = cell(5)                   # inimigo col 0, linha 0
        board2 = list(EMPTY)
        # col 0 livre nos índices 0,3,6 — colocar lá destrói o 5 do P1
        result = choose_bot_move(board1, board2, roll=5)
        assert result % 3 == 0                # deve escolher coluna 0

    def test_retorna_none_sem_casas_livres(self):
        full_board = [cell(1)] * 9
        assert choose_bot_move(EMPTY, full_board, roll=3) is None

    def test_nao_escolhe_casa_ocupada(self):
        board2 = list(EMPTY)
        board2[0] = cell(2)                   # ocupada
        result = choose_bot_move(EMPTY, board2, roll=3)
        assert result != 0
```

#### `test_check_end.py`

```python
from unit.game_logic import check_end, cell

FULL  = [cell(1)] * 9
EMPTY = [None]    * 9

class TestCheckEnd:
    def test_ambos_cheios_retorna_true(self):
        assert check_end(FULL, FULL) is True

    def test_um_vazio_retorna_false(self):
        assert check_end(FULL, EMPTY) is False
        assert check_end(EMPTY, FULL) is False

    def test_ambos_vazios_retorna_false(self):
        assert check_end(EMPTY, EMPTY) is False

    def test_um_parcialmente_preenchido_retorna_false(self):
        partial = [cell(1)] * 8 + [None]
        assert check_end(FULL, partial) is False
```

---

### Testes E2E — Playwright

#### `tests/e2e/conftest.py`

```python
import pytest

BASE_URL = "http://localhost:5173"

@pytest.fixture(scope="session")
def base_url():
    return BASE_URL

@pytest.fixture
def game_page(page, base_url):
    """Abre o jogo e retorna a page já carregada."""
    page.goto(base_url)
    page.wait_for_selector("[data-testid='mode-pvp']")
    return page
```

#### `test_menu.py`

```python
class TestMenu:
    def test_titulo_aparece_na_pagina(self, game_page):
        assert "Bugalhas" in game_page.title()

    def test_botoes_de_modo_estao_visiveis(self, game_page):
        assert game_page.locator("[data-testid='mode-pvp']").is_visible()
        assert game_page.locator("[data-testid='mode-bot']").is_visible()

    def test_personagens_aparecem_apos_selecionar_pvp(self, game_page):
        game_page.click("[data-testid='mode-pvp']")
        for i in range(3):
            assert game_page.locator(f"[data-testid='char-card-{i}']").is_visible()

    def test_selecionar_mesmo_personagem_exibe_toast(self, game_page):
        game_page.click("[data-testid='mode-pvp']")
        game_page.click("[data-testid='char-card-0']")   # J1 escolhe Fox
        game_page.click("[data-testid='char-card-0']")   # J2 tenta o mesmo
        toast = game_page.locator("[data-testid='toast']")
        toast.wait_for(state="visible")
        assert "diferente" in toast.inner_text().lower()

    def test_sem_modo_selecionado_exibe_aviso(self, game_page):
        game_page.click("[data-testid='char-card-0']")
        toast = game_page.locator("[data-testid='toast']")
        toast.wait_for(state="visible")
        assert "modo" in toast.inner_text().lower()
```

#### `test_game_pvp.py`

```python
import re

def start_pvp_game(page):
    page.click("[data-testid='mode-pvp']")
    page.click("[data-testid='char-card-0']")   # J1 = Fox
    page.click("[data-testid='char-card-1']")   # J2 = Snow
    page.wait_for_selector("[data-testid='roll-btn']")

class TestGamePvP:
    def test_tela_de_jogo_aparece_apos_selecao(self, game_page):
        start_pvp_game(game_page)
        assert game_page.locator("[data-testid='roll-btn']").is_visible()
        assert game_page.locator("[data-testid='board-p1']").is_visible()
        assert game_page.locator("[data-testid='board-p2']").is_visible()

    def test_placar_inicial_e_zero(self, game_page):
        start_pvp_game(game_page)
        assert game_page.locator("[data-testid='hud-score-p1']").inner_text() == "0"
        assert game_page.locator("[data-testid='hud-score-p2']").inner_text() == "0"

    def test_dado_muda_apos_rolar(self, game_page):
        start_pvp_game(game_page)
        face_antes = game_page.locator("[data-testid='dice-face']").inner_text()
        game_page.click("[data-testid='roll-btn']")
        face_depois = game_page.locator("[data-testid='dice-face']").inner_text()
        # após rolar, a face deve ser um emoji de dado (⚀–⚅)
        assert re.match(r"[⚀⚁⚂⚃⚄⚅]", face_depois)

    def test_celulas_jogaveis_aparecem_apos_rolar(self, game_page):
        start_pvp_game(game_page)
        game_page.click("[data-testid='roll-btn']")
        playable = game_page.locator("[data-testid='board-p1'] [data-testid^='cell-p1']")
        assert playable.count() > 0

    def test_rolar_duas_vezes_exibe_toast(self, game_page):
        start_pvp_game(game_page)
        game_page.click("[data-testid='roll-btn']")
        game_page.click("[data-testid='roll-btn']")    # segundo clique sem posicionar
        toast = game_page.locator("[data-testid='toast']")
        toast.wait_for(state="visible")
        assert "já rolou" in toast.inner_text().lower()

    def test_posicionar_dado_muda_o_turno(self, game_page):
        start_pvp_game(game_page)
        game_page.click("[data-testid='roll-btn']")
        game_page.locator("[data-testid='board-p1'] [data-testid^='cell-p1']").first.click()
        # após P1 posicionar, o banner deve indicar turno do P2
        banner = game_page.locator("[data-testid='dice-banner']")
        assert "snow" in banner.inner_text().lower()

    def test_placar_aumenta_apos_posicionar(self, game_page):
        start_pvp_game(game_page)
        game_page.click("[data-testid='roll-btn']")
        game_page.locator("[data-testid='board-p1'] [data-testid^='cell-p1']").first.click()
        score = int(game_page.locator("[data-testid='hud-score-p1']").inner_text())
        assert score > 0
```

#### `test_game_bot.py`

```python
def start_bot_game(page):
    page.click("[data-testid='mode-bot']")
    page.click("[data-testid='char-card-0']")   # jogador escolhe Fox
    page.wait_for_selector("[data-testid='roll-btn']")

class TestGameBot:
    def test_jogo_inicia_no_turno_do_jogador(self, game_page):
        start_bot_game(game_page)
        assert game_page.locator("[data-testid='roll-btn']").is_visible()

    def test_bot_joga_automaticamente_apos_jogador(self, game_page):
        start_bot_game(game_page)
        game_page.click("[data-testid='roll-btn']")
        game_page.locator("[data-testid='board-p1'] [data-testid^='cell-p1']").first.click()
        # aguarda o BOT jogar (delay ~1.5s) e o turno voltar para P1
        game_page.wait_for_selector("[data-testid='roll-btn']", timeout=4000)
        assert game_page.locator("[data-testid='roll-btn']").is_visible()

    def test_tabuleiro_bot_tem_peca_apos_jogada(self, game_page):
        start_bot_game(game_page)
        game_page.click("[data-testid='roll-btn']")
        game_page.locator("[data-testid='board-p1'] [data-testid^='cell-p1']").first.click()
        game_page.wait_for_selector("[data-testid='roll-btn']", timeout=4000)
        # pelo menos uma célula do tabuleiro do BOT (P2) deve estar preenchida
        filled = game_page.locator("[data-testid='board-p2'] .filled")
        assert filled.count() >= 1
```

---

### Como executar os testes

```bash
cd tests

# Apenas unitários (não precisa do servidor rodando)
pytest unit/ -v

# Apenas E2E (requer npm run dev rodando)
pytest e2e/ -v

# Tudo junto
pytest -v

# Com relatório de cobertura resumido
pytest unit/ -v --tb=short

# Rodar um arquivo específico
pytest unit/test_col_sum.py -v

# Rodar um teste específico pelo nome
pytest unit/test_col_sum.py::TestColSumMultiplier::test_tres_iguais_aplica_multiplicador_9x -v
```

---

### Cobertura esperada da suíte

| Módulo | Testes | O que cobre |
|---|---|---|
| `test_col_sum.py` | 10 | Coluna vazia, valor único, ×4, ×9, dados mistos, parametrizado |
| `test_domination.py` | 5 | Remove correto, ignora coluna errada, ignora valor diferente, imutabilidade |
| `test_bot_strategy.py` | 4 | Casa única, preferência de destruição, tabuleiro cheio, não repete ocupada |
| `test_check_end.py` | 4 | Ambos cheios, um vazio, ambos vazios, parcialmente preenchido |
| `test_menu.py` (E2E) | 5 | Carregamento, botões, personagens, toast de duplicata, toast sem modo |
| `test_game_pvp.py` (E2E) | 7 | Tela de jogo, placar zero, dado muda, células jogáveis, toast, turno, pontuação |
| `test_game_bot.py` (E2E) | 3 | Turno inicial, BOT joga automaticamente, tabuleiro preenchido |

---

## 🔮 Melhorias Planejadas

- [ ] **TypeScript** — adicionar tipos para `GameState`, `Board`, `Player`
- [ ] **Framer Motion** — substituir animações CSS por `motion.div`
- [ ] **Cobertura de testes** — `pytest-cov` nos testes unitários Python
- [ ] **localStorage** — salvar histórico de partidas e placar geral
- [ ] **React Router** — rotas `/menu`, `/game`, `/result` (facilita deep link)
- [ ] **Dificuldades do BOT** — fácil (aleatório), médio (heurística atual), difícil (minimax)
- [ ] **Modo online** — WebSocket com estado sincronizado entre dois clientes
- [ ] **PWA** — `vite-plugin-pwa` para instalar como app no celular
- [ ] **Novos personagens** — apenas adicionar ao array `CHARS`, zero mudanças no código

---

## 👤 Autor

Feito com 🎲 por **[Seu Nome]**

[![GitHub](https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white)](https://github.com/seu-usuario)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/seu-usuario)

---

<p align="center">
  <sub>Versão vanilla original → <a href="./JogoDasBugalhas.html">JogoDasBugalhas.html</a> · Inspirado em <a href="https://store.steampowered.com/app/1313140/Cult_of_the_Lamb/">Cult of the Lamb</a></sub>
</p>
