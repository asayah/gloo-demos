# Gloo Edge GraphQL - Grpc data sources 


## Create the demo services

```bash
kubectl apply -f demo-grpc-services.yaml
```

## Turn on discovery

```bash 
    kubectl label service users discovery.solo.io/function_discovery=enabled
    kubectl label service products discovery.solo.io/function_discovery=enabled
    kubectl label service reviews discovery.solo.io/function_discovery=enabled
```

Then after a couple of seconds you should see the Graphql schemas created on your cluster

```bash
kubectl get graphqlapis.graphql.gloo.solo.io -A
```

## create Virtual Service 

```bash
kubectl apply -f vs.yaml
```

Here is how we attach a graphqlAPI to a route in a vs:

```text
    routes:
    - graphqlApiRef:
        name: default-users-8080
        namespace: gloo-system
      matchers:
      - prefix: /users
```

Now you can call the gateway on /users and make a GraphQL API call, using the following query for example:

```text

query GetUser($userSearch: UserSearchInput) {
  GetUser(UserSearch: $userSearch) {
            first_name
            last_name
            username  
            }
}

```
Passing this vars: 

```text

{
  "productSearch": {},
  "userSearch": {
    "username": "wpatel"
  }
}
```

