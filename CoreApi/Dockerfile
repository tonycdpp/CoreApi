# Get Base Image (Full .NET Core SDK)
#The Docker Engine builds up an image in stages by using an empty container at each stage and working inside that. 
#Therefore we have to assume that the container environment is “empty”, hence why we need to pull down the .NET SDK base image to allow for the compilation of our app.
FROM mcr.microsoft.com/dotnet/core/sdk AS build-env
#We specify a dedicated “working directory” where our app will eventually reside
WORKDIR /app

# Copy csproj and restore
COPY *.csproj ./
#Using dotnet restore we resolve any project dependencies (this is done using the .csproj file and retrieving any additional dependencies via Nuget)
RUN dotnet restore

# Copy everything else and build
COPY . ./
#We run the dotnet publish command, specifying that it is a Release build, (-c Release), as well as specifying a folder, (out), to contain the app build dll and any support files & libraries.
RUN dotnet publish -c Release -o out

# Generate runtime image
FROM mcr.microsoft.com/dotnet/core/aspnet
#Re-specify our working directory
WORKDIR /app
#We expose the port we want to use from inside our app
EXPOSE 80 
#Copy the relevant files from both the dependency resolution step, (build-env), and build step, (/app/out), to our working directory /app
COPY --from=build-env /app/out .
#Set the entry point for the app, (i.e. what should start), in this case it’s our published .dll using “dotnet”.
ENTRYPOINT ["dotnet", "CoreApi.dll"]