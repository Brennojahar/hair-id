# Hair ID — Cartela & Visagismo

Ferramenta de diagnóstico para salão: coloração pessoal nas 12 estações e visagismo
por formato de rosto e temperamento, a partir de uma foto da cliente.

**No ar:** https://brennojahar.github.io/hair-id/

## Como funciona

Tudo roda dentro do navegador. Nenhuma foto sai do computador e nada é enviado
para servidor nenhum.

1. **Recorte** — o segmentador multiclasse do MediaPipe separa fundo, cabelo,
   pele do rosto, pele do corpo e roupa. A pessoa é recomposta sobre fundo
   branco, e é essa versão que segue para a análise e para o painel. O modelo
   tem ~16 MB e baixa uma vez só, ficando em cache.
2. **Pontos faciais** — o detector facial devolve 478 pontos do rosto.
3. **Geometria** — dessas coordenadas saem as proporções que definem o formato:
   altura sobre largura, testa e mandíbula sobre maçãs, e o ângulo do maxilar.
   As mesmas medidas alimentam a leitura de linhas dominantes (Hallawell).
4. **Cor** — amostras de pele, cabelo e íris são convertidas de sRGB para CIELAB.
   A esclera do olho serve de referência neutra e corrige o balanço de branco da
   foto, no papel que o cartão cinza cumpre na consultoria presencial.
5. **Estação** — os quatro eixos (subtom, profundidade, intensidade, contraste)
   são comparados com as 12 estações e vence a de menor distância ponderada.
6. **Painel** — um PNG 1080 × 1350 desenhado em canvas, pronto para mandar à cliente.

A classificação é determinística: a mesma foto e os mesmos pontos dão sempre o
mesmo resultado. As recomendações não são geradas na hora — saem de três tabelas
fixas (`ESTACOES`, `ROSTOS`, `TEMPERAMENTOS`) no próprio arquivo, o que torna a
saída auditável e editável num lugar só.

## Limites conhecidos

- A cor do **cabelo** sai da máscara de segmentação (mediana aparada, descartando
  22% mais escuro e 22% mais claro para tirar sombra e reflexo). Quando a máscara
  falha, há uma varredura que sobe da testa até cruzar a fronteira de cor com a
  pele. A página mostra onde amostrou e avisa quando a leitura ficou suspeita.
- A **esclera** nem sempre é encontrada limpa — olho pequeno na foto, desviado ou
  com maquiagem. Sem ela o subtom fica à mercê do balanço de branco da foto, e a
  página diz isso.
- O eixo de **intensidade** é o menos mensurável numa foto e pesa pouco na decisão.
- **Subtom neutro** rebaixa a confiança automaticamente: é o caso que mais exige
  drapeado presencial.
- A calibragem das proporções foi feita com poucas fotos. O segundo formato mais
  provável aparece sempre na leitura, e todos os campos continuam editáveis.

## Método

Visagismo segundo **Philip Hallawell** (linhas, formas e temperamento) e coloração
pessoal pelo sistema sazonal expandido de 12 estações. Não existe método
"Claude Juilliard visagisme 3D" — essa referência não corresponde a autor ou
instituição verificável.

## Editar

Arquivo único, sem build. Abra `index.html` e edite. As paletas, os cortes e as
recomendações estão nas três tabelas no topo do `<script>`.
