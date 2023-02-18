# stage with all files that needed for depedency restore
FROM alpine:3.15 as ctx
WORKDIR /source

# copy all source code
COPY . /source

# and filter out what we dont need for restore stage
# NB: -delete at the end 
# todo: research for more info on what can affect restore process
RUN find .  \
    -not -iname *.csproj  \
    -and  \
    -not -iname *.sln  \
    -and \
    -not -iname *.props  \
    -delete


# restoring & building
# copy files from prev stage
# docker should be using cache if there are no new dependencies
FROM mcr.microsoft.com/dotnet/sdk:7.0 AS builder
WORKDIR /src

COPY --from=ctx /source .

RUN dotnet restore 

COPY . .

RUN dotnet publish -o out -c Release

FROM mcr.microsoft.com/dotnet/aspnet:7.0-alpine
WORKDIR /app

COPY --from=builder /src ./

ENTRYPOINT ["dotnet", "./out/web-api.dll"]
