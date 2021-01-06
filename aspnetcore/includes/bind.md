La méthode recommandée pour lire les valeurs de configuration associées utilise le [modèle d’options](xref:fundamentals/configuration/options). Par exemple, pour lire les valeurs de configuration suivantes :

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

Créez la `PositionOptions` classe suivante :

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

Une classe d’options :

* Doit être non abstract avec un constructeur sans paramètre public.
* Toutes les propriétés publiques en lecture/écriture du type sont liées.
* Les champs ne sont **pas** liés. Dans le code précédent, `Position` n’est pas lié. La `Position` propriété est utilisée afin que la chaîne `"Position"` ne doive pas être codée en dur dans l’application lors de la liaison de la classe à un fournisseur de configuration.

Le code suivant :

_ Appelle [ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) pour lier la `PositionOptions` classe à la `Position` section.
* Affiche les `Position` données de configuration.

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

Dans le code précédent, par défaut, les modifications apportées au fichier de configuration JSON après le démarrage de l’application sont lues.

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) lie et retourne le type spécifié. `ConfigurationBinder.Get<T>` peut être plus pratique que l’utilisation de `ConfigurationBinder.Bind` . Le code suivant montre comment utiliser `ConfigurationBinder.Get<T>` avec la `PositionOptions` classe :

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

Dans le code précédent, par défaut, les modifications apportées au fichier de configuration JSON après le démarrage de l’application sont lues.

Une autre approche qui consiste à utiliser le **modèle d’options*** consiste à lier la `Position` section et à l’ajouter au [conteneur du service d’injection de dépendances](xref:fundamentals/dependency-injection). Dans le code suivant, `PositionOptions` est ajouté au conteneur de services avec <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> et lié à la configuration :

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

À l’aide du code précédent, le code suivant lit les options de position :

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

Dans le code précédent, les modifications apportées au fichier de configuration JSON après le démarrage de l’application ne sont ***pas*** lues. Pour lire les modifications une fois l’application démarrée, utilisez [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).
