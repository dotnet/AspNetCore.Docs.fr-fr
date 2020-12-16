## <a name="share-interop-code-in-a-class-library"></a>Partager du code d’interopérabilité dans une bibliothèque de classes

Le code d’interopérabilité JS peut être inclus dans une bibliothèque de classes, ce qui vous permet de partager le code dans un package NuGet.

La bibliothèque de classes gère l’incorporation des ressources JavaScript dans l’assembly généré. Les fichiers JavaScript sont placés dans le `wwwroot` dossier. Les outils s’occupent de l’incorporation des ressources lors de la génération de la bibliothèque.

Le package NuGet créé est référencé dans le fichier projet de l’application de la même façon que n’importe quel package NuGet. Une fois le package restauré, le code d’application peut appeler JavaScript comme s’il s’agissait de C#.

Pour plus d’informations, consultez <xref:blazor/components/class-libraries>.
