
# App Service Demo - Bring Your Own Storage with Azure Web App on Windows and .NET 6 / .NET Core

Note: For a comparable example in Web App on Linux / Containers, refer to [this example](https://github.com/rramachand21-zz/appsvcdemobyos).

This .NET 6 / .NET Core-based web application is a sample application that shows how you can use the "Bring your own storage" feature in Azure Web App on Windows to integrate your Web App with an Azure storage (files). Currently, the mount path for Bring Your Own Storage starts at c:\mounts (or at d:\mounts, depending upon which cluster your App Service is hosted in), whereas virtual directories in Azure Web App for Windows are restricted to paths within the c:\home\site (or d:\home\site) physical path, unless you use an applicationHost transform*. With .NET 6 / .NET Core, if you enable static files (via UseStaticFiles), by default the location of the static files will be in the wwwroot folder that is within the application, which in App Service would be somewhere under c:\home\site (or d:\home\site) physical path (eg c:\home\site\wwwroot\wwwroot). To get around this limitation, you can [change the UseStaticFiles method](https://docs.microsoft.com/aspnet/core/fundamentals/static-files?view=aspnetcore-6.0#serve-static-files) in your application startup to reference the physical mount path that you configure for Bring Your Own Storage, which the Program.cs in this sample application does.

The home page refers to some images, css and js files that are in an azure blob storage and Azure app services can be configured to mount the blob storage to have those contents readily available to the web application.

<b>Steps to deploy to Azure App Service:</b>

1. Create an Azure Files share - https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share
2. Extract <a href="https://github.com/gabesmsft/AspNetStaticFilesDemo/blob/master/wwwroot_upload_to_storage.zip">this zip file</a> and then upload the entire wwwroot folder to your azure storage file share, so that you end up with the following directory structure under your Storage account:
   - File Share name
     - wwwroot

Note: You can use [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) to upload the contents.

3. Create an Azure Web App on Windows
4. [Create a storage mount](https://docs.microsoft.com/azure/app-service/configure-connect-to-azure-storage?tabs=portal&pivots=code-windows) on the Azure Web App, using the following as the mount path:

   /mounts/byos1

<b>Sample Az CLI command:</b>
<br/>
<code>
az webapp config storage-account add -g YourResourceGroup -n YourAppServiceName --custom-id YourCustomId --storage-type AzureFiles--account-name YourStorageAccountName --share-name YourStorageContainerOrShareName --access-key &lt;youraccesskey&gt; --mount-path /mounts/byos1
  </code>

5. Deploy this application to your Azure Web App 

You can now browse to your web application hosted on Azure App Service.

*Alternatively (eg. for other application frameworks), you can use an applicationHost transform to map the physical path to a virtual directory, using [this guidance](https://www.thebestcsharpprogrammerintheworld.com/2022/06/03/bring-your-own-storage-azure-app-service-map-directory-path/).

