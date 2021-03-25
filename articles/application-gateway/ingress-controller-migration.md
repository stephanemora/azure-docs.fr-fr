---
title: Migrer du contrôleur d’entrée Helm Azure Application Gateway vers le module complémentaire AGIC
description: Cet article fournit des instructions sur la migration d’AGIC déployé via Helm vers AGIC déployé en tant que module complémentaire AKS
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: e83834fd5f8ca95826118c952f7884a494c7abbb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102050832"
---
# <a name="migrate-from-agic-helm-to-agic-add-on"></a>Migrer d’AGIC Helm vers le module complémentaire AGIC 

Si vous avez déjà déployé AGIC via Helm, mais que vous souhaitez migrer vers AGIC déployé en tant que module complémentaire AKS, les étapes suivantes vous guideront tout au long du processus de migration. 

## <a name="prerequisites"></a>Prérequis 
Avant de commencer le processus de migration, voici quelques points à vérifier. 
  - Utilisez-vous des fonctionnalités avec AGIC Helm qui [ne sont actuellement pas prises en charge avec le module complémentaire AGIC](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) ?
  - Utilisez-vous plusieurs déploiements AGIC Helm par cluster AKS ? 
  - Utilisez-vous plusieurs déploiements AGIC Helm pour cibler une Application Gateway ? 

Si vous avez répondu oui à l’une des questions ci-dessus, le module complémentaire AGIC ne prend pas encore en charge votre cas d’utilisation. Il est donc préférable de continuer à utiliser AGIC Helm. Sinon, poursuivez le processus de migration ci-dessous en dehors des heures d’ouverture. 

## <a name="find-the-application-gateway-resource-id-that-agic-helm-is-currently-targeting"></a>Rechercher l’ID de ressource Application Gateway actuellement ciblé par AGIC Helm 
Accédez à l’instance Application Gateway ciblée par votre déploiement AGIC Helm. Copiez et enregistrez l’ID de ressource de cette Application Gateway. Vous aurez besoin de l’ID de ressource dans une étape ultérieure. L’ID de ressource peut être retrouvé dans le portail, sous l’onglet Propriétés de votre Application Gateway ou via Azure CLI. L’exemple suivant enregistre l’ID de ressource Application Gateway dans *appgwId* pour une passerelle nommée *myApplicationGateway* dans le groupe de ressources *myResourceGroup*.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
```

## <a name="delete-agic-helm-from-your-aks-cluster"></a>Supprimer AGIC Helm de votre cluster AKS
À l’aide d’Azure CLI, supprimez votre déploiement AGIC Helm de votre cluster. Vous devez d’abord supprimer le déploiement AGIC Helm avant de pouvoir activer le module complémentaire AGIC AKS. Notez que les modifications apportées dans votre cluster AKS entre le moment où vous supprimez votre déploiement AGIC Helm et le moment où vous activez le module complémentaire AGIC ne seront pas reflétées sur votre Application Gateway. Ce processus de migration doit donc être effectué en dehors des heures d’ouverture pour réduire l’impact. Application Gateway continuera à utiliser la dernière configuration appliquée par AGIC afin que les règles d’acheminement existantes ne soient pas affectées. 

## <a name="enable-agic-add-on-using-your-existing-application-gateway"></a>Activer le module complémentaire AGIC à l’aide de votre Application Gateway existante 
Vous pouvez maintenant activer le module complémentaire AGIC dans votre cluster AKS pour cibler votre instance Application Gateway existante via Azure CLI ou le portail. Exécutez la commande Azure CLI suivante pour activer le module complémentaire AGIC dans votre cluster AKS. L’exemple active le module complémentaire dans un cluster nommé *myCluster*, dans un groupe de ressources nommé *myResourceGroup*, à l’aide de l’ID de ressource Application Gateway *appgwId* que nous avons enregistré ci-dessus à l’étape précédente. 


```azurecli-interactive
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

Vous pouvez également accéder à votre cluster AKS dans le portail à l’aide de ce [lien](https://portal.azure.com/?feature.aksagic=true) et activer le module complémentaire AGIC dans l’onglet Réseau de votre cluster. Sélectionnez votre instance Application Gateway existante dans le menu déroulant lorsque vous choisissez l’instance Application Gateway que le module complémentaire doit cibler. 

![Portail du contrôleur d’entrée Application Gateway](./media/tutorial-ingress-controller-add-on-existing/portal-ingress-controller-add-on.png)

## <a name="next-steps"></a>Étapes suivantes
- [**Résolution des problèmes du contrôleur d’entrée Application Gateway**](ingress-controller-troubleshoot.md) : Guide de résolution des problèmes pour AGIC 
- [**Annotations du contrôleur d’entrée Application Gateway**](ingress-controller-annotations.md) : Liste des annotations sur AGIC 