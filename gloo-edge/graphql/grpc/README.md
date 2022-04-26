# Gloo Edge GraphQL - Grpc data sources 

## Install Gloo Edge: 
```bash
 helm install glooe glooe/gloo-ee --namespace gloo-system --create-namespace  --set-string license_key=$GLOO_EDGE_LICENSE  --set gloo-fed.enabled=false
```
When the pods are up, please run the following command to register the cluster (for the UI): 

```bash
glooctl upgrade 
glooctl cluster register --cluster-name kind-kind --remote-context kind-kind --local-cluster-domain-override host.docker.internal
# --local-cluster-domain-override is just incase you are using kind
```

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
you can use the Gloo Edge UI to see your graphql API and make a request, first port-forward the UI

```bash
 kubectl port-forward svc/gloo-fed-console -n gloo-system 8090
```

Then under APIs -> GraphQL -> default-users-8080
you can make requests: 
![Gloo UI](https://raw.githubusercontent.com/asayah/gloo-demos/main/gloo-edge/graphql/grpc/UI.png)
