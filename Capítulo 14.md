# Capítulo 14 — Interação Gráfica

## 14.1 Objetivos de aprendizagem

Ao final deste capítulo, o estudante deverá ser capaz de compreender o papel da interação gráfica em aplicações computacionais, identificar os principais tipos de eventos de entrada e explicar como eles são processados por uma aplicação interativa. Também deverá compreender o funcionamento do *event loop*, distinguir as etapas de atualização do estado e renderização da cena, e implementar interações simples com mouse e teclado utilizando Python.

Espera-se, ainda, que o estudante consiga relacionar os conceitos de interação gráfica com aplicações de computação gráfica, jogos digitais, ferramentas de edição, visualizadores de imagens e sistemas multimídia. A implementação prática deverá servir como instrumento de experimentação e análise dos algoritmos, sem substituir a compreensão dos conceitos que os fundamentam.

## 14.2 Introdução e motivação

Uma aplicação gráfica não é definida apenas pela capacidade de produzir imagens. Em muitos sistemas, o usuário precisa modificar o conteúdo apresentado, selecionar objetos, movimentar elementos, alterar parâmetros e controlar a execução de determinadas operações. A interação gráfica é o conjunto de mecanismos que permite que essas ações sejam recebidas, interpretadas e refletidas na representação visual da aplicação.

Em um visualizador de imagens, por exemplo, o usuário pode ampliar uma região, deslocar a imagem ou selecionar uma ferramenta. Em um programa de modelagem tridimensional, pode movimentar a câmera, selecionar objetos e modificar suas transformações. Em um jogo, os comandos recebidos pelo teclado ou pelo mouse podem alterar a posição de um personagem, a direção de uma câmera ou o estado de uma animação.

Essas ações envolvem uma sequência de processamento. O sistema recebe uma entrada, identifica o evento correspondente, interpreta a intenção do usuário, atualiza o estado interno da aplicação e produz uma nova imagem. Essa sequência ocorre repetidamente durante a execução do programa.

A interação gráfica, portanto, estabelece uma relação direta entre entrada, estado e saída visual. Compreender essa relação é essencial para desenvolver aplicações responsivas, organizadas e previsíveis.

## 14.3 Pré-requisitos

O estudo deste capítulo pressupõe conhecimentos básicos de programação, especialmente variáveis, estruturas condicionais, laços de repetição, funções e estruturas de dados simples. Também é desejável que o estudante tenha compreendido os conceitos de coordenadas, vetores e transformações apresentados nos capítulos anteriores.

A noção de estado de um objeto é particularmente importante. Em uma aplicação gráfica, o estado pode incluir a posição de um objeto, sua orientação, sua escala, sua cor, sua visibilidade ou qualquer outra propriedade que determine como ele será representado.

A interação não modifica necessariamente a imagem de forma direta. Em geral, ela modifica o estado da aplicação, e a imagem é produzida a partir desse estado atualizado.

## 14.4 Conceitos fundamentais de interação gráfica

### 14.4.1 Entrada do usuário e eventos

Uma entrada é uma informação fornecida ao sistema por meio de algum dispositivo. Entre os dispositivos mais comuns estão o mouse, o teclado, a tela sensível ao toque, controles de jogos e dispositivos especializados.

Um evento representa uma ocorrência que pode ser identificada e processada pela aplicação. Por exemplo, pressionar uma tecla, liberar um botão do mouse ou movimentar o cursor são ações que podem gerar eventos.

Os eventos podem ser classificados de acordo com sua origem e natureza. Eventos de teclado incluem pressionamento e liberação de teclas. Eventos de mouse podem incluir movimento, clique, pressionamento de botão e liberação de botão. Outros eventos podem representar o fechamento da janela, a alteração de seu tamanho ou a perda de foco da aplicação.

É importante distinguir o dispositivo de entrada do evento. O mouse é um dispositivo; o movimento do cursor é uma ocorrência; e o evento de movimento é a representação dessa ocorrência que a aplicação recebe para processamento.

### 14.4.2 Eventos discretos e entradas contínuas

Algumas entradas são naturalmente discretas. Um clique, por exemplo, pode ser tratado como uma ocorrência pontual. O pressionamento de uma tecla também pode gerar um evento discreto.

Outras entradas são contínuas ou podem ser interpretadas como contínuas. O movimento do mouse, por exemplo, pode produzir uma sequência de eventos com diferentes posições do cursor. Em uma aplicação de animação ou em um jogo, manter uma tecla pressionada pode ser interpretado como uma condição contínua que afeta o movimento de um objeto ao longo do tempo.

Essa distinção influencia a implementação. Uma ação que deve ocorrer uma única vez pode ser associada ao evento de pressionamento de uma tecla. Já uma ação que deve continuar enquanto a tecla estiver pressionada pode depender da consulta ao estado atual da tecla em cada atualização.

Considere uma aplicação na qual a tecla de seta para a direita move um objeto. Se o movimento for realizado somente no evento de pressionamento, o comportamento poderá depender da repetição automática de teclas configurada no sistema. Se a aplicação consultar continuamente o estado da tecla, poderá controlar o movimento de maneira mais uniforme.

### 14.4.3 Estado da aplicação

O estado da aplicação corresponde ao conjunto de informações necessárias para determinar seu comportamento e sua representação visual em um dado instante.

Em uma aplicação bidimensional simples, o estado pode ser representado pela posição de um objeto:

\[
S = (x,y)
\]

Se o objeto também possuir velocidade, o estado pode ser ampliado:

\[
S = (x,y,v_x,v_y)
\]

Em aplicações mais complexas, o estado pode conter diversos objetos, propriedades de materiais, parâmetros de câmera, seleções, configurações de ferramentas e informações de animação.

Uma interação pode modificar uma ou várias dessas propriedades. Ao clicar em um objeto, por exemplo, a aplicação pode alterar sua condição de seleção. Ao pressionar uma tecla de movimento, pode modificar sua posição. Ao arrastar o mouse, pode modificar a posição de um objeto ou a orientação de uma câmera.

Essa abordagem favorece a separação entre a lógica da aplicação e a representação gráfica. A lógica modifica o estado; o sistema de renderização utiliza esse estado para produzir a imagem.

## 14.5 O ciclo de eventos (*event loop*)

O *event loop* é uma estrutura de repetição responsável por manter uma aplicação interativa em funcionamento. Em cada iteração, o programa verifica os eventos disponíveis, processa as entradas, atualiza o estado e apresenta uma nova imagem.

Uma representação simplificada desse ciclo é:

1. Receber eventos de entrada.
2. Interpretar e processar os eventos.
3. Atualizar o estado da aplicação.
4. Renderizar a cena.
5. Repetir o ciclo enquanto a aplicação estiver ativa.

O ciclo deve continuar até que uma condição de encerramento seja satisfeita, como o recebimento de um evento de fechamento da janela.

A organização do *event loop* é importante porque uma aplicação gráfica precisa responder às entradas sem interromper a apresentação visual. Se o processamento bloquear o ciclo por um período muito longo, a janela poderá deixar de responder ou a animação poderá parecer travada.

### 14.5.1 Processamento de eventos

O processamento de eventos consiste em identificar o tipo de ocorrência recebida e executar a ação correspondente.

Por exemplo:

- Um evento de fechamento pode encerrar a aplicação.
- Um evento de pressionamento de tecla pode modificar uma variável de estado.
- Um evento de clique pode selecionar um objeto.
- Um evento de movimento do mouse pode atualizar a posição de um cursor ou de um elemento gráfico.

O tratamento de eventos costuma ser implementado com estruturas condicionais ou funções específicas associadas a cada tipo de evento.

É recomendável que o processamento seja organizado em pequenas funções, especialmente quando a aplicação apresenta muitas interações. Essa organização facilita a manutenção, os testes e a compreensão do comportamento do sistema.

### 14.5.2 Atualização e renderização

A atualização e a renderização são etapas distintas.

A **atualização** modifica o estado interno da aplicação. Pode incluir o cálculo de uma nova posição, a alteração de uma propriedade, a aplicação de uma regra de movimento ou a avaliação de uma condição de interação.

A **renderização** utiliza o estado atual para produzir a imagem que será apresentada na janela.

Essa separação é importante porque uma aplicação pode atualizar seu estado sem necessariamente modificar todos os elementos visuais. Da mesma forma, a renderização pode ocorrer várias vezes enquanto o estado permanece praticamente inalterado.

Uma arquitetura simples pode ser representada da seguinte forma:

\[
\text{Entrada}
\rightarrow
\text{Processamento}
\rightarrow
\text{Atualização}
\rightarrow
\text{Renderização}
\]

Em sistemas mais sofisticados, a atualização e a renderização podem operar com frequências diferentes. Isso permite, por exemplo, manter a simulação em uma frequência estável enquanto a apresentação visual utiliza a frequência disponível da tela.

## 14.6 Interação com o mouse

O mouse é um dispositivo de entrada especialmente relevante para aplicações gráficas. Ele permite apontar para regiões da janela, selecionar objetos e realizar operações de arrastar e soltar.

A posição do cursor normalmente é representada por coordenadas bidimensionais:

\[
P = (x,y)
\]

Em uma janela gráfica, essas coordenadas geralmente são expressas em pixels. A origem do sistema de coordenadas costuma estar no canto superior esquerdo da área de desenho. O eixo horizontal cresce para a direita e o eixo vertical cresce para baixo.

Esse sistema é diferente do sistema cartesiano frequentemente utilizado em matemática, no qual o eixo vertical cresce para cima. Ao implementar interações, é importante verificar qual convenção está sendo utilizada pela biblioteca gráfica.

### 14.6.1 Seleção de objetos

Uma operação comum é selecionar um objeto por meio de um clique. Para isso, a aplicação precisa determinar se a posição do cursor está dentro da região ocupada pelo objeto.

Considere um objeto retangular definido por:

\[
R = (x_{\min},y_{\min},x_{\max},y_{\max})
\]

Um ponto \(P=(x,y)\) está dentro desse retângulo se:

\[
x_{\min} \leq x \leq x_{\max}
\]

e

\[
y_{\min} \leq y \leq y_{\max}
\]

A verificação pode ser implementada por meio de uma função:

```python
def ponto_no_retangulo(x, y, xmin, ymin, xmax, ymax):
    return (
        xmin <= x <= xmax
        and ymin <= y <= ymax
    )
```

Essa abordagem é adequada para objetos retangulares simples. Em objetos com formas mais complexas, pode ser necessário utilizar testes geométricos específicos, como interseção entre ponto e círculo, ponto e polígono ou técnicas de seleção baseadas em identificadores.

### 14.6.2 Arrastar e soltar (*drag and drop*)

A operação de arrastar e soltar consiste em selecionar um objeto, movimentá-lo enquanto um botão do mouse permanece pressionado e finalizar a operação quando o botão é liberado.

Uma implementação simplificada pode seguir estas etapas:

1. Detectar o pressionamento do botão do mouse.
2. Verificar se o cursor está sobre o objeto.
3. Registrar que o objeto está sendo arrastado.
4. Atualizar sua posição durante o movimento do cursor.
5. Encerrar o arraste quando o botão for liberado.

Para evitar que o objeto “salte” para o centro do cursor ao iniciar o arraste, é comum armazenar o deslocamento entre o cursor e a origem do objeto.

Se a posição inicial do objeto for \(O=(x_o,y_o)\) e a posição inicial do cursor for \(M=(x_m,y_m)\), o deslocamento será:

\[
\Delta x = x_o-x_m
\]

\[
\Delta y = y_o-y_m
\]

Durante o arraste, a nova posição do objeto pode ser calculada por:

\[
x_o'=x_m'+\Delta x
\]

\[
y_o'=y_m'+\Delta y
\]

Esse mecanismo mantém a mesma região do objeto sob o cursor durante toda a operação.

## 14.7 Interação com o teclado

O teclado permite que o usuário forneça comandos por meio de teclas individuais ou combinações de teclas. Em aplicações gráficas, é comum utilizar o teclado para movimentar objetos, selecionar ferramentas, acionar comandos e controlar a execução de animações.

O tratamento do teclado pode considerar eventos de pressionamento e liberação. O primeiro representa o início de uma ação; o segundo representa seu término.

Em uma aplicação que movimenta um objeto, por exemplo, as teclas direcionais podem modificar sua posição. Uma tecla pode também alternar uma propriedade, como a visibilidade de um elemento ou o estado de execução de uma animação.

Uma estratégia comum é manter variáveis booleanas indicando quais teclas estão pressionadas. Durante a atualização, o programa consulta essas variáveis para determinar o movimento.

Considere um objeto com posição \((x,y)\) e velocidade \(v\). Para um intervalo de tempo \(\Delta t\), seu deslocamento horizontal pode ser calculado por:

\[
\Delta x = v\Delta t
\]

Se a tecla de movimento para a direita estiver ativa:

\[
x_{\text{novo}}=x+v\Delta t
\]

O uso de \(\Delta t\) torna o movimento dependente do tempo transcorrido, em vez de depender diretamente do número de quadros renderizados. Isso é importante para reduzir diferenças de comportamento entre máquinas com frequências de atualização distintas.

## 14.8 Experimento em Python: uma aplicação interativa com Pygame

A biblioteca Pygame permite criar janelas, receber eventos de teclado e mouse, desenhar formas e controlar a atualização de aplicações interativas. Ela será utilizada neste experimento para implementar um objeto que pode ser movimentado pelo teclado e selecionado com o mouse.

Antes de executar o exemplo, instale a biblioteca:

```bash
pip install pygame
```

O código a seguir apresenta uma aplicação simples. Um retângulo pode ser movimentado com as teclas direcionais. Ao clicar sobre ele, o objeto é selecionado; ao clicar fora dele, a seleção é removida.

```python
import pygame

pygame.init()

LARGURA = 800
ALTURA = 600

janela = pygame.display.set_mode((LARGURA, ALTURA))
pygame.display.set_caption("Interação Gráfica")

relogio = pygame.time.Clock()

# Estado do objeto
x = 350.0
y = 250.0
largura_objeto = 100
altura_objeto = 80
velocidade = 250.0

selecionado = False
executando = True

while executando:
    # Tempo transcorrido desde o quadro anterior, em segundos
    dt = relogio.tick(60) / 1000.0

    # 1. Processamento de eventos
    for evento in pygame.event.get():
        if evento.type == pygame.QUIT:
            executando = False

        elif evento.type == pygame.MOUSEBUTTONDOWN:
            if evento.button == 1:
                mx, my = evento.pos

                retangulo = pygame.Rect(
                    int(x),
                    int(y),
                    largura_objeto,
                    altura_objeto
                )

                selecionado = retangulo.collidepoint(mx, my)

    # 2. Atualização do estado
    teclas = pygame.key.get_pressed()

    if teclas[pygame.K_LEFT]:
        x -= velocidade * dt

    if teclas[pygame.K_RIGHT]:
        x += velocidade * dt

    if teclas[pygame.K_UP]:
        y -= velocidade * dt

    if teclas[pygame.K_DOWN]:
        y += velocidade * dt

    # Limita o objeto à área da janela
    x = max(0, min(x, LARGURA - largura_objeto))
    y = max(0, min(y, ALTURA - altura_objeto))

    # 3. Renderização
    janela.fill((30, 30, 40))

    cor = (255, 180, 60) if selecionado else (80, 170, 240)

    pygame.draw.rect(
        janela,
        cor,
        (int(x), int(y), largura_objeto, altura_objeto)
    )

    pygame.display.flip()

pygame.quit()
```

### 14.8.1 Análise do experimento

O exemplo apresenta os principais componentes de uma aplicação interativa.

A variável `x` e a variável `y` representam o estado espacial do objeto. A variável `selecionado` representa uma propriedade de interação. Os eventos recebidos pelo programa são processados no laço principal, enquanto as teclas pressionadas são consultadas durante a etapa de atualização.

A chamada `relogio.tick(60)` limita a frequência do laço a aproximadamente 60 iterações por segundo. O valor retornado representa o tempo transcorrido desde a chamada anterior, em milissegundos. A divisão por 1000 converte esse intervalo para segundos.

O movimento é calculado a partir da velocidade e do tempo transcorrido. Dessa forma, o deslocamento é aproximadamente consistente mesmo quando a frequência de execução varia.

A chamada `pygame.display.flip()` apresenta o conteúdo desenhado na janela. Antes dela, a aplicação limpa a área de desenho e representa o objeto conforme seu estado atual.

O experimento também ilustra uma separação conceitual importante: o evento de clique modifica a condição de seleção, enquanto a renderização representa visualmente essa condição por meio da cor do objeto.

## 14.9 Exemplo progressivo: arrastar um objeto com o mouse

O experimento anterior utiliza o mouse para selecionar o objeto. Uma extensão natural consiste em permitir que o objeto seja arrastado.

Para implementar essa funcionalidade, é necessário armazenar se o objeto está sendo arrastado e o deslocamento entre o cursor e a posição do objeto.

O seguinte trecho apresenta a lógica essencial que pode ser incorporada ao laço de eventos:

```python
arrastando = False
offset_x = 0
offset_y = 0
```

No evento de pressionamento do botão esquerdo:

```python
if evento.type == pygame.MOUSEBUTTONDOWN:
    if evento.button == 1:
        mx, my = evento.pos

        retangulo = pygame.Rect(
            int(x),
            int(y),
            largura_objeto,
            altura_objeto
        )

        if retangulo.collidepoint(mx, my):
            arrastando = True
            offset_x = x - mx
            offset_y = y - my
```

Durante o movimento do mouse:

```python
if evento.type == pygame.MOUSEMOTION and arrastando:
    mx, my = evento.pos

    x = mx + offset_x
    y = my + offset_y
```

No evento de liberação do botão:

```python
if evento.type == pygame.MOUSEBUTTONUP:
    if evento.button == 1:
        arrastando = False
```

Essa lógica demonstra como uma interação pode depender de múltiplos eventos relacionados. O pressionamento inicia o arraste, o movimento atualiza a posição e a liberação encerra a operação.

Em uma aplicação completa, a lógica de arraste deve considerar também os limites da janela, a seleção de objetos sobrepostos e a possibilidade de a aplicação perder o foco durante a operação.

## 14.10 Aplicações em sistemas gráficos e multimídia

Os conceitos apresentados neste capítulo são utilizados em diferentes tipos de software.

Em jogos digitais, o *event loop* recebe comandos do jogador, atualiza o estado dos personagens e objetos e produz os quadros da cena. A separação entre entrada, atualização e renderização contribui para organizar a lógica do jogo.

Em ferramentas de modelagem e edição gráfica, o mouse é utilizado para selecionar, movimentar, redimensionar e transformar objetos. O teclado pode acionar ferramentas e comandos específicos. A interação frequentemente envolve operações geométricas, como transformação de coordenadas e testes de interseção.

Em visualizadores de imagens e vídeos, o usuário pode ampliar regiões, deslocar a visualização, controlar a reprodução e selecionar quadros. Essas ações alteram parâmetros de apresentação ou o estado de reprodução do conteúdo.

Em aplicações de visualização de dados, a interação pode permitir a seleção de elementos, a filtragem de informações, a alteração de escalas e a exploração de regiões específicas de uma representação visual.

Em sistemas multimídia, a interação também pode controlar áudio, vídeo, legendas, transições e sincronização entre diferentes componentes. Nesses casos, o estado da aplicação pode incluir tanto informações visuais quanto informações temporais e de reprodução.

## 14.11 Limitações e problemas comuns

Uma aplicação interativa pode apresentar problemas quando o processamento de eventos, a atualização e a renderização não são organizados adequadamente.

Um dos problemas mais comuns é bloquear o *event loop*. Uma operação demorada executada diretamente no laço principal pode impedir que novos eventos sejam processados. Como consequência, a janela pode parecer congelada e o usuário pode não conseguir realizar ações.

Outro problema é associar o movimento diretamente à quantidade de quadros renderizados. Se um objeto avançar um número fixo de pixels por iteração, sua velocidade dependerá da frequência de execução. A utilização do intervalo de tempo \(\Delta t\) permite definir velocidades em unidades por segundo e torna o comportamento mais consistente.

Também é possível que a aplicação interprete incorretamente as coordenadas do mouse. Isso pode ocorrer quando a janela utiliza um sistema de coordenadas diferente daquele empregado pela geometria dos objetos, ou quando a posição do cursor não é convertida corretamente entre coordenadas de tela, janela e mundo.

A seleção de objetos pode apresentar dificuldades quando existem elementos sobrepostos. Nesse caso, a aplicação precisa definir uma regra de prioridade, como selecionar o objeto mais próximo da câmera ou o objeto que aparece na camada superior.

Por fim, o tratamento inadequado da liberação de teclas e botões pode provocar estados persistentes de interação. Por exemplo, um objeto pode continuar sendo arrastado caso a aplicação não processe corretamente o evento de liberação do botão.

## 14.12 Equívocos frequentes

Um equívoco comum é considerar que a renderização, por si só, implementa a interação. A renderização produz a representação visual, mas a interação depende da recepção e do processamento das entradas.

Outro equívoco é tratar todo evento como uma ação contínua. O evento de pressionamento de uma tecla representa uma ocorrência; manter a tecla pressionada é uma condição que pode ser consultada ao longo de várias atualizações.

Também é incorreto assumir que a frequência de renderização é sempre constante. A carga de processamento, o sistema operacional e as características do hardware podem alterar a frequência efetiva de execução.

Por fim, deve-se evitar misturar indiscriminadamente o tratamento de eventos, a lógica de atualização e o desenho da cena. Embora aplicações pequenas possam utilizar um único laço principal, a separação conceitual dessas etapas contribui para uma arquitetura mais clara e facilita a evolução do software.

## 14.13 Síntese do capítulo

A interação gráfica permite que o usuário influencie o comportamento e a representação visual de uma aplicação. Ela é construída a partir de eventos de entrada, do processamento desses eventos e da atualização do estado interno do sistema.

O *event loop* organiza a execução contínua da aplicação. Em cada iteração, os eventos são recebidos e processados, o estado é atualizado e a cena é renderizada. Essa estrutura permite que o sistema responda às ações do usuário e mantenha a apresentação visual atualizada.

O mouse e o teclado fornecem mecanismos complementares de interação. O mouse é especialmente adequado para operações de apontamento, seleção e arraste, enquanto o teclado permite comandos discretos e controle contínuo de movimento.

A utilização de um intervalo de tempo nas atualizações permite definir comportamentos com base no tempo transcorrido, reduzindo a dependência da frequência de renderização. A separação entre entrada, atualização e renderização favorece a organização do código e constitui uma base importante para o desenvolvimento de aplicações gráficas mais complexas.

## 14.14 Questões de revisão

1. O que é um evento em uma aplicação gráfica? Apresente três exemplos.
2. Qual é a diferença entre um evento de pressionamento de tecla e o estado de uma tecla mantida pressionada?
3. Qual é a função do *event loop*?
4. Por que é importante separar atualização e renderização?
5. Como a posição do mouse pode ser representada em uma janela bidimensional?
6. Como uma aplicação pode determinar se um clique ocorreu dentro de um retângulo?
7. Por que o uso de \(\Delta t\) contribui para a consistência do movimento?
8. Quais problemas podem ocorrer quando uma operação demorada bloqueia o *event loop*?
9. Como a lógica de arraste de um objeto pode ser dividida em etapas?
10. Por que a seleção de objetos sobrepostos exige uma regra de prioridade?

## 14.15 Exercícios

### Exercícios básicos

**Exercício 1 — Movimento por teclado**

Modifique o experimento principal para permitir que o objeto seja movimentado pelas teclas direcionais. Explique como a posição é atualizada e por que o intervalo de tempo é utilizado.

**Exercício 2 — Seleção visual**

Implemente uma aplicação na qual um clique sobre um retângulo altera sua cor. Um novo clique sobre o objeto deve alternar novamente a condição de seleção.

**Exercício 3 — Limites da janela**

Adapte o programa para impedir que o objeto ultrapasse os limites da área de desenho. Explique como as dimensões do objeto influenciam os valores máximos de `x` e `y`.

### Exercícios intermediários

**Exercício 4 — Arraste com deslocamento**

Implemente a funcionalidade de arrastar e soltar. O objeto deve permanecer na mesma posição relativa ao cursor durante o movimento. Explique a função das variáveis de deslocamento.

**Exercício 5 — Múltiplos objetos**

Crie uma aplicação com pelo menos três objetos retangulares. O usuário deverá conseguir selecionar e movimentar cada um deles. Defina uma regra para o caso em que dois objetos se sobrepõem.

**Exercício 6 — Controle de velocidade**

Adicione um mecanismo para alterar a velocidade do objeto por meio de teclas. A velocidade deve ser representada em unidades por segundo, e o movimento deve continuar utilizando o intervalo de tempo.

### Exercícios avançados

**Exercício 7 — Conversão de coordenadas**

Implemente uma aplicação na qual a posição do mouse seja expressa inicialmente em coordenadas da janela e depois convertida para um sistema de coordenadas de mundo. Explique em quais situações essa conversão é necessária.

**Exercício 8 — Separação de responsabilidades**

Reorganize o experimento principal em funções distintas para processamento de eventos, atualização do estado e renderização. Explique como essa organização favorece a manutenção e a testabilidade do código.

**Exercício 9 — Interação com objetos sobrepostos**

Implemente uma regra de seleção para objetos sobrepostos. A aplicação deve selecionar o objeto que possui prioridade visual. Descreva como essa prioridade é determinada.

### Atividade prática integradora

**Exercício 10 — Editor gráfico bidimensional simples**

Desenvolva uma aplicação que permita criar, selecionar e movimentar objetos retangulares em uma janela. O sistema deverá apresentar, no mínimo:

- Criação de objetos por meio de uma interação definida pelo estudante.
- Seleção de objetos com o mouse.
- Movimento de objetos por arraste.
- Movimento de um objeto selecionado por meio do teclado.
- Restrição dos objetos aos limites da janela.
- Indicação visual do objeto selecionado.
- Encerramento adequado da aplicação.
- Separação entre processamento de eventos, atualização e renderização.

O relatório da atividade deverá apresentar a arquitetura adotada, explicar o funcionamento do *event loop* e descrever como o estado da aplicação é modificado pelas entradas do usuário. Também deverá discutir pelo menos dois problemas encontrados durante a implementação e as soluções utilizadas.

## 14.16 Referências

As referências a seguir oferecem fundamentos para o estudo de computação gráfica, interação, programação gráfica e sistemas multimídia. A utilização de cada obra deve ser relacionada aos conceitos efetivamente consultados na elaboração ou no aprofundamento do capítulo.

- FOLEY, James D. et al. *Computer Graphics: Principles and Practice*. 3. ed. Addison-Wesley, 2013.
- HUGHES, John F. et al. *Computer Graphics: Principles and Practice*. 3. ed. Addison-Wesley, 2013.
- PARENT, Rick. *Computer Animation: Algorithms and Techniques*. 3. ed. Morgan Kaufmann, 2012.
- PYGAME. *Pygame Documentation*. Documentação oficial da biblioteca Pygame.
- PYTHON SOFTWARE FOUNDATION. *Python Documentation*. Documentação oficial da linguagem Python.