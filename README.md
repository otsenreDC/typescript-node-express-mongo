# typescript-node-express-mongo
Typescript, Node, Express and Mongo starting project.

_______________________________________________USING_______________________________________________
 - NodeJS
 - ExpressJS
 - GulpJS
 - TypeScript

_______________________________________________STEPS_______________________________________________</br>
 Create project's directory > 'mkdir <your-project's-name>'</br>
 Create tsconfig.json > 'touch tsconfig.json'</br>
    TypeScript compiler configuration</br>
 Add a 'src' directory > 'mkdir src'</br>
 Create package.json inside src > 'npm init -y'</br>
 Install TypeScript > 'npm install typescript --save-dev'</br>
 Install gulp > 'npm install gulp --save-dev'</br>
 Install gulp-typescript > 'npm install gulp-typescript --save-dev'</br>
 Add gulpfile.js to the root directory > 'touch gulpfile.js'</br>
 Install ExpressJS > 'npm install express --save'</br>
 Install Debug > 'npm install debug --save'</br>
 Install types NodeJS > 'npm install @types/node --save-dev'</br>
 Install types ExpressJS > 'npm install @types/express --save-dev'</br>
 Install types Debug > 'npm install @types/debug --save-dev'</br>
 Create index file within source directory > 'touch index.js'</br>
 Add start script to package.json > "scripts": {</br>
  "start": "node dist/index.js",</br>
  "test": "mocha --reporter spec --compilers ts:ts-node/register 'test/**/*.test.ts'"</br>
}</br>
 Create App.js within src file > 'touch App.js'</br>
 Install Body Parser > 'npm install body-parser --save'</br>
 Install Morgan > 'npm install morgan --save'</br>
 Install types Body Parser > 'npm install @types/body-parser --save-dev'</br>
 Install types Morgan > 'npm install @types/morgan --save-dev'</br>
 Gulp scripts</br>
 npm start</br>
 Install mocha > 'npm install mocha --save-dev'</br>
 Install chai > 'npm install chai --save-dev'</br>
 Install chai-http > 'npm install chai-http --save-dev'</br>
 Install types mocha > 'npm install @types/mocha --save-dev'</br>
 Install types chai > 'npm install @types/chai --save-dev'</br>
 Install types chai-http > 'npm install @types/chai-http --save-dev'</br>
 Install ts-node > 'npm install ts-node --save-dev'</br>
 Create test folder</br>
 Create file helloWorld.test.ts   </br>
 Run tests > 'npm test'</br>

______________________________________________CONTENT______________________________________________</br>
tsconfig.json</br>
```
{
  "compilerOptions": {
    "target": "es6",
    "module": "commonjs",
    "outDir": "dist"
  },
  "include": [
    "src/**/*.ts"
  ],
  "exclude": [
    "node_modules"
  ]
}
```

gulpfile.js
```
const gulp = require('gulp');
const ts = require('gulp-typescript');
const JSON_FILES = ['src/*.json', 'src/**/*.json'];

// pull in the project TypeScript config
const tsProject = ts.createProject('tsconfig.json');

gulp.task('scripts', () => {
  const tsResult = tsProject.src()
  .pipe(tsProject());
  return tsResult.js.pipe(gulp.dest('dist'));
});

gulp.task('watch', ['scripts'], () => {
  gulp.watch('src/**/*.ts', ['scripts']);
});

gulp.task('assets', function() {
  return gulp.src(JSON_FILES)
  .pipe(gulp.dest('dist'));
});

gulp.task('default', ['watch', 'assets']);
```

index.ts
```
import * as http from 'http';
import * as debug from 'debug';

import App from './App';

debug('ts-express:server');

const port = normalizePort(process.env.PORT || 3000);
App.set('port', port);

const server = http.createServer(App);
server.listen(port);
server.on('error', onError);
server.on('listening', onListening);

function normalizePort(val: number|string): number|string|boolean {
  let port: number = (typeof val === 'string') ? parseInt(val, 10) : val;
  if (isNaN(port)) return val;
  else if (port >= 0) return port;
  else return false;
}

function onError(error: NodeJS.ErrnoException): void {
  if (error.syscall !== 'listen') throw error;
  let bind = (typeof port === 'string') ? 'Pipe ' + port : 'Port ' + port;
  switch(error.code) {
    case 'EACCES':
      console.error(`${bind} requires elevated privileges`);
      process.exit(1);
      break;
    case 'EADDRINUSE':
      console.error(`${bind} is already in use`);
      process.exit(1);
      break;
    default:
      throw error;
  }
}

function onListening(): void {
  let addr = server.address();
  let bind = (typeof addr === 'string') ? `pipe ${addr}` : `port ${addr.port}`;
  debug(`Listening on ${bind}`);
}
```

App.js
```
import * as path from 'path';
import * as express from 'express';
import * as logger from 'morgan';
import * as bodyParser from 'body-parser';

// Creates and configures an ExpressJS web server.
class App {

  // ref to Express instance
  public express: express.Application;

  //Run configuration methods on the Express instance.
  constructor() {
    this.express = express();
    this.middleware();
    this.routes();
  }

  // Configure Express middleware.
  private middleware(): void {
    this.express.use(logger('dev'));
    this.express.use(bodyParser.json());
    this.express.use(bodyParser.urlencoded({ extended: false }));
  }

  // Configure API endpoints.
  private routes(): void {
    /* This is just to get up and running, and to make sure what we've got is
     * working so far. This function will change when we start to add more
     * API endpoints */
    let router = express.Router();
    // placeholder route handler
    router.get('/', (req, res, next) => {
      res.json({
        message: 'Hello World!'
      });
    });
    this.express.use('/', router);
  }

}

export default new App().express;
```

helloWorld.test.ts
```
import * as mocha from 'mocha';
import * as chai from 'chai';
import chaiHttp = require('chai-http');

import app from '../src/App';

chai.use(chaiHttp);
const expect = chai.expect;

describe('baseRoute', () => {

  it('should be json', () => {
    return chai.request(app).get('/')
    .then(res => {
      expect(res.type).to.eql('application/json');
    });
  });

  it('should have a message prop', () => {
    return chai.request(app).get('/')
    .then(res => {
      expect(res.body.message).to.eql('Hello World!');
    });
  });

});
```
