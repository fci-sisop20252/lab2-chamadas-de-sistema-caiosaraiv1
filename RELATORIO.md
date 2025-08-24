# Relatório do Laboratório 2 - Chamadas de Sistema

---

## Exercício 1a - Observação printf() vs 1b - write()

### Comandos executados:
```bash
strace -e write ./ex1a_printf
strace -e write ./ex1b_write
```

### Análise

**1. Quantas syscalls write() cada programa gerou?**
- ex1a_printf: __9__ syscalls
- ex1b_write: __7__ syscalls

**2. Por que há diferença entre printf() e write()?**

```
A diferença aconteceu pois no código A 2 dois dos printf foram quebrados em 2 syscalls por causa dos \n.
```

**3. Qual implementação você acha que é mais eficiente? Por quê?**

```
Acredito que a implementação A. Pois na B foi necessário alocar mais espaço na memoria para armazenar os arrays das mensagens e depois fazer calculo do tamanho deles para usar no write. 
```

---

## Exercício 2 - Leitura de Arquivo

### Resultados da execução:
- File descriptor: _____
- Bytes lidos: _____

### Comando strace:
```bash
strace -e open,read,close ./ex2_leitura
```

### Análise

**1. Por que o file descriptor não foi 0, 1 ou 2?**

```
Pois os valores 0, 1 ou 2 são reservados. Sendo o 0 para sdtin, 1 para stdout e 2 para stderr. Como foi aberto com sucesso recebeu o 3.
```

**2. Como você sabe que o arquivo foi lido completamente?**

```
Pois a variavel bytes_lidos tem valor de 127, ou seja a função read retornou 127 bytes, o que corresponde com tamanho do buffer que é de 128 menos 1.
```

---

## Exercício 3 - Contador com Loop

### Resultados (BUFFER_SIZE = 64):
- Linhas: __24__ (esperado: 25)
- Caracteres: __1299__
- Chamadas read(): __21__
- Tempo: __0.000107__ segundos

### Experimentos com buffer:

| Buffer Size | Chamadas read() | Tempo (s) |
|-------------|-----------------|-----------|
| 16          |      87         | 0.000221  |
| 64          |      21         | 0.000107  |
| 256         |      6          | 0.000093  |
| 1024        |      2          | 0.000096  |

### Análise

**1. Como o tamanho do buffer afeta o número de syscalls?**

```
Quanto menor o buffer, mais chamadas serão feitas.
```

**2. Como você detecta o fim do arquivo?**

```
A função read() retona o valor 0 quando chega no fim do arquivo.
```

---

## Exercício 4 - Cópia de Arquivo

### Resultados:
- Bytes copiados: __1364__
- Operações: __6__
- Tempo: __0.000253__ segundos
- Throughput: __5264.95__ KB/s

### Verificação:
```bash
diff dados/origem.txt dados/destino.txt
```
Resultado: [X] Idênticos [ ] Diferentes

### Análise

**1. Por que devemos verificar que bytes_escritos == bytes_lidos?**

```
Para verificar se houve ou não perda de dados durante a cópia.
```

**2. Que flags são essenciais no open() do destino?**

```
São essenciais a O_WRONLY, O_CREAT e a O_TRUNC.
```

---

## Análise Geral

### Conceitos Fundamentais

**1. Como as syscalls demonstram a transição usuário → kernel?**

```
Elas demostram essa transição pois cada chamada representa o momento que o programa deixa o modo usuário para o kernel executar a operação.
```

**2. Qual é o seu entendimento sobre a importância dos file descriptors?**

```
File descriptors são importantes pois o programa não precisa saber onde o arquivo está armazenado, ele pode usar o fd para ler, escrever e etc.
```

**3. Discorra sobre a relação entre o tamanho do buffer e performance:**

```
Quanto menor o tamanho do buffer mais syscalls, por conseguinte, mais sobrecarga na CPU e maior a lentidão. Já quanto maior o tamanho do buffer menos syscalls, diminuindo a sobrecarga na CPU e deixando a execução do programa mais rapido. 
```

### Comparação de Performance

```bash
# Teste seu programa vs cp do sistema
time ./ex4_copia
time cp dados/origem.txt dados/destino_cp.txt
```

**Qual foi mais rápido?** __./ex4_copia__

**Por que você acha que foi mais rápido?**

```
Acredito que foi por causa de realizar poucas syscalls e porque o programa seja mais especifico do que o 'cp' que é algo mais genérico.
```

---

## Entrega

Certifique-se de ter:
- [X] Todos os códigos com TODOs completados
- [X] Traces salvos em `traces/`
- [X] Este relatório preenchido como `RELATORIO.md`

```bash
strace -e write -o traces/ex1a_trace.txt ./ex1a_printf
strace -e write -o traces/ex1b_trace.txt ./ex1b_write
strace -o traces/ex2_trace.txt ./ex2_leitura
strace -c -o traces/ex3_stats.txt ./ex3_contador
strace -o traces/ex4_trace.txt ./ex4_copia
```
