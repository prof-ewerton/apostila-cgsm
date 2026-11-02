# Parte 7 — Iluminação, Materiais e Texturas

## Capítulo 7 — Fundamentos de Iluminação e Aparência Visual

### 7.1 Introdução

Nos capítulos anteriores, estudamos como representar objetos por meio de pontos, arestas, faces e malhas poligonais, além de compreender como esses objetos são posicionados em uma cena tridimensional e projetados sobre uma superfície bidimensional. Entretanto, a representação geométrica de um objeto não é suficiente para produzir uma imagem visualmente convincente. Um modelo tridimensional pode possuir uma geometria perfeitamente definida e, ainda assim, parecer uma estrutura plana, sem volume ou sem características que permitam reconhecer seu material.

Para que um objeto seja apresentado de maneira visualmente compreensível, é necessário determinar como a luz interage com sua superfície. Uma esfera, por exemplo, pode ser representada apenas por uma malha de triângulos. Quando essa malha é desenhada sem iluminação, o observador percebe principalmente sua silhueta. Ao adicionar um modelo de iluminação, entretanto, diferentes regiões da superfície passam a apresentar intensidades distintas, produzindo a sensação de volume. Se também forem adicionadas propriedades de material e uma textura, a esfera poderá adquirir a aparência de uma bola de borracha, uma esfera metálica ou um planeta.

A iluminação, portanto, não é apenas um recurso estético. Ela fornece informações visuais importantes sobre a forma, a posição, a orientação e as características físicas aparentes dos objetos. Em aplicações de visualização científica, por exemplo, a iluminação pode facilitar a interpretação de superfícies complexas. Em jogos digitais, ela contribui para a atmosfera visual e para a percepção espacial. Em sistemas de realidade virtual, a coerência entre iluminação, geometria e materiais é importante para a sensação de presença no ambiente.

Neste capítulo, estudaremos os fundamentos matemáticos da iluminação computacional, os modelos de reflexão, as propriedades dos materiais e o uso de texturas. Também apresentaremos exemplos em Python para demonstrar como esses conceitos podem ser implementados de forma simplificada.

### 7.2 O que é iluminação computacional?

A iluminação computacional é o processo de estimar como a luz interage com os objetos de uma cena e como essa interação contribui para a cor e a intensidade dos pixels de uma imagem. Em uma cena real, a luz pode ser emitida por fontes luminosas, refletida por superfícies, absorvida por materiais e espalhada em diferentes direções. Simular todos esses fenômenos com precisão física pode ser computacionalmente caro.

Por essa razão, muitos sistemas gráficos utilizam modelos matemáticos simplificados. Esses modelos não precisam reproduzir todos os fenômenos físicos para gerar imagens úteis. Em determinadas aplicações, uma aproximação relativamente simples já é suficiente para produzir uma representação visual adequada. O modelo escolhido depende dos requisitos da aplicação, do hardware disponível e do nível de realismo desejado.

É importante distinguir iluminação de sombreamento. A iluminação corresponde ao modelo utilizado para estimar a interação da luz com uma superfície. O sombreamento, ou *shading*, é o processo de aplicar esse modelo aos pontos, vértices ou fragmentos de uma superfície durante a geração da imagem. Assim, um sistema pode utilizar o mesmo modelo de iluminação e produzir resultados diferentes dependendo da estratégia de sombreamento adotada.

Por exemplo, em uma malha poligonal, a iluminação pode ser calculada uma única vez para cada face, para cada vértice ou para cada fragmento gerado durante a rasterização. Essas abordagens apresentam diferenças de qualidade visual e custo computacional, que serão discutidas posteriormente.

### 7.3 Fontes de luz

Uma cena gráfica pode conter diferentes tipos de fontes luminosas. Cada tipo de fonte apresenta características próprias, que influenciam a maneira como a iluminação é calculada.

A luz direcional representa uma fonte muito distante, cujos raios podem ser considerados paralelos. Essa aproximação é frequentemente utilizada para representar a luz solar em cenas de escala relativamente pequena. Como a fonte é considerada distante, a direção da iluminação é aproximadamente igual em todos os pontos da cena, e sua intensidade não precisa diminuir com a distância entre o objeto e a fonte.

A luz pontual representa uma fonte localizada em uma posição específica do espaço, irradiando luz em várias direções. Uma lâmpada pode ser aproximada por esse tipo de fonte. Nesse caso, a direção da luz depende da posição do ponto iluminado, e a intensidade pode diminuir à medida que a distância aumenta.

A luz spot, ou luz cônica, é semelhante a uma fonte pontual, mas sua emissão é limitada a uma região angular. Esse tipo de iluminação é utilizado para representar lanternas, faróis e refletores. Além da posição e da intensidade, o modelo precisa considerar a direção principal da fonte e o ângulo de abertura do cone luminoso.

Também é comum utilizar uma componente de iluminação ambiente, que representa de forma simplificada a contribuição indireta da luz presente no ambiente. Essa componente não representa necessariamente uma fonte física específica. Ela é utilizada em modelos tradicionais para evitar que regiões não diretamente iluminadas se tornem completamente pretas.

### 7.4 Vetores utilizados nos modelos de iluminação

Os modelos de iluminação tradicionais utilizam vetores para representar a orientação da superfície e as direções relevantes da interação luminosa. Entre os vetores mais importantes estão o vetor normal, o vetor da luz e o vetor de observação.

O vetor normal representa a direção perpendicular à superfície em determinado ponto. Em uma face plana, a normal é constante. Em uma superfície curva, a normal pode variar continuamente de acordo com o ponto considerado. A orientação da normal é fundamental porque determina o ângulo entre a superfície e a luz incidente.

O vetor da luz representa a direção da fonte luminosa em relação ao ponto que está sendo iluminado. Em modelos simplificados, ele é frequentemente definido como um vetor unitário. O vetor de observação representa a direção entre o ponto da superfície e a câmera. Esse vetor é necessário para calcular componentes de reflexão que dependem da posição do observador, como o brilho especular.

Um vetor unitário possui comprimento igual a um. A normalização de um vetor é realizada dividindo cada uma de suas componentes pelo seu comprimento:

\[
\hat{v}=\frac{v}{\|v\|}
\]

O comprimento de um vetor tridimensional é calculado por:

\[
\|v\|=\sqrt{x^2+y^2+z^2}
\]

Em Python, podemos implementar essa operação com NumPy:

```python
import numpy as np

def normalizar(v):
    v = np.asarray(v, dtype=float)
    comprimento = np.linalg.norm(v)

    if comprimento == 0:
        raise ValueError("Não é possível normalizar um vetor nulo.")

    return v / comprimento
```

Essa função é útil em diversos pontos da Computação Gráfica. Antes de calcular produtos escalares entre vetores que representam direções, é comum normalizá-los para que os resultados dependam principalmente do ângulo entre eles, e não de seus comprimentos.

### 7.5 Produto escalar e orientação da superfície

O produto escalar entre dois vetores permite determinar a relação angular entre suas direções. Para vetores \(a\) e \(b\), temos:

\[
a\cdot b=a_xb_x+a_yb_y+a_zb_z
\]

Quando ambos são unitários, o produto escalar corresponde ao cosseno do ângulo entre eles:

\[
a\cdot b=\cos\theta
\]

Esse resultado é particularmente importante na iluminação. Quando a luz incide perpendicularmente sobre uma superfície, a intensidade da componente difusa tende a ser máxima. Quando a luz incide de maneira rasante, essa componente diminui.

Se a normal da superfície for \(N\) e a direção da luz for \(L\), podemos utilizar:

\[
N\cdot L
\]

para estimar a contribuição da luz difusa. Entretanto, se o produto escalar for negativo, significa que a luz está incidindo pelo lado oposto à orientação da normal. Em um modelo simples, essa contribuição deve ser considerada nula.

Por isso, utiliza-se frequentemente:

\[
\max(0,N\cdot L)
\]

Em Python:

```python
def contribuicao_difusa(normal, luz):
    normal = normalizar(normal)
    luz = normalizar(luz)

    return max(0.0, np.dot(normal, luz))
```

Essa função fornece um valor entre zero e um quando os vetores de entrada são normalizados. O valor representa a contribuição angular da luz difusa, antes da aplicação dos parâmetros do material e da intensidade da fonte.

### 7.6 Modelo de iluminação de Phong

O modelo de Phong é um modelo clássico de iluminação local. Ele combina três componentes: ambiente, difusa e especular. Embora não represente todos os fenômenos físicos da interação entre luz e matéria, é didaticamente importante porque permite compreender como diferentes características visuais são produzidas.

A componente ambiente representa uma iluminação geral simplificada. Sua função é evitar que regiões não diretamente iluminadas sejam completamente escuras. A componente difusa representa a reflexão da luz por uma superfície que espalha a energia luminosa em várias direções. Ela está relacionada à orientação da superfície em relação à fonte luminosa. A componente especular representa o brilho concentrado que aparece em determinadas direções, dependendo da posição da luz, da superfície e do observador.

Uma forma simplificada do modelo pode ser expressa como:

\[
I=I_a k_a+I_d k_d\max(0,N\cdot L)
+I_s k_s\max(0,R\cdot V)^n
\]

Nessa expressão:

- \(I_a\) representa a intensidade da componente ambiente;
- \(I_d\) representa a intensidade da componente difusa;
- \(I_s\) representa a intensidade da componente especular;
- \(k_a\), \(k_d\) e \(k_s\) são coeficientes associados ao material;
- \(N\) é a normal da superfície;
- \(L\) é a direção da luz;
- \(R\) é a direção de reflexão ideal;
- \(V\) é a direção de observação;
- \(n\) é o expoente especular.

O expoente \(n\) controla a concentração do brilho especular. Valores maiores produzem um destaque mais estreito e concentrado. Valores menores produzem um brilho mais amplo. Essa característica permite simular, de maneira aproximada, diferenças entre materiais mais polidos e materiais mais rugosos.

É importante observar que o modelo de Phong tradicional é um modelo empírico. Seus parâmetros não correspondem necessariamente a propriedades físicas rigorosas de um material real. Ainda assim, ele é útil para compreender os fundamentos da iluminação e para desenvolver aplicações didáticas.

### 7.7 Componente ambiente

A componente ambiente é a parte mais simples do modelo. Ela representa uma iluminação uniforme que contribui para a aparência geral do objeto, independentemente da orientação da superfície.

Em sua forma escalar:

\[
I_{ambiente}=I_a k_a
\]

Em uma implementação com cores RGB, os valores podem ser representados por vetores:

```python
cor_luz_ambiente = np.array([0.2, 0.2, 0.2])
coeficiente_ambiente = np.array([0.8, 0.4, 0.3])

componente_ambiente = (
    cor_luz_ambiente * coeficiente_ambiente
)
```

O resultado representa uma contribuição de cor para o objeto. Em um sistema mais completo, a intensidade ambiente pode ser combinada com outras componentes e limitada ao intervalo permitido pela representação de cor.

Embora seja útil em modelos didáticos, a iluminação ambiente uniforme não reproduz corretamente a iluminação indireta de uma cena real. Ela não considera, por exemplo, a cor de paredes próximas ou a distribuição espacial da luz refletida. Modelos mais avançados utilizam técnicas como iluminação global, oclusão ambiente e iluminação baseada em imagens para produzir resultados mais sofisticados.

### 7.8 Componente difusa

A componente difusa é frequentemente associada a materiais como madeira sem acabamento brilhante, papel, concreto e superfícies foscas. Nesse tipo de reflexão, a luz é espalhada em diversas direções, de modo que a aparência da superfície depende principalmente do ângulo de incidência da luz.

A expressão simplificada é:

\[
I_d=k_d I_L\max(0,N\cdot L)
\]

O coeficiente \(k_d\) representa a contribuição difusa do material, enquanto \(I_L\) representa a intensidade da luz incidente. Quando a normal está alinhada com a direção da luz, o produto escalar é próximo de um e a contribuição difusa é elevada. Quando a luz chega quase paralelamente à superfície, o produto escalar se aproxima de zero.

Podemos implementar a componente difusa em Python:

```python
def iluminacao_difusa(normal, luz, cor_material, intensidade_luz=1.0):
    normal = normalizar(normal)
    luz = normalizar(luz)

    fator = max(0.0, np.dot(normal, luz))

    return cor_material * intensidade_luz * fator
```

O parâmetro `cor_material` pode ser um vetor RGB, como:

```python
cor_material = np.array([0.8, 0.2, 0.1])
```

Nesse caso, o material apresenta uma contribuição predominantemente avermelhada. A intensidade final dependerá da orientação da superfície e da intensidade da luz.

### 7.9 Componente especular

A componente especular está relacionada à aparência brilhante de uma superfície. Em materiais polidos, como metais ou plásticos brilhantes, é possível observar regiões de destaque que mudam de posição conforme a câmera ou a fonte luminosa se movimenta.

No modelo de Phong, primeiro calculamos a direção de reflexão ideal \(R\). Em seguida, comparamos essa direção com a direção de observação \(V\). Quanto mais alinhadas estiverem essas direções, maior será a contribuição especular.

A expressão utilizada é:

\[
I_s=k_sI_L\max(0,R\cdot V)^n
\]

O expoente \(n\) determina a concentração do brilho. Um valor elevado produz uma região especular pequena e intensa. Um valor baixo produz uma região mais ampla.

A componente especular ajuda a distinguir visualmente materiais que possuem cores difusas semelhantes. Por exemplo, duas superfícies podem apresentar a mesma cor básica, mas uma delas pode ter um acabamento fosco e a outra um acabamento polido. A diferença entre seus destaques especulares contribui para que o observador reconheça essa distinção.

### 7.10 Implementação simplificada do modelo de Phong

A seguir, apresentamos uma implementação didática do modelo de Phong para um único ponto de uma superfície. A função recebe a normal, a direção da luz, a direção de observação e os parâmetros do material.

```python
import numpy as np

def normalizar(v):
    v = np.asarray(v, dtype=float)
    norma = np.linalg.norm(v)

    if norma == 0:
        raise ValueError("Vetor nulo.")

    return v / norma


def phong(
    normal,
    luz,
    observador,
    cor_ambiente,
    cor_difusa,
    cor_especular,
    intensidade_luz=1.0,
    expoente=32
):
    N = normalizar(normal)
    L = normalizar(luz)
    V = normalizar(observador)

    ambiente = np.asarray(cor_ambiente, dtype=float)

    difusa = (
        np.asarray(cor_difusa, dtype=float)
        * intensidade_luz
        * max(0.0, np.dot(N, L))
    )

    # Direção de reflexão da luz
    R = 2 * np.dot(N, L) * N - L
    R = normalizar(R)

    especular = (
        np.asarray(cor_especular, dtype=float)
        * intensidade_luz
        * max(0.0, np.dot(R, V)) ** expoente
    )

    resultado = ambiente + difusa + especular

    return np.clip(resultado, 0.0, 1.0)
```

Um teste simples:

```python
normal = np.array([0, 0, 1])
luz = np.array([0, 0, 1])
observador = np.array([0, 0, 1])

cor = phong(
    normal,
    luz,
    observador,
    cor_ambiente=[0.1, 0.1, 0.1],
    cor_difusa=[0.7, 0.2, 0.1],
    cor_especular=[0.5, 0.5, 0.5],
    expoente=32
)

print(cor)
```

Esse exemplo considera que os vetores estão definidos em um mesmo sistema de coordenadas. Em aplicações gráficas reais, é necessário garantir que as normais, a direção da luz e a direção de observação sejam expressas em sistemas compatíveis.

A função também utiliza uma simplificação importante: a iluminação é calculada para uma única fonte e para um único ponto. Um renderizador completo precisa considerar múltiplos pontos, múltiplas fontes, visibilidade, sombras, materiais e outros elementos da cena.

### 7.11 Materiais

Na Computação Gráfica, um material descreve como uma superfície responde à iluminação. Ele pode armazenar parâmetros como cor difusa, cor especular, intensidade ambiente e expoente especular. Em modelos mais modernos, também pode armazenar propriedades como rugosidade, metalicidade, transparência e características de refração.

A definição de um material permite separar a geometria da aparência visual. Um mesmo modelo geométrico pode ser apresentado com diferentes materiais sem que seus vértices ou suas faces precisem ser alterados. Um modelo de esfera, por exemplo, pode representar uma bola de plástico, uma superfície metálica ou uma esfera de vidro, dependendo dos parâmetros utilizados.

Essa separação é importante em aplicações de software porque favorece a reutilização. A geometria pode ser armazenada em uma estrutura de dados, enquanto o material é representado por outra estrutura. O sistema de renderização utiliza ambas para determinar a aparência final do objeto.

Uma estrutura simples em Python pode ser definida assim:

```python
from dataclasses import dataclass
import numpy as np

@dataclass
class Material:
    cor_ambiente: np.ndarray
    cor_difusa: np.ndarray
    cor_especular: np.ndarray
    expoente_especular: float = 32.0
```

Um material pode ser instanciado da seguinte maneira:

```python
material_plastico = Material(
    cor_ambiente=np.array([0.1, 0.1, 0.1]),
    cor_difusa=np.array([0.2, 0.5, 0.8]),
    cor_especular=np.array([0.5, 0.5, 0.5]),
    expoente_especular=64
)
```

A estrutura não realiza a renderização por conta própria. Ela apenas organiza os parâmetros necessários para que outro componente do sistema calcule a aparência do objeto.

### 7.12 Modelos de iluminação e modelos de materiais

O modelo de Phong é um modelo clássico e didático, mas sistemas gráficos modernos frequentemente utilizam abordagens conhecidas como *Physically Based Rendering* (PBR), ou renderização baseada em princípios físicos.

O PBR procura representar a interação entre luz e materiais de maneira mais consistente com princípios físicos. Em vez de depender apenas de coeficientes empíricos, esses modelos utilizam parâmetros que possuem interpretações mais próximas das propriedades dos materiais, como rugosidade e metalicidade.

A rugosidade está relacionada à distribuição microscópica das irregularidades da superfície. Uma superfície muito lisa tende a produzir reflexões mais concentradas, enquanto uma superfície rugosa espalha a reflexão em uma região mais ampla. A metalicidade representa uma aproximação da distinção entre materiais metálicos e não metálicos.

Isso não significa que o PBR seja sempre fisicamente perfeito. Ele também utiliza aproximações e depende de parâmetros e condições de iluminação adequados. Entretanto, permite obter resultados consistentes entre diferentes condições de luz e facilita a criação de materiais reutilizáveis.

Em uma disciplina introdutória, o modelo de Phong é adequado para compreender os fundamentos. O PBR pode ser apresentado como uma evolução conceitual, especialmente quando a disciplina utilizar bibliotecas ou motores gráficos modernos.

### 7.13 Texturas

Uma textura é uma representação de dados que pode ser aplicada à superfície de um objeto. Embora a textura mais conhecida seja uma imagem colorida, ela não precisa armazenar apenas cor. Texturas também podem representar rugosidade, normais, transparência, altura e outras propriedades utilizadas durante a renderização.

O mapeamento de textura consiste em estabelecer uma correspondência entre pontos de uma superfície tridimensional e posições em uma imagem. Essa correspondência permite que uma imagem seja aplicada sobre uma malha sem que seja necessário aumentar a quantidade de polígonos.

Por exemplo, uma parede pode ser representada por uma malha relativamente simples. Em vez de modelar individualmente cada tijolo, podemos aplicar uma textura que contém a imagem de uma parede de tijolos. A geometria permanece simples, mas a superfície passa a apresentar uma aparência visual mais detalhada.

Essa técnica é amplamente utilizada em jogos, simulações, visualização arquitetônica e aplicações multimídia. Ela permite aumentar o detalhamento visual sem exigir que toda a informação seja armazenada na geometria.

### 7.14 Coordenadas UV

Para aplicar uma textura sobre uma superfície, normalmente utilizamos coordenadas UV. Essas coordenadas representam posições em uma imagem bidimensional.

Enquanto as coordenadas de um vértice no mundo podem ser representadas por:

\[
(x,y,z)
\]

as coordenadas de textura são representadas por:

\[
(u,v)
\]

Em muitas convenções, \(u\) representa a direção horizontal da textura e \(v\) representa a direção vertical. O intervalo usual é:

\[
0\leq u\leq1
\]

\[
0\leq v\leq1
\]

Assim, a posição \((0,0)\) representa um canto da textura, enquanto \((1,1)\) representa o canto oposto. A orientação exata do eixo vertical depende da convenção adotada pela biblioteca ou pelo formato utilizado.

As coordenadas UV não precisam corresponder diretamente às coordenadas do mundo. Elas são definidas de acordo com a maneira como a textura será aplicada à superfície. Um mesmo objeto pode possuir diferentes conjuntos de coordenadas UV para diferentes texturas.

A definição de UVs é conhecida como *UV mapping*. Em objetos simples, como um plano, essa operação é relativamente direta. Em objetos complexos, como personagens ou modelos orgânicos, pode ser necessário realizar um processo de abertura da superfície, conhecido como *UV unwrapping*. Esse processo busca transformar partes da superfície tridimensional em regiões bidimensionais, minimizando distorções e sobreposições indesejadas.

### 7.15 Amostragem de uma textura

Quando uma textura é aplicada a uma superfície, o renderizador precisa determinar qual valor da imagem corresponde a cada ponto visível. Esse processo é chamado de amostragem.

Considere uma textura representada por uma matriz de pixels. Se suas dimensões forem \(W\) e \(H\), uma coordenada UV pode ser convertida em uma posição aproximada da imagem:

\[
x=u(W-1)
\]

\[
y=v(H-1)
\]

O resultado pode não ser um número inteiro. Por isso, é necessário utilizar uma técnica de filtragem para estimar o valor da textura entre os pixels existentes.

A forma mais simples é a filtragem pelo vizinho mais próximo. Ela seleciona o pixel mais próximo da posição solicitada. Essa técnica é rápida, mas pode produzir resultados visualmente irregulares quando a textura é ampliada.

Outra técnica é a interpolação bilinear. Ela utiliza os quatro pixels mais próximos para calcular uma cor intermediária. Esse procedimento tende a produzir uma aparência mais suave, embora não resolva todos os problemas de amostragem.

### 7.16 Exemplo de textura com NumPy

Podemos criar uma textura simples utilizando uma matriz NumPy. O exemplo seguinte produz um padrão quadriculado:

```python
import numpy as np
import matplotlib.pyplot as plt

largura = 256
altura = 256
tamanho_celula = 32

textura = np.zeros(
    (altura, largura, 3),
    dtype=np.uint8
)

for y in range(altura):
    for x in range(largura):
        celula_x = x // tamanho_celula
        celula_y = y // tamanho_celula

        if (celula_x + celula_y) % 2 == 0:
            textura[y, x] = [240, 240, 240]
        else:
            textura[y, x] = [40, 40, 40]

plt.imshow(textura)
plt.axis("off")
plt.show()
```

A matriz possui três canais de cor, correspondentes a vermelho, verde e azul. Cada posição da matriz representa um pixel. O padrão é construído a partir da divisão da imagem em células, alternando entre duas cores.

Esse exemplo demonstra que uma textura é, essencialmente, uma estrutura de dados que armazena valores associados a posições bidimensionais. Em uma aplicação gráfica, esses valores seriam consultados durante o processo de renderização.

### 7.17 Aplicando textura a um plano

Para compreender o mapeamento UV, considere um plano formado por quatro vértices. Cada vértice pode receber uma coordenada UV correspondente a um canto da textura.

| Vértice | Coordenada 3D | Coordenada UV |
|---|---|---|
| A | \((0,0,0)\) | \((0,0)\) |
| B | \((1,0,0)\) | \((1,0)\) |
| C | \((1,1,0)\) | \((1,1)\) |
| D | \((0,1,0)\) | \((0,1)\) |

Dessa maneira, a textura inteira é mapeada sobre o plano. Se as coordenadas UV forem alteradas, a textura poderá ser ampliada, reduzida, deslocada ou repetida, dependendo das regras de amostragem utilizadas.

Em um motor gráfico, a associação entre vértices e coordenadas UV costuma ser armazenada junto à malha. Durante a renderização, as coordenadas de textura são interpoladas ao longo dos triângulos, permitindo consultar a textura em cada fragmento produzido pela rasterização.

### 7.18 Filtragem de texturas

A filtragem de texturas é necessária porque a resolução da imagem utilizada como textura nem sempre coincide com a resolução da região ocupada pelo objeto na tela. Quando uma textura é ampliada, o sistema precisa estimar valores entre os pixels existentes. Quando é reduzida, vários pixels da textura podem contribuir para um único pixel da imagem final.

A filtragem pelo vizinho mais próximo seleciona o valor de um único pixel. É uma técnica simples e eficiente, adequada para determinadas aplicações, como imagens pixel art. Entretanto, em texturas fotográficas, pode produzir bordas irregulares e padrões visivelmente fragmentados.

A filtragem bilinear combina os valores dos quatro pixels mais próximos. Essa combinação reduz descontinuidades e produz transições mais suaves. Ainda assim, quando uma textura é reduzida significativamente, a filtragem bilinear isolada pode não ser suficiente para evitar artefatos.

Para lidar com diferentes escalas de observação, sistemas gráficos frequentemente utilizam mipmaps. Um mipmap é uma sequência de versões de uma textura em resoluções progressivamente menores. Quando o objeto ocupa uma região pequena da tela, o renderizador pode selecionar uma versão reduzida da textura, diminuindo o custo de amostragem e reduzindo problemas de aliasing.

### 7.19 Texturas e desempenho

O uso de texturas envolve uma relação entre qualidade visual, consumo de memória e desempenho. Uma textura de alta resolução pode apresentar mais detalhes, mas também ocupa mais memória e pode aumentar a quantidade de dados transferidos entre diferentes níveis da hierarquia de memória do sistema.

Em aplicações interativas, é importante considerar a resolução das texturas, a quantidade de materiais distintos, o uso de mipmaps e a frequência com que os recursos são carregados. Uma aplicação que utiliza muitas texturas de grande dimensão pode apresentar problemas de desempenho ou consumir mais memória gráfica do que o necessário.

A organização dos recursos também é uma questão de Engenharia de Software. Texturas, materiais e modelos devem possuir uma estratégia de armazenamento e carregamento que permita reutilização e gerenciamento eficiente. Em projetos maiores, é comum utilizar sistemas de recursos que controlam o ciclo de vida desses elementos, evitando carregamentos duplicados e facilitando a manutenção.

### 7.20 Integração entre geometria, iluminação, materiais e texturas

A aparência final de um objeto é resultado da combinação de diferentes informações. A geometria determina a forma e a posição da superfície. As normais descrevem sua orientação local. A iluminação define as condições de interação com as fontes luminosas. O material fornece parâmetros de reflexão. A textura acrescenta informação espacial sobre a superfície.

Esses elementos são complementares. Uma textura pode apresentar uma imagem de tijolos, mas não substitui necessariamente a geometria de uma parede. A iluminação pode produzir sombras e destaques, mas não determina sozinha a forma do objeto. O material pode definir uma superfície brilhante, mas a posição da câmera e da luz influencia a localização do brilho.

Essa separação conceitual ajuda a compreender a arquitetura de um renderizador. Em uma implementação modular, a geometria pode ser mantida em um componente, os materiais em outro e as fontes luminosas em uma estrutura de cena. O renderizador recebe essas informações e calcula a imagem de acordo com a câmera e as técnicas de iluminação utilizadas.

### 7.21 Exercícios de fixação

**Exercício 1 — Vetores e iluminação**

Implemente uma função que receba uma normal e uma direção de luz e retorne o valor de \(\max(0,N\cdot L)\). Teste diferentes orientações da superfície e explique como o resultado muda quando a luz passa de uma incidência perpendicular para uma incidência rasante.

**Exercício 2 — Modelo de Phong**

Implemente as componentes ambiente, difusa e especular separadamente. Depois, combine-as em uma única função. Modifique o expoente especular e compare os resultados obtidos.

**Exercício 3 — Materiais**

Crie três materiais com a mesma cor difusa, mas com diferentes coeficientes especulares. Explique como a aparência dos objetos muda quando os coeficientes e o expoente especular são modificados.

**Exercício 4 — Textura quadriculada**

Crie uma textura com duas cores e aplique-a a um plano. Modifique a quantidade de células e observe como o padrão se altera.

**Exercício 5 — Coordenadas UV**

Implemente uma função que converta coordenadas UV para coordenadas de uma imagem. Teste valores dentro e fora do intervalo de zero a um e discuta como o sistema deve tratar coordenadas externas.

**Exercício 6 — Projeto integrado**

Desenvolva uma aplicação que represente um objeto 3D com uma malha, uma câmera, uma fonte de luz, um material e uma textura. A aplicação deverá permitir modificar a posição da luz e observar a mudança na aparência do objeto.

### 7.22 Projeto prático — Visualizador de materiais

O objetivo deste projeto é construir uma aplicação didática que permita comparar diferentes materiais sob condições de iluminação controladas. O sistema deverá apresentar um objeto tridimensional, como uma esfera ou um cubo, e permitir a seleção de diferentes parâmetros de material.

O usuário deverá ser capaz de modificar a cor difusa, a intensidade especular e o expoente especular. Também deverá ser possível alterar a direção da luz e observar como a aparência do objeto se modifica. Em uma etapa posterior, o projeto poderá incorporar texturas e coordenadas UV, permitindo comparar objetos com diferentes padrões superficiais.

A implementação deverá separar as responsabilidades de geometria, câmera, iluminação, material e renderização. Essa organização torna o sistema mais fácil de testar e ampliar. Por exemplo, a implementação de um novo material não deverá exigir que a estrutura geométrica do objeto seja modificada.

### 7.23 Perguntas de revisão

1. Qual é a função da iluminação computacional?
2. Qual é a diferença entre iluminação e sombreamento?
3. O que é um vetor normal?
4. Por que os vetores utilizados nos cálculos de iluminação frequentemente são normalizados?
5. Qual é a função do produto escalar entre a normal e a direção da luz?
6. Quais são as três componentes tradicionais do modelo de Phong?
7. Como o expoente especular influencia a aparência de um material?
8. O que é um material em Computação Gráfica?
9. O que é uma textura?
10. Qual é a função das coordenadas UV?
11. Qual é a diferença entre filtragem pelo vizinho mais próximo e filtragem bilinear?
12. O que são mipmaps?
13. Como a resolução das texturas pode influenciar o desempenho de uma aplicação?
14. Por que é importante separar geometria, material e iluminação na arquitetura de um sistema gráfico?

### 7.24 Síntese

Neste capítulo, estudamos os fundamentos utilizados para determinar a aparência visual de objetos tridimensionais. A iluminação permite estimar a interação da luz com as superfícies, enquanto os materiais definem parâmetros que influenciam essa interação. As texturas acrescentam informação espacial que pode representar cores, padrões e outras propriedades superficiais.

O modelo de Phong foi apresentado como uma abordagem clássica para compreender a iluminação local, combinando componentes ambiente, difusa e especular. Também estudamos os vetores envolvidos nos cálculos, o produto escalar e a importância da normalização.

Em relação às texturas, foram introduzidos os conceitos de mapeamento UV, amostragem, filtragem e mipmaps. Esses elementos são importantes para produzir imagens com maior detalhamento visual sem exigir que toda a informação seja representada diretamente na geometria.

A ideia central deste capítulo é que a aparência de um objeto não depende exclusivamente de sua forma. Ela resulta da combinação entre geometria, orientação das superfícies, iluminação, propriedades do material, textura e condições de visualização.

Essa compreensão prepara o estudante para estudar técnicas de renderização mais completas e, posteriormente, integrar iluminação e materiais em aplicações gráficas interativas.