---
title: BlazorChargements de fichiers ASP.net Core
author: guardrex
description: Découvrez comment charger des fichiers dans à Blazor l’aide du composant FichierEntrée.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
ms.date: 02/18/2021
uid: blazor/file-uploads
ms.openlocfilehash: a31821f03efd39d774a4a3c61d027983a1783e2d
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395121"
---
# <a name="aspnet-core-blazor-file-uploads"></a><span data-ttu-id="02f08-103">BlazorChargements de fichiers ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="02f08-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="02f08-104">Utilisez le `InputFile` composant pour lire les données du fichier browser dans le code .net, y compris pour les chargements de fichiers.</span><span class="sxs-lookup"><span data-stu-id="02f08-104">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="02f08-105">Suivez toujours les meilleures pratiques en matière de sécurité du chargement des fichiers.</span><span class="sxs-lookup"><span data-stu-id="02f08-105">Always follow file upload security best practices.</span></span> <span data-ttu-id="02f08-106">Pour plus d’informations, consultez <xref:mvc/models/file-uploads#security-considerations>.</span><span class="sxs-lookup"><span data-stu-id="02f08-106">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="02f08-107">Composant `InputFile`</span><span class="sxs-lookup"><span data-stu-id="02f08-107">`InputFile` component</span></span>

<span data-ttu-id="02f08-108">Le `InputFile` composant est rendu sous la forme d’une entrée HTML de type `file` .</span><span class="sxs-lookup"><span data-stu-id="02f08-108">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="02f08-109">Par défaut, l’utilisateur sélectionne des fichiers uniques.</span><span class="sxs-lookup"><span data-stu-id="02f08-109">By default, the user selects single files.</span></span> <span data-ttu-id="02f08-110">Ajoutez l' `multiple` attribut pour permettre à l’utilisateur de télécharger plusieurs fichiers à la fois.</span><span class="sxs-lookup"><span data-stu-id="02f08-110">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="02f08-111">Quand un ou plusieurs fichiers sont sélectionnés par l’utilisateur, le `InputFile` composant déclenche un `OnChange` événement et transmet un `InputFileChangeEventArgs` qui fournit l’accès à la liste des fichiers sélectionnés et des détails sur chaque fichier.</span><span class="sxs-lookup"><span data-stu-id="02f08-111">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="02f08-112">Pour lire des données à partir d’un fichier sélectionné par l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="02f08-112">To read data from a user-selected file:</span></span>

* <span data-ttu-id="02f08-113">Appelez `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` sur le fichier et lisez à partir du flux retourné.</span><span class="sxs-lookup"><span data-stu-id="02f08-113">Call `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="02f08-114">Pour plus d’informations, consultez la section [flux de fichier](#file-streams) .</span><span class="sxs-lookup"><span data-stu-id="02f08-114">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="02f08-115">Le <xref:System.IO.Stream> retourné par `OpenReadStream` applique une taille maximale en octets du en cours de `Stream` lecture.</span><span class="sxs-lookup"><span data-stu-id="02f08-115">The <xref:System.IO.Stream> returned by `OpenReadStream` enforces a maximum size in bytes of the `Stream` being read.</span></span> <span data-ttu-id="02f08-116">Par défaut, les fichiers dont la taille ne dépasse pas 512 000 octets (500 Ko) peuvent être lus avant que toute autre lecture n’entraîne une exception.</span><span class="sxs-lookup"><span data-stu-id="02f08-116">By default, files no larger than 512,000 bytes (500 KB) in size are allowed to be read before any further reads would result in an exception.</span></span> <span data-ttu-id="02f08-117">Cette limite est présente pour empêcher les développeurs de lire accidentellement des fichiers volumineux dans la mémoire.</span><span class="sxs-lookup"><span data-stu-id="02f08-117">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="02f08-118">Le `maxAllowedSize` paramètre sur `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` peut être utilisé pour spécifier une taille supérieure si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="02f08-118">The `maxAllowedSize` parameter on `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` can be used to specify a larger size if required.</span></span>
* <span data-ttu-id="02f08-119">Évitez de lire le flux de fichier entrant directement dans la mémoire.</span><span class="sxs-lookup"><span data-stu-id="02f08-119">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="02f08-120">Par exemple, ne copiez pas les octets du fichier dans un <xref:System.IO.MemoryStream> ou lisez-le en tant que tableau d’octets.</span><span class="sxs-lookup"><span data-stu-id="02f08-120">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="02f08-121">Ces approches peuvent entraîner des problèmes de performances et de sécurité, en particulier dans Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="02f08-121">These approaches can result in performance and security problems, especially in Blazor Server.</span></span> <span data-ttu-id="02f08-122">Au lieu de cela, envisagez de copier les octets de fichier dans un magasin externe, tel qu’un objet BLOB ou un fichier sur le disque.</span><span class="sxs-lookup"><span data-stu-id="02f08-122">Instead, consider copying file bytes to an external store, such as a blob or a file on disk.</span></span>

<span data-ttu-id="02f08-123">Un composant qui reçoit un fichier image peut appeler la `RequestImageFileAsync` méthode pratique sur le fichier pour redimensionner les données de l’image dans le runtime JavaScript du navigateur avant que l’image ne soit diffusée dans l’application.</span><span class="sxs-lookup"><span data-stu-id="02f08-123">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="02f08-124">L’exemple suivant illustre le chargement de plusieurs fichiers dans un composant.</span><span class="sxs-lookup"><span data-stu-id="02f08-124">The following example demonstrates multiple file upload in a component.</span></span> <span data-ttu-id="02f08-125">`InputFileChangeEventArgs.GetMultipleFiles` autorise la lecture de plusieurs fichiers.</span><span class="sxs-lookup"><span data-stu-id="02f08-125">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="02f08-126">Spécifiez le nombre maximal de fichiers que vous prévoyez de lire pour empêcher un utilisateur malveillant de télécharger un plus grand nombre de fichiers que celle attendue par l’application.</span><span class="sxs-lookup"><span data-stu-id="02f08-126">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="02f08-127">`InputFileChangeEventArgs.File` permet de lire le premier et le seul fichier uniquement si le téléchargement de fichier ne prend pas en charge plusieurs fichiers.</span><span class="sxs-lookup"><span data-stu-id="02f08-127">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload doesn't support multiple files.</span></span>

> [!NOTE]
> <span data-ttu-id="02f08-128"><xref:Microsoft.AspNetCore.Components.Forms.InputFileChangeEventArgs> se trouve dans l' <xref:Microsoft.AspNetCore.Components.Forms?displayProperty=fullName> espace de noms, qui est généralement l’un des espaces de noms dans le fichier de l’application `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="02f08-128"><xref:Microsoft.AspNetCore.Components.Forms.InputFileChangeEventArgs> is in the <xref:Microsoft.AspNetCore.Components.Forms?displayProperty=fullName> namespace, which is typically one of the namespaces in the app's `_Imports.razor` file.</span></span>

<span data-ttu-id="02f08-129">`Pages/UploadFiles.razor`:</span><span class="sxs-lookup"><span data-stu-id="02f08-129">`Pages/UploadFiles.razor`:</span></span>

```razor
@page "/upload-files"
@using System.IO

<h3>Upload Files</h3>

<p>
    <label>
        Max file size:
        <input type="number" @bind="maxFileSize" />
    </label>
</p>

<p>
    <label>
        Max allowed files:
        <input type="number" @bind="maxAllowedFiles" />
    </label>
</p>

<p>
    <label>
        Upload up to @maxAllowedFiles files of up to @maxFileSize bytes each:
        <InputFile OnChange="@LoadFiles" multiple />
    </label>
</p>

<p>@exceptionMessage</p>

@if (isLoading)
{
    <p>Loading...</p>
}

<ul>
    @foreach (var (file, content) in loadedFiles)
    {
        <li>
            <ul>
                <li>Name: @file.Name</li>
                <li>Last modified: @file.LastModified.ToString()</li>
                <li>Size (bytes): @file.Size</li>
                <li>Content type: @file.ContentType</li>
                <li>Content: @content</li>
            </ul>
        </li>
    }
</ul>

@code {
    private Dictionary<IBrowserFile, string> loadedFiles =
        new Dictionary<IBrowserFile, string>();
    private long maxFileSize = 1024 * 15;
    private int maxAllowedFiles = 3;
    private bool isLoading;
    string exceptionMessage;

    async Task LoadFiles(InputFileChangeEventArgs e)
    {
        isLoading = true;
        loadedFiles.Clear();
        exceptionMessage = string.Empty;

        try
        {
            foreach (var file in e.GetMultipleFiles(maxAllowedFiles))
            {
                using var reader = 
                    new StreamReader(file.OpenReadStream(maxFileSize));

                loadedFiles.Add(file, await reader.ReadToEndAsync());
            }
        }
        catch (Exception ex)
        {
            exceptionMessage = ex.Message;
        }

        isLoading = false;
    }
}
```

<span data-ttu-id="02f08-130">`IBrowserFile` retourne les métadonnées [exposées par le navigateur](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) en tant que propriétés.</span><span class="sxs-lookup"><span data-stu-id="02f08-130">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="02f08-131">Ces métadonnées peuvent être utiles pour la validation préliminaire.</span><span class="sxs-lookup"><span data-stu-id="02f08-131">This metadata can be useful for preliminary validation.</span></span>

## <a name="upload-files-to-a-server"></a><span data-ttu-id="02f08-132">Charger des fichiers sur un serveur</span><span class="sxs-lookup"><span data-stu-id="02f08-132">Upload files to a server</span></span>

<span data-ttu-id="02f08-133">L’exemple suivant illustre le chargement de fichiers sur un serveur à l’aide d’une solution hébergée Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="02f08-133">The following example demonstrates uploading files to a server using a hosted Blazor WebAssembly solution.</span></span>

> [!WARNING]
> <span data-ttu-id="02f08-134">Soyez prudent lorsque vous fournissez aux utilisateurs la possibilité de charger des fichiers sur un serveur.</span><span class="sxs-lookup"><span data-stu-id="02f08-134">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="02f08-135">Pour plus d’informations, consultez <xref:mvc/models/file-uploads#security-considerations>.</span><span class="sxs-lookup"><span data-stu-id="02f08-135">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

<span data-ttu-id="02f08-136">La `UploadResult` classe suivante dans le **`Shared`** projet gère le résultat d’un fichier téléchargé.</span><span class="sxs-lookup"><span data-stu-id="02f08-136">The following `UploadResult` class in the **`Shared`** project maintains the result of an uploaded file.</span></span> <span data-ttu-id="02f08-137">En cas d’échec du chargement d’un fichier sur le serveur, un code d’erreur est retourné dans `ErrorCode` pour être affiché à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="02f08-137">When a file fails to upload on the server, an error code is returned in `ErrorCode` for display to the user.</span></span> <span data-ttu-id="02f08-138">Un nom de fichier stocké sécurisé est généré sur le serveur pour chaque fichier et retourné au client dans `StoredFileName` pour l’affichage.</span><span class="sxs-lookup"><span data-stu-id="02f08-138">A safe stored file name is generated on the server for each file and returned to the client in `StoredFileName` for display.</span></span> <span data-ttu-id="02f08-139">Les fichiers sont indexés entre le client et le serveur à l’aide du nom de fichier non sécurisé/non approuvé dans `FileName` .</span><span class="sxs-lookup"><span data-stu-id="02f08-139">Files are keyed between the client and server using the unsafe/untrusted file name in `FileName`.</span></span>

<span data-ttu-id="02f08-140">`UploadResult.cs`:</span><span class="sxs-lookup"><span data-stu-id="02f08-140">`UploadResult.cs`:</span></span>

```csharp
public class UploadResult
{
    public bool Uploaded { get; set; }

    public string FileName { get; set; }

    public string StoredFileName { get; set; }

    public int ErrorCode { get; set; }
}
```

<span data-ttu-id="02f08-141">Le `UploadFiles` composant suivant dans le **`Client`** projet :</span><span class="sxs-lookup"><span data-stu-id="02f08-141">The following `UploadFiles` component in the **`Client`** project:</span></span>

* <span data-ttu-id="02f08-142">Permet aux utilisateurs de télécharger des fichiers à partir du client.</span><span class="sxs-lookup"><span data-stu-id="02f08-142">Permits users to upload files from the client.</span></span>
* <span data-ttu-id="02f08-143">Affiche le nom de fichier non approuvé/non sécurisé fourni par le client dans l’interface utilisateur, car Razor encode automatiquement les chaînes au format html.</span><span class="sxs-lookup"><span data-stu-id="02f08-143">Displays the untrusted/unsafe file name provided by the client in the UI because Razor automatically HTML-encodes strings.</span></span> <span data-ttu-id="02f08-144">**N’approuvez pas les noms de fichiers fournis par les clients dans d’autres scénarios.**</span><span class="sxs-lookup"><span data-stu-id="02f08-144">**Don't trust file names supplied by clients in other scenarios.**</span></span>

<span data-ttu-id="02f08-145">`Pages/UploadFiles.razor`:</span><span class="sxs-lookup"><span data-stu-id="02f08-145">`Pages/UploadFiles.razor`:</span></span>

```razor
@page "/upload-files"
@using System.Linq
@using Microsoft.Extensions.Logging
@inject HttpClient Http
@inject ILogger<UploadFiles> logger

<h1>Upload Files</h1>

<p>
    <label>
        Upload up to @maxAllowedFiles files:
        <InputFile OnChange="@OnInputFileChange" multiple />
    </label>
</p>

@if (files.Count > 0)
{
    <div class="card">
        <div class="card-body">
            <ul>
                @foreach (var file in files)
                {
                    <li>
                        File: @file.Name
                        <br>
                        @if (FileUpload(uploadResults, file.Name, logger,
                            out var result))
                        {
                            <span>
                                Stored File Name: @result.StoredFileName
                            </span>
                        }
                        else
                        {
                            <span>
                                There was an error uploading the file
                                (Error: @result.ErrorCode).
                            </span>
                        }
                    </li>
                }
            </ul>
        </div>
    </div>
}

@code {
    private IList<File> files = new List<File>();
    private IList<UploadResult> uploadResults = new List<UploadResult>();
    private int maxAllowedFiles = 3;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    private async Task OnInputFileChange(InputFileChangeEventArgs e)
    {
        shouldRender = false;
        long maxFileSize = 1024 * 1024 * 15;
        var upload = false;

        using var content = new MultipartFormDataContent();

        foreach (var file in e.GetMultipleFiles(maxAllowedFiles))
        {
            if (uploadResults.SingleOrDefault(
                f => f.FileName == file.Name) is null)
            {
                var fileContent = new StreamContent(file.OpenReadStream());

                files.Add(
                    new File()
                    {
                        Name = file.Name,
                    });

                if (file.Size < maxFileSize)
                {
                    content.Add(
                        content: fileContent,
                        name: "\"files\"",
                        fileName: file.Name);

                    upload = true;
                }
                else
                {
                    logger.LogInformation("{FileName} not uploaded", file.Name);

                    uploadResults.Add(
                        new UploadResult()
                        {
                            FileName = file.Name,
                            ErrorCode = 6,
                            Uploaded = false,
                        });
                }
            }
        }

        if (upload)
        {
            var response = await Http.PostAsync("/Filesave", content);

            var newUploadResults = await response.Content
                .ReadFromJsonAsync<IList<UploadResult>>();

            uploadResults = uploadResults.Concat(newUploadResults).ToList();
        }

        shouldRender = true;
    }

    private static bool FileUpload(IList<UploadResult> uploadResults,
        string fileName, ILogger<UploadFiles> logger, out UploadResult result)
    {
        result = uploadResults.SingleOrDefault(f => f.FileName == fileName);

        if (result is null)
        {
            logger.LogInformation("{FileName} not uploaded", fileName);
            result = new UploadResult();
            result.ErrorCode = 5;
        }

        return result.Uploaded;
    }

    private class File
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="02f08-146">Pour utiliser le code suivant, créez un `Development/unsafe_uploads` dossier à la racine du **`Server`** projet.</span><span class="sxs-lookup"><span data-stu-id="02f08-146">To use the following code, create a `Development/unsafe_uploads` folder at the root of the **`Server`** project.</span></span>

<span data-ttu-id="02f08-147">Le `FilesaveController` contrôleur suivant dans le **`Server`** projet enregistre les fichiers téléchargés à partir du client.</span><span class="sxs-lookup"><span data-stu-id="02f08-147">The following `FilesaveController` controller in the **`Server`** project saves uploaded files from the client.</span></span>

> [!WARNING]
> <span data-ttu-id="02f08-148">L’exemple enregistre les fichiers sans analyser leur contenu.</span><span class="sxs-lookup"><span data-stu-id="02f08-148">The example saves files without scanning their contents.</span></span> <span data-ttu-id="02f08-149">Dans la plupart des scénarios de production, une API d’analyse anti-virus/anti-programme malveillant est utilisée sur les fichiers avant de les mettre à disposition pour le téléchargement ou pour une utilisation par d’autres systèmes.</span><span class="sxs-lookup"><span data-stu-id="02f08-149">In most production scenarios, an anti-virus/anti-malware scanner API is used on files before making them available for download or for use by other systems.</span></span> <span data-ttu-id="02f08-150">Pour plus d’informations sur les considérations relatives à la sécurité lors du chargement de fichiers sur un serveur, consultez <xref:mvc/models/file-uploads#security-considerations> .</span><span class="sxs-lookup"><span data-stu-id="02f08-150">For more information on security considerations when uploading files to a server, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

<span data-ttu-id="02f08-151">`Controllers/FilesaveController.cs`:</span><span class="sxs-lookup"><span data-stu-id="02f08-151">`Controllers/FilesaveController.cs`:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;

[ApiController]
[Route("[controller]")]
public class FilesaveController : ControllerBase
{
    private readonly IWebHostEnvironment env;
    private readonly ILogger<FilesaveController> logger;

    public FilesaveController(IWebHostEnvironment env, 
        ILogger<FilesaveController> logger)
    {
        this.env = env;
        this.logger = logger;
    }

    [HttpPost]
    public async Task<ActionResult<IList<UploadResult>>> PostFile(
        [FromForm] IEnumerable<IFormFile> files)
    {
        var maxAllowedFiles = 3;
        long maxFileSize = 1024 * 1024 * 15;
        var filesProcessed = 0;
        var resourcePath = new Uri($"{Request.Scheme}://{Request.Host}/");
        IList<UploadResult> uploadResults = new List<UploadResult>();

        foreach (var file in files)
        {
            var uploadResult = new UploadResult();
            string trustedFileNameForFileStorage;
            var untrustedFileName = file.FileName;
            uploadResult.FileName = untrustedFileName;
            var trustedFileNameForDisplay = 
                WebUtility.HtmlEncode(untrustedFileName);

            if (filesProcessed < maxAllowedFiles)
            {
                if (file.Length == 0)
                {
                    logger.LogInformation("{FileName} length is 0", 
                        trustedFileNameForDisplay);
                    uploadResult.ErrorCode = 1;
                }
                else if (file.Length > maxFileSize)
                {
                    logger.LogInformation("{FileName} of {Length} bytes is " +
                        "larger than the limit of {Limit} bytes", 
                        trustedFileNameForDisplay, file.Length, maxFileSize);
                    uploadResult.ErrorCode = 2;
                }
                else
                {
                    try
                    {
                        trustedFileNameForFileStorage = Path.GetRandomFileName();
                        var path = Path.Combine(env.ContentRootPath, 
                            env.EnvironmentName, "unsafe_uploads", 
                            trustedFileNameForFileStorage);
                        using MemoryStream ms = new();
                        await file.CopyToAsync(ms);
                        await System.IO.File.WriteAllBytesAsync(path, ms.ToArray());
                        logger.LogInformation("{FileName} saved at {Path}", 
                            trustedFileNameForDisplay, path);
                        uploadResult.Uploaded = true;
                        uploadResult.StoredFileName = trustedFileNameForFileStorage;
                    }
                    catch (IOException ex)
                    {
                        logger.LogError("{FileName} error on upload: {Message}", 
                            trustedFileNameForDisplay, ex.Message);
                        uploadResult.ErrorCode = 3;
                    }
                }

                filesProcessed++;
            }
            else
            {
                logger.LogInformation("{FileName} not uploaded because the " +
                    "request exceeded the allowed {Count} of files", 
                    trustedFileNameForDisplay, maxAllowedFiles);
                uploadResult.ErrorCode = 4;
            }

            uploadResults.Add(uploadResult);
        }

        return new CreatedResult(resourcePath, uploadResults);
    }
}
```

## <a name="file-streams"></a><span data-ttu-id="02f08-152">Flux de fichiers</span><span class="sxs-lookup"><span data-stu-id="02f08-152">File streams</span></span>

<span data-ttu-id="02f08-153">Dans Blazor WebAssembly , les données de fichier sont transmises directement dans le code .net au sein du navigateur.</span><span class="sxs-lookup"><span data-stu-id="02f08-153">In Blazor WebAssembly, file data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="02f08-154">Dans Blazor Server , les données de fichier sont diffusées en continu via la SignalR connexion dans du code .net sur le serveur lors de la lecture du fichier à partir du flux.</span><span class="sxs-lookup"><span data-stu-id="02f08-154">In Blazor Server, file data is streamed over the SignalR connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="02f08-155"><xref:Microsoft.AspNetCore.Components.Forms.RemoteBrowserFileStreamOptions> autorise la configuration des caractéristiques de chargement de fichier pour Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="02f08-155"><xref:Microsoft.AspNetCore.Components.Forms.RemoteBrowserFileStreamOptions> allows configuring file upload characteristics for Blazor Server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="02f08-156">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="02f08-156">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
