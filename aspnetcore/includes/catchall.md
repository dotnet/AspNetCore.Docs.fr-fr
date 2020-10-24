::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

> [!WARNING]
> <span data-ttu-id="c2353-101">Un paramètre **catch-all** peut faire correspondre les itinéraires de manière incorrecte en raison d’un [bogue](https://github.com/dotnet/aspnetcore/issues/18677) dans le routage.</span><span class="sxs-lookup"><span data-stu-id="c2353-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="c2353-102">Les applications affectées par ce bogue présentent les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="c2353-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="c2353-103">Un itinéraire de rattrapage, par exemple, `{**slug}"`</span><span class="sxs-lookup"><span data-stu-id="c2353-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="c2353-104">L’itinéraire catch-all ne parvient pas à faire correspondre les demandes auxquelles il doit correspondre.</span><span class="sxs-lookup"><span data-stu-id="c2353-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="c2353-105">La suppression d’autres itinéraires rend le fonctionnement de l’itinéraire de rattrapage.</span><span class="sxs-lookup"><span data-stu-id="c2353-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="c2353-106">Consultez les bogues GitHub [18677](https://github.com/dotnet/aspnetcore/issues/18677) et [16579](https://github.com/dotnet/aspnetcore/issues/16579) pour obtenir des exemples de cas qui rencontrent ce bogue.</span><span class="sxs-lookup"><span data-stu-id="c2353-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="c2353-107">Un correctif d’abonnement pour ce bogue est contenu dans [.net Core 3.1.301 SDK et versions ultérieures](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="c2353-107">An opt-in fix for this bug is contained in [.NET Core 3.1.301 SDK and later](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="c2353-108">Le code suivant définit un commutateur interne qui corrige ce bogue :</span><span class="sxs-lookup"><span data-stu-id="c2353-108">The following code sets an internal switch that fixes this bug:</span></span>
>
>```csharp
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```

::: moniker-end