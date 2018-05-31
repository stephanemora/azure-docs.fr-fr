---
title: Afficher des journaux de conteneurs dans Azure Service Fabric | Microsoft Docs
description: Décrit comment afficher des fichiers journaux de conteneurs de services Service Fabric en cours d’exécution à l’aide de Service Fabric Explorer.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/15/2018
ms.author: ryanwi
ms.openlocfilehash: b2b3562f65e7e861b7e4dff7b7c26d58081ff29e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211923"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Afficher les fichiers journaux d’un service de conteneurs Service Fabric
Azure Service Fabric, qui est un orchestrateur de conteneurs, prend en charge les [conteneurs Linux et Windows](service-fabric-containers-overview.md).  Cet article décrit comment afficher les fichiers journaux de conteneurs d’un service de conteneurs en cours d’exécution ou d’un conteneur mort, de manière à pouvoir diagnostiquer et résoudre les problèmes.

## <a name="access-the-logs-of-a-running-container"></a>Accéder aux journaux d’un conteneur en cours d’exécution
Les fichiers journaux de conteneurs sont accessibles via [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  Dans un navigateur web, ouvrez Service Fabric Explorer à partir du point de terminaison de gestion du cluster, en accédant à [http://mycluster.region.cloudapp.azure.com:19080/Explorer](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

Les fichiers journaux de conteneurs sont hébergés sur le nœud de cluster sur lequel est exécutée l’instance du service de conteneurs. Par exemple, récupérez les journaux du conteneur web frontal de l’[exemple d’application Linux Voting](service-fabric-quickstart-containers-linux.md). Dans l’arborescence, développez **Cluster**>**Applications**>**VotingType**>**fabric:/Voting/azurevotefront**.  Ensuite, développez la partition (d1aa737e-f22a-e347-be16-eec90be24bc1, dans cet exemple) et vérifiez que le conteneur est exécuté sur le nœud de cluster *_lnxvm_0*.

Dans l’arborescence, recherchez le package de code sur le nœud *_lnxvm_0*, en développant **Nœuds**>**_lnxvm_0**>**fabric:/Voting**>**azurevotfrontPkg**>**Packages de code**>**code**.  Ensuite, sélectionnez **Journaux de conteneur** afin d’afficher les fichiers journaux de conteneurs.

![Plateforme Service Fabric][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Accéder aux journaux d’un conteneur planté ou mort
À compter de v6.2, vous pouvez également extraire les journaux pour un conteneur mort ou planté à l’aide d’[API REST](/rest/api/servicefabric/sfclient-index) ou de commandes [SFCTL (Service Fabric CLI)](service-fabric-cli.md).

### <a name="rest"></a>REST
Utilisez l’opération [Obtenir les journaux de conteneur déployés sur le nœud](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) afin d’obtenir les journaux pour un conteneur planté. Spécifiez le nom du nœud sur lequel le conteneur s’exécutait, le nom de l’application, le nom du manifeste de service et le nom du package de code.  Spécifiez `&Previous=true`. La réponse contiendra les journaux du conteneur mort de l’instance de package de code.

L’URI de requête a le format suivant :

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Exemple de requête :
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

Corps de réponse 200 :
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
Utilisez la commande [sfctl service get-container-logs](service-fabric-sfctl-service.md) afin d’extraire les journaux pour un conteneur planté.  Spécifiez le nom du nœud sur lequel le conteneur s’exécutait, le nom de l’application, le nom du manifeste de service et le nom du package de code. Spécifiez l’indicateur `-previous`.  La réponse contiendra les journaux du conteneur mort de l’instance de package de code.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –previous
```
Réponse :
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Étapes suivantes
- Exécutez le [didacticiel sur la création d’une application conteneur Linux](service-fabric-tutorial-create-container-images.md).
- En savoir plus sur [Service Fabric et les conteneurs](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
