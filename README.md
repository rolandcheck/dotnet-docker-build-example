## About Repo

Simple example of utilizing docker layer caching in dotnet solution building when it contains arbitrary number of
projects and folder nesting.

### Prerequisites

* Docker

### Showcase

1. Clone the repo
2. Build docker image with something like in ./src folder
    ```shell
    docker build . -t web-app:1.0  
    ```
3. Run container with just created image and check if it works
    ```shell
    docker run -p 80:80 -it --rm -d --name my-web-app web-app:1.0
    ```
    Open in browser: http://localhost/WeatherForecast
4. You can stop running container
    ```shell
    docker stop  my-web-app
    ```
5. Try to change source code. For example: add some property on forecast model
    ```csharp
    public class WeatherForecast
    {
        //... other props
        public int LifeMeaning { get; set; } = 42;
    }
    ```
6. Lets rebuild:
    ```shell
    docker build . -t web-app:1.1  
    ```
    Notice: restore command was cached
    ```
    CACHED [builder 4/6] RUN dotnet restore
    ```
7. You can run and check that modified source code was used in publish command:
    ```shell
    docker run -p 80:80 -it --rm -d --name my-web-app web-app:1.1
    ```
    And stop it
    ```shell
    docker stop  my-web-app
    ```
8. Lets try add some deps to projects.  
    To contracts.csproj
    ```xml
    <ItemGroup>
        <PackageReference Include="Newtonsoft.Json" Version="13.0.3-beta1" />
    </ItemGroup>
    ```
    To web-api.csproj
    ```xml
    <ItemGroup>
        <PackageReference Include="MediatR" Version="12.0.0" />
    </ItemGroup>
    ```
9. Rebuild and run
    ```shell
    docker build . -t web-app:1.2 
    docker run -p 80:80 -it --rm -d --name my-web-app web-app:1.2
    ```
    Restore command should be triggered as we have added new packages 

### Misc

All tutorials creates multi stage Dockerfiles where we should list all csproj files paths by hand cause docker unable to copy nested folder files with wildcards.  
This Dockerfile resolve an issue for repositories where projects within solution being created for features/UseCases  
Also can be used as template for multiple services as we dont use concrete paths for files 

