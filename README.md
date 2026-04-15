# Exercício Prático III — DAX aplicado ao Negócio

## Contexto

Você é o analista de dados da Olist, responsável por construir as métricas que alimentarão um dashboard estratégico no Power BI.

O modelo de dados segue um star schema com as tabelas:

- fato_pedidos (Fato)  
- dim_clientes  
- dim_produtos  
- dim_vendedor  
- dim_reviews  

---

# Bloco 1 — Volume e Operações

### Q01. Número total de pedidos  
Etapa A — Raciocínio: Contabilizar os IDs únicos dos pedidos

Etapa B — DAX: Pedidos = DISTINCTCOUNT(fato_pedidos[id_pedido]) 

---

### Q02. Clientes únicos  
Etapa A — Raciocínio: Contabilizar os IDs únicos dos clientes 

Etapa B — DAX: Clientes = DISTINCTCOUNT(fato_pedidos[id_cliente])  

---

### Q03. Valor total faturado  
Etapa A — Raciocínio: Somar os valores das compras feitas  

Etapa B — DAX: Faturamento Total = SUM(fato_pedidos[valor_produto]) 

---

### Q04. Valor total de frete  
Etapa A — Raciocínio: Somar os valores de todos os fretes registrados  

Etapa B — DAX: Frete Total = SUM(fato_pedidos[valor_frete]) 

---

### Q05. Produtos distintos vendidos  
Etapa A — Raciocínio: Contabilizar os produtos únicos vendidos 

Etapa B — DAX: Produtos Vendidos = DISTINCTCOUNT(fato_pedidos[id_produto]) 

---

### Q06. Vendedores distintos  
Etapa A — Raciocínio: Contabilizar os IDs únicos dos vendedores  

Etapa B — DAX: Vendedores distintos = COUNTROWS(dim_vendedores) 

---

### Q07. Ticket médio  
Etapa A — Raciocínio: Fazer a divisão do faturamento total pelo número total de pedidos  

Etapa B — DAX:  Ticket Médio = DIVIDE([Faturamento Total],
DISTINCTCOUNT (fato_pedidos[id_pedido])) 

---

### Q08. Frete médio  
Etapa A — Raciocínio:  Realizar a média do número total de frete  

Etapa B — DAX: Frete Médio = AVERAGE(fato_pedidos[valor_frete])
---

# Bloco 2 — Satisfação e Qualidade

### Q09. Nota média  
Etapa A — Raciocínio: Realizar a média da soma total de reviews

Etapa B — DAX: Nota Média = AVERAGE(dim_reviews[nota_review]) 

---

### Q10. Pedidos com nota 5  
Etapa A — Raciocínio:  Contabilizar os pedidos com review 5 

Etapa B — DAX: Pedidos Nota 5 = CALCULATE(DISTINCTCOUNT(fato_pedidos[id_pedido]), 
dim_reviews[nota_review] = 5) 

---

### Q11. Pedidos com nota 1 ou 2  
Etapa A — Raciocínio:  Contabilizar os pedidos com review menor que 2  

Etapa B — DAX: Pedidos Baixa Avaliação = CALCULATE(DISTINCTCOUNT(fato_pedidos[id_pedido]), 
dim_reviews[nota_review] <= 2) 

---

### Q12. % de nota 5  
Etapa A — Raciocínio: Divide a quantidade total de pedidos com review 5 pela quantidade total de pedidos 

Etapa B — DAX: % Nota 5 = DIVIDE([Pedidos Nota 5],
DISTINCTCOUNT(fato_pedidos[id_pedido])) 

---

### Q13. % de notas críticas  
Etapa A — Raciocínio:  Divide a quantidade total de pedidos com review menor que 2 pela quantidade total de pedidos

Etapa B — DAX:  % Baixa Avaliação = DIVIDE([Pedidos Baixa Avaliação],
DISTINCTCOUNT(fato_pedidos[id_pedido]))

---

### Q14. Classificação de avaliação (coluna calculada)  
Etapa A — Raciocínio:  Rotular cada nota com um tipo de avaliação 

Etapa B — DAX: classificacao = 
SWITCH(
    dim_reviews[nota_review],
    5, "Excelente",
    4, "Bom",
    3, "Regular",
    2, "Ruim",
    1, "Péssimo",
    "Sem Avaliação"
) 

---

### Q15. Quantidade de avaliações negativas  
Etapa A — Raciocínio: Filtrar a contagem de reviews negativas  

Etapa B — DAX: Avaliações Negativas = CALCULATE(COUNTROWS(dim_reviews),
dim_reviews[classificacao])  

---

# Bloco 3 — Logística

### Q16. Tempo de entrega (coluna)  
Etapa A — Raciocínio: Diferença de tempo entre a data de compra e de entrega  

Etapa B — DAX: Tempo de Entrega = DATEDIFF(fato_pedidos[data_compra], fato_pedidos[data_entrega], DAY) 

---

### Q17. Status de entrega (coluna)  
Etapa A — Raciocínio: Caso a data de entrega seja menor ou igual a data prevista de entrega, retorna no prazo, se não retorna atrasado

Etapa B — DAX: Status de Entrega = IF(fato_pedidos[data_entrega] <= fato_pedidos[data_prevista_entrega], "No Prazo", "Atrasado")

---

### Q18. Tempo médio de entrega  
Etapa A — Raciocínio: Calcular a média do tempo de entrega 

Etapa B — DAX: Tempo Médio de Entrega = AVERAGE(fato_pedidos[Tempo de Entrega]) 

---

### Q19. Pedidos atrasados  
Etapa A — Raciocínio: Filtrar a quantidade de pedidos atrasados  

Etapa B — DAX: Pedidos Atrasados = CALCULATE(COUNTROWS(fato_pedidos), fato_pedidos[Status de Entrega] = "Atrasado") 

---

### Q20. % entregas no prazo  
Etapa A — Raciocínio: Dividir a quantidade de pedidos totais pelos pedidos com status = No prazo  

Etapa B — DAX: % Entregas no Prazo = 
DIVIDE(
    CALCULATE(COUNTROWS(fato_pedidos), fato_pedidos[Status de Entrega] = "No Prazo"),
    COUNTROWS(fato_pedidos)
)  

---

### Q21. Tempo entre aprovação e envio  
Etapa A — Raciocínio:  Diferença entre a data de envio e a data de aprovação 

Etapa B — DAX:  Dias para Envio = DATEDIFF(fato_pedidos[data_aprovacao], fato_pedidos[data_envio], DAY)

---

### Q22. Prazo prometido (coluna)  
Etapa A — Raciocínio: Diferença entre a data prevista e a data de compra  

Etapa B — DAX: Prazo Prometido = DATEDIFF(fato_pedidos[data_compra], fato_pedidos[data_prevista_entrega], DAY) 

---

### Q23. Peso médio dos produtos  
Etapa A — Raciocínio: Fazer a média do peso total dos produtos  

Etapa B — DAX: Peso Médio Produtos = AVERAGE(dim_produtos[peso_g]) 

---

# Bloco 4 — Segmentação

### Q24. Estado do cliente (coluna)  
Etapa A — Raciocínio:  "Puxar" a tabela de estado do cliente para a fato 

Etapa B — DAX: Estado do Cliente = RELATED(dim_clientes[estado_cliente]) 

---

### Q25. Pedidos de SP  
Etapa A — Raciocínio: Contabilizar os pedidos de SP   

Etapa B — DAX: Pedidos SP = CALCULATE(COUNTROWS(fato_pedidos), dim_clientes[estado_cliente] = "SP") 

---

### Q26. Faturamento Sudeste  
Etapa A — Raciocínio: Dividir o faturamento total pelos clientes que compraram em SP, RJ, MG e ES 

Etapa B — DAX:  Faturamento Sudeste = 
CALCULATE(
    SUM(fato_pedidos[valor_produto]), 
    dim_clientes[estado_cliente] IN {"SP", "RJ", "MG", "ES"}
)

---

### Q28. Faturamento cama_mesa_banho  
Etapa A — Raciocínio: Somar os valores de todos os produtos vendidos filtrando a categoria por cama_mesa_banho  

Etapa B — DAX: Faturamento Cama Mesa Banho = 
CALCULATE(
    SUM(fato_pedidos[valor_produto]), 
    dim_produtos[categoria_produto] = "cama_mesa_banho"
) 

---

### Q29. Pedidos de vendedores de SP  
Etapa A — Raciocínio: Cacular os pedidos feitos por vendedores de SP 

Etapa B — DAX: Pedidos Vendedores SP = CALCULATE(COUNTROWS(fato_pedidos), dim_vendedores[estado_vendedor] = "SP") 

---

# Bloco 5 — OKRs

### Q30. % Entregas no Prazo  
Etapa A — Raciocínio:  Dividir os pedidos no prazo pelo total de pedidos 

Etapa B — DAX:  % Entregas no Prazo = 
DIVIDE(
    CALCULATE(COUNTROWS(fato_pedidos), fato_pedidos[Status de Entrega] = "No Prazo"),
    COUNTROWS(fato_pedidos)
)

---

### Q31. % Avaliações Negativas  
Etapa A — Raciocínio: Dividir os pedidos com avaliação negativa pelos pedidos totais  

Etapa B — DAX: % Avaliações Negativas = 
DIVIDE(
    CALCULATE(COUNTROWS(dim_reviews), dim_reviews[nota_review] <= 2),
    COUNTROWS(dim_reviews)
) 

---

### Q32. Tempo Médio de Entrega  
Etapa A — Raciocínio: Calcular a média do tempo de entrega total 

Etapa B — DAX:  Tempo Médio de Entrega = AVERAGE(fato_pedidos[Tempo de Entrega])

---

### Q33. Nota Média (robusta)  
Etapa A — Raciocínio: Calcular a média das notas totais 

Etapa B — DAX: Nota Média = AVERAGE(dim_reviews[nota_review]) 

---

### Q34. Nota média — pedidos atrasados  

Etapa A — Raciocínio: Fazer a média das notas dos pedidos com status atrasado  

Etapa B — DAX: Nota Média Pedidos Atrasados = 
CALCULATE(
    AVERAGE(dim_reviews[nota_review]),
    fato_pedidos[Status de Entrega] = "Atrasado"
) 
