# Deploy de aplicação ASP .NET Core 8 - Kubernetes
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
