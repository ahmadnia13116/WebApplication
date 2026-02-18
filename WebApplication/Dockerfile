# Stage 1: Build
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
ARG BUILD_CONFIGURATION=Release
COPY ["WebApplication.csproj", "./"]
RUN dotnet restore "WebApplication.csproj"
COPY . .
RUN dotnet build "WebApplication.csproj" -c $BUILD_CONFIGURATION -o /app/build --no-restore

# Stage 2: Publish
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "WebApplication.csproj" -c $BUILD_CONFIGURATION -o /app/publish --no-build -p:UseAppHost=false -p:DebugType=None

# Stage 3: Runtime
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS runtime
WORKDIR /app
COPY --from=publish /app/publish .
ENV DOTNET_RUNNING_IN_CONTAINER=true
ENV DOTNET_USE_POLLING_FILE_WATCHER=true
ENTRYPOINT ["dotnet", "WebApplication.dll"]