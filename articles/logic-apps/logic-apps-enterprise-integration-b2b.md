---
title: Échanger des messages B2B à l’aide de workflows
description: Échangez des messages entre partenaires commerciaux en créant des workflows avec Azure Logic Apps et Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/17/2021
ms.openlocfilehash: 6c9580a051a7473e7009cf8df8d2ea8759834683
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351176"
---
# <a name="exchange-b2b-messages-between-partners-using-workflows-in-azure-logic-apps"></a>Échangez des messages B2B entre partenaires en utilisant des workflows dans Azure Logic Apps

Si vous disposez d’un compte d’intégration définissant des partenaires et contrats commerciaux, vous pouvez créer un workflow B2B automatisé pour l’échange de messages entre partenaires commerciaux en utilisant Azure Logic Apps. Votre workflow peut utiliser des connecteurs qui prennent en charge des protocoles standard, tels que AS2, X12, EDIFACT et RosettaNet. Vous pouvez également inclure des opérations fournies par d’autres [connecteurs dans Azure Logic Apps](/connectors/connector-reference/connector-reference-logicapps-connectors), comme Office 365 Outlook, SQL Server et Salesforce.

Cet article explique comment créer un exemple de workflow d’application logique qui peut recevoir des requêtes HTTP à l’aide d’un déclencheur **Requête**, décoder le contenu des messages à l’aide des actions **Décodage AS2** et **Décodage X12**, et retourner une réponse à l’aide de l’action **Réponse**. Cet exemple utilise le Concepteur de workflow dans le portail Azure, mais vous pouvez suivre des étapes similaires pour le Concepteur de workflow dans Visual Studio.

Si vous débutez avec les applications logiques, voir [Qu’est-ce qu’Azure Logic Apps](logic-apps-overview.md) ? Pour plus d’informations sur l’intégration d’entreprise B2B, consultez [Workflows d’intégration d’entreprise B2B avec Azure Logic Apps](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource de compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) dans laquelle vous définissez et stockez les artefacts, comme les parties, les contrats, les certificats, etc., à utiliser dans vos flux de travail d’intégration d’entreprise et B2B. Cette ressource doit remplir les conditions suivantes :

  * Associé au même abonnement Azure que votre ressource d’application logique.

  * Existe dans le même emplacement ou la même région Azure que votre ressource d’application logique.

  * Si vous utilisez le [type de ressource **Application logique (Consommation)**](logic-apps-overview.md#resource-type-and-host-environment-differences), votre compte d’intégration requiert un [lien vers votre ressource d’application logique](logic-apps-enterprise-integration-create-integration-account.md#link-account) avant de pouvoir utiliser des artefacts dans votre workflow.

  * Si vous utilisez le [type de ressource **Application logique (Standard)**](logic-apps-overview.md#resource-type-and-host-environment-differences), votre compte d’intégration n’a pas besoin d’un lien vers votre ressource d’application logique, mais il est toujours nécessaire pour stocker d’autres artefacts, comme les partenaires, contrats et certificats, ainsi que pour utiliser les opérations [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) ou [EDIFACT](logic-apps-enterprise-integration-edifact.md). Votre compte d’intégration doit toujours répondre à d’autres exigences, comme l’utilisation du même abonnement Azure et la présence dans le même emplacement que votre ressource d’application logique.

  > [!NOTE]
  > Actuellement, seul le type de ressource **Application logique (Consommation)** prend en charge les opérations [RosettaNet](logic-apps-enterprise-integration-rosettanet.md). Le type de ressource **Application logique (Standard)** n’inclut pas les opérations [RosettaNet](logic-apps-enterprise-integration-rosettanet.md).

* Au moins deux [partenaires commerciaux](logic-apps-enterprise-integration-partners.md) dans votre compte d’intégration. Les définitions des deux partenaires doivent utiliser le même qualificateur d’*identité d’entreprise*, qui est AS2, X12, EDIFACT ou RosettaNet.

* Un [accord AS2 et un accord X12](logic-apps-enterprise-integration-agreements.md) pour les partenaires que vous utilisez dans ce workflow. Un accord requiert un partenaire hôte et un partenaire invité.

* Une ressource d’application logique avec un workflow vide dans lequel vous pouvez ajouter le déclencheur [Requête](../connectors/connectors-native-reqres.md), puis les actions suivantes :

  * [Décodage AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [Condition](../logic-apps/logic-apps-control-flow-conditional-statement.md) qui envoie une [Réponse](../connectors/connectors-native-reqres.md) en fonction de la réussite ou de l’échec de l’action de décodage AS2

  * [Décoder le message X12](../logic-apps/logic-apps-enterprise-integration-x12.md)

<a name="add-request-trigger"></a>

## <a name="add-the-request-trigger"></a>Ajout du déclencheur Request

Pour démarrer le workflow dans cet exemple, ajoutez le déclencheur Requête.

### <a name="consumption"></a>[Consommation](#tab/consumption)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre ressource d’application logique et un workflow vide dans le concepteur de workflow.

1. Sous la zone de recherche du concepteur, sélectionnez **Tout** si cette option n’est pas sélectionnée. Dans la zone de recherche, entrez `when a http request`. Sélectionnez le déclencheur Requête nommé **Lors de la réception d’une requête HTTP**.

   ![Capture d’écran montrant le portail Azure et le concepteur mutualisé avec « Lors de la réception d’une requête HTTP » dans la zone de recherche et le déclencheur Requête sélectionné.](./media/logic-apps-enterprise-integration-b2b/select-request-trigger-consumption.png)

1. Dans le déclencheur, laissez la zone **Schéma JSON du corps de la requête** vide.

   En effet, le déclencheur recevra un message X12 dans un format de fichier plat.

   ![Capture d’écran montrant les propriétés du déclencheur Requête et le concepteur mutualisé.](./media/logic-apps-enterprise-integration-b2b/request-trigger-consumption.png)

1. Lorsque c’est chose faite, dans la barre d’outils du concepteur, sélectionnez **Enregistrer**.

   Cette étape génère l’**URL HTTP POST** que vous utilisez ultérieurement pour envoyer une requête qui déclenche un workflow d’application logique.

   ![Capture d’écran montrant le concepteur mutualisé et l’URL générée pour le déclencheur Requête.](./media/logic-apps-enterprise-integration-b2b/request-trigger-generated-url-consumption.png)

1. Copiez et enregistrez l’URL pour l’utiliser plus tard.

### <a name="standard"></a>[Standard](#tab/standard)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre ressource d’application logique et un workflow vide dans le concepteur de workflow.

1. Dans le concepteur, sélectionnez **Choisir une opération**. Dans la zone de recherche, sélectionnez **Intégré**. Dans la zone de recherche, entrez `when a http request`. Sélectionnez le déclencheur Requête nommé **Lors de la réception d’une requête HTTP**.

   ![Capture d’écran montrant le portail Azure et le concepteur monolocataire avec « Lors de la réception d’une requête HTTP » dans la zone de recherche et le déclencheur Requête sélectionné.](./media/logic-apps-enterprise-integration-b2b/select-request-trigger-standard.png)

1. Dans le déclencheur, laissez la zone **Schéma JSON du corps de la requête** vide.

   En effet, le déclencheur recevra un message X12 dans un format de fichier plat.

   ![Capture d’écran montrant les propriétés du déclencheur Requête et le concepteur monolocataire.](./media/logic-apps-enterprise-integration-b2b/request-trigger-standard.png)

1. Lorsque c’est chose faite, dans la barre d’outils du concepteur, sélectionnez **Enregistrer**.

   Cette étape génère l’**URL HTTP POST** que vous utilisez ultérieurement pour envoyer une requête qui déclenche un workflow d’application logique.

   ![Capture d’écran montrant le concepteur monolocataire et l’URL générée pour le déclencheur Requête.](./media/logic-apps-enterprise-integration-b2b/request-trigger-generated-url-standard.png)

1. Copiez et enregistrez l’URL pour l’utiliser plus tard.

---

<a name="add-decode-as2-trigger"></a>

## <a name="add-the-decode-as2-action"></a>Ajouter l’action Décodage AS2

À présent, ajoutez les actions B2B pour cet exemple, qui utilise les actions AS2 et X12.

### <a name="consumption"></a>[Consommation](#tab/consumption)

1. Sous le déclencheur, sélectionnez **Nouvelle étape**.

   > [!TIP]
   > Pour masquer les détails du déclencheur de requête, sélectionnez la barre de titre du déclencheur.

   ![Capture d’écran montrant le concepteur et le déclencheur mutualisé avec l’option « Nouvelle étape » sélectionnée.](./media/logic-apps-enterprise-integration-b2b/add-action-under-trigger-consumption.png)

1. Sous la zone de recherche **Choisir une opération**, sélectionnez **Toutes**, si l’option n’est pas sélectionnée. Dans la zone de recherche, entrez `as2`, puis sélectionnez **Décodage AS2**.

   ![Capture d’écran montrant le concepteur mutualisé avec l’action « Décodage AS2 » sélectionnée.](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action-consumption.png)

1. Dans la propriété **Message à décoder** de l’action, indiquez l’entrée que vous voulez que l’action AS2 décode, à savoir la sortie `body` du déclencheur de requête. Vous avez plusieurs moyens de spécifier ce contenu comme entrée, soit à partir de la liste de contenu dynamique, soit en tant qu’expression :

   * Pour opérer une sélection dans une liste des sorties de déclencheur disponibles, cliquez à l’intérieur de la zone **Message à décoder**. Lorsque la liste de contenu dynamique s’affiche, sous **Lors de la réception d’une requête HTTP**, sélectionnez la valeur de propriété **Corps**, par exemple :

     ![Capture d’écran montrant le concepteur mutualisé avec la liste de contenu dynamique et la propriété « Corps » sélectionnée.](./media/logic-apps-enterprise-integration-b2b/select-trigger-output-body-consumption.png)

     > [!TIP]
     > Si aucune sortie de déclencheur ne s’affiche, dans la liste de propriétés dynamiques, sous **Lors de la réception d’une requête HTTP**, sélectionnez **Afficher plus**.

   * Pour entrer une expression qui fait référence à la sortie `body` du déclencheur, cliquez dans la zone **Message à décoder**. Lorsque la liste de contenu dynamique s’affiche, sélectionnez **Expression**. Dans l’éditeur d’expression, entrez l’expression suivante, puis sélectionnez **OK** :

     `triggerOutputs()['body']`

     Ou bien, dans la zone **Message à décoder**, entrez directement l’expression suivante :

     `@triggerBody()`

     L’expression est résolue en jeton **Corps**.

     ![Capture d’écran montrant le concepteur mutualisé avec la sortie de la propriété « Corps » résolue.](./media/logic-apps-enterprise-integration-b2b/resolved-body-property-consumption.png)

1. Dans la propriété **En-têtes de message** de l’action, entrez les en-têtes requis pour l’action AS2, qui se trouvent dans la sortie `headers` du déclencheur Requête.

   1. Pour entrer une expression qui fait référence à la sortie `headers` du déclencheur, sélectionnez **Basculer les en-têtes de message en mode texte**.

      ![Capture d’écran montrant le concepteur mutualisé avec l’option « Basculer les en-têtes de message en mode texte » sélectionnée.](./media/logic-apps-enterprise-integration-b2b/switch-text-mode-consumption.png)

   1. Cliquez dans la zone **En-têtes de message**. Lorsque la liste de contenu dynamique s’affiche, sélectionnez **Expression**. Dans l’éditeur d’expression, entrez l’expression suivante, puis sélectionnez **OK** :

      `triggerOutputs()['Headers']`

      Dans l’action **Décodage AS2**, l’expression apparaît maintenant en tant que jeton :

      ![Capture d’écran montrant le concepteur mutualisé et la zone « En-têtes de message » avec le jeton « () « @triggerOutputs()[’Headers’] ».](./media/logic-apps-enterprise-integration-b2b/as2-header-expression-consumption.png)

   1. Pour obtenir le jeton d’expression à résoudre dans le jeton **En-têtes**, basculez entre le concepteur et le mode Code. Après cette étape, l’action **Décodage AS2** ressemble à cet exemple :

      ![Capture d’écran montrant la sortie du concepteur mutualisé et les en-têtes résolus à partir du déclencheur.](./media/logic-apps-enterprise-integration-b2b/resolved-as2-header-expression-consumption.png)

### <a name="standard"></a>[Standard](#tab/standard)

1. Sous le déclencheur, sélectionnez **Insérer une nouvelle étape** (signe plus), puis **Ajouter une action**.

   ![Capture d’écran montrant le concepteur monolocataire et le déclencheur avec le signe plus sélectionné.](./media/logic-apps-enterprise-integration-b2b/add-action-under-trigger-standard.png)

1. Dans la zone de recherche **Choisir une opération**, sélectionnez **Azure**, si cette option n’est pas déjà sélectionnée. Dans la zone de recherche, entrez `as2`, puis sélectionnez **Décoder un message AS2**.

   ![Capture d’écran montrant le concepteur monolocataire avec l’action « Décoder un message AS2 » sélectionnée.](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action-standard.png)

1. Lorsque vous êtes invité à créer une connexion à votre compte d’intégration, fournissez un nom à utiliser pour votre connexion, puis sélectionnez votre compte d’intégration et **Créer**.

1. Dans la propriété **Corps** de l’action, indiquez l’entrée que vous voulez que l’action AS2 décode, à savoir la sortie `body` du déclencheur de requête. Vous avez plusieurs moyens de spécifier ce contenu comme entrée de l’action, soit à partir de la liste de contenu dynamique, soit en tant qu’expression :

   * Pour opérer une sélection dans une liste des sorties de déclencheur disponibles, cliquez à l’intérieur de la zone de la propriété **Corps**. Lorsque la liste de contenu dynamique s’affiche, sous **Lors de la réception d’une requête HTTP**, sélectionnez la valeur de propriété **Corps**, par exemple :

     ![Capture d’écran montrant le concepteur monolocataire avec la liste de contenu dynamique et la propriété « Corps » sélectionnée.](./media/logic-apps-enterprise-integration-b2b/select-trigger-output-body-standard.png)

     > [!TIP]
     > Si aucune sortie de déclencheur ne s’affiche, dans la liste de propriétés dynamiques, sous **Lors de la réception d’une requête HTTP**, sélectionnez **Afficher plus**.

   * Pour entrer une expression qui fait référence à la sortie `body` du déclencheur, cliquez dans la zone de la propriété **Corps**. Lorsque la liste de contenu dynamique s’affiche, sélectionnez **Expression**. Dans l’éditeur d’expression, entrez l’expression suivante, puis sélectionnez **OK** :

     `triggerOutputs()['body']`

     Ou, dans la zone de propriété **Corps**, entrez directement l’expression suivante :

     `@triggerBody()`

     L’expression est résolue en jeton **Corps**.

     ![Capture d’écran montrant le concepteur monolocataire avec la sortie de la propriété « Corps » résolue.](./media/logic-apps-enterprise-integration-b2b/resolved-body-property-standard.png)

1. Dans la zone de propriété **En-têtes**, entrez les en-têtes requis pour l’action AS2, qui se trouvent dans la sortie `headers` du déclencheur de requête.

   1. Pour entrer une expression qui fait référence à la sortie `headers` du déclencheur, sélectionnez **Basculer les en-têtes de message en mode texte**.

      ![Capture d’écran montrant le concepteur monolocataire avec l’option « Basculer les en-têtes de message en mode texte » sélectionnée.](./media/logic-apps-enterprise-integration-b2b/switch-text-mode-standard.png)

   1. Cliquez à l’intérieur de la zone de propriété **En-têtes**. Lorsque la liste de contenu dynamique s’affiche, sélectionnez **Expression**. Dans l’éditeur d’expression, entrez l’expression suivante, puis sélectionnez **OK** :

      `triggerOutputs()['Headers']`

      Dans l’action **Décoder un message AS2**, l’expression apparaît maintenant en tant que jeton :

      ![Capture d’écran montrant un concepteur monolocataire et la propriété « En-têtes » avec le jeton « @triggerOutputs()[’Headers’] ».](./media/logic-apps-enterprise-integration-b2b/as2-header-expression-standard.png)

   1. Pour obtenir le jeton d’expression à résoudre dans le jeton **En-têtes**, basculez entre le concepteur et le mode Code. Après cette étape, l’action **Décodage AS2** ressemble à cet exemple :

      ![Capture d’écran montrant la sortie du concepteur monolocataire et les en-têtes résolus à partir du déclencheur.](./media/logic-apps-enterprise-integration-b2b/resolved-as2-header-expression-standard.png)

---

<a name="add-response-action"></a>

## <a name="add-the-response-action-as-a-message-receipt"></a>Ajouter l’action Réponse en tant qu’accusé de réception d’un message

Pour informer le partenaire commercial de la réception du message, vous pouvez retourner une réponse contenant une notification d’état du message (MDN) AS2 à l’aide des actions Condition et Réponse. En ajoutant ces actions immédiatement après l’action AS2, le workflow de l’application logique peut continuer le traitement si l’action AS2 réussit. Sinon, si l’action AS2 échoue, le workflow de l’application logique arrête le traitement.

### <a name="consumption"></a>[Consommation](#tab/consumption)

1. Sous l’action **Décodage AS2**, sélectionnez **Nouvelle étape**.

1. Dans la zone de recherche **Choisir une opération**, sélectionnez **Intégrée**, si cette option n’est pas déjà sélectionnée. Dans la zone de recherche, entrez `condition`. Sélectionnez l’action **Condition**.

   ![Capture d’écran montrant le concepteur mutualisé et l’action « Condition ».](./media/logic-apps-enterprise-integration-b2b/add-condition-action-consumption.png)

   La forme Condition s’affiche, qui inclut les chemins d’accès déterminant si la condition est remplie ou non.

   ![Capture d’écran montrant le concepteur mutualisé et la forme Condition avec des chemins d’accès vides.](./media/logic-apps-enterprise-integration-b2b/added-condition-action-consumption.png)

1. Spécifiez à présent la condition à évaluer. Dans la zone **Choisir une valeur**, entrez l’expression suivante :

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   Dans la zone centrale, assurez-vous que l’opération de comparaison est définie sur `is equal to`. Dans la zone de droite, entrez la valeur `Expected`.

1. Enregistrez votre workflow d’application logique. Pour obtenir l’expression à résoudre en ce jeton, basculez entre le concepteur et le mode Code.

   ![Capture d’écran montrant le concepteur mutualisé et la forme Condition avec une opération.](./media/logic-apps-enterprise-integration-b2b/evaluate-condition-expression-consumption.png)

1. Maintenant, spécifiez les réponses à retourner selon que l’action **Décodage AS2** réussit ou non.

   1. Pour le cas où l’action **Décodage AS2** réussit, dans la forme **Si vrai**, sélectionnez **Ajouter une action**. Sous la zone de recherche **Choisir une opération**, entrez `response`, puis sélectionnez **Réponse**.

      ![Capture d’écran montrant le concepteur mutualisé et l’action « Réponse ».](./media/logic-apps-enterprise-integration-b2b/select-response-consumption.png)

   1. Pour accéder au MDN AS2 à partir de la sortie de l’action **Décodage AS2**, spécifiez les expressions suivantes :

      * Dans la propriété **En-têtes** de l’action **Réponse**, entrez l’expression suivante :

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * Dans la propriété **Corps** de l’action **Réponse**, entrez l’expression suivante :

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. Pour obtenir les expressions à résoudre en tant que jetons, basculez entre le concepteur et le mode Code :

      ![Capture d’écran montrant le concepteur mutualisé et l’expression résolue pour accéder à AS2 MDN.](./media/logic-apps-enterprise-integration-b2b/response-success-resolved-expression-consumption.png)

   1. Dans le cas où l’action **Décodage AS2** échoue, dans la forme **Faux**, sélectionnez **Ajouter une action**. Sous la zone de recherche **Choisir une opération**, entrez `response`, puis sélectionnez **Réponse**. Configurez l’action **Réponse** pour retourner l’état et l’erreur de votre choix.

1. Enregistrez votre workflow d’application logique.

### <a name="standard"></a>[Standard](#tab/standard)

1. Sous l’action **Décoder un message AS2**, sélectionnez **Insérer une nouvelle étape** (signe plus), puis **Ajouter une action**.

1. Dans la zone de recherche **Choisir une opération**, sélectionnez **Intégrée**, si cette option n’est pas déjà sélectionnée. Dans la zone de recherche, entrez `condition`. Sélectionnez l’action **Condition**.

   ![Capture d’écran montrant un concepteur monolocataire avec l’action « Condition » sélectionnée.](./media/logic-apps-enterprise-integration-b2b/add-condition-action-standard.png)

   La forme Condition s’affiche, qui inclut les chemins d’accès déterminant si la condition est remplie ou non.

   ![Capture d’écran montrant un concepteur monolocataire et la forme « Condition » avec des chemins d’accès vides.](./media/logic-apps-enterprise-integration-b2b/added-condition-action-standard.png)

1. Spécifiez à présent la condition à évaluer. Sélectionnez la forme **Condition** afin que le panneau Détails s’affiche. Dans la zone **Choisir une valeur**, entrez l’expression suivante :

   `@body('Decode_AS2_message')?['AS2Message']?['MdnExpected']`

   Dans la zone centrale, assurez-vous que l’opération de comparaison est définie sur `is equal to`. Dans la zone de droite, entrez la valeur `Expected`.

1. Enregistrez votre workflow d’application logique. Pour obtenir l’expression à résoudre en ce jeton, basculez entre le concepteur et le mode Code.

   ![Capture d’écran montrant un concepteur monolocataire et la forme Condition avec une opération.](./media/logic-apps-enterprise-integration-b2b/evaluate-condition-expression-standard.png)

1. Maintenant, spécifiez les réponses à retourner selon que l’action **Décoder un message AS2** réussi ou non.

   1. Dans le cas où l’action **Décoder un message AS2** se déroule correctement, dans la forme **True**, sélectionnez le signe plus, puis **Ajouter une action**. Dans le volet **Ajouter une action**, dans la zone de recherche **Choisir une opération**, entrez `response`, puis sélectionnez **Réponse**.

      ![Capture d’écran montrant un concepteur monolocataire et l’action « Réponse ».](./media/logic-apps-enterprise-integration-b2b/select-response-standard.png)

   1. Pour accéder au MDN AS2 à partir de la sortie de l’action **Décodage AS2**, spécifiez les expressions suivantes :

      * Dans la propriété **En-têtes** de l’action **Réponse**, entrez l’expression suivante :

        `@body('Decode_AS2_message')?['OutgoingMdn']?['OutboundHeaders']`

      * Dans la propriété **Corps** de l’action **Réponse**, entrez l’expression suivante :

        `@body('Decode_AS2_message')?['OutgoingMdn']?['Content']`

   1. Pour obtenir les expressions à résoudre en tant que jetons, basculez entre le concepteur et le mode Code :

      ![Capture d’écran montrant un concepteur monolocataire et une expression résolue pour accéder à AS2 MDN.](./media/logic-apps-enterprise-integration-b2b/response-success-resolved-expression-standard.png)

   1. Dans le cas où l’action **Décoder un message AS2** échoue, dans la forme **False**, sélectionnez le signe plus, puis **Ajouter une action**. Dans le volet **Ajouter une action**, dans la zone de recherche **Choisir une opération**, entrez `response`, puis sélectionnez **Réponse**. Configurez l’action **Réponse** pour retourner l’état et l’erreur de votre choix.

1. Enregistrez votre workflow d’application logique.

---

<a name="add-decode-x12-action"></a>

## <a name="add-the-decode-x12-message-action"></a>Ajouter l’action Décoder le message X12

Ajoutez maintenant l’action **Décoder le message X12**.

### <a name="consumption"></a>[Consommation](#tab/consumption)

1. Sous l’action **Réponse**, sélectionnez **Ajouter une action**.

1. Sous **Choisir une opération**, dans la zone de recherche, entrez `x12 decode`, puis sélectionnez **Décoder le message X12**.

   ![Capture d’écran montrant le concepteur mutualisé et l’action « Décoder un message X12 » sélectionnée.](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action-consumption.png)

1. Si l’action X12 vous demande les informations de connexion, indiquez le nom de la connexion, sélectionnez le compte d’intégration à utiliser, puis sélectionnez **Créer**.

   ![Capture d’écran montrant le concepteur mutualisé et la connexion au compte d’intégration.](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection-consumption.png)

1. Spécifiez à présent l’entrée pour l’action X12. Cet exemple utilise la sortie de l’action AS2, qui est le contenu du message, mais ce contenu est au format d’objet JSON et encodé en base64. Par conséquent, vous devez convertir ce contenu en chaîne.

   Dans la zone **Message de fichier plat X12 à décoder**, entrez l’expression suivante pour convertir la sortie AS2 :

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

1. Enregistrez votre workflow d’application logique. Pour obtenir l’expression à résoudre en ce jeton, basculez entre le concepteur et le mode Code.

    ![Capture d’écran montrant le concepteur mutualisé et la conversion du contenu encodé en base64 en chaîne.](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content-consumption.png)

1. Enregistrez votre workflow d’application logique.

   Si vous avez besoin d’étapes supplémentaires pour ce workflow d’application logique, par exemple, pour décoder le contenu du message et sortir ce contenu au format d’objet JSON, continuez à ajouter les actions nécessaires pour votre workflow d’application logique.

### <a name="standard"></a>[Standard](#tab/standard)

1. Sous l’action **Réponse**, sélectionnez le signe plus, puis **Ajouter une action**. Dans le volet **Ajouter une action**, sous la zone de recherche **Choisir une opération**, sélectionnez **Azure**, si l’option n’est pas déjà sélectionnée. Dans la zone de recherche, entrez `x12 decode`, puis sélectionnez **Décoder un message X12**.

   ![Capture d’écran montrant le concepteur monolocataire et l’action « Décoder un message X12 » sélectionnée.](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action-standard.png)

1. Si l’action X12 vous demande les informations de connexion, indiquez le nom de la connexion, sélectionnez le compte d’intégration à utiliser, puis sélectionnez **Créer**.

   ![Capture d’écran montrant le concepteur monolocataire et la connexion à un compte d’intégration.](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection-standard.png)

1. Spécifiez à présent l’entrée pour l’action X12. Cet exemple utilise la sortie de l’action AS2, qui est le contenu du message, mais ce contenu est au format d’objet JSON et encodé en base64. Par conséquent, vous devez convertir ce contenu en chaîne.

   Dans la zone **Message de fichier plat X12 à décoder**, entrez l’expression suivante pour convertir la sortie AS2 :

   `@base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])`

1. Enregistrez votre workflow d’application logique. Pour obtenir l’expression à résoudre en ce jeton, basculez entre le concepteur et le mode Code.

    ![Capture d’écran montrant le concepteur monolocataire et la conversion du contenu encodé en base64 en chaîne.](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content-standard.png)

1. Enregistrez à nouveau votre workflow d’application logique.

   Si vous avez besoin d’étapes supplémentaires pour ce workflow d’application logique, par exemple, pour décoder le contenu du message et sortir ce contenu au format d’objet JSON, continuez à ajouter les actions nécessaires pour votre workflow d’application logique.

---

Vous avez maintenant terminé la configuration de votre workflow d’application logique B2B. Dans une application réelle, vous pourriez stocker les données X12 décodées dans une application métier ou un magasin de données. Par exemple, consultez la documentation suivante :

* [Se connecter aux systèmes SAP à partir d’Azure Logic Apps](logic-apps-using-sap-connector.md)

* [Superviser, créer et gérer des fichiers SFTP à l’aide de SSH et d’Azure Logic Apps](../connectors/connectors-sftp-ssh.md)

Pour vous connecter à vos propres applications métier et utiliser ces API dans votre application logique, vous pouvez ajouter des actions ou [écrire des API personnalisées](logic-apps-create-api-app.md).

## <a name="next-steps"></a>Étapes suivantes

* [Recevoir des appels HTTPs entrants et y répondre](../connectors/connectors-native-reqres.md)
* [Échanger des messages AS2 pour l’intégration d’entreprise B2B](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [Échanger des messages XS2 pour l’intégration d’entreprise B2B](../logic-apps/logic-apps-enterprise-integration-x12.md)
* En savoir plus sur [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
