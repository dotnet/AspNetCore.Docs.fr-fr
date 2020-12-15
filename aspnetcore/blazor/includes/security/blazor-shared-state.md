Applications serveur éblouissantes en direct dans la mémoire du serveur. Cela signifie qu’il existe plusieurs applications hébergées dans le même processus. Pour chaque session d’application, éblouissant démarre un circuit avec sa propre étendue d’injection de conteneurs. Cela signifie que les services délimités sont uniques pour chaque session éblouissante.

> [!WARNING]
> Nous vous déconseillons d’utiliser les applications sur le même état de partage de serveur à l’aide des services Singleton, sauf si une extrême prudence est prise, car cela peut introduire des failles de sécurité, telles que la fuite de l’état utilisateur entre les circuits.

Vous pouvez utiliser des services Singleton avec état dans des applications éblouissantes si elles sont spécifiquement conçues pour celle-ci. Par exemple, il est possible d’utiliser un cache mémoire en tant que singleton, car il requiert une clé pour accéder à une entrée donnée, en supposant que les utilisateurs n’ont pas le contrôle des clés de cache utilisées.

**De plus, pour des raisons de sécurité, vous ne devez pas utiliser <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> dans les applications éblouissantes.** Les applications éblouissantes s’exécutent en dehors du contexte du pipeline ASP.NET Core. Le <xref:Microsoft.AspNetCore.Http.HttpContext> n’est pas forcément disponible dans le <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> , et il n’est pas garanti qu’il détient le contexte qui a démarré l’application éblouissante.

La méthode recommandée pour passer l’état de la demande à l’application éblouissant est d’utiliser les paramètres du composant racine dans le rendu initial de l’application :

* Définissez une classe avec toutes les données que vous souhaitez transmettre à l’application éblouissante.
* Remplissez ces données à partir de la page Razor en utilisant le <xref:Microsoft.AspNetCore.Http.HttpContext> disponible à ce moment-là.
* Transmettez les données à l’application éblouissant en tant que paramètre au composant racine (application).
* Définissez un paramètre dans le composant racine pour stocker les données passées à l’application.
* Utilisez les données spécifiques à l’utilisateur dans l’application. vous pouvez également copier ces données dans un service étendu dans <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> afin qu’elles puissent être utilisées dans l’application.

Pour obtenir des informations supplémentaires ainsi qu'un code d'exemple, consultez <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.
