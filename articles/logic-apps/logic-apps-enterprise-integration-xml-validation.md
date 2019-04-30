---
title: Valider des documents XML pour l’intégration d’entreprise B2B - Azure Logic Apps | Microsoft Docs
description: Valider des documents XML avec des schémas pour des solutions B2B dans Azure Logic Apps avec Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.date: 07/08/2016
ms.openlocfilehash: 8db0dbadd944007ff953f9ea69695bf988ffebb7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996051"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Valider des documents XML dans le cadre d’une intégration d’entreprise B2B dans Azure Logic Apps avec Enterprise Integration Pack

Souvent, dans les scénarios B2B, les partenaires dans un contrat doivent s’assurer que les messages qu’ils échangent sont valides avant le début du traitement des données. Vous pouvez utiliser le connecteur XML Validation dans Enterprise Integration Pack pour valider les documents par rapport à un schéma prédéfini.

## <a name="validate-a-document-with-the-xml-validation-connector"></a>Valider un document avec le connecteur XML Validation

1. Créez une application logique et [liez-la à votre compte d’intégration](../logic-apps/logic-apps-enterprise-integration-accounts.md "Découvrez comment lier un compte d’intégration à une application logique") qui contient le schéma que celui que vous voulez utiliser pour valider les données XML.

2. Ajoutez un déclencheur **Requête - Lors de la réception d’une requête HTTP** à votre application logique.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1.png)

3. Pour ajouter l’action **Validation XML** choisissez **Ajouter une action**.

4. Pour filtrer toutes les actions et obtenir celle que vous souhaitez utiliser, entrez *xml* dans la zone de recherche. Choisissez **Validation XML**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-2.png)

5. Pour spécifier le contenu XML que vous souhaitez valider, sélectionnez **CONTENU**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1-5.png)

6. Sélectionnez la balise body comme contenu à valider.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-3.png)

7. Pour spécifier le schéma que vous souhaitez utiliser pour la validation de la précédente entrée de *contenu*, choisissez **NOM DU SCHÉMA**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-4.png)

8. Enregistrez votre travail   

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-5.png)

Vous avez maintenant terminé de configurer votre connecteur de validation. Dans une application réelle, vous souhaiterez peut-être stocker les données validées dans une application métier telle que Salesforce. Pour envoyer la sortie validée à Salesforce, ajoutez une action.

Pour tester votre action de validation, envoyez une demande au point de terminaison HTTP.

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "En savoir plus sur Enterprise Integration Pack")   

