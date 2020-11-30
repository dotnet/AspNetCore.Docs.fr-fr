---
title: Publier une API Web ASP.NET Core dans gestion des API Azure avec Visual Studio
author: codemillmatt
description: Découvrez comment publier une API Web ASP.NET Core dans gestion des API Azure à l’aide de Visual Studio.
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 11/22/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: 395b5981a3018486235c38f032893f985ab71383
ms.sourcegitcommit: 619200f2981656ede6d89adb6a22ad1a0e16da22
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96332209"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a>Publier une API Web ASP.NET Core dans gestion des API Azure avec Visual Studio

Par [Matt Soucoup](https://twitter.com/codemillmatt)

## <a name="set-up"></a>Configurer

- Ouvrez un [compte Azure gratuit](https://azure.microsoft.com/free/dotnet/) si vous n’en avez pas.
- [Créez une instance de gestion des API Azure](/azure/api-management/get-started-create-service-instance) si vous ne l’avez pas déjà fait.
- [Créez un projet d’application API Web](xref:tutorials/first-web-api#create-a-web-project).

## <a name="configure-the-app"></a>Configurer l’application

L’ajout de définitions Swagger à l’API Web ASP.NET Core permet à la gestion des API Azure de lire les définitions d’API de l’application. Effectuez ensuite les tâches suivantes.

### <a name="add-swagger"></a>Ajouter Swagger

1. Ajoutez le package NuGet [Swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) au projet d’API Web ASP.net Core :

    ![Capture d’écran pour configurer la boîte de dialogue NuGet](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. Ajoutez la ligne suivante à la méthode `Startup.ConfigureServices` :
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. Ajoutez la ligne suivante à la méthode `Startup.Configure` :

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a>Modifier le routage d’API

Ensuite, vous allez modifier la structure d’URL nécessaire pour accéder à l' `Get` action de `WeatherForecastController` . Suivez les étapes ci-dessous :

1. Ouvrez le fichier *WeatherForecastController.cs* .
1. Supprimez l' `[Route("[controller]")]` attribut de niveau classe. La définition de la classe doit ressembler à ce qui suit :

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. Ajoutez un `[Route("/")]` attribut à l' `Get()` action. La définition de la fonction doit ressembler à ce qui suit :

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a>Publier l’API Web sur Azure App Service

Procédez comme suit pour publier l’API Web ASP.NET Core dans gestion des API Azure :

1. Publiez l’application API sur Azure App Service.
1. Ajoutez une API vide à l’instance du service gestion des API Azure.
1. Publiez l’application API Web ASP.NET Core sur l’instance du service gestion des API Azure.

### <a name="publish-the-api-app-to-azure-app-service"></a>Publier l’application API sur Azure App Service

Procédez comme suit pour publier l’API Web ASP.NET Core dans gestion des API Azure :

1. Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **publier**:

    ![Menu contextuel ouvert avec le lien Publier mis en surbrillance](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. Dans la boîte de dialogue **publier** , sélectionnez **Azure** , puis cliquez sur le bouton **suivant** :

    ![Boîte de dialogue Publier](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. Sélectionnez **Azure App service (Windows)** , puis cliquez sur le bouton **suivant** :

    ![Boîte de dialogue publier : sélectionnez App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. Sélectionnez **créer un Azure App service**.

    ![Boîte de dialogue publier : sélectionner une instance de service Azure](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    La boîte de dialogue **Créer App Service** apparaît. Les champs d’entrée **Nom de l’application**, **Groupe de ressources** et **Plan App Service** sont renseignés. Vous pouvez conserver ces noms ou les changer.
1. Cliquez sur le bouton **Créer**.

    ![Boîte de dialogue Créer App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

Une fois la création terminée, la boîte de dialogue est fermée automatiquement et la boîte de dialogue **publier** est de nouveau axée sur le focus. L’instance créée est sélectionnée automatiquement.

![Boîte de dialogue publier : sélectionner une instance de App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

À ce stade, vous devez ajouter une API au service gestion des API Azure. Laissez Visual Studio ouvert pendant que vous effectuez les tâches suivantes.

### <a name="add-an-api-to-azure-api-management"></a>Ajouter une API à gestion des API Azure

1. Ouvrez l’instance de service gestion des API créée précédemment dans le Portail Azure. Sélectionnez le panneau **API** :

  ![Panneau API sélectionné à partir de l’instance du service gestion des API](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. Sélectionnez les 3 points en regard de l' **API Echo** , puis sélectionnez **supprimer** dans le menu contextuel pour la supprimer.

  ![Suppression de l’API Echo de l’instance du service gestion des API](publish-to-azure-api-management-using-vs/_static/portal_delete_echo.png)

1. Dans le panneau **Ajouter une nouvelle API** , sélectionnez la vignette **API vide** :

  ![Écran présentant la vignette de l’API vide mise en surbrillance](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. Entrez les valeurs suivantes dans la boîte de dialogue **créer une API vide** qui s’affiche :    

    - **Nom complet**: *WeatherForecasts*
    - **Nom**: *weatherforecasts*
    - **Suffixe** de l’URL de l’API : *v1*
    - Laissez le champ **URL du service Web** vide.

1. Cliquez sur le bouton **Créer**.

    ![Capture d’écran de la boîte de dialogue créer une API vide](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

L’API vide est créée.

![Capture d’écran d’une API vide dans le portail de gestion des API](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a>Publier l’API Web ASP.NET Core dans gestion des API Azure

1. Revenez à Visual Studio. La boîte de dialogue **publier** doit toujours être ouverte là où vous l’avez laissée.
1. Sélectionnez la Azure App Service que vous venez de publier pour la mettre en surbrillance.
1. Sélectionnez le bouton **Suivant**.

    ![Capture d’écran de la boîte de dialogue publier avec l’application App service sélectionnée](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. La boîte de dialogue affiche maintenant le service gestion des API Azure créé précédemment. Développez-le et le dossier *API* pour voir l’API vide que vous avez créée.
1. Sélectionnez le nom de l’API vide, puis cliquez sur le bouton **Terminer** .

    ![Capture d’écran de l’API vide de gestion des API Azure nouvellement créée sélectionnée dans la boîte de dialogue publier](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. La boîte de dialogue se ferme et un écran de résumé s’affiche avec des informations sur la publication. Cliquez sur le bouton **Publier**.

    ![Capture d’écran de Visual Studio avec le profil de publication affiché](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    L’API Web sera publiée dans Azure App Service et dans gestion des API Azure. Une nouvelle fenêtre de navigateur s’affiche et affiche l’API exécutée dans Azure App Service. Vous pouvez fermer cette fenêtre.

1. Revenez à l’instance de gestion des API Azure dans le Portail Azure.
1. Actualisez la fenêtre du navigateur.
1. Sélectionnez l’API vide que vous avez créée dans les étapes précédentes. Elle est maintenant remplie et vous pouvez explorer.

    ![Capture d’écran de l’API remplie dans gestion des API Azure](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a>Configurer le nom de l’API publiée

Notez que le nom de l’API est différent de celui que vous avez nommé. L’API publiée est nommée *WeatherAPI*; Toutefois, vous l’avez nommé *WeatherForecasts* quand vous l’avez créé. Pour corriger le nom, procédez comme suit :

![Capture d’écran de l’API remplie avec des noms différents mis en surbrillance](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. Supprimez la ligne suivante de la `Startup.ConfigureServices` méthode :
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. Ajoutez le code suivant à la méthode `Startup.ConfigureServices` :
    
    ```csharp
    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("WeatherForecasts", new Microsoft.OpenApi.Models.OpenApiInfo
        {
            Title = "Weather Forecasts",
            Version = "v1"
        });
    });
    ```

1. Ouvrez le profil de publication nouvellement créé. Il se trouve dans **Explorateur de solutions** dans le dossier *Propriétés/PublishProfiles* .

    ![Capture d’écran montrant l’emplacement du fichier de profil de publication mis en surbrillance](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. Remplacez la `<OpenAPIDocumentName>` valeur de l’élément `v1` par `WeatherForecasts` .

    ![capture d’écran des modifications nécessaires pour le profil de publication](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. Republiez l’API Web ASP.NET Core et ouvrez l’instance gestion des API Azure dans le Portail Azure.
1. Actualisez la page dans votre navigateur. Vous verrez que le nom de l’API est désormais correct.

    ![Capture d’écran de l’API terminée dans le portail](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a>Vérifier que l’API Web fonctionne

Vous pouvez tester l’API Web ASP.NET Core déployée dans gestion des API Azure à partir de la Portail Azure en procédant comme suit :

1. Ouvrez l’onglet **Test**.
1. Sélectionnez **/** ou l’opération de **récupération** .
1. Sélectionnez **Envoyer**.

    ![Capture d’écran du portail avant le test](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

Une réponse correcte se présente comme suit :

![Capture d’écran d’une réponse correcte de la gestion des API](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a>Nettoyage

Une fois que vous avez fini de tester l’application, accédez au [portail Azure](https://portal.azure.com/) et supprimez l’application.

1. Sélectionnez **Groupes de ressources**, puis sélectionnez le groupe de ressources que vous avez créé.

    ![Portail Azure : Groupes de ressources dans le menu de la barre latérale](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. Dans la page **Groupes de ressources**, sélectionnez **Supprimer**.

    ![Portail Azure : page Groupes de ressources](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. Entrez le nom du groupe de ressources et sélectionnez **supprimer**. Votre application et toutes les autres ressources créées dans ce didacticiel sont désormais supprimées d’Azure.

## <a name="next-steps"></a>Étapes suivantes

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Ressources supplémentaires

- [Gestion des API Azure](/azure/api-management/api-management-key-concepts)
- [Azure App Service](/azure/app-service/app-service-web-overview)
