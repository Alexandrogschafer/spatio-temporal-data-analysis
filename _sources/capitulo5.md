---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.16.4
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---



# 5. MODELOS ESPAÇO-TEMPORAIS


<section class="cap-audio">
  <p><strong>Ouça o resumo do capítulo em áudio:</strong></p>
  <audio controls preload="none" style="width:100%;max-width:720px">
    <source src="audio/capitulo5.mp3" type="audio/mpeg">
    Seu navegador não suporta a reprodução de áudio.
    <a href="audio/capitulo5.mp3">Baixar MP3</a>
  </audio>
</section>


<section class="cap-video">
  <p><strong>Assista ao resumo do capítulo em vídeo:</strong></p>

  <!-- wrapper responsivo 16:9 -->
  <div style="position:relative;padding-bottom:56.25%;height:0;overflow:hidden;max-width:100%;border-radius:8px;background:#000;">
    <iframe
      src="https://www.youtube-nocookie.com/embed/dtBbKE8FRXQ"
      title="Vídeo do capítulo"
      allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
      allowfullscreen
      loading="lazy"
      referrerpolicy="strict-origin-when-cross-origin"
      style="position:absolute;top:0;left:0;width:100%;height:100%;border:0;">
    </iframe>
  </div>

  <p style="margin-top:0.5rem;">
    Se o vídeo não carregar, <a href="https://www.youtube.com/watch?v=dtBbKE8FRXQ" target="_blank" rel="noopener">assista no YouTube</a>.
  </p>
</section>




Ler mapas e séries separadamente conta só metade da história: mapas dizem onde diferem; séries mostram como evoluem. Como os fenômenos acontecem no espaço e no tempo ao mesmo tempo, modelar no espaço-tempo é transformar essa dupla dependência em evidência mensurável para responder, com clareza e margem de erro, perguntas práticas como: onde e quando o risco aumenta, quanto um fator explica e o que esperar adiante — e com quanta incerteza.

O ponto de partida costuma ser visual: contrastes no mapa, tendências na série, indícios de aglomeração. A passagem do “parece” para o “é” ocorre quando formulamos a pergunta de modo mensurável (unidade de análise, janela temporal, vizinhança espacial, tipo de entrega) e tornamos explícitas as estruturas que o olho intui. Isso implica escolher o objeto adequado — um campo contínuo $Y(\mathbf{s}, t)$, uma intensidade de eventos $\lambda(\mathbf{x}, t)$ ou um indicador por área e período $Y_{it}$ — e representar como a semelhança decai com a distância no espaço e no tempo (covariância/semivariograma, matriz de vizinhança $W$, intensidade condicional). Em seguida, ligamos explicações a medidas por meio de covariáveis $X(\mathbf{s}, t)$, separando o que é nível (tendência, sazonalidade, gradientes) do que é vizinhança (correlação residual). O resultado desse encadeamento são quantidades estimáveis: alcances espaciais, defasagens temporais, forças de propagação, efeitos com sinal e magnitude — sempre acompanhados de barra de incerteza.

Estimar é só metade do trabalho; a outra metade é quantificar o erro e testar a robustez. Modelos E-T sempre extrapolam um pouco (no espaço e/ou no tempo), por isso intervalos/variâncias preditivas, probabilidades de excedência e, quando há rede de vizinhanças, impactos direto/indireto/total fazem parte do produto, não do apêndice. A validação precisa respeitar espaço e tempo (bloqueios espaciais/temporais que evitam proximidade entre treino e teste) e vir acompanhada de análises de sensibilidade a escolhas substantivas: escala, definição de vizinhança ($W$), transformação e seleção de covariáveis. Dizer o que o modelo capta e o que não capta é requisito, não cortesia.

Evidência só vira prática quando informa escolhas. Métricas do modelo se traduzem em gatilhos operacionais — valores ou probabilidades que separam “atuar” de “monitorar” de acordo com custos de erro e tolerância ao risco. Mapas e séries priorizam onde e quando agir, organizam cenários (timing, intensidade, cobertura) e deixam claro o trade-off entre benefício esperado e recursos. Indicadores precisam caber na rotina (poucos, estáveis, interpretáveis) e estar acoplados a planos de resposta (quem, como, quando, com quais recursos). Depois, compara-se o previsto ao observado, recalibram-se limiares e parâmetros: decisão espaço-temporal é ciclo de aprendizado, não ato único. Transparência fecha o circuito — dados, versões de modelo, parâmetros e validação documentados; vieses e efeitos colaterais inspecionados; proporcionalidade assegurada.

Nem sempre vale modelar já. Há sinal quando vemos semelhança entre vizinhos e persistência temporal, quando existem lacunas relevantes a preencher, quando precisamos medir efeitos ou antecipar cenários de curto/médio prazo. Falta sinal quando tudo se comporta como ruído branco, quando a cobertura é tão desigual que inviabiliza validação ou quando há não-estacionariedade forte sem estratégia para tratá-la (covariáveis, transformações, modelos locais). A regra de bolso é simples: se o fenômeno “se parece consigo mesmo” em vizinhanças espaciais e temporais, há material para modelar; se não, o melhor investimento costuma estar na escala de análise, na qualidade dos dados e nas explicativas.

Ao longo do capítulo, “modelagem E-T” funciona como guarda-chuva para três famílias, escolhidas conforme o tipo de dado e a pergunta de interesse: geoestatística para campos contínuos (krigagem E-T, §5.1.1), processos pontuais para eventos no espaço-tempo (STEP, §5.1.2) e econometria E-T em áreas para painéis areais com dependência espacial e dinâmica temporal (§5.1.3), além de técnicas de agrupamento espaço-temporal usadas como diagnóstico estruturado (§5.1.4). A anatomia mínima comum — resposta, explicativas, dependências e ruído — é detalhada nas subseções; aqui, fica a motivação: transformar indícios em evidência quantificada, e evidência em decisão responsável.



## 5.1 Tipos de Modelos Espaço-Temporais

Depois de entender por que modelar, seguimos para como escolher a lente adequada para o seu problema. Nesta seção, vamos preparar o terreno para quatro caminhos que você encontrará com frequência na prática. A ideia é simples: cada caminho parte do mesmo fenômeno, mas o observa por um ângulo diferente, de acordo com o tipo de dado disponível e com a pergunta que você quer responder. Vale lembrar que, na organização geral do capítulo, trabalhamos com três famílias de modelos (campos contínuos, eventos e painéis areais); a clusterização não é uma quarta família, e sim um conjunto de técnicas transversais que aparece dentro dessas três. Por ser muito usada na prática, dedicamos a ela uma subseção específica (§ 5.1.4) para orientar quando aplicar e como interpretar seus resultados em cada tipo de dado.

Quando o interesse é estimar valores contínuos em locais e momentos não observados — como mapear chuva, temperatura ou poluição ao longo do ano — a krigagem espaço-temporal (§ 5.1.1) é a lente apropriada: ela combina informação de vizinhos no espaço e no tempo para produzir superfícies e suas incertezas. Se os dados não são medidas contínuas, mas sim ocorrências em coordenadas e instantes (casos de dengue, crimes, raios), passamos a pensar em processos pontuais espaço-temporais (§ 5.1.2), que descrevem a intensidade dos eventos, verificam aglomerações e ajudam a entender quando e onde o risco aumenta.

Há situações em que trabalhamos com indicadores agregados por áreas repetidas no tempo — bairros, municípios, setores. Nesses casos, a regressão em painel com dependência espacial (§ 5.1.3) permite explicar variações considerando a interação entre áreas vizinhas e a memória temporal, sendo útil para avaliar efeitos de políticas e medir possíveis transbordamentos. Por fim, quando a pergunta principal é identificar concentrações anômalas no espaço e no tempo — hotspots persistentes ou surtos de curta duração — recorremos ao agrupamento espaço-temporal (§ 5.1.4), que destaca padrões locais de interesse e orienta a priorização de ações.


### 5.1.1 Krigagem Espaço-Temporal 

Krigagem espaço-temporal é a técnica usada para “completar” o mapa e a linha do tempo quando não há medições em todos os lugares e momentos. Pense em uma cidade com poucas estações de chuva e leituras esparsas ao longo do dia: ainda assim, queremos saber quanta chuva caiu em qualquer ponto e a qualquer instante. A ideia é simples e poderosa: aproveitar que lugares próximos tendem a ter valores parecidos e que momentos próximos costumam se assemelhar. A krigagem combina essas duas proximidades para estimar o valor onde não medimos e, tão importante quanto, informar quão incerta é essa estimativa.

Na prática, isso permite mapear fenômenos contínuos — chuva, temperatura, poluição do ar, umidade do solo — mesmo com monitoramento limitado. Com as estimativas, é possível gerar mapas por data, extrair séries para locais específicos, comparar áreas, calcular a probabilidade de ultrapassar limites regulatórios (como padrões de qualidade do ar) e, assim, tomar decisões fundamentadas mesmo com poucos sensores. Em termos intuitivos, cada medição “empresta” informação para perto, tanto no espaço quanto no tempo; a krigagem organiza esse empréstimo de modo ótimo e transparente, entregando o valor estimado junto com a sua incerteza.

**a) Como funciona**  
A krigagem parte de uma decomposição simples: o que observamos em cada lugar e instante é a soma de um comportamento médio e de uma variação local em torno desse médio:

$$
Z(\mathbf{s}, t) = m(\mathbf{s}, t) + \varepsilon(\mathbf{s}, t),
$$

em que $m(\mathbf{s}, t)$ representa a tendência (por exemplo, altitudes maiores chovem mais; o verão é mais úmido) e $\varepsilon(\mathbf{s}, t)$ é o desvio que muda de ponto para ponto e de momento para momento.


O passo decisivo é descrever a semelhança entre desvios. Para isso usamos a função de covariância $C(\mathbf{h}, u)$, que mede o quanto dois pontos se parecem quando estão separados por uma distância espacial $\mathbf{h}$ e uma separação temporal $u$. Em termos práticos: quanto menores $\mathbf{h}$ e $u$, maior a semelhança esperada.


Com essa “régua de semelhança” em mãos, a krigagem calcula, para um local/instante sem dado, uma combinação ponderada das observações disponíveis, atribuindo pesos maiores aos vizinhos mais próximos no espaço e no tempo e menores aos mais distantes. O método entrega duas saídas acopladas: o valor estimado e a sua incerteza (a variância de krigagem), que indica o quão confiável é a estimativa naquele ponto do mapa e naquele momento.

Uma intuição útil: imagine “pintar” o mapa-linha-do-tempo usando as cores conhecidas nos pontos medidos e espalhando essas cores ao redor; elas se difundem com força decrescente à medida que nos afastamos no espaço e no tempo. Ao final, cada pixel/instante recebe uma cor (o valor estimado) e um sombreamento de confiança (a incerteza), deixando claro o que sabemos melhor e onde sabemos menos.

**b) Essência matemática**  
Em krigagem, o alvo é um campo espaço-temporal: um processo aleatório contínuo $Z(\mathbf{s}, t)$ que atribui um valor (chuva, temperatura, PM$_{2.5}$, umidade do solo) a cada ponto do espaço $\mathbf{s}$ e a cada instante $t$. As medições de estações ou sensores são amostras pontuais desse campo; ele existe também onde/quando não medimos. Supomos uma suavidade prática: vizinhos no espaço e no tempo tendem a se parecer. É essa semelhança local que a krigagem explora para preencher lacunas e quantificar incerteza.


Com o objeto definido, descrevemos o campo por:

$$
Z(\mathbf{s}, t) = m(\mathbf{s}, t) + \varepsilon(\mathbf{s}, t),
$$

onde:
- $Z(\mathbf{s}, t)$: valor do campo no local $\mathbf{s}$ e no tempo $t$;
- $m(\mathbf{s}, t)$: tendência média (nível), possivelmente função de covariáveis, por exemplo $m(\mathbf{s}, t)=\mathbf{x}(\mathbf{s}, t)^\top \beta$;
- $\varepsilon(\mathbf{s}, t)$: variação aleatória correlacionada, com média zero.


A proximidade no espaço e no tempo é codificada por uma função de covariância:

$$
C(\mathbf{h}, u) = \mathrm{Cov}\!\big[ Z(\mathbf{s}+\mathbf{h}, t+u),\, Z(\mathbf{s}, t) \big],
$$

em que $\mathbf{h}$ é o deslocamento espacial e $u$ o deslocamento temporal. Quanto menores $\mathbf{h}$ e $u$, maior a semelhança esperada; conforme aumentam, a semelhança decai.


Um equivalente diagnóstico é o semivariograma:

$$
\gamma(\mathbf{h}, u) = \tfrac12\,\mathrm{Var}\!\big[ Z(\mathbf{s}+\mathbf{h}, t+u) - Z(\mathbf{s}, t) \big],
$$

que tende a ser baixo para pares próximos e alto para pares distantes. Na prática, ajustamos um modelo válido para $C(\mathbf{h}, u)$ (ou $\gamma$) — separável ou não separável — que servirá de régua para ponderar vizinhos.


**Como a predição é calculada**  
Para um alvo \((\mathbf{s}_0, t_0)\) sem medição, a krigagem combina, com pesos, as observações disponíveis:

$$
\widehat{Z}(\mathbf{s}_0, t_0)
= m(\mathbf{s}_0, t_0) + \sum_{i=1}^{n} w_i \,\big[ Z(\mathbf{s}_i, t_i) - m(\mathbf{s}_i, t_i) \big].
$$

Os pesos $w_i$ são escolhidos para que a estimativa seja não viesada e tenha mínima variância, conforme a régua $C(\mathbf{h}, u)$. Intuição direta: pontos próximos de $(\mathbf{s}_0, t_0)$ (no mapa e no relógio) recebem peso maior; pontos distantes, peso menor.


A predição vem acompanhada da variância de krigagem $\sigma_K^2(\mathbf{s}_0, t_0)$, que expressa a incerteza local — maior quando faltam vizinhos próximos, quando a correlação decai muito rápido ou quando a tendência $m(\mathbf{s}, t)$ explica pouco.


**Passo a passo mínimo**  
- Especificar a tendência $m(\mathbf{s}, t)$ (constante ou com covariáveis) e diagnosticar a dependência com variogramas/correlogramas E–T.  
- Ajustar um modelo válido para $C(\mathbf{h}, u)$ (a régua de semelhança espacial e temporal).  
- Predizer $\widehat{Z}$ em locais/tempos sem medição e reportar $\sigma_K^2$ (bandas/intervalos), deixando claro onde sabemos mais e onde sabemos menos.


**c) Aplicações comuns**  
A krigagem espaço-temporal mostra seu valor quando o fenômeno é contínuo, o monitoramento é esparso e precisamos preencher onde e quando faltam medições — sempre com uma noção clara da incerteza. Exemplos:
- Meteorologia e clima: preencher chuva/temperatura entre estações, montar mapas diários/horários.  
- Qualidade do ar: estimar PM$_{2.5}$ onde não há sensor, indicar probabilidade de ultrapassar limites.  
- Agricultura e solos: umidade do solo, índice de vegetação, salinidade ao longo de safras.  
- Recursos hídricos: níveis ou qualidade de água em bacias com poucos pontos de medição.  
- Saúde ambiental: concentração de alérgenos ou poluentes ao longo do dia na malha urbana.


**d) O que você obtém**  
- Mapas por tempo (ex.: mapa do dia 10/01 às 12h) e séries por local (ex.: valores diários no bairro X), ambos com bandas de incerteza.  
- Mapas de excedência: onde e quando é provável que um valor tenha passado do limite (por exemplo, 50 $\mu$g/m$^3$).  
- Resumo da incerteza: locais/horários com pouca confiança ficam sinalizados; decisões podem priorizar áreas de maior certeza.


**e) Limitações e cuidados**  
- Escala do fenômeno: a krigagem funciona melhor quando o processo é suave no espaço e no tempo; quebras bruscas (relevo muito complexo, frentes rápidas) pedem modelos mais sofisticados.  
- Dados e relógio organizados: unidades consistentes, calendário alinhado (fuso/horário de verão) e coordenadas corretas são essenciais para que distâncias e defasagens façam sentido.  
- Tendência versus vizinhança: gradientes (altitude, sazonalidade) entram em $m(\mathbf{s}, t)$; a covariância fica para a semelhança entre vizinhos. Misturar os dois distorce a dependência.  
- Evitar confiança exagerada: validações “aleatórias” que misturam pontos muito próximos entre treino e teste inflacionam o desempenho. Preferir bloqueios separados no espaço e no tempo.  
- Custo computacional: em bases grandes, estimar a semelhança entre todos os pares é pesado; usar aproximações (matrizes esparsas, vizinhos mais próximos, janelas móveis) quando necessário.


**f) Abordagens de referência**  
- Krigagem E–T clássica (covariância separável / não separável): ajusta um processo gaussiano com estrutura de correlação no espaço e no tempo; a versão não separável (por exemplo, família de Gneiting) captura propagação. Entrega mapas/séries preditos e variâncias de krigagem, além de mapas de probabilidade de excedência.  
- Krigagem com tendência/regressão (Universal/Regression Kriging E–T): inclui covariáveis $X(\mathbf{s}, t)$ na tendência $m(\mathbf{s}, t)$ + krigagem do resíduo correlacionado. Entrega predições com efeitos de covariáveis e incerteza.  
- Co-krigagem / krigagem colocalizada E–T: usa variáveis correlatas (por exemplo, satélite) para reforçar a predição de uma variável-alvo esparsa. Entrega ganho de precisão em áreas pouco medidas.  
- SPDE/GMRF via INLA (campos gaussianos esparsos): aproxima o processo contínuo por malhas triangulares (GMRF), escalando para grades grandes. Entrega predições rápidas com intervalos de credibilidade.  
- Filtro/“krigagem” de Kalman em grade (estado-espaço): para campos em grade (raster) com dinâmica temporal explícita (advecção/difusão). Entrega *nowcasting/forecasting* com bandas preditivas.




### 5.1.2 Processos Pontuais Espaço-Temporais (STEP) 

Processos Pontuais Espaço-Temporais (STEP) lidam com dados em que cada registro é um evento com lugar e tempo — um ponto no mapa e um instante no relógio. O interesse não é medir “quanto há” em toda a área (como numa variável contínua), e sim compreender o desenho dos pontos: onde e quando eles se acumulam, como se espalham, se atraem (um evento eleva a chance de outro nas proximidades pouco depois) ou se inibem (a ocorrência reduz a probabilidade de novos eventos ao redor por algum tempo). Em termos práticos, STEP transforma uma nuvem de ocorrências — crimes, casos de dengue, raios, panes — em um mapa-calendário de risco que varia no espaço e no tempo, permitindo localizar hotspots com início e duração, comparar a intensidade entre bairros e períodos e detectar sinais de propagação ou saturação.  
O produto típico inclui superfícies de risco $\hat{\lambda}(\mathbf{x},t)$ que indicam onde/quando a probabilidade de evento é maior, cronogramas que registram o aparecimento e o declínio de aglomerações e parâmetros interpretáveis (alcance espacial, meia-vida temporal, força de excitação/inibição). Esses resultados são reportados com incerteza explícita, de modo que gestores possam priorizar ações, posicionar recursos e ajustar estratégias à medida que novas ocorrências chegam — sem exigir, de início, uma teoria causal completa, mas oferecendo pistas sólidas para investigações posteriores.


**a) Como funciona**  
Em processos pontuais espaço-temporais, pensamos o fenômeno por meio de uma intensidade que varia no mapa e no relógio — uma espécie de “temperatura de risco”. No nível mais básico, modelamos a intensidade média $\lambda(x,t)$: onde $\lambda(x,t)$ é alta, esperamos mais eventos por unidade de área e de tempo. Essa intensidade pode depender de covariáveis (iluminação, renda, fluxo de pessoas, clima), como em um Poisson inomogêneo: as covariáveis explicam por que certos lugares/instantes têm, em média, mais ou menos ocorrências, gerando mapas e cronogramas de risco. A intuição é direta: condições que dificultam o evento reduzem $\lambda$; condições que o favorecem elevam $\lambda$.  
Há, porém, situações em que o próprio histórico de eventos altera o risco atual. Nesses casos, usamos uma intensidade condicional $\lambda_c(x,t \mid \mathcal{H}_t)$ que incorpora propagação (excitação) ou inibição: a chance de um novo evento aumenta (ou diminui) perto de eventos recentes, dentro de um alcance espacial e de uma meia-vida temporal. Modelos do tipo Hawkes/epidêmicos formalizam essa ideia somando ao nível de base $\mu(x,t)$ o impacto dos eventos passados via uma função de decaimento $g$:

$$
\lambda_c(x,t \mid \mathcal{H}_t)
=\underbrace{\mu(x,t)}_{\text{nível de base (covariáveis)}}
+\underbrace{\sum_{(x_j,t_j)<t} g\!\big(\lVert x-x_j\rVert,\, t-t_j\big)}_{\text{efeito dos eventos recentes}}.
$$

Se $g$ é positivo e decai com a distância e o tempo, um evento eleva temporariamente o risco em seu entorno e esse efeito se dissipa depois; se $g$ for negativo (ou se impusermos janelas de saturação), o último evento desencoraja novos eventos próximos por algum período.  
Resumindo: a primeira ideia explica por que certos lugares e momentos, em média, são mais propensos a eventos ($\lambda(x,t)$ guiada por covariáveis); a segunda explica como os próprios eventos reorganizam o risco no curto prazo ($\lambda_c$ guiada pelo histórico). Juntas, elas permitem mapear risco, identificar hotspots com janela (início, duração, intensidade) e descrever dinâmicas de propagação ou inibição com parâmetros de leitura prática (alcance, meia-vida, força do efeito).


**b) Essência matemática**  
Antes de qualquer detalhe técnico, deixemos claro o que governa o processo: a intensidade espaço-temporal. Ela é a função que dita o “ritmo esperado” de eventos em cada lugar e instante. A formulação que usaremos é:

$$
\lambda(x,t)=\mu(x,t)+\sum_{(x_j,t_j)<t} g\!\big(x-x_j,\, t-t_j\big).
$$

Como ler a equação  
$\lambda(x,t)$ — **intensidade (ritmo de eventos).** É a taxa esperada de eventos por unidade de área e de tempo no ponto $(x,t)$. Se observarmos uma área $A$ durante um intervalo $\Delta t$, o número esperado de eventos é, aproximadamente,

$$
\mathbb{E}[N(A,\Delta t)] \approx \int_{\Delta t}\!\!\int_A \lambda(x,t)\,dx\,dt.
$$

Valores altos de \(\lambda\) significam maior propensão a observar eventos ali, naquele período.

$\mu(x,t)$ — **nível de base (estrutura e exposição).** É o risco “de partida”, antes de considerar o histórico recente. Pode depender de covariáveis (chuva, renda, iluminação, fluxo) e de exposição (offset de população/mobilidade). Uma forma comum é

$$
\mu(x,t)=\exp\!\big(\mathbf{z}(x,t)^\top\gamma\big)\times \mathrm{offset}(x,t),
$$

o que expressa que lugares/tempos com fatores estruturais mais favoráveis ao evento começam com $\mu$ maior.

$\sum g(\cdot,\cdot)$ — **impacto do histórico (propagação ou inibição).** A soma percorre todos os eventos passados $(x_j,t_j)<t$ e adiciona, para cada um, uma contribuição dada por $g$. A função $g(\Delta x,\Delta t)$ descreve quanto um evento anterior altera o risco ao redor ($\Delta x$) e logo depois ($\Delta t$):

- Em processos excitantes (Hawkes/epidêmicos), $g\ge 0$: um evento eleva temporariamente $\lambda$ nas vizinhanças; o efeito decai com o tempo e a distância.  
- Pode haver inibição (partes negativas de $g$): logo após um evento, a chance de outro cai por algum período (saturação, resposta policial, capacidade limitada).


**Parâmetros de $g$ com leitura prática**  
Alcance espacial: até que distância o efeito é relevante (p.ex., 200–300 m).  
Meia-vida temporal: em quanto tempo o efeito cai pela metade (p.ex., 2–3 dias).  
Força inicial: tamanho do salto em $\lambda$ logo após o evento.  
(Opcional) **Índice de ramificação** $\displaystyle \eta=\iint g\,dx\,dt$: fração média de eventos “gerados” por um evento. $\eta<1$ indica processo estável (sem cascatas explosivas).

Exemplo em uma linha (para fixar a ideia): “Após um caso, a intensidade dobra num raio de 150 m por 3 dias e depois cai pela metade a cada 2 dias.” Isso corresponde a um $g$ com alcance curto e decadência rápida.


**Como usar $\hat{\lambda}$ na prática**  
- **Mapas e cronogramas de risco:** avaliar $\hat{\lambda}(x,t)$ em uma grade espacial e horários de interesse para visualizar onde/quando o risco é maior.  
- **Contagem esperada em janelas:** para um bairro $A$ na próxima semana $\Delta t$,

  $$
  \widehat{\mathbb{E}}[N(A,\Delta t)] = \int_{\Delta t}\!\!\int_A \hat{\lambda}(x,t)\,dx\,dt,
  $$

  obtendo uma previsão quantitativa do volume esperado de eventos.  
- **Risco relativo claro:** comparar $\hat{\lambda}(x,t)$ com uma referência (mediana, meta ou período base) para comunicar “$\approx 2\times$ o esperado” de forma direta.

Em resumo, a intensidade $\lambda(x,t)$ separa o que é estrutural ($\mu$) do que é dinâmico (efeito de eventos recentes via $g$). Essa decomposição permite mapear risco, detectar propagação ou inibição e prever contagens em janelas específicas — sempre com parâmetros de interpretação simples que conectam o modelo ao fenômeno observado.


**c) Aplicações comuns**  
Em dados de eventos com carimbo de lugar e momento, os modelos STEP ajudam a mapear onde/quando o risco se concentra, detectar aglomerados e capturar propagação entre ocorrências. Exemplos:
- Saúde pública: localização/época de surtos (dengue, diarreia, influenza) e janelas críticas de transmissão.  
- Segurança: bairros e horários de maior risco de furtos/roubos; sinais de “efeito dominó” após certos eventos.  
- Meio ambiente: focos de queimadas ou descargas atmosféricas concentrados em áreas/periodicidades específicas.  
- Atendimento de emergência: padrões de chamadas por região e hora, ajudando a posicionar bases e equipes.  
- Operações urbanas: quedas de energia, falhas de água, buracos de asfalto — “onde/quando” aparecem em bloco.

**d) O que você obtém**  
Nos modelos de Processos Pontuais Espaço-Temporais (STEP), as saídas típicas incluem:
- Mapas e cronogramas de risco ($\hat{\lambda}(x,t)$): onde/quando o risco é alto.  
- Hotspots com janela: lugar, início–fim, duração, intensidade relativa (por exemplo, “$2\times$ acima do esperado”).  
- Parâmetros com leitura prática (nos modelos com história): alcance espacial, meia-vida temporal, força de excitação/inibição.  
- Cenários “e se…”: simulações simples para avaliar impactos de ações (mais vigilância, bloqueio de criadouros etc.).


**e) Limitações e cuidados**  
Nos processos pontuais espaço-temporais (STEP), a interpretação depende de correções de exposição, escolhas de escala e tratamento de bordas; além disso, são modelos voltados a padrões e dinâmica, não a causalidade direta. Observe:
- **Exposição importa.** Áreas com mais gente tendem a ter mais eventos. Sem ajustar por população/fluxo, confunde-se movimento com risco.  
- **Escala bem definida.** O que é “perto”: 200 m ou 2 km? 1 hora ou 1 semana? A escala muda os agrupamentos; declare e justifique sua escolha.  
- **Bordas do estudo.** Próximo às fronteiras espacial/temporal, a percepção de cluster pode distorcer; aplique correções de borda ou interprete com cautela.  
- **Sinal ≠ causa.** STEP revela onde/quando e como os eventos se agregam; não explica por quê. Para causalidade, use modelos com hipóteses substantivas e testes apropriados.  
- **Custo e calibração.** Modelos com memória (Hawkes) e mapeamento com incerteza (LGCP) exigem computação e calibração cuidadosa (parâmetros, separação treino–teste).

**f) Abordagens de referência**  
Os modelos a seguir descrevem a intensidade de eventos no espaço-tempo e sua dinâmica (propagação ou inibição), com ou sem heterogeneidade latente.
- **Poisson inhomogêneo E–T (com offset):** modela a intensidade média $\lambda(x,t)$ em função de covariáveis e exposição (população/fluxo). Entrega: mapas/cronogramas de risco esperado.  
- **Log-Gaussian Cox Process (LGCP) E–T:** a intensidade é estocástica (log-gaussiana), capturando heterogeneidade latente e incerteza. Entrega: $\hat{\lambda}(x,t)$ com intervalos; melhores mapas de risco quando há superdispersão.  
- **Processos Hawkes / epidêmicos E–T (intensidade condicional):** capturam propagação; eventos recentes elevam (ou inibem) o risco ao redor por certo tempo. Entrega: parâmetros com leitura prática (alcance, meia-vida, força de excitação) e simulações “e se…”.  
- **Famílias de clusterização de Neyman–Scott/Matérn:** geram aglomerados por “sementes” latentes; úteis para diagnóstico de estrutura de clusters. Entrega: tamanho/compacidade de clusters, ajuste de segunda ordem.  
- **Estatísticas de segunda ordem E–T (K, Knox, Mantel):** testes/diagnósticos de clustering espaço-temporal acima do acaso. Entrega: evidência (ou não) de aglomeração nas escalas analisadas.





### 5.1.3 Regressão em Painel com Dependência Espacial 

A Regressão em Painel com Dependência Espacial analisa indicadores observados repetidamente nas mesmas áreas (bairros, municípios, escolas, regiões) e, ao mesmo tempo, reconhece que vizinhos se influenciam. Em vez de uma foto estática, acompanhamos um filme: como cada área evolui no tempo e como mudanças em um lugar transbordam para os arredores. Essa abordagem permite distinguir o que é característica permanente de cada unidade (geografia, perfil socioeconômico, cultura institucional) do que de fato mudou no período analisado.  
Na prática, o modelo estima quanto uma intervenção ou fator local altera o indicador na própria área e quanto desse efeito se propaga para as áreas vizinhas (spillover), além de separar impactos imediatos daqueles que se acumulam com o tempo. Com isso, evita-se atribuir a uma área méritos ou culpas que, na verdade, refletem influências externas; e obtém-se uma leitura mais fiel do efeito direto, do efeito indireto e do efeito total sobre a rede de unidades conectadas.

**a) Como funciona**  
Em painéis com dependência espacial, combinamos três peças — tempo, espaço e condições locais — numa mesma equação.  
Primeiro, há o eixo do tempo: observamos o indicador $y_{it}$ repetidamente para a mesma área $i$. Isso permite comparar cada área consigo mesma em momentos diferentes, capturando persistência e reduzindo vieses de fatores fixos (características que não mudam rápido).  
Depois vem o espaço: definimos quem influencia quem por meio de uma matriz de vizinhança $W=[w_{ij}]$. Essa matriz codifica a intensidade da ligação entre áreas (contiguidade, distância, $k$-vizinhos, fluxos, rede) e produz, para cada $i$, a “média ponderada dos vizinhos” $(Wy)_{it}=\sum_j w_{ij}y_{jt}$.  
Por fim, juntamos tudo no modelo espaço–tempo. Um formato simples e bastante usado é:

$$
y_{it}
=\underbrace{\phi\,y_{i,t-1}}_{\text{memória no tempo}}
+\underbrace{\rho\,(Wy)_{it}}_{\text{efeito dos vizinhos}}
+\underbrace{\mathbf{x}_{it}^\top\beta}_{\text{condições locais}}
+\underbrace{\alpha_i+\tau_t}_{\text{efeitos fixos}}
+\varepsilon_{it}.
$$

$\phi$ mede persistência temporal (efeitos que demoram a se completar).  
$\rho$ mede spillovers espaciais: quanto os vizinhos “puxam/empurram” $y_{it}$.  
$\mathbf{x}_{it}$ reúne fatores locais (renda, políticas, infraestrutura).  
$\alpha_i$ e $\tau_t$ controlam níveis próprios da área e choques comuns do período.

A intuição é direta: políticas, choques econômicos ou surtos não param na fronteira. O termo espacial $\rho (Wy)_{it}$ captura esse transbordamento; o termo temporal $\phi y_{i,t-1}$ captura a memória do próprio indicador. Com a matriz $W$ bem definida e os efeitos fixos explicitados, o modelo consegue separar impacto local (na própria área) de impacto indireto (nos vizinhos) e distinguir efeitos imediatos dos que se acumulam ao longo do tempo.


**b) Essência matemática**  

**Quem influencia quem (matriz de vizinhança $W$).**  
A vizinhança é codificada por uma matriz $W=[w_{ij}]$. Colocamos $w_{ij}>0$ quando a área $j$ é “vizinha” da área $i$ — por contiguidade, distância, $k$-vizinhos, fluxos ou uma rede conhecida —, e $w_{ij}=0$ quando não há ligação. É comum padronizar por linha (cada linha soma 1), de modo que

$$
(Wy)_{it}=\sum_j w_{ij}\,y_{jt}
$$

vire, literalmente, a média ponderada do que os vizinhos de $i$ estão fazendo no tempo $t$.

**Como isso entra no modelo (transbordamento espacial).**  
Com $W$ definido, conectamos vizinhos, condições locais e níveis fixos em uma equação simples:

$$
y_{it}
=\rho\,(Wy)_{it}
+\mathbf{x}_{it}^\top\beta
+\alpha_i+\tau_t
+\varepsilon_{it}.
$$

Aqui, $\rho$ mede quanto os vizinhos “puxam” $y_{it}$: se $\rho>0$, valores altos nos vizinhos tendem a elevar $y_{it}$; se $\rho<0$, há compensação/substituição. O termo $\mathbf{x}_{it}^\top\beta$ capta características locais; $\alpha_i$ e $\tau_t$ representam, respectivamente, níveis próprios da área e choques comuns do período.

**Por que não basta olhar $\beta$ (impactos $\ne$ coeficientes).**  
Quando há transbordamento, um choque em $\mathbf{x}_{it}$ não para em $i$: ele se espalha para os vizinhos ($\rho W$), volta pelos vizinhos dos vizinhos ($\rho^2 W^2$), e assim sucessivamente. Esse “eco” é sintetizado pela **matriz multiplicadora espacial**:

$$
(I-\rho W)^{-1}=I+\rho W+\rho^2 W^2+\cdots
$$

Assim, o impacto de uma pequena mudança em $\mathbf{x}$ é

$$
\underbrace{(I-\rho W)^{-1}}_{\text{propagação na rede}}\ \times\ \beta,
$$

do qual extraímos três números para relatar com clareza:
- **Direto:** média dos elementos diagonais (efeito do $x$ de $i$ sobre o próprio $y_i$);  
- **Indireto (spillover):** média dos elementos fora da diagonal (efeito do $x$ de $i$ sobre os vizinhos);  
- **Total:** direto $+$ indireto (o que importa para a decisão).

**Quando existe memória temporal (curto vs. longo prazo).**  
Muitos indicadores lembram do passado. Para isso, incluímos uma defasagem temporal:

$$
y_{it}
=\phi\,y_{i,t-1}
+\rho\,(Wy)_{it}
+\mathbf{x}_{it}^\top\beta
+\alpha_i+\tau_t
+\varepsilon_{it}.
$$

Agora há dois mecanismos: o espacial $(I-\rho W)^{-1}$ (espalha na rede) e o temporal (com acúmulo ao longo do tempo). Se a mudança em $\mathbf{x}$ persiste nos períodos seguintes (como uma política mantida), o **impacto de longo prazo** (estado estacionário) aproxima-se de

$$
\text{Impacto de longo prazo} \;\approx\; (I-\rho W)^{-1}\times \frac{1}{1-\phi}\times \beta,
$$

enquanto o **curto prazo** é o efeito no período corrente (sem o fator $1/(1-\phi)$).


**Resumo em português claro.**  
$(Wy)_{it}$ é “o que os vizinhos estão fazendo” ao redor de $i$; $\rho$ diz quanto isso respinga em $i$ (transbordamento). Não reporte só $\beta$: comunique impactos direto, indireto e total, que já incorporam o eco na rede. Se houver memória temporal ($\phi$), separe efeito imediato do efeito acumulado — o longo prazo tende a ser maior porque se espalha e se acumula.


**c) Aplicações comuns**  
Em painéis areais com interação entre vizinhos, a regressão em painel espacial quantifica efeitos locais, spillovers e sua dinâmica no tempo, informando políticas que atravessam fronteiras administrativas. Exemplos:
- Políticas públicas municipais: impacto de um programa de iluminação sobre a segurança do próprio bairro e dos vizinhos.  
- Saúde coletiva: efeito de vacinação ou atenção básica sobre hospitalizações, medindo efeitos diretos e indiretos.  
- Economia regional: como investimentos ou choques de emprego se propagam por cidades conectadas.  
- Educação: impacto de recursos escolares em redes de municípios próximos.  
- Meio ambiente: qualidade do ar/água em bacias contíguas e efeitos cruzados entre municípios.

**d) O que você obtém**  
Na regressão em painel com dependência espacial, você obtém:
- Efeitos **direto**, **indireto (spillover)** e **total** das variáveis explicativas: quanto muda na própria área, quanto muda nas vizinhas e a soma dos dois.  
- (Quando há dinâmica) **Impactos de curto e de longo prazo**, mostrando o que aparece já e o que se acumula com o tempo.  
- **Mapas/resumos** que ajudam a visualizar onde os efeitos são mais fortes.  
- **Texto interpretativo** que traduza os números em mensagens de política: “o programa X reduziu Y em média em 3% na própria cidade e 1% nas vizinhas conectadas”.

**e) Limitações e cuidados**  
Na regressão em painel com dependência espacial, a credibilidade dos resultados depende de uma vizinhança bem definida, de impactos corretamente interpretados (em vez de coeficientes brutos) e de um tratamento explícito para choques de período e persistência temporal. Atenção aos seguintes pontos:
- **Matriz de vizinhança $W$ bem fundamentada.** Defina quem é vizinho de quem e por quê (contiguidade, distância, $k$-vizinhos, fluxos, rede). Uma $W$ ad hoc compromete a interpretação dos spillovers.  
- **Coeficiente ≠ impacto.** Com termo espacial, não leia $\beta$ como efeito. Relate impactos direto, indireto (spillover) e total, via multiplicador $(I-\rho W)^{-1}$.  
- **Choques comuns no tempo.** Anos “atípicos” (crise, pandemia) pedem efeitos de período; ignorá-los pode inflar artificialmente $\rho$.  
- **Persistência temporal importa.** Se $y$ tem memória, modele-a (defasagem temporal, termos dinâmicos) para não confundir inércia com efeito espacial.  
- **Custo computacional.** Em muitos locais/conexões, a estimação pesa. Considere tornar $W$ mais esparsa, usar aproximações e métodos eficientes (p.ex., GMM/IV, rotinas para matrizes esparsas).


**f) Abordagens de referência**  
Os modelos listados quantificam efeitos locais e spillovers entre áreas ao longo do tempo, distinguindo impactos de curto e de longo prazo.
- **SAR (Spatial Autoregressive) / SEM (Spatial Error) / SDM (Spatial Durbin Model)**  
  SAR: defasagem espacial na dependente; SEM: erro espacialmente correlacionado; SDM: inclui defasagem nas covariáveis (spillovers nas explicativas).  
  **Entrega:** impactos direto/indireto/total (não só coeficientes), mapas de efeitos.
- **SAC/SARAR (SAR + SEM)**  
  Combina lag na dependente e erro espacial nos resíduos; robusto a fontes múltiplas de dependência.  
  **Entrega:** impactos + diagnóstico de ruído espacial remanescente.
- **Painéis dinâmicos espaciais (SDPD/SDM dinâmico)**  
  Incluem memória temporal ($y_{i,t-1}$) e termos espaciais (lag de $y$ e/ou $x$); estimados via MLE, QMLE, IV/GMM.  
  **Entrega:** curto vs. longo prazo e spillovers na rede.
- **Modelos com efeitos fixos/aleatórios e matriz $W$ flexível**  
  Incorporam heterogeneidade de área e escolhem $W$ por contiguidade, distância, $k$-vizinhos ou redes/fluxos.  
  **Entrega:** inferência de políticas com robustez a $W$.




### 5.1.4 Clustering (Agrupamento) Espaço-Temporal 
Clustering espaço-temporal é uma família de métodos que descobre concentrações de eventos no mapa e no tempo. Pense em “bolsões” onde muita coisa acontece no mesmo lugar e num período parecido: um surto de dengue em alguns quarteirões durante maio e junho, uma sequência de furtos perto de estações em horários de pico, ou vários focos de queimadas numa mesma região ao longo de um fim de semana.
O objetivo não é explicar a causa (isso vem depois), e sim mostrar onde e quando há acúmulo (hotspots) ou regularidade/inibição (lugares que quase nunca concentram eventos).

**a) Como funciona**  
Há duas maneiras clássicas de enxergar o agrupamento espaço-temporal.  
A primeira compara o padrão observado com o “azar”. A pergunta é direta: dadas as posições e os horários dos eventos, o arranjo parece mais concentrado do que seria por simples acaso? Para responder, define-se um cenário nulo (eventos aleatórios no espaço e no tempo, ou proporcionais à população/fluxo) e mede-se quanta vizinhança evento–evento existe. Uma forma simples é contar pares $(i,j)$ que estão perto no mapa (distância $\le r_s$) e perto no relógio (defasagem $\le r_t$); compara-se esse total com o esperado sob o nulo. Se o observado excede sistematicamente o esperado, há indício de cluster nas escalas $(r_s,r_t)$. Essa linha produz um sinal com confiança estatística (p-valores, significância ajustada).  
A segunda foca em proximidade/densidade, sem formular um teste formal. Aqui, pontos suficientemente próximos se agregam e formam um cluster; pontos isolados viram ruído. Em termos contínuos, estimamos uma intensidade $\hat{\lambda}(x,y,t)$ — pense em um “mapa-calendário de calor” — e identificamos os picos dessa intensidade como hotspots. O resultado é intuitivo e útil para operação: indica onde/quando a concentração é maior, com controle explícito da escala via larguras de banda (no espaço e no tempo) ou parâmetros de vizinhança.  
Em resumo: a abordagem por teste responde “há mais concentração do que o acaso permitiria?” e oferece significância; a abordagem por densidade responde “onde/quando estão os picos?” e oferece localização e escala dos agrupamentos. Idealmente, usa-se ambas de modo complementar: testar para confirmar o sinal e estimar intensidade para descrever o hotspot em detalhe.


**b) Essência matemática**  
Antes de qualquer algoritmo, fixamos o objeto: a intensidade espaço-temporal $\lambda(x,y,t)$, que representa o ritmo esperado de eventos por unidade de área e de tempo em cada ponto $(x,y,t)$. Pense nela como um mapa–calendário de propensão: valores altos indicam maior chance de observar eventos naquela região e período. Em termos operacionais, se olhamos uma área $A$ durante um intervalo $\Delta t$, o número esperado de eventos é, aproximadamente,

$$
\mathbb{E}[N(A,\Delta t)] \approx \int_{\Delta t}\!\!\int_A \lambda(x,y,t)\,dx\,dy\,dt.
$$

Isso também permite um risco relativo imediato:

$$
\mathrm{RR}(x,y,t)=\frac{\lambda(x,y,t)}{\lambda_{\text{ref}}}
\quad\text{(por exemplo, usando a mediana como referência).}
$$

A partir desse objeto, há dois caminhos complementares para detectar agrupamentos.

**“Há mais pares próximos do que o acaso permitiria?” (teste por pares)**  
Aqui, a pergunta é: o padrão observado está mais concentrado (no espaço e no tempo) do que esperaríamos sob um cenário nulo? Para responder com clareza:

- **Defina a vizinhança.** Escolha um raio espacial $r_s$ (ex.: 300 m) e uma janela temporal $r_t$ (ex.: 7 dias). Para cada par $(i,j)$, calcule a distância espacial $d_s(i,j)$ e a defasagem temporal $d_t(i,j)$.

- **Conte os pares próximos.**

  $$
  C_{\text{obs}}=\#\{(i,j):\ d_s(i,j)\le r_s\ \text{e}\ d_t(i,j)\le r_t\}.
  $$

- **Construa o “mundo ao acaso”.** Gere $B$ reamostragens que quebram o padrão mas preservam o essencial.  
  – Nulo simples: embaralhe tempos ou posições.  
  – Nulo com exposição: embaralhe respeitando população/fluxo, para não confundir movimento com risco.  
  Em cada reamostragem $b$, recalcule $C_b$.

- **Compare observado vs. esperado.**

  $$
  p=\frac{\#\{\,C_b \ge C_{\text{obs}}\,\}+1}{B+1}.
  $$

  $p$ pequeno indica excesso de pares próximos $\Rightarrow$ evidência de cluster nas escalas $(r_s,r_t)$.

**Boas práticas.** Se você testa muitas escalas $(r_s,r_t)$ ou muitos locais/tempos, ajuste para múltiplas comparações (p.ex., FDR). E lembre que bordas espacial/temporal reduzem pares possíveis perto do limite — aplique correção de borda ou interprete com cautela.

**“Onde estão os picos de intensidade?” (densidade/“calor”)**  
Em vez de testar pares, você pode estimar $\lambda$ diretamente por suavização e procurar picos — os hotspots. Uma versão comum é o estimador kernel espaço-temporal:

$$
\hat{\lambda}(x,y,t)
=\frac{1}{h_s^{2}\,h_t}
\sum_{i=1}^{n}
K_s\!\Big(\frac{\lVert (x,y)-(x_i,y_i)\rVert}{h_s}\Big)\,
K_t\!\Big(\frac{|t-t_i|}{h_t}\Big)\Big/\mathrm{offset}(x,y,t),
$$

onde $h_s$ e $h_t$ são larguras de banda (quanto suavizar no espaço e no tempo), $K_s$ e $K_t$ são núcleos que decaem com a distância/defasagem, e o offset corrige por exposição (população, mobilidade, fluxo).  
A leitura é direta: picos de $\hat{\lambda}$ correspondem a hotspots. Você pode padronizar (z-scores ou $\hat{\lambda}/\text{mediana}$) e, se desejar confiança estatística, usar permutações para comparar o pico observado com o que ocorreria sob um nulo equivalente.  
**Escolhas que importam.** $h_s$ e $h_t$ definem a escala do que será chamado de “cluster”: menores $\Rightarrow$ surtos curtos e locais; maiores $\Rightarrow$ padrões persistentes e amplos.


**Síntese prática**  
A abordagem por teste responde “há cluster?” e fornece significância na escala analisada.  
A abordagem por densidade responde “onde/quando?” e descreve extensão e intensidade dos agrupamentos.  
Juntas, oferecem um quadro completo: confirmam o sinal e localizam o hotspot com a escala adequada, sempre controlando por exposição e respeitando bordas.

**c) Aplicações comuns**  
Quando o objetivo é detectar hotspots e surtos — isto é, identificar onde e quando a concentração de eventos foge do esperado ao acaso — o agrupamento E-T oferece sinais diretos para priorização operacional e vigilância. Exemplos:
- Saúde: surtos localizados de arboviroses, intoxicações, síndromes respiratórias; “quando e onde” focar agentes e insumos.  
- Segurança pública: horários e áreas de maior furto/roubo; apoio a rondas direcionadas e iluminação pública.  
- Meio ambiente: focos de queimadas, deslizamentos após chuvas intensas, mortalidade de fauna.  
- Mobilidade/trânsito: pontos e horários de maior incidência de acidentes; impacto de eventos (shows, jogos).  
- Serviços urbanos: falhas de rede elétrica/água concentradas em bairros e janelas de tempo específicas.

**d) O que você obtém**  
No agrupamento espaço-temporal, você obtém:
- Mapa de hotspots com rótulos claros: lugar, período (início–fim), duração e intensidade relativa (“este hotspot tem $\sim 2\times$ mais eventos do que áreas similares”).  
- Cronograma mostrando quando cada hotspot começou e terminou.  
- Sinal de confiança, quando houver teste: “baixo/médio/alto” (ou p-valor simples de entender).  
- Texto curto de interpretação: “Concentração de casos no bairro X entre 12/05 e 03/06; pico em 24/05; recomenda-se ação Y.”

**e) Limitações e cuidados**  
No agrupamento espaço-temporal, a leitura de hotspots depende fortemente de escolhas de escala, correções de exposição e tratamento de bordas; além disso, o método é descritivo — aponta onde/quando, não por que. Observe:
- **Escala manda:** “Perto” é quanto? 300 m ou 3 km? 2 horas ou 2 dias? Escalas mal escolhidas mudam o desenho dos clusters.  
- **Exposição:** lugares com mais gente tendem a ter mais eventos. Sem corrigir por população/fluxo, confunde-se movimento com risco.  
- **Bordas do mapa:** áreas na fronteira podem parecer menos concentradas porque parte dos eventos “cai fora” da janela observada.  
- **Não é causa:** clustering indica onde/quando, mas não o porquê. Para isso, entram modelos explicativos (Poisson/LGCP, intensidade condicional).  
- **Varia no tempo:** hotspots mudam; repita a análise periodicamente.

**f) Abordagens de referência**  
Os métodos seguintes detectam concentrações espaço-temporais por testes contra nulos ou por densidade, destacando janelas críticas e, quando aplicável, a significância dos achados.
- **Varredura espaço-temporal (Space-Time Scan; SaTScan)**  
  Procura janelas cilíndricas (área × duração) com excesso de casos vs. nulo (com/sem ajuste por população).  
  **Entrega:** hotspots com início–fim, risco relativo e significância (ajuste para múltiplas janelas).
- **ST-DBSCAN / HDBSCAN E-T (densidade no espaço e no tempo)**  
  Agrupa eventos por proximidade espaço-temporal; não exige forma pré-definida.  
  **Entrega:** clusters + ruído; útil para monitoramento operacional e triagem.
- **KDE espaço-temporal (núcleo) com picos/hotspots**  
  Estima $\hat{\lambda}(x,t)$ suavizada; picos são áreas/tempos quentes; significância via permutações.  
  **Entrega:** mapas de calor E-T e cronogramas de hotspots.
- **LISA/Getis-Ord com janelas temporais**  
  Indicadores locais de associação (hot/cold spots) aplicados por janelas de tempo.  
  **Entrega:** hot/cold spots locais e sua evolução.
- **Testes por pares (Knox/Mantel) com múltiplas escalas $(r_s,r_t)$**  
  Quantificam excesso de pares próximos no espaço e no tempo.  
  **Entrega:** evidência de cluster nas escalas testadas (com FDR/correções).




### 5.1.5 Síntese dos principais modelos espaço-temporais

Nesta subseção reunimos, de forma organizada, os principais modelos espaço-temporais apresentados no capítulo. Para orientar a leitura e apoiar escolhas metodológicas, apresentamos dois quadros complementares: primeiro, um quadro comparativo detalhado (Quadro 1), agora em formato retrato — com os modelos nas colunas e os critérios nas linhas —, que explicita a pergunta que cada modelo responde, o tipo de dado esperado, a ideia central, as entradas mínimas, as saídas, quando usar e as armadilhas; em seguida, um guia rápido (Quadro 2), pensado como um “resumo de bolso” para decidir qual modelo usar conforme o objetivo da análise.

**Quadro 1 — Comparação detalhada**

| Critério \ Modelo | Krigagem E-T (Geoestatística) | Processos Pontuais E-T | Econometria Espacial em Painel | Clustering E-T |
|---|---|---|---|---|
| **Pergunta que responde** | “Quanto vale $Y(\mathbf{s},t)$ em locais/tempos não observados?” | “Como/onde/quando os eventos ocorrem e com que risco?” | “Quais efeitos explicam a variação no espaço-tempo? Há dinâmica e interdependência?” | “Existem grupos/estruturas de concentração no espaço-tempo?” |
| **Dados típicos** | Variáveis contínuas em pontos (estações) ao longo do tempo; covariáveis (altitude, uso do solo, distância do mar). | Eventos $(x,y,t)$ (crimes, dengue, raios), com atributos e covariáveis (densidade, clima, uso do solo). | Séries por área (bairros/municípios/zonas) em múltiplos períodos: $\{Y_{i,t},X_{i,t}\}$ + matriz $W$. | Contagens/taxas por área e tempo ou eventos $(x,y,t)$. |
| **Ideia central** | Tratar $Y(\mathbf{s},t)$ como campo aleatório com tendência + correlação E-T; ajustar semivariograma E-T e krigar (com/sem covariáveis). | Modelar intensidade $\lambda(\mathbf{x},t)$: empírico (p.ex. LGCP) ou mecanicista (intensidade condicional ao histórico $H_t$); detectar clusters/hotspots. | Modelos de painel com termos espaciais (SAR/SEM/Durbin) e defasagens temporais; efeitos fixos/aleatórios; spillovers via $W$; tratar endogeneidade (IV/GMM). | Detectar aglomerados: varredura E-T (scan), hotspot dinâmica, ST-DBSCAN/KDE para trajetórias ou perfis por local. |
| **Entradas mínimas** | $(x,y)$, $t$, valores de $Y$; (opcional) $X(\mathbf{s},t)$. | Coordenadas, tempo, janela $\mathcal{W}\times\mathcal{T}$; (opcional) covariáveis. | Séries por unidade areal, $W$, covariáveis (tempo-variantes e invariantes). | Séries ou eventos; janelas E-T (raio, duração); controle de múltiplos testes. |
| **Saídas** | Mapas preditos por tempo; séries previstas por local; incerteza (variância, prob. de excedência). | Mapas de risco e sua evolução; janelas/locais de surto com significância; recorrência/persistência. | Efeitos marginais; spillovers (via $W$); impactos dinâmicos (curto vs. longo prazo). | Clusters com significância; calendário de surtos (início/fim, duração); tipologias por local. |
| **Quando usar** | Há lacunas no espaço/tempo; precisa de superfícies contínuas e incerteza explícita. | Dados discretos (eventos) e interesse em onde/quando o risco aumenta. | Avaliação causal/comportamental; políticas públicas por área ao longo do tempo. | Exploração/monitoramento de anomalias; priorização de ação e vigilância. |
| **Armadilhas comuns** | Assumir separabilidade sem diagnóstico; ignorar não-estacionariedade. | Ignorar exposição (população em risco); confundir artefatos de amostragem com “hotspots”. | Definir $W$ sem justificativa; ignorar dependência residual e diagnósticos. | Escolhas ad hoc de janelas → falsos positivos; não corrigir múltiplas comparações. |

**Quadro 2 — Guia rápido**

| Tipo de modelo | Objetivo principal | Tipo de dado | Exemplo típico |
|---|---|---|---|
| Regressão em painel (econometria espacial) | Estimar efeitos e dinâmica com interdependência espacial | Séries por área (unidades areais + $W$) | Evolução da renda municipal e impacto de políticas |
| Krigagem espaço-temporal (geoestatística) | Predizer em locais/tempos não observados com incerteza | Variáveis contínuas em pontos + covariáveis | Interpolação/nowcasting de chuva |
| Clusters e anomalias (scan/hotspots) | Detectar concentrações e períodos atípicos | Contagens/taxas ou eventos | Surtos de doenças; janelas críticas |
| Processos pontuais E-T | Modelar ocorrência de eventos e risco | Pontos $(x,y,t)$ | Ocorrência de raios, crimes, incêndios |




### 5.1.6 Outras Abordagens Emergentes

Além dos quatro grupos principais, há procedimentos recentes que combinam modelagem estatística e aprendizado de máquina para lidar com dependências espaço-temporais complexas. Não são o foco deste material, mas vale conhecê-los em linhas gerais, sobretudo para cenários com grande volume de dados, múltiplas fontes e exigência de operação quase em tempo real.

**Modelos bayesianos espaço-temporais (hierárquicos)**  
**Ideia central.** Representar explicitamente três camadas de incerteza — dos dados, do processo (variação no espaço e no tempo) e dos parâmetros — em uma estrutura hierárquica.  
**Como funcionam (em síntese).** Especifica-se um modelo em camadas (dados \| processo \| parâmetros) e obtêm-se distribuições a posteriori para quantidades de interesse: valores não observados, tendências, efeitos de covariáveis e medidas de risco. Ferramentas como INLA/SPDE e MCMC são usuais para inferência e predição, permitindo incorporar informação prévia (p. ex., conhecimento de especialistas ou resultados de estudos anteriores).  
**Quando são úteis.** Dados esparsos ou ruidosos; necessidade de quantificar incerteza de forma completa; integração de fontes heterogêneas (estações, satélite, modelos numéricos).  
**Entregáveis típicos.** Mapas e séries com intervalos de credibilidade, probabilidades de excedência e efeitos de covariáveis com incerteza propagada.  
**Atenção.** Custo computacional mais elevado; necessidade de escolhas de priors e de ferramentas específicas; maior cuidado na verificação de convergência e na sensibilidade às suposições.

**Redes neurais e séries temporais espaciais (aprendizado de máquina)**  
**Ideia central.** Utilizar arquiteturas capazes de capturar padrões não lineares e interações complexas no espaço e no tempo — por exemplo, ConvLSTM, redes em grafos (GNNs) para dados em malhas/lattice ou redes urbanas, e Transformers com codificação espacial.  
**Força principal.** Previsão e nowcasting em grande escala quando há muito dado e diversas covariáveis (imagens, produtos de satélite, mobilidade, clima), com capacidade de atualização frequente para operações quase em tempo real.  
**Entregáveis típicos.** Predições rápidas e, em geral, acuradas em contextos ricos em dados; úteis para suporte operacional, monitoramento e detecção precoce de anomalias.  
**Atenção.** Menor interpretabilidade e risco de overfitting; a validação deve respeitar separações no espaço e no tempo (evitando “vazamentos” de informação); a incerteza costuma ser menos transparente, exigindo técnicas adicionais (ensembles, quantile regression, MC dropout, entre outras).



## 5.2 Etapas básicas de uma análise espaço-temporal

Uma boa análise espaço-temporal não começa “escolhendo o método”. Começa organizando o problema para que os dados possam responder, de forma verificável, onde e quando algo acontece, por quê e com quanta incerteza. Abaixo, um roteiro em cinco etapas que aparece, em maior ou menor grau, em qualquer estudo — do exploratório ao avançado.

**a) Coleta e estruturação dos dados**  
Antes de modelar, é preciso saber o que se está medindo, onde e quando. Esta etapa garante que espaço e tempo estejam bem definidos e que as bases “conversem” entre si.
- Defina o domínio: área de estudo $A$ com sistema de referência/projeção coerente (CRS) e janela temporal $T$ com a resolução adequada (hora, dia, mês).
- Classifique o tipo de dado:
  - Geoestatístico (variável contínua: chuva, temperatura, PM₂.₅).
  - Eventos $(x,y,t)$ (crimes, casos, raios).
  - Painel por área (unidade areal × tempo: bairros×meses, municípios×anos).
- Integre e padronize no esquema mínimo $(x,y,t)$ + valores/atributos; trate faltantes/outliers; alinhe calendários e fusos; documente metadados (unidades, fonte, mudanças de instrumento).
- Considere exposição (população em risco): inclua offsets ou covariáveis (população, fluxo viário, intensidade de uso) para não confundir “muitos eventos” com “alto risco”.

**b) Exploração visual e descritiva**  
Agora, olhamos para os dados. O objetivo é identificar padrões e problemas óbvios antes de escolher um modelo.
- Mapas e séries: mapas temáticos (padrões espaciais), séries temporais (tendências/sazonalidade) e, quando possível, animações ou “cubos” E–T para ver movimento.
- Primeira ordem (nível médio):
  - Para eventos, estime intensidade média $\lambda(\mathbf{x},t)$ (p.ex., KDE espaço-temporal).
  - Para variáveis contínuas, use médias móveis/sazonais.
- Pistas de dependência: autocorrelação temporal, semelhança espacial entre vizinhos, períodos/áreas atípicos.
- Checagens úteis: separabilidade de primeira ordem $\lambda(\mathbf{x},t)=\rho(\mathbf{x})\mu(t)?$; efeitos de borda; MAUP (sensibilidade à agregação).

**c) Escolha do tipo de modelo (guiada pela pergunta)**  
Com os padrões em mente, a pergunta manda no método. O modelo deve combinar o tipo de dado com o objetivo.

| Pergunta/objetivo | Tipo de modelo indicado | O que você recebe |
|---|---|---|
| Comparar áreas no tempo (efeitos, políticas, spillovers) | Painel espacial (SAR/SEM/SDM; dinâmico se preciso) | Efeitos diretos/indiretos e curto vs. longo prazo |
| Interpolar/prever valores em locais/instantes sem medição | Geoestatística E–T (krigagem) | Mapas/séries com incerteza e prob. de excedência |
| Detectar hotspots e períodos críticos | Clustering E–T (scan; ST-DBSCAN; KDE) | Localização, duração, intensidade, significância |
| Modelar ocorrência de eventos (mecanismo/propagação) | Processos pontuais E–T (Poisson inhom., LGCP, Hawkes) | Mapas de risco/intensidade; parâmetros de propagação |

Regra prática: comece simples e coerente com a pergunta; só aumente a complexidade se os diagnósticos pedirem.

**d) Ajuste, interpretação e validação**  
Aqui o modelo é estimado, os resultados são lidos e a qualidade é testada de modo honesto para espaço e tempo.
- **Geoestatística E–T:** modele a tendência $m(\mathbf{s},t)$; estime semivariograma/covariância E–T; ajuste modelo (separável vs. não separável); krigue valores e variâncias.  
  **Validação:** cross-validation bloqueada no espaço e no tempo; métricas (RMSE/MAE, CRPS); resíduos sem autocorrelação.
- **Processos pontuais:** estime $\lambda(\mathbf{x},t)$; avalie segunda ordem (funções $K$/par-correlação inhomogêneas); escolha Poisson inhom./LGCP (heterogeneidade) ou intensidade condicional (dinâmica).  
  **Validação:** envelopes de simulação, resíduos de intensidade, divisão temporal treino→teste.
- **Painel espacial:** defina $W$ (contiguidade, distância, rede); escolha SAR/SEM/SDM (dinâmico se houver persistência); estime (MLE/QMLE, IV/GMM); relate impactos direto/indireto/total.  
  **Validação:** testes LM, Hausman/Mundlak, sensibilidade a $W$, separação temporal em previsão.
- **Clustering E–T:** calibre escalas e parâmetros (raios $r_s,r_t$, minPts, bandwidth $h_s,h_t$); rode scan/DBSCAN/KDE.  
  **Validação:** significância (scan), sensibilidade a escalas, correção de múltiplas comparações e bordas.

Em todos os casos, comunique incerteza (intervalos/bandas, probabilidades, envelopes) e justifique escolhas de parâmetros/escala com análise de sensibilidade.

**e) Comunicação dos resultados**  
Por fim, conte a história de forma que quem decide consiga usar.
- Mapas de predição/risco com legendas claras e camadas de incerteza (variância, intervalos, prob. de excedência).
- Séries e comparativos por área/período; cronogramas de hotspots (início, pico, duração).
- Painéis interativos quando o público precisa navegar por tempo × espaço.
- Narrativa final: explicite pergunta, dados, método, achados, incerteza, limitações e implicações para decisão.

Dica: decisões sólidas pedem reprodutibilidade. Versões de dados, scripts e parâmetros devem ficar registrados; assim, resultados podem ser explicados e auditados depois.




## 5.3 Ferramentas e Softwares para Modelagem Espaço-Temporal

A escolha de ferramentas em análise espaço-temporal raramente é “ou/ou”. Na prática, combinamos SIG para organizar e inspecionar dados, linguagens para modelar e validar, e aplicativos especializados para tarefas pontuais (por exemplo, scan statistics). Nesta seção, cada subseção começa com um breve texto orientador — para situar quando usar — seguido de quadros de referência que facilitam a comparação.

### 5.3.1 Ambientes de SIG e Análise Espacial

Por que começar por aqui?  
Os SIG são a porta de entrada para ver o fenômeno no mapa, conferir a qualidade dos dados e criar animações temporais. Eles organizam camadas, projeções e tempos de observação com baixo custo de entrada. Quando a análise exige inferência e validação estatística, o fluxo naturalmente migra para R/Python — mas o SIG permanece como hub de visualização e publicação.

Quadro 3 — SIGs com suporte a tempo

| Software | Características principais | Nível de complexidade |
|---|---|---|
| QGIS (open source) | Interface intuitiva; vetorial/raster; Temporal Controller nativo; ecossistema de plugins. | Fácil |
| ArcGIS Pro (comercial) | Fluxos guiados; Space Time Cube (agregação 2D+tempo e análises dedicadas). | Intermediário |
| gvSIG / TerrSet / SPRING | Plataformas geográficas com recursos de visualização e análises temporais temáticas. | Intermediário |

Comparação curta. QGIS é rápido para explorar e comunicar; ArcGIS Pro oferece workflows prontos (p. ex., cubos E–T); demais plataformas atendem nichos institucionais. Todas são ótimas antes da modelagem formal.



### 5.3.2 Linguagens Estatísticas e Computacionais

Quando sair do SIG?  
Sempre que a pergunta exigir modelagem, predição, inferência e validação, você precisará de uma linguagem. R destaca-se pela maturidade estatística e ecossistema acadêmico; Python brilha na integração com ML, dados massivos e entrega aplicada (APIs, dashboards). MATLAB é frequente em engenharia e sinais.

Quadro 4 — Linguagens para modelagem E–T

| Linguagem | Pontos fortes | Usos típicos | Pacotes/bibliotecas notáveis |
|---|---|---|---|
| R | Vocação estatística; comunidade científica; integra com SIG | Krigagem E–T, processos pontuais, painéis espaciais, scan | sf; terra/stars; gstat; spTimer; INLA; spatstat; stpp; lgcp; smerc; SpatialEpi; spdep/spatialreg; splm |
| Python | Versátil; ML/IA; big data; dashboards | Séries/grids E–T, previsão, aprendizado, produção | geopandas; rasterio/rioxarray; xarray+dask; PySAL (esda, spreg); scikit-learn; statsmodels; pymc/cmdstanpy; PyTorch/TF |
| MATLAB | Ambiente numérico estável | Modelagem de sinais/processos; protótipos | Toolboxes Mapping e Statistics & ML |

Comparação curta. Geoestatística E–T, processos pontuais e scan: R é mais completo. Dados massivos, ML e entrega: Python. Prototipagem numérica: MATLAB.



### 5.3.3 Aplicativos e Serviços Especializados

Por que usar ferramentas focadas?  
Algumas tarefas — como detecção de clusters com significância ou extração de séries multitemporais de satélite — ficam mais seguras e eficientes com soluções dedicadas. Essas ferramentas complementam R/Python e não os substituem.

Quadro 5 — Especializados

| Ferramenta | O que faz bem | Observações |
|---|---|---|
| SaTScan | Scan statistics espaço-temporais (Poisson/Binomial/Normal), *p*-values | Integra com R/Python por scripts; padrão em vigilância |
| Google Earth Engine (GEE) | Séries temporais de satélite em escala planetária | Excelente para covariáveis E–T; análise estatística fora do GEE |
| PostGIS (+ extensões) | Consultas e gestão E–T em banco relacional | Infra de dados; combine com R/Python para modelar |

Comparação curta. Precisa de clusters auditáveis? SaTScan. Raster multitemporal pesado? GEE. Operação contínua com versionamento de dados? PostGIS.



### 5.3.4 Qual ferramenta para qual objetivo?

Como decidir rápido?  
Pense no resultado que precisa: explorar, modelar, detectar hotspots, prever com incerteza, ou operacionalizar.

- Explorar/comunicar → SIG (QGIS/ArcGIS Pro): mapas, animações, story maps.  
- Interpolar/prever com incerteza → R (gstat, spTimer, INLA) e Python (xarray/dask + implementações de krigagem).  
- Eventos e risco → R (spatstat, stpp, lgcp, smerc/SaTScan); Python (PySAL + integrações).  
- Painel espacial → R (spatialreg, splm) e Python (PySAL.spreg) com efeitos direto/indireto/total.  
- Previsão/ML em larga escala → Python (DL, GNNs, ConvLSTM); incerteza explícita → R–INLA.



### 5.3.5 Interoperabilidade e Fluxo de Trabalho

Por que isso importa?  
Resultados confiáveis dependem de reprodutibilidade e de formatos que circulam bem entre ferramentas.

- **Formatos padrão:** GeoPackage (vetor), COG (raster), NetCDF/Zarr (grades E–T), Parquet (tabelas grandes).  
- **Fluxo típico:** SIG (checar/limpar) → R/Python (modelar/validar) → SIG (publicar mapas preditos/risco).  
- **Reprodutibilidade:** R Markdown/Quarto, Jupyter; versionamento de dados e scripts; registrar parâmetros (semivariograma, \(W\), *bandwidths*, janelas de scan).  
- **Escalabilidade:** xarray+dask (Python); INLA/SPDE (R) para campos contínuos; *tiling* para rasters grandes.




## 5.4 Boas práticas na modelagem espaço-temporal

Uma análise E–T sólida se apoia em poucos princípios — simples de enunciar e fáceis de verificar.

**1) Validação no espaço e no tempo.** Resultados só são críveis quando testados sem “vazamento” entre treino e teste. Separe dados em blocos espaciais (regiões, hexágonos, malhas) e janelas temporais (rolling origin, blocos de meses), evitando que vizinhos próximos ou períodos futuros influenciem o ajuste. Vá além da acurácia média: inspecione erros por área e por período; padrões sistemáticos em certas regiões ou estações revelam fragilidades que um único número esconde.

**2) Incerteza visível.** Mapa, série ou tabela sem variância/intervalo conta só metade da história. Em geoestatística, reporte variância de krigagem e probabilidades de excedência; em painéis espaciais, traga impactos direto/indireto/total com intervalos, distinguindo curto vs. longo prazo quando houver dinâmica; em processos pontuais, use envelopes de simulação e diagnósticos de intensidade; em clustering, indique significância (ou níveis qualitativos) e trate múltiplas comparações. O leitor precisa ver quanto estimamos e quão confiantes estamos.

**3) Robustez a escolhas razoáveis.** Conclusões não podem depender de um único variograma, de uma única vizinhança ou de um único *bandwidth*. Em geoestatística, compare modelos de covariância e discuta o alcance; em painéis, varie a matriz $W$ (contiguidade, distância, $k$-vizinhos, rede) e examine a sensibilidade dos impactos; em métodos de densidade/varredura, justifique $(h_s,h_t,r_s,r_t)$ com curvas de sensibilidade. Se pequenos ajustes derrubam o achado, o problema é menos científico e mais de parametrização.

**4) Reprodutibilidade como critério de qualidade.** Registre CRS/unidades, proveniência e transformações; congele versões de pacotes e scripts; fixe sementes aleatórias; salve, em arquivo de configuração, as escolhas que afetam o resultado (janelas, $W$, *bandwidths*). Outro analista, com o mesmo material, deve reconstruir o estudo e chegar essencialmente às mesmas conclusões.

**5) Fronteira clara entre exploração e inferência.** A exploração (EDA) serve para conhecer o dado, levantar hipóteses e escolher escalas; a inferência confirma (ou refuta) essas hipóteses em um conjunto não usado na exploração. Misturar etapas inflaciona desempenho e produz *overfitting* “guiado por visualização”. Declare o que foi exploratório e o que foi confirmatório; documente regras de seleção de variáveis e critérios de parada antes de abrir o conjunto de teste.

Cuidados transversais que evitam tropeços comuns: alinhar fusos e horário de verão; considerar exposição/offset em dados de eventos (população, fluxo); discutir efeitos de borda e MAUP quando houver agregação; verificar autocorrelação residual (no espaço, no tempo e no espaço–tempo) após o ajuste.

Em suma, boa prática não é sobre a ferramenta “certa”, e sim sobre disciplina: validar de forma honesta, explicitar a incerteza, testar a solidez das escolhas, tornar o caminho reproduzível e separar curiosidade de confirmação. Isso é o que mantém a análise E–T confiável — e útil para decidir.



