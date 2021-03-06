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
# <a name="aspnet-core-blazor-file-uploads"></a>BlazorChargements de fichiers ASP.net Core

Utilisez le `InputFile` composant pour lire les données du fichier browser dans le code .net, y compris pour les chargements de fichiers.

> [!WARNING]
> Suivez toujours les meilleures pratiques en matière de sécurité du chargement des fichiers. Pour plus d’informations, consultez <xref:mvc/models/file-uploads#security-considerations>.

## <a name="inputfile-component"></a>Composant `InputFile`

Le `InputFile` composant est rendu sous la forme d’une entrée HTML de type `file` .

Par défaut, l’utilisateur sélectionne des fichiers uniques. Ajoutez l' `multiple` attribut pour permettre à l’utilisateur de télécharger plusieurs fichiers à la fois. Quand un ou plusieurs fichiers sont sélectionnés par l’utilisateur, le `InputFile` composant déclenche un `OnChange` événement et transmet un `InputFileChangeEventArgs` qui fournit l’accès à la liste des fichiers sélectionnés et des détails sur chaque fichier.

Pour lire des données à partir d’un fichier sélectionné par l’utilisateur :

* Appelez `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` sur le fichier et lisez à partir du flux retourné. Pour plus d’informations, consultez la section [flux de fichier](#file-streams) .
* Le <xref:System.IO.Stream> retourné par `OpenReadStream` applique une taille maximale en octets du en cours de `Stream` lecture. Par défaut, les fichiers dont la taille ne dépasse pas 512 000 octets (500 Ko) peuvent être lus avant que toute autre lecture n’entraîne une exception. Cette limite est présente pour empêcher les développeurs de lire accidentellement des fichiers volumineux dans la mémoire. Le `maxAllowedSize` paramètre sur `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` peut être utilisé pour spécifier une taille supérieure si nécessaire.
* Évitez de lire le flux de fichier entrant directement dans la mémoire. Par exemple, ne copiez pas les octets du fichier dans un <xref:System.IO.MemoryStream> ou lisez-le en tant que tableau d’octets. Ces approches peuvent entraîner des problèmes de performances et de sécurité, en particulier dans Blazor Server . Au lieu de cela, envisagez de copier les octets de fichier dans un magasin externe, tel qu’un objet BLOB ou un fichier sur le disque.

Un composant qui reçoit un fichier image peut appeler la `RequestImageFileAsync` méthode pratique sur le fichier pour redimensionner les données de l’image dans le runtime JavaScript du navigateur avant que l’image ne soit diffusée dans l’application.

L’exemple suivant illustre le chargement de plusieurs fichiers dans un composant. `InputFileChangeEventArgs.GetMultipleFiles` autorise la lecture de plusieurs fichiers. Spécifiez le nombre maximal de fichiers que vous prévoyez de lire pour empêcher un utilisateur malveillant de télécharger un plus grand nombre de fichiers que celle attendue par l’application. `InputFileChangeEventArgs.File` permet de lire le premier et le seul fichier uniquement si le téléchargement de fichier ne prend pas en charge plusieurs fichiers.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Components.Forms.InputFileChangeEventArgs> se trouve dans l' <xref:Microsoft.AspNetCore.Components.Forms?displayProperty=fullName> espace de noms, qui est généralement l’un des espaces de noms dans le fichier de l’application `_Imports.razor` .

`Pages/UploadFiles.razor`:

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

`IBrowserFile` retourne les métadonnées [exposées par le navigateur](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) en tant que propriétés. Ces métadonnées peuvent être utiles pour la validation préliminaire.

## <a name="upload-files-to-a-server"></a>Charger des fichiers sur un serveur

L’exemple suivant illustre le chargement de fichiers sur un serveur à l’aide d’une solution hébergée Blazor WebAssembly .

> [!WARNING]
> Soyez prudent lorsque vous fournissez aux utilisateurs la possibilité de charger des fichiers sur un serveur. Pour plus d’informations, consultez <xref:mvc/models/file-uploads#security-considerations>.

La `UploadResult` classe suivante dans le **`Shared`** projet gère le résultat d’un fichier téléchargé. En cas d’échec du chargement d’un fichier sur le serveur, un code d’erreur est retourné dans `ErrorCode` pour être affiché à l’utilisateur. Un nom de fichier stocké sécurisé est généré sur le serveur pour chaque fichier et retourné au client dans `StoredFileName` pour l’affichage. Les fichiers sont indexés entre le client et le serveur à l’aide du nom de fichier non sécurisé/non approuvé dans `FileName` .

`UploadResult.cs`:

```csharp
public class UploadResult
{
    public bool Uploaded { get; set; }

    public string FileName { get; set; }

    public string StoredFileName { get; set; }

    public int ErrorCode { get; set; }
}
```

Le `UploadFiles` composant suivant dans le **`Client`** projet :

* Permet aux utilisateurs de télécharger des fichiers à partir du client.
* Affiche le nom de fichier non approuvé/non sécurisé fourni par le client dans l’interface utilisateur, car Razor encode automatiquement les chaînes au format html. **N’approuvez pas les noms de fichiers fournis par les clients dans d’autres scénarios.**

`Pages/UploadFiles.razor`:

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

Pour utiliser le code suivant, créez un `Development/unsafe_uploads` dossier à la racine du **`Server`** projet.

Le `FilesaveController` contrôleur suivant dans le **`Server`** projet enregistre les fichiers téléchargés à partir du client.

> [!WARNING]
> L’exemple enregistre les fichiers sans analyser leur contenu. Dans la plupart des scénarios de production, une API d’analyse anti-virus/anti-programme malveillant est utilisée sur les fichiers avant de les mettre à disposition pour le téléchargement ou pour une utilisation par d’autres systèmes. Pour plus d’informations sur les considérations relatives à la sécurité lors du chargement de fichiers sur un serveur, consultez <xref:mvc/models/file-uploads#security-considerations> .

`Controllers/FilesaveController.cs`:

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

## <a name="file-streams"></a>Flux de fichiers

Dans Blazor WebAssembly , les données de fichier sont transmises directement dans le code .net au sein du navigateur.

Dans Blazor Server , les données de fichier sont diffusées en continu via la SignalR connexion dans du code .net sur le serveur lors de la lecture du fichier à partir du flux. <xref:Microsoft.AspNetCore.Components.Forms.RemoteBrowserFileStreamOptions> autorise la configuration des caractéristiques de chargement de fichier pour Blazor Server .

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
