<span data-ttu-id="920cb-101">Les codes de fin HTTP sont similaires aux en-têtes HTTP, sauf qu’ils sont envoyés après l’envoi du corps de la réponse.</span><span class="sxs-lookup"><span data-stu-id="920cb-101">HTTP Trailers are similar to HTTP Headers, except they are sent after the response body is sent.</span></span> <span data-ttu-id="920cb-102">Pour IIS et HTTP.SYS, seules les codes de fin de réponse HTTP/2 sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="920cb-102">For IIS and HTTP.SYS, only HTTP/2 response trailers are supported.</span></span>

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

<span data-ttu-id="920cb-103">Dans l’exemple de code précédent :</span><span class="sxs-lookup"><span data-stu-id="920cb-103">In the preceding example code:</span></span>

* <span data-ttu-id="920cb-104">`SupportsTrailers` garantit que les codes de fin sont pris en charge pour la réponse.</span><span class="sxs-lookup"><span data-stu-id="920cb-104">`SupportsTrailers` ensures that trailers are supported for the response.</span></span>
* <span data-ttu-id="920cb-105">`DeclareTrailer` Ajoute le nom de la terminaison donnée à l' `Trailer` en-tête de réponse.</span><span class="sxs-lookup"><span data-stu-id="920cb-105">`DeclareTrailer` adds the given trailer name to the `Trailer` response header.</span></span> <span data-ttu-id="920cb-106">La déclaration des codes de fin d’une réponse est facultative, mais recommandée.</span><span class="sxs-lookup"><span data-stu-id="920cb-106">Declaring a response's trailers is optional, but recommended.</span></span> <span data-ttu-id="920cb-107">Si `DeclareTrailer` est appelé, il doit être avant l’envoi des en-têtes de réponse.</span><span class="sxs-lookup"><span data-stu-id="920cb-107">If `DeclareTrailer` is called, it must be before the response headers are sent.</span></span>
* <span data-ttu-id="920cb-108">`AppendTrailer` Ajoute le code de fin.</span><span class="sxs-lookup"><span data-stu-id="920cb-108">`AppendTrailer` appends the trailer.</span></span>
