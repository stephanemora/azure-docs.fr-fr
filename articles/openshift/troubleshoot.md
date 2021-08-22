---
title: Résoudre les problèmes liés à Azure Red Hat OpenShift
description: Résoudre les problèmes courants liés à Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: 1b68a84b599f8dab071b54f566721ecd7118294e
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090598"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Résolution des problèmes liés à Azure Red Hat OpenShift

Cet article décrit en détail certains problèmes courants rencontrés lors de la création ou de la gestion de clusters Microsoft Azure Red Hat OpenShift.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Nouvelle tentative de création d’un cluster défaillant

Si la création d’un cluster Azure Red Hat OpenShift à l’aide de la commande CLI `az` échoue, toute nouvelle tentative de sa création continue d’échouer.
Utilisez `az openshift delete` pour supprimer le cluster défaillant, puis créez un cluster entièrement nouveau.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Groupe de ressources de cluster Azure Red Hat OpenShift masquées

Actuellement, la ressource `Microsoft.ContainerService/openShiftManagedClusters` qui est automatiquement créée par l’interface Azure CLI (commande `az openshift create`) est masquée dans le portail Azure. Dans l’affichage **Groupe de ressources**, cochez **Afficher les types masqués** pour voir le groupe de ressources.

![Capture d’écran de la case à cocher des types masqués dans le portail](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>La création d’un cluster entraîne une erreur indiquant qu’aucun fournisseur de ressources inscrit n’a été trouvé

Si la création d’un cluster entraîne une erreur indiquant que `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`, cela est dû au fait que vous avez utilisé la préversion et vous devez à présent [acheter des instances réservées de machine virtuelle Azure](https://aka.ms/openshift/buy) pour utiliser le produit mis à la disposition générale. Une réservation réduit vos dépenses si vous effectuez un prépaiement pour les services Azure complètement managés. Pour en savoir plus sur les réservations et la façon de réaliser des économies, reportez-vous à la rubrique [*Qu’est-ce qu’une réservation Azure ?*](../cost-management-billing/reservations/save-compute-costs-reservations.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la résolution des problèmes liés à OpenShift, consultez le [Centre d’aide de Red Hat OpenShift](https://help.openshift.com/).

- Obtenez des réponses aux [questions fréquentes sur Azure Red Hat OpenShift](openshift-faq.yml).