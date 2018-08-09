---
title: Identité de service managée dans App Service et Azure Functions | Microsoft Docs
description: Guide de référence conceptuel et d’installation pour la prise en charge de l’identité de service managée dans Azure App Service et Azure Functions
services: app-service
author: mattchenderson
manager: cfowler
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/25/2018
ms.author: mahender
ms.openlocfilehash: 2e392a3a50cda3daacb5bc358baaea2627eeafc0
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578828"
---
# <a name="how-to-use-azure-managed-service-identity-in-app-service-and-azure-functions"></a>Guide pratique pour utiliser Managed Service Identity dans App Service et Azure Functions

> [!NOTE] 
> App Service sur Linux et Web App pour conteneurs ne prennent pas en charge Managed Service Identity.

> [!Important] 
> Si vous migrez votre application entre différents abonnements/locataires, Managed Service Identity pour App Service et Azure Functions présentera un comportement anormal. L’application devra obtenir une nouvelle identité, ce qui peut être effectué par la désactivation et la réactivation de la fonctionnalité. Consultez [Suppression d’une identité](#remove) ci-dessous. Les ressources en aval devront également disposer de stratégies d’accès mises à jour pour utiliser la nouvelle identité.

Cette rubrique vous montre comment créer une identité d’application managée pour les applications App Service et Azure Functions et comment l’utiliser pour accéder à d’autres ressources. Une identité de service managée issue d’Azure Active Directory permet à votre application d’accéder facilement aux autres ressources protégées par AAD telles qu’Azure Key Vault. Managée par la plateforme Azure, l’identité ne nécessite pas que vous approvisionniez ou permutiez de secrets. Pour plus d’informations sur l’identité de service managée, consultez la [vue d’ensemble de l’identité de service managée](../active-directory/managed-service-identity/overview.md).

## <a name="creating-an-app-with-an-identity"></a>Création d’une application avec une identité

Créer une application avec une identité requiert la définition d’une propriété supplémentaire sur cette application.

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure

Pour configurer une identité de service managée dans le portail, vous créez une application selon la procédure habituelle, puis vous activez la fonctionnalité.

1. Créez une application dans le portail, comme vous le feriez normalement. Accédez-y dans le portail.

2. Si vous utilisez une application de fonction, accédez à **Fonctionnalités de la plateforme**. Pour les autres types d’application, faites défiler jusqu’au groupe **Paramètres** dans le volet de navigation de gauche.

3. Sélectionnez **Identité de service managée**.

4. Définissez **Inscrire auprès d’Azure Active Directory** sur **Activé**. Cliquez sur **Enregistrer**.

![Identité de service managée dans App Service](media/app-service-managed-service-identity/msi-blade.png)

### <a name="using-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure (CLI)

Pour configurer une identité de service managée avec Azure CLI, vous devez utiliser la commande `az webapp identity assign` sur une application existante. Vous avez le choix entre trois options pour exécuter les exemples de cette section :

- Utiliser [Azure Cloud Shell](../cloud-shell/overview.md) à partir du portail Azure.
- Utiliser le service incorporé Azure Cloud Shell via le bouton « Essayer », en haut à droite de chaque bloc de code ci-dessous.
- [Installer la dernière version de CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 ou version ultérieure) si vous préférez utiliser une console CLI locale. 

Les étapes suivantes vous guident dans la création d’une application web à laquelle vous attribuez une identité en utilisant l’interface CLI :

1. Si vous utilisez l’interface de ligne de commande Azure dans une console locale, commencez par vous connecter à Azure avec [az login](/cli/azure/reference-index#az-login). Utilisez un compte associé à l’abonnement Azure dans lequel vous souhaitez déployer l’application :

    ```azurecli-interactive
    az login
    ```
2. Créez une application web avec CLI. Pour plus d’exemples d’utilisation de CLI avec App Service, consultez [Exemples CLI App Service](../app-service/app-service-cli-samples.md) :

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Exécutez la commande `identity assign` pour créer l’identité de cette application :

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Utilisation de Microsoft Azure PowerShell

Les étapes suivantes vous guident dans la création d’une application web à laquelle vous attribuez une identité en utilisant Azure PowerShell :

1. Si nécessaire, installez Azure PowerShell à l’aide des instructions figurant dans le [Guide Azure PowerShell](/powershell/azure/overview), puis exécutez `Login-AzureRmAccount` pour créer une connexion avec Azure.

2. Créez une application web avec Azure PowerShell. Pour plus d’exemples sur l’utilisation d’Azure PowerShell avec App Service, consultez [Exemples App Service PowerShell](../app-service/app-service-powershell-samples.md) :

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzureRmResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzureRmAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzureRmWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Exécutez la commande `identity assign` pour créer l’identité de cette application :

    ```azurepowershell-interactive
    Set-AzureRmWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Utilisation d’un modèle Azure Resource Manager

Vous pouvez utiliser un modèle Azure Resource Manager pour automatiser le déploiement de vos ressources Azure. Pour en savoir plus sur le déploiement sur App Service et Functions, consultez [Automatiser le déploiement de ressources dans App Service](../app-service/app-service-deploy-complex-application-predictably.md) et [Automatiser le déploiement de ressources dans Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Vous pouvez créer n’importe quelle ressource de type `Microsoft.Web/sites` avec une identité en incluant la propriété suivante dans la définition de la ressource :
```json
"identity": {
    "type": "SystemAssigned"
}    
```

Ce code indique à Azure de créer et de manager l’identité de votre application.

Par exemple, une application web peut se présenter comme suit :
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

Quand le site est créé, il a les propriétés supplémentaires suivantes :
```json
"identity": {
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Où `<TENANTID>` et `<PRINCIPALID>` sont remplacés par des GUID. La propriété tenantId identifie le locataire AAD auquel appartient l’identité. La propriété principalId est un identificateur unique pour la nouvelle identité de l’application. Dans AAD, le principal de service porte le même nom que celui que vous avez donné à votre instance App Service ou Azure Functions.

## <a name="obtaining-tokens-for-azure-resources"></a>Obtention de jetons pour les ressources Azure

Une application peut utiliser son identité pour obtenir des jetons pour d’autres ressources protégées par AAD, telles qu’Azure Key Vault. Ces jetons représentent l’application qui accède à la ressource, pas un utilisateur spécifique de l’application. 

> [!IMPORTANT]
> Vous pouvez être amené à configurer la ressource cible pour autoriser l’accès à partir de votre application. Par exemple, si vous demandez un jeton de coffre de clés, vous devez vérifier que vous avez ajouté une stratégie d’accès qui inclut l’identité de votre application. Si tel n’est pas le cas, vos appels au coffre de clés sont rejetés, même s’ils incluent le jeton. Pour en savoir plus sur les ressources qui prennent en charge les jetons d’identité de service managée, consultez [Services Azure prenant en charge l’authentification Azure AD](../active-directory/managed-service-identity/overview.md#which-azure-services-support-managed-service-identity).

Il existe un protocole REST simple pour obtenir un jeton dans App Service et Azure Functions. Pour les applications .NET, la bibliothèque Microsoft.Azure.Services.AppAuthentication fournit une abstraction sur ce protocole et prend en charge une expérience de développement local.

### <a name="asal"></a>Utilisation de la bibliothèque Microsoft.Azure.Services.AppAuthentication pour .NET

Pour les applications et les fonctions .NET, la façon la plus simple pour utiliser une identité de service managée consiste à passer par le package Microsoft.Azure.Services.AppAuthentication. Cette bibliothèque vous permet également de tester votre code localement sur votre machine de développement, à l’aide de votre compte d’utilisateur à partir de Visual Studio, [d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) ou de l’authentification intégrée Azure Active Directory. Pour plus d’informations sur les options de développement local avec cette bibliothèque, consultez le [Guide de référence technique sur Microsoft.Azure.Services.AppAuthentication]. Cette section vous montre comment prendre en main la bibliothèque dans votre code.

1. Ajoutez des références aux packages NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) et [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) à votre application.

2.  Ajoutez le code suivant à votre application :

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.KeyVault;
// ...
var azureServiceTokenProvider = new AzureServiceTokenProvider();
string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
// OR
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
```

Pour en savoir plus sur Microsoft.Azure.Services.AppAuthentication et les opérations qu’il expose, consultez le [Guide de référence technique sur Microsoft.Azure.Services.AppAuthentication] et [l’exemple .NET associant App Service, Key Vault et l’identité du service administré](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-rest-protocol"></a>Utilisation du protocole REST

Une application avec une identité de service managée a deux variables d’environnement définies :
- MSI_ENDPOINT
- MSI_SECRET

**MSI_ENDPOINT** est une URL locale à partir de laquelle votre application peut demander des jetons. Pour obtenir un jeton pour une ressource, effectuez une requête HTTP GET à destination de ce point de terminaison, en indiquant notamment les paramètres suivants :

> [!div class="mx-tdBreakAll"]
> |Nom du paramètre|Dans|Description|
> |-----|-----|-----|
> |resource|Requête|URI de ressource AAD de la ressource pour laquelle un jeton doit être obtenu.|
> |api-version|Requête|Version de l’API de jeton à utiliser. « 2017-09-01 » est la seule version prise en charge.|
> |secret|En-tête|Valeur de la variable d’environnement MSI_SECRET.|


Une réponse 200 OK correcte comprend un corps JSON avec les propriétés suivantes :

> [!div class="mx-tdBreakAll"]
> |Nom de la propriété|Description|
> |-------------|----------|
> |access_token|Le jeton d’accès demandé. Le service web appelant peut utiliser ce jeton pour s’authentifier auprès du service web de destination.|
> |expires_on|L’heure d’expiration du jeton d’accès. La date est représentée en nombre de secondes à partir du 1er janvier 1970 (1970-01-01T0:0:0Z) UTC jusqu’au moment de l’expiration. Cette valeur est utilisée pour déterminer la durée de vie des jetons en cache.|
> |resource|L’URI ID d’application du service web de destination.|
> |token_type|Indique la valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est le jeton porteur. Pour plus d’informations sur les jetons du porteur, consultez [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt).|


Cette réponse est la même que la [réponse pour la demande de jeton d’accès de service à service AAD](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

> [!NOTE] 
> Les variables d’environnement sont configurées au premier démarrage du processus, ainsi après l’activation de Managed Service Identity pour votre application, vous devrez peut-être redémarrer votre application, ou redéployer son code avant que `MSI_ENDPOINT` et `MSI_SECRET` soient disponibles pour votre code.

### <a name="rest-protocol-examples"></a>Exemples de protocole REST
Voici un exemple de demande :
```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```
Et voici un exemple de réponse :
```
HTTP/1.1 200 OK
Content-Type: application/json
 
{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>Exemples de code
<a name="token-csharp"></a>Pour écrire cette requête en C# :
```csharp
public static async Task<HttpResponseMessage> GetToken(string resource, string apiversion)  {
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await client.GetAsync(String.Format("{0}/?resource={1}&api-version={2}", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource, apiversion));
}
```
> [!TIP]
> Dans le cas des langages .NET, vous pouvez également utiliser [Microsoft.Azure.Services.AppAuthentication](#asal) au lieu d’élaborer cette demande vous-même.

<a name="token-js"></a>En Node.JS :
```javascript
const rp = require('request-promise');
const getToken = function(resource, apiver, cb) {
    var options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=${apiver}`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

<a name="token-powershell"></a>Dans PowerShell :
```powershell
$apiVersion = "2017-09-01"
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=$apiVersion"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

## <a name="remove"></a>Suppression d’une identité

Vous pouvez supprimer une identité en désactivant la fonctionnalité à l’aide du portail, de PowerShell ou de l’interface CLI, de la même façon que vous l’avez créée. Dans le protocole de modèle REST/ARM, vous pouvez le faire en définissant le type sur « Aucun » :

```json
"identity": {
    "type": "None"
}    
```

Si vous supprimez l’identité de cette façon, vous supprimez également le principal d’AAD. Les identités attribuées par le système sont automatiquement supprimées d’AAD lorsque la ressource d’application est supprimée.

> [!NOTE] 
> Vous pouvez également définir le paramètre d’application WEBSITE_DISABLE_MSI, qui désactive uniquement le service de jetons local. Toutefois, cela ne touche pas à l’identité, et les outils continueront d’afficher MSI comme étant activé. Par conséquent, l’utilisation de ce paramètre n’est pas recommandée.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Accéder à Azure SQL Database en toute sécurité à l’aide de l’identité du service managée](app-service-web-tutorial-connect-msi.md)

[Guide de référence technique sur Microsoft.Azure.Services.AppAuthentication]: https://go.microsoft.com/fwlink/p/?linkid=862452
