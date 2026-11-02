# Capítulo 18 — Vídeo Digital

## Objetivos de aprendizagem

Ao final deste capítulo, o estudante deverá ser capaz de compreender como um vídeo digital é representado como uma sequência temporal de imagens, identificar os principais parâmetros que caracterizam um vídeo e explicar como resolução, taxa de quadros e taxa de bits influenciam sua qualidade e seu armazenamento.

Também deverá compreender a organização temporal dos quadros, realizar operações básicas de leitura e manipulação de frames e desenvolver pequenos experimentos com Python. Espera-se que consiga relacionar esses conceitos a aplicações de Engenharia de Software, como sistemas de videoconferência, plataformas de streaming, editores de vídeo e sistemas de análise de imagens em movimento.

## 18.1 Introdução

O vídeo digital é uma das formas mais comuns de representação de conteúdo multimídia. Ele está presente em plataformas de streaming, redes sociais, jogos, sistemas de vigilância, videoconferências, aplicações educacionais e ferramentas de análise visual.

Embora seja percebido como movimento contínuo, um vídeo digital é normalmente representado por uma sequência de imagens individuais apresentadas em instantes sucessivos. Cada imagem é denominada quadro ou *frame*. A sensação de movimento resulta da apresentação desses quadros em uma sequência temporal suficientemente rápida.

Essa representação combina dois aspectos fundamentais: o conteúdo espacial de cada imagem e a organização temporal da sequência. O conteúdo espacial depende de propriedades como resolução e representação de pixels. O conteúdo temporal depende de propriedades como taxa de quadros, duração e ordem de apresentação.

Além disso, vídeos frequentemente precisam ser armazenados e transmitidos. A quantidade de dados envolvida pode ser elevada, especialmente quando há alta resolução, muitos quadros por segundo e representação de cores com grande precisão. Por esse motivo, a taxa de bits e os mecanismos de codificação são aspectos importantes dos sistemas de vídeo.

Neste capítulo, serão estudados os fundamentos da representação digital de vídeo e as operações básicas de leitura e manipulação de quadros. Python será utilizado como ferramenta de experimentação, permitindo observar como os parâmetros de um vídeo afetam sua representação e seu processamento.

## 18.2 Pré-requisitos

O estudo deste capítulo pressupõe conhecimentos básicos de representação digital de imagens, pixels, resolução espacial e modelos de cor. Também é desejável que o estudante conheça os conceitos de amostragem e taxa de bits apresentados no capítulo de áudio digital.

Para os exemplos computacionais, são necessários conhecimentos básicos de Python, funções, estruturas condicionais, laços de repetição e arrays. A biblioteca OpenCV será utilizada para ler e processar vídeos, enquanto NumPy será empregado em operações numéricas.

Instale as bibliotecas com:

```bash
pip install opencv-python numpy matplotlib
```

## 18.3 Vídeo como sequência temporal de imagens

### 18.3.1 Intuição

Imagine uma sequência de fotografias de uma pessoa caminhando. Cada fotografia registra uma posição diferente do corpo. Quando essas imagens são apresentadas em uma ordem adequada e em intervalos regulares, o observador percebe o movimento.

O vídeo digital utiliza esse princípio. Cada quadro contém uma imagem, e a sequência de quadros representa a evolução visual de uma cena ao longo do tempo.

Uma representação simplificada de um vídeo pode ser escrita como:

\[
V=\{I_0,I_1,I_2,\ldots,I_{N-1}\}
\]

em que:

- \(V\) representa o vídeo;
- \(I_i\) representa o quadro de índice \(i\);
- \(N\) é o número total de quadros.

Cada quadro é uma imagem digital. Em um vídeo colorido que utiliza o modelo RGB, por exemplo, um quadro pode ser representado por uma matriz tridimensional:

\[
I_i \in \mathbb{R}^{H\times W\times 3}
\]

em que \(H\) é a altura em pixels, \(W\) é a largura e o último eixo representa os canais de cor.

Essa representação descreve a estrutura espacial do quadro. Para representar o vídeo completo, é necessário também considerar a ordem temporal dos quadros e os instantes em que eles devem ser apresentados.

### 18.3.2 Representação temporal

Em um vídeo com taxa de quadros constante, os quadros são apresentados em intervalos regulares. Se a taxa de quadros for \(f\), o intervalo entre quadros será:

\[
\Delta t=\frac{1}{f}
\]

Para uma taxa de 30 quadros por segundo:

\[
\Delta t=\frac{1}{30}\approx0{,}0333\text{ s}
\]

Assim, aproximadamente 33,3 milissegundos separam a apresentação de dois quadros consecutivos.

O instante associado ao quadro de índice \(i\), considerando o primeiro quadro no instante zero, pode ser calculado por:

\[
t_i=\frac{i}{f}
\]

Essa formulação é adequada para uma sequência com taxa de quadros constante. Vídeos com taxa de quadros variável podem utilizar informações temporais específicas para cada quadro, como timestamps.

### 18.3.3 Duração do vídeo

Em uma sequência com \(N\) quadros e taxa de quadros constante \(f\), a duração aproximada é:

\[
T=\frac{N}{f}
\]

Por exemplo, um vídeo com 1800 quadros apresentado a 30 quadros por segundo possui duração aproximada de:

\[
T=\frac{1800}{30}=60\text{ s}
\]

Ou seja, aproximadamente um minuto.

A duração efetiva de um arquivo pode depender da organização temporal e dos timestamps registrados. A fórmula anterior é uma estimativa apropriada para sequências com taxa de quadros constante.

## 18.4 Resolução de vídeo

A resolução espacial de um vídeo descreve as dimensões de cada quadro, normalmente expressas pela quantidade de pixels na horizontal e na vertical.

Um vídeo com resolução de 1280 × 720 possui:

\[
W=1280
\]

\[
H=720
\]

A quantidade de pixels por quadro é:

\[
P=W\cdot H
\]

Portanto:

\[
P=1280\cdot720=921600
\]

Cada quadro contém 921.600 pixels.

A resolução influencia a quantidade de detalhes espaciais que pode ser representada. Entretanto, uma resolução maior não garante, por si só, uma imagem de maior qualidade perceptiva. A qualidade também depende de fatores como foco, iluminação, compressão, taxa de bits, ruído e características do conteúdo.

### 18.4.1 Resolução e proporção de tela

A proporção de tela (*aspect ratio*) corresponde à razão entre a largura e a altura do quadro:

\[
r=\frac{W}{H}
\]

Para uma resolução de 1920 × 1080:

\[
r=\frac{1920}{1080}=\frac{16}{9}
\]

A proporção 16:9 é comum em vídeos destinados a telas modernas. Outras proporções podem ser utilizadas conforme a aplicação, como vídeos verticais, conteúdo cinematográfico ou formatos especializados.

É importante distinguir a resolução em pixels da dimensão física da tela. Uma imagem de 1920 × 1080 pixels pode ser exibida em telas de tamanhos físicos diferentes. A densidade de pixels da tela influencia a dimensão física de cada pixel, mas não altera a resolução do arquivo.

## 18.5 Taxa de quadros

A taxa de quadros (*frame rate*) representa a quantidade de quadros apresentados por segundo. Sua unidade é frames per second (FPS), ou quadros por segundo.

Uma taxa de quadros maior significa que mais imagens são apresentadas em um mesmo intervalo de tempo. Isso pode produzir uma representação temporal mais detalhada, especialmente em cenas com movimento rápido.

A taxa de quadros não deve ser confundida com a resolução. A resolução descreve a quantidade de pixels de cada imagem; a taxa de quadros descreve a frequência de apresentação das imagens.

### 18.5.1 Comparação de taxas de quadros

Considere um vídeo de 10 segundos:

| Taxa de quadros | Número aproximado de quadros |
|---:|---:|
| 24 FPS | 240 |
| 30 FPS | 300 |
| 60 FPS | 600 |
| 120 FPS | 1200 |

Para a mesma duração, uma taxa de quadros maior implica mais quadros. Se todos os quadros possuírem a mesma resolução e a mesma representação de dados, a quantidade de dados não comprimidos também será maior.

Entretanto, a taxa de quadros não determina isoladamente a qualidade visual. A percepção depende da natureza do movimento, da forma de apresentação, do dispositivo e de outros parâmetros do vídeo.

### 18.5.2 Taxa de quadros constante e variável

Em uma sequência com taxa de quadros constante (*Constant Frame Rate — CFR*), os quadros são associados a intervalos temporais regulares.

Em uma sequência com taxa de quadros variável (*Variable Frame Rate — VFR*), os intervalos entre quadros podem variar. Essa abordagem pode ser utilizada em sistemas de gravação e distribuição para adaptar a representação temporal às condições de captura ou processamento.

Em vídeos VFR, não é adequado assumir que o instante de cada quadro pode ser calculado exclusivamente pelo índice do quadro e por uma única taxa de quadros. A informação temporal registrada no arquivo deve ser considerada.

## 18.6 Taxa de bits de vídeo

A taxa de bits (*bit rate*) representa a quantidade de bits utilizada por segundo de vídeo. Ela é expressa normalmente em bit/s, kbit/s ou Mbit/s.

Em vídeos comprimidos, a taxa de bits depende do codec, dos parâmetros de codificação, da resolução, da taxa de quadros e da complexidade do conteúdo.

Uma cena com pouco movimento e grandes regiões uniformes pode ser comprimida de maneira diferente de uma cena com movimento intenso, detalhes finos e mudanças frequentes. Portanto, a relação entre taxa de bits e qualidade não é determinada apenas pela resolução.

### 18.6.1 Estimativa de taxa de bits para vídeo não comprimido

Para estimar a taxa de bits de um vídeo não comprimido, pode-se considerar:

\[
R=W\cdot H\cdot f\cdot B_p
\]

em que:

- \(R\) é a taxa de bits;
- \(W\) é a largura do quadro;
- \(H\) é a altura do quadro;
- \(f\) é a taxa de quadros;
- \(B_p\) é a quantidade de bits por pixel.

Essa expressão pressupõe uma representação com quantidade fixa de bits por pixel e não inclui cabeçalhos ou outros dados auxiliares.

Considere um vídeo RGB de 1920 × 1080 pixels, 30 FPS e 24 bits por pixel.

A taxa de bits aproximada será:

\[
R=1920\cdot1080\cdot30\cdot24
\]

\[
R=1.492.992.000\text{ bits/s}
\]

Ou seja:

\[
R\approx1{,}493\text{ Gbit/s}
\]

Esse valor é elevado porque representa o vídeo sem compressão. Os formatos de vídeo utilizados em armazenamento e transmissão normalmente empregam técnicas de compressão para reduzir a quantidade de dados.

### 18.6.2 Estimativa de armazenamento

Se a taxa de bits for aproximadamente constante, o tamanho de um vídeo pode ser estimado por:

\[
S=R\cdot T
\]

em que \(S\) é a quantidade de bits e \(T\) é a duração em segundos.

Para converter o resultado em bytes, divide-se por 8:

\[
S_{\text{bytes}}=\frac{R\cdot T}{8}
\]

Em vídeos comprimidos, essa estimativa depende da taxa de bits efetiva. Em arquivos reais, o tamanho também pode incluir áudio, metadados, cabeçalhos e estruturas do contêiner.

## 18.7 Quadros e sequência temporal

A ordem dos quadros é fundamental para a interpretação do vídeo. Uma sequência de imagens pode conter os mesmos quadros, mas produzir resultados diferentes se sua ordem for alterada.

Considere três quadros que representam um objeto nas posições:

\[
I_0: x=10
\]

\[
I_1: x=20
\]

\[
I_2: x=30
\]

A apresentação na ordem \(I_0,I_1,I_2\) representa um movimento para a direita. A apresentação na ordem \(I_2,I_1,I_0\) representa um movimento para a esquerda.

A sequência temporal, portanto, é parte essencial da informação de vídeo.

### 18.7.1 Quadros-chave e quadros intermediários

Em alguns sistemas de codificação, nem todos os quadros são armazenados como imagens independentes. Um codificador pode utilizar quadros de referência e representar outros quadros por meio de informações de diferença ou movimento.

Essa organização é relevante para compressão e será aprofundada no capítulo seguinte. Neste capítulo, o foco está na leitura e manipulação de quadros como imagens individuais.

## 18.8 Processamento básico de vídeo

O processamento básico de vídeo consiste em aplicar operações aos quadros de uma sequência. Essas operações podem ser espaciais, temporais ou envolver ambos os aspectos.

Um processamento espacial modifica cada quadro individualmente. Exemplos incluem conversão para escala de cinza, redimensionamento, ajuste de brilho e aplicação de filtros.

Um processamento temporal utiliza informações de diferentes quadros. Exemplos incluem cálculo de diferenças entre quadros, estimativa de movimento e suavização temporal.

Uma operação espacial simples pode ser aplicada quadro a quadro:

\[
I_i'=F(I_i)
\]

em que \(F\) é uma função de processamento e \(I_i'\) é o quadro resultante.

Já uma operação temporal pode depender de uma sequência de quadros:

\[
I_i'=F(I_{i-k},\ldots,I_i,\ldots,I_{i+k})
\]

Essa segunda forma indica que o resultado pode depender de quadros anteriores e posteriores ao quadro atual. Em aplicações em tempo real, o uso de quadros futuros pode introduzir atraso, pois eles ainda precisam ser capturados ou decodificados.

## 18.9 Leitura de vídeo com OpenCV

A biblioteca OpenCV oferece recursos para leitura, processamento e análise de vídeos. O objeto `VideoCapture` permite acessar um arquivo de vídeo ou uma fonte de captura, como uma câmera.

Para ler um arquivo, é necessário abrir a fonte e verificar se ela foi carregada corretamente. Em seguida, o programa pode solicitar quadros sucessivos até que não existam mais dados disponíveis.

O método `read()` retorna uma indicação de sucesso e o quadro lido. Quando a leitura falha, o laço pode ser encerrado.

### 18.9.1 Lendo um vídeo e exibindo seus quadros

O exemplo a seguir lê um arquivo de vídeo e apresenta seus quadros em uma janela.

```python
import cv2

caminho_video = "video.mp4"

captura = cv2.VideoCapture(caminho_video)

if not captura.isOpened():
    raise RuntimeError("Não foi possível abrir o vídeo.")

while True:
    sucesso, frame = captura.read()

    if not sucesso:
        break

    cv2.imshow("Video", frame)

    # Aguarda aproximadamente 1 ms por uma tecla
    tecla = cv2.waitKey(1) & 0xFF

    if tecla == ord("q"):
        break

captura.release()
cv2.destroyAllWindows()
```

O programa abre o arquivo e lê os quadros em sequência. A função `imshow()` apresenta o quadro atual, enquanto `waitKey()` permite que a janela processe eventos de teclado.

A tecla `q` encerra a execução. Ao final, `release()` libera o recurso associado ao arquivo e `destroyAllWindows()` fecha as janelas abertas pelo OpenCV.

Esse exemplo é adequado para experimentar a leitura de quadros, mas não constitui um reprodutor de vídeo completo. A temporização de apresentação, a sincronização com áudio e o tratamento de vídeos com taxa de quadros variável exigem mecanismos adicionais.

## 18.10 Experimento em Python: leitura e manipulação de frames

Neste experimento, um vídeo será lido quadro a quadro. Cada quadro será convertido para escala de cinza e apresentado na janela.

A conversão para escala de cinza pode ser compreendida como uma transformação de uma imagem colorida em uma representação com um único canal de intensidade.

```python
import cv2

caminho_video = "video.mp4"

captura = cv2.VideoCapture(caminho_video)

if not captura.isOpened():
    raise RuntimeError("Não foi possível abrir o vídeo.")

while True:
    sucesso, frame = captura.read()

    if not sucesso:
        break

    # Conversão para escala de cinza
    frame_cinza = cv2.cvtColor(
        frame,
        cv2.COLOR_BGR2GRAY
    )

    cv2.imshow("Video original", frame)
    cv2.imshow("Video em escala de cinza", frame_cinza)

    tecla = cv2.waitKey(1) & 0xFF

    if tecla == ord("q"):
        break

captura.release()
cv2.destroyAllWindows()
```

O OpenCV utiliza, por padrão, a ordem de canais BGR em muitos de seus métodos de leitura e exibição. Essa convenção difere da ordem RGB utilizada em diversas outras bibliotecas.

A função `cvtColor()` recebe o quadro e um código de conversão que indica a transformação desejada. Nesse exemplo, `COLOR_BGR2GRAY` converte a imagem colorida em uma imagem de intensidade.

A operação é aplicada individualmente a cada quadro. Dessa forma, o vídeo processado mantém a sequência temporal original, mas apresenta uma transformação espacial em cada imagem.

## 18.11 Redimensionamento de quadros

O redimensionamento modifica a resolução de um quadro. Essa operação pode ser utilizada para reduzir o custo de processamento, adaptar um vídeo a uma janela ou preparar dados para algoritmos de análise.

```python
import cv2

captura = cv2.VideoCapture("video.mp4")

if not captura.isOpened():
    raise RuntimeError("Não foi possível abrir o vídeo.")

while True:
    sucesso, frame = captura.read()

    if not sucesso:
        break

    frame_reduzido = cv2.resize(
        frame,
        (640, 360)
    )

    cv2.imshow("Video reduzido", frame_reduzido)

    if cv2.waitKey(1) & 0xFF == ord("q"):
        break

captura.release()
cv2.destroyAllWindows()
```

A função `resize()` recebe o quadro original e as dimensões desejadas. Nesse exemplo, cada quadro é redimensionado para 640 × 360 pixels.

O redimensionamento pode reduzir o custo computacional de operações posteriores, pois diminui a quantidade de pixels processados. Entretanto, também pode eliminar detalhes espaciais. A escolha das dimensões deve considerar o objetivo da aplicação.

## 18.12 Análise temporal: diferença entre quadros

Uma operação simples de análise temporal consiste em calcular a diferença entre dois quadros consecutivos.

Se \(I_i\) representa o quadro atual e \(I_{i-1}\) representa o quadro anterior, a diferença pode ser calculada por:

\[
D_i=|I_i-I_{i-1}|
\]

O resultado destaca regiões em que houve alteração entre os quadros. Essa técnica pode ser utilizada como etapa inicial em aplicações de detecção de movimento.

O exemplo seguinte calcula a diferença entre quadros consecutivos e apresenta o resultado.

```python
import cv2

captura = cv2.VideoCapture("video.mp4")

if not captura.isOpened():
    raise RuntimeError("Não foi possível abrir o vídeo.")

quadro_anterior = None

while True:
    sucesso, frame = captura.read()

    if not sucesso:
        break

    quadro_cinza = cv2.cvtColor(
        frame,
        cv2.COLOR_BGR2GRAY
    )

    if quadro_anterior is not None:
        diferenca = cv2.absdiff(
            quadro_cinza,
            quadro_anterior
        )

        cv2.imshow("Diferenca entre quadros", diferenca)

    quadro_anterior = quadro_cinza.copy()

    if cv2.waitKey(1) & 0xFF == ord("q"):
        break

captura.release()
cv2.destroyAllWindows()
```

A função `absdiff()` calcula a diferença absoluta entre duas imagens. Regiões sem alterações significativas tendem a apresentar valores próximos de zero, enquanto regiões modificadas podem apresentar valores maiores.

Essa técnica possui limitações. Alterações de iluminação, ruído da câmera e movimento da própria câmera podem produzir diferenças mesmo quando não há movimento de um objeto específico. Por isso, a diferença entre quadros é uma abordagem simples, mas não resolve sozinha todos os problemas de detecção de movimento.

## 18.13 Aplicações em sistemas multimídia

O vídeo digital é utilizado em diferentes contextos da Engenharia de Software.

Em plataformas de streaming, os vídeos precisam ser armazenados, codificados, transmitidos e apresentados de acordo com as capacidades do dispositivo e as condições da rede. Resolução, taxa de quadros e taxa de bits influenciam a experiência de reprodução.

Em sistemas de videoconferência, a captura de vídeo precisa ser integrada à transmissão de áudio, ao processamento de imagem e à comunicação em rede. A latência é um requisito importante, pois atrasos elevados prejudicam a interação entre participantes.

Em sistemas de vigilância, a leitura e o processamento de quadros podem ser utilizados para detectar movimento, acompanhar objetos ou identificar eventos. O processamento pode ocorrer continuamente ou apenas quando determinadas condições são satisfeitas.

Em aplicações de edição de vídeo, os quadros podem ser selecionados, recortados, redimensionados, filtrados e organizados em uma linha do tempo. O sistema precisa preservar a relação temporal entre os quadros e, quando aplicável, sincronizar o vídeo com o áudio.

Em sistemas de análise visual, vídeos podem ser utilizados como entrada para algoritmos de visão computacional, reconhecimento de ações, rastreamento e análise de movimento.

## 18.14 Limitações e problemas comuns

A leitura e o processamento de vídeos podem exigir recursos computacionais significativos. A resolução, a taxa de quadros e a complexidade das operações influenciam o tempo de processamento e o consumo de memória.

Uma aplicação que armazena todos os quadros de um vídeo na memória pode consumir uma quantidade elevada de recursos. Em muitos casos, é preferível processar os quadros sequencialmente, mantendo apenas os dados necessários para a operação atual.

Outro problema é a temporização. A leitura de quadros de um arquivo não garante que eles serão apresentados no instante correto. Um reprodutor de vídeo precisa considerar a taxa de quadros, os timestamps e a sincronização com o áudio.

Também é importante considerar que diferentes bibliotecas podem utilizar convenções distintas para os canais de cor. A interpretação incorreta dessas convenções pode produzir imagens com cores trocadas.

O processamento temporal apresenta desafios adicionais. Uma operação que depende de quadros futuros pode exigir armazenamento temporário ou introduzir atraso. Em aplicações em tempo real, esse atraso pode ser um requisito crítico.

Por fim, a taxa de bits e a qualidade visual não possuem uma relação universal e direta. A qualidade depende do conteúdo, do método de codificação, dos parâmetros utilizados e das características do sistema de reprodução.

## 18.15 Equívocos frequentes

Um equívoco comum é considerar que um vídeo é apenas uma imagem com resolução maior. Um vídeo é uma sequência temporal de imagens, e sua representação precisa incluir informações relacionadas à ordem e ao tempo de apresentação.

Outro erro é confundir resolução com taxa de quadros. A resolução descreve as dimensões espaciais de cada quadro; a taxa de quadros descreve a frequência temporal de apresentação.

Também é incorreto assumir que a taxa de bits de um vídeo comprimido pode ser calculada apenas pela resolução e pela taxa de quadros. A taxa de bits efetiva depende do codec, dos parâmetros de codificação e do conteúdo.

A leitura de quadros também não deve ser confundida com a reprodução correta de um vídeo. A reprodução exige temporização e, quando existe áudio, sincronização entre os componentes.

Por fim, a conversão de um quadro para escala de cinza não altera sua sequência temporal. Trata-se de uma transformação espacial aplicada individualmente a cada imagem.

## 18.16 Síntese do capítulo

O vídeo digital é representado como uma sequência temporal de imagens. Cada quadro possui uma representação espacial, normalmente baseada em pixels, e a sequência é organizada para produzir a percepção de movimento.

A resolução determina as dimensões espaciais dos quadros. A taxa de quadros determina a quantidade de imagens apresentadas por segundo. A taxa de bits representa a quantidade de dados utilizada por segundo e influencia o armazenamento e a transmissão.

A leitura e a manipulação de quadros permitem implementar operações básicas de processamento de vídeo. A conversão para escala de cinza e o redimensionamento são exemplos de operações espaciais aplicadas a cada quadro. A diferença entre quadros é um exemplo de operação temporal que utiliza informações de imagens consecutivas.

O uso de Python e OpenCV permite experimentar esses conceitos, observar os dados e implementar operações simples. Em aplicações reais, o processamento de vídeo também precisa considerar desempenho, memória, temporização, sincronização e características dos formatos de armazenamento.

## 18.17 Questões de revisão

1. Como um vídeo digital pode ser representado matematicamente?
2. O que é um quadro (*frame*)?
3. Qual é a diferença entre resolução e taxa de quadros?
4. Como calcular a duração aproximada de um vídeo com taxa de quadros constante?
5. O que significa uma taxa de 30 FPS?
6. Qual é a diferença entre taxa de quadros constante e variável?
7. Como estimar a taxa de bits de um vídeo não comprimido?
8. Por que a taxa de bits de um vídeo comprimido depende do conteúdo?
9. Qual é a diferença entre processamento espacial e processamento temporal?
10. Como a diferença entre quadros pode ser utilizada para destacar movimento?
11. Por que a leitura sequencial de quadros não constitui, necessariamente, uma reprodução de vídeo com temporização correta?
12. Quais problemas podem ocorrer ao armazenar todos os quadros de um vídeo na memória?

## 18.18 Exercícios

### Exercícios básicos

**Exercício 1 — Duração de um vídeo**

Um vídeo possui 2400 quadros e taxa de quadros constante de 30 FPS. Calcule sua duração aproximada.

**Exercício 2 — Resolução**

Calcule a quantidade de pixels de um vídeo com resolução de 1920 × 1080. Compare o resultado com um vídeo de 1280 × 720.

**Exercício 3 — Taxa de quadros**

Explique a diferença entre um vídeo de 24 FPS e um vídeo de 60 FPS. Considere a quantidade de quadros apresentados por segundo e as possíveis consequências para a representação de movimento.

### Exercícios intermediários

**Exercício 4 — Estimativa de taxa de bits**

Calcule a taxa de bits aproximada de um vídeo RGB não comprimido com resolução de 1280 × 720, 25 FPS e 24 bits por pixel.

**Exercício 5 — Redimensionamento**

Modifique o exemplo de leitura de vídeo para redimensionar todos os quadros para 320 × 240 pixels. Compare a resolução original e a resolução resultante e explique os efeitos sobre o conteúdo espacial.

**Exercício 6 — Escala de cinza**

Implemente a conversão de um vídeo para escala de cinza e apresente uma comparação visual entre os quadros originais e processados. Explique por que a transformação é aplicada individualmente a cada quadro.

### Exercícios avançados

**Exercício 7 — Diferença temporal**

Implemente uma operação que calcule a diferença absoluta entre quadros consecutivos. Analise os resultados para uma cena estática e para uma cena com movimento.

**Exercício 8 — Memória**

Estime a quantidade de memória necessária para armazenar todos os quadros de um vídeo de 10 segundos, resolução 1920 × 1080, 30 FPS e representação RGB de 8 bits por canal. Desconsidere metadados e estruturas auxiliares.

**Exercício 9 — Comparação de parâmetros**

Considere dois vídeos com a mesma duração. O primeiro possui resolução maior e taxa de quadros menor; o segundo possui resolução menor e taxa de quadros maior. Analise as diferenças em termos de informação espacial, informação temporal e custo de processamento.

### Atividade prática integradora

**Exercício 10 — Analisador de vídeo**

Desenvolva uma aplicação em Python que leia um arquivo de vídeo e apresente informações sobre sua representação. O sistema deverá:

- Abrir um arquivo de vídeo.
- Verificar se o arquivo pode ser lido.
- Obter a largura e a altura dos quadros.
- Obter a taxa de quadros informada pelo arquivo.
- Estimar a duração e o número de quadros.
- Exibir os quadros em sequência.
- Permitir converter os quadros para escala de cinza.
- Permitir redimensionar os quadros.
- Calcular e visualizar a diferença entre quadros consecutivos.

O relatório deverá apresentar os parâmetros do vídeo utilizado, explicar as operações implementadas e discutir as limitações observadas. Também deverá comparar o custo de processamento das operações realizadas e indicar como a resolução e a taxa de quadros influenciam esse custo.

## 18.19 Referências

- GONZALEZ, Rafael C.; WOODS, Richard E. *Digital Image Processing*. Pearson.
- PARENT, Rick. *Computer Animation: Algorithms and Techniques*. Morgan Kaufmann.
- SZELISKI, Richard. *Computer Vision: Algorithms and Applications*. Springer.
- OPENCV. *OpenCV Documentation*. Documentação oficial da biblioteca OpenCV.
- NUMPY. *NumPy Documentation*. Documentação oficial da biblioteca NumPy.
- MATPLOTLIB. *Matplotlib Documentation*. Documentação oficial da biblioteca Matplotlib.
- PYTHON SOFTWARE FOUNDATION. *Python Documentation*. Documentação oficial da linguagem Python.