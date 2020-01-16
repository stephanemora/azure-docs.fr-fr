---
title: Augmentation de la limite réseau | Microsoft Docs
description: Augmentation de la limite réseau
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 04a22d987e27f054648637890fbd61c56533c2ee
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75898709"
---
# <a name="networking-limit-increase"></a>Augmentation de la limite réseau

Pour afficher votre utilisation et votre quota de réseau actuels, vous pouvez consulter le panneau **Utilisation + Quotas** sur le portail Azure. Vous pouvez également utiliser l’[interface de ligne de commande](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0) ou l’[API d’utilisation du réseau](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage) pour afficher l’utilisation et les limites de votre réseau.

Vous pouvez demander une augmentation via le panneau **Aide + Support** ou **Utilisation + Quotas** sur le portail.

> [!Note]
> Pour modifier la taille par défaut du préfixe des adresses IP publiques, sélectionnez « Longueur minimale du préfixe d’interconnexion des adresses IP publiques » dans la liste déroulante.

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Demander une augmentation du quota de réseau au niveau de l’abonnement à l’aide du panneau **Aide + Support**

Suivez les instructions ci-dessous pour créer une demande de support via le panneau « Aide + Support » d’Azure disponible dans le portail Azure. 

1. À partir de https://portal.azure.com, sélectionnez **Aide + Support**.

    ![Aide + support](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Sélectionnez **Nouvelle demande de support**. 

    ![Nouvelle demande de support](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Dans la liste déroulante Type de problème, choisissez **Limites du service et des abonnements (quotas)** .

    ![Liste déroulante Type de problème](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Sélectionnez l’abonnement nécessitant une augmentation du quota.

    ![Sélectionner l’abonnement newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Sélectionnez **Mise en réseau** dans la liste déroulante **Type de quota**. 

    ![Sélectionner un type de quota](./media/networking-quota-request/select-quota-type-network.png)

6. Dans **Détails du problème**, fournissez des informations supplémentaires pour faciliter le traitement de votre demande en cliquant sur **Fournir des informations**.

    ![Fournir des informations](./media/resource-manager-core-quotas-request/provide-details.png)

7. Dans le volet **détails du quota**, sélectionnez le modèle de déploiement, un emplacement et les ressources dont vous souhaitez demander une augmentation.

    ![Détails des quotas : modèle de déploiement](./media/networking-quota-request/quota-details-network.png)

8.  Entrez les nouvelles limites que vous souhaitez appliquer à l’abonnement. Pour supprimer une ligne, désactivez la ressource dans la liste déroulante Ressource ou cliquez sur l’icône de fermeture « x ». Après avoir entré le quota de votre choix pour chaque ressource, cliquez sur **Enregistrer et continuer** dans le panneau Détails du quota pour poursuivre la création de demande de support.

    ![Nouvelles limites](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Demander une augmentation du quota de réseau au niveau de l’abonnement à l’aide du panneau **Utilisation + Quotas**

Suivez les instructions ci-dessous pour créer une demande de support via le panneau « Utilisation + Quotas » d’Azure disponible dans le portail Azure. 

1. Dans https://portal.azure.com, sélectionnez **Abonnements**.

    ![Abonnements](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Sélectionnez l’abonnement nécessitant une augmentation du quota.

    ![Sélectionner un abonnement](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Sélectionnez **Utilisation + quotas**.

    ![Sélectionnez Utilisation + quotas](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. Dans l’angle supérieur droit, cliquez sur **Demander une augmentation**.

    ![Demander une augmentation](./media/resource-manager-core-quotas-request/request-increase.png)

5. Suivez les étapes en commençant par l’étape 3 de la section *Demander une augmentation de quota au niveau de l’abonnement* en utilisant la section **Aide + Support** du panneau

## <a name="about-networking-limits"></a>À propos des limites de mise en réseau

Pour en savoir plus sur les limites de mise en réseau, voir la section [Mise en réseau](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) de la page Limites ou notre FAQ sur limites de réseau
