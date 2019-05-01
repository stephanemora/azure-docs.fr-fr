---
title: Messages AS2 pour intégration d’entreprise B2B - Azure Logic Apps
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
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64729398"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Échanger des messages AS2 dans le cadre d’une intégration d’entreprise B2B dans Azure Logic Apps avec Enterprise Integration Pack

Pour travailler avec des messages AS2 dans Azure Logic Apps, vous pouvez utiliser le connecteur AS2, qui fournit des déclencheurs et actions pour la gestion de la communication de AS2. Par exemple, pour établir la sécurité et fiabilité lors de la transmission des messages, vous pouvez utiliser ces actions :

* [**Coder en message AS2** action](#encode) pour fournir le chiffrement, digital signature et accusés de réception par Message Disposition Notifications (MDN), ce qui vous aider à prendre en charge la non-répudiation. Par exemple, cette action s’applique les en-têtes AS2/HTTP et effectue ces tâches lorsque configuré :

  * Signe les messages sortants.
  * Chiffre les messages sortants.
  * Compresse le message.
  * Transmet le nom de fichier dans l’en-tête MIME.

* [**Décoder le message AS2** action](#decode) pour fournir le décryptage, la signature numérique et les accusés de réception par Message Disposition Notifications (MDN). Par exemple, cette action effectue ces tâches : 

  * Traite les en-têtes AS2/HTTP.
  * Rapproche un MDN reçu avec les messages sortants d’origine.
  * Met à jour et met en corrélation les enregistrements dans la base de données de non-répudiation.
  * Écrit les enregistrements pour le rapport d’état AS2.
  * Contenu de charge utile de sorties que codée en base64.
  * Détermine si le MDN est requis. Basé sur AS2 accord, détermine si le MDN doit être synchrone ou asynchrone.
  * Génère un MDN synchrone ou asynchrone en fonction de l’accord AS2.
  * Définit les jetons de corrélation et les propriétés sur les MDN.

  Cette action effectue également les tâches lorsque configuré :

  * Vérifie la signature.
  * Déchiffre les messages.
  * Décompresse le message. 
  * Vérifiez et interdire les doublons d’ID de message.

Cet article explique comment ajouter le codage AS2 et décodage d’actions à une application logique existante.

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* L’application logique dans lequel vous voulez utiliser le connecteur AS2 et un déclencheur qui démarre le flux de travail de votre application logique. Le connecteur AS2 fournit des actions uniquement, pas de déclencheurs. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md) et [Démarrage rapide : Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Un [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) qui a associé à votre abonnement Azure et lié à l’application logique dans lequel vous envisagez d’utiliser le connecteur AS2. À la fois votre application et l’intégration compte logique doit exister dans le même emplacement ou la région Azure.

* Au moins deux [des partenaires commerciaux](../logic-apps/logic-apps-enterprise-integration-partners.md) que vous avez déjà défini dans votre compte d’intégration à l’aide du qualificateur d’identité AS2.

* Avant de pouvoir utiliser le connecteur AS2, vous devez créer un AS2 [accord](../logic-apps/logic-apps-enterprise-integration-agreements.md) entre vos partenaires commerciaux et le magasin de cet accord dans votre compte d’intégration.

* Si vous utilisez [Azure Key Vault](../key-vault/key-vault-overview.md) gestion des certificats, vérifiez que vos clés de coffre autorisent la **Encrypt** et **déchiffrer** operations. Sinon, l’encodage et décodage des actions échouent.

  Dans le portail Azure, accédez à votre coffre de clés, d’afficher votre clé de coffre **opérations autorisées**et vérifiez que le **Encrypt** et **déchiffrer** opérations sont sélectionnées.

  ![Vérifiez les opérations de clé de coffre](media/logic-apps-enterprise-integration-as2/vault-key-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Encoder des messages AS2

1. Si vous n’avez pas déjà fait, dans le [Azure portal](https://portal.azure.com), ouvrez votre application logique dans le Concepteur d’application logique.

1. Dans le concepteur, ajoutez une nouvelle action à votre application logique. 

1. Sous **choisir une action** et la recherche, sélectionnez **tous les**. Dans la zone de recherche, entrez « coder as2 », puis sélectionnez cette action : **Coder en message AS2**.

   ![Sélectionnez « Encode AS2 message »](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Si vous n’avez une connexion existante à votre compte d’intégration, vous êtes invité à créer cette connexion maintenant. Nommez votre connexion, sélectionnez le compte d’intégration que vous souhaitez vous connecter, puis choisissez **créer**.

   ![créer la connexion au compte d’intégration](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  
 
1. Désormais fournir des informations pour ces propriétés :

   | Propriété | Description |
   |----------|-------------|
   | **AS2-From** | L’identificateur de l’expéditeur du message comme spécifié par votre contrat AS2 |
   | **AS2-To** | L’identificateur pour le récepteur du message comme spécifié par votre contrat AS2 |
   | **body** | Charge utile de message |
   |||

   Par exemple : 

   ![Propriétés de codage de message](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Décoder des messages AS2

1. Si vous n’avez pas déjà fait, dans le [Azure portal](https://portal.azure.com), ouvrez votre application logique dans le Concepteur d’application logique.

1. Dans le concepteur, ajoutez une nouvelle action à votre application logique. 

1. Sous **choisir une action** et la recherche, sélectionnez **tous les**. Dans la zone de recherche, entrez « décoder as2 », puis sélectionnez cette action : **Décoder le message AS2**

   ![Sélectionnez « Decode AS2 message »](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Si vous n’avez une connexion existante à votre compte d’intégration, vous êtes invité à créer cette connexion maintenant. Nommez votre connexion, sélectionnez le compte d’intégration que vous souhaitez vous connecter, puis choisissez **créer**.

   ![créer la connexion au compte d’intégration](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  

1. Pour **corps** et **en-têtes**, sélectionnez ces valeurs à partir des sorties de déclencheur ou l’action précédentes.

   Par exemple, supposons que votre application logique reçoit des messages via un déclencheur de requête. Vous pouvez sélectionner les sorties à partir de ce déclencheur.

   ![Sélectionnez le corps et les en-têtes à partir des sorties de requête.](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png) 

## <a name="sample"></a>Exemple

Pour déployer une application logique totalement opérationnelle dans le cadre d’un exemple de scénario AS2, consultez le [scénario et le modèle d’application logique AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations techniques, telles que des déclencheurs, actions et limites, comme décrit par OpenAPI du connecteur (anciennement Swagger), consultez le [page de référence du connecteur](/connectors/as2/).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
