# <a name="aspnet-core-url-rewriting-sample"></a><span data-ttu-id="af1fc-101">Exemple de réécriture d’URL ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af1fc-101">ASP.NET Core URL Rewriting Sample</span></span>

<span data-ttu-id="af1fc-102">Cet exemple illustre l’utilisation de l’intergiciel (middleware) de réécriture d’URL ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af1fc-102">This sample illustrates usage of ASP.NET Core URL Rewriting Middleware.</span></span> <span data-ttu-id="af1fc-103">L’application montre les options de redirection et de réécriture d’URL.</span><span class="sxs-lookup"><span data-stu-id="af1fc-103">The app demonstrates URL redirect and URL rewriting options.</span></span>

<span data-ttu-id="af1fc-104">Quand vous exécutez l’exemple, des réponses autres que des réponses de fichier retournent l’URL réécrite ou redirigée quand une des règles est appliquée à une URL de requête.</span><span class="sxs-lookup"><span data-stu-id="af1fc-104">When running the sample, non-file responses return the rewritten or redirected URL when one of the rules is applied to a request URL.</span></span> <span data-ttu-id="af1fc-105">Pour les exemples de fichier XML et texte, le middleware de fichiers statiques délivre le fichier une fois qu’il a réécrit l’URL de requête.</span><span class="sxs-lookup"><span data-stu-id="af1fc-105">For the XML and text file examples, Static File Middleware serves the file after the request URL is rewritten by the middleware.</span></span>

## <a name="examples-in-this-sample"></a><span data-ttu-id="af1fc-106">Exemples dans cet exemple</span><span class="sxs-lookup"><span data-stu-id="af1fc-106">Examples in this sample</span></span>

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - <span data-ttu-id="af1fc-107">Code d’état de réussite : 302 (Trouvé)</span><span class="sxs-lookup"><span data-stu-id="af1fc-107">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="af1fc-108">Exemple (redirection) : **/redirect-rule/{capture_group}** en **/redirected/{capture_group}**</span><span class="sxs-lookup"><span data-stu-id="af1fc-108">Example (redirect): **/redirect-rule/{capture_group}** to **/redirected/{capture_group}**</span></span>
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - <span data-ttu-id="af1fc-109">Code d’état de réussite : 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="af1fc-109">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="af1fc-110">Exemple (réécriture) : **/rewrite-rule/{capture_group_1}/{capture_group_2}** en **/rewritten?var1={capture_group_1}&var2={capture_group_2}**</span><span class="sxs-lookup"><span data-stu-id="af1fc-110">Example (rewrite): **/rewrite-rule/{capture_group_1}/{capture_group_2}** to **/rewritten?var1={capture_group_1}&var2={capture_group_2}**</span></span>
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - <span data-ttu-id="af1fc-111">Code d’état de réussite : 302 (Trouvé)</span><span class="sxs-lookup"><span data-stu-id="af1fc-111">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="af1fc-112">Exemple (redirection) : **/apache-mod-rules-redirect/{capture_group}** en **/redirected?id={capture_group}**</span><span class="sxs-lookup"><span data-stu-id="af1fc-112">Example (redirect): **/apache-mod-rules-redirect/{capture_group}** to **/redirected?id={capture_group}**</span></span>
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - <span data-ttu-id="af1fc-113">Code d’état de réussite : 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="af1fc-113">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="af1fc-114">Exemple (réécriture) : **/iis-rules-rewrite/{capture_group}** en **/rewritten?id={capture_group}**</span><span class="sxs-lookup"><span data-stu-id="af1fc-114">Example (rewrite): **/iis-rules-rewrite/{capture_group}** to **/rewritten?id={capture_group}**</span></span>
* `Add(RedirectXmlFileRequests)`
  - <span data-ttu-id="af1fc-115">Code d’état de réussite : 301 (déplacé de façon permanente)</span><span class="sxs-lookup"><span data-stu-id="af1fc-115">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="af1fc-116">Exemple (redirection) : **/file.xml** en **/xmlfiles/file.xml**</span><span class="sxs-lookup"><span data-stu-id="af1fc-116">Example (redirect): **/file.xml** to **/xmlfiles/file.xml**</span></span>
* `Add(RewriteTextFileRequests)`
  - <span data-ttu-id="af1fc-117">Code d’état de réussite : 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="af1fc-117">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="af1fc-118">Exemple (réécriture) : **/some_file.txt** en **/file.txt**</span><span class="sxs-lookup"><span data-stu-id="af1fc-118">Example (rewrite): **/some_file.txt** to **/file.txt**</span></span>
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - <span data-ttu-id="af1fc-119">Code d’état de réussite : 301 (déplacé de façon permanente)</span><span class="sxs-lookup"><span data-stu-id="af1fc-119">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="af1fc-120">Exemple (redirection) : **/image.png** en **/png-images/image.png**</span><span class="sxs-lookup"><span data-stu-id="af1fc-120">Example (redirect): **/image.png** to **/png-images/image.png**</span></span>
  - <span data-ttu-id="af1fc-121">Exemple (redirection) : **/image.jpg** en **/jpg-images/image.jpg**</span><span class="sxs-lookup"><span data-stu-id="af1fc-121">Example (redirect): **/image.jpg** to **/jpg-images/image.jpg**</span></span>

## <a name="use-a-physicalfileprovider"></a><span data-ttu-id="af1fc-122">Utiliser un PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="af1fc-122">Use a PhysicalFileProvider</span></span>

<span data-ttu-id="af1fc-123">Vous pouvez également obtenir un `IFileProvider` en créant un `PhysicalFileProvider` qui est ensuite passé aux méthodes `AddApacheModRewrite()` et `AddIISUrlRewrite()` :</span><span class="sxs-lookup"><span data-stu-id="af1fc-123">You can also obtain an `IFileProvider` by creating a `PhysicalFileProvider` to pass into the `AddApacheModRewrite()` and `AddIISUrlRewrite()` methods:</span></span>

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a><span data-ttu-id="af1fc-124">Extensions de redirection sécurisées</span><span class="sxs-lookup"><span data-stu-id="af1fc-124">Secure redirection extensions</span></span>

<span data-ttu-id="af1fc-125">Pour faciliter l’exploration des méthodes de redirection sécurisées, cet exemple présente la configuration de `WebHostBuilder` pour que l’application utilise des URL (`https://localhost:5001`, `https://localhost`) et un certificat de test (*testCert.pfx*).</span><span class="sxs-lookup"><span data-stu-id="af1fc-125">This sample includes `WebHostBuilder` configuration for the app to use URLs (`https://localhost:5001`, `https://localhost`) and a test certificate (*testCert.pfx*) to assist in exploring the secure redirect methods.</span></span> <span data-ttu-id="af1fc-126">Si le port 443 est déjà affecté ou utilisé sur le serveur, l’exemple `https://localhost` ne fonctionne pas &mdash; supprimez `ListenOptions` pour le port 443 dans la méthode `CreateWebHostBuilder` du fichier *Program.cs* ou annulez la liaison du port 443 sur le serveur, pour que Kestrel puisse utiliser le port.</span><span class="sxs-lookup"><span data-stu-id="af1fc-126">If the server already has port 443 assigned or in use, the `https://localhost` example doesn't work&mdash;remove the `ListenOptions` for port 443 in the `CreateWebHostBuilder` method of the *Program.cs* file or unbind port 443 on the server so that Kestrel can use the port.</span></span>

| <span data-ttu-id="af1fc-127">Méthode</span><span class="sxs-lookup"><span data-stu-id="af1fc-127">Method</span></span>                           | <span data-ttu-id="af1fc-128">Code d’état</span><span class="sxs-lookup"><span data-stu-id="af1fc-128">Status Code</span></span> |    <span data-ttu-id="af1fc-129">Port</span><span class="sxs-lookup"><span data-stu-id="af1fc-129">Port</span></span>    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     <span data-ttu-id="af1fc-130">301</span><span class="sxs-lookup"><span data-stu-id="af1fc-130">301</span></span>     | <span data-ttu-id="af1fc-131">Null (465)</span><span class="sxs-lookup"><span data-stu-id="af1fc-131">null (465)</span></span> |
| `.AddRedirectToHttps()`          |     <span data-ttu-id="af1fc-132">302</span><span class="sxs-lookup"><span data-stu-id="af1fc-132">302</span></span>     | <span data-ttu-id="af1fc-133">Null (465)</span><span class="sxs-lookup"><span data-stu-id="af1fc-133">null (465)</span></span> |
| `.AddRedirectToHttps(301)`       |     <span data-ttu-id="af1fc-134">301</span><span class="sxs-lookup"><span data-stu-id="af1fc-134">301</span></span>     | <span data-ttu-id="af1fc-135">Null (465)</span><span class="sxs-lookup"><span data-stu-id="af1fc-135">null (465)</span></span> |
| `.AddRedirectToHttps(301, 5001)` |     <span data-ttu-id="af1fc-136">301</span><span class="sxs-lookup"><span data-stu-id="af1fc-136">301</span></span>     |    <span data-ttu-id="af1fc-137">5001</span><span class="sxs-lookup"><span data-stu-id="af1fc-137">5001</span></span>    |
