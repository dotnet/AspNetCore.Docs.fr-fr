# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application. La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`. La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local. Localhost traite uniquement les requêtes web de l’ordinateur local. Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.
 
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Appuyez sur **Ctrl+F5** pour exécuter sans le débogueur.

  Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `http://localhost:5001`. La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`. La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local. Localhost traite uniquement les requêtes web de l’ordinateur local.

  
# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Dans Visual Studio, appuyez sur **OPT-CMD-Return** pour exécuter sans le débogueur. Vous pouvez également accéder à la barre de menus et accéder à **exécuter>démarrer sans débogage**.

  Visual Studio démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `http://localhost:5001`.

<!-- End of VS tabs -->

---
