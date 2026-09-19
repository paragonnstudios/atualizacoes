# Audit — Paragonn

Atualizado em 19/09/2026.

**Legenda:** ✅ verificado · 🟡 aguardando teste · ⬜ pendente · ⚠️ precisa de decisão

**Resumo:** 18 verificadas · 4 pendentes · 2 aguardando teste

> **Para o dev que vai colocar em produção:** tudo abaixo foi feito e testado no ambiente **local**
> (`c:\paragonn`). A tabela resume; **Jars que mudaram** lista o que copiar; **Detalhes por tarefa**
> diz o que mudou, como configurar e como testar; **Deploy em produção** tem o passo a passo e o que
> NÃO replicar.

---

## LOBBY

| Código | Tarefa |
|--------|--------|
| L1 ⬜ | Renomear as Cápsulas do Lobby para Caixas. |
| L2 ⬜ | Reformular o menu de Cosméticos. |

## BOSS

| Código | Tarefa |
|--------|--------|
| B1 ✅ | Mob que cair no void volta para dentro da arena. |
| B2 ✅ | Menu de missões que libera uma permissão ao completar (configurável). |
| B3 ✅ | Spawner gerar vários mobs juntos, não 1 por vez. |
| B4 ✅ | Holograma com mobs mortos, top boss e top mobs da arena. |

## RUNAS

| Código | Tarefa |
|--------|--------|
| R1 ✅ | Armadura com runa não cai na morte; só se perde se quebrar em PvP. |
| R2 ✅ | Limite de runas por peça e bloqueio de runas em peças específicas. |
| R3 ✅ | Runas equipáveis em ferramentas. |

## PETS

| Código | Tarefa |
|--------|--------|
| P1 ✅ | Missões dos pets resetarem em vez de completar só 1 vez. |
| P2 ✅ | Menu de confirmação antes de comprar o pet. |
| P3 ✅ | Pet não sumir ao morrer. |

## CLAN

| Código | Tarefa |
|--------|--------|
| C1 ✅ | Corrigir a tag do clan no tab e sobre a cabeça do jogador. |

## HARDUP

| Código | Tarefa |
|--------|--------|
| H1 ✅ | Top ranks mostrarem quem tem o rank mais alto. |

## PLUGINS NOVOS

| Código | Tarefa |
|--------|--------|
| N1 ⬜ | Plugin que mostra o tempo restante de uma permissão. |
| N2 ⚠️ | Criar o plugin ApBlock (não existe no projeto; falta a descrição). |
| N3 ✅ | Armazém aceitar novos itens de farm (NexusArmazemV2 corrigido). |

## EXTRAS

| Código | Tarefa |
|--------|--------|
| E1 ✅ | Core conectar ao MySQL no hardup (nome do banco corrigido). |
| E2 ✅ | Pesca reabrir a conexão MySQL que caía por ociosidade. |
| E3 ✅ | Armazém sem lag (`SQLITE_BUSY` corrigido). |
| E4 ✅ | Armazém contar plantações que crescem (cana e outras, configurável por item). |
| E5 ✅ | Espaço vazio depois da tag do clan no tablist. |
| E6 🟡 | Pet fica parado para o jogador olhar para ele (só segue quando o jogador se afasta). |
| E7 🟡 | Menu de missões dos pets sem os vidros de preenchimento. |

---

# Jars que mudaram

São **8 plugins** (10 arquivos, porque o Core vai para 3 servidores). Origem: `paragonn-sources\plugins\<Plugin>\build\libs\`
(os jars abaixo são **idênticos** aos que estão implantados no servidor local, conferido por tamanho).
Data/hora = quando foi implantado no servidor local em 19/09/2026.

| Jar | Origem (build\libs) | Destino | Tamanho | Implantado | Tarefas |
|-----|---------------------|---------|---------|------------|---------|
| `Boss.jar` | `Boss\build\libs\Boss.jar` | `hardup\plugins\` | 8.478.703 | 16:15 | B1, B2, B3, B4 |
| `Missions.jar` | `Missions\build\libs\Missions.jar` | `hardup\plugins\` | 8.662.956 | 15:31 | P1, E7 |
| `Companions.jar` | `Companions\build\libs\Companions.jar` | `hardup\plugins\` | 6.988.801 | 14:53 | P2, P3, E6 |
| `Core.jar` | `Core\build\libs\Core.jar` | `hardup\plugins\`, `lobby\plugins\` e `proxy\plugins\` | 22.289.674 | 14:20 | E5 |
| `Runes.jar` | `Runes\build\libs\Runes.jar` | `hardup\plugins\` | 8.366.960 | 14:11 | R1, R2, R3 |
| `Pesca.jar` | `Pesca\build\libs\Pesca.jar` | `hardup\plugins\` | 59.130 | 14:11 | E2 |
| `NexusArmazemV2.jar` | `NexusArmazemV2\build\libs\NexusArmazemV2.jar` | `hardup\plugins\` | 242.325 | 14:10 | N3, E3, E4 |
| `Hardup.jar` | `Hardup\build\libs\Hardup.jar` | `hardup\plugins\` | 109.705 | 14:10 | H1 |

**Atenção ao copiar:**
- Use o arquivo com o **nome simples** (`Hardup.jar`, `Core.jar`...). Em `build\libs` há também `Hardup-0.0.0-SNAPSHOT.jar`, `Core-1.14.5.jar`, `paragonn-companions.jar` e `paragonn-pesca.jar`: são artefatos antigos/sem dependências, **não** são os que foram implantados.
- Tamanhos grandes são normais: `Boss`, `Missions` e `Runes` embutem o H2; o `Core` embute várias bibliotecas.
- **`NexusArmazemV2.jar` substitui o jar de terceiros** (que estava infectado por malware): é o projeto recompilado do zero, sem o loader do malware. Não reaproveite o jar antigo.
- **Não mudaram** (não precisa copiar): `Almas`, `Bans`, `Pix`, `Eventos`, `System`, `Utils`, `SimpleClans` e os demais.

---

# Detalhes por tarefa

Convenções: **Onde** = código-fonte em `paragonn-sources\plugins\<Plugin>` e arquivos de servidor em
`paragonn-server\hardup\plugins\<Plugin>`. **Testar** = passo a passo no jogo.

## B1 — Mob que cai no void volta para a arena
- **Jar:** `Boss.jar`.
- **Mudou:** `MobSpawnService#rescueFallenMobs` + nova `ArenaVoidRescueTask` (Boss). Varre os mundos a cada 10 ticks; todo mob de spawner **rastreado** que estiver abaixo do limite inferior da arena (`pos1Y - 1`) é teleportado para um spawner aleatório da arena (ou para o spawn dela), com a queda e a velocidade zeradas. O **boss** já voltava sozinho pelo `ArenaLeashTask` (não mudou).
- **Configurar** (`Boss\config.yml`, seção `general`; se a chave não existir vale o padrão `true`/`10`):
  `void-rescue-enabled: true` e `void-rescue-interval-ticks: 10`.
- **Testar:** numa arena com spawners, deixe os mobs nascerem e faça um cair abaixo do piso da arena. Em até ~0,5 s ele reaparece sobre um spawner.

## B2 — Menu de missões do Boss (libera permissão)
- **Jar:** `Boss.jar`.
- **Mudou:** novo pacote `studio.paragonn.boss.extras` (`BossMissionService`, `BossMissionsMenu`, `ArenaStatsStore`, `BossKillHooks`) e subcomando **`/boss missoes <arena>`**. **As missões são POR ARENA:** cada missão pertence a uma arena (`arena:` obrigatório) e só conta mortes feitas nela; o jogador mata mobs de spawner/bosses (filtro opcional por boss ou tipo de mob); ao completar roda um **comando de console** que concede a permissão e avisa no chat. Menu no estilo do menu dos pets: 5 linhas, livro no slot 4, sem vidros, ✔ + brilho ao concluir, máximo de **21 missões por arena** (sem paginação). O comando **exige a arena** (nome de `/boss arena list`, com tab-complete das arenas que têm missões): abre as missões **daquela** arena; sem argumento só mostra o uso e lista as arenas com missões; arena inexistente ou sem missões avisa no chat.
- **Permissão só aparece para dev:** a linha `[dev] libera: <permissão>` só é mostrada a quem tem `paragonnboss.dev`, que é `default: false` (**nem OP recebe sozinho**). Jogadores não veem nada sobre permissão.
- **Configurar** (`Boss\missions.yml`, criado sozinho na 1ª execução; depois de editar use `/boss reload`):
  - `permission-command`: comando de console que concede a permissão. Padrão LuckPerms: `lp user {player} permission set {permission} true` (placeholders `{player}`, `{permission}`, `{mission}`).
  - `menu-title`, `messages.completed`, `messages.permission`.
  - Por missão: **`arena` (obrigatório** — nome exato de `/boss arena list`; missão sem `arena` é ignorada e o log avisa; se a arena não existir, o log também avisa), `name`, `description`, `category` (padrão `Boss`), `type` (`KILL_MOB` ou `KILL_BOSS`), `amount`, `boss`/`mob` (filtros opcionais), `icon` (Material), `permission`, `commands` (opcional: lista de comandos que **substitui** o `permission-command`).
  - O **id da missão é único no arquivo** (o progresso é guardado por id): para a mesma missão em outra arena, copie com outro id (ex.: `cacador_iniciante_ceifador`).
  - Local: a única arena cadastrada é `ceifador`, e as 3 missões de exemplo apontam para ela. **Em produção troque pelo nome real das arenas** (o arquivo de exemplo vem com `NOME_DA_ARENA`).
  - **As 3 missões do arquivo são exemplos** (`boss.cacador.1`, `boss.cacador.2`, `boss.matador`): troque pelas permissões reais de produção.
- **Banco:** a tabela `<prefixo>boss_missions` (prefixo do Boss, padrão `pboss_`) é criada automaticamente (H2 ou MySQL, conforme o Boss).
- **Testar:** `/boss missoes` (sem argumento mostra o uso e as arenas) e `/boss missoes ceifador` (abre as missões da arena). Mate mobs **fora** da arena da missão e confira que ela **não** avança. Baixe o `amount` de uma missão para 2 e use `/boss reload`. Mate 2 mobs de uma arena: aparece `MISSÃO CONCLUÍDA` e a permissão é concedida (`lp user <nick> permission info`). Confira que, como OP, você **não** vê a linha `[dev]`; para vê-la: `lp user <nick> permission set paragonnboss.dev true`.

## B3 — Spawner solta os mobs de uma vez
- **Jar:** `Boss.jar`.
- **Mudou:** nenhuma linha nova de código — a lógica de "leva completa até o teto" (`MobSpawnService#tick`, round-robin entre os spawners) **já estava no código-fonte, mas o `Boss.jar` do servidor era mais antigo** (09/09 11:42) e nunca foi recompilado. O que resolveu foi **compilar e implantar o Boss atual**.
- **Comportamento:** por arena, a cada `interval-seconds` o spawner solta todos os mobs que faltam até o `mob-cap` de uma vez (não 1 por spawner). Depois de matar mobs, a reposição vem na leva seguinte.
- **Configurar** (`Boss\bosses.yml`, seção `spawner` de cada boss): `interval-seconds`, `mob-cap`, `entity-type`, `health`, `kills-to-spawn`. Baixe `interval-seconds` se quiser reposição mais rápida.
- **Testar:** numa arena com spawners, veja os mobs encherem até o teto de uma só vez; mate alguns e espere o intervalo — repõe o que faltou de uma vez.

## B4 — Holograma da arena
- **Jar:** `Boss.jar`.
- **Mudou:** `ArenaHologramManager` + `ArenaStatsStore` + subcomando `/boss holograma`. Mostra (por arena) o **total de mobs mortos**, o **top de bosses** e o **top de mobs** (nome + quantidade). Feito com ArmorStands invisíveis (sem dependência de outro plugin); apaga linhas órfãs no boot (marcador invisível no nome).
- **Contagem:** por arena, e só conta quando um **jogador** dá o golpe final. **Começa do zero no deploy** (não existe histórico por arena; o ranking global antigo `/boss stats` continua como estava).
- **Comandos** (`paragonnboss.admin`): `/boss holograma set <arena>` (fica na sua posição), `/boss holograma remove <arena>`, `/boss holograma list`.
- **Configurar** (`Boss\holograms.yml`): `top-size` (1-10, padrão 3), `refresh-seconds` (padrão 10), textos em `format.*` (`title`, `total`, `boss-header`, `mob-header`, `entry`, `empty`). As posições ficam na chave `holograms:` (gravadas pelo próprio plugin).
- **Banco:** tabela `<prefixo>arena_stats` criada automaticamente.
- **Testar:** `/boss holograma set <arena>`; mate mobs na arena e espere ~10 s: o "Mobs mortos" e o ranking sobem. Reinicie o servidor: o holograma volta sem duplicar.

## R1, R2, R3 — Runas
- **Jar:** `Runes.jar`.
- **Mudou:**
  - **R1:** armadura com runa aplicada sai dos drops na morte e volta ao renascer (para o slot certo, ou inventário se ocupado). Se o jogador sair na tela de morte ou o servidor reiniciar, é devolvida no inventário. Só se perde se quebrar.
  - **R2:** limite total **por peça** (antes era um só para todas) e bloqueio por peça (`allowed-slots`); as mensagens de limite mostram o máximo.
  - **R3:** runas aplicáveis em **ferramentas/armas** (`applicable-to-tool`, `allowed-tools`); o menu de aplicar (`/runaaplicar`) aceita armadura **ou** ferramenta.
- **Configurar:**
  - `Runes\config.yml` → `general.keep-armor-on-death` (padrão `true`), `general.max-runes-per-slot` (`HELMET: 2`, `CHESTPLATE: 3`, `LEGGINGS: 3`, `BOOTS: 2`; usa `max-runes-per-armor` se a peça não estiver listada), `general.max-runes-per-tool` (3), novos `runes.stat-names` (`MINING_HASTE`, `BLOCK_DOUBLE_DROP`) e mensagens `socket-not-applicable-tool`, `socket-wrong-tool` (as de limite aceitam `{max}`).
  - `Runes\runes.yml`: **Vida II** só em CHESTPLATE/LEGGINGS; **Velocidade II** só em BOOTS/LEGGINGS; **Força II** deixou de ser de armadura e passou para **SWORD/AXE** (na armadura só funcionava se a peça estivesse na mão); novas **Runa da Pressa I/II** (`MINING_HASTE`, Pressa enquanto a ferramenta está na mão; PICKAXE/AXE/SHOVEL) e **Runa da Fortuna I/II** (`BLOCK_DOUBLE_DROP`, 5%/12% de chance de drop em dobro; PICKAXE/AXE/SHOVEL/HOE), com receitas de fusão (2 runas I → 1 runa II).
  - **Os números (limites por peça, restrições de Vida/Velocidade) são valores nossos de partida:** ajuste ao balanceamento de produção.
- **Pendente nas runas de ferramenta:** as runas novas de tier I ainda **não dropam de boss** (`Boss\drops.yml`) e não têm textura de cabeça — por enquanto só saem por `/prunes give`.
- **Testar:**
  1. `/prunes give <nick> runa_vida_2 3`, abra `/runaaplicar`: capacete → bloqueado; peitoral → aplica; 3ª runa igual no mesmo item → "limite (max 2)".
  2. Peça com o limite total → "limite total (max N)".
  3. `/prunes give <nick> runa_pressa_2` e `runa_fortuna_2`: picareta aceita; espada bloqueia Pressa; 4ª runa numa ferramenta → "limite total (max 3)". Pressa: ícone de efeito com a picareta na mão. Fortuna: quebre ~50 blocos e conte os drops em dobro (~12%).
  4. `/prunes give <nick> runa_forca_2`: espada/machado aceita, armadura bloqueia.
  5. Morra (`/kill`) com armadura runada equipada e também com uma no inventário: nada dela cai e tudo volta ao renascer.
  6. Fusão: `/prunes give <nick> runa_pressa_1 2` → `/runafusao` → sai 1 runa II.

## P1 — Missões dos pets resetam (reset configurável) + E7
- **Jar:** `Missions.jar`.
- **Mudou (Missions):** o bug era `completed = true` gravado para sempre — missão repetível acumulava progresso mas nunca pagava de novo. Agora, em missão repetível, ao bater a meta paga o XP e **reseta** (guardando o excedente). Missões que já estavam travadas recomeçam sozinhas no próximo progresso (sem mexer no banco). `MissionProgress` ganhou `updatedAt` (usa a coluna `updated_at` que já existia; **sem migração**).
- **Configurar** (`Missions\missions.yml`):
  - `defaults.reset-delay` (padrão `0`) vale para todas; `reset-delay` por missão sobrescreve.
  - Valores: `0`/`instant` = reseta na hora (comportamento pedido); `30s`, `10m`, `2h`, `1d` ou número (segundos) = a missão fica "CONCLUÍDA (reseta em ...)" no menu e só volta depois desse tempo.
  - `repeatable: false` = só uma vez na vida.
  - `Missions\config.yml` → `messages.sem-companheiro`: aviso do `/pmissions` quando o jogador **não tem pet ativo** (o menu não abre).
- **Importante:** o progresso só conta para o **pet ativo**, e só nas missões da lista `missions:` daquele pet em `Companions\companheiros.yml` (ex.: `KILL_COWS` não está na lista de nenhum pet).
- **Testar:** compre a Abelha (`/pet`), ative, abra as missões dela e mate 5 zumbis (`KILL_ZOMBIES`): mensagem `[MISSÃO CONCLUÍDA] ... +1 XP` e o menu volta a `0/5`; mate 5 de novo e paga outra vez. Sem pet ativo, `/pmissions` mostra o aviso.
- **E7 (🟡):** removido o preenchimento com vidros escuros no menu de missões dos pets (`MissionsMenus`); slots vazios ficam vazios. Testar: abrir o menu de missões de um pet.

## P2 — Confirmar a compra do pet
- **Jar:** `Companions.jar`.
- **Mudou (Companions):** clicar num pet **ainda não comprado** abre `ConfirmPurchaseMenu` (pet + preço + CONFIRMAR / CANCELAR); antes comprava direto. Sem saldo, avisa antes de pedir confirmação. `ShopMenu#buy` faz a compra (cobra, entrega, ativa, spawna). Pet já possuído continua ativando com 1 clique.
- **Configurar:** nada (textos no código).
- **Testar:** `/pet` → clique num pet não comprado → tela de confirmação; CANCELAR não cobra; CONFIRMAR cobra, ativa e mostra a mensagem de compra.

## P3 — Pet não some ao morrer
- **Jar:** `Companions.jar`.
- **Mudou (Companions):** o pet era removido na morte e nunca recriado. `PlayerListener` agora guarda quem tinha o pet fora e o re-spawna 10 ticks após o `PlayerRespawnEvent`.
- **Testar:** com pet ativo, `/kill` e renasça: o pet reaparece.

## E6 — Pet parado para o jogador olhar (🟡)
- **Jar:** `Companions.jar`.
- **Mudou (Companions, `CompanionEntity`):** o pet era reposicionado a cada tick a partir da **direção do olhar** do dono (girava junto com a câmera). Agora tem posição própria: fica parado entre ~1,0 e ~2,6 blocos do dono, vai atrás quando o dono se afasta, se afasta se o dono encostar, fica de frente para o dono, e reaparece perto em teleporte/troca de mundo/distância > 16.
- **Configurar:** constantes no código (`FOLLOW_MAX_DISTANCE`, `FOLLOW_MIN_DISTANCE`, `FOLLOW_IDEAL_DISTANCE`, `FOLLOW_SNAP_DISTANCE`). A altura de flutuação continua em `Companions\config.yml`.
- **Testar:** ative um pet, pare e vire a câmera: ele fica parado e de frente; ande/corra: ele acompanha sem ficar para trás.

## C1 — Tag do clan
- **Situação:** marcada como concluída a pedido do responsável; **o SimpleClans não foi alterado**. O espaço vazio depois da tag no tablist foi corrigido no Core (ver E5).

## H1 — Top ranks do Hardup
- **Jar:** `Hardup.jar`.
- **Mudou (Hardup, `TopMenu`):** antes só listava jogadores **online** e ordenava só por prestígio. Agora lê a tabela `paragonn-hardup` inteira (consulta assíncrona), ordena por **prestígio e depois rank**, inclui quem está offline e usa os valores ao vivo de quem está online. Cada cabeça ganhou a linha `Rank`.
- **Configurar:** `Hardup\menus\top.yml` — o lore do item ganhou `{rank}` (linha `&fRank: &7{rank}`); `Slots` define quantas posições aparecem (padrão 7).
- **Testar:** `/rank` → **Top**: devem aparecer os de maior prestígio/rank, inclusive offline, na ordem certa.

## N3 (+ E3, E4) — Armazém (NexusArmazemV2)
- **Jar:** `NexusArmazemV2.jar`.
- **Situação de partida:** plugin de terceiros **sem código-fonte**, e o jar estava infectado por malware. Foi decompilado e virou o projeto `paragonn-sources\plugins\NexusArmazemV2` (Gradle, JDK 8), **sem o loader do malware**.
- **Mudou:**
  - **Bug de origem (N3):** o JSON do armazém de cada lote é gravado quando o lote é criado, com os itens do config da época; item novo no config nunca entrava nos lotes existentes. `DatabaseMethod#getPlotModelByID` agora **sincroniza** o JSON com o config a cada leitura (adiciona itens novos e atualiza nome/preço/`recolher`). **Sem migração manual de banco.**
  - **Lag (E3):** SQLite com `database is locked` — a gravação é feita depois de fechar a leitura e o pool do SQLite passou a 1 conexão (`HikariConnect`).
  - **Plantações (E4):** novo `PlantGrowListener`. Plantas que crescem sem dropar item (cana, melancia, abóbora, trigo...) contam no armazém a cada crescimento dentro de um lote; nas de bloco novo (cana/melancia/abóbora) o bloco novo é cancelado (a cana **fica em 1 bloco**, igual ao cacto); nas por estágio (trigo/cenoura/batata/verruga/cacau) só conta ao amadurecer e a planta volta ao estágio 0. Armazém cheio: a planta cresce normal.
- **Configurar** (`NexusArmazemV2\config.yml`, seção `Itens-armazem`; há um comentário no arquivo): em cada item que cresce e não dropa, acrescente `crescimento: <MATERIAL>` (ex.: `SUGAR_CANE_BLOCK`, `MELON_BLOCK`, `PUMPKIN`, `CROPS`, `CARROT`, `POTATO`, `NETHER_WARTS`, `COCOA`; aceita lista) e, opcional, `crescimento-quantidade` (padrão 1; a melancia dá ~5). O item também precisa estar em `InventoryConfiguration` (slot e lore). **Cacto não precisa** (já conta pelo drop). A cana antiga sem a chave continua funcionando (compatibilidade).
- **Testar:** coloque o item novo no config e reinicie; `/armazem` num lote **antigo** mostra o item com 0. Plantada a cana, o contador sobe a cada crescimento (com `randomTickSpeed 3` é lento; para testar use `/gamerule randomTickSpeed 100` no mundo dos terrenos e volte para `3`). Não deve aparecer `SQLITE_BUSY` nem `Could not pass event ItemSpawnEvent` no log.
- **Atenção (produção):** o `database.db` do armazém é do ambiente; **não copie o local**.

## E1 — Core conecta ao MySQL (hardup)
- **Jar:** nenhum (só configuração) e **nada a fazer em produção**.
- **Mudou:** só no ambiente **local**: em `hardup\plugins\Core\config.yml`, `database.mysql.nome` estava `pargonn` (faltava o "a") e passou a `paragonn`. **Em produção o banco é `s11_global` e já funciona** (confirmado): não mexer no `Core\config.yml` de produção.
- **Testar:** no log deve aparecer `[Core] Conectado ao MySQL!` e `O plugin foi ativado`.

## E2 — Pesca reabre a conexão
- **Jar:** `Pesca.jar`.
- **Mudou (Pesca, `DatabaseManager`):** a conexão única era derrubada pelo `wait_timeout` do MySQL (`No operations allowed after connection closed`). Novo `getConnection()` valida (`isValid`) e reabre antes de cada uso; URL com `autoReconnect=true`.
- **Testar:** pesque, deixe o servidor parado por bastante tempo (ou reduza o `wait_timeout` do MySQL) e volte a pescar: sem erro de conexão.

## E5 — Espaço vazio depois da tag do clan (tablist, Core)
- **Jar:** `Core.jar`.
- **Causa real:** o prefixo do cargo tinha **negrito (`&l`) sem `&r`**. O cliente 1.8 só desliga o negrito na **largura** da linha com `§r` (um código de cor desliga só ao desenhar); assim contava nome + tag como negrito e reservava espaço vazio no fim.
- **Mudou (`TablistListener`):** `resetBold` insere `§r` antes das cores finais do prefixo; a tag do clan (`%simpleclans_tag_label%`) perde espaços/códigos de cor do **fim** (`stripTrailing`); o corte de 16 caracteres não deixa `§` solto.
- **Configurar:** `Core\tablist.yml` → `debug: false` (padrão). Com `true` loga o prefixo/sufixo reais de cada jogador (só para diagnóstico; gera 1 linha a cada 5 s por jogador).
- **Atenção:** tags novas com negrito (`&l`) já são tratadas automaticamente.
- **Testar:** entrar no jogo e abrir o tab: a linha do jogador termina logo depois de `[TAG]`, sem espaço vazio.

## Pendentes
- **L1** (Cápsulas → Caixas) e **L2** (menu de Cosméticos): não iniciados.
- **N1** (tempo restante da permissão): não iniciado.
- **N2** (ApBlock): não existe no projeto; **falta a descrição** do que ele deve fazer.

---

# Deploy em produção

## 1. Como compilar
Em cada `paragonn-sources\plugins\<Plugin>`, com **JDK 8** no `JAVA_HOME` (o JDK 25 padrão quebra o Gradle):
`./gradlew build` (o `Companions` usa `./gradlew shadowJar`, que dispara a tarefa `copyJar`). O jar sai em `build\libs\`.
As tarefas de deploy do Gradle escrevem em `C:/paragonn/paragonn-server/hardup/plugins` (**caminho local**): em produção, copie os jars à mão (lista completa em **Jars que mudaram**).

## 2. Configs que NÃO se atualizam sozinhas
Os plugins só criam o arquivo se ele **não existir**; em produção os arquivos antigos ficam como estão. É preciso **mesclar** manualmente:
- `Runes\config.yml` e `Runes\runes.yml` (limites por peça, novas runas, restrições, mensagens, `stat-names`).
- `Missions\missions.yml` (`defaults.reset-delay`) e `Missions\config.yml` (`messages.sem-companheiro`; opcional, tem padrão no código).
- `Hardup\menus\top.yml` (linha `{rank}` no lore).
- `NexusArmazemV2\config.yml` (chave `crescimento` nos itens que crescem).
- `Core\tablist.yml` (`debug: false`, opcional). O `Core\config.yml` de produção **não muda** (o banco `s11_global` já está certo).
- `Boss\config.yml` (`general.void-rescue-*`, opcional — se faltar, vale o padrão `true`/`10`). `Boss\missions.yml` e `Boss\holograms.yml` **são criados sozinhos** na 1ª execução.

## 3. Banco de dados
Nenhuma migração manual. Criadas sozinhas: `<prefixo>arena_stats` e `<prefixo>boss_missions` (Boss). O armazém sincroniza o JSON dos lotes ao ler (ver N3).

## 4. Permissões (LuckPerms)
- `paragonnboss.dev` — **`default: false`**: conceder só a devs (mostra a linha `[dev] libera:` no menu de missões do Boss).
- O comando padrão de concessão das missões do Boss usa o LuckPerms (`lp user {player} permission set {permission} true`); confira que o `lp` existe no servidor de produção e troque as permissões de exemplo.

## 5. Ordem sugerida
1. Backup do banco e da pasta `plugins`.
2. **Parar** o servidor (o Windows trava jar em uso; deploy com o servidor ligado falha).
3. Copiar os jars e mesclar as configs da seção 2.
4. Subir e conferir no log: `PBoss habilitado`, `PRunes habilitado`, `PMissions habilitado`, `Hardup habilitado`, `[Core] Conectado ao MySQL!`, `[NexusArmazem] Plugin iniciado com sucesso`, sem `Could not load`.
5. Rodar os testes de cada tarefa (seção Detalhes).

## 6. Ao mexer em `plugin.yml`
**Valide o YAML antes de implantar** (`: ` dentro de texto sem aspas quebra o carregamento e o plugin inteiro não sobe — aconteceu com o Boss uma vez durante os testes).

## 7. NÃO replicar (foi só teste local)
- Limpeza dos pets e do progresso de missões do jogador de teste (`MajorNeves`) no banco.
- A troca do nome do banco no `Core\config.yml` (`pargonn` → `paragonn`): era um erro só do ambiente local.
- `debug: true` do tablist (foi desligado depois do diagnóstico).
- Mudança de `randomTickSpeed` para testar a cana.
- O `database.db` do armazém e os dados de `Boss\storage` locais.
- As permissões de exemplo do `Boss\missions.yml` (`boss.cacador.1`, `boss.cacador.2`, `boss.matador`).

---

Código-fonte: `paragonn-sources\plugins\` · Servidores: `paragonn-server\{hardup,lobby,proxy}`
