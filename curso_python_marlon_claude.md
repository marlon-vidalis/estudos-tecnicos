# Curso Caseiro de Python — Escola Marlon+Claude

Perfil do leitor: alfabetizado em lógica via C++, iniciante em Python, ex-suporte técnico de hardware. Ver spec completa em `spec_curso_python.md`.

---

# MÓDULO 1 — Modelo Mental da Linguagem

## Lição 1.1 — O que é um paradigma de programação

**Conceito:** Paradigma é o "estilo de pensamento" que uma linguagem empurra você a usar pra resolver problemas. Não é regra rígida, é tendência.

- **Orientado a objetos (OO):** organiza o código em torno de objetos que têm dados + comportamento juntos. Java quase obriga isso — até um `Hello World` em Java precisa de uma classe.
- **Funcional:** organiza o código em torno de funções puras (sem efeito colateral), evita estado mutável. Haskell força esse estilo.
- **Procedural:** sequência de instruções, passo a passo, sem objeto obrigatório. C é o exemplo clássico.
- **Multi-paradigma:** deixa você escolher/misturar. C++ é multi-paradigma mas cobra sintaxe explícita pra cada estilo. Python também é multi-paradigma, mas com sintaxe mais permissiva — você pode escrever igual C procedural, igual Java OO, ou misturar num mesmo arquivo.

Analogia com C++: você já usou classes em C++ (OO) e provavelmente também escreveu funções soltas fora de classe (procedural) sem perceber que estava "trocando de paradigma". Python é assim, só que mais solto ainda.

**Código de exemplo:**
```python
# Estilo procedural (função solta, sem classe)
def saudacao(nome):
    return f"Olá, {nome}"

print(saudacao("Marlon"))
```
Saída: `Olá, Marlon`

```python
# Estilo orientado a objetos (mesma ideia, dentro de uma classe)
class Saudador:
    def cumprimentar(self, nome):
        return f"Olá, {nome}"

s = Saudador()
print(s.cumprimentar("Marlon"))
```
Saída: `Olá, Marlon`

**Nota idiomática:** Python não te pune por escrever procedural. Diferente de Java, você não é obrigado a embrulhar tudo em classe. Use classe quando o problema pedir agrupar dado+comportamento; não use só porque "é o certo".

**Exercício:** Reescreva a função `saudacao` para receber dois nomes e cumprimentar os dois numa mesma frase. Rode e confira a saída.

---

## Lição 1.2 — Tipagem: estática vs dinâmica, forte vs fraca

**Conceito:** Tipagem é a regra que define quando e como o tipo de uma variável é decidido e verificado.

- **Estática (C++):** o tipo é fixado na declaração e checado em tempo de compilação. `int x = 5;` — `x` é `int` para sempre, o compilador barra se você tentar `x = "texto"`.
- **Dinâmica (Python):** o tipo é decidido em tempo de execução (runtime), e pode mudar. `x = 5` depois `x = "texto"` funciona sem erro de compilação — porque não existe compilação, existe interpretação linha a linha.
- **Forte vs fraca:** não é sobre estática/dinâmica, é sobre se a linguagem converte tipos escondido de você. Python é dinamicamente tipada mas **fortemente** tipada — não faz conversão implícita entre tipos incompatíveis.

**Código de exemplo:**
```python
x = 5
print(type(x))   # o tipo é decidido agora, em runtime
x = "agora sou texto"
print(type(x))   # e pode mudar, sem erro
```
Saída:
```
<class 'int'>
<class 'str'>
```

```python
# Tipagem forte: Python não converte str + int escondido
resultado = "5" + 5
```
Saída: `TypeError: can only concatenate str (not "int") to str`

**Nota idiomática:** Anti-exemplo — tentar "declarar tipo" do jeito C++ não existe em Python puro (`int x = 5` dá erro de sintaxe). O jeito pythônico é confiar no runtime, mas se quiser documentar intenção de tipo, usa-se **type hint** (visto no Módulo 6): `x: int = 5` — isso é só documentação, o Python não trava se você desrespeitar.

**Exercício:** No terminal Python, digite `type(3.14)`, `type(True)`, `type([1,2,3])` e anote o que cada um retorna.

---

## Lição 1.3 — Gerenciamento de memória (introdução)

**Conceito:** Toda variável, em qualquer linguagem, ocupa espaço na memória RAM. A diferença entre linguagens é **quem é responsável por liberar esse espaço** quando não é mais usado.

- **C/C++:** você controla manualmente. `new` aloca, `delete` libera. Esquecer o `delete` = vazamento de memória (memory leak) — o programa vai consumindo RAM até travar.
- **Python (e Java, JS):** tem **garbage collector** — um processo automático que detecta quando um objeto não tem mais nenhuma referência apontando pra ele, e libera a memória sozinho.
- **Rust:** um terceiro modelo, chamado *ownership* — o compilador rastreia em tempo de compilação quem "dono" de cada dado e libera automaticamente quando o dono sai de escopo, sem precisar de garbage collector rodando em runtime. Só citando pra você saber que existe; não vamos usar Rust aqui.

Ponte com hardware: pensa no `new` do C++ como "reservar uma mesa no restaurante manualmente e você precisa lembrar de cancelar a reserva". O garbage collector do Python é o garçom que passa de tempos em tempos e libera mesas que ninguém está mais sentado.

**Código de exemplo:**
```python
import sys

a = [1, 2, 3]
b = a  # b aponta para o MESMO objeto na memória, não uma cópia
print(sys.getrefcount(a))  # quantas referências apontam pro mesmo objeto
```
Saída (aproximada, varia): `3` (a, b, e uma referência temporária do próprio getrefcount)

**Nota idiomática:** Você não vai chamar o garbage collector manualmente quase nunca — ele é automático e silencioso. Vamos ver isso de forma mais tangível no Módulo 3, quando falarmos de listas e cópia de referência vs cópia de valor — é onde esse conceito costuma confundir quem vem de ponteiro em C++.

**Exercício:** Rode o código acima e depois faça `b = None`. Rode `sys.getrefcount(a)` de novo — o número deve cair, mostrando que uma referência a menos aponta pro objeto.

---

# MÓDULO 2 — Sintaxe Essencial + Primeiro Script Rodando

## Lição 2.1 — Indentação como delimitador de bloco

**Conceito:** Em C++, blocos são delimitados por `{ }`, e o `;` fecha cada instrução — a indentação é só estética. Em Python, a indentação **é** a sintaxe: ela define onde um bloco começa e termina. Não existe `;` obrigatório nem `{ }`.

**Código de exemplo:**
```python
if True:
    print("dentro do bloco")   # indentado = pertence ao if
print("fora do bloco")          # sem indentação = já saiu do if
```
Saída:
```
dentro do bloco
fora do bloco
```

**Nota idiomática/anti-exemplo:** Misturar espaços e tabs na indentação quebra o script com `IndentationError`. Configure o VSCode para converter Tab em 4 espaços (padrão PEP 8) e evite dor de cabeça.

**Exercício:** Escreva um `if` que imprime "par" ou "ímpar" dependendo de um número, usando `%` (operador de resto).

---

## Lição 2.2 — Variáveis, tipos primitivos e f-strings

**Conceito:** Variável em Python não precisa de tipo declarado (ver Lição 1.2). f-string é o jeito moderno de inserir variáveis dentro de texto.

**Código de exemplo:**
```python
nome = "Marlon"
idade = 30
altura = 1.75
estuda = True

print(f"{nome} tem {idade} anos e {altura}m de altura. Estuda: {estuda}")
```
Saída: `Marlon tem 30 anos e 1.75m de altura. Estuda: True`

**Nota idiomática:** Anti-exemplo — concatenar com `+` (`"Nome: " + nome + " idade: " + str(idade)`) funciona, mas obriga conversão manual com `str()` e fica ilegível com várias variáveis. f-string é o jeito pythônico.

**Exercício:** Crie três variáveis suas (nome, curso, ano de formatura previsto) e monte uma frase com f-string.

---

## Lição 2.3 — if/elif/else

**Conceito:** Estrutura condicional. Igual ao `if/else if/else` do C++, mas sem `{}` e com `elif` (contração de "else if") em vez de `else if`.

**Código de exemplo:**
```python
nota = 7

if nota >= 9:
    print("Excelente")
elif nota >= 6:
    print("Aprovado")
else:
    print("Reprovado")
```
Saída: `Aprovado`

**Exercício:** Adapte para 4 faixas de nota (A, B, C, D) e teste com 3 valores diferentes.

---

## Lição 2.4 — for, while, range

**Conceito:** `for` em Python itera sobre uma sequência de itens diretamente (não sobre um índice numérico como o `for` clássico de C++). `range(n)` gera uma sequência de números de 0 até n-1, útil quando você precisa de um índice.

**Código de exemplo:**
```python
for i in range(5):
    print(i)
```
Saída:
```
0
1
2
3
4
```

```python
# for iterando direto sobre uma lista, sem precisar de índice
frutas = ["maçã", "banana", "uva"]
for fruta in frutas:
    print(fruta)
```
Saída:
```
maçã
banana
uva
```

```python
contador = 0
while contador < 3:
    print(f"contador = {contador}")
    contador += 1
```
Saída:
```
contador = 0
contador = 1
contador = 2
```

**Nota idiomática:** Anti-exemplo comum de quem vem de C++: `for i in range(len(frutas)): print(frutas[i])`. Funciona, mas não é pythônico — o jeito idiomático é iterar direto sobre a lista, como no segundo exemplo, a menos que você realmente precise do índice (nesse caso, usa-se `enumerate`, visto no Módulo 6).

**Exercício:** Some os números de 1 a 10 usando um `for` com `range`, guardando o total numa variável acumuladora.

---

## Lição 2.5 — Projeto de fechamento do módulo

**Tarefa:** Escreva um script `calculadora.py` que:
1. Define duas variáveis numéricas.
2. Usa `if/elif/else` para escolher entre soma, subtração, multiplicação ou divisão (baseado numa terceira variável tipo `"soma"`).
3. Imprime o resultado com f-string.

Rode no VSCode e confirme a saída antes de seguir pro Módulo 3.

---

# MÓDULO 3 — Coleções + Memória na Prática

## Lição 3.1 — Listas

**Conceito:** Lista é uma coleção ordenada e **mutável** (pode ser alterada depois de criada). Equivalente mais próximo em C++ seria um `std::vector`, mas sem tipo fixo — uma lista Python pode misturar tipos diferentes.

**Código de exemplo:**
```python
numeros = [10, 20, 30]
numeros.append(40)
numeros[0] = 99
print(numeros)
```
Saída: `[99, 20, 30, 40]`

**Exercício:** Crie uma lista com 5 nomes e remova o terceiro usando `.pop(2)`.

---

## Lição 3.2 — Tuplas

**Conceito:** Igual a lista, mas **imutável** — uma vez criada, não muda. Usada quando você quer garantir que os dados não sejam alterados por acidente.

**Código de exemplo:**
```python
coordenada = (10, 20)
print(coordenada[0])

coordenada[0] = 99  # isso vai dar erro
```
Saída:
```
10
TypeError: 'tuple' object does not support item assignment
```

**Exercício:** Explique com suas palavras (comentário no código) por que retornar coordenadas geográficas como tupla faz mais sentido do que como lista.

---

## Lição 3.3 — Dicionários

**Conceito:** Coleção de pares chave-valor, sem ordem garantida por posição numérica (a chave é o "índice"). Equivalente ao `std::map` do C++, mas com sintaxe muito mais direta.

**Código de exemplo:**
```python
pessoa = {"nome": "Marlon", "curso": "Geografia"}
print(pessoa["nome"])
pessoa["ano"] = 2027   # adiciona uma nova chave
print(pessoa.get("cidade", "não informado"))  # .get evita erro se a chave não existir
```
Saída:
```
Marlon
não informado
```

**Nota idiomática:** Anti-exemplo — usar `pessoa["cidade"]` diretamente quando a chave pode não existir gera `KeyError` e quebra o programa. `.get()` com valor padrão é o jeito seguro e idiomático.

**Exercício:** Crie um dicionário representando seu próprio perfil (nome, curso, universidade) e adicione uma chave nova depois de criado.

---

## Lição 3.4 — Sets

**Conceito:** Coleção não-ordenada de itens **únicos** (sem duplicata). Útil pra remover repetições ou checar pertencimento rápido.

**Código de exemplo:**
```python
numeros = [1, 2, 2, 3, 3, 3]
unicos = set(numeros)
print(unicos)
```
Saída: `{1, 2, 3}`

**Exercício:** Dado `["rs", "sc", "pr", "rs", "sc"]`, use `set()` para descobrir quantos estados únicos existem.

---

## Lição 3.5 — Slicing

**Conceito:** Fatiar uma sequência (lista, tupla, string) pegando um pedaço dela, usando `[início:fim]`. O `fim` nunca é incluído.

**Código de exemplo:**
```python
letras = ["a", "b", "c", "d", "e"]
print(letras[1:3])   # pega índice 1 e 2, não o 3
print(letras[:2])    # do início até o índice 2 (exclusive)
print(letras[-1])    # último item, índice negativo conta de trás pra frente
```
Saída:
```
['b', 'c']
['a', 'b']
e
```

**Exercício:** Dada a string `"UFFS Erechim"`, use slicing para extrair só `"UFFS"`.

---

## Lição 3.6 — Compreensões de lista e dict

**Conceito:** Sintaxe compacta para criar uma lista (ou dict) aplicando uma operação a cada item de uma sequência, substituindo um `for` + `.append()` em uma linha só.

**Código de exemplo:**
```python
# Jeito "tradicional" (não-idiomático para casos simples)
dobrados = []
for n in range(5):
    dobrados.append(n * 2)
print(dobrados)

# Jeito idiomático: list comprehension
dobrados2 = [n * 2 for n in range(5)]
print(dobrados2)
```
Saída (ambos):
```
[0, 2, 4, 6, 8]
[0, 2, 4, 6, 8]
```

**Nota idiomática:** Use list comprehension para transformações simples (uma linha de lógica). Se a lógica interna exigir várias linhas ou condicionais complexos, o `for` tradicional é mais legível — comprehension não é "sempre melhor", é melhor quando cabe numa linha sem virar ilegível.

**Exercício:** Crie uma list comprehension que filtra só os números pares de `range(20)`.

---

## Lição 3.7 — Métodos essenciais de coleções

**Conceito:** Métodos são funções que já vêm "grudadas" no objeto (ver definição formal na Lição 4.0 abaixo, mas aqui já usamos na prática).

**Código de exemplo:**
```python
lista = [3, 1, 2]
lista.append(4)      # adiciona no fim
lista.sort()          # ordena
print(lista)

dicionario = {"a": 1, "b": 2}
for chave, valor in dicionario.items():  # .items() retorna pares chave-valor
    print(chave, valor)
```
Saída:
```
[1, 2, 3, 4]
a 1
b 2
```

**Exercício:** Use `.get()`, `.keys()` e `.values()` em um dicionário de sua escolha e imprima cada resultado.

---

## Lição 3.8 — Memória na prática: referência vs cópia

**Conceito:** Retomando a Lição 1.3 — quando você faz `lista2 = lista1`, **não está copiando os dados**, está fazendo `lista2` apontar para o mesmo objeto na memória que `lista1`. Isso é exatamente como um ponteiro em C++ apontando para o mesmo endereço. Para copiar de verdade (novo espaço de memória), usa-se `.copy()`.

**Código de exemplo:**
```python
lista1 = [1, 2, 3]
lista2 = lista1          # lista2 aponta pro MESMO objeto
lista2.append(4)
print(lista1)             # lista1 também mudou!
```
Saída: `[1, 2, 3, 4]`

```python
lista1 = [1, 2, 3]
lista3 = lista1.copy()   # lista3 é um objeto NOVO, cópia dos valores
lista3.append(4)
print(lista1)             # lista1 permanece intacta
print(lista3)
```
Saída:
```
[1, 2, 3]
[1, 2, 3, 4]
```

**Nota idiomática:** Esse é o bug mais comum de quem vem de linguagens com cópia por valor "por padrão". Em Python, listas, dicts e sets são sempre passados/atribuídos por referência; só tipos primitivos (int, str, float, bool, tuplas) se comportam como se fossem copiados por valor quando reatribuídos.

**Exercício:** Repita o exemplo acima com um dicionário em vez de lista, usando `.copy()`, e confirme que o comportamento é o mesmo.

---

# MÓDULO 4 — Funções e Escopo

## Lição 4.0 — Função, método e argumento: definições formais

**Conceito:**
- **Função:** bloco de código reutilizável, definido com `def`, chamado pelo nome seguido de `()`.
- **Método:** uma função que "mora dentro" de um objeto e é chamada com `objeto.metodo()`. Todo método é função, nem toda função é método.
- **Argumento:** o valor que você passa dentro dos parênteses ao chamar a função/método. **Parâmetro** é o nome que a função usa internamente para receber esse valor (a distinção é sutil e muitas vezes usada como sinônimo na prática).

**Código de exemplo:**
```python
def somar(a, b):     # a e b são PARÂMETROS
    return a + b

resultado = somar(2, 3)   # 2 e 3 são ARGUMENTOS passados na chamada
print(resultado)
```
Saída: `5`

**Exercício:** Escreva uma função `multiplicar(a, b)` e chame com dois argumentos diferentes, imprimindo o resultado.

---

## Lição 4.1 — Argumentos posicionais, keyword, *args e **kwargs

**Conceito:**
- **Posicional:** a ordem em que você passa importa (`somar(2, 3)`).
- **Keyword (nomeado):** você especifica o nome do parâmetro na chamada, ordem não importa (`somar(b=3, a=2)`).
- **`*args`:** recebe qualquer quantidade de argumentos posicionais extras, empacotados numa tupla.
- **`**kwargs`:** recebe qualquer quantidade de argumentos nomeados extras, empacotados num dicionário.

**Código de exemplo:**
```python
def apresentar(nome, idade=18):   # idade tem valor padrão
    print(f"{nome}, {idade} anos")

apresentar("Marlon")               # usa o padrão de idade
apresentar("Marlon", idade=30)     # sobrescreve via keyword
```
Saída:
```
Marlon, 18 anos
Marlon, 30 anos
```

```python
def somar_tudo(*args):
    return sum(args)

print(somar_tudo(1, 2, 3, 4))
```
Saída: `10`

```python
def mostrar_dados(**kwargs):
    for chave, valor in kwargs.items():
        print(f"{chave}: {valor}")

mostrar_dados(nome="Marlon", curso="Geografia")
```
Saída:
```
nome: Marlon
curso: Geografia
```

**Nota idiomática:** `*args`/`**kwargs` aparecem muito em bibliotecas (você já viu `parents=True, exist_ok=True` no `.mkdir()` — isso é keyword argument sendo usado, não `**kwargs` na definição, mas o mesmo princípio de "nomear o que está passando").

**Exercício:** Escreva uma função que recebe `**kwargs` e imprime só as chaves (sem os valores).

---

## Lição 4.2 — Retorno múltiplo e lambda

**Conceito:** Uma função pode retornar mais de um valor de uma vez — na prática, o Python empacota tudo numa tupla automaticamente. Lambda é uma função anônima de uma linha só, usada quando a lógica é simples demais para justificar um `def` completo.

**Código de exemplo:**
```python
def dividir(a, b):
    quociente = a // b
    resto = a % b
    return quociente, resto     # retorno múltiplo

q, r = dividir(17, 5)            # desempacotamento automático
print(q, r)
```
Saída: `3 2`

```python
dobro = lambda x: x * 2
print(dobro(5))
```
Saída: `10`

**Nota idiomática:** Anti-exemplo — usar lambda para lógica complexa de múltiplas linhas é ilegível e não-pythônico. Lambda é para operações curtas, geralmente passadas como argumento pra outra função (ex: `sorted(lista, key=lambda x: x[1])`, visto no Módulo 6).

**Exercício:** Escreva uma função que retorna nome e idade separadamente, e desempacote o retorno em duas variáveis.

---

## Lição 4.3 — Escopo e closures básicas

**Conceito:** Escopo define onde uma variável "existe" e pode ser acessada. Variável criada dentro de uma função é **local** — só existe ali dentro. Variável criada fora é **global** — acessível de qualquer lugar (com ressalvas). Closure é quando uma função interna "lembra" de uma variável da função externa mesmo depois que a externa já terminou de executar.

**Código de exemplo:**
```python
x = 10   # global

def teste():
    x = 20   # local, diferente do global
    print(x)

teste()
print(x)   # o global não mudou
```
Saída:
```
20
10
```

```python
def criar_contador():
    contagem = 0
    def incrementar():
        nonlocal contagem   # sem isso, Python trataria contagem como nova variável local
        contagem += 1
        return contagem
    return incrementar

contador = criar_contador()
print(contador())
print(contador())
```
Saída:
```
1
2
```

**Nota idiomática:** Comparação com C++: em C++ você teria que usar uma classe ou variável estática para simular esse comportamento de "memória entre chamadas"; em Python, closure resolve isso de forma mais direta.

**Exercício:** Modifique `criar_contador` para aceitar um valor inicial diferente de 0.

---

# MÓDULO 5 — Módulos, Erros e Debug

## Lição 5.1 — import, from...import e if __name__ == "__main__"

**Conceito:** `import` traz um módulo (arquivo .py ou biblioteca) inteiro para uso. `from X import Y` traz só uma parte específica. `if __name__ == "__main__":` garante que um bloco de código só rode quando o arquivo é executado diretamente, não quando é importado por outro script.

**Código de exemplo:**
```python
import math
print(math.sqrt(16))

from math import sqrt
print(sqrt(16))   # mesma função, sem precisar prefixar "math."
```
Saída:
```
4.0
4.0
```

```python
# arquivo exemplo.py
def funcao_principal():
    print("rodando")

if __name__ == "__main__":
    funcao_principal()
```
Se você rodar `exemplo.py` diretamente: imprime `rodando`. Se outro arquivo fizer `import exemplo`, nada é impresso automaticamente — só roda se você chamar `exemplo.funcao_principal()` explicitamente.

**Exercício:** Crie dois arquivos: `utilitarios.py` com uma função, e `principal.py` que importa e usa essa função.

---

## Lição 5.2 — try/except/finally

**Conceito:** Mecanismo para capturar erros em tempo de execução sem travar o programa. `try` roda o código arriscado, `except` captura o erro se ele acontecer, `finally` roda sempre, com ou sem erro.

**Código de exemplo:**
```python
try:
    resultado = 10 / 0
except ZeroDivisionError:
    print("Não dá pra dividir por zero")
finally:
    print("Isso roda sempre")
```
Saída:
```
Não dá pra dividir por zero
Isso roda sempre
```

**Nota idiomática:** Capture exceções específicas (`ZeroDivisionError`, `KeyError`, `FileNotFoundError`) em vez de um `except:` genérico — isso esconde bugs que você não previu.

**Exercício:** Tente abrir um arquivo que não existe com `open("arquivo_falso.txt")` dentro de um `try/except FileNotFoundError`.

---

## Lição 5.3 — pip, venv, requirements.txt

**Conceito:** `pip` é o gerenciador de pacotes do Python — instala bibliotecas de terceiros. `venv` cria um ambiente virtual isolado por projeto, evitando que bibliotecas de projetos diferentes conflitem entre si. `requirements.txt` lista as dependências de um projeto para que outra pessoa (ou você, em outra máquina) reproduza o ambiente.

**Comandos (terminal, não código Python):**
```bash
python -m venv venv          # cria o ambiente virtual na pasta "venv"
venv\Scripts\activate         # ativa no Windows
source venv/bin/activate      # ativa no Linux/Mac
pip install requests          # instala uma biblioteca dentro do ambiente ativo
pip freeze > requirements.txt # salva a lista de dependências instaladas
pip install -r requirements.txt  # reinstala tudo a partir do arquivo
```

**Nota idiomática:** Sempre trabalhe dentro de um venv ativado por projeto. Instalar tudo globalmente (sem venv) é a causa mais comum de "funciona na minha máquina mas não na do colega".

**Exercício:** Crie um venv para um projeto de teste, ative, instale a biblioteca `requests`, e gere o `requirements.txt`.

---

## Lição 5.4 — Ler e depurar código

**Conceito:** Ler um traceback de cima para baixo é ler o histórico da chamada até o erro; a última linha geralmente diz o tipo de erro e a mensagem, e a penúltima costuma apontar a linha exata do problema. Debugger é uma ferramenta que pausa a execução (breakpoint) para você inspecionar variáveis passo a passo, em vez de espalhar `print()` pelo código.

**Como ler um traceback (exemplo):**
```
Traceback (most recent call last):
  File "script.py", line 5, in <module>
    resultado = dividir(10, 0)
  File "script.py", line 2, in dividir
    return a / b
ZeroDivisionError: division by zero
```
Leitura: o erro final é `ZeroDivisionError: division by zero` (última linha). Suba: aconteceu dentro da função `dividir`, na linha 2, chamada a partir da linha 5 do script principal.

**Debugger no VSCode:**
1. Clique à esquerda do número da linha para criar um breakpoint (ponto vermelho).
2. Rode com "Debug" em vez de "Run".
3. Quando pausar no breakpoint, use "Step Over" (executa a linha atual) ou "Step Into" (entra dentro da função chamada) para avançar.
4. Inspecione o valor das variáveis no painel lateral, sem precisar de `print()`.

**Como navegar código de terceiros no GitHub:**
1. Leia o `README.md` primeiro — geralmente explica o propósito e como rodar.
2. Procure o arquivo de entrada (`main.py`, `__main__.py`, ou o que estiver referenciado no README/`if __name__ == "__main__":`).
3. Siga os imports a partir dali, um de cada vez, em vez de tentar entender o projeto inteiro de uma vez.

**Exercício:** Provoque um `IndexError` de propósito (acessando um índice de lista que não existe) e leia o traceback completo antes de corrigir.

---

# MÓDULO 6 — Idiomático (Consolidação) + Projeto Integrador

## Lição 6.1 — Consolidação de notas idiomáticas

Recapitulando o que já apareceu disperso nos módulos anteriores, mais alguns complementos:

- **PEP 8:** guia oficial de estilo do Python (nomes em `snake_case`, 4 espaços de indentação, linhas com no máximo ~79-99 caracteres). VSCode com extensão de linter aponta violações automaticamente.
- **Type hints:** anotação opcional de tipo esperado, só para documentação/ferramentas — não é checado em runtime.
  ```python
  def somar(a: int, b: int) -> int:
      return a + b
  ```
- **`enumerate`:** dá índice + item ao mesmo tempo, evitando `range(len(lista))`.
  ```python
  for i, fruta in enumerate(["maçã", "banana"]):
      print(i, fruta)
  ```
  Saída: `0 maçã` / `1 banana`
- **`zip`:** percorre duas listas em paralelo.
  ```python
  nomes = ["Ana", "Bruno"]
  idades = [25, 30]
  for nome, idade in zip(nomes, idades):
      print(nome, idade)
  ```
  Saída: `Ana 25` / `Bruno 30`
- **`sorted(..., key=...)`:** ordena por critério customizado.
  ```python
  pessoas = [("Ana", 30), ("Bruno", 25)]
  pessoas_ordenadas = sorted(pessoas, key=lambda p: p[1])
  print(pessoas_ordenadas)
  ```
  Saída: `[('Bruno', 25), ('Ana', 30)]`
- **Context managers (`with`):** garante que um recurso (arquivo, conexão) seja fechado automaticamente, mesmo se der erro no meio.
  ```python
  with open("teste.txt", "w") as f:
      f.write("olá")
  # arquivo já foi fechado automaticamente aqui, sem precisar de f.close()
  ```
- **Generator expressions:** como list comprehension, mas não guarda tudo em memória de uma vez — gera item por item sob demanda. Sintaxe com `()` em vez de `[]`.
  ```python
  soma = sum(n * 2 for n in range(1000000))  # não cria a lista inteira em memória
  ```

**Exercício:** Reescreva um exemplo de leitura de arquivo do Módulo 5 usando `with` em vez de abrir/fechar manualmente.

---

## Lição 6.2 — Projeto Integrador

**Objetivo:** consolidar módulos 1-5 num script real.

**Tarefa:** Escreva um CLI (`processador.py`) que:
1. Lê um arquivo CSV simples (pode criar um de teste com 3-4 colunas, ex: nome, cidade, ano).
2. Usa `try/except` para tratar o caso do arquivo não existir.
3. Processa os dados: por exemplo, conta quantos registros existem por cidade (usando dicionário).
4. Usa uma função separada para o processamento (não tudo dentro do `if __name__ == "__main__":`).
5. Salva o resultado processado em um novo arquivo (JSON ou texto).
6. Usa pelo menos: uma list/dict comprehension, um `with`, uma f-string, e uma função com argumento nomeado.

Rode, depure com o VSCode debugger se algo falhar, e confirme a saída final antes de considerar concluído.

---

*Fim do material. Dúvidas ou trechos que precisem de mais profundidade: revisitar a lição específica antes de avançar.*
