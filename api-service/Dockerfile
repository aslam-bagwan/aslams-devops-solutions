FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

COPY . .

RUN project_file="$(find . -name '*.csproj' | head -n 1)" \
    && test -n "$project_file" \
    && dotnet restore "$project_file" \
    && dotnet publish "$project_file" -c Release -o /app/publish --no-restore

FROM mcr.microsoft.com/dotnet/aspnet:8.0-alpine AS final
WORKDIR /app

COPY --from=build /app/publish .

RUN addgroup -S appgroup \
    && adduser -S appuser -G appgroup \
    && printf '#!/bin/sh\nset -e\nDLL=$(find /app -maxdepth 1 -name "*.dll" | head -n 1)\nif [ -z "$DLL" ]; then\n  echo "No application DLL found in /app"\n  exit 1\nfi\nexec dotnet "$DLL"\n' > /entrypoint.sh \
    && chmod +x /entrypoint.sh

USER appuser

EXPOSE 8080

ENV ASPNETCORE_URLS=http://+:8080

ENTRYPOINT ["/entrypoint.sh"]