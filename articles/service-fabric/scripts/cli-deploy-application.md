---
title: Exemple de déploiement de script de l’interface CLI Azure Service Fabric (sfctl)
description: Déployer une application sur un cluster Azure Service Fabric via l’interface CLI Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
origin.date: 04/16/2018
ms.date: 04/29/2019
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: f3ab32101a46c1044954f2efe88ac05ab81af24a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621996"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Déployer une application sur un cluster Service Fabric

Cet exemple de script copie un package d’application dans le magasin d’images d’un cluster, inscrit le type d’application dans le cluster et crée une instance d’application à partir du type d’application. Tous les services par défaut sont également créés à ce stade.

Si nécessaire, installez l’[interface CLI Service Fabric](../service-fabric-cli.md).

## <a name="sample-script"></a>Exemple de script

```sh
#!/bin/bash

# Select cluster
sfctl cluster select \
    --endpoint http://svcfab1.chinanorth.cloudapp.chinacloudapi.cn:19080

# Upload the application files to the image store
# (note the last folder name, Debug in this example)
sfctl application upload \
    --path  C:\Code\svcfab-vs\svcfab-vs\pkg\Debug \
    --show-progress

# Register the application (manifest files) from the image store
# (Note the last folder from the previous command is used: Debug)
sfctl application provision \
    --application-type-build-path Debug \
    --timeout 500

# Create an instance of the registered application and 
# auto deploy any defined services
sfctl application create \
    --app-name fabric:/MyApp \
    --app-type MyAppType \
    --app-version 1.0.0

```

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Lorsque vous avez terminé, vous pouvez utiliser le script [remove](cli-remove-application.md) pour supprimer l’application. Ce script supprime l’instance d’application, annule l’inscription du type d’application et supprime le package d’application du magasin d’images.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez la [documentation relative à Service Fabric CLI](../service-fabric-cli.md).

Vous trouverez des exemples Service Fabric CLI supplémentaires pour Azure Service Fabric dans la rubrique [Exemples Service Fabric CLI](../samples-cli.md).

<!--Update_Description: update meta properties, update link -->