# Blazor WASM (Azure Storage + CDN) and Backend in Azure Function App

This repo created from .NET 8 [Blazor WebAssembly](https://docs.microsoft.com/aspnet/core/blazor/?view=aspnetcore-6.0#blazor-webassembly) client application.

Frontend is developed using Blazor Web Assembly and deploy via Azure CDN.

Backend is developed using Azure Function.

## Pre-Requisite
1. Install [Visual Studio Code](https://code.visualstudio.com/download)
2. Install [.Net 8 SDK](https://dotnet.microsoft.com/download)
3. Install [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli)
4. Install [AZ Powershell Module](https://learn.microsoft.com/en-us/powershell/azure/install-azps-windows?tabs=powershell&pivots=windows-psgallery)
5. Register new [Azure Account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
6. Install [Azure Static Web Apps CLI](https://www.npmjs.com/package/@azure/static-web-apps-cli)
7. Install [Azure Function Core Tools](https://go.microsoft.com/fwlink/?linkid=2174087)
8. Install Storage Emulator [Azurite](https://learn.microsoft.com/en-us/azure/storage/common/storage-use-azurite?tabs=visual-studio-code%2Cblob-storage#install-azurite) extension.

## Getting Started

The following are necessary steps to run locally.

### How to run Blazor app locally via Visual Studio Code 

You can run the app locally either via Debug mode or manually using steps below.

1. Open the folder in Visual Studio Code.

2. Start Azurite by going to command pallete (Ctrl + Shift + P), find and choose Azurite: Start and then press Enter.

3. In the **Api** folder, copy `local.settings.example.json` to `local.settings.json`

3. In the **Client/settings** folder, copy `appsettings.sample.json` to `appsettings.dev.json`. Update the settings. This is needed when deploying to Azure.

4. In the VS Code terminal, run the following command to start the Static Web Apps CLI, along with the Blazor WebAssembly client application and the Functions API app:

    In the Client folder, run:
    ```bash
    dotnet run
    ```

    In the API folder, run:
    ```bash
    func start
    ```

    In another terminal, run:
    ```bash
    swa start http://localhost:5000 --api-location http://localhost:7071
    ```

    The Static Web Apps CLI (`swa`) starts a proxy on port 4280 that will forward static site requests to the Blazor server on port 5000 and requests to the `/api` endpoint to the Functions server. 

5. Open a browser and navigate to the Static Web Apps CLI's address at `http://localhost:4280`. You'll be able to access both the client application and the Functions API app in this single address. When you navigate to the "Fetch Data" page, you'll see the data returned by the Functions API app.

6. Enter Ctrl-C to stop the Static Web Apps CLI.

## Application Structure

- **Client**: The Blazor WebAssembly application
- **Api**: A C# Azure Functions API, in which serves as backend
- **Shared**: A C# class library with a shared data model between the Blazor and Functions application

## Security Setup

1. Using SPA / MSAL authentication (OAuth2 / OpenID) for frontend.
2. Using Managed Identities and RBAC role access for CDN to access the Azure Function APIs. Configured via bicep file.
3. Added Network ACLs to allow only CDN and My Home Ip address to access Azure Function APIs. Configured via bicep file. Note: You can update the My Home Ip address inside the azureFunction.bicep file.
4. Added CORS to allow only request from the CDN endpoint URL. Configured via bicep file.

## Deploy to Azure Static Web Apps

This application can be deployed to (Api) Azure Function and (Client) Azure Storage and CDN.

Run below via Powershell.  

Login to Azure:

    az login

Run deployment script:

    .\infra\script\deploy.ps1

### Access the Blazor App via CDN

After deployment above and make sure no errors. Open Azure Portal, locate your resource group and CDN Endpoint, you can click the Endpoint hostname shown in the overview page to access the Blazor App.
