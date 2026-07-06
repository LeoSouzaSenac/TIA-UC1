# UC1 — Arquitetura de Computadores e GPU
### Material de apoio para os alunos | Aulas 1 a 6

> Esse material complementa os slides e as aulas ao vivo. A ideia aqui não é substituir a aula, é ser aquele "cola do bom" pra você revisar depois, principalmente se cochilou 2 minutinhos (a gente sabe que rola 😄).

---

# 🗺️ Antes de tudo: pra onde a gente vai nesse curso?

Beleza, antes de entrar em Von Neumann e essas paradas com nome de cientista morto, deixa eu te mostrar o mapa completo. Essa UC tem **21 aulas de 3h**, divididas em **4 blocos**. Pensa assim: é tipo um RPG — cada bloco é uma fase, e no final você enfrenta o chefão (o projeto final).

### 🧱 Bloco 1 — "Os fundamentos" (Aulas 1-6)
Aqui a gente entende **como um computador pensa** por dentro: como CPU e GPU são diferentes, como a memória funciona, o que é processo/thread, como redes conversam entre máquinas, e como o Linux organiza tudo isso. É a base — sem isso, tudo que vem depois vira decoreba sem sentido.

### ⚡ Bloco 2 — "Bota a mão na massa com GPU" (Aulas 7-12)
Aqui a gente sai da teoria e parte pra programar a GPU de verdade: CUDA (o "idioma" que a NVIDIA usa), depois OpenCL e ROCm (as alternativas, incluindo o mundo AMD). Fecha com você mesmo escrevendo um código que roda em paralelo numa GPU. Tem **avaliação em grupo** nesse bloco.

### 🤖 Bloco 3 — "Automação com Bash" (Aulas 13-15)
Curto e direto: aprender a fazer a GPU se cuidar sozinha — scripts que monitoram temperatura, uso, energia — tipo um "personal trainer" pra sua placa de vídeo, só que em Bash.

### 🚀 Bloco 4 — "Projeto Final" (Aulas 16-20)
Você escolhe um problema real de IA (visão computacional, NLP, o que for), implementa usando tudo que aprendeu, automatiza o monitoramento, e no final faz um pitch pra turma. É aqui que tudo se junta.

**Resumindo o caminho:** entender a máquina → programar a GPU → automatizar a GPU → usar tudo isso num projeto real. Simples assim (a execução é que não é simples, mas o caminho é).

Agora sim, bora pra Aula 1.

---

## Aula 1 — "CPU e GPU são tipo Sheldon Cooper e uma sala de aula inteira"
**Duração: 3h**

### O gancho da aula
Por que seu PC roda um jogo em 3D suave, mas ia demorar *dias* pra treinar uma IA sem uma GPU boa? Guarda essa pergunta, a resposta vem sozinha ao longo da aula.

### Von Neumann: o avô de quase todo computador que existe

Em 1945, um cara chamado Von Neumann bolou um jeito de organizar computador que a gente usa até hoje (seu notebook, seu celular provavelmente é uma variação disso). A ideia é simples:

- Tem uma **CPU** (o "cérebro" que executa instruções)
- Tem uma **Memória** (onde ficam guardadas TANTO as instruções quanto os dados, tudo junto, no mesmo espaço)
- Tudo isso se conecta por **barramentos** (pensa neles como "rodovias" por onde trafegam dado, endereço e comando)

**O problema disso** (chamado de "gargalo de Von Neumann"): como instrução e dado ficam no mesmo "armário" e usam a mesma "rodovia", a CPU não consegue pegar uma instrução nova E ler um dado ao mesmo tempo. É tipo você tentando ler a receita E pegar os ingredientes usando só uma mão — dá, mas não é o ideal.

```
        Rodovia de Endereços
   CPU ◄─────────────────────────► Memória
        Rodovia de Dados            (Instruções + Dados,
        Rodovia de Controle          tudo no mesmo armário)
```

### Harvard: o "cada coisa no seu lugar"

A arquitetura **Harvard** resolve aquele gargalo de um jeito meio óbvio quando você para pra pensar: **separa** a memória de instrução da memória de dado. Cada uma com sua própria "rodovia". Assim a CPU pega a próxima instrução e mexe num dado ao mesmo tempo, sem trombar.

Onde você encontra isso na vida real: Arduino, controladores de eletrodomésticos, e (curiosidade) o **cache L1** dos processadores modernos, que é dividido — parte pra instrução, parte pra dado. Ou seja: seu PC moderno é meio "Frankenstein", Von Neumann por fora, com uns truques Harvard escondidos por dentro pra ganhar velocidade.

| | Von Neumann | Harvard |
|---|---|---|
| Memória | Uma só, tudo junto | Separada (dado ≠ instrução) |
| Gargalo? | Sim | Não |
| Onde aparece | PC, servidor | Arduino, DSP, cache L1 |

### Agora sim: CPU vs. GPU (a resposta da pergunta lá de cima)

Imagina o seguinte cenário: você tem uma prova de 5000 continhas de somar simples (tipo 4+7, 12+3...).

- **Time CPU**: 8 professores de matemática com PhD, cada um MUITO inteligente, capaz de resolver equação diferencial complexa — mas são só 8 pessoas.
- **Time GPU**: 5000 alunos do 6º ano, cada um sabe fazer só continha simples — mas são 5000 ao mesmo tempo.

Pra resolver equação diferencial de verdade? Chama os PhDs (CPU). Pra somar 5000 continhas simples de uma vez? Os 5000 alunos do 6º ano ganham disparado, mesmo sendo "menos inteligentes" individualmente (GPU).

**E treinar uma IA é literalmente isso**: milhões de continhas simples (multiplicação de matriz) repetidas à exaustão. Por isso GPU manda muito mais que CPU nessa tarefa específica — não é que GPU seja "melhor", é que o problema pede exatamente o que ela faz bem.

| | CPU | GPU |
|---|---|---|
| Núcleos | Poucos (tipo 8-64), cada um fera | Milhares, cada um "burrinho" mas em bando |
| Bom em | Lógica complexa, decisão, um problema difícil por vez | Mesma tarefa repetida em MUITOS dados |
| Exemplo | Rodar seu sistema operacional | Renderizar jogo, treinar rede neural |

### Hora do play: `nvidia-smi`

Chega de conversa, bora ver a GPU respirando de verdade. Se tiver GPU NVIDIA no lab, roda:

```bash
nvidia-smi
```

Você vai ver algo tipo:

```
| GPU  Name        | Fan  Temp   Pwr:Usage/Cap | Memory-Usage         | GPU-Util |
|   0  RTX 4090     |  32%   45C    20W / 450W  | 1024MiB / 24564MiB   |     2%   |
```

Traduzindo pra português claro:
- **GPU-Util**: quanto a placa tá suando agora (2% = tá de boa, quase parada)
- **Memory-Usage**: quanta "gaveta" (VRAM) já tá ocupada
- **Temp**: temperatura — se passar muito de 80-85°C rodando pesado, é hora de se preocupar

Sem GPU no lab? Sem crise: abre o Google Colab, ele te dá GPU de graça, e roda `!nvidia-smi` numa célula.

**Manda ver:** roda `nvidia-smi -l 1` (atualiza a cada 1 segundo) e fica de olho enquanto abre um vídeo do YouTube em 4K — vê o `GPU-Util` subir.

### Pra casa (não é punição, é rapidinho)
Escolhe um aparelho que você usa todo dia (celular, videogame, smart TV) e em 5 linhas explica se ele se parece mais com Von Neumann ou Harvard, e por quê. Vale pesquisar.

---

## Aula 2 — "SIMD, MIMD, RISC, CISC: alfabeto de arquitetura sem trauma"
**Duração: 3h**

### Retomando rapidinho
Bora ouvir 2-3 respostas do exercício de casa antes de seguir.

### A "tabela periódica" das arquiteturas (taxonomia de Flynn)

Um cara chamado Flynn, em 1966, olhou pra todo computador que existe e falou "só existem 4 jeitos de organizar isso". Ele cruzou duas perguntas: **quantas instruções rodam** e **quantos dados são processados** ao mesmo tempo.

| | 1 dado por vez | Vários dados ao mesmo tempo |
|---|---|---|
| **1 instrução por vez** | SISD | **SIMD** |
| **Várias instruções ao mesmo tempo** | MISD | **MIMD** |

Traduzindo os que importam pra gente:

- **SIMD** (Single Instruction, Multiple Data): a **mesma ordem** executada em **vários dados de uma vez**. É basicamente o espírito da GPU: "todo mundo, some 1 nesse número" — e milhares de núcleos fazem isso simultaneamente, cada um com seu próprio número.
- **MIMD** (Multiple Instruction, Multiple Data): cada núcleo faz uma coisa **diferente**, em dado **diferente**. É como sua CPU multi-core funciona — um núcleo cuidando do Spotify, outro do navegador, cada um numa boa sozinho.
- **MISD**: raridade extrema (tipo computador de avião que roda o mesmo cálculo em sistemas redundantes pra conferir se bateu — não perde seu sono com esse).

**Detalhe que separa os visionários dos leigos**: GPU não é 100% SIMD "purinho" — o nome técnico correto é **SIMT** (Single Instruction, Multiple *Threads*). Grupos de 32 threads (chamados de *warp* na NVIDIA) rodam a mesma instrução, mas cada thread tem seu próprio "boletim" de dados e pode, às vezes, "discordar" do grupo (o que custa performance). Guarda esse nome, SIMT — ele vai voltar com força quando a gente ver CUDA de verdade.

### RISC vs. CISC: o "faz tudo" contra o "faz pouco, mas rápido"

**CISC** (Complex Instruction Set Computer): instruções "canivete suíço" — um comando só já faz várias coisas embutidas. É o mundo do x86 (Intel/AMD, o processador do seu PC provavelmente).

**RISC** (Reduced Instruction Set Computer): instruções bem simples e enxutas, quase sempre 1 ciclo de clock pra executar. É o mundo do ARM (seu celular), do RISC-V, e — surpresa — o jeito que os núcleos de uma GPU são desenhados por dentro.

| | CISC | RISC |
|---|---|---|
| Instrução | Complexa, "faz tudo" | Simples, direta |
| Exemplo | x86 (PC, servidor) | ARM (celular), núcleos de GPU |
| Por que importa aqui | — | É porque é simples que dá pra caber MILHARES de núcleos no mesmo chip |

**Gancho pra puxar assunto em aula**: os chips da Apple (M1 pra cá) são ARM/RISC e viraram meme de "dura a vida toda de bateria" — é justamente por essa simplicidade das instruções que gasta menos energia.

### Casos reais — trabalho em grupo (o "quiz do bafão")

Grupos de 3-4, cada um pega um caso, 5 minutos de apresentação:

1. **Celular** — por que ele não usa a mesma arquitetura do seu PC?
2. **PS5/Xbox** — tem CPU x86 junto com GPU num design meio "híbrido", como isso se sustenta?
3. **Cluster de GPU pra treinar IA** (tipo o que treina um ChatGPT) — onde entra SIMT, onde entra MIMD?
4. **Servidor de banco de dados** — por que ainda insiste em CISC/x86 mesmo hoje?

### Fechamento: quiz relâmpago
Grito coletivo: eu leio um cenário, vocês gritam SIMD, MIMD, RISC ou CISC. Sem meio-termo, sem "depende" — comita rápido.

---

## Aula 3 — "A geladeira da GPU: por que 'mais memória' nem sempre é 'mais rápido'"
**Duração: 3h**

### O gancho
Uma GPU com 24GB pode, em certas tarefas, ser MAIS rápida que uma com 48GB. Isso é bug? Não — é sobre entender a hierarquia de memória. Vamo lá.

### A hierarquia (do "na mão" ao "no fundo do armário")

Pensa numa cozinha:

1. **Registradores** — o que você já tá segurando na mão, cortando agora. Rapidíssimo, mas cabe muito pouco (poucos registradores por thread).
2. **Shared Memory / L1** — a bancada da cozinha, perto de você, onde você deixa os ingredientes que vai usar toda hora. Rápido, compartilhado entre "quem tá cozinhando junto" (um bloco de threads).
3. **L2 Cache** — a despensa da cozinha. Um pouco mais longe, mas ainda é rapidinho pegar, e é compartilhada entre todo mundo da casa.
4. **Memória Global (VRAM)** — o mercado. Tem MUITO mais coisa lá, mas você perde tempo indo buscar.

```
Registradores  (na mão, poucochinho)
      ↓
Shared/L1      (bancada, rápido)
      ↓
L2             (despensa)
      ↓
VRAM/Global    (mercado — grande, mas longe)
```

**A regra de ouro** (spoiler da Aula 8): se você vai usar o mesmo ingrediente várias vezes, deixa ele na bancada (shared memory), não fica correndo pro mercado (VRAM) toda hora. É basicamente 90% do jogo de otimizar código pra GPU.

### RAM do PC vs. VRAM da GPU

| | RAM (seu PC) | VRAM (a GPU) |
|---|---|---|
| Tipo | DDR4/DDR5 | GDDR6X, HBM |
| Velocidade de "estrada" (banda) | ~50-90 GB/s | 500 GB/s até 3 TB/s+ |
| Prioridade | Resposta rápida (latência) | Mover MUITA coisa de uma vez (banda) |

**Por que a GPU não se importa tanto com latência?** Porque ela não fica esperando com os braços cruzados — enquanto um grupo de threads espera um dado chegar, ela já colocou OUTRO grupo pra trabalhar. Isso se chama **"latency hiding"** (esconder a espera trabalhando em outra coisa) — tipo você colocar o arroz pra cozinhar e, enquanto espera, já ir cortando a cebola.

### Mão na massa: espiando a memória via Bash

```bash
# Memória RAM do sistema
free -h

# Memória da GPU
nvidia-smi --query-gpu=memory.used,memory.total,memory.free --format=csv

# Ficar de olho ao vivo
watch -n 2 nvidia-smi --query-gpu=memory.used,memory.total --format=csv
```

**Exercício:** cola esse mini-script, roda, e abre/fecha algo pesado (um jogo, um Jupyter com modelo carregado) pra ver o log crescer:

```bash
#!/bin/bash
while true; do
  echo "$(date '+%H:%M:%S') - $(nvidia-smi --query-gpu=memory.used --format=csv,noheader)" >> vram_log.txt
  sleep 5
done
```

### Fechamento
Resposta da pergunta de abertura: banda + como a memória tá organizada importa tanto (ou mais) quanto capacidade bruta. "Mais GB" sem a arquitetura de memória certa é que nem ter um armazém gigante longe de casa — não adianta se você usa o ingrediente toda hora.

---

## Aula 4 — "Processo é a casa, thread é o cômodo"
**Duração: 3h**

### O gancho
Quantas coisas o seu PC tá fazendo agora, literalmente nesse segundo? (Spotify, navegador com 30 abas, antivírus, Discord...) Isso tudo junto é o assunto de hoje.

### Processos: cada programa é uma casa isolada

Um **processo** é um programa rodando, com paredes próprias — memória isolada, ninguém de fora mexe direto. Tem um **PID** (tipo o CPF do processo), seu próprio espaço de memória, seus próprios arquivos abertos.

**Os estados de um processo** (o "ciclo de vida"):

```
   [Novo] → [Pronto] ⇄ [Executando] → [Terminado]
                ↑            ↓
                └──── [Bloqueado] (esperando algo, tipo disco ou rede)
```

- **Pronto**: na fila, esperando a vez na CPU
- **Executando**: rodando agora
- **Bloqueado**: parado esperando algo externo (tipo você esperando o carregamento de uma página)
- **Terminado**: já era

### Threads: os cômodos dentro da casa

Uma **thread** é uma "linha de execução" dentro do processo. A diferença chave: threads do MESMO processo **compartilham** a memória (é tudo a mesma casa), mas cada uma tem sua própria "vibe" de execução (pilha própria).

| | Processo (a casa) | Thread (o cômodo) |
|---|---|---|
| Memória | Isolada de outras casas | Compartilhada com outros cômodos da mesma casa |
| Criar | Mais caro (construir casa nova) | Mais barato (só decorar um cômodo) |
| Se der zebra | Só aquela casa cai | Pode derrubar a casa inteira |

### E na GPU, isso vira outra escala TOTALMENTE diferente

CPU roda umas dezenas de threads ao mesmo tempo, no máximo. GPU roda **milhares, às vezes dezenas de milhares** de threads simultaneamente. Pra organizar essa loucura toda, a GPU agrupa threads em **blocos**, e blocos numa **grade** (grid) — isso vai virar vocabulário oficial quando a gente chegar em CUDA (Aula 7), mas já dá pra visualizar:

```
Grade (Grid)
 └── Bloco 0        Bloco 1        Bloco 2   ...
      ├── thread 0   ├── thread 0   ├── thread 0
      ├── thread 1   ├── thread 1   ├── thread 1
      └── ...        └── ...        └── ...
```

Threads de CPU são "pesadas" (trocar entre elas custa caro). Threads de GPU são "leves", feitas pra nascer e morrer aos milhares sem drama nenhum.

### Sentindo na prática (Python, sem precisar de GPU)

```python
import time
from multiprocessing import Pool

def quadrado(n):
    return n * n

numeros = list(range(1_000_000))

inicio = time.time()
resultado_seq = [quadrado(n) for n in numeros]
print(f"Sequencial: {time.time() - inicio:.2f}s")

inicio = time.time()
with Pool(4) as p:
    resultado_par = p.map(quadrado, numeros)
print(f"Paralelo (4 processos): {time.time() - inicio:.2f}s")
```

Compara os dois tempos com a turma — e discute por que o ganho não é "4x mais rápido" simplesmente (tem custo de criar/gerenciar processos). Isso já é um gostinho de por que threads leves de GPU escalam tão melhor.

### Pra fechar
Escreve com suas palavras: por que a GPU aguenta milhares de threads e a CPU só dezenas?

---

## Aula 5 — "Como os computadores fofocam entre si"
**Duração: 3h**

### O gancho
Um modelo de IA gigante não treina numa GPU só — treina em centenas, conectadas em rede. Como diabos elas se falam?

### A pilha TCP/IP (o "correio" da internet)

```
Aplicação    → HTTP, SSH, DNS... (o que VOCÊ usa)
Transporte   → TCP, UDP (como o pacote viaja)
Internet     → IP (o endereço de destino)
Acesso à Rede → Wi-Fi, Ethernet (o "cabo" físico)
```

**TCP**: tipo Sedex com rastreio e confirmação de entrega. Garante que tudo chegue, na ordem certa, e reenvia se perder algo. Mais lento, mas confiável. Usado onde erro não pode acontecer: site, SSH, transferência de arquivo.

**UDP**: tipo jogar o pacote pela janela do carro em movimento e torcer. Não garante entrega nem ordem, mas é RÁPIDO. Usado em streaming, jogo online, e em comunicação de baixíssima latência entre GPUs em clusters de IA (onde velocidade importa mais que "garantir 100%").

| | TCP | UDP |
|---|---|---|
| Confere entrega? | Sim | Não |
| Velocidade | Mais devagar | Mais rápido |
| Usa onde | Site, SSH, e-mail | Streaming, jogo, GPU-a-GPU em cluster |

### IPv4: o endereço que já tá "lotado"

Formato: `192.168.1.1` (4 números de 0-255). Só que isso dá "só" 4,3 bilhões de endereços possíveis — e o mundo já tem mais dispositivo conectado que isso. Literalmente acabou o estoque, e por isso existe NAT (compartilhar 1 IP público entre vários dispositivos de uma casa) e o IPv6.

### IPv6: o endereço que nunca vai acabar

Formato gigante em hexadecimal: `2001:0db8:85a3:0000:0000:8a2e:0370:7334`. O número de endereços possíveis é tão absurdo (340 undecilhões) que não tem lógica prática de acabar tão cedo. Já vem com segurança embutida de fábrica.

### Por que isso importa pra IA?

Quando você treina uma IA gigante em várias GPUs ao mesmo tempo, elas precisam trocar informação (gradientes) toda hora entre si — se a rede for lenta, isso vira o gargalo do treino inteiro. Por isso existem tecnologias tipo **InfiniBand** e **NVLink**: são "atalhos" super rápidos de GPU-pra-GPU, bem melhores que uma rede Ethernet comum pra esse tipo de conversa constante.

### Mão na massa

```bash
ip a                       # ver seu próprio IP
ping 8.8.8.8                # testar se a internet responde
traceroute google.com       # ver o "caminho" até o destino
netstat -tuln                # ver portas/conexões abertas
```

**Exercício em dupla**: descobre o IP da sua máquina, identifica se é IP privado (10.x, 172.16-31.x, 192.168.x) ou público, e a que classe pertence.

### Gancho pra próxima
Hoje vimos como máquinas se falam de fora. Na próxima, entramos DENTRO de uma máquina Linux pra ver como ela organiza tudo — incluindo a própria GPU.

---

## Aula 6 — "Linux e GPU: quem manda na casa"
**Duração: 3h**

### O gancho
Por que praticamente todo servidor de IA do planeta roda Linux e não Windows? (Discussão rápida: driver, performance, ecossistema open source de ML — jogar pra turma opinar antes de fechar.)

### A "planta da casa": estrutura de diretórios do Linux

| Pasta | O que tem lá |
|---|---|
| `/bin`, `/usr/bin` | Os "programas" executáveis |
| `/etc` | Configurações |
| `/home` | A pasta pessoal de cada usuário |
| `/var` | Logs, coisas que mudam sempre |
| `/dev` | Dispositivos — **inclusive a GPU!** (`/dev/nvidia0`) |
| `/proc` | Informação ao vivo de processos e do sistema |
| `/opt` | Programas de terceiros (é onde geralmente cai o CUDA Toolkit) |

Sim, isso mesmo: no Linux, a **GPU literalmente aparece como um arquivo** em `/dev/nvidia*`. Roda `ls -la /dev/nvidia*` e mostra pra turma — é assim que os drivers conversam com a placa por baixo dos panos.

### Comandos que você vai usar até enjoar

```bash
pwd                  # onde eu tô?
ls -la               # o que tem aqui (com os arquivos escondidos)
cd /caminho          # ir pra algum lugar
mkdir pasta          # criar pasta
cp origem destino    # copiar
mv origem destino    # mover/renomear
rm arquivo           # apagar (com cuidado, não tem lixeira aqui)
find / -name "*.log" # caçar um arquivo perdido
grep "erro" log.txt  # caçar uma palavra dentro de um arquivo
```

### Permissões: quem pode fazer o quê

```
-rwxr-xr-- 1 leo dev  4096 Jul  6 10:00 script.sh
```

Read, Write, eXecute — pra **dono**, **grupo**, **outros**. Numérico: r=4, w=2, x=1.

```bash
chmod 754 script.sh              # dono tudo, grupo lê/executa, outros nada
sudo usermod -aG video leo       # colocar o usuário no grupo que acessa a GPU
```

Em servidor de IA compartilhado (tipo faculdade, empresa), é super comum restringir quem acessa a GPU justamente via permissões desses arquivos em `/dev`.

### Processos no Linux

```bash
ps aux              # lista geral de processos
top / htop           # monitor ao vivo (htop é o "top só que bonito")
kill -9 PID           # forçar a matar um processo teimoso
nvidia-smi            # (relembrando) mostra os processos rodando NA GPU
```

Repara: lá embaixo do `nvidia-smi` aparece uma listinha de quais processos estão ocupando a GPU agora — é o conceito de processo (Aula 4) aparecendo de novo, só que aplicado à GPU.

### Prática final: seu primeiro "robozinho" de monitoramento

```bash
#!/bin/bash
# verifica_gpu.sh — avisa se a GPU tá suando muito

LIMITE=50

uso=$(nvidia-smi --query-gpu=utilization.gpu --format=csv,noheader,nounits)

if [ "$uso" -gt "$LIMITE" ]; then
  echo "⚠️  GPU suando: ${uso}%"
else
  echo "✅ GPU de boa: ${uso}%"
fi
```

**Desafio em grupo**: faz esse script rodar em loop a cada 10s e guardar os avisos num arquivo `alerta_gpu.log`. Guarda esse código, porque ele vai ser praticamente a base do que vamos formalizar lá na Aula 13.

### Fechando o bloco 1
Recapitulando a jornada até aqui: como o computador é montado por dentro → CPU vs GPU → SIMD/MIMD/RISC/CISC → memória → processo/thread → rede → Linux. Você já tem o esqueleto inteiro. Bloco 2 é hora de programar a GPU de verdade.

---

## Avaliação Individual — Questionário Técnico

1. Explica com tuas palavras a diferença entre Von Neumann e Harvard, com um exemplo de cada.
2. Por que a GPU aposta em "muitos núcleos simples" em vez de "poucos núcleos fera" como a CPU?
3. Classifica na taxonomia de Flynn: renderizar imagem, CPU multi-core rodando vários apps, sistema redundante de avião, CPU de núcleo único rodando uma tarefa.
4. RISC ou CISC: qual se parece mais com o núcleo interno de uma GPU? Por quê?
5. Ordena do mais rápido pro mais lento: memória global, shared memory, registrador, L2.
6. O que é "latency hiding" e por que a GPU depende disso?
7. Qual a diferença entre processo e thread? Dá um exemplo de quando usar cada um.
8. TCP ou UDP: qual faz mais sentido pra sincronizar GPUs num cluster de treino de IA? Justifica.
9. Que comando mostra quais processos estão usando a GPU agora? E pra ver a VRAM ocupada?
10. (Prática) Escreve um `chmod` que dê ao dono leitura+escrita+execução, ao grupo só leitura+execução, e a outros nada.

---

*Esse material é só um apoio — a aula ao vivo (com as analogias ao vivo, as perguntas da turma e os exemplos improvisados) sempre vale mais. Usa isso pra revisar, não pra substituir.*
