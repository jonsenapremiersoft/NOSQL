# Tipos de Bancos de Dados NoSQL

## Introdução aos Bancos NoSQL

### O que é NoSQL?
NoSQL significa "Not Only SQL" (Não Apenas SQL) e representa uma categoria de bancos de dados que oferecem mecanismos de armazenamento e recuperação de dados diferentes dos bancos relacionais tradicionais. Surgiram para resolver desafios específicos que os bancos SQL tradicionais não conseguiam abordar eficientemente, como:

- Escalabilidade horizontal
- Processamento de grandes volumes de dados não estruturados
- Flexibilidade no schema (esquema)
- Alta disponibilidade
- Melhor performance em determinados casos de uso

### Por que usar NoSQL?
- **Flexibilidade**: Não exige um esquema rígido como bancos SQL
- **Escalabilidade**: Mais fácil de escalar horizontalmente (adicionar mais máquinas)
- **Performance**: Podem ser muito mais rápidos para determinados tipos de operações
- **Disponibilidade**: Muitos são projetados para alta disponibilidade e tolerância a falhas

## 1. Document Stores (Bancos Orientados a Documentos)

### Conceito Básico
Document Stores armazenam dados em documentos semelhantes a JSON. Cada documento é uma unidade independente que encapsula todos os dados relacionados.

### MongoDB

#### Características Principais:
- **Documentos BSON**: Formato binário do JSON com tipos adicionais
- **Consultas Poderosas**: Suporta consultas complexas, agregações e índices
- **Esquema Dinâmico**: Documentos na mesma coleção podem ter estruturas diferentes
- **Sharding Nativo**: Distribuição automática de dados entre servidores

#### Exemplo Detalhado:
```javascript
// Coleção: usuarios
{
    "_id": ObjectId("5f7d3b2e1c9d440000f1c2d3"),
    "nome": "João Silva",
    "email": "joao@email.com",
    "perfil": {
        "idade": 30,
        "profissao": "Desenvolvedor",
        "habilidades": ["JavaScript", "Python", "MongoDB"]
    },
    "enderecos": [
        {
            "tipo": "residencial",
            "rua": "Av Principal",
            "numero": 123,
            "cidade": "São Paulo",
            "estado": "SP"
        },
        {
            "tipo": "comercial",
            "rua": "Rua do Comércio",
            "numero": 456,
            "cidade": "São Paulo",
            "estado": "SP"
        }
    ],
    "dataCadastro": ISODate("2023-01-15T10:00:00Z")
}
```

#### Operações Comuns:
```javascript
// Inserir documento
db.usuarios.insertOne({
    nome: "Maria Santos",
    email: "maria@email.com",
    perfil: { idade: 25 }
});

// Consulta com filtros
db.usuarios.find({
    "perfil.idade": { $gte: 25 },
    "enderecos.cidade": "São Paulo"
});

// Atualização
db.usuarios.updateOne(
    { email: "joao@email.com" },
    { $set: { "perfil.idade": 31 }}
);
```

### CouchDB

#### Características Principais:
- **API REST**: Todas as operações são feitas via HTTP
- **Consistência Eventual**: Modelo de consistência que prioriza disponibilidade
- **Replicação Bidirecional**: Sincronização automática entre instâncias
- **Revisões de Documentos**: Mantém histórico de alterações

#### Exemplo de Documento:
```json
{
    "_id": "user123",
    "_rev": "1-a23b45c",
    "tipo": "usuario",
    "nome": "Ana Pereira",
    "email": "ana@email.com",
    "preferencias": {
        "notificacoes": true,
        "tema": "escuro"
    }
}
```

### Firestore

#### Características Principais:
- **Estrutura Hierárquica**: Organização em coleções e subcoleções
- **Tempo Real**: Atualizações instantâneas para clientes conectados
- **Segurança Granular**: Regras de segurança por documento
- **Offline First**: Funciona sem conexão e sincroniza depois

#### Exemplo de Estrutura:
```javascript
// Coleção: usuarios
usuarios/{userId} {
    nome: "Pedro Costa",
    email: "pedro@email.com"
}

// Subcoleção: pedidos
usuarios/{userId}/pedidos/{pedidoId} {
    data: timestamp,
    valor: 199.99,
    itens: [...]
}
```

## 2. Key-Value Stores (Bancos Chave-Valor)

### Conceito Básico
São os mais simples dos NoSQL. Funcionam como um grande dicionário/hash map distribuído, onde cada valor é acessado através de uma chave única.

### Redis

#### Características Principais:
- **In-Memory**: Dados primariamente em memória RAM
- **Estruturas de Dados**: Suporta strings, hashes, lists, sets, sorted sets
- **Pub/Sub**: Sistema de mensageria integrado
- **Cache**: Excelente como cache distribuído

#### Exemplos de Uso:
```bash
# Operações básicas
SET usuario:1000 "João Silva"
GET usuario:1000

# Trabalhando com hashes
HSET usuario:1000 nome "João Silva" email "joao@email.com" idade "30"
HGET usuario:1000 email

# Listas
LPUSH ultimos_acessos "usuario:1000"
LRANGE ultimos_acessos 0 -1

# Sets
SADD tags:usuario:1000 "premium" "ativo" "verificado"
SMEMBERS tags:usuario:1000

# Sorted Sets
ZADD ranking_pontos 1000 "usuario:1000"
ZRANK ranking_pontos "usuario:1000"
```

### DynamoDB

#### Características Principais:
- **Serverless**: Totalmente gerenciado pela AWS
- **Escalabilidade Automática**: Ajusta capacidade conforme demanda
- **Consistência Configurável**: Oferece consistência eventual ou forte
- **Índices Secundários**: Suporte a múltiplos padrões de acesso

#### Exemplo de Tabela:
```javascript
// Tabela: Usuarios
{
    "UserId": "123", // Partition Key
    "Email": "jose@email.com", // Sort Key
    "Nome": "José Santos",
    "Status": "ativo",
    "UltimoAcesso": "2024-01-15T14:30:00Z"
}
```

### Firebase Realtime Database

#### Características Principais:
- **JSON Tree**: Todos os dados são armazenados em uma árvore JSON
- **Sincronização em Tempo Real**: Atualizações instantâneas
- **Offline Persistence**: Cache local automático
- **Segurança Declarativa**: Regras de segurança em JSON

#### Exemplo de Estrutura:
```json
{
    "usuarios": {
        "u1": {
            "nome": "Carlos Lima",
            "status": "online",
            "amigos": {
                "u2": true,
                "u3": true
            }
        }
    },
    "mensagens": {
        "m1": {
            "texto": "Olá!",
            "autor": "u1",
            "timestamp": 1642234567890
        }
    }
}
```

## 3. Column-Family Stores (Bancos Orientados a Colunas)

### Cassandra

#### Conceito Básico
Projetado para lidar com grandes volumes de dados distribuídos em vários servidores. Os dados são organizados em famílias de colunas, que são grupos de dados relacionados.

#### Características Principais:
- **Distribuído**: Arquitetura peer-to-peer sem ponto único de falha
- **Alta Disponibilidade**: Replicação configurável entre nós
- **Escalabilidade Linear**: Performance aumenta linearmente com novos nós
- **Modelo de Dados Único**: Baseado em famílias de colunas

#### Modelo de Dados:
```sql
-- Keyspace (similar a um banco de dados)
CREATE KEYSPACE ecommerce WITH replication = {
    'class': 'SimpleStrategy',
    'replication_factor': 3
};

-- Família de Colunas (similar a uma tabela)
CREATE TABLE ecommerce.produtos (
    produto_id uuid PRIMARY KEY,
    nome text,
    preco decimal,
    categoria text,
    estoque map<text, int>
);

-- Inserção de Dados
INSERT INTO ecommerce.produtos (
    produto_id,
    nome,
    preco,
    categoria,
    estoque
) VALUES (
    uuid(),
    'Smartphone X',
    999.99,
    'Eletrônicos',
    {'SP': 100, 'RJ': 50}
);
```

## 4. Graph Databases (Bancos de Dados em Grafo)

### Neo4j

#### Conceito Básico
Especializados em gerenciar dados altamente conectados, usando estruturas de grafos com nós (entidades) e relacionamentos (arestas).

#### Características Principais:
- **Modelo de Grafo Nativo**: Otimizado para percorrer relacionamentos
- **Cypher Query Language**: Linguagem declarativa para consultas em grafos
- **ACID Compliance**: Transações totalmente ACID
- **Visualização Integrada**: Ferramentas visuais para explorar dados

#### Exemplos de Queries:
```cypher
// Criar usuários e relacionamentos
CREATE (joao:Usuario {nome: 'João'})
CREATE (maria:Usuario {nome: 'Maria'})
CREATE (pedro:Usuario {nome: 'Pedro'})
CREATE (joao)-[:AMIGO]->(maria)
CREATE (maria)-[:AMIGO]->(pedro)

// Encontrar amigos de amigos
MATCH (usuario:Usuario {nome: 'João'})-[:AMIGO]->(:Usuario)-[:AMIGO]->(amigoDeAmigo:Usuario)
RETURN amigoDeAmigo.nome

// Criar uma rede social simples
CREATE 
    (post1:Post {conteudo: 'Hello World'}),
    (joao)-[:PUBLICOU]->(post1),
    (maria)-[:CURTIU]->(post1),
    (pedro)-[:COMENTOU {texto: 'Legal!'}]->(post1)
```

## Comparação e Casos de Uso

### Document Stores
- **Melhor para**:
  - Catálogos de produtos
  - Blogs e CMS
  - Análise de dados em tempo real
  - Aplicações com dados hierárquicos
- **Desafios**:
  - Consistência em operações complexas
  - Consultas envolvendo múltiplos documentos

### Key-Value Stores
- **Melhor para**:
  - Cache distribuído
  - Sessões de usuário
  - Configurações em tempo real
  - Filas de mensagens
- **Desafios**:
  - Consultas por valor
  - Relacionamentos entre dados
  - Agregações complexas

### Column-Family Stores
- **Melhor para**:
  - Big Data
  - Séries temporais
  - Sistemas de recomendação
  - Logs e eventos
- **Desafios**:
  - Modelagem de dados complexa
  - Alterações no modelo de dados
  - Consultas ad-hoc

### Graph Databases
- **Melhor para**:
  - Redes sociais
  - Sistemas de recomendação
  - Detecção de fraude
  - Análise de impacto
- **Desafios**:
  - Escalabilidade horizontal
  - Particionamento de dados
  - Complexidade de queries em grandes grafos

## Critérios de Escolha

### Performance
- **Leitura**: Redis > MongoDB > Cassandra > Neo4j
- **Escrita**: Cassandra > MongoDB > Redis > Neo4j
- **Consultas Complexas**: Neo4j > MongoDB > Cassandra > Redis

### Escalabilidade
- **Horizontal**: Cassandra > MongoDB > Redis > Neo4j
- **Vertical**: Redis > MongoDB > Neo4j > Cassandra

### Flexibilidade
- **Schema**: MongoDB > Neo4j > Cassandra > Redis
- **Consultas**: Neo4j > MongoDB > Cassandra > Redis
- **Tipos de Dados**: Redis > MongoDB > Neo4j > Cassandra

### Consistência
- **ACID**: Neo4j > MongoDB > Redis > Cassandra
- **Eventual**: Cassandra > MongoDB > Redis > Neo4j

## Melhores Práticas

### Document Stores (MongoDB, CouchDB, Firestore)
1. Modelagem de Dados
   - Favoreça documentos denormalizados
   - Limite o tamanho dos documentos
   - Use índices apropriadamente
   - Planeje a estrutura pensando nos padrões de acesso

2. Performance
   - Monitore o tamanho dos documentos
   - Use índices compostos para queries frequentes
   - Evite arrays muito grandes
   - Implement paginação adequadamente

### Key-Value Stores (Redis, DynamoDB)
1. Design de Chaves
   - Use prefixos para organização
   - Mantenha chaves concisas mas descritivas
   - Planeje a expiração de dados

2. Otimização
   - Use estruturas de dados apropriadas
   - Implemente cache adequadamente
   - Monitore uso de memória
   - Configure persistência conforme necessidade

### Column-Family Stores (Cassandra)
1. Modelagem
   - Denormalize baseado em queries
   - Planeje particionamento adequadamente
   - Use compactação apropriada
   - Defina TTL quando aplicável

2. Performance
   - Evite partições muito grandes
   - Use batch statements com cautela
   - Monitore compactação
   - Implemente tombstones adequadamente

### Graph Databases (Neo4j)
1. Modelagem
   - Use labels eficientemente
   - Planeje propriedades dos relacionamentos
   - Crie índices apropriados
   - Mantenha relacionamentos relevantes

2. Queries
   - Otimize traversals
   - Use EXPLAIN/PROFILE
   - Limite profundidade de busca
   - Use parâmetros em vez de valores hardcoded

## Conclusão

A escolha do banco de dados NoSQL ideal depende de diversos fatores:

1. **Natureza dos Dados**
   - Estrutura
   - Volume
   - Volatilidade
   - Relacionamentos

2. **Requisitos da Aplicação**
   - Padrões de acesso
   - Escalabilidade necessária
   - Consistência requerida
   - Performance esperada

3. **Recursos Disponíveis**
   - Expertise da equipe
   - Infraestrutura
   - Orçamento
   - Tempo de desenvolvimento

4. **Considerações Operacionais**
   - Manutenção
   - Monitoramento
   - Backup e recuperação
   - Segurança
