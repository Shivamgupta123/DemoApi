# #See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.

# FROM mcr.microsoft.com/dotnet/core/aspnet:3.1-buster-slim AS base
# WORKDIR /app
# EXPOSE 80
# EXPOSE 443

# FROM mcr.microsoft.com/dotnet/core/sdk:3.1-buster AS build
# WORKDIR /src
# COPY ["DemoApi/DemoApi.csproj", "DemoApi/"]
# RUN dotnet restore "DemoApi/DemoApi.csproj"
# COPY . .
# WORKDIR "/src/DemoApi"
# RUN dotnet build "DemoApi.csproj" -c Release -o /app/build

# FROM build AS publish
# RUN dotnet publish "DemoApi.csproj" -c Release -o /app/publish

# FROM base AS final
# WORKDIR /app
# COPY --from=publish /app/publish .
# ENTRYPOINT ["dotnet", "DemoApi.dll"]


FROM mcr.microsoft.com/dotnet/core/sdk:3.1 AS build-env
WORKDIR /app

# COPY *.csproj .
COPY DemoApi/DemoApi.csproj ./
RUN dotnet restore
# RUN dotnet restore TestWebApi.csproj

COPY . ./

# RUN dotnet publish --no-restore -c Release -o out
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:3.1
WORKDIR /app
COPY --from=build-env /app/out .
ENTRYPOINT ["dotnet", "DemoApi.dll"]
