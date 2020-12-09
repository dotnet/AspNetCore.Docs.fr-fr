> [!WARNING]
> <span data-ttu-id="62a65-101">Pour des raisons de sécurité, vous devez choisir de lier les données de requête `GET` aux propriétés du modèle de page.</span><span class="sxs-lookup"><span data-stu-id="62a65-101">For security reasons, you must opt in to binding `GET` request data to page model properties.</span></span> <span data-ttu-id="62a65-102">Vérifiez l’entrée utilisateur avant de la mapper à des propriétés.</span><span class="sxs-lookup"><span data-stu-id="62a65-102">Verify user input before mapping it to properties.</span></span> <span data-ttu-id="62a65-103">L’utilisation `GET` de la liaison est utile lors de l’adressage de scénarios qui reposent sur une chaîne de requête ou des valeurs de route.</span><span class="sxs-lookup"><span data-stu-id="62a65-103">Opting into `GET` binding is useful when addressing scenarios that rely on query string or route values.</span></span>
>
> <span data-ttu-id="62a65-104">Pour lier une propriété à des `GET` demandes, affectez [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) à la propriété de l’attribut la valeur `SupportsGet` `true` :</span><span class="sxs-lookup"><span data-stu-id="62a65-104">To bind a property on `GET` requests, set the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute's `SupportsGet` property to `true`:</span></span>
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> <span data-ttu-id="62a65-105">Pour plus d’informations, consultez [ASP.net Core de la communauté réunions : lier sur obtenir une discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span><span class="sxs-lookup"><span data-stu-id="62a65-105">For more information, see [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span></span>
