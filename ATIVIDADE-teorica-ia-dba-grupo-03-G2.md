# Atividade Teórica: Usuários Especialistas, IA e Distribuição Segura de Dados

**Aluno(s):** Josué Mateus das Chagas Oliveira, Théo Tavernard da Rocha machado, Alessandro Moura de Oliveira Machado, Ana Karoline Lopes Leite
**Turma:** Introdução a Banco de Dados 2026/2
**Data:** 14/08/2026
**Repositório Git:** https://github.com/josueoliveiraa2006/atividade_bd_afya2026.git

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

Os usuários especialistas possuem conhecimento sobre as regras de negócio e utilizam o banco de dados para realizar consultas, análises e geração de relatórios. Com o uso de ferramentas de Inteligência Artificial generativa, esses usuários passaram a produzir consultas SQL de forma mais rápida, porém nem sempre conseguem avaliar se o código gerado está correto ou se respeita as políticas de segurança da organização. Dessa forma, o uso da IA deve ser acompanhado por mecanismos de controle e validação.

Um dos principais riscos é a geração de consultas incorretas. A IA pode produzir comandos SQL com erros de lógica, como junções inadequadas, filtros incompletos ou agregações incorretas. Embora a consulta seja executada pelo SGBD, o resultado pode apresentar informações inconsistentes, levando a interpretações equivocadas e decisões baseadas em dados incorretos.

Outro risco relevante é a exposição de dados sensíveis. Ferramentas de IA podem gerar consultas que retornem informações pessoais, como CPF, endereço, telefone, dados financeiros ou históricos de clientes, sem que exista necessidade para a atividade desempenhada pelo usuário. Caso o controle de acesso seja inadequado, informações protegidas pela Lei Geral de Proteção de Dados (LGPD) poderão ser visualizadas por pessoas sem autorização.

Também deve ser considerada a degradação de desempenho do banco de dados. Consultas geradas automaticamente podem utilizar operações de alto custo computacional, como junções entre tabelas muito grandes, ausência de filtros ou ordenações desnecessárias. Essas consultas consomem recursos do servidor, aumentam o tempo de resposta das aplicações e podem comprometer a disponibilidade do sistema para os demais usuários.

Um quarto risco consiste no vazamento de informações por meio dos prompts enviados às ferramentas de IA. Quando usuários copiam tabelas, registros ou trechos de consultas contendo informações corporativas para serviços externos de IA, esses dados deixam o ambiente controlado pela organização. Dependendo da política da ferramenta utilizada, essas informações podem ser armazenadas ou processadas em infraestrutura de terceiros, criando riscos de confidencialidade e de descumprimento da LGPD.

Além desses aspectos, existe o risco de uso inadequado de privilégios. Caso um usuário possua permissões excessivas, a IA poderá gerar comandos que acessem tabelas ou informações além daquelas necessárias para sua função. Embora a IA não conceda permissões por si só, ela pode facilitar o aproveitamento de privilégios já existentes, aumentando a superfície de exposição dos dados.

Em conjunto, esses riscos demonstram que a utilização de IA em ambientes de banco de dados exige mecanismos de segurança capazes de limitar o acesso às informações, monitorar as consultas executadas e garantir que a tecnologia seja utilizada de forma compatível com as políticas da organização.

### 1.4 Distribuição segura de dados

A distribuição segura de dados consiste em disponibilizar informações aos usuários de acordo com suas necessidades de trabalho, preservando a confidencialidade, a integridade e a disponibilidade dos dados. No contexto de usuários especialistas que utilizam ferramentas de IA para elaborar consultas SQL, essa distribuição deve ser baseada em políticas de segurança implementadas pelo SGBD e administradas pelo DBA.

O primeiro princípio é o princípio do menor privilégio (Least Privilege). Esse conceito estabelece que cada usuário deve possuir apenas as permissões estritamente necessárias para executar suas atividades. Assim, um analista responsável por consultas de vendas, por exemplo, não deve ter acesso às tabelas de recursos humanos ou à alteração de registros no banco de dados. A limitação dos privilégios reduz os impactos de erros humanos, consultas inadequadas e possíveis usos indevidos das permissões existentes.

Outra estratégia importante é a utilização de views. Uma view é uma tabela virtual construída a partir de uma consulta SQL, permitindo disponibilizar apenas as colunas e linhas necessárias para determinado perfil de usuário. Dessa forma, informações sensíveis, como CPF ou endereço completo, podem ser omitidas da visualização, enquanto os dados necessários para análises permanecem disponíveis. Essa abordagem também reduz a complexidade das consultas elaboradas pelos usuários especialistas.

A criação de roles customizadas constitui outra medida essencial. No PostgreSQL, uma role representa um conjunto de permissões que pode ser atribuído a diferentes usuários. Em vez de conceder permissões individualmente, o DBA define perfis específicos, como analista financeiro, analista comercial ou auditor, atribuindo a cada um apenas os acessos compatíveis com suas responsabilidades. Essa organização simplifica a administração das permissões e diminui o risco de concessões inadequadas.

O controle do tempo de execução e da concorrência das consultas também contribui para a segurança operacional. O PostgreSQL permite estabelecer limites para consultas excessivamente longas, evitando que comandos gerados pela IA consumam recursos por tempo indefinido. Da mesma forma, o monitoramento da concorrência impede que consultas pesadas comprometam o desempenho do banco de dados e afetem outros usuários.

Outra prática indispensável é a auditoria e o rastreamento das operações. O registro de logs possibilita identificar quem acessou determinados dados, quais consultas foram executadas e em que momento ocorreram. Essas informações auxiliam a investigação de incidentes, permitem detectar padrões de uso inadequado e apoiam o cumprimento de requisitos legais e regulatórios.

Por fim, todas essas estratégias devem estar alinhadas às exigências da Lei Geral de Proteção de Dados (LGPD). A legislação determina que dados pessoais sejam tratados de forma segura, apenas para finalidades legítimas e com acesso restrito às pessoas autorizadas. Nesse contexto, mecanismos como controle de permissões, uso de views, auditoria e monitoramento não apenas fortalecem a segurança do banco de dados, mas também contribuem para a conformidade legal da organização.

Assim, a distribuição segura de dados depende da combinação de controles técnicos e administrativos, garantindo que os usuários especialistas possam utilizar ferramentas de IA para apoiar suas atividades sem comprometer a segurança, a integridade e a privacidade das informações armazenadas no banco de dados.

### 1.5 Atuação do DBA no cenário de IA

Em um ambiente no qual usuários especialistas utilizam ferramentas de Inteligência Artificial para gerar consultas SQL e produzir relatórios, a atuação do **Administrador de Banco de Dados (DBA)** torna-se essencial para garantir segurança, integridade, disponibilidade e desempenho. O uso da IA pode facilitar a análise dos dados, mas não elimina a necessidade de controle sobre quem pode acessá-los e quais operações podem ser realizadas.

Uma das principais responsabilidades do DBA nesse cenário é a definição e manutenção das **políticas de acesso**. Cada usuário deve possuir somente os privilégios necessários para desempenhar sua função, seguindo o princípio do menor privilégio. No PostgreSQL, esse controle pode ser realizado por meio de *roles*, privilégios e *views*. Dessa forma, um usuário responsável por relatórios de vendas pode receber permissão apenas para consultar as informações necessárias, sem possuir autorização para alterar ou excluir registros. Dados pessoais, como CPF, endereço ou telefone, também podem ser ocultados quando não forem necessários para a atividade realizada.

O DBA também deve realizar o **monitoramento das consultas** executadas no banco. Uma consulta gerada por IA pode estar sintaticamente correta e, mesmo assim, apresentar erros de lógica ou utilizar recursos de maneira excessiva. Consultas com muitas junções, processamento de grandes quantidades de registros ou ausência de filtros adequados podem aumentar o consumo de memória, processamento e operações de entrada e saída, prejudicando outros usuários e aplicações. Cabe ao DBA acompanhar esse comportamento, identificar consultas lentas ou abusivas e estabelecer limites de utilização quando necessário.

Outro aspecto importante é a **auditoria**, que consiste no registro e acompanhamento das operações realizadas no banco. Os registros de acesso permitem identificar qual usuário realizou determinada operação, quando ela ocorreu e quais recursos foram utilizados. Mesmo quando uma consulta é elaborada com auxílio de IA, a responsabilidade pelo seu uso continua vinculada ao usuário autenticado no sistema. A auditoria, portanto, contribui tanto para a investigação de incidentes quanto para a responsabilização e melhoria das políticas de segurança.

A atuação do DBA também envolve a **orientação dos usuários especialistas**. Informações reais de clientes não devem ser inseridas indiscriminadamente em ferramentas externas de IA. Dados como CPF, endereço, informações financeiras e históricos de compras podem estar sujeitos às regras de proteção de dados pessoais. Nesse sentido, o DBA deve atuar em conjunto com as políticas de segurança e privacidade da organização, contribuindo para que o acesso e o tratamento das informações estejam de acordo com princípios estabelecidos pela **Lei Geral de Proteção de Dados Pessoais (LGPD)**, especialmente necessidade, segurança e prevenção.

Além dessas responsabilidades, permanecem fundamentais atividades tradicionais de administração, como manutenção de **backups, índices e mecanismos de recuperação**. Os backups permitem recuperar informações diante de falhas, exclusões indevidas ou outros incidentes, enquanto os índices, quando adequadamente planejados, podem melhorar o desempenho das consultas. Assim, a introdução de ferramentas de IA não substitui o trabalho do DBA, mas amplia a necessidade de acompanhamento técnico e de políticas capazes de preservar a segurança e o funcionamento adequado do banco de dados.

### 1.6 Análise crítica: qual a melhor abordagem?

A posição do grupo é que o uso de Inteligência Artificial por usuários especialistas pode contribuir para aumentar a produtividade e facilitar a elaboração de consultas complexas, porém **não deve resultar em acesso irrestrito aos dados**. A segurança não pode depender apenas da capacidade do usuário de formular corretamente um prompt nem da possibilidade de a ferramenta de IA produzir uma consulta adequada. Os mecanismos de proteção devem existir independentemente da consulta gerada.

A abordagem considerada mais adequada é a adoção de **camadas complementares de segurança**. Inicialmente, cada usuário deve possuir uma identificação própria e receber somente os privilégios necessários para sua atividade. As *roles* podem organizar essas permissões de acordo com os diferentes perfis existentes na organização, enquanto as *views* podem restringir as colunas e informações disponibilizadas. Quando necessário, mecanismos adicionais, como políticas de segurança em nível de linha (*Row-Level Security*), podem limitar quais registros determinados usuários podem consultar.

Por exemplo, um analista responsável por verificar o total de vendas por região pode necessitar de informações como estado, data e valor da venda, mas não precisa necessariamente conhecer o CPF ou o endereço completo de cada cliente. Nesse caso, o DBA pode disponibilizar uma *view* contendo somente os dados necessários para a análise. Mesmo que uma ferramenta de IA produza uma consulta tentando acessar informações adicionais, as permissões definidas no banco devem impedir o acesso não autorizado.

Essa estratégia deve ser complementada por **auditoria, monitoramento e controle de recursos**. Consultas excessivamente pesadas precisam ser identificadas para evitar degradação do desempenho, e operações relevantes devem permanecer associadas ao usuário responsável. Também devem existir regras organizacionais para impedir que dados pessoais ou informações confidenciais sejam enviados sem autorização para serviços externos de IA.

Essa abordagem está relacionada ao princípio da **necessidade** previsto na LGPD, segundo o qual o tratamento de dados pessoais deve ser limitado ao mínimo necessário para atingir determinada finalidade. Portanto, se uma análise pode ser realizada sem disponibilizar CPF, endereço ou outras informações pessoais, esses dados não devem ser expostos ao usuário ou à ferramenta utilizada.

Dessa forma, o grupo considera que a IA deve funcionar como **ferramenta de apoio à análise e à elaboração de consultas, e não como mecanismo de autorização de acesso aos dados**. A autorização deve continuar sendo definida pelo SGBD e pelas políticas estabelecidas pela organização. A combinação de menor privilégio, *roles*, *views*, auditoria, monitoramento, proteção de dados pessoais e acompanhamento do DBA permite aproveitar os benefícios da IA sem comprometer a confidencialidade, a integridade, a disponibilidade e a governança das informações.

## 2. Exemplos e Casos

**Exemplo de `VIEW` para limitar o acesso aos dados**

Uma forma prática de aplicar o princípio do menor privilégio no PostgreSQL é utilizar views para disponibilizar somente as informações necessárias ao usuário especialista. Uma view é uma consulta armazenada que apresenta ao usuário um conjunto específico de linhas e colunas, sem que seja necessário conceder acesso direto à tabela original. No contexto apresentado, isso permite que o usuário realize análises por meio de IA sem receber automaticamente todos os dados existentes no banco.

Supondo uma tabela `clientes` com as colunas `id`, `nome`, `cpf`, `endereco`, `email` e `data_cadastro`, uma view destinada à análise comercial poderia ser criada da seguinte maneira:

```sql
CREATE VIEW clientes_visiveis AS
SELECT
    id,
    nome,
    email,
    data_cadastro
FROM clientes;
```

Nesse exemplo, informações como `cpf` e `endereco` não são disponibilizadas pela view. O usuário especialista pode realizar consultas sobre os clientes que estão autorizados para sua atividade, mas não possui acesso direto aos campos que não são necessários para a análise.

Também é possível limitar as linhas apresentadas. Por exemplo, uma organização pode permitir que determinado setor consulte apenas clientes de uma região específica:

```sql
CREATE VIEW clientes_norte AS
SELECT
    id,
    nome,
    email,
    data_cadastro
FROM clientes
WHERE regiao = 'Norte';
```

Essa estratégia é importante porque restringir o acesso somente às colunas não é suficiente quando diferentes usuários também devem visualizar conjuntos diferentes de registros. Em situações que exigem controle por usuário ou função, o PostgreSQL também oferece Row-Level Security (RLS), mecanismo que permite estabelecer políticas para determinar quais linhas cada usuário pode consultar ou modificar.

**Exemplo de `ROLE` e permissões**

No PostgreSQL, usuários e grupos de permissões são representados por roles. Uma role pode receber privilégios específicos sobre tabelas, views e outros objetos do banco. Dessa forma, o DBA pode organizar os acessos de acordo com a função exercida por cada usuário, evitando que todos recebam os mesmos privilégios.

Um exemplo seria criar uma role destinada aos usuários especialistas responsáveis por consultas analíticas:

```sql
CREATE ROLE usuario_analista NOLOGIN;

GRANT USAGE ON SCHEMA public TO usuario_analista;

GRANT SELECT ON clientes_visiveis TO usuario_analista;
```

Posteriormente, um usuário específico pode ser associado a essa role:

```sql
GRANT usuario_analista TO especialista01;
```

Nesse modelo, `especialista01` poderá consultar a view autorizada, mas não necessariamente terá permissão para consultar diretamente a tabela `clientes`. Isso representa uma aplicação prática do princípio do menor privilégio, pois o usuário recebe somente os acessos necessários para desempenhar sua atividade.

Também é importante evitar privilégios administrativos desnecessários. No PostgreSQL, uma role com atributo `SUPERUSER` pode ignorar diversas restrições de acesso, razão pela qual esse nível de privilégio deve ser reservado a situações que realmente o exijam.

**Caso aplicado: sistema de vendas**

Considere uma empresa que utiliza PostgreSQL para armazenar clientes, produtos, vendas e pagamentos. Os usuários especialistas do setor comercial utilizam ferramentas de IA para elaborar consultas e gerar relatórios, como:

"Qual foi o faturamento mensal por região durante o último ano?"

O uso da IA pode aumentar a produtividade, mas não significa que o usuário deva possuir acesso irrestrito ao banco. O DBA pode criar uma estrutura em que a ferramenta seja utilizada somente sobre objetos previamente autorizados.

Uma possibilidade seria disponibilizar uma view de análise contendo apenas informações como região, mês, quantidade de vendas e valor total:

```sql
CREATE VIEW relatorio_vendas AS
SELECT
    regiao,
    DATE_TRUNC('month', data_venda) AS mes,
    COUNT(*) AS quantidade_vendas,
    SUM(valor) AS faturamento
FROM vendas
GROUP BY regiao, DATE_TRUNC('month', data_venda);
```

A role utilizada pelos especialistas receberia apenas permissão de leitura nessa view. Assim, uma consulta gerada pela IA poderia analisar os resultados disponíveis sem possuir acesso direto a informações pessoais ou financeiras que não façam parte da finalidade da análise.

Por exemplo:

```sql
SELECT
    regiao,
    mes,
    faturamento
FROM relatorio_vendas
ORDER BY mes, regiao;
```

Nesse cenário, a IA permanece como ferramenta de apoio à construção da consulta, enquanto o banco continua responsável por aplicar as restrições de acesso. A consulta produzida automaticamente não deve ser considerada confiável apenas porque possui sintaxe válida: ela precisa respeitar as permissões existentes e, quando necessário, ser analisada pelo DBA ou por mecanismos de controle definidos pela organização.

**Exemplo de controle sobre dados pessoais**

Suponha que a tabela de clientes contenha `cpf`, telefone, endereço e e-mail. Para uma análise de vendas, essas informações podem ser desnecessárias. A organização pode fornecer ao usuário somente dados agregados ou campos necessários para a finalidade da consulta.

Essa medida está relacionada aos princípios de segurança e prevenção previstos na LGPD. A legislação determina a adoção de medidas técnicas e administrativas para proteger dados pessoais contra acessos não autorizados, perda, alteração, comunicação ou tratamento inadequado ou ilícito.

Assim, em vez de disponibilizar:

CPF | Nome | Endereço | Telefone | Valor da compra

uma estrutura destinada à análise comercial poderia fornecer:

Região | Mês | Quantidade de vendas | Faturamento

A diferença demonstra que distribuir dados não significa distribuir todo o conteúdo armazenado no banco. A distribuição deve considerar a finalidade do acesso, o perfil do usuário e a necessidade de cada informação.

**Caso de uso indevido de consulta gerada por IA**

Considere ainda que um especialista solicite à IA:

"Mostre todos os clientes e seus CPFs que compraram acima de R$ 10.000."

Mesmo que a consulta SQL produzida esteja sintaticamente correta, ela pode ser inadequada do ponto de vista de segurança e privacidade. O problema não está somente na capacidade da IA de gerar SQL, mas também na autorização concedida ao usuário e na finalidade do tratamento dos dados.

Em uma arquitetura segura, a existência de uma view sem `cpf`, combinada com uma role que não possui `SELECT` sobre a tabela original, impede que a consulta simplesmente obtenha essa informação. O controle, portanto, não depende exclusivamente da capacidade do usuário de compreender ou revisar o código SQL; ele é aplicado diretamente pelo SGBD.

Além disso, quando ferramentas externas de IA são utilizadas, dados pessoais ou informações internas não devem ser enviados indiscriminadamente aos serviços. A organização deve estabelecer regras para determinar quais informações podem ser inseridas nos prompts e quais devem permanecer exclusivamente no ambiente corporativo. Essa preocupação decorre do dever de proteção e segurança dos dados pessoais previsto na LGPD.

Dessa forma, os exemplos demonstram que a utilização de IA por usuários especialistas deve ocorrer sobre uma estrutura de acesso controlada, na qual views, roles, permissões e políticas de segurança definem previamente quais dados podem ser consultados. A IA pode auxiliar na elaboração e interpretação das consultas, mas não deve substituir os mecanismos de autorização e governança implementados pelo banco de dados.

## 3. Referências

POSTGRESQL GLOBAL DEVELOPMENT GROUP. PostgreSQL 18 Documentation: Database Roles. PostgreSQL, 2026. Documentação oficial do PostgreSQL — Database Roles

POSTGRESQL GLOBAL DEVELOPMENT GROUP. PostgreSQL 18 Documentation: Privileges. PostgreSQL, 2026. Documentação oficial do PostgreSQL — Privileges

BRASIL. Lei nº 13.709, de 14 de agosto de 2018 — Lei Geral de Proteção de Dados Pessoais (LGPD). Brasília, DF: Presidência da República. Texto oficial da LGPD — Planalto

GARCIA, Lara R. Lei Geral de Proteção de Dados (LGPD): Guia de implantação. São Paulo: Editora Blucher, 2020. E-book. p.1. ISBN 9786555060164. Disponível em: https://integrada.minhabiblioteca.com.br/reader/books/9786555060164/. Acesso em: 15 ago. 2026.

SILBERSCHATZ, Abraham. Sistema de Banco de Dados. 7. ed. Rio de Janeiro: GEN LTC, 2020. E-book. p.22. ISBN 9788595157552. Disponível em: https://integrada.minhabiblioteca.com.br/reader/books/9788595157552/. Acesso em: 14 ago. 2026.

## 4. Conclusão

A análise realizada demonstra que o uso de ferramentas de Inteligência Artificial por usuários especialistas pode facilitar a elaboração de consultas e a análise de informações, mas também exige maior atenção aos mecanismos de controle do banco de dados. É necessário compreender as permissões, a estrutura dos dados e as regras que devem ser respeitadas.

Nesse contexto, o papel do DBA é fundamental. Pois além de administrar a estrutura e o desempenho do PostgreSQL, ele deve definir políticas de acesso compatíveis com cada perfil de usuário, utilizando recursos como roles, privilégios e views. Dessa maneira, um usuário pode realizar as consultas necessárias sem receber acesso a informações que não precisa ou autorização para alterar dados de forma ilegal. Os exemplos apresentados também mostram que a segurança deve ser aplicada diretamente no banco. Mesmo que uma ferramenta de IA produza uma consulta válida, ela não deve conseguir ultrapassar as permissões estabelecidas. Então, esse modelo reduz os impactos de erros de consulta, exposição de dados pessoais e operações que possam comprometer o desempenho do sistema.

Por fim, conclui-se que a utilização de IA no acesso aos dados pode sim fazer parte da rotina de uma organização, desde que esteja acompanhada por controle de acesso, monitoramento, auditoria e critérios para proteger os dados. A principal questão não é impedir o uso da tecnologia, mas estabelecer limites para que seus benefícios sejam aproveitados sem retirar do DBA e do SGBD a responsabilidade pelo controle e pela segurança das informações.

## Link do Repositório Git

https://github.com/josueoliveiraa2006/atividade_bd_afya2026.git