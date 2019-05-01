---
title: Créer et gérer des accords de partenariat commercial - Azure Logic Apps
description: Créer et gérer des accords entre partenaires commerciaux à l’aide d’Azure Logic Apps et Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 447ffb8e-3e91-4403-872b-2f496495899d
ms.date: 04/05/2019
ms.openlocfilehash: 26d653b873e959f0804e0456ed87ee68c39413e5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720678"
---
# <a name="create-and-manage-trading-partner-agreements-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Créer et gérer des accords de partenariat commercial à l’aide d’Azure Logic Apps et Enterprise Integration Pack

Un [partenaire commercial](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*contrat* organisations et entreprises communiquent en toute transparence entre eux en définissant le protocole standard spécifique à utiliser lors de l’échange messages de Business-to-business (B2B). Contrats fournissent les avantages communs, par exemple :

* Permettent aux organisations d’échanger des informations à l’aide d’un format bien connu.
* Améliorer l’efficacité lors de l’exécution des transactions B2B.
* Sont faciles à créer, gérer et utiliser pour créer des solutions d’intégration enterprise.

Cet article explique comment créer un AS2, EDIFACT ou X12 contrat que vous pouvez utiliser lors de la création de solutions d’intégration pour les scénarios B2B enterprise à l’aide de la [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) et [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Une fois que vous créez un accord, vous pouvez ensuite utiliser AS2, EDIFACT ou X12 connecteurs pour échanger des messages de B2B.

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* Un [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) pour stocker votre contrat et autres artefacts B2B. Ce compte d’intégration doit être associé à votre abonnement Azure.

* Au moins deux [des partenaires commerciaux](../logic-apps/logic-apps-enterprise-integration-partners.md) que vous avez déjà créées dans votre compte d’intégration. Un contrat nécessite un partenaire hôte et un partenaire invité. Les deux partenaires doivent utiliser le qualificateur « identité d’entreprise » de même que l’accord que vous souhaitez créer, tels que AS2, X 12 ou EDIFACT.

* Facultatif : L’application logique dans lequel vous voulez utiliser votre contrat et un déclencheur qui démarre le flux de travail de votre application logique. Pour simplement créer votre compte d’intégration et les artefacts de B2B, vous n’avez pas besoin une application logique. Toutefois, avant que votre application logique peut utiliser les artefacts B2B dans votre compte d’intégration, vous devez lier votre compte d’intégration à votre application logique. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md) et [Démarrage rapide : Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Créer des accords

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
Dans le menu principal Azure, sélectionnez **Tous les services**. Dans la zone de recherche, entrez « intégration » comme filtre. Dans les résultats, sélectionnez cette ressource : **Comptes d’intégration**

   ![Recherche du compte d’intégration](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. Sous **comptes d’intégration**, sélectionnez le compte d’intégration dans lequel vous souhaitez créer le contrat.

   ![Sélectionnez le compte d’intégration dans lequel vous souhaitez créer le contrat](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. Dans le volet de droite, sous **composants**, choisissez le **accords** vignette.

   ![Choisissez « Contrats »](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. Sous **Contrats**, choisissez **Ajouter**. Dans le **ajouter** volet, fournissent des informations sur votre contrat, par exemple :

   ![Choisir « Ajouter »](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Propriété | Obligatoire | Value | Description |
   |----------|----------|-------|-------------|
   | **Name** | Oui | <*agreement-name*> | Le nom de votre contrat |
   | **Type de contrat** | Oui | **AS2**, **X12**, ou **EDIFACT** | Le type de protocole de votre contrat. Lorsque vous créez votre fichier de contrat, le contenu dans ce fichier doit correspondre au type de contrat. | |  
   | **Partenaire hôte** | Oui | <*host-partner-name*> | Le partenaire hôte représente l’organisation qui spécifie le contrat |
   | **Identité de l’hôte** | Oui | <*host-partner-identifier*> | Identificateur du partenaire hôte |
   | **Partenaire invité** | Oui | <*guest-partner-name*> | Le partenaire invité représente l’organisation qui travaille avec le partenaire hôte. |
   | **Identité de l’invité** | Oui | <*guest-partner-identifier*> | Identificateur du partenaire invité |
   | **Paramètres de réception** | Varie | Varie | Ces propriétés spécifient comment gérer tous les messages entrants reçus par le contrat. Pour plus d’informations, consultez le type de contrat respectif : <p>- [Paramètres des messages AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Paramètres de message EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Paramètres de message X12](logic-apps-enterprise-integration-x12.md) |
   | **Paramètres d’envoi** | Varie | Varie | Ces propriétés spécifient comment gérer tous les messages sortants envoyés par le contrat. Pour plus d’informations, consultez le type de contrat respectif : <p>- [Paramètres des messages AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Paramètres de message EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Paramètres de message X12](logic-apps-enterprise-integration-x12.md) |
   |||||

1. Lorsque vous avez terminé la création de votre contrat, sur le **ajouter** page, choisissez **OK**et revenir à votre compte d’intégration.

   Le **accords** liste affiche maintenant votre nouveau contrat.

## <a name="edit-agreements"></a>Modifier des contrats

1. Dans le [Azure portal](https://portal.azure.com), dans le menu Azure principal, sélectionnez **tous les services**.

1. Dans la zone de recherche, entrez « intégration » comme filtre. Dans les résultats, sélectionnez cette ressource : **Comptes d’intégration**

1. Sous **comptes d’intégration**, sélectionnez le compte d’intégration qui a l’accord que vous souhaitez modifier.

1. Dans le volet de droite, sous **composants**, choisissez le **accords** vignette.

1. Sous **accords**, sélectionnez votre contrat et choisissez **modifier**.

1. Vérifiez, puis enregistrez vos modifications.

## <a name="delete-agreements"></a>Supprimer des contrats

1. Dans le [Azure portal](https://portal.azure.com), dans le menu Azure principal, sélectionnez **tous les services**.

1. Dans la zone de recherche, entrez « intégration » comme filtre. Dans les résultats, sélectionnez cette ressource : **Comptes d’intégration**

1. Sous **comptes d’intégration**, sélectionnez le compte d’intégration qui a l’accord que vous souhaitez supprimer.

1. Dans le volet de droite, sous **composants**, choisissez le **accords** vignette.

1. Sous **accords**, sélectionnez votre contrat et choisissez **supprimer**.

1. Confirmez que vous souhaitez supprimer le contrat sélectionné.

## <a name="next-steps"></a>Étapes suivantes

* [Échanger des messages AS2](logic-apps-enterprise-integration-as2.md)
* [Échanger des messages EDIFACT](logic-apps-enterprise-integration-edifact.md)
* [Messages Exchange X12](logic-apps-enterprise-integration-x12.md)
