# Processo de captura e processamento de arquivos

###### _Para consumo dos Distribuidores_

Este documento trata sobre regras de negócio desde a emissão de arquivos texto pelos Distribuidores Mondelez até a captura destes pelo processo de ETL dentro do PGV e posterior importação para o banco de dados do do projeto.

A seguir, ilustramos o esquema geral de envio, captura, classificação e consolidação dos arquivos.

![Esquema de Captura][esquema-captura]

[esquema-captura]: https://raw.github.com/oncase/somedocs/master/img/doc-cockpit-images/Overview%20Projeto%20Kraft.png "Esquema de captura"

## Canal para envio de arquivos

------

A submissão do arquivos de texto será feita através da cópia destes em pastas do Google Drive, com o uso da credencial (login/senha) fornecida, de acordo com as regras a seguir descritas neste documento.

O responsável por fazer a coordenação dos acessos é o Márcio Silva (marcio.silva2@mdlz.com).

## Tipos de arquivos

------

### Arquivos Diários

Serão considerados arquivos diários, aqueles que estiverem dentro das pastas com nomes de meses do ano:
* 01.Janeiro
* 02.Fevereiro
* 03.Março
* 04.Abril
* 05.Maio
* 06.Junho
* 07.Julho
* 08.Agosto
* 09.Setembro
* 10.Outobro
* 11.Novembro
* 12.Dezembro

**Não** serão considerados arquivos diários a serem importados, aqueles TXTs que porventura estiverem em locais diferentes das pastas imediatamente acima listadas. Isto significa que não devem ser criadas subpastas dentro desta estrutura.

------

###### Completude
Os arquivos diários devem conter **todas** as movimentações dos dias nele contidos.

Isto significa que sim, o Usuário pode gerar um arquivo diário com movimentações de mais de um dia.

Entretanto, ao se gerar, por exemplo, um arquivo com os dias 04, 05 e 06 de Junho, o Usuário deve **garantir** que todas as movimentações dos dias 04, 05 e 06 de Junho estejam inclusas no documento.

Isto acontece, porque antes de inserir um arquivo diário X, que contém os dias 04, 05 e 06 de Junho, o sistema vai eliminar da base de valores, todas as movimentações dos dias 04, 05 e 06 de Junho.

------

###### Domínio
Um arquivo diário **não** deve conter informações de meses diferentes.

Isso significa dizer que, arquivos que contiverem movimentações de meses diferentes serão descartados.


------


### Arquivos Consolidados

Os arquivos serão considerados consolidados, quando alocados dentro da subpasta de nome **Consolidado** do mês correspondente, como demonstrado no exemplo:
* DISTRIBUIDOR
    * 2013
        * 02.Fevereiro 
            * **Consolidado <----**

Cada mês deverá conter apenas um arquivo consolidado, entretanto, quando da necessidade de correção, um novo arquivo poderá ser posicionado na mesma pasta sem sobrescrever o arquivo antigo. O Sistema é inteligente o suficiente para processar o arquivo mais recente por último.

**Não** serão considerados arquivos consolidados, aqueles posicionados em locais diferentes do especificado. Subpastas não devem ser criadas, pois os arquivos nelas contidos serão descartados.


------

###### Mês de referência e forma de processamento

O _Mês de Referência_ de um arquivo consolidado será capturado pelo nome da pasta onde ele foi copiado.

No exemplo dado...

* DISTRIBUIDOR
    * 2013
        * 02.Fevereiro 
            * **Consolidado**
                * **ZZZZZZ.txt <----**

O _Mês de Referência_ do arquivo ZZZZZZ.txt, será **Fevereiro de 2013**.

**Atenção** para o local onde o arquivo deve ser salvo, pois a carga de ~~cada~~ um arquivo Consolidado:
* Apaga **toda** a movimentação do _Mês de Referência_; e depois
* Carrega todo o arquivo como sendo o responsável por trazer todas as movimentações daquele _Mês de Referência_.



------

###### Domínio

Arquivos consolidados devem conter a movimentação de somente um mês. 

Não é permitido, um TXT conter dados referentes a mais de um mês. Estes serão descartados.

------

###### Junção de arquivos

Caso o seu sistema gere o consolidado para um mês em dois ou mais arquivos, você pode utilizar a ferramenta [File Merger](http://oncase.com.br/) para fazer a unificação dos seus TXTs em apenas um.

Toda a documentação de utilização está disponível em http://oncase.com.br/.

------

## Perguntas Frequentes

A partir de agora, estão listados os casos de uso normais, bem como alternativas previstas para necessidades extraordinárias.

------

#### Como carregar Arquivo diário?

No fluxo normal para o posicionamento do arquivo diário, o Distribuidor:

1. Gera o arquivo com todas as movimentações do dia anterior em seu sistema;
2. Copia o arquivo para a estrutura de pastas, dentro da pasta do mês correspondente (ex.: 2013\02.Fevereiro\ZZZ.txt).

_**Importante lembrar que "Os arquivos diários devem conter todas as movimentações dos dias nele contidos".**_

------

#### Como carregar Arquivo consolidado?

No fluxo normal para o posicionamento do arquivo diário, o Distribuidor:

1. Até o dia 10 de cada mês (M), gera o arquivo com todas as movimentações somente do mês anterior (M-1);
2. Copia o arquivo para a estrutura de pastas, dentro da pasta Consolidado do mês correspondente (ex.: 2013\02.Fevereiro\Consolidado\ZZZ.txt).

_**Importante lembrar que "Os arquivos consolidados devem conter todas as movimentações do Mês de Referência".**_

------

#### Como substituir arquivo enviado erroneamente?

O Distribuidor deve gerar um novo arquivo e copiá-lo para a pasta do mês correspondente. Caso se trate da sustituição de um arquivo consolidado, o Distribuidor
deverá copiar o novo arquivo consolidado para a pasta Consolidado dentro da pasta do mês correspondente.

O Distribuidor **não** deverá apagar o arquivo que deseja substituir,
pois o sistema irá sempre considerar os dados do arquivo enviado por último e desprezar dados de qualquer outro arquivo anterior que se refira ao mesmo período.

------

#### Como corrigir valores de arquivos já enviados?

Sempre que for preciso corrigir valores de dias anteriores, os quais já tiveram seus arquivos diários copiados para o Google Drive, o Distribuidor deverá informar todas
as informações que continham no arquivo diário enviado e incluir as alterações que deseja que sejam feitas. Isso ocorre, pois cada arquivo enviado apaga todas as informações
que haviam sido processadas. De forma que uma atualização não acontece de forma incremental, mas com reprocessamento completo do dia em questão.

_Exemplo de um erro_:
Considere o caso em que o Distribuidor precise, por exemplo, lançar um extorno de $500 na movimentação de um dia que já teve seu arquivo diário copiado para a pasta 
no Google Drive. Se o distribuidor enviar um txt apenas com o valor do extorno, o saldo final daquele dia será -500, pois todos os valores que foram informados no 
arquivo anterior serão desprezados e será considerado apenas o valor contido no novo arquivo, ou seja, -500.

------

#### Uma linha com erro pode invalidar todo o arquivo?

Sim. Uma linha com erro é suficiente para que todo o arquivo seja descartado.

------

#### Um arquivo que contém apenas dados de Sell Out ou apenas dados de Estoque é válido?

Sim. Para que um arquivo seja processado, ele deve possuir ao menos registros de estoque ou registros de sell out.

------

#### Eu posso enviar um arquivo diário contendo Sell Out de uma data e Estoque de outra data?

Sim. Desde que a data do sell out e a data do estoque estejam dentro do mês de referência.
O mês de referência é sempre o da pasta onde o arquivo foi colocado. Ou seja, um arquivo postado
na pasta DISTRIBUIDOR\2013\10.Outubro terá como mês de referência 201310 (Outubro de 2013). 
Se o arquivo contiver valor de estoque do dia 09 de outubro e valor de sell out do dia 10 de outubro
não haverá problema, mas caso qualquer um dos valores esteja fora do mês de referência, o arquivo
será marcado como DESCARTAR por conter dias de outros meses.

-------

#### O que faz com que o arquivo seja classificado com erro?

Um arquivo pode ser rejeitado por um dos motivos abaixo:

**Captura em Local Inadequado** - Arquivo capturado em uma pasta fora da estrutura esperada. Espera-se que o arquivo esteja dentro da pasta *'Distribuidor/Ano/Mes'* (para arquivos diários) ou *'Distribuidor/Ano/Mes/Consolidado'* (para arquivos consolidados).

**Contém Dias de Outro Mês** - A data de emissão do documento não pertence ao mês de referência. O mês de referência é sempre o da pasta onde o arquivo foi postado.
                               Se por exemplo, o arquivo foi postado na pasta *'DISTRIBUIDOR/2013/10.Outubro'* o seu mês de referência será **201310**. Caso a data de emissão do 
								       arquivo seja de outro mês que não outubro de 2013, este arquivo será classificado com este erro.

**Erro de Estoque** -  Valor de Quantidade de Unidades (do Distribuidor) em estoque for inválido.

**Erro de Sell Out** - Valor inválido em qualquer um dos campos abaixo:
* Numero de linha do detalhe do documento
* Quantidade de unidades
* Valor faturado unitario. (Com Impostos, com descontos)
* Valor Bruto unitario. (Com Impostos, sem descontos)
* Valor do ICMS unitario

**Sem Estoque e Sem Sell Out** - Arquivo postado não contém registro de estoque nem registro de sell out. Para que um arquivo seja processado, ele deve possuir ao menos registros de estoque ou registros de sell out.

**Distribuidor Não Encontrado** - O arquivo foi postado em uma pasta de um distribuidor que não existe no cadastro de distribuidores.

**Distribuidor Diferente** - O código de loja informado não pertence ao distribuidor.
