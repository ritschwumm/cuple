# Cuple RPC

REST-compatible RPC for typescript services

It's designed with compatibility in mind with other microservices but also keeping the advantages of tightly coupled microservers. For example, a Java microservice can also send requests as usual. It's REST first, so it typechecks HTTP headers, URL parameters, query strings, as well, not just the bodies. Unlike trpc, it also tracks error responses, and even lets you return custom validation errors. It tries to be out of the way as much as possbile, and lets you do everything that is possbile with `express`.

## Status

Waiting for early adaptors.

## About RPCs in general

RPC stands for Remote Rrocedure Call. You define procedures in the server, and you call them from the client.
Let it be either backend-backend communication or backend-frontend.
RPC usually indicates strict typing of procedures for maximal compatibility and tight coupling.

## Example Server

```ts
const builder = createBuilder(expressApp);

export const routes = {
  getPost: builder
    .path("/post/:id") // optional for REST compatibility
    .paramsSchema(
      z.object({
        id: z.coerce.number(),
      })
    )
    .get(async ({ data }) => {
      const post = await getPost(data.params.id);

      if (!post)
        return notFoundError({
          message: "Post is not found",
        });

      return success({
        post,
      });
    }),
};

initRpc(expressApp, {
  path: "/rpc",
  routes,
});
```

## Example Client

```ts
const client = createClient<typeof routes>({
  path: "http://localhost:8080/rpc",
});

async function getPost(id: number) {
  const response = await client.getPosts.get({ params: { id } });

  console.log(postsResponse.post); // type error

  if (response.result === "success") {
    console.log(postsResponse.post); // no type error
  }
}
```

## Other Resources

Boilerplate: https://github.com/fxdave/react-express-cuple-boilerplate  
Server docs: [Server README](https://github.com/fxdave/cuple/tree/main/packages/server)  
Client docs: [Server README](https://github.com/fxdave/cuple/tree/main/packages/client)  
Examples: `./test/src/examples`  
Tests: `./test/src`  
