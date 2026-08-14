# Linux no Colab — Guia Prático Completo
### Técnico em Inteligência Artificial (Senac RS) — UC1

> Todo comando abaixo é pra ser **rodado de verdade**, célula por célula, no Google Colab. Sempre com "!" na frente (exceto os de `%cd`, que usam "%").

---

## Por que vocês precisam saber disso, especificamente pra IA

Isso não é "cultura geral de TI" — cada bloco de comando resolve um problema real e recorrente do trabalho com Inteligência Artificial.

**Navegação e manipulação de arquivos** — datasets de IA são, na prática, uma bagunça de milhares de arquivos (imagens, textos, CSVs) organizados em pastas. Organizar isso, contar arquivos, mover os corrompidos pra outro lugar — não dá pra fazer clicando um por um.

**`cat`, `head`, `tail`** — um dataset em CSV pode ter gigabytes; abrir ele inteiro pode travar o computador. `head` é o jeito padrão de "espiar" as primeiras linhas sem abrir tudo. `tail -f` é como se monitora um treino rodando ao vivo, em tempo real.

**`grep` e `find`** — um treino rodou a noite toda e travou. Como descobrir por quê, sem ler um log de 10 mil linhas na mão? `grep "Error" treino.log` acha na hora.

**Processos (`ps`, `kill`)** — lembra do `nvidia-smi` mostrando um processo travado, ocupando a VRAM inteira? Sem saber `kill`, a única opção é reiniciar a máquina inteira. Em servidores compartilhados (o normal numa empresa de IA), matar só o processo travado — sem derrubar o servidor pra todo mundo — é rotina.

**Permissões (`chmod`)** — baixar um script e ele não rodar por falta de permissão de execução é erro clássico de iniciante. Em empresas, datasets sensíveis têm permissão restrita por política/lei.

**`df -h` e `free -h`** — baixar um dataset de 40GB sem checar espaço em disco trava o download na metade. E o erro mais comum em Machine Learning — "Out of Memory", o processo morrendo sem aviso — se evita checando a RAM disponível antes.

**Compactação (`zip`, `tar`)** — datasets do Kaggle e de repositórios de pesquisa quase sempre vêm compactados. Depois de treinar um modelo, compactar ele pra mandar pra outro lugar é rotina.

**`apt-get` / `pip`** — muita biblioteca de IA (processamento de imagem, áudio, vídeo) depende de programas do próprio sistema operacional, não só de bibliotecas Python. `apt-get install ffmpeg`, por exemplo, é pré-requisito de várias bibliotecas de vídeo.

**O fio condutor:** praticamente toda infraestrutura real de IA — servidores de nuvem, clusters de treino, containers que rodam modelos em produção — roda Linux, sem interface gráfica nenhuma. Terminal não é um "extra" do curso: é o ambiente onde o trabalho de verdade acontece.

---

## Duas particularidades do Colab, antes de começar

1. **Cada célula roda numa "sub-sessão" nova.** Um `!cd pasta` sozinho não muda de pasta pra célula seguinte — o efeito desaparece assim que a célula termina. Pra mudar de pasta **de forma permanente** entre células, usa-se `%cd pasta` (com `%`, não `!`).
2. **Você já é root** (o usuário administrador) no Colab. Por isso, testes de permissão às vezes não bloqueiam como bloqueariam num Linux comum — o root passa por cima da maioria das restrições.

---

## 1. Primeiro diagnóstico: onde você está

### `!pwd`
Mostra em qual pasta você está agora.

```
!pwd
```

**Saída esperada:**
```
/content
```

**O que isso significa:** `/content` é a pasta padrão onde o Colab coloca você ao abrir um notebook novo — é a "raiz de trabalho" da sua sessão.

### `!ls`
Lista o que tem na pasta atual.

```
!ls
```

**Saída esperada:**
```
sample_data
```

**O que isso significa:** `sample_data` é uma pasta que o próprio Colab já cria automaticamente em toda sessão nova, com alguns datasets de exemplo do Google — não fomos nós que criamos.

### `!ls -la`
Lista com detalhes, incluindo arquivos ocultos.

```
!ls -la
```

**Saída esperada:**
```
total 20
drwxr-xr-x 1 root root 4096 jan 10 12:00 .
drwxr-xr-x 1 root root 4096 jan 10 12:00 ..
drwxr-xr-x 4 root root 4096 jan 10 11:58 .config
drwxr-xr-x 2 root root 4096 jan 10 11:58 sample_data
```

**O que isso significa:** cada linha é um item, com permissões (`drwxr-xr-x`), dono/grupo (`root root`), tamanho, data, e nome. O `.` representa a própria pasta atual; o `..` representa a pasta de cima. `.config` é uma pasta oculta (começa com ponto) — por isso só apareceu com o `-a`.

---

## 2. Explorando a estrutura de diretórios do Linux

```
!ls /
!ls /home
!ls /etc
!ls /bin
!ls /usr
!ls /var
```

**Saída esperada (para `!ls /`):**
```
bin  boot  content  datalab  dev  etc  home  lib  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

**O que isso significa:** essa é a estrutura raiz que vimos na aula, ao vivo, dentro de um Linux de verdade. Repare no `content` — é a pasta especial que o Colab cria, diferente de um Linux "puro". As outras (`bin`, `etc`, `home`, `usr`, `var`) são exatamente as pastas padrão que vimos no diagrama da aula.

---

## 3. Criando sua área de trabalho

### `!mkdir -p`
Cria uma ou mais pastas de uma vez, incluindo as intermediárias.

```
!mkdir -p /content/meu-projeto/dados
```

**Saída esperada:** (nenhuma — no Linux, comando que funciona sem erro geralmente não imprime nada)

**O que isso significa:** o silêncio é o resultado esperado! Se algo der errado (por exemplo, sem permissão), você veria uma mensagem de erro. Vamos confirmar que funcionou no próximo passo.

### `%cd` — mudando de pasta de forma permanente
```
%cd /content/meu-projeto
```

**Saída esperada:**
```
/content/meu-projeto
```

**O que isso significa:** diferente do `!cd`, o `%cd` é um "comando mágico" do Colab que muda a pasta de trabalho pra valer, persistindo nas próximas células. Ele já confirma o novo caminho na saída.

### `!touch`
Cria um arquivo vazio.

```
!touch notas.txt
!ls -l
```

**Saída esperada:**
```
total 4
drwxr-xr-x 2 root root 4096 jan 10 12:05 dados
-rw-r--r-- 1 root root    0 jan 10 12:06 notas.txt
```

**O que isso significa:** `notas.txt` aparece com tamanho `0` — porque `touch` só cria o arquivo vazio, sem escrever nada dentro dele. O `dados` é a subpasta que criamos no `mkdir -p`.

---

## 4. Escrevendo e lendo arquivos

### `!echo` com `>` e `>>`
```
!echo "linha 1" > log.txt
!echo "linha 2 com erro" >> log.txt
!echo "linha 3" >> log.txt
!cat log.txt
```

**Saída esperada:**
```
linha 1
linha 2 com erro
linha 3
```

**O que isso significa:** `>` cria (ou sobrescreve) o arquivo com aquele conteúdo; `>>` adiciona uma linha nova, sem apagar o que já tinha. O `cat` mostrou o arquivo inteiro, com as 3 linhas na ordem em que foram escritas.

### `!head` e `!tail`
```
!head -n 2 log.txt
!tail -n 2 log.txt
```

**Saída esperada:**
```
linha 1
linha 2 com erro
```
```
linha 2 com erro
linha 3
```

**O que isso significa:** `head -n 2` mostrou só as 2 primeiras linhas; `tail -n 2` mostrou só as 2 últimas. Num arquivo de log real com milhares de linhas, isso evita ter que carregar o arquivo inteiro só pra ver o começo ou o fim.

### `!wc -l`
```
!wc -l log.txt
```

**Saída esperada:**
```
3 log.txt
```

**O que isso significa:** conta quantas linhas o arquivo tem (3), seguido do nome do arquivo. Muito usado pra saber rapidamente "quantos registros" tem um arquivo, sem abri-lo.

---

## 5. Copiando, movendo e apagando

```
!cp log.txt backup.txt
!ls
```

**Saída esperada:**
```
backup.txt  dados  log.txt  notas.txt
```

**O que isso significa:** `backup.txt` é uma cópia idêntica de `log.txt` — os dois existem agora, independentes um do outro.

```
!mv notas.txt anotacoes.txt
!ls
```

**Saída esperada:**
```
anotacoes.txt  backup.txt  dados  log.txt
```

**O que isso significa:** `notas.txt` sumiu da lista, e `anotacoes.txt` apareceu no lugar — `mv` moveu o arquivo pro "mesmo lugar, com nome novo", que é como se renomeia no Linux.

```
!rm backup.txt
!ls
```

**Saída esperada:**
```
anotacoes.txt  dados  log.txt
```

**O que isso significa:** `backup.txt` foi apagado — e note que não existe "lixeira" no terminal: uma vez rodado o `rm`, o arquivo já era, sem recuperação fácil.

---

## 6. Buscando arquivos e texto

```
!echo "processando dados" > dados/etapa1.txt
!echo "ocorreu um erro na etapa 2" > dados/etapa2.txt
!echo "finalizado com sucesso" > dados/etapa3.txt

!find . -name "*.txt"
```

**Saída esperada:**
```
./log.txt
./anotacoes.txt
./dados/etapa1.txt
./dados/etapa2.txt
./dados/etapa3.txt
```

**O que isso significa:** `find` vasculhou a pasta atual (`.`) e todas as subpastas, achando todo arquivo cujo NOME termina em `.txt` — inclusive dentro de `dados/`.

```
!grep -r "erro" .
```

**Saída esperada:**
```
./log.txt:linha 2 com erro
./dados/etapa2.txt:ocorreu um erro na etapa 2
```

**O que isso significa:** `grep -r` (recursivo) procurou a palavra "erro" DENTRO do CONTEÚDO de todos os arquivos, em todas as subpastas — e mostrou exatamente qual arquivo e qual linha contém a palavra. Repare que é bem diferente do `find`: um busca pelo nome do arquivo, o outro busca pelo que está escrito dentro.

---

## 7. Permissões

```
!ls -l log.txt
```

**Saída esperada:**
```
-rw-r--r-- 1 root root 33 jan 10 12:10 log.txt
```

**O que isso significa:** `-rw-r--r--` — o dono (root) pode ler e escrever; o grupo e outros só podem ler. Nenhum dos três tem permissão de execução (`x`), porque isso é um arquivo de texto, não um programa.

```
!chmod +x log.txt
!ls -l log.txt
```

**Saída esperada:**
```
-rwxr-xr-x 1 root root 33 jan 10 12:10 log.txt
```

**O que isso significa:** o `+x` adicionou permissão de execução pros três grupos (dono, grupo, outros) de uma vez. Isso não faz sentido pra um `.txt` na prática (não é um programa executável de verdade) — foi só pra ver o `chmod` mudando a saída do `ls -l` ao vivo.

> **Nota:** como você é root no Colab, mesmo removendo TODAS as permissões de um arquivo (`chmod 000 log.txt`), o root ainda consegue ler/escrever nele — é uma limitação de testar isso especificamente no Colab. Pra ver permissões bloqueando de verdade, o Git Bash (ou um Linux com usuário comum) mostra melhor.

---

## 8. Processos — o ponto que fica devendo, na maioria dos cursos

### `!whoami`
```
!whoami
```

**Saída esperada:**
```
root
```

**O que isso significa:** confirma que você está rodando tudo como o usuário administrador — é por isso que quase nenhum comando dá erro de permissão no Colab.

### `!ps aux`
```
!ps aux | head -6
```

**Saída esperada:**
```
USER   PID  %CPU %MEM    VSZ   RSS TTY  STAT START   TIME COMMAND
root     1   0.0  0.1  8892  6120 ?    Ss   11:58   0:00 /sbin/docker-init
root     7   0.1  1.2 412300 98200 ?   Sl   11:58   0:02 /usr/bin/python3 /usr/local/bin/colab-fileshim.py
root    23   0.0  0.8 320100 65400 ?   Sl   11:58   0:01 /usr/bin/python3 -m ipykernel_launcher
root    45   0.0  0.0   7200  3400 ?    R    12:11   0:00 ps aux
```

**O que isso significa:** cada linha é um processo rodando na máquina agora — repare que o próprio `ps aux` aparece na lista (o último processo, ainda "se vendo" rodar). `%CPU` e `%MEM` mostram o quanto de processador e memória cada um está usando; `PID` é o número único de cada processo — é ele que usamos pra "mirar" num processo específico com o `kill`.

### `!kill`
```
# supondo que você viu, no ps aux, um processo travado com PID 1234
!kill 1234
```

**Saída esperada:** (nenhuma, se o processo existir e puder ser encerrado)

**O que isso significa:** o silêncio de novo é sinal de sucesso. Se o PID não existisse, apareceria um erro tipo `kill: (1234): No such process`.

### `!pkill`
```
!pkill -f "nome_do_script.py"
```

**O que isso significa:** em vez de precisar descobrir o PID primeiro, `pkill` já busca e mata processo(s) pelo NOME (ou parte do comando, com a flag `-f`) — útil quando você sabe o nome do script que travou, mas não decorou o número.

### `!nice`
```
!nice -n 10 python3 script.py
```

**O que isso significa:** roda o script com prioridade REDUZIDA (números mais altos = menos prioridade) — pedindo educadamente pro escalonador (lembra da Aula 2?) dar preferência a outros processos, quando o seu não é urgente.

---

## 9. Informações do sistema

```
!uname -a
```

**Saída esperada:**
```
Linux a1b2c3d4e5f6 6.1.85+ #1 SMP PREEMPT_DYNAMIC Ubuntu x86_64 GNU/Linux
```

**O que isso significa:** mostra o nome do sistema (Linux), o "hostname" da máquina (um nome aleatório gerado pelo Google), a versão do kernel, e a arquitetura (x86_64 — a mesma família CISC que vimos na Aula 2).

```
!df -h
```

**Saída esperada:**
```
Filesystem      Size  Used Avail Use% Mounted on
overlay         108G   28G   75G  27% /
tmpfs            64M     0   64M   0% /dev
shm              5.7G     0  5.7G   0% /dev/shm
```

**O que isso significa:** mostra o espaço em disco. A linha `/` é a mais importante — é a partição principal, mostrando quanto está usado (`Used`) e quanto ainda tem livre (`Avail`). É exatamente o que checar ANTES de baixar um dataset gigante.

```
!free -h
```

**Saída esperada:**
```
              total        used        free      shared  buff/cache   available
Mem:           12Gi       1.2Gi       8.5Gi       1.0Mi       2.3Gi        11Gi
Swap:            0B          0B          0B
```

**O que isso significa:** mostra a memória RAM. `total` é o total disponível na sessão do Colab (varia, mas gira em torno de 12GB no plano gratuito); `used` é o que já está ocupado; `available` é uma estimativa de quanto ainda dá pra usar sem travar — é o número que se checa antes de tentar carregar um dataset gigante inteiro na memória.

---

## 10. Rede (retomando a Aula 4)

```
!hostname -I
```

**Saída esperada:**
```
172.28.0.12
```

**O que isso significa:** o endereço IP da própria máquina do Colab, dentro da rede interna do Google — uma faixa privada, já que essa máquina fica escondida atrás da infraestrutura do Google, não exposta direto na internet.

```
!cat /etc/resolv.conf
```

**Saída esperada:**
```
nameserver 8.8.8.8
```

**O que isso significa:** mostra qual servidor DNS a máquina usa pra traduzir nomes de site em endereços IP — nesse caso, o próprio DNS público do Google.

> **Nota:** `!ping` normalmente NÃO funciona no Colab — o ambiente bloqueia esse tipo de pacote por segurança. Não é erro seu, é uma limitação conhecida do ambiente.

---

## 11. Compactação

```
!zip -r projeto.zip meu-projeto/
```

**Saída esperada:**
```
  adding: meu-projeto/ (stored 0%)
  adding: meu-projeto/log.txt (deflated 12%)
  adding: meu-projeto/anotacoes.txt (stored 0%)
  adding: meu-projeto/dados/ (stored 0%)
  adding: meu-projeto/dados/etapa1.txt (deflated 8%)
  adding: meu-projeto/dados/etapa2.txt (deflated 15%)
  adding: meu-projeto/dados/etapa3.txt (deflated 10%)
```

**O que isso significa:** cada linha confirma um arquivo/pasta sendo adicionado ao `.zip`, junto com a taxa de compressão alcançada (`deflated 12%` significa que aquele arquivo ficou 12% menor depois de compactado).

```
!unzip -l projeto.zip
```

**O que isso significa:** o `-l` lista o CONTEÚDO do zip sem descompactar nada — útil pra conferir o que tem dentro antes de extrair de verdade.

---

## Script consolidado — rode tudo em sequência

```python
%cd /content
!mkdir -p meu-teste/dados
%cd meu-teste
!echo "linha 1" > log.txt
!echo "linha 2 com erro" >> log.txt
!echo "linha 3" >> log.txt
!cat log.txt
!grep "erro" log.txt
!ls -l log.txt
!chmod 644 log.txt
!ps aux | head -5
!whoami
!df -h
!zip -r backup.zip .
!ls -l backup.zip
```

Rodando célula por célula (ou tudo de uma vez), esse script cobre navegação, criação, escrita, busca, permissão, processo, sistema e compactação — praticamente o indicador inteiro da UC1 sobre Linux, dentro do próprio Colab.
