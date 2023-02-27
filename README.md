# Manager App BackEnd

## Prerequisites

- MongoDB: 
1) Register on mongodb.com and create Shared Claster. 
2) Create a user for database (userName & password)
3) IP Access List - add 0.0.0.0
4) Click "Connect" button and select "Connetct your application"
5) Copy claster info from connection string. By default - "claster0.xxxxxx"

## Downloading

```bash
git clone {repository URL}
cd back-manager-app
```

## Connect to your DataBase on MongoDB
- open src/index.ts. In mongoose.connect method replace ${} by userName, Password and ClasterInfo from Prerequisites

## Run Xeroku
Run commands

```bash
git switch source
heroku create --region eu
heroku git:remote -a <YOUR_APP_NAME>
git push heroku source:master
```

# REST service docs

## Endpoints:

- `Swagger Docs` (`/api-docs` route)
- `Auth` (`auth/` route)
  - `POST /signup` - new user registration
  - `POST /signin` - user authorization
- `User` (`users/` route)

  - `GET /` - get all users
  - `GET /:userId` - get the user by id (ex. “/users/123”)
  - `PUT /:userId` - update user
  - `DELETE /:userId` - delete user

- `Board` (`/boards` route)

  - `GET /` - get all boards
  - `GET /:boardId` - get the board by id
  - `POST /` - create board
  - `PUT /:boardId` - update board
  - `DELETE /:boardId` - delete board
- `Boards` (`/boardsSet` route)
  - `GET /` - Get Boards by list of boardId
  - `GET /:userId` - Get Boards where user is owner or one of invited

- `Column` (`boards/:boardId/columns` route)

  - `GET /` - get all columns
  - `GET /:columnsId` - get the column by id
  - `POST /:columnsId` - create column
  - `PUT /:columnsId` - update column
  - `DELETE /:columnsId` - delete column
- `Columns` (`/columnsSet` route)
  - `GET /` - Get Columns by list of columnId or in Boards where user is owner or one of invited
  - `PATCH /` - Change oreder of list of columns
  - `POST /` - Create set of Columns


* `Task` (`boards/:boardId/columns/:columnsId/tasks` route)

  - `GET /` - get all tasks
  - `GET /:taskId` - get the task by id
  - `POST /` - create task
  - `PUT /:taskId` - update task
  - `DELETE /:taskId` - delete task
  - 
- `Tasks` (`/tasksSet` route)
  - `GET /` - Get Tasks by list of taskId or in Boards where user is owner or one of invited, or by search request
  - `PATCH /` - Change oreder and column of list of tasks
  - `GET /:boardId` - Get Tasks in selected Board

* `File` (`file/` route)
  - `GET /` - Get Files by list of taskId or in Boards where user is owner or one of invited, or by TaskId
  - `POST /` - upload file `multipart/form-data`
  - `GET /:boardId` - Get files by BoardId
  - `DELETE /:fileId` - delete file
  - 
* `Point` (`points/` route)
  - `GET /` - Get Points by list of pointId or in Boards where user is owner or one of invited
  - `POST /` - Create a new point
  - `GET /:taskId` - Get Points by TaskId
  - `PACTH /:pointId` - Change title and done
  - `PACTH /` - Change done filed in set of points
  - `DELETE /:pointId` - delete point

# Advanced

## Socket Events
- events: "users", "boards", "columns", "tasks", "files", "points"
Listen events on backend deploy main route (soket = io('https://xxx.herokuapp.com'))
- socket payload: 
```bash
{
  action: 'add' | 'update' | 'delete' // Тип изменения в базе
  users: string[] // Список id юзеров, которые имеют доступ к данным об обновлении чего-то в базе(Например, при изменении колонки здесь будет список из владельца доски и приглашенных на нее пользователей)
  ids: string[] // Список id созданных/измененных/удаленных записей в базе
  guid: string // Уникальный код запроса (Присваивается в хэддере Guid запроса на бэкенд)
  notify: boolean // Нужно ли уведомлять текущего пользователя об изменениях в базе
  initUser: string // id пользователя, инициировавшего изменения в базе (Присваивается в хэддере initUser запроса на бэкенд) 
}
```