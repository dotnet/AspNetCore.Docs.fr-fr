* <span data-ttu-id="23191-101">Approuvez le certificat de développement HTTPS en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="23191-101">Trust the HTTPS development certificate by running the following command:</span></span>

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  <span data-ttu-id="23191-102">La commande précédente ne fonctionne pas dans Linux.</span><span class="sxs-lookup"><span data-stu-id="23191-102">The preceding command doesn't work on Linux.</span></span> <span data-ttu-id="23191-103">Consultez la documentation de votre distribution Linux concernant l’approbation des certificats.</span><span class="sxs-lookup"><span data-stu-id="23191-103">See your Linux distribution's documentation for trusting a certificate.</span></span>

  <span data-ttu-id="23191-104">La commande précédente affiche la boîte de dialogue suivante :</span><span class="sxs-lookup"><span data-stu-id="23191-104">The preceding command displays the following dialog:</span></span>

  ![Boîte de dialogue Avertissement de sécurité](~/getting-started/_static/cert.png)

* <span data-ttu-id="23191-106">Sélectionnez **Oui** si vous acceptez d’approuver le certificat de développement.</span><span class="sxs-lookup"><span data-stu-id="23191-106">Select **Yes** if you agree to trust the development certificate.</span></span>

  <span data-ttu-id="23191-107">Pour plus d’informations, consultez [Approuver le certificat de développement HTTPS ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="23191-107">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]