---
title: 'Tutoriel : Supprimer une application s’exécutant dans Azure Service Fabric Mesh | Microsoft Docs'
description: Dans ce tutoriel, vous allez découvrir comment enlever une application en cours d’exécution dans Service Fabric Mesh et supprimer des ressources.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 9b72ca2a166e805d960eab47835f449e0f5b991e
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52889512"
---
# <a name="tutorial-remove-an-application-and-resources"></a>Tutoriel : Supprimer une application et des ressources

Ce tutoriel est la quatrième partie de la série. Vous allez apprendre à supprimer une application en cours d’exécution qui a été [déployée sur Service Fabric Mesh](service-fabric-mesh-tutorial-template-deploy-app.md). 

Dans ce quatrième volet, vous apprenez à :

> [!div class="checklist"]
> * Supprimer une application en cours d’exécution dans Service Fabric Mesh
> * Supprimer les ressources d’application

Cette série de tutoriels vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * [Déployer une application sur Service Fabric Mesh à l’aide d’un modèle](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Mettre à l’échelle une application en cours d’exécution dans Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Mettre à niveau une application en cours d’exécution dans Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * Supprimer une application

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce tutoriel :

* Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

* Ouvrez [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md) ou [installez localement Azure CLI et l’interface CLI Service Fabric Mesh](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="delete-the-resource-group-and-all-the-resources"></a>Supprimer le groupe de ressources et toutes les ressources

Lorsque vous n’en avez plus besoin, supprimez toutes les ressources créées. Vous avez déjà [créé un groupe de ressources](service-fabric-mesh-tutorial-template-deploy-app.md#create-a-container-registry) pour héberger l’instance Azure Container Registry et les ressources d’application Service Fabric Mesh.  Vous pouvez supprimer ce groupe de ressources, entraînant ainsi la suppression de toutes les ressources associées.

```azurecli
az group delete --resource-group myResourceGroup
```

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="individually-delete-the-resources"></a>Supprimer les ressources individuellement
Vous pouvez également supprimer l’instance ACR, l’application Service Fabric Mesh et les ressources réseau individuellement.

Pour supprimer l’instance ACR :

```azurecli
az acr delete --resource-group myResourceGroup --name myContainerRegistry
```

Pour supprimer l’application Service Fabric Mesh :

```azurecli
az mesh app delete --resource-group myResourceGroup --name todolistapp
```

Pour supprimer le réseau :
```azurecli
az mesh network delete --resource-group myResourceGroup --name todolistappNetwork
```

## <a name="next-steps"></a>Étapes suivantes

Dans cette partie du tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Supprimer une application en cours d’exécution dans Service Fabric Mesh
> * Supprimer les ressources d’application