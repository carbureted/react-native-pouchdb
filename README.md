# react-native-pouchdb
Hacky and mostly untested way to run pouchdb in react-native! I haven't encountered any glaring problems during development, but would not use this in a space shuttle app.

First, install [react-native-sqlite-storage](https://github.com/andpor/react-native-sqlite-storage).

Then, install react-native-pouchdb.

`npm install --save react-native-pouchdb`

Then, use it with the websql adapter.

```
var db = new PouchDB('test', {
  adapter: 'websql'
});
```

Now you have all the normal pouchdb goodness!

```
var db = new PouchDB('test', {
  adapter: 'websql'
});

var remote = 'https://my.remote.couch'
var remoteDB = new PouchDB(remote)

var sync = db.sync(remoteDB, {
  live: true,
  retry: true
}).on('change', function(change) {
  console.log(change, 'change!')
}).on('error', function(error) {
  console.log(error, 'error!')
})

db.info().then(console.log.bind(console));

function showTodos() {
  db.allDocs({include_docs: true, descending: true}, function(err, doc) {
    console.log(doc.rows, doc.rows.length, 'show docs')
  });
}

function addTodo(text) {
  var todo = {
    _id: new Date().toISOString(),
    title: text,
    completed: false
  };
  db.put(todo, function callback(err, result) {
    if (!err) {
      console.log('Successfully posted a todo!', result);
      showTodos()
    }
    else {
      console.log(err, 'err')
    }
  });
}

addTodo("testing todo")

db.changes({
  since: 'now'
}).on('change', function (change) {
  console.log('thats a change')
}).on('error', function (err) {
  console.log('change error?')
});
```
