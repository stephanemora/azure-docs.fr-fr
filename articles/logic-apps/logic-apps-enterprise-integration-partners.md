---
title: Ajouter des partenaires commerciaux pour les intégrations B2B - Azure Logic Apps | Microsoft Docs
description: Créer des partenaires commerciaux pour votre compte d’intégration dans Azure Logic Apps avec Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.date: 07/08/2016
ms.openlocfilehash: 137ed89c276338b534cad8fdf81ec31b5e5610b5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60845982"
---
# <a name="add-trading-partners-for-integration-accounts-in-azure-logic-apps-with-enterprise-integration-pack"></a>Ajouter des partenaires commerciaux pour les comptes d’intégration dans Azure Logic Apps avec Enterprise Integration Pack

Les partenaires sont des entités qui participent aux transactions d’entreprise à entreprise (B2B) et qui échangent des messages entre eux. Avant de pouvoir créer des partenaires qui vous représentent et qui représentent une autre organisation dans le cadre de ces transactions, vous devez partager des informations qui identifient et valident les messages envoyés par chacun. Après avoir discuté de ces détails et quand vous serez prêt à initier cette relation commerciale, vous pouvez créer des partenaires dans votre compte d’intégration pour vous représenter, ainsi que l’organisation.

## <a name="what-roles-do-partners-play-in-your-integration-account"></a>Quels rôles jouent les partenaires dans votre compte d’intégration ?

Pour définir les détails relatifs aux messages échangés entre les partenaires, vous pouvez créer des contrats entre ces partenaires. Toutefois, avant de pouvoir créer un contrat, vous devez avoir ajouté au moins deux partenaires à votre compte d’intégration. Votre organisation doit faire partie du contrat, en tant que **partenaire hôte**. L’autre partenaire, ou **partenaire invité**, représente l’organisation qui échange des messages avec votre organisation. Le partenaire invité peut être une autre entreprise ou même un service au sein de votre organisation.

Après avoir ajouté ces partenaires, vous pouvez créer un contrat.

Les paramètres de réception et d’envoi sont basés du point de vue du partenaire hébergé. Par exemple, les paramètres de réception dans un contrat déterminent la façon dont le partenaire hébergé reçoit les messages envoyés à partir d’un partenaire invité. De même, les paramètres d’envoi du contrat indiquent la façon dont le partenaire hébergé envoie des messages au partenaire invité.

## <a name="create-partner"></a>Créer un partenaire

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Dans le menu principal Azure, sélectionnez **Tous les services**. Entrez « intégration » dans la zone de recherche, puis sélectionnez **Comptes d’intégration**.

   ![Chercher le compte d’intégration](./media/logic-apps-enterprise-integration-partners/account-1.png)

3. Sous **Comptes d’intégration**, sélectionnez le compte d’intégration dans lequel vous souhaitez ajouter vos partenaires.

   ![Sélectionner le compte d’intégration](./media/logic-apps-enterprise-integration-partners/account-2.png)

4. Choisissez la mosaïque **Partenaires**.

   ![Choisir les « Partenaires »](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. Sous **Partenaires**, choisissez **Ajouter**.

   ![Choisir « Ajouter »](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. Entrez un nom pour votre partenaire, puis sélectionnez un **qualificateur**. Entrez une **valeur** pour identifier les documents que vos applications reçoivent. Une fois que vous avez terminé, sélectionnez **OK**.

   ![Ajouter des détails sur le partenaire](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. Choisissez à nouveau la mosaïque **Partenaires**.

   ![Choisir la mosaïque « Partenaires »](./media/logic-apps-enterprise-integration-partners/partner-5.png)

   Votre nouveau partenaire s’affiche désormais. 

   ![Voir le nouveau partenaire](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="edit-partner"></a>Modifier un partenaire

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez votre compte d’intégration. Choisissez la mosaïque **Partenaires**.

   ![Choisir la mosaïque « Partenaires »](./media/logic-apps-enterprise-integration-partners/edit.png)

2. Sous **Partenaires**, sélectionnez le partenaire à modifier.

   ![Sélectionner le partenaire à supprimer](./media/logic-apps-enterprise-integration-partners/edit-1.png)

3. Sous **Mettre à jour le partenaire**, apportez les modifications nécessaires.
Une fois que vous avez terminé, sélectionnez **Enregistrer**. 

   ![Effectuer et enregistrer vos modifications](./media/logic-apps-enterprise-integration-partners/edit-2.png)

   Pour annuler vos modifications, sélectionnez **Ignorer**.

## <a name="delete-partner"></a>Supprimer un partenaire

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez votre compte d’intégration. Choisissez la mosaïque **Partenaires**.

   ![Choisir la mosaïque « Partenaires »](./media/logic-apps-enterprise-integration-partners/delete.png)

2. Sous **Partenaires**, sélectionnez le partenaire à supprimer.
Choisissez **Supprimer**.

   ![Supprimer un partenaire](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les contrats](../logic-apps/logic-apps-enterprise-integration-agreements.md "Découvrez les contrats d’intégration d’entreprise")  

