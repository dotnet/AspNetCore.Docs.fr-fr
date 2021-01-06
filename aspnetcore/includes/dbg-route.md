## <a name="debug-diagnostics"></a><span data-ttu-id="ef8a9-101">Diagnostics de débogage</span><span class="sxs-lookup"><span data-stu-id="ef8a9-101">Debug diagnostics</span></span>

<span data-ttu-id="ef8a9-102">Pour une sortie de diagnostic de routage détaillée, affectez à la valeur `Logging:LogLevel:Microsoft` `Debug` .</span><span class="sxs-lookup"><span data-stu-id="ef8a9-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="ef8a9-103">Dans l’environnement de développement, définissez le niveau de journalisation dans *appsettings.Development.jssur*:</span><span class="sxs-lookup"><span data-stu-id="ef8a9-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```
