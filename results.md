# Análise de Ameaças STRIDE para Aplicação Web

Esta análise de modelagem de ameaças baseia-se na arquitetura de aplicação fornecida, que consiste em um frontend com React, um backend com Spring Boot e um banco de dados relacional.

### 1. Agentes de Ameaça (Threat Agents)

Os seguintes agentes de ameaça foram considerados para esta arquitetura:

* **TA-1: Atacante Externo não Autenticado:** Um indivíduo na internet sem credenciais legítimas que tenta explorar vulnerabilidades publicamente acessíveis na aplicação (por exemplo, na API REST, no servidor web ou na aplicação cliente).
* **TA-2: Usuário Legítimo Mal-intencionado:** Um usuário autenticado que tenta exceder seus privilégios para acessar dados ou funcionalidades que não deveria (por exemplo, tentar acessar os dados de outro usuário).
* **TA-3: Atacante Interno (Insider):** Um indivíduo com acesso privilegiado à infraestrutura, como um desenvolvedor, um administrador de sistemas ou um funcionário com acesso ao banco de dados, que abusa de seus privilégios.
* **TA-4: Dependências de Terceiros Comprometidas:** Software vulnerável utilizado na aplicação, como uma biblioteca NPM no frontend (React), uma dependência Maven/Gradle no backend (Spring Boot) ou o próprio software do servidor web (Nginx, Apache).

### 2. Análise de Ameaças (Threats)

As ameaças a seguir foram identificadas usando o modelo STRIDE.

#### S - Spoofing (Falsificação de Identidade)

* **T-1 (Falsificação de Usuário):** Um atacante (TA-1) tenta se passar por um usuário legítimo para ganhar acesso não autorizado.
    * **Componentes Afetados:** Camada de Autenticação, Controller Layer.
* **T-2 (Falsificação de Servidor):** Um atacante (TA-1) intercepta a comunicação entre o cliente e o servidor (Man-in-the-Middle) e se passa pelo servidor real.
    * **Componentes Afetados:** Todo o fluxo de comunicação entre o Client Side e o Server Side.

#### T - Tampering (Adulteração de Dados)

* **T-3 (Adulteração de Dados em Trânsito):** Um atacante (TA-1) modifica os dados trocados entre a aplicação React e a API REST.
    * **Componentes Afetados:** API Layer (Client), Controller Layer (Server).
* **T-4 (Adulteração de Lógica no Cliente - XSS):** Um atacante (TA-1) injeta scripts maliciosos na aplicação React (Cross-Site Scripting).
    * **Componentes Afetados:** React Components.
* **T-5 (Adulteração de Dados no Banco):** Um atacante com acesso ao servidor (TA-1, TA-3) ou explorando uma vulnerabilidade de SQL Injection modifica diretamente os dados no banco de dados.
    * **Componentes Afetados:** Database, Data Access Layer.
* **T-6 (Adulteração de Token de Autenticação):** Um atacante (TA-1, TA-2) tenta modificar o conteúdo de um token de autenticação (ex: JWT).
    * **Componentes Afetados:** Authentication/Security Layer.

#### R - Repudiation (Repúdio)

* **T-7 (Repúdio de Ações):** Um usuário legítimo (TA-2) realiza uma ação crítica e posteriormente nega tê-la feito devido à falta de logs auditáveis.
    * **Componentes Afetados:** Service Layer, Controller Layer.

#### I - Information Disclosure (Divulgação de Informação)

* **T-8 (Exposição de Dados em Trânsito):** Um atacante (TA-1) intercepta a comunicação e lê dados sensíveis.
    * **Componentes Afetados:** Todo o fluxo de comunicação entre Client Side e Server Side.
* **T-9 (Exposição de Dados em Repouso):** Um atacante (TA-1, TA-3) obtém acesso direto ao servidor de banco de dados e lê informações sensíveis.
    * **Componentes Afetados:** Database.
* **T-10 (Mensagens de Erro Detalhadas):** A aplicação retorna mensagens de erro detalhadas (stack traces, erros de SQL) para o cliente.
    * **Componentes Afetados:** Controller Layer, Service Layer.
* **T-11 (Quebra de Controle de Acesso Horizontal):** Um usuário autenticado (TA-2) consegue acessar recursos de outro usuário alterando um ID na requisição.
    * **Componentes Afetados:** Controller Layer, Service Layer.
* **T-12 (Exposição de Segredos no Código Cliente):** Chaves de API ou segredos são embutidos no código da aplicação React.
    * **Componentes Afetados:** React Application.

#### D - Denial of Service (Negação de Serviço)

* **T-13 (Negação de Serviço na Camada de Aplicação):** Um atacante (TA-1) envia um volume massivo de requisições para a API REST, esgotando os recursos do servidor.
    * **Componentes Afetados:** Web Server, Java/Backend Application.
* **T-14 (Consultas Lentas e Otimizadas):** Um atacante (TA-1, TA-2) envia requisições que disparam consultas ao banco de dados que consomem muitos recursos.
    * **Componentes Afetados:** Service Layer, Data Access Layer, Database.

#### E - Elevation of Privilege (Elevação de Privilégio)

* **T-15 (Elevação de Privilégio Vertical):** Um usuário comum (TA-2) explora uma falha na lógica de autorização para obter privilégios de administrador.
    * **Componentes Afetados:** Controller Layer, Service Layer.
* **T-16 (Elevação via Vulnerabilidades de Componentes):** Um atacante (TA-1) explora uma vulnerabilidade conhecida em uma dependência desatualizada (TA-4) para executar código remotamente.
    * **Componentes Afetados:** Web Server, Java/Backend Application.
* **T-17 (Injeção de SQL):** Um atacante (TA-1) insere comandos SQL maliciosos em campos de entrada da API que não são devidamente sanitizados.
    * **Componentes Afetados:** Data Access Layer, Controller Layer.

### 3. Contramedidas (Countermeasures)

* **C-1 (Autenticação Forte):** Implementar MFA e políticas de senha forte. (*Mitiga: T-1*)
* **C-2 (Uso de TLS/HTTPS):** Forçar o uso de TLS em toda a comunicação. (*Mitiga: T-2, T-3, T-8*)
* **C-3 (Validação e Assinatura de Tokens):** Usar algoritmos de assinatura fortes para tokens e validá-los a cada requisição. (*Mitiga: T-6*)
* **C-4 (Validação e Sanitização de Entradas):** Validar e sanitizar todos os dados recebidos no backend. (*Mitiga: T-4, T-17*)
* **C-5 (Gerenciamento de Erros Seguro):** Retornar mensagens de erro genéricas e registrar detalhes no servidor. (*Mitiga: T-10*)
* **C-6 (Controle de Acesso Centralizado e Robusto):** Implementar verificações de autorização no backend para cada requisição. (*Mitiga: T-11, T-15*)
* **C-7 (Princípio do Menor Privilégio):** Executar processos com o mínimo de permissões necessárias. (*Mitiga: T-5, T-16*)
* **C-8 (Logging e Auditoria):** Manter logs detalhados e seguros de ações críticas. (*Mitiga: T-7*)
* **C-9 (Criptografia em Repouso):** Criptografar dados sensíveis no banco de dados. (*Mitiga: T-9*)
* **C-10 (Não Expor Segredos no Frontend):** Manter todos os segredos no lado do servidor. (*Mitiga: T-12*)
* **C-11 (Rate Limiting e Throttling):** Implementar limites de taxa de requisições na API. (*Mitiga: T-1, T-13*)
* **C-12 (Gerenciamento de Vulnerabilidades):** Manter todas as dependências atualizadas e usar ferramentas de SCA. (*Mitiga: T-16*)
* **C-13 (Uso de Prepared Statements):** Utilizar ORMs ou Prepared Statements para prevenir SQL Injection. (*Mitiga: T-17*)
* **C-14 (Cabeçalhos de Segurança HTTP):** Implementar cabeçalhos como `Content-Security-Policy`. (*Mitiga: T-4*)
* **C-15 (Otimização e Timeouts de Consultas):** Otimizar e definir timeouts para queries ao banco de dados. (*Mitiga: T-14*)
* **C-16 (Web Application Firewall - WAF):** Utilizar um WAF para filtrar tráfego malicioso. (*Mitiga: T-4, T-13, T-17*)
