---
title: Authentification Azure AD pour Application Insights (préversion)
description: Découvrez comment activer l'authentification Azure Active Directory (Azure AD) pour garantir que seule la télémétrie authentifiée est ingérée dans vos ressources Application Insights.
ms.topic: conceptual
ms.date: 08/02/2021
ms.openlocfilehash: 9d93da1a8567a7c50dac43c29e3a962652ceee33
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111466"
---
# <a name="azure-ad-authentication-for-application-insights-preview"></a>Authentification Azure AD pour Application Insights (préversion)
Application Insights prend désormais en charge l'authentification Azure Active Directory (Azure AD). À l’aide d’Azure AD, vous pouvez désormais vous assurer que seules les données de télémétrie authentifiées sont ingérées dans les ressources de vos Insights d’Application. 

En règle générale, l’utilisation de différents systèmes d’authentification peut être lourde et poser un risque, car il est difficile de gérer les informations d’identification à grande échelle. Vous pouvez maintenant choisir de ne pas utiliser l'authentification locale et vous assurer que seule la télémétrie qui est exclusivement authentifiée en utilisant [Managed Identities ](../../active-directory/managed-identities-azure-resources/overview.md) et [Azure Active Directory est ](../../active-directory/fundamentals/active-directory-whatis.md) ingérée dans votre ressource Application Insights. Cette fonctionnalité est une étape permettant d’améliorer la sécurité et la fiabilité des données de télémétrie utilisées pour rendre les opérations critiques (alertes/mise à l’échelle automatique, etc.) et les décisions commerciales.

> [!IMPORTANT]
> L'authentification Azure AD est actuellement en PRÉVERSION.
> Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Voici les kits de développement logiciel (SDK) et scénarios non pris en charge dans la version préliminaire publique :
- [Application Insights Java 2.x SDK](java-2x-agent.md) - L'authentification Azure AD est uniquement disponible pour Application Insights Java Agent >=3.2.0. 
- [SDK Web JavaScript ApplicationInsights](javascript.md). 
- [Application Insights OpenCensus Python SDK](opencensus-python.md) avec Python version 3.4 et 3.5.
- La [Azure ad basée sur un certificat ou une clé secrète](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) n’est pas recommandée pour la production. Utilisez plutôt les Identités gérées. 
- Activé par défaut de l’analyse du code (pour les langues) des App Service, machines virtuelles/groupes de machines virtuelles identiques, Azure Functions etc.
- [Tests de disponibilité](availability-overview.md).
- [Profiler](profiler-overview.md).

## <a name="prerequisites-to-enable-azure-ad-authentication-ingestion"></a>Conditions préalables à l’activation de l’ingestion d’authentification Azure AD

- Connaissances préalables :
    - [Identité managée](../../active-directory/managed-identities-azure-resources/overview.md). 
    - Le [principal du service](../../active-directory/develop/howto-create-service-principal-portal.md).
    - [Attribution de rôles Azure](../../role-based-access-control/role-assignments-portal.md). 
- Vous disposez d’un rôle de « Propriétaire » pour le groupe de ressources afin d’accorder l’accès à l’aide des [rôles intégrés Azure](../../role-based-access-control/built-in-roles.md).

## <a name="configuring-and-enabling-azure-ad-based-authentication"></a>Configuration et activation de l’authentification Azure AD 

1. Créez une identité, si vous n’en avez pas déjà un, à l’aide de l’identité gérée ou du principal du service :

    1. Identité managée (Recommandé) :

        [Configurez une identité gérée pour votre service Azure](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) (machine virtuelle, app service, etc.).

    1. Utilisation du principal du service (non recommandé) :

        Pour plus d'informations sur la création d'une application Azure AD et d'un principal de service pouvant accéder aux ressources, voir [Créer un principal de service](../../active-directory/develop/howto-create-service-principal-portal.md).

1. Affecter un rôle au principal de service Azure. 

    Suivez les étapes décrites dans [assigner des rôles Azure](../../role-based-access-control/role-assignments-portal.md) pour ajouter le rôle « analyse des métriques Publisher » de la ressource Application Insights cible à la ressource Azure à partir de laquelle les données de télémétrie sont envoyées. 

    > [!NOTE]
    > Bien que le rôle « surveillance des métriques Publisher » indique des mesures, il publie toutes les données de télémétrie sur l’application Informations ressource.

1. Suivez les instructions de configuration ci-dessous.

### <a name="aspnet-and-net"></a>[ASP.NET et .NET](#tab/net)

> [!NOTE]
> La prise en charge de Azure AD dans le kit de développement logiciel (SDK) Application Insights .net est incluse à partir de la [version 2.18-Beta3](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.18.0-beta3).

Application Insights SDK .net prend en charge les classes d’informations d’identification fournies par l' [identité Azure](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity#credential-classes).

- `DefaultAzureCredential` est recommandé pour le développement local.
- `ManagedIdentityCredential`est recommandé pour les identités gérées attribuées par le système et par l'utilisateur.
    - Pour l’affectation par le système, utilisez le constructeur par défaut sans paramètres.
    - Pour la valeur assignée à l’utilisateur, fournissez le clientId au constructeur.
- `ClientSecretCredential` est recommandé pour les principaux de service. 
    - Fournissez les tenantId, clientId et clientSecret au constructeur.

Voici un exemple de création et de configuration manuelles d’un `TelemetryConfiguration` à l’aide de .NET :

```csharp
var config = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/"
}
var credential = new DefaultAzureCredential();
config.SetAzureTokenCredential(credential);

```

Vous trouverez ci-dessous un exemple de configuration de l'`TelemetryConfiguration`utilisation d'ASP.NET Core :
```csharp
services.Configure<TelemetryConfiguration>(config =>
{
       var credential = new DefaultAzureCredential();
       config.SetAzureTokenCredential(credential);
});
services.AddApplicationInsightsTelemetry(new ApplicationInsightsServiceOptions
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/"
});
```
### <a name="nodejs"></a>[Node.JS](#tab/nodejs)
 
> [!NOTE]
> La prise en charge de Azure AD dans le kit de développement logiciel (SDK) Application Insights .net est incluse à partir de la [version 2.1.0-beta.1](https://www.npmjs.com/package/applicationinsights/v/2.1.0-beta.1).

Application Insights Node.JS prend en charge les classes d’informations d’identification fournies par [l’identité Azure](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/identity/identity#credential-classes).

#### <a name="defaultazurecredential"></a>DefaultAzureCredential

```javascript
let appInsights = require("applicationinsights");
import { DefaultAzureCredential } from "@azure/identity"; 
 
const credential = new DefaultAzureCredential();
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/").start();
appInsights.defaultClient.aadTokenCredential = credential;

```

#### <a name="clientsecretcredential"></a>ClientSecretCredential

```javascript
let appInsights = require("applicationinsights");
import { ClientSecretCredential } from "@azure/identity"; 
 
const credential = new ClientSecretCredential(
    "<YOUR_TENANT_ID>",
    "<YOUR_CLIENT_ID>",
    "<YOUR_CLIENT_SECRET>"
  );
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/").start();
appInsights.defaultClient.aadTokenCredential = credential;

```

### <a name="java"></a>[Java](#tab/java)

> [!NOTE]
> La prise en charge d’Azure AD dans l’agent Application Insights est incluse à partir de la version [Java 3.2.0-BETA](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.2.0-BETA). 

1. [Configurez votre application avec l'agent Java.](java-in-process-agent.md#quickstart)

    > [!IMPORTANT]
    > Utilisez la chaîne de connexion complète qui comprend « IngestionEndpoint » lors de la configuration de votre application avec l’agent Java. Par exemple, `InstrumentationKey=XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX;IngestionEndpoint=https://XXXX.applicationinsights.azure.com/`.

    > [!NOTE]
    >  Pour plus d’informations sur la migration du kit de développement logiciel (SDK) 2.X vers l’agent java 3.X, consultez [upgrading from Application Insights java 2.x SDK](java-standalone-upgrade-from-2x.md).

1. Ajoutez la configuration JSON à ApplicationInsights.jssur le fichier de configuration en fonction de l’authentification que vous utilisez. Nous recommandons aux utilisateurs d’utiliser des identités gérées.

#### <a name="system-assigned-managed-identity"></a>Identité managée affectée par le système

Vous trouverez ci-dessous un exemple de configuration de l’agent Java pour utiliser l’identité gérée affectée par le système pour l’authentification avec Azure AD.

```JSON
{ 
  "connectionString": "App Insights Connection String with IngestionEndpoint", 
  "preview": { 
    "authentication": { 
      "enabled": true, 
      "type": "SAMI" 
    } 
  } 
} 
```

#### <a name="user-assigned-managed-identity"></a>Identité managée affectée par l’utilisateur

Vous trouverez ci-dessous un exemple de configuration de l’agent Java pour utiliser l’identité gérée affectée à l’utilisateur pour l’authentification avec Azure AD.

```JSON
{ 
  "connectionString": "App Insights Connection String with IngestionEndpoint", 
  "preview": { 
    "authentication": { 
      "enabled": true, 
      "type": "UAMI", 
      "clientId":"<USER-ASSIGNED MANAGED IDENTITY CLIENT ID>" 
    } 
  }  
} 
```
:::image type="content" source="media/azure-ad-authentication/user-assigned-managed-identity.png" alt-text="Capture d’écran de l’identité attribuée à l’utilisateur." lightbox="media/azure-ad-authentication/user-assigned-managed-identity.png":::

#### <a name="client-secret"></a>Clé secrète client

Voici un exemple de configuration de l’agent Java pour utiliser le principal du service pour l’authentification avec Azure AD. Nous recommandons aux utilisateurs d’utiliser ce type d’authentification uniquement pendant le développement. L’objectif ultime de l’ajout de la fonctionnalité d’authentification consiste à éliminer les secrets.

```JSON
{ 
  "connectionString": "App Insights Connection String with IngestionEndpoint",
   "preview": { 
        "authentication": { 
        "enabled": true, 
        "type": "CLIENTSECRET", 
        "clientId":"<YOUR CLIENT ID>", 
        "clientSecret":"<YOUR CLIENT SECRET>", 
        "tenantId":"<YOUR TENANT ID>" 
    } 
  } 
} 
```
:::image type="content" source="media/azure-ad-authentication/client-secret-tenant-id.png" alt-text="Capture d’écran de la clé secrète client avec tenantID et ClientID." lightbox="media/azure-ad-authentication/client-secret-tenant-id.png":::

:::image type="content" source="media/azure-ad-authentication/client-secret-cs.png" alt-text="Capture d’écran de la clé secrète client avec la clé secrète client." lightbox="media/azure-ad-authentication/client-secret-cs.png":::

### <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> L’authentification Azure AD est disponible uniquement pour Python v 2.7, v 3.6 et v 3.7. La prise en charge de Azure AD dans le kit de développement logiciel (SDK) Opencensus Application Insights Python est incluse à partir de la version bêta [Opencensus-ext-Azure 1.1 b0](https://pypi.org/project/opencensus-ext-azure/1.1b0/).


Construisez les [informations d'identification](/python/api/overview/azure/identity-readme?view=azure-python#credentials) appropriées et passez-les dans le constructeur de l'exportateur Azure Monitor. Assurez-vous que votre chaîne de connexion est configurée avec la clé d’instrumentation et le point de terminaison d’ingestion de votre ressource.

Voici les types d’authentification pris en charge par les exportateurs Opencensus Azure Monitor. Il est recommandé d’utiliser des identités gérées dans des environnements de production.


#### <a name="system-assigned-managed-identity"></a>Identité managée affectée par le système

```python
from azure.identity import ManagedIdentityCredential

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

credential = ManagedIdentityCredential()
tracer = Tracer(
    exporter=AzureExporter(credential=credential, connection_string="InstrumentationKey=<your-instrumentation-key>;IngestionEndpoint=<your-ingestion-endpoint>"),
    sampler=ProbabilitySampler(1.0)
)
...

```

#### <a name="user-assigned-managed-identity"></a>Identité managée affectée par l’utilisateur

```python
from azure.identity import ManagedIdentityCredential

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

credential = ManagedIdentityCredential(client_id="<client-id>")
tracer = Tracer(
    exporter=AzureExporter(credential=credential, connection_string="InstrumentationKey=<your-instrumentation-key>;IngestionEndpoint=<your-ingestion-endpoint>"),
    sampler=ProbabilitySampler(1.0)
)
...

```

#### <a name="client-secret"></a>Clé secrète client

```python
from azure.identity import ClientSecretCredential

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tenant_id = "<tenant-id>"
client_id = "<client-id"
client_secret = "<client-secret>"

credential = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)
tracer = Tracer(
    exporter=AzureExporter(credential=credential, connection_string="InstrumentationKey=<your-instrumentation-key>;IngestionEndpoint=<your-ingestion-endpoint>"),
    sampler=ProbabilitySampler(1.0)
)
...
```
---

## <a name="disable-local-authentication"></a>Désactiver l’authentification locale

Une fois l’authentification Azure AD activée, vous pouvez choisir de désactiver l’authentification locale. Cela vous permettra d’ingérer la télémétrie authentifiée exclusivement par Azure AD et a un impact sur l’accès aux données (par exemple, via des clés API). 

Vous pouvez désactiver l’authentification locale à l’aide du Portail Azure, Azure Policy ou par programmation.

### <a name="azure-portal"></a>Portail Azure

1.  À partir de votre ressource Application Insights, sélectionnez **Propriétés** sous le titre *Configurer* dans la barre de menus de gauche. Sélectionnez ensuite **Activé (cliquez pour modifier)** si l'authentification locale est activée. 

    :::image type="content" source="./media/azure-ad-authentication/enabled.png" alt-text="Capture d’écran des propriétés sous le bouton * configurer * sélectionné et activé (Cliquer pour modifier) authentification locale.":::

1. Sélectionnez **Désactivé** et appliquez les modifications. 

    :::image type="content" source="./media/azure-ad-authentication/disable.png" alt-text="Capture d’écran de l’authentification locale avec le bouton activé/désactivé mis en surbrillance.":::

1. Une fois que votre ressource a désactivé l'authentification locale, vous verrez l'information correspondante dans le volet **Aperçu**.

    :::image type="content" source="./media/azure-ad-authentication/overview.png" alt-text="Capture d’écran de l’onglet vue d’ensemble avec l’onglet désactivé (Cliquer pour modifier) mis en surbrillance.":::

### <a name="azure-policy"></a>Azure Policy 

Azure Policy pour « DisableLocalAuth » refusera aux utilisateurs de créer une ressource d’Application Insights sans affecter la valeur « true » à ce paramètre de propriété. Le nom de la stratégie est « Application Insights composants doivent bloquer l’ingestion de l’authentification non AAD ».

Pour appliquer cette définition de stratégie à votre abonnement, [créez une attribution de stratégie et attribuez-lui la stratégie](../../governance/policy/assign-policy-portal.md).

Voici la définition du modèle de stratégie :
```JSON
{
    "properties": {
        "displayName": "Application Insights components should block non-AAD auth ingestion",
        "policyType": "BuiltIn",
        "mode": "Indexed",
        "description": "Improve Application Insights security by disabling log ingestion that are not AAD-based.",
        "metadata": {
            "version": "1.0.0",
            "category": "Monitoring"
        },
        "parameters": {
            "effect": {
                "type": "String",
                "metadata": {
                    "displayName": "Effect",
                    "description": "The effect determines what happens when the policy rule is evaluated to match"
                },
                "allowedValues": [
                    "audit",
                    "deny",
                    "disabled"
                ],
                "defaultValue": "audit"
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Insights/components"
                    },
                    {
                        "field": "Microsoft.Insights/components/DisableLocalAuth",
                        "notEquals": "true"                        
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effect')]"
            }
        }
    }
}
```

### <a name="programmatic-enablement"></a>Activation par programmation 

La propriété `DisableLocalAuth` est utilisée pour désactiver toute authentification locale sur votre ressource d’Application Insights. Lorsque la valeur est `true`, cette propriété impose que Azure ad authentification doive être utilisée pour tous les accès.

Voici un exemple du modèle Azure Resource Manager que vous pouvez utiliser pour créer une ressource d’Application Insights basée sur un espace de travail avec l’authentification locale désactivée.

```JSON 
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        },
        "disableLocalAuth": {
            "type": "bool"
        }
     
    },
    "resources": [
        {
        "name": "[parameters('name')]",
        "type": "microsoft.insights/components",
        "location": "[parameters('regionId')]",
        "tags": "[parameters('tagsArray')]",
        "apiVersion": "2020-02-02-preview",
        "dependsOn": [],
        "properties": {
            "Application_Type": "[parameters('type')]",
            "Flow_Type": "Redfield",
            "Request_Source": "[parameters('requestSource')]",
            "WorkspaceResourceId": "[parameters('workspaceResourceId')]",
            "DisableLocalAuth": "[parameters('disableLocalAuth')]"
            }
    }
 ]
}

```

## <a name="troubleshooting"></a>Dépannage

Cette section fournit des scénarios de résolution des problèmes distincts et des étapes que les utilisateurs peuvent entreprendre pour résoudre les problèmes avant qu’ils ne génèrent un ticket de support. 

### <a name="ingestion-http-errors"></a>Erreurs HTTP d’ingestion

Le service d'ingestion renverra des erreurs spécifiques, quelle que soit la langue du SDK. Le trafic réseau peut être collecté à l’aide d’un outil comme Fiddler. Filtrez le trafic destiné au point de terminaison d’ingestion défini dans la chaîne de connexion.

#### <a name="http11-400-authentication-not-support"></a>Authentification HTTP/1.1 400 non prise en charge 

Cette erreur indique que la ressource Application Insights a été configurée pour Azure AD uniquement, mais que le kit SDK n’a pas été correctement configuré et envoie les données à la mauvaise API.

> [!NOTE]
>  "v2/track" ne prend pas en charge Azure AD. Lorsque le SDK est correctement configuré, la télémétrie sera envoyée à "v2.1/track".

Les étapes suivantes doivent être pour vérifier la configuration du kit de développement logiciel (SDK).

#### <a name="http11-401-authorization-required"></a>HTTP/1.1 401 Autorisation requise

Cette erreur indique que le kit SDK a été correctement configuré, mais qu’il n’est pas parvenu à acquérir un jeton valide. Il peut s’agir d’un problème lié à Azure Active Directory.

Les étapes suivantes devraient consister à identifier les exceptions dans les journaux du SDK ou les erreurs de réseau d'Azure Identity.

#### <a name="http11-403-unauthorized"></a>HTTP/1.1 403 Non autorisé 

Cette erreur indique que le kit SDK a été configuré avec des informations d’identification qui ne confèrent pas d’autorisation sur la ressource ou l’abonnement Application Insights.

Les étapes suivantes consistent à examiner le contrôle d'accès de la ressource Application Insights. Le kit SDK doit être configuré avec des informations d’identification qui ont reçu le rôle «Éditeur de métriques de surveillance».

### <a name="language-specific-troubleshooting"></a>Résolution des problèmes propres à la langue

### <a name="aspnet-and-net"></a>[ASP.NET et .NET](#tab/net)

#### <a name="event-source"></a>Source de l’événement

L’Application Insights SDK .net émet des journaux d’erreurs à l’aide de la source d’événements. Pour en savoir plus sur la collecte des journaux des sources d’événements, consultez la [section Dépannage des journaux de collecte de données avec PerfView](asp-net-troubleshoot-no-data.md#PerfView).

Si le kit de développement logiciel (SDK) ne parvient pas à obtenir un jeton, le message d’exception est enregistré sous la forme : «Échec de l’extraction du jeton AAD. Message d’erreur : ”

### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

Les journaux internes peuvent être activés à l’aide de la configuration suivante. Une fois cette option activée, les journaux des erreurs s’affichent dans la console, y compris toute erreur liée à l’intégration de Azure AD. Par exemple, il n’est pas possible de générer le jeton quand des informations d’identification incorrectes sont fournies ou des erreurs lorsque le point de terminaison d’ingestion échoue à s’authentifier à l’aide des informations d’identification fournies.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/").setInternalLogging(true, true);
```

### <a name="java"></a>[Java](#tab/java)

#### <a name="http-traffic"></a>Trafic HTTP

Vous pouvez inspecter le trafic réseau à l’aide d’un outil tel que Fiddler. Pour activer le trafic pour le tunnel via Fiddler, ajoutez les paramètres de proxy suivants dans le fichier de configuration :

```JSON
"proxy": {
"host": "localhost",
"port": 8888
}
```

Ou ajoutez les arguments JVM suivants lors de l’exécution de votre application :`-Djava.net.useSystemProxies=true -Dhttps.proxyHost=localhost -Dhttps.proxyPort=8888`

Si Azure AD est activé dans l’agent, le trafic sortant inclut l’en-tête HTTP « Authorization ».


#### <a name="401-unauthorized"></a>401 Non autorisé 

Si le message d’avertissement suivant s’affiche dans le fichier journal`WARN c.m.a.TelemetryChannel - Failed to send telemetry with status code: 401, please check your credentials`, il indique que l’agent n’a pas réussi à envoyer des données de télémétrie. Vous n’avez probablement pas activé l’authentification Azure AD sur l’agent, mais votre ressource Application Insights est configurée avec `DisableLocalAuth: true`. Assurez-vous que vous passez des informations d’identification valides et qu’il est autorisé à accéder à votre ressource d’Application Insights.


Si vous utilisez Fiddler, l’en-tête de réponse suivant peut s’afficher : `HTTP/1.1 401 Unauthorized - please provide the valid authorization token` .


#### <a name="credentialunavailableexception"></a>CredentialUnavailableException

Si l’exception suivante est visible dans le fichier journal  `com.azure.identity.CredentialUnavailableException: ManagedIdentityCredential authentication unavailable. Connection to IMDS endpoint cannot be established` , cela signifie que l’agent n’a pas réussi à acquérir le jeton d’accès et que la raison probable est que vous avez fourni un code non valide `clientId` dans la configuration d’identité gérée attribuée à l’utilisateur.


#### <a name="failed-to-send-telemetry"></a>Échec de l’envoi de la télémétrie

Si le message d’avertissement suivant s’affiche dans le fichier journal`WARN c.m.a.TelemetryChannel - Failed to send telemetry with status code: 403, please check your credentials`, il indique que l’agent n’a pas réussi à envoyer des données de télémétrie. Cela peut être dû au fait que les informations d’identification fournies n’autorisent pas l’accès à la télémétrie dans le composant.

Si vous utilisez Fiddler, l’en-tête de réponse suivant peut s’afficher : `HTTP/1.1 403 Forbidden - provided credentials do not grant the access to ingest the telemetry into the component` .

L’une des raisons suivantes peut en être la cause :
- Vous avez créé la ressource en activant l'identité gérée attribuée par le système ou vous avez peut-être associé l'identité attribuée par l'utilisateur à la ressource mais vous avez oublié d'ajouter le `Monitoring Metrics Publisher` rôle à la ressource (si vous utilisez SAMI) ou à l'identité attribuée par l'utilisateur (si vous utilisez UAMI).
- Vous avez fourni les informations d’identification appropriées pour récupérer les jetons d’accès, mais les informations d’identification n’appartiennent pas à la ressource d’Application Insights appropriée. Vérifiez que vous voyez votre ressource (machine virtuelle, app service, etc.) ou une identité affectée par l’utilisateur avec des `Monitoring Metrics Publisher` rôles dans votre ressource d’Application Insights.

#### <a name="invalid-tenantid"></a>TenantId non valide

Si l’exception suivante est visible dans le fichier journal `com.microsoft.aad.msal4j.MsalServiceException: Specified tenant identifier <TENANT-ID> is neither a valid DNS name, nor a valid external domain.`, cela signifie que l’agent n’a pas réussi à acquérir le jeton d’accès et que la raison probable est que vous avez fourni un code non valide `tenantId` dans la configuration d’identité gérée attribuée à l’utilisateur.

#### <a name="invalid-client-secret"></a>Secret client non valide

Si l’exception suivante est visible dans le fichier journal `com.microsoft.aad.msal4j.MsalServiceException: Invalid client secret is provided`, cela signifie que l’agent n’a pas réussi à acquérir le jeton d’accès et que la raison probable est que vous avez fourni un code non valide `clientSecret` dans la configuration d’identité gérée attribuée à l’utilisateur.


#### <a name="invalid-clientid"></a>ClientId non valide

Si l'exception suivante apparaît dans le fichier journal `com.microsoft.aad.msal4j.MsalServiceException: Application with identifier <CLIENT_ID> was not found in the directory`, cela signifie que l'agent n'a pas réussi à acquérir le jeton d'accès et la raison probable est que vous avez fourni un "clientId" invalide ou erroné dans votre configuration du secret du client.

 Cela peut se produire si l’application n’a pas été installée par l’administrateur du locataire ni acceptée par un utilisateur dans le locataire. Vous avez peut-être envoyé votre requête d’authentification au locataire incorrect.

### <a name="python"></a>[Python](#tab/python)

#### <a name="error-starts-with-credential-error-with-no-status-code"></a>L'erreur commence par "credential error" (sans code d'état)

Un problème se pose sur les informations d’identification que vous utilisez et le client n’est pas en mesure d’obtenir un jeton pour l’autorisation. Cela est généralement dû à l’absence des données requises pour l’état. Un exemple peut passer un ManagedIdentityCredential système, mais la ressource n’est pas configurée pour utiliser l’identité gérée par le système.

#### <a name="error-starts-with-authentication-error-with-no-status-code"></a>L’erreur commence par « erreur d’authentification » (sans code d’état)

Le client n’a pas pu s’authentifier avec les informations d’identification fournies. Se produit généralement lorsque les informations d’identification utilisées n’ont pas d’attributions de rôles correctes.

#### <a name="im-getting-a-status-code-400-in-my-error-logs"></a>J’obtiens un code d’état 400 dans mes journaux des erreurs

Vous n’avez probablement pas d’informations d’identification ou vos informations d’identification ont la valeur `None` , mais votre Application Insights ressource est configurée avec `DisableLocalAuth: true`. Assurez-vous que vous passez des informations d’identification valides et qu’il est autorisé à accéder à votre ressource d’Application Insights.

#### <a name="im-getting-a-status-code-403-in-my-error-logs"></a>J’obtiens un code d’état 403 dans mes journaux des erreurs

Se produit généralement lorsque les informations d’identification fournies n’accordent pas l’accès à la télémétrie de réception pour la ressource Application Insights. Vérifiez que votre ressource d’IA a les attributions de rôle appropriées.

---
## <a name="next-steps"></a>Étapes suivantes
* [Surveillez votre télémétrie dans le portail](overview-dashboard.md).
* [Diagnostiquer avec le flux de métriques en direct](live-stream.md).
