---
title: Service DNS dans Azure Service Fabric | Microsoft Docs
description: Utilisez le service DNS de Service Fabric pour détecter des microservices depuis le cluster.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/27/2017
ms.author: msfussell
ms.openlocfilehash: 656aed1f1fbd3294c4318520058ace480fd2219c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204992"
---
# <a name="dns-service-in-azure-service-fabric"></a>Service DNS dans Azure Service Fabric
Le service DNS est un service système facultatif que vous pouvez activer dans votre cluster pour détecter d’autres services utilisant le protocole DNS. 

De nombreux services, en particulier les services en conteneur, peuvent avoir un nom d’URL existant, et il est utile de pouvoir utiliser le protocole DNS standard (plutôt que le protocole d’affectation de noms), notamment dans les scénarios de développement et de transfert. Le service DNS vous permet de mapper les noms DNS à un nom de service et, par conséquent, de résoudre les adresses IP des points de terminaison. 

Le service DNS mappe les noms DNS aux noms de service, qui sont à leur tour résolus par le service de nommage pour retourner le point de terminaison de service. Le nom DNS du service est fourni au moment de la création.

![points de terminaison de service](./media/service-fabric-dnsservice/dns.png)

Les ports dynamiques ne sont pas pris en charge par le service DNS. Pour résoudre des services exposés sur des ports dynamiques, utilisez le [service de proxy inverse](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>Activation du service DNS
Quand vous créez un cluster à l’aide du portail, le service DNS est activé par défaut dans la case à cocher **Inclure le service DNS** du menu **Configuration du cluster** :

![Activation du service DNS via le portail][2]

Si vous n’utilisez pas le portail pour créer votre cluster ou si vous mettez à jour un cluster existant, vous devez activer le service DNS dans un modèle :

- Pour déployer un nouveau cluster, vous pouvez utiliser les [exemples de modèles](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) ou créer votre propre modèle Resource Manager. 
- Pour mettre à jour un cluster existant, vous pouvez accéder au groupe de ressources du cluster dans le portail et cliquer sur **Script d’automatisation** pour travailler avec un modèle qui reflète l’état actuel du cluster et d’autres ressources du groupe. Pour plus d’informations, consultez [Exportation du modèle à partir d’un groupe de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template#export-the-template-from-resource-group).

Une fois que vous avez un modèle, vous pouvez activer le service DNS en effectuant les étapes suivantes :

1. Vérifiez que `apiversion` a la valeur `2017-07-01-preview` ou plus pour la ressource `Microsoft.ServiceFabric/clusters` et, si ce n’est pas le cas, procédez à une mise à jour comme indiqué dans l’extrait de code suivant :

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. À présent, activez le service DNS en ajoutant la section `addonFeatures` suivante après la section `fabricSettings`, comme indiqué dans l’extrait de code ci-après : 

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. Après avoir mis à jour votre modèle de cluster avec les modifications précédentes, appliquez-les et laissez la mise à niveau s’accomplir. Une fois la mise à niveau terminée, le service DNS démarre votre cluster. Le nom du service est `fabric:/System/DnsService`. Il est mentionné dans la section **Système** de Service Fabric explorer. 


## <a name="setting-the-dns-name-for-your-service"></a>Configuration du nom DNS de votre service
Une fois le service DNS en cours d’exécution dans votre cluster, vous pouvez définir un nom DNS pour vos services, soit de manière déclarative pour les services par défaut dans le fichier `ApplicationManifest.xml`, soit à l’aide de commandes PowerShell.

Le nom DNS de votre service peut être résolu dans l’ensemble du cluster. Nous vous recommandons vivement d’utiliser un schéma de nommage `<ServiceDnsName>.<AppInstanceName>`, par exemple `service1.application1`. Cela garantit l’unicité du nom DNS dans tout le cluster. Si une application est déployée à l’aide de Docker Compose, des noms DNS sont affectés automatiquement aux services à l’aide de ce schéma de nommage.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Définition du nom DNS pour un service par défaut du paramètre dans le fichier ApplicationManifest.xml
Ouvrez votre projet dans Visual Studio ou dans votre éditeur favori, puis ouvrez le fichier `ApplicationManifest.xml`. Accédez à la section des services par défaut puis, pour chaque service, ajoutez l’attribut `ServiceDnsName`. L’exemple suivant montre comment définir le nom DNS du service sur `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
Une fois l’application déployée, l’instance de service dans Service Fabric Explorer indique le nom DNS de cette instance, comme illustré dans la figure suivante : 

![points de terminaison de service][1]

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Définition du nom DNS d’un service à l’aide de Powershell
Vous pouvez définir le nom DNS d’un service lors de sa création à l’aide de la commande Powershell `New-ServiceFabricService`. L’exemple suivant permet de créer un service sans état avec le nom DNS `service1.application1`

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="using-dns-in-your-services"></a>Utilisation de DNS dans vos services
Si vous déployez plusieurs services, vous pouvez utiliser un nom DNS pour trouver les points de terminaison d’autres services avec lesquels communiquer. Le service DNS s’applique uniquement aux services sans état, puisque le protocole DNS ne peut pas communiquer avec les services avec état. Pour les services avec état, vous pouvez utiliser le [service de proxy inverse](./service-fabric-reverseproxy.md) intégré pour les appels http afin d’appeler une partition de service particulière. Les ports dynamiques ne sont pas pris en charge par le service DNS. Vous pouvez utiliser le proxy inverse pour résoudre des services qui utilisent des ports dynamiques.

Le code suivant montre comment appeler un autre service, grâce à un simple appel http standard dans lequel vous fournissez le port et un chemin d’accès facultatif dans le cadre de l’URL.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la communication de service au sein du cluster, consultez l’article [Se connecter aux services et communiquer avec eux dans Service Fabric](service-fabric-connect-and-communicate-with-services.md)

[0]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[1]: ./media/service-fabric-dnsservice/servicefabric-explorer-dns.PNG
[2]: ./media/service-fabric-dnsservice/DNSService.PNG
