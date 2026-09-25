# Parte 4 — Primitivas Gráficas e Algoritmos de Rasterização

## Objetivos de aprendizagem

Ao final deste capítulo, o estudante deverá ser capaz de:

* explicar o conceito de primitiva gráfica;
* distinguir representação geométrica de representação raster;
* compreender o processo de rasterização;
* representar pontos, linhas e polígonos;
* compreender o princípio dos algoritmos de rasterização de linhas;
* implementar algoritmos básicos de rasterização em Python;
* compreender o problema da discretização;
* identificar aliasing e suas causas;
* aplicar técnicas simples para visualizar e comparar resultados de rasterização.

---

# 1. Introdução

No capítulo anterior, trabalhamos com objetos geométricos representados matematicamente.

Podemos, por exemplo, representar um triângulo por três pontos:

$$
P_1=(2,1)
$$

$$
P_2=(6,1)
$$

$$
P_3=(4,5)
$$

Para nós, esses três pontos e as relações entre eles são suficientes para descrever geometricamente o objeto.

Entretanto, uma tela de computador não trabalha diretamente com pontos e segmentos matemáticos infinitamente precisos.

Ela possui uma quantidade finita de pixels.

Portanto, precisamos realizar uma transformação:

```text
Representação geométrica
        ↓
Rasterização
        ↓
Pixels
        ↓
Imagem
```

A **rasterização** é o processo de determinar quais elementos da representação discreta devem ser utilizados para representar uma determinada primitiva geométrica.

---

# 2. Primitivas gráficas

Uma **primitiva gráfica** é um elemento geométrico básico utilizado para construir objetos mais complexos.

Entre as primitivas mais comuns estão:

* ponto;
* linha;
* segmento de reta;
* polígono;
* triângulo;
* círculo;
* curvas.

Podemos pensar em uma construção hierárquica:

```text
Primitivas
    ↓
Formas simples
    ↓
Objetos
    ↓
Cenas
```

Por exemplo:

```text
Pontos
  ↓
Segmentos
  ↓
Triângulos
  ↓
Malha 3D
  ↓
Personagem
  ↓
Cena
```

Essa decomposição é extremamente importante em Computação Gráfica.

---

# 3. Representação vetorial × raster

Existem duas ideias fundamentais que precisam ser diferenciadas.

## 3.1 Representação vetorial

Uma forma pode ser descrita utilizando entidades geométricas.

Por exemplo, um segmento:

$$
P_1=(2,3)
$$

e:

$$
P_2=(8,6)
$$

A representação não precisa armazenar cada pixel que posteriormente será utilizado.

Ela armazena a descrição geométrica.

---

## 3.2 Representação raster

Na representação raster, armazenamos valores associados a uma grade discreta de pixels.

```text
+---+---+---+---+
|   |   |   |   |
+---+---+---+---+
|   | X | X |   |
+---+---+---+---+
|   |   | X |   |
+---+---+---+---+
```

Uma imagem raster é essencialmente uma organização bidimensional de elementos discretos.

---

# 4. O problema da rasterização

Considere uma linha matemática:

```text
        /
       /
      /
     /
    /
```

Em uma tela, entretanto, temos apenas posições discretas.

Precisamos aproximar a linha:

```text
        █
       █
      █
     █
   ██
```

Portanto, rasterizar significa resolver uma questão fundamental:

> **Quais pixels devem ser ativados para representar uma determinada geometria?**

Esse problema parece simples, mas possui diversas implicações.

Uma escolha inadequada pode produzir:

* linhas irregulares;
* lacunas;
* espessuras inconsistentes;
* artefatos visuais;
* aliasing.

---

# 5. Sistema de coordenadas da tela

Vamos considerar uma imagem com:

```text
largura = 10
altura  = 8
```

Uma representação simplificada:

```text
(0,0) ----------------------→ x
  |
  |
  |
  |
  ↓
  y
```

Um pixel pode ser identificado por:

$$
(x,y)
$$

mas, na implementação, devemos lembrar que a imagem é normalmente armazenada como:

```python
imagem[y, x]
```

Ou seja:

```text
coordenada geométrica
(x, y)

acesso à matriz
imagem[y, x]
```

Essa distinção será recorrente.

---

# 6. Desenhando um ponto

Um ponto é a primitiva mais simples.

Considere uma imagem inicialmente preta:

```python id="8xgvuj"
import numpy as np
import matplotlib.pyplot as plt

imagem = np.zeros((100, 100))

imagem[50, 30] = 1

plt.imshow(imagem, cmap="gray")
plt.show()
```

A instrução:

```python
imagem[50, 30] = 1
```

modifica um pixel.

Novamente, temos:

```text
imagem[linha, coluna]
       ↓       ↓
       y       x
```

---

# 7. Linha como objeto geométrico

Considere dois pontos:

$$
P_0=(x_0,y_0)
$$

e:

$$
P_1=(x_1,y_1)
$$

Uma parametrização do segmento pode ser escrita como:

$$
P(t)=P_0+t(P_1-P_0)
$$

para:

$$
0\leq t\leq1
$$

Expandindo:

$$
x(t)=x_0+t(x_1-x_0)
$$

$$
y(t)=y_0+t(y_1-y_0)
$$

Essa equação descreve uma linha contínua.

O problema da rasterização é transformar essa representação contínua em pixels discretos.

---

# 8. Algoritmo ingênuo de rasterização

Uma primeira tentativa consiste em calcular vários pontos intermediários e arredondar suas coordenadas.

```python id="y42gkj"
import numpy as np

def desenhar_linha_simples(x0, y0, x1, y1, imagem):
    passos = max(abs(x1 - x0), abs(y1 - y0))

    for i in range(passos + 1):
        t = i / passos

        x = round(x0 + t * (x1 - x0))
        y = round(y0 + t * (y1 - y0))

        imagem[y, x] = 1
```

Esse algoritmo é útil para fins didáticos.

Ele mostra claramente o processo:

```text
Equação contínua
      ↓
Pontos intermediários
      ↓
Arredondamento
      ↓
Pixels
```

Entretanto, ele não é a forma mais eficiente de rasterizar uma linha.

---

# 9. Algoritmo de Bresenham

Um algoritmo clássico para rasterização de linhas é o **algoritmo de Bresenham**.

Sua importância histórica e didática está em mostrar como podemos rasterizar uma linha utilizando operações discretas e evitando cálculos de ponto flutuante desnecessários em sua formulação clássica.

A ideia é acompanhar a linha ideal e, a cada passo, decidir qual pixel é mais adequado.

Considere:

```text
        ideal
          /
         /
        /
       /
      /
```

A cada avanço horizontal, podemos decidir entre dois pixels candidatos:

```text
        •
        |
    •   |   ← candidatos
    |   |
    •---+
```

O algoritmo mantém uma variável de decisão que determina quando é necessário avançar também na direção vertical.

---

# 10. Implementação didática de Bresenham

Uma implementação geral pode ser feita da seguinte forma:

```python id="mmx3o0"
def linha_bresenham(x0, y0, x1, y1, imagem):

    dx = abs(x1 - x0)
    dy = abs(y1 - y0)

    sx = 1 if x0 < x1 else -1
    sy = 1 if y0 < y1 else -1

    erro = dx - dy

    while True:

        imagem[y0, x0] = 1

        if x0 == x1 and y0 == y1:
            break

        e2 = 2 * erro

        if e2 > -dy:
            erro -= dy
            x0 += sx

        if e2 < dx:
            erro += dx
            y0 += sy
```

Vamos testar.

```python id="k9j8p0"
import numpy as np
import matplotlib.pyplot as plt

imagem = np.zeros((100, 100))

linha_bresenham(
    10, 20,
    80, 70,
    imagem
)

plt.imshow(imagem, cmap="gray")
plt.show()
```

---

# 11. Analisando o algoritmo

O código possui três ideias importantes.

## 11.1 Direção

```python
sx = 1 if x0 < x1 else -1
sy = 1 if y0 < y1 else -1
```

Determina em qual direção devemos avançar.

---

## 11.2 Erro acumulado

```python
erro = dx - dy
```

A variável `erro` representa uma medida relacionada à diferença entre o caminho discreto escolhido e a linha ideal.

---

## 11.3 Decisão

```python
if e2 > -dy:
```

e:

```python
if e2 < dx:
```

determinam quando devemos avançar nas respectivas direções.

A grande ideia é:

> O algoritmo não precisa calcular explicitamente todos os pontos reais da reta para decidir qual pixel deve ser utilizado.

---

# 12. Complexidade

Para uma linha com aproximadamente \(n\) pixels, o algoritmo percorre uma quantidade proporcional a \(n\).

Podemos caracterizar o custo como:

$$
O(n)
$$

Isso é importante porque algoritmos gráficos são frequentemente executados muitas vezes.

Imagine uma aplicação que precisa desenhar:

* milhares de segmentos;
* dezenas de milhares de triângulos;
* milhões de pixels.

Pequenas diferenças de eficiência podem se tornar relevantes.

---

# 13. Rasterização de polígonos

Um polígono pode ser representado por seus vértices.

Considere um triângulo:

```text
       •
      / \
     /   \
    /     \
   •-------•
```

Primeiro podemos rasterizar suas arestas.

Entretanto, ainda precisamos preencher a região interna.

Isso gera outro problema:

> Quais pixels pertencem ao interior do polígono?

Existem diversos métodos para resolver essa questão.

Um conceito importante é o preenchimento por **scanlines**.

---

# 14. Scanline

A ideia de scanline é percorrer a imagem linha por linha.

```text
───────────────
───────────────
───────────────
───────────────
```

Para cada linha horizontal:

1. encontramos as interseções com o polígono;
2. organizamos essas interseções;
3. determinamos os intervalos internos;
4. preenchemos os pixels correspondentes.

Para um triângulo:

```text
       /\
      /  \
-----/----\-----
    /      \
---/--------\---
```

Cada linha horizontal pode gerar um intervalo diferente.

---

# 15. Exemplo conceitual de preenchimento

Considere:

```text
       •
      / \
     /   \
    /     \
   •-------•
```

Uma scanline pode interceptar o triângulo assim:

```text
       |  |
       ↓  ↓

      •----•
```

Os pixels entre as duas interseções podem ser preenchidos.

Esse princípio é fundamental para compreender como primitivas como triângulos podem ser convertidas em regiões de pixels.

---

# 16. Triângulos na Computação Gráfica

O triângulo possui importância especial.

Muitos sistemas gráficos utilizam triângulos como unidade básica para representar superfícies.

Por exemplo, uma superfície complexa pode ser aproximada por uma malha:

```text
      /\    /\
     /__\__/__\
     \  /  \  /
      \/____\/
```

Cada triângulo é definido por três vértices.

Uma malha pode conter:

* centenas;
* milhares;
* milhões de triângulos.

A GPU pode então processar esses elementos de maneira altamente paralela.

---

# 17. Interpolação

Ao rasterizar um triângulo, frequentemente precisamos determinar valores nos pixels internos.

Por exemplo, se os vértices possuem cores diferentes:

```text
      🔴
     /  \
    /    \
   🔵----🟢
```

precisamos calcular uma cor apropriada para os pixels interiores.

Isso envolve **interpolação**.

O conceito geral é:

```text
Valores nos vértices
        ↓
Interpolação
        ↓
Valores nos pixels
```

O mesmo princípio pode ser aplicado a outras propriedades:

* cor;
* profundidade;
* coordenadas de textura;
* normais.

Esse assunto será aprofundado quando estudarmos renderização.

---

# 18. Aliasing

Como uma imagem raster possui uma grade discreta, linhas diagonais e curvas podem apresentar aparência irregular.

Considere uma linha diagonal:

```text
██
  ██
    ██
      ██
```

Em uma representação de baixa resolução, percebemos claramente os degraus.

Esse fenômeno está relacionado ao **aliasing**.

---

# 19. Anti-aliasing

Uma técnica para reduzir a aparência desses degraus é o **anti-aliasing**.

A ideia básica é utilizar informações de cobertura ou intensidade para produzir uma transição visual mais suave.

Em vez de:

```text
0 0 1 1 0
```

podemos ter algo conceitualmente semelhante a:

```text
0 0.2 0.8 1 0.3
```

Na visualização, pixels parcialmente envolvidos pela geometria podem receber valores intermediários.

Isso cria uma aparência mais suave.

---

# 20. Experimento com baixa resolução

Podemos observar o efeito da rasterização criando uma imagem pequena.

```python id="4tkxob"
import numpy as np
import matplotlib.pyplot as plt

imagem = np.zeros((20, 20))

linha_bresenham(
    2, 2,
    17, 15,
    imagem
)

plt.imshow(
    imagem,
    cmap="gray",
    interpolation="nearest"
)

plt.grid()
plt.show()
```

O parâmetro:

```python
interpolation="nearest"
```

é interessante neste experimento porque permite visualizar diretamente os pixels.

---

# 21. Rasterização versus desenho vetorial

É importante distinguir:

```text
Descrição geométrica
```

de:

```text
Resultado rasterizado
```

Podemos ter:

```text
Segmento:
P1 = (10, 20)
P2 = (80, 70)
```

e, posteriormente:

```text
Pixels:
(10,20)
(11,21)
(12,21)
...
```

A primeira é uma descrição geométrica.

A segunda é uma representação discreta resultante do processo de rasterização.

---

# 22. Rasterização como etapa de uma pipeline

A rasterização não ocorre isoladamente.

Em uma pipeline gráfica simplificada:

```text
Modelo
  ↓
Transformações
  ↓
Câmera
  ↓
Projeção
  ↓
Primitivas
  ↓
Rasterização
  ↓
Fragmentos/pixels
  ↓
Imagem
```

Nos próximos capítulos veremos etapas anteriores à rasterização.

Posteriormente, estudaremos também o que acontece depois dela.

---

# 23. Exemplo: desenhando uma figura com primitivas

Podemos construir uma figura utilizando segmentos.

```python id="m5a9d9"
import numpy as np
import matplotlib.pyplot as plt

imagem = np.zeros((100, 100))

arestas = [
    (20, 20, 80, 20),
    (80, 20, 50, 80),
    (50, 80, 20, 20)
]

for x0, y0, x1, y1 in arestas:
    linha_bresenham(
        x0, y0,
        x1, y1,
        imagem
    )

plt.imshow(
    imagem,
    cmap="gray",
    interpolation="nearest"
)

plt.show()
```

O resultado é um triângulo rasterizado.

A arquitetura conceitual é:

```text
Triângulo
   ↓
3 segmentos
   ↓
Rasterização de cada segmento
   ↓
Imagem
```

---

# 24. Exercício: desenhando um polígono

Podemos generalizar a ideia.

Considere uma lista de vértices:

```python id="0sj8kw"
vertices = [
    (20, 20),
    (80, 20),
    (90, 60),
    (50, 85),
    (15, 60)
]
```

Para fechar o polígono, precisamos conectar o último vértice ao primeiro.

```python id="uqkg0h"
for i in range(len(vertices)):
    x0, y0 = vertices[i]

    x1, y1 = vertices[
        (i + 1) % len(vertices)
    ]

    linha_bresenham(
        x0, y0,
        x1, y1,
        imagem
    )
```

O operador:

```python
%
```

permite voltar ao primeiro elemento depois do último.

---

# 25. Primitivas e Engenharia de Software

Aqui surge uma conexão importante com Engenharia de Software.

Uma biblioteca gráfica não precisa fazer com que o programador manipule pixels individualmente o tempo todo.

Podemos criar uma abstração:

```python id="bby0mt"
class Canvas:

    def ponto(self, x, y):
        pass

    def linha(self, x0, y0, x1, y1):
        pass

    def poligono(self, vertices):
        pass
```

Internamente:

```text
Canvas.linha()
       ↓
algoritmo de rasterização
       ↓
alteração dos pixels
```

O usuário da classe não precisa conhecer necessariamente os detalhes internos.

Isso ilustra um princípio importante:

> **Abstrações permitem separar a interface de utilização da implementação do algoritmo.**

---

# 26. Uma pequena classe Canvas

Podemos implementar uma versão simples:

```python id="f9j18u"
import numpy as np

class Canvas:

    def __init__(self, largura, altura):
        self.imagem = np.zeros(
            (altura, largura),
            dtype=np.uint8
        )

    def ponto(self, x, y):
        self.imagem[y, x] = 255

    def linha(self, x0, y0, x1, y1):
        linha_bresenham(
            x0, y0,
            x1, y1,
            self.imagem
        )
```

Uso:

```python id="7g4d1q"
canvas = Canvas(200, 200)

canvas.ponto(50, 50)

canvas.linha(
    20, 20,
    150, 100
)
```

A aplicação passa a trabalhar em um nível de abstração mais alto.

---

# 27. Por que isso importa?

Compare:

### Baixo nível

```python
imagem[50, 30] = 255
imagem[51, 31] = 255
imagem[52, 32] = 255
```

com:

### Alto nível

```python
canvas.linha(30, 50, 100, 120)
```

A segunda abordagem é muito mais expressiva.

A implementação pode até ser substituída posteriormente por:

* outra biblioteca;
* GPU;
* API gráfica;
* algoritmo mais eficiente;

sem necessariamente modificar o código que utiliza a abstração.

Essa é uma conexão direta entre Computação Gráfica e projeto de software.

---

# 28. Exercício prático integrado

Desenvolva uma pequena biblioteca gráfica em Python contendo:

```python
class Canvas:
    ...
```

Ela deverá oferecer:

```text
ponto()
linha()
poligono()
```

A aplicação deve permitir construir uma figura utilizando essas primitivas.

### Requisitos

1. criar um canvas;
2. desenhar pelo menos cinco pontos;
3. desenhar segmentos;
4. construir um polígono;
5. exibir a imagem;
6. salvar a imagem em arquivo.

### Extensão

Implemente:

```python
retangulo()
circulo()
```

sem utilizar diretamente uma função pronta de desenho.

O objetivo é fazer o estudante compreender que formas aparentemente complexas podem ser construídas a partir de algoritmos e primitivas básicas.

---

# 29. Atividade de investigação

Compare duas formas de desenhar uma linha:

### Método A

Interpolação e arredondamento.

### Método B

Bresenham.

Teste diferentes inclinações:

```text
quase horizontal
diagonal
quase vertical
```

Observe:

* continuidade;
* regularidade;
* quantidade de pixels;
* tempo de execução.

Depois responda:

> Em quais situações as diferenças entre os métodos são mais perceptíveis?

---

# 30. Questões de revisão

1. O que é uma primitiva gráfica?

2. Qual é a diferença entre uma representação vetorial e uma representação raster?

3. O que significa rasterizar uma linha?

4. Por que uma linha matemática precisa ser aproximada quando representada em uma tela?

5. Qual é a ideia fundamental do algoritmo de Bresenham?

6. Por que algoritmos de rasterização precisam considerar coordenadas discretas?

7. O que é aliasing?

8. Qual é a finalidade do anti-aliasing?

9. Por que triângulos são importantes na Computação Gráfica?

10. O que é interpolação?

11. Como a rasterização se relaciona com a pipeline gráfica?

12. Qual é a vantagem de criar uma abstração como `Canvas` em vez de manipular diretamente a matriz de pixels?

---

# 31. Exercícios

## Nível básico

### Exercício 1 — Pixel

Crie uma imagem de:

$$
20\times20
$$

pixels.

Coloque um único pixel no centro.

Exiba a imagem sem interpolação.

---

### Exercício 2 — Retas

Utilize o algoritmo de Bresenham para desenhar:

* uma linha horizontal;
* uma linha vertical;
* uma diagonal;
* uma linha inclinada.

Compare os resultados.

---

### Exercício 3 — Polígono

Crie um pentágono utilizando apenas segmentos de reta.

---

## Nível intermediário

### Exercício 4 — Canvas

Implemente:

```python
class Canvas:
    def ponto(...)
    def linha(...)
    def poligono(...)
```

Teste sua implementação.

---

### Exercício 5 — Retângulo

Crie:

```python
canvas.retangulo(x, y, largura, altura)
```

O retângulo deve ser construído a partir de quatro segmentos.

---

### Exercício 6 — Comparação de algoritmos

Implemente:

```python
linha_simples()
linha_bresenham()
```

Gere imagens utilizando as duas abordagens.

Compare os resultados visualmente.

---

## Nível avançado

### Exercício 7 — Preenchimento

Implemente uma estratégia simples de preenchimento de um polígono utilizando scanlines.

O algoritmo deve:

1. percorrer cada linha horizontal;
2. encontrar as interseções;
3. determinar os intervalos;
4. preencher os pixels internos.

---

### Exercício 8 — Anti-aliasing experimental

Modifique seu algoritmo de desenho para utilizar valores intermediários de intensidade em pixels próximos da linha.

Compare:

```text
sem suavização
```

e:

```text
com suavização
```

Discuta a relação entre qualidade visual e custo computacional.

---

# 32. Projeto prático — Mini biblioteca gráfica

Como atividade de fechamento deste capítulo, desenvolva uma pequena biblioteca chamada, por exemplo:

```text
mini_graphics
```

Sugestão de estrutura:

```text
mini_graphics/
│
├── canvas.py
├── primitives.py
├── rasterization.py
└── demo.py
```

### `rasterization.py`

Responsável por algoritmos como:

```text
bresenham()
scanline()
```

### `primitives.py`

Responsável por:

```text
ponto
linha
polígono
retângulo
```

### `canvas.py`

Responsável pela imagem e operações de alto nível.

### `demo.py`

Responsável por demonstrar o funcionamento.

Essa organização permite ao estudante perceber que **um algoritmo gráfico não precisa estar diretamente misturado à interface ou ao código da aplicação**.

---

# Síntese do capítulo

A Computação Gráfica trabalha frequentemente com objetos geométricos que precisam ser convertidos para uma representação discreta.

Esse processo pode ser resumido como:

$$
\text{Geometria}
\rightarrow
\text{Rasterização}
\rightarrow
\text{Pixels}
$$

Estudamos:

* primitivas gráficas;
* representação vetorial;
* representação raster;
* rasterização;
* rasterização de linhas;
* algoritmo de Bresenham;
* rasterização de polígonos;
* scanlines;
* interpolação;
* aliasing;
* anti-aliasing.

Também observamos uma conexão importante com Engenharia de Software: a implementação dos algoritmos pode ser encapsulada em abstrações como `Canvas`, permitindo separar:

```text
Interface de programação
        ↓
Lógica gráfica
        ↓
Algoritmos
        ↓
Representação de pixels
```

Esse princípio será retomado ao estudarmos bibliotecas, APIs e frameworks gráficos.