
![image](https://github.com/FeralUnsettler/nestjs-guide/assets/143057938/573f3289-404a-4c3e-9a9f-47d755f52e53)


# NEST JS
## What is Nest Js?
Nest Js is a framework for building server-side applications using Node.js efficiently. It utilizes TypeScript combined with Object-Oriented Programming (OOP), Functional Programming (FP), and Functional Reactive Programming (FRP) features.

## How to create a new project
```bash
npm i -g @nestjs/cli
nest new project-name
```
When created successfully, we will have 3 main files to view:
- `app.controller.ts`: Contains routers to handle requests and return responses to the client.
- `app.module.ts`: Root module of the application.
- `app.service.ts`: Service contains the logic that the controller will use.
- `main.ts`: Use NestFactory to initialize the application.

## Controller
---
![image](https://github.com/FeralUnsettler/nestjs-guide/assets/143057938/34da4ab4-886d-40df-bfd7-33d0583ffb99)


The Controller is responsible for processing requests sent from the client side and returning responses to the client. `@Controller()` is responsible for linking those Controller classes with the corresponding request.

Create 1 controller:
```bash
nest g controller users
```

The decorator in Nest JS helps us make queries on requests as well as process response data returned to the client. 

![image](https://github.com/FeralUnsettler/nestjs-guide/assets/143057938/703beed9-5445-4320-8ec2-b7b0dfbcb725)

In particular, Nest allows binding data sent from requests to help prevent invalid data before processing, which is Data Transfer Object (DTO).

## Module

![image](https://github.com/FeralUnsettler/nestjs-guide/assets/143057938/fd75c037-e3dc-4c2b-bc0e-d96a04a8de7b)

The module is responsible for packaging the related logic of the functions that need to be deployed to the client independently. A module in Nest is a class defined with `@Module()`. `@Module()` will provide the metadata that Nest uses to organize the application structure.

A basic module file will look like this:

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

A module will include the following main components:

- `providers`: Responsible for initializing and providing services that will be used by the controller in the module.
- `controllers`: Responsible for initializing the controllers defined in the module.
- `imports`: Responsible for importing components of another module that the module will use.
- `exports`: Responsible for exporting provider components and other modules to import for use.

## How to create a module

```bash
nest g module users
```

### Share Module
You can share any provider in the current module with other modules. For example, you can share `UserService` for other modules to use by adding it to the `exports` array in `users.module.ts` as follows:

```typescript
import { Module } from '@nestjs/common';
import { UsersController } from './users.controller';
import { UsersService } from './users.service';

@Module({
  controllers: [UsersController],
  providers: [UsersService],
  exports: [UsersService]
})
export class UsersModule {}
```

After exporting, other modules can import `UsersModule` and access `UsersService` for use.

### Global Module
If you don't want to have to import a certain module too many times, Nest provides `@Global()` which allows you to use a module from another module without importing it. Just add `@Global()` to make it a global module.

```typescript
import { Module, Global } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Global()
@Module({
  controllers: [CatsController],
  providers: [CatsService],
  exports: [CatsService],
})
export class CatsModule {}
```

## Providers

![image](https://github.com/FeralUnsettler/nestjs-guide/assets/143057938/fb79cff9-d099-41d0-b7ba-a5ca02058f92)

Provider is the place that provides services, repositories, factories, helpers, etc., for controllers in a module to use. This is also where the processing logic will be stored that is separate from the controller.

To create a provider, declare `@Injectable()` before a defined class. Using `@Injectable()` will tell Nest that this is a class belonging to a provider.

To create a service that contains the processing logic of the UserController, let's create a `UserService` in the `user.service.ts` file below or use:

```bash
nest g service users
```

```typescript
import { Injectable } from '@nestjs/common';
import { User } from './interfaces/user.interface';

@Injectable()
export class UsersService {
  private readonly users: User[] = [];

  create(user: User) {
    this.users.push(user);
  }

  findAll(): User[] {
    return this.users;
  }
}
```

## Middleware

![image](https://github.com/FeralUnsettler/nestjs-guide/assets/143057938/7046fc55-55ef-4b96-94ef-6ef8ca5e7203)

Middleware is a function called before the router handler. Middleware can access the request and response objects and call `next()` to call the next middleware in the application's request-response cycle.

### Deploying Middleware
#### Class

You deploy custom Nest middleware in a function or in a class with the `@Injectable()` decorator. The class should implement the NestMiddleware interface.

```typescript
@Injectable()
export class DemoClassMiddeWare implements NestMiddleware {
  use(req: Request, res: Response, next: NextFunction) {
    const user: User = req.body;
    if (user.age > 15) {
      user.nameMid = 'class middleware';
    }
    next();
  }
}
```

#### Function

You can also use a function to write Middleware:

```typescript
export function loggerMiddleware(
  req: Request,
  res: Response,
  next: NextFunction,
) {
  console.log(req.body);

  const users: User = req.body;
  users.isAdult = false;
  if (users.age > 15) {
    users.isAdult = true;
  }
  console.log(`Call MDW`);
  next();
}
```

### Applying Middleware
#### How to add middleware to a specific request method
```typescript
export class AppModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(loggerMiddleware, DemoClassMiddeWare)
      .forRoutes({ path: 'users', method: RequestMethod.POST });
  }
}
```

#### Middleware Consumer
`MiddlewareConsumer` is a helper class. It provides several middleware integration methods. The `forRoutes()` method can receive a single string, multiple strings, a RouteInfo object, a controller class, and even multiple controller classes. The `apply()` method can take a single middleware or multiple arguments to specify multiple middlewares.

#### Excluding Routes
Sometimes we want to exclude certain routes from having middleware applied. We can easily exclude certain routes using the `exclude()` method.

```typescript
export class AppModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(loggerMiddleware, DemoClassMiddeWare)
      .exclude({
        path: 'users',
        method: RequestMethod.GET,
      })
      .forRoutes(UsersController);
  }
}
```

#### Global Middleware
If we want to associate middleware with every registered route at once, we can use the `use()` method provided by the `INestApplication` entity in `main.ts`:

```typescript
app.use(loggerMiddleware);
```

## Exception Filters
Nest comes with a built-in exception class, which is responsible for handling all unhandled exceptions on an application. When an exception is not handled by your application code, it is caught by this class, which then automatically sends an appropriate user-friendly response.

```json
{
  "statusCode": 500,
  "message": "Internal server error"
}
```

### Throwing Standard Exceptions
Nest provides

 a built-in `HttpException` class, exposed from the `@nestjs/common` package. For typical HTTP REST/GraphQL API-based applications, it is best practice to send standard HTTP response objects when certain error conditions occur.

```typescript
@Get()
async findAll() {
  throw new HttpException('Forbidden', HttpStatus.FORBIDDEN);
}
```

```json
{
  "statusCode": 403,
  "message": "Forbidden"
}
```

To override only the message part of the JSON response body, use:

```typescript
@Get()
async findAll() {
  throw new HttpException({
    status: HttpStatus.FORBIDDEN,
    error: "This is a custom message!!!"
  }, HttpStatus.FORBIDDEN);
}
```

### Custom Exceptions
If you need to create custom exceptions, you should create your own exception hierarchy, where your custom exceptions inherit from the base `HttpException` class.

```typescript
export class ForbiddenExceptionC extends HttpException {
  constructor() {
    super('Custom Exception', HttpStatus.FORBIDDEN);
  }
}

throw new ForbiddenExceptionC();
```

### Built-in HTTP Exceptions
Nest provides a set of standard exceptions that inherit from the base `HttpException`. These are exposed from the `@nestjs/common` package and represent many of the most common HTTP exceptions. [Read more](https://docs.nestjs.com/exception-filters#built-in-http-exceptions)

## Pipe
A Pipe is defined as a class annotated with an `@Injectable()` decorator and implements the `PipeTransform` interface. Pipes are often used for data transformation and validation.

### Transformation
In the example, I use Binding pipes for demos such as `ParseIntPipe`, `ParseFloatPipe`.

```typescript
@Get(':id')
findOne(
  @Param(
    'id',
    ParseIntPipe,
  )
  id: number,
): User {
  const user = this.usersService.findById(id);
  if (!user) {
    throw new NotFoundException();
  }
  return user;
}
```

This ensures that either the parameter received in the `findById()` method is a number (as expected in the call to `this.usersService.findById(id)`) or an exception is thrown before the route handler is called.

### Validation
Check input data and report an error if that data does not satisfy the condition. In the example, I use class-validator to demo global pipes. [Read more](https://github.com/typestack/class-validator)

## Guards

![image](https://github.com/FeralUnsettler/nestjs-guide/assets/143057938/a8c5e559-1ead-4104-9f17-561e854686f8)

A Guard is a class annotated with the `@Injectable()` decorator. Guards must implement the `CanActivate` interface.

Guards have a single responsibility: they determine whether a given request will be handled by the route handler or not, depending on certain conditions present at runtime.

## Interceptors

![image](https://github.com/FeralUnsettler/nestjs-guide/assets/143057938/ce1d6390-0ec2-40dc-918e-2f7f7a1f70ef)

An Interceptor is a class annotated with the `@Injectable()` decorator. Interceptors must implement the `NestInterceptor` interface. Interceptors have a set of useful capabilities inspired by Aspect Oriented Programming (AOP).

## Custom Providers
```
