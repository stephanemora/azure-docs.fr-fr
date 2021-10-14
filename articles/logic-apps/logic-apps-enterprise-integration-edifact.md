---
title: Échanger des messages EDIFACT dans les flux de travail B2B
description: Échanger des messages EDIFACT entre partenaires en créant des workflows avec Azure Logic Apps et Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/29/2021
ms.openlocfilehash: 19fea6a0405d1fcc4cfbc9b1aa7647c52b4459b6
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401472"
---
# <a name="exchange-edifact-messages-using-workflows-in-azure-logic-apps"></a>Échanger des messages EDIFACT à l’aide de flux de travail dans Azure Logic Apps

Pour envoyer et recevoir des messages EDIFACT dans les flux de travail que vous créez avec Azure Logic Apps, utilisez le connecteur **EDIFACT**, qui fournit des déclencheurs et des actions qui prennent en charge et gèrent la communication EDIFACT.

Cet article explique comment ajouter des actions de codage et de décodage EDIFACT à un flux de travail d’application logique existant. Bien que vous puissiez utiliser n’importe quel déclencheur pour démarrer votre flux de travail, les exemples utilisent le déclencheur de [Requête](../connectors/connectors-native-reqres.md). Pour plus d’informations sur les déclencheurs, les actions et les limites de la version d’origine du connecteur **EDIFACT**, consultez la [page de référence du connecteur](/connectors/edifact/) comme indiqué par le fichier Swagger du connecteur.

![Capture d’écran montrant l’opération « Décoder le message EDIFACT » avec les propriétés de décodage du message.](./media/logic-apps-enterprise-integration-edifact/overview-edifact-message-consumption.png)

## <a name="edifact-encoding-and-decoding"></a>Encodage et décodage EDIFACT

Les sections suivantes décrivent les tâches que vous pouvez effectuer à l’aide des actions d’encodage et de décodage EDIFACT.

### <a name="encode-to-edifact-message-action"></a>Encodage en message EDIFACT

* Résoudre le contrat en mettant en correspondance les identificateurs et les qualificateurs de l’expéditeur et du récepteur.

* Sérialiser l’échange de données informatisé (EDI) qui convertit les messages codés en XML en documents informatisés EDI au sein de l’échange.

* Appliquer les segments d'en-tête et de code de fin de document informatisé.

* Générer un numéro de contrôle d’échange, un numéro de contrôle de groupe et un numéro de contrôle de document informatisé pour chaque échange sortant.

* Remplacer les séparateurs dans les données de charge utile.

* Valider les propriétés EDI et spécifiques au partenaire, telles que le schéma pour les éléments de données du document informatisé par rapport au schéma de message, les éléments de données du document informatisé et la validation étendue sur les éléments de données du jeu de transactions.

* Générer un document XML pour chaque document informatisé.

* Demander un accusé de réception technique, un accusé de réception fonctionnel ou les deux, s’ils sont configurés.

  * En tant qu’accusé de réception technique, le message CONTRL indique la réception d’un échange.

  * En tant qu’accusé de réception fonctionnel, le message CONTRL indique l’acceptation ou le rejet du message, du groupe ou de l’échange reçu, avec une liste des erreurs ou des fonctionnalités non prises en charge.

### <a name="decode-edifact-message-action"></a>Action Decode EDIFACT Message

* Valider l’enveloppe par rapport à l’accord de partenariat commercial.

* Résoudre le contrat en mettant en correspondance les identificateurs et les qualificateurs de l’expéditeur et du récepteur.

* Fractionner un échange en plusieurs transactions lorsque l’échange a plusieurs transactions basées sur les **Paramètres de réception** de l’accord.

* désassemblage de l'échange ;

* Valider les propriétés EDI (Electronic Data Interchange) et spécifiques au partenaire, telles que la structure de l’enveloppe d’échange, le schéma d’enveloppe par rapport au schéma de contrôle, le schéma pour les éléments de données du document informatisé par rapport au schéma de message et la validation étendue sur les éléments de données du document informatisé.

* Vérifier que les numéros de contrôle de l’échange, du groupe et du document informatisé ne sont pas des doublons, si configuré, par exemple :

  * Vérifie le numéro de contrôle de l'échange par rapport aux échanges reçus précédemment.

  * Vérifier le numéro de contrôle du groupe par rapport aux autres numéros de contrôle de groupe dans l’échange.

  * Vérifier le numéro de contrôle du document informatisé par rapport aux autres numéros de contrôle de document informatisé dans ce groupe.

* Fractionner l’échange en documents informatisés ou conserver l’échange entier, par exemple :

  * Fractionner l’échange en documents informatisés - suspendre les documents informatisés en cas d’erreur.

    L’action de décodage scinde l’échange en documents informatisés et analyse chaque document informatisé. L’action ne génère que les documents informatisés qui échouent à la validation `badMessages` et produit les documents informatisés restants en tant que `goodMessages`.

  * Fractionner l’échange en documents informatisés - suspendre l’échange en cas d’erreur.

    L’action de décodage scinde l’échange en documents informatisés et analyse chaque document informatisé. Si la validation d’un ou de plusieurs documents informatisés de l’échange échoue, l’action génère tous les documents informatisés dans cet échange en tant que `badMessages`.

  * Préserver l’échange - suspendre les documents informatisés en cas d’erreur.

    L’action de décodage conserve l’échange et traite la totalité de l’échange traité par lot. L’action ne génère que les documents informatisés qui échouent à la validation `badMessages` et produit les documents informatisés restants en tant que `goodMessages`.

  * Préserver l’échange - suspendre l’échange en cas d’erreur.

    L’action de décodage conserve l’échange et traite la totalité de l’échange traité par lot. Si la validation d’un ou de plusieurs documents informatisés de l’échange échoue, l’action génère tous les documents informatisés dans cet échange en tant que `badMessages`.

* Demander un accusé de réception technique, un accusé de réception fonctionnel ou les deux, s’ils sont configurés.

  * Un accusé de réception technique ou l’ACK CONTRL qui renvoie les résultats d’une vérification syntaxique de tout l’échange reçu.

  * Un accusé de réception fonctionnel qui accuse réception de l’acceptation ou du rejet pour l’échange ou le groupe reçu.

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations techniques sur cette version, consultez le connecteur **EDIFACT**, passez en revue la [page de référence du connecteur](/connectors/edifact/), qui décrit les déclencheurs, les actions et les limites comme indiqué par le fichier Swagger du connecteur. Passez également en revue les [limites du protocole B2B pour les tailles de message](logic-apps-limits-and-config.md#b2b-protocol-limits) pour les [flux de travail qui s’exécutent dans les Azure Logic Apps mutualisées, les Azure Logic Apps à locataire unique ou l’environnement de service d’intégration (ISE)](logic-apps-overview.md#resource-environment-differences). À titre d’un exemple, dans un [environnement de service d’intégration (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md), la version ISE de ce connecteur applique les [limites de messages B2B de l’ISE](logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource de compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) dans laquelle vous définissez et stockez les artefacts, comme les parties, les contrats, les certificats, etc., à utiliser dans vos flux de travail d’intégration d’entreprise et B2B. Cette ressource doit remplir les conditions suivantes :

  * Associé au même abonnement Azure que votre ressource d’application logique.

  * Existe dans le même emplacement ou la même région Azure que votre ressource d’application logique.

  * Lorsque vous utilisez le type de ressource [**Application logique (consommation)**](logic-apps-overview.md#resource-environment-differences) et les opérations **EDIFACT**, votre ressource d’application logique a besoin d’un lien vers votre compte d’intégration. Toutefois, vous avez toujours besoin de ce compte pour stocker les artefacts, tels que les partenaires, les contrats et les certificats, en plus d’utiliser les opérations EDIFACT, [X12](logic-apps-enterprise-integration-x12.md) ou [AS2](logic-apps-enterprise-integration-as2.md). Votre compte d’intégration doit toujours répondre à d’autres exigences, comme l’utilisation du même abonnement Azure et la présence dans le même emplacement que votre ressource d’application logique.

  * Lorsque vous utilisez le type de ressource [**Application logique (standard)**](logic-apps-overview.md#resource-environment-differences) et les opérations **EDIFACT**, votre flux de travail nécessite une connexion à votre compte d’intégration que vous créez directement depuis votre flux de travail lorsque vous ajoutez l’opération AS2.

* Au moins deux [parties](logic-apps-enterprise-integration-partners.md) dans votre compte d’intégration. Les définitions des deux partenaires doivent utiliser le même qualificateur d’*identité d’entreprise*, qui est **ZZZ - Défini mutuellement**, pour ce scénario.

* Un [contrat EDIFACT](logic-apps-enterprise-integration-agreements.md) dans votre compte d’intégration entre les parties qui participent à votre flux de travail. Chaque contrat requiert un partenaire hôte et un partenaire invité. Le contenu des messages entre vous et l’autre partenaire doit correspondre au type de contrat.

  > [!IMPORTANT]
  > Le connecteur EDIFACT prend en charge uniquement les caractères UTF-8. Si votre sortie contient des caractères inattendus, vérifiez que vos messages EDIFACT utilisent le jeu de caractères UTF-8.

* La ressource d’application logique et le flux de travail dans lesquels vous souhaitez utiliser les opérations EDIFACT.

  Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](logic-apps-overview.md) et [Démarrage rapide : Créer votre première application logique](quickstart-create-first-logic-app-workflow.md).

<a name="encode"></a>

## <a name="encode-edifact-messages"></a>Encoder des messages EDIFACT

### <a name="consumption"></a>[Consommation](#tab/consumption)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre ressource d’application logique et votre flux de travail dans le concepteur.

1. Dans le concepteur, sous le déclencheur auquel/l’action à laquelle vous souhaitez ajouter l’action EDIFACT, sélectionnez **Nouvelle étape**.

1. Sous la zone de recherche **Choisir une opération**, sélectionnez **Toutes**. Dans la zone de recherche, entrez `edifact encode`. Pour cet exemple, sélectionnez l’action **Encoder en message EDIFACT par nom de contrat**.

   ![Capture d’écran montrant le Portail Azure, le concepteur de flux de travail et l’action « Coder en message EDIFACT par nom d’accord » sélectionnée.](./media/logic-apps-enterprise-integration-edifact/select-encode-edifact-message-consumption.png)

   > [!NOTE]
   > Vous pouvez choisir d’utiliser à la place l’action **Coder en message EDIFACT par identités**, mais vous devez fournir des valeurs différentes ultérieurement, telles que l’**identificateur de l'expéditeur** et l'**identificateur du récepteur** spécifiés par votre accord EDIFACT. Vous devez également spécifier le **message XML à coder**, qui peut être la sortie du déclencheur ou une action précédente.

1. Lorsque vous êtes invité à créer une connexion à votre compte d’intégration, fournissez les informations suivantes :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom de connexion** | Oui | Un nom pour la connexion |
   | **Compte d’intégration** | Oui | Dans la liste de comptes d’intégration disponibles, sélectionnez un compte à utiliser. |
   ||||

   Par exemple :

   ![Capture d’écran montrant le volet de connexion « Coder en message EDIFACT par nom d’accord ».](./media/logic-apps-enterprise-integration-edifact/create-edifact-encode-connection-consumption.png)

1. Sélectionnez **Créer** lorsque vous avez terminé.

1. Une fois l’opération EDIFACT affichée dans le concepteur, renseignez des informations pour les propriétés suivantes spécifiques à cette opération :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom de l’accord EDIFACT** | Oui | L'accord EDIFACT à utiliser. |
   | **Message XML à coder** | Oui | Identificateur d’entreprise de l’expéditeur du message tel que spécifié par votre contrat EDIFACT |
   | Autres paramètres | Non | Cette opération comprend les autres paramètres suivants : <p>- **Séparateur d'éléments de données** <br>- **Indicateur de version** <br>- **Séparateur de composants** <br>- **Séparateur de répétition** <br>- **Terminateur de segment** <br>- **Suffixe du terminateur de segment** <br>- **Séparateur décimal** <p>Pour plus d’informations, consultez [Paramètres des messages EDIFACT](logic-apps-enterprise-integration-edifact-message-settings.md). |
   ||||

   Par exemple, la charge utile du message XML peut être la sortie de contenu du **Corps** du déclencheur Requête :

   ![Capture d’écran montrant l’opération « Coder en message EDIFACT par nom de contrat » avec les propriétés d’encodage de message.](./media/logic-apps-enterprise-integration-edifact/encode-edifact-message-agreement-consumption.png)

### <a name="standard"></a>[Standard](#tab/standard)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre ressource d’application logique et votre flux de travail dans le concepteur.

1. Dans le concepteur, sous le déclencheur auquel/l’action à laquelle vous souhaitez ajouter l’action EDIFACT, sélectionnez **Insérer une nouvelle étape** (signe plus), puis sélectionnez **Ajouter une action**.

1. Sous la zone de recherche **Choisir une opération**, sélectionnez **Azure**. Dans la zone de recherche, entrez `edifact encode`. Sélectionnez l’action **Encoder en message EDIFACT par nom de contrat**.

   ![Capture d’écran montrant le Portail Azure, le concepteur de flux de travail et l’opération « Coder en message EDIFACT par nom d’accord » sélectionnée.](./media/logic-apps-enterprise-integration-edifact/select-encode-edifact-message-standard.png)

   > [!NOTE]
   > Vous pouvez choisir d’utiliser à la place l’action **Coder en message EDIFACT par identités**, mais vous devez fournir des valeurs différentes ultérieurement, telles que l’**identificateur de l'expéditeur** et l'**identificateur du récepteur** spécifiés par votre accord EDIFACT. Vous devez également spécifier le **message XML à coder**, qui peut être la sortie du déclencheur ou une action précédente.

1. Lorsque vous êtes invité à créer une connexion à votre compte d’intégration, fournissez les informations suivantes :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom de connexion** | Oui | Un nom pour la connexion |
   | **Compte d’intégration** | Oui | Dans la liste de comptes d’intégration disponibles, sélectionnez un compte à utiliser. |
   ||||

   Par exemple :

   ![Capture d’écran montrant le volet de connexion « Coder en message EDIFACT par nom de paramètre ».](./media/logic-apps-enterprise-integration-edifact/create-edifact-encode-connection-standard.png)

1. Sélectionnez **Créer** lorsque vous avez terminé.

1. Une fois le volet d’informations EDIFACT affiché dans le concepteur, renseignez des informations pour les propriétés suivantes :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom de l’accord EDIFACT** | Oui | L'accord EDIFACT à utiliser. |
   | **Message XML à coder** | Oui | Identificateur d’entreprise de l’expéditeur du message tel que spécifié par votre contrat EDIFACT |
   | Autres paramètres | Non | Cette opération comprend les autres paramètres suivants : <p>- **Séparateur d'éléments de données** <br>- **Indicateur de version** <br>- **Séparateur de composants** <br>- **Séparateur de répétition** <br>- **Terminateur de segment** <br>- **Suffixe du terminateur de segment** <br>- **Séparateur décimal** <p>Pour plus d’informations, consultez [Paramètres des messages EDIFACT](logic-apps-enterprise-integration-edifact-message-settings.md). |
   ||||

   Par exemple, la charge utile du message est la sortie de contenu du **Corps** du déclencheur Requête :

   ![Capture d’écran montrant l’opération « Coder en message EDIFACT par nom de paramètre » avec les propriétés d’encodage de message.](./media/logic-apps-enterprise-integration-edifact/encode-edifact-message-agreement-standard.png)

---

<a name="decode"></a>

## <a name="decode-edifact-messages"></a>Messages Decode EDIFACT

### <a name="consumption"></a>[Consommation](#tab/consumption)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre ressource d’application logique et votre flux de travail dans le concepteur.

1. Dans le concepteur, sous le déclencheur auquel/l’action à laquelle vous souhaitez ajouter l’action EDIFACT, sélectionnez **Nouvelle étape**.

1. Sous la zone de recherche **Choisir une opération**, sélectionnez **Toutes**. Dans la zone de recherche, entrez `edifact encode`. Sélectionnez l’action nommée **Décoder un message EDIFACT**.

1. Lorsque vous êtes invité à créer une connexion à votre compte d’intégration, fournissez les informations suivantes :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom de connexion** | Oui | Un nom pour la connexion |
   | **Compte d’intégration** | Oui | Dans la liste de comptes d’intégration disponibles, sélectionnez un compte à utiliser. |
   ||||

   Par exemple :

   ![Capture d’écran montrant le volet de connexion « Décoder un message EDIFACT ».](./media/logic-apps-enterprise-integration-edifact/create-edifact-decode-connection-consumption.png)

1. Sélectionnez **Créer** lorsque vous avez terminé.

1. Une fois l’opération EDIFACT affichée dans le concepteur, renseignez des informations pour les propriétés suivantes spécifiques à cette opération :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Message de fichier plat EDIFACT à décoder** | Oui | Le message de fichier plat EDIFACT à décoder. |
   | Autres paramètres | Non | Cette opération comprend les autres paramètres suivants : <p>- **Séparateur de composants** <br>- **Séparateur d'éléments de données** <br>- **Indicateur de version** <br>- **Séparateur de répétition** <br>- **Terminateur de segment** <br>- **Suffixe du terminateur de segment** <br>- **Séparateur décimal** <br>- **Jeu de caractères de charge utile** <br>- **Suffixe du terminateur de segment** <br>- **Conserver l'échange** <br>- **Suspendre l'échange en cas d'erreur** <p>Pour plus d’informations, consultez [Paramètres des messages EDIFACT](logic-apps-enterprise-integration-edifact-message-settings.md). |
   ||||

   Par exemple, la charge utile du message XML à décoder peut être la sortie de contenu du **Corps** du déclencheur Requête :

   ![Capture d’écran montrant l’opération « Décoder le message EDIFACT » avec les propriétés de décodage du message.](./media/logic-apps-enterprise-integration-edifact/decode-edifact-message-consumption.png)

### <a name="standard"></a>[Standard](#tab/standard)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre ressource d’application logique et votre flux de travail dans le concepteur.

1. Dans le concepteur, sous le déclencheur auquel/l’action à laquelle vous souhaitez ajouter l’action EDIFACT, sélectionnez **Insérer une nouvelle étape** (signe plus), puis sélectionnez **Ajouter une action**.

1. Sous la zone de recherche **Choisir une opération**, sélectionnez **Azure**. Dans la zone de recherche, entrez `edifact encode`. Sélectionnez l’action nommée **Décoder un message EDIFACT**.

   ![Capture d’écran montrant le portail Azure, le concepteur de flux de travail et l’opération « Décoder un message EDIFACT » sélectionnée.](./media/logic-apps-enterprise-integration-edifact/select-decode-edifact-message-standard.png)

1. Lorsque vous êtes invité à créer une connexion à votre compte d’intégration, fournissez les informations suivantes :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom de connexion** | Oui | Un nom pour la connexion |
   | **Compte d’intégration** | Oui | Dans la liste de comptes d’intégration disponibles, sélectionnez un compte à utiliser. |
   ||||

   Par exemple :

   ![Capture d’écran montrant le volet de connexion « Décoder un message EDIFACT ».](./media/logic-apps-enterprise-integration-edifact/create-edifact-decode-connection-standard.png)

1. Sélectionnez **Créer** lorsque vous avez terminé.

1. Une fois le volet d’informations EDIFACT affiché dans le concepteur, renseignez des informations pour les propriétés suivantes :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom de l’accord EDIFACT** | Oui | L'accord EDIFACT à utiliser. |
   | **Message XML à coder** | Oui | Identificateur d’entreprise de l’expéditeur du message tel que spécifié par votre contrat EDIFACT |
   | Autres paramètres | Non | Cette opération comprend les autres paramètres suivants : <p>- **Séparateur d'éléments de données** <br>- **Indicateur de version** <br>- **Séparateur de composants** <br>- **Séparateur de répétition** <br>- **Terminateur de segment** <br>- **Suffixe du terminateur de segment** <br>- **Séparateur décimal** <p>Pour plus d’informations, consultez [Paramètres des messages EDIFACT](logic-apps-enterprise-integration-edifact-message-settings.md). |
   ||||

   Par exemple, la charge utile du message est la sortie de contenu du **Corps** du déclencheur Requête :

   ![Capture d’écran montrant l’opération « Décoder le message EDIFACT » avec les propriétés de décodage du message.](./media/logic-apps-enterprise-integration-edifact/decode-edifact-message-standard.png)

---

## <a name="handle-unh25-segments-in-edifact-documents"></a>Gérer les segments UNH2.5 dans les documents EDIFACT

Dans un document EDIFACT, le [segment UNH2.5](logic-apps-enterprise-integration-edifact-message-settings.md#receive-settings-schemas) est utilisé pour la recherche de schéma. Ainsi, dans cet exemple de message EDIFACT, le champ UNH est `EAN008` :

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Pour gérer un document EDIFACT ou traiter un message EDIFACT qui a un segment UN2.5, procédez comme suit :

1. Mettez à jour ou déployez un schéma qui a le nom de nœud racine UNH2.5.

   Ainsi, imaginons que le nom de la racine du schéma pour l’exemple de champ UNH est `EFACT_D03B_ORDERS_EAN008`. Pour chaque `D03B_ORDERS` qui a un autre segment UNH2.5, vous devez déployer un schéma individuel.

1. Dans le [portail Azure](https://portal.azure.com), ajoutez le schéma à votre ressource de compte d’intégration ou à la ressource d’application logique, selon que vous utilisez le type de ressource **Application logique (consommation)** ou **Application logique (standard)** , respectivement.

1. Que vous utilisiez l’action de décodage ou d’encodage EDIFACT, chargez votre schéma et configurez les paramètres de schéma dans les sections **Paramètres de réception** ou **Paramètres d’envoi** de l’accord EDIFACT, respectivement.

1. Pour modifier votre accord EDIFACT, dans le volet **Accords**, sélectionnez votre accord. Dans la barre d’outils du volet **Contrats**, sélectionnez **Modifier en tant que JSON**.

   * Dans la section de l’accord `receiveAgreement`, recherchez la section `schemaReferences` et ajoutez la valeur UNH2.5.

     ![Capture d’écran montrant le Portail Azure avec la section « receiveAgreement » de l’accord EDIFACT dans l’éditeur JSON, et la section « schemaReferences » est mise en surbrillance.](./media/logic-apps-enterprise-integration-edifact/agreement-receive-schema-references.png)

   * Dans la section `sendAgreement` de l’accord, recherchez la section `schemaReferences` et ajoutez la valeur UNH2.5.

     ![Capture d’écran montrant le Portail Azure avec la section « sendAgreement » de l’accord EDIFACT dans l’éditeur JSON, et la section « schemaReferences » est mise en surbrillance.](./media/logic-apps-enterprise-integration-edifact/agreement-send-schema-references.png)

## <a name="next-steps"></a>Étapes suivantes

* [Paramètres des messages EDIFACT](logic-apps-enterprise-integration-edifact-message-settings.md)
