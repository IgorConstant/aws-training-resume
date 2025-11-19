### Treinamento AWS - Dia 3 - 19/11

## Módulo 11: Building Modern Applications

### 🎯 Aplicações Modernas - Características Principais

Dois pilares fundamentais para construir aplicações modernas:

1. **Arquitetura:** Microserviços
2. **Entrega de Software:** CI/CD (DevOps)

---

### 📦 Aplicações Monolíticas - Características e Limitações

#### O que é uma Aplicação Monolítica?
Uma estrutura única onde todos os componentes estão integrados em um único sistema.

#### Principais Características:

| Característica | Impacto |
|---|---|
| **Tudo em um lugar** | Faz tudo (negócio, processamento, dados) |
| **Estado compartilhado** | Mantém estado em cada instância de execução |
| **Tecnologia única** | Uma única stack de tecnologia para tudo |
| **Dados limitados** | Opções restritas de armazenamento e dados |
| **Organização** | Estruturada ao redor da tecnologia, não do negócio |
| **Complexidade** | Dificuldade na implantação |
| **Releases** | Cronogramas de lançamento rígidos e inflexíveis |

---

### 🧩 Microserviços - Arquitetura Moderna

#### O que são Microserviços?
Pequenos serviços independentes que trabalham juntos para formar uma aplicação completa.

#### Características Principais:

| Característica | Benefício |
|---|---|
| **Serviços Mínimos** | Cada serviço faz uma coisa bem |
| **Deploy Independente** | Podem ser atualizados separadamente |
| **Integração Entre Si** | Trabalham juntos de forma orquestrada |
| **Dados por Serviço** | Cada um escolhe sua melhor opção de dados |
| **Organizado por Negócio** | Estruturado em torno de capacidades de negócio |
| **Estado Externalizado** | Dados salvos fora do serviço |
| **Flexibilidade Tecnológica** | Cada serviço pode usar tecnologias diferentes |
| **Modelo Automatizado** | Suporta serverless e operações automáticas |

#### ⚙️ Diagrama do Funcionamento:

```
┌─────────────────────────────────────────────────────────┐
│                    API Gateway                          │
│              (Ponto de entrada único)                   │
└──────────┬──────────────┬──────────────┬────────────────┘
           │              │              │
    ┌──────▼──────┐  ┌───▼──────┐  ┌───▼──────────┐
    │  Serviço 1  │  │Serviço 2 │  │  Serviço 3   │
    │ (Autenticação)│ │(Pedidos) │  │(Pagamento)  │
    └──────┬──────┘  └───┬──────┘  └───┬──────────┘
           │              │              │
      ┌────▼────┐    ┌────▼────┐   ┌───▼────┐
      │ Database│    │Database │   │Database│
      │    1    │    │    2    │   │   3    │
      └─────────┘    └─────────┘   └────────┘

    🔄 Comunicação via:
    • REST APIs
    • Message Queues
    • Event Bus
    • gRPC
```

#### 🎯 Vantagens dos Microserviços:

✅ **Escalabilidade:** Dimensionar apenas o que precisa  
✅ **Resiliência:** Falha de um não derruba tudo  
✅ **Facilidade de Deploy:** Atualizações independentes  
✅ **Equipes Autônomas:** Times podem trabalhar em paralelo  
✅ **Inovação Rápida:** Melhorias sem impactar toda aplicação  

#### ⚠️ Desafios dos Microserviços:

❌ **Complexidade:** Mais componentes para gerenciar  
❌ **Network:** Latência na comunicação entre serviços  
❌ **Consistência de Dados:** Desafio de manter sincronização  
❌ **Monitoramento:** Mais difícil rastrear problemas  
❌ **Operações:** Requer DevOps robusto

---

### 🔄 Padrões de Interação entre Serviços

#### API-Driven (Processamento Síncrono)
Serviços e aplicações se comunicam através de APIs:
- **Como funciona:** Aplicações fazem requisições HTTP/REST
- **Sincronismo:** Aguardam resposta imediata
- **Uso ideal:** Operações que precisam de confirmação imediata
- **Exemplo:** Sistema de autenticação consultando banco de usuários

```
Cliente → [REST API] → Serviço → [Resposta] → Cliente
  ↓
 Aguarda resposta
```

---

### 🔨 Decomposição de Aplicações Monolíticas

#### Estratégia para Quebrar o Monolito

Passos recomendados para transformar uma aplicação monolítica em microserviços:

1. **Comece com o Pequeno** 📍
   - Identifique um pequeno serviço para extrair
   - Minimize riscos na primeira mudança

2. **Reduza Dependências** 🔗
   - Minimize acoplamento entre serviços
   - Cada serviço deve ser independente

3. **Quebre Dependências Complexas** 🧩
   - Identifique pontos de acoplamento forte
   - Crie camadas de abstração se necessário

4. **Desacople Funcionalidades em Mudança** 🔄
   - Serviços que mudam frequentemente devem ser isolados
   - Permite inovação sem impactar o resto

#### Exemplo Prático:

```
ANTES (Monolito):
┌─────────────────────────────────────┐
│  Autenticação                       │
│  Pedidos                            │
│  Pagamento                          │
│  Notificações                       │
│  Relatórios                         │
└─────────────────────────────────────┘
        ↓ (Quando muda uma, tudo derruba)

DEPOIS (Microserviços):
┌──────────────┐  ┌─────────────┐  ┌─────────────┐
│ Autenticação │  │   Pedidos   │  │  Pagamento  │
└──────────────┘  └─────────────┘  └─────────────┘
      ↓                ↓                  ↓
┌──────────────┐  ┌─────────────┐  ┌─────────────┐
│Notificações  │  │ Relatórios  │  │ Inventário  │
└──────────────┘  └─────────────┘  └─────────────┘
        ↓ (Cada um evolui independentemente)
```

---

### 🚀 Software Delivery com CI/CD (DevOps)

#### O que é CI/CD?
- **CI (Continuous Integration):** Integração contínua de código
- **CD (Continuous Delivery):** Entrega contínua de atualizações

#### 🏗️ Monolito em DevOps:

```
┌─────────────────────────────────────────────┐
│           Git Repository                    │
│     (Código da Aplicação Monolítica)       │
└────────────────────┬────────────────────────┘
                     │
         ┌───────────▼───────────┐
         │   Build (Compilação)  │
         │   Test (Testes)       │
         │   Package (Empacote)  │
         └───────────┬───────────┘
                     │
         ┌───────────▼───────────┐
         │  Deploy em Produção   │
         │  (Aplicação Inteira)  │
         └───────────┬───────────┘
                     │
            ⚠️ PROBLEMA:
            • Toda a aplicação é testada
            • Deploy é tudo ou nada
            • Um erro derruba tudo
            • Releases são raras e grandes
```

#### 🎯 Microserviços em DevOps:

```
┌──────────────────────────────────────────────────┐
│           Git Repository                         │
│    (Código separado para cada Microserviço)     │
└─┬────────────┬────────────┬──────────────────────┘
  │            │            │
  ▼            ▼            ▼
Serviço 1   Serviço 2   Serviço 3
  │            │            │
  ▼            ▼            ▼
Build      Build      Build
Test       Test       Test
Package    Package    Package
  │            │            │
  └────────────┬────────────┘
               │
       ┌───────▼───────┐
       │ Deploy Paralelo│
       │ (Independente) │
       └───────┬───────┘
               │
      ✅ BENEFÍCIOS:
      • Cada serviço tem seu pipeline
      • Deploy independente
      • Falha isolada em um serviço
      • Releases contínuas e pequenas
      • Rollback rápido se necessário
```

#### 📊 Comparativo:

| Aspecto | Monolito | Microserviços |
|---|---|---|
| **Tempo de Build** | ⏱️ Longo (tudo junto) | ⚡ Rápido (paralelo) |
| **Teste** | 🔴 Tudo ou nada | 🟢 Isolado por serviço |
| **Deploy** | 🔄 Sincronizado | 🎯 Independente |
| **Frequência** | 📅 Semanal/Mensal | 📆 Diária/Contínua |
| **Rollback** | ⚠️ Complexo e arriscado | ✅ Rápido e seguro |
| **Monitoramento** | 📊 Simples (um ponto) | 📡 Distribuído (múltiplos) |

---

## ☁️ Serverless Computing - Computação sem Servidor

### O que é Serverless Computing?

Serverless é um modelo de computação em nuvem onde **você não precisa se preocupar com servidores**. A plataforma gerencia toda a infraestrutura automaticamente.

#### ⚙️ Deploy Tradicional vs Serverless:

**Abordagem Tradicional:**
```
┌─────────────────────────────────────┐
│ 1. Provisionar instância (VM)      │
│ 2. Configurar SO (Sistema Operacional)
│ 3. Instalar runtime (Node, Python)  │
│ 4. Deploy da aplicação              │
│ 5. Monitorar e manter sempre UP     │
│ 6. Escalar manualmente              │
└─────────────────────────────────────┘
     ❌ Trabalho manual constante!
```

**Abordagem Serverless:**
```
┌─────────────────────────────────────┐
│ 1. Fazer upload do código           │
│ 2. Configurar gatilho (trigger)     │
│ 3. Pronto para usar! ✅             │
│                                     │
│ (AWS cuida de tudo automaticamente) │
└─────────────────────────────────────┘
     ✅ Sem gerenciamento de infraestrutura!
```

---

### 🎯 Benefícios do Serverless Computing

✅ **Sem Gerenciamento de Servidor**
- Você escreve código, AWS gerencia infraestrutura
- Nada para provisionar ou manter

✅ **Pague Apenas pelo Uso**
- Sem servidores ociosos consumindo recursos
- Cobrança por execução (por ms ou invocações)

✅ **Escalabilidade Automática**
- Escala automaticamente conforme demanda
- De 0 a milhões de requisições sem intervenção

✅ **Alta Disponibilidade e Tolerância a Falhas**
- Redundância automática
- Distribuição em múltiplas AZs (Availability Zones)

✅ **Desenvolvimento Mais Rápido**
- Foco no código, não em infraestrutura
- Time reduzido de operações

---

### 🏗️ Serverless Application Stack na AWS

Um aplicativo serverless é composto por várias camadas de serviços AWS:

#### 1. 🖥️ **Compute** (Processamento)
Onde o código é executado:

| Serviço | Uso |
|---|---|
| **AWS Lambda** | Funções sem servidor (principal) |
| **AWS Fargate** | Containers gerenciados |
| **AppSync** | APIs GraphQL |

**Exemplo:** Função Lambda que processa pedidos quando um arquivo é enviado ao S3

#### 2. 🔗 **Application Integration** (Integração)
Conecta diferentes componentes da aplicação:

| Serviço | Uso |
|---|---|
| **API Gateway** | Cria APIs REST e WebSocket |
| **SNS (Simple Notification Service)** | Publica mensagens para múltiplos subscribers |
| **SQS (Simple Queue Service)** | Fila de mensagens para desacoplamento |
| **EventBridge** | Rota eventos entre serviços |

**Exemplo:** API Gateway → Lambda → Salvar em DynamoDB

#### 3. 🗄️ **Database** (Banco de Dados)
Armazenamento de dados:

| Serviço | Tipo |
|---|---|
| **DynamoDB** | NoSQL escalável |
| **Aurora** | SQL relacional serverless |
| **ElastiCache** | Cache em memória |

#### 4. 🔌 **API Proxy** (Gateway)
Gerencia acesso às APIs:

| Serviço | Função |
|---|---|
| **API Gateway** | Expõe funções Lambda via HTTP |
| **CloudFront** | Distribuição de conteúdo global |

#### 5. 🛠️ **Dev Tools** (Ferramentas de Desenvolvimento)
Facilita desenvolvimento e testes:

| Serviço | Uso |
|---|---|
| **AWS SAM (Serverless Application Model)** | Framework para buildar apps serverless |
| **CloudFormation** | Infrastructure as Code |
| **CodeBuild** | Compilação e testes |
| **CodeDeploy** | Deployment automático |

#### 6. 📋 **Orchestration** (Orquestração)
Coordena fluxos de trabalho complexos:

| Serviço | Uso |
|---|---|
| **Step Functions** | Orquestra múltiplas Lambdas |
| **EventBridge** | Roteia eventos condicionalmente |

**Exemplo Prático:**
```
Usuário faz pedido
    ↓
API Gateway invoca Lambda (Validação)
    ↓
Se válido → Lambda (Processamento Pagamento)
    ↓
Se sucesso → SNS publica evento
    ↓
Múltiplos Lambdas acionados:
  • Atualizar Inventário
  • Enviar Confirmação por Email
  • Gerar Relatório
```

#### 7. 📊 **Analytics** (Análise)
Processa e analisa dados em grande escala:

| Serviço | Uso |
|---|---|
| **Kinesis** | Processamento de stream de dados |
| **Athena** | Análise de dados em S3 |
| **Lambda** | Processar logs e métricas |

#### 8. 🗃️ **Storage** (Armazenamento)
Guarda arquivos e dados:

| Serviço | Uso |
|---|---|
| **S3 (Simple Storage Service)** | Armazenamento de objetos escalável |
| **EFS (Elastic File System)** | Sistema de arquivos |

**Trigger comum:** Arquivo enviado ao S3 → Lambda processa → Salva resultado

#### 9. 👁️ **Visibility** (Monitoramento)
Monitora e registra comportamento da aplicação:

| Serviço | Função |
|---|---|
| **CloudWatch** | Logs, métricas e alarmes |
| **X-Ray** | Rastreamento distribuído |
| **CloudTrail** | Auditoria de chamadas AWS |

**Exemplo:** Lambda escreve log → CloudWatch coleta → Alarme acionado se erro

---

### 📐 Arquitetura Típica de uma Aplicação Serverless

```
                    ┌─────────────────┐
                    │     Usuário     │
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │  API Gateway    │ ← Entrada principal
                    └────────┬────────┘
                             │
        ┌────────────────────┼────────────────────┐
        │                    │                    │
    ┌───▼───┐           ┌───▼───┐           ┌───▼───┐
    │Lambda │           │Lambda │           │Lambda │
    │(Auth) │           │(CRUD) │           │(Proc) │
    └───┬───┘           └───┬───┘           └───┬───┘
        │                   │                   │
    ┌───▼───────────────────┼───────────────────▼───┐
    │           DynamoDB / Aurora                   │
    │         (Banco de Dados Serverless)          │
    └───────────────────────┬───────────────────────┘
                            │
        ┌───────────────────┼───────────────────┐
        │                   │                   │
    ┌───▼────┐         ┌───▼────┐         ┌───▼────┐
    │   S3   │         │  SNS   │         │ Events │
    │Storage │         │ Notif. │         │  Bus   │
    └────────┘         └────────┘         └────────┘

CloudWatch monitora tudo acima ↑
X-Ray rastreia requisições através da aplicação
```

---

### 💡 Casos de Uso Ideais para Serverless

✅ **APIs REST** - Endpoints sob demanda
✅ **Processamento de Eventos** - Reage a mudanças de dados
✅ **Microsserviços** - Funções independentes e desacopladas
✅ **Processamento Assíncrono** - Tarefas em background
✅ **Web Hooks** - Responde a integrações externas
✅ **Chatbots e Alexa Skills** - Aplicações conversacionais

---

## 🔐 Amazon Cognito - Autenticação e Autorização

### O que é Amazon Cognito?

Amazon Cognito é um serviço gerenciado da AWS que fornece **autenticação, autorização e gerenciamento de usuários** para aplicações web e mobile de forma simples e segura.

#### 🎯 Principais Funcionalidades:

✅ **Gerenciamento de Usuários** - Crie e gerencie diretório de usuários
✅ **Autenticação** - Login seguro com suporte a MFA
✅ **Autorização** - Controle de acesso baseado em papéis
✅ **Armazenamento de Identidade** - Banco de dados de usuários integrado
✅ **Controle de Acesso a Recursos AWS** - Permissões granulares
✅ **Compliance** - Atende regulamentações de segurança

---

### 🔄 Como o Cognito Funciona?

```
┌─────────────────────────────────────────────────────────────┐
│                     SUA APLICAÇÃO                           │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   ┌──────────────────┐           ┌──────────────────┐     │
│   │   User Pool      │           │ Identity Pool    │     │
│   │ (Gerenciar       │           │ (Papéis AWS &    │     │
│   │  Identidades)    │           │  Credenciais)    │     │
│   └────────┬─────────┘           └────────┬─────────┘     │
│            │                              │                │
│     Autentica usuário          Fornece credenciais        │
│            │                              │                │
│   ┌────────▼──────────────────────────────▼───────┐       │
│   │         AWS IAM (Identity & Access Mgmt)      │       │
│   │         AWS STS (Security Token Service)      │       │
│   └────────────────────────────────────────────────┘       │
│                          │                                 │
│              Acesso autorizado aos recursos              │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

### 👥 User Pools - Gerenciamento de Usuários

#### O que é um User Pool?

Um **User Pool** é um diretório de usuários gerenciado pela AWS. Ele:
- Armazena dados de usuários de forma segura
- Fornece interface de login (Hosted UI)
- Gerencia senhas e autenticação multi-fator
- Emite tokens JWT (JSON Web Tokens)

#### Fluxo de Autenticação com User Pool:

```
┌──────────────┐
│   Usuário    │
│   Final      │
└───────┬──────┘
        │ 1. Insere credenciais
        ▼
┌──────────────────────────────────┐
│   Hosted UI do Cognito            │
│ (Tela de Login fornecida)        │
└────────────┬─────────────────────┘
             │ 2. Valida credenciais
             ▼
    ┌─────────────────┐
    │  User Pool      │
    │  (Banco dados)  │
    └────────┬────────┘
             │ 3. Credenciais OK?
             ▼
    ┌─────────────────┐
    │  Gera 3 Tokens: │
    │ • ID Token      │
    │ • Access Token  │
    │ • Refresh Token │
    └────────┬────────┘
             │ 4. Retorna tokens
             ▼
    ┌─────────────────┐
    │  Sua App        │
    │  (Armazena)     │
    └─────────────────┘
```

#### Recursos Principais do User Pool:

| Funcionalidade | Descrição |
|---|---|
| **Hosted UI** | Interface de login pronta para uso |
| **Standard Tokens** | ID Token, Access Token, Refresh Token |
| **User Directory** | Armazena e gerencia usuários |
| **MFA (Multi-Factor Authentication)** | Aumenta segurança com segundo fator |
| **Social Sign-in** | Login com Google, Facebook, Apple |
| **Custom Attributes** | Campos customizados por usuário |
| **User Migration** | Importa usuários existentes |

#### Exemplo Prático - Fluxo Completo:

```
1️⃣ Usuário acessa sua app
2️⃣ Clica em "Login"
3️⃣ É redirecionado para Hosted UI do Cognito
4️⃣ Insere email e senha
5️⃣ Cognito valida no User Pool
6️⃣ Sucesso → Retorna tokens (ID, Access, Refresh)
7️⃣ App recebe tokens
8️⃣ Armazena tokens localmente (localStorage/sessionStorage)
9️⃣ Usa Access Token para acessar APIs
🔟 Access Token expira? Usa Refresh Token para novo Access Token
```

---

### 🔑 Identity Pools - Credenciais AWS e Acesso a Recursos

#### O que é um Identity Pool?

Um **Identity Pool** fornece **credenciais temporárias da AWS** para:
- Acessar serviços AWS (S3, DynamoDB, etc)
- Permitir usuários autenticados E não-autenticados
- Usar identidades federadas (Google, Facebook, OIDC)

#### Diferença: User Pool vs Identity Pool

| Aspecto | User Pool | Identity Pool |
|---|---|---|
| **Propósito** | Autenticação de usuários | Autorização de recursos AWS |
| **Armazena** | Credenciais de usuário | Roles IAM e credenciais AWS |
| **Tokens** | JWT (ID, Access, Refresh) | AWS Credentials (Access Key, Secret) |
| **Acesso** | Sua aplicação | Serviços AWS (S3, DynamoDB, etc) |
| **Uso Típico** | Login/autenticação | Acesso direto a AWS |

#### Fluxo de Autorização com Identity Pool:

```
┌──────────────────────────────────────────────────────────┐
│                  SUA APLICAÇÃO                           │
└───────────────────┬──────────────────────────────────────┘
                    │
         ┌──────────▼──────────┐
         │   User Pool         │
         │ (Autenticação)      │
         │   Retorna ID Token  │
         └──────────┬──────────┘
                    │
                    │ ID Token
                    ▼
         ┌──────────────────────┐
         │  Identity Pool       │
         │ (Troca tokens)       │
         └──────────┬───────────┘
                    │
          ┌─────────▼────────────┐
          │  AWS STS             │
          │ (Gera credenciais)   │
          └──────────┬───────────┘
                     │
         ┌───────────▼────────────┐
         │ AWS Credentials        │
         │ (Access Key + Secret)  │
         └───────────┬────────────┘
                     │
         ┌───────────▼────────────┐
         │  Acesso a Recursos     │
         │  • S3                  │
         │  • DynamoDB            │
         │  • Lambda              │
         └────────────────────────┘
```

#### Tipos de Identidades Suportadas:

✅ **Usuários Autenticados** - Passaram por autenticação (User Pool)
✅ **Usuários Não-Autenticados** - Acesso limitado sem login
✅ **Identidades Federadas** - Google, Facebook, Twitter, OIDC customizado
✅ **Provedores Sociais** - Login com redes sociais

---

### 🛡️ Fluxo Completo de Segurança com Lambda Authorizer

Para máximo controle, você pode usar um **Lambda Authorizer** (custom authorizer) com Cognito:

```
┌─────────────────┐
│   Requisição    │
│   do Cliente    │
└────────┬────────┘
         │
         │ Envia Access Token
         ▼
┌──────────────────────────────┐
│   API Gateway                │
│ (Recebe requisição)          │
└──────────────┬───────────────┘
               │
               │ Chama Lambda Authorizer
               ▼
    ┌──────────────────────┐
    │  Lambda Authorizer   │
    │  (Custom Logic)      │
    └──────────┬───────────┘
               │
   ┌───────────▼───────────┐
   │  Valida Token         │
   │  Verifica Cognito     │
   │  Aplica lógica custom │
   └───────────┬───────────┘
               │
     ┌─────────▼────────┐
     │  Retorna Policy  │
     │  (Allow/Deny)    │
     └─────────┬────────┘
               │
    ┌──────────▼─────────────┐
    │  AWS IAM               │
    │  (Aplica Policy)       │
    └──────────┬─────────────┘
               │
     ┌─────────▼──────────┐
     │  Acesso Autorizado │
     │  ou Negado         │
     └────────────────────┘
```

#### Componentes:

1. **Lambda Authorizer** - Sua função personalizada que valida tokens
2. **API Gateway** - Chama o authorizer antes de processar requisição
3. **AWS IAM** - Aplica permissões baseadas na policy retornada
4. **AWS STS** - Gerencia sessões seguras

---

### 🔐 Exemplo Prático: Sistema Completo

#### Cenário: App de E-commerce

```
┌─────────────────────────────────────────────────────────────┐
│                        CLIENTE                              │
│              (Web App / Mobile App)                         │
└────────────────┬────────────────────────────────────────────┘
                 │
    1️⃣ Faz login via Hosted UI
                 │
         ┌───────▼────────┐
         │  User Pool     │
         │  Cognito       │
         └───────┬────────┘
                 │
    2️⃣ Retorna ID Token + Access Token
                 │
         ┌───────▼────────────────────┐
         │  Sua App Armazena Tokens   │
         └───────┬────────────────────┘
                 │
    3️⃣ Requisição com Access Token
                 │
         ┌───────▼──────────────────────────┐
         │  API Gateway (Endpoint)          │
         │  + Lambda Authorizer             │
         │  Valida: Token é válido?         │
         └───────┬──────────────────────────┘
                 │
    4️⃣ Se OK → Lambda Autorizado
                 │
         ┌───────▼──────────────────────────┐
         │  Lambda Function (Negócio)       │
         │  • Busca produtos               │
         │  • Processa pedidos             │
         │  • Atualiza inventário          │
         └───────┬──────────────────────────┘
                 │
    5️⃣ Acessa dados em DynamoDB/S3 com Identity Pool
                 │
         ┌───────▼──────────────────────────┐
         │  DynamoDB / S3 / RDS             │
         │  (Recursos AWS)                  │
         └───────────────────────────────────┘
```

---

### ✅ Benefícios do Amazon Cognito

✅ **Sem Gerenciar Infraestrutura** - AWS cuida de tudo
✅ **Escalável** - Suporta milhões de usuários
✅ **Seguro** - Criptografia em trânsito e repouso
✅ **Conformidade** - HIPAA, PCI-DSS, SOC 2, GDPR
✅ **Integração Fácil** - SDKs para JavaScript, iOS, Android, Java
✅ **Customizável** - Hooks Lambda para lógica customizada
✅ **Custo Eficiente** - Pague apenas pelos usuários ativos

---

### 🚨 Considerações de Segurança

⚠️ **Sempre use HTTPS** - Proteja tokens em trânsito
⚠️ **Nunca exponha Secrets** - Mantenha credenciais no backend
⚠️ **Implemente MFA** - Aumente segurança de contas importantes
⚠️ **Refresh Token Rotation** - Renove tokens regularmente
⚠️ **Monitor Cognito Logs** - Acompanhe tentativas de acesso

---

## Módulo 12: DevOps e AWS SAM

### 👥 Gerenciamento de Usuários no Cognito

#### Attributes (Atributos)
**O que são:** Metadados que descrevem características individuais dos usuários

Exemplos comuns:
- Email
- Telefone
- Nome
- Data de nascimento
- Localização
- Atributos customizados (ID de cliente, departamento, etc)

#### Groups (Grupos)
**O que são:** Agrupamentos de usuários com características ou permissões similares

| Tipo | Função | Exemplo |
|---|---|---|
| **End Users** | Usuários finais da aplicação | Clientes de e-commerce |
| **Admins** | Acesso total ao sistema | Gerentes de projeto |
| **Moderators** | Acesso intermediário | Suporte ao cliente |
| **Analysts** | Acesso a dados e relatórios | Equipe de BI |

#### Scopes (Escopos)
**O que são:** Níveis de acesso que um usuário tem aos recursos da aplicação

| Escopo | Permissões |
|---|---|
| **read-only** | Pode visualizar, não pode modificar |
| **read-write** | Pode visualizar e modificar |
| **admin** | Acesso total |
| **custom** | Definido por sua aplicação |

**Exemplo Prático:**
```
┌─────────────────────────────────────┐
│   User Pool com Estrutura Completa  │
├─────────────────────────────────────┤
│                                     │
│ Usuário: joao@email.com             │
│ ├─ Attributes:                      │
│ │  ├─ Email: joao@email.com        │
│ │  ├─ Name: João Silva             │
│ │  └─ Department: Vendas           │
│ ├─ Groups: [Moderators]            │
│ └─ Scopes: [read-write]            │
│                                     │
│ Usuário: maria@email.com            │
│ ├─ Attributes:                      │
│ │  ├─ Email: maria@email.com       │
│ │  ├─ Name: Maria Santos           │
│ │  └─ Department: TI               │
│ ├─ Groups: [Admins]                │
│ └─ Scopes: [admin]                 │
│                                     │
└─────────────────────────────────────┘
```

---

## 🚀 DevOps - Cultura e Práticas Modernas

### O que é DevOps?

DevOps é uma **cultura, metodologia e conjunto de práticas** que:
- Quebra barreiras entre desenvolvimento e operações
- Automatiza processos de build, test e deploy
- Foca em entrega contínua com qualidade
- Melhora continuamente a experiência do usuário

### ⚠️ Riscos de Releases Tradicionais (Sem DevOps)

#### Problemas Comuns:

❌ **Pacotes Inconsistentes**
- Ambiente de dev diferente da produção
- Dependências faltando em alguns ambientes

❌ **Deployments Inconsistentes**
- Deploy em servidor A diferente do servidor B
- Configurações manuais e variáveis

❌ **Dificuldade em Upgrades**
- Versões antigas acumulam débito técnico
- Atualizar causa quebras no sistema

❌ **Sem Plano de Rollback**
- Deploy quebra? Não há volta segura
- Downtime prolongado até correção

❌ **Possibilidade de Erro Humano**
- Passos manuais são propensos a erros
- Diferentes pessoas fazem diferente

#### Impacto:

```
RELEASE TRADICIONAL (Problema):

┌──────────────┐
│ 2-3 meses    │
│ de espera    │
└───────┬──────┘
        │
┌───────▼────────────────────────────┐
│  Deploy Grande (1000+ linhas)      │
└───────┬────────────────────────────┘
        │
        ▼
❌ QUEBRA EM PRODUÇÃO!
    → Usuários afetados
    → Receita perdida
    → Reputação abalada
        │
┌───────▼────────────────────────────┐
│  Diagnóstico Lento                 │
│  (Onde está o erro?)               │
└───────┬────────────────────────────┘
        │
        ▼
⏰ 4-8 HORAS para corrigir
```

### ✅ Princípios e Pilares da Cultura DevOps

1. **🎯 Foco nas Necessidades do Consumidor**
   - Entender o que o usuário precisa
   - Velocidade de entrega importa

2. **🤝 Criar Ambiente Altamente Colaborativo**
   - Dev + Ops trabalhando juntos
   - Sem silos departamentais
   - Comunicação aberta

3. **🔬 Experimentar e Aprender Continuamente**
   - Testar hipóteses rapidamente
   - Aprender com falhas
   - Iteração constante

4. **🔐 Incluir Segurança no Processo**
   - Não deixar para o final
   - DevSecOps (segurança em tudo)
   - Testes de segurança automatizados

5. **🤖 Automatizar quando Possível**
   - Builds automáticos
   - Testes automáticos
   - Deployments automáticos

6. **📦 Desenvolver em Pequenas Porções**
   - Features pequenas
   - Commits frequentes
   - Releases incrementais

7. **🚀 Lançar Frequentemente**
   - Deploy diário ou múltiplas vezes por dia
   - Mudanças pequenas = menos risco
   - Feedback mais rápido

8. **📊 Monitorar e Melhorar Continuamente**
   - Métricas e alertas
   - Observabilidade completa
   - Loop de feedback contínuo

#### Fluxo DevOps Ideal:

```
Plan → Code → Build → Test → Release → Deploy → Operate → Monitor
  ↑                                                        │
  └────────────────────────────────────────────────────────┘
              (Feedback contínuo para melhoria)
```

---

### 🛠️ Ferramentas AWS para DevOps

A AWS fornece um conjunto completo de ferramentas para automação:

#### 1. **🔨 AWS CodeBuild**
- **O que faz:** Compila código, executa testes, gera pacotes prontos
- **Uso:** Build automático após cada commit
- **Exemplo:** Compilar Java → Executar testes → Gerar JAR

```
Git Push → CodeBuild → Testa código → Gera artefato
```

#### 2. **📦 AWS CodeArtifact**
- **O que faz:** Repositório gerenciado para artefatos (libs, pacotes)
- **Uso:** Armazenar dependências, versionar bibliotecas
- **Tipos:** Maven, npm, pip, NuGet

```
CodeBuild → CodeArtifact → (Armazena JAR/NPM packages)
            ↓
         Aplicações usam as dependências
```

#### 3. **🚀 AWS CodeDeploy**
- **O que faz:** Automatiza deploy em instâncias EC2, on-premises
- **Uso:** Deploy automático de aplicações
- **Estratégias:** All at once, Rolling, Canary

```
Artefato → CodeDeploy → Instância 1 (10%) → Instância 2 (10%) → ...
           (Canary)     ↓ Monitorar problemas antes de 100%
```

#### 4. **📋 AWS CodePipeline**
- **O que faz:** Orquestra todo o fluxo CI/CD
- **Uso:** Conecta todos os serviços em um pipeline automatizado
- **Fases:** Source → Build → Test → Deploy → Production

```
┌─────────────┐
│ Git Commit  │
└──────┬──────┘
       │
    CodePipeline (Orquestra tudo)
       │
       ├─→ CodeBuild (Compilation)
       ├─→ CodeBuild (Tests)
       ├─→ CodeArtifact (Store)
       └─→ CodeDeploy (Deploy)
```

#### 5. **⚙️ AWS Config**
- **O que faz:** Monitora conformidade de configurações AWS
- **Uso:** Auditoria, rastreamento de mudanças
- **Exemplo:** EC2 deve ter tag "Environment"? Config verifica

#### 6. **🏗️ AWS CloudFormation**
- **O que faz:** Infrastructure as Code (IaC) - Define infraestrutura em YAML/JSON
- **Uso:** Criar, atualizar, deletar recursos AWS automaticamente
- **Benefício:** Infraestrutura versionada no Git

```yaml
# Exemplo: Criar Lambda + API Gateway + DynamoDB
Resources:
  MyLambda:
    Type: AWS::Lambda::Function
    Properties:
      Code: s3://bucket/code.zip
      Runtime: python3.9
```

#### 7. **📊 AWS CloudWatch**
- **O que faz:** Coleta logs, métricas e cria alarmes
- **Uso:** Monitorar saúde da aplicação, alertar em problemas
- **Exemplo:** Se CPU > 80%, enviar SMS

#### 8. **🔍 AWS X-Ray**
- **O que faz:** Rastreamento distribuído de requisições
- **Uso:** Ver exatamente onde a requisição passa, onde demora
- **Exemplo:** Requisição → Lambda (2ms) → DynamoDB (50ms) → S3 (100ms)

#### Visão Geral das Ferramentas:

| Ferramenta | Fase | Função |
|---|---|---|
| **CodePipeline** | Orquestração | Coordena todo fluxo |
| **CodeBuild** | Build | Compila e testa |
| **CodeArtifact** | Artefato | Armazena pacotes |
| **CodeDeploy** | Deploy | Distribui em produção |
| **CloudFormation** | Infraestrutura | Define recursos AWS |
| **Config** | Conformidade | Monitora mudanças |
| **CloudWatch** | Monitoramento | Logs e métricas |
| **X-Ray** | Tracing | Rastreia requisições |

---

## 📦 AWS SAM - Serverless Application Model

### O que é AWS SAM?

**AWS SAM** é um **framework open-source** que facilita construir aplicações serverless. Simplifica:
- Escrita de templates CloudFormation
- Testes locais antes de deploy
- Deploy automático na AWS

### 🔄 Como AWS SAM Funciona?

```
┌──────────────────────────────────────────────────┐
│   Arquivo SAM Template (template.yaml)           │
│   (Simples, focado em serverless)               │
└──────────────┬───────────────────────────────────┘
               │
               │ Processa
               ▼
┌──────────────────────────────────────────────────┐
│   AWS CloudFormation Template                    │
│   (Completo, com todos os detalhes)             │
└──────────────┬───────────────────────────────────┘
               │
               │ Deploy
               ▼
┌──────────────────────────────────────────────────┐
│   Recursos AWS Criados:                          │
│   ✅ Lambda Functions                           │
│   ✅ API Gateway                                │
│   ✅ DynamoDB Tables                           │
│   ✅ S3 Buckets                                │
│   ✅ SNS Topics                                │
│   ✅ SQS Queues                                │
└──────────────────────────────────────────────────┘
```

### 🚀 Começando com AWS SAM

#### Pré-requisitos:

1. **🔑 Configurar Credenciais AWS**
   ```bash
   aws configure
   # Insira: Access Key ID, Secret Access Key, Region
   ```

2. **☕ Instalar Java JDK 11+**
   ```bash
   # Verificar instalação
   java -version
   # Saída: openjdk version "11.0.x" ou superior
   ```

3. **🔨 Instalar Maven (Construtor Java)**
   ```bash
   # macOS
   brew install maven
   
   # Linux (Ubuntu/Debian)
   sudo apt-get install maven
   
   # Verificar
   mvn -version
   ```

4. **🐳 Instalar Docker**
   - Necessário para testes locais
   - Download: https://www.docker.com/

5. **📦 Instalar AWS SAM CLI**
   ```bash
   # macOS
   brew install aws-sam-cli
   
   # Linux
   pip install aws-sam-cli
   ```

6. **✅ Verificar Instalação**
   ```bash
   sam --version
   # Saída: SAM CLI, version 1.x.x
   ```

---

### 🧪 Testando Localmente com SAM CLI

#### Comando 1: sam local invoke
**Objetivo:** Invocar uma função Lambda localmente por nome

```bash
# Primeiro, fazer build do projeto Java
sam build

# Invoca função específica com evento de teste
sam local invoke OrderProcessor -e events/order-event.json

# Saída:
# START RequestId: abc123...
# INFO: Recebida requisição de pedido
# DEBUG: Pedido validado com sucesso
# INFO: Pagamento processado - ID: pay-12345
# {"statusCode": 200, "body": "Pedido processado"}
# END RequestId: abc123...
```

**Quando usar:**
- Testar função individual
- Debugar lógica rápido
- Simular eventos específicos sem AWS

#### Comando 2: sam local start-api
**Objetivo:** Inicia um API Gateway local para testar endpoints

```bash
# Compilar código Java
sam build

# Iniciar API local
sam local start-api
# Saída: Running on http://127.0.0.1:3000

# Em outro terminal, testar endpoint
curl -X POST http://127.0.0.1:3000/orders \
  -H "Content-Type: application/json" \
  -d '{"orderId": "123", "productId": "ABC", "quantity": 5}'

# Resposta:
# {"statusCode": 200, "body": "Pedido processado"}
```

**Quando usar:**
- Testar fluxo completo da API
- Desenvolver frontend local
- Simular autenticação e autorização

#### Comando 3: sam local generate-event
**Objetivo:** Gera eventos de exemplo para testar sem fonte real

```bash
# Gerar evento S3
sam local generate-event s3 put > events/s3-event.json

# Gerar evento API Gateway
sam local generate-event apigateway aws-proxy > events/api-event.json

# Gerar evento DynamoDB
sam local generate-event dynamodb update-stream > events/ddb-event.json

# Gerar evento SQS
sam local generate-event sqs receive-message > events/sqs-event.json

# Serviços Suportados:
# ✅ API Gateway, S3, DynamoDB, Kinesis, SNS, SQS
# ✅ CloudWatch, Cognito, Lex, Lambda, RDS, e mais
```

**Quando usar:**
- Desenvolver sem dados reais
- Testes unitários/integração
- CI/CD pipelines

#### Fluxo Completo de Desenvolvimento:

```
1️⃣ Escrever Código Java
   └─ src/main/java/com/example/OrderProcessor.java

2️⃣ Fazer Build
   └─ sam build

3️⃣ Testar Localmente
   ├─ sam local invoke OrderProcessor -e events/order-event.json
   ├─ sam local start-api
   └─ Verificar saída nos logs

4️⃣ Gerar Eventos de Teste
   └─ sam local generate-event [service] [action]

5️⃣ Testes Integrados com Maven
   └─ mvn test

6️⃣ Deploy em Produção
   └─ sam deploy --guided

7️⃣ Monitorar
   └─ CloudWatch Logs + X-Ray
```

---

### 🎯 Estratégias de Deployment (Implantação)

Ao fazer deploy, você pode escolher como as mudanças são introduzidas:

#### 1. **🔴 All at Once (Tudo de Uma Vez)**

```
v1.0 (Atual)
    │
    └─→ v1.1 (Nova versão)
    
    Características:
    ✅ Rápido
    ✅ Simples
    ❌ Sem rollback automático
    ❌ 100% de risco
    
    Quando usar:
    • Hotfix crítico
    • Ambiente de test/staging
```

#### 2. **📊 Linear (Incremento Gradual)**

```
v1.0 → v1.1 (25% do tráfego)
    → v1.1 (50% do tráfego)  [Aguarda 5 min, monitora]
    → v1.1 (75% do tráfego)  [Aguarda 5 min, monitora]
    → v1.1 (100% do tráfego) [Aguarda 5 min, confirma]

    Características:
    ✅ Risco controlado
    ✅ Fácil rollback
    ✅ Monitora cada passo
    ❌ Mais lento (15-30 min)
    
    Quando usar:
    • Updates importantes
    • Mudanças em dados críticos
    • Versão com novo banco de dados
```

#### 3. **🎪 Canary (Teste com Pequeno Grupo)**

```
v1.0 (99% do tráfego)
v1.1 (1% do tráfego, "canários")

Se OK → Aumenta para 50%
Se ERRO → Rollback automático para v1.0

    Características:
    ✅ Menor risco (1% afetado)
    ✅ Feedback rápido
    ✅ Rollback automático em erro
    ❌ Requer monitoramento

    Quando usar:
    • Ficar features grandes
    • Machine Learning models
    • Produção crítica
```

#### Comparativo de Estratégias:

| Aspecto | All at Once | Linear | Canary |
|---|---|---|---|
| **Velocidade** | ⚡ Muito rápido | ⏱️ 15-30 min | ⏱️ 10-20 min |
| **Risco** | 🔴 Alto (100%) | 🟡 Médio (25-50%) | 🟢 Baixo (1%) |
| **Rollback** | ❌ Manual | ✅ Automático | ✅ Automático |
| **Monitoramento** | ⚠️ Crítico | 📊 Importante | 📊 Essencial |
| **Melhor para** | Testes/Hotfix | Produção normal | Produção crítica |

#### Exemplo de Configuração SAM (Canary):

```yaml
Resources:
  MyFunction:
    Type: AWS::Serverless::Function
    Properties:
      AutoPublishAlias: live
      DeploymentPreference:
        Type: Canary
        TriggerConfigurations:
          - DeploymentSuccess:
              StatisticType: Average
              Type: Alexa Skills
              Value: 0.01  # Alerta se 1% de erro
          - DeploymentFailure:
              Type: Alexa Skills
```

---

### 📋 Exemplo Completo: SAM Template

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31

Description: Aplicação Serverless com SAM em Java

Globals:
  Function:
    Timeout: 20
    Runtime: java11
    MemorySize: 512
    Environment:
      Variables:
        TABLE_NAME: !Ref MyTable

Resources:
  # Lambda Function em Java
  HelloFunction:
    Type: AWS::Serverless::Function
    Properties:
      CodeUri: hello_world/
      Handler: com.example.OrderProcessor::handleRequest
      Runtime: java11
      Events:
        HelloAPI:
          Type: Api
          Properties:
            Path: /orders
            Method: POST
      Policies:
        - DynamoDBCrudPolicy:
            TableName: !Ref MyTable

  # DynamoDB Table
  MyTable:
    Type: AWS::DynamoDB::Table
    Properties:
      TableName: Orders
      BillingMode: PAY_PER_REQUEST
      AttributeDefinitions:
        - AttributeName: orderId
          AttributeType: S
      KeySchema:
        - AttributeName: orderId
          KeyType: HASH

  # API Gateway
  ServerlessApi:
    Type: AWS::Serverless::Api
    Properties:
      StageName: prod
      Auth:
        DefaultAuthorizer: MyCognito
        Authorizers:
          MyCognito:
            Type: COGNITO_USER_POOLS
            IdentitySource: method.request.header.Authorization
            UserPoolArn: arn:aws:cognito:region:account-id:userpool/pool-id

Outputs:
  OrdersApi:
    Description: "API Gateway endpoint URL para processar pedidos"
    Value: !Sub "https://${ServerlessApi}.execute-api.${AWS::Region}.amazonaws.com/prod/orders"
  
  OrderProcessorFunction:
    Description: "ARN da Lambda Function"
    Value: !GetAtt HelloFunction.Arn
    
  OrdersTable:
    Description: "Nome da tabela DynamoDB"
    Value: !Ref MyTable
```

---

### 🎬 Fluxo Completo com SAM

```
┌─────────────────────────────────────────────────────┐
│  1. Desenvolver Localmente (Java + Maven)          │
│  $ mvn clean install                               │
│  $ sam build                                       │
│  $ sam local start-api                             │
└────────────────────┬────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────┐
│  2. Testar Unitariamente                           │
│  $ mvn test                                        │
│  ✅ Todos os testes passam                         │
└────────────────────┬────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────┐
│  3. Commit ao Git                                  │
│  $ git add .                                       │
│  $ git commit -m "Add order processor"             │
│  $ git push origin feature/orders                  │
└────────────────────┬────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────┐
│  4. CodePipeline Acionado (CI/CD)                  │
│  ├─ CodeBuild: mvn clean package                  │
│  ├─ CodeBuild: mvn test                           │
│  ├─ CodeBuild: sam build                          │
│  └─ CodeArtifact: Armazena JAR                    │
└────────────────────┬────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────┐
│  5. Deploy com SAM (Canary)                        │
│  $ sam deploy --guided                            │
│  ├─ Deploy em 1% dos usuários (canário)          │
│  ├─ Monitor por 5 minutos                        │
│  ├─ Verificar métricas e logs                    │
│  └─ Expand para 100% se OK                       │
└────────────────────┬────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────┐
│  6. Monitorar em Produção                         │
│  ├─ CloudWatch: Logs, Métricas, Alarmes          │
│  ├─ X-Ray: Rastreamento distribuído              │
│  ├─ Dashboards: Visualização em tempo real       │
│  └─ Alertas: Email, SMS, Slack                   │
└─────────────────────────────────────────────────────┘
```

---

### ✅ Benefícios de usar AWS SAM

✅ **Desenvolvimento Rápido** - Escrever menos código
✅ **Testes Locais** - Não precisa da AWS para testes
✅ **Deploy Automatizado** - Um comando faz tudo
✅ **Segurança** - Integrado com Cognito, IAM, etc
✅ **Escalabilidade** - Serverless automático
✅ **Custo Eficiente** - Pague apenas pelo uso
✅ **Observabilidade** - CloudWatch + X-Ray integrados


## Módulo 13: Monitoramento e Logging na AWS

### 📊 O que é Observabilidade?

Observabilidade é a **capacidade de observar, entender e usar dados** sobre o estado e comportamento das suas aplicações e infraestrutura.

**Não é apenas monitorar uptime!** É sobre:
- 👁️ **Visibilidade Completa** - Ver o que está acontecendo na aplicação
- 🔧 **Troubleshooting em Tempo Real** - Identificar e resolver problemas rápido
- 😊 **Experiência do Cliente** - Entender como os usuários são afetados
- 💰 **Performance = Receita** - Aplicação lenta = usuários indo embora

#### Por que Observabilidade Importa?

```
SEM Observabilidade:          COM Observabilidade:
┌──────────────┐              ┌──────────────────┐
│ Problema!    │              │ Problema!        │
│              │              │                  │
│ Onde está?   │  ❌          │ Onde está? ✅    │
│ Por quê?     │              │ Por quê? ✅      │
│ Como consertar?             │ Como consertar? ✅
│              │              │                  │
│ 4-8 horas    │              │ 5-10 minutos     │
└──────────────┘              └──────────────────┘
```

---

### 🎯 Plano de Observabilidade - Arquitetura

#### Passo 1: Sua Aplicação
```
┌──────────────────────────────┐
│   Sua Aplicação              │
│  • Lambda Functions          │
│  • EC2 Instances             │
│  • Containers (ECS)          │
│  • Databases                 │
│  • APIs                      │
└────────────────┬─────────────┘
                 │
        Gera dados constantemente
```

#### Passo 2: Coletar Dados
```
         ┌─────────────────────────────┐
         │  Coletar Metrics & Logs     │
         │                             │
         │ • CloudWatch Agents         │
         │ • X-Ray SDKs                │
         │ • Application Logs          │
         │ • Custom Metrics            │
         └────────────┬────────────────┘
                      │
         Dados estruturados e enviados
```

#### Passo 3: Ferramentas AWS de Monitoramento
```
         ┌─────────────────────────────┐
         │  AWS Monitoring Tools       │
         │                             │
         │ • CloudWatch                │
         │ • X-Ray                     │
         │ • CloudTrail                │
         │ • EventBridge               │
         └────────────┬────────────────┘
                      │
         Armazena, processa e correlaciona
```

#### Passo 4: Análise e Visualização
```
         ┌─────────────────────────────┐
         │  Analyze & Visualize        │
         │                             │
         │ • Dashboards               │
         │ • Alarmes Automáticos      │
         │ • Rastreamento de Traces   │
         │ • Logs Insights            │
         │ • Relatórios               │
         └────────────┬────────────────┘
                      │
         Insights e ações
```

#### Fluxo Completo Visual:

```
┌──────────────────────────────┐
│   Sua Aplicação              │
│  (Lambda, EC2, Containers)   │
└────────────────┬─────────────┘
                 │
    ┌────────────▼──────────────────────────┐
    │   Coleta de Dados (Agents/SDKs)      │
    │  • Métricas (CPU, Memory, Latência)  │
    │  • Logs (Eventos, Erros, Debug)      │
    │  • Traces (Caminho das requisições)  │
    └────────────┬──────────────────────────┘
                 │
    ┌────────────▼──────────────────────────┐
    │   Ferramentas AWS                     │
    │  • CloudWatch (Logs + Métricas)       │
    │  • X-Ray (Rastreamento)               │
    │  • CloudTrail (Auditoria)             │
    └────────────┬──────────────────────────┘
                 │
    ┌────────────▼──────────────────────────┐
    │   Análise & Visualização              │
    │  • Dashboards                         │
    │  • Alarmes                            │
    │  • Traces                             │
    │  • Logs Insights                      │
    └────────────┬──────────────────────────┘
                 │
                 ▼
    ┌────────────────────────────────────────┐
    │   Ações & Insights                     │
    │  • Alertas via Email/SMS/Slack         │
    │  • Auto-escalamento                    │
    │  • Remediation automática              │
    │  • Relatórios e análise                │
    └────────────────────────────────────────┘
```

---

### 🔩 Os Três Pilares da Observabilidade

#### 1. 📝 **Logging (Registros de Eventos)**

**O que é:** Registro detalhado de eventos que ocorrem na aplicação

**Exemplos:**
```
[2025-11-19 14:30:45] INFO: Usuário joao@email.com fez login
[2025-11-19 14:30:50] DEBUG: Query executada em 125ms
[2025-11-19 14:31:00] ERROR: Erro ao conectar ao banco de dados
[2025-11-19 14:31:05] WARN: Latência acima do esperado (500ms)
```

**Quando usar:**
- Entender sequência de eventos
- Debugar problemas
- Auditoria de ações
- Rastrear fluxo de uma requisição

**Nível de Log:**
| Nível | Uso | Exemplo |
|---|---|---|
| **DEBUG** | Informações detalhadas para troubleshooting | Valores de variáveis |
| **INFO** | Eventos importantes | Login, requisição recebida |
| **WARN** | Situações anormais | Latência alta, retry |
| **ERROR** | Erros que não impedem execução | Query falhou |
| **FATAL** | Aplicação não pode continuar | Banco de dados offline |

#### 2. 📊 **Metrics (Métricas Numéricas)**

**O que é:** Representação numérica de dados em um momento específico

**Exemplos:**
```
CPU Usage: 45%
Memory Used: 2.5 GB
Requests per Second: 150
Response Time: 125ms
Error Rate: 0.5%
Database Connections: 42/100
```

**Características:**
- Ponto único no tempo
- Agregáveis (somas, médias, percentis)
- Ideais para alertas

**Tipos de Métricas:**
| Tipo | Descrição | Exemplo |
|---|---|---|
| **Gauge** | Valor em um ponto no tempo | CPU: 45% |
| **Counter** | Incrementa continuamente | Total de requisições: 1.2M |
| **Histogram** | Distribuição de valores | Tempo de resposta |
| **Summary** | Estatísticas | P50, P99 latência |

#### 3. 🔍 **Tracing (Rastreamento de Requisições)**

**O que é:** Seguir o caminho completo de uma requisição através dos serviços

**Exemplo de Trace:**
```
Requisição do Cliente
    │
    ├─→ [0ms] API Gateway (entrada)
    │
    ├─→ [2ms] Lambda Authorizer (autenticação)
    │
    ├─→ [15ms] Lambda Principal (lógica)
    │   │
    │   ├─→ [8ms] DynamoDB Query (buscar dados)
    │   │
    │   └─→ [5ms] S3 Read (obter arquivo)
    │
    ├─→ [3ms] Lambda (formatar resposta)
    │
    └─→ [25ms] Resposta ao cliente

Total: 50ms
```

**Por que é importante:**
- Ver onde o tempo está sendo gasto
- Identificar gargalos
- Entender dependências entre serviços
- Detectar cascata de falhas

---

### 🛠️ Ferramentas AWS para Observabilidade

#### 1. **CloudWatch** (Logs + Métricas)
- **Logs:** Armazena e analisa logs de aplicações
- **Métricas:** Coleta métricas automáticas de recursos AWS
- **Alarms:** Cria alarmes para notificações
- **Dashboards:** Visualiza dados em tempo real

#### 2. **AWS X-Ray** (Rastreamento)
- **Distributed Tracing:** Rastreia requisições entre serviços
- **Service Map:** Visualiza dependências entre componentes
- **Error Analysis:** Identifica onde erros ocorrem
- **Performance Insights:** Mostra gargalos

#### 3. **CloudTrail** (Auditoria)
- **API Calls:** Registra todas as chamadas de API
- **Compliance:** Prove auditoria para regulamentações
- **User Activity:** Rastreia quem fez o quê
- **Resource Changes:** Monitora mudanças em recursos

---

### 📈 Como CloudWatch Funciona?

#### Fluxo Completo:

```
┌──────────────────────────────────────────────────────────┐
│  Seus Recursos AWS + Dados Customizados                 │
│  • EC2 CPU, Memory, Disk                                │
│  • RDS Connections, Query Time                          │
│  • Lambda Invocations, Duration                         │
│  • Seus custom metrics (aplicação)                      │
└────────────────────┬─────────────────────────────────────┘
                     │
                     │ Envia dados
                     ▼
          ┌──────────────────────┐
          │  CloudWatch          │
          │  (Coleta e Armazena) │
          └──────────┬───────────┘
                     │
        ┌────────────┴────────────┐
        │                         │
        ▼                         ▼
   ┌─────────────┐         ┌─────────────┐
   │  Métricas   │         │  Logs       │
   │  (Numéricos)│         │  (Texto)    │
   └──────┬──────┘         └─────┬───────┘
          │                      │
   ┌──────▼──────────────────────▼───────┐
   │  CloudWatch Alarms                  │
   │  (Define condições de alerta)       │
   └──────────────────┬───────────────────┘
                      │
        ┌─────────────▼─────────────┐
        │  Estatísticas Disponíveis │
        │  • Average                │
        │  • Sum                    │
        │  • Maximum                │
        │  • Minimum                │
        │  • Count                  │
        │  • Percentiles (P50, P99) │
        └─────────────┬─────────────┘
                      │
        ┌─────────────▼──────────────────┐
        │  AWS Management Console        │
        │  (Visualiza dados)             │
        └────────────┬────────────────────┘
                     │
        ┌────────────┴────────────┐
        │                         │
        ▼                         ▼
   ┌──────────────┐      ┌──────────────────┐
   │  Dashboards  │      │  Consumidores    │
   │  (Visualizar)│      │  de Alertas      │
   └──────────────┘      │  • Email         │
                         │  • SMS           │
                         │  • SNS           │
                         │  • Lambda        │
                         │  • Auto Scaling  │
                         └──────────────────┘
```

---

### 📋 Exemplo Prático: Monitorando uma Lambda

#### Cenário: Função que processa pedidos

```java
// 1. Lambda Handler com Logging em Java
import com.amazonaws.services.lambda.runtime.Context;
import com.amazonaws.services.lambda.runtime.RequestHandler;
import org.json.JSONObject;

public class OrderProcessor implements RequestHandler<JSONObject, String> {
    
    @Override
    public String handleRequest(JSONObject event, Context context) {
        System.out.println("INFO: Recebida requisição de pedido");
        
        try {
            // Validar pedido
            validateOrder(event);
            System.out.println("DEBUG: Pedido validado com sucesso");
            
            // Processar pagamento
            String paymentId = processPayment(event);
            System.out.println("INFO: Pagamento processado - ID: " + paymentId);
            
            // Salvar em DynamoDB
            saveToDB(event, paymentId);
            
            return "{\"statusCode\": 200, \"body\": \"Pedido processado\"}";
        } catch (Exception e) {
            System.err.println("ERROR: Falha ao processar pedido - " + e.getMessage());
            return "{\"statusCode\": 500, \"body\": \"Erro ao processar\"}";
        }
    }
    
    private void validateOrder(JSONObject order) {
        // Validação lógica
        if (!order.has("productId") || !order.has("quantity")) {
            throw new IllegalArgumentException("Pedido inválido");
        }
    }
    
    private String processPayment(JSONObject order) {
        // Lógica de pagamento
        return "pay-" + System.currentTimeMillis();
    }
    
    private void saveToDB(JSONObject order, String paymentId) {
        // Salvar em DynamoDB
        System.out.println("DEBUG: Salvando pedido no DynamoDB");
    }
}
```

#### 2. CloudWatch captura:

**Logs:**
```
[2025-11-19 14:30:00] INFO: Recebida requisição de pedido
[2025-11-19 14:30:01] DEBUG: Pedido validado com sucesso
[2025-11-19 14:30:05] INFO: Pagamento processado - ID: pay-12345
[2025-11-19 14:30:07] (response retornada)
```

**Métricas Automáticas:**
- Invocations: 1
- Duration: 7000ms
- Errors: 0
- Throttles: 0
- ConcurrentExecutions: 1

#### 3. Alarme Configurado:

```
Se: Duration > 10000ms (10 segundos)
Então: Enviar email ao administrador
```

#### 4. Dashboard:

```
┌─────────────────────────────────────────────────┐
│        Lambda OrderProcessor - Dashboard        │
├─────────────────────────────────────────────────┤
│                                                 │
│  Invocations (Last Hour): 4,532               │
│  ████████████████████ 100%                    │
│                                                 │
│  Average Duration: 7.2s                       │
│  ████████░░░░░░░░░░ 36%                       │
│                                                 │
│  Error Rate: 0.2%                             │
│  ░░░░░░░░░░░░░░░░░░ 0%                        │
│                                                 │
│  Concurrent Executions: 12/1000               │
│  ██░░░░░░░░░░░░░░░░░ 1%                       │
│                                                 │
└─────────────────────────────────────────────────┘
```

---

### ✅ Boas Práticas de Observabilidade

✅ **Logar em Níveis Apropriados**
- DEBUG: Detalhes para desenvolvimento
- INFO: Eventos importantes
- WARN: Situações anormais
- ERROR: Erros reais

✅ **Incluir Contexto nos Logs**
```
❌ BAD:  "Erro ao salvar"
✅ GOOD: "Erro ao salvar pedido ID:123 para usuário:456 - connection timeout"
```

✅ **Criar Métricas Customizadas**
```java
// Exemplo: Métrica customizada com Java
import software.amazon.awssdk.services.cloudwatch.CloudWatchClient;
import software.amazon.awssdk.services.cloudwatch.model.*;

public class CustomMetrics {
    public static void publishMetric(String value) {
        try (CloudWatchClient cloudwatch = CloudWatchClient.builder().build()) {
            PutMetricDataRequest request = PutMetricDataRequest.builder()
                .namespace("OrderProcessor")
                .metricData(MetricDatum.builder()
                    .metricName("ProcessingTime")
                    .value(Double.parseDouble(value))
                    .unit(StandardUnit.SECONDS)
                    .build())
                .build();
            
            cloudwatch.putMetricData(request);
            System.out.println("Métrica enviada com sucesso!");
        }
    }
}
```

✅ **Configurar Alarms Inteligentes**
- Não alertar por cada pico
- Usar médias e percentis (P99)
- Escalonar alertas

✅ **Rastrear Requisições de Ponta a Ponta**
- Use Correlation IDs
- Propague IDs entre serviços
- Visualize com X-Ray

✅ **Revisar Logs Regularmente**
- Identificar padrões de erro
- Detectar anomalias
- Melhorar código preventivamente

---

### 🎯 Resumo da Observabilidade

| Pilar | Ferramenta | O que mede | Quando usar |
|---|---|---|---|
| **Logging** | CloudWatch Logs | Eventos textuais | Entender sequência, debugar |
| **Metrics** | CloudWatch Metrics | Valores numéricos | Alertas, trends, SLA |
| **Tracing** | AWS X-Ray | Caminho requisições | Performance, gargalos |

---

## 🔍 AWS X-Ray - Rastreamento Distribuído Completo

### O que é X-Ray?

AWS X-Ray é um **serviço que rastreia requisições** através de toda sua arquitetura distribuída, mostrando exatamente por onde a requisição passou, quanto tempo levou em cada etapa e onde os problemas ocorrem.

**Pense assim:**
> Se CloudWatch mostra "sua Lambda demorou 5 segundos", X-Ray mostra "0.5s em validação, 2s em acesso ao DynamoDB, 1.5s em chamada à API externa, 1s em salvar cache"

---

### 🏗️ Conceitos Principais do X-Ray

#### 1️⃣ **Trace** (Rastreamento Completo)

Um **Trace** é o caminho completo de uma requisição do início ao fim.

```
📱 Cliente faz requisição
        ↓
🌐 API Gateway (Trace começa aqui)
        ↓
⚡ Lambda 1 (Processamento)
        ↓
🗄️ DynamoDB (Consulta)
        ↓
📤 API Externa (Pagamento)
        ↓
⚡ Lambda 2 (Finalização)
        ↓
✅ Resposta ao cliente (Trace termina aqui)

⏱️ Tempo Total: 5 segundos (todo esse fluxo = 1 Trace)
```

#### 2️⃣ **Segment** (Etapas Principais)

Um **Segment** representa cada serviço que a requisição passa.

```
Trace Completo: "Processar Pedido"
│
├─ Segment 1: API Gateway (200ms)
├─ Segment 2: Lambda - OrderProcessor (2000ms)
├─ Segment 3: DynamoDB - GetCustomer (500ms)
├─ Segment 4: External API - PaymentGateway (1500ms)
└─ Segment 5: DynamoDB - SaveOrder (300ms)

Total: 4500ms (5 Segments = 1 Trace)
```

#### 3️⃣ **Subsegment** (Detalhes Internos)

Um **Subsegment** é um detalhe DENTRO de um Segment.

```
Segment: Lambda - OrderProcessor (2000ms)
│
├─ Subsegment: validateOrder() (100ms)
├─ Subsegment: calculateTax() (50ms)
├─ Subsegment: applyDiscount() (150ms)
├─ Subsegment: connectToDatabase() (800ms)
│   ├─ Subsegment: executeQuery() (700ms)
│   └─ Subsegment: parseResults() (100ms)
└─ Subsegment: logMetrics() (100ms)

Total: 2000ms (vários Subsegments = 1 Segment)
```

#### 4️⃣ **Annotations** (Metadados para Filtro)

São **tags que você adiciona** para depois filtrar Traces.

```java
// Exemplo: Adicionar annotations em Java
import com.amazonaws.xray.AWSXRay;

public String processOrder(JSONObject event) {
    // Adiciona annotations
    AWSXRay.getCurrentSegment().putAnnotation("customerId", event.getString("customerId"));
    AWSXRay.getCurrentSegment().putAnnotation("orderAmount", event.getDouble("amount"));
    AWSXRay.getCurrentSegment().putAnnotation("region", "us-east-1");
    
    // Seu código...
}

// Depois você pode filtrar: "customerId = 'CUST123'"
```

---

### 🔄 Fluxo Completo do X-Ray

```
┌──────────────────────────────────────────────────────┐
│  Sua Aplicação Java + AWS SDK                       │
│  ├─ API Gateway                                     │
│  ├─ Lambda (com X-Ray SDK)                         │
│  ├─ DynamoDB                                       │
│  └─ APIs Externas                                  │
└────────────────────┬─────────────────────────────────┘
                     │
                     │ Envia dados de tracing
                     │ (automaticamente com SDK)
                     ▼
          ┌──────────────────────┐
          │   AWS X-Ray Service  │
          │   (Coleta Traces)    │
          └──────────┬───────────┘
                     │
        ┌────────────▼────────────┐
        │                         │
        ▼                         ▼
   ┌──────────────┐         ┌──────────────┐
   │  Service Map │         │  Traces      │
   │  (Graf de    │         │  (Timeline   │
   │   serviços)  │         │   detalhado) │
   └──────┬───────┘         └──────┬───────┘
          │                        │
   ┌──────▼────────────────────────▼──────┐
   │   CloudWatch Insights                │
   │   (Análise de performance)           │
   └────────────────────┬─────────────────┘
                        │
          ┌─────────────▼──────────────┐
          │  Descoberta de Problemas   │
          │  • Serviço lento?          │
          │  • Onde é o gargalo?       │
          │  • Qual função falhou?     │
          │  • Quem foi afetado?       │
          └────────────────────────────┘
```

---

### 📊 O que você vê no Console do X-Ray?

#### Service Map (Mapa de Serviços):

```
┌─────────────┐     ┌──────────────┐     ┌───────────┐
│   Client    │────▶│ API Gateway  │────▶│  Lambda   │
└─────────────┘     └──────────────┘     └─────┬─────┘
                                              │
                                    ┌─────────▼──────────┐
                                    │                    │
                                 ┌──▼──┐         ┌──────▼──┐
                                 │  DB  │         │External │
                                 │      │         │  API    │
                                 └─────┘         └────────┘

Performance:
- API Gateway: 50ms (verde ✅)
- Lambda: 2000ms (amarelo ⚠️ - verificar)
- DB: 500ms (verde ✅)
- API Externa: 1500ms (vermelho ❌ - problema!)
```

#### Timeline de uma Requisição:

```
00ms ├─ API Gateway inicia
50ms │  └─ Lambda inicia
     │    ├─ Validação: 100ms
100ms│    │
     │    ├─ Busca em DB: 500ms
500ms│    │
     │    ├─ Chamada API: 1500ms
     │    │ (lentidão aqui!)
1500ms│   │
     │    └─ Salva resultado: 200ms
2000ms├─ Lambda termina
     └─ Resposta enviada ao cliente
```

---

### ✅ Benefícios do X-Ray

✅ **Visualizar Arquitetura** - Veja como seus serviços se conectam
✅ **Identificar Gargalos** - Qual serviço é lento?
✅ **Debug Distribuído** - Entenda fluxo de requisições complexas
✅ **Performance** - Otimize baseado em dados reais
✅ **Rastrear Erros** - De onde vieram os problemas?
✅ **Análise de Dependências** - Como serviços dependem um do outro?

---

### 🎯 Casos de Uso Reais

| Situação | Como X-Ray Ajuda |
|----------|-----------------|
| **API lenta** | Mostra qual serviço é o culpado |
| **Requisição falha** | Aponta em qual etapa falhou |
| **Debug em produção** | Vê exatamente o que aconteceu |
| **Otimizar custos** | Identifica chamadas desnecessárias |
| **Usuário relata problema** | Rastreia requests daquele usuário |

