---
title: Mettre à niveau la configuration d’un cluster Azure Service Fabric autonome | Microsoft Docs
description: Découvrez comment mettre à niveau la configuration qui exécute un cluster Service Fabric autonome.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: f99c1ebb64bf881bcd42f15e13bb81b96ccfa064
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58665600"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>Mettre à niveau la configuration d’un cluster autonome 

Pour les systèmes modernes, la possibilité de mettre à niveau est essentielle à la réussite à long terme de votre produit. Un cluster Azure Service Fabric est une ressource que vous possédez. Cet article explique comment mettre à niveau les paramètres de configuration de votre cluster Service Fabric autonome.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>Personnaliser les paramètres de cluster dans le fichier ClusterConfig.json
Les clusters autonomes sont configurés via le fichier *ClusterConfig.json*. Pour en savoir plus sur les différents paramètres, consultez [Paramètres de configuration pour un cluster Windows autonome](service-fabric-cluster-manifest.md).

Vous pouvez ajouter, mettre à jour ou supprimer des paramètres dans la section `fabricSettings` sous la section des [propriétés du cluster](./service-fabric-cluster-manifest.md#cluster-properties) du fichier *ClusterConfig.json*. 

Par exemple, le JSON suivant ajoute un nouveau paramètre *MaxDiskQuotaInMB* à la section *Diagnostics* sous `fabricSettings` :

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Après avoir modifié les paramètres dans votre fichier ClusterConfig.json, [testez la configuration du cluster](#test-the-cluster-configuration), puis [mettez à niveau la configuration du cluster](#upgrade-the-cluster-configuration) pour appliquer les paramètres à votre cluster. 

## <a name="test-the-cluster-configuration"></a>Tester la configuration du cluster
Avant de lancer la mise à niveau de la configuration, vous pouvez tester votre nouveau modèle JSON de configuration de cluster en exécutant le script PowerShell suivant dans le package autonome :

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Ou utilisez ce script :

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Certaines configurations ne peuvent pas être mises à niveau, notamment les points de terminaison, le nom du cluster, l’IP du nœud, etc. Cette opération teste le nouveau modèle JSON de configuration de cluster en le comparant à l’ancien modèle, et consigne les erreurs dans la fenêtre PowerShell en cas de problème.

## <a name="upgrade-the-cluster-configuration"></a>Mettre à niveau la configuration du cluster
Pour mettre à niveau la configuration du cluster, exécutez [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). La mise à niveau de la configuration est traitée par domaine de mise à niveau.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Mettre à niveau la configuration du certificat de cluster
Un certificat de cluster est utilisé pour l’authentification entre les nœuds de cluster. La substitution de certificat doit être effectuée avec prudence, car toute défaillance bloque la communication entre les nœuds de cluster.

Quatre options sont prises en charge :  

* Mise à niveau du seul certificat : Le chemin d’accès de mise à niveau est le certificat A (principal) -> certificat B (principal) -> certificat C (principal) ->...

* Mise à niveau de certificat double : Le chemin d’accès de mise à niveau est le certificat A (principal) -> certificat A (principal) et B (secondaire) -> certificat B (principal) -> certificat B (principal) et C (secondaire) -> certificat C (principal) ->...

* Mise à niveau du type de certificat : Configuration de certificat basée sur CommonName configuration <> – basée sur l’empreinte de certificat. Par exemple, certificat Thumbprint A (Principal) et Thumbprint B (Secondaire) -> certificat CommonName C.

* Mise à niveau de l’émetteur empreinte numérique du certificat : Le chemin d’accès de mise à niveau est Certificate CN = A, IssuerThumbprint = IT1 (principal) -> Certificate CN = A, IssuerThumbprint = IT1, IT2 (Primary) -> Certificate CN = A, IssuerThumbprint = IT2 (principal).


## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment personnaliser certains [paramètres de clusters Service Fabric](service-fabric-cluster-fabric-settings.md).
* Découvrez comment [diminuer et augmenter la taille de votre cluster](service-fabric-cluster-scale-up-down.md).
* Découvrez les [mises à niveau d’applications](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
