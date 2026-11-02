
# Capítulo 13 — Fundamentos de Animação Computacional

## 13.1 Objetivos de aprendizagem

A animação computacional é uma área da Computação Gráfica dedicada à representação e à geração de movimento por meio de recursos computacionais. Seu desenvolvimento envolve conceitos matemáticos, algoritmos, representação temporal e técnicas de apresentação visual. Em aplicações interativas, a animação também precisa considerar a entrada do usuário, o desempenho do sistema e a atualização contínua dos elementos gráficos.

Ao estudar este capítulo, o estudante deverá compreender como o movimento pode ser representado por uma sequência de estados de um objeto ao longo do tempo. Deverá reconhecer a relação entre quadros (*frames*), tempo discreto e taxa de quadros (*frame rate*), além de compreender como os *keyframes* e a interpolação permitem construir movimentos intermediários.

Espera-se também que o estudante seja capaz de implementar uma animação simples em Python, calcular a posição de um objeto em diferentes instantes, aplicar interpolação linear e compreender como transformações geométricas podem ser animadas. Por fim, deverá reconhecer os fundamentos da animação procedural e identificar situações em que essa abordagem pode ser utilizada.

A sequência didática seguirá a proposta geral da disciplina: intuição visual, conceito formal, matemática essencial, algoritmo, experimentação em Python e aplicação em Engenharia de Software. O objetivo não é apenas utilizar uma biblioteca para exibir movimento, mas compreender como os estados temporais de um objeto são calculados e apresentados.

## 13.2 Introdução e motivação

Uma imagem estática representa uma cena em um determinado instante. Uma animação, por outro lado, apresenta uma sequência de imagens ou estados visuais que, quando exibidos ao longo do tempo, produzem a percepção de movimento. Essa percepção é explorada em jogos digitais, filmes, simulações, interfaces gráficas, visualizações científicas e aplicações educacionais.

Em um jogo, por exemplo, um personagem pode caminhar, saltar ou alterar sua orientação. Em uma interface, um botão pode expandir suavemente, um painel pode deslizar para a tela ou um indicador pode representar o progresso de uma operação. Em uma simulação física, um objeto pode cair, colidir com uma superfície ou seguir uma trajetória. Embora essas aplicações apresentem objetivos distintos, todas precisam representar como o estado visual de um elemento varia ao longo do tempo.

A animação computacional não consiste necessariamente em armazenar todas as imagens que serão exibidas. Em muitas aplicações, o sistema mantém uma representação do objeto e calcula sua posição, orientação, escala ou outras propriedades a cada atualização. A imagem correspondente ao estado atual é então produzida pelo processo de renderização.

Essa abordagem é especialmente importante em aplicações interativas. Um jogo não pode depender de uma sequência de imagens previamente preparada para cada possível movimento do usuário. O sistema precisa calcular os estados de maneira dinâmica, considerando o tempo decorrido e os eventos recebidos.

A construção de uma animação envolve, portanto, duas atividades relacionadas, mas distintas. A primeira é a atualização do estado temporal e geométrico dos objetos. A segunda é a apresentação visual desse estado. Separar essas atividades permite organizar melhor o código e compreender problemas como movimentos que variam de velocidade, animações que dependem do desempenho do computador e atualizações visuais inconsistentes.

Neste capítulo, o movimento será tratado inicialmente como uma função do tempo. Em seguida, serão estudadas formas de definir estados importantes e calcular os estados intermediários. Essa abordagem fornece a base para compreender animações mais elaboradas, incluindo movimentos hierárquicos, animação procedural e sistemas interativos.

## 13.3 Pré-requisitos

O conteúdo pressupõe conhecimentos básicos de programação em Python, especialmente variáveis, funções, estruturas de repetição e operações aritméticas. Também são importantes os conceitos estudados nos capítulos anteriores sobre sistemas de coordenadas, vetores, matrizes e transformações geométricas.

A animação utiliza esses conceitos porque o movimento pode ser representado pela alteração das coordenadas de um objeto. Uma translação animada modifica sua posição; uma rotação animada modifica sua orientação; uma escala animada altera suas dimensões. Assim, a animação pode ser compreendida como a aplicação de transformações geométricas que variam ao longo do tempo.

Os exemplos utilizarão NumPy para cálculos numéricos e Matplotlib para visualização. Em uma aplicação interativa mais completa, bibliotecas como Pygame podem ser utilizadas para gerenciar janelas, eventos, atualização de estados e renderização em tempo real.

## 13.4 Conceitos fundamentais da animação computacional

### 13.4.1 Estado de um objeto

Para animar um objeto, é necessário definir quais propriedades descrevem seu estado. Em uma representação bidimensional simples, o estado pode incluir a posição horizontal e vertical:

\[
S(t)=(x(t),y(t))
\]

Nesse caso, \(t\) representa o tempo, enquanto \(x(t)\) e \(y(t)\) indicam as coordenadas do objeto em cada instante. A função temporal determina como a posição varia.

Em uma aplicação mais completa, o estado pode incluir outras propriedades, como orientação, escala, cor, transparência ou velocidade. Um objeto tridimensional, por exemplo, pode ser representado por uma posição, uma orientação e uma escala. Cada uma dessas propriedades pode variar independentemente ou estar relacionada às demais.

Essa representação permite compreender que a animação não é necessariamente uma sequência de imagens completas. Ela pode ser definida por funções que descrevem a evolução das propriedades do objeto. A cada instante, o sistema calcula o estado e utiliza esse resultado para gerar a imagem.

### 13.4.2 Tempo discreto

O tempo em um fenômeno físico é frequentemente tratado como contínuo. Uma animação computacional, entretanto, costuma ser apresentada por meio de atualizações discretas. Isso significa que o sistema calcula e exibe estados em instantes específicos.

Podemos representar esses instantes por:

\[
t_0,t_1,t_2,\ldots,t_n
\]

Cada instante corresponde a uma atualização ou quadro da animação. Se o intervalo entre quadros for constante, teremos:

\[
t_n=t_0+n\Delta t
\]

em que \(\Delta t\) representa o intervalo temporal entre duas atualizações consecutivas.

Essa discretização é necessária porque o computador não apresenta uma quantidade infinita de imagens por segundo. A animação é construída por uma sequência finita de quadros, e a continuidade percebida depende da frequência com que esses quadros são apresentados e das características do movimento.

A escolha do intervalo temporal influencia diretamente a qualidade visual. Se os quadros forem muito espaçados, o movimento poderá parecer irregular ou apresentar saltos. Se forem atualizados com maior frequência, a trajetória poderá ser percebida de maneira mais suave, embora isso também dependa da complexidade do movimento e da capacidade de renderização.

### 13.4.3 Quadros (*frames*)

Um quadro é uma imagem correspondente a um instante da animação. Em uma animação simples, cada quadro apresenta o objeto em uma posição diferente. Em uma animação mais complexa, também podem variar a orientação, a forma, a iluminação ou outras propriedades visuais.

Uma sequência de quadros pode ser representada conceitualmente como:

\[
F_0,F_1,F_2,\ldots,F_n
\]

Cada \(F_i\) corresponde à imagem gerada a partir do estado do sistema no instante \(t_i\).

É importante distinguir o quadro da atualização lógica do objeto. Um sistema pode atualizar o estado várias vezes antes de apresentar uma imagem, ou pode apresentar uma imagem sem que todas as propriedades do objeto tenham sido alteradas. Essa distinção é relevante em sistemas gráficos que separam simulação, atualização e renderização.

Em uma animação tradicional, os quadros podem ser desenhados manualmente. Em uma animação computacional, muitos deles são calculados automaticamente a partir de regras, funções ou estados previamente definidos.

### 13.4.4 Taxa de quadros (*frame rate*)

A taxa de quadros indica quantos quadros são apresentados por unidade de tempo, normalmente em quadros por segundo, ou FPS (*frames per second*).

Se uma animação apresenta \(N\) quadros durante um intervalo de \(T\) segundos, sua taxa média de quadros é:

\[
FPS=\frac{N}{T}
\]

Uma animação de 60 FPS apresenta aproximadamente 60 quadros por segundo. Isso corresponde a um intervalo médio entre quadros de:

\[
\Delta t=\frac{1}{60}\approx0{,}0167\text{ s}
\]

Ou seja, aproximadamente 16,7 milissegundos entre apresentações consecutivas.

A taxa de quadros influencia a percepção de suavidade, mas não deve ser confundida com a velocidade do objeto. Um objeto pode atravessar a tela em um segundo tanto em uma animação de 30 FPS quanto em uma de 60 FPS. A diferença está na quantidade de estados apresentados durante esse segundo, e não necessariamente na duração do movimento.

Essa distinção é fundamental para a implementação de animações. Se o programa mover um objeto por uma distância fixa a cada quadro, a velocidade dependerá da taxa de quadros efetivamente alcançada. Em computadores diferentes, o mesmo código poderá produzir movimentos com velocidades distintas.

Uma implementação mais robusta considera o tempo decorrido entre atualizações. Dessa forma, a posição é calculada em função do tempo, e não apenas da quantidade de quadros processados.

## 13.5 Representação matemática do movimento

O movimento pode ser descrito por uma função que relaciona uma propriedade do objeto ao tempo. Para uma posição unidimensional, temos:

\[
x=x(t)
\]

Em duas dimensões:

\[
P(t)=(x(t),y(t))
\]

Em três dimensões:

\[
P(t)=(x(t),y(t),z(t))
\]

Essa representação permite determinar onde o objeto estará em um instante específico. A função pode ser simples, como uma expressão linear, ou mais complexa, como uma função trigonométrica ou uma sequência de segmentos interpolados.

### 13.5.1 Movimento uniforme

No movimento uniforme, a posição varia linearmente com o tempo. A velocidade é constante e a aceleração é nula.

A posição em uma dimensão pode ser calculada por:

\[
x(t)=x_0+vt
\]

em que:

- \(x_0\) é a posição inicial;
- \(v\) é a velocidade constante;
- \(t\) é o tempo decorrido.

Se um objeto parte de \(x_0=2\) e se desloca com velocidade \(v=3\) unidades por segundo, sua posição após dois segundos será:

\[
x(2)=2+3\cdot2=8
\]

O objeto percorreu seis unidades durante o intervalo.

Em duas dimensões, a posição pode ser calculada utilizando um vetor de velocidade:

\[
P(t)=P_0+\vec{v}t
\]

Essa expressão é útil para animações de movimento retilíneo, como um objeto que atravessa uma tela ou um elemento que se desloca em uma direção constante.

### 13.5.2 Velocidade e aceleração

A velocidade descreve como a posição varia em relação ao tempo. Em uma formulação contínua, a velocidade é a derivada da posição:

\[
v(t)=\frac{dx(t)}{dt}
\]

A aceleração descreve como a velocidade varia:

\[
a(t)=\frac{dv(t)}{dt}
\]

Em uma animação computacional, essas grandezas podem ser aproximadas por diferenças entre estados consecutivos. Se duas posições forem conhecidas em instantes separados por \(\Delta t\), uma aproximação da velocidade média será:

\[
v\approx\frac{x_{n+1}-x_n}{\Delta t}
\]

Essa aproximação é útil em simulações e animações que atualizam seus estados de maneira discreta.

A distinção entre posição, velocidade e aceleração permite construir movimentos mais expressivos. Um objeto pode começar parado, acelerar, atingir uma velocidade máxima e depois desacelerar. Esse comportamento é diferente de um movimento uniforme, mesmo que ambos percorram a mesma distância total.

## 13.6 *Keyframes*: definição de estados importantes

Os *keyframes*, ou quadros-chave, são instantes nos quais o animador define explicitamente o estado de um objeto. Em vez de especificar cada quadro da animação, são definidos alguns estados importantes, e o sistema calcula os estados intermediários.

Considere um objeto que começa na posição \(A\) e termina na posição \(B\). O primeiro *keyframe* define a posição inicial, enquanto o segundo define a posição final. A animação precisa determinar como o objeto se deslocará entre esses dois estados.

Os *keyframes* podem representar posições, rotações, escalas, cores ou outras propriedades. Em uma animação de personagem, por exemplo, um quadro-chave pode representar a pose inicial de um braço e outro a pose final. Em uma interface gráfica, podem definir a largura inicial e final de um painel.

A utilização de quadros-chave reduz o trabalho de especificação e facilita a edição. Se o animador desejar alterar a duração de uma animação ou modificar sua posição final, poderá ajustar os estados principais sem precisar redesenhar todos os quadros intermediários.

Entretanto, os *keyframes* não determinam necessariamente como o movimento ocorre entre eles. Essa tarefa é realizada por uma técnica de interpolação ou por uma regra de animação. Diferentes técnicas podem produzir movimentos visualmente distintos mesmo quando utilizam os mesmos estados inicial e final.

## 13.7 Interpolação

A interpolação é o processo de estimar valores intermediários a partir de valores conhecidos. Em animação, ela permite calcular estados entre dois ou mais *keyframes*.

Considere uma propriedade que assume o valor \(A\) no início de um intervalo e o valor \(B\) no final. Para um instante intermediário, podemos calcular um valor entre \(A\) e \(B\) utilizando um parâmetro \(u\), normalmente definido no intervalo de zero a um.

Quando \(u=0\), o resultado corresponde ao estado inicial. Quando \(u=1\), corresponde ao estado final. Valores intermediários determinam a posição entre esses dois estados.

### 13.7.1 Interpolação linear

A interpolação linear, também chamada de *Linear Interpolation* ou LERP, calcula o valor intermediário por meio de uma combinação linear dos extremos:

\[
L(A,B,u)=A+(B-A)u
\]

O parâmetro \(u\) representa o progresso da animação. Se \(u=0{,}5\), o resultado estará exatamente no meio do intervalo entre \(A\) e \(B\).

Para uma posição bidimensional, a mesma operação pode ser aplicada a cada componente:

\[
P(u)=P_0+(P_1-P_0)u
\]

Considere um objeto que se desloca do ponto \(P_0=(2,1)\) até \(P_1=(8,5)\). Para \(u=0{,}5\), temos:

\[
P(0{,}5)=(2,1)+((8,5)-(2,1))0{,}5
\]

\[
P(0{,}5)=(2,1)+(6,4)0{,}5
\]

\[
P(0{,}5)=(5,3)
\]

O objeto estará no ponto \((5,3)\), que corresponde ao ponto médio da trajetória.

A interpolação linear produz uma variação constante da propriedade em relação ao parâmetro \(u\). Quando \(u\) cresce linearmente com o tempo, o objeto percorre o segmento com velocidade constante. Essa característica torna a LERP simples e previsível, mas pode produzir movimentos visualmente rígidos quando utilizada sem ajustes.

### 13.7.2 Interpolação temporal

Para utilizar a interpolação linear em uma animação, é necessário converter o tempo decorrido em um parâmetro \(u\).

Se a animação começa em \(t_0\) e termina em \(t_1\), podemos calcular:

\[
u=\frac{t-t_0}{t_1-t_0}
\]

Essa expressão considera que o tempo \(t\) está entre \(t_0\) e \(t_1\). Para evitar que o parâmetro ultrapasse os limites, é comum restringi-lo ao intervalo:

\[
u=\min(1,\max(0,u))
\]

Esse procedimento é conhecido como *clamping*. Ele garante que, antes do início da animação, o valor permaneça no estado inicial e, após o término, permaneça no estado final.

A interpolação temporal separa a duração da animação da quantidade de quadros apresentados. O sistema pode calcular a posição em qualquer instante dentro do intervalo, independentemente de estar operando a 30, 60 ou outra taxa de quadros.

### 13.7.3 Interpolação não linear e suavização

Embora a interpolação linear seja útil para compreender os fundamentos, muitos movimentos naturais não apresentam velocidade constante durante todo o intervalo. Um objeto pode começar lentamente, acelerar e depois desacelerar. Esse comportamento é frequentemente chamado de *ease-in* e *ease-out*.

Uma função simples de suavização é:

\[
s(u)=3u^2-2u^3
\]

A função mantém os extremos em zero e um, mas altera a progressão intermediária. O valor de \(u\) pode ser substituído por \(s(u)\) na fórmula de interpolação:

\[
P(u)=P_0+(P_1-P_0)s(u)
\]

O resultado é um movimento que começa e termina com velocidade nula, assumindo uma progressão temporal contínua dentro do intervalo.

A suavização é bastante utilizada em interfaces e animações de apresentação, pois reduz mudanças abruptas de velocidade. Entretanto, ela não é adequada para todos os casos. Uma animação que representa um objeto em movimento uniforme, por exemplo, pode exigir uma interpolação linear sem suavização.

## 13.8 Animação de transformações geométricas

Os conceitos de transformação geométrica estudados anteriormente podem ser aplicados à animação. A diferença é que os parâmetros das transformações deixam de ser constantes e passam a variar com o tempo.

Uma translação animada altera a posição do objeto. Uma rotação animada altera seu ângulo. Uma escala animada modifica suas dimensões. Essas operações podem ser combinadas para construir movimentos mais complexos.

### 13.8.1 Translação animada

Na translação, a posição do objeto varia ao longo do tempo. Para uma animação linear:

\[
P(t)=P_0+(P_1-P_0)u
\]

em que \(u\) representa o progresso temporal entre os estados inicial e final.

A fórmula pode ser utilizada para movimentar um objeto horizontalmente, verticalmente ou ao longo de uma trajetória retilínea em três dimensões. A principal vantagem é que a posição é calculada diretamente a partir do tempo, evitando que o movimento dependa da quantidade de atualizações executadas.

### 13.8.2 Rotação animada

A rotação pode ser representada por uma matriz que depende do ângulo \(\theta\). Em duas dimensões, a matriz de rotação é:

\[
R(\theta)=
\begin{bmatrix}
\cos\theta & -\sin\theta\\
\sin\theta & \cos\theta
\end{bmatrix}
\]

Para animar uma rotação, o ângulo passa a ser uma função do tempo:

\[
\theta=\theta(t)
\]

Se o ângulo variar linearmente, podemos utilizar:

\[
\theta(t)=\theta_0+(\theta_1-\theta_0)u
\]

A matriz de rotação é recalculada para cada instante. O objeto é então transformado de acordo com o ângulo atual.

A rotação exige atenção ao ponto em torno do qual o objeto gira. Uma rotação aplicada diretamente às coordenadas de um objeto ocorre em torno da origem do sistema de coordenadas. Para girar um objeto em torno de seu próprio centro, normalmente é necessário organizar suas coordenadas locais ou compor transformações de translação e rotação.

### 13.8.3 Escala animada

A escala altera as dimensões de um objeto. Em duas dimensões, uma transformação de escala pode ser representada por:

\[
S=
\begin{bmatrix}
s_x & 0\\
0 & s_y
\end{bmatrix}
\]

Na animação, os fatores de escala podem variar com o tempo:

\[
s_x=s_x(t)
\]

\[
s_y=s_y(t)
\]

Uma escala uniforme utiliza o mesmo fator para as duas dimensões. Uma escala não uniforme utiliza fatores diferentes, podendo alterar a proporção do objeto.

A escala animada é utilizada em efeitos de aproximação, expansão, pulsação e transições visuais. Em interfaces, por exemplo, um elemento pode aumentar ligeiramente de tamanho quando selecionado. Em animações de personagens, a escala pode ser utilizada de forma controlada para produzir efeitos estilizados.

## 13.9 Animação procedural

A animação procedural é uma abordagem na qual o movimento é gerado por regras, funções ou algoritmos, em vez de depender exclusivamente de estados definidos manualmente. O programa calcula o comportamento do objeto a partir de parâmetros e do tempo.

Uma animação procedural pode utilizar funções matemáticas, simulações físicas, regras de comportamento ou combinações dessas técnicas. Por exemplo, um objeto pode oscilar verticalmente utilizando uma função seno:

\[
y(t)=y_0+A\sin(\omega t)
\]

Nessa expressão, \(y_0\) representa a posição central, \(A\) representa a amplitude da oscilação e \(\omega\) controla a frequência angular.

Esse tipo de função pode representar movimentos repetitivos, como oscilações, pulsos ou movimentos periódicos. A animação é calculada automaticamente, sem que seja necessário definir manualmente cada quadro.

A animação procedural também pode ser utilizada para gerar movimentos mais complexos. Um conjunto de objetos pode seguir regras diferentes, mas compartilhar uma mesma função temporal. Em uma simulação, por exemplo, cada objeto pode responder a forças ou condições específicas.

Uma vantagem dessa abordagem é a possibilidade de controlar o movimento por parâmetros. Alterar a amplitude, a frequência ou a velocidade pode modificar o comportamento sem exigir a criação de novos quadros-chave. Por outro lado, movimentos procedurais podem ser mais difíceis de editar artisticamente quando suas regras são complexas.

É importante distinguir animação procedural de simulação física. Uma animação procedural pode utilizar uma função matemática arbitrária para produzir um movimento visualmente desejado. Uma simulação física procura representar o comportamento de um sistema de acordo com modelos físicos, como forças, massa, velocidade e aceleração. As duas abordagens podem ser combinadas, mas não são equivalentes.

## 13.10 Experimentos em Python

Os experimentos a seguir apresentam quatro níveis de utilização de Python. Inicialmente, o estudante visualizará valores temporais. Em seguida, implementará a interpolação, utilizará recursos de bibliotecas e construirá uma animação visual.

### 13.10.1 Nível 1 — Exploração: calcular posições ao longo do tempo

O exemplo apresenta um objeto que se desloca horizontalmente com velocidade constante. A posição é calculada para diferentes instantes.

```python
def posicao(t, x_inicial=2, velocidade=3):
    return x_inicial + velocidade * t


for t in range(5):
    print(f"Tempo: {t}s | Posição: {posicao(t)}")
```

A saída será:

```text
Tempo: 0s | Posição: 2
Tempo: 1s | Posição: 5
Tempo: 2s | Posição: 8
Tempo: 3s | Posição: 11
Tempo: 4s | Posição: 14
```

O exemplo demonstra que a posição é determinada pelo tempo e pela velocidade. O objeto avança três unidades a cada segundo. Essa abordagem é diferente de simplesmente somar três unidades a cada quadro, pois o tempo é explicitamente considerado.

### 13.10.2 Nível 2 — Implementação: interpolação linear

O próximo exemplo implementa uma função de interpolação linear. Ela recebe um valor inicial, um valor final e um parâmetro de progresso.

```python
def lerp(inicial, final, u):
    u = max(0, min(1, u))
    return inicial + (final - inicial) * u


print(lerp(2, 8, 0))
print(lerp(2, 8, 0.5))
print(lerp(2, 8, 1))
```

O resultado será:

```text
2
5.0
8
```

A função restringe o parâmetro \(u\) ao intervalo entre zero e um. Isso impede que o valor interpolado ultrapasse os extremos definidos. A função pode ser aplicada a posições, escalas, ângulos ou outras propriedades numéricas.

Para interpolar uma posição bidimensional, basta aplicar a mesma operação a cada componente.

```python
import numpy as np

def lerp_vetor(p0, p1, u):
    u = max(0, min(1, u))
    return p0 + (p1 - p0) * u


p0 = np.array([2.0, 1.0])
p1 = np.array([8.0, 5.0])

print(lerp_vetor(p0, p1, 0.5))
```

A saída será:

```text
[5. 3.]
```

O resultado corresponde ao ponto médio entre as duas posições.

### 13.10.3 Nível 3 — Uso de biblioteca: visualizar a trajetória

O Matplotlib permite visualizar a trajetória produzida pela interpolação. Nesse exemplo, o programa calcula diversas posições intermediárias e apresenta o caminho percorrido.

```python
import numpy as np
import matplotlib.pyplot as plt

p0 = np.array([2.0, 1.0])
p1 = np.array([8.0, 5.0])

valores_u = np.linspace(0, 1, 20)

posicoes = [
    p0 + (p1 - p0) * u
    for u in valores_u
]

posicoes = np.array(posicoes)

plt.plot(
    posicoes[:, 0],
    posicoes[:, 1],
    marker="o"
)

plt.scatter(
    [p0[0], p1[0]],
    [p0[1], p1[1]]
)

plt.annotate("Início", p0)
plt.annotate("Fim", p1)

plt.grid(True)
plt.xlabel("Eixo x")
plt.ylabel("Eixo y")
plt.title("Trajetória de uma animação linear")
plt.show()
```

A função `np.linspace` cria valores igualmente espaçados entre zero e um. Cada valor é utilizado para calcular uma posição intermediária. O gráfico apresenta a trajetória e os pontos calculados.

Esse exemplo ainda não apresenta movimento em tempo real, mas permite compreender a sequência de estados que uma animação pode utilizar. Cada posição calculada pode ser associada a um quadro da animação.

### 13.10.4 Nível 4 — Aplicação: animação com Matplotlib

O exemplo seguinte utiliza `FuncAnimation` para atualizar a posição de um objeto ao longo do tempo. O objeto é representado por um ponto que se desloca horizontalmente.

```python
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.animation import FuncAnimation

fig, ax = plt.subplots()

ax.set_xlim(0, 10)
ax.set_ylim(0, 5)
ax.set_xlabel("Eixo x")
ax.set_ylabel("Eixo y")
ax.set_title("Animação de movimento linear")
ax.grid(True)

ponto, = ax.plot(
    [],
    [],
    marker="o",
    markersize=10
)

duracao = 4.0
x_inicial = 1.0
x_final = 9.0
y = 2.5


def atualizar(quadro):
    progresso = quadro / 100

    x = x_inicial + (
        x_final - x_inicial
    ) * progresso

    ponto.set_data([x], [y])

    return ponto,


animacao = FuncAnimation(
    fig,
    atualizar,
    frames=101,
    interval=duracao * 1000 / 100,
    blit=True
)

plt.show()
```

A função `atualizar` recebe o índice do quadro e calcula o progresso da animação. Esse progresso é utilizado para determinar a posição horizontal do ponto. A função `set_data` atualiza os dados apresentados pelo objeto gráfico.

O parâmetro `frames` define a quantidade de atualizações previstas, enquanto `interval` indica o intervalo de apresentação em milissegundos. O exemplo é adequado para explorar o funcionamento de uma animação, mas a atualização baseada no índice do quadro não é a abordagem mais robusta para aplicações que precisam manter uma velocidade independente do desempenho.

Em aplicações interativas mais completas, é recomendável calcular o estado com base no tempo efetivamente decorrido. Dessa maneira, uma queda temporária na taxa de quadros não altera necessariamente a duração total do movimento.

### 13.10.5 Animação baseada no tempo decorrido

Uma forma de organizar o cálculo temporal consiste em definir o instante inicial e calcular o progresso a partir do tempo atual. O exemplo a seguir apresenta a função responsável por calcular esse progresso.

```python
def progresso_animacao(
    tempo_atual,
    tempo_inicio,
    duracao
):
    if duracao <= 0:
        return 1.0

    u = (
        tempo_atual - tempo_inicio
    ) / duracao

    return max(0.0, min(1.0, u))
```

A função recebe o tempo atual, o instante de início e a duração desejada. O resultado é restringido ao intervalo de zero a um. Antes do início, o progresso permanece em zero; depois do término, permanece em um.

Essa organização facilita a reutilização do cálculo em diferentes propriedades. O mesmo parâmetro temporal pode ser utilizado para interpolar posição, escala, cor ou transparência, desde que cada propriedade tenha seus valores inicial e final definidos.

## 13.11 Aplicações em sistemas reais

A animação computacional está presente em diversos tipos de software. Em jogos digitais, ela é utilizada para representar o movimento de personagens, objetos, veículos, câmeras e elementos da interface. A animação pode ser controlada por quadros-chave, regras procedurais, simulações ou combinações dessas técnicas.

Em interfaces gráficas, animações podem fornecer feedback sobre ações do usuário. Um painel que se expande, um indicador de carregamento ou uma transição entre telas ajuda a comunicar mudanças de estado. Entretanto, a animação deve ter uma finalidade clara. Movimentos excessivos ou desnecessários podem prejudicar a compreensão da interface e aumentar o custo de processamento.

Em visualização científica, a animação permite observar a evolução temporal de fenômenos. Uma simulação de fluidos, por exemplo, pode apresentar a variação de velocidade e pressão ao longo do tempo. Uma animação pode tornar mais compreensível uma sequência de estados que seria difícil interpretar por meio de imagens isoladas.

Em aplicações educacionais, animações podem demonstrar conceitos abstratos, como movimentos planetários, processos físicos, estruturas moleculares ou transformações geométricas. A possibilidade de controlar a velocidade e observar estados intermediários pode favorecer a exploração do conteúdo.

Na Engenharia de Software, a implementação de animações exige decisões sobre arquitetura e desempenho. O sistema precisa definir como os estados serão armazenados, como serão atualizados, como os recursos gráficos serão utilizados e como a animação responderá à interação do usuário. Separar a lógica temporal da renderização facilita testes, manutenção e reutilização.

## 13.12 Limitações e problemas

Uma animação pode apresentar problemas quando a atualização do estado depende diretamente da quantidade de quadros processados. Se o programa movimentar um objeto por uma distância fixa a cada atualização, a velocidade final dependerá do desempenho do sistema. Uma máquina que apresenta mais quadros por segundo poderá produzir um movimento mais rápido do que outra.

Outro problema está relacionado à variação do intervalo entre atualizações. Mesmo quando a animação considera o tempo, uma grande variação no intervalo pode afetar a estabilidade de simulações numéricas. Em situações desse tipo, pode ser necessário utilizar um passo temporal fixo para a atualização lógica e separar essa atualização da apresentação visual.

A interpolação linear também possui limitações. Embora seja simples e eficiente, ela produz uma variação constante em relação ao parâmetro de progresso. Isso pode gerar movimentos rígidos ou transições abruptas quando diferentes segmentos são conectados sem considerar a continuidade da velocidade.

A definição de *keyframes* também exige atenção. Se os estados forem incompatíveis ou estiverem muito distantes, a interpolação poderá produzir movimentos pouco naturais. Em animações tridimensionais, a interpolação de orientações apresenta dificuldades adicionais, pois ângulos e rotações possuem propriedades diferentes das posições escalares.

O desempenho é outra preocupação. Uma animação que exige renderização complexa em cada quadro pode não alcançar a taxa de atualização desejada. O desenvolvedor precisa considerar a complexidade dos objetos, o número de elementos animados, a resolução da imagem e o custo das operações realizadas durante a atualização.

Por fim, a animação não deve ser tratada apenas como um efeito visual. Em sistemas interativos, ela pode comunicar estados importantes. Se uma transição atrasar a resposta do sistema ou impedir que o usuário execute uma ação, poderá prejudicar a experiência. A duração, a frequência e a possibilidade de reduzir movimentos devem ser consideradas conforme o contexto da aplicação.

## 13.13 Erros conceituais comuns

Um erro frequente é confundir taxa de quadros com velocidade do movimento. A taxa de quadros indica quantas imagens são apresentadas por segundo; a velocidade indica quanto a posição varia por unidade de tempo. Um objeto pode manter a mesma velocidade em diferentes taxas de quadros se sua posição for calculada adequadamente a partir do tempo.

Outro equívoco é acreditar que uma animação precisa armazenar todos os quadros. Em muitas aplicações, os estados intermediários são calculados dinamicamente. Os *keyframes* definem estados importantes, e a interpolação ou uma regra procedural determina os valores entre eles.

Também é comum considerar que a interpolação linear sempre produz o movimento mais adequado. A LERP é uma ferramenta útil, mas não representa todos os comportamentos desejados. Movimentos que precisam começar ou terminar suavemente podem exigir funções de interpolação não lineares.

Um erro de implementação ocorre quando o progresso da animação ultrapassa o intervalo esperado. Se o parâmetro \(u\) for maior que um, a interpolação poderá ultrapassar o estado final. Por isso, o uso de *clamping* é importante quando a animação deve permanecer dentro de seus limites.

Outro problema é atualizar a posição sem considerar o tempo decorrido. Essa abordagem pode funcionar em um experimento simples, mas torna o comportamento dependente do desempenho. Para aplicações mais robustas, a atualização deve considerar o tempo ou utilizar um passo temporal controlado.

## 13.14 Síntese do capítulo

A animação computacional representa a evolução de propriedades visuais ao longo do tempo. Um objeto pode ser descrito por um estado que inclui posição, orientação, escala e outras características. A animação ocorre quando esse estado é atualizado e apresentado em uma sequência de quadros.

O tempo é tratado de maneira discreta na apresentação de imagens. A taxa de quadros indica quantas imagens são exibidas por segundo, mas não determina, por si só, a velocidade do movimento. Para produzir comportamentos consistentes, é importante calcular as propriedades dos objetos em função do tempo.

Os *keyframes* definem estados importantes da animação. A interpolação permite calcular estados intermediários, sendo a interpolação linear uma das técnicas mais simples e utilizadas. Funções de suavização podem modificar a progressão temporal e produzir movimentos que começam ou terminam de maneira gradual.

As transformações geométricas também podem ser animadas. Translação, rotação e escala passam a depender do tempo, permitindo construir diferentes tipos de movimento. A animação procedural amplia essas possibilidades ao gerar movimentos a partir de funções, regras ou algoritmos.

Esses fundamentos são essenciais para o desenvolvimento de aplicações gráficas e multimídia. No próximo capítulo, a discussão será ampliada para a interação gráfica, considerando eventos, entradas do usuário, mouse, teclado, loop de eventos e atualização de aplicações interativas.

## 13.15 Questões de revisão

1. O que caracteriza uma animação computacional?
2. Qual é a diferença entre o estado de um objeto e o quadro que apresenta esse estado?
3. O que significa tratar o tempo de maneira discreta?
4. Como se calcula a taxa de quadros de uma animação?
5. Por que a taxa de quadros não deve ser confundida com a velocidade do objeto?
6. O que são *keyframes* e qual é sua função?
7. Como a interpolação linear calcula valores intermediários?
8. Qual é a função do parâmetro \(u\) na interpolação?
9. Como a interpolação não linear pode produzir movimentos mais suaves?
10. Como uma transformação geométrica pode ser animada?
11. Qual é a diferença entre animação procedural e simulação física?
12. Por que uma animação baseada no tempo decorrido tende a ser mais robusta do que uma animação baseada apenas na quantidade de quadros?

## 13.16 Exercícios

### Exercícios básicos

**Exercício 1 — Posição ao longo do tempo**

Um objeto parte da posição \(x_0=5\) e se desloca com velocidade constante de \(2\) unidades por segundo. Calcule sua posição após 1, 3 e 5 segundos. Implemente uma função em Python para realizar esses cálculos.

**Exercício 2 — Taxa de quadros**

Uma animação apresenta 240 quadros durante 8 segundos. Calcule sua taxa média de quadros. Em seguida, determine o intervalo médio entre quadros em milissegundos.

**Exercício 3 — Interpolação linear**

Considere uma propriedade que varia de 10 para 50. Calcule os valores interpolados para \(u=0\), \(u=0{,}25\), \(u=0{,}5\), \(u=0{,}75\) e \(u=1\).

### Exercícios intermediários

**Exercício 4 — Interpolação de posição**

Implemente uma função em Python que receba duas posições bidimensionais e um parâmetro \(u\). A função deverá retornar a posição interpolada. Teste o programa com diferentes valores de \(u\).

**Exercício 5 — Suavização temporal**

Implemente a função:

\[
s(u)=3u^2-2u^3
\]

Compare os valores de \(s(u)\) com os valores originais de \(u\). Utilize Matplotlib para representar as duas funções no mesmo gráfico e explique como a suavização modifica a progressão da animação.

**Exercício 6 — Animação de escala**

Crie uma função que receba um fator de escala inicial, um fator final e um parâmetro de progresso. Utilize interpolação linear para calcular o fator de escala em cada instante. Explique como essa função poderia ser aplicada a um objeto gráfico.

### Exercícios avançados

**Exercício 7 — Movimento baseado no tempo**

Implemente uma função que calcule a posição de um objeto a partir do tempo atual, do tempo inicial, da duração e das posições inicial e final. A função deverá manter o objeto na posição inicial antes do início da animação e na posição final após o término.

**Exercício 8 — Animação procedural**

Utilize a função:

\[
y(t)=y_0+A\sin(\omega t)
\]

para representar um objeto que oscila verticalmente. Implemente um programa que apresente a posição do objeto em diferentes instantes. Altere a amplitude e a frequência e observe os resultados.

**Exercício 9 — Comparação de movimentos**

Construa duas animações com os mesmos estados inicial e final. A primeira deverá utilizar interpolação linear; a segunda deverá utilizar uma função de suavização. Compare os movimentos e explique as diferenças em relação à velocidade percebida.

### Exercício prático

**Exercício 10 — Animação de um objeto em Python**

Desenvolva uma aplicação simples que apresente um objeto se deslocando entre duas posições em uma janela gráfica. A aplicação deverá:

- definir uma posição inicial e uma posição final;
- estabelecer a duração da animação;
- calcular o progresso temporal;
- utilizar interpolação para determinar a posição;
- atualizar a representação visual do objeto;
- manter o objeto na posição final após o término.

O código deverá ser organizado em funções, separando o cálculo temporal, a interpolação e a atualização visual. Como extensão, o estudante poderá adicionar rotação, escala ou uma função de suavização.

## 13.17 Referências

As referências abaixo estão relacionadas aos fundamentos de Computação Gráfica e animação computacional abordados no capítulo. Os dados editoriais completos devem ser confirmados na edição efetivamente utilizada.

- PARENT, Rick. *Computer Animation: Algorithms and Techniques*.
- FOLEY et al. *Computer Graphics: Principles and Practice*.
- SHIRLEY et al. *Fundamentals of Computer Graphics*.
- Documentação oficial do NumPy, para operações numéricas e representação vetorial.
- Documentação oficial do Matplotlib, para visualização e animação de gráficos em Python.