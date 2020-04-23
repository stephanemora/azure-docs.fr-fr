---
title: Identités managées
description: Découvrez comment les identités managées fonctionnent dans Azure App Service et Azure Functions, comment configurer une identité managée et comment générer un jeton pour une ressource back-end.
author: mattchenderson
ms.topic: article
ms.date: 04/14/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.openlocfilehash: 875d2bbebdfa95c6d180979399d876eb2afc01b4
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392519"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Guide pratique pour utiliser des identités managées pour App Service et Azure Functions

> [!Important] 
> Si vous migrez votre application entre différents abonnements/locataires, les identités managées pour App Service et Azure Functions présentent un comportement anormal. L’application devra obtenir une nouvelle identité, ce qui peut être effectué par la désactivation et la réactivation de la fonctionnalité. Consultez [Suppression d’une identité](#remove) ci-dessous. Les ressources en aval devront également disposer de stratégies d’accès mises à jour pour utiliser la nouvelle identité.

Cette rubrique vous montre comment créer une identité managée pour les applications App Service et Azure Functions et comment l’utiliser pour accéder à d’autres ressources. Une identité managée provenant d’Azure Active Directory (Azure AD) permet à votre application d’accéder facilement à d’autres ressources protégées par Azure AD, comme Azure Key Vault. Managée par la plateforme Azure, l’identité ne nécessite pas que vous approvisionniez ou permutiez de secrets. Pour plus d’informations sur les identités managées dans Azure AD, consultez [Identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md).

Deux types d’identité peuvent être accordés à votre application :

- Une **identité attribuée par le système** est liée à votre application et est supprimée si votre application est supprimée. Une application ne peut avoir qu’une seule identité attribuée par le système.
- Une **identité attribuée par l’utilisateur** est une ressource Azure autonome qui peut être assignée à votre application. Une application peut avoir plusieurs identités attribuées par l’utilisateur.

## <a name="add-a-system-assigned-identity"></a>Ajouter une identité affectée par le système

Créer une application avec une identité attribuée par le système requiert la définition d’une propriété supplémentaire sur cette application.

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure

Pour configurer une identité managée dans le portail, vous créez une application selon la procédure habituelle, puis vous activez la fonctionnalité.

1. Créez une application dans le portail, comme vous le feriez normalement. Accédez-y dans le portail.

2. Si vous utilisez une application de fonction, accédez à **Fonctionnalités de la plateforme**. Pour les autres types d’application, faites défiler jusqu’au groupe **Paramètres** dans le volet de navigation de gauche.

3. Sélectionnez **Identité**.

4. Dans l’onglet **Attribuée par le système**, définissez **État** sur **Activé**. Cliquez sur **Enregistrer**.

    ![Identité managée dans App Service](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)

### <a name="using-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure (CLI)

Pour configurer une identité managée avec Azure CLI, vous devez utiliser la commande `az webapp identity assign` sur une application existante. Vous avez le choix entre trois options pour exécuter les exemples de cette section :

- Utiliser [Azure Cloud Shell](../cloud-shell/overview.md) à partir du portail Azure.
- Utiliser le service incorporé Azure Cloud Shell via le bouton « Essayer », en haut à droite de chaque bloc de code ci-dessous.
- [Installez la dernière version d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 ou ultérieure) si vous préférez utiliser une console CLI locale. 

Les étapes suivantes vous guident dans la création d’une application web à laquelle vous attribuez une identité en utilisant l’interface CLI :

1. Si vous utilisez l’interface de ligne de commande Azure dans une console locale, commencez par vous connecter à Azure avec [az login](/cli/azure/reference-index#az-login). Utilisez un compte associé à l’abonnement Azure dans lequel vous souhaitez déployer l’application :

    ```azurecli-interactive
    az login
    ```

2. Créez une application web avec CLI. Pour plus d’exemples d’utilisation de CLI avec App Service, consultez [Exemples CLI App Service](../app-service/samples-cli.md) :

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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Les étapes suivantes vous guident dans la création d’une application web à laquelle vous attribuez une identité en utilisant Azure PowerShell :

1. Le cas échéant, installez Azure PowerShell à l’aide des instructions figurant dans le [guide Azure PowerShell](/powershell/azure/overview), puis exécutez `Login-AzAccount` pour créer une connexion avec Azure.

2. Créez une application web avec Azure PowerShell. Pour plus d’exemples sur l’utilisation d’Azure PowerShell avec App Service, consultez [Exemples App Service PowerShell](../app-service/samples-powershell.md) :

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name myResourceGroup -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Exécutez la commande `Set-AzWebApp -AssignIdentity` pour créer l’identité de cette application :

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Utilisation d’un modèle Azure Resource Manager

Vous pouvez utiliser un modèle Azure Resource Manager pour automatiser le déploiement de vos ressources Azure. Pour en savoir plus sur le déploiement sur App Service et Functions, consultez [Automatiser le déploiement de ressources dans App Service](../app-service/deploy-complex-application-predictably.md) et [Automatiser le déploiement de ressources dans Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Vous pouvez créer n’importe quelle ressource de type `Microsoft.Web/sites` avec une identité en incluant la propriété suivante dans la définition de la ressource :

```json
"identity": {
    "type": "SystemAssigned"
}
```

> [!NOTE]
> Une application peut avoir simultanément une identité attribuée par le système et une identité attribuée par l’utilisateur. Dans ce cas, la propriété `type` est `SystemAssigned,UserAssigned`

L’ajout du type attribué par le système indique à Azure de créer et de manager l’identité de votre application.

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
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

La propriété tenantId identifie le locataire Azure AD auquel appartient l’identité. La propriété principalId est un identificateur unique pour la nouvelle identité de l’application. Dans Azure AD, le principal de service a le même nom que celui que vous avez donné à votre instance App Service ou Azure Functions.

## <a name="add-a-user-assigned-identity"></a>Ajouter une identité attribuée par l’utilisateur

La création d’une application avec une identité attribuée par l’utilisateur nécessite la création de l’identité, puis l’ajout de son identificateur de ressource à la configuration de votre application.

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure

Tout d’abord, vous devrez créer une ressource d’identité attribuée par l’utilisateur.

1. Créez une ressource d’identité managée attribuée par l’utilisateur en suivant [ces instructions](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Créez une application dans le portail, comme vous le feriez normalement. Accédez-y dans le portail.

3. Si vous utilisez une application de fonction, accédez à **Fonctionnalités de la plateforme**. Pour les autres types d’application, faites défiler jusqu’au groupe **Paramètres** dans le volet de navigation de gauche.

4. Sélectionnez **Identité**.

5. Dans l’onglet **Attribuée par l’utilisateur**, cliquez sur **Ajouter**.

6. Recherchez l’identité que vous avez créée précédemment et sélectionnez-la. Cliquez sur **Add**.

    ![Identité managée dans App Service](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-an-azure-resource-manager-template"></a>Utilisation d’un modèle Azure Resource Manager

Vous pouvez utiliser un modèle Azure Resource Manager pour automatiser le déploiement de vos ressources Azure. Pour en savoir plus sur le déploiement sur App Service et Functions, consultez [Automatiser le déploiement de ressources dans App Service](../app-service/deploy-complex-application-predictably.md) et [Automatiser le déploiement de ressources dans Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Vous pouvez créer n’importe quelle ressource de type `Microsoft.Web/sites` avec une identité en incluant le bloc suivant dans la définition de la ressource, en remplaçant `<RESOURCEID>` par l’ID de ressource de l’identité requise :

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

> [!NOTE]
> Une application peut avoir simultanément une identité attribuée par le système et une identité attribuée par l’utilisateur. Dans ce cas, la propriété `type` est `SystemAssigned,UserAssigned`

L’ajout du type attribué par l’utilisateur indique à Azure d’utiliser l’identité affectée par l’utilisateur qui est spécifiée pour votre application.

Par exemple, une application web peut se présenter comme suit :

```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

Quand le site est créé, il a les propriétés supplémentaires suivantes :

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

principalId est un identificateur unique pour l’identité qui est utilisée pour l’administration d’Azure AD. clientId est un identificateur unique pour la nouvelle identité de l’application qui est utilisée pour spécifier l’identité à utiliser lors des appels de runtime.

## <a name="obtain-tokens-for-azure-resources"></a>Obtenir des jetons pour les ressources Azure

Une application peut utiliser son identité managée pour obtenir des jetons afin d’accéder à d’autres ressources protégées par Azure AD, comme Azure Key Vault. Ces jetons représentent l’application qui accède à la ressource, pas un utilisateur spécifique de l’application. 

Vous pouvez être amené à configurer la ressource cible pour autoriser l’accès à partir de votre application. Par exemple, si vous demandez un jeton pour accéder à Key Vault, vous devez vérifier que vous avez ajouté une stratégie d’accès qui inclut l’identité de votre application. Si tel n’est pas le cas, vos appels au coffre de clés sont rejetés, même s’ils incluent le jeton. Pour en savoir plus sur les ressources qui prennent en charge les jetons Azure Active Directory, consultez [Services Azure prenant en charge l’authentification Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

> [!IMPORTANT]
> Les services principaux pour les identités gérées conservent un cache par URI de ressource pendant environ 8 heures. Si vous mettez à jour la stratégie d’accès d’une ressource cible particulière et que vous récupérez immédiatement un jeton pour cette ressource, vous pouvez continuer à obtenir un jeton mis en cache avec des autorisations obsolètes jusqu’à ce que ce jeton expire. Il n’existe actuellement aucun moyen de forcer l’actualisation d’un jeton.

Il existe un protocole REST simple pour obtenir un jeton dans App Service et Azure Functions. Cela peut être utilisé pour toutes les applications et tous les langages. Pour .NET et Java, le Kit de développement logiciel (SDK) Azure fournit une abstraction par rapport à ce protocole et facilite l’expérience de développement locale.

### <a name="using-the-rest-protocol"></a>Utilisation du protocole REST

Une application avec une identité managée a deux variables d’environnement définies :

- IDENTITY_ENDPOINT - URL du service de jetons local.
- IDENTITY_HEADER - en-tête utilisé afin de limiter les attaques par falsification de requête côté serveur (SSRF). La plateforme effectue la rotation de la valeur.

**IDENTITY_ENDPOINT** est une URL locale à partir de laquelle votre application peut demander des jetons. Pour obtenir un jeton pour une ressource, effectuez une requête HTTP GET à destination de ce point de terminaison, en indiquant notamment les paramètres suivants :

> | Nom du paramètre    | Dans     | Description                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | resource          | Requête  | URI de ressource Azure AD de la ressource pour laquelle un jeton doit être obtenu. Il peut s’agir d’un des [services Azure prenant en charge l’authentification Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) ou toute autre ressource URI.    |
> | api-version       | Requête  | Version de l’API de jeton à utiliser. Utilisez « 2019-08-01 » ou une version ultérieure.                                                                                                                                                                                                                                                                 |
> | X-IDENTITY-HEADER | En-tête | Valeur de la variable d’environnement IDENTITY_HEADER. Cet en-tête est utilisé afin de limiter les attaques de falsification de requêtes côté serveur (SSRF).                                                                                                                                                                                                    |
> | client_id         | Requête  | (Facultatif) ID de client de l’identité affectée par l’utilisateur qui doit être utilisée. Ne peut pas être utilisée sur une demande qui inclut `principal_id`, `mi_res_id` ou `object_id`. Si tous les paramètres d’ID (`client_id`, `principal_id`,`object_id` et `mi_res_id`) sont omis, l’identité affectée par le système est utilisée.                                             |
> | principal_id      | Requête  | (Facultatif) ID de principal de service de l’identité affectée par l’utilisateur qui doit être utilisée. `object_id` est un alias qui peut être utilisé à la place. Ne peut pas être utilisé sur une demande qui inclut client_id, mi_res_id ou object_id. Si tous les paramètres d’ID (`client_id`, `principal_id`,`object_id` et `mi_res_id`) sont omis, l’identité affectée par le système est utilisée. |
> | mi_res_id         | Requête  | (Facultatif) L’ID de ressource Azure de l’identité affectée par l’utilisateur qui doit être utilisée. Ne peut pas être utilisée sur une demande qui inclut `principal_id`, `client_id` ou `object_id`. Si tous les paramètres d’ID (`client_id`, `principal_id`,`object_id` et `mi_res_id`) sont omis, l’identité affectée par le système est utilisée.                                      |

> [!IMPORTANT]
> Si vous tentez d’obtenir des jetons pour des identités affectées par l’utilisateur, vous devez inclure une des propriétés facultatives. Sinon, le service de jetons essaie d’obtenir un jeton pour une identité attribuée par le système, laquelle peut exister ou non.

Une réponse 200 OK correcte comprend un corps JSON avec les propriétés suivantes :

> | Nom de la propriété | Description                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | Le jeton d’accès demandé. Le service web appelant peut utiliser ce jeton pour s’authentifier auprès du service web de destination.                                                                                                                               |
> | client_id     | ID client de l’identité qui a été utilisée.                                                                                                                                                                                                       |
> | expires_on    | L’intervalle de temps lorsque le jeton d’accès expire. La date est exprimée en nombre de secondes à partir de « 1970-01-01T0:0:0Z UTC » (correspond à la revendication `exp` du jeton).                                                                                |
> | not_before    | L’intervalle de temps pendant lequel le jeton d’accès prend effet, et peut être accepté. La date est exprimée en nombre de secondes à partir de « 1970-01-01T0:0:0Z UTC » (correspond à la revendication `nbf` du jeton).                                                      |
> | resource      | La ressource pour laquelle le jeton d’accès a été demandé, correspondant au paramètre de chaîne de requête `resource` de la requête.                                                                                                                               |
> | token_type    | Indique la valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est FBearer. Pour plus d’informations sur les jetons du porteur, consultez [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |

Cette réponse est la même que la [réponse pour la demande de jeton d’accès de service à service d’Azure AD](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

> [!NOTE]
> Une version antérieure de ce protocole, avec la version de l’API « 2017-09-01 », utilisait l’en-tête `secret` au lieu de `X-IDENTITY-HEADER` et acceptait seulement la propriété `clientid` pour l’affectation par l’utilisateur. Elle retournait aussi `expires_on` à un format d’horodatage. MSI_ENDPOINT peut être utilisé comme alias pour IDENTITY_ENDPOINT et MSI_SECRET peut être utilisé comme alias pour IDENTITY_HEADER.

### <a name="rest-protocol-examples"></a>Exemples de protocole REST

Voici un exemple de demande :

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

Et voici un exemple de réponse :

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "1586984735",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer",
    "client_id": "5E29463D-71DA-4FE0-8E69-999B57DB23B0"
}
```

### <a name="code-examples"></a>Exemples de code

# <a name="net"></a>[.NET](#tab/dotnet)

> [!TIP]
> Dans le cas des langages .NET, vous pouvez également utiliser [Microsoft.Azure.Services.AppAuthentication](#asal) au lieu d’élaborer cette demande vous-même.

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2019-08-01", Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT"), resource));
    request.Headers.Add("X-IDENTITY-HEADER", Environment.GetEnvironmentVariable("IDENTITY_HEADER"));
    return await _client.SendAsync(request);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["IDENTITY_ENDPOINT"]}/?resource=${resource}&api-version=2019-08-01`,
        headers: {
            'X-IDENTITY-HEADER': process.env["IDENTITY_HEADER"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="python"></a>[Python](#tab/python)

```python
import os
import requests

identity_endpoint = os.environ["IDENTITY_ENDPOINT"]
identity_header = os.environ["IDENTITY_HEADER"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{identity_endpoint}?resource={resource_uri}&api-version=2019-08-01"
    head_msi = {'X-IDENTITY-HEADER':identity_header}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:IDENTITY_ENDPOINT + "?resource=$resourceURI&api-version=2019-08-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"X-IDENTITY-HEADER"="$env:IDENTITY_HEADER"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>Utilisation de la bibliothèque Microsoft.Azure.Services.AppAuthentication pour .NET

Pour les applications et les fonctions .NET, la façon la plus simple pour utiliser une identité managée consiste à passer par le package Microsoft.Azure.Services.AppAuthentication. Cette bibliothèque vous permet également de tester votre code localement sur votre machine de développement, à l’aide de votre compte d’utilisateur à partir de Visual Studio, [d’Azure CLI](/cli/azure) ou de l’authentification intégrée à Active Directory. Pour plus d’informations sur les options de développement local avec cette bibliothèque, consultez le [Guide de référence technique sur Microsoft.Azure.Services.AppAuthentication]. Cette section vous montre comment prendre en main la bibliothèque dans votre code.

1. Ajoutez des références aux packages [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) et aux autres packages NuGet nécessaires à votre application. L’exemple ci-dessous utilise également [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

2. Ajoutez le code suivant à votre application, modification pour cibler la bonne ressource. Cet exemple montre deux façons de travailler avec Azure Key Vault :

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Pour en savoir plus sur Microsoft.Azure.Services.AppAuthentication et les opérations qu’il expose, consultez le [Guide de référence technique sur Microsoft.Azure.Services.AppAuthentication] et [l’exemple .NET associant App Service, Key Vault et l’identité du service administré](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-azure-sdk-for-java"></a>Utilisation du SDK Azure pour Java

Pour les fonctions et applications Java, la façon la plus simple d’utiliser une identité managée consiste à utiliser le [SDK Azure pour Java](https://github.com/Azure/azure-sdk-for-java). Cette section vous montre comment prendre en main la bibliothèque dans votre code.

1. Ajoutez une référence à la [bibliothèque du SDK Azure](https://mvnrepository.com/artifact/com.microsoft.azure/azure). Pour les projets Maven, vous pouvez ajouter cet extrait de code à la section `dependencies` du fichier POM du projet :

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Utilisez l’objet `AppServiceMSICredentials` pour l’authentification. Cet exemple montre comment ce mécanisme peut être utilisé avec Azure Key Vault :

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove-an-identity"></a><a name="remove"></a>Supprimer une identité

Vous pouvez supprimer une identité attribuée par le système en désactivant la fonctionnalité à l’aide du portail, de PowerShell ou de l’interface CLI, de la même façon que vous l’avez créée. Les identités attribuées par l’utilisateur peuvent être supprimées individuellement. Pour supprimer toutes les identités, définissez le type sur « None » dans le [Modèle ARM](#using-an-azure-resource-manager-template) :

```json
"identity": {
    "type": "None"
}
```

Si vous supprimez une identité affectée par le système de cette façon, vous la supprimez également d’Azure AD. Les identités affectées par le système sont aussi automatiquement supprimées d’Azure AD quand la ressource d’application est supprimée.

> [!NOTE]
> Vous pouvez également définir le paramètre d’application WEBSITE_DISABLE_MSI, qui désactive uniquement le service de jetons local. Toutefois, cela ne touche pas à l’identité, et les outils continueront d’afficher l’identité managée comme étant activée. Par conséquent, l’utilisation de ce paramètre n’est pas recommandée.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Accéder à Azure SQL Database de manière sécurisée à l’aide d’une identité managée](app-service-web-tutorial-connect-msi.md)

[Guide de référence technique sur Microsoft.Azure.Services.AppAuthentication]: https://go.microsoft.com/fwlink/p/?linkid=862452
