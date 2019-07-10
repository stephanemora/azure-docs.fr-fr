---
title: Didacticiel sur la suppression d’un cluster autonome Service Fabric - Azure Service Fabric | Microsoft Docs
description: Ce didacticiel vous explique comment supprimer votre cluster autonome
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 9127ad1fe148f85779913454adf6578a9a8a1055
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274093"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Didacticiel : Supprimer votre cluster autonome

Les clusters autonomes Service Fabric vous permettent de choisir votre propre environnement et de créer un cluster dans le cadre de l’approche « n’importe quel SE, n’importe quel cloud » suivie par Service Fabric. Dans le cadre de cette série de tutoriels, vous créez un cluster autonome hébergé sur AWS ou Azure et vous installez une application dans celui-ci.

Ce tutoriel est la quatrième partie de la série. Cette partie du didacticiel vous montre comment supprimer les ressources AWS ou Azure que vous avez créées pour héberger votre cluster Service Fabric.

Dans ce quatrième volet, vous apprenez à :

> [!div class="checklist"]
> * Supprimer un cluster Service Fabric
> * Nettoyer vos ressources AWS ou Azure

## <a name="clean-up-service-fabric-cluster"></a>Supprimer un cluster Service Fabric

1. Établissez une connexion RDP à la machine virtuelle que vous avez utilisée pour installer Service Fabric.
2. Ouvrez PowerShell.
3. Basculez vers le dossier extrait à partir du deuxième didacticiel.
4. Exécutez la commande suivante pour supprimer le cluster Service Fabric :

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Entrez `Y` lorsque vous y êtes invité, si l’opération s’est déroulée correctement, vous devez obtenir le résultat suivant, avec vos propres adresses IP dans :

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

1. Connectez-vous à votre compte AWS.
2. Accédez à la console EC2.
3. Sélectionnez les trois nœuds que vous avez créés dans la première partie du didacticiel.
4. Cliquez sur **Actions** > **État de l’instance** > **Terminer**.

## <a name="clean-up-azure-resources"></a>Nettoyage des ressources Azure

1. Connectez-vous au portail Azure.
2. Accédez à la section **Machines virtuelles**.
3. Sélectionnez les cases des trois nœuds que vous avez créés dans la première partie du didacticiel.
4. Cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans la quatrième partie de la série, vous avez appris à supprimer vos ressources créées au cours des étapes précédentes.

> [!div class="checklist"]
> * Nettoyage des ressources

> [!div class="nextstepaction"]
> [Revenir au début](service-fabric-tutorial-standalone-create-infrastructure.md)
