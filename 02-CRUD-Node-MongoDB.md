# MongoDB na Prática com NodeJS e ExpressJS

## 1. Instalação do MongoDB

### Windows
1. Acesse o site oficial do MongoDB (mongodb.com) e baixe o MongoDB Community Server
2. Execute o instalador e siga o assistente de instalação
3. Marque a opção "Install MongoDB as a Service"
4. O MongoDB será instalado por padrão em C:\Program Files\MongoDB
5. O serviço será iniciado automaticamente após a instalação

### Linux (Ubuntu)
```bash
# Importe a chave pública do MongoDB
curl -fsSL https://pgp.mongodb.com/server-7.0.asc | sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg --dearmor

# Adicione o repositório do MongoDB
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list

# Atualize os pacotes e instale o MongoDB
sudo apt-get update
sudo apt-get install -y mongodb-org

# Inicie o serviço
sudo systemctl start mongod
```

### macOS
```bash
# Usando Homebrew
brew tap mongodb/brew
brew install mongodb-community
brew services start mongodb-community
```

## 2. MongoDB Compass

1. Baixe o MongoDB Compass do site oficial
2. Instale o programa seguindo o assistente
3. Ao abrir, você verá a tela de conexão
4. Para conectar localmente, use a URI: `mongodb://localhost:27017`
5. Clique em "Connect" para estabelecer a conexão

### Principais recursos do Compass:
- Visualização da estrutura do banco de dados
- Interface para criar e gerenciar collections
- Editor de consultas visual
- Importação e exportação de dados
- Monitoramento de performance

## 3. Conectando com NodeJS e ExpressJS

Primeiro, configure seu projeto Node.js:

```bash
mkdir projeto-mongodb
cd projeto-mongodb
npm init -y
npm install express mongoose dotenv
```

Crie um arquivo `app.js`:

```javascript
const express = require('express');
const mongoose = require('mongoose');
require('dotenv').config();

const app = express();
app.use(express.json());

// Conexão com MongoDB
mongoose.connect('mongodb://localhost:27017/minha-database')
  .then(() => console.log('Conectado ao MongoDB'))
  .catch(err => console.error('Erro na conexão:', err));

// Definindo um modelo
const Usuario = mongoose.model('Usuario', {
  nome: String,
  email: String,
  idade: Number
});

app.listen(3000, () => {
  console.log('Servidor rodando na porta 3000');
});
```

## 4. CRUD Básico

### Create (Criar)
```javascript
// POST /usuarios
app.post('/usuarios', async (req, res) => {
  try {
    const usuario = new Usuario(req.body);
    await usuario.save();
    res.status(201).json(usuario);
  } catch (error) {
    res.status(400).json({ erro: error.message });
  }
});
```

### Read (Ler)
```javascript
// GET /usuarios
app.get('/usuarios', async (req, res) => {
  try {
    const usuarios = await Usuario.find();
    res.json(usuarios);
  } catch (error) {
    res.status(500).json({ erro: error.message });
  }
});

// GET /usuarios/:id
app.get('/usuarios/:id', async (req, res) => {
  try {
    const usuario = await Usuario.findById(req.params.id);
    if (!usuario) return res.status(404).json({ message: 'Usuário não encontrado' });
    res.json(usuario);
  } catch (error) {
    res.status(500).json({ erro: error.message });
  }
});
```

### Update (Atualizar)
```javascript
// PUT /usuarios/:id
app.put('/usuarios/:id', async (req, res) => {
  try {
    const usuario = await Usuario.findByIdAndUpdate(
      req.params.id,
      req.body,
      { new: true }
    );
    if (!usuario) return res.status(404).json({ message: 'Usuário não encontrado' });
    res.json(usuario);
  } catch (error) {
    res.status(500).json({ erro: error.message });
  }
});
```

### Delete (Excluir)
```javascript
// DELETE /usuarios/:id
app.delete('/usuarios/:id', async (req, res) => {
  try {
    const usuario = await Usuario.findByIdAndDelete(req.params.id);
    if (!usuario) return res.status(404).json({ message: 'Usuário não encontrado' });
    res.json({ message: 'Usuário removido com sucesso' });
  } catch (error) {
    res.status(500).json({ erro: error.message });
  }
});
```

## Testando a API

Você pode testar as rotas usando o Postman ou curl:

```bash
# Criar usuário
curl -X POST http://localhost:3000/usuarios \
  -H "Content-Type: application/json" \
  -d '{"nome": "João", "email": "joao@email.com", "idade": 25}'

# Listar usuários
curl http://localhost:3000/usuarios

# Atualizar usuário
curl -X PUT http://localhost:3000/usuarios/[ID] \
  -H "Content-Type: application/json" \
  -d '{"nome": "João Silva"}'

# Deletar usuário
curl -X DELETE http://localhost:3000/usuarios/[ID]
```
