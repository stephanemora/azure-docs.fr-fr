---
title: Résoudre les problèmes d’Azure Red Hat OpenShift | Microsoft Docs
description: Dépanner et résoudre les problèmes courants avec Azure Red Hat OpenShift
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: 7f2bdf643f12671bec3d0c087d8775844099fe9a
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306246"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Résolution des problèmes pour Azure Red Hat OpenShift

Cet article décrit en détail certains problèmes courants rencontrés lors de la création ou la gestion des clusters de Microsoft Azure Red Hat OpenShift.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Une nouvelle tentative de la création d’un cluster ayant échoué

Si la création d’un Azure Red Hat OpenShift de cluster à l’aide de la `az` commande CLI échoue, une nouvelle tentative de la création continue d’échouer.
Utilisez `az openshift delete` pour supprimer le cluster ayant échoué, puis créez un cluster entièrement nouvel.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Groupe de ressources de cluster Azure Red Hat OpenShift masqué

Actuellement, le `Microsoft.ContainerService/openShiftManagedClusters` ressource qui est automatiquement créé par l’interface CLI (`az openshift create` commande) est masqué dans le portail Azure. Dans le **groupe de ressources** vue, vérification **afficher les types masqués** pour afficher le groupe de ressources.

![Capture d’écran de la case à cocher de type masqué dans le portail](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Création d’un cluster résultats erreur aucun fournisseur de ressources inscrit trouvé

Si la création d’un cluster entraîne une erreur `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`, puis vous faisaient partie de la version préliminaire et que vous devez à présent [instances réservées de machine virtuelle d’achat](https://aka.ms/openshift/buy) à utiliser le produit à la disposition générale. Une réservation permet de réduire vos dépenses en prépayant des services Azure entièrement gérés. Reportez-vous à [ *quelles sont les réservations Azure* ](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) pour en savoir plus sur les réservations et comment ils économiser de l’argent.

## <a name="next-steps"></a>Étapes suivantes

- Essayez le [centre d’aide de Red Hat OpenShift](https://help.openshift.com/) pour le dépannage de OpenShift sur plus.

- Trouvez des réponses aux [Forum aux questions sur Azure Red Hat OpenShift](openshift-faq.md).
