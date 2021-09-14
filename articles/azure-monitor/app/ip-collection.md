---
title: Collection d’adresses IP dans Azure Application Insights | Microsoft Docs
description: Découvrez comment Application Insights gère les adresses IP et la géolocalisation.
ms.topic: conceptual
ms.date: 09/23/2020
ms.custom: devx-track-js, devx-track-azurepowershell
ms.openlocfilehash: 28b3fb6242d496be7962961d36db3a85c6187d8e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532554"
---
# <a name="geolocation-and-ip-address-handling"></a>Gestion de la géolocalisation et des adresses IP

Cet article explique le fonctionnement de la recherche de géolocalisation et de la gestion des adresses IP dans Application Insights, ainsi que la façon de modifier le comportement par défaut.

## <a name="default-behavior"></a>Comportement par défaut

Par défaut, les adresses IP sont collectées temporairement, mais elles ne sont pas stockées dans Application Insights. Le processus de base est le suivant :

Lorsque les données de télémétrie sont envoyées à Azure, Application Insights utilise l’adresse IP pour effectuer une recherche de géolocalisation à l’aide de [GeoLite2 à partir de MaxMind](https://dev.maxmind.com/geoip/geoip2/geolite2/). Application Insights utilise les résultats de cette recherche pour renseigner les champs `client_City`, `client_StateOrProvince` et `client_CountryOrRegion`. L’adresse est alors ignorée et `0.0.0.0` est écrit dans le champ `client_IP`. 

Les types de télémétrie sont les suivants :

* Télémétrie de navigateur : Application Insights collecte l’adresse IP de l’expéditeur. Le point de terminaison d’ingestion calcule l’adresse IP.
* Télémétrie de serveur : Le module de télémétrie d’Application Insights collecte temporairement l’adresse IP du client. Cette adresse IP n’est pas collectée localement quand l’en-tête `X-Forwarded-For` est défini. Quand la liste entrante d’adresses IP contient plusieurs éléments, la dernière adresse IP est utilisée pour renseigner les champs de géolocalisation.

Ce comportement est conçu afin d’éviter la collecte inutile de données personnelles. Dans la mesure du possible, nous vous recommandons d’éviter la collecte des données personnelles. 

> [!NOTE]
> Bien que le paramètre par défaut est de ne pas collecter les adresses IP, vous pouvez remplacer ce comportement. Nous vous recommandons de vérifier que la collecte n’enfreint pas les exigences en matière de conformité ni la réglementation locale. 
>
> Pour en savoir plus sur la gestion des données personnelles dans Application Insights, consultez les [conseils relatifs aux données personnelles](../logs/personal-data-mgmt.md).


## <a name="storage-of-ip-address-data"></a>Stockage des données d’adresse IP

Pour activer la collecte et le stockage d’adresses IP, la propriété `DisableIpMasking` du composant Application Insights doit être définie sur `true`. Vous pouvez définir cette propriété par l’intermédiaire de modèles Azure Resource Manager ou en appelant l’API REST. 

### <a name="azure-resource-manager-template"></a>Modèle Azure Resource Manager

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>Portail

Si vous devez uniquement modifier le comportement d’une seule ressource Application Insights, utilisez le portail Azure. 

1. Accédez à votre ressource Application Insights, puis sélectionnez **Automation** > **Exporter le modèle**. 

2. Sélectionnez **Déployer**.

    ![Capture d’écran montrant le bouton Déployer encadré en rouge.](media/ip-collection/deploy.png)

3. Sélectionnez **Modifier un modèle**.

    ![Capture d’écran montrant le bouton Modifier encadré en rouge, ainsi qu’un avertissement sur le groupe de ressources](media/ip-collection/edit-template.png)

    > [!NOTE]
    > Si vous rencontrez l’erreur suivante (telle qu’indiquée dans la capture d’écran), vous pouvez la résoudre : « Le groupe de ressources se trouve dans un emplacement qui n’est pas pris en charge par une ou plusieurs ressources dans le modèle. Choisissez un autre groupe de ressources. » Sélectionnez temporairement un autre groupe de ressources dans la liste déroulante, puis sélectionnez à nouveau votre groupe de ressources d’origine.

4. Dans le modèle JSON, recherchez `properties` dans `resources`, ajoutez une virgule au dernier champ JSON, puis ajoutez la nouvelle ligne suivante : `"DisableIpMasking": true`. Ensuite, sélectionnez **Enregistrer**.

    ![Capture d’écran montrant l’ajout d’une virgule et d’une nouvelle ligne après la propriété pour la source de la demande](media/ip-collection/save.png)

5. Sélectionnez **Vérifier + créer** > **Créer**.

    > [!NOTE]
    > Si vous voyez le message « Votre déploiement a échoué », consultez les détails de votre déploiement pour trouver celui avec le type `microsoft.insights/components` et vérifiez son état. S’il réussit, les modifications apportées à `DisableIpMasking` ont été déployées.

6. Une fois le déploiement terminé, les nouvelles données de télémétrie sont enregistrées.

    Si vous sélectionnez et modifiez à nouveau le modèle, vous voyez uniquement le modèle par défaut sans la propriété nouvellement ajoutée. Si vous ne voyez pas les données d’adresse IP et souhaitez confirmer que `"DisableIpMasking": true` est défini, exécutez les commandes PowerShell suivantes :
    
    ```powershell
    # Replace `Fabrikam-dev` with the appropriate resource and resource group name.
    # If you aren't using Azure Cloud Shell, you need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    La liste des propriétés est retournée en tant que résultat. L’une des propriétés doit être lue `DisableIpMasking: true`. Si vous exécutez ces commandes PowerShell avant de déployer la nouvelle propriété avec Azure Resource Manager, la propriété n’existera pas.

### <a name="rest-api"></a>API REST

La charge utile de l’[API REST](/rest/api/azure/) pour apporter les mêmes modifications est la suivante :

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>Initialiseur de télémétrie

Si vous avez besoin d’une alternative plus flexible que `DisableIpMasking`, vous pouvez utiliser un [initialiseur de télémétrie](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) pour copier tout ou partie de l’adresse IP dans un champ personnalisé. 

# <a name="net"></a>[.NET](#tab/net)

### <a name="aspnet-or-aspnet-core"></a>ASP.NET ou ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> Si vous ne parvenez pas à accéder à `ISupportProperties`, assurez-vous d’exécuter la dernière version stable du kit SDK Application Insights. `ISupportProperties` est destiné à des valeurs de cardinalité élevées. `GlobalProperties` est plus approprié pour les valeurs de cardinalité faible, telles que le nom de la région et le nom de l’environnement.

### <a name="enable-the-telemetry-initializer-for-aspnet"></a>Activer l’initialiseur de télémétrie pour ASP.NET

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-the-telemetry-initializer-for-aspnet-core"></a>Activer l’initialiseur de télémétrie pour ASP.NET Core

Vous pouvez créer votre initialiseur de télémétrie de la même façon pour ASP.NET Core que pour ASP.NET. Pour activer l’initialiseur, utilisez l’exemple suivant comme référence :

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```
# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```
# <a name="client-side-javascript"></a>[JavaScript côté client](#tab/javascript)

### <a name="client-side-javascript"></a>JavaScript côté client

Contrairement aux kits SDK côté serveur, le kit SDK JavaScript côté client ne calcule pas d’adresse IP. Par défaut, le calcul d’adresse IP pour la télémétrie côté client s’effectue au point de terminaison d’ingestion dans Azure. 

Si vous souhaitez calculer l’adresse IP directement côté client, vous devez ajouter votre propre logique personnalisée et utiliser le résultat pour définir l’étiquette `ai.location.ip`. Quand `ai.location.ip` est défini, le point de terminaison d’ingestion n’effectue pas le calcul d’adresse IP et l’adresse IP fournie est utilisée pour la recherche de géolocalisation. Dans ce scénario, l’adresse IP est toujours mise à zéro par défaut. 

Pour conserver l’intégralité de l’adresse IP calculée à partir de votre logique personnalisée, vous pouvez utiliser un initialiseur de télémétrie pour copier les données d’adresse IP que vous avez fournies dans `ai.location.ip` vers un champ personnalisé distinct. Mais là encore, contrairement aux kits SDK côté serveur, le kit SDK côté client ne calculera pas l’adresse IP pour vous s’il ne peut pas compter sur les bibliothèques tierces ou sur votre propre logique personnalisée.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

Si les données côté client traversent un proxy avant d’être transférées vers le point de terminaison d’ingestion, le calcul d’adresse IP peut afficher l’adresse IP du proxy et non celle du client. 

---

### <a name="view-the-results-of-your-telemetry-initializer"></a>Afficher les résultats de votre initialiseur de télémétrie

Si vous envoyez un nouveau trafic à votre site et attendez quelques minutes, vous pouvez exécuter une requête pour confirmer que la collecte fonctionne :

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Les adresses IP nouvellement collectées apparaîtront dans la colonne `customDimensions_client-ip`. La colonne `client-ip` par défaut remettra à zéro tout de même quatre octets. 

Si vous effectuez le test à partir de localhost et que la valeur de `customDimensions_client-ip` est `::1`, cette valeur est le comportement attendu. La valeur `::1` représente l’adresse de bouclage dans IPv6. Elle est équivalente à `127.0.0.1` dans IPv4.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la [collecte de données personnelles](../logs/personal-data-mgmt.md) dans Application Insights.

* En savoir plus sur le fonctionnement de la [collecte d’adresses IP](https://apmtips.com/posts/2016-07-05-client-ip-address/) dans Application Insights. Cet article est un billet de blog externe plus ancien écrit par l’un de nos ingénieurs. Il a été écrit avant le comportement par défaut actuel où l’adresse IP est enregistrée en tant que `0.0.0.0`, mais il examine plus en détail les mécanismes de l’initialiseur de télémétrie intégré.
