# Atividade Teórica: Usuários Especialistas, IA e Distribuição Segura de Dados

**Aluno(s):** Josué Mateus das Chagas Oliveira, Théo Tavernard da Rocha machado, Alessandro Moura de Oliveira Machado, Ana Karoline Lopes Leite
**Turma:** Introdução a Banco de Dados 2026/2
**Data:** 14/08/2026
**Repositório Git:** https://github.com/josueoliveiraa2006/atividade_bd_afya2026.git

## Resumo Executivo

## Resumo Executivo

A utilização de ferramentas de Inteligência Artificial generativa por usuários especialistas para elaboração de consultas SQL modifica a forma tradicional de interação com os bancos de dados e cria novos desafios para a administração dos dados. Embora essas ferramentas possam facilitar a elaboração de consultas complexas e auxiliar na produção de análises, suas respostas não devem ser consideradas automaticamente corretas ou autorizadas. Uma consulta pode apresentar erros de sintaxe, produzir resultados logicamente incorretos, acessar informações além da necessidade do usuário ou consumir recursos excessivos do Sistema de Gerenciamento de Banco de Dados **(SGBD)**.

Nesse contexto, a distribuição dos dados deve ser realizada de forma controlada, considerando a função de cada usuário e a finalidade de seu acesso. O princípio do menor privilégio deve orientar a concessão de permissões, de modo que cada usuário receba somente os acessos necessários para desempenhar suas atividades. Recursos do PostgreSQL, como `roles`, privilégios e `views`, podem ser utilizados para limitar o acesso a determinados dados e operações.

O DBA mantém papel central nesse modelo. Cabe a esse profissional definir e manter a estrutura do banco, administrar permissões, estabelecer mecanismos de proteção dos dados, preservar sua integridade e acompanhar o desempenho do SGBD. No cenário de utilização de IA, sua atuação também deve contemplar o acompanhamento das consultas realizadas pelos usuários, a identificação de operações inadequadas e a revisão das políticas de acesso conforme as necessidades da organização.

A posição adotada neste trabalho é que a utilização de IA para acesso e análise de dados pode ser admitida, desde que não resulte em acesso irrestrito ao banco. A ferramenta deve estar subordinada às regras de segurança e autorização estabelecidas pela organização e implementadas no SGBD. Dessa maneira, é possível aproveitar os benefícios da automação na elaboração de consultas sem comprometer a confidencialidade, a integridade, a disponibilidade e a conformidade dos dados, especialmente quando houver tratamento de dados pessoais sujeitos à Lei Geral de Proteção de Dados Pessoais (LGPD).


## 1. Desenvolvimento Teórico

### 1.1 O que é o DBA e quais suas funções?
O **Database Administrator (DBA)**, ou administrador de banco de dados, é o profissional responsável pela administração técnica e pelo controle do ambiente de banco de dados de uma organização. Sua atuação envolve aspectos relacionados à estruturação dos dados, ao controle de acesso, à segurança, à integridade, à disponibilidade e ao desempenho do **Sistema de Gerenciamento de Banco de Dados (SGBD).**

Em um ambiente PostgreSQL, o DBA possui papel fundamental na definição de quais usuários podem acessar determinados objetos do banco, quais operações podem executar e quais informações podem ser disponibilizadas para cada perfil. O PostgreSQL utiliza o conceito de *roles* para administrar permissões. Uma role pode representar um usuário individual ou um conjunto de usuários, permitindo que privilégios sejam concedidos de maneira organizada.

A importância do DBA aumenta em ambientes nos quais usuários especialistas passam a utilizar ferramentas de Inteligência Artificial para formular consultas diretamente sobre os dados. Embora a ferramenta de IA possa produzir uma instrução SQL sintaticamente válida, isso não significa que a consulta esteja necessariamente correta do ponto de vista dos requisitos da organização, nem que sua execução deva ser autorizada. Dessa forma, a administração do banco não pode ser substituída pela geração automática de consultas.

**Definição do esquema**

Uma das funções fundamentais do DBA é participar da **definição e manutenção do esquema do banco de dados**. O esquema estabelece a organização lógica dos dados, incluindo tabelas, colunas, relacionamentos, restrições, índices, funções, views e outros objetos.

A definição adequada do esquema é importante porque determina como as informações serão armazenadas e relacionadas. Em um sistema de vendas, por exemplo, podem existir tabelas como clientes, produtos, vendas e itens_venda. O DBA deve assegurar que essas estruturas representem adequadamente as necessidades do sistema e que existam mecanismos para preservar a consistência dos dados.

Nesse cenário, o uso de IA por usuários especialistas não elimina essa responsabilidade. Pelo contrário, consultas geradas automaticamente precisam respeitar a estrutura existente. Uma consulta pode, por exemplo, relacionar tabelas incorretamente e produzir resultados aparentemente plausíveis, mas que não correspondem aos dados que deveriam ser analisados.

Assim, o DBA deve estabelecer uma estrutura de banco que facilite o acesso autorizado às informações e dificulte operações inadequadas. A organização correta do esquema também contribui para o desempenho, pois permite que índices, relacionamentos e demais estruturas sejam planejados de acordo com as operações realizadas com maior frequência.

**Estrutura de dados e acesso**

Outra responsabilidade do DBA consiste em administrar a estrutura utilizada para armazenamento e acesso aos dados. Isso inclui tabelas, índices, views, funções e outros objetos necessários ao funcionamento do banco.

No PostgreSQL, os privilégios podem ser concedidos de maneira específica para diferentes tipos de objetos. Entre os privilégios existentes estão **SELECT**, **INSERT**, **UPDATE**, **DELETE**, **CREATE**, **CONNECT** e **EXECUTE**, entre outros. Dessa forma, o acesso não precisa ser tratado como uma autorização única para todo o banco.

Essa característica é particularmente relevante quando usuários especialistas utilizam ferramentas de IA. Um usuário que precisa realizar consultas analíticas pode necessitar de permissão de leitura sobre determinadas informações, mas isso não significa que deva possuir autorização para alterar ou excluir registros.

Por exemplo, um especialista responsável por analisar as vendas poderia receber permissão para consultar informações agregadas de vendas, mas não necessariamente para executar:

**DELETE FROM vendas;**

ou:

**UPDATE clientes**
**SET cpf = ...;**

O controle de acesso deve, portanto, considerar a finalidade da atividade realizada pelo usuário. A existência de uma ferramenta capaz de produzir SQL não deve resultar em autorização irrestrita sobre o banco.

**Autorização de acesso**

A autorização determina quais operações cada usuário ou grupo de usuários está autorizado a realizar depois de ter sido autenticado.

No PostgreSQL, as roles são o principal mecanismo para organizar essa autorização. Uma role pode receber privilégios sobre determinados objetos e também pode ser membro de outra role, possibilitando a organização das permissões por grupos.

No contexto analisado, o DBA deve evitar a utilização de uma única conta com privilégios elevados para todos os usuários especialistas. Cada usuário deve possuir somente as permissões necessárias para sua atividade.

Essa abordagem corresponde ao princípio do **menor privilégio**, segundo o qual uma conta deve receber apenas os privilégios necessários para executar suas funções. O princípio reduz o impacto de erros e de acessos indevidos. Se uma consulta produzida por uma ferramenta de IA estiver incorreta, por exemplo, uma conta que possui somente **SELECT** terá uma capacidade de alteração muito menor do que uma conta com privilégios de administrador.

É importante observar que a autorização deve ser aplicada independentemente da origem da consulta. Uma instrução SQL escrita manualmente por um usuário e uma instrução produzida por uma ferramenta de IA devem estar sujeitas às mesmas regras de autorização. A origem automática do comando não constitui uma justificativa para conceder privilégios adicionais.

**Regras de integridade**

O DBA também participa da definição e manutenção de mecanismos que preservam a integridade dos dados.

Integridade significa, nesse contexto, manter os dados corretos, coerentes e compatíveis com as regras estabelecidas para o sistema. O banco pode utilizar mecanismos como chaves primárias, chaves estrangeiras, restrições **NOT NULL**, **UNIQUE**, **CHECK** e outras regras para impedir determinados estados inválidos.

Em um sistema de vendas, por exemplo, uma venda pode precisar estar associada a um cliente existente. Uma chave estrangeira pode impedir que seja registrada uma venda apontando para um cliente inexistente.

Esse aspecto é especialmente importante quando usuários especialistas utilizam IA para gerar comandos SQL. Uma consulta pode estar sintaticamente correta e, ainda assim, representar uma operação inadequada para o modelo de dados. As restrições de integridade funcionam como uma camada adicional de proteção, impedindo que determinadas operações incompatíveis com as regras do banco sejam realizadas.

Portanto, o DBA não deve considerar a correção sintática de uma consulta como sinônimo de correção da operação. A instrução precisa também respeitar as regras de negócio, a estrutura do banco e as permissões concedidas ao usuário.

**Relação entre o DBA e o uso de IA**

A utilização de IA por usuários especialistas modifica a forma como o acesso aos dados é realizado, mas não modifica as responsabilidades fundamentais da administração do banco.

O DBA continua responsável por estabelecer uma estrutura adequada, definir mecanismos de autorização, preservar a integridade dos dados e controlar os recursos do SGBD. A principal diferença é que, nesse cenário, existe uma nova fonte de consultas SQL que pode produzir comandos em grande quantidade e com diferentes níveis de complexidade.

Por essa razão, a atuação do DBA deve considerar que uma consulta gerada por IA pode apresentar três situações distintas:

*Erro de sintaxe: a instrução não pode ser executada pelo SGBD.*
*Erro lógico: a instrução é válida para o PostgreSQL, mas produz um resultado diferente daquele pretendido pelo usuário.*
Operação válida, porém inadequada: a consulta está correta, mas pode acessar informações que o usuário não deveria consultar ou consumir recursos excessivos do servidor.

A administração do banco, portanto, permanece como uma função de controle independente da ferramenta utilizada para produzir as consultas.

### 1.2 Perfis de usuários de banco de dados

Os usuários de um banco de dados não possuem necessariamente as mesmas necessidades, conhecimentos técnicos ou responsabilidades. A classificação dos usuários é importante porque auxilia na definição dos mecanismos de acesso e das permissões adequadas a cada atividade.

No cenário apresentado, destacam-se quatro perfis: programadores de aplicações, usuários sofisticados, usuários especialistas e usuários navegantes. A distinção entre esses grupos permite compreender por que o usuário especialista representa uma situação particularmente relevante quando ferramentas de IA passam a produzir consultas diretamente sobre o banco.

**Programadores de aplicações**

Os programadores de aplicações são profissionais responsáveis pelo desenvolvimento de sistemas que utilizam o banco de dados como parte de sua infraestrutura.

Normalmente, esses profissionais não interagem com o banco apenas para realizar consultas ocasionais. Eles desenvolvem aplicações que executam operações de leitura e escrita de acordo com as funcionalidades previstas no sistema.

Por exemplo, em um sistema de vendas, um programador pode desenvolver uma funcionalidade responsável por registrar uma venda. A aplicação executará comandos SQL ou utilizará mecanismos de acesso ao banco para realizar essa operação.

A principal característica desse perfil é que o acesso aos dados geralmente ocorre por meio de uma aplicação previamente desenvolvida. As operações esperadas são conhecidas e fazem parte da lógica do sistema.

Isso permite ao DBA estabelecer permissões específicas para a aplicação. Em vez de permitir que cada usuário final tenha acesso direto a todas as tabelas, pode-se concentrar determinadas operações na camada da aplicação, reduzindo a exposição direta da estrutura do banco.

No contexto de IA, esse perfil possui uma diferença importante em relação ao usuário especialista: o programador normalmente trabalha dentro de uma aplicação ou de uma estrutura de desenvolvimento previamente definida, enquanto o usuário especialista pode formular consultas diretamente para fins de análise.

**Usuários sofisticados**

Os usuários sofisticados são aqueles que possuem conhecimento técnico suficiente para interagir diretamente com o banco de dados por meio de linguagens de consulta e ferramentas especializadas.

Esse usuário não depende necessariamente de uma aplicação tradicional para obter todas as informações de que necessita. Ele pode formular consultas, utilizar ferramentas de análise ou desenvolver procedimentos próprios para explorar os dados.

Um exemplo seria um analista com conhecimento de SQL que necessita realizar uma consulta específica para comparar o volume de vendas entre diferentes períodos.

Esse perfil possui maior autonomia que um usuário comum, mas essa autonomia não implica necessariamente em acesso irrestrito. O DBA deve continuar determinando quais objetos podem ser consultados e quais operações podem ser realizadas.

A utilização de ferramentas de IA aumenta ainda mais essa autonomia, pois o usuário pode solicitar que uma ferramenta formule uma consulta SQL complexa sem necessariamente escrever toda a instrução manualmente.

**Usuários especialistas**

Os usuários especialistas constituem o principal foco do cenário apresentado.

Esse perfil possui conhecimento especializado em determinada área de atuação e utiliza o banco de dados para realizar análises ou executar atividades específicas relacionadas à sua área. O termo "especialista" não significa necessariamente que o indivíduo seja administrador de banco de dados. Trata-se de um usuário que possui conhecimento suficiente de sua área e, em muitos casos, conhecimento técnico para utilizar ferramentas de consulta e análise.

Por exemplo, um especialista da área comercial pode precisar analisar:

quantidade de vendas por período;
desempenho de determinados produtos;
comportamento de vendas por região;
valores médios das operações;
evolução das vendas ao longo dos meses.

Esse usuário conhece o problema que deseja analisar, mas pode não conhecer completamente a estrutura interna do banco de dados.

É nesse ponto que a utilização de IA apresenta uma mudança significativa. O especialista pode descrever em linguagem natural aquilo que deseja obter e utilizar uma ferramenta para produzir uma consulta SQL correspondente.

Por exemplo, o usuário poderia solicitar uma consulta que identificasse o total de vendas por mês. A ferramenta poderia produzir uma instrução SQL que relacionasse tabelas, utilizasse funções de agregação e aplicasse filtros.

O problema é que a ferramenta não deve ser considerada uma autoridade sobre os dados. Ela pode interpretar incorretamente a solicitação, utilizar uma coluna inadequada, estabelecer um relacionamento incorreto entre tabelas ou acessar informações que não deveriam estar disponíveis ao usuário.

Além disso, o usuário especialista pode não possuir conhecimento suficiente para identificar todos os efeitos de uma consulta complexa. Uma instrução pode retornar resultados aparentemente corretos e, mesmo assim, apresentar erro conceitual.

Por essa razão, o fato de o usuário ser especialista em sua área de atuação não significa que ele deva receber privilégios administrativos no banco. O acesso deve continuar condicionado às necessidades da função desempenhada.

No PostgreSQL, essa separação pode ser implementada por meio das roles e dos privilégios associados a cada uma delas. O usuário pode receber uma role que permita determinadas operações e negue outras, mantendo o controle de acesso independente da ferramenta utilizada para gerar a consulta.

**Usuários navegantes**

Os usuários navegantes são aqueles que utilizam o sistema principalmente por meio de interfaces previamente construídas, sem necessidade de formular diretamente consultas SQL.

Um exemplo é um funcionário que utiliza um sistema de vendas para pesquisar o cadastro de um cliente. Ele seleciona opções na interface e fornece informações em campos determinados pela aplicação. O usuário não precisa conhecer a estrutura das tabelas nem a linguagem SQL.

Esse perfil normalmente possui menor autonomia sobre o banco de dados. A aplicação determina quais operações estão disponíveis e quais informações podem ser visualizadas.

Essa característica pode proporcionar uma camada adicional de controle, pois o usuário não precisa possuir acesso direto às tabelas. O sistema pode apresentar somente as informações necessárias para a execução da tarefa.

Em comparação, o usuário especialista possui maior liberdade para formular consultas e explorar os dados. Essa maior liberdade aumenta a necessidade de mecanismos de autorização, controle e acompanhamento das operações.

A classificação demonstra que os usuários possuem necessidades distintas e, consequentemente, não devem receber necessariamente o mesmo nível de acesso.

No cenário estudado, o usuário especialista merece atenção especial porque combina conhecimento sobre uma área de negócio com maior autonomia para consultar os dados. A introdução de ferramentas de IA amplia essa autonomia, pois reduz a dificuldade de elaboração de consultas complexas.

Isso não significa que o usuário especialista seja, por definição, um risco para a organização. O problema está na combinação entre acesso direto aos dados, ferramentas capazes de produzir consultas automaticamente e ausência de controles adequados.

Assim, a administração do banco deve separar duas questões: quem precisa consultar determinada informação e qual ferramenta essa pessoa utiliza para formular a consulta. A segunda questão não deve determinar a ampliação dos privilégios da primeira.

A utilização de IA pode facilitar a construção de consultas, mas os limites de acesso devem permanecer definidos pelo SGBD e pelas políticas da organização. No PostgreSQL, as permissões associadas às roles constituem um mecanismo fundamental para essa separação.

Essa abordagem também está relacionada aos princípios da **Lei Geral de Proteção de Dados Pessoais (LGPD)**. A legislação estabelece, entre outros, os princípios da finalidade, adequação, necessidade, segurança, prevenção e responsabilização. O princípio da necessidade determina que o tratamento deve se limitar ao mínimo necessário para a realização de suas finalidades, enquanto o princípio da segurança exige medidas destinadas a proteger os dados pessoais contra acessos não autorizados e outras situações que possam comprometer sua proteção.

Consequentemente, a distribuição de dados entre os diferentes perfis de usuários não deve ser baseada apenas na capacidade técnica de consultar o banco. Ela deve considerar a finalidade da atividade, a necessidade efetiva de acesso e o nível de autorização compatível com a função exercida.

### 1.3 Riscos do uso de IA por usuários especialistas
Consulta incorreta, exposição de dados sensíveis, degradação de performance,
vazamento por prompts — impactos na segurança e na integridade.

### 1.4 Distribuição segura de dados
Menor privilégio, views, roles customizadas, controle de execução, auditoria,
conformidade (LGPD).

### 1.5 Atuação do DBA no cenário de IA
Monitoramento, políticas de acesso, auditoria, orientação aos usuários,
performance e backups.

### 1.6 Análise crítica: qual a melhor abordagem?
Posição fundamentada do grupo sobre como distribuir dados com segurança
no contexto do uso de IA.

## 2. Exemplos e Casos

Exemplo de view `clientes_visiveis` no PostgreSQL e exemplo de role/permissão.
Um caso real: sistema de vendas, clínica ou biblioteca.

## 3. Referências

POSTGRESQL GLOBAL DEVELOPMENT GROUP. PostgreSQL 18 Documentation: Database Roles. PostgreSQL, 2026. Documentação oficial do PostgreSQL — Database Roles

POSTGRESQL GLOBAL DEVELOPMENT GROUP. PostgreSQL 18 Documentation: Privileges. PostgreSQL, 2026. Documentação oficial do PostgreSQL — Privileges

BRASIL. Lei nº 13.709, de 14 de agosto de 2018 — Lei Geral de Proteção de Dados Pessoais (LGPD). Brasília, DF: Presidência da República. Texto oficial da LGPD — Planalto

## 4. Conclusões

Aprendizados, reflexões e principais pontos observados pelo grupo.

## Link do Repositório Git