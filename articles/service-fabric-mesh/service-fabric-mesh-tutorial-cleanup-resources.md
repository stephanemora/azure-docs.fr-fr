---
title: Tutoriel - Nettoyer des ressources Azure Service Fabric Mesh | Microsoft Docs
description: Découvrez comment supprimer des ressources Azure Service Fabric Mesh afin de ne pas être facturé pour des ressources que vous n’utilisez plus.
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: fb7a444c54a57e7f2c38d941eb99f2fea7eebcef
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993355"
---
# <a name="tutorial-remove-azure-resources"></a>Tutoriel : Supprimer des ressources Azure

Ce tutoriel est la cinquième partie d’une série de tutoriels. Il vous explique comment supprimer l’application et ses ressources pour ne pas qu’elles vous soient facturées.

Ce didacticiel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Nettoyez les ressources utilisées par l’application pour ne pas qu’elles vous soient facturées.

Cette série de tutoriels vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * [Créer une application Service Fabric Mesh dans Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Déboguer une application Service Fabric Mesh qui s’exécute dans votre cluster de développement local](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Déployer une application Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Mettre à niveau une application Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)
> * Nettoyer des ressources Service Fabric Mesh

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce tutoriel :

* Si vous n’avez pas déployé l’application To-Do, suivez les instructions fournies dans [Publier une application web Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="clean-up-resources"></a>Supprimer les ressources

Ceci est la fin du tutoriel. Une fois que vous avez fini d’utiliser les ressources que vous avez créées, supprimez-les afin d’éviter d’être facturé pour des ressources que vous n’utilisez plus. Cela est particulièrement important dans la mesure où Mesh est un service serverless facturé à la seconde. Pour en savoir plus sur la tarification de Mesh, consultez https://aka.ms/sfmeshpricing.

Avec Azure, quand vous créez des ressources associées à un groupe de ressources particulier, la suppression de ce groupe de ressources entraîne la suppression de toutes les ressources associées. Ainsi, vous n’avez pas à les supprimer individuellement.

Dans la mesure où vous avez créé un groupe de ressources pour créer l’application To-Do, vous pouvez supprimer ce groupe de ressources sans risque, et supprimer du même coup toutes les ressources associées.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Vous pouvez également supprimer le groupe de ressources **sfmeshTutorial1RG** [à partir du portail](../azure-resource-manager/resource-group-portal.md#delete-resource-group-or-resources). 

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez terminé la publication d’une application Service Fabric Mesh dans Azure, essayez ce qui suit :

* Pour voir un autre exemple de communication de service à service, explorez l’[exemple d’application de vote](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp)
* Pour en savoir plus sur le modèle de ressource Service Fabric, voir [Modèle de ressource Service Fabric mesh](service-fabric-mesh-service-fabric-resources.md).
* Pour en savoir plus sur Service Fabric mesh, voir la [vue d’ensemble de Service Fabric mesh](service-fabric-mesh-overview.md).
* Lire de la documentation relative à [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)