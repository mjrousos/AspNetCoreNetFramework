# Build stage
# Use .NET Framework SDK as the base build image
FROM mcr.microsoft.com/dotnet/framework/sdk:4.8-windowsservercore-ltsc2022 AS build

WORKDIR /src
COPY ["WebApp.csproj", "WebApp.csproj"]
RUN dotnet restore
COPY . .
RUN dotnet build
RUN dotnet publish -c Release

# Run stage
# Use Server Core ASP.NET base image to have access to IIS
# https://hub.docker.com/_/microsoft-dotnet-framework/
FROM mcr.microsoft.com/dotnet/framework/aspnet:4.8-windowsservercore-ltsc2022

# Install ASP.NET Core Module (https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/aspnet-core-module?view=aspnetcore-2.1)
WORKDIR /temp
ADD https://download.visualstudio.microsoft.com/download/pr/5ee633f2-bf6d-49bd-8fb6-80c861c36d54/caa93641707e1fd5b8273ada22009246/dotnet-hosting-2.2.1-win.exe dotnethosting.exe
RUN dotnethosting.exe /quiet /install

WORKDIR /inetpub/wwwroot

# Replace IIS wwwroot with app; this will listen (on port 80) via IIS
RUN powershell -NoProfile -Command Remove-Item -Recurse C:\inetpub\wwwroot\*
COPY --from=build /src/bin/Release/net48/publish .

# This will listen (on port 5000) with Kestrel directly
ENTRYPOINT [ "WebApp.exe", "--urls", "http://*:5000"]