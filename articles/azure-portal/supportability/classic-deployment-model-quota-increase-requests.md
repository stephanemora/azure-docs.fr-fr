---
title: Modèle de déploiement Azure Classic | Microsoft Docs
description: Modèle de déploiement classique Azure
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/20/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: d8f4bf04251347c44ea1692cfdda2602e23117f4
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75898785"
---
# <a name="classic-deployment-model"></a>Modèle de déploiement classique

Le modèle de déploiement Classic est le modèle de déploiement Azure de génération antérieure. Il applique une limite de quota de processeur virtuel global pour les machines virtuelles et les groupes de machines virtuelles identiques. Le modèle de déploiement Classic n’est plus recommandé. Il est désormais remplacé par le modèle Resource Manager. 

Pour en savoir plus sur ces deux modèles de déploiement et sur les avantages liés à l’utilisation de Resource Manager, voir la page [Déploiement Azure Resource Manager et déploiement Classic](../../azure-resource-manager/management/deployment-models.md).
 
Quand un abonnement est créé, un quota de processeurs virtuels par défaut lui est attribué. Chaque fois qu’une nouvelle machine virtuelle doit être déployée à l’aide du modèle de déploiement Classic, la somme de l’utilisation des processeurs virtuels nouveaux et existants dans toutes les régions ne doit pas dépasser le quota de processeur virtuel approuvé pour le modèle de déploiement Classic.
 
Pour en savoir plus sur les quotas, voir la page [Abonnement Azure et limites, quotas et contraintes de service](https://aka.ms/quotalimits).

Vous pouvez demander une augmentation de la limite de quota de processeur virtuel pour le modèle de déploiement Classic par le biais du panneau « Aide + Support » ou « Utilisations + Quota » dans le portail.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Demandez une augmentation du quota total de processeurs virtuels par série de machines virtuelles au niveau de l’abonnement à l’aide du panneau **Aide + Support**

Suivez les instructions ci-dessous pour créer une demande de support via le panneau « Aide + Support » d’Azure disponible dans le portail Azure. 

1. À partir de https://portal.azure.com, sélectionnez **Aide + Support**.

   ![Aide + support](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Sélectionnez **Nouvelle demande de support**. 

      ![Nouvelle demande de support](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Dans la liste déroulante Type de problème, choisissez **Limites du service et des abonnements (quotas)** .

   ![Liste déroulante Type de problème](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Sélectionnez l’abonnement nécessitant une augmentation du quota.

   ![Sélectionner l’abonnement newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Sélectionnez **La limite d’abonnement de calcul/machine virtuelle (cœurs/processeurs virtuels) augmente** dans la liste déroulante **Type de quota**. 

   ![Sélectionner un type de quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. Dans **Détails du problème**, fournissez des informations supplémentaires pour faciliter le traitement de votre demande en cliquant sur **Fournir des informations**.

   ![Fournir des informations](./media/resource-manager-core-quotas-request/provide-details.png)

7. Dans le panneau **Détails du quota**, sélectionnez Classique, puis sélectionnez un emplacement.

   ![Détails des quotas : modèle de déploiement](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. Sélectionnez les **familles de références** qui nécessitent une augmentation. 

   ![Famille de références](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. Entrez les nouvelles limites que vous souhaitez appliquer à l’abonnement. Pour supprimer une ligne, désactivez la case de la référence dans la liste déroulante de la famille de références ou cliquez sur l’icône de fermeture « x ». Après avoir entré le quota de votre choix pour chaque famille de références, cliquez sur **Enregistrer et continuer** dans le panneau Détails du quota pour poursuivre la création de demande de support.

   ![Nouvelles limites](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Demandez une augmentation du quota total de processeurs virtuels par série de machines virtuelles au niveau de l’abonnement à l’aide du panneau **Utilisation + quotas**

Suivez les instructions ci-dessous pour créer une demande de support via le panneau « Utilisation + Quotas » d’Azure disponible dans le portail Azure. 

1. Dans https://portal.azure.com, sélectionnez **Abonnements**.

   ![Abonnements](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Sélectionnez l’abonnement nécessitant une augmentation du quota.

   ![Sélectionner un abonnement](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Sélectionnez **Utilisation + quotas**.

   ![Sélectionnez Utilisation + quotas](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. Dans l’angle supérieur droit, cliquez sur **Demander une augmentation**.

   ![Demander une augmentation](./media/resource-manager-core-quotas-request/request-increase.png)

5. Sélectionnez **La limite d’abonnement de calcul/machine virtuelle (cœurs/processeurs virtuels) augmente** comme type de quota. 

   ![Renseignez le formulaire](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. Dans **Détails du problème**, fournissez des informations supplémentaires pour faciliter le traitement de votre demande en cliquant sur **Fournir des informations**.

   ![Fournir des informations](./media/resource-manager-core-quotas-request/provide-details.png)

7. Dans le panneau **Détails du quota**, sélectionnez Classique, puis sélectionnez un emplacement.

   ![Détails des quotas : modèle de déploiement](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. Sélectionnez les **familles de références** qui nécessitent une augmentation. 

   ![Famille de références](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. Entrez les nouvelles limites que vous souhaitez appliquer à l’abonnement. Pour supprimer une ligne, désactivez la case de la référence dans la liste déroulante de la famille de références ou cliquez sur l’icône de fermeture « x ». Après avoir entré le quota de votre choix pour chaque famille de références, cliquez sur **Enregistrer et continuer** dans le panneau Détails du quota pour poursuivre la création de demande de support.

   ![Nouvelles limites](./media/resource-manager-core-quotas-request/new-limits-classic.png)

