FROM mcr.microsoft.com/dotnet/core/aspnet:2.2-stretch-slim AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/core/sdk:2.2-stretch AS build
WORKDIR /src
COPY ["MicroServiceDemo/MicroServiceDemo.csproj", "MicroServiceDemo/"]
RUN dotnet restore "MicroServiceDemo/MicroServiceDemo.csproj"
COPY . .
WORKDIR "/src/MicroServiceDemo"
RUN dotnet build "MicroServiceDemo.csproj" -c Release -o /app

FROM build AS publish
RUN dotnet publish "MicroServiceDemo.csproj" -c Release -o /app

FROM base AS final
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "MicroServiceDemo.dll"]