# 🚀 Treinamento AWS - Guia Completo

> **Objetivo:** Capacitação em desenvolvimento e deploy de aplicações AWS

---
## 📋 Índice Rápido

- [📚 Módulo 1: Configuração e Deploy](#modulo-1)
- [👋 Módulo 2: Apresentações](#modulo-2)
- [🔧 Módulo 3: Conceitos Técnicos Avançados](#modulo-3)
- [🧪 Laboratório Prático #1: Políticas IAM](#lab-1)
- [💾 Módulo 5: Storage](#modulo-5)

---

## 📚 Módulo 1: Configuração e Deploy {#modulo-1}

### 🎯 Objetivos do Módulo
- ✅ Configurar permissões IAM para ambiente de desenvolvimento
- ✅ Projetar diagramas e fazer deploy usando AWS SDKs
- ✅ Implementar monitoramento e manutenção de aplicações

### 📖 Tópicos Abordados

#### 🔐 IAM (Identity and Access Management)
- Configuração de permissões e políticas de segurança
- Gerenciamento de roles e usuários
- Boas práticas de segurança

#### 🛠️ AWS SDKs
- Desenvolvimento com SDKs oficiais
- Criação de diagramas de arquitetura
- Estratégias de deploy automatizado

#### 📊 Monitoramento
- **CloudWatch:** Métricas e alarmes
- **Logs:** Centralização e análise
- **Performance:** Otimização e troubleshooting

---

## 👋 Módulo 2: Apresentações {#modulo-2}

### 🎯 Objetivos
- Apresentações individuais dos participantes
- Networking e troca de experiências
- Compartilhamento de cases e desafios

---

## 🔧 Módulo 3: Conceitos Técnicos Avançados {#modulo-3}

### 🌐 HTTP Status Codes

| Código | Categoria | Descrição |
|--------|-----------|-----------|
| **1xx** | Informational | Respostas informativas do servidor |
| **2xx** | Success | Requisições processadas com sucesso |
| **3xx** | Redirection | Redirecionamentos necessários |
| **4xx** | Client Error | Erros originados pelo cliente |
| **5xx** | Server Error | Erros internos do servidor |

### ⏱️ AWS SDK para Java - Waiters

**Funcionalidade Principal:**
- Verificação contínua do status de recursos AWS
- Polling automatizado até atingir estado desejado

**Casos de Uso:**
- ✅ Aguardar criação de tabelas DynamoDB
- ✅ Monitorar status de instâncias EC2
- ✅ Verificar deployment de stacks CloudFormation

**Exemplo Prático:**
```java
// Aguardar até que a tabela esteja ativa
DynamoDBWaiter waiter = dynamoDBClient.waiter();
waiter.waitUntilTableExists(DescribeTableRequest.builder()
    .tableName("MinhaTabela")
    .build());
```

### 🔒 Signature Version 4

**Definição:**  
Processo de autenticação criptográfica para todas as requisições AWS API.

**Características:**
- 🔐 Garante integridade das requisições
- 🛡️ Previne ataques man-in-the-middle
- ⚡ Implementação automática nos SDKs

**Processo de Assinatura:**

| Passo | Ação |
|-------|------|
| 1 | Criação da string canônica da requisição |
| 2 | Geração do hash SHA-256 |
| 3 | Assinatura com a chave secreta da AWS |
| 4 | Inclusão no cabeçalho `Authorization` |

### 🎫 Temporary Credentials (STS)

**Security Token Service (STS)** - Gerenciamento de credenciais temporárias


#### ⚙️ Como Funciona

| Passo | Ação |
|-------|------|
| 1 | Requisição: a aplicação solicita credenciais temporárias ao STS. |
| 2 | Emissão: o STS gera e retorna as credenciais temporárias. |
| 3 | Uso: a aplicação usa as credenciais para acessar serviços AWS. |
| 4 | Validação: o serviço AWS valida as credenciais junto ao STS. |
| 5 | Processamento: se válidas, a requisição é processada pelo serviço. |
| 6 | Expiração: as credenciais expiram após o tempo configurado (1-12h). |

#### ✨ Benefícios das Credenciais Temporárias:

| Benefício | Descrição |
|-----------|-----------|
| 🔒 **Segurança Aprimorada** | Reduz riscos de exposição de credenciais |
| 🔄 **Rotação Automática** | Renovação sem intervenção manual |
| 🎯 **Menor Privilégio** | Permissões específicas e limitadas |
| ⏰ **Controle Temporal** | Duração configurável (1-12 horas) |

---

## 🧪 Laboratório Prático #1: Políticas IAM {#lab-1}

### 🎯 Objetivo
Criar políticas personalizadas no IAM para diferentes níveis de acesso e testar a exclusão de um item no S3.


### 🚦 Passo a Passo

#### 1. Instalação e Configuração

- Instale o AWS CLI (caso não tenha): https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
- Configure o perfil `developer`:
  ```bash
  aws configure --profile developer
  ```

#### 2. Listar Buckets S3

Execute:
```bash
aws s3 ls --profile developer
```
_Saída esperada:_
```bash
2022-03-24 11:38:41 4eq9tqpsgbhm-lab1deletemebucket-1ns6v6ffsasnu
2022-03-24 11:38:41 4eq9tqpsgbhmdzusorac-lab1bucket-dnoj4tigrosq
```

#### 3. Definir o Bucket para Exclusão

```bash
bucketToDelete="4eq9tqpsgbhm-lab1deletemebucket-1ns6v6ffsasnu"
```

#### 4. Tentar Deletar o Bucket (esperado erro de permissão)

```bash
aws s3 rb s3://$bucketToDelete --profile developer
```
_Deve retornar erro de permissão._

#### 5. Analisar o Erro com Debug

```bash
aws s3 rb s3://$bucketToDelete --profile developer --debug
```
_Procure por 'AccessDenied' no output._

#### 6. Localizar a Política de Deleção

```bash
aws iam list-policies --output text --query "Policies[?PolicyName == `S3-Delete-Bucket-Policy`].Arn" --profile developer
```

#### 7. Configurar o ARN da Política

```bash
export policyArn=arn:aws:iam::123456789101:policy/S3-Delete-Bucket-Policy
```

#### 8. Anexar a Política ao Role

```bash
aws iam attach-role-policy --policy-arn $policyArn --role-name notes-application-role --profile developer
```

#### 9. Confirmar Políticas Anexadas

```bash
aws iam list-attached-role-policies --role-name notes-application-role --profile developer
```

#### 10. Tentar Deletar Novamente

```bash
aws s3 rb s3://$bucketToDelete --profile developer
```

---
### 💡 Dicas e Boas Práticas

- Sempre use o princípio do menor privilégio
- Teste permissões em ambiente controlado
- Use `--debug` para troubleshooting
- Documente as políticas criadas

## 📚 Módulo 5: Storage {#modulo-5}

### 🎯 Objetivos do Módulo
- Entender os principais tipos de armazenamento AWS e suas aplicações

---
### 📦 Tipos de Storage AWS

| Tipo         | Serviço/Classe                      | Casos de Uso Principais                      |
|--------------|-------------------------------------|----------------------------------------------|
| Block        | EBS (gp3, io2, st1, sc1)            | Volumes persistentes para EC2, bancos, etc.  |
| File         | EFS, FSx, FSx for Lustre, NetApp    | Compartilhamento de arquivos, HPC, Windows   |
| Object       | S3, Glacier (várias classes)        | Backup, arquivos, dados não estruturados     |

#### Exemplos de Classes S3

- S3 Standard, S3 Intelligent-Tiering, S3 Standard-IA, S3 One Zone-IA
- S3 Glacier Instant Retrieval, S3 Glacier Flexible Retrieval, S3 Glacier Deep Archive

---
### 🔍 Comparativo Rápido

| Storage        | Performance | Custo | Durabilidade | Indicado para...           |
|----------------|------------|-------|--------------|----------------------------|
| EBS gp3        | Alta       | Médio | 99,8%        | EC2, bancos, apps críticos |
| EFS Standard   | Alta       | Alto  | 99,9%        | Compartilhamento Linux     |
| S3 Standard    | Média      | Baixo | 99,999999999%| Backup, arquivos gerais    |
| Glacier Deep   | Baixa      | Muito Baixo | 99,999999999%| Arquivamento longo prazo  |

---
### 💡 Dicas de Armazenamento

- Escolha a classe conforme frequência de acesso
- Use versionamento e políticas de ciclo de vida no S3
- Considere custos de recuperação no Glacier
- Para alta performance, avalie EBS io2 ou EFS

---
### 📚 Endpoint S3

**Definição:**
Um endpoint do S3 é um padrão de URL para acessar objetos diretamente em um bucket S3.

```text
https://{nome-do-bucket}.s3.{regiao}.amazonaws.com/{caminho-do-objeto}
```

Exemplo:
```text
https://meu-bucket-exemplo.s3.sa-east-1.amazonaws.com/imagens/foto.png
```

---
### Exemplos de comandos AWS CLI (S3)

- Listar buckets (lista todos os buckets da conta na região atual):
  ```bash
  aws s3 ls
  ```

- Copiar arquivo para bucket (faz upload de um arquivo local):
  ```bash
  aws s3 cp arquivo.txt s3://meu-bucket/
  ```

- Sincronizar diretórios (replica diferenças entre pastas local e S3):
  ```bash
  aws s3 sync ./local-dir s3://meu-bucket/remote-dir
  ```

---
### Exemplo: Usando S3 com SDK (Java)

```java
import software.amazon.awssdk.core.sync.RequestBody;
import software.amazon.awssdk.regions.Region;
import software.amazon.awssdk.services.s3.S3Client;
import software.amazon.awssdk.services.s3.model.PutObjectRequest;

public class UploadS3Exemplo {
  public static void main(String[] args) {
    String bucket = "meu-bucket-exemplo";
    String chave = "arquivos/hello.txt";
    String conteudo = "Olá, S3!";

    // 1) Cria cliente S3 (SDK v2). Ajuste a região conforme necessário.
    S3Client s3 = S3Client.builder()
        .region(Region.SA_EAST_1) // São Paulo
        .build();

    // 2) Define a requisição de upload
    PutObjectRequest putReq = PutObjectRequest.builder()
        .bucket(bucket)
        .key(chave)
        .contentType("text/plain")
        .build();

    // 3) Envia o objeto (upload)
    s3.putObject(putReq, RequestBody.fromString(conteudo));

    System.out.println("Upload concluído em s3://" + bucket + "/" + chave);
  }
}
```

### Módulo 6: Trabalhando com Buckets {#modulo-6}

---
## 🪣 Operações de Buckets

> Exemplos usando AWS CLI. Ajuste a região e o nome do bucket conforme necessário.

```bash
REGIAO="sa-east-1"               # São Paulo
BUCKET="meu-bucket-exemplo-$RANDOM"
```

### Criar bucket

```bash
aws s3api create-bucket \
  --bucket "$BUCKET" \
  --region "$REGIAO" \
  --create-bucket-configuration LocationConstraint="$REGIAO"
```

Observações:
- Em `us-east-1`, não use `--create-bucket-configuration` (regra da API S3).
- Prefira nomes únicos e em minúsculas.

### Listar buckets

```bash
aws s3 ls
```

### Upload e download de objetos

```bash
echo "Olá, S3!" > hello.txt
aws s3 cp hello.txt s3://$BUCKET/pasta/hello.txt
aws s3 cp s3://$BUCKET/pasta/hello.txt ./copia.txt
```

### Excluir bucket

O bucket precisa estar vazio. Para remover tudo de uma vez:

```bash
aws s3 rb s3://$BUCKET --force
```

---
## ⚙️ Configurações de Buckets

### Versionamento

```bash
aws s3api put-bucket-versioning \
  --bucket "$BUCKET" \
  --versioning-configuration Status=Enabled
```

### Criptografia no lado do servidor (SSE-S3)

```bash
aws s3api put-bucket-encryption \
  --bucket "$BUCKET" \
  --server-side-encryption-configuration '{
    "Rules": [
      {"ApplyServerSideEncryptionByDefault": {"SSEAlgorithm": "AES256"}}
    ]
  }'
```

### Bloqueio de Acesso Público

```bash
aws s3api put-public-access-block \
  --bucket "$BUCKET" \
  --public-access-block-configuration \
  BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true
```

### CORS (Cross-Origin Resource Sharing)

Crie um arquivo `cors.json` com a política desejada:

```bash
cat > cors.json <<'JSON'
{
  "CORSRules": [
    {
      "AllowedOrigins": ["https://minhaapp.com"],
      "AllowedMethods": ["GET", "PUT", "POST"],
      "AllowedHeaders": ["*"],
      "ExposeHeaders": ["ETag"],
      "MaxAgeSeconds": 3000
    }
  ]
}
JSON

aws s3api put-bucket-cors \
  --bucket "$BUCKET" \
  --cors-configuration file://cors.json
```

> Dica: para ambientes de desenvolvimento, você pode usar `AllowedOrigins` com `http://localhost:3000`.

---
### Políticas de Bucket (Bucket Policies)

Políticas de bucket são documentos JSON anexados diretamente ao bucket S3 para controlar quem pode realizar quais ações em quais recursos (objetos/prefixos). Elas complementam políticas IAM e ACLs.

Quando usar:
- Controlar acesso por condição (IP, VPC Endpoint, criptografia obrigatória, prefixos).
- Delegar acesso entre contas (cross-account) no nível do bucket/objeto.
- Restringir comportamento independente de quem é o chamador (via Deny).

Observações importantes:
- “Block Public Access” pode bloquear políticas que tornem o bucket/objetos públicos.
- Use sempre o princípio do menor privilégio e teste em ambiente de desenvolvimento.
- Para listar prefixos, use `s3:ListBucket` no recurso do bucket; para acessar objetos, use `s3:GetObject` no recurso com `/*`.

---
#### Exemplo 1 — Leitura para um principal específico (role/usuário)

Substitua `ARN_DO_PRINCIPAL` e `MEU_BUCKET` pelos seus valores.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowReadToSpecificPrincipal",
      "Effect": "Allow",
      "Principal": { "AWS": "ARN_DO_PRINCIPAL" },
      "Action": ["s3:GetObject"],
      "Resource": ["arn:aws:s3:::MEU_BUCKET/*"]
    }
  ]
}
```

---
#### Exemplo 2 — Exigir criptografia no upload (SSE-S3 AES256)

Nega uploads que não definam cabeçalho `x-amz-server-side-encryption` correto.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyUnencryptedUploads",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::MEU_BUCKET/*",
      "Condition": {
        "StringNotEquals": { "s3:x-amz-server-side-encryption": "AES256" }
      }
    }
  ]
}
```

Para KMS, troque por `aws:kms` e adicione restrições de CMK se necessário.

---
#### Exemplo 3 — Restringir acesso por IP

Permite acesso apenas a IPs/cidrs especificados.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowOnlyFromSpecificIP",
      "Effect": "Deny",
      "Principal": "*",
      "Action": ["s3:GetObject", "s3:PutObject"],
      "Resource": "arn:aws:s3:::MEU_BUCKET/*",
      "Condition": {
        "NotIpAddress": { "aws:SourceIp": ["203.0.113.0/24", "198.51.100.10/32"] }
      }
    }
  ]
}
```

---
#### Exemplo 4 — Acesso apenas a um prefixo específico

Concede listagem e leitura apenas dentro de `projetos/clienteA/` para um principal.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ListOnlyClientAPrefix",
      "Effect": "Allow",
      "Principal": { "AWS": "ARN_DO_PRINCIPAL" },
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::MEU_BUCKET",
      "Condition": { "StringLike": { "s3:prefix": ["projetos/clienteA/*"] } }
    },
    {
      "Sid": "GetOnlyClientAPrefix",
      "Effect": "Allow",
      "Principal": { "AWS": "ARN_DO_PRINCIPAL" },
      "Action": ["s3:GetObject"],
      "Resource": "arn:aws:s3:::MEU_BUCKET/projetos/clienteA/*"
    }
  ]
}
```

---
#### Aplicar, inspecionar e remover a policy (AWS CLI)

Salvar a política em `policy.json` e aplicar:

```bash
aws s3api put-bucket-policy \
  --bucket "$BUCKET" \
  --policy file://policy.json
```

Ler a política aplicada:

```bash
aws s3api get-bucket-policy \
  --bucket "$BUCKET"
```

Remover a política do bucket:

```bash
aws s3api delete-bucket-policy \
  --bucket "$BUCKET"
```