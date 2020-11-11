# <a name="blazor-webassembly-sample-app"></a>Exemple d’application de webassembly éblouissant

Cet exemple illustre l’utilisation de scénarios éblouissants décrits dans la documentation de éblouissant.

## <a name="call-web-api-example"></a>Exemple d’appel d’API Web

L’exemple d’API Web requiert une API Web en cours d’exécution basée sur l’exemple d’application pour la rubrique <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">créer une API Web avec ASP.net Core</a> , qui utilise par défaut le même port HTTPS (5001) que l’exemple d’application éblouissant. Pour utiliser les deux applications sur le même ordinateur en même temps, modifiez le port de l’API Web (par exemple, utilisez le port 10000). L’exemple d’application envoie des demandes à l’API Web à l’adresse `https://localhost:10000/api/TodoItems` . Si une autre adresse d’API Web est utilisée, mettez à jour la `ServiceEndpoint` valeur de constante dans le bloc du composant Razor `@code` .</p>

L’exemple d’application effectue une demande de <a href="https://docs.microsoft.com/aspnet/core/security/cors">partage des ressources Cross-Origin (cors)</a> à partir de `http://localhost:5000` ou `https://localhost:5001` vers l’API Web. Les informations d’identification (cookies d’autorisation/en-têtes) sont autorisées. Ajoutez la configuration de middleware CORS suivante à la méthode de l’API Web `Startup.Configure` :</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Ajustez les domaines et les ports de `WithOrigins` en fonction des besoins de l’application éblouissante.

L’API Web est configurée pour CORS pour autoriser les cookies/en-têtes d’autorisation et les demandes provenant du code client, mais l’API Web telle qu’elle a été créée par le didacticiel n’autorise pas réellement les demandes. Pour obtenir des conseils d’implémentation, consultez les <a href="https://docs.microsoft.com/aspnet/core/security/">articles ASP.net Core Security and Identity</a> .
