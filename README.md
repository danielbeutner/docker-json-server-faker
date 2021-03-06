# docker-json-server-faker

[JSON Server](https://github.com/typicode/json-server) provides REST API mocking based on plain JSON.
This is a [docker](https://www.docker.io) image with included **[faker](https://www.npmjs.com/package/Faker)** and 
**[lodash](https://www.npmjs.com/package/lodash)** npm packages that eases programmatically data creation with JS file.

Idea for Dockerfile came from **[clue](https://github.com/clue/docker-json-server)** and  **[stephencoe](https://github.com/stephencoe/docker-json-server)**

Image size is 135.4 MB.

## Usage

This docker image is available as automated build at (https://index.docker.io/u/docker pull rivancic/docker-json-server-faker/), so there's no setup required.
Using this image for the first time will start a download automatically.
Further runs will be immediate, as the image will be cached locally.

The recommended way to run this container looks like this:

```bash
$ docker run -d -p 80:80 -v /home/user/articles.json:/data/db.json rivancic/docker-json-server-faker
```

The above example exposes the JSON Server REST API on port 80, so that you can now browse to:

```
http://localhost/
```

This is a rather common setup following docker's conventions:

* `-d` will run a detached instance in the background
* `-p {OutsidePort}:80` will bind the webserver to the given outside port
* `-v {AbsolutePathToJsonFile}:/data/db.json` should be passed to mount the given JSON file into the container
* `clue/json-server` the name of this docker image

### Help

You can supply any number of JSON Server arguments that will be passed through unmodified.

```bash
$ docker run -it --rm rivancic/docker-json-server-faker --help
```

### JS seed file

If you mount a file to `/data/file.js`,
it will automatically be used as a JS seed file.

JSON Server expects JS files to export a function that returns an object.
Seed files are useful if you need to programmaticaly create a lot of data.

A sample file could look like this:

```javascript
module.exports = function() {
  var data = {};

  data.posts = [];
  data.posts.push({ id: 1, body: 'foo' });
  //...

  return data;
}
```

And example of JS seed file that uses faker and lodash modules for generating massive amounts of fake contextual data.

```javascript
module.exports = function() {
    var faker = require("faker");
    var _ = require("lodash");
    return {

        categories: _.times(6, function(n) {
            return {
                image: faker.random.image(),
                short_description: faker.lorem.sentence(),
                category_ID: faker.random.uuid()
            };

        }),

        sub_categories: _.times(100, function(n) {
            return {
                image: faker.random.image(),
                short_description: faker.lorem.sentence(),
                category_ID: faker.random.uuid()
            };

        }),

        search: _.times(100, function(n) {
                return {
                    image: faker.random.image(),
                    short_description: faker.lorem.sentence(),
                    gtin13: faker.random.uuid(),
                    price: faker.commerce.price()

                };

            })
            //, ...
    };
};
```

### JSON source

If you mount a file to `/data/db.json` (as in the above example),
it will automatically be used as the plain JSON data source file.

A sample file could look like this:

```json
{
  "posts": [
    { "id": 1, "body": "foo" },
    { "id": 2, "body": "bar" }
  ],
  "comments": [
    { "id": 1, "body": "baz", "postId": 1 },
    { "id": 2, "body": "qux", "postId": 2 }
  ]
}
```


