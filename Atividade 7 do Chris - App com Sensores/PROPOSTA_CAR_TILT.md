# Car Tilt — Miniapp de corrida com sensores
Christian Hollnagel
## Conceito
Interlagos Tilt é um jogo de corrida retrô para smartphone Android em que o celular vira o volante. O jogador segura o aparelho deitado, com as duas mãos, e conduz um carrinho visto de cima por uma pista com o traçado do Autódromo de Interlagos. Girar o celular para os lados faz o carro virar. Inclinar para a frente acelera e inclinar para trás freia.
O objetivo é completar uma volta no menor tempo possível sem sair da pista. Quem sai da pista perde velocidade na grama, e o celular vibra a cada saída. Não há botões na tela durante a corrida: todo o controle vem do sensor de orientação.
O app roda direto no navegador do celular, a partir de um link, sem instalação. É um único arquivo HTML, publicado no Netlify Drop ou no Vercel Drop.
## Origem na Atividade 7
A proposta parte da lista “2.000 ideias de miniapps com sensores”, da disciplina Design de Produtos com Tecnologia Embutida. A lista é formada por cerca de 200 padrões de interação (sensor + gesto), cada um repetido com resultados diferentes nos 25 temas.
O jogo usa o padrão de “corredor conduzido por inclinação”, presente no tema Corpo em movimento, dança e recreação:
- **1369** — Ao inclinar o aparelho num jogo de precisão, controle uma prancha virtual e atravesse uma onda desenhada.
- **1409** — Ao inclinar o aparelho num jogo de precisão, mantenha uma pipa virtual dentro do corredor de vento.

O carro em Interlagos mantém a mesma interação dessas ideias e troca apenas o contexto: a pipa no corredor de vento vira um carro numa pista real.
Entre as ideias da lista, o padrão está no grupo de complexidade baixa a média. A leitura do sensor é direta, e o trabalho fica na física do carro e no desenho da pista.
## Experiência do jogador
Do link à primeira volta, o fluxo tem seis passos e cabe em menos de um minuto.
1. **Abrir o link** no Chrome do celular. A tela inicial mostra o nome do jogo, um botão Jogar e a instrução “segure o celular deitado, como um volante”.
2. **Tocar em Jogar.** O app entra em tela cheia, trava a tela na horizontal e impede que ela apague durante a corrida.
3. **Calibrar.** Uma contagem de 3 segundos pede que o jogador segure o celular na posição confortável. Essa posição vira o “reto” e o “neutro” do acelerador.
4. **Largada.** O carro parte da linha de chegada, na Reta dos Boxes, em sentido anti-horário.
5. **Volta.** A câmera acompanha o carro. No topo ficam o tempo e a velocidade, e no canto um minimapa mostra a posição no circuito. Ao passar por cada curva, o nome dela aparece rapidamente na tela.
6. **Resultado.** Ao cruzar a linha, o jogo mostra o tempo da volta e o melhor tempo do aparelho, com botões para correr de novo ou recalibrar.

O melhor tempo fica salvo só no navegador daquele celular. Não há ranking online nem cadastro.
## Sensores e mapeamento
O jogo depende de um sensor obrigatório, a orientação. A vibração é uma saída de retorno, e o acelerômetro é um complemento opcional.

|Recurso|Papel|Gesto ou evento|Efeito no jogo|
|---|---|---|---|
|Orientação|Obrigatório|Girar o celular para os lados, como volante|Carro vira para a esquerda ou a direita|
|Orientação|Obrigatório|Inclinar para a frente ou para trás|Acelera ou freia, de forma proporcional|
|Vibração|Saída|Carro sai da pista|Pulso curto de vibração|
|Acelerômetro|Opcional|Sacudida rápida|Buzina ou “conserto” depois de uma saída|
Três sensores da lista ficaram de fora por decisão de projeto:
- **Microfone:** retirado para evitar o pedido de permissão e a interferência do barulho da sala.
- **Câmera:** não acrescenta nada ao controle e exigiria processamento de imagem.
- **Localização:** o jogo acontece parado, e o GPS não funciona bem dentro da sala.

## Tratamento do sinal
O sensor de orientação é ruidoso e muda conforme a pessoa segura o aparelho. Quatro cuidados transformam a leitura bruta em um controle confortável:
- **Calibração:** a posição registrada no início vira o zero. O “reto” é o jeito como o jogador segura, não o celular perfeitamente nivelado.
- **Zona morta:** alguns graus de folga em torno do zero, para o carro não tremer quando a mão oscila.
- **Filtro de suavização:** cada leitura nova se mistura à anterior (filtro passa-baixa), o que dá resposta suave sem atraso perceptível.
- **Curva de sensibilidade:** giros pequenos dão ajustes finos e giros grandes dão curvas fechadas. Um limite máximo de ângulo evita que o jogador precise virar demais o pulso.

Com o celular na horizontal, os eixos do sensor trocam de papel. O giro de volante passa a ser lido no eixo que, em retrato, mede a inclinação para a frente. O sinal se inverte conforme o lado para o qual a tela foi girada. O app consulta a orientação da tela e corrige isso automaticamente.
## A pista: Interlagos
A pista reproduz o traçado do Autódromo José Carlos Pace, em São Paulo: cerca de 4,3 km, percorridos em sentido anti-horário. A sequência de trechos que aparece na tela é:
Reta dos Boxes → S do Senna → Curva do Sol → Reta Oposta → Descida do Lago → Ferradura → Laranjinha → Pinheirinho → Bico de Pato → Mergulho → Junção → Subida dos Boxes.
O traçado combina bem com o controle por inclinação:
- **Retas longas** (Reta Oposta e Subida dos Boxes): o jogador inclina para a frente e sente a velocidade.
- **Miolo travado** (Pinheirinho e Bico de Pato): exige frear e girar bastante o celular, ou seja, usar o sensor por inteiro.
- **S do Senna** logo após a largada: testa de cara se a calibração ficou boa.

A geometria vem do OpenStreetMap, e não é desenhada de memória, para o formato sair fiel. Os pontos do traçado são convertidos para coordenadas da tela e suavizados. O rodapé do jogo dá o crédito exigido pela licença: “© colaboradores do OpenStreetMap”. O jogo não usa logotipo do autódromo, marcas de categorias de corrida nem nomes de pilotos além do nome oficial da curva.
## Design visual
O visual é deliberadamente simples, para o esforço ficar no controle. Tudo é desenhado por código, sem imagens, sprites ou texturas.
- **Pista:** uma faixa cinza grossa sobre fundo verde liso, com bordas mais claras nas curvas e uma linha branca de largada e chegada.
- **Carro:** um retângulo colorido com uma marca indicando a frente.
- **Câmera:** acompanha o carro, que fica sempre no centro da tela. A pista inteira não caberia legível numa tela de celular.
- **Minimapa:** o contorno completo do circuito num canto da tela, com um ponto marcando o carro.
- **Texto:** tempo, velocidade e nome da curva em fonte grande e contrastada, legível com o celular nas mãos e o braço em movimento.

A estética retrô vem da paleta curta (verde, cinza, branco e uma cor de destaque para o carro) e das formas geométricas, não de efeitos.
## Arquitetura técnica
O app é um único arquivo HTML com o jogo em JavaScript puro e desenho em Canvas 2D. Não usa frameworks nem bibliotecas externas, e o traçado da pista vai embutido no próprio arquivo.

|Recurso do navegador|Uso no jogo|
|---|---|
|Evento de orientação do aparelho|Leitura contínua do giro e da inclinação|
|Movimento do aparelho (opcional)|Detecção da sacudida|
|Vibração|Retorno ao sair da pista|
|Tela cheia + trava de orientação|Manter a tela na horizontal|
|Wake Lock|Impedir que a tela apague durante a corrida|
|Armazenamento local|Guardar o melhor tempo do aparelho|
Os sensores só funcionam em HTTPS, o que o Netlify Drop e o Vercel Drop já fornecem. No Chrome do Android, o sensor de orientação não pede permissão ao usuário, ao contrário do iPhone.
O desenvolvimento acontece num tablet Lenovo com Android, usando Termux e o editor Acode. O tablet serve para escrever e publicar. Os testes de controle são feitos no smartphone Android, abrindo o link publicado, porque é o aparelho e o tamanho de tela para os quais o jogo foi pensado.
## Riscos e plano B

|Risco|Como aparece|Resposta|
|---|---|---|
|Sensor não responde|Carro parado ao inclinar o celular|Botões de toque na tela (esquerda, direita, acelerar), ativados automaticamente se nenhum dado chegar em 2 segundos|
|Calibração ruim|Carro puxa para um lado|Botão Recalibrar sempre visível na pausa|
|Controle sensível demais|Carro zigue-zagueando nas retas|Ajuste de sensibilidade em três níveis na tela inicial|
|Tela apaga ou gira|Corrida interrompida|Tela cheia, trava na horizontal e Wake Lock ao tocar em Jogar|
|Link aberto fora do HTTPS|Sensores bloqueados pelo navegador|Aviso na tela pedindo para abrir pelo link publicado|
Um modo de demonstração, com o carro dando a volta sozinho, serve de reserva caso o celular falhe na hora da apresentação.
## Apresentação e questões em aberto
A demonstração cabe em três minutos:
1. **Contexto (30 s):** o padrão das ideias 1369 e 1409 e a troca da pipa pelo carro em Interlagos.
2. **Calibração ao vivo (30 s):** mostrar que o “reto” é a posição de quem segura.
3. **Volta completa (90 s):** com a tela do celular espelhada no projetor, para a turma ver o carro e o nome das curvas.
4. **Convite (30 s):** passar o celular para um colega dar uma volta. É o momento que mostra que o controle é intuitivo.

Antes da entrega, ficam três pontos para decidir:
- Manter ou não o acelerômetro (sacudida para buzina ou conserto).
- Confirmar se o professor aceita uma adaptação das ideias 1369 e 1409, em vez de uma ideia literal da lista.
- Testar o espelhamento da tela do celular no projetor da sala.
