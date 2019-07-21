---
title: Résoudre les erreurs liées au déplacement de ressources Azure vers un groupe de ressources ou abonnement
description: Utilisez Azure Resource Manager ou une API REST pour déplacer des ressources vers un nouveau groupe de ressources ou abonnement.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: e23d7c571a010e5bfb42e5f15368e0194272ed53
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723341"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Résoudre les problèmes liés au déplacement de ressources vers un nouveau groupe de ressource ou abonnement

Cet article propose des suggestions pour résoudre les problèmes liés au déplacement de ressources.

## <a name="upgrade-a-subscription"></a>Mettre à niveau un abonnement

Vous devez convertir votre abonnement Azure si vous souhaitez le mettre à niveau (par exemple, passer d’une offre gratuite au paiement à l’utilisation).

* Pour mettre à niveau votre essai gratuit, consultez [Passer d’un essai gratuit ou d’un abonnement Azure Microsoft Imagine au paiement à l’utilisation](../billing/billing-upgrade-azure-subscription.md).
* Pour changer un compte de paiement à l’utilisation, consultez [Remplacer votre abonnement Paiement à l’utilisation Azure par une autre offre](../billing/billing-how-to-switch-azure-offer.md).

Si vous ne pouvez pas convertir l’abonnement, [créez une demande de support Azure](../azure-supportability/how-to-create-azure-support-request.md). Sélectionnez **Gestion des abonnements** comme type de problème.

## <a name="service-limitations"></a>Limitations de service

Certains services doivent faire l'objet de considérations supplémentaires en termes de déplacement de ressources. Si vous déplacez les services suivants, assurez-vous d'avoir vérifié les conseils et limitations.

* [Services d’application](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Modèle de déploiement classique](./move-limitations/classic-model-move-limitations.md)
* [Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Machines virtuelles](./move-limitations/virtual-machines-move-limitations.md)
* [Réseaux virtuels](./move-limitations/virtual-network-move-limitations.md)

## <a name="large-requests"></a>Requêtes volumineuses

Quand c’est possible, divisez les grands déplacements en opérations de déplacement distinctes. Resource Manager retourne immédiatement une erreur en cas de tentative de déplacement de plus de 800 ressources en une seule opération. Cependant, un déplacement de moins de 800 ressources peut également échouer en raison d’un dépassement du délai d’expiration.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir les commandes permettant de déplacer des ressources, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](resource-group-move-resources.md).
