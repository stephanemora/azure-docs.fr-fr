---
title: Définir les accords entre partenaires dans les flux de travail
description: Ajoutez des accords entre des partenaires dans votre compte d’intégration pour les flux de travail dans Azure Logic Apps à l’aide de l’extension Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 7b634da15248513ee782967eb1c86092e940ec9b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361308"
---
# <a name="add-agreements-between-partners-in-integration-accounts-for-workflows-in-azure-logic-apps"></a>Ajouter des accords entre partenaires à des comptes d’intégration pour les flux de travail dans Azure Logic Apps

Après avoir ajouté des partenaires à votre compte d’intégration, spécifiez la manière dont les partenaires échangent des messages en définissant des [*accords*](logic-apps-enterprise-integration-agreements.md) dans votre compte d’intégration. Les accords aident les organisations à communiquer entre elles de manière transparente en définissant le protocole conforme aux normes du secteur d’activité spécifique à l’échange de messages et en offrant les avantages partagés suivants :

* Ils permettent aux organisations d'échanger des informations en utilisant un format qu'elles connaissent bien.

* Ils améliorent l’efficacité des transactions entre entreprises (B2B).

* Ils facilitent la création, la gestion et l’utilisation d’accords visant à créer des solutions d’intégration d’entreprise.

Un accord nécessite un *partenaire hôte*, qui est toujours votre organisation, et un *partenaire invité*, qui est l’organisation qui échange des messages avec votre organisation. Le partenaire invité peut être une autre entreprise ou même un service au sein de votre organisation. Cet accord vous permet de spécifier la manière de traiter les messages entrants et sortants du point de vue du partenaire hôte.

Cet article explique comment créer et gérer un accord, que vous pouvez ensuite utiliser pour échanger des messages B2B avec un autre partenaire en utilisant les opérations AS2, X12, EDIFACT ou RosettaNet.

Si vous débutez avec les applications logiques, voir [Qu’est-ce qu’Azure Logic Apps](logic-apps-overview.md) ? Pour plus d’informations sur l’intégration d’entreprise B2B, consultez [Workflows d’intégration d’entreprise B2B avec Azure Logic Apps et Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource de compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) dans laquelle vous définissez et stockez les artefacts, comme les parties, les contrats, les certificats, etc., à utiliser dans vos flux de travail d’intégration d’entreprise et B2B. Cette ressource doit remplir les conditions suivantes :

  * Associé au même abonnement Azure que votre ressource d’application logique.

  * Existe dans le même emplacement ou la même région Azure que votre ressource d’application logique.

  * Si vous utilisez le [type de ressource **Application logique (Consommation)**](logic-apps-overview.md#resource-type-and-host-environment-differences), votre compte d’intégration requiert un [lien vers votre ressource d’application logique](logic-apps-enterprise-integration-create-integration-account.md#link-account) avant de pouvoir utiliser des artefacts dans votre workflow.

  * Si vous utilisez le [type de ressource **Application logique (Standard)** ](logic-apps-overview.md#resource-type-and-host-environment-differences), votre compte d’intégration n’a pas besoin d’un lien vers votre ressource d’application logique, mais il est toujours nécessaire pour stocker d’autres artefacts, comme les partenaires, contrats et certificats, ainsi que pour utiliser les opérations [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) et [EDIFACT](logic-apps-enterprise-integration-edifact.md). Votre compte d’intégration doit toujours répondre à d’autres exigences, comme l’utilisation du même abonnement Azure et la présence dans le même emplacement que votre ressource d’application logique.

  > [!NOTE]
  > Actuellement, seul le type de ressource **Application logique (Consommation)** prend en charge les opérations [RosettaNet](logic-apps-enterprise-integration-rosettanet.md). Le type de ressource **Application logique (Standard)** n’inclut pas les opérations [RosettaNet](logic-apps-enterprise-integration-rosettanet.md).

* Au moins deux [partenaires commerciaux](logic-apps-enterprise-integration-partners.md) dans votre compte d’intégration. Un accord requiert un partenaire hôte et un partenaire invité. En outre, un accord requiert que les deux partenaires utilisent le même qualificateur d’*identité d’entreprise* ou un qualificateur compatible et approprié pour un accord AS2, X12, EDIFACT ou RosettaNet.

* Il peut aussi s’agir de la ressource d’application logique et du flux de travail dans lesquels vous souhaitez utiliser l’accord pour échanger des messages. Le workflow nécessite un déclencheur qui démarre le workflow de votre application logique.

Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](logic-apps-overview.md) et [Démarrage rapide : Créer votre première application logique](quickstart-create-first-logic-app-workflow.md).

## <a name="add-an-agreement"></a>Ajouter un accord

1. Dans la zone de recherche du [portail Azure](https://portal.azure.com), entrez `integration accounts` et sélectionnez **Comptes d’intégration**.

1. Sous **Comptes d’intégration**, sélectionnez le compte d’intégration dans lequel vous souhaitez ajouter vos partenaires.

1. Dans le menu du compte d’intégration, sous **Paramètres**, sélectionnez **Accords**.

1. Dans le volet **Accords**, sélectionnez **Ajouter**.

1. Dans le volet **Ajouter**, fournissez les informations suivantes concernant l’accord :

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **Nom** | Oui | <*nom de l'accord*> | Nom de votre accord |
   | **Type d'accord** | Oui | **AS2**, **X12**, **EDIFACT** ou **RosettaNet** | Type de protocole de votre accord. Lorsque vous créez le fichier de votre accord, son contenu doit correspondre au type de l'accord. |
   | **Partenaire hôte** | Oui | <*nom du partenaire hôte*> | Le partenaire hôte représente votre organisation |
   | **Identité de l'hôte** | Oui | <*identificateur du partenaire hôte*> | Identificateur du partenaire hôte. |
   | **Partenaire invité** | Oui | <*nom du partenaire invité*> | Le partenaire invité représente l’organisation qui communique avec votre organisation |
   | **Identité de l'invité** | Oui | <*identificateur du partenaire invité*> | Identificateur du partenaire invité. |
   | **Paramètres de réception** | Variable | Variable | Ces propriétés spécifient la façon dont le partenaire hôte reçoit les messages entrants du partenaire invité dans l’accord. Pour plus d’informations, consultez le type d’accord correspondant : <p>- [Paramètres des messages AS2](logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Paramètres des messages EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Paramètres des messages X12](logic-apps-enterprise-integration-x12.md) |
   | **Paramètres d'envoi** | Variable | Variable | Ces propriétés spécifient la façon dont le partenaire hôte envoie les messages sortants au partenaire invité dans l’accord. Pour plus d’informations, consultez le type d’accord correspondant : <p>- [Paramètres des messages AS2](logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Paramètres des messages EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Paramètres des messages X12](logic-apps-enterprise-integration-x12.md) |
   | **RosettaNet PIP references** (Références de processus PIP RosettaNet) | Variable | Variable | Ce volet contient des informations sur un ou plusieurs processus d’interface partenaire (PIP) pour utiliser les messages RosettaNet. Pour plus d’informations, consultez [Échanger des messages RosettaNet](logic-apps-enterprise-integration-rosettanet.md) |
   |||||

   > [!IMPORTANT]
   > La résolution d’un accord dépend de la correspondance des éléments suivants qui sont définis dans le partenaire et le message entrant :
   >
   > * Qualificateur et identificateur de l’expéditeur
   > * Qualificateur et identificateur du récepteur
   >
   > Si ces valeurs changent pour votre partenaire, veillez à également mettre à jour le contrat.

1. Quand vous avez terminé, sélectionnez **OK**.

   Votre accord figure désormais dans la liste des **Accords**.

## <a name="edit-an-agreement"></a>Modifier un accord

1. Dans la zone de recherche du [portail Azure](https://portal.azure.com), entrez `integration accounts` et sélectionnez **Comptes d’intégration**.

1. Sous **Comptes d’intégration**, sélectionnez le compte d’intégration dans lequel vous souhaitez ajouter vos partenaires.

1. Dans le menu du compte d’intégration, sous **Paramètres**, sélectionnez **Accords**.

1. Dans le volet **Accords**, sélectionnez votre accord, sélectionnez **Modifier**, puis apportez vos modifications.

1. Quand vous avez terminé, sélectionnez **OK**.

## <a name="delete-an-agreement"></a>Supprimer un accord

1. Dans la zone de recherche du [portail Azure](https://portal.azure.com), entrez `integration accounts` et sélectionnez **Comptes d’intégration**.

1. Sous **Comptes d’intégration**, sélectionnez le compte d’intégration dans lequel vous souhaitez ajouter vos partenaires.

1. Dans le menu du compte d’intégration, sous **Paramètres**, sélectionnez **Accords**.

1. Dans le volet **Accords**, sélectionnez l’accord à supprimer, puis sélectionnez **Supprimer**.

1. Sélectionnez **Oui** pour confirmer que vous souhaitez supprimer l’accord.

## <a name="next-steps"></a>Étapes suivantes

* [Échanger des messages AS2](logic-apps-enterprise-integration-as2.md)
* [Échanger des messages EDIFACT](logic-apps-enterprise-integration-edifact.md)
* [Échanger des messages X12](logic-apps-enterprise-integration-x12.md)
* [Messages Exchange RosettaNet](logic-apps-enterprise-integration-rosettanet.md)
