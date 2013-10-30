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

## Casos 

A partir de agora, estão listados os casos de uso normais, bem como alternativas previstas para necessidades extraordinárias.

------

#### Carregar Arquivo diário

No fluxo normal para o posicionamento do arquivo diário, o Distribuidor:

1. Gera o arquivo com todas as movimentações do dia anterior em seu sistema;
2. Copia o arquivo para a estrutura de pastas, dentro da pasta do mês correspondente (ex.: 2013\02.Fevereiro\ZZZ.txt).

_**Importante lembrar que "Os arquivos diários devem conter todas as movimentações dos dias nele contidos".**_

------

#### Carregar Arquivo consolidado

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
