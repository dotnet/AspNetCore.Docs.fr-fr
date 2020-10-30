---
title: Configurer l’authentification par certificat dans ASP.NET Core
author: blowdart
description: Découvrez comment configurer l’authentification par certificat dans ASP.NET Core pour IIS et HTTP.sys.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 07/16/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/certauth
ms.openlocfilehash: 83525a4c1e87a60b57130c1bba14360c7d03f552
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061377"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a>Configurer l’authentification par certificat dans ASP.NET Core

`Microsoft.AspNetCore.Authentication.Certificate` contient une implémentation similaire à [l’authentification par certificat](https://tools.ietf.org/html/rfc5246#section-7.4.4) pour ASP.net core. L’authentification par certificat se produit au niveau du TLS, à long terme avant qu’il ne soit ASP.NET Core. Plus précisément, il s’agit d’un gestionnaire d’authentification qui valide le certificat et vous donne un événement dans lequel vous pouvez résoudre ce certificat en `ClaimsPrincipal` . 

[Configurez votre serveur](#configure-your-server-to-require-certificates) pour l’authentification par certificat, qu’il soit IIS, Kestrel, Azure Web Apps ou tout autre que vous utilisez.

## <a name="proxy-and-load-balancer-scenarios"></a>Scénarios de proxy et d’équilibrage de charge

L’authentification par certificat est un scénario avec état principalement utilisé dans lequel un proxy ou un équilibreur de charge ne gère pas le trafic entre les clients et les serveurs. Si un proxy ou un équilibreur de charge est utilisé, l’authentification par certificat fonctionne uniquement si le proxy ou l’équilibreur de charge :

* Gère l’authentification.
* Transmet les informations d’authentification de l’utilisateur à l’application (par exemple, dans un en-tête de demande), qui agit sur les informations d’authentification.

Une alternative à l’authentification par certificat dans les environnements où les proxies et les équilibreurs de charge sont utilisés est Active Directory Services fédérés (ADFS) avec OpenID Connect (OIDC).

## <a name="get-started"></a>Prendre en main

Obtenez un certificat HTTPs, appliquez-le et [configurez votre serveur](#configure-your-server-to-require-certificates) pour exiger des certificats.

Dans votre application Web, ajoutez une référence au package [Microsoft. AspNetCore. Authentication. Certificate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) . Ensuite, dans la `Startup.ConfigureServices` méthode, appelez `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` avec vos options, en fournissant un délégué à pour `OnCertificateValidated` effectuer une validation supplémentaire sur le certificat client envoyé avec les demandes. Transformez ces informations en un `ClaimsPrincipal` et définissez-les sur la `context.Principal` propriété.

Si l’authentification échoue, ce gestionnaire renvoie une `403 (Forbidden)` réponse plutôt `401 (Unauthorized)` qu’un, comme vous pouvez l’imaginer. Le raisonnement est que l’authentification doit se produire pendant la connexion TLS initiale. Au moment où il atteint le gestionnaire, il est trop tard. Il n’existe aucun moyen de mettre à niveau la connexion d’une connexion anonyme à une avec un certificat.

Ajoutez également `app.UseAuthentication();` dans la `Startup.Configure` méthode. Dans le cas contraire, le `HttpContext.User` n’est pas défini à `ClaimsPrincipal` créé à partir du certificat. Exemple :

::: moniker range=">= aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate()
        // Adding an ICertificateValidationCache results in certificate auth caching the results.
        // The default implementation uses a memory cache.
        .AddCertificateCache();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

L’exemple précédent illustre la méthode par défaut pour ajouter l’authentification par certificat. Le gestionnaire construit un principal d’utilisateur à l’aide des propriétés de certificat courantes.

## <a name="configure-certificate-validation"></a>Configurer la validation des certificats

Le `CertificateAuthenticationOptions` gestionnaire a des validations intégrées qui sont les validations minimales que vous devez effectuer sur un certificat. Chacun de ces paramètres est activé par défaut.

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a>AllowedCertificateTypes = chained, SelfSigned ou All (chaîné | SelfSigned)

Valeur par défaut : `CertificateTypes.Chained`

Cette vérification valide que seul le type de certificat approprié est autorisé. Si l’application utilise des certificats auto-signés, cette option doit être définie sur `CertificateTypes.All` ou `CertificateTypes.SelfSigned` .

### <a name="validatecertificateuse"></a>ValidateCertificateUse

Valeur par défaut : `true`

Cette vérification permet de vérifier que le certificat présenté par le client a l’utilisation améliorée de la clé d’authentification du client, ou aucune EKU. Comme les spécifications indiquent, si aucune EKU n’est spécifiée, toutes les utilisations améliorées de la EKU sont considérées comme valides.

### <a name="validatevalidityperiod"></a>ValidateValidityPeriod

Valeur par défaut : `true`

Ce contrôle vérifie que le certificat se trouve dans sa période de validité. À chaque demande, le gestionnaire s’assure qu’un certificat valide lorsqu’il a été présenté n’a pas expiré pendant sa session active.

### <a name="revocationflag"></a>RevocationFlag

Valeur par défaut : `X509RevocationFlag.ExcludeRoot`

Indicateur qui spécifie les certificats de la chaîne qui sont vérifiés pour la révocation.

Les vérifications de révocation sont effectuées uniquement lorsque le certificat est chaîné à un certificat racine.

### <a name="revocationmode"></a>RevocationMode

Valeur par défaut : `X509RevocationMode.Online`

Indicateur qui spécifie comment les vérifications de révocation sont effectuées.

La spécification d’un contrôle en ligne peut entraîner un délai de longue durée pendant que l’autorité de certification est contactée.

Les vérifications de révocation sont effectuées uniquement lorsque le certificat est chaîné à un certificat racine.

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a>Puis-je configurer mon application pour exiger un certificat uniquement sur certains chemins d’accès ?

Cela n’est pas possible. Rappelez-vous que l’échange de certificats est effectué au début de la conversation HTTPs, car il est effectué par le serveur avant la réception de la première requête sur cette connexion, ce qui signifie qu’il n’est pas possible d’effectuer une étendue basée sur des champs de demande.

## <a name="handler-events"></a>Événements du gestionnaire

Le gestionnaire a deux événements :

* `OnAuthenticationFailed`: Appelé si une exception se produit pendant l’authentification et vous permet de réagir.
* `OnCertificateValidated`: Appelée une fois que le certificat a été validé, la validation a réussi et un principal par défaut a été créé. Cet événement vous permet d’effectuer votre propre validation et d’augmenter ou de remplacer le principal. Voici quelques exemples :
  * Déterminer si le certificat est connu de vos services.
  * Construction de votre propre principal. Prenons l’exemple suivant dans `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var claims = new[]
                {
                    new Claim(
                        ClaimTypes.NameIdentifier, 
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer),
                    new Claim(ClaimTypes.Name,
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer)
                };

                context.Principal = new ClaimsPrincipal(
                    new ClaimsIdentity(claims, context.Scheme.Name));
                context.Success();

                return Task.CompletedTask;
            }
        };
    });
```

Si vous trouvez que le certificat entrant ne répond pas à votre validation supplémentaire, appelez `context.Fail("failure reason")` en raison d’une erreur.

Pour les fonctionnalités réelles, vous souhaiterez probablement appeler un service enregistré dans une injection de dépendances qui se connecte à une base de données ou à un autre type de magasin d’utilisateurs. Accédez à votre service à l’aide du contexte passé dans votre délégué. Prenons l’exemple suivant dans `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetRequiredService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

D’un plan conceptuel, la validation du certificat est un problème d’autorisation. L’ajout d’un contrôle, par exemple, à un émetteur ou une empreinte numérique dans une stratégie d’autorisation, plutôt qu’à l’intérieur de `OnCertificateValidated` , est parfaitement acceptable.

## <a name="configure-your-server-to-require-certificates"></a>Configurer votre serveur pour exiger des certificats

### <a name="kestrel"></a>Kestrel

Dans *Program.cs* , configurez Kestrel comme suit :

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> Les valeurs par défaut sont appliquées aux points de terminaison créés en appelant avant d’appeler.

### <a name="iis"></a>IIS

Effectuez les étapes suivantes dans le gestionnaire des services Internet :

1. Sélectionnez votre site à partir de l’onglet **connexions** .
1. Double-cliquez sur l’option **Paramètres SSL** dans la fenêtre **affichage des fonctionnalités** .
1. Cochez la case **exiger SSL** , puis sélectionnez la case d’option **exiger** dans la section **certificats clients** .

![Paramètres de certificat client dans IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Proxys Web Azure et personnalisés

Consultez la [documentation sur l’hôte et le déploiement](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) pour savoir comment configurer l’intergiciel (middleware) de transfert de certificats.

### <a name="use-certificate-authentication-in-azure-web-apps"></a>Utiliser l’authentification par certificat dans Azure Web Apps

Aucune configuration de transfert n’est requise pour Azure. Ce programme est déjà configuré dans l’intergiciel (middleware) de transfert de certificats.

> [!NOTE]
> Cela nécessite que le CertificateForwardingMiddleware soit présent.

### <a name="use-certificate-authentication-in-custom-web-proxies"></a>Utiliser l’authentification par certificat dans des proxys Web personnalisés

La `AddCertificateForwarding` méthode est utilisée pour spécifier :

* Nom de l’en-tête du client.
* Comment le certificat doit être chargé (à l’aide de la `HeaderConverter` propriété).

Dans les proxys Web personnalisés, le certificat est transmis en tant qu’en-tête de demande personnalisée, par exemple `X-SSL-CERT` . Pour l’utiliser, configurez le transfert de certificats dans `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-SSL-CERT";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;
        
            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

La `Startup.Configure` méthode ajoute ensuite l’intergiciel (middleware). `UseCertificateForwarding` est appelé avant les appels à `UseAuthentication` et `UseAuthorization` :

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

Une classe distincte peut être utilisée pour implémenter une logique de validation. Étant donné que le même certificat auto-signé est utilisé dans cet exemple, assurez-vous que seul votre certificat peut être utilisé. Vérifiez que les empreintes numériques du certificat client et du certificat de serveur correspondent. dans le cas contraire, n’importe quel certificat peut être utilisé et sera suffisant pour l’authentification. Cela est utilisé dans la `AddCertificate` méthode. Vous pouvez également valider l’objet ou l’émetteur ici si vous utilisez des certificats intermédiaires ou enfants.

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a>Implémenter un HttpClient à l’aide d’un certificat et de HttpClientHandler

`HttpClientHandler`Peut être ajouté directement dans le constructeur de la `HttpClient` classe. Vous devez veiller à créer des instances du `HttpClient` . Le `HttpClient` envoie ensuite le certificat avec chaque demande.

```csharp
private async Task<JsonDocument> GetApiDataUsingHttpClientHandler()
{
    var cert = new X509Certificate2(Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(cert);
    var client = new HttpClient(handler);
     
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a>Implémenter un HttpClient à l’aide d’un certificat et d’un nommé HttpClient à partir de IHttpClientFactory 

Dans l’exemple suivant, un certificat client est ajouté à un à `HttpClientHandler` l’aide `ClientCertificates` de la propriété du gestionnaire. Ce gestionnaire peut ensuite être utilisé dans une instance nommée d’un `HttpClient` à l’aide de la `ConfigurePrimaryHttpMessageHandler` méthode. Il s’agit de l’installation de `Startup.ConfigureServices` :

```csharp
var clientCertificate = 
    new X509Certificate2(
      Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
 
var handler = new HttpClientHandler();
handler.ClientCertificates.Add(clientCertificate);
 
services.AddHttpClient("namedClient", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handler);
```

`IHttpClientFactory`Peut ensuite être utilisé pour récupérer l’instance nommée avec le gestionnaire et le certificat. La `CreateClient` méthode avec le nom du client défini dans la `Startup` classe est utilisée pour récupérer l’instance. La requête HTTP peut être envoyée à l’aide du client en fonction des besoins.

```csharp
private readonly IHttpClientFactory _clientFactory;
 
public ApiService(IHttpClientFactory clientFactory)
{
    _clientFactory = clientFactory;
}
 
private async Task<JsonDocument> GetApiDataWithNamedClient()
{
    var client = _clientFactory.CreateClient("namedClient");
 
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

Si le bon certificat est envoyé au serveur, les données sont retournées. Si aucun certificat ou le mauvais certificat n’est envoyé, un code d’état HTTP 403 est renvoyé.

### <a name="create-certificates-in-powershell"></a>Créer des certificats dans PowerShell

La création de certificats est la partie la plus difficile dans la configuration de ce fluide. Un certificat racine peut être créé à l’aide de l’applet de commande `New-SelfSignedCertificate` PowerShell. Lors de la création du certificat, utilisez un mot de passe fort. Il est important d’ajouter le `KeyUsageProperty` paramètre et le `KeyUsage` paramètre comme indiqué.

#### <a name="create-root-ca"></a>Créer une autorité de certification racine

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> La `-DnsName` valeur du paramètre doit correspondre à la cible de déploiement de l’application. Par exemple, « localhost » pour le développement.

#### <a name="install-in-the-trusted-root"></a>Installer dans la racine approuvée

Le certificat racine doit être approuvé sur votre système hôte. Un certificat racine qui n’a pas été créé par une autorité de certification n’est pas approuvé par défaut. Le lien suivant explique comment cela peut être accompli sur Windows :

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a>Certificat intermédiaire

Un certificat intermédiaire peut maintenant être créé à partir du certificat racine. Cela n’est pas obligatoire pour tous les cas d’usage, mais vous devrez peut-être créer de nombreux certificats ou avoir besoin d’activer ou de désactiver des groupes de certificats. Le `TextExtension` paramètre est requis pour définir la longueur du chemin d’accès dans les contraintes de base du certificat.

Le certificat intermédiaire peut ensuite être ajouté au certificat intermédiaire approuvé dans le système hôte Windows.

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a>Créer un certificat enfant à partir d’un certificat intermédiaire

Un certificat enfant peut être créé à partir du certificat intermédiaire. Il s’agit de l’entité finale et n’a pas besoin de créer des certificats enfants supplémentaires.

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a>Créer un certificat enfant à partir du certificat racine

Un certificat enfant peut également être créé directement à partir du certificat racine. 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a>Exemple de certificat de certificat intermédiaire racine

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

Lors de l’utilisation de certificats racine, intermédiaires ou enfants, les certificats peuvent être validés à l’aide de l’empreinte numérique ou PublicKey, si nécessaire.

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```

<a name="occ"></a>

::: moniker range=">= aspnetcore-5.0"

## <a name="certificate-validation-caching"></a>Mise en cache de la validation des certificats

ASP.NET Core 5,0 et versions ultérieures prennent en charge la possibilité d’activer la mise en cache des résultats de la validation. La mise en cache améliore considérablement les performances de l’authentification par certificat, car la validation est une opération coûteuse.

Par défaut, l’authentification par certificat désactive la mise en cache. Pour activer la mise en cache, appelez `AddCertificateCache` dans `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate()
            .AddCertificateCache(options =>
            {
                options.CacheSize = 1024;
                options.CacheEntryExpiration = TimeSpan.FromMinutes(2);
            });
}
```

L’implémentation de la mise en cache par défaut stocke les résultats en mémoire. Vous pouvez fournir votre propre cache en l’implémentant et en l' `ICertificateValidationCache` inscrivant avec l’injection de dépendances. Par exemple : `services.AddSingleton<ICertificateValidationCache, YourCache>()`.

::: moniker-end

## <a name="optional-client-certificates"></a>Certificats clients facultatifs

Cette section fournit des informations pour les applications qui doivent protéger un sous-ensemble de l’application à l’aide d’un certificat. Par exemple, une Razor page ou un contrôleur dans l’application peut nécessiter des certificats clients. Cela présente des défis en tant que certificats clients :
  
* Sont une fonctionnalité TLS, et non une fonctionnalité HTTP.
* Sont négociés par connexion et doivent être négociés au début de la connexion avant que toutes les données HTTP soient disponibles. Au début de la connexion, seule la Indication du nom du serveur (SNI) &dagger; est connue. Les certificats du client et du serveur sont négociés avant la première demande sur une connexion et les demandes ne sont généralement pas en mesure de renégocier.

La renégociation TLS était une ancienne façon d’implémenter des certificats clients facultatifs. Cette option n’est plus recommandée, car :
- Dans HTTP/1.1, la renégociation au cours d’une demande ultérieure peut entraîner un blocage où le corps de la demande a rempli la fenêtre TCP et les paquets de renégociation ne peuvent pas être reçus.
- HTTP/2 [interdit explicitement](https://tools.ietf.org/html/rfc7540#section-9.2.1) la renégociation.
- TLS 1,3 a [supprimé](https://tools.ietf.org/html/rfc8740#section-1) la prise en charge de la renégociation.

ASP.NET Core 5 Preview 7 et versions ultérieures ajoute une prise en charge plus pratique pour les certificats clients facultatifs. Pour plus d’informations, consultez l' [exemple de certificats facultatifs](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample).

L’approche suivante prend en charge les certificats clients facultatifs :

* Configuration de la liaison pour le domaine et le sous-domaine :
  * Par exemple, configurez des liaisons sur `contoso.com` et `myClient.contoso.com` . L' `contoso.com` hôte n’a pas besoin d’un certificat client, mais ce `myClient.contoso.com` n’est pas le cas.
  * Pour plus d'informations, consultez les pages suivantes :
    * [Kestrel](/fundamentals/servers/kestrel):
      * [ListenOptions.UseHttps](xref:fundamentals/servers/kestrel#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * Notez que Kestrel ne prend pas en charge plusieurs configurations TLS sur une seule liaison, vous aurez besoin de deux liaisons avec des adresses IP ou des ports uniques. Voir https://github.com/dotnet/runtime/issues/31097
    * IIS
      * [Hébergement d’IIS](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [Configurer la sécurité sur IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * Http.Sys : [configurer Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)
* Pour les demandes adressées à l’application Web qui requièrent un certificat client et n’en ont pas :
  * Rediriger vers la même page à l’aide du sous-domaine protégé par certificat du client.
  * Par exemple, redirigez vers `myClient.contoso.com/requestedPage` . Étant donné que la demande à `myClient.contoso.com/requestedPage` est un nom d’hôte différent de `contoso.com/requestedPage` , le client établit une connexion différente et le certificat client est fourni.
  * Pour plus d'informations, consultez <xref:security/authorization/introduction>.

Laissez des questions, des commentaires et d’autres commentaires sur les certificats clients facultatifs dans ce problème de [discussion GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/18720) .

&dagger; Indication du nom du serveur (SNI) est une extension TLS pour inclure un domaine virtuel dans le cadre de la négociation SSL. Cela signifie que le nom de domaine virtuel, ou un nom d’hôte, peut être utilisé pour identifier le point de terminaison réseau.
