# Guia de desenvolvimento de apps com NestJS

O NestJS é um framework Node.js progressivo para a construção de aplicações eficientes, confiáveis e escaláveis do lado do servidor. Inspirado pelo Angular, ele utiliza TypeScript e segue os princípios SOLID, tornando seu código modular, testável e de fácil manutenção.

Este guia irá ajudá-lo a dar os primeiros passos com o NestJS e desenvolver seu próprio aplicativo.

## 1. Configuração do Ambiente

- **Node.js:** O NestJS requer Node.js instalado. Baixe e instale a versão LTS (Long Term Support) do [site oficial](https://nodejs.org/).
- **npm ou yarn:** O gerenciador de pacotes npm já vem instalado com o Node.js. Você pode usar yarn como alternativa.
- **IDE:** Escolha a IDE de sua preferência. Visual Studio Code, WebStorm e Atom são opções populares com bom suporte ao TypeScript.

## 2. Criação do Projeto

A maneira mais fácil de iniciar um projeto NestJS é usando o Nest CLI:

```bash
npm i -g @nestjs/cli
nest new nome-do-projeto
```

Isso criará um novo diretório "nome-do-projeto" com a estrutura básica do projeto.

## 3. Estrutura do Projeto

- **src:** Contém o código fonte da sua aplicação.
  - **main.ts:** Ponto de entrada da aplicação.
  - **app.module.ts:** Módulo raiz da aplicação.
  - **app.controller.ts:** Controller básico para gerenciar as rotas.
  - **app.service.ts:** Service básico para lógica de negócio.
- **node_modules:** Contém as dependências do projeto.
- **package.json:** Contém informações sobre o projeto e suas dependências.

## 4. Controllers

Controllers são responsáveis por lidar com as requisições HTTP. Eles utilizam decorators para definir as rotas e os métodos que as manipulam:

```typescript
import { Controller, Get } from '@nestjs/common';

@Controller('users')
export class UsersController {
  @Get()
  findAll(): string {
    return 'This action returns all users';
  }
}
```

## 5. Providers

Providers são usados para implementar a lógica de negócio e injeção de dependência. Eles podem ser services, repositories, factories, etc.:

```typescript
import { Injectable } from '@nestjs/common';

@Injectable()
export class UsersService {
  findAll(): string {
    return 'This action returns all users';
  }
}
```

## 6. Módulos

Módulos organizam a estrutura da aplicação. Eles declaram controllers, providers e outros módulos que fazem parte do escopo:

```typescript
import { Module } from '@nestjs/common';
import { UsersController } from './users.controller';
import { UsersService } from './users.service';

@Module({
  controllers: [UsersController],
  providers: [UsersService],
})
export class UsersModule {}
```

## 7. Execução do Projeto

Para iniciar o servidor de desenvolvimento:

```bash
npm run start:dev
```

A aplicação estará disponível em [http://localhost:3000](http://localhost:3000) por padrão.

## 8. Recursos Adicionais

- Documentação oficial: [https://docs.nestjs.com/](https://docs.nestjs.com/)
- Tutoriais: [https://docs.nestjs.com/first-steps](https://docs.nestjs.com/first-steps)
- Comunidade: [https://discord.gg/nestjs](https://discord.gg/nestjs)

## Dicas

- Use TypeScript para ter uma base de código mais robusta e segura.
- Aproveite o sistema de injeção de dependência para criar código modular e testável.
- Utilize os recursos do Nest CLI para gerar código boilerplate e acelerar o desenvolvimento.
- Explore a documentação e os exemplos disponíveis para conhecer as funcionalidades do framework.

## Conclusão

O NestJS fornece uma base sólida para desenvolver aplicações Node.js de forma eficiente e escalável. Ao seguir este guia, você estará pronto para criar seus próprios projetos e explorar as diversas funcionalidades que o framework oferece.
