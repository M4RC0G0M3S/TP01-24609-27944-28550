# TP01-24609-27944-28550

O nosso grupo escolheu um jogo 2d, que foi inspirado no jogo Super Mario Bros.
O objetivo do jogo e conseguir atravessar os níveis, coletar itens e evitar ou derrotar inimigos.

O jogador pode:
mover se para a esquerda e direita
saltar entre plataformas
interagir com o ambiente
e derrotar ou evitar inimigos
Caso o jogador colida com o inimigo, perde e reinicia o nível

Os níveis são construídos, com plataformas, paredes e zonas perigosas

Os inimigos movem-se automaticamente, o que cria um desafio ao jogador, porem podem ser derrotados quando saltamos em cima deles

O sistema de pontuação, consiste em coletar as gemas, o que gera pontos e incentiva o jogador a percorrer o mapa todo
## Arquitetura do Projeto

O projeto segue um padrão **Core + Plataformas**:

```
Platformer2D.sln
├── Platformer2D.Core/        ← lógica partilhada (toda a lógica do jogo)
│   ├── Game/                 ← classes C# do jogo
│   └── Content/              ← assets (sprites, sons, níveis, fontes)
├── Platformer2D.DesktopGL/   ← plataforma: Windows / Linux / macOS
├── Platformer2D.Android/     ← plataforma: Android
├── Platformer2D.iOS/         ← plataforma: iOS
├── Platformer2D.WindowsDX/   ← plataforma: Windows (DirectX)
└── Documentation/            ← tutoriais e guias progressivos
```

Cada projeto de plataforma **referencia o Core** e contém apenas o ponto de entrada e configurações específicas do sistema operativo. Todo o comportamento do jogo vive no Core e é partilhado por todas as plataformas.

---

## Estrutura de Pastas

### Raiz

```
Platformer2D/
├── Platformer2D.sln     → Solução Visual Studio (referencia todos os projetos)
└── README.md            → Documentação geral do projeto
```

---

### `Platformer2D.Core/` — Lógica Partilhada

Biblioteca de classe cross-platform que contém **todo o código do jogo**.

```
Platformer2D.Core/
├── Platformer2D.Core.csproj
├── Game/                        ← classes principais (ver detalhe abaixo)
└── Content/                     ← assets do jogo (ver detalhe abaixo)
```

---

#### `Game/` — Classes Principais

```
Game/
├── Game.cs                  → PlatformerGame — ciclo principal do jogo
├── Level.cs                 → Grelha de tiles; gere entidades e pontuação
├── Player.cs                → Personagem jogável; física e input
├── Enemy.cs                 → IA dos inimigos; patrulha de plataformas
├── Gem.cs                   → Objeto colecionável; atribui pontos
├── Tile.cs                  → Estrutura de um tile (tipo + colisão)
├── Animation.cs             → Definição de spritesheet (frames + velocidade)
├── AnimationPlayer.cs       → Reprodutor de animações
├── RectangleExtensions.cs   → Extensão de Rectangle (colisões avançadas)
├── Circle.cs                → Bounding circle para colisão com gems
└── VirtualGamePad.cs        → Gamepad virtual para ecrãs tácteis
```

---

#### `Content/` — Assets do Jogo

```
Content/
├── Levels/
│   ├── 0.txt                → Nível 0 (formato ASCII, ver secção abaixo)
│   ├── 1.txt                → Nível 1
│   └── 2.txt  …             → Níveis adicionais (numeração sequencial)
│
├── Sprites/
│   ├── Player/
│   │   ├── Idle.png         → Animação parado
│   │   ├── Run.png          → Animação corrida
│   │   ├── Jump.png         → Animação salto
│   │   ├── Celebrate.png    → Animação vitória
│   │   └── Die.png          → Animação morte
│   ├── MonsterA/
│   │   ├── Run.png
│   │   ├── Idle.png
│   │   └── Die.png          → Incluído mas não utilizado (para desenvolvimento futuro)
│   ├── MonsterB/            → (mesma estrutura)
│   ├── MonsterC/            → (mesma estrutura)
│   ├── MonsterD/            → (mesma estrutura)
│   └── Gem.png              → Spritesheet da gema animada
│
├── Tiles/
│   ├── BlockA.png           → Tile sólido (variante A)
│   ├── BlockB.png           → Tile sólido (variante B)
│   ├── Platform.png         → Plataforma passável por baixo
│   └── Impassable.png       → Tile intransponível
│
├── Backgrounds/
│   ├── Layer0.png           → Camada mais ao fundo
│   ├── Layer1.png           → Camada intermédia
│   └── Layer2.png           → Camada da frente (EntityLayer — entidades desenhadas aqui)
│
├── Fonts/
│   └── Hud.spritefont       → Fonte do HUD (score e tempo restante)
│
└── Sounds/
    ├── GemCollected.wav
    ├── PlayerJump.wav
    ├── PlayerDie.wav
    ├── ExitReached.wav
    └── Music.mp3
```

---

### Projetos de Plataforma

Cada projeto contém apenas o ponto de entrada, a Activity/AppDelegate e o ficheiro de build de conteúdo (`.mgcb`). Toda a lógica é herdada do Core.

| Projeto | Plataforma | Ponto de Entrada | Notas |
|---|---|---|---|
| `Platformer2D.DesktopGL/` | Windows, Linux, macOS | `Program.cs` | OpenGL; build com .NET |
| `Platformer2D.Android/` | Android | `MainActivity.cs` | Inclui VirtualGamePad |
| `Platformer2D.iOS/` | iPhone / iPad | `AppDelegate.cs` | Requer build em Mac |
| `Platformer2D.WindowsDX/` | Windows | `Program.cs` | DirectX; melhor performance em hardware Microsoft |

---

### `Documentation/` — Tutoriais

Quatro guias Markdown organizados por complexidade crescente:

```
Documentation/
├── 1_basic_platformer_features.md        → Gem, Circle, Tile
├── 2_intermediate_platformer_features.md → AnimationPlayer, Enemy, Player, VirtualGamePad
├── 3_advanced_platformer_features.md     → Level, PlatformerGame, RectangleExtensions
└── 3_adding_a_scrolling_level.md         → Extensão: parallax scrolling
```

---

## Fluxo de Execução

```
Arranque
   └── PlatformerGame.LoadContent()
           └── LoadNextLevel()
                   └── Level.LoadTiles()   ← lê o ficheiro .txt do nível

Loop principal
   └── PlatformerGame.Update()
           ├── [jogador morreu OU tempo esgotado] → pausa; ignora input
           ├── [jogador chegou à saída]           → converte tempo restante em pontos
           └── [em jogo]
                   ├── level.TimeRemaining--
                   ├── Player.Update()
                   ├── Enemy.Update()      ← cada inimigo gere o próprio movimento
                   ├── Gem.Update()
                   └── Verificar: jogador na saída? jogador caiu do ecrã?

   └── PlatformerGame.Draw()
           ├── Level.Draw()
           │       ├── DrawTiles()         ← camadas de fundo (0 → 1 → 2)
           │       ├── Gem.Draw()
           │       ├── Enemy.Draw()
           │       └── Player.Draw()
           └── DrawHud()                  ← SCORE e TIME no ecrã
```

---

## Classes e Responsabilidades

### `Game.cs` — PlatformerGame

Classe principal que herda de `Microsoft.Xna.Framework.Game`. Métodos importantes:

- `LoadContent()` — carrega fontes e chama `LoadNextLevel()`
- `LoadNextLevel()` — localiza e lê o ficheiro do nível seguinte
- `Update()` — processa input (teclado, gamepad) e chama `Level.Update()`
- `Draw()` — desenha o gameplay e o HUD

### `Level.cs`

Grelha uniforme de tiles que contém coleções de gems e inimigos. Controla:

- Estrutura física do nível (`Tile[,] tiles`)
- Três camadas de fundo (`Texture2D[] layers`)
- Lista de gems e inimigos
- Posição de início do jogador e localização da saída
- Pontuação e tempo restante

### `Player.cs`

Física simplificada: queda, salto e deteção de colisões dinâmicas. Aceita input de:
- Teclado
- Gamepad físico
- VirtualGamePad (ecrã táctil)

### `Enemy.cs`

Comportamento padrão: patrulha uma plataforma de um lado ao outro. Inverte direção quando:
- Deteta um tile intransponível (parede)
- Está prestes a cair da aresta da plataforma

### `RectangleExtensions.cs`

Extensão da estrutura `Rectangle` do MonoGame. Implementa dois métodos que tratam o retângulo como um "círculo quadrado" com dois raios (largura e altura), facilitando a deteção de colisão entre entidades.

### `AnimationPlayer.cs`

Armazena uma spritesheet em `Animation` e reproduz-a frame a frame. Se a animação pedida já estiver a tocar, o método `PlayAnimation()` retorna imediatamente — evitando reinícios indesejados.

---

## Formato dos Níveis

Os níveis são ficheiros `.txt` em ASCII guardados em `Content/Levels/`. Cada caractere representa uma célula da grelha:

| Caractere | Significado |
|---|---|
| `.` | Espaço vazio |
| `#` | Tile sólido (bloco) |
| `X` | Saída do nível |
| `G` | Gem colecionável |
| `1` | Inimigo tipo A (MonsterA) |
| `2` | Inimigo tipo B (MonsterB) |
| `3` | Inimigo tipo C (MonsterC) |
| `4` | Inimigo tipo D (MonsterD) |

**Exemplo de mapa (nível 0):**

```
..............................................................................
..............................................................................
...........................G..............................................X....
..........................###.....................................############
```

> Para adicionar novos níveis: criar `N.txt` (numeração sequencial) em `Content/Levels/` e garantir que a propriedade *Copy to Output Directory* está ativa no projeto de conteúdo.

---

## Sistema de Animação

Cada entidade tem as suas animações definidas como spritesheets (uma imagem com todos os frames em linha). O `AnimationPlayer` avança o frame com base no tempo decorrido.

**Animações do jogador (5):** `Idle`, `Run`, `Jump`, `Celebrate`, `Die`

**Animações de cada inimigo (3):** `Run`, `Idle`, `Die`
> A animação `Die` dos inimigos está incluída mas não é utilizada na versão base — reservada para desenvolvimento futuro.

> **Requisito:** os frames de animação têm de ser **quadrados** (largura = altura).

---

## Funcionalidades

- Suporte cross-platform: Windows, Android, iOS, Linux
- Controlo por teclado, gamepad e gamepad virtual (ecrã táctil)
- Física simples: queda e salto com deteção de colisões dinâmica
- Assets de produção: spritesheets, efeitos sonoros e fundos com múltiplas camadas
- Resolução dupla: assets de alta e baixa resolução incluídos
- Projeto de conteúdo unificado que compila para cada plataforma alvo
