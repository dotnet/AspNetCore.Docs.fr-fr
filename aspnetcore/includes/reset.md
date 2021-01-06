<span data-ttu-id="72f28-101">La réinitialisation permet au serveur de réinitialiser une requête HTTP/2 avec un code d’erreur spécifié.</span><span class="sxs-lookup"><span data-stu-id="72f28-101">Reset allows for the server to reset a HTTP/2 request with a specified error code.</span></span> <span data-ttu-id="72f28-102">Une demande de réinitialisation est considérée comme abandonnée.</span><span class="sxs-lookup"><span data-stu-id="72f28-102">A reset request is considered aborted.</span></span>

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

<span data-ttu-id="72f28-103">`Reset` dans l’exemple de code précédent, spécifie le `INTERNAL_ERROR` code d’erreur.</span><span class="sxs-lookup"><span data-stu-id="72f28-103">`Reset` in the preceding code example specifies the `INTERNAL_ERROR` error code.</span></span> <span data-ttu-id="72f28-104">Pour plus d’informations sur les codes d’erreur HTTP/2, consultez la [section Code d’erreur de la spécification http/2](https://tools.ietf.org/html/rfc7540#page-50).</span><span class="sxs-lookup"><span data-stu-id="72f28-104">For more information about HTTP/2 error codes, visit the [HTTP/2 specification error code section](https://tools.ietf.org/html/rfc7540#page-50).</span></span>