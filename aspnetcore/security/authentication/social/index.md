---
title: Authentification à l’aide de fournisseurs externes (Facebook, Google et autres) dans ASP.NET Core
author: rick-anderson
description: Ce didacticiel montre comment créer une application ASP.NET Core à l’aide d’OAuth 2,0 avec des fournisseurs d’authentification externes.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
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
uid: security/authentication/social/index
ms.openlocfilehash: f7bc7c6a14d014f8c3c283fb09718b9191b7b478
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035825"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="8fb75-103">Authentification à l’aide de fournisseurs externes (Facebook, Google et autres) dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8fb75-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="8fb75-104">Par [Valeriy Novytskyy](https://github.com/01binary) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8fb75-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8fb75-105">Ce didacticiel montre comment créer une application ASP.NET Core qui permet aux utilisateurs de se connecter à l’aide d’OAuth 2,0 avec les informations d’identification des fournisseurs d’authentification externes.</span><span class="sxs-lookup"><span data-stu-id="8fb75-105">This tutorial demonstrates how to build an ASP.NET Core app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="8fb75-106">Les fournisseurs [Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins)et [Microsoft](xref:security/authentication/microsoft-logins) sont traités dans les sections suivantes et utilisent le projet de démarrage créé dans cet article.</span><span class="sxs-lookup"><span data-stu-id="8fb75-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections and use the starter project created in this article.</span></span> <span data-ttu-id="8fb75-107">D’autres fournisseurs sont disponibles dans des packages tiers, comme [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) et [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span><span class="sxs-lookup"><span data-stu-id="8fb75-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

<span data-ttu-id="8fb75-108">Permettre aux utilisateurs de se connecter avec leurs informations d’identification existantes :</span><span class="sxs-lookup"><span data-stu-id="8fb75-108">Enabling users to sign in with their existing credentials:</span></span>

* <span data-ttu-id="8fb75-109">Est pratique pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="8fb75-109">Is convenient for the users.</span></span>
* <span data-ttu-id="8fb75-110">Transfère une grande partie des complexités de la gestion du processus de connexion à un tiers.</span><span class="sxs-lookup"><span data-stu-id="8fb75-110">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span>

<span data-ttu-id="8fb75-111">Pour obtenir des exemples de la façon dont les connexions des réseaux sociaux peuvent amener du trafic et des conversions de clients, consultez les études de cas par [Facebook](https://www.facebook.com/unsupportedbrowser) et [Twitter](https://dev.twitter.com/resources/case-studies).</span><span class="sxs-lookup"><span data-stu-id="8fb75-111">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="8fb75-112">Créer un projet ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8fb75-112">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8fb75-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8fb75-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8fb75-114">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="8fb75-114">Create a new project.</span></span>
* <span data-ttu-id="8fb75-115">Sélectionnez **Nouvelle application web ASP.NET Core** et **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="8fb75-115">Select **ASP.NET Core Web Application** and **Next**.</span></span>
* <span data-ttu-id="8fb75-116">Fournissez un **Nom de projet** et confirmez ou changez l’**Emplacement**.</span><span class="sxs-lookup"><span data-stu-id="8fb75-116">Provide a **Project name** and confirm or change the **Location**.</span></span> <span data-ttu-id="8fb75-117">Sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="8fb75-117">Select **Create**.</span></span>
* <span data-ttu-id="8fb75-118">Sélectionnez la dernière version de ASP.NET Core dans la liste déroulante (**ASP.net Core {X. Y}**), puis sélectionnez **application Web**.</span><span class="sxs-lookup"><span data-stu-id="8fb75-118">Select the latest version of ASP.NET Core in the drop-down (**ASP.NET Core {X.Y}**), and then select **Web Application**.</span></span>
* <span data-ttu-id="8fb75-119">Sous **Authentification**, sélectionnez **Changer** et définissez l’authentification sur **Comptes d’utilisateur individuels**.</span><span class="sxs-lookup"><span data-stu-id="8fb75-119">Under **Authentication**, select **Change** and set the authentication to **Individual User Accounts**.</span></span> <span data-ttu-id="8fb75-120">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="8fb75-120">Select **OK**.</span></span>
* <span data-ttu-id="8fb75-121">Dans la fenêtre **Créer une application web ASP.NET Core**, sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="8fb75-121">In the **Create a new ASP.NET Core Web Application** window, select **Create**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8fb75-122">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="8fb75-122">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="8fb75-123">Ouvrez le terminal.</span><span class="sxs-lookup"><span data-stu-id="8fb75-123">Open the terminal.</span></span>  <span data-ttu-id="8fb75-124">Pour Visual Studio Code vous pouvez ouvrir le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="8fb75-124">For Visual Studio Code you can open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="8fb75-125">Accédez à un répertoire (`cd`) destiné à contenir le projet.</span><span class="sxs-lookup"><span data-stu-id="8fb75-125">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="8fb75-126">Pour Windows, exécutez la commande ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="8fb75-126">For Windows, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  <span data-ttu-id="8fb75-127">Pour macOS et Linux, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="8fb75-127">For macOS and Linux, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * <span data-ttu-id="8fb75-128">La `dotnet new` commande crée un Razor projet de pages dans le dossier *application Web 1* .</span><span class="sxs-lookup"><span data-stu-id="8fb75-128">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="8fb75-129">`-au Individual` crée le code servant à l’authentification individuelle.</span><span class="sxs-lookup"><span data-stu-id="8fb75-129">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="8fb75-130">`-uld` utilise la base de données locale, une version allégée de SQL Server Express pour Windows.</span><span class="sxs-lookup"><span data-stu-id="8fb75-130">`-uld` uses LocalDB, a lightweight version of SQL Server Express for Windows.</span></span> <span data-ttu-id="8fb75-131">Omettez `-uld` pour utiliser SQLite.</span><span class="sxs-lookup"><span data-stu-id="8fb75-131">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="8fb75-132">La commande `code` ouvre le dossier *WebApp1* dans une nouvelle instance de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="8fb75-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="8fb75-133">Appliquer des migrations</span><span class="sxs-lookup"><span data-stu-id="8fb75-133">Apply migrations</span></span>

* <span data-ttu-id="8fb75-134">Exécutez l’application et sélectionnez le lien **S’inscrire**.</span><span class="sxs-lookup"><span data-stu-id="8fb75-134">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="8fb75-135">Entrez l’adresse e-mail et le mot de passe du nouveau compte, puis sélectionnez **S’inscrire**.</span><span class="sxs-lookup"><span data-stu-id="8fb75-135">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="8fb75-136">Suivez les instructions pour appliquer des migrations.</span><span class="sxs-lookup"><span data-stu-id="8fb75-136">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="8fb75-137">Utilisez SecretManager pour stocker les jetons affectés par les fournisseurs de connexion</span><span class="sxs-lookup"><span data-stu-id="8fb75-137">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="8fb75-138">Les fournisseurs de connexion de réseaux sociaux affectent des jetons **ID d’application** et **Secret de l’application** lors du processus d’inscription.</span><span class="sxs-lookup"><span data-stu-id="8fb75-138">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="8fb75-139">Les noms de jeton exacts varient selon le fournisseur.</span><span class="sxs-lookup"><span data-stu-id="8fb75-139">The exact token names vary by provider.</span></span> <span data-ttu-id="8fb75-140">Ces jetons représentent les informations d’identification que votre application utilise pour accéder à son API.</span><span class="sxs-lookup"><span data-stu-id="8fb75-140">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="8fb75-141">Les jetons constituent les « secrets utilisateur » qui peuvent être liés à la configuration de votre application à l’aide du [Gestionnaire de secret](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="8fb75-141">The tokens constitute the "user secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="8fb75-142">Les secrets de l’utilisateur constituent une alternative plus sécurisée au stockage des jetons dans un fichier de configuration, tel que *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="8fb75-142">User secrets are a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8fb75-143">Secret Manager est uniquement réservé au développement.</span><span class="sxs-lookup"><span data-stu-id="8fb75-143">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="8fb75-144">Vous pouvez stocker et protéger les secrets de test et de production Azure avec le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="8fb75-144">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="8fb75-145">Suivez les étapes de la rubrique [Stockage sécurisé des secrets d’application lors du développement dans ASP.NET Core](xref:security/app-secrets) pour stocker les jetons affectés par chaque fournisseur de connexion ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="8fb75-145">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="8fb75-146">Configurer les fournisseurs de connexion nécessaires à votre application</span><span class="sxs-lookup"><span data-stu-id="8fb75-146">Setup login providers required by your application</span></span>

<span data-ttu-id="8fb75-147">Utilisez les rubriques suivantes pour configurer votre application pour utiliser ces différents fournisseurs :</span><span class="sxs-lookup"><span data-stu-id="8fb75-147">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="8fb75-148">Instructions pour [Facebook](xref:security/authentication/facebook-logins)</span><span class="sxs-lookup"><span data-stu-id="8fb75-148">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="8fb75-149">Instructions pour [Twitter](xref:security/authentication/twitter-logins)</span><span class="sxs-lookup"><span data-stu-id="8fb75-149">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="8fb75-150">Instructions pour [Google](xref:security/authentication/google-logins)</span><span class="sxs-lookup"><span data-stu-id="8fb75-150">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="8fb75-151">Instructions pour [Microsoft](xref:security/authentication/microsoft-logins)</span><span class="sxs-lookup"><span data-stu-id="8fb75-151">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="8fb75-152">Instructions pour les [autres fournisseurs](xref:security/authentication/otherlogins)</span><span class="sxs-lookup"><span data-stu-id="8fb75-152">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="8fb75-153">Définition facultative d’un mot de passe</span><span class="sxs-lookup"><span data-stu-id="8fb75-153">Optionally set password</span></span>

<span data-ttu-id="8fb75-154">Quand vous vous inscrivez auprès d’un fournisseur de connexion externe, vous n’avez pas de mot de passe inscrit auprès de l’application.</span><span class="sxs-lookup"><span data-stu-id="8fb75-154">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="8fb75-155">Ceci vous évite de devoir créer et mémoriser un mot de passe pour le site, mais vous rend aussi dépendant du fournisseur de connexion externe.</span><span class="sxs-lookup"><span data-stu-id="8fb75-155">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="8fb75-156">Si le fournisseur de connexion externe n’est pas disponible, vous ne pouvez pas vous connecter au site web.</span><span class="sxs-lookup"><span data-stu-id="8fb75-156">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="8fb75-157">Pour créer un mot de passe et vous connecter à l’aide de l’e-mail que vous avez défini lors du processus de connexion avec des fournisseurs externes :</span><span class="sxs-lookup"><span data-stu-id="8fb75-157">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="8fb75-158">Sélectionnez le lien **Bonjour &lt;alias d’e-mail&gt;** en haut à droite pour accéder à la vue **Gérer**.</span><span class="sxs-lookup"><span data-stu-id="8fb75-158">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Vue Gérer de l’application web](index/_static/pass1a.png)

* <span data-ttu-id="8fb75-160">Sélectionnez **Créer**</span><span class="sxs-lookup"><span data-stu-id="8fb75-160">Select **Create**</span></span>

![Page Définir votre mot de passe](index/_static/pass2a.png)

* <span data-ttu-id="8fb75-162">Définissez un mot de passe valide à utiliser pour vous connecter avec votre e-mail.</span><span class="sxs-lookup"><span data-stu-id="8fb75-162">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8fb75-163">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="8fb75-163">Next steps</span></span>

* <span data-ttu-id="8fb75-164">Pour plus d’informations sur la personnalisation des boutons de connexion, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/10563) .</span><span class="sxs-lookup"><span data-stu-id="8fb75-164">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/10563) for information on how to customize the login buttons.</span></span>
* <span data-ttu-id="8fb75-165">Cet article a présenté l’authentification externe et expliqué les prérequis nécessaires pour ajouter des connexions externes à votre application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8fb75-165">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>
* <span data-ttu-id="8fb75-166">Référencez les pages spécifiques au fournisseur pour configurer les connexions pour les fournisseurs nécessaires à votre application.</span><span class="sxs-lookup"><span data-stu-id="8fb75-166">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>
* <span data-ttu-id="8fb75-167">Vous souhaiterez peut-être conserver des données supplémentaires relatives à l’utilisateur et à ses jetons d’accès et d’actualisation.</span><span class="sxs-lookup"><span data-stu-id="8fb75-167">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="8fb75-168">Pour plus d’informations, consultez <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="8fb75-168">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
