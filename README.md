Engenharia de Dados: Modelagem Dimensional (Star Schema) - Financial Sample

**Desenvolvido** como parte do desafio prático de modelagem de dados da DIO, este projeto demonstra a transformação de uma base de dados relacional e flat (tabela única) em uma arquitetura dimensional otimizada para Business Intelligence (Star Schema).

---

Objetivo do Projeto
O propósito deste desafio foi aplicar conceitos de modelagem dimensional sobre a base de dados pública `Financial Sample`. A tabela original única foi desmembrada em tabelas fato e dimensão para estruturar um modelo limpo, eliminando redundâncias e otimizando a performance para futuras análises de vendas, produtos e períodos temporais.

---

Processo de Construção do Diagrama

Abaixo estão detalhadas as etapas aplicadas no **Power Query** e na visualização de **Modelagem**:

### 1. Isolamento da Base Original
* A tabela original foi mantida no modelo com o nome de `financials_origem`, configurada no modo oculto. Ela atua estritamente como um backup e base de dados bruta para a geração das demais tabelas, sem poluir a interface do usuário final.

### 2. Criação das Tabelas Dimensão (D_)
A partir de cópias e referências da tabela original, foram selecionadas colunas específicas para compor cada contexto de negócio:
* **`D_Produtos`:** Contém o índice identificador (`ID_Produto`), o nome do produto e métricas agregadas (Média de Unidades Vendidas, Média do Valor de Vendas, Mediana, Valor Máximo e Mínimo).
* **`D_Produtos_detalhes`:** Armazena características financeiras específicas de manufatura e preço de venda de cada produto.
* **`D_Descontos`:** Concentra as informações sobre faixas de descontos (`Discount Band`) aplicadas.
* **`D_Categoria` (Detalhes):** Atuando como a tabela de contexto geográfico e de mercado recomendada pelo desafio, agrupa de forma indexada via coluna condicional as informações de `Segment` (Segmento) e `Country` (País).

### 3. Estruturação da Tabela Fato (F_)
* **`F_Vendas`:** Posicionada no centro do modelo. Armazena a chave substituta (`SK_id`), chaves estrangeiras de ligação e todas as métricas numéricas cruciais para o negócio (como `Sales`, `Profit`, `Units Sold` e custos). Mantém chaves textuais de apoio em conformidade com os requisitos do desafio.

### 4. Criação da Tabela Calendário por DAX
Atendendo aos critérios técnicos solicitados, a tabela de tempo foi construída dinamicamente diretamente na camada de dados utilizando a linguagem **DAX**:
```dax
D_Calendário = 
VAR DataMinima = MIN(F_Vendas[Date])
VAR DataMaxima = MAX(F_Vendas[Date])
RETURN
ADDCOLUMNS (
    CALENDAR (DataMinima, DataMaxima),
    "Ano", YEAR ( [Date] ),
    "Mês Número", MONTH ( [Date] ),
    "Mês Nome", FORMAT ( [Date], "MMMM" ),
    "Trimestre", "T" & FORMAT ( [Date], "Q" )
)
