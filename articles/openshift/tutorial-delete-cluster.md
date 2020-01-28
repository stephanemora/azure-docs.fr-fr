---
title: Tutoriel - Supprimer un cluster Azure Red Hat OpenShift
description: Dans ce tutoriel, découvrez comment supprimer un cluster Azure Red Hat OpenShift à l’aide d’Azure CLI
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c335236a2b0b05f03bef1ebef37f1129a5d0352b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278770"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Tutoriel : Supprimer un cluster Azure Red Hat OpenShift

Ceci est la fin du tutoriel. Lorsque vous avez terminé de tester l’exemple de cluster, voici comment le supprimer, ainsi que ses ressources associées afin de ne pas payer ce que vous n’utilisez pas.

Dans ce troisième volet, vous apprenez à :

> [!div class="checklist"]
> * Supprimer un cluster Azure Red Hat OpenShift

Cette série de tutoriels vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * [Créer un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md)
> * [Mettre à l'échelle un cluster Azure Red Hat OpenShift](tutorial-scale-cluster.md)
> * Supprimer un cluster Azure Red Hat OpenShift

## <a name="prerequisites"></a>Conditions préalables requises

Avant de commencer ce tutoriel :

* Créer un cluster en suivant le tutoriel [Créer un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md).

## <a name="step-1-sign-in-to-azure"></a>Étape 1 : Connexion à Azure

Si vous utilisez Azure CLI localement, exécutez `az login` pour vous connecter à Azure.

```bash
az login
```

Si vous avez accès à plusieurs abonnements, exécutez `az account set -s {subscription ID}` en remplaçant `{subscription ID}` par l’abonnement que vous souhaitez utiliser.

## <a name="step-2-delete-the-cluster"></a>Étape 2 : Supprimer le cluster

Ouvrez un terminal Bash et définissez la variable CLUSTER_NAME par le nom de votre cluster :

```bash
CLUSTER_NAME=yourclustername
```

Maintenant, supprimez votre cluster :

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

Il vous est demandé si vous souhaitez supprimer le cluster. Après que vous aurez confirmé avec `y`, la suppression du cluster prendra plusieurs minutes. Lorsque la commande est terminée, tout le groupe de ressources et toutes les ressources qu’il contient, y compris le cluster, sont supprimés.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Suppression d’un cluster à l’aide du portail Azure

Vous pouvez également supprimer le groupe de ressources associé de votre cluster par le biais du portail Azure en ligne. Le groupe de ressources porte le même nom que votre cluster.

Actuellement, la ressource `Microsoft.ContainerService/openShiftManagedClusters` qui est créée lorsque vous créez le cluster est masquée dans le portail Azure. Dans la vue `Resource group`, cochez `Show hidden types` pour afficher le groupe de ressources.

![Capture d’écran de la case à cocher de type masqué](./media/aro-portal-hidden-type.png)

La suppression du groupe de ressources entraîne la suppression de toutes vos ressources associées créées en même temps que le cluster Azure Red Hat OpenShift.

## <a name="next-steps"></a>Étapes suivantes

Dans cette partie du tutoriel, vous avez appris à :
> [!div class="checklist"]
> * Supprimer un cluster Azure Red Hat OpenShift

En savoir plus sur l’utilisation d’OpenShift avec la [documentation Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html) officielle
