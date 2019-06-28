---
title: Messages AS2 pour l’intégration d’entreprise B2B - Azure Logic Apps
description: Échanger des messages AS2 dans Azure Logic Apps avec Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: b494f6524e5105a95bc8a24a6fa2521abcca3f7b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64729398"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Échanger des messages AS2 dans le cadre d’une intégration d’entreprise B2B dans Azure Logic Apps avec Enterprise Integration Pack

Pour utiliser des messages AS2 dans Azure Logic Apps, vous pouvez vous servir du connecteur AS2, qui fournit des déclencheurs et des actions pour la gestion de la communication AS2. Par exemple, pour garantir la sécurité et la fiabilité lors de la transmission des messages, vous pouvez utiliser ces actions :

* [Action **Coder en message AS2**](#encode) pour fournir les fonctionnalités de chiffrement, de signature numérique et d’accusés de réception par notification de réception du message (MDN), ce qui renforce la non-répudiation. Par exemple, cette action applique des en-têtes AS2/HTTP et effectue les tâches suivantes lorsqu’elles sont configurées :

  * Signature des messages sortants.
  * Chiffrement des messages sortants.
  * Compression du message.
  * Transmission du nom de fichier dans l’en-tête MIME.

* [Action **décoder le message AS2**](#decode) pour fournir les fonctionnalités de déchiffrement, de signature numérique et d’accusés de réception par notification de réception du message (MDN). Par exemple, cette action effectue les tâches suivantes : 

  * Traitement des en-têtes AS2/HTTP.
  * Rapprochement des MDN reçues avec le message sortant d’origine.
  * Mise à jour et mise en corrélation les enregistrements dans la base de données de non-répudiation.
  * Écriture des enregistrements pour le rapport d’état AS2.
  * Génération des contenus de charge codés en base64.
  * Détermination du caractère obligatoire des MDN. Selon le contrat AS2, détermination du caractère synchrone ou asynchrone des MDN.
  * Génération des MDN synchrones ou asynchrones en fonction du contrat AS2.
  * Définition des propriétés et des jetons de corrélation sur les MDN.

  Cette action effectue également les tâches suivantes lorsqu’elles sont configurées :

  * Vérification de la signature.
  * Déchiffrement des messages.
  * Décompression du message. 
  * Vérification et interdiction des doublons d’ID de message.

Cet article explique comment ajouter des actions de codage et de décodage AS2 à une application logique existante.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* L’application logique à partir de laquelle vous souhaitez utiliser le connecteur AS2 et un déclencheur qui démarre le flux de travail de votre application logique. Le connecteur AS2 fournit uniquement des actions, pas des déclencheurs. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md) et [Démarrage rapide : Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Un [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) associé à votre abonnement Azure et lié à l’application logique dans laquelle vous prévoyez d’utiliser le connecteur AS2. Votre application logique et votre compte d’intégration doivent tous deux exister dans le même emplacement ou dans la même région Azure.

* Au moins deux [partenaires commerciaux](../logic-apps/logic-apps-enterprise-integration-partners.md) déjà définis dans votre compte d’intégration à l’aide du qualificateur d’identité AS2.

* Pour pouvoir utiliser le connecteur AS2, vous devez créer un [contrat](../logic-apps/logic-apps-enterprise-integration-agreements.md) AS2 entre vos partenaires commerciaux et le stocker dans votre compte d’intégration.

* Si vous utilisez **Azure Key Vault** pour la gestion des certificats, vérifiez que les clés de coffre autorisent les opérations [Chiffrer](../key-vault/key-vault-overview.md) et **Déchiffrer**. Sinon, le codage et le décodage échouent.

  Dans le portail Azure, accédez à votre coffre de clés, affichez les **opérations autorisées** sur les clés du coffre et vérifiez que les actions **Chiffrer** et **Déchiffrer** sont sélectionnées.

  ![Vérifier les opérations sur les clés de coffre](media/logic-apps-enterprise-integration-as2/vault-key-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Encoder des messages AS2

1. Si ce n’est pas déjà fait, dans le [portail Azure](https://portal.azure.com), ouvrez votre application logique dans le Concepteur d’applications logiques.

1. Dans le concepteur, ajoutez une nouvelle action à votre application logique. 

1. Sous **Choisir une action** et la zone de recherche, sélectionnez **Tous**. Dans la zone de recherche, entrez « encode as2 », puis sélectionnez cette action : **Coder en message AS2**.

   ![Sélectionnez « Coder en message AS2 ».](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Si vous n’avez pas encore de connexions dans votre compte d’intégration, vous êtes invité à créer une connexion à cette étape. Donnez un nom à votre connexion, sélectionnez le compte d’intégration que vous souhaitez connecter, puis choisissez **Créer**.

   ![Créer une connexion à un compte d’intégration](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  
 
1. Maintenant, fournissez des informations pour les propriétés suivantes :

   | Propriété | Description |
   |----------|-------------|
   | **AS2-From** | Identificateur de l’expéditeur du message tel que spécifié par votre contrat AS2 |
   | **AS2-To** | Identificateur du destinataire du message tel que spécifié par votre contrat AS2 |
   | **body** | Charge utile du message |
   |||

   Par exemple :

   ![Propriétés de codage de message](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Décoder des messages AS2

1. Si ce n’est pas déjà fait, dans le [portail Azure](https://portal.azure.com), ouvrez votre application logique dans le Concepteur d’applications logiques.

1. Dans le concepteur, ajoutez une nouvelle action à votre application logique. 

1. Sous **Choisir une action** et la zone de recherche, sélectionnez **Tous**. Dans la zone de recherche, entrez « decode as2 », puis sélectionnez cette action : **Décoder le message AS2**

   ![Sélectionnez « Décoder le message AS2 ».](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Si vous n’avez pas encore de connexions dans votre compte d’intégration, vous êtes invité à créer une connexion à cette étape. Donnez un nom à votre connexion, sélectionnez le compte d’intégration que vous souhaitez connecter, puis choisissez **Créer**.

   ![Créer une connexion à un compte d’intégration](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  

1. Pour le **corps** et l’**en-tête**, sélectionnez ces valeurs à partir des sorties de déclencheur ou d’action précédentes.

   Par exemple, supposons que votre application logique reçoive des messages via un déclencheur de requête. Vous pouvez sélectionner les sorties de ce déclencheur.

   ![Sélectionnez le corps et les en-têtes à partir des sorties de requête.](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png) 

## <a name="sample"></a>Exemple

Pour déployer une application logique totalement opérationnelle dans le cadre d’un exemple de scénario AS2, consultez le [scénario et le modèle d’application logique AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations techniques, notamment sur les déclencheurs, les actions et les limites, comme décrit dans le fichier OpenAPI (anciennement Swagger) du connecteur, consultez la [page de référence du connecteur](/connectors/as2/).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
