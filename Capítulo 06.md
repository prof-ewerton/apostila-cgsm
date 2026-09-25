# Parte 6 — Visualização, Câmera e Projeções

## Capítulo 6 — Câmera, Sistemas de Referência e Projeções

### 6.1 Objetivos

Ao final deste capítulo, o estudante deverá ser capaz de:

* compreender como um objeto 3D é transformado em uma imagem 2D;
* distinguir diferentes sistemas de coordenadas;
* compreender o conceito de câmera virtual;
* entender a transformação de mundo para o sistema da câmera;
* diferenciar projeção ortográfica e perspectiva;
* compreender campo de visão (*Field of View — FOV*);
* implementar projeções simples em Python;
* relacionar o processo de visualização com uma *pipeline* gráfica;
* compreender a importância dessas operações em aplicações gráficas e multimídia.

---

# 6.2 O problema fundamental da visualização 3D

Um modelo tridimensional possui três coordenadas:

$$
P=(x,y,z)
$$

Uma tela, entretanto, possui essencialmente duas dimensões:

$$
p=(u,v)
$$

Surge então uma questão fundamental:

> **Como transformar uma cena tridimensional em uma imagem bidimensional?**

Esse processo é chamado de **projeção**.

De forma simplificada:

$$
\text{Mundo 3D}
\rightarrow
\text{Câmera}
\rightarrow
\text{Projeção}
\rightarrow
\text{Imagem 2D}
$$

Essa ideia está presente em praticamente todos os sistemas gráficos 3D.

Por exemplo, quando um jogo apresenta uma cena contendo:

* personagens;
* árvores;
* edifícios;
* veículos;
* iluminação;
* objetos distantes;

todos esses elementos precisam ser transformados em uma representação que possa ser exibida na tela.

---

# 6.3 O conceito de câmera virtual

Na Computação Gráfica, normalmente não existe uma câmera física.

Existe uma **câmera virtual**.

Ela define:

* onde estamos observando;
* para qual direção estamos olhando;
* qual é a orientação da imagem;
* qual região da cena será visualizada;
* como a profundidade será convertida em tamanho aparente.

Podemos imaginar uma câmera posicionada em:

$$
C=(c_x,c_y,c_z)
$$

e olhando para algum ponto:

$$
T=(t_x,t_y,t_z)
$$

Por exemplo:

```text
                objeto
                  ●
                 /
                /
               /
        C ●---/
      câmera
```

A posição da câmera sozinha não é suficiente.

É necessário também determinar sua orientação.

---

# 6.4 Sistemas de coordenadas

Uma das ideias mais importantes deste capítulo é que um objeto pode ser descrito em diferentes sistemas de coordenadas.

Considere um vértice:

$$
P=(2,1,3)
$$

Essas coordenadas podem significar coisas diferentes dependendo do sistema de referência utilizado.

Os principais sistemas são:

1. coordenadas do objeto;
2. coordenadas do mundo;
3. coordenadas da câmera;
4. coordenadas de projeção;
5. coordenadas da tela.

---

## 6.4.1 Coordenadas do objeto

São as coordenadas utilizadas para definir a geometria do próprio objeto.

Considere um cubo centrado na origem:

```text
(-1,-1,-1)
(+1,-1,-1)
(+1,+1,-1)
(-1,+1,-1)
...
```

Essas coordenadas descrevem o cubo independentemente de onde ele será colocado na cena.

Isso é extremamente útil.

Um modelo de carro, por exemplo, pode ser criado uma única vez em coordenadas locais.

Depois podemos criar vários carros:

```text
carro 1 → posição (10, 0, 5)
carro 2 → posição (-5, 0, 20)
carro 3 → posição (30, 0, -10)
```

O modelo geométrico continua sendo o mesmo.

---

# 6.5 Coordenadas do mundo

O **sistema de coordenadas do mundo** representa a cena inteira.

Podemos definir, por exemplo:

* eixo X → esquerda/direita;
* eixo Y → baixo/cima;
* eixo Z → profundidade.

Uma cena pode conter:

```text
               Y
               ↑
               |
               |
               |
               +------------→ X
              /
             /
            ↓
            Z
```

Os objetos são posicionados nesse espaço.

Por exemplo:

```python
objetos = [
    {"nome": "cubo", "posicao": (2, 0, 5)},
    {"nome": "esfera", "posicao": (-3, 1, 8)},
    {"nome": "cone", "posicao": (4, 0, 12)}
]
```

Cada objeto possui uma posição no mundo.

---

# 6.6 Coordenadas da câmera

A câmera possui seu próprio sistema de referência.

Em vez de pensar:

> "Onde está o objeto no mundo?"

podemos pensar:

> "Onde está o objeto em relação à câmera?"

Essa mudança de perspectiva é fundamental.

Suponha:

```text
Câmera → (0, 0, 0)
Objeto → (0, 0, 10)
```

O objeto está a 10 unidades da câmera.

Se a câmera se deslocar para:

```text
(0, 0, 5)
```

o objeto passa a estar a 5 unidades dela, mesmo que sua posição no mundo não tenha mudado.

Portanto, movimentar a câmera altera as coordenadas utilizadas para representar os objetos **relativamente à câmera**.

---

# 6.7 Transformação de visualização

A transformação que converte coordenadas do mundo para coordenadas da câmera é frequentemente chamada de:

**transformação de visualização**, ou *view transformation*.

Conceitualmente:

$$
P_{camera}=V P_{world}
$$

onde:

* \(P_{world}\) é o ponto no sistema do mundo;
* \(V\) é a transformação de visualização;
* \(P_{camera}\) é o ponto no sistema da câmera.

Na prática, essa transformação é normalmente representada por uma matriz \(4\times4\).

---

# 6.8 Por que utilizar coordenadas homogêneas?

Transformações gráficas utilizam frequentemente coordenadas homogêneas.

Um ponto 3D:

$$
(x,y,z)
$$

é representado como:

$$
(x,y,z,1)
$$

Isso permite representar diversas operações utilizando multiplicação matricial:

* translação;
* rotação;
* escala;
* composição de transformações.

Por exemplo, uma translação pode ser representada por:

$$
T=
\begin{bmatrix}
1&0&0&t_x\\
0&1&0&t_y\\
0&0&1&t_z\\
0&0&0&1
\end{bmatrix}
$$

e:

$$
P'=TP
$$

 $$
\begin{bmatrix}
1&0&0&t_x\\
0&1&0&t_y\\
0&0&1&t_z\\
0&0&0&1
\end{bmatrix}
$$

 ### Escala

 $$
\begin{bmatrix}
s_x&0&0&0\\
0&s_y&0&0\\
0&0&s_z&0\\
0&0&0&1
\end{bmatrix}
$$

 ### Rotação

 Por exemplo, rotação em torno de $Z$:

 $$
\begin{bmatrix}
\cos\theta&-\sin\theta&0&0\\
\sin\theta&\cos\theta&0&0\\
0&0&1&0\\
0&0&0&1
\end{bmatrix}
$$

---

# 6.9 Exemplo de transformação em Python

Podemos representar um ponto usando NumPy:

```python
import numpy as np

p = np.array([2, 3, 4, 1])

T = np.array([
    [1, 0, 0, 5],
    [0, 1, 0, 2],
    [0, 0, 1, 1],
    [0, 0, 0, 1]
])

resultado = T @ p

print(resultado)
```

Resultado:

```text
[7 5 5 1]
```

Ou seja:

$$
(2,3,4)
\rightarrow
(7,5,5)
$$

A transformação deslocou o ponto em:

$$
(5,2,1)
$$

---

# 6.10 Rotação da câmera

A câmera também pode possuir orientação.

Por exemplo, ela pode:

* girar para a esquerda;
* girar para a direita;
* olhar para cima;
* olhar para baixo.

Isso pode ser representado por rotações.

Uma rotação em torno do eixo Y é dada por:

$$
R_y(\theta)=
\begin{bmatrix}
\cos\theta&0&\sin\theta&0\\
0&1&0&0\\
-\sin\theta&0&\cos\theta&0\\
0&0&0&1
\end{bmatrix}
$$

Em Python:

```python
import numpy as np

def rotacao_y(angulo):
    c = np.cos(angulo)
    s = np.sin(angulo)

    return np.array([
        [c, 0, s, 0],
        [0, 1, 0, 0],
        [-s, 0, c, 0],
        [0, 0, 0, 1]
    ])
```

---

# 6.11 A câmera como transformação inversa

Existe uma ideia importante:

> Para visualizar uma cena como se a câmera tivesse se movimentado, normalmente transformamos a cena na direção oposta.

Se a câmera se desloca para a direita, por exemplo, os objetos são tratados como se tivessem se deslocado para a esquerda no sistema da câmera.

Isso explica por que a matriz de visualização está relacionada à **transformação inversa da câmera**.

Se a câmera possui transformação:

$$
M_{camera}
$$

a transformação utilizada para levar o mundo ao espaço da câmera é conceitualmente:

$$
V=M_{camera}^{-1}
$$

Essa distinção é importante na implementação de motores gráficos.

---

# 6.12 Projeção

Depois de transformar os objetos para o sistema da câmera, precisamos projetá-los.

A projeção transforma:

$$
(x,y,z)
\rightarrow
(u,v)
$$

Existem dois tipos fundamentais:

1. **projeção ortográfica**;
2. **projeção perspectiva**.

---

# 6.13 Projeção ortográfica

Na projeção ortográfica, objetos não diminuem de tamanho quando se afastam.

Linhas paralelas permanecem paralelas.

Imagine:

```text
Objeto próximo       Objeto distante

    ███                  ███
    ███                  ███
    ███                  ███
```

Os dois objetos possuem aproximadamente o mesmo tamanho projetado.

Essa projeção é muito utilizada em:

* CAD;
* engenharia;
* desenho técnico;
* visualizações arquitetônicas;
* interfaces 3D;
* alguns jogos;
* visualização científica.

---

# 6.14 Projeção ortográfica simplificada

Uma projeção ortográfica simples pode ignorar a coordenada \(z\):

$$
u=x
$$

$$
v=y
$$

Portanto:

```python
def projetar_ortografico(pontos):
    return pontos[:, :2]
```

Exemplo:

```python
import numpy as np

pontos = np.array([
    [1, 2, 5],
    [3, 4, 10],
    [-2, 1, 20]
])

projetados = projetar_ortografico(pontos)

print(projetados)
```

Resultado:

```text
[[ 1  2]
 [ 3  4]
 [-2  1]]
```

Observe que o valor de \(z\) não altera diretamente a posição projetada.

---

# 6.15 Projeção perspectiva

A projeção perspectiva é mais próxima do comportamento da visão humana.

Objetos distantes parecem menores.

Por exemplo:

```text
Objeto próximo

████████
████████
████████


Objeto distante

  ████
  ████
```

Esse efeito é conhecido como **perspectiva**.

É fundamental para produzir a sensação de profundidade em cenas 3D.

---

# 6.16 Modelo matemático da perspectiva

Um modelo simplificado da projeção perspectiva é:

$$
u=f\frac{x}{z}
$$

$$
v=f\frac{y}{z}
$$

onde:

* \(x\) é a coordenada horizontal;
* \(y\) é a coordenada vertical;
* \(z\) representa a profundidade;
* \(f\) é a distância focal.

Observe a característica fundamental:

$$
u,v \propto \frac{1}{z}
$$

Quanto maior \(z\), menor será a projeção.

---

# 6.17 Implementando perspectiva em Python

```python
import numpy as np

def projetar_perspectiva(pontos, f=1.0):
    x = pontos[:, 0]
    y = pontos[:, 1]
    z = pontos[:, 2]

    u = f * x / z
    v = f * y / z

    return np.column_stack((u, v))
```

Teste:

```python
pontos = np.array([
    [1, 1, 2],
    [1, 1, 4],
    [1, 1, 8]
])

resultado = projetar_perspectiva(pontos)

print(resultado)
```

Conceitualmente teremos:

```text
z = 2 → (0.5, 0.5)
z = 4 → (0.25, 0.25)
z = 8 → (0.125, 0.125)
```

O mesmo ponto relativo em X e Y aparece progressivamente menor à medida que se afasta.

---

# 6.18 Visualizando a perspectiva

Podemos utilizar Matplotlib para visualizar vários pontos.

```python
import numpy as np
import matplotlib.pyplot as plt

pontos = np.array([
    [-2, -1, 2],
    [ 2, -1, 2],
    [ 2,  1, 2],
    [-2,  1, 2],

    [-2, -1, 6],
    [ 2, -1, 6],
    [ 2,  1, 6],
    [-2,  1, 6]
])

projetados = projetar_perspectiva(pontos, f=2)

plt.scatter(
    projetados[:, 0],
    projetados[:, 1]
)

plt.xlabel("u")
plt.ylabel("v")
plt.axis("equal")
plt.grid()
plt.show()
```

Os pontos correspondentes à região mais distante aparecerão mais próximos do centro da projeção.

---

# 6.19 Comparando ortográfica e perspectiva

| Característica      | Ortográfica          | Perspectiva     |
| ------------------- | -------------------- | --------------- |
| Objetos distantes   | Mesmo tamanho        | Menores         |
| Paralelas           | Permanecem paralelas | Podem convergir |
| Profundidade visual | Menor                | Maior           |
| Uso técnico         | Muito comum          | Menos comum     |
| Realismo visual     | Menor                | Maior           |
| Jogos 3D            | Possível             | Muito comum     |
| CAD                 | Muito comum          | Também possível |

A escolha da projeção depende do objetivo da aplicação.

---

# 6.20 Campo de visão — FOV

Em uma câmera perspectiva, outro conceito importante é o **campo de visão** (*Field of View*).

O FOV determina quanto da cena a câmera consegue observar.

Imagine:

```text
          /\
         /  \
        /    \
       /      \
      /        \
     C----------→
```

Um FOV maior mostra uma região maior da cena.

Um FOV menor mostra uma região mais estreita.

---

# 6.21 FOV e sensação de profundidade

O campo de visão também altera a percepção visual.

Um FOV mais amplo pode produzir uma aparência de maior amplitude espacial.

Um FOV muito grande pode produzir distorções perceptíveis.

Um FOV pequeno pode produzir uma aparência mais "aproximada".

Em aplicações interativas, o FOV normalmente é configurável.

Por exemplo:

```python
fov = 60
```

ou:

```python
fov = 90
```

Em uma câmera de jogo, alterar o FOV pode mudar significativamente a experiência do usuário.

---

# 6.22 Aspect ratio

Outro parâmetro importante é a razão de aspecto:

$$
aspect=\frac{largura}{altura}
$$

Por exemplo, uma janela:

```text
1920 × 1080
```

possui:

$$
aspect=\frac{1920}{1080}\approx1.7778
$$

ou aproximadamente:

$$
16:9
$$

Se o aspecto da projeção não for compatível com o da janela, objetos podem parecer deformados.

Esse é um problema comum em aplicações gráficas.

---

# 6.23 Clipping

Nem tudo que está na cena precisa ser desenhado.

Uma câmera possui limites de visualização.

Podemos definir:

* plano de recorte próximo (*near plane*);
* plano de recorte distante (*far plane*).

Conceitualmente:

```text
        near                    far
         │                       │
         ↓                       ↓
Camera   |=======================|
   C     |     região visível    |
```

Objetos fora dessa região podem ser descartados antes da rasterização.

Isso é chamado de **clipping**.

Além de ser necessário para definir a região visualizada, o clipping ajuda no desempenho.

---

# 6.24 Pipeline de visualização

Podemos agora construir uma visão geral do processo.

Uma forma simplificada é:

```text
Coordenadas do objeto
        ↓
Transformação do modelo
        ↓
Coordenadas do mundo
        ↓
Transformação da câmera
        ↓
Coordenadas da câmera
        ↓
Projeção
        ↓
Coordenadas normalizadas
        ↓
Viewport
        ↓
Coordenadas da tela
        ↓
Rasterização
        ↓
Imagem
```

Essa sequência é chamada de **pipeline gráfica**.

---

# 6.25 Transformação do modelo

A transformação do modelo posiciona o objeto no mundo.

Podemos ter:

$$
P_{world}=M P_{object}
$$

onde \(M\) é a matriz do modelo.

Por exemplo, um objeto inicialmente localizado na origem pode ser:

* escalado;
* rotacionado;
* deslocado.

---

# 6.26 Transformação de visualização

Depois:

$$
P_{camera}=V P_{world}
$$

onde \(V\) é a matriz de visualização.

Ela transforma a representação para o sistema da câmera.

---

# 6.27 Transformação de projeção

Finalmente:

$$
P_{clip}=P P_{camera}
$$

onde \(P\) representa a transformação de projeção.

Em uma implementação completa, essas operações normalmente aparecem combinadas:

$$
P_{clip}=PVM P_{object}
$$

Essa equação é extremamente importante.

Ela mostra que um ponto passa por três grandes transformações:

$$
M \rightarrow V \rightarrow P
$$

ou:

```text
Model
  ↓
View
  ↓
Projection
```

---

# 6.28 Viewport

Depois da projeção, ainda precisamos transformar as coordenadas para a região efetiva da janela.

Essa região é chamada de **viewport**.

Suponha que a janela tenha:

```text
800 × 600
```

As coordenadas projetadas precisam ser convertidas para:

$$
0 \leq x < 800
$$

$$
0 \leq y < 600
$$

Uma transformação simples de coordenadas normalizadas para tela pode ser feita por:

```python
def viewport(x, y, largura, altura):
    tela_x = (x + 1) * largura / 2
    tela_y = (1 - y) * altura / 2

    return tela_x, tela_y
```

O detalhe:

```python
1 - y
```

é importante porque sistemas matemáticos e sistemas de imagem frequentemente utilizam orientações diferentes para o eixo vertical.

---

# 6.29 Um pipeline simplificado em Python

Podemos construir um exemplo didático completo.

```python
import numpy as np

def perspectiva(pontos, f=1.0):
    x = pontos[:, 0]
    y = pontos[:, 1]
    z = pontos[:, 2]

    u = f * x / z
    v = f * y / z

    return np.column_stack((u, v))


def viewport(pontos, largura, altura):
    x = (pontos[:, 0] + 1) * largura / 2
    y = (1 - pontos[:, 1]) * altura / 2

    return np.column_stack((x, y))
```

Podemos utilizar:

```python
pontos = np.array([
    [-1, -1, 4],
    [ 1, -1, 4],
    [ 1,  1, 4],
    [-1,  1, 4]
])

projetados = perspectiva(pontos, f=2)

tela = viewport(
    projetados,
    800,
    600
)

print(tela)
```

O processo é:

```text
Ponto 3D
   ↓
Perspectiva
   ↓
Coordenadas 2D
   ↓
Viewport
   ↓
Coordenadas de pixels
```

---

# 6.30 Exercício: comparar duas projeções

Considere os pontos:

```python
pontos = np.array([
    [-2, -1, 2],
    [ 2, -1, 2],
    [ 2,  1, 2],
    [-2,  1, 2],

    [-2, -1, 8],
    [ 2, -1, 8],
    [ 2,  1, 8],
    [-2,  1, 8]
])
```

Implemente:

```python
projetar_ortografico()
```

e:

```python
projetar_perspectiva()
```

Depois compare os resultados.

Perguntas:

1. Qual projeção preserva melhor as dimensões?
2. Qual produz sensação de profundidade?
3. O que acontece com os pontos mais distantes na projeção perspectiva?
4. Por que isso acontece?

---

# 6.31 Exercício: câmera simples

Crie uma classe:

```python
class Camera:
    def __init__(self, posicao):
        self.posicao = np.array(posicao)

    def visualizar(self, pontos):
        return pontos - self.posicao
```

Teste:

```python
camera = Camera((0, 0, 5))

pontos = np.array([
    [0, 0, 10],
    [2, 0, 10],
    [-2, 0, 10]
])

print(camera.visualizar(pontos))
```

A ideia é desenvolver intuitivamente o conceito de transformar coordenadas do mundo para coordenadas relativas à câmera.

---

# 6.32 Exercício: câmera orientável

Amplie a classe `Camera` para possuir:

* posição;
* rotação;
* direção de observação.

Por exemplo:

```python
class Camera:
    def __init__(self):
        self.posicao = np.zeros(3)
        self.rotacao = np.zeros(3)
```

Depois implemente métodos:

```python
mover()
```

```python
rotacionar()
```

```python
transformar()
```

O objetivo não é construir ainda uma câmera profissional, mas compreender a separação entre:

* estado da câmera;
* transformação;
* projeção.

---

# 6.33 Exercício: visualizar um cubo

Utilize o cubo desenvolvido no capítulo anterior.

Faça:

1. definir os oito vértices;
2. posicionar o cubo no mundo;
3. criar uma câmera;
4. aplicar uma transformação de visualização;
5. aplicar projeção perspectiva;
6. converter para viewport;
7. desenhar as arestas usando Matplotlib.

O resultado deverá ser uma representação 2D de um objeto 3D.

Esse exercício integra os conceitos dos capítulos anteriores.

---

# 6.34 Conexão com Engenharia de Software

A pipeline gráfica é também um excelente exemplo de **separação de responsabilidades**.

Podemos estruturar uma aplicação em componentes:

```text
Modelo
  │
  ├── Geometria
  └── Transformação
        │
        ↓
Câmera
        │
        ↓
Projeção
        │
        ↓
Rasterizador
        │
        ↓
Tela
```

Cada componente possui uma responsabilidade específica.

Isso favorece:

* modularidade;
* testabilidade;
* manutenção;
* reutilização;
* substituição de componentes.

Por exemplo, podemos trocar:

```text
projeção ortográfica
```

por:

```text
projeção perspectiva
```

sem necessariamente modificar a geometria do objeto.

Essa separação é um princípio importante de projeto de software.

---

# 6.35 Estrutura sugerida de uma pequena biblioteca

Um projeto didático poderia ser organizado assim:

```text
graphics/
│
├── geometry.py
├── transform.py
├── camera.py
├── projection.py
├── viewport.py
├── rasterizer.py
└── main.py
```

Por exemplo:

### `geometry.py`

Responsável por:

* pontos;
* vetores;
* arestas;
* faces;
* malhas.

### `transform.py`

Responsável por:

* translação;
* rotação;
* escala;
* composição de matrizes.

### `camera.py`

Responsável por:

* posição;
* orientação;
* transformação de visualização.

### `projection.py`

Responsável por:

* projeção ortográfica;
* projeção perspectiva.

### `rasterizer.py`

Responsável por:

* linhas;
* triângulos;
* pixels.

Esse tipo de decomposição aproxima o conteúdo de Computação Gráfica da prática de Engenharia de Software.

---

# 6.36 Erros conceituais comuns

## Erro 1 — Confundir posição da câmera com posição do objeto

A câmera possui seu próprio sistema de referência.

Mover a câmera não modifica necessariamente a posição do objeto no mundo.

---

## Erro 2 — Achar que projeção perspectiva simplesmente "remove" Z

Na projeção ortográfica simplificada podemos ignorar \(z\).

Na perspectiva, porém:

$$
u=f\frac{x}{z}
$$

$$
v=f\frac{y}{z}
$$

Logo, \(z\) influencia diretamente o resultado.

---

## Erro 3 — Misturar coordenadas de mundo com coordenadas de tela

Um ponto como:

```text
(100, 200)
```

pode significar:

* uma posição no mundo;
* uma posição na câmera;
* uma coordenada projetada;
* uma coordenada de pixel.

O significado depende do estágio da pipeline.

---

## Erro 4 — Ignorar o sistema de coordenadas da imagem

Em muitas bibliotecas de imagem:

```python
imagem[y, x]
```

é utilizado.

Portanto, a ordem não é:

```python
imagem[x, y]
```

mas:

```python
imagem[y, x]
```

Essa diferença é pequena, mas causa muitos erros práticos.

---

# 6.37 Projeto prático — Mini visualizador 3D

Desenvolva uma aplicação Python capaz de:

1. criar um cubo;
2. representar seus vértices;
3. definir suas arestas;
4. criar uma câmera;
5. mover a câmera;
6. aplicar transformação de visualização;
7. utilizar projeção perspectiva;
8. converter para coordenadas de tela;
9. rasterizar as arestas;
10. exibir o resultado.

Estrutura sugerida:

```text
MiniViewer3D
│
├── Mesh
│
├── Transform
│
├── Camera
│
├── Projection
│
├── Viewport
│
└── Renderer
```

### Extensões

Depois de implementar a versão básica, adicione:

* rotação do objeto;
* zoom;
* diferentes FOVs;
* projeção ortográfica;
* múltiplos objetos;
* controle por teclado;
* clipping;
* preenchimento de triângulos.

---

# 6.38 Perguntas de revisão

### 1. O que é uma câmera virtual?

### 2. Qual é a diferença entre coordenadas do objeto e coordenadas do mundo?

### 3. Para que serve a transformação de visualização?

### 4. Por que a transformação da câmera está relacionada à transformação inversa?

### 5. O que caracteriza uma projeção ortográfica?

### 6. O que caracteriza uma projeção perspectiva?

### 7. Por que objetos distantes parecem menores na perspectiva?

### 8. O que é FOV?

### 9. O que é clipping?

### 10. O que é viewport?

### 11. Qual é a função das matrizes Model, View e Projection?

### 12. O que significa a expressão:

$$
P_{clip}=PVM P_{object}
$$

### 13. Por que separar câmera, projeção e rasterização em módulos diferentes?

---

# 6.39 Síntese

Neste capítulo estudamos como uma cena 3D pode ser transformada em uma imagem 2D.

O processo envolve diferentes sistemas de coordenadas:

```text
Objeto
  ↓
Mundo
  ↓
Câmera
  ↓
Projeção
  ↓
Viewport
  ↓
Tela
```

As ideias centrais são:

* câmera virtual;
* sistemas de coordenadas;
* transformação de visualização;
* coordenadas homogêneas;
* projeção;
* projeção ortográfica;
* projeção perspectiva;
* FOV;
* clipping;
* viewport;
* pipeline gráfica.

A ideia matemática central pode ser resumida por:

$$
P_{clip}=PVM P_{object}
$$

ou, conceitualmente:

$$
\boxed{
\text{Objeto}
\rightarrow
\text{Mundo}
\rightarrow
\text{Câmera}
\rightarrow
\text{Projeção}
\rightarrow
\text{Tela}
}
$$

Essa pipeline constitui a base para os próximos conceitos de Computação Gráfica.