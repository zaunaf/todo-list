# Tutorial Loopback 4

[![LoopBack](<https://github.com/strongloop/loopback-next/raw/master/docs/site/imgs/branding/Powered-by-LoopBack-Badge-(blue)-@2x.png>)](http://loopback.io/)

## Install LB4 CLI

Jika belum, install dulu:
```
npm -i @loopback/cli
```

## Create App

Masuk ke folder project anda, misal `/Project/test/` lalu buat app dengan perintah:
```
lb4 app
```
Maka akan ada daftar pertanyaan, jawab sesuai kebutuhan:
```
? Project name: todo-list
? Project description: A todo list API made with LoopBack 4.
? Project root directory: todo-list
? Application class name: TodoListApplication
? Select features to enable in the project Enable eslint, Enable prettier,
? Select features to enable in the project:
 ◉ Enable eslint: add a linter with pre-configured lint rules
 ◉ Enable prettier: install prettier to format code conforming to rules
 ◉ Enable mocha: install mocha to run tests
 ◉ Enable loopbackBuild: use @loopback/build helpers (e.g. lb-eslint)
 ◉ Enable vscode: add VSCode config files
 ◉ Enable docker: include Dockerfile and .dockerignore
 ◉ Enable repositories: include repository imports and RepositoryMixin
 ◉ Enable services: include service-proxy imports and ServiceMixin
? Yarn is available. Do you prefer to use it by default? Yes
```

## File Structure

Buka folder `/Project/test/` perhatikan struktur file yang di-generate.
Perhatikan bahwa file2 source utama berektensi .ts, yaitu typescript. Versi stronged type dari javascript.

File-file dan folder penting yang perlu diperhatikan:

| File                | Tujuan                                       |
| ------------------- | -------------------------------------------- |
| src/application.ts  | Application class                            |
| src/index.ts        | Starting point app                           |
| src/sequence.ts     | Tahap2 aksi saat melayani REST req/resp      |
| src/controllers/\*  | Controller yg melayani request               |
| src/datasources/\*  | Code config untuk satu atau lebih datasource |
| src/models/\*       | Code config model basis data yang dilayani   |
| src/repositories/\* | Code config repository utk tiap model        |
| src/**tests**/      | Berisi code utk tests                        |

Perhatikan bahwa dalam beberapa folder ada README.md-nya masing-masing, yang
berisi info mengenai fungsi tiap folder.

## Membuat Model

Model adalah representasi obyek suatu jenis data, berfungsi juga sebagai DTO (Data Transfer Object).

Mari kita buat model untuk To Do.
```
lb4 model
```
Jawab satu persatu pertanyaan dengan jawaban berikut:
```
? Model class name: todo
? Please select the model base class: Entity (A persisted model with an ID)
? Allow additional (free-form) properties? No

| column     | type   | is id?/automatic? | required?  | desc                                                            |
| -----------|--------|-------------------|------------|-----------------------------------------------------------------|
| id         | number | yes/no            | no         | a unique id                                                     |
| title      | string | no                | yes        | a title                                                         |
| desc       | string | no                | no         | a description that details the specific task to be accomplished |
| isComplete | string | no                | no         | a boolean flag for whether or not we’ve completed the task      |
```

## Membuat Datasource

Datasource adalah konfigurasi koneksi dengan berbagai sumber data.

### Generate Datasource

Mari kita buat datasources untuk To Do, kali ini kita pakai In-memory db,
dan untuk presistencenya kita gunakan file `db.json`. Pertama kita buat dulu
datasourcenya:
```
lb4 datasource
```
Isi pertanyaannya sbb:
```
? Datasource name: db
? Select the connector for db: In-memory db (supported by StrongLoop)
? window.localStorage key to use for persistence (browser only):
? Full path to file for persistence (server only): ./data/db.json

create src/datasources/db.datasource.ts
update src/datasources/index.ts

Datasource Db was created in src/datasources/
```

Buka file `src/datasources/db.datasource.ts`, coba dipahami isinya.

### Sample Data

Kemudian buat folder `db` di root application, tambahkan db.json berisikan contoh berikut:
```json
{
  "ids": {
    "Todo": 5
  },
  "models": {
    "Todo": {
      "1": "{\"title\":\"Take over the galaxy\",\"desc\":\"MWAHAHAHAHAHAHAHAHAHAHAHAHAMWAHAHAHAHAHAHAHAHAHAHAHAHA\",\"id\":1}",
      "2": "{\"title\":\"destroy alderaan\",\"desc\":\"Make sure there are no survivors left!\",\"id\":2}",
      "3": "{\"title\":\"play space invaders\",\"desc\":\"Become the very best!\",\"id\":3}",
      "4": "{\"title\":\"crush rebel scum\",\"desc\":\"Every.Last.One.\",\"id\":4}"
    }
  }
}
```

## Membuat Repository

Pada loopback4, tidak seperti versi sebelumnya, dilakukan pemisahan antara
fungsi pelayanan CRUD dengan modelnya. `Repository` mewakili suatu `Service` yang
menyediakan operasi akses data bertipe data.

Kita buat repository untuk todo:
```
lb4 repository
```
Isi jawaban
```
? Please select the datasource: DbDatasource (tekan enter)
? Select the model(s) you want to generate a repository for Todo (jawab *a* untuk all)
? Please select the repository base class DefaultCrudRepository (Legacy juggler bridge)
   create src/repositories/todo.repository.ts
   update src/repositories/index.ts

Repository TodoRepository was created in src/repositories/
```

Buka file `src/repositories/todo.repository.ts`, coba pahami isinya.

## Membuat Controller

Controller bertugas melayani suatu route untuk kemudian menjalankan suatu business logic.
```
lb4 controller
```
Jawab:
```
? Controller class name: todo
Controller Todo will be created in src/controllers/todo.controller.ts

? What kind of controller would you like to generate? REST Controller with CRUD functions
? What is the name of the model to use with this CRUD repository? Todo
? What is the name of your CRUD repository? TodoRepository
? What is the name of ID property? id
? What is the type of your ID? number
? Is the id omitted when creating a new instance? Yes
? What is the base HTTP path name of the CRUD operations? /todos
   create src/controllers/todo.controller.ts
   update src/controllers/index.ts

Controller Todo was created in src/controllers/
```

Buka file `src/controllers/todo.controller.ts`, coba pahami isinya.
Di dalam file tersebut didefinisikan berbagai route termasuk pembedaan
http method yang diterima.

## Mencoba

Sekarang setelah semua code digenerate, kita coba API yang baru kita buat:
```
npm start
```

Kemudian buka http://localhost:3000/explorer/ untuk melihat hasilnya.
Jangan lupa ganti dulu parameters dan filters sebelum execute dengan empty object:
```
{}
```

Atau langsung query ke backend menggunakan postman / browser seperti ini:
http://localhost:3000/todos/
http://localhost:3000/todos/count
http://localhost:3000/todos/1
http://localhost:3000/todos/2
http://localhost:3000/todos/3

## Relation

### Menambah model TodoList

Kita coba relation dengan menambahkan model baru, `TodoList` dgn perintah `lb4 model`:
```
? Model class name: TodoList
? Please select the model base class: Entity (A persisted model with an ID)
? Allow additional (free-form) properties? No

| column     | type   | is id?/automatic? | required?  | desc                                    |
| -----------|--------|-------------------|------------|-----------------------------------------|
| id         | number | yes/no            | no         | a unique id                             |
| title      | string | no                | yes        | a title                                 |
| color      | string | no                | no         | a color to represent the TodoList with  |
```

### Menambah Repository
Setelah itu buatlah repository-nya seperti contoh di atas.
```
lb4 repository
? Please select the datasource DbDatasource
? Select the model(s) you want to generate a repository TodoList
? Please select the repository base class DefaultCrudRepository (Legacy juggler
bridge)
   create src/repositories/todo-list.repository.ts
   update src/repositories/index.ts

Repository TodoListRepository was created in src/repositories/
```

### Menambah model Relation

Nah sekarang kita buat relation dari sisi TodoList. Logikanya setiap TodoList
memiliki satu atau lebih (`hasMany`) Todo:
```
lb4 relation
```

Jawab:
```
? Please select the relation type hasMany
? Please select source model TodoList
? Please select target model Todo
? Foreign key name to define on the target model todoListId
? Source property name for the relation getter (will be the relation name) todos
? Allow TodoList queries to include data from related Todo instances? Yes
   create src/controllers/todo-list-todo.controller.ts

Relation HasMany was created in src/
```

Dan sekarang kita buat sebaliknya, relation dari sisi Todo. Logikanya setiap Todo
itu merupakan bagian dari (`belongsTo`) sebuah TodoList:
```
lb4 relation
```

Jawab:
```
? Please select the relation type belongsTo
? Please select source model Todo
? Please select target model TodoList
? Foreign key name to define on the source model todoListId
? Relation name todoList
? Allow Todo queries to include data from related TodoList instances? Yes
   create src/controllers/todo-todo-list.controller.ts

Relation BelongsTo was created in src/
```

Perhatikan bahwa pada tiap file model akan muncul code relasinya.

### Menambah Data
Ubah `data/data.json` dengan menampilkan record untuk model baru tadi:
```json
{
  "ids": {
    "Todo": 5,
    "TodoList": 3
  },
  "models": {
    "Todo": {
      "1": "{\"title\":\"Take over the galaxy\",\"desc\":\"MWAHAHAHAHAHAHAHAHAHAHAHAHAMWAHAHAHAHAHAHAHAHAHAHAHAHA\",\"todoListId\":1,\"id\":1}",
      "2": "{\"title\":\"destroy alderaan\",\"desc\":\"Make sure there are no survivors left!\",\"todoListId\":1,\"id\":2}",
      "3": "{\"title\":\"play space invaders\",\"desc\":\"Become the very best!\",\"todoListId\":2,\"id\":3}",
      "4": "{\"title\":\"crush rebel scum\",\"desc\":\"Every.Last.One.\",\"todoListId\":1,\"id\":4}"
    },
    "TodoList": {
      "1": "{\"title\":\"Sith lord's check list\",\"lastModified\":\"a long time ago\",\"id\":1}",
      "2": "{\"title\":\"My daily chores\",\"lastModified\":\"2018-07-13\",\"id\":2}"
    }
  }
}
```

Perhatikan bahwa pada tiap todo kita tambahkan foreignKey `todoListId`.

## Membuat Controller
Kemudian buatlah controllernya seperti contoh di atas, `lb4 controller`:
```
? Controller class name: TodoList
Controller TodoList will be created in src/controllers/todo-list.controller.ts

? What kind of controller would you like to generate? REST Controller with CRUD functions
? What is the name of the model to use with this CRUD repository? TodoList
? What is the name of your CRUD repository? TodoListRepository
? What is the name of ID property? id
? What is the type of your ID? number
? Is the id omitted when creating a new instance? Yes
? What is the base HTTP path name of the CRUD operations? /todo-lists
   create src/controllers/todo-list.controller.ts
   update src/controllers/index.ts

Controller TodoList was created in src/controllers/
```

Demikian! Start kembali service dengan `npm start` kemudian amati perubahannya
di localhost:3000/explore.


Catatan: Jika anda ingin menambahkan fungsi yang memudahkan pada repo,
```js
  // Add the following function
  public findByTitle(title: string) {
    return this.findOne({where: {title}});
  }
```
