Les codes de fin HTTP sont similaires aux en-têtes HTTP, sauf qu’ils sont envoyés après l’envoi du corps de la réponse. Pour IIS et HTTP.sys, seules les codes de fin de réponse HTTP/2 sont pris en charge.

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

Dans l’exemple de code précédent :

* `SupportsTrailers` garantit que les codes de fin sont pris en charge pour la réponse.
* `DeclareTrailer` Ajoute le nom de la terminaison donnée à l' `Trailer` en-tête de réponse. La déclaration des codes de fin d’une réponse est facultative, mais recommandée. Si `DeclareTrailer` est appelé, il doit être avant l’envoi des en-têtes de réponse.
* `AppendTrailer` Ajoute le code de fin.
