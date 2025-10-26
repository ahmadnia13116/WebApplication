# -----------------------
# Base image (runtime)
# -----------------------
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

# -----------------------
# Build stage
# -----------------------
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src

# Copy csproj and restore dependencies
COPY ["WebApplication/WebApplication.csproj", "WebApplication/"]
RUN dotnet restore "./WebApplication/WebApplication.csproj"

# Copy everything else and build
COPY . .
WORKDIR "/src/WebApplication"
RUN dotnet build "./WebApplication.csproj" -c $BUILD_CONFIGURATION -o /app/build

# -----------------------
# Publish stage
# -----------------------
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./WebApplication.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# -----------------------
# Final stage (runtime)
# -----------------------
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "WebApplication.dll"]
