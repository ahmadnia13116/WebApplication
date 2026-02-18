## Stage 1: Build
#FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
#WORKDIR /src
#
## Copy csproj and restore dependencies
#COPY ["WebApplication.csproj", "./"]
#RUN dotnet restore "WebApplication.csproj"
#
## Copy everything else and build
#COPY . .
#RUN dotnet build "WebApplication.csproj" -c Release -o /app/build
#
## Stage 2: Publish
#FROM build AS publish
#RUN dotnet publish "WebApplication.csproj" -c Release -o /app/publish
#
## Stage 3: Runtime
#FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS final
#WORKDIR /app
#COPY --from=publish /app/publish .
#ENTRYPOINT ["dotnet", "WebApplication.dll"]
#
# ======================
# Stage 1: Build
# ======================
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

# ARG برای امکان تغییر Configuration
ARG BUILD_CONFIGURATION=Release

# Copy csproj first and restore dependencies (caching layers)
COPY ["WebApplication.csproj", "./"]
RUN dotnet restore "WebApplication.csproj"

# Copy all source code
COPY . .

# Build the project
RUN dotnet build "WebApplication.csproj" \
    -c $BUILD_CONFIGURATION \
    -o /app/build \
    --no-restore

# ======================
# Stage 2: Publish
# ======================
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "WebApplication.csproj" \
    -c $BUILD_CONFIGURATION \
    -o /app/publish \
    --no-build \
    -p:UseAppHost=false \
    -p:DebugType=None

# ======================
# Stage 3: Runtime
# ======================
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS runtime
WORKDIR /app

# Copy published output from previous stage
COPY --from=publish /app/publish .

# Set environment variable for production
ENV DOTNET_RUNNING_IN_CONTAINER=true
ENV DOTNET_USE_POLLING_FILE_WATCHER=true

# Entry point
ENTRYPOINT ["dotnet", "WebApplication.dll"]

