# <a name="visual-studio"></a>[<span data-ttu-id="9a04b-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a04b-101">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9a04b-102">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="9a04b-102">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="9a04b-103">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="9a04b-103">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="9a04b-104">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="9a04b-104">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="9a04b-105">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="9a04b-105">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="9a04b-106">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="9a04b-106">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="9a04b-107">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="9a04b-107">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-code"></a>[<span data-ttu-id="9a04b-108">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a04b-108">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="9a04b-109">Appuyez sur **Ctrl+F5** pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="9a04b-109">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="9a04b-110">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="9a04b-110">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="9a04b-111">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="9a04b-111">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="9a04b-112">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="9a04b-112">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="9a04b-113">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="9a04b-113">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9a04b-114">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9a04b-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="9a04b-115">Dans Visual Studio, appuyez sur **OPT-CMD-Return** pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="9a04b-115">From Visual Studio, press **Opt-Cmd-Return** to run without the debugger.</span></span> <span data-ttu-id="9a04b-116">Vous pouvez également accéder à la barre de menus et accéder à **exécuter>démarrer sans débogage**.</span><span class="sxs-lookup"><span data-stu-id="9a04b-116">Alternatively, navigate to the menu bar and go to **Run>Start Without Debugging**.</span></span>

  <span data-ttu-id="9a04b-117">Visual Studio démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="9a04b-117">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---
