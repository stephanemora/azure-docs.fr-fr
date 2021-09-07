---
title: Résoudre les problèmes de commande Azure Stack Edge via le Portail Azure
description: Décrit comment résoudre des problèmes de commandes liés à Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/21/2021
ms.author: alkohli
ms.openlocfilehash: 46a7d0ed41b8f10874c19c8d70ddc5d8c5d7f197
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122608284"
---
# <a name="troubleshoot-your-azure-stack-edge-ordering-issues"></a>Résoudre vos problèmes de commandes liés à Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article décrit comment résoudre des problèmes de commandes liés à Azure Stack Edge.

## <a name="unsupported-subscription-or-region"></a>Abonnement ou région non pris en charge

**Description de l’erreur :** Dans Portail Azure, si vous recevez l’erreur :

*Abonnement ou région non pris en charge. Choisissez un abonnement ou une région différent.*

![Abonnement ou région non pris en charge](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-01.png)

**Solution suggérée :**  Vérifiez que vous avez utilisé un abonnement pris en charge tel que [Contrat Entreprise (EA) Microsoft](https://azure.microsoft.com/overview/sales-number/), [Fournisseur de solutions cloud (CSP)](/partner-center/azure-plan-lp) ou [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Les abonnements de type Paiement à l’utilisation ne sont pas pris en charge. Pour plus d’informations, consultez les [Composants requis pour les ressources Azure Stack Edge](azure-stack-edge-deploy-prep.md#prerequisites).

Il est possible que Microsoft autorise la mise à niveau d’un type d’abonnement au cas par cas. Contactez [Support Microsoft](https://azure.microsoft.com/support/options/) afin qu’ils puissent comprendre vos besoins et ajuster ces limites en conséquence.

## <a name="selected-subscription-type-not-supported"></a>Le type d’abonnement sélectionné n’est pas pris en charge

**Erreur :** Vous avez un abonnement au service Broker External Activator, au fournisseur de solutions Cloud ou sponsorisé et vous recevez l’erreur suivante :

* Le type d’abonnement sélectionné n’est pas pris en charge. Veillez à utiliser un abonnement pris en charge. [Plus d’informations](azure-stack-edge-deploy-prep.md#prerequisites) Si vous utilisez un type d’abonnement pris en charge, assurez-vous que :
 
- Le fournisseur `Microsoft.DataBoxEdge` est inscrit, au moment du passage de commandes sur le portail Classic.
- Le fournisseur `Microsoft.EdgeOrder` est inscrit, au moment du passage de commandes sur Azure Edge Hardware Center (préversion).
 
Pour plus d’informations sur l’inscription, consultez [Inscrire un fournisseur de ressources](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)*.

**Solution suggérée :** Pour inscrire votre fournisseur de ressources Azure Stack Edge, procédez comme suit :

1. Dans le Portail Azure, accédez à **Accueil** > **Abonnements**.

2. Sélectionnez l’abonnement que vous allez utiliser pour commander votre appareil.

3. Sélectionnez **Fournisseurs de ressources**, puis recherchez **Microsoft.DataBoxEdge**.

    ![S’inscrire auprès du fournisseur de ressources](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-02.png)

Si vous ne disposez pas d’un accès propriétaire ou contributeur pour inscrire le fournisseur de ressources, l’erreur suivante s’affiche : *L’abonnement &lt;nom d’abonnement&gt; ne dispose pas des autorisations nécessaires pour inscrire le ou les fournisseurs de ressources : Microsoft.DataBoxEdge.*

Pour plus d’informations, consultez [Inscrire les fournisseurs de ressources](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).

## <a name="resource-provider-not-registered-for-subscription"></a>Le fournisseur de ressources n’est pas inscrit pour cet abonnement

**Erreur :** Dans le portail Azure, vous sélectionnez un abonnement à utiliser pour Azure Stack Edge ou Data Box Gateway et vous recevez l’erreur suivante :

*Fournisseur(s) de ressources : Microsoft.DataBoxEdge n’est pas inscrit pour l’abonnement &lt;nom d’abonnement&gt; et vous ne disposez pas des autorisations pour inscrire un fournisseur de ressources pour l’abonnement &lt;nom d’abonnement&gt;* .

*Fournisseur(s) de ressources : Microsoft.EdgeOrder n’est pas inscrit pour l’abonnement &lt;nom d’abonnement&gt; et vous ne disposez pas des autorisations pour inscrire un fournisseur de ressources pour l’abonnement &lt;nom d’abonnement&gt;* .

**Solution suggérée :** Élevez votre accès à l’abonnement ou trouvez une personne disposant d’un accès propriétaire ou contributeur pour inscrire le fournisseur de ressources.

## <a name="resource-disallowed-by-policy"></a>La ressource a été rejetée par la stratégie

**Erreur :** Dans le Portail Azure, vous tentez d’inscrire un fournisseur de ressources et d’obtenir l’erreur suivante :

La *ressource &lt;nom de ressource&gt; a été rejetée par la stratégie. (Code : RequestDisallowedByPolicy). Initiative : Refusez les types de ressources généralement indésirables. Stratégie : Types de ressources non autorisés.*

**Solution suggérée :** Cette erreur est provoquée par une attribution Azure Policy existante qui bloque la création des ressources. Les définitions et attributions Azure Policy sont définies par l’administrateur système d’une organisation afin de garantir la conformité lors de l’utilisation ou de la création de ressources Azure. Si une attribution Azure Policy bloque la création de ressources Azure Stack Edge, contactez votre administrateur système pour modifier votre définition Azure Policy.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [Résoudre vos problèmes liés à Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md).
