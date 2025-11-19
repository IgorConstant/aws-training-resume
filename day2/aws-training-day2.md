# AWS Training - Day 2: DVA Focus

## 📚 Sumário
1. [Banco de Dados](#banco-de-dados)
   - AWS Database Services
   - Relacional vs Não Relacional
   - DynamoDB - Conceitos Básicos
   - Operações e Capacidades
   - RRU (Read Request Unit)
   - WRU (Write Request Unit)
   - Cálculo de Capacidade - Exemplo Prático
   - Formas de Acessar o DynamoDB
   - Programando com DynamoDB
   - Cache em DynamoDB

2. [Computação Serverless](#computação-serverless)
   - AWS Lambda - Comparando Serviços
   - Principais Eventos que Invocam Lambda
   - Amazon EC2
   - Amazon ECS
   - Amazon EKS
   - Comparação: EC2 vs ECS vs EKS vs Lambda
   - Cold Start vs Warm Start
   - Monitoramento de Cold Starts
   - Ciclo de Vida do Lambda
   - Lambda Snap Start

3. [API e Integrações](#api-e-integrações)
   - Amazon API Gateway

---

## Banco de Dados

### 🏗️ AWS Database Services

AWS oferece múltiplas soluções de banco de dados para diferentes necessidades:

**Tipos Principais:**
- **Relacional** (SQL): RDS, Aurora, PostgreSQL, MySQL
- **NoSQL**: DynamoDB, DocumentDB, MongoDB
- **In-Memory**: ElastiCache, MemoryDB
- **Data Warehouse**: Redshift
- **Busca**: OpenSearch, Elasticsearch

**Critérios de Escolha:**
- Tipo de dado
- Padrão de acesso
- Escala e performance necessária
- Custo

---

### 🔄 Relacional vs Não Relacional

#### Banco de Dados Relacional (SQL)

**Características:**
- Dados organizados em **tabelas com linhas e colunas**
- **Schema rígido** - estrutura definida antecipadamente
- **ACID** - Atomicidade, Consistência, Isolamento, Durabilidade
- **Relacionamentos** entre tabelas via chaves estrangeiras
- **SQL** como linguagem padrão

**Vantagens:**
- Dados estruturados e bem organizados
- Integridade referencial garantida
- Consultas complexas com JOINs

**Desvantagens:**
- Menos flexível para dados não estruturados
- Escalabilidade vertical (difícil escalar horizontalmente)
- Menos ideal para grandes volumes de dados não estruturados

**Exemplo de Uso:** Aplicações de RH, Contabilidade, E-commerce

---

#### Banco de Dados Não Relacional (NoSQL)

**Características:**
- **Schema flexível** - estrutura pode variar entre documentos
- Dados em formatos variados: Documentos JSON, Pares chave-valor, Grafos
- **Escalabilidade horizontal** - distribuição em múltiplos servidores
- Eventual Consistency (nem sempre ACID)

**Tipos NoSQL:**
1. **Key-Value** (DynamoDB): Acesso rápido por chave
2. **Documentos** (MongoDB, DocumentDB): JSON/BSON
3. **Grafos** (Neptune): Relacionamentos complexos
4. **Time Series** (Timestream): Dados com timestamp

**Vantagens:**
- Altamente escalável
- Flexível para dados semi-estruturados
- Performance superior para leitura/escrita
- Ideal para Big Data

**Desvantagens:**
- Queries complexas mais difíceis
- Eventual consistency (pode haver inconsistências temporárias)
- Menos transações ACID

**Exemplo de Uso:** Redes sociais, IoT, Analytics, Catálogos de produtos

---

### 🚀 DynamoDB - Conceitos Básicos

#### O que é DynamoDB?

**DynamoDB** é um banco de dados **NoSQL totalmente gerenciado** da AWS:
- ✅ Sem servidor - não precisa gerenciar infraestrutura
- ✅ Altamente disponível - replicado automaticamente
- ✅ Escalável - cresce com sua aplicação
- ✅ Performance previsível em milissegundos

#### Estrutura de Dados

**Tabela:**
```
┌─────────────────────────────────────┐
│ Usuários (Tabela)                   │
├──────────────┬───────────┬──────────┤
│ UserID (PK)  │ Nome      │ Email    │
├──────────────┼───────────┼──────────┤
│ user-001     │ João      │ j@ex.com │
│ user-002     │ Maria     │ m@ex.com │
│ user-003     │ Pedro     │ p@ex.com │
└──────────────┴───────────┴──────────┘
```

**Componentes Principais:**

1. **Partition Key (PK)** - Chave de Partição
   - Obrigatória
   - Identifica unicamente o item
   - Determina em qual partição o dado é armazenado
   - Exemplo: `UserID = "user-001"`

2. **Sort Key (SK)** - Chave de Classificação (Opcional)
   - Permite múltiplos itens com mesma PK
   - Ordena itens dentro da partição
   - Exemplo: `OrderID = "order-123"` com `Timestamp` como SK

3. **Atributos**
   - Dados adicionais de cada item
   - Não precisam de schema pré-definido
   - Tipos: String, Number, Binary, Boolean, Null, Set, Map, List

#### Exemplo de Tabela com PK + SK

```
┌──────────────┬───────────────┬──────────┬───────────┐
│ UserID (PK)  │ Timestamp(SK) │ Produto  │ Quantidade│
├──────────────┼───────────────┼──────────┼───────────┤
│ user-001     │ 2025-11-01    │ Livro    │ 2         │
│ user-001     │ 2025-11-15    │ Pen      │ 5         │
│ user-002     │ 2025-11-10    │ Notebook │ 1         │
└──────────────┴───────────────┴──────────┴───────────┘
```

#### Capacidade de Leitura/Escrita

**Modo de Capacidade:**

1. **Provisionado** (Padrão)
   - Define RCU (Read Capacity Units) e WCU (Write Capacity Units)
   - Cobrado pelo que provisiona, não pelo que usa
   - Bom quando tráfego é previsível

2. **On-Demand** (Pagamento por uso)
   - Paga apenas pelo que usa
   - Sem limite de throughput
   - Bom para tráfego imprevisível

---

### 📖 RRU - Read Request Unit (Unidade de Leitura)

#### O que é RRU?

**RRU** é a unidade de medida para operações de leitura no DynamoDB:

**1 RRU = 1 leitura de 4KB com consistência forte**

#### Tipos de Leitura

| Tipo | RRU Consumido | Caso de Uso |
|------|---------------|-----------|
| **Strongly Consistent** | 1 RRU por 4KB | Dados críticos que precisam estar atualizados |
| **Eventually Consistent** | 0.5 RRU por 4KB | Dados que podem estar levemente desatualizados |

#### Exemplo Prático RRU

```
Seu item tem 2KB e você faz uma leitura com consistência forte:
→ 4KB é a unidade, então 2KB = arredonda para cima = 1 RRU

Seu item tem 9KB e você faz uma leitura com consistência forte:
→ 9KB ÷ 4KB = 2.25 → arredonda para cima = 3 RRU

Seu item tem 2KB e você faz uma leitura eventually consistent:
→ 2KB ÷ 4KB = 0.5 RRU (metade do custo!)
```

#### Operações que Consomem RRU

| Operação | Cálculo |
|----------|---------|
| **GetItem** | Tamanho do item ÷ 4KB |
| **Query** | Soma dos itens retornados ÷ 4KB |
| **Scan** | Todos os itens varridos ÷ 4KB (mesmo filtrados depois) |
| **BatchGetItem** | Soma de todos os itens ÷ 4KB |

---

### ✍️ WRU - Write Request Unit (Unidade de Escrita)

#### O que é WRU?

**WRU** é a unidade de medida para operações de escrita no DynamoDB:

**1 WRU = 1 escrita de 1KB**

#### Operações que Consomem WRU

| Operação | Cálculo |
|----------|---------|
| **PutItem** | Tamanho do item ÷ 1KB (arredonda para cima) |
| **UpdateItem** | Tamanho do item novo ÷ 1KB |
| **DeleteItem** | 1 WRU (tamanho não importa) |
| **BatchWriteItem** | Soma de todos os itens ÷ 1KB |

#### Exemplo Prático WRU

```
Você insere um item de 500 bytes:
→ 500B < 1KB → arredonda para cima = 1 WRU

Você insere um item de 2.5KB:
→ 2.5KB ÷ 1KB = 2.5 → arredonda para cima = 3 WRU

Você deleta um item de 10KB:
→ DeleteItem = 1 WRU (tamanho não afeta!)

Você atualiza um item adicionando 300 bytes:
→ 300B < 1KB → 1 WRU
```

---

### 🧮 Cálculo de Capacidade - Exemplo Prático Completo

#### Cenário: Aplicação de E-commerce

**Requisitos:**
- 1.000 usuários simultâneos
- Cada usuário lê seu perfil (5KB) a cada 5 segundos
- Cada usuário compra algo a cada 30 segundos (item de 2KB)
- Aplicação roda 24h/dia

#### Passo 1: Calcular RRU necessários

```
Leitura de Perfil (GetItem):
- Tamanho: 5KB
- RRU por leitura: 5KB ÷ 4KB = 1.25 → 2 RRU
- Frequência: 1.000 usuários × 1 leitura a cada 5 segundos
- Por segundo: 1.000 ÷ 5 = 200 requisições por segundo
- RRU necessários: 200 × 2 = 400 RRU/s
```

**Query de histórico (Query - 10 itens de 1KB cada):**
```
- Tamanho retornado: 10 × 1KB = 10KB
- RRU por query: 10KB ÷ 4KB = 2.5 → 3 RRU
- Frequência: 100 queries por segundo (estimado)
- RRU necessários: 100 × 3 = 300 RRU/s

Total RRU: 400 + 300 = 700 RRU/s
```

#### Passo 2: Calcular WRU necessários

```
Escrita de Pedido (PutItem):
- Tamanho do item: 2KB
- WRU por escrita: 2KB ÷ 1KB = 2 WRU
- Frequência: 1.000 usuários × 1 compra a cada 30 segundos
- Por segundo: 1.000 ÷ 30 = 33.33 requisições por segundo
- WRU necessários: 34 × 2 = 68 WRU/s

Total WRU: 68 WRU/s
```

#### Passo 3: Considerar Picos

```
Em um e-commerce típico:
- Pico de tráfego: 3-5x do tráfego normal
- Segurança: adicionar 20% buffer

RRU Provisionado: 700 × 5 × 1.2 = 4.200 RRU/s
WRU Provisionado: 68 × 5 × 1.2 = 408 WRU/s
```

#### Passo 4: Cálculo de Custo Mensal

```
Região: us-east-1 (2024)
- RRU: $0.00013 por RRU/s
- WRU: $0.00065 por WRU/s
- Mês: 30 dias × 86.400 segundos = 2.592.000 segundos

Custo RRU: 4.200 × 0.00013 × 2.592.000 = $1.417,32/mês
Custo WRU: 408 × 0.00065 × 2.592.000 = $689,21/mês

Total: $2.106,53/mês

❌ PROBLEMA: Muito gasto em horas de baixa demanda!

✅ SOLUÇÃO: Usar On-Demand pricing
- Custo On-Demand: ~$1,25 por milhão de RRU, $6,25 por milhão WRU
```

#### Tabela Resumo

| Métrica | Valor |
|---------|-------|
| **RRU Necessários (pico)** | 4.200 RRU/s |
| **WRU Necessários (pico)** | 408 WRU/s |
| **Custo Provisionado** | $2.106/mês |
| **Custo On-Demand** | Variável ~$500-800/mês |
| **Melhor Opção** | On-Demand para este caso |

---

### 🔗 Formas de Acessar o DynamoDB

#### 1. **AWS SDK** (Programático)

Acesso via código Java, Python, Node.js, etc.

```java
// Exemplo Java
DynamoDbClient dynamoDB = DynamoDbClient.builder().build();
GetItemRequest request = GetItemRequest.builder()
    .tableName("Usuarios")
    .key(Map.of("UserID", AttributeValue.builder().s("user-001").build()))
    .build();
GetItemResponse response = dynamoDB.getItem(request);
```

**Vantagens:**
- ✅ Controle total
- ✅ Integração fácil com aplicações
- ✅ Suporta batch operations

**Desvantagens:**
- ❌ Requer código
- ❌ Gerenciamento de conexões

#### 2. **AWS Console** (Web UI)

Acesso visual via AWS Management Console

```
AWS Console → DynamoDB → Tabelas → Editar Itens
```

**Vantagens:**
- ✅ Fácil para testes
- ✅ Sem necessidade de código
- ✅ Visualização clara

**Desvantagens:**
- ❌ Não escalável
- ❌ Lento para grandes volumes
- ❌ Apenas para administração

#### 3. **AWS CLI** (Command Line Interface)

Acesso via terminal

```bash
# Get item
aws dynamodb get-item \
    --table-name Usuarios \
    --key '{"UserID":{"S":"user-001"}}'

# Put item
aws dynamodb put-item \
    --table-name Usuarios \
    --item '{"UserID":{"S":"user-001"},"Nome":{"S":"João"}}'

# Scan
aws dynamodb scan --table-name Usuarios
```

**Vantagens:**
- ✅ Rápido para operações únicas
- ✅ Automatização de scripts

**Desvantagens:**
- ❌ Verboso
- ❌ Não ideal para aplicações

#### 4. **API Gateway + Lambda** (RESTful)

Acesso via HTTP/HTTPS

```
Cliente HTTP → API Gateway → Lambda → DynamoDB
```

```bash
curl -X GET https://api.exemplo.com/usuarios/user-001
curl -X POST https://api.exemplo.com/usuarios \
  -H "Content-Type: application/json" \
  -d '{"UserID":"user-002","Nome":"Maria"}'
```

**Vantagens:**
- ✅ Acesso público
- ✅ Fácil integração com frontend
- ✅ Controle de segurança

**Desvantagens:**
- ❌ Latência adicional
- ❌ Custos de Lambda/API Gateway

#### 5. **DynamoDB Streams** (Event-driven)

Acesso via eventos de mudança

```
Mudança no DynamoDB → Stream → Lambda/Kinesis → Aplicação
```

**Vantagens:**
- ✅ Processamento assíncrono
- ✅ Desacoplamento

**Desvantagens:**
- ❌ Latência não determinística
- ❌ Mais complexo

#### 6. **PartiQL** (SQL-like)

Acesso com sintaxe similar a SQL

```sql
SELECT * FROM Usuarios WHERE UserID = 'user-001'
INSERT INTO Usuarios VALUE {'UserID': ?, 'Nome': ?}
```

**Vantagens:**
- ✅ Familiar para Devs SQL
- ✅ Simples

**Desvantagens:**
- ❌ Performance menor que SDK nativo
- ❌ Menos funcionalidades

---

### ⚙️ Operações e Capacidades

#### Operações Básicas

**1. PutItem** - Inserir ou sobrescrever um item
```
PUT Usuários
{
  "UserID": "user-004",
  "Nome": "Ana",
  "Email": "ana@example.com",
  "Idade": 28
}
```

**2. GetItem** - Recuperar um item por chave
```
GET Usuários
{
  "UserID": "user-001",
  "Timestamp": "2025-11-01"
}
```

**3. UpdateItem** - Atualizar atributos específicos
```
UPDATE Usuários SET Idade = 29
WHERE UserID = "user-001"
```

**4. DeleteItem** - Deletar um item
```
DELETE Usuários
WHERE UserID = "user-004"
```

**5. Query** - Buscar itens por chave
```
QUERY Usuários
WHERE UserID = "user-001"
AND Timestamp BETWEEN "2025-11-01" AND "2025-11-30"
```

**6. Scan** - Varrer todos os itens (evitar em produção!)
```
SCAN Usuários
```

#### Índices

**Global Secondary Index (GSI)**
- Índice em diferentes chaves que a tabela principal
- Permite queries em outras colunas
- Custa RCU/WCU adicionais
- Limite: 10GB por partição GSI

**Local Secondary Index (LSI)**
- Mesma PK, SK diferente
- Criado na criação da tabela
- Limite: 10GB por valor de PK

---

### 💻 Programando com DynamoDB

#### Operações com AWS SDK

**Exemplo em Java (AWS SDK v2):**

```java
import software.amazon.awssdk.services.dynamodb.DynamoDbClient;
import software.amazon.awssdk.services.dynamodb.model.*;
import software.amazon.awssdk.core.waiters.WaiterResponse;

public class DynamoDBExample {
    private static DynamoDbClient dynamoDb;
    
    public static void main(String[] args) {
        dynamoDb = DynamoDbClient.builder().build();
        
        // 1. Put Item
        putItem("user-005", "Carlos", "carlos@example.com", 35);
        
        // 2. Get Item
        getItem("user-001");
        
        // 3. Update Item
        updateItem("user-001", 30);
        
        // 4. Query
        queryItems("user-001");
        
        // 5. Delete Item
        deleteItem("user-005");
        
        dynamoDb.close();
    }
    
    // 1. PUT ITEM
    public static void putItem(String userId, String nome, String email, int idade) {
        PutItemRequest request = PutItemRequest.builder()
            .tableName("Usuarios")
            .item(Map.of(
                "UserID", AttributeValue.builder().s(userId).build(),
                "Nome", AttributeValue.builder().s(nome).build(),
                "Email", AttributeValue.builder().s(email).build(),
                "Idade", AttributeValue.builder().n(String.valueOf(idade)).build()
            ))
            .build();
        
        dynamoDb.putItem(request);
        System.out.println("Item inserted: " + userId);
    }
    
    // 2. GET ITEM
    public static void getItem(String userId) {
        GetItemRequest request = GetItemRequest.builder()
            .tableName("Usuarios")
            .key(Map.of("UserID", AttributeValue.builder().s(userId).build()))
            .build();
        
        GetItemResponse response = dynamoDb.getItem(request);
        System.out.println("Item: " + response.item());
    }
    
    // 3. UPDATE ITEM
    public static void updateItem(String userId, int novaIdade) {
        UpdateItemRequest request = UpdateItemRequest.builder()
            .tableName("Usuarios")
            .key(Map.of("UserID", AttributeValue.builder().s(userId).build()))
            .updateExpression("SET Idade = :age")
            .expressionAttributeValues(Map.of(
                ":age", AttributeValue.builder().n(String.valueOf(novaIdade)).build()
            ))
            .build();
        
        dynamoDb.updateItem(request);
        System.out.println("Item updated: " + userId);
    }
    
    // 4. QUERY
    public static void queryItems(String userId) {
        QueryRequest request = QueryRequest.builder()
            .tableName("Usuarios")
            .keyConditionExpression("UserID = :uid")
            .expressionAttributeValues(Map.of(
                ":uid", AttributeValue.builder().s(userId).build()
            ))
            .build();
        
        QueryResponse response = dynamoDb.query(request);
        response.items().forEach(item -> System.out.println("Found: " + item));
    }
    
    // 5. DELETE ITEM
    public static void deleteItem(String userId) {
        DeleteItemRequest request = DeleteItemRequest.builder()
            .tableName("Usuarios")
            .key(Map.of("UserID", AttributeValue.builder().s(userId).build()))
            .build();
        
        dynamoDb.deleteItem(request);
        System.out.println("Item deleted: " + userId);
    }
}
```

#### Batch Operations

**Processar múltiplos itens:**

```java
import software.amazon.awssdk.services.dynamodb.model.*;
import java.util.*;

public class DynamoBatchOperations {
    private static DynamoDbClient dynamoDb;
    
    // BATCH WRITE
    public static void batchWriteItems() {
        List<WriteRequest> writeRequests = new ArrayList<>();
        
        for (int i = 0; i < 100; i++) {
            WriteRequest writeRequest = WriteRequest.builder()
                .putRequest(PutRequest.builder()
                    .item(Map.of(
                        "UserID", AttributeValue.builder().s("user-" + i).build(),
                        "Nome", AttributeValue.builder().s("Usuario " + i).build()
                    ))
                    .build())
                .build();
            writeRequests.add(writeRequest);
        }
        
        // Dividir em chunks de 25 (limite do DynamoDB)
        for (int i = 0; i < writeRequests.size(); i += 25) {
            List<WriteRequest> chunk = writeRequests.subList(i, 
                Math.min(i + 25, writeRequests.size()));
            
            BatchWriteItemRequest batchRequest = BatchWriteItemRequest.builder()
                .requestItems(Map.of(
                    "Usuarios", chunk
                ))
                .build();
            
            dynamoDb.batchWriteItem(batchRequest);
        }
        System.out.println("Batch write completed");
    }
    
    // BATCH GET
    public static void batchGetItems() {
        List<Map<String, AttributeValue>> keys = new ArrayList<>();
        keys.add(Map.of("UserID", AttributeValue.builder().s("user-001").build()));
        keys.add(Map.of("UserID", AttributeValue.builder().s("user-002").build()));
        
        KeysAndAttributes keysAndAttributes = KeysAndAttributes.builder()
            .keys(keys)
            .build();
        
        BatchGetItemRequest request = BatchGetItemRequest.builder()
            .requestItems(Map.of("Usuarios", keysAndAttributes))
            .build();
        
        BatchGetItemResponse response = dynamoDb.batchGetItem(request);
        response.responses().forEach((table, items) -> {
            items.forEach(item -> System.out.println("Found: " + item));
        });
    }
}
```

#### Tratamento de Erros

```java
import software.amazon.awssdk.services.dynamodb.model.*;

public class DynamoDBErrorHandling {
    private static DynamoDbClient dynamoDb;
    
    public static void handleErrors() {
        try {
            PutItemRequest request = PutItemRequest.builder()
                .tableName("Usuarios")
                .item(Map.of(
                    "UserID", AttributeValue.builder().s("user-006").build()
                ))
                .build();
            
            dynamoDb.putItem(request);
            
        } catch (ResourceNotFoundException e) {
            System.out.println("Erro: Tabela não encontrada");
            
        } catch (ProvisionedThroughputExceededException e) {
            System.out.println("Erro: Limite de throughput excedido");
            // Implementar retry com backoff exponencial
            // retryWithBackoff(request);
            
        } catch (ConditionalCheckFailedException e) {
            System.out.println("Erro: Condição não foi atendida");
            
        } catch (DynamoDbException e) {
            System.out.println("Erro DynamoDB: " + e.awsErrorDetails().errorMessage());
        }
    }
    
    private static void retryWithBackoff(PutItemRequest request) {
        int maxRetries = 3;
        int delayMs = 100;
        
        for (int i = 0; i < maxRetries; i++) {
            try {
                Thread.sleep(delayMs);
                dynamoDb.putItem(request);
                return;
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            } catch (ProvisionedThroughputExceededException e) {
                delayMs *= 2; // Backoff exponencial
            }
        }
    }
}
```

---

### 🔄 Cache em DynamoDB

#### O Problema

DynamoDB é muito rápido, mas:
- Ainda leva milissegundos
- Queries repetidas consomem RCU
- Hot keys podem virar gargalo

#### Solução: ElastiCache ou DAX

**DynamoDB Accelerator (DAX)**

Especificamente designed para DynamoDB:
- Cache em-memória nativo
- Compatível com SDK existente
- Latência submilissegundos
- Gerenciamento automático de invalidação

```java
import com.amazon.dax.client.dynamodbv2.ClientConfig;
import com.amazon.dax.client.dynamodbv2.AmazonDaxClientBuilder;
import software.amazon.awssdk.services.dynamodb.DynamoDbClient;
import software.amazon.awssdk.services.dynamodb.model.*;

public class DAXExample {
    public static void main(String[] args) {
        // Configurar DAX
        AmazonDaxClientBuilder builder = AmazonDaxClientBuilder.standard();
        builder.withEndpointConfiguration(
            new ClientConfig.ClientConfigBuilder()
                .withEndpoint("dax-endpoint.amazonaws.com:8111")
                .build()
        );
        
        // Obtém cliente DynamoDB com suporte a DAX
        DynamoDbClient daxClient = builder.build();
        
        // Mesmo código, mas MUITO mais rápido!
        GetItemRequest request = GetItemRequest.builder()
            .tableName("Usuarios")
            .key(Map.of("UserID", AttributeValue.builder().s("user-001").build()))
            .build();
        
        // Primeira requisição: 5ms (carrega do DynamoDB)
        long start = System.currentTimeMillis();
        GetItemResponse response1 = daxClient.getItem(request);
        System.out.println("1ª requisição: " + (System.currentTimeMillis() - start) + "ms");
        
        // Segunda requisição: <1ms (vem do cache DAX)
        start = System.currentTimeMillis();
        GetItemResponse response2 = daxClient.getItem(request);
        System.out.println("2ª requisição: " + (System.currentTimeMillis() - start) + "ms");
        
        daxClient.close();
    }
}
```

**Benefícios:**
- Reduz carga no DynamoDB
- Melhora latência
- Economiza dinheiro (menos RCU necessário)
- Ideal para reads repetidos

**Padrão de Cache:**
```
Requisição → DAX → Cache Hit? → SIM: Retorna (µs)
                   ↓ NÃO
                 DynamoDB → Armazena no DAX → Retorna (ms)
```

---

## Computação Serverless

### ⚡ AWS Lambda - Comparando Serviços

#### O que é Lambda?

**AWS Lambda** é um serviço de computação serverless que permite executar código sem gerenciar servidores:
- ✅ Sem infraestrutura para gerenciar
- ✅ Auto-escalável
- ✅ Pague apenas pelo uso
- ✅ Linguagens suportadas: Python, Node.js, Java, C#, Go, Ruby, PHP

### ⚡ AWS Lambda - Comparando Serviços

#### O que é Lambda?

**AWS Lambda** é um serviço de computação serverless que permite executar código sem gerenciar servidores:
- ✅ Sem infraestrutura para gerenciar
- ✅ Auto-escalável
- ✅ Pague apenas pelo uso
- ✅ Linguagens suportadas: Python, Node.js, Java, C#, Go, Ruby, PHP

---

### 🎯 Principais Eventos que Invocam AWS Lambda

Lambda pode ser acionado por várias fontes de eventos. Aqui está uma visão completa:

#### 1. **Invocação Síncrona** (Requisitor aguarda resposta)

| Fonte | Descrição | Exemplo |
|-------|-----------|---------|
| **API Gateway** | Requisições HTTP/HTTPS | Cliente web chama API REST |
| **Application Load Balancer (ALB)** | Requisições de load balancer | Aplicação tradicional |
| **CloudFront** | Distribuição de conteúdo | Processamento de requisições |
| **Cognito** | Autenticação | Validação de usuário |
| **Alexa** | Smart home / Voice | Comando de voz |

**Fluxo:**
```
Cliente → API Gateway → Lambda (processa) → Resposta (espera)
```

#### 2. **Invocação Assíncrona** (Requisitor não aguarda)

| Fonte | Descrição | Exemplo |
|-------|-----------|---------|
| **S3** | Upload/Delete de arquivos | Gerar thumbnail após upload |
| **SNS** | Notificações publicadas | Enviar email em lote |
| **SQS** | Mensagens na fila | Processar pedidos |
| **DynamoDB Streams** | Mudanças na tabela | Replicar dados para ElasticSearch |
| **Kinesis Streams** | Stream de dados em tempo real | Análise de dados IoT |
| **EventBridge** | Eventos agendados/customizados | Executar diariamente às 2am |
| **SNS** | Tópicos de notificação | Fan-out a múltiplos lambdas |
| **CloudWatch Logs** | Novos logs criados | Processar logs em tempo real |

**Fluxo:**
```
Evento → SQS → Lambda (processa em background)
Cliente não aguarda resposta
```

#### 3. **Invocação por Polling** (Lambda consulta a fonte)

| Fonte | Descrição | Exemplo |
|-------|-----------|---------|
| **SQS** | Lambda consulta fila regularmente | Worker para processar tarefas |
| **DynamoDB Streams** | Lambda lê stream | CDC (Change Data Capture) |
| **Kinesis** | Lambda consome stream | Processar logs em streaming |

**Fluxo:**
```
SQS → Lambda (polling a cada minuto) → Processa mensagens
```

#### Tabela Comparativa de Eventos

```
┌─────────────────┬──────────────┬────────────┬──────────────┐
│ Fonte           │ Síncrono     │ Assíncrono │ Polling      │
├─────────────────┼──────────────┼────────────┼──────────────┤
│ API Gateway     │ ✅           │            │              │
│ S3              │              │ ✅         │              │
│ SQS             │              │ ✅ / ✅    │ ✅           │
│ DynamoDB Stream │              │ ✅         │ ✅           │
│ SNS             │              │ ✅         │              │
│ EventBridge     │              │ ✅         │              │
│ CloudWatch      │              │ ✅         │              │
│ Kinesis         │              │ ✅         │ ✅           │
└─────────────────┴──────────────┴────────────┴──────────────┘
```

**Padrão de Múltiplas Fontes:**

```
┌─────────────────────────────────────────┐
│     Arquitetura Event-Driven            │
├─────────────────────────────────────────┤
│                                         │
│ SÍNCRONOS:                              │
│ API Gateway ──→ Lambda (responde <1s)   │
│ ALB ──────────→ Lambda (responde <1s)   │
│                                         │
│ ASSÍNCRONOS:                            │
│ S3 Upload ────→ Lambda (background)     │
│ SNS ──────────→ Lambda (background)     │
│ SQS ──────────→ Lambda (polling)        │
│                                         │
│ AGENDADOS:                              │
│ EventBridge ──→ Lambda (cron job)       │
│                                         │
└─────────────────────────────────────────┘
```

---

### 🖥️ Amazon EC2 (Elastic Compute Cloud)

#### O que é?

**EC2** é um serviço que oferece **máquinas virtuais (instâncias) na nuvem**. Você tem controle total sobre a infraestrutura.

#### Características Principais

| Aspecto | Descrição |
|---------|-----------|
| **Modelo** | IaaS (Infrastructure as a Service) |
| **Gerenciamento** | Manual (você gerencia tudo) |
| **Escalabilidade** | Manual ou Auto Scaling Groups |
| **Duração** | Contínua (24/7 se quiser pagar) |
| **Custo** | Por hora (ou reservado/spot) |
| **Startup** | Minutos |
| **Performance** | Previsível e controlável |

#### Quando Usar EC2

**✅ Ideal para:**
- Aplicações que precisam rodar 24/7
- Requisitos específicos de SO (Windows, Linux específicas)
- Múltiplos cores/CPU necessários
- Controle total do ambiente
- Aplicações legadas
- Bases de dados complexas

**❌ Não ideal para:**
- Tarefas ocasionais/eventos
- Cargas de trabalho imprevisíveis
- Quando você quer zero gerenciamento

#### Arquitetura Típica

```
┌─────────────────────────────────┐
│        AWS Account              │
├─────────────────────────────────┤
│  ┌──────────────────────────┐  │
│  │   EC2 Instance (Linux)   │  │
│  │  ├─ CPU: 4 vCPU         │  │
│  │  ├─ RAM: 16GB           │  │
│  │  ├─ Disco: 100GB EBS    │  │
│  │  └─ IP: 10.0.1.10       │  │
│  └──────────────────────────┘  │
│  ┌──────────────────────────┐  │
│  │   EC2 Instance (Linux)   │  │
│  │  └─ (similar)            │  │
│  └──────────────────────────┘  │
│                                 │
│  Load Balancer → Distribui      │
│                                 │
└─────────────────────────────────┘
```

#### Exemplo de Custo (us-east-1)

```
t3.large (2 vCPU, 8GB RAM):
- On-Demand: ~$0.083/hora = ~$60/mês
- 1 ano reservado: ~$300
- 3 anos reservado: ~$450

c5.xlarge (4 vCPU, 8GB RAM):
- On-Demand: ~$0.17/hora = ~$120/mês
```

---

### 🐳 Amazon ECS (Elastic Container Service)

#### O que é?

**ECS** é um serviço de **orquestração de containers** gerenciado pela AWS. Você define containers e o ECS gerencia a execução.

#### Características Principais

| Aspecto | Descrição |
|---------|-----------|
| **Modelo** | CaaS (Container as a Service) |
| **Gerenciamento** | Parcialmente gerenciado |
| **Escalabilidade** | Automática (com policies) |
| **Duração** | Contínua ou on-demand |
| **Custo** | Por recurso EC2 (infraestrutura) |
| **Container Runtime** | Docker (nativamente) |
| **Orquestração** | AWS gerencia scheduling |

#### Tipos de Launch

**1. EC2 Launch Type**
```
Você gerencia instâncias EC2 + ECS orquestra containers
```
- ✅ Controle total
- ✅ Custo mais previsível
- ❌ Você gerencia patches/updates

**2. Fargate Launch Type** (Serverless)
```
AWS gerencia infraestrutura, você só define containers
```
- ✅ Zero gerenciamento de infraestrutura
- ✅ Pay-per-use
- ❌ Menos controle granular

#### Quando Usar ECS

**✅ Ideal para:**
- Aplicações containerizadas
- Microserviços
- Deployment contínuo
- Quando você quer gerenciar menos infraestrutura que EC2
- APIs e web services

**❌ Não ideal para:**
- Aplicações muito simples (use Lambda)
- Quando precisa de máximo controle (use EC2)

#### Arquitetura Típica

```
┌──────────────────────────────────┐
│    ECS Cluster (Fargate)         │
├──────────────────────────────────┤
│                                  │
│  ┌────────────────────────────┐ │
│  │   Task Definition          │ │
│  │  ├─ Container Image        │ │
│  │  ├─ Port: 8080            │ │
│  │  ├─ Memory: 512MB         │ │
│  │  └─ CPU: 256              │ │
│  └────────────────────────────┘ │
│                                  │
│  ┌──────────┐  ┌──────────┐    │
│  │  Task 1  │  │  Task 2  │    │
│  │ Container│  │ Container│    │
│  └──────────┘  └──────────┘    │
│                                  │
│  Load Balancer → Distribui       │
│                                  │
└──────────────────────────────────┘
```

---

### ☸️ Amazon EKS (Elastic Kubernetes Service)

#### O que é?

**EKS** é um serviço **Kubernetes gerenciado** pela AWS. Você trabalha com Kubernetes nativamente.

#### Características Principais

| Aspecto | Descrição |
|---------|-----------|
| **Modelo** | Kubernetes as a Service |
| **Gerenciamento** | AWS gerencia control plane |
| **Escalabilidade** | Automática (HPA, cluster scaler) |
| **Duração** | Contínua ou on-demand |
| **Custo** | Cluster + Nodes EC2/Fargate |
| **Container Runtime** | Docker (e outros) |
| **Orquestração** | Kubernetes nativo |

#### Quando Usar EKS

**✅ Ideal para:**
- Grandes aplicações (enterprise)
- Quando você já usa Kubernetes
- Portabilidade entre clouds
- Microserviços complexos
- Multi-cloud strategy

**❌ Não ideal para:**
- Projetos pequenos
- Quando não precisa de Kubernetes
- Maior complexidade operacional

#### Arquitetura Típica

```
┌──────────────────────────────────┐
│    EKS Cluster (Kubernetes)      │
├──────────────────────────────────┤
│  Control Plane (AWS Managed)     │
│  ├─ API Server                   │
│  ├─ Scheduler                    │
│  └─ etcd                         │
├──────────────────────────────────┤
│  Worker Nodes (EC2/Fargate)      │
│  ┌─────────────────────────────┐ │
│  │  Node 1 (EC2 Instance)      │ │
│  │  ├─ Pod 1                   │ │
│  │  ├─ Pod 2                   │ │
│  │  └─ kubelet                 │ │
│  └─────────────────────────────┘ │
│  ┌─────────────────────────────┐ │
│  │  Node 2 (EC2 Instance)      │ │
│  │  ├─ Pod 3                   │ │
│  │  └─ kubelet                 │ │
│  └─────────────────────────────┘ │
│                                  │
│  Service → LoadBalancer          │
│                                  │
└──────────────────────────────────┘
```

---

### 🔍 Comparação: EC2 vs ECS vs EKS vs Lambda

```
┌──────────────┬──────────┬──────────┬──────────┬──────────┐
│   Aspecto    │   EC2    │   ECS    │   EKS    │  Lambda  │
├──────────────┼──────────┼──────────┼──────────┼──────────┤
│ Abstração    │ Máquina  │Container │ Orq. K8s │ Função   │
│ Gerenciamento│ Manual   │ Parcial  │ Parcial  │ Auto     │
│ Escalabilid │ Manual   │ Automática│ Automática│ Automática│
│ Duração      │ 24/7     │ 24/7     │ 24/7     │ <15min   │
│ Custo        │ /hora    │ /recurso │ /recurso │ /execução│
│ Startup      │ Minutos  │ Segundos │ Segundos │ 100ms    │
│ Complexidade │ Alta     │ Média    │ Muito Alta│ Baixa    │
│ Cold Start   │ Não      │ Sim (lag)│ Sim (lag)│ Sim (1-3s) │
│ Ideal para   │ Long-run │Microserv│ Enterprise│ Event    │
└──────────────┴──────────┴──────────┴──────────┴──────────┘
```

#### Matriz de Decisão

```
┌─ Qual é sua aplicação?
│
├─ É uma tarefa ocasional?
│  └─ Sim → Lambda ✅ (serverless, pague por uso)
│  └─ Não → Continua...
│
├─ É uma aplicação que roda 24/7?
│  └─ Sim → Vai continua...
│  └─ Não → Talvez Lambda ou ECS
│
├─ Você quer máximo controle?
│  └─ Sim → EC2 ✅
│  └─ Não → Continua...
│
├─ Sua aplicação é containerizada?
│  └─ Sim → Continua...
│  └─ Não → EC2 ✅
│
├─ Você usa ou precisa de Kubernetes?
│  └─ Sim → EKS ✅ (enterprise)
│  └─ Não → ECS ✅ (mais simples)
│
└─ Qual é o tamanho do projeto?
   ├─ Pequeno/Médio → ECS + Fargate ✅
   └─ Grande/Enterprise → EKS ✅
```

---

### ❄️ Cold Start vs Warm Start

#### O que é Cold Start?

**Cold Start** ocorre quando Lambda inicia um novo container porque:
- Primeira invocação
- Função inativa por 15+ minutos
- Tráfego muito alto (precisa novos containers)

#### Fases do Cold Start

```
Timeline:

1️⃣ Init Phase (~300-500ms)
   ├─ Baixar imagem do Lambda
   ├─ Inicializar JVM/Runtime
   └─ Alocar memória

2️⃣ Load Code (~100-200ms)
   ├─ Descompactar arquivo ZIP
   └─ Carregar dependências

3️⃣ Static Initialization (~500ms-1s)
   ├─ Executar blocos static
   ├─ Criar conexões (DB, API)
   └─ Inicializar SDKs

4️⃣ Handler Execution (~10-50ms)
   └─ Seu código efetivamente roda

Total: 1.0 - 1.7 SEGUNDOS ❌
```

#### Fases do Warm Start

```
Timeline:

Container já existe e carregado

1️⃣ Handler Execution (~10-50ms)
   └─ Seu código roda direto

Total: 10 - 50 ms ✅
```

#### Impacto por Linguagem

| Linguagem | Cold Start | Warm Start |
|-----------|-----------|-----------|
| **Python** | 200-500ms | 10-50ms |
| **Node.js** | 300-800ms | 10-50ms |
| **Java** | 800-2000ms | 50-200ms |
| **.NET** | 600-1500ms | 50-200ms |
| **Go** | 100-300ms | 10-50ms |
| **Ruby** | 300-600ms | 10-50ms |

**Java é a linguagem com maior cold start!**

---

### 📊 Monitoramento de Cold Starts

#### Como Detectar Cold Starts

**Via CloudWatch Metrics:**

```java
// Lambda
@Override
public String handleRequest(Map<String, Object> event, Context context) {
    // Verificar se é cold start
    String initDuration = context.getFunctionVersion(); // Não direto, mas há logs
    
    context.getLogger().log("Init Duration: " + context.getMemoryLimitInMB());
    
    // Log customizado
    long startTime = System.currentTimeMillis();
    
    // Seu código
    String result = processRequest(event);
    
    long duration = System.currentTimeMillis() - startTime;
    context.getLogger().log("Handler Duration: " + duration + "ms");
    
    return result;
}
```

#### CloudWatch Logs

```
Init Duration: 1200ms
Handler Duration: 45ms
```

#### Métricas CloudWatch Disponíveis

| Métrica | Descrição |
|---------|-----------|
| **Duration** | Tempo total da execução |
| **Billed Duration** | Tempo cobrado (arredondado para 1ms) |
| **Max Memory Used** | Pico de memória usada |
| **Concurrent Executions** | Funções rodando ao mesmo tempo |
| **Throttles** | Requisições rejeitadas |
| **Errors** | Falhas na execução |

#### Dashboard CloudWatch para Cold Starts

```bash
# Via CLI
aws cloudwatch get-metric-statistics \
    --namespace AWS/Lambda \
    --metric-name Duration \
    --dimensions Name=FunctionName,Value=minha-funcao \
    --start-time 2025-01-01T00:00:00Z \
    --end-time 2025-01-02T00:00:00Z \
    --period 60 \
    --statistics Maximum,Average
```

#### Estratégias para Reduzir Cold Starts

**1. Lambda Provisioned Concurrency**
```
Manter N containers "quentes" o tempo todo
Custo: ~0.015/hora por unidade de concorrência
```

**2. Lambda SnapStart** (Java)
```
Snapshot após init, restore em 10ms
Custa ~10% de overhead anual
```

**3. Otimizar Dependências**
```
- Remover libs não usadas
- Usar lazy loading
- Compilação nativa (GraalVM)
```

**4. Manter Função Aquecida**
```
- CloudWatch Rule chamando a cada 5 minutos
- Custa pouquíssimo
- Mantém container "warm"
```

**5. Escolher Linguagem Certa**
```
Go/Python < Node.js < .NET < Java
```

#### Exemplo: Warm vs Cold Start

```
Cenário: 1.000 requisições por hora

COLD START (sem estratégia):
├─ 50% de frio: 500 × 1.5s = 750s
├─ 50% de quente: 500 × 0.05s = 25s
└─ Total: 775s latência agregada ❌

WARM START (SnapStart Java):
├─ 50% de frio: 500 × 0.05s = 25s
├─ 50% de quente: 500 × 0.05s = 25s
└─ Total: 50s latência agregada ✅

Redução: 93.5% de melhoria!
```

---

### 🔄 Ciclo de Vida do Lambda

#### 1. **Init** (Inicialização) - Ocorre UMA VEZ

```java
import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
import com.amazonaws.services.dynamodbv2.model.*;
import com.amazonaws.services.lambda.runtime.Context;
import com.amazonaws.services.lambda.runtime.RequestHandler;
import java.util.Map;

public class LambdaLifecycleExample implements RequestHandler<Map<String, Object>, String> {
    
    // INIT: Código fora do handler
    // Executado UMA VEZ quando a função é criada/provisionada
    static {
        System.out.println("[INIT] Inicializando DynamoDB...");
    }
    
    private static final AmazonDynamoDB dynamoDB = 
        AmazonDynamoDBClientBuilder.defaultClient();
    
    public LambdaLifecycleExample() {
        System.out.println("[INIT] Construtor da classe chamado");
    }
    
    // INVOKE: Código do handler
    // Executado A CADA INVOCAÇÃO
    @Override
    public String handleRequest(Map<String, Object> event, Context context) {
        System.out.println("[INVOKE] Processando requisição");
        
        String userId = (String) event.get("user_id");
        
        GetItemRequest request = new GetItemRequest()
            .withTableName("Usuarios")
            .withKey(Map.of(
                "UserID", new AttributeValue().withS(userId)
            ));
        
        GetItemResult result = dynamoDB.getItem(request);
        System.out.println("[INVOKE] Item encontrado: " + result.getItem());
        
        return result.getItem().toString();
    }
}
```

**O que acontece:**
- Lambda cria um "container"
- Inicializa bibliotecas
- Conecta a recursos (DB, APIs)
- Fica aguardando invocações

#### 2. **Invoke** (Invocação) - Ocorre A CADA EXECUÇÃO

```java
public String handleRequest(Map<String, Object> event, Context context) {
    // Processamento rápido
    String userId = (String) event.get("user_id");
    String name = (String) event.get("name");
    
    // Sua lógica
    Map<String, Object> result = processUser(userId, name);
    
    return "{\n" +
           "    \"statusCode\": 200,\n" +
           "    \"body\": \"" + result.toString() + "\"\n" +
           "}";
}

private Map<String, Object> processUser(String userId, String name) {
    // Implementação
    return Map.of(
        "userId", userId,
        "name", name,
        "processed", true
    );
}
```

#### 3. **Shutdown** (Encerramento) - Quando Não Há Tráfego

Lambda encerra containers inativos após ~15 minutos:
- Conexões são fechadas
- Memória é liberada
- Próxima requisição terá "cold start"

---

### 🚀 Lambda Snap Start

#### O Problema
Cold starts são lentos, especialmente em Java e .NET.

#### A Solução: Snap Start

**Snapshot** do estado inicializado:
1. Cria imagem VM após inicialização
2. Armazena na Amazon S3
3. Próximas invocações usam o snapshot

**Benefícios:**
- ⚡ Reduz cold start de 1-2s para 10-100ms
- 💰 Economiza custo (menos tempo = menos cobrança)
- 🎯 Melhora experiência do usuário

**Como Funciona:**

```
Primeira Invocação:
┌──────────────┐
│ Cold Start   │ 1s
└──────────────┘
      ↓
┌──────────────────────┐
│ Criar Snapshot       │ (Automático)
└──────────────────────┘

Invocações Posteriores:
┌──────────────┐
│ Restore snap │ 10ms
└──────────────┘
      ↓
┌──────────────┐
│ Execute      │ 20ms
└──────────────┘
```

**Habilitando Snap Start:**

```bash
# Ao criar função Java com SnapStart habilitado
aws lambda create-function \
    --function-name my-java-function \
    --runtime java21 \
    --role arn:aws:iam::ACCOUNT:role/lambda-role \
    --handler com.example.Handler::handleRequest \
    --zip-file fileb://function.jar \
    --snap-start ApplyOn=PublishedVersions

# Ou com SAM (Serverless Application Model)
aws sam build
aws sam deploy --guided
```

**template.yaml (SAM):**
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31

Resources:
  MyJavaFunction:
    Type: AWS::Serverless::Function
    Properties:
      CodeUri: target/lambda-function.jar
      Handler: com.example.Handler::handleRequest
      Runtime: java21
      SnapStartResponse:
        ApplyOn: PublishedVersions
      Memory: 512
      Timeout: 60
```

**Limitações:**
- Apenas Java 11+, .NET 6+
- Não funciona com container images
- Estado deve ser idempotente

---

## API e Integrações

### 🌐 Amazon API Gateway

#### O que é API Gateway?

**Amazon API Gateway** é um serviço gerenciado para criar, publicar e gerenciar APIs:
- ✅ Criar RESTful e HTTP APIs
- ✅ Autenticação e autorização
- ✅ Throttling e rate limiting
- ✅ Transformação de requests/responses
- ✅ Cache
- ✅ Logging e monitoramento

#### Tipos de APIs

**1. REST API** (Tradicional)
```
GET    /users         → ListUsers
POST   /users         → CreateUser
GET    /users/{id}    → GetUser
PUT    /users/{id}    → UpdateUser
DELETE /users/{id}    → DeleteUser
```

**2. HTTP API** (Mais rápido e barato)
```
Melhor performance
Custo 70% menor
Menos funcionalidades que REST API
```

**3. WebSocket API** (Real-time)
```
Conexão bidirecional
Ideal para chat, notificações
```

#### Arquitetura REST API

```
┌─────────────────────────────────────────┐
│        Amazon API Gateway               │
├──────────────┬──────────────┬───────────┤
│              │              │           │
│ GET /users   │ POST /users  │ DELETE... │
│              │              │           │
└──────┬───────┴──────┬───────┴─┬─────────┘
       │              │         │
   Lambda 1       Lambda 2   Lambda 3
       │              │         │
       └──────────────┼─────────┘
                      ↓
              DynamoDB / RDS
```

#### Componentes Principais

**1. Resources** (Caminhos)
```
/users              → Coleção de usuários
/users/{userId}     → Usuário específico
/users/{userId}/posts → Posts do usuário
```

**2. Methods** (Verbos HTTP)
- GET    - Recuperar
- POST   - Criar
- PUT    - Atualizar completo
- PATCH  - Atualizar parcial
- DELETE - Deletar

**3. Integration** (Destino)
- Lambda
- EC2
- HTTP Endpoint
- Mock
- AWS Services (SNS, SQS, DynamoDB)

**4. Authorization** (Proteção)
```
├─ API Key
├─ Lambda Authorizer (Custom)
├─ Cognito User Pools
├─ IAM
└─ Open ID Connect
```

#### Configuração de Exemplo

**Criar API REST:**

```bash
# 1. Criar API Gateway
aws apigateway create-rest-api \
    --name "Users API" \
    --description "API para gerenciar usuários"

# 2. Criar resource /users
aws apigateway create-resource \
    --rest-api-id api-id \
    --parent-id root-id \
    --path-part users

# 3. Criar método GET
aws apigateway put-method \
    --rest-api-id api-id \
    --resource-id resource-id \
    --http-method GET \
    --authorization-type NONE

# 4. Integrar com Lambda
aws apigateway put-integration \
    --rest-api-id api-id \
    --resource-id resource-id \
    --http-method GET \
    --type AWS_PROXY \
    --integration-http-method POST \
    --uri arn:aws:apigateway:region:lambda:path/2015-03-31/functions/function-arn/invocations
```

#### Exemplo de Lambda com API Gateway

```java
import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
import com.amazonaws.services.dynamodbv2.model.*;
import com.amazonaws.services.lambda.runtime.Context;
import com.amazonaws.services.lambda.runtime.RequestHandler;
import com.google.gson.Gson;
import java.util.Map;

public class ApiGatewayHandler implements RequestHandler<Map<String, Object>, Map<String, Object>> {
    
    private static final AmazonDynamoDB dynamoDB = 
        AmazonDynamoDBClientBuilder.defaultClient();
    private static final Gson gson = new Gson();
    
    @Override
    public Map<String, Object> handleRequest(Map<String, Object> event, Context context) {
        try {
            String httpMethod = (String) event.get("httpMethod");
            String path = (String) event.get("path");
            Map<String, String> pathParameters = 
                (Map<String, String>) event.get("pathParameters");
            String body = (String) event.get("body");
            
            if ("GET".equals(httpMethod) && path.contains("/users/")) {
                // GET /users/{userId}
                String userId = pathParameters.get("userId");
                return getUser(userId);
                
            } else if ("POST".equals(httpMethod) && "/users".equals(path)) {
                // POST /users
                return createUser(body);
                
            } else if ("PUT".equals(httpMethod) && path.contains("/users/")) {
                // PUT /users/{userId}
                String userId = pathParameters.get("userId");
                return updateUser(userId, body);
                
            } else if ("DELETE".equals(httpMethod) && path.contains("/users/")) {
                // DELETE /users/{userId}
                String userId = pathParameters.get("userId");
                return deleteUser(userId);
                
            } else {
                return errorResponse(405, "Method not allowed");
            }
            
        } catch (Exception e) {
            context.getLogger().log("Error: " + e.getMessage());
            return errorResponse(500, e.getMessage());
        }
    }
    
    private Map<String, Object> getUser(String userId) {
        GetItemRequest request = new GetItemRequest()
            .withTableName("Usuarios")
            .withKey(Map.of("UserID", new AttributeValue().withS(userId)));
        
        GetItemResult result = dynamoDB.getItem(request);
        
        if (result.getItem() != null) {
            return successResponse(200, result.getItem());
        } else {
            return errorResponse(404, "User not found");
        }
    }
    
    private Map<String, Object> createUser(String bodyJson) {
        Map<String, Object> user = gson.fromJson(bodyJson, Map.class);
        
        PutItemRequest request = new PutItemRequest()
            .withTableName("Usuarios")
            .withItem(convertToAttributeValues(user));
        
        dynamoDB.putItem(request);
        
        return successResponse(201, Map.of("message", "User created", "userId", user.get("UserID")));
    }
    
    private Map<String, Object> updateUser(String userId, String bodyJson) {
        Map<String, Object> updates = gson.fromJson(bodyJson, Map.class);
        
        StringBuilder updateExpression = new StringBuilder("SET ");
        Map<String, AttributeValue> expressionValues = new java.util.HashMap<>();
        
        updates.forEach((key, value) -> {
            updateExpression.append(key).append(" = :").append(key).append(", ");
            expressionValues.put(":" + key, new AttributeValue().withS(value.toString()));
        });
        
        updateExpression.setLength(updateExpression.length() - 2);
        
        UpdateItemRequest request = new UpdateItemRequest()
            .withTableName("Usuarios")
            .withKey(Map.of("UserID", new AttributeValue().withS(userId)))
            .withUpdateExpression(updateExpression.toString())
            .withAttributeUpdates(expressionValues);
        
        dynamoDB.updateItem(request);
        
        return successResponse(200, Map.of("message", "User updated"));
    }
    
    private Map<String, Object> deleteUser(String userId) {
        DeleteItemRequest request = new DeleteItemRequest()
            .withTableName("Usuarios")
            .withKey(Map.of("UserID", new AttributeValue().withS(userId)));
        
        dynamoDB.deleteItem(request);
        
        return successResponse(200, Map.of("message", "User deleted"));
    }
    
    private Map<String, Object> successResponse(int statusCode, Object data) {
        return Map.of(
            "statusCode", statusCode,
            "headers", Map.of("Content-Type", "application/json"),
            "body", gson.toJson(data)
        );
    }
    
    private Map<String, Object> errorResponse(int statusCode, String message) {
        return Map.of(
            "statusCode", statusCode,
            "headers", Map.of("Content-Type", "application/json"),
            "body", gson.toJson(Map.of("error", message))
        );
    }
    
    private Map<String, AttributeValue> convertToAttributeValues(Map<String, Object> map) {
        Map<String, AttributeValue> result = new java.util.HashMap<>();
        map.forEach((key, value) -> {
            if (value instanceof Number) {
                result.put(key, new AttributeValue().withN(value.toString()));
            } else {
                result.put(key, new AttributeValue().withS(value.toString()));
            }
        });
        return result;
    }
}
```

#### Tratamento de Requisições

**Request Transformation:**
```json
{
    "requestTemplates": {
        "application/json": {
            "userId": "$input.params('userId')",
            "name": "$input.path('$.name')"
        }
    }
}
```

**Response Mapping:**
```json
{
    "responseTemplates": {
        "application/json": {
            "message": "$input.path('$.message')",
            "data": "$input.path('$.data')"
        }
    }
}
```

#### Segurança

**1. API Key**
```
client → API Key no Header → API Gateway verifica
```

**2. Lambda Authorizer (Token-based)**
```java
import com.amazonaws.services.lambda.runtime.Context;
import com.amazonaws.services.lambda.runtime.RequestHandler;
import java.util.Map;
import com.google.gson.Gson;

public class TokenAuthorizer implements RequestHandler<Map<String, Object>, Map<String, Object>> {
    
    @Override
    public Map<String, Object> handleRequest(Map<String, Object> event, Context context) {
        String token = (String) event.get("authorizationToken");
        String methodArn = (String) event.get("methodArn");
        
        context.getLogger().log("Token: " + token);
        
        try {
            // Validar token (aqui você implementaria validação real)
            if (isTokenValid(token)) {
                return generatePolicy("user-id", "Allow", methodArn);
            } else {
                return generatePolicy("user", "Deny", methodArn);
            }
        } catch (Exception e) {
            throw new RuntimeException("Unauthorized");
        }
    }
    
    private boolean isTokenValid(String token) {
        // Exemplo: validar JWT
        // Em produção, verificar assinatura, expiração, etc.
        return token != null && token.startsWith("Bearer ");
    }
    
    private Map<String, Object> generatePolicy(String principalId, String effect, String resource) {
        Map<String, Object> policyDocument = Map.of(
            "Version", "2012-10-17",
            "Statement", new Object[]{
                Map.of(
                    "Action", "execute-api:Invoke",
                    "Effect", effect,
                    "Resource", resource
                )
            }
        );
        
        return Map.of(
            "principalId", principalId,
            "policyDocument", policyDocument
        );
    }
}
```

**3. CORS** (Cross-Origin Resource Sharing)
```
Cliente Web → Requisição com Origin
           ← API Gateway adiciona headers CORS
           → Navegador permite acesso
```

#### Monitoramento

```python
# CloudWatch Logs habilitados automaticamente
# Métricas disponíveis:
# - Contagem de requisições
# - Latência
# - Taxa de erro (4xx, 5xx)
# - Throttling
```

---

## 🎯 Resumo e Relações

### Fluxo Típico de uma Aplicação DVA

```
┌─────────────┐
│   Cliente   │
│  (Browser)  │
└──────┬──────┘
       │ HTTP Request
       ↓
┌──────────────────────┐
│  API Gateway         │ Autenticação/Validação
│  - REST API          │ Request Transformation
│  - Lambda Authorizer │
└──────┬───────────────┘
       │ Event
       ↓
┌──────────────────┐
│  AWS Lambda      │ Processamento
│  - Cold/Warm     │ Lógica de Negócio
│  - Snap Start    │
└────────┬──────────┘
         │
    ┌────┴────┐
    │          │
    ↓          ↓
┌────────┐  ┌──────────┐
│DynamoDB│  │  S3      │ Dados/Storage
│ Table  │  │ Bucket   │
└────────┘  └──────────┘
    │
    ↓ (opcional)
┌──────────┐
│  DAX     │ Cache
│ (Cache)  │
└──────────┘
```

### Checklist para Entrevista DVA

- ✅ Entender diferenças entre SQL e NoSQL
- ✅ Conhecer operações DynamoDB (Get, Put, Query, Scan)
- ✅ Saber criar índices (GSI, LSI)
- ✅ Programar com SDK (Batch, Error Handling)
- ✅ Entender Lambda (Cold Start, Warm Start, SnapStart)
- ✅ Conhecer fontes de eventos Lambda
- ✅ Usar API Gateway (REST, Resources, Methods)
- ✅ Implementar autenticação (API Key, Lambda Authorizer)

---

## 📚 Referências Úteis

- [AWS DynamoDB Documentation](https://docs.aws.amazon.com/dynamodb/)
- [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/)
- [API Gateway Documentation](https://docs.aws.amazon.com/apigateway/)
- [AWS SnapStart](https://docs.aws.amazon.com/lambda/latest/dg/snapstart.html)

---

**Última atualização:** 19 de novembro de 2025

