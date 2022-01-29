## Ejecting Apollo Server

Apollo는 GraphQL만을 위한 서버이다.  
Rest API를 이용하거나 다른 미들웨어, 라이브러리를 사용하기 위해

서버를 Eject 해야할 때가 있다.  
apollo에서 지원하는 라이브러리로는

Node 기반: apollo-server-express, apollo-server-fastify  
serverless 기반: apollo-server-lambda 등이 있다.

파일 업로드를 위한 apollo server ejecting 예시.

> server.ts

```javascript
// apollo server ejecting 후 express 도입

const app = express();
app.use("/static", express.static("src/uploads"));
```

> fileupload.ts

```javascript

  const { filename, createReadStream } = (await avatar) as Upload;

    /**
     * createReadStream과 createWriteStream을 pipe로 연결시킨다
     */
    const readStream = createReadStream();
    const newFileName = `${loggedInUser.id}-${Date.now()}-${filename}`;
    const writeStream = createWriteStream(
      process.cwd() + "/src/uploads/" + newFileName
    );
    readStream.pipe(writeStream);
    avatarUrl = `http://localhost:4000/static/${newFileName}`;

```
