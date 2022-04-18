#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/core/aspnet:3.1-buster-slim AS base
WORKDIR /app
EXPOSE 8080
EXPOSE 443

# ENV ASPNETCORE_URLS=https://+:443;http://+:80
# ENV ASPNETCORE_HTTPS_PORT=8001
# ENV ASPNETCORE_ENVIRONMENT=Development
# ENV ASPNETCORE_Kestrel__Certificates__Default__Password: password
# ENV ASPNETCORE_Kestrel__Certificates__Default__Path: /.aspnet/https/RestApi.pfx

# Creates a non-root user with an explicit UID and adds permission to access the /app folder
# For more info, please refer to https://aka.ms/vscode-docker-dotnet-configure-containers
#RUN adduser -u 5678 --disabled-password --gecos "" appuser && chown -R appuser /app
#USER appuser

FROM mcr.microsoft.com/dotnet/core/sdk:3.1-buster AS build
WORKDIR /src
COPY ["Apps/RestApi/RestApi.csproj", "Apps/RestApi/"]
COPY ["Business/AppPersistence.Interface/AppPersistence.Interface.csproj", "Business/AppPersistence.Interface/"]
COPY ["Business/AppBusiness.Data/AppBusiness.Data.csproj", "Business/AppBusiness.Data/"]
COPY ["Modules/AppPersistence.Mysql/AppPersistence.MySql.csproj", "Modules/AppPersistence.MySql/"]
COPY ["Business/AppBusiness.Interface/AppBusiness.Interface.csproj", "Business/AppBusiness.Interface/"]
COPY ["Business/AppBusiness.Impl/AppBusiness.Impl.csproj", "Business/AppBusiness.Impl/"]
RUN dotnet restore "Apps/RestApi/RestApi.csproj"
COPY . .
WORKDIR "/src/Apps/RestApi"
RUN dotnet build "RestApi.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "RestApi.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "RestApi.dll"]
