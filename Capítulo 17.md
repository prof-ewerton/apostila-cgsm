# Capítulo 17 — Áudio Digital

## Objetivos de aprendizagem

Ao final deste capítulo, o estudante deverá ser capaz de compreender como um sinal sonoro pode ser representado digitalmente, explicar a diferença entre amostragem e quantização e relacionar frequência de amostragem, resolução em bits e qualidade de áudio.

Também deverá compreender o funcionamento básico da representação PCM (*Pulse-Code Modulation*), calcular a taxa de bits de um sinal de áudio não comprimido e distinguir formatos de arquivo de áudio de codecs. Por fim, deverá gerar, visualizar e manipular uma onda sonora simples utilizando Python.

## 17.1 Introdução

O áudio digital está presente em praticamente todos os sistemas multimídia contemporâneos. Aplicações de comunicação, jogos, plataformas de música, sistemas de videoconferência, editores de mídia e dispositivos móveis precisam representar, armazenar, transmitir ou reproduzir sinais sonoros.

O som, em sua origem física, é uma onda de pressão que se propaga em um meio, como o ar. Um microfone transforma variações de pressão em um sinal elétrico. Para que esse sinal possa ser processado por um computador, é necessário convertê-lo em uma representação digital.

Essa conversão envolve duas operações fundamentais: **amostragem**, que registra o sinal em instantes discretos de tempo, e **quantização**, que representa cada amostra por um valor pertencente a um conjunto finito de níveis.

O resultado é uma sequência de números que pode ser armazenada em arquivos, processada por algoritmos e transmitida por redes. Essa representação permite realizar operações como ajuste de volume, filtragem, mixagem, análise de frequência e compressão.

A compreensão do áudio digital exige relacionar fenômenos físicos, matemática, representação de dados e algoritmos. Python será utilizado neste capítulo para experimentar esses conceitos, mas a biblioteca não substitui o entendimento do processo de digitalização.

## 17.2 Pré-requisitos

Para acompanhar o capítulo, é desejável que o estudante conheça variáveis, funções, listas ou arrays, operações matemáticas básicas e representação de números. Noções de gráficos cartesianos e funções periódicas também são úteis.

Os exemplos utilizam NumPy para operações numéricas e Matplotlib para visualização. Essas bibliotecas podem ser instaladas com:

```bash
pip install numpy matplotlib
```

## 17.3 O som como sinal

### 17.3.1 Intuição física

Quando um objeto vibra, ele pode provocar variações de pressão no ambiente. Essas variações se propagam e podem ser percebidas pelo sistema auditivo humano como som.

Um sinal sonoro pode ser representado por uma função que descreve a variação de uma grandeza física ao longo do tempo. Em uma representação simplificada, essa função é escrita como:

\[
x(t)
\]

Nessa expressão, \(t\) representa o tempo e \(x(t)\) representa o valor do sinal naquele instante.

Um sinal pode variar de forma complexa, como ocorre com a voz humana ou com instrumentos musicais. Entretanto, para introduzir os fundamentos do áudio digital, é útil começar com sinais periódicos simples, especialmente a onda senoidal.

### 17.3.2 Onda senoidal

Uma onda senoidal pode ser definida por:

\[
x(t)=A\sin(2\pi ft+\varphi)
\]

em que:

- \(A\) é a amplitude;
- \(f\) é a frequência, medida em hertz (Hz);
- \(t\) é o tempo, medido em segundos;
- \(\varphi\) é a fase inicial, medida em radianos.

A amplitude determina a magnitude da oscilação do sinal. A frequência indica quantos ciclos completos ocorrem por segundo. A fase determina a posição inicial da onda em relação ao ciclo.

Uma frequência de 440 Hz, por exemplo, corresponde a 440 ciclos por segundo. Essa frequência é frequentemente utilizada como referência musical para a nota Lá acima do dó central, embora a percepção de altura dependa de diversos fatores.

O período \(T\) de uma onda é o tempo necessário para completar um ciclo. Ele é inversamente proporcional à frequência:

\[
T=\frac{1}{f}
\]

Para uma onda de 440 Hz:

\[
T=\frac{1}{440}\approx 0{,}00227\text{ s}
\]

Portanto, cada ciclo dura aproximadamente 2,27 milissegundos.

### 17.3.3 Amplitude e percepção

A amplitude está relacionada à intensidade do sinal, mas não deve ser confundida diretamente com a percepção subjetiva de volume. A percepção sonora também depende da frequência, da duração, do contexto e das características do sistema auditivo.

Em sistemas digitais, é comum representar o sinal por valores normalizados no intervalo:

\[
-1 \leq x(t) \leq 1
\]

Essa convenção é frequente em processamento de áudio com números de ponto flutuante. Ela não significa que todos os formatos de áudio utilizem esse intervalo; formatos PCM inteiros podem empregar outros intervalos numéricos.

## 17.4 Digitalização do áudio

A digitalização transforma um sinal contínuo em uma sequência finita de valores. O processo pode ser compreendido por duas operações.

A **amostragem** determina em quais instantes o sinal será observado. A **quantização** determina quais valores digitais poderão representar as amplitudes observadas.

Essas operações introduzem limitações. A amostragem limita a representação temporal do sinal, enquanto a quantização limita a precisão com que suas amplitudes são representadas.

A sequência resultante pode ser escrita como:

\[
x[n]=x(nT_s)
\]

em que:

- \(n\) é o índice inteiro da amostra;
- \(T_s\) é o intervalo entre amostras;
- \(x[n]\) é o valor da amostra de índice \(n\).

O intervalo de amostragem está relacionado à frequência de amostragem:

\[
f_s=\frac{1}{T_s}
\]

Uma frequência de amostragem de 48.000 Hz, por exemplo, significa que o sistema registra 48 mil amostras por segundo.

## 17.5 Amostragem temporal

### 17.5.1 Conceito

A amostragem consiste em medir o sinal em instantes discretos. Em vez de armazenar todos os valores de uma função contínua, o sistema registra apenas os valores correspondentes a uma sequência de instantes.

Para uma frequência de amostragem \(f_s\), os instantes podem ser definidos por:

\[
t_n=\frac{n}{f_s}
\]

A sequência amostrada é, portanto:

\[
x[n]=x\left(\frac{n}{f_s}\right)
\]

Quanto maior a frequência de amostragem, menor será o intervalo entre as amostras. Isso permite representar variações temporais mais rápidas, desde que a quantização e os demais componentes do sistema também sejam adequados.

### 17.5.2 Exemplo numérico

Considere uma onda senoidal de frequência \(f=1000\) Hz.

Se a frequência de amostragem for:

\[
f_s=8000\text{ Hz}
\]

o intervalo entre amostras será:

\[
T_s=\frac{1}{8000}=0{,}000125\text{ s}
\]

Ou seja, uma amostra será registrada a cada 125 microssegundos.

Em um segundo, serão obtidas 8000 amostras. Em uma duração de 2 segundos, serão obtidas:

\[
N=f_sT
\]

\[
N=8000\cdot2=16000
\]

amostras.

### 17.5.3 Teorema da amostragem

O teorema da amostragem estabelece uma condição fundamental para a reconstrução de um sinal limitado em frequência.

Se o sinal contém frequências até uma frequência máxima \(f_{\max}\), a frequência de amostragem deve satisfazer:

\[
f_s>2f_{\max}
\]

O valor \(2f_{\max}\) é conhecido como limite de Nyquist. A frequência \(f_s/2\) é chamada de frequência de Nyquist do sistema de amostragem.

Essa condição é necessária para evitar sobreposição espectral, fenômeno conhecido como *aliasing*. Na prática, sistemas de aquisição utilizam filtros antialiasing antes da amostragem, pois sinais reais podem conter componentes de frequência acima da faixa desejada.

A condição teórica não deve ser interpretada como uma garantia de qualidade perceptiva. A escolha da frequência de amostragem também depende da aplicação, da faixa de frequência de interesse, dos filtros utilizados e dos requisitos de armazenamento e processamento.

### 17.5.4 Aliasing

O *aliasing* ocorre quando componentes de frequência do sinal original são representadas incorretamente após a amostragem. Uma frequência elevada pode aparecer como uma frequência mais baixa no sinal digital.

Esse fenômeno pode produzir distorções audíveis ou dificultar a análise do sinal. Em áudio, o aliasing é especialmente relevante quando o sistema digitaliza sinais sem filtragem adequada ou quando algoritmos de processamento geram componentes de frequência acima da faixa representável.

Uma maneira de reduzir esse problema é utilizar um filtro passa-baixas antes da conversão analógico-digital, limitando a faixa de frequência do sinal.

## 17.6 Quantização

### 17.6.1 Conceito

A amostragem determina os instantes em que o sinal será observado, mas não determina quantos valores diferentes poderão ser armazenados.

A quantização associa cada amplitude amostrada a um nível pertencente a um conjunto finito. Assim, uma amplitude que originalmente poderia assumir qualquer valor dentro de uma faixa passa a ser representada por um dos níveis disponíveis.

A quantidade de níveis depende da profundidade de bits, também chamada de resolução em bits.

Se cada amostra utiliza \(B\) bits, o número de níveis possíveis é:

\[
L=2^B
\]

Por exemplo:

| Profundidade de bits | Número de níveis |
|---:|---:|
| 8 bits | 256 |
| 16 bits | 65.536 |
| 24 bits | 16.777.216 |

Quanto maior a quantidade de níveis, maior será a precisão potencial da representação da amplitude.

### 17.6.2 Erro de quantização

A quantização introduz uma diferença entre o valor original e o valor digital representado. Essa diferença é chamada de erro de quantização:

\[
e[n]=x[n]-x_q[n]
\]

em que \(x[n]\) é a amostra original e \(x_q[n]\) é o valor quantizado.

Para um quantizador uniforme ideal, com arredondamento e sem sobrecarga, o erro costuma ser limitado aproximadamente a metade do intervalo entre níveis:

\[
|e[n]|\leq\frac{\Delta}{2}
\]

em que \(\Delta\) é o espaçamento entre níveis de quantização.

O erro de quantização pode ser percebido como ruído, especialmente em sinais de baixa amplitude. A utilização de maior profundidade de bits reduz o espaçamento entre níveis e, em condições comparáveis, diminui o erro de quantização.

### 17.6.3 Relação entre resolução e faixa dinâmica

Para um quantizador ideal aplicado a uma onda senoidal de amplitude máxima, uma aproximação comum da relação sinal-ruído de quantização é:

\[
\mathrm{SNR}_{dB}\approx6{,}02B+1{,}76
\]

Essa relação pressupõe condições específicas, como quantização uniforme ideal e sinal senoidal de amplitude adequada. Ela não deve ser aplicada indiscriminadamente a qualquer sinal ou sistema de áudio.

O resultado indica que cada bit adicional pode aumentar a relação sinal-ruído teórica em aproximadamente 6 dB.

A profundidade de bits influencia a precisão da amplitude. Já a frequência de amostragem influencia a representação temporal e a faixa de frequência que pode ser representada sem aliasing. São parâmetros diferentes e não devem ser confundidos.

## 17.7 Taxa de bits e armazenamento

A taxa de bits (*bit rate*) indica a quantidade de bits transmitidos ou armazenados por unidade de tempo.

Para áudio PCM não comprimido, a taxa de bits pode ser calculada por:

\[
R=f_sBC
\]

em que:

- \(R\) é a taxa de bits, em bits por segundo;
- \(f_s\) é a frequência de amostragem, em amostras por segundo;
- \(B\) é a profundidade de bits por amostra;
- \(C\) é o número de canais.

### 17.7.1 Exemplo: áudio estéreo

Considere um áudio PCM com:

- frequência de amostragem de 48.000 Hz;
- profundidade de 16 bits;
- dois canais.

A taxa de bits será:

\[
R=48000\cdot16\cdot2
\]

\[
R=1536000\text{ bits/s}
\]

Convertendo para kilobits por segundo, usando a convenção decimal:

\[
R=1536\text{ kbit/s}
\]

Esse valor representa a taxa de dados do áudio PCM, sem considerar cabeçalhos ou outros dados de contêiner.

### 17.7.2 Estimativa de armazenamento

Para estimar o armazenamento de áudio PCM, pode-se utilizar:

\[
S=f_sBTC
\]

em que \(T\) é a duração em segundos e \(S\) é a quantidade de bits.

Para um minuto do exemplo anterior:

\[
S=48000\cdot16\cdot2\cdot60
\]

\[
S=92.160.000\text{ bits}
\]

Dividindo por 8:

\[
S=11.520.000\text{ bytes}
\]

Isso corresponde a aproximadamente 11,52 MB na convenção decimal, sem incluir cabeçalhos e metadados.

A estimativa evidencia uma característica importante do áudio não comprimido: a quantidade de dados cresce linearmente com a frequência de amostragem, a profundidade de bits, o número de canais e a duração.

## 17.8 PCM — Pulse-Code Modulation

A modulação por código de pulsos, conhecida como PCM (*Pulse-Code Modulation*), é uma forma de representar digitalmente um sinal por meio de amostras quantizadas e codificadas.

Em uma descrição simplificada, o processo envolve:

1. Filtrar o sinal analógico para limitar sua faixa de frequência.
2. Amostrar o sinal em instantes discretos.
3. Quantizar os valores das amostras.
4. Codificar cada nível quantizado como uma sequência de bits.

O resultado é uma sequência de valores digitais que representa o sinal sonoro.

PCM é uma representação, não necessariamente um formato de arquivo específico. Um arquivo WAV, por exemplo, pode armazenar áudio PCM, mas WAV é um formato de contêiner que pode acomodar diferentes tipos de dados de áudio.

Em aplicações de processamento, PCM é frequentemente utilizado porque os valores das amostras podem ser acessados diretamente. Isso facilita operações como ajuste de amplitude, filtragem e análise numérica.

## 17.9 Formatos de áudio e codecs

Um **formato de arquivo** define como os dados e informações auxiliares são organizados em um arquivo. Um **codec** (*coder-decoder*) é um mecanismo de codificação e decodificação de dados de mídia.

Esses conceitos estão relacionados, mas não são equivalentes. Um formato pode admitir diferentes codecs, e um codec pode ser utilizado em mais de um formato ou contêiner.

### 17.9.1 WAV

WAV é um formato de contêiner frequentemente utilizado para armazenar áudio PCM. É comum em aplicações de gravação, edição e experimentação, pois pode permitir acesso direto às amostras.

Arquivos WAV não são necessariamente pequenos. Quando armazenam PCM sem compressão, seu tamanho depende principalmente da frequência de amostragem, da profundidade de bits, do número de canais e da duração.

### 17.9.2 MP3

MP3 é um formato de áudio comprimido com perdas. Ele utiliza técnicas de codificação perceptual para reduzir a quantidade de dados, explorando características da percepção auditiva.

A compressão com perdas pode diminuir significativamente o tamanho do arquivo, mas não preserva exatamente todas as informações do sinal original. A qualidade resultante depende de fatores como a taxa de bits e as características do material sonoro.

### 17.9.3 AAC

AAC (*Advanced Audio Coding*) é uma família de técnicas de codificação de áudio com perdas. É utilizada em diferentes sistemas de distribuição de áudio e vídeo.

Assim como no MP3, a codificação envolve uma representação que busca reduzir a quantidade de dados sem produzir degradação perceptiva excessiva para a aplicação considerada.

### 17.9.4 FLAC

FLAC (*Free Lossless Audio Codec*) é um codec de compressão sem perdas. Ele reduz o tamanho dos dados mantendo a possibilidade de reconstruir exatamente as amostras originais.

A compressão sem perdas não significa que o arquivo terá sempre um tamanho pequeno. O grau de compressão depende da estrutura e da redundância do sinal.

### 17.9.5 Comparação

| Formato ou codec | Tipo de compressão | Característica geral |
|---|---|---|
| PCM | Sem compressão de dados | Representação direta das amostras |
| MP3 | Com perdas | Reduz o tamanho explorando características perceptuais |
| AAC | Com perdas | Codificação perceptual de áudio |
| FLAC | Sem perdas | Permite reconstrução exata das amostras originais |

A escolha de um formato depende dos requisitos da aplicação. Para edição e processamento, pode ser conveniente trabalhar com áudio PCM ou formatos sem perdas. Para distribuição pela internet, formatos com perdas podem oferecer menor consumo de armazenamento e largura de banda.

## 17.10 Experimento em Python: geração e visualização de uma onda senoidal

Neste experimento, será criada uma onda senoidal digital. O programa permitirá visualizar o sinal e observar como a frequência de amostragem determina a quantidade de pontos utilizados para representá-lo.

### 17.10.1 Gerando o sinal

```python
import numpy as np
import matplotlib.pyplot as plt

# Parâmetros do sinal
frequencia = 440       # Hz
frequencia_amostragem = 8000  # amostras por segundo
duracao = 0.02         # segundos
amplitude = 1.0

# Instantes de amostragem
t = np.arange(
    0,
    duracao,
    1 / frequencia_amostragem
)

# Geração da onda senoidal
sinal = amplitude * np.sin(
    2 * np.pi * frequencia * t
)

# Visualização
plt.figure(figsize=(10, 4))
plt.plot(t, sinal)
plt.xlabel("Tempo (s)")
plt.ylabel("Amplitude")
plt.title("Onda senoidal de 440 Hz")
plt.grid(True)
plt.show()
```

O código utiliza `np.arange` para construir os instantes de amostragem. Cada instante é separado por \(1/f_s\) segundos.

A função seno é avaliada em cada instante, produzindo uma sequência de valores. Essa sequência constitui a representação digital do sinal.

O gráfico apresenta os valores amostrados conectados por linhas para facilitar a visualização. A linha contínua é uma representação gráfica dos dados; ela não significa que o computador armazenou todos os valores intermediários.

### 17.10.2 Comparando frequências de amostragem

O experimento pode ser modificado para comparar diferentes frequências de amostragem:

```python
import numpy as np
import matplotlib.pyplot as plt

frequencia_sinal = 440
duracao = 0.01

frequencias_amostragem = [1000, 4000, 8000]

plt.figure(figsize=(10, 5))

for fs in frequencias_amostragem:
    t = np.arange(0, duracao, 1 / fs)

    sinal = np.sin(
        2 * np.pi * frequencia_sinal * t
    )

    plt.plot(
        t,
        sinal,
        marker="o",
        label=f"{fs} Hz"
    )

plt.xlabel("Tempo (s)")
plt.ylabel("Amplitude")
plt.title("Comparação de frequências de amostragem")
plt.grid(True)
plt.legend()
plt.show()
```

Ao comparar os gráficos, observa-se que uma frequência de amostragem maior produz mais pontos por unidade de tempo. Entretanto, uma frequência maior não garante automaticamente uma representação adequada: é necessário considerar a frequência máxima do sinal e a condição de amostragem.

No exemplo, a frequência de amostragem de 1000 Hz é maior que duas vezes a frequência da onda de 440 Hz. Ainda assim, a representação visual pode ser menos detalhada que a obtida com 4000 Hz ou 8000 Hz.

## 17.11 Experimento complementar: quantização de uma onda

O código seguinte demonstra uma quantização uniforme simplificada de um sinal normalizado no intervalo \([-1,1]\).

```python
import numpy as np
import matplotlib.pyplot as plt

frequencia = 440
fs = 8000
duracao = 0.01

t = np.arange(0, duracao, 1 / fs)
sinal = np.sin(2 * np.pi * frequencia * t)

bits = 4
niveis = 2 ** bits

# Quantização uniforme em [0, níveis - 1]
indices = np.round(
    (sinal + 1) * (niveis - 1) / 2
)

# Reconstrução aproximada para [-1, 1]
sinal_quantizado = (
    2 * indices / (niveis - 1) - 1
)

plt.figure(figsize=(10, 4))

plt.plot(t, sinal, label="Sinal original")
plt.step(
    t,
    sinal_quantizado,
    where="mid",
    label="Sinal quantizado"
)

plt.xlabel("Tempo (s)")
plt.ylabel("Amplitude")
plt.title("Efeito da quantização")
plt.grid(True)
plt.legend()
plt.show()
```

A quantização utilizada é uma demonstração didática. Ela mapeia o intervalo de amplitude para um conjunto finito de níveis igualmente espaçados. Em sistemas reais, o comportamento exato depende da convenção de representação, do tipo de quantizador e do formato de armazenamento.

Com apenas 4 bits, o número de níveis é:

\[
L=2^4=16
\]

A diferença entre o sinal original e o quantizado permite observar o erro de quantização. Aumentar a profundidade de bits aumenta o número de níveis e tende a reduzir esse erro.

## 17.12 Aplicações em sistemas multimídia

O áudio digital é utilizado em diversas áreas da Engenharia de Software e dos Sistemas Multimídia.

Em sistemas de comunicação, a voz é digitalizada, codificada e transmitida pela rede. A escolha da frequência de amostragem, do codec e da taxa de bits influencia a qualidade percebida, o consumo de banda e a latência.

Em jogos digitais, o áudio é utilizado para produzir efeitos sonoros, música e feedback para o usuário. O sistema pode combinar diferentes fontes sonoras, controlar volumes e reproduzir efeitos conforme os eventos do jogo.

Em aplicações de edição de mídia, as amostras digitais podem ser manipuladas para realizar cortes, ajustes de volume, filtros e mixagem. A representação numérica permite que essas operações sejam implementadas por algoritmos.

Em sistemas de análise de áudio, o sinal pode ser utilizado para reconhecimento de fala, detecção de eventos acústicos, classificação de sons e extração de características. Essas aplicações podem exigir etapas adicionais de processamento, como análise no domínio da frequência.

## 17.13 Limitações e problemas

A digitalização não preserva automaticamente todas as propriedades do sinal original. A frequência de amostragem limita a faixa de frequência representável, enquanto a quantização limita a precisão da amplitude.

O aliasing pode ocorrer quando a frequência de amostragem é insuficiente ou quando não há filtragem adequada antes da conversão. Esse problema pode introduzir componentes de frequência que não existiam no sinal original.

A quantização pode introduzir ruído ou distorção. Em sinais de baixa amplitude, esse efeito pode ser mais perceptível, especialmente quando a profundidade de bits é reduzida.

A compressão com perdas também introduz alterações no sinal. Embora essas alterações sejam projetadas para reduzir a percepção de degradação, o resultado depende do codec, da taxa de bits e do conteúdo sonoro.

Além disso, a taxa de bits influencia o armazenamento e a transmissão. Aumentar a frequência de amostragem, a profundidade de bits ou o número de canais aumenta a quantidade de dados do áudio PCM não comprimido.

## 17.14 Erros conceituais comuns

Um erro frequente é confundir frequência do sinal com frequência de amostragem. A frequência do sinal descreve quantas oscilações ocorrem por segundo. A frequência de amostragem descreve quantas vezes por segundo o sinal é medido.

Outro erro é considerar que aumentar a profundidade de bits aumenta a frequência máxima representável. A profundidade de bits está relacionada à precisão da amplitude; a frequência de amostragem está relacionada à representação temporal.

Também é incorreto afirmar que um arquivo WAV é sempre sem compressão. WAV é um contêiner que frequentemente armazena PCM, mas pode conter outros tipos de dados.

Da mesma forma, não se deve confundir codec com formato de arquivo. O codec define como os dados são codificados e decodificados; o contêiner organiza os dados e informações auxiliares.

Por fim, a taxa de bits não deve ser confundida com a profundidade de bits. A profundidade de bits é uma quantidade de bits por amostra; a taxa de bits é uma quantidade de bits por segundo.

## 17.15 Síntese do capítulo

O áudio digital representa sinais sonoros por meio de sequências numéricas. A digitalização envolve amostragem temporal e quantização de amplitude.

A frequência de amostragem determina quantas amostras são registradas por segundo e está relacionada à faixa de frequência que pode ser representada. O teorema da amostragem estabelece uma condição necessária para evitar aliasing em sinais limitados em frequência.

A profundidade de bits determina a quantidade de níveis de quantização disponíveis. Uma maior profundidade de bits permite representar amplitudes com maior precisão potencial, reduzindo o erro de quantização em condições comparáveis.

A taxa de bits do áudio PCM depende da frequência de amostragem, da profundidade de bits e do número de canais. Esses parâmetros também determinam a quantidade de armazenamento necessária para uma duração específica.

PCM representa as amostras quantizadas diretamente. Formatos e codecs, por sua vez, definem mecanismos de organização, codificação e compressão dos dados. A compressão pode ser sem perdas ou com perdas, conforme a possibilidade de reconstrução exata do sinal original.

## 17.16 Questões de revisão

1. O que é um sinal sonoro e como ele pode ser representado matematicamente?
2. Qual é a diferença entre frequência e período de uma onda?
3. O que significa amostrar um sinal?
4. Como a frequência de amostragem está relacionada ao intervalo entre amostras?
5. Explique o teorema da amostragem e o conceito de frequência de Nyquist.
6. O que é aliasing e como ele pode ser reduzido?
7. Qual é a diferença entre amostragem e quantização?
8. Como a profundidade de bits determina a quantidade de níveis de quantização?
9. O que é erro de quantização?
10. Como calcular a taxa de bits de um sinal PCM?
11. Qual é a diferença entre PCM, formato de arquivo e codec?
12. Compare compressão com perdas e compressão sem perdas.

## 17.17 Exercícios

### Exercícios básicos

**Exercício 1 — Período de uma onda**

Calcule o período de uma onda senoidal de 500 Hz. Explique o significado do resultado.

**Exercício 2 — Número de amostras**

Um sinal é digitalizado com frequência de amostragem de 16.000 Hz durante 3 segundos. Calcule o número total de amostras por canal.

**Exercício 3 — Níveis de quantização**

Determine o número de níveis possíveis para amostras de 8, 12 e 16 bits. Explique como a profundidade de bits influencia a representação da amplitude.

### Exercícios intermediários

**Exercício 4 — Taxa de bits PCM**

Calcule a taxa de bits de um áudio estéreo com frequência de amostragem de 44.100 Hz e profundidade de 16 bits. Apresente o resultado em bit/s e kbit/s.

**Exercício 5 — Armazenamento**

Estime o tamanho, em MB, de um arquivo PCM com duração de 5 minutos, frequência de amostragem de 48.000 Hz, profundidade de 24 bits e dois canais. Desconsidere cabeçalhos e utilize a convenção decimal para MB.

**Exercício 6 — Visualização**

Modifique o experimento de geração de onda senoidal para utilizar frequências de 220 Hz, 440 Hz e 880 Hz. Compare os períodos e explique as diferenças observadas nos gráficos.

### Exercícios avançados

**Exercício 7 — Análise de amostragem**

Considere um sinal que contém componentes de frequência até 7.000 Hz. Determine uma condição para a frequência de amostragem que permita evitar aliasing, considerando um sinal limitado em frequência e um sistema ideal. Explique por que um sistema real precisa considerar filtros.

**Exercício 8 — Comparação de quantização**

Implemente a quantização de uma mesma onda senoidal utilizando 3, 5 e 8 bits. Calcule e compare o erro quadrático médio entre o sinal original e o quantizado.

**Exercício 9 — Avaliação de armazenamento**

Considere duas alternativas para armazenar um áudio de mesma duração: uma com frequência de amostragem maior e outra com profundidade de bits maior. Compare os efeitos sobre armazenamento, faixa de frequência representável e precisão da amplitude.

### Atividade prática integradora

**Exercício 10 — Gerador e analisador de áudio**

Desenvolva um programa em Python que permita gerar uma onda senoidal e visualizar sua representação digital. O programa deverá:

- Permitir definir a frequência do sinal.
- Permitir definir a frequência de amostragem.
- Permitir definir a duração.
- Permitir definir a profundidade de bits para uma quantização didática.
- Exibir o sinal original e o quantizado.
- Calcular o número de amostras.
- Estimar a taxa de bits de um sinal PCM.
- Estimar o armazenamento para a duração escolhida.
- Calcular o erro quadrático médio da quantização.

O relatório deverá explicar a diferença entre amostragem e quantização, apresentar os resultados obtidos e discutir como as escolhas dos parâmetros influenciam a representação e o armazenamento do áudio.

## 17.18 Referências

- LYONS, Richard G. *Understanding Digital Signal Processing*. Pearson.
- OPPENHEIM, Alan V.; SCHAFER, Ronald W. *Discrete-Time Signal Processing*. Pearson.
- SMITH, Steven W. *The Scientist and Engineer's Guide to Digital Signal Processing*. California Technical Publishing.
- NUMPY. *NumPy Documentation*. Documentação oficial da biblioteca NumPy.
- MATPLOTLIB. *Matplotlib Documentation*. Documentação oficial da biblioteca Matplotlib.
- PYTHON SOFTWARE FOUNDATION. *Python Documentation*. Documentação oficial da linguagem Python.