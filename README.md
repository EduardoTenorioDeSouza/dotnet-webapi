# Deploy de aplicação WebApi Core 8 no Kubernetes(Kind)
Criando e subindo uma aplicação web api net 8.0 no kubernetes com kind.

# Dockerfile

```
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build-env
WORKDIR /App

COPY ./src ./

RUN dotnet restore

RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/aspnet:8.0
WORKDIR /App
COPY --from=build-env /App/out .

EXPOSE 80

ENTRYPOINT ["dotnet", "dotnet-webapi.dll"]
```

# Docker build
docker build -t esouza/dotnet-webapi .

# Docker run
docker run --rm -p 80:80 -e ASPNETCORE_HTTP_PORTS=80 esouza/dotnet-webapi --name webapi

# K8S Deployment

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dotnetapi
  labels:
    app: dotnetapi
spec:
  selector:
    matchLabels:
      app: dotnetapi    
  replicas: 1
  template:
    metadata:
      labels:
        app: "dotnetapi"
    spec:
      containers:
        - name: dotnetapi
          image: "esouza/dotnet-webapi:latest"
          resources:
            requests:
              cpu: 100m
              memory: 128Mi
            limits:
              cpu: 250m
              memory: 256Mi
          ports:
           - containerPort: 80
          env:
            - name: ASPNETCORE_URLS
              value: http://*:80          
```

# K8S Service
```
apiVersion: v1
kind: Service
metadata:
  name: dotnetapi-service
spec:
  selector:
    app: dotnetapi
  type: LoadBalancer
  ports:
  - name: dotnetapi-service
    port: 80
    targetPort: 80
    
```

# Referências

https://kubernetes.io/

https://kind.sigs.k8s.io/docs/user/quick-start/


#
