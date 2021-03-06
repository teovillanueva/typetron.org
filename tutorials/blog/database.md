---
layout: tutorial
title: Database
---

## {{ page.title }}

The next thing we have to do is to show our visitors a list of articles. But we need to store that data somewhere.
Right now, since Typetron is not event in version 1, it offers support only for SQLite databases (file based database).
But don't worry. Typetron will support most of the popular SQL and NoSQL (MySQL, PostgreSQL, Mongodb, DynamoDB etc) 
databases before version 1 release. For now, let's just work with SQLite and get some features up and running.
 
Open the _database.sqlite_ file found in the root of the project in your favorite SQL editor. You can try 
[DB Browser](https://sqlitebrowser.org/) which is free. Intellij IDEA and PHPStorm have a database module built-in 
that you can use. For other Intellij products, like Webstorm, you can install the Database Navigator plugin. 
Let's create the _articles_ table to store our data:
```sql
create table articles
(
	id integer constraint article_pk primary key autoincrement,
	title varchar(128) not null,
	content text,
	createdAt datetime not null,
	updatedAt datetime not null
);
```

Also, fill the tables with some data:
```sql
INSERT INTO articles (title, content, createdAt, updatedAt) VALUES 
    ('Ultimate Crispy "Chicken" Sandwich', 'Content here', CURRENT_TIMESTAMP, CURRENT_TIMESTAMP),
    ('Chunky Monkey Smoothie Bowls', 'Content here too', CURRENT_TIMESTAMP, CURRENT_TIMESTAMP),
    ('Chicken chunks with green curry', 'Content here more', CURRENT_TIMESTAMP, CURRENT_TIMESTAMP);
``` 

#### Creating the `Article` entity

Now, since we have our database ready, we can go back to our TypetronBlog app and write some code. First of all,
we need to create the entities that we will work with. An [Entity](/docs/orm) is a special class that resembles an entry in
our database. In this case we have the _articles_ table, so we need to create the _Article_ entity by creating an
_Article.ts_ file inside Entities folder and write this:  
```file-path
📁 Entity/Article.ts
```
```ts
import { Column, Entity, ID, Meta } from '@Typetron/Database';

@Meta({
    table: 'articles'
})
export class Article extends Entity {
    @Column()
    id: ID;
    
    @Column()
    title: string;
    
    @Column()
    content: string;
}  
```

Let's also add timestamp fields to know when the article was created or updated:
```file-path
📁 Entity/Article.ts
```
```ts
import { Column, Entity, ID, Meta, CreatedAt, UpdatedAt } from '@Typetron/Database';

@Meta({
    table: 'articles',
})
export class Article extends Entity {
    @Column()
    id: ID;

    @Column()
    title: string;

    @Column()
    content: string;

    @CreatedAt()
    createdAt: Date;

    @UpdatedAt()
    updatedAt: Date;
}
```

The _createdAt_ and _updatedAt_ fields will be automatically populated with their respective values when an Entity
is created or updated because they were annotated with the _@CreatedAt()_ and the _@UpdatedAt()_ decorators.

Let's add a method inside _HomeController_ that will show all the articles from the database. Remove the _welcome_ 
method (which is just a dummy method) and add this: 

```file-path
📁 Controllers/Http/HomeController.ts
```
```ts
import { Controller, Get } from '@Typetron/Router';
import { Article } from 'App/Entities/Article';

@Controller()
export class HomeController {

    @Get()
    index() {
        return Article.get();
    }
}
```

The _@Get()_ decorator will register a route inside Typetron that will respond to HTTP GET requests.

The _Article.get()_ will go inside the database and select everything from the _articles_ table and return the
result to the user. This is similar with making a SQL select like this:`SELECT * from articles`. Now, if we go inside
the browser we should see the articles in a JSON format.
 

<div class="tutorial-next-page">
    In the next part we will add the basic actions to create, update and delete articles.
    
    <a href="crud">
        <h3>Next ></h3>
        Managing articles
    </a>
</div>
