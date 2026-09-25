# Parte 3 — Capítulo 3: Sistemas de Coordenadas, Vetores, Matrizes e Transformações Geométricas

## Objetivos de aprendizagem

Ao final deste capítulo, o estudante deverá ser capaz de:

* compreender o papel dos sistemas de coordenadas na Computação Gráfica;
* representar pontos e vetores em duas e três dimensões;
* distinguir conceitualmente pontos de vetores;
* realizar operações básicas com vetores;
* compreender a utilização de matrizes em transformações geométricas;
* aplicar transformações de translação, escala e rotação;
* compreender o problema da translação e a necessidade das coordenadas homogêneas;
* combinar transformações geométricas;
* implementar e visualizar transformações utilizando Python.

---

## Introdução

No capítulo anterior, estudamos imagens como estruturas formadas por pixels. Entretanto, muitos problemas de Computação Gráfica não começam diretamente com pixels.

Considere um jogo. Antes que um personagem apareça na tela, o sistema precisa saber, entre outras informações:

* onde o personagem está;
* qual é seu tamanho;
* em que direção está orientado;
* como ele deve se movimentar;
* qual é sua posição em relação aos demais objetos;
* de onde a cena está sendo observada.

Da mesma forma, em um sistema de desenho, precisamos representar:

* pontos;
* linhas;
* polígonos;
* objetos;
* posições;
* transformações.

Essas representações são normalmente descritas utilizando conceitos matemáticos como:

* coordenadas;
* vetores;
* matrizes;
* transformações.

Um fluxo simplificado pode ser representado assim:

```text
Objeto geométrico
       ↓
Coordenadas
       ↓
Transformações
       ↓
Nova representação geométrica
       ↓
Rasterização
       ↓
Pixels
       ↓
Imagem exibida
```

Este capítulo estabelece a base matemática necessária para compreender esse processo.

> **📌 Ideia central**
>
> Na Computação Gráfica, objetos são frequentemente descritos matematicamente antes de serem convertidos em imagens.

---

# 1. Sistemas de coordenadas

## 1.1 Localização de um ponto

Em um espaço bidimensional, podemos localizar um ponto utilizando duas coordenadas:

$$
P = (x,y)
$$

Por exemplo:

$$
P=(3,2)
$$

Podemos representar esse ponto em um plano:

```text
y
↑
|
|        • P(3,2)
|
|
+----------------------→ x
```

O valor (x) indica a posição em uma direção horizontal e o valor (y) indica a posição na outra direção.

---

## 1.2 Coordenadas em Computação Gráfica

Uma convenção matemática comum utiliza:

```text
          y
          ↑
          |
          |
----------+----------→ x
          |
          |
```

Nesse sistema:

* a origem é ((0,0));
* valores positivos de (x) deslocam-se para a direita;
* valores positivos de (y) deslocam-se para cima.

Entretanto, em imagens digitais, é comum encontrarmos outra convenção:

```text
(0,0) --------------------→ x
 |
 |
 |
 ↓
 y
```

Nesse caso:

* a origem está no canto superior esquerdo;
* (x) cresce para a direita;
* (y) cresce para baixo.

Essa diferença é importante.

> **⚠️ Atenção**
>
> Não existe uma única convenção universal para todos os sistemas gráficos. É necessário saber qual sistema de coordenadas está sendo utilizado.

---

# 2. Pontos e vetores

Esses conceitos são relacionados, mas não são iguais.

## 2.1 Ponto

Um ponto representa uma **posição**.

Por exemplo:

$$
P=(2,3)
$$

indica uma localização no plano.

---

## 2.2 Vetor

Um vetor representa, de maneira simplificada:

* direção;
* sentido;
* magnitude.

Por exemplo:

$$
\vec{v}=
\begin{bmatrix}
3\
2
\end{bmatrix}
$$

pode representar um deslocamento de:

* 3 unidades na direção (x);
* 2 unidades na direção (y).

Visualmente:

```text
Ponto inicial
     •
      \
       \
        → deslocamento
```

Um vetor não precisa ser interpretado como uma posição absoluta.

Considere:

$$
P_1=(1,1)
$$

e:

$$
P_2=(4,3)
$$

O vetor que representa o deslocamento de (P_1) até (P_2) é:

$$
\vec{v}=P_2-P_1
$$

Portanto:

$$
\vec{v}=
\begin{bmatrix}
4-1\
3-1
\end{bmatrix}
=============

\begin{bmatrix}
3\
2
\end{bmatrix}
$$

---

# 3. Operações básicas com vetores

## 3.1 Adição

Considere:

$$
\vec{u}=
\begin{bmatrix}
u_x\
u_y
\end{bmatrix}
$$

e:

$$
\vec{v}=
\begin{bmatrix}
v_x\
v_y
\end{bmatrix}
$$

A soma é:

$$
\vec{u}+\vec{v}
===============

\begin{bmatrix}
u_x+v_x\
u_y+v_y
\end{bmatrix}
$$

Exemplo:

$$
\begin{bmatrix}
2\
1
\end{bmatrix}
+
\begin{bmatrix}
3\
4
\end{bmatrix}
=============

\begin{bmatrix}
5\
5
\end{bmatrix}
$$

Em Python:

```python
import numpy as np

u = np.array([2, 1])
v = np.array([3, 4])

resultado = u + v

print(resultado)
```

Resultado:

```text
[5 5]
```

---

## 3.2 Multiplicação por escalar

Podemos multiplicar um vetor por um número.

Se:

$$
\vec{v}=
\begin{bmatrix}
2\
3
\end{bmatrix}
$$

e:

$$
k=2
$$

então:

$$
k\vec{v}
========

2
\begin{bmatrix}
2\
3
\end{bmatrix}
=============

\begin{bmatrix}
4\
6
\end{bmatrix}
$$

Em Python:

```python
v = np.array([2, 3])

resultado = 2 * v

print(resultado)
```

A direção permanece a mesma, mas a magnitude é multiplicada.

---

# 4. Magnitude de um vetor

A magnitude, ou comprimento, de um vetor bidimensional:

$$
\vec{v}=
\begin{bmatrix}
x\
y
\end{bmatrix}
$$

é dada por:

$$
||\vec{v}||=\sqrt{x^2+y^2}
$$

Considere:

$$
\vec{v}=
\begin{bmatrix}
3\
4
\end{bmatrix}
$$

Então:

$$
||\vec{v}||
===========

# \sqrt{3^2+4^2}

# \sqrt{25}

5
$$

Em Python:

```python
import numpy as np

v = np.array([3, 4])

magnitude = np.linalg.norm(v)

print(magnitude)
```

Resultado:

```text
5.0
```

---

# 5. Vetor unitário

Um vetor unitário possui magnitude igual a 1.

Podemos normalizar um vetor:

$$
\hat{v}
=======

\frac{\vec{v}}
{||\vec{v}||}
$$

Por exemplo:

$$
\vec{v}=
\begin{bmatrix}
3\
4
\end{bmatrix}
$$

Como:

$$
||\vec{v}||=5
$$

temos:

$$
\hat{v}
=======

\begin{bmatrix}
3/5\
4/5
\end{bmatrix}
$$

Em Python:

```python
v = np.array([3, 4])

v_unitario = v / np.linalg.norm(v)

print(v_unitario)
```

A normalização será importante posteriormente em conceitos como:

* direção;
* iluminação;
* orientação;
* movimento;
* vetores normais.

---

# 6. Produto escalar

O produto escalar entre dois vetores pode ser definido como:

$$
\vec{u}\cdot\vec{v}
===================

u_xv_x+u_yv_y
$$

Por exemplo:

$$
\vec{u}=
\begin{bmatrix}
2\
3
\end{bmatrix}
$$

e:

$$
\vec{v}=
\begin{bmatrix}
4\
1
\end{bmatrix}
$$

Então:

$$
\vec{u}\cdot\vec{v}
===================

# 2(4)+3(1)

11
$$

Em Python:

```python
u = np.array([2, 3])
v = np.array([4, 1])

resultado = np.dot(u, v)

print(resultado)
```

O produto escalar possui uma interpretação geométrica importante:

$$
\vec{u}\cdot\vec{v}
===================

||\vec{u}||
||\vec{v}||
\cos(\theta)
$$

em que (\theta) representa o ângulo entre os vetores.

Consequentemente:

* produto escalar positivo: vetores possuem direções relativamente semelhantes;
* produto escalar igual a zero: vetores perpendiculares;
* produto escalar negativo: vetores apontam predominantemente em direções opostas.

Esse conceito será reutilizado quando estudarmos iluminação.

---

# 7. Matrizes

Uma matriz é uma estrutura organizada em linhas e colunas.

Por exemplo:

$$
A=
\begin{bmatrix}
1 & 2\
3 & 4
\end{bmatrix}
$$

Na Computação Gráfica, matrizes são particularmente importantes porque permitem representar transformações geométricas.

---

# 8. Transformações geométricas

Imagine um quadrado:

```text
•---------•
|         |
|         |
|         |
•---------•
```

Podemos querer:

* movê-lo;
* aumentá-lo;
* diminuí-lo;
* girá-lo;
* combinar diferentes operações.

Essas alterações são chamadas de **transformações geométricas**.

As principais transformações introdutórias são:

1. translação;
2. escala;
3. rotação.

---

# 9. Translação

Uma translação desloca um objeto.

Considere:

$$
P=(x,y)
$$

e um deslocamento:

$$
(t_x,t_y)
$$

O novo ponto será:

$$
P'=
(x+t_x,y+t_y)
$$

Por exemplo:

$$
P=(2,3)
$$

e:

$$
T=(4,1)
$$

Então:

$$
P'=(6,4)
$$

Em Python:

```python
import numpy as np

ponto = np.array([2, 3])

translacao = np.array([4, 1])

novo_ponto = ponto + translacao

print(novo_ponto)
```

---

# 10. Escala

Uma transformação de escala modifica o tamanho de um objeto.

Para um ponto:

$$
P=
\begin{bmatrix}
x\
y
\end{bmatrix}
$$

podemos utilizar:

$$
P'
==

\begin{bmatrix}
s_x & 0\
0 & s_y
\end{bmatrix}
P
$$

Portanto:

$$
x'=s_xx
$$

$$
y'=s_yy
$$

A matriz de escala é:

$$
S=
\begin{bmatrix}
s_x & 0\
0 & s_y
\end{bmatrix}
$$

Exemplo:

$$
S=
\begin{bmatrix}
2 & 0\
0 & 3
\end{bmatrix}
$$

Aplicando ao ponto:

$$
P=
\begin{bmatrix}
1\
2
\end{bmatrix}
$$

temos:

$$
P'
==

\begin{bmatrix}
2 & 0\
0 & 3
\end{bmatrix}
\begin{bmatrix}
1\
2
\end{bmatrix}
=============

\begin{bmatrix}
2\
6
\end{bmatrix}
$$

Em Python:

```python
ponto = np.array([1, 2])

escala = np.array([
    [2, 0],
    [0, 3]
])

novo_ponto = escala @ ponto

print(novo_ponto)
```

---

# 11. Rotação em duas dimensões

A rotação de um ponto em torno da origem pode ser representada pela matriz:

$$
R(\theta)
=========

\begin{bmatrix}
\cos(\theta) & -\sin(\theta)\
\sin(\theta) & \cos(\theta)
\end{bmatrix}
$$

Assim:

$$
P'=R(\theta)P
$$

Considere uma rotação de:

$$
90^\circ
$$

Sabemos que:

$$
\cos(90^\circ)=0
$$

e:

$$
\sin(90^\circ)=1
$$

Portanto:

$$
R(90^\circ)
===========

\begin{bmatrix}
0 & -1\
1 & 0
\end{bmatrix}
$$

Se:

$$
P=
\begin{bmatrix}
1\
0
\end{bmatrix}
$$

então:

$$
P'
==

\begin{bmatrix}
0 & -1\
1 & 0
\end{bmatrix}
\begin{bmatrix}
1\
0
\end{bmatrix}
=============

\begin{bmatrix}
0\
1
\end{bmatrix}
$$

---

## 11.1 Implementação em Python

```python
import numpy as np

angulo = np.radians(45)

rotacao = np.array([
    [np.cos(angulo), -np.sin(angulo)],
    [np.sin(angulo),  np.cos(angulo)]
])

ponto = np.array([1, 0])

novo_ponto = rotacao @ ponto

print(novo_ponto)
```

> **⚠️ Atenção**
>
> Funções trigonométricas utilizadas em bibliotecas numéricas normalmente trabalham com ângulos em radianos.

A conversão é:

$$
\theta_{rad}
============

\theta_{graus}
\frac{\pi}{180}
$$

---

# 12. Visualizando transformações com Python

Vamos representar um triângulo.

```python
import numpy as np
import matplotlib.pyplot as plt

triangulo = np.array([
    [0, 0],
    [2, 0],
    [1, 2],
    [0, 0]
])

plt.plot(triangulo[:, 0], triangulo[:, 1])

plt.axis("equal")
plt.grid()

plt.show()
```

A estrutura:

```python
triangulo[:, 0]
```

seleciona as coordenadas (x).

Já:

```python
triangulo[:, 1]
```

seleciona as coordenadas (y).

---

## 12.1 Aplicando escala

Podemos aplicar:

```python
escala = np.array([
    [2, 0],
    [0, 1.5]
])

triangulo_escalado = triangulo @ escala.T
```

O resultado é um novo conjunto de coordenadas.

```python
plt.plot(
    triangulo[:, 0],
    triangulo[:, 1],
    label="Original"
)

plt.plot(
    triangulo_escalado[:, 0],
    triangulo_escalado[:, 1],
    label="Escalado"
)

plt.axis("equal")
plt.grid()
plt.legend()

plt.show()
```

---

# 13. O problema da translação com matrizes 2 × 2

A escala pode ser representada por:

$$
P'=SP
$$

A rotação também:

$$
P'=RP
$$

Mas a translação possui uma estrutura diferente:

$$
P'=P+T
$$

Existe uma adição.

Isso dificulta a representação de todas as transformações por uma única operação matricial simples em coordenadas bidimensionais.

A solução clássica utilizada em Computação Gráfica é introduzir as **coordenadas homogêneas**.

---

# 14. Coordenadas homogêneas

Um ponto bidimensional:

$$
(x,y)
$$

pode ser representado como:

$$
\begin{bmatrix}
x\
y\
1
\end{bmatrix}
$$

Essa terceira componente permite representar a translação por meio de uma matriz.

A matriz de translação é:

$$
T=
\begin{bmatrix}
1 & 0 & t_x\
0 & 1 & t_y\
0 & 0 & 1
\end{bmatrix}
$$

Aplicando:

$$
T
\begin{bmatrix}
x\
y\
1
\end{bmatrix}
=============

\begin{bmatrix}
x+t_x\
y+t_y\
1
\end{bmatrix}
$$

Agora, escala, rotação e translação podem ser representadas utilizando matrizes (3 \times 3).

---

# 15. Transformações homogêneas em 2D

## 15.1 Escala

$$
S=
\begin{bmatrix}
s_x & 0 & 0\
0 & s_y & 0\
0 & 0 & 1
\end{bmatrix}
$$

---

## 15.2 Rotação

$$
R=
\begin{bmatrix}
\cos(\theta) & -\sin(\theta) & 0\
\sin(\theta) & \cos(\theta) & 0\
0 & 0 & 1
\end{bmatrix}
$$

---

## 15.3 Translação

$$
T=
\begin{bmatrix}
1 & 0 & t_x\
0 & 1 & t_y\
0 & 0 & 1
\end{bmatrix}
$$

Isso permite estabelecer um modelo uniforme:

```text
Ponto homogêneo
        ↓
Matriz de transformação
        ↓
Novo ponto homogêneo
```

---

# 16. Ponto e vetor em coordenadas homogêneas

Em coordenadas homogêneas, podemos distinguir:

### Ponto

$$
P=
\begin{bmatrix}
x\
y\
1
\end{bmatrix}
$$

### Vetor

$$
V=
\begin{bmatrix}
x\
y\
0
\end{bmatrix}
$$

Essa diferença possui uma consequência importante.

Considere uma matriz de translação:

$$
T=
\begin{bmatrix}
1 & 0 & t_x\
0 & 1 & t_y\
0 & 0 & 1
\end{bmatrix}
$$

Aplicando-a a um vetor:

$$
T
\begin{bmatrix}
x\
y\
0
\end{bmatrix}
=============

\begin{bmatrix}
x\
y\
0
\end{bmatrix}
$$

Ou seja, uma translação altera posições, mas não deve alterar diretamente um vetor que representa direção ou deslocamento.

Esse é um dos motivos conceituais pelos quais a distinção entre pontos e vetores é importante.

---

# 17. Composição de transformações

Em aplicações reais, raramente aplicamos apenas uma transformação.

Um objeto pode ser:

1. escalado;
2. rotacionado;
3. transladado.

Podemos escrever:

$$
P'=TRSP
$$

A ordem das operações é importante.

Em geral:

$$
TR \neq RT
$$

Portanto:

> **⚠️ A ordem das transformações pode alterar o resultado.**

---

## 17.1 Exemplo intuitivo

Imagine uma seta localizada em determinado ponto.

### Caso A

Primeiro rotacionamos e depois transladamos.

### Caso B

Primeiro transladamos e depois rotacionamos.

O resultado pode ser completamente diferente porque a rotação ocorre em relação a um sistema de referência.

---

# 18. Exemplo completo: transformação de um triângulo

Vamos criar matrizes homogêneas para aplicar transformações.

```python
import numpy as np
import matplotlib.pyplot as plt

triangulo = np.array([
    [0, 0, 1],
    [2, 0, 1],
    [1, 2, 1],
    [0, 0, 1]
]).T
```

Cada coluna representa um ponto.

Agora criamos uma escala:

```python
S = np.array([
    [1.5, 0,   0],
    [0,   1.5, 0],
    [0,   0,   1]
])
```

Uma rotação:

```python
angulo = np.radians(30)

R = np.array([
    [np.cos(angulo), -np.sin(angulo), 0],
    [np.sin(angulo),  np.cos(angulo), 0],
    [0,               0,              1]
])
```

E uma translação:

```python
T = np.array([
    [1, 0, 3],
    [0, 1, 2],
    [0, 0, 1]
])
```

Combinamos:

```python
M = T @ R @ S
```

Aplicamos:

```python
resultado = M @ triangulo
```

Para visualizar:

```python
plt.plot(
    triangulo[0],
    triangulo[1],
    label="Original"
)

plt.plot(
    resultado[0],
    resultado[1],
    label="Transformado"
)

plt.axis("equal")
plt.grid()
plt.legend()

plt.show()
```

O fluxo é:

```text
Triângulo
   ↓
Escala
   ↓
Rotação
   ↓
Translação
   ↓
Triângulo transformado
```

---

# 19. Transformações em três dimensões

Até agora trabalhamos principalmente com:

$$
(x,y)
$$

Em Computação Gráfica 3D, utilizamos:

$$
(x,y,z)
$$

Um ponto pode ser representado como:

$$
P=
\begin{bmatrix}
x\
y\
z
\end{bmatrix}
$$

Com coordenadas homogêneas:

$$
P=
\begin{bmatrix}
x\
y\
z\
1
\end{bmatrix}
$$

Consequentemente, as transformações 3D são frequentemente representadas por matrizes:

$$
4\times4
$$

Uma transformação genérica pode possuir a estrutura:

$$
M=
\begin{bmatrix}
m_{11} & m_{12} & m_{13} & t_x\
m_{21} & m_{22} & m_{23} & t_y\
m_{31} & m_{32} & m_{33} & t_z\
0 & 0 & 0 & 1
\end{bmatrix}
$$

Essa estrutura será fundamental quando estudarmos:

* modelagem tridimensional;
* transformações de objetos;
* sistemas de câmera;
* projeções;
* renderização.

---

# 20. Sistemas de referência

Um objeto não precisa existir diretamente no mesmo sistema de coordenadas utilizado pela tela.

Considere um personagem em um jogo.

Podemos definir:

```text
Coordenadas locais
       ↓
Coordenadas do objeto
       ↓
Coordenadas do mundo
       ↓
Coordenadas da câmera
       ↓
Coordenadas da projeção
       ↓
Tela
```

Por exemplo, uma roda pode ser definida em relação ao centro de um carro.

O carro, por sua vez, pode ser definido em relação ao mundo.

Essa organização permite estruturar objetos complexos.

---

## 20.1 Transformações hierárquicas

Considere:

```text
Braço
  ↓
Mão
  ↓
Dedo
```

Se o braço se movimenta, a mão acompanha.

Se a mão se movimenta, os dedos acompanham.

Podemos representar:

```text
Transformação do braço
           ↓
Transformação da mão
           ↓
Transformação do dedo
```

Esse conceito será importante para:

* animação;
* modelagem;
* robótica;
* jogos;
* interfaces.

---

# 21. Um experimento interativo simples

Podemos criar uma função para aplicar uma transformação geométrica a um conjunto de pontos.

```python
import numpy as np

def transformar(pontos, matriz):
    return pontos @ matriz.T
```

Agora podemos definir:

```python
pontos = np.array([
    [0, 0],
    [1, 0],
    [1, 1],
    [0, 1]
])
```

Uma escala:

```python
escala = np.array([
    [2, 0],
    [0, 2]
])
```

Aplicação:

```python
resultado = transformar(pontos, escala)

print(resultado)
```

Esse pequeno programa mostra uma ideia importante para Engenharia de Software:

> É possível separar a representação geométrica da lógica responsável pela transformação.

Por exemplo:

```text
Dados geométricos
       +
Matriz de transformação
       ↓
Função de transformação
       ↓
Novo conjunto de dados
```

Essa separação facilita testes e reutilização.

---

# 22. Aplicações práticas

## Jogos

Transformações são utilizadas para:

* movimentar personagens;
* girar objetos;
* escalar elementos;
* controlar câmeras.

## Interfaces

Transformações podem permitir:

* animações;
* zoom;
* rotação;
* transições visuais.

## CAD e modelagem

Objetos podem ser:

* posicionados;
* rotacionados;
* redimensionados.

## Visualização científica

Transformações permitem modificar a forma como conjuntos de dados são apresentados.

## Realidade virtual e aumentada

Objetos precisam ser continuamente transformados entre diferentes sistemas de referência.

---

# 23. Erros conceituais comuns

## Erro 1 — Confundir ponto com vetor

Um ponto representa uma posição.

Um vetor normalmente representa direção, deslocamento ou diferença entre posições.

---

## Erro 2 — Ignorar a origem da transformação

Uma escala ou rotação aplicada em relação à origem pode produzir um resultado diferente daquele desejado.

---

## Erro 3 — Acreditar que a ordem das transformações não importa

Em geral:

$$
AB\neq BA
$$

Portanto, mudar a ordem pode modificar completamente o resultado.

---

## Erro 4 — Confundir graus com radianos

Funções trigonométricas em bibliotecas como NumPy normalmente esperam valores em radianos.

---

## Erro 5 — Misturar convenções de vetor linha e vetor coluna

Existem diferentes convenções para multiplicação de matrizes.

Por exemplo:

$$
P'=MP
$$

e:

$$
P'=PM
$$

não são a mesma convenção.

É fundamental manter consistência no projeto e no código.

---

## Erro 6 — Esquecer a diferença entre coordenadas matemáticas e coordenadas de imagens

Em um plano cartesiano tradicional, o eixo (y) normalmente cresce para cima.

Em muitos sistemas de imagem, o eixo (y) cresce para baixo.

---

# Síntese do capítulo

Neste capítulo, estudamos os fundamentos matemáticos necessários para representar e transformar objetos geométricos.

Os principais conceitos foram:

### Ponto

Representa uma posição:

$$
P=(x,y)
$$

ou:

$$
P=(x,y,z)
$$

### Vetor

Representa direção, deslocamento ou magnitude:

$$
\vec{v}=
\begin{bmatrix}
x\
y
\end{bmatrix}
$$

### Matriz

Permite representar operações e transformações.

### Transformações fundamentais

* translação;
* escala;
* rotação.

### Coordenadas homogêneas

Permitem representar diferentes transformações utilizando uma estrutura matricial uniforme.

O fluxo conceitual central é:

```text
Objeto
  ↓
Representação por coordenadas
  ↓
Aplicação de transformações
  ↓
Nova representação geométrica
```

Esse conhecimento será utilizado diretamente no próximo capítulo, quando estudaremos primitivas gráficas e a conversão de representações geométricas em pixels.

---

# Questões de revisão

1. Qual é a diferença entre um ponto e um vetor?

2. O que representa a magnitude de um vetor?

3. Como um vetor pode ser normalizado?

4. O que significa afirmar que dois vetores possuem produto escalar igual a zero?

5. Quais são as três transformações geométricas básicas estudadas neste capítulo?

6. Por que a translação não pode ser representada da mesma forma simples que escala e rotação utilizando apenas uma matriz (2 \times 2)?

7. Qual é o papel das coordenadas homogêneas?

8. Por que a ordem das transformações geométricas é importante?

9. Qual é a diferença entre coordenadas utilizadas em um plano cartesiano tradicional e aquelas frequentemente utilizadas em imagens digitais?

10. Por que sistemas gráficos utilizam diferentes sistemas de referência?

---

# Exercícios

## Nível básico

### Exercício 1 — Vetores

Considere:

$$
\vec{u}=
\begin{bmatrix}
2\
3
\end{bmatrix}
$$

e:

$$
\vec{v}=
\begin{bmatrix}
4\
-1
\end{bmatrix}
$$

Calcule:

a.

$$
\vec{u}+\vec{v}
$$

b.

$$
2\vec{u}
$$

c.

$$
\vec{u}\cdot\vec{v}
$$

---

### Exercício 2 — Magnitude

Calcule a magnitude dos vetores:

a.

$$
\begin{bmatrix}
3\
4
\end{bmatrix}
$$

b.

$$
\begin{bmatrix}
5\
12
\end{bmatrix}
$$

---

### Exercício 3 — Translação

Considere:

$$
P=(2,5)
$$

Aplique uma translação:

$$
T=(3,-2)
$$

Qual é a nova posição?

---

## Nível intermediário

### Exercício 4 — Escala

Implemente uma função:

```python
def aplicar_escala(ponto, sx, sy):
    ...
```

A função deve receber um ponto e aplicar fatores de escala independentes para (x) e (y).

---

### Exercício 5 — Rotação

Implemente uma função:

```python
def rotacionar(ponto, angulo):
    ...
```

A função deve receber um ponto bidimensional e um ângulo em graus.

Teste com:

* 0°;
* 45°;
* 90°;
* 180°.

---

### Exercício 6 — Triângulo

Represente um triângulo utilizando um conjunto de coordenadas.

Utilize NumPy e Matplotlib para:

1. visualizar o triângulo original;
2. aplicar uma escala;
3. aplicar uma rotação;
4. aplicar uma translação;
5. visualizar o resultado.

---

## Nível avançado

### Exercício 7 — Transformações compostas

Utilizando coordenadas homogêneas, aplique a seguinte sequência a um objeto:

1. escala de 2 em ambos os eixos;
2. rotação de 45°;
3. translação de ((3,4)).

Depois, altere a ordem:

1. translação;
2. rotação;
3. escala.

Compare os resultados e explique por que são diferentes.

---

### Exercício 8 — Arquitetura de software

Projete uma pequena estrutura de software para uma aplicação capaz de manipular objetos geométricos.

A aplicação deve separar, pelo menos:

```text
Representação dos objetos
        ↓
Operações matemáticas
        ↓
Transformações
        ↓
Visualização
```

Discuta quais funções ou classes poderiam pertencer a cada componente.

---

# Exercício prático — Transformador geométrico

Desenvolva uma pequena aplicação em Python que permita:

1. definir um conjunto de pontos;
2. visualizar a forma geométrica correspondente;
3. aplicar escala;
4. aplicar rotação;
5. aplicar translação;
6. visualizar a forma antes e depois das transformações.

Como extensão, implemente uma estrutura semelhante a:

```python
class Objeto2D:
    def __init__(self, pontos):
        self.pontos = pontos

    def escalar(self, sx, sy):
        pass

    def rotacionar(self, angulo):
        pass

    def transladar(self, tx, ty):
        pass
```

Uma possível evolução posterior seria separar:

```text
Objeto2D
    ├── dados geométricos
    │
    ├── Transformacoes2D
    │       ├── escala
    │       ├── rotação
    │       └── translação
    │
    └── Visualizador
            └── apresentação gráfica
```

Essa organização permite que a lógica matemática seja testada independentemente da interface gráfica.

---

## Controle de continuidade

* **Parte concluída:** Parte 3 — Sistemas de Coordenadas, Vetores, Matrizes e Transformações Geométricas
* **Capítulo concluído:** Capítulo 3
* **Próxima parte:** Parte 4 — Primitivas Gráficas e Algoritmos de Rasterização
* **Próximo capítulo:** Capítulo 4 — Do Modelo Geométrico aos Pixels
* **Próximos conceitos:** primitivas gráficas, representação de linhas e polígonos, rasterização, algoritmos de desenho de linhas, preenchimento e problemas de discretização.
