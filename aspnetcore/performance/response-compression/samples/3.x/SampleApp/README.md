# <a name="response-compression-sample-application-aspnet-core-3x"></a>Exemple d’application de compression de réponse (ASP.NET Core 3. x)

Cet exemple illustre l’utilisation de l’intergiciel (middleware) de compression des réponses ASP.NET Core 3. x pour compresser les réponses HTTP. L’exemple illustre les fournisseurs de compression gzip, Brotli et personnalisées pour les réponses de texte et d’image et montre comment ajouter un type MIME pour la compression. Pour obtenir l’exemple ASP.NET Core 2. x, consultez [exemple d’application de compression de réponse (ASP.net Core 2. x)](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/response-compression/samples/2.x).

## <a name="examples-in-this-sample"></a>Exemples dans cet exemple

* `BrotliCompressionProvider`
  * `text/plain`
    * **/** -La réponse du fichier texte Lorem ipsum à 2 044 octets qui compressent jusqu’à ~ 979 octets.
    * **/testfile1kb.txt** -réponse de fichier texte à 1 033 octets qui compresse jusqu’à ~ 36 octets.
    * **/Trickle** -réponse émise sous forme de caractères uniques à des intervalles de 1 seconde.
* `GzipCompressionProvider`
  * `text/plain`
    * **/** -La réponse du fichier texte Lorem ipsum à 2 044 octets qui compressent jusqu’à ~ 927 octets.
    * **/testfile1kb.txt** -réponse de fichier texte à 1 033 octets qui compresse jusqu’à ~ 47 octets.
    * **/Trickle** -réponse émise sous forme de caractères uniques à des intervalles de 1 seconde.
  * `image/svg+xml`
    * **/Banner.svg** : réponse d’image SVG (Scalable Vector Graphics) à 9 707 octets qui compresse jusqu’à ~ 4 459 octets.
* `CustomCompressionProvider`<br>Montre comment implémenter un fournisseur de compression personnalisé à utiliser avec l’intergiciel (middleware).

Lorsque la requête inclut l' `Accept-Encoding` en-tête et que la compression de la réponse est réussie, l’intergiciel ajoute automatiquement un `Vary: Accept-Encoding` en-tête à la réponse. L' `Vary` en-tête indique aux caches de conserver plusieurs copies de la réponse en fonction des autres valeurs de `Accept-Encoding` , de sorte qu’une version compressée (gzip ou Brotli) et une version non compressée sont stockées dans des caches pour les systèmes qui peuvent accepter la réponse compressée ou non compressée.

## <a name="use-the-sample"></a>Utiliser l’exemple

1. Effectuez une demande à l’aide de [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)ou du [billet](https://www.getpostman.com/) à l’application sans `Accept-Encoding` en-tête et notez la charge utile de réponse, la taille de la réponse et les en-têtes de réponse.
1. Ajoutez un `Accept-Encoding: br` `Accept-Encoding: gzip` en-tête ou et notez la taille de réponse compressée et les en-têtes de réponse. La taille de la réponse est abandonnée et l' `Content-Encoding` en-tête de réponse est inclus par l’intergiciel (middleware) indiquant que la compression avec gzip ou Brotli s’est produite. Lorsque vous examinez le corps de la réponse pour la réponse Lorem ipsum ou **testfile1kb.txt** , vous constatez que le texte est compressé et illisible.
1. Ajoutez un `Accept-Encoding: mycustomcompression` en-tête et notez les en-têtes de réponse. `CustomCompressionProvider`Est une implémentation vide qui ne compresse pas réellement la réponse, mais vous pouvez créer un wrapper de flux de compression personnalisé pour la `CreateStream()` méthode.
