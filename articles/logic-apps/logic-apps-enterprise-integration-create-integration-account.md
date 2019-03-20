---
title: Créer et gérer des comptes d’intégration pour des solutions B2B - Azure Logic Apps | Microsoft Docs
description: Créer, lier, déplacer et supprimer des comptes d’intégration pour des solutions d’intégration d’entreprise et B2B avec Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: 43ecdafac4f0a5cdc9e619537cdbe2a42ff7fe1b
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57191916"
---
# <a name="create-and-manage-integration-accounts-for-b2b-solutions-with-logic-apps"></a>Créer et gérer des comptes d’intégration pour des solutions B2B avec des applications logiques

Avant de pouvoir générer des [solutions d’intégration d’entreprise et B2B](../logic-apps/logic-apps-enterprise-integration-overview.md) avec [Azure Logic Apps](../logic-apps/logic-apps-overview.md), vous devez disposer d’un compte d’intégration dans lequel créer, stocker et gérer des artefacts B2B tels que des partenaires commerciaux, accords, mappages, schémas, certificats et autres. Avant que votre application logique puisse utiliser les artefacts de votre compte d’intégration et les connecteurs Logic Apps B2B tels que la validation XML, vous devez [associer votre compte d’intégration](#link-account) à votre application logique. Pour que vous puissiez les lier, votre compte d’intégration et votre application logique doivent avoir le *même* emplacement (ou région) Azure.

Cet article explique comment effectuer ces tâches :

* Créez votre compte d’intégration.
* Liez votre compte d’intégration à une application logique.
* Déplacez votre compte d’intégration vers un autre groupe de ressources ou abonnement Azure.
* Supprimez votre compte d’intégration.

Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au <a href="https://portal.azure.com" target="_blank">portail Azure</a> avec les informations d’identification de votre compte Azure.

## <a name="create-integration-account"></a>Créer un compte d’intégration

1. Dans le menu principal Azure, sélectionnez **Tous les services**. Dans la zone de recherche, entrez « comptes intégration » comme filtre, puis sélectionnez **Comptes d’intégration**.

   ![Cherchez des comptes d’intégration](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. Sous **Comptes d’intégration**, choisissez **Ajouter**.

   ![Choisissez « Ajouter » pour créer un compte d’intégration](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

3. Fournissez des informations sur votre compte d’intégration : 

   ![Indiquez les détails de votre compte d’intégration](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Propriété | Obligatoire | Exemple de valeur | Description | 
   |----------|----------|---------------|-------------|
   | Name | Oui | test-integration-account | Nom pour votre compte d’intégration. Pour cet exemple, utilisez le nom spécifié. | 
   | Abonnement | Oui | <*Azure-subscription-name*> | Nom de l’abonnement Azure à utiliser. | 
   | Groupe de ressources | Oui | test-integration-account-rg | Nom du [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) utilisé pour organiser les ressources connexes. Pour cet exemple, créez un groupe de ressources portant le nom spécifié. | 
   | Niveau de tarification | Oui | Gratuit | Niveau tarifaire que vous souhaitez utiliser. Pour cet exemple, sélectionnez **Gratuit** mais, pour plus d’informations, voir [Limites et configuration de Logic Apps](../logic-apps/logic-apps-limits-and-config.md) et [Tarification de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/). | 
   | Lieu | Oui | USA Ouest | Région dans laquelle stocker les informations sur votre compte d’intégration. Sélectionnez l’emplacement de votre application logique ou créez une application logique dans l’emplacement de votre compte d’intégration. | 
   | Espace de travail Log Analytics | Non  | Off | Maintenez le paramètre de journalisation des diagnostics **Désactivé**. | 
   ||||| 

4. Lorsque vous êtes prêt, sélectionnez **Épingler au tableau de bord**, puis **Créer**.

   Une fois avoir déployé votre compte d’intégration dans l’emplacement sélectionné, ce qui prend généralement une minute, Azure ouvre votre compte d’intégration.

   ![Azure ouvre votre compte d’intégration](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

À présent, avant que votre application logique puisse utiliser votre compte d’intégration, vous devez lier celui-ci à votre application logique.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Créer un lien vers l’application logique

Pour donner à vos applications logiques l’accès à un compte d’intégration contenant vos artefacts B2B, tels que des partenaires commerciaux, accords, cartes et autres schémas, vous devez lier votre compte d’intégration à votre application logique. 

> [!NOTE]
> Votre compte d’intégration et votre application logique doivent se trouver dans la même région.

1. Dans le portail Azure, recherchez et sélectionnez votre application logique.

2. Dans le menu de votre application logique, sous **Paramètres**, sélectionnez **Paramètres de flux de travail**. Dans la liste **Sélectionner un compte d’intégration**, sélectionnez le compte d’intégration à lier à votre application logique.

   ![Sélectionnez votre compte d’intégration](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-2.png)

3. Pour terminer la liaison, choisissez **Enregistrer**.

   ![Sélectionnez votre compte d’intégration](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-3.png)

   Une fois votre compte d’intégration est correctement associé, Azure affiche un message de confirmation. 

   ![Azure confirme la réussite de la liaison](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-5.png)

Désormais, votre application logique peut utiliser tous les artefacts de votre compte d’intégration en plus des connecteurs B2B, tels que la validation XML et l’encodage ou le décodage de fichiers plats.  

## <a name="unlink-from-logic-app"></a>Dissocier de l’application logique

Pour lier votre application logique à un autre compte d’intégration, ou ne plus utiliser un compte d’intégration avec votre application logique, vous pouvez supprimer la liaison via Azure Resource Explorer.

1. Dans votre navigateur, accédez à <a href="https://resources.azure.com" target="_blank">Azure Resource Explorer (https://resources.azure.com)</a>. Assurez-vous que vous êtes connecté avec les mêmes informations d’identification Azure.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

2. Dans la zone de recherche, entrez le nom de l’application de votre logique, puis recherchez et sélectionnez votre application logique.

   ![Rechercher et sélectionner l’application logique](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

3. Dans la barre de titre de l’explorateur, choisissez **Lecture/écriture**.

   ![Activer le mode « Lecture/écriture »](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-read-write-mode.png)

4. Sous l’onglet **Données**, sélectionnez **Modifier**.

   ![Sous l’onglet « Données », choisir « Modifier »](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-edit.png)

5. Dans l’éditeur, recherchez la propriété `integrationAccount` pour le compte d’intégration et supprimez-la. Son format est le suivant :

   ```json
   "integrationAccount": {
      "name": "<integration-account-name>",
      "id": "<integration-account-resource-ID>",
      "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Par exemple : 

   ![Rechercher la définition de propriété « integrationAccount »](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

6. Sous l’onglet **Données**, choisissez **Put** pour enregistrer vos modifications. 

   ![Choisir « Put » pour enregistrer les modifications](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

7. Dans le portail Azure, sous les **Paramètres de flux de travail** de votre application logique, vérifiez que la propriété **Compte d’intégration** apparaît désormais vide.

   ![Vérifier que le compte d’intégration n’est pas lié](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Déplacer un compte d’intégration

Vous pouvez déplacer votre compte d’intégration vers un autre abonnement ou groupe de ressources Azure.

1. Dans le menu principal Azure, sélectionnez **Tous les services**. Dans la zone de recherche, entrez « comptes intégration » comme filtre, puis sélectionnez **Comptes d’intégration**.

   ![Recherche du compte d’intégration](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. Sous **Comptes d’intégration**, sélectionnez le compte d’intégration à déplacer. Dans le menu de votre compte d’intégration, sous **Paramètres**, choisissez **Propriétés**.

   ![Sous Paramètres, choisir Propriétés](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-properties.png)

3. Modifiez le groupe de ressources ou l’abonnement Azure de votre compte d’intégration.

   ![Sélectionner Modifier le groupe de ressources ou Modifier l’abonnement](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

4. Lorsque vous avez terminé, veillez à mettre à jour tous les scripts avec les nouveaux ID de ressource pour vos artefacts.  

## <a name="delete-integration-account"></a>Supprimer un compte d’intégration

1. Dans le menu principal Azure, sélectionnez **Tous les services**. Dans la zone de recherche, entrez « comptes intégration » comme filtre, puis sélectionnez **Comptes d’intégration**.

   ![Recherche du compte d’intégration](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. Sous **Comptes d’intégration**, sélectionnez le compte d’intégration à supprimer. Dans le menu du compte d’intégration, choisissez **Vue d’ensemble**, puis **Supprimer**. 

   ![Sélectionner un compte d’intégration Dans la page Présentation, choisir Supprimer](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

3. Pour confirmer que vous souhaitez supprimer votre compte d’intégration, choisissez **Oui**.

   ![Pour confirmer la suppression, choisir Oui](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Étapes suivantes

* [Créer des partenaires commerciaux](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Créer des contrats](../logic-apps/logic-apps-enterprise-integration-agreements.md)
