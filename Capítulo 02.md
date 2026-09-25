# Parte 2 — Capítulo 2: Representação e Processamento de Imagens Digitais

## Objetivos de aprendizagem

Ao final deste capítulo, o estudante deverá ser capaz de:

* explicar como uma imagem digital pode ser representada computacionalmente;
* distinguir os conceitos de pixel, resolução espacial e profundidade de cor;
* interpretar uma imagem como matriz ou arranjo multidimensional;
* compreender modelos básicos de representação de cores;
* explicar os conceitos de amostragem e quantização;
* realizar operações básicas sobre imagens utilizando Python;
* analisar como alterações nos valores dos pixels afetam a imagem visualizada;
* reconhecer limitações e erros comuns na manipulação de imagens digitais.

---

## Introdução

Uma imagem digital está presente em praticamente todos os sistemas computacionais modernos: interfaces gráficas, redes sociais, sistemas médicos, aplicações de engenharia, jogos, comércio eletrônico e sistemas de visão.

Apesar de uma imagem ser percebida visualmente como uma estrutura contínua, o computador precisa representá-la utilizando uma quantidade finita de dados.

Essa diferença entre a **informação visual percebida** e sua **representação computacional** é fundamental.

Considere uma fotografia. Para um observador, ela pode apresentar:

* formas;
* cores;
* sombras;
* detalhes;
* texturas;
* variações suaves de intensidade.

Para o computador, entretanto, uma imagem digital pode ser representada, de forma simplificada, como uma coleção organizada de valores numéricos.

```text
Imagem visual
      ↓
Divisão em posições discretas
      ↓
Pixels
      ↓
Valores numéricos
      ↓
Representação digital
```

Neste capítulo, estudaremos essa representação e utilizaremos Python para observar diretamente a relação entre os dados armazenados e a imagem percebida.

---

## Pré-requisitos

* programação básica em Python;
* variáveis e funções;
* listas e matrizes em nível introdutório;
* noções elementares de números inteiros e reais.

Para os exemplos, utilizaremos principalmente:

```python
numpy
matplotlib
Pillow
```

---

# 1. O que é uma imagem digital?

## 1.1 Intuição

Imagine uma folha de papel quadriculado.

Se cada quadrado puder receber uma cor, podemos criar uma figura preenchendo diferentes posições com diferentes valores.

Uma imagem digital pode ser compreendida de maneira semelhante.

```text
+---+---+---+---+---+
|   |   |   |   |   |
+---+---+---+---+---+
|   | X | X | X |   |
+---+---+---+---+---+
|   | X | X | X |   |
+---+---+---+---+---+
|   |   |   |   |   |
+---+---+---+---+---+
```

Cada pequena unidade é chamada de **elemento de imagem (picture element)**, tradicionalmente abreviado como **pixel**.

> **📌 Conceito-chave**
>
> Um pixel é uma unidade discreta utilizada para representar uma informação visual em determinada posição de uma imagem digital.

Entretanto, é importante não interpretar um pixel simplesmente como um pequeno quadrado físico.

O pixel é прежде de tudo uma **unidade de representação**. A forma como ele será apresentado depende do dispositivo e do processo de visualização.

---

# 2. Imagem como matriz

## 2.1 Imagem em tons de cinza

Uma imagem em tons de cinza pode ser representada por uma matriz bidimensional.

Considere:

$$
I(x,y)
$$

em que:

* (x) representa uma posição horizontal;
* (y) representa uma posição vertical;
* (I) representa a intensidade associada àquela posição.

Uma matriz pequena poderia ser:

$$
I =
\begin{bmatrix}
0 & 50 & 100 & 150 \
50 & 100 & 150 & 200 \
100 & 150 & 200 & 255
\end{bmatrix}
$$

Se utilizarmos uma representação de 8 bits, podemos associar:

* (0): preto;
* (255): branco;
* valores intermediários: diferentes níveis de cinza.

Visualmente:

```text
Intensidade baixa                     Intensidade alta

0 -------------------------------------- 255

preto       cinza escuro      cinza       branco
```

---

## 2.2 Representando uma imagem com NumPy

Podemos criar uma matriz utilizando Python:

```python
import numpy as np

imagem = np.array([
    [0, 50, 100, 150],
    [50, 100, 150, 200],
    [100, 150, 200, 255]
])

print(imagem)
```

A variável `imagem` contém números. Ainda não estamos, necessariamente, vendo uma imagem.

Para visualizá-la:

```python
import matplotlib.pyplot as plt

plt.imshow(imagem, cmap="gray", vmin=0, vmax=255)
plt.colorbar()
plt.show()
```

Aqui aparece uma ideia fundamental:

> Os dados armazenados e sua representação visual são conceitos relacionados, mas não idênticos.

A matriz é uma estrutura de dados. A visualização é uma forma de interpretar essa estrutura.

---

# 3. Resolução espacial

A **resolução espacial** está relacionada à quantidade de posições discretas utilizadas para representar uma imagem.

Por exemplo:

```text
640 × 480
```

significa:

* 640 posições horizontais;
* 480 posições verticais.

A quantidade total de pixels é:

$$
N = largura \times altura
$$

Para uma imagem de:

$$
640 \times 480
$$

temos:

$$
N = 640 \times 480 = 307200
$$

pixels.

Para uma imagem de:

$$
1920 \times 1080
$$

temos:

$$
N = 1920 \times 1080 = 2073600
$$

pixels.

Portanto, aumentar a resolução aumenta significativamente a quantidade de dados.

---

## 3.1 Mais resolução significa sempre melhor imagem?

Não necessariamente.

Uma resolução maior permite representar mais detalhes espaciais, mas a qualidade percebida também depende de outros fatores:

* qualidade da captura;
* profundidade de cor;
* ruído;
* compressão;
* qualidade do dispositivo de exibição;
* tamanho físico da tela;
* distância do observador.

> **⚠️ Atenção**
>
> Resolução é uma característica importante, mas não é sinônimo absoluto de qualidade visual.

---

# 4. Profundidade de cor

Além da quantidade de pixels, precisamos saber **quantos valores diferentes podem ser associados a cada pixel**.

Considere uma imagem binária.

Cada posição pode possuir apenas dois valores:

$$
I(x,y) \in {0,1}
$$

Por exemplo:

```text
0 = preto
1 = branco
```

Uma representação de 1 bit permite:

$$
2^1 = 2
$$

possibilidades.

Se utilizarmos 8 bits:

$$
2^8 = 256
$$

possibilidades.

De forma geral, para uma profundidade de (b) bits:

$$
L = 2^b
$$

em que:

* (L) é a quantidade de níveis possíveis;
* (b) é a quantidade de bits utilizada.

---

## 4.1 Exemplo com 8 bits

Uma imagem em tons de cinza de 8 bits normalmente utiliza valores entre:

$$
0 \leq I(x,y) \leq 255
$$

Assim, temos 256 níveis possíveis de intensidade.

Podemos criar uma escala simples:

```python
import numpy as np
import matplotlib.pyplot as plt

escala = np.linspace(0, 255, 256)

imagem = np.tile(escala, (50, 1))

plt.imshow(imagem, cmap="gray", vmin=0, vmax=255)
plt.axis("off")
plt.show()
```

O resultado é uma transição gradual entre intensidades.

---

# 5. Imagens coloridas

Uma imagem colorida pode ser representada utilizando múltiplos canais.

Um modelo muito conhecido é o **RGB**, formado por:

* **R — Red**: vermelho;
* **G — Green**: verde;
* **B — Blue**: azul.

Cada pixel pode possuir três valores:

$$
(R,G,B)
$$

Por exemplo:

$$
(255,0,0)
$$

representa, em uma convenção RGB de 8 bits por canal, vermelho intenso.

Outros exemplos:

$$
(0,255,0)
$$

verde intenso.

$$
(0,0,255)
$$

azul intenso.

$$
(255,255,255)
$$

branco.

$$
(0,0,0)
$$

preto.

---

## 5.1 Imagem RGB como arranjo tridimensional

Uma imagem colorida pode ser representada por um arranjo:

$$
altura \times largura \times canais
$$

Por exemplo:

```text
200 × 300 × 3
```

pode representar:

* 200 linhas;
* 300 colunas;
* 3 canais de cor.

Podemos criar uma pequena imagem artificial:

```python
import numpy as np
import matplotlib.pyplot as plt

imagem = np.zeros((200, 300, 3), dtype=np.uint8)

imagem[:, :100, 0] = 255
imagem[:, 100:200, 1] = 255
imagem[:, 200:, 2] = 255

plt.imshow(imagem)
plt.axis("off")
plt.show()
```

O código cria três regiões:

```text
+-------------+-------------+-------------+
|             |             |             |
|  VERMELHO   |    VERDE    |    AZUL     |
|             |             |             |
+-------------+-------------+-------------+
```

---

# 6. Canal alfa e transparência

Algumas imagens também possuem um quarto canal:

$$
(R,G,B,A)
$$

O componente (A) geralmente representa uma forma de controle associada à opacidade ou transparência, dependendo da convenção e do contexto de composição.

Uma representação RGBA pode possuir:

```text
altura × largura × 4
```

Por exemplo:

```text
R = 255
G = 0
B = 0
A = 128
```

pode representar uma cor vermelha com transparência parcial, considerando uma convenção de 8 bits.

É importante observar que a presença de um canal alfa não significa automaticamente que todos os sistemas utilizarão a mesma forma de composição.

Mais adiante, quando discutirmos renderização e composição de imagens, veremos esse conceito com maior precisão.

---

# 7. Amostragem

## 7.1 Intuição

Imagine uma fotografia como uma superfície visual contínua.

Para representá-la digitalmente, precisamos observar essa informação em posições discretas.

```text
Informação contínua

────────────────────────────

Amostragem

•   •   •   •   •   •   •   •

Representação discreta
```

No contexto de imagens, a amostragem espacial está relacionada à escolha de posições discretas para representar a informação visual.

Quanto menor for a quantidade de amostras disponíveis para representar determinados detalhes, maior pode ser a perda de informação.

---

## 7.2 Relação com resolução

Uma maneira simplificada de pensar é:

```text
Mais amostras espaciais
        ↓
Maior capacidade de representar detalhes
```

Entretanto, aumentar indefinidamente a quantidade de amostras possui custos:

* mais memória;
* mais armazenamento;
* maior quantidade de dados para transmissão;
* maior custo de processamento.

---

# 8. Quantização

Depois de determinar onde vamos realizar as amostras, precisamos representar seus valores utilizando uma quantidade finita de níveis.

Considere uma intensidade que, conceitualmente, poderia assumir qualquer valor real:

$$
x \in \mathbb{R}
$$

Um sistema digital precisa associar essa intensidade a um conjunto limitado de valores.

Por exemplo:

$$
{0,1,2,\ldots,255}
$$

Esse processo é chamado de **quantização**.

De maneira intuitiva:

```text
Valor contínuo

      127.43
         ↓

Quantização

         ↓

        127
```

A quantização introduz uma aproximação.

---

## 8.1 Exemplo

Considere uma imagem com valores normalizados entre 0 e 1:

```python
import numpy as np

imagem = np.array([
    [0.10, 0.25],
    [0.72, 0.95]
])

quantizada = (imagem * 255).astype(np.uint8)

print(quantizada)
```

Nesse exemplo, transformamos uma representação contínua aproximada em valores inteiros de 8 bits.

---

# 9. Amostragem e quantização não são a mesma coisa

Esses dois conceitos são frequentemente confundidos.

| Conceito    | Pergunta principal                 |
| ----------- | ---------------------------------- |
| Amostragem  | Onde ou quando vamos medir?        |
| Quantização | Quais valores podemos representar? |

Para uma imagem:

```text
Amostragem espacial
        ↓
Define posições dos pixels
```

Já:

```text
Quantização
        ↓
Define os valores possíveis em cada posição
```

Essa distinção será importante também no estudo de áudio e vídeo.

---

# 10. Operações básicas sobre imagens

Uma das vantagens de representar imagens como matrizes é que podemos realizar operações diretamente sobre seus valores.

Considere uma imagem em escala de cinza.

## 10.1 Negativo

Se os valores estiverem no intervalo:

$$
[0,255]
$$

podemos definir o negativo como:

$$
I'(x,y) = 255 - I(x,y)
$$

Em Python:

```python
def negativo(imagem):
    return 255 - imagem
```

Se:

$$
I(x,y)=0
$$

então:

$$
I'(x,y)=255
$$

Se:

$$
I(x,y)=255
$$

então:

$$
I'(x,y)=0
$$

---

## 10.2 Ajuste de brilho

Podemos adicionar uma constante:

$$
I'(x,y)=I(x,y)+b
$$

em que (b) representa uma alteração de intensidade.

Entretanto, devemos respeitar os limites possíveis.

```python
import numpy as np

def ajustar_brilho(imagem, valor):
    resultado = imagem.astype(np.int16) + valor
    resultado = np.clip(resultado, 0, 255)

    return resultado.astype(np.uint8)
```

### Por que converter temporariamente para `int16`?

Se trabalharmos diretamente com um tipo sem sinal de 8 bits, determinadas operações podem produzir resultados inesperados devido aos limites de representação.

Por isso:

1. realizamos o cálculo em uma representação adequada;
2. limitamos os valores;
3. retornamos para `uint8`.

---

## 10.3 Ajuste simples de contraste

Uma transformação linear pode ser representada como:

$$
I'(x,y)=aI(x,y)+b
$$

em que:

* (a) controla uma transformação multiplicativa;
* (b) adiciona um deslocamento.

Uma implementação simples:

```python
def ajustar_contraste(imagem, fator):
    resultado = imagem.astype(float) * fator

    resultado = np.clip(resultado, 0, 255)

    return resultado.astype(np.uint8)
```

Essa operação é uma simplificação didática. O conceito de contraste e suas transformações podem ser tratados de maneiras mais elaboradas.

---

# 11. Carregando uma imagem real

Agora vamos utilizar a biblioteca `Pillow`.

```python
from PIL import Image
import matplotlib.pyplot as plt

imagem = Image.open("imagem.jpg")

plt.imshow(imagem)
plt.axis("off")
plt.show()
```

Podemos converter a imagem para um arranjo NumPy:

```python
import numpy as np

dados = np.array(imagem)

print(dados.shape)
```

Se o resultado for:

```text
(600, 800, 3)
```

podemos interpretar como:

* 600 posições verticais;
* 800 posições horizontais;
* 3 canais.

---

## 11.1 Observando um pixel

Podemos selecionar uma posição:

```python
pixel = dados[100, 200]

print(pixel)
```

O resultado pode ser algo semelhante a:

```text
[120 85 42]
```

Esses valores representam os componentes RGB daquele pixel.

É importante observar que:

```python
dados[linha, coluna]
```

não segue necessariamente a mesma ordem intuitiva utilizada quando escrevemos coordenadas geométricas como:

$$
(x,y)
$$

Em arranjos, é comum trabalhar com:

```text
[linha, coluna]
```

Portanto:

```text
dados[y, x]
```

é uma interpretação frequentemente necessária ao relacionar uma imagem com coordenadas geométricas.

> **⚠️ Atenção**
>
> A convenção de índices de uma matriz e a convenção geométrica de coordenadas não devem ser confundidas.

---

# 12. Convertendo uma imagem para tons de cinza

Uma conversão para tons de cinza pode utilizar uma combinação dos canais de cor.

Conceitualmente:

$$
Y = w_RR + w_GG + w_BB
$$

em que:

* (R), (G) e (B) são os canais;
* (w_R), (w_G) e (w_B) são pesos definidos por uma determinada convenção ou modelo.

Para fins didáticos, podemos usar uma biblioteca:

```python
from PIL import Image

imagem = Image.open("imagem.jpg")

cinza = imagem.convert("L")

cinza.save("imagem_cinza.png")
```

Nesse caso, a biblioteca realiza uma implementação específica da conversão.

É importante distinguir:

```text
Conceito
    ↓
Conversão de uma representação colorida
para uma representação de intensidade
```

de:

```text
Implementação
    ↓
imagem.convert("L")
```

A chamada de função não é a definição matemática do conceito.

---

# 13. Um experimento completo em Python

O exemplo a seguir integra algumas operações básicas.

```python
import numpy as np
import matplotlib.pyplot as plt
from PIL import Image

imagem = Image.open("imagem.jpg").convert("L")

dados = np.array(imagem)

negativa = 255 - dados

brilho = np.clip(
    dados.astype(np.int16) + 50,
    0,
    255
).astype(np.uint8)

fig, axs = plt.subplots(1, 3)

axs[0].imshow(dados, cmap="gray")
axs[0].set_title("Original")
axs[0].axis("off")

axs[1].imshow(negativa, cmap="gray")
axs[1].set_title("Negativo")
axs[1].axis("off")

axs[2].imshow(brilho, cmap="gray")
axs[2].set_title("Brilho aumentado")
axs[2].axis("off")

plt.show()
```

O fluxo conceitual é:

```text
Arquivo
   ↓
Carregamento
   ↓
Representação em memória
   ↓
Matriz NumPy
   ↓
Transformação dos valores
   ↓
Nova matriz
   ↓
Visualização
```

Esse padrão aparecerá repetidamente na disciplina.

---

# 14. Estimativa do tamanho de uma imagem

Considere uma imagem RGB sem compressão, com:

$$
1920 \times 1080
$$

pixels.

Se cada pixel possui:

* 3 canais;
* 8 bits por canal;

então cada pixel utiliza:

$$
3 \times 8 = 24\text{ bits}
$$

Como existem:

$$
1920 \times 1080 = 2073600
$$

pixels, a quantidade total de bits é:

$$
2073600 \times 24 = 49766400\text{ bits}
$$

Convertendo para bytes:

$$
\frac{49766400}{8}=6220800\text{ bytes}
$$

Aproximadamente:

$$
6{,}22\text{ MB}
$$

considerando (1\text{ MB}=10^6) bytes.

Esse cálculo é uma estimativa da quantidade de dados dos pixels em uma representação RGB de 8 bits por canal, sem considerar detalhes adicionais de armazenamento ou compressão.

Essa simples análise permite compreender por que técnicas de:

* compressão;
* codificação;
* armazenamento eficiente;
* transmissão;

são tão importantes em sistemas multimídia.

---

# 15. Aplicações

## 15.1 Edição de imagens

Operações como:

* corte;
* redimensionamento;
* alteração de brilho;
* alteração de contraste;
* filtros;

podem ser interpretadas como transformações sobre a representação digital.

---

## 15.2 Visão computacional

Sistemas podem processar imagens para extrair:

* bordas;
* regiões;
* objetos;
* características.

Nesta disciplina, o foco inicial será a representação e o processamento básico, estabelecendo fundamentos para aplicações posteriores.

---

## 15.3 Interfaces

Imagens podem ser utilizadas como:

* ícones;
* botões;
* fundos;
* elementos informativos;
* feedback visual.

---

## 15.4 Sistemas multimídia

Imagens também fazem parte de:

* páginas web;
* vídeos;
* apresentações;
* sistemas educacionais;
* aplicações móveis.

---

# 16. Limitações e problemas

## Resolução insuficiente

Detalhes podem não ser adequadamente representados.

## Quantização

Uma quantidade limitada de níveis pode introduzir aproximações.

## Ruído

Os valores armazenados podem conter variações indesejadas.

## Armazenamento

Imagens de alta resolução podem exigir quantidades significativas de memória.

## Processamento

Operações aplicadas sobre todos os pixels podem se tornar custosas para imagens grandes.

Se uma operação percorre uma imagem de largura (W) e altura (H), uma análise simplificada pode resultar em:

$$
O(W \times H)
$$

operações.

Assim, dobrar largura e altura não apenas dobra necessariamente o número de pixels.

Por exemplo:

```text
100 × 100 = 10.000 pixels

200 × 200 = 40.000 pixels
```

Ao dobrar ambas as dimensões, quadruplicamos a quantidade de pixels.

---

# 17. Erros conceituais comuns

## Erro 1 — Pensar que pixel é sinônimo de resolução

Um pixel é uma unidade da imagem.

A resolução descreve a quantidade ou organização dessas unidades.

---

## Erro 2 — Confundir dimensão física e resolução

Uma tela fisicamente grande não é necessariamente uma tela de alta resolução.

---

## Erro 3 — Considerar RGB como a única representação possível de cor

RGB é apenas um dos modelos de representação utilizados em sistemas computacionais.

Outros modelos podem ser mais adequados para determinadas operações ou aplicações.

---

## Erro 4 — Confundir profundidade de cor com resolução

Resolução está relacionada à quantidade de posições espaciais.

Profundidade de cor está relacionada à quantidade de valores disponíveis para representar cada posição.

---

## Erro 5 — Alterar diretamente valores `uint8` sem considerar limites

Operações aritméticas podem produzir efeitos inesperados quando os valores ultrapassam os limites do tipo.

---

## Erro 6 — Confundir uma imagem exibida com os dados armazenados

A imagem observada depende do processo de visualização e do dispositivo. A matriz é uma representação computacional dos valores que serão interpretados.

---

# Síntese do capítulo

Uma imagem digital pode ser compreendida como uma representação discreta de informação visual.

Os principais conceitos estudados foram:

* **pixel:** unidade de representação visual;
* **resolução espacial:** quantidade de posições utilizadas para representar a imagem;
* **profundidade de cor:** quantidade de bits ou níveis disponíveis para representar valores;
* **canais:** componentes utilizados para representar informações, como as componentes RGB;
* **amostragem:** seleção de posições discretas;
* **quantização:** representação dos valores utilizando um conjunto finito de possibilidades.

Também vimos que uma imagem pode ser representada computacionalmente como:

$$
I \in \mathbb{R}^{H \times W}
$$

para uma representação bidimensional simplificada, ou como:

$$
I \in \mathbb{R}^{H \times W \times C}
$$

quando consideramos múltiplos canais.

Por fim, utilizamos Python para demonstrar que diversas operações de processamento podem ser compreendidas como transformações aplicadas diretamente aos valores que representam os pixels.

---

# Questões de revisão

1. O que é um pixel?

2. Qual é a diferença entre resolução espacial e profundidade de cor?

3. Como uma imagem em tons de cinza pode ser representada por uma matriz?

4. O que representam os três canais de uma imagem RGB?

5. Explique a diferença entre amostragem e quantização.

6. Por que uma imagem de maior resolução pode exigir mais memória?

7. Considere uma imagem de dimensão (1000 \times 1000). Quantos pixels ela possui?

8. Explique por que o código abaixo precisa considerar os limites de representação:

```python
imagem + 100
```

9. Por que uma imagem RGB pode ser representada como um arranjo tridimensional?

10. Qual é a diferença entre uma operação sobre a representação dos pixels e a forma como o resultado é exibido ao usuário?

---

# Exercícios

## Nível básico

### Exercício 1 — Matriz como imagem

Considere:

$$
I=
\begin{bmatrix}
0 & 255 \
255 & 0
\end{bmatrix}
$$

Desenhe uma representação visual aproximada da imagem em tons de cinza.

---

### Exercício 2 — Resolução

Calcule a quantidade total de pixels das seguintes imagens:

a. (800 \times 600)

b. (1280 \times 720)

c. (1920 \times 1080)

---

### Exercício 3 — Profundidade

Quantos valores diferentes podem ser representados utilizando:

a. 1 bit;

b. 2 bits;

c. 4 bits;

d. 8 bits?

---

## Nível intermediário

### Exercício 4 — Negativo

Implemente uma função:

```python
def negativo(imagem):
    ...
```

A função deve receber uma imagem em tons de cinza de 8 bits e retornar sua versão negativa.

Explique matematicamente a transformação utilizada.

---

### Exercício 5 — Brilho

Implemente:

```python
def ajustar_brilho(imagem, valor):
    ...
```

O programa deve:

* aumentar ou diminuir o brilho;
* impedir valores menores que 0;
* impedir valores maiores que 255.

Teste a função utilizando diferentes valores.

---

### Exercício 6 — RGB

Crie artificialmente uma imagem RGB contendo:

* uma região vermelha;
* uma região verde;
* uma região azul;
* uma região branca.

Utilize NumPy e Matplotlib.

---

## Nível avançado

### Exercício 7 — Comparação de representações

Considere duas imagens RGB:

* Imagem A: (640 \times 480);
* Imagem B: (1920 \times 1080).

Supondo 8 bits por canal e três canais, estime a quantidade de bytes necessária para representar cada imagem sem compressão.

Compare os resultados.

---

### Exercício 8 — Amostragem e armazenamento

Explique por que aumentar simultaneamente:

* largura;
* altura;
* profundidade de cor;

pode produzir um aumento significativo na quantidade de dados necessária para representar uma imagem.

---

## Exercício prático — Miniaplicação de processamento de imagens

Desenvolva uma aplicação em Python capaz de:

1. carregar uma imagem;
2. exibir a imagem original;
3. converter para tons de cinza;
4. produzir o negativo;
5. alterar o brilho;
6. salvar o resultado.

Como extensão, organize o programa separando:

```text
Interface
    ↓
Controle da aplicação
    ↓
Operações de processamento
    ↓
Entrada e saída de arquivos
```

Essa organização antecipa um princípio importante para os próximos capítulos: **os algoritmos de Computação Gráfica e Multimídia devem ser integrados a uma estrutura de software coerente**.