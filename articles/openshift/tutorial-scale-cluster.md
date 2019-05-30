---
title: 'Tutoriel : mise à l’échelle un cluster Azure Red Hat OpenShift | Microsoft Docs'
description: Découvrez comment mettre à l’échelle un cluster Microsoft Azure Red Hat OpenShift à l’aide d’Azure CLI
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: b25e17e7064006a1421142dfcd32997cb4426e8e
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305983"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>Didacticiel : Mettre à l’échelle un cluster Azure Red Hat OpenShift

Ce tutoriel est le deuxième d’une série. Vous allez apprendre à créer un cluster Microsoft Azure Red Hat OpenShift à l’aide d’Azure CLI, à le mettre à l'échelle, puis à le supprimer pour nettoyer les ressources.

Dans ce deuxième volet, vous apprenez à :

> [!div class="checklist"]
> * Mettre à l’échelle un cluster Red Hat OpenShift

Cette série de tutoriels vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * [Créer un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md)
> * Mettre à l’échelle un cluster Azure Red Hat OpenShift
> * [Supprimer un cluster Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce tutoriel :

* Créer un cluster en suivant le tutoriel [Créer un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md).

## <a name="step-1-sign-in-to-azure"></a>Étape 1 : Connexion à Azure

Si vous utilisez Azure CLI localement, exécutez `az login` pour vous connecter à Azure.

```bash
az login
```

Si vous avez accès à plusieurs abonnements, exécutez `az account set -s {subscription ID}` en remplaçant `{subscription ID}` par l’abonnement que vous souhaitez utiliser.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>Étape 2 : Mise à l’échelle du cluster avec des nœuds supplémentaires

À partir d’un terminal Bash, définissez la variable CLUSTER_NAME par le nom de votre cluster :

```bash
CLUSTER_NAME=yourclustername
```

Maintenant nous allons mettre à l’échelle le cluster à cinq nœuds à l’aide d’Azure CLI :

```bash
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

Après quelques minutes, `az openshift scale` a été exécutée et retourne un document JSON contenant les détails du cluster mis à l’échelle.

## <a name="next-steps"></a>Étapes suivantes

Dans cette partie du tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Mettre à l’échelle un cluster Azure Red Hat OpenShift

Passez au tutoriel suivant :
> [!div class="nextstepaction"]
> [Supprimer un cluster Azure Red Hat OpenShift](tutorial-delete-cluster.md)