# API de Gerenciamento de Receitas Culinárias

## 1. Configuração Inicial do Projeto

```bash
mkdir receitas-api
cd receitas-api
npm init -y
```

Dependências:

```bash
npm install express mongoose dotenv typescript ts-node @types/express @types/node
npm install --save-dev nodemon @types/mongoose
```

## 2. Configuração do TypeScript

Crie um arquivo `tsconfig.json` na raiz do projeto:

```json
{
  "compilerOptions": {
    "target": "es6",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"]
}
```

## 3. Estrutura de Pastas

Crie a seguinte estrutura de pastas:

```
receitas-api/
  ├── src/
  │   ├── models/
  │   ├── routes/
  │   ├── controllers/
  │   └── app.ts
  ├── package.json
  ├── tsconfig.json
  └── .env
```

## 4. Configuração do Ambiente

Crie um arquivo `.env` na raiz do projeto:

```plaintext
MONGODB_URI=mongodb://localhost:27017/receitas-db
PORT=3000
```

## 5. Criando o Modelo

Crie um arquivo `src/models/Recipe.ts`:

```typescript
import mongoose, { Document } from 'mongoose';

interface IRecipe extends Document {
  title: string;
  ingredients: string[];
  instructions: string;
  prepTime: number;
  difficulty: 'Fácil' | 'Médio' | 'Difícil';
}

const RecipeSchema = new mongoose.Schema({
  title: { type: String, required: true },
  ingredients: { type: [String], required: true },
  instructions: { type: String, required: true },
  prepTime: { type: Number, required: true }, // tempo em minutos
  difficulty: { 
    type: String, 
    enum: ['Fácil', 'Médio', 'Difícil'],
    required: true 
  }
});

export default mongoose.model<IRecipe>('Recipe', RecipeSchema);
```

## 6. Criando o Controller

Crie um arquivo `src/controllers/recipeController.ts`:

```typescript
import { Request, Response } from 'express';
import Recipe from '../models/Recipe';

export const recipeController = {
  // Criar uma nova receita
  async create(req: Request, res: Response) {
    try {
      const recipe = await Recipe.create(req.body);
      return res.status(201).json(recipe);
    } catch (error) {
      return res.status(400).json({ error: 'Erro ao criar receita' });
    }
  },

  // Listar todas as receitas
  async list(req: Request, res: Response) {
    try {
      const recipes = await Recipe.find();
      return res.json(recipes);
    } catch (error) {
      return res.status(400).json({ error: 'Erro ao listar receitas' });
    }
  },

  // Buscar uma receita específica
  async getById(req: Request, res: Response) {
    try {
      const recipe = await Recipe.findById(req.params.id);
      if (!recipe) {
        return res.status(404).json({ error: 'Receita não encontrada' });
      }
      return res.json(recipe);
    } catch (error) {
      return res.status(400).json({ error: 'Erro ao buscar receita' });
    }
  }
};
```

## 7. Criando as Rotas

Crie um arquivo `src/routes/recipeRoutes.ts`:

```typescript
import { Router } from 'express';
import { recipeController } from '../controllers/recipeController';

const router = Router();

router.post('/recipes', recipeController.create);
router.get('/recipes', recipeController.list);
router.get('/recipes/:id', recipeController.getById);

export default router;
```

## 8. Configurando o App Principal

Crie um arquivo `src/app.ts`:

```typescript
import express from 'express';
import mongoose from 'mongoose';
import dotenv from 'dotenv';
import recipeRoutes from './routes/recipeRoutes';

dotenv.config();

const app = express();
app.use(express.json());

// Conectar ao MongoDB
mongoose.connect(process.env.MONGODB_URI!)
  .then(() => console.log('Conectado ao MongoDB'))
  .catch((error) => console.log('Erro ao conectar ao MongoDB:', error));

// Rotas
app.use('/api', recipeRoutes);

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Servidor rodando na porta ${PORT}`);
});
```

## 9. Configurando o package.json

Adicione os seguintes scripts ao seu `package.json`:

```json
{
  "scripts": {
    "start": "node dist/app.js",
    "dev": "nodemon src/app.ts",
    "build": "tsc"
  }
}
```

## 10. Testando a API

Para iniciar o servidor em modo de desenvolvimento:

```bash
npm run dev
```

Você pode testar a API usando o Postman ou curl:

```bash
# Criar uma receita
curl -X POST http://localhost:3000/api/recipes -H "Content-Type: application/json" -d '{
  "title": "Bolo de Chocolate",
  "ingredients": ["farinha", "chocolate", "ovos", "açúcar"],
  "instructions": "Misture tudo e asse por 40 minutos",
  "prepTime": 60,
  "difficulty": "Médio"
}'

# Listar receitas
curl http://localhost:3000/api/recipes
```

## Sugestões de Melhorias para Praticar

1. **Validação Básica**:
   - Adicione validação para garantir que o título tenha pelo menos 3 caracteres
   - Verifique se a lista de ingredientes não está vazia

2. **Funcionalidades Extras**:
   - Adicione uma rota para atualizar receitas (PUT/PATCH)
   - Adicione uma rota para deletar receitas (DELETE)
   - Crie uma busca por título de receita

3. **Organização**:
   - Crie uma pasta `config` para mover as configurações do MongoDB
   - Adicione mensagens de erro mais descritivas

4. **Usabilidade**:
   - Adicione paginação na listagem de receitas
   - Implemente uma rota para filtrar receitas por dificuldade
