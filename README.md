# Sistema de Análise Patológica — Explicação de uso das estruturas/algoritmos

## Identificação do Grupo — 2ESR (CodeBreakers)
- 558385 - Alexia Ramalho Izidio Dos Santos  
- 554746 - Beatriz Vieira de Novais  
- 559008 - Hellen Aparecida Moura Silva  
- 557397 - Lorenzo Adinolfi Acquesta  
- 558859 - Wendell Dos Santos Silva

---

## Resumo do projeto
Nas unidades de diagnóstico, o consumo diário de insumos (reagentes e descartáveis) não é registrado com precisão,
dificultando o controle de estoque e a previsão de reposição. DPara esta atividade será necessário organizar os dados de
forma eficiente com estruturas de dados e algoritmos clássicos. Simule dados de consumo diário de insumos e implemente
as seguintes soluções:

---

Como executar (rápido)
1. Abra `consumo_insumos_notebook.ipynb` no Google Colab ou Jupyter.  
2. Execute as células na ordem.  
3. Verifique as tabelas exibidas com `display()` para insumos, registros de consumo, fila, pilha, resultados de busca e ordenações.

---

## Como cada estrutura/algoritmo foi usada no contexto do problema

Objetivo geral: organizar e processar registros de consumo diário de insumos (reagentes e descartáveis), com foco em garantir que seja possível:
- registrar consumos em ordem cronológica,
- consultar consumos em ordem inversa (últimos primeiro),
- localizar rapidamente um insumo/registro,
- ordenar insumos por quantidade consumida ou por validade,
- gerar entradas para relatórios e controle de estoque.

Abaixo está o mapeamento do que foi implementado no notebook, com explicação contextual, onde está no código e observações práticas.

1) Fila (Queue)
- Onde: Célula 5 (classe `Fila`) + Célula 6 (uso com `df_consumo`).
- O que faz no código: cada registro diário de consumo é `enqueue` na fila na ordem cronológica (data de consumo). Em seguida a fila pode ser processada na mesma ordem em que os consumos foram registrados.
- Por que é útil: preserva sequência temporal de lançamentos — essencial para auditoria e para rotinas que processam consumos diários (ex.: atualização de estoque, geração de relatórios diários).
- Complexidade: enqueue O(1), dequeue O(1) (com `collections.deque`).
- Exemplo de uso prático: um processo de reposição pode `dequeue` os registros em ordem para atualizar níveis de estoque e acionar pedidos.

2) Pilha (Stack)
- Onde: Célula 5 (classe `Pilha`) + Célula 7 (uso com `df_consumo`).
- O que faz no código: empilha (push) todos os registros de consumo; ao fazer `pop` recuperamos os últimos consumos lançados (LIFO).
- Por que é útil: permite consultas/inspeções recentes rapidamente e é útil para operações de “undo” — se um lançamento foi errado, a última versão pode ser retirada ou revertida.
- Complexidade: push O(1), pop O(1).
- Exemplo de uso prático: auditoria dos últimos 10 lançamentos de consumo para investigação rápida de discrepâncias.

3) Busca Sequencial
- Onde: Célula 8 (`busca_sequencial(df_records, chave, valor)`).
- O que faz no código: percorre `df_consumo` linha a linha e devolve todos os registros que correspondem à chave/valor buscados (por exemplo, `nome == "Reagente A"`).
- Por que é útil: simples e direta para consultas ad-hoc em pequenos/médios conjuntos de dados ou quando a coleção não está ordenada.
- Complexidade: O(n).
- Observação prática: boa para relatórios rápidos e diagnósticos; em bases muito grandes, considere indexação ou busca binária/hash.

4) Busca Binária
- Onde: Célula 9 (preparação de `lista_insumos` ordenada por `nome`) e Célula 12 (exemplo por `item_id` após ordenar).
- O que faz no código: busca eficiente (O(log n)) em uma lista ordenada de insumos (por `nome` ou `item_id`) para localizar um insumo específico.
- Requisito: a lista precisa estar ordenada pela chave usada na busca (o notebook ordena antes de buscar).
- Por que é útil: quando há necessidade de buscas frequentes e a lista pode ser mantida ordenada (por exemplo, catálogo de insumos), a busca binária reduz muito o tempo de localização.
- Exemplo de uso prático: localização rápida do registro de validade de um item para decidir prioridade de descarte ou reabastecimento.

5) Merge Sort
- Onde: Célula 11 (`merge_sort`), aplicado aos registros agregados por insumo (`df_total`) para ordenar por `total_consumido`.
- O que faz no código: ordena a lista de insumos por total consumido em ordem descendente (ranking de consumo).
- Por que é útil: algoritmo estável e com complexidade garantida O(n log n) mesmo no pior caso — apropriado para criar rankings de consumo que serão usados para prever reposição.
- Exemplo de uso prático: identificar os top-N insumos com maior consumo (prioridade de compra); estabilidade preserva ordem entre elementos com mesmos valores.

6) Quick Sort
- Onde: Célula 11 (`quick_sort`), aplicado para ordenar por `validade` (datas de vencimento).
- O que faz no código: ordena os insumos/registro por data de validade em ordem crescente (mais próximos primeiro).
- Por que é útil: encontrar rapidamente insumos que vencerão em breve permite planejar descarte ou uso preferencial. Quick Sort é geralmente muito rápido na prática.
- Observação: Quick Sort tem pior caso O(n^2) em casos adversos; no notebook usamos uma implementação simples para propósitos educacionais; em produção, considere usar sort nativo de bibliotecas otimizadas ou garantir escolha robusta de pivô.
- Exemplo prático: lista de itens ordenada por validade para alertas de validade próxima (< 30 dias).

7) Agregação de consumo por insumo
- Onde: Célula 10 (`df_total` = groupby sobre `df_consumo`).
- O que faz no código: soma as quantidades consumidas por `item_id` ao longo do período simulado; entrada necessária para ordenar por quantidade consumida (Merge Sort).
- Por que é útil: a ordenação por quantidade consumida precisa de um valor agregado por insumo para priorização de compras e previsão de reposição.

---
