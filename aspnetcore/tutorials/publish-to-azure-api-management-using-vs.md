---
title: Publier une API Web ASP.NET Core dans gestion des API Azure avec Visual Studio
author: codemillmatt
description: Découvrez comment publier une API Web ASP.NET Core dans gestion des API Azure à l’aide de Visual Studio.
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 11/22/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: ddff54bbd146c98cf83a865910401df26e7ac4ec
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217581"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a><span data-ttu-id="095e5-103">Publier une API Web ASP.NET Core dans gestion des API Azure avec Visual Studio</span><span class="sxs-lookup"><span data-stu-id="095e5-103">Publish an ASP.NET Core web API to Azure API Management with Visual Studio</span></span>

<span data-ttu-id="095e5-104">Par [Matt Soucoup](https://twitter.com/codemillmatt)</span><span class="sxs-lookup"><span data-stu-id="095e5-104">By [Matt Soucoup](https://twitter.com/codemillmatt)</span></span>

## <a name="set-up"></a><span data-ttu-id="095e5-105">Configurer</span><span class="sxs-lookup"><span data-stu-id="095e5-105">Set up</span></span>

- <span data-ttu-id="095e5-106">Ouvrez un [compte Azure gratuit](https://azure.microsoft.com/free/dotnet/) si vous n’en avez pas.</span><span class="sxs-lookup"><span data-stu-id="095e5-106">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="095e5-107">[Créez une instance de gestion des API Azure](/azure/api-management/get-started-create-service-instance) si vous ne l’avez pas déjà fait.</span><span class="sxs-lookup"><span data-stu-id="095e5-107">[Create a new Azure API Management instance](/azure/api-management/get-started-create-service-instance) if you haven't already.</span></span>
- <span data-ttu-id="095e5-108">[Créez un projet d’application API Web](xref:tutorials/first-web-api#create-a-web-project).</span><span class="sxs-lookup"><span data-stu-id="095e5-108">[Create a web API app project](xref:tutorials/first-web-api#create-a-web-project).</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="095e5-109">Configurer l’application</span><span class="sxs-lookup"><span data-stu-id="095e5-109">Configure the app</span></span>

<span data-ttu-id="095e5-110">L’ajout de définitions Swagger à l’API Web ASP.NET Core permet à la gestion des API Azure de lire les définitions d’API de l’application.</span><span class="sxs-lookup"><span data-stu-id="095e5-110">Adding Swagger definitions to the ASP.NET Core web API allows Azure API Management to read the app's API definitions.</span></span> <span data-ttu-id="095e5-111">Effectuez ensuite les tâches suivantes.</span><span class="sxs-lookup"><span data-stu-id="095e5-111">Complete the following steps.</span></span>

### <a name="add-swagger"></a><span data-ttu-id="095e5-112">Ajouter Swagger</span><span class="sxs-lookup"><span data-stu-id="095e5-112">Add Swagger</span></span>

1. <span data-ttu-id="095e5-113">Ajoutez le package NuGet [Swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) au projet d’API Web ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="095e5-113">Add the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet package to the ASP.NET Core web API project:</span></span>

    ![Capture d’écran pour configurer la boîte de dialogue NuGet](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. <span data-ttu-id="095e5-115">Ajoutez la ligne suivante à la méthode `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="095e5-115">Add the following line to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. <span data-ttu-id="095e5-116">Ajoutez la ligne suivante à la méthode `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="095e5-116">Add the following line to the `Startup.Configure` method:</span></span>

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a><span data-ttu-id="095e5-117">Modifier le routage d’API</span><span class="sxs-lookup"><span data-stu-id="095e5-117">Change the API routing</span></span>

<span data-ttu-id="095e5-118">Ensuite, vous allez modifier la structure d’URL nécessaire pour accéder à l' `Get` action de `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="095e5-118">Next, you'll change the URL structure needed to access the `Get` action of the `WeatherForecastController`.</span></span> <span data-ttu-id="095e5-119">Suivez les étapes ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="095e5-119">Complete the following steps:</span></span>

1. <span data-ttu-id="095e5-120">Ouvrez le fichier *WeatherForecastController.cs* .</span><span class="sxs-lookup"><span data-stu-id="095e5-120">Open the *WeatherForecastController.cs* file.</span></span>
1. <span data-ttu-id="095e5-121">Supprimez l' `[Route("[controller]")]` attribut de niveau classe.</span><span class="sxs-lookup"><span data-stu-id="095e5-121">Delete the `[Route("[controller]")]` class-level attribute.</span></span> <span data-ttu-id="095e5-122">La définition de la classe doit ressembler à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="095e5-122">The class definition will look like the following:</span></span>

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. <span data-ttu-id="095e5-123">Ajoutez un `[Route("/")]` attribut à l' `Get()` action.</span><span class="sxs-lookup"><span data-stu-id="095e5-123">Add a `[Route("/")]` attribute to the `Get()` action.</span></span> <span data-ttu-id="095e5-124">La définition de la fonction doit ressembler à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="095e5-124">The function definition will look like the following:</span></span>

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a><span data-ttu-id="095e5-125">Publier l’API Web sur Azure App Service</span><span class="sxs-lookup"><span data-stu-id="095e5-125">Publish the web API to Azure App Service</span></span>

<span data-ttu-id="095e5-126">Procédez comme suit pour publier l’API Web ASP.NET Core dans gestion des API Azure :</span><span class="sxs-lookup"><span data-stu-id="095e5-126">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="095e5-127">Publiez l’application API sur Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="095e5-127">Publish the API app to Azure App Service.</span></span>
1. <span data-ttu-id="095e5-128">Ajoutez une API vide à l’instance du service gestion des API Azure.</span><span class="sxs-lookup"><span data-stu-id="095e5-128">Add a blank API to the Azure API Management service instance.</span></span>
1. <span data-ttu-id="095e5-129">Publiez l’application API Web ASP.NET Core sur l’instance du service gestion des API Azure.</span><span class="sxs-lookup"><span data-stu-id="095e5-129">Publish the ASP.NET Core web API app to the Azure API Management service instance.</span></span>

### <a name="publish-the-api-app-to-azure-app-service"></a><span data-ttu-id="095e5-130">Publier l’application API sur Azure App Service</span><span class="sxs-lookup"><span data-stu-id="095e5-130">Publish the API app to Azure App Service</span></span>

<span data-ttu-id="095e5-131">Procédez comme suit pour publier l’API Web ASP.NET Core dans gestion des API Azure :</span><span class="sxs-lookup"><span data-stu-id="095e5-131">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="095e5-132">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **publier**:</span><span class="sxs-lookup"><span data-stu-id="095e5-132">In **Solution Explorer**, right-click the project and select **Publish**:</span></span>

    ![Menu contextuel ouvert avec le lien Publier mis en surbrillance](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. <span data-ttu-id="095e5-134">Dans la boîte de dialogue **publier** , sélectionnez **Azure** , puis cliquez sur le bouton **suivant** :</span><span class="sxs-lookup"><span data-stu-id="095e5-134">In the **Publish** dialog, select **Azure** and select the **Next** button:</span></span>

    ![Boîte de dialogue Publier](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. <span data-ttu-id="095e5-136">Sélectionnez **Azure App service (Windows)** , puis cliquez sur le bouton **suivant** :</span><span class="sxs-lookup"><span data-stu-id="095e5-136">Select **Azure App Service (Windows)** and select the **Next** button:</span></span>

    ![Boîte de dialogue publier : sélectionnez App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. <span data-ttu-id="095e5-138">Sélectionnez **créer un Azure App service**.</span><span class="sxs-lookup"><span data-stu-id="095e5-138">Select **Create a new Azure App Service**.</span></span>

    ![Boîte de dialogue publier : sélectionner une instance de service Azure](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    <span data-ttu-id="095e5-140">La boîte de dialogue **Créer App Service** apparaît.</span><span class="sxs-lookup"><span data-stu-id="095e5-140">The **Create App Service** dialog appears.</span></span> <span data-ttu-id="095e5-141">Les champs d’entrée **Nom de l’application**, **Groupe de ressources** et **Plan App Service** sont renseignés.</span><span class="sxs-lookup"><span data-stu-id="095e5-141">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="095e5-142">Vous pouvez conserver ces noms ou les changer.</span><span class="sxs-lookup"><span data-stu-id="095e5-142">You can keep these names or change them.</span></span>
1. <span data-ttu-id="095e5-143">Cliquez sur le bouton **Créer**.</span><span class="sxs-lookup"><span data-stu-id="095e5-143">Select the **Create** button.</span></span>

    ![Boîte de dialogue Créer App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

<span data-ttu-id="095e5-145">Une fois la création terminée, la boîte de dialogue est fermée automatiquement et la boîte de dialogue **publier** est de nouveau axée sur le focus.</span><span class="sxs-lookup"><span data-stu-id="095e5-145">After creation is completed, the dialog is automatically closed and the **Publish** dialog gets focus again.</span></span> <span data-ttu-id="095e5-146">L’instance créée est sélectionnée automatiquement.</span><span class="sxs-lookup"><span data-stu-id="095e5-146">The instance that was created is automatically selected.</span></span>

![Boîte de dialogue publier : sélectionner une instance de App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

<span data-ttu-id="095e5-148">À ce stade, vous devez ajouter une API au service gestion des API Azure.</span><span class="sxs-lookup"><span data-stu-id="095e5-148">At this point, you need to add an API to the Azure API Management service.</span></span> <span data-ttu-id="095e5-149">Laissez Visual Studio ouvert pendant que vous effectuez les tâches suivantes.</span><span class="sxs-lookup"><span data-stu-id="095e5-149">Leave Visual Studio open while you complete the following tasks.</span></span>

### <a name="add-an-api-to-azure-api-management"></a><span data-ttu-id="095e5-150">Ajouter une API à gestion des API Azure</span><span class="sxs-lookup"><span data-stu-id="095e5-150">Add an API to Azure API Management</span></span>

1. <span data-ttu-id="095e5-151">Ouvrez l’instance de service gestion des API créée précédemment dans le Portail Azure.</span><span class="sxs-lookup"><span data-stu-id="095e5-151">Open the API Management Service instance created previously in the Azure portal.</span></span> <span data-ttu-id="095e5-152">Sélectionnez le panneau **API** :</span><span class="sxs-lookup"><span data-stu-id="095e5-152">Select the **APIs** blade:</span></span>

  ![Panneau API sélectionné à partir de l’instance du service gestion des API](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. <span data-ttu-id="095e5-154">Sélectionnez les 3 points en regard de l' **API Echo** , puis sélectionnez **supprimer** dans le menu contextuel pour la supprimer.</span><span class="sxs-lookup"><span data-stu-id="095e5-154">Select the 3 dots next to **Echo API** and then select **Delete** from the pop-up menu to remove it.</span></span>

  ![Suppression de l’API Echo de l’instance du service gestion des API](publish-to-azure-api-management-using-vs/_static/portal_delete_echo.png)

1. <span data-ttu-id="095e5-156">Dans le panneau **Ajouter une nouvelle API** , sélectionnez la vignette **API vide** :</span><span class="sxs-lookup"><span data-stu-id="095e5-156">From the **Add a new API** panel, select the **Blank API** tile:</span></span>

  ![Écran présentant la vignette de l’API vide mise en surbrillance](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. <span data-ttu-id="095e5-158">Entrez les valeurs suivantes dans la boîte de dialogue **créer une API vide** qui s’affiche :</span><span class="sxs-lookup"><span data-stu-id="095e5-158">Enter the following values in the **Create a blank API** dialog that appears:</span></span>    

    - <span data-ttu-id="095e5-159">**Nom complet**: *WeatherForecasts*</span><span class="sxs-lookup"><span data-stu-id="095e5-159">**Display Name**: *WeatherForecasts*</span></span>
    - <span data-ttu-id="095e5-160">**Nom**: *weatherforecasts*</span><span class="sxs-lookup"><span data-stu-id="095e5-160">**Name**: *weatherforecasts*</span></span>
    - <span data-ttu-id="095e5-161">**Suffixe** de l’URL de l’API : *v1*</span><span class="sxs-lookup"><span data-stu-id="095e5-161">**API Url suffix**: *v1*</span></span>
    - <span data-ttu-id="095e5-162">Laissez le champ **URL du service Web** vide.</span><span class="sxs-lookup"><span data-stu-id="095e5-162">Leave the **Web service URL** field empty.</span></span>

1. <span data-ttu-id="095e5-163">Cliquez sur le bouton **Créer**.</span><span class="sxs-lookup"><span data-stu-id="095e5-163">Select the **Create** button.</span></span>

    ![Capture d’écran de la boîte de dialogue créer une API vide](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

<span data-ttu-id="095e5-165">L’API vide est créée.</span><span class="sxs-lookup"><span data-stu-id="095e5-165">The blank API is created.</span></span>

![Capture d’écran d’une API vide dans le portail de gestion des API](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a><span data-ttu-id="095e5-167">Publier l’API Web ASP.NET Core dans gestion des API Azure</span><span class="sxs-lookup"><span data-stu-id="095e5-167">Publish the ASP.NET Core web API to Azure API Management</span></span>

1. <span data-ttu-id="095e5-168">Revenez à Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="095e5-168">Switch back to Visual Studio.</span></span> <span data-ttu-id="095e5-169">La boîte de dialogue **publier** doit toujours être ouverte là où vous l’avez laissée.</span><span class="sxs-lookup"><span data-stu-id="095e5-169">The **Publish** dialog should still be open where you left off before.</span></span>
1. <span data-ttu-id="095e5-170">Sélectionnez la Azure App Service que vous venez de publier pour la mettre en surbrillance.</span><span class="sxs-lookup"><span data-stu-id="095e5-170">Select the newly published Azure App Service so it's highlighted.</span></span>
1. <span data-ttu-id="095e5-171">Sélectionnez le bouton **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="095e5-171">Select the **Next** button.</span></span>

    ![Capture d’écran de la boîte de dialogue publier avec l’application App service sélectionnée](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. <span data-ttu-id="095e5-173">La boîte de dialogue affiche maintenant le service gestion des API Azure créé précédemment.</span><span class="sxs-lookup"><span data-stu-id="095e5-173">The dialog now shows the Azure API Management service created before.</span></span> <span data-ttu-id="095e5-174">Développez-le et le dossier *API* pour voir l’API vide que vous avez créée.</span><span class="sxs-lookup"><span data-stu-id="095e5-174">Expand it and the *APIs* folder to see the blank API you created.</span></span>
1. <span data-ttu-id="095e5-175">Sélectionnez le nom de l’API vide, puis cliquez sur le bouton **Terminer** .</span><span class="sxs-lookup"><span data-stu-id="095e5-175">Select the blank API's name and select the **Finish** button.</span></span>

    ![Capture d’écran de l’API vide de gestion des API Azure nouvellement créée sélectionnée dans la boîte de dialogue publier](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. <span data-ttu-id="095e5-177">La boîte de dialogue se ferme et un écran de résumé s’affiche avec des informations sur la publication.</span><span class="sxs-lookup"><span data-stu-id="095e5-177">The dialog closes and a summary screen appears with information about the publish.</span></span> <span data-ttu-id="095e5-178">Cliquez sur le bouton **Publier**.</span><span class="sxs-lookup"><span data-stu-id="095e5-178">Select the **Publish** button.</span></span>

    ![Capture d’écran de Visual Studio avec le profil de publication affiché](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    <span data-ttu-id="095e5-180">L’API Web sera publiée dans Azure App Service et dans gestion des API Azure.</span><span class="sxs-lookup"><span data-stu-id="095e5-180">The web API will publish to both Azure App Service and Azure API Management.</span></span> <span data-ttu-id="095e5-181">Une nouvelle fenêtre de navigateur s’affiche et affiche l’API exécutée dans Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="095e5-181">A new browser window will appear and show the API running in Azure App Service.</span></span> <span data-ttu-id="095e5-182">Vous pouvez fermer cette fenêtre.</span><span class="sxs-lookup"><span data-stu-id="095e5-182">You can close that window.</span></span>

1. <span data-ttu-id="095e5-183">Revenez à l’instance de gestion des API Azure dans le Portail Azure.</span><span class="sxs-lookup"><span data-stu-id="095e5-183">Switch back to the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="095e5-184">Actualisez la fenêtre du navigateur.</span><span class="sxs-lookup"><span data-stu-id="095e5-184">Refresh the browser window.</span></span>
1. <span data-ttu-id="095e5-185">Sélectionnez l’API vide que vous avez créée dans les étapes précédentes.</span><span class="sxs-lookup"><span data-stu-id="095e5-185">Select the blank API you created in the preceding steps.</span></span> <span data-ttu-id="095e5-186">Elle est maintenant remplie et vous pouvez explorer.</span><span class="sxs-lookup"><span data-stu-id="095e5-186">It's now populated and you can explore around.</span></span>

    ![Capture d’écran de l’API remplie dans gestion des API Azure](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a><span data-ttu-id="095e5-188">Configurer le nom de l’API publiée</span><span class="sxs-lookup"><span data-stu-id="095e5-188">Configure the published API name</span></span>

<span data-ttu-id="095e5-189">Notez que le nom de l’API est différent de celui que vous avez nommé.</span><span class="sxs-lookup"><span data-stu-id="095e5-189">Notice the name of the API is different than what you named it.</span></span> <span data-ttu-id="095e5-190">L’API publiée est nommée *WeatherAPI*; Toutefois, vous l’avez nommé *WeatherForecasts* quand vous l’avez créé.</span><span class="sxs-lookup"><span data-stu-id="095e5-190">The published API is named *WeatherAPI*; however, you named it *WeatherForecasts* when you created it.</span></span> <span data-ttu-id="095e5-191">Pour corriger le nom, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="095e5-191">Complete the following steps to fix the name:</span></span>

![Capture d’écran de l’API remplie avec des noms différents mis en surbrillance](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. <span data-ttu-id="095e5-193">Supprimez la ligne suivante de la `Startup.ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="095e5-193">Delete the following line from the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. <span data-ttu-id="095e5-194">Ajoutez le code suivant à la méthode `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="095e5-194">Add the following code to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("v1", new Microsoft.OpenApi.Models.OpenApiInfo
        {
            Title = "WeatherForecasts",
            Version = "v1"
        });
    });
    ```

1. <span data-ttu-id="095e5-195">Ouvrez le profil de publication nouvellement créé.</span><span class="sxs-lookup"><span data-stu-id="095e5-195">Open the newly created publish profile.</span></span> <span data-ttu-id="095e5-196">Il se trouve dans **Explorateur de solutions** dans le dossier *Propriétés/PublishProfiles* .</span><span class="sxs-lookup"><span data-stu-id="095e5-196">It can be found from **Solution Explorer** in the *Properties/PublishProfiles* folder.</span></span>

    ![Capture d’écran montrant l’emplacement du fichier de profil de publication mis en surbrillance](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. <span data-ttu-id="095e5-198">Remplacez la `<OpenAPIDocumentName>` valeur de l’élément `v1` par `WeatherForecasts` .</span><span class="sxs-lookup"><span data-stu-id="095e5-198">Change the `<OpenAPIDocumentName>` element's value from `v1` to `WeatherForecasts`.</span></span>

    ![capture d’écran des modifications nécessaires pour le profil de publication](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. <span data-ttu-id="095e5-200">Republiez l’API Web ASP.NET Core et ouvrez l’instance gestion des API Azure dans le Portail Azure.</span><span class="sxs-lookup"><span data-stu-id="095e5-200">Republish the ASP.NET Core web API and open the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="095e5-201">Actualisez la page dans votre navigateur.</span><span class="sxs-lookup"><span data-stu-id="095e5-201">Refresh the page in your browser.</span></span> <span data-ttu-id="095e5-202">Vous verrez que le nom de l’API est désormais correct.</span><span class="sxs-lookup"><span data-stu-id="095e5-202">You'll see the name of the API is now correct.</span></span>

    ![Capture d’écran de l’API terminée dans le portail](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a><span data-ttu-id="095e5-204">Vérifier que l’API Web fonctionne</span><span class="sxs-lookup"><span data-stu-id="095e5-204">Verify the web API is working</span></span>

<span data-ttu-id="095e5-205">Vous pouvez tester l’API Web ASP.NET Core déployée dans gestion des API Azure à partir de la Portail Azure en procédant comme suit :</span><span class="sxs-lookup"><span data-stu-id="095e5-205">You can test the deployed ASP.NET Core web API in Azure API Management from the Azure portal with the following steps:</span></span>

1. <span data-ttu-id="095e5-206">Ouvrez l’onglet **Test**.</span><span class="sxs-lookup"><span data-stu-id="095e5-206">Open the **Test** tab.</span></span>
1. <span data-ttu-id="095e5-207">Sélectionnez **/** ou l’opération de **récupération** .</span><span class="sxs-lookup"><span data-stu-id="095e5-207">Select **/** or the **Get** operation.</span></span>
1. <span data-ttu-id="095e5-208">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="095e5-208">Select **Send**.</span></span>

    ![Capture d’écran du portail avant le test](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

<span data-ttu-id="095e5-210">Une réponse correcte se présente comme suit :</span><span class="sxs-lookup"><span data-stu-id="095e5-210">A successful response will look like the following:</span></span>

![Capture d’écran d’une réponse correcte de la gestion des API](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a><span data-ttu-id="095e5-212">Nettoyage</span><span class="sxs-lookup"><span data-stu-id="095e5-212">Clean up</span></span>

<span data-ttu-id="095e5-213">Une fois que vous avez fini de tester l’application, accédez au [portail Azure](https://portal.azure.com/) et supprimez l’application.</span><span class="sxs-lookup"><span data-stu-id="095e5-213">When you've finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

1. <span data-ttu-id="095e5-214">Sélectionnez **Groupes de ressources**, puis sélectionnez le groupe de ressources que vous avez créé.</span><span class="sxs-lookup"><span data-stu-id="095e5-214">Select **Resource groups**, then select the resource group you created.</span></span>

    ![Portail Azure : Groupes de ressources dans le menu de la barre latérale](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. <span data-ttu-id="095e5-216">Dans la page **Groupes de ressources**, sélectionnez **Supprimer**.</span><span class="sxs-lookup"><span data-stu-id="095e5-216">In the **Resource groups** page, select **Delete**.</span></span>

    ![Portail Azure : page Groupes de ressources](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. <span data-ttu-id="095e5-218">Entrez le nom du groupe de ressources et sélectionnez **supprimer**.</span><span class="sxs-lookup"><span data-stu-id="095e5-218">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="095e5-219">Votre application et toutes les autres ressources créées dans ce didacticiel sont désormais supprimées d’Azure.</span><span class="sxs-lookup"><span data-stu-id="095e5-219">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

## <a name="next-steps"></a><span data-ttu-id="095e5-220">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="095e5-220">Next steps</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="095e5-221">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="095e5-221">Additional resources</span></span>

- [<span data-ttu-id="095e5-222">Gestion des API Azure</span><span class="sxs-lookup"><span data-stu-id="095e5-222">Azure API Management</span></span>](/azure/api-management/api-management-key-concepts)
- [<span data-ttu-id="095e5-223">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="095e5-223">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
