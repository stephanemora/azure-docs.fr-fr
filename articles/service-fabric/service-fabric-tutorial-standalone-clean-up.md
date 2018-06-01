---
title: Didacticiel sur la suppression d’un cluster autonome Service Fabric - Azure Service Fabric | Microsoft Docs
description: Ce didacticiel vous explique comment supprimer votre cluster autonome
services: service-fabric
documentationcenter: .net
author: david-stanford
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dastanfo
ms.custom: mvc
ms.openlocfilehash: d92712200920f135e362e15e1d9536dc2342ebaa
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211087"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Didacticiel : Supprimer votre cluster autonome

Les clusters autonomes Service Fabric vous permettent de choisir votre propre environnement et de créer un cluster dans le cadre de l’approche « n’importe quel SE, n’importe quel cloud » suivie par Service Fabric. Dans cette série de didacticiels, vous créez un cluster autonome hébergé sur AWS et vous installez une application dans celui-ci.

Ce didacticiel est la quatrième partie de la série. Cette partie du didacticiel vous montre comment supprimer les ressources AWS que vous avez créées pour héberger votre cluster Service Fabric.

Dans ce quatrième volet, vous apprenez à :

> [!div class="checklist"]
> * Supprimer un cluster Service Fabric
> * Supprimer vos ressources AWS

## <a name="clean-up-service-fabric-cluster"></a>Supprimer un cluster Service Fabric

* Établissez une connexion RDP à l’instance EC2 que vous avez utilisée pour installer Service Fabric.
* Ouvrez PowerShell
* Basculez vers le dossier extrait à partir du deuxième didacticiel.
* Exécutez la commande suivante pour supprimer le cluster Service Fabric :

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

* `Y` lorsque vous y êtes invité, si l’opération s’est déroulée correctement, vous devez obtenir le résultat suivant, avec vos propres adresses IP dans :

```powershell
Best Practices Analyzer completed successfully.
Removing configuration from machine 172.31.21.141
Removing configuration from machine 172.31.27.1
Removing configuration from machine 172.31.20.163
Configuration removed from machine 172.31.21.141
Configuration removed from machine 172.31.27.1
Configuration removed from machine 172.31.20.163
The cluster is successfully removed.
```

## <a name="clean-up-aws-resources"></a>Supprimer les ressources AWS

* Connectez-vous à votre compte AWS.
* Accédez à la console EC2.
* Sélectionnez les trois nœuds que vous avez créés dans la première partie du didacticiel.
* Cliquez sur **Actions** > **État de l’instance** > **Terminer**

## <a name="next-steps"></a>Étapes suivantes

Dans la quatrième partie de la série, vous avez appris à supprimer vos ressources créées au cours des étapes précédentes.

> [!div class="checklist"]
> * Nettoyage des ressources

> [!div class="nextstepaction"]
> [Revenir au début](service-fabric-tutorial-standalone-create-infrastructure.md)