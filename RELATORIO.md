# 📝 Relatório do Laboratório 2 - Chamadas de Sistema

---

## 1️⃣ Exercício 1a - Observação printf() vs 1b - write()

### 💻 Comandos executados:
```bash
strace -e write ./ex1a_printf
strace -e write ./ex1b_write
```

### 🔍 Análise

**1. Quantas syscalls write() cada programa gerou?**
- ex1a_printf: _____ syscalls
- ex1b_write: _____ syscalls

**2. Por que há diferença entre os dois métodos? Consulte o docs/printf_vs_write.md**

```
Não consigo deixar os arquivos traces e usar o dtruss ou dtrace (equivalente do macOS do strace), porque meu sistema está com System Integrity Protection (SIP) ativado.
```

**3. Qual método é mais previsível? Por quê você acha isso?**

```
Não consigo deixar os arquivos traces e usar o dtruss ou dtrace (equivalente do macOS do strace), porque meu sistema está com System Integrity Protection (SIP) ativado.
```

---

## 2️⃣ Exercício 2 - Leitura de Arquivo

### 📊 Resultados da execução:
- File descriptor: 3
- Bytes lidos: 127

### 🔧 Comando strace:
```bash
strace -e openat,read,close ./ex2_leitura
```

### 🔍 Análise

**1. Qual file descriptor foi usado? Por que não começou em 0, 1 ou 2?**

```
0=stdin, 1=stdout, 2=stderr, file=3
```

**2. Como você sabe que o arquivo foi lido completamente?**

```
read() returned 0 com exatamente o read do BUFFERSIZE -1 (127)
```

**3. Por que verificar retorno de cada syscall?**

```
evitar deixar erros de segurança. Syscalls são notórias por falhas sistêmicas de segurança caso interceptadas ou não administradas corretamente. O tratamento de erro delas é crucial não só para seguir com a execução do código onde existe dependência do sucesso de certas syscalls, mas também para tratamento de erro, debugging, e segurança.
```

---

## 3️⃣ Exercício 3 - Contador com Loop

### 📋 Resultados (BUFFER_SIZE = 64):
- Linhas: 25 (esperado: 25)
- Caracteres: 1300
- Chamadas read(): 21
- Tempo: 0.000236 segundos

### 🧪 Experimentos com buffer:

| Buffer Size | Chamadas read() | Tempo (s) |
|-------------|-----------------|-----------|
| 16          |      82         | 0.000341  |
| 64          |      21         | 0.000236  |
| 256         |       6         | 0.000237  |
| 1024        |       2         | 0.000217  |

### 🔍 Análise

**1. Como o tamanho do buffer afeta o número de syscalls?**

```
maior buffer = menos syscalls
menor buffer = mais syscalls

overhead e troca entre usermode para kernel. Custo fixo da syscall, com um buffer maior, aumenta eficiência e menos overhead.
```

**2. Todas as chamadas read() retornaram BUFFER_SIZE bytes? Discorra brevemente sobre**

```
o buffer é apenas igual seu nome diz, um buffer, armazenamento temporário para realizar uma tarefa. Não necessariamente toda chamada utilizará todo esse espaço reservado pelo buffer.
```

**3. Qual é a relação entre syscalls e performance?**

```
syscalls requerem transição entre usermode e kernel e tem custo fixo. Fazer uma quantidade exorbitante de syscalls com valores pequenos do que uma com um tamanho grande, é péssimo para a performance.
```

---

## 4️⃣ Exercício 4 - Cópia de Arquivo

### 📈 Resultados:
- Bytes copiados: 1364
- Operações: 6
- Tempo: 0.000582 segundos
- Throughput: 2288.71 KB/s

### ✅ Verificação:
```bash
diff dados/origem.txt dados/destino.txt
```
Resultado: [X] Idênticos [ ] Diferentes

### 🔍 Análise

**1. Por que devemos verificar que bytes_escritos == bytes_lidos?**

```
vários tipos de unexpected behaviour podem causar isso. Alguns fáceis de debugging (memory full, permission, etc), outros nem tanto. Mas written != read significa que a cópia falhou, e o target file está corrompido ou incompleto de alguma forma.
```

**2. Que flags são essenciais no open() do destino?**

```
 O_WRONLY | O_CREAT | O_TRUNC

  - O_WRONLY: Write only
  - O_CREAT: Create file, tratamento do caso que o user não preparou a file  
  - O_TRUNC: Truncate file para 0 bytes (limpa conteúdo 
  anterior)

  Sem O_CREAT: File does not exist, erro crucial
  Sem O_TRUNC: Resquícios de old data podem comprometer nosso write
  Sem O_WRONLY: No write permissions (read only by default no unix)

  Permissão 0644 também é importante: rw-r--r-- (owner rw, outros só 
  read)

```

**3. O número de reads e writes é igual? Por quê?**

```
É igual, pois a cópia foi bem sucedida sem corrupção, perda, e unexpected behaviour. Programa completou a execução planejada.
```

**4. Como você saberia se o disco ficou cheio?**

```
O retorno do write, ou catch do erro específico. Mas nesse caso, a simples verificação se write retornou -1 ou < bytes é suficiente.
```

**5. O que acontece se esquecer de fechar os arquivos?**

```
memory leak do file descriptor. O recurso existe mas não tem uso, em certos casos pode levar a fd overflow. Unexpected behaviour em diversas ações ligadas a fds e files por causa do recurso estagnado no sistema.
```

---

## 🎯 Análise Geral

### 📖 Conceitos Fundamentais

**1. Como as syscalls demonstram a transição usuário → kernel?**

```
[Sua análise aqui]
```

**2. Qual é o seu entendimento sobre a importância dos file descriptors?**

```
É um recurso crucial para o usermode comunicar com o kernel para a disponibilidade de diversos recursos cruciais para manipulação de arquivos.
```

**3. Discorra sobre a relação entre o tamanho do buffer e performance:**

```
overhead. Buffer maior = menos overhead = menos syscalls. É necessário otimizar a alocação do buffer para suas necessidades, e balancear ele com performance.
```

### ⚡ Comparação de Performance

```bash
# Teste seu programa vs cp do sistema
time ./ex4_copia
time cp dados/origem.txt dados/destino_cp.txt
```

**Qual foi mais rápido?** OS

**Por que você acha que foi mais rápido?**

```
cp está utilizando ao máximo recursos do hardware e não sofrem as limitações da linguagem C, e todo o adicional e processo de compilação. O nível de otimização não chega perto a poder ser comparado, mas o programa em C oferece a versatilidade de fácil implementação e uso em outras máquinas.
```

---

## 📤 Entrega
Certifique-se de ter:
- [ ] Todos os códigos com TODOs completados
- [ ] Traces salvos em `traces/`
- [ ] Este relatório preenchido como `RELATORIO.md`

```bash
strace -e write -o traces/ex1a_trace.txt ./ex1a_printf
strace -e write -o traces/ex1b_trace.txt ./ex1b_write
strace -o traces/ex2_trace.txt ./ex2_leitura
strace -c -o traces/ex3_stats.txt ./ex3_contador
strace -o traces/ex4_trace.txt ./ex4_copia
```
# Bom trabalho!
