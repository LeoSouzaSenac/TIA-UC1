# Aula 1 — Arquitetura de Computadores e GPU
### Material de apoio 4/4 — Técnico em Inteligência Artificial (Senac RS)
### UC1: Reconhecer Modelos de Arquitetura de Computadores e GPU

> Este é o material mais completo dos quatro. Cada seção está identificada com o número do slide correspondente na apresentação da aula, para que você consiga acompanhar em paralelo. Todo termo técnico usado é explicado no momento em que aparece, e também reunido no glossário final.

---

## Slide 29 — Abertura da Parte 4: UC1, Aula 1

Chegamos à primeira Unidade Curricular do curso: UC1, Reconhecer Modelos de Arquitetura de Computadores e GPU[^1], com carga horária total de 64 horas. Esta é também a nossa primeira aula técnica, depois da aula de abertura sobre o que é Inteligência Artificial, sobre mercado de trabalho e sobre a estrutura do curso.

O foco desta primeira aula é entender como um computador funciona por dentro, no nível mais fundamental: como a informação é organizada, processada e armazenada dentro do hardware. Esse conhecimento é a base sobre a qual absolutamente tudo o que vier depois no curso será construído.

---

## Slide 30 — Por que começar por hardware, e não por Machine Learning

Uma pergunta legítima, que provavelmente já passou pela cabeça de alguns alunos: por que o curso não começa diretamente ensinando Machine Learning ou algum tópico mais diretamente associado a "Inteligência Artificial" no sentido popular do termo?

A resposta é estrutural: toda Inteligência Artificial, por mais sofisticada que seja, é executada em cima de hardware real. Um modelo de Machine Learning, por mais elegante que seja matematicamente, precisa ser processado fisicamente por circuitos eletrônicos que executam operações matemáticas. Entender como esses circuitos — especificamente a CPU[^2] (Central Processing Unit, ou Unidade Central de Processamento) e a GPU (Graphics Processing Unit, ou Unidade de Processamento Gráfico) — funcionam por dentro é o alicerce necessário para compreender, mais adiante no curso, por que treinar Inteligência Artificial exige especificamente GPU, e não apenas uma CPU potente.

Sem essa base de hardware, muitos conceitos que aparecerão nas próximas Unidades Curriculares (como o motivo pelo qual certas operações de Machine Learning são tão mais rápidas em GPU) seriam aprendidos de forma decorada, sem compreensão real do porquê.

---

## Slide 31 — O gancho da aula

A pergunta que guia toda esta aula é: por que treinar um sistema de Inteligência Artificial sem uma GPU pode levar dias inteiros, enquanto o mesmo treinamento, realizado com uma GPU adequada, pode levar apenas algumas horas?

A resposta começa exatamente na diferença fundamental entre como uma CPU e uma GPU são construídas por dentro — e é isso que vamos examinar, passo a passo, ao longo desta aula.

---

## Slide 32 — Arquitetura Von Neumann

### O que é a Arquitetura Von Neumann

A Arquitetura Von Neumann[^3], batizada em homenagem ao matemático John von Neumann, que a descreveu em 1945, é o modelo estrutural sobre o qual a grande maioria dos computadores de uso geral (incluindo praticamente todo notebook, desktop e servidor comum) foi construída ao longo das últimas oito décadas.

### Conceito central

O conceito central da Arquitetura Von Neumann é que ela utiliza um único espaço de endereçamento de memória e um único barramento[^4] (em inglês, bus) para transportar tanto dados quanto instruções.

Antes de prosseguir, é necessário explicar cada um desses termos com precisão:

- Espaço de endereçamento de memória: é a organização lógica da memória do computador, na qual cada posição possui um endereço único, permitindo que o processador localize e acesse qualquer informação armazenada.
- Barramento (bus): é o "caminho físico" — um conjunto de fios ou trilhas em uma placa de circuito — por onde a informação viaja entre diferentes componentes do computador (por exemplo, entre o processador e a memória). Pense nele como uma rodovia por onde trafegam dados, endereços e sinais de controle.
- Instrução: um comando específico que o processador deve executar (por exemplo, "somar dois números" ou "mover um valor de um lugar para outro").
- Dado: a informação em si, sobre a qual as instruções operam (por exemplo, os próprios números que serão somados).

Na Arquitetura Von Neumann, tanto as instruções (o "programa" que diz o que fazer) quanto os dados (a informação sobre a qual o programa opera) ficam armazenados na mesma memória e trafegam pelo mesmo barramento.

### A estrutura interna da CPU segundo este modelo

Segundo este modelo, uma CPU é composta por:

- Unidade de Controle[^5] (Control Unit): responsável por coordenar e dirigir a execução das instruções, determinando a sequência de operações do processador.
- Unidade Lógica e Aritmética[^6], conhecida pela sigla em inglês ALU (Arithmetic and Logic Unit): componente responsável por executar, de fato, as operações matemáticas (soma, subtração, multiplicação, entre outras) e lógicas (comparações, por exemplo).
- Unidade de Memória (Memory Unit): onde ficam armazenados tanto os dados quanto as instruções do programa em execução.

### A vantagem e o problema deste modelo

A vantagem da Arquitetura Von Neumann é a sua simplicidade e flexibilidade — um único tipo de memória, um único barramento, uma estrutura relativamente simples de projetar e fabricar.

O problema, conhecido tecnicamente como Gargalo de Von Neumann[^7] (Von Neumann bottleneck), decorre exatamente dessa simplicidade: como instruções e dados compartilham o mesmo barramento, o processador não consegue, ao mesmo tempo, buscar uma nova instrução na memória e ler ou escrever um dado. Essas duas operações precisam necessariamente acontecer em sequência, uma de cada vez, o que limita a velocidade máxima com que o sistema consegue operar.

---

## Slide 33 — Arquitetura Harvard

### O que é a Arquitetura Harvard

A Arquitetura Harvard[^8] surge como uma resposta direta ao gargalo identificado na Arquitetura Von Neumann. O nome faz referência à Universidade Harvard, onde um dos primeiros computadores a utilizar esse modelo (o Harvard Mark I) foi desenvolvido.

### Conceito central

Na Arquitetura Harvard, a memória de instrução e a memória de dado são fisicamente separadas, e cada uma delas possui seu próprio barramento independente.

Isso significa que existem, na prática, dois "caminhos" distintos: um dedicado exclusivamente ao tráfego de instruções, e outro dedicado exclusivamente ao tráfego de dados.

### A vantagem crítica deste modelo

A vantagem crítica da Arquitetura Harvard é que ela permite a busca de uma nova instrução e a transferência (leitura ou escrita) de um dado ao mesmo tempo, simultaneamente, sem que uma operação precise esperar a outra terminar. Isso proporciona uma largura de banda (a quantidade de informação que pode trafegar em um determinado intervalo de tempo) maior do que a Arquitetura Von Neumann tradicional consegue oferecer.

### Onde este modelo é utilizado na prática

A Arquitetura Harvard, em sua forma mais pura, é utilizada em microcontroladores simples (como os presentes em placas Arduino) e em DSPs (Digital Signal Processors, ou Processadores Digitais de Sinais, chips especializados em processar sinais de áudio, vídeo ou outros sinais digitais em tempo real).

É importante destacar que essa arquitetura é, também, a filosofia estrutural por trás de como as GPUs modernas são desenhadas — um ponto que retomaremos logo a seguir, quando compararmos CPU e GPU diretamente. Além disso, mesmo processadores modernos baseados essencialmente em Von Neumann costumam incorporar uma variante chamada Harvard Modificada em seus níveis de cache mais próximos do núcleo[^9] do processador (cache L1), separando fisicamente o cache de instruções do cache de dados, justamente para obter parte dos ganhos de desempenho que a Arquitetura Harvard proporciona.

---

## Slide 34 — CPU versus GPU: a diferença que importa para Inteligência Artificial

### A analogia central desta aula

Para entender a diferença entre CPU e GPU de forma intuitiva, considere o seguinte cenário: imagine uma prova composta por 5.000 continhas simples de soma (por exemplo, "4 mais 7", "12 mais 3", e assim por diante, 5.000 vezes).

- Uma CPU equivale a 8 professores de matemática com doutorado (PhD): cada um deles é extremamente capaz, conseguindo resolver até equações complexas e sofisticadas — mas são apenas 8 pessoas trabalhando ao mesmo tempo.
- Uma GPU equivale a 5.000 alunos do sexto ano do ensino fundamental: cada um deles, individualmente, só sabe resolver continhas simples — mas são 5.000 pessoas resolvendo, cada uma, uma continha, todas ao mesmo tempo.

Para resolver uma equação diferencial complexa, você precisa dos professores (a CPU). Mas para resolver 5.000 continhas simples de uma só vez, os 5.000 alunos (a GPU) terminam a tarefa disparadamente mais rápido, mesmo sendo, individualmente, muito menos sofisticados que os professores.

### Por que isso importa especificamente para treinar Inteligência Artificial

Treinar um modelo de Inteligência Artificial (particularmente redes neurais e Deep Learning, discutidos no material 1 deste conjunto) consiste, matematicamente, em realizar uma quantidade descomunal de operações simples e repetitivas — principalmente multiplicações de matrizes (estruturas matemáticas organizadas em linhas e colunas de números). Esse tipo de tarefa é exatamente o cenário das "5.000 continhas simples": é por isso que a GPU supera a CPU de forma tão significativa nessa aplicação específica.

### Tabela comparativa detalhada

| Característica | CPU | GPU |
|---|---|---|
| Foco de projeto | Poucos núcleos, cada um extremamente poderoso e complexo | Milhares de núcleos, cada um relativamente simples |
| Tipo de tarefa ideal | Lógica sequencial complexa, com muitas decisões e desvios | Repetir a mesma operação matemática simples em massa, sobre muitos dados diferentes |
| Papel dentro de um sistema de Inteligência Artificial | Controle geral do sistema, gerenciamento do sistema operacional, orquestração de tarefas | Treinamento e execução de redes neurais |

Alguns termos técnicos usados nesta comparação:

- Núcleo (core): unidade de processamento independente dentro de um processador, capaz de executar instruções por conta própria. Um processador com múltiplos núcleos consegue executar múltiplas tarefas verdadeiramente em paralelo.
- Paralelismo[^10]: capacidade de executar múltiplas operações ao mesmo tempo.
- Latência[^11]: o tempo que uma única operação leva para ser concluída.
- Throughput[^12] (vazão): a quantidade total de trabalho que um sistema consegue processar em um determinado intervalo de tempo, mesmo que cada operação individual não seja a mais rápida possível. A CPU é otimizada para baixa latência (resolver uma tarefa complexa rapidamente); a GPU é otimizada para alto throughput (resolver uma quantidade enorme de tarefas simples simultaneamente).

---

## Slide 35 — Por dentro do chip: Cache versus Cálculo

### Onde vai o espaço físico dentro de cada tipo de chip

Uma forma reveladora de entender a diferença entre CPU e GPU é observar como cada uma delas distribui o espaço físico disponível dentro do chip (o circuito integrado de silício onde o processador é fabricado).

A CPU dedica uma parcela significativa do seu espaço físico para memória cache[^13] e para a unidade de controle. Isso acontece porque a CPU precisa lidar com tarefas complexas e imprevisíveis, exigindo mecanismos sofisticados de antecipação e de armazenamento temporário de dados frequentemente utilizados.

- Memória cache: uma memória pequena, porém extremamente rápida, localizada fisicamente muito próxima ao núcleo do processador, usada para armazenar temporariamente dados e instruções frequentemente acessados, evitando a necessidade de buscar essa informação na memória principal (mais lenta) repetidamente. Processadores modernos costumam ter três níveis de cache, chamados L1, L2 e L3 — sendo L1 o menor e mais rápido (mais próximo do núcleo), e L3 o maior e relativamente mais lento (compartilhado entre múltiplos núcleos).

A GPU, por outro lado, dedica quase a totalidade do seu espaço físico para unidades de cálculo, chamadas ALUs (Arithmetic and Logic Units, ou Unidades Lógicas e Aritméticas, o mesmo componente descrito na explicação da Arquitetura Von Neumann, porém replicado aos milhares dentro de uma GPU). Essa escolha de projeto sacrifica a sofisticação do controle (a GPU tem uma unidade de controle proporcionalmente mais simples que a CPU) em troca de poder de cálculo bruto: uma quantidade enorme de unidades simples, capazes de executar operações matemáticas básicas em paralelo, massivamente.

Essa distribuição de espaço físico é a razão de engenharia, no nível mais fundamental, pela qual a GPU consegue executar tantas operações simultâneas: cada milímetro quadrado de silício que, em uma CPU, seria usado para cache e controle sofisticado, é usado, em uma GPU, para mais uma unidade de cálculo.

---

## Slide 36 — Monitoramento com nvidia-smi

### O que é o nvidia-smi

O nvidia-smi[^14] (NVIDIA System Management Interface, ou Interface de Gerenciamento de Sistema da NVIDIA) é uma ferramenta de linha de comando, disponível em sistemas com placas de vídeo (GPUs) da fabricante NVIDIA, que permite monitorar, em tempo real, o estado de funcionamento da GPU. É a primeira ferramenta prática de monitoramento de hardware que utilizaremos neste curso, e será usada repetidamente ao longo de toda a UC1.

### As quatro métricas principais que o nvidia-smi exibe

**GPU Utilization (Utilização da GPU)**: indica o percentual de tempo em que os núcleos de processamento da GPU estão efetivamente ocupados executando cálculos. Um valor próximo de 0% indica uma GPU praticamente ociosa; um valor próximo de 100% indica uma GPU trabalhando no seu limite de capacidade.

**Memory Usage (Uso de Memória)**: indica quanta VRAM[^15] (Video RAM, a memória própria da placa de vídeo, que será estudada em detalhe na próxima aula) está atualmente ocupada, geralmente exibida como uma fração — por exemplo, "3500 MiB de 12288 MiB" significa que 3.500 mebibytes de um total de 12.288 mebibytes de VRAM estão em uso.

**Temperature (Temperatura)**: indica a temperatura atual do chip da GPU. Esse valor é fundamental para evitar um fenômeno chamado throttling[^16]: um mecanismo de proteção automática em que a GPU reduz deliberadamente sua própria velocidade de processamento quando a temperatura atinge níveis considerados arriscados, para evitar danos físicos permanentes ao hardware.

**Processes (Processos)**: exibe uma lista de quais processos (programas em execução, identificados por um número único chamado PID[^17], Process Identifier, ou Identificador de Processo) estão, no momento, consumindo recursos da GPU, além de quanta memória cada um desses processos está utilizando.

---

## Slide 37 — Prática em terminal: primeiros comandos

Esta seção apresenta e explica, linha por linha, os comandos de terminal[^18] introduzidos na aula. Um terminal (ou linha de comando) é uma interface de texto que permite controlar o computador digitando comandos diretamente, em vez de utilizar cliques e janelas gráficas — é a ferramenta de trabalho central de praticamente qualquer profissional de tecnologia, e será usada extensivamente ao longo de todo este curso.

```bash
# Ver estado geral instantâneo
nvidia-smi
```

Este comando, executado sozinho, exibe uma "fotografia" única do estado atual da GPU no exato momento em que o comando foi executado, incluindo todas as métricas descritas na seção anterior (utilização, memória, temperatura e processos).

```bash
# Monitoramento contínuo (atualiza a cada 1 segundo)
watch -n 1 nvidia-smi
```

O comando watch é um utilitário do sistema Linux que executa repetidamente um outro comando, em intervalos regulares de tempo, atualizando a tela automaticamente. O parâmetro `-n 1` especifica que o intervalo entre cada execução deve ser de 1 segundo. Ou seja, este comando transforma a "fotografia única" do nvidia-smi em algo parecido com um vídeo ao vivo do estado da GPU, atualizado a cada segundo — extremamente útil para observar, em tempo real, o comportamento da GPU enquanto ela executa alguma tarefa pesada.

```bash
# Saída limpa em formato CSV (útil para scripts)
nvidia-smi --query-gpu=utilization.gpu,memory.used --format=csv
```

Este comando utiliza dois parâmetros adicionais do nvidia-smi. O parâmetro `--query-gpu` permite especificar exatamente quais métricas devem ser exibidas (neste caso, utilização da GPU e memória usada), em vez de exibir o relatório completo padrão. O parâmetro `--format=csv` especifica que a saída deve ser formatada como CSV[^19] (Comma-Separated Values, ou Valores Separados por Vírgula), um formato de texto simples, tabular, amplamente utilizado para que outros programas ou scripts[^20] (pequenos programas de automação, tema da Unidade Curricular futura sobre automação com Bash) consigam ler e processar essa informação automaticamente, sem depender de interpretação humana visual.

### Alternativa sem GPU física

Caso o computador utilizado não possua uma GPU física disponível para testes, é possível utilizar o Google Colab, um serviço gratuito que disponibiliza acesso temporário a GPUs na nuvem, permitindo executar o comando `!nvidia-smi` (o sinal de exclamação antes do comando é a sintaxe utilizada no Google Colab para executar comandos de terminal dentro de uma célula de código) e observar o resultado da mesma forma.

---

## Slide 38 — Fechamento da aula e próximos passos

Ao longo desta primeira aula técnica do curso, foram estabelecidos os seguintes conceitos fundamentais:

- A diferença entre a Arquitetura Von Neumann (memória única, compartilhada entre dados e instruções, sujeita ao chamado Gargalo de Von Neumann) e a Arquitetura Harvard (memórias separadas para dados e instruções, permitindo maior largura de banda);
- A diferença estrutural entre CPU (poucos núcleos poderosos, otimizados para baixa latência em tarefas complexas) e GPU (milhares de núcleos simples, otimizados para alto throughput em tarefas repetitivas e paralelas);
- Por que essa diferença estrutural é exatamente o motivo pelo qual GPUs são tão mais eficientes que CPUs para treinar modelos de Inteligência Artificial;
- A ferramenta nvidia-smi e suas quatro métricas principais de monitoramento (utilização, memória, temperatura e processos);
- Os primeiros comandos práticos de terminal para monitorar uma GPU.

Na próxima aula, o curso avança para os modelos de processamento SIMD, MIMD, RISC e CISC — conceitos que explicam, em maior profundidade técnica, exatamente por que a GPU é fisicamente desenhada da forma como vimos nesta aula.

---

## Glossário completo de termos técnicos desta aula

[^1]: GPU (Graphics Processing Unit / Unidade de Processamento Gráfico): processador especializado em executar uma grande quantidade de operações simples em paralelo, originalmente projetado para renderização gráfica e hoje amplamente utilizado para treinar Inteligência Artificial.
[^2]: CPU (Central Processing Unit / Unidade Central de Processamento): o processador principal de um computador, otimizado para executar tarefas sequenciais complexas.
[^3]: Arquitetura Von Neumann: modelo de computador com memória única e barramento único, compartilhados entre dados e instruções.
[^4]: Barramento (bus): caminho físico por onde trafegam dados, endereços e sinais de controle dentro de um computador.
[^5]: Unidade de Controle (Control Unit): componente do processador responsável por coordenar a execução das instruções.
[^6]: Unidade Lógica e Aritmética (ALU): componente do processador responsável por executar operações matemáticas e lógicas.
[^7]: Gargalo de Von Neumann: limitação de desempenho causada pelo compartilhamento de um único barramento entre dados e instruções.
[^8]: Arquitetura Harvard: modelo de computador com memórias e barramentos separados para dados e instruções.
[^9]: Núcleo (core): unidade de processamento independente dentro de um processador.
[^10]: Paralelismo: execução simultânea de múltiplas operações.
[^11]: Latência: tempo necessário para concluir uma única operação.
[^12]: Throughput (vazão): quantidade total de trabalho processado em um intervalo de tempo.
[^13]: Memória cache: memória pequena e rápida, próxima ao núcleo do processador, usada para armazenar dados de acesso frequente.
[^14]: nvidia-smi (NVIDIA System Management Interface): ferramenta de linha de comando para monitoramento de GPUs NVIDIA.
[^15]: VRAM (Video RAM): memória própria de uma placa de vídeo (GPU), separada da memória RAM principal do computador.
[^16]: Throttling: redução automática de desempenho de um componente de hardware para evitar superaquecimento.
[^17]: PID (Process Identifier / Identificador de Processo): número único que identifica um processo em execução em um sistema operacional.
[^18]: Terminal (linha de comando): interface de texto para controle direto do computador por meio de comandos digitados.
[^19]: CSV (Comma-Separated Values / Valores Separados por Vírgula): formato de arquivo de texto simples e tabular, amplamente utilizado para troca de dados entre sistemas.
[^20]: Script: pequeno programa, geralmente utilizado para automatizar tarefas repetitivas.

---

Este material corresponde à Parte 4, a mais completa, da aula de abertura do curso. Os quatro materiais deste conjunto (Inteligência Artificial, Mercado de Trabalho, Sobre o Curso, e esta aula técnica) formam a base conceitual sobre a qual todo o restante da UC1, e do curso como um todo, será construído.
