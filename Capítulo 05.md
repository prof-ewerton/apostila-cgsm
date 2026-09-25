# Parte 5 — Modelagem e Representação de Objetos 2D e 3D

## Objetivos de aprendizagem

Ao final deste capítulo, o estudante deverá ser capaz de:

* compreender o conceito de modelagem geométrica;
* diferenciar modelos 2D e 3D;
* representar objetos por vértices, arestas e faces;
* compreender o conceito de malha poligonal;
* construir objetos 2D e 3D simples com Python;
* compreender a relação entre topologia e geometria;
* aplicar transformações a objetos modelados;
* compreender estruturas hierárquicas de objetos;
* reconhecer limitações de diferentes representações geométricas.

---

# 1. Introdução

Nos capítulos anteriores, estudamos:

* como imagens podem ser representadas por pixels;
* como pontos e vetores podem representar posições e deslocamentos;
* como matrizes podem representar transformações;
* como primitivas geométricas podem ser rasterizadas.

Agora vamos dar um passo anterior à rasterização:

> **Como representar um objeto gráfico antes de transformá-lo em uma imagem?**

Considere um carro em um jogo.

Uma fotografia do carro poderia ser armazenada como pixels:

```text id="m8x6fw"
Imagem
 ↓
Pixels
 ↓
Valores de cor
```

Mas, para construir um carro virtual, pode ser mais interessante armazená-lo como geometria:

```text id="9x7s2f"
Carro
 ↓
Partes
 ↓
Objetos geométricos
 ↓
Vértices
 ↓
Arestas
 ↓
Faces
```

Esse processo de definir matematicamente a estrutura de um objeto é chamado de **modelagem geométrica**.

---

# 2. O que é um modelo geométrico?

Um modelo geométrico é uma representação matemática da forma de um objeto.

Ele pode conter informações como:

* posição;
* dimensões;
* orientação;
* vértices;
* curvas;
* superfícies;
* relações entre componentes.

Por exemplo, um retângulo pode ser descrito por quatro pontos:

$$
P_1=(0,0)
$$

$$
P_2=(4,0)
$$

$$
P_3=(4,2)
$$

$$
P_4=(0,2)
$$

A partir dessas informações, podemos reconstruir sua geometria.

---

# 3. Modelo 2D

Objetos bidimensionais são representados em um plano.

Cada ponto possui duas coordenadas:

$$
P=(x,y)
$$

Um retângulo pode ser representado por:

```python id="8j6v9x"
retangulo = np.array([
    [0, 0],
    [4, 0],
    [4, 2],
    [0, 2]
])
```

Podemos visualizá-lo:

```python id="8gkzj0"
import numpy as np
import matplotlib.pyplot as plt

retangulo = np.array([
    [0, 0],
    [4, 0],
    [4, 2],
    [0, 2],
    [0, 0]
])

plt.plot(
    retangulo[:, 0],
    retangulo[:, 1]
)

plt.axis("equal")
plt.grid()
plt.show()
```

A última coordenada repete a primeira para fechar o contorno.

---

# 4. Vértices

Um **vértice** representa uma posição no espaço.

Em 2D:

$$
V=(x,y)
$$

Em 3D:

$$
V=(x,y,z)
$$

Considere um cubo.

Ele possui oito vértices.

Uma representação simplificada:

```text id="4q0x2s"
       4---------5
      /|        /|
     / |       / |
    0---------1  |
    |  7------|--6
    | /       | /
    |/        |/
    3---------2
```

Cada número identifica um vértice.

---

# 5. Arestas

Uma aresta conecta dois vértices.

Se:

$$
V_1=(0,0)
$$

e:

$$
V_2=(1,0)
$$

podemos representar uma aresta:

$$
E=(V_1,V_2)
$$

Em uma estrutura computacional:

```python id="v8cn2v"
vertices = [
    (0, 0),
    (1, 0)
]

arestas = [
    (0, 1)
]
```

Os números da lista `arestas` podem indicar os índices dos vértices.

Isso evita duplicar as coordenadas.

---

# 6. Faces

Uma face representa uma região delimitada por arestas.

Por exemplo, um triângulo:

```text id="20s3m6"
     V0
     /\
    /  \
   /    \
 V1------V2
```

pode ser representado por:

```python id="7x7w3m"
face = (0, 1, 2)
```

onde:

```text id="e7fj3x"
0 → primeiro vértice
1 → segundo vértice
2 → terceiro vértice
```

Uma face quadrilateral poderia ser:

```python id="8a9vxm"
face = (0, 1, 2, 3)
```

Entretanto, em muitos pipelines gráficos, superfícies são subdivididas em triângulos.

---

# 7. Malha poligonal

Uma **malha poligonal** ou *polygon mesh* é uma estrutura composta por:

* vértices;
* arestas;
* faces.

Podemos representá-la conceitualmente como:

```text id="4t6x3m"
           Malha
             │
     ┌───────┼───────┐
     ↓       ↓       ↓
 Vértices  Arestas  Faces
```

Esse é um dos modelos mais importantes para representação de objetos tridimensionais.

---

# 8. Por que utilizar malhas?

Imagine representar uma esfera utilizando apenas uma equação matemática.

Uma esfera pode ser definida implicitamente por:

$$
x^2+y^2+z^2=r^2
$$

Essa representação é matematicamente elegante.

Porém, para renderizar a superfície em um pipeline baseado em polígonos, pode ser conveniente aproximá-la utilizando uma malha.

```text id="d4x6t1"
       ______
    .-'      '-.
  .'  /\  /\    '.
 /   /__\/__\     \
|    \  /\  /      |
 \    \/__\/      /
  '.            .'
    '-.______.-'
```

Quanto mais polígonos forem utilizados, mais próxima a aproximação pode ficar de uma superfície suave.

---

# 9. Resolução geométrica

Considere duas representações de uma esfera.

### Baixa resolução

```text id="f8x8u0"
       ___
    .-'   '-.
   /  /\ /\  \
  |  /__V__\ |
   \         /
    '-.____.-'
```

### Alta resolução

```text id="n8d2qk"
        ______
    .-''      ''-.
  .'  /\/\/\/\/\  '.
 /  /\/\/\/\/\/\/\  \
|  /\/\/\/\/\/\/\/\  |
 \  \/\/\/\/\/\/\/  /
  '.  \/\/\/\/\/  .'
    '-.________.-'
```

A segunda utiliza mais elementos.

Isso pode proporcionar uma aproximação visual melhor, mas aumenta:

* memória;
* processamento;
* armazenamento;
* quantidade de dados transferidos.

---

# 10. Triangulação

Um polígono pode ser subdividido em triângulos.

Considere um quadrado:

```text id="r5n4as"
A---------B
|        /|
|      /  |
|    /    |
|  /      |
|/        |
D---------C
```

Ele pode ser dividido em:

```text id="0j6w8f"
Triângulo 1:
A, B, C

Triângulo 2:
A, C, D
```

Em Python:

```python id="5w8m2u"
vertices = np.array([
    [0, 0],
    [1, 0],
    [1, 1],
    [0, 1]
])

faces = [
    (0, 1, 2),
    (0, 2, 3)
]
```

Essa estrutura é muito comum em Computação Gráfica.

---

# 11. Por que triângulos?

Triângulos possuem propriedades importantes:

* três pontos sempre definem um plano, desde que não sejam colineares;
* são simples de rasterizar;
* podem representar superfícies complexas;
* permitem interpolação de atributos;
* são amplamente suportados pelas APIs gráficas.

Assim:

```text id="i9v8s7"
Objeto complexo
      ↓
Malha
      ↓
Triângulos
      ↓
Rasterização
      ↓
Pixels
```

---

# 12. Representando uma malha em Python

Podemos criar uma estrutura simples:

```python id="o4m4gl"
import numpy as np

vertices = np.array([
    [0, 0, 0],
    [1, 0, 0],
    [1, 1, 0],
    [0, 1, 0]
])

faces = [
    (0, 1, 2),
    (0, 2, 3)
]
```

Temos:

```text id="7w4n7j"
vertices
    ↓
posições

faces
    ↓
índices que conectam posições
```

Uma vantagem dessa representação é evitar repetição.

O ponto:

```text id="3jv4bw"
[0, 0, 0]
```

pode participar de várias faces sem precisar ser armazenado novamente.

---

# 13. Topologia × geometria

Essa distinção é importante.

## Geometria

Está relacionada aos valores das posições.

Por exemplo:

$$
V_0=(0,0,0)
$$

$$
V_1=(1,0,0)
$$

## Topologia

Está relacionada à forma como os elementos estão conectados.

Por exemplo:

```text id="52r3d5"
Face 1 = (0,1,2)
Face 2 = (0,2,3)
```

Se modificarmos:

```text id="y3d7tr"
V_0
```

alteramos a geometria.

Se modificarmos:

```text id="c0q3mw"
Face 1
```

podemos alterar a topologia.

Essa distinção será particularmente útil em modelagem 3D.

---

# 14. Objeto 3D simples: um cubo

Um cubo é um excelente exemplo didático.

Podemos definir seus oito vértices:

```python id="p3yn1z"
vertices = np.array([
    [-1, -1, -1],
    [ 1, -1, -1],
    [ 1,  1, -1],
    [-1,  1, -1],

    [-1, -1,  1],
    [ 1, -1,  1],
    [ 1,  1,  1],
    [-1,  1,  1]
])
```

Cada coordenada possui:

```text id="qgh0su"
x
y
z
```

---

# 15. Faces do cubo

Podemos representar suas faces como quadriláteros:

```python id="yqk8m5"
faces = [
    (0, 1, 2, 3),
    (4, 5, 6, 7),
    (0, 1, 5, 4),
    (2, 3, 7, 6),
    (1, 2, 6, 5),
    (0, 3, 7, 4)
]
```

Para muitas aplicações gráficas, essas faces podem posteriormente ser trianguladas.

---

# 16. Visualizando um cubo

Para visualização didática, podemos utilizar o Matplotlib.

```python id="74fj3x"
import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D

fig = plt.figure()
ax = fig.add_subplot(111, projection="3d")

ax.scatter(
    vertices[:, 0],
    vertices[:, 1],
    vertices[:, 2]
)

ax.set_xlabel("X")
ax.set_ylabel("Y")
ax.set_zlabel("Z")

plt.show()
```

Aqui estamos visualizando os vértices.

Ainda não estamos realizando uma renderização 3D completa.

Essa distinção é importante.

---

# 17. Desenhando as arestas

Podemos conectar os vértices.

```python id="7j9v0x"
arestas = [
    (0, 1), (1, 2), (2, 3), (3, 0),
    (4, 5), (5, 6), (6, 7), (7, 4),
    (0, 4), (1, 5), (2, 6), (3, 7)
]
```

Depois:

```python id="5b7c9x"
for i, j in arestas:
    ax.plot(
        [vertices[i, 0], vertices[j, 0]],
        [vertices[i, 1], vertices[j, 1]],
        [vertices[i, 2], vertices[j, 2]]
    )
```

Agora temos uma representação em **wireframe**.

---

# 18. Wireframe

Wireframe significa representar um objeto principalmente por suas arestas.

Conceitualmente:

```text id="8jbr9q"
      +------+
     /      /|
    +------+ |
    |      | |
    |      | +
    |      |/
    +------+
```

Essa representação é útil para:

* visualizar a estrutura;
* depurar modelos;
* observar topologia;
* estudar transformações;
* compreender a geometria.

Entretanto, wireframe não representa necessariamente as superfícies preenchidas.

---

# 19. Superfícies

Para representar visualmente uma superfície, precisamos considerar as faces.

Em uma renderização mais completa, as faces podem receber informações como:

* cor;
* material;
* normal;
* textura.

Por exemplo:

```text id="v4w7g3"
Face
 ├── vértices
 ├── normal
 ├── material
 └── coordenadas de textura
```

Essas informações serão fundamentais nos capítulos sobre:

* câmera;
* iluminação;
* materiais;
* texturas;
* renderização.

---

# 20. Normais

Uma superfície pode possuir um vetor normal.

Em uma face plana, a normal é perpendicular à superfície.

Considere:

```text id="s3h6uj"
       ↑ normal
       |
       |
-------+-------
      superfície
```

Se temos dois vetores pertencentes à superfície:

$$
\vec{u}
$$

e:

$$
\vec{v}
$$

podemos obter um vetor perpendicular utilizando o produto vetorial:

$$
\vec{n}=\vec{u}\times\vec{v}
$$

A direção da normal depende da ordem dos vetores.

---

# 21. Produto vetorial

Para vetores 3D:

$$
\vec{u}=
(u_x,u_y,u_z)
$$

e:

$$
\vec{v}=
(v_x,v_y,v_z)
$$

o produto vetorial produz um vetor perpendicular aos dois.

Em Python:

```python id="l5p1rd"
u = np.array([1, 0, 0])
v = np.array([0, 1, 0])

normal = np.cross(u, v)

print(normal)
```

Resultado:

```text id="q5v6jk"
[0 0 1]
```

Ou seja, obtemos uma direção perpendicular ao plano formado pelos vetores.

---

# 22. Normalização da normal

Podemos normalizar:

```python id="1w6h3n"
normal = normal / np.linalg.norm(normal)
```

Uma normal unitária é frequentemente desejável porque facilita cálculos posteriores de iluminação.

Por exemplo:

```text id="n9zq1v"
normal
  ↓
iluminação
  ↓
intensidade da superfície
```

Esse assunto será retomado quando estudarmos modelos de iluminação.

---

# 23. Modelagem hierárquica

Objetos complexos normalmente são compostos por partes.

Considere um robô:

```text id="v3k7b1"
Robô
│
├── Tronco
│
├── Braço esquerdo
│   ├── Antebraço
│   └── Mão
│
├── Braço direito
│   ├── Antebraço
│   └── Mão
│
├── Perna esquerda
│
└── Perna direita
```

Cada componente pode possuir seu próprio sistema de coordenadas.

Isso permite construir estruturas hierárquicas.

---

# 24. Transformações hierárquicas

Suponha:

```text id="v8f2xj"
Carro
 └── Roda
      └── Pneu
```

Se o carro se move, a roda deve acompanhá-lo.

Podemos representar:

$$
M_{roda}=M_{carro}M_{roda-local}
$$

Se houver uma transformação adicional para o pneu:

$$
M_{pneu}
========

M_{carro}
M_{roda}
M_{pneu-local}
$$

Isso permite construir objetos complexos a partir de componentes menores.

---

# 25. Exemplo em Python

Podemos representar um objeto hierárquico de maneira simplificada:

```python id="m3v0ab"
class Objeto3D:

    def __init__(self, nome):
        self.nome = nome
        self.filhos = []
        self.transformacao = np.eye(4)

    def adicionar(self, objeto):
        self.filhos.append(objeto)
```

Uso:

```python id="j7x8m3"
robo = Objeto3D("Robô")

braco = Objeto3D("Braço")
mao = Objeto3D("Mão")

braco.adicionar(mao)
robo.adicionar(braco)
```

Temos uma árvore:

```text id="r9k3xz"
Robô
  └── Braço
       └── Mão
```

---

# 26. Por que hierarquia é importante?

Sem uma estrutura hierárquica, uma aplicação precisaria controlar manualmente todas as dependências.

Com hierarquia:

```text id="w4t1e2"
Transformação do pai
        ↓
propaga-se aos filhos
```

Isso é útil em:

* animação;
* jogos;
* simuladores;
* aplicações CAD;
* robótica;
* interfaces gráficas.

---

# 27. Representação de objetos 2D

Nem todo objeto precisa de uma malha 3D.

Uma interface pode utilizar:

```text id="k0t4z6"
Botão
 ├── retângulo
 ├── texto
 └── ícone
```

Um mapa pode utilizar:

```text id="2xq9bf"
Mapa
 ├── linhas
 ├── polígonos
 └── pontos
```

Um editor vetorial pode utilizar:

* segmentos;
* círculos;
* curvas;
* polígonos;
* textos.

---

# 28. Representações alternativas

Malhas poligonais são importantes, mas não são a única forma de representação.

Podemos utilizar:

### Geometria analítica

Equações descrevendo objetos.

### Curvas

Por exemplo, curvas paramétricas.

### Superfícies

Modelos matemáticos de superfícies.

### Voxels

Representação volumétrica baseada em elementos tridimensionais discretos.

### Nuvens de pontos

Conjuntos de pontos obtidos, por exemplo, por sensores ou reconstrução 3D.

Cada representação apresenta vantagens e limitações.

---

# 29. Malha versus nuvem de pontos

Considere:

```text id="1x5n9f"
Nuvem de pontos

•   •     •
   •  •
•       •
    •      •
```

A nuvem informa posições, mas não necessariamente informa diretamente quais pontos formam uma superfície conectada.

Uma malha fornece conectividade:

```text id="1g4v4s"
•------•
|\     |
| \    |
|  \   |
|   \  |
|    \ |
•------•
```

Portanto:

```text id="y8h1x5"
Nuvem de pontos
      ↓
posições

Malha
      ↓
posições + conectividade
```

---

# 30. Formatos e armazenamento

Modelos 3D precisam ser armazenados.

Existem diferentes formatos e estruturas para isso.

Um modelo pode precisar armazenar:

```text id="m3v2g8"
Vértices
Arestas
Faces
Normais
Materiais
Texturas
Coordenadas UV
```

O formato utilizado depende da aplicação.

No contexto de Engenharia de Software, uma decisão importante é não confundir:

```text id="0j5g0m"
Modelo lógico
```

com:

```text id="y7b2f4"
Formato de arquivo
```

Um formato de arquivo é uma maneira de serializar os dados.

---

# 31. Separação entre modelo e visualização

Considere um objeto armazenado como:

```python id="1t3m9j"
class Modelo:
    vertices = ...
    faces = ...
```

Podemos visualizá-lo utilizando diferentes ferramentas.

```text id="m2z4xp"
Modelo
  │
  ├── Visualizador A
  │
  ├── Visualizador B
  │
  └── Renderizador
```

Isso é uma decisão arquitetural importante.

O modelo não deveria necessariamente depender diretamente de uma biblioteca específica de visualização.

---

# 32. Projeto de software para objetos gráficos

Uma arquitetura introdutória poderia ser:

```text id="s6d9j2"
             Aplicação
                 │
        ┌────────┴────────┐
        ↓                 ↓
    Modelos            Visualização
        │                 │
        ↓                 ↓
   Geometria          Renderização
        │
        ↓
 Transformações
```

Isso permite separar responsabilidades.

Por exemplo:

```python id="7a6z0q"
class Mesh:
    def __init__(self, vertices, faces):
        self.vertices = vertices
        self.faces = faces
```

E:

```python id="m8r3k4"
class Transform:
    def __init__(self):
        self.matrix = np.eye(4)
```

A aplicação pode combinar os componentes.

---

# 33. Exercício prático — Construindo um cubo

Implemente uma classe:

```python id="2n4f1v"
class Mesh:
    def __init__(self, vertices, faces):
        self.vertices = vertices
        self.faces = faces
```

Depois crie:

```python id="x7j3y9"
cubo = Mesh(vertices, faces)
```

Implemente uma função:

```python id="9z5g8k"
def desenhar_wireframe(mesh):
    ...
```

A função deverá:

1. percorrer as faces;
2. identificar seus vértices;
3. desenhar suas arestas;
4. exibir o objeto.

Como extensão, permita aplicar uma matriz de transformação (4\times4).

---

# 34. Exercício — Malha de um plano

Crie uma malha representando um plano:

```text id="6h5d2x"
•------•------•
|      |      |
|      |      |
•------•------•
|      |      |
|      |      |
•------•------•
```

A malha deve possuir:

* 9 vértices;
* 4 faces quadrilaterais.

Depois transforme cada quadrilátero em dois triângulos.

Pergunta:

> Quantos triângulos serão obtidos?

---

# 35. Exercício — Esfera aproximada

Utilize uma biblioteca ou gere matematicamente uma malha simples de esfera.

Compare:

* 8 segmentos;
* 16 segmentos;
* 32 segmentos.

Analise:

* número de vértices;
* número de faces;
* aparência;
* custo computacional.

A pergunta central é:

> **Qual é o custo de aumentar a resolução geométrica?**

---

# 36. Exercício — Hierarquia

Modele um braço robótico simples:

```text id="q8y5u1"
Base
 |
 └── Braço
      |
      └── Antebraço
            |
            └── Garra
```

Cada componente deve possuir:

* posição;
* transformação;
* relação com o pai.

Depois faça o braço girar e observe como os componentes filhos acompanham o movimento.

---

# 37. Projeto integrado

Desenvolva uma pequena aplicação de modelagem 3D.

A aplicação deverá:

1. representar uma malha;
2. armazenar vértices;
3. armazenar faces;
4. visualizar a malha em wireframe;
5. aplicar transformações;
6. permitir combinar objetos;
7. representar pelo menos uma hierarquia de objetos.

### Sugestão

Crie primitivas:

```python id="v1q8n0"
criar_cubo()
criar_plano()
criar_piramide()
```

Todas devem retornar objetos do mesmo tipo:

```python id="g8r2m7"
Mesh
```

Assim, a aplicação poderá tratar diferentes formas de maneira uniforme.

---

# 38. Questões de revisão

1. O que é modelagem geométrica?

2. Qual é a diferença entre uma representação raster e uma representação geométrica?

3. O que são vértices?

4. O que são arestas?

5. O que são faces?

6. O que é uma malha poligonal?

7. Por que triângulos são tão utilizados em Computação Gráfica?

8. Qual é a diferença entre geometria e topologia?

9. O que é uma normal?

10. Para que serve uma representação wireframe?

11. Por que modelos 3D podem utilizar estruturas hierárquicas?

12. Qual é a diferença entre uma nuvem de pontos e uma malha?

13. Quais são os custos associados ao aumento da resolução geométrica?

14. Por que é interessante separar o modelo geométrico da visualização?

---

# Síntese do capítulo

A modelagem geométrica define como objetos são representados antes de serem convertidos em imagens.

Uma das representações mais importantes é a malha poligonal:

$$
\text{Malha} =
{\text{vértices},\text{arestas},\text{faces}}
$$

Para objetos 3D, essa representação permite construir desde formas simples até modelos extremamente complexos.

Também estudamos:

* vértices;
* arestas;
* faces;
* triangulação;
* malhas;
* wireframe;
* normais;
* topologia;
* geometria;
* hierarquia;
* nuvens de pontos;
* representação de objetos 2D e 3D.

A relação com os capítulos anteriores pode ser resumida como:

```text id="s5t4z2"
Modelagem
    ↓
Vértices / faces
    ↓
Transformações
    ↓
Câmera
    ↓
Projeção
    ↓
Rasterização
    ↓
Pixels
```

Essa sequência constitui uma visão simplificada da pipeline gráfica.

No próximo capítulo, começaremos a estudar justamente os elementos que permitem transformar uma cena tridimensional em uma imagem observável: **câmera, sistemas de referência e projeção**.