* Approuvez le certificat de développement HTTPS en exécutant la commande suivante :

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  La commande précédente ne fonctionne pas dans Linux. Consultez la documentation de votre distribution Linux concernant l’approbation des certificats.

  La commande précédente affiche la boîte de dialogue suivante :

  ![Boîte de dialogue Avertissement de sécurité](~/getting-started/_static/cert.png)

* Sélectionnez **Oui** si vous acceptez d’approuver le certificat de développement.

  Pour plus d’informations, consultez [Approuver le certificat de développement HTTPS ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]