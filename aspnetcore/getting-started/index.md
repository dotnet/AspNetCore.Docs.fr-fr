---
title: Bien démarrer avec ASP.NET Core
author: rick-anderson
description: Bref tutoriel qui crée et exécute une application Hello World de base à l’aide d’ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: getting-started
ms.openlocfilehash: fef5ae525a7c01d0ea7733e990233f413aac61a7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057789"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="d185a-103">Tutoriel : Bien démarrer avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d185a-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="d185a-104">Ce didacticiel montre comment créer et exécuter une application Web ASP.NET Core à l’aide de l’CLI .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d185a-104">This tutorial shows how to create and run an ASP.NET Core web app using the .NET Core CLI.</span></span>

<span data-ttu-id="d185a-105">Vous découvrirez comment effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="d185a-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d185a-106">Créer un projet application web.</span><span class="sxs-lookup"><span data-stu-id="d185a-106">Create a web app project.</span></span>
> * <span data-ttu-id="d185a-107">Approuver le certificat de développement.</span><span class="sxs-lookup"><span data-stu-id="d185a-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="d185a-108">Exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="d185a-108">Run the app.</span></span>
> * <span data-ttu-id="d185a-109">Modifiez une :::no-loc(Razor)::: page.</span><span class="sxs-lookup"><span data-stu-id="d185a-109">Edit a :::no-loc(Razor)::: page.</span></span>

<span data-ttu-id="d185a-110">À la fin du tutoriel, vous disposerez d’une application web qui fonctionne et s’exécute sur votre machine locale.</span><span class="sxs-lookup"><span data-stu-id="d185a-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Page d’accueil d’application web](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="d185a-112">Prérequis</span><span class="sxs-lookup"><span data-stu-id="d185a-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="d185a-113">Créer un projet d’application web</span><span class="sxs-lookup"><span data-stu-id="d185a-113">Create a web app project</span></span>

<span data-ttu-id="d185a-114">Ouvrez un interpréteur de commandes, puis entrez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="d185a-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="d185a-115">La commande précédente :</span><span class="sxs-lookup"><span data-stu-id="d185a-115">The preceding command:</span></span>

* <span data-ttu-id="d185a-116">Crée une application Web.</span><span class="sxs-lookup"><span data-stu-id="d185a-116">Creates a new web app.</span></span>  
* <span data-ttu-id="d185a-117">Le `-o aspnetcoreapp` paramètre crée un répertoire nommé *aspnetcoreapp* avec les fichiers sources de l’application.</span><span class="sxs-lookup"><span data-stu-id="d185a-117">The `-o aspnetcoreapp` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="d185a-118">Approuver le certificat de développement</span><span class="sxs-lookup"><span data-stu-id="d185a-118">Trust the development certificate</span></span>

<span data-ttu-id="d185a-119">Approuvez le certificat de développement HTTPS :</span><span class="sxs-lookup"><span data-stu-id="d185a-119">Trust the HTTPS development certificate:</span></span>

# <a name="windows"></a>[<span data-ttu-id="d185a-120">Windows</span><span class="sxs-lookup"><span data-stu-id="d185a-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="d185a-121">La commande précédente affiche la boîte de dialogue suivante :</span><span class="sxs-lookup"><span data-stu-id="d185a-121">The preceding command displays the following dialog:</span></span>

![Boîte de dialogue Avertissement de sécurité](~/getting-started/_static/cert.png)

<span data-ttu-id="d185a-123">Sélectionnez **Oui** si vous acceptez d’approuver le certificat de développement.</span><span class="sxs-lookup"><span data-stu-id="d185a-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macos"></a>[<span data-ttu-id="d185a-124">macOS</span><span class="sxs-lookup"><span data-stu-id="d185a-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="d185a-125">La commande précédente affiche le message suivant :</span><span class="sxs-lookup"><span data-stu-id="d185a-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="d185a-126">*L’approbation du certificat de développement https a été demandée. Si le certificat n’est pas déjà approuvé, nous allons exécuter la commande suivante :*`'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="d185a-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted, we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="d185a-127">Cette commande risque de vous demander votre mot de passe afin d’installer le certificat sur le trousseau système.</span><span class="sxs-lookup"><span data-stu-id="d185a-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="d185a-128">Entrez votre mot de passe si vous acceptez d’approuver le certificat de développement.</span><span class="sxs-lookup"><span data-stu-id="d185a-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linux"></a>[<span data-ttu-id="d185a-129">Linux</span><span class="sxs-lookup"><span data-stu-id="d185a-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="d185a-130">Consultez la documentation de votre distribution Linux pour savoir comment approuver le certificat de développement HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d185a-130">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="d185a-131">Pour plus d’informations, consultez [Approuver le certificat de développement ASP.NET Core HTTPS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span><span class="sxs-lookup"><span data-stu-id="d185a-131">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="d185a-132">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="d185a-132">Run the app</span></span>

<span data-ttu-id="d185a-133">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="d185a-133">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

<span data-ttu-id="d185a-134">Une fois que l’interface de commande indique que l’application a démarré, accédez à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="d185a-134">After the command shell indicates that the app has started, browse to `https://localhost:5001`.</span></span>

## <a name="edit-a-no-locrazor-page"></a><span data-ttu-id="d185a-135">Modifier une :::no-loc(Razor)::: page</span><span class="sxs-lookup"><span data-stu-id="d185a-135">Edit a :::no-loc(Razor)::: page</span></span>

<span data-ttu-id="d185a-136">Ouvrez *pages/index. cshtml* et modifiez et enregistrez la page avec le balisage en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="d185a-136">Open *Pages/Index.cshtml* and modify and save the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="d185a-137">Accédez à `https://localhost:5001` , actualisez la page et vérifiez que les modifications sont affichées.</span><span class="sxs-lookup"><span data-stu-id="d185a-137">Browse to `https://localhost:5001`, refresh the page, and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d185a-138">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="d185a-138">Next steps</span></span>

<span data-ttu-id="d185a-139">Dans ce didacticiel, vous avez appris à :</span><span class="sxs-lookup"><span data-stu-id="d185a-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d185a-140">Créer un projet application web.</span><span class="sxs-lookup"><span data-stu-id="d185a-140">Create a web app project.</span></span>
> * <span data-ttu-id="d185a-141">Approuver le certificat de développement.</span><span class="sxs-lookup"><span data-stu-id="d185a-141">Trust the development certificate.</span></span>
> * <span data-ttu-id="d185a-142">Exécutez le projet.</span><span class="sxs-lookup"><span data-stu-id="d185a-142">Run the project.</span></span>
> * <span data-ttu-id="d185a-143">Apportez la modification souhaitée.</span><span class="sxs-lookup"><span data-stu-id="d185a-143">Make a change.</span></span>

<span data-ttu-id="d185a-144">Pour en savoir plus sur ASP.NET Core, consultez le parcours d’apprentissage recommandé dans la présentation :</span><span class="sxs-lookup"><span data-stu-id="d185a-144">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
