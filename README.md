1. `npm i -g @nestjs/cli`;
2. `nest new [project-name]`

В папке src файлы и содержимое:

`main.ts`:

```
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

(async function () {
  const PORT = process.env.PORT || 5000;
  const app = await NestFactory.create(AppModule);

  await app.listen(PORT, () => console.log(`Server launch at port ${PORT}`));
})();

```

`app.module.ts`:

```
import { AppController } from './app.controller';

@Module({
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
{}

```

`app.services.ts`:

```
import { Injectable } from '@nestjs/common';

@Injectable()
export class AppService {
  getUsers() {
    return [{ id: 1, name: 'Test' }];
  }
}

```

`app.controller.ts`:

```
import { Controller, Get } from '@nestjs/common';

@Controller('/api')
export class AppController {
  @Get('./users')
  getUsers() {
    return [{ id: 1, name: 'Test' }];
  }
}

```

### Установка sequelize

`npm install --save @nestjs/sequelize sequelize sequelize-typescript` [ссылка на доки](https://docs.nestjs.com/recipes/sql-sequelize)
`npm install --save pg pg-hstore` [ссылка на доки](https://sequelize.org/master/manual/getting-started.html)
`npm install --save-dev @types/sequelize` [ссылка на доки](https://sequelize.org/master/manual/getting-started.html)

Аналогично добавлению модуля по докам [nest.js](https://docs.nestjs.com/techniques/database) добавляется [модуль с настройками для postgres]

Установка ORM системы `pgAdmin 4`

`nest generate module users` - генерация модуля users в папке users
`nest generate service users` - генерация сервиса
`nest generate controller users` - генерация контроллера

Конфигурация БД в импортах `app.module.ts` следует вынести отдельно. Нужен пакет `npm i --save @nestjs/config`

а в файл .env можно вынести конфигурацию:

```
PORT=4041
POSTGRES_HOST=localhost
POSTGRES_USER=POSTGRES_HOS
POSTGRES_DB=test-db
POSTGRESS_PASSWORD=root
POSTGRESS_PORT=5432
```

Теперь в файле `app.module.ts`

```
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { SequelizeModule } from '@nestjs/sequelize';
import { UsersModule } from './users/users.module';
import { ConfigModule } from '@nestjs/config';

@Module({
  controllers: [AppController],
  providers: [AppService],
  imports: [
    ConfigModule.forRoot({
      envFilePath: '.env',
    }),
    SequelizeModule.forRoot({
      dialect: 'postgres',
      host: process.env.POSTGRES_HOST,
      port: Number(process.env.POSTGRESS_PORT),
      username: process.env.POSTGRES_USER,
      password: process.env.POSTGRES_PASSWORD,
      database: process.env.POSTGRES_DB_NAME,
      models: [],
      autoLoadModels: true,
    }),
    UsersModule,
  ],
})
export class AppModule {}
```

`npm i cross-env`
