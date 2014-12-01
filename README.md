# 1. [Delphos] Criar ETL para extrair 3 datas bases de sinistros [ETL] [16h] [8 pontos]
Criar processo de etl que extraia de forma parametrizada 3 datas bases do datamart de sinistros e construa retroativamente esses dados.

Precisamos que o datamart já nos dê - pelo menos:
 - Data de competência
 - Valor do sinistro
 - Tipo [DFI/MIP]
 - Valor de pagamento.
 
# 2. [Delphos] Criar cubo de Sinistros dentro do schema [Criação do cubo] [2h] [3 pontos]
Criar dentro do schema pre-existente, do Projeto Caixa, o cubo de Sinistros, para trazer as informações construídas através da Issue "[Delphos] Criar ETL para extrair 3 datas bases de sinistros".
 
# 3. [Delphos] Incluir Demais informações gerais no cubo de sinistros [ETL] [8h] [5 pontos]
Complementar etl para incluir informações que pertencem tanto a DFI quanto a MIP que não foram entregues na issue "[Delphos] Criar ETL para extrair 3 datas bases de sinistros".

# 4. [Delphos] Complementar informações gerais no dubo de Sinistros [Criação do cubo] [2h] [3 pontos]
Complementar campos no cubo, inclusos na issue "3. [Delphos] Incluir Demais informações gerais no cubo de sinistros".

# 5. [Delphos] Incluir dimensões univaloradas de MIP [ETL] [8h] [5 pontos]
Incluir no ETL de Sinistros, as informações que não são multivaloradas, referentes a MIP.
A principio, elas podem ficar na fato, mas deve-se estudar as melhores formas de se implementar isso.

# 6. [Delphos] Incluir dimensões univaloradas de DFI [ETL] [8h] [5 pontos]
Incluir no ETL de Sinistros, as informações que não são multivaloradas, referentes a DFI.
A principio, elas podem ficar na fato, mas deve-se estudar as melhores formas de se implementar isso.

# 7. [Delphos] Incluir dimensões multivaloradas de MIP [ETL] [20h]
Incluir no ETL de Sinistros, as informações multivaloradas, referentes a MIP.
A principio, elas podem ficar em tabelas-ponte [bridge], mas deve-se estudar as melhores formas de se implementar isso.

A Razão inicial pela escolha das tabelas bridge, é que a abordagem de se contar o valor do sinistro uma vez ao longo dos registros pode comprometer algumas visões que se deseja fazer.

Ex.: Imagine a seguinte massa de dados - single table:
```
COD_SINISTRO ....... COD_LAUDO ....... VALOR_SINISTRO
sinistro1 .......... Laudo1 .......... 1200,00
sinistro1 .......... Laudo2 .......... 0
```
Se eu quiser fazer uma analise filtrando pelo Laudo2, eu não terei o valor do sinistro.

Poderíamos fazer operações matemáticas para dividir o VALOR_SINISTRO que poderia até permear todos os registros, mas temos que estudar a complexidade que seria adicionada ao desenvolvimento e à utilização.


# 7. [Delphos] Incluir dimensões multivaloradas de DFI [ETL] [20h]
### [Delphos] Incluir dimensões multivaloradas de DFI - LAUDO DE VISTORIA INICIAL
LAUDO DE VISTORIA ESPECIAL
LAUDO TECNICO DO INSTITUTO
LAUDO DE INSPEÇÃO DE OBRA
RELATORIO DE VISTORIA COMPLEMENTAR
ORÇAMENTO
CONTRATO
ADITIVO DO CONTRATO
PAGAMENTO EM ESPECIE



Incluir no ETL de Sinistros, as informações multivaloradas, referentes a DFI.
A principio, elas podem ficar em tabelas-ponte [bridge], mas deve-se estudar as melhores formas de se implementar isso.



A Razão inicial pela escolha das tabelas bridge, é que a abordagem de se contar o valor do sinistro uma vez ao longo dos registros pode comprometer algumas visões que se deseja fazer.

Ex.: Imagine a seguinte massa de dados - single table:
```
COD_SINISTRO ....... COD_LAUDO ....... VALOR_SINISTRO
sinistro1 .......... Laudo1 .......... 1200,00
sinistro1 .......... Laudo2 .......... 0
```
Se eu quiser fazer uma analise filtrando pelo Laudo2, eu não terei o valor do sinistro.

Poderíamos fazer operações matemáticas para dividir o VALOR_SINISTRO que poderia até permear todos os registros, mas temos que estudar a complexidade que seria adicionada ao desenvolvimento e à utilização.


8. [Delphos] Inserir informações univaloradas de Sinistros - MIP [Criação do cubo] [2h]
Inserir no schema xml do cubo, as informações univaloradas trazidas pela issue "[Delphos] Incluir dimensões univaloradas de MIP"
As informações já devem conter - se informado pela Delphos - a organização proposta pelo cliente.

9. [Delphos] Inserir informações univaloradas de Sinistros - DFI [Criação do cubo] [2h]
Inserir no schema xml do cubo, as informações univaloradas trazidas pela issue "[Delphos] Incluir dimensões univaloradas de DFI"
As informações já devem conter - se informado pela Delphos - a organização proposta pelo cliente.

10. [Delphos] Inserir informações multivaloradas de Sinistros - DFI [Criação do cubo] [2h]
Inserir no schema xml do cubo, as informações multivaloradas trazidas pela issue "[Delphos] Incluir dimensões multivaloradas de DFI"
As informações já devem conter - se informado pela Delphos - a organização proposta pelo cliente.

11. [Delphos] Inserir informações multivaloradas de Sinistros - MIP [Criação do cubo] [2h]
Inserir no schema xml do cubo, as informações multivaloradas trazidas pela issue "[Delphos] Incluir dimensões multivaloradas de MIP"
As informações já devem conter - se informado pela Delphos - a organização proposta pelo cliente.

