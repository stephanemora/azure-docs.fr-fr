---
title: Échanger des messages AS2 dans les flux de travail B2B
description: Échangez des messages AS2 entre les transactions en créant des flux de travail à l’aide d’Azure Logic Apps et Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: article
ms.date: 09/27/2021
ms.openlocfilehash: 8b42987055ca2e2b6533ae2f9d45b4bb62fbe016
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129082352"
---
# <a name="exchange-as2-messages-using-workflows-in-azure-logic-apps"></a>Échangez des messages AS2 à l’aide de flux de travail dans Azure Logic Apps

Pour envoyer et recevoir des messages AS2 dans les flux de travail que vous créez avec Azure Logic Apps, utilisez le connecteur **AS2**, qui fournit des déclencheurs et des actions qui prennent en charge et gèrent la communication AS2 (version 1.2).

* Si vous utilisez le type de ressource **Application logique (consommation)** et que vous n’avez pas besoin de fonctionnalités de suivi, utilisez le connecteur **AS2 (v2)** , plutôt que le connecteur **AS2** d’origine, qui est déconseillé.

  À l’exception du suivi, le connecteur **AS2 (v2)** offre de meilleures performances, les mêmes capacités que la version d’origine. Il est natif du runtime Azure Logic Apps et apporte d’importantes améliorations des performances en ce qui concerne le débit, la taille et la latence des messages. De plus, le connecteur v2 ne nécessite pas la création d’une connexion à votre compte d’intégration. Comme indiqué dans les prérequis, assurez-vous plutôt de lier votre compte d’intégration à la ressource d’application logique dans laquelle vous envisagez d’utiliser le connecteur.

* Si vous utilisez le type de ressource **Application logique (standard)** , seul le connecteur **AS2** d’origine est actuellement disponible. Pour plus d’informations sur cette version, consultez la [page de référence du connecteur](/connectors/as2/), qui décrit les déclencheurs, les actions et les limites comme indiqué par le fichier Swagger du connecteur.

### <a name="consumption"></a>[Consommation](#tab/consumption)

Les listes suivantes décrivent les actions réalisées par le connecteur **AS2 (v2)** pour l’établissement de la sécurité et de la fiabilité lors de la transmission de messages :

* [**Action** Encodage AS2](#encode) pour fournir les fonctionnalités de chiffrement, de signature numérique et d’accusés de réception par notification de réception du message (MDN), ce qui renforce la non-répudiation. Par exemple, cette action applique des en-têtes AS2/HTTP et effectue les tâches suivantes lorsqu’elles sont configurées :

  * Signature des messages sortants.
  * Chiffrement des messages sortants.
  * Compression du message.
  * Transmission du nom de fichier dans l’en-tête MIME.

* [**Action** Décodage AS2](#decode) pour fournir les fonctionnalités de déchiffrement, de signature numérique et d’accusés de réception par notification de réception du message (MDN). Par exemple, cette action effectue les tâches suivantes :

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

### <a name="standard"></a>[Standard](#tab/standard)

Pour plus d’informations sur les déclencheurs, les actions et les limites de la version d’origine du connecteur **AS2**, consultez la [page de référence du connecteur](/connectors/as2/) comme indiqué par le fichier Swagger du connecteur.

---

Cet article explique comment ajouter des actions de codage et de décodage AS2 à un flux de travail d’application logique existant. Bien que vous puissiez utiliser n’importe quel déclencheur pour démarrer votre flux de travail, les exemples utilisent le déclencheur de [Requête](../connectors/connectors-native-reqres.md).

## <a name="limits"></a>Limites

Pour plus d’informations sur les limites du connecteur AS2 pour les flux de travail qui s’exécutent dans les [Azure Logic Apps mutualisées](logic-apps-overview.md#resource-environment-differences), les Azure Logic Apps à locataire unique ou l’environnement de service d’intégration (ISE)[, passez en revue les ](logic-apps-limits-and-config.md#b2b-protocol-limits)Limites du protocole B2B pour la taille des messages.

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource de compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) dans laquelle vous définissez et stockez les artefacts, comme les parties, les contrats, les certificats, etc., à utiliser dans vos flux de travail d’intégration d’entreprise et B2B. Cette ressource doit remplir les conditions suivantes :

  * Associé au même abonnement Azure que votre ressource d’application logique.

  * Existe dans le même emplacement ou la même région Azure que votre ressource d’application logique.

  * Lorsque vous utilisez le type de ressource [**Application logique (consommation)**](logic-apps-overview.md#resource-environment-differences) et les opérations **AS2 (v2)** , votre ressource d’application logique a besoin d’un lien vers votre compte d’intégration. Toutefois, vous avez toujours besoin de ce compte pour stocker les artefacts, tels que les partenaires, les contrats et les certificats, en plus d’utiliser les opérations AS2, [X12](logic-apps-enterprise-integration-x12.md) ou [EDIFACT](logic-apps-enterprise-integration-edifact.md). Votre compte d’intégration doit encore répondre à d’autres exigences, telles que l’utilisation du même abonnement Azure et le même emplacement que votre ressource d’application logique.

  * Lorsque vous utilisez le type de ressource [**Application logique (standard)**](logic-apps-overview.md#resource-environment-differences) et les opérations de l’**AS2** d’origine, votre flux de travail nécessite une connexion à votre compte d’intégration que vous créez directement depuis votre flux de travail lorsque vous ajoutez l’opération AS2.

* Au moins deux [parties](logic-apps-enterprise-integration-partners.md) dans votre compte d’intégration. Les définitions des deux partenaires doivent utiliser le même qualificateur d’*identité d’entreprise*, qui est **AS2Identity**, pour ce scénario.

* Un [contrat AS2](logic-apps-enterprise-integration-agreements.md) dans votre compte d’intégration entre les parties qui participent à votre flux de travail. Chaque contrat requiert un partenaire hôte et un partenaire invité.

* La ressource d’application logique et le flux de travail dans lesquels vous souhaitez utiliser les opérations AS2.

  > [!NOTE]
  > Le connecteur **AS2 (v2)** fournit uniquement des actions, pas des déclencheurs. Dans cet article, les exemples de ce connecteur utilisent le déclencheur [Requête](../connectors/connectors-native-reqres.md). Le connecteur **AS2** d’origine comprend des déclencheurs et des actions. Pour plus d’informations sur les déclencheurs, les actions et les limites de la version d’origine du connecteur **AS2**, consultez la [page de référence du connecteur](/connectors/as2/) comme indiqué par le fichier Swagger du connecteur.

  Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](logic-apps-overview.md) et [Démarrage rapide : Créer votre première application logique](quickstart-create-first-logic-app-workflow.md).

* Si vous utilisez **Azure Key Vault** pour la gestion des certificats, vérifiez que les clés de coffre autorisent les opérations [Chiffrer](../key-vault/general/overview.md) et **Déchiffrer**. Sinon, le codage et le décodage échouent.

  1. Dans le portail Azure, ouvrez votre coffre de clés. Dans le menu de coffre de clés, sous **Paramètres**, sélectionnez **Clés**.

  1. Dans le volet **Clés**, sélectionnez votre clé. Dans le volet **Versions**, sélectionnez la version de clé que vous utilisez.

  1. Dans le volet **Version de clé**, sous **Opérations autorisées**, confirmez que les opérations **Chiffrer** et **Déchiffrer** sont sélectionnées, par exemple :

     ![Capture d’écran montrant le portail Azure avec les volets Coffre de clés, Clé et Version de clé ouverts, et dont les opérations « Chiffrer » et « Déchiffrer » sont sélectionnées.](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Encoder des messages AS2

### <a name="consumption"></a>[Consommation](#tab/consumption)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre ressource d’application logique et votre flux de travail dans le concepteur.

1. Dans le concepteur, sous le déclencheur auquel/l’action à laquelle vous souhaitez ajouter l’action AS2, sélectionnez **Nouvelle étape**.

1. Sous la zone de recherche **Choisir une opération**, sélectionnez **Toutes**. Dans la zone de recherche, entrez `as2 encode`. Sélectionnez l’action nommée **Encodage AS2**.

   ![Capture d’écran montrant le portail Azure, le concepteur de flux de travail, et l’action « Encodage AS2 » sélectionnée.](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Une fois l’opération AS2 affichée dans le concepteur, renseignez des informations pour les propriétés suivantes :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Message à encoder** | Oui | Charge utile du message |
   | **AS2 à partir de** | Oui | Identificateur d’entreprise de l’expéditeur du message tel que spécifié par votre contrat AS2 |
   | **AS2 vers** | Oui | Identificateur d’entreprise du destinataire du message tel que spécifié par votre contrat AS2 |
   ||||

   Par exemple, la charge utile du message est la sortie de contenu du **Corps** du déclencheur Requête :

   ![Capture d’écran montrant l’action « Encodage AS2 » avec les propriétés d’encodage de message.](./media/logic-apps-enterprise-integration-as2/as2-message-encode-details.png)

   > [!TIP]
   > Si vous rencontrez des problèmes lors de l’envoi de messages signés ou chiffrés, pensez à essayer différents formats de l’algorithme SHA256. Comme la spécification AS2 ne fournit pas d’informations sur les formats SHA256, chaque fournisseur utilise sa propre implémentation ou son propre format.

### <a name="standard"></a>[Standard](#tab/standard)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre ressource d’application logique et votre flux de travail dans le concepteur.

1. Dans le concepteur, sous le déclencheur auquel/l’action à laquelle vous souhaitez ajouter l’action AS2, sélectionnez **Insérer une nouvelle étape** (signe plus), puis sélectionnez **Ajouter une action**.

1. Sous la zone de recherche **Choisir une opération**, sélectionnez **Azure**. Dans la zone de recherche, entrez `as2 encode`. Sélectionnez l’action nommée **Encoder en message AS2**.

   ![Capture d’écran montrant le portail Azure, le concepteur de flux de travail et l’opération « Encoder en message AS2 » sélectionnée.](./media/logic-apps-enterprise-integration-as2/select-encode-as2-message.png)

1. Lorsque vous êtes invité à créer une connexion à votre compte d’intégration, fournissez les informations suivantes :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom de connexion** | Oui | Un nom pour la connexion |
   | **Compte d’intégration** | Oui | Dans la liste de comptes d’intégration disponibles, sélectionnez un compte à utiliser. |
   ||||

   Par exemple :

   ![Capture d’écran montrant le volet de connexion « Encoder en message AS2 ».](./media/logic-apps-enterprise-integration-as2/create-as2-encode-connection-standard.png)

1. Sélectionnez **Créer** lorsque vous avez terminé.

1. Une fois le volet d’informations AS2 affiché dans le concepteur, renseignez des informations pour les propriétés suivantes :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Message à encoder** | Oui | Charge utile du message |
   | **AS2 à partir de** | Oui | Identificateur d’entreprise de l’expéditeur du message tel que spécifié par votre contrat AS2 |
   | **AS2 vers** | Oui | Identificateur d’entreprise du destinataire du message tel que spécifié par votre contrat AS2 |
   ||||

   Par exemple, la charge utile du message est la sortie de contenu du **Corps** du déclencheur Requête :

   ![Capture d’écran montrant l’opération « Encodage AS2 » avec les propriétés d’encodage de message.](./media/logic-apps-enterprise-integration-as2/encode-as2-message-details.png)

   > [!TIP]
   > Si vous rencontrez des problèmes lors de l’envoi de messages signés ou chiffrés, pensez à essayer différents formats de l’algorithme SHA256. Comme la spécification AS2 ne fournit pas d’informations sur les formats SHA256, chaque fournisseur utilise sa propre implémentation ou son propre format.

---

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Décoder des messages AS2

### <a name="consumption"></a>[Consommation](#tab/consumption)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre ressource d’application logique et votre flux de travail dans le concepteur.

1. Dans le concepteur, sous le déclencheur auquel/l’action à laquelle vous souhaitez ajouter l’action AS2, sélectionnez **Nouvelle étape**. Cet exemple utilise le déclencheur [Requête](../connectors/connectors-native-reqres.md).

1. Sous la zone de recherche **Choisir une opération**, sélectionnez **Toutes**. Dans la zone de recherche, entrez `as2 decode`. Sélectionnez l’action nommée **Décodage AS2**.

   ![Capture d’écran montrant le portail Azure, le concepteur de flux de travail, et l’opération « Décodage AS2 » sélectionnée.](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Dans la forme d’opération AS2, sélectionnez les valeurs des propriétés **Message à encoder** et **En-têtes de message** à partir des sorties de déclencheur ou d’action précédentes.

   Dans cet exemple, vous pouvez sélectionner les sorties du déclencheur Requête.

   ![Capture d’écran montrant le portail Azure, le concepteur de flux de travail et l’opération « Décodage AS2 » avec la sortie « Corps » et « En-têtes » sélectionnée à partir du déclencheur Requête.](media/logic-apps-enterprise-integration-as2/as2-message-decode-details.png)

### <a name="standard"></a>[Standard](#tab/standard)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre ressource d’application logique et votre flux de travail dans le concepteur.

1. Dans le concepteur, sous le déclencheur auquel/l’action à laquelle vous souhaitez ajouter l’action AS2, sélectionnez **Insérer une nouvelle étape** (signe plus), puis sélectionnez **Ajouter une action**.

1. Sous la zone de recherche **Choisir une opération**, sélectionnez **Azure**. Dans la zone de recherche, entrez `as2 decode`. Sélectionnez l’action nommée **Décoder un message AS2 message**.

   ![Capture d’écran montrant le portail Azure, le concepteur de flux de travail et l’opération « Décoder un message AS2 » sélectionnée.](./media/logic-apps-enterprise-integration-as2/select-decode-as2-message.png)

1. Lorsque vous êtes invité à créer une connexion à votre compte d’intégration, fournissez les informations suivantes :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom de connexion** | Oui | Un nom pour la connexion |
   | **Compte d’intégration** | Oui | Dans la liste de comptes d’intégration disponibles, sélectionnez un compte à utiliser. |
   ||||

   Par exemple :

   ![Capture d’écran montrant le volet de connexion « Décoder un message AS2 ».](./media/logic-apps-enterprise-integration-as2/create-as2-decode-connection-standard.png)

1. Sélectionnez **Créer** lorsque vous avez terminé.

1. Dans le volet d’informations AS2, sélectionnez les valeurs des propriétés **Message à encoder** et **En-têtes de message** à partir des sorties de déclencheur ou d’action précédentes.

   Dans cet exemple, vous pouvez sélectionner les sorties du déclencheur Requête.

   ![Capture d’écran montrant le portail Azure, le concepteur de flux de travail et l’opération « Décoder un message AS2 » avec la sortie « Corps » et « En-têtes » sélectionnée à partir du déclencheur Requête.](media/logic-apps-enterprise-integration-as2/decode-as2-message-details.png)

---

## <a name="sample"></a>Exemple

Pour déployer une application logique totalement opérationnelle dans le cadre d’un exemple de scénario AS2 (v2), consultez le [scénario et le modèle d’application logique AS2 (v2)](https://azure.microsoft.com/resources/templates/logic-app-as2-send-receive/).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [tous les autres connecteurs pour Azure Logic apps](../connectors/apis-list.md)
