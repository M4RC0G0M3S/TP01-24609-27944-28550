# 🎮 TP01 — Platformer 2D

> Trabalho Prático 1 — Análise e Documentação de Projeto MonoGame

---

## 👥 Grupo

| Nome | Número |
|---|---|
| Afonso Miranda | 27944 |
| Marco Gomes | 28550 |
| David Costa | 24609 |

---

## 📋 Descrição do Projeto

O nosso grupo escolheu um jogo 2D inspirado no clássico **Super Mario Bros**.

O objetivo do jogo é conseguir atravessar os níveis, coletar itens e evitar ou derrotar inimigos.

**O jogador pode:**
- Mover-se para a esquerda e para a direita
- Saltar entre plataformas
- Interagir com o ambiente
- Derrotar ou evitar inimigos

> ⚠️ Caso o jogador colida com um inimigo, perde e reinicia o nível.

Os níveis são construídos com plataformas, paredes e zonas perigosas. Os inimigos movem-se automaticamente, criando um desafio ao jogador — porém podem ser derrotados quando saltamos em cima deles.

O sistema de pontuação consiste em coletar gemas, o que gera pontos e incentiva o jogador a percorrer o mapa todo.

---

## 🏗️ Arquitetura do Projeto

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

## 📁 Estrutura de Pastas

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
├── Game/                        ← classes principais
└── Content/                     ← assets do jogo
```

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

#### `Content/` — Assets do Jogo

```
Content/
├── Levels/
│   ├── 0.txt                → Nível 0 (formato ASCII)
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
│   └── Layer2.png           → Camada da frente (EntityLayer)
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

| Projeto | Plataforma | Ponto de Entrada | Notas |
|---|---|---|---|
| `Platformer2D.DesktopGL/` | Windows, Linux, macOS | `Program.cs` | OpenGL; build com .NET |
| `Platformer2D.Android/` | Android | `MainActivity.cs` | Inclui VirtualGamePad |
| `Platformer2D.iOS/` | iPhone / iPad | `AppDelegate.cs` | Requer build em Mac |
| `Platformer2D.WindowsDX/` | Windows | `Program.cs` | DirectX; melhor performance |

---

### `Documentation/` — Tutoriais

```
Documentation/
├── 1_basic_platformer_features.md        → Gem, Circle, Tile
├── 2_intermediate_platformer_features.md → AnimationPlayer, Enemy, Player, VirtualGamePad
├── 3_advanced_platformer_features.md     → Level, PlatformerGame, RectangleExtensions
└── 3_adding_a_scrolling_level.md         → Extensão: parallax scrolling
```

---

## ▶️ Fluxo de Execução

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

## 🧩 Classes e Responsabilidades

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

Física simplificada: queda, salto e deteção de colisões dinâmicas. Aceita input de teclado, gamepad físico e VirtualGamePad (ecrã táctil).

### `Enemy.cs`

Comportamento padrão: patrulha uma plataforma de um lado ao outro. Inverte direção quando deteta um tile intransponível ou está prestes a cair da aresta da plataforma.

### `RectangleExtensions.cs`

Extensão da estrutura `Rectangle` do MonoGame. Implementa dois métodos que tratam o retângulo como um "círculo quadrado" com dois raios, facilitando a deteção de colisão entre entidades.

### `AnimationPlayer.cs`

Armazena uma spritesheet em `Animation` e reproduz-a frame a frame. Se a animação pedida já estiver a tocar, o método `PlayAnimation()` retorna imediatamente — evitando reinícios indesejados.

---

## 🗺️ Formato dos Níveis

Os níveis são ficheiros `.txt` em ASCII em `Content/Levels/`. Cada caractere representa uma célula da grelha:

| Caractere | Significado |
|---|---|
| `.` | Espaço vazio |
| `#` | Tile sólido (bloco) |
| `X` | Saída do nível |
| `G` | Gem colecionável |
| `A` | Inimigo MonsterA |
| `B` | Inimigo MonsterB |
| `C` | Inimigo MonsterC |
| `D` | Inimigo MonsterD |
| `1` | Ponto de início do jogador |
| `-` | Plataforma flutuante |
| `~` | Bloco de plataforma (variante) |

**Exemplo de mapa (nível 0):**

```
..............................................................................
..............................................................................
...........................G..............................................X....
..........................###.....................................############
```

> 💡 Para adicionar novos níveis: criar `N.txt` (numeração sequencial) em `Content/Levels/` e garantir que a propriedade *Copy to Output Directory* está ativa.

---

## 🎬 Sistema de Animação

Cada entidade tem as suas animações definidas como spritesheets. O `AnimationPlayer` avança o frame com base no tempo decorrido.

**Animações do jogador (5):** `Idle`, `Run`, `Jump`, `Celebrate`, `Die`

**Animações de cada inimigo (3):** `Run`, `Idle`, `Die`

> A animação `Die` dos inimigos está incluída mas não é utilizada na versão base — reservada para desenvolvimento futuro.

> ⚠️ **Requisito:** os frames de animação têm de ser **quadrados** (largura = altura).

---

## ✅ Funcionalidades

- Suporte cross-platform: Windows, Android, iOS, Linux
- Controlo por teclado, gamepad e gamepad virtual (ecrã táctil)
- Física simples: queda e salto com deteção de colisões dinâmica
- Assets de produção: spritesheets, efeitos sonoros e fundos com múltiplas camadas
- Resolução dupla: assets de alta e baixa resolução incluídos
- Projeto de conteúdo unificado que compila para cada plataforma alvo

---

## 🔍 Análise Completa dos Códigos

---

### `Game.cs` — PlatformerGame

**299 linhas.** Classe principal e orquestradora. Herda de `Microsoft.Xna.Framework.Game`.

#### Campos declarados

```csharp
private GraphicsDeviceManager graphics;
private SpriteBatch spriteBatch;
Vector2 baseScreenSize = new Vector2(800, 480);  // resolução base fixa
private Matrix globalTransformation;              // matriz de escala para adaptar ao ecrã
int backbufferWidth, backbufferHeight;

private SpriteFont hudFont;
private Texture2D winOverlay, loseOverlay, diedOverlay;  // overlays de fim de jogo

private int levelIndex = -1;   // começa em -1 para que o primeiro LoadNextLevel carregue o índice 0
private Level level;
private bool wasContinuePressed;

private const int numberOfLevels = 3;  // número total de níveis hard-coded
```

O `levelIndex` começa em `-1` propositadamente. Quando `LoadNextLevel()` é chamado pela primeira vez executa `levelIndex = (levelIndex + 1) % numberOfLevels`, resultando em `0`. É um truque simples para não duplicar código de inicialização.

#### `ScalePresentationArea()` — adaptação de resolução

Calcula uma `Matrix` de escala proporcional à diferença entre o backbuffer real e os 800×480 base. Todo o `SpriteBatch.Begin` usa esta matriz, o que significa que o jogo escala automaticamente para qualquer resolução sem alterar uma linha de lógica de jogo.

```csharp
float horScaling = backbufferWidth / baseScreenSize.X;
float verScaling = backbufferHeight / baseScreenSize.Y;
globalTransformation = Matrix.CreateScale(new Vector3(horScaling, verScaling, 1));
```

#### `HandleInput()` — input de alto nível

Recolhe todos os estados de input uma vez por frame e decide a ação de alto nível. A variável `wasContinuePressed` evita que manter a tecla premida avance múltiplos estados — só atua na transição de não-premido para premido.

```csharp
bool continuePressed =
    keyboardState.IsKeyDown(Keys.Space) ||
    gamePadState.IsButtonDown(Buttons.A) ||
    touchState.AnyTouch();

if (!wasContinuePressed && continuePressed)
{
    if (!level.Player.IsAlive)        level.StartNewLife();
    else if (level.TimeRemaining == TimeSpan.Zero)
    {
        if (level.ReachedExit)        LoadNextLevel();
        else                          ReloadCurrentLevel();
    }
}
wasContinuePressed = continuePressed;
```

#### `LoadNextLevel()` — carregamento de nível

```csharp
levelIndex = (levelIndex + 1) % numberOfLevels;
if (level != null) level.Dispose();  // liberta conteúdo do nível anterior
string levelPath = string.Format("Content/Levels/{0}.txt", levelIndex);
using (Stream fileStream = TitleContainer.OpenStream(levelPath))
    level = new Level(Services, fileStream, levelIndex);
```

O uso de `TitleContainer.OpenStream` em vez de `File.Open` é deliberado — funciona em todas as plataformas incluindo Android e iOS onde o sistema de ficheiros é sandboxed.

#### `Draw()` + `DrawHud()`

O `Draw` principal é simples: limpa o ecrã, abre o `SpriteBatch` com a matriz global, delega em `Level.Draw` e chama `DrawHud`. O HUD tem lógica de piscar quando o tempo está a acabar:

```csharp
if (level.TimeRemaining > WarningTime ||
    level.ReachedExit ||
    (int)level.TimeRemaining.TotalSeconds % 2 == 0)
    timeColor = Color.Yellow;
else
    timeColor = Color.Red;
```

`DrawShadowedString` é um padrão clássico: desenha o texto primeiro a preto com offset de 1px, depois a cor pretendida — cria uma sombra sem necessidade de shaders.

```csharp
private void DrawShadowedString(SpriteFont font, string value, Vector2 position, Color color)
{
    spriteBatch.DrawString(font, value, position + new Vector2(1.0f, 1.0f), Color.Black);
    spriteBatch.DrawString(font, value, position, color);
}
```

---

### `Level.cs`

**551 linhas.** A classe mais complexa do projeto. Implementa `IDisposable` porque tem um `ContentManager` próprio que precisa de ser libertado explicitamente quando o nível termina.

#### Estado interno

```csharp
private Tile[,] tiles;          // grelha 2D de tiles
private Texture2D[] layers;     // 3 texturas de fundo
private const int EntityLayer = 2;  // entidades desenhadas acima das 2 primeiras camadas

private List<Gem> gems;
private List<Enemy> enemies;
private Vector2 start;
private Point exit = InvalidPosition;

// seed constante — garante que o mesmo nível tem sempre a mesma variação visual
private Random random = new Random(354668);

private TimeSpan timeRemaining;
private const int PointsPerSecond = 5;
```

O `Random` com seed fixa é uma decisão de design importante: garante reprodutibilidade dos níveis — os tiles com variação visual são sempre os mesmos em cada execução.

#### `LoadTiles()` — leitura e validação do mapa

Lê o ficheiro `.txt` e valida que todas as linhas têm o mesmo comprimento. Depois percorre cada caractere e chama `LoadTile(char, x, y)`:

```csharp
switch (tileType) {
    case '.': return new Tile(null, TileCollision.Passable);
    case 'X': return LoadExitTile(x, y);
    case 'G': return LoadGemTile(x, y);
    case '-': return LoadTile("Platform", TileCollision.Platform);
    case 'A': return LoadEnemyTile(x, y, "MonsterA");
    case 'B': return LoadEnemyTile(x, y, "MonsterB");
    case 'C': return LoadEnemyTile(x, y, "MonsterC");
    case 'D': return LoadEnemyTile(x, y, "MonsterD");
    case '~': return LoadVarietyTile("BlockB", 2, TileCollision.Platform);
    case ':': return LoadVarietyTile("BlockB", 2, TileCollision.Passable);
    case '1': return LoadStartTile(x, y);
    case '#': return LoadVarietyTile("BlockA", 7, TileCollision.Impassable);
    default:  throw new NotSupportedException(...);
}
```

O método termina com validações explícitas — se não existir ponto de início ou saída, lança `NotSupportedException`. É design defensivo: falha rápido com uma mensagem clara em vez de o jogo crashar misteriosamente mais tarde.

#### `LoadVarietyTile()` — variação visual determinística

```csharp
private Tile LoadVarietyTile(string baseName, int variationCount, TileCollision collision)
{
    int index = random.Next(variationCount);
    return LoadTile(baseName + index, collision);
}
```

#### `Update()` — máquina de estados central

```csharp
if (!Player.IsAlive || TimeRemaining == TimeSpan.Zero)
{
    // física continua mesmo morto — o jogador cai naturalmente
    Player.ApplyPhysics(gameTime);
}
else if (ReachedExit)
{
    // converte tempo restante em pontos com animação gradual
    int seconds = (int)Math.Round(gameTime.ElapsedGameTime.TotalSeconds * 100.0f);
    seconds = Math.Min(seconds, (int)Math.Ceiling(TimeRemaining.TotalSeconds));
    timeRemaining -= TimeSpan.FromSeconds(seconds);
    score += seconds * PointsPerSecond;
}
else
{
    timeRemaining -= gameTime.ElapsedGameTime;
    Player.Update(...);
    UpdateGems(gameTime);

    if (Player.BoundingRectangle.Top >= Height * Tile.Height)
        OnPlayerKilled(null);  // cair do ecrã mata o jogador

    UpdateEnemies(gameTime);

    if (Player.IsAlive && Player.IsOnGround && Player.BoundingRectangle.Contains(exit))
        OnExitReached();  // condição de vitória
}
```

#### `UpdateGems()` — remoção segura durante iteração

```csharp
for (int i = 0; i < gems.Count; ++i)
{
    Gem gem = gems[i];
    gem.Update(gameTime);
    if (gem.BoundingCircle.Intersects(Player.BoundingRectangle))
    {
        gems.RemoveAt(i--);  // decrementa i para não saltar o próximo elemento
        OnGemCollected(gem, Player);
    }
}
```

#### `Draw()` — ordem de renderização em camadas

```csharp
// 1. Camadas de fundo (índices 0 e 1)
for (int i = 0; i <= EntityLayer; ++i)
    spriteBatch.Draw(layers[i], Vector2.Zero, Color.White);

// 2. Tiles do nível
DrawTiles(spriteBatch);

// 3. Entidades (gems, jogador, inimigos) — EntityLayer = 2
foreach (Gem gem in gems)        gem.Draw(gameTime, spriteBatch);
Player.Draw(gameTime, spriteBatch);
foreach (Enemy enemy in enemies) enemy.Draw(gameTime, spriteBatch);

// 4. Camadas da frente (índice 3+, se existirem)
for (int i = EntityLayer + 1; i < layers.Length; ++i)
    spriteBatch.Draw(layers[i], Vector2.Zero, Color.White);
```

---

### `Player.cs`

**447 linhas.** A classe mais matemática do projeto, com um sistema de física completo implementado de raiz.

#### Constantes de física

```csharp
// Movimento horizontal
private const float MoveAcceleration   = 13000.0f;  // aceleração
private const float MaxMoveSpeed       = 1750.0f;   // velocidade máxima
private const float GroundDragFactor   = 0.48f;     // atrito no chão (travagem rápida)
private const float AirDragFactor      = 0.58f;     // atrito no ar (travagem mais lenta)

// Salto
private const float MaxJumpTime         = 0.35f;    // duração máxima do salto
private const float JumpLaunchVelocity  = -3500.0f; // velocidade inicial (negativa = para cima)
private const float GravityAcceleration = 3400.0f;  // gravidade
private const float MaxFallSpeed        = 550.0f;   // velocidade máxima de queda
private const float JumpControlPower    = 0.14f;    // curvatura da curva de controlo
```

Todas as constantes são ajustáveis sem tocar na lógica. É aqui que um designer alteraria o "feel" do jogo.

#### `GetInput()` — prioridade de input

Recolhe input com três camadas de prioridade: analógico → acelerómetro → digital. O input digital sobrepõe-se sempre por ser mais preciso. O acelerómetro tem dead zone de `0.10` para evitar drift em dispositivos móveis:

```csharp
// analógico
movement = gamePadState.ThumbSticks.Left.X * MoveStickScale;
if (Math.Abs(movement) < 0.5f) movement = 0.0f;  // dead zone

// acelerómetro (dispositivos móveis)
if (Math.Abs(accelState.Acceleration.Y) > 0.10f)
    movement = MathHelper.Clamp(-accelState.Acceleration.Y * AccelerometerScale, -1f, 1f);

// digital (sobrepõe-se ao analógico)
if (keyboardState.IsKeyDown(Keys.Left) || keyboardState.IsKeyDown(Keys.A))
    movement = -1.0f;
else if (keyboardState.IsKeyDown(Keys.Right) || keyboardState.IsKeyDown(Keys.D))
    movement = 1.0f;
```

#### `ApplyPhysics()` — núcleo da física

```csharp
float elapsed = (float)gameTime.ElapsedGameTime.TotalSeconds;
Vector2 previousPosition = Position;

// aceleração horizontal + gravidade
velocity.X += movement * MoveAcceleration * elapsed;
velocity.Y = MathHelper.Clamp(velocity.Y + GravityAcceleration * elapsed,
                               -MaxFallSpeed, MaxFallSpeed);

velocity.Y = DoJump(velocity.Y, gameTime);  // salto override do eixo Y

// drag diferente consoante no chão ou no ar
velocity.X *= IsOnGround ? GroundDragFactor : AirDragFactor;
velocity.X = MathHelper.Clamp(velocity.X, -MaxMoveSpeed, MaxMoveSpeed);

// snap a pixel inteiro — evita jitter visual
Position += velocity * elapsed;
Position = new Vector2((float)Math.Round(Position.X), (float)Math.Round(Position.Y));

HandleCollisions();

// se colisão parou o movimento, zera velocidade
if (Position.X == previousPosition.X) velocity.X = 0;
if (Position.Y == previousPosition.Y) velocity.Y = 0;
```

#### `DoJump()` — curva de potência para controlo do salto

```csharp
private float DoJump(float velocityY, GameTime gameTime)
{
    if (isJumping)
    {
        if ((!wasJumping && IsOnGround) || jumpTime > 0.0f)
        {
            if (jumpTime == 0.0f) jumpSound.Play();
            jumpTime += (float)gameTime.ElapsedGameTime.TotalSeconds;
            sprite.PlayAnimation(jumpAnimation);
        }

        if (0.0f < jumpTime && jumpTime <= MaxJumpTime)
        {
            // curva de potência: mais controlo no topo do salto
            velocityY = JumpLaunchVelocity *
                        (1.0f - (float)Math.Pow(jumpTime / MaxJumpTime, JumpControlPower));
        }
        else
        {
            jumpTime = 0.0f;  // atingiu o máximo, gravidade retoma
        }
    }
    else
    {
        jumpTime = 0.0f;  // soltar o botão cancela o salto
    }

    wasJumping = isJumping;
    return velocityY;
}
```

Soltar o botão cedo resulta num salto mais baixo — comportamento idêntico ao de jogos como Super Mario Bros.

#### `HandleCollisions()` — deteção e resolução tile a tile

```csharp
int leftTile   = (int)Math.Floor((float)bounds.Left / Tile.Width);
int rightTile  = (int)Math.Ceiling(((float)bounds.Right / Tile.Width)) - 1;
int topTile    = (int)Math.Floor((float)bounds.Top / Tile.Height);
int bottomTile = (int)Math.Ceiling(((float)bounds.Bottom / Tile.Height)) - 1;
```

Para cada tile potencialmente colidível, usa `RectangleExtensions.GetIntersectionDepth` para obter a profundidade de interseção em X e Y. A resolução é feita pelo eixo mais raso, o que produz comportamento correto em cantos.

Para `Platform` tiles, a colisão só é resolvida se o jogador estava acima (`previousBottom <= tileBounds.Top`), permitindo saltar através da plataforma por baixo mas aterrar em cima.

#### `Draw()` — flip horizontal baseado na velocidade

```csharp
if (Velocity.X > 0)      flip = SpriteEffects.FlipHorizontally;
else if (Velocity.X < 0) flip = SpriteEffects.None;
// se velocity == 0: mantém a última direção
sprite.Draw(gameTime, spriteBatch, Position, flip);
```

---

### `Animation.cs` e `AnimationPlayer.cs`

#### `Animation`

Estrutura de dados simples: guarda a `Texture2D` da spritesheet, o tempo por frame (`FrameTime`), se é loop (`IsLooping`), e calcula `FrameCount` e `FrameWidth` assumindo frames quadrados:

```csharp
public int FrameCount  => Texture.Width / FrameWidth;
public int FrameWidth  => Texture.Height;  // frames são quadrados: largura = altura
```

#### `AnimationPlayer`

Motor de reprodução de animações. A proteção contra interrupção evita que a mesma animação seja reiniciada:

```csharp
public void PlayAnimation(Animation animation)
{
    if (Animation == animation) return;  // não interrompe a mesma animação
    this.animation = animation;
    this.frameIndex = 0;
    this.time = 0.0f;
}
```

No `Draw`, avança o tempo e calcula o frame atual com suporte a loop e one-shot:

```csharp
time += (float)gameTime.ElapsedGameTime.TotalSeconds;
while (time > animation.FrameTime)
{
    time -= animation.FrameTime;
    if (animation.IsLooping)
        frameIndex = (frameIndex + 1) % animation.FrameCount;
    else
        frameIndex = Math.Min(frameIndex + 1, animation.FrameCount - 1);
}
```

O `Origin` é o centro do frame, usado para posicionar o sprite corretamente em relação à posição do jogo.

---

### `Gem.cs` e `Circle.cs`

#### `Gem` — animação sinusoidal

A gem tem uma animação de flutuação baseada em onda sinusoidal. A posição X é usada como offset de fase, fazendo com que cada gem oscile ligeiramente desfasada das outras:

```csharp
float t = (float)gameTime.TotalGameTime.TotalSeconds + position.X;
bounce = (float)Math.Sin(t * BounceRate) * BounceHeight * BounceSync;
```

Este detalhe faz com que as gems pareçam "respirar" de forma orgânica — um efeito elegante com apenas 3 linhas de código.

#### `Circle` — deteção de colisão eficiente

Struct de colisão usada pelas gems. O método `Intersects` usa `DistanceSquared` em vez de `Distance` para evitar o cálculo de uma raiz quadrada — micro-otimização correta para código que corre em cada frame para cada gem:

```csharp
public bool Intersects(Rectangle rectangle)
{
    Vector2 closest = Vector2.Clamp(Center,
        new Vector2(rectangle.Left, rectangle.Top),
        new Vector2(rectangle.Right, rectangle.Bottom));

    return Vector2.DistanceSquared(Center, closest) < Radius * Radius;
}
```

---

### `Tile.cs` e `TileCollision`

`Tile` é uma `struct` (tipo de valor) com apenas dois campos: `Texture2D Texture` e `TileCollision Collision`. A escolha de `struct` é intencional — o array `Tile[,]` fica em memória contígua, sem overhead de garbage collection por objeto.

```csharp
public enum TileCollision
{
    Passable,    // sem colisão (espaço vazio, gem, início, saída)
    Impassable,  // bloco sólido em todas as direções
    Platform     // sólido apenas por cima (pode saltar por baixo)
}
```

`Platform` é o tipo mais interessante: só impede a passagem se o jogador vinha de cima, permitindo saltar por baixo.

---

### `RectangleExtensions.cs`

O método mais matemático do projeto. Implementa dois métodos de extensão para a struct `Rectangle` do MonoGame.

#### `GetIntersectionDepth()` — profundidade de interseção com direção

```csharp
public static Vector2 GetIntersectionDepth(Rectangle rectA, Rectangle rectB)
{
    Vector2 centerA = new Vector2(rectA.Center.X, rectA.Center.Y);
    Vector2 centerB = new Vector2(rectB.Center.X, rectB.Center.Y);

    Vector2 halfSizeA = new Vector2(rectA.Width / 2.0f, rectA.Height / 2.0f);
    Vector2 halfSizeB = new Vector2(rectB.Width / 2.0f, rectB.Height / 2.0f);

    float distanceX = centerA.X - centerB.X;
    float distanceY = centerA.Y - centerB.Y;

    float minDistanceX = halfSizeA.X + halfSizeB.X;
    float minDistanceY = halfSizeA.Y + halfSizeB.Y;

    if (Math.Abs(distanceX) >= minDistanceX || Math.Abs(distanceY) >= minDistanceY)
        return Vector2.Zero;  // sem interseção

    float depthX = distanceX > 0 ? minDistanceX - distanceX : -minDistanceX - distanceX;
    float depthY = distanceY > 0 ? minDistanceY - distanceY : -minDistanceY - distanceY;
    return new Vector2(depthX, depthY);
}
```

O sinal do resultado diz a direção em que separar os objetos: positivo empurra para direita/baixo, negativo para esquerda/cima. É este valor que o `HandleCollisions` do `Player` usa para resolver penetrações.

#### `GetBottomCenter()` — centro da base de um tile

Método utilitário que devolve o ponto central da base de um retângulo — usado para posicionar o jogador e os inimigos no chão quando são instanciados.

---

### `Enemy.cs`

Comportamento simples mas bem implementado. O inimigo patrulha em linha reta e inverte a direção quando deteta obstáculo à frente ou borda de plataforma:

```csharp
TileCollision tileInFront  = level.GetCollision(nextTileX, tileY);
TileCollision tileOnGround = level.GetCollision(nextTileX, tileY + 1);

if (tileInFront == TileCollision.Impassable || tileOnGround == TileCollision.Passable)
    direction = (FaceDirection)(-(int)direction);  // inverte direção
```

A inversão por cast para `int` e negação funciona porque `FaceDirection` é `{ Left = -1, Right = 1 }` — um truque numérico limpo que evita um `if/else`.

```csharp
private void LoadContent(string spriteSet)
{
    spriteSet = "Sprites/" + spriteSet + "/";
    runAnimation  = new Animation(Level.Content.Load<Texture2D>(spriteSet + "Run"),  0.1f, true);
    idleAnimation = new Animation(Level.Content.Load<Texture2D>(spriteSet + "Idle"), 0.1f, true);
    // Die carregado mas não usado — reservado para desenvolvimento futuro
}
```

---

## 🧠 Padrões de Design Observados

### 1. Componente auto-suficiente

Cada entidade tem `LoadContent`, `Update` e `Draw` próprios. O `Level` apenas os invoca; não conhece os detalhes internos de nenhuma delas. Adicionar um novo tipo de entidade não requer alterar o `Level`.

### 2. Gestão de memória explícita

`Level` implementa `IDisposable` e tem o seu próprio `ContentManager`. Quando o nível termina, `level.Dispose()` liberta toda a memória dos assets desse nível sem afetar os assets globais do `PlatformerGame`.

### 3. Input centralizado e passado para baixo

O `PlatformerGame` recolhe todos os estados de input uma única vez por frame e passa-os como parâmetros para `Level.Update`, que os passa para `Player.Update`. Nenhuma entidade chama `Keyboard.GetState()` diretamente.

### 4. Separação de dados e lógica

`Tile` e `Animation` são structs de dados puros. `AnimationPlayer`, `Player`, `Enemy` são as classes que contêm a lógica. A separação torna o código mais fácil de ler e de substituir partes independentemente.

### 5. Constantes nomeadas no topo das classes

Todas as constantes de física, timing e gameplay estão nomeadas e agrupadas. Não existe nenhum "magic number" no meio da lógica:

```csharp
private const float MoveAcceleration    = 13000.0f;
private const float MaxMoveSpeed        = 1750.0f;
private const float GroundDragFactor    = 0.48f;
private const float JumpLaunchVelocity  = -3500.0f;
private const float GravityAcceleration = 3400.0f;
```

### 6. Design defensivo (fail fast)

`LoadTiles` valida que o mapa tem início e saída. `LoadStartTile` valida que não há dois inícios. Se algo estiver errado no ficheiro de nível, o jogo lança uma exceção com mensagem descritiva imediatamente.

### 7. Otimizações micro mas corretas

- `Circle.Intersects` usa `DistanceSquared` em vez de `Distance` — evita raiz quadrada em código de colisão executado por frame
- `Tile` é `struct` para garantir memória contígua no array da grelha
- `AnimationPlayer.PlayAnimation` não reinicia se a animação já está a tocar
- `Math.Round` na posição do jogador previne jitter visual sub-pixel

### 8. Seed constante no Random

O `Random` do `Level` usa sempre a mesma seed (`354668`), garantindo que a variação visual dos tiles é determinística. O mesmo nível tem sempre o mesmo aspeto visual, independentemente de quantas vezes for carregado.

---
