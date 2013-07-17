# Silly SQL

SQL Query Construction Toolset

Basic usage:

```javascript
var query = sql.query([
  'SELECT {fields} FROM mytable {otherTableJoin} {where}',
    '{sort} {limit}'
]);

query.otherTableJoin = 'INNER JOIN othertable ON othertable.mytableid = mytable.id';

query.fields = sql.fields();
query.fields
  .addSelectMap('mytable', { id:'myid', name:'myName' })
  .addSelectMap('othertable', { id:'otherid', name:'otherName' });

query.where = sql.where('id > 5').and('(created_at < NOW() OR created_at IS NULL)');
query.sort = sql.sort('-id');
query.limit = sql.limit(10, 5);

query.toString();
// => SELECT "mytable"."id" AS "myid", "mytable"."name" AS "myName", "othertable"."id" AS "otherId", "othertable"."name" AS "othername"
//      FROM mytable
//      INNER JOIN othertable ON othertable.mytableid = mytable.id
//      WHERE id > 5 AND (created_at < NOW() OR created_at IS NULL)
//      ORDER BY id DESC
//      LIMIT 10 OFFSET 5
```

Escaping values:

```javascript
var query = sql.query('UPDATE mytable SET name=$name, age=$age');

query.$('name', 'Alice');
query.$('age', 38);

query.toString();
// => UPDATE mytable SET name=$1, age=$2
query.$values
// => ['Alice', 38]
```

Inserting an object:

```javascript
var query = sql.query('INSERT INTO mytable ({columns}) VALUES {values}');

var myInput = {
  name: 'Bob',
  age: 54
};
query.columns = sql.fields().addObjectKeys(myInput);
query.values = sql.fields().addObjectValues(myInput);

query.toString();
// => INSERT INTO mytable ("name", "age") VALUES $1, $2
query.$values
// ['Bob', 54]
```

### Authors

* [Paul Frazee](https://github.com/pfraze) - Did everything
* [John Fawcett](https://github.com/jrf0110) - Initialized the git repo, named it