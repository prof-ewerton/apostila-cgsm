# Capítulo 19 — Codificação, Compressão e Armazenamento

## Objetivos de aprendizagem

Ao final deste capítulo, o estudante deverá ser capaz de:

- explicar por que dados multimídia precisam de compressão;
- identificar diferentes formas de redundância em imagens, áudio e vídeo;
- distinguir compressão sem perdas (*lossless*) de compressão com perdas (*lossy*);
- calcular e interpretar a taxa de compressão;
- diferenciar codec, formato de arquivo e contêiner;
- analisar os efeitos da compressão sobre qualidade, armazenamento e transmissão;
- realizar experimentos de compressão de imagens utilizando Python;
- avaliar escolhas de codificação em sistemas de software multimídia.

## Introdução

Imagens, áudio e vídeo podem ocupar grandes quantidades de armazenamento e exigir elevada largura de banda para transmissão. Uma imagem colorida de alta resolução, por exemplo, pode conter milhões de pixels. Um vídeo, por sua vez, combina muitas imagens ao longo do tempo e frequentemente inclui uma ou mais trilhas de áudio.

Mesmo quando os dados são representados digitalmente, isso não significa que sua representação seja compacta. Uma imagem RGB sem compressão armazena, em geral, três valores por pixel. Um vídeo não comprimido pode armazenar uma sequência de imagens desse tipo, resultando em um volume de dados muito elevado.

A compressão procura reduzir esse volume. Ela pode explorar padrões, repetições e características perceptuais dos dados para produzir uma representação menor. A redução é importante para:

- diminuir o espaço ocupado em discos e dispositivos móveis;
- reduzir o consumo de memória;
- acelerar o envio de arquivos pela rede;
- viabilizar streaming de áudio e vídeo;
- melhorar o desempenho de aplicações multimídia.

Entretanto, compressão não é uma operação sem consequências. Algumas técnicas preservam exatamente os dados originais; outras descartam informações para obter arquivos menores. A escolha deve considerar a finalidade da aplicação, a qualidade desejada, o custo computacional e as condições de armazenamento e transmissão.

Este capítulo apresenta os fundamentos da codificação, compressão e armazenamento multimídia, com exemplos e experimentos em Python.

## Pré-requisitos

Para acompanhar o capítulo, é recomendável conhecer:

- representação digital de imagens por pixels e matrizes;
- noções de amostragem e quantização;
- representação digital de áudio por amostras;
- vídeo como sequência temporal de quadros (*frames*);
- conceitos básicos de arquivos, bits e bytes;
- programação básica em Python.

---

## 1. Por que dados multimídia precisam de compressão?

### 1.1 Volume de dados sem compressão

Considere uma imagem RGB com largura \(W\), altura \(H\) e 8 bits por canal. Cada pixel possui três canais de cor, portanto:

\[
\text{bits por pixel}=3\cdot 8=24
\]

O tamanho teórico dos dados de pixel é:

\[
S=W\cdot H\cdot 3
\]

em bytes, pois cada canal ocupa 1 byte.

Para uma imagem de \(1920\times1080\) pixels:

\[
S=1920\cdot1080\cdot3
\]

\[
S=6\,220\,800\text{ bytes}
\]

Isso corresponde a aproximadamente 6,22 MB em unidades decimais, ou cerca de 5,93 MiB em unidades binárias. O cálculo considera apenas os dados dos pixels, sem cabeçalhos, metadados ou estruturas adicionais.

Em vídeo, o volume cresce rapidamente. Se cada quadro tiver esse tamanho e forem armazenados 30 quadros por segundo, o fluxo bruto será:

\[
R=6\,220\,800\cdot30
\]

\[
R=186\,624\,000\text{ bytes/s}
\]

Multiplicando por 8 para converter bytes em bits:

\[
R\approx1{,}49\text{ Gbit/s}
\]

Esse valor representa um fluxo bruto aproximado, sem compressão e sem considerar áudio. A taxa é elevada para transmissão em redes comuns, o que explica a importância dos métodos de compressão de vídeo.

### 1.2 Intuição: encontrar padrões

Imagine uma imagem composta quase inteiramente por uma região azul uniforme. Armazenar individualmente o valor de cada pixel repete muitas informações.

Em vez de registrar milhares de vezes a mesma cor, uma representação compacta poderia registrar:

> Repetir a cor azul por determinada quantidade de pixels.

Essa ideia simples é um exemplo de exploração de redundância. Métodos reais podem utilizar técnicas mais sofisticadas, mas a motivação é semelhante: evitar armazenar repetidamente informações que podem ser reconstruídas a partir de uma descrição menor.

### 1.3 Compressão e codificação

Os termos **codificação** e **compressão** estão relacionados, mas não são equivalentes.

Codificação é a representação de uma informação por meio de símbolos ou bits, segundo determinadas regras. Uma codificação pode ser usada para compressão, mas nem toda codificação reduz o tamanho dos dados.

A compressão é uma codificação projetada para reduzir a quantidade de informação necessária para representar os dados, segundo um modelo ou objetivo específico.

Uma transformação de dados pode, por exemplo, alterar sua representação sem necessariamente diminuir o arquivo. O resultado só será uma compressão efetiva se a representação final ocupar menos espaço do que a original, considerando também os dados auxiliares necessários à decodificação.

---

## 2. Redundância

A compressão explora redundâncias presentes nos dados. Em multimídia, é útil distinguir algumas formas de redundância.

### 2.1 Redundância espacial

A redundância espacial ocorre quando pixels próximos apresentam valores semelhantes ou repetidos.

Exemplos:

- uma parede de cor uniforme em uma imagem;
- o céu em uma fotografia;
- áreas de fundo em uma interface gráfica;
- regiões de uma imagem digitalizada que possuem tons semelhantes.

Uma técnica de compressão pode explorar essas relações entre pixels vizinhos.

### 2.2 Redundância temporal

A redundância temporal ocorre quando quadros próximos de um vídeo contêm informações semelhantes.

Considere uma câmera fixa que registra uma pessoa caminhando diante de um fundo estático. Grande parte do fundo permanece igual entre quadros consecutivos. Armazenar todos os pixels de todos os quadros de forma independente pode repetir muitas informações.

Métodos de compressão de vídeo podem representar diferenças entre quadros, estimar movimento e codificar apenas partes que mudaram, além de utilizar quadros de referência.

### 2.3 Redundância estatística

A redundância estatística está relacionada à frequência de ocorrência dos símbolos.

Se determinado símbolo aparece muito mais frequentemente do que outros, pode ser vantajoso atribuir a ele uma representação menor. Codificações de comprimento variável, como a codificação de Huffman, exploram esse princípio.

Por exemplo, em uma sequência em que o símbolo `A` aparece frequentemente e o símbolo `Z` raramente, pode ser eficiente utilizar menos bits para representar `A` e mais bits para representar `Z`.

### 2.4 Redundância perceptual

A redundância perceptual está relacionada às características da percepção humana. Nem todas as diferenças entre sinais são igualmente perceptíveis.

Em imagens, pequenas alterações em determinadas regiões ou componentes de cor podem ser menos perceptíveis do que alterações em outras. Em áudio, certos componentes podem ser mascarados por sons mais intensos próximos em frequência ou tempo.

Técnicas com perdas utilizam modelos perceptuais para descartar ou representar com menor precisão informações que tendem a ter menor impacto perceptual.

Isso não significa que a informação descartada seja matematicamente irrelevante. Ela pode ser recuperável em princípio no sinal original, mas não é preservada pelo processo de compressão com perdas.

---

## 3. Compressão sem perdas (*lossless*)

### 3.1 Conceito

A compressão sem perdas reduz o tamanho dos dados sem eliminar informação. Após a descompressão, os dados reconstruídos são exatamente iguais aos dados originais.

Formalmente, seja:

- \(X\) o conjunto de dados original;
- \(C(X)\) o resultado da compressão;
- \(D(C(X))\) o resultado da descompressão.

Em uma técnica sem perdas:

\[
D(C(X))=X
\]

A igualdade é exata, considerando a sequência de dados que está sendo comprimida.

### 3.2 Exemplos de técnicas

#### Codificação por comprimento de sequência

A codificação por comprimento de sequência, conhecida como *Run-Length Encoding* (RLE), representa sequências de símbolos repetidos por meio do símbolo e da quantidade de repetições.

Considere:

```text
AAAAABBBCC
```

Uma representação conceitual poderia ser:

```text
5A3B2C
```

A sequência original possui 10 símbolos. A representação codificada possui 6 caracteres, se cada número e cada símbolo forem armazenados como um caractere. Em um formato real, a codificação dos comprimentos e símbolos depende da implementação.

O RLE é eficiente quando há sequências longas de valores repetidos. Entretanto, pode ser ineficiente em dados sem repetições, pois acrescenta informações de contagem.

#### Codificação de Huffman

A codificação de Huffman utiliza códigos de comprimento variável para representar símbolos. Símbolos mais frequentes tendem a receber códigos menores, enquanto símbolos menos frequentes recebem códigos maiores.

Uma propriedade importante é que os códigos são prefix-free: nenhum código de símbolo é prefixo de outro. Isso permite decodificar a sequência sem ambiguidade.

A codificação de Huffman pode reduzir a quantidade média de bits por símbolo quando as frequências dos símbolos não são uniformes.

#### Codificação preditiva

Em uma codificação preditiva, o sistema estima um valor com base em valores anteriores ou vizinhos e codifica o erro de predição.

Se o valor real for \(x\) e a previsão for \(\hat{x}\), o resíduo é:

\[
e=x-\hat{x}
\]

Quando a previsão é boa, o resíduo tende a ter magnitude menor e pode ser codificado com menos bits. Em uma implementação sem perdas, o resíduo deve ser representado de forma que o valor original possa ser reconstruído exatamente.

### 3.3 Exemplos de formatos

Alguns formatos conhecidos por oferecerem compressão sem perdas incluem:

- PNG para imagens;
- FLAC para áudio;
- ZIP para arquivos em geral.

A classificação depende do método utilizado e das opções de codificação. Um formato pode admitir diferentes modos de representação; por isso, é importante verificar as características específicas do arquivo e do codec.

### 3.4 Quando utilizar compressão sem perdas?

A compressão sem perdas é apropriada quando a preservação exata dos dados é necessária, como em:

- imagens utilizadas em processamento científico;
- documentos e diagramas;
- arquivos de projeto;
- dados de áudio destinados a edição;
- armazenamento de dados que não podem sofrer alterações;
- etapas intermediárias de processamento.

Em aplicações de processamento de imagens, a compressão sem perdas é especialmente útil quando operações posteriores dependem dos valores exatos dos pixels.

---

## 4. Compressão com perdas (*lossy*)

### 4.1 Conceito

A compressão com perdas reduz o tamanho dos dados eliminando ou aproximando parte da informação original.

Nesse caso:

\[
D(C(X))\neq X
\]

O resultado descomprimido é uma aproximação do original. A diferença pode ser pequena ou perceptível, dependendo do método e dos parâmetros de compressão.

O objetivo não é necessariamente preservar cada valor, mas encontrar um equilíbrio entre tamanho e qualidade.

### 4.2 Quantização

Uma operação frequentemente utilizada em compressão com perdas é a quantização. Ela mapeia vários valores possíveis para um conjunto menor de valores representativos.

Considere uma quantização uniforme simplificada:

\[
Q(x)=\Delta\cdot
\operatorname{round}\left(\frac{x}{\Delta}\right)
\]

onde:

- \(x\) é o valor original;
- \(\Delta\) é o passo de quantização;
- \(Q(x)\) é o valor quantizado.

Quanto maior o passo \(\Delta\), menor tende a ser o número de valores distintos representados. Isso pode reduzir o volume de dados, mas também aumenta o erro de quantização.

O erro é:

\[
e=x-Q(x)
\]

A quantização é um exemplo de operação com perdas porque diferentes valores de entrada podem produzir o mesmo valor de saída.

### 4.3 Compressão de imagens

O JPEG é um exemplo conhecido de compressão de imagens com perdas. Em uma descrição introdutória, seu processo envolve etapas como:

1. transformação do espaço de cor;
2. divisão da imagem em blocos;
3. aplicação de uma transformada, como a Transformada Discreta do Cosseno (DCT);
4. quantização dos coeficientes;
5. codificação dos valores resultantes.

A transformada permite representar padrões de variação da imagem em componentes de frequência. A quantização reduz a precisão de determinados coeficientes, contribuindo para a redução do tamanho.

A compressão JPEG pode produzir artefatos, como blocos visíveis, perda de detalhes finos e alterações em bordas. Esses efeitos tendem a se tornar mais evidentes com compressão mais intensa.

### 4.4 Compressão de áudio

Em áudio, codecs com perdas podem utilizar modelos perceptuais para reduzir a quantidade de informação codificada. O objetivo é preservar uma qualidade percebida aceitável com uma taxa de bits menor.

Formatos como MP3 e AAC são exemplos de tecnologias amplamente utilizadas em áudio comprimido com perdas.

A qualidade final depende de fatores como:

- taxa de bits;
- conteúdo sonoro;
- configuração do codificador;
- implementação do codec;
- condições de reprodução.

Uma taxa de bits maior geralmente permite preservar mais informação, mas não garante, isoladamente, melhor qualidade em todos os casos.

### 4.5 Compressão de vídeo

A compressão de vídeo combina estratégias espaciais e temporais. Além de reduzir redundâncias dentro de cada quadro, pode explorar semelhanças entre quadros consecutivos.

Um codificador pode utilizar:

- quadros codificados de forma independente;
- predição a partir de quadros anteriores;
- predição a partir de quadros posteriores;
- predição bidirecional;
- vetores de movimento;
- codificação de resíduos;
- quantização e codificação estatística.

A estrutura exata depende do codec. O princípio geral é evitar armazenar repetidamente informações que podem ser previstas a partir de dados já disponíveis.

### 4.6 Quando utilizar compressão com perdas?

A compressão com perdas é frequentemente adequada para:

- distribuição de fotografias;
- streaming de áudio;
- videoconferência;
- plataformas de vídeo;
- armazenamento de conteúdo multimídia em dispositivos com espaço limitado.

Ela pode não ser adequada quando a preservação exata dos dados é necessária, ou quando a perda de detalhes compromete análises posteriores.

---

## 5. Taxa de compressão

A taxa de compressão permite comparar o tamanho de um arquivo original com o tamanho de sua representação comprimida.

Se:

- \(S_o\) é o tamanho original;
- \(S_c\) é o tamanho comprimido;

a razão de compressão pode ser definida como:

\[
CR=\frac{S_o}{S_c}
\]

Uma razão maior que 1 indica que o arquivo comprimido é menor que o original.

### 5.1 Exemplo numérico

Considere um arquivo de 12 MB que, após compressão, ocupa 3 MB:

\[
CR=\frac{12}{3}=4
\]

A razão de compressão é 4:1. Isso significa que o arquivo original ocupa quatro vezes o espaço do arquivo comprimido.

A redução percentual de tamanho é:

\[
R=\frac{S_o-S_c}{S_o}\cdot100\%
\]

Substituindo:

\[
R=\frac{12-3}{12}\cdot100\%=75\%
\]

Portanto, o arquivo comprimido ocupa 75% menos espaço que o original.

### 5.2 Atenção às convenções

A expressão “taxa de compressão” pode ser usada com diferentes significados em contextos técnicos. Neste capítulo, a razão \(CR\) é definida como tamanho original dividido pelo tamanho comprimido.

Em sistemas multimídia, também é comum utilizar taxa de bits (*bitrate*), geralmente expressa em bit/s, kbit/s ou Mbit/s. A taxa de bits representa a quantidade de bits transmitidos ou armazenados por unidade de tempo, e não a razão entre tamanhos.

É importante não confundir:

- **razão de compressão:** comparação entre tamanho original e comprimido;
- **redução percentual:** percentual de espaço economizado;
- **taxa de bits:** quantidade de bits por segundo em um fluxo.

---

## 6. Codec versus formato de contêiner

Esta é uma distinção terminológica essencial.

> **Codec não é sinônimo de formato de arquivo.**

### 6.1 O que é um codec?

A palavra *codec* deriva de *coder-decoder* ou *compressor-decompressor*. Um codec é um método ou componente de software/hardware responsável por codificar e decodificar dados de mídia.

Um codec define como os dados de áudio ou vídeo são representados e, quando aplicável, comprimidos e reconstruídos.

Exemplos de codecs incluem:

- H.264/AVC para vídeo;
- H.265/HEVC para vídeo;
- AV1 para vídeo;
- AAC para áudio;
- MP3 para áudio;
- FLAC para áudio sem perdas.

O codec influencia a eficiência da compressão, a qualidade, a complexidade computacional e a compatibilidade.

### 6.2 O que é um contêiner?

Um formato de contêiner organiza e armazena uma ou mais trilhas de mídia, além de metadados e informações de sincronização.

Um contêiner pode reunir:

- uma trilha de vídeo;
- uma ou mais trilhas de áudio;
- legendas;
- metadados;
- informações de tempo e sincronização.

Exemplos de contêineres incluem:

- MP4;
- Matroska (MKV);
- WebM;
- AVI.

O contêiner não determina necessariamente qual codec foi utilizado. Um arquivo MP4, por exemplo, pode conter vídeo codificado com H.264 ou com outros codecs compatíveis com as especificações e implementações utilizadas.

### 6.3 Comparação

| Característica | Codec | Formato de contêiner |
|---|---|---|
| Função principal | Codificar e decodificar mídia | Organizar e armazenar trilhas e metadados |
| Atua sobre | Representação do áudio ou vídeo | Estrutura do arquivo |
| Pode realizar compressão? | Sim, dependendo do codec | Não é, por si só, o método de compressão |
| Exemplos | H.264, AV1, AAC, FLAC | MP4, MKV, WebM, AVI |
| Define sincronização entre trilhas? | Não é sua função principal | Pode armazenar informações de sincronização |

### 6.4 Exemplo aplicado

Considere um arquivo chamado:

```text
aula.mp4
```

A extensão `.mp4` sugere o uso de um contêiner MP4, mas não informa, sozinha, qual codec foi utilizado para o vídeo ou para o áudio.

O vídeo poderia estar codificado com H.264, enquanto o áudio poderia estar codificado com AAC. O contêiner organiza essas trilhas e suas informações associadas.

Consequentemente, alterar apenas a extensão de um arquivo não converte seu conteúdo para outro codec ou contêiner. Para realizar uma conversão, é necessário um processo de remultiplexação (*remuxing*) ou de recodificação (*transcoding*), conforme o caso.

---

## 7. Impacto sobre qualidade, armazenamento e transmissão

A escolha de um método de compressão envolve requisitos que podem entrar em conflito.

### 7.1 Qualidade

Em compressão sem perdas, os dados são reconstruídos exatamente. Em compressão com perdas, a qualidade depende da quantidade e da natureza das informações descartadas.

A qualidade pode ser avaliada por medidas objetivas ou por avaliação perceptual. Uma medida numérica pode indicar diferenças entre sinais, mas nem sempre corresponde diretamente à percepção humana.

### 7.2 Armazenamento

Arquivos menores reduzem o consumo de espaço em discos, servidores e dispositivos móveis. Em grandes sistemas de mídia, essa economia pode representar uma redução significativa de custos.

Entretanto, a compressão pode exigir processamento adicional para codificar e decodificar os dados. O sistema deve considerar tanto o espaço economizado quanto o custo computacional.

### 7.3 Transmissão

Uma taxa de bits menor pode facilitar a transmissão em redes com largura de banda limitada. Em streaming, a taxa de bits deve ser compatível com a capacidade da rede para reduzir interrupções.

Taxas elevadas podem oferecer melhor qualidade, mas exigem mais largura de banda. Sistemas adaptativos podem utilizar diferentes versões de um mesmo conteúdo para se ajustar às condições de conexão.

### 7.4 Latência e complexidade

A compressão também pode afetar a latência. Alguns métodos exigem análise de dados futuros ou processamento complexo, o que pode aumentar o atraso.

Em videoconferência e interação em tempo real, a latência é um requisito importante. Em armazenamento de vídeos para reprodução posterior, pode ser aceitável utilizar métodos de codificação mais demorados, desde que a decodificação seja adequada à reprodução.

---

## 8. Experimento em Python — Comparando compressão de imagens

Neste experimento, será utilizada a biblioteca Pillow para salvar uma imagem em PNG e JPEG e comparar os tamanhos dos arquivos.

O experimento não pretende demonstrar todos os detalhes internos dos codecs. Seu objetivo é observar, de forma prática, como diferentes opções de armazenamento podem afetar o tamanho e a representação dos dados.

### 8.1 Instalação

Se necessário, instale a biblioteca:

```bash
pip install pillow
```

### 8.2 Código

O código cria uma imagem sintética, salva uma versão PNG e uma versão JPEG e calcula a razão de compressão relativa ao tamanho do PNG.

```python
from PIL import Image, ImageDraw
import os

largura = 800
altura = 600

# Cria uma imagem RGB com fundo uniforme.
imagem = Image.new("RGB", (largura, altura), (230, 230, 240))
desenho = ImageDraw.Draw(imagem)

# Adiciona formas e regiões de cor.
desenho.rectangle(
    (100, 100, 350, 300),
    fill=(30, 120, 200)
)

desenho.ellipse(
    (400, 150, 650, 400),
    fill=(220, 80, 60)
)

desenho.line(
    (0, 0, largura - 1, altura - 1),
    fill=(20, 20, 20),
    width=5
)

# Salva em PNG.
imagem.save("imagem.png", format="PNG")

# Salva em JPEG com qualidade definida.
imagem.save(
    "imagem.jpg",
    format="JPEG",
    quality=85,
    optimize=True
)

tamanho_png = os.path.getsize("imagem.png")
tamanho_jpeg = os.path.getsize("imagem.jpg")

print(f"PNG:  {tamanho_png} bytes")
print(f"JPEG: {tamanho_jpeg} bytes")

if tamanho_jpeg > 0:
    razao = tamanho_png / tamanho_jpeg
    reducao = (
        (tamanho_png - tamanho_jpeg)
        / tamanho_png
    ) * 100

    print(f"Razão PNG/JPEG: {razao:.2f}:1")
    print(f"Redução em relação ao PNG: {reducao:.2f}%")
```

### 8.3 Interpretação

O PNG utiliza compressão sem perdas para imagens, enquanto JPEG normalmente utiliza compressão com perdas. Entretanto, o tamanho observado depende do conteúdo da imagem e das opções de codificação.

A imagem sintética possui regiões uniformes e formas geométricas. Esse tipo de conteúdo pode ser particularmente favorável ao PNG. Em fotografias, JPEG frequentemente apresenta uma redução de tamanho maior.

O experimento também evidencia que não é correto afirmar que um formato sempre produzirá um arquivo menor. O resultado depende da imagem, das configurações e do método utilizado.

### 8.4 Experimentos complementares

Modifique o código para:

1. salvar JPEG com `quality=95`;
2. salvar JPEG com `quality=50`;
3. criar uma imagem com ruído aleatório;
4. comparar o PNG e o JPEG em uma fotografia;
5. abrir os arquivos e observar visualmente as diferenças;
6. registrar os tamanhos em uma tabela.

Ao comparar resultados, mantenha a mesma imagem de entrada e altere uma variável por vez. Isso permite atribuir as diferenças observadas à configuração modificada.

---

## 9. Aplicações em Engenharia de Software

A compressão não deve ser tratada apenas como uma operação isolada. Ela faz parte da arquitetura de sistemas que armazenam, processam e transmitem mídia.

### 9.1 Sistemas de armazenamento

Um sistema de gerenciamento de imagens pode armazenar arquivos originais sem perdas e gerar versões menores para visualização. Essa estratégia preserva os dados de origem e permite otimizar o acesso cotidiano.

### 9.2 Aplicações de streaming

Plataformas de streaming precisam selecionar formatos, codecs e taxas de bits compatíveis com os dispositivos e com a rede. O objetivo é manter uma reprodução fluida sem comprometer desnecessariamente a qualidade.

### 9.3 Aplicações móveis

Dispositivos móveis possuem limitações de armazenamento, processamento, energia e conectividade. A escolha do codec pode afetar o consumo de bateria e a capacidade de reprodução.

### 9.4 Processamento e edição

Em aplicações de edição, a recodificação repetida com perdas pode degradar progressivamente o conteúdo. Quando possível, é recomendável preservar uma versão original ou utilizar formatos e fluxos de trabalho apropriados à edição.

### 9.5 Requisitos de software

Ao especificar um sistema multimídia, é importante definir requisitos como:

- tamanho máximo dos arquivos;
- qualidade mínima aceitável;
- codecs e contêineres suportados;
- compatibilidade com dispositivos;
- tempo máximo de codificação e decodificação;
- largura de banda disponível;
- necessidade de preservação exata;
- comportamento diante de arquivos inválidos ou codecs não suportados.

Esses requisitos influenciam decisões de arquitetura, bibliotecas, testes e implantação.

---

## 10. Limitações e problemas

Nenhuma técnica de compressão é ideal para todos os tipos de conteúdo.

A compressão sem perdas preserva os dados, mas pode não alcançar reduções suficientemente grandes para determinados vídeos ou fotografias. A compressão com perdas pode produzir arquivos menores, mas introduz distorções.

Além disso, a compressão e a descompressão podem exigir recursos computacionais. Em sistemas embarcados ou dispositivos móveis, esse custo pode ser relevante.

Outros problemas incluem:

- incompatibilidade entre codecs;
- arquivos com metadados ausentes ou inconsistentes;
- degradação causada por recodificações sucessivas;
- aumento de latência;
- dificuldade de acesso aleatório em alguns formatos de vídeo;
- diferenças entre qualidade objetiva e qualidade percebida;
- arquivos que ocupam mais espaço após uma tentativa de compressão inadequada.

Uma decisão de projeto deve considerar o ciclo de vida completo dos dados: captura, processamento, armazenamento, transmissão, reprodução e eventual edição.

---

## 11. Erros comuns

### Erro 1 — Confundir codec com contêiner

O codec define como a mídia é codificada e decodificada. O contêiner organiza trilhas, metadados e informações de sincronização. São componentes relacionados, mas diferentes.

### Erro 2 — Acreditar que toda compressão reduz o tamanho

Alguns dados são difíceis de comprimir. Certos métodos podem até aumentar o tamanho devido à inclusão de informações auxiliares.

### Erro 3 — Supor que compressão sem perdas significa arquivo sem compressão

Um arquivo pode ser comprimido e ainda preservar exatamente os dados originais. PNG e FLAC são exemplos de formatos associados a compressão sem perdas.

### Erro 4 — Considerar que a extensão converte o arquivo

Renomear `video.avi` para `video.mp4` não transforma automaticamente o conteúdo. A conversão exige procedimentos adequados de remultiplexação ou recodificação.

### Erro 5 — Comparar taxas de compressão sem considerar o conteúdo

Uma fotografia, uma imagem com grandes áreas uniformes e uma imagem com ruído podem produzir resultados muito diferentes com o mesmo método.

### Erro 6 — Avaliar qualidade apenas pelo tamanho

Um arquivo pequeno pode ter qualidade insuficiente para a aplicação. Tamanho, qualidade, latência e compatibilidade devem ser avaliados em conjunto.

---

## Síntese do capítulo

Dados multimídia podem ocupar grandes volumes de armazenamento e exigir taxas elevadas de transmissão. A compressão reduz esse volume ao explorar redundâncias espaciais, temporais, estatísticas ou perceptuais.

A compressão **sem perdas** permite reconstruir os dados originais exatamente. A compressão **com perdas** descarta ou aproxima parte da informação para obter arquivos menores, podendo introduzir distorções.

A razão de compressão compara o tamanho original com o tamanho comprimido. Ela não deve ser confundida com taxa de bits, que representa a quantidade de bits por unidade de tempo.

Um codec é responsável pela codificação e decodificação da mídia. Um contêiner organiza uma ou mais trilhas e seus metadados. Portanto, **codec não é sinônimo de formato de arquivo**.

Em Engenharia de Software, a escolha de compressão deve considerar qualidade, armazenamento, largura de banda, latência, custo computacional, compatibilidade e requisitos de preservação dos dados.

---

## Questões de revisão

1. Por que imagens e vídeos sem compressão podem exigir grandes quantidades de armazenamento?
2. O que é redundância espacial? Apresente um exemplo.
3. O que é redundância temporal em vídeo?
4. Qual é a diferença entre compressão sem perdas e compressão com perdas?
5. Como funciona, de forma geral, a codificação por comprimento de sequência (RLE)?
6. Qual é a ideia fundamental da codificação de Huffman?
7. O que é quantização e por que ela pode introduzir perdas?
8. Como se calcula a razão de compressão?
9. Qual é a diferença entre razão de compressão e taxa de bits?
10. Explique por que codec e contêiner são conceitos diferentes.
11. Por que renomear a extensão de um arquivo não realiza uma conversão de formato?
12. Quais fatores devem ser considerados ao escolher um codec para uma aplicação de streaming?

## Exercícios

### Nível básico

**1. Cálculo de armazenamento**

Uma imagem RGB possui resolução de \(1280\times720\) pixels e 8 bits por canal.

a) Quantos bits são necessários para representar os pixels sem compressão?  
b) Quantos bytes isso representa?  
c) Desconsidere cabeçalhos e metadados.

**2. Razão de compressão**

Um arquivo original possui 20 MB e sua versão comprimida possui 5 MB.

a) Calcule a razão de compressão.  
b) Calcule a redução percentual do tamanho.

**3. Classificação**

Classifique os exemplos a seguir como compressão sem perdas ou com perdas, considerando os usos típicos apresentados:

- PNG;
- JPEG;
- FLAC;
- MP3.

Explique por que a classificação é relevante para uma aplicação de edição de mídia.

### Nível intermediário

**4. Redundância**

Considere os seguintes conteúdos:

- uma imagem de uma parede uniforme;
- uma fotografia de uma floresta;
- um vídeo de uma câmera fixa que registra uma pessoa caminhando.

Identifique formas de redundância que podem ser exploradas em cada caso.

**5. Codec e contêiner**

Um arquivo MP4 contém vídeo H.264 e áudio AAC.

Explique o papel de cada componente e por que o contêiner não deve ser confundido com os codecs.

**6. Avaliação experimental**

Execute o experimento em Python com diferentes valores de qualidade JPEG.

Organize os resultados em uma tabela com:

- qualidade configurada;
- tamanho do arquivo;
- razão de compressão;
- observações visuais.

Descreva a tendência observada e indique se ela foi igual para todos os tipos de imagem testados.

### Nível avançado

**7. Projeto de armazenamento**

Uma aplicação precisa armazenar imagens utilizadas em análises que dependem dos valores exatos dos pixels, mas também disponibilizar versões rápidas para visualização.

Proponha uma estratégia de armazenamento. Justifique a escolha dos formatos e explique como preservar a versão original.

**8. Projeto de transmissão**

Uma aplicação de videoconferência precisa operar em redes com largura de banda variável.

Discuta como a taxa de bits, a qualidade, a latência e a complexidade de codificação podem influenciar a escolha de um codec e a adaptação do fluxo.

**9. Recodificação**

Explique por que salvar repetidamente uma imagem JPEG em diferentes etapas de edição pode degradar sua qualidade. Proponha uma alternativa de fluxo de trabalho.

### Exercício prático

Desenvolva um programa em Python que:

1. carregue uma imagem;
2. gere versões PNG e JPEG;
3. permita configurar a qualidade JPEG;
4. calcule os tamanhos dos arquivos;
5. calcule a razão de compressão e a redução percentual;
6. apresente os resultados em uma tabela;
7. permita comparar visualmente a imagem original e as versões comprimidas.

O programa deve tratar erros como arquivo inexistente, formato não suportado e falha ao salvar. Organize o código em funções para separar carregamento, codificação, cálculo das métricas e apresentação dos resultados.

## Referências

- GONZALEZ, Rafael C.; WOODS, Richard E. *Digital Image Processing*. Pearson.
- SAYOOD, Khalid. *Introduction to Data Compression*. Morgan Kaufmann.
- SALOMON, David; MOTTA, Giovanni. *Handbook of Data Compression*. Springer.
- SZELISKI, Richard. *Computer Vision: Algorithms and Applications*. Springer.
- Documentação oficial da biblioteca Pillow: https://pillow.readthedocs.io/
- Documentação oficial do Python: https://docs.python.org/3/