---
title: Accusés de réception MDN AS2
description: Apprenez-en davantage sur les accusés de réception de notification d’état du message (MDN) pour les messages AS2 dans Azure Logic Apps.
services: logic-apps
ms.suite: integration
author: S-Jindal
ms.author: shivamjindal
ms.reviewer: estfan, azla
ms.topic: reference
ms.date: 08/12/2021
ms.openlocfilehash: 510be5ca1343f115bcdf13c2d67b598b4d410564
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563366"
---
# <a name="mdn-acknowledgments-for-as2-messages-in-azure-logic-apps"></a>Accusés de réception MDN des messages AS2 dans Azure Logic Apps

Dans Azure Logic Apps, vous pouvez créer des workflows qui gèrent les messages AS2 pour la communication EDI (échange de données informatisé) lorsque vous utilisez des opérations **AS2**. Dans la messagerie EDI, les accusés de réception fournissent l’état du traitement d’un échange EDI. En cas de réception d’un échange, l’action [**Décodage AS2**](logic-apps-enterprise-integration-as2.md#decode) peut renvoyer une notification d’état du message (MDN) ou un accusé de réception à l’expéditeur. Un MDN vérifie les éléments suivants :

* Le partenaire destinataire a reçu le message d’origine.

  Le partenaire expéditeur compare le `MessageID` du message envoyé à l’origine avec le champ `original-message-id` que le destinataire inclut dans le MDN.

* Le partenaire destinataire a vérifié l’intégrité des données échangées.

  Une vérification de l’intégrité du message (MIC) ou un condensat MIC est calculé à partir de la charge utile dans le message initialement envoyé. Le partenaire expéditeur compare ce MIC à celui que le destinataire a calculé à partir de la charge utile dans le message reçu et inclus dans le champ `Received-content-MIC` dans le MDN, s’il est signé.

  > [!NOTE]
  > Un MDN peut être signé, mais pas chiffré ou compressé.

* Non répudiation de l’accusé

  Le partenaire expéditeur compare le MDN signé à la clé publique du partenaire destinataire et vérifie que la valeur MIC renvoyée dans le MDN est identique à celle de la charge du message d’origine conservé dans la base de données de non-répudiation.

> [!NOTE]
> Si vous activez l’envoi d’un MDN en réponse, votre application logique tente de renvoyer un MDN pour signaler l’état du traitement des messages AS2, même si une erreur se produit pendant le processus. La transmission AS2 n’est finalisée que lorsque l’expéditeur reçoit et vérifie le MDN.
> Un MDN synchrone sert de réponse HTTP (par exemple, un état `200 OK`).

Cette rubrique fournit une brève présentation de l’accusé de réception MDN AS2, notamment les propriétés utilisées pour générer l’accusé de réception, les en-têtes MDN à utiliser, et la vérification de l’intégrité du message (MIC). Pour plus d’informations à ce sujet, consultez la documentation suivante :

* [Échangez des messages AS2 dans le cadre d’une intégration d’entreprise B2B dans Azure Logic Apps](logic-apps-enterprise-integration-as2.md)
* [Paramètres des messages AS2](logic-apps-enterprise-integration-as2-message-settings.md)
* [Qu’est-ce qu’Azure Logic Apps ?](logic-apps-overview.md)

## <a name="mdn-generation"></a>Génération du MDN

L’action Décodage AS2 génère un MDN sur la base des propriétés de l’accord AS2 d’un partenaire commercial lorsque l’option **Envoyer un MDN** est sélectionnée dans les **paramètres de réception** de l’accord. Dans le cas présent, la propriété **AS2-From** dans l’en-tête du message sert à générer le MDN, mais les autres propriétés et leurs valeurs sont extraites des paramètres de l’accord AS2 du partenaire.

Par défaut, les en-têtes des message AS2 entrants sont utilisés pour la validation et la génération du MDN. Pour utiliser les paramètres de validation et de MDN de l’accord à la place, dans les **paramètres de réception** de l’accord, sélectionnez **Remplacer les propriétés du message**. Dans le cas contraire, si cette option n’est pas sélectionnée ou si un accord n’est pas disponible, l’action Décodage AS2 utilise les en-têtes des messages entrants à la place.

## <a name="mdn-headers"></a>En-têtes MDN

Pour corréler un MDN au message AS2 comme réponse, l’en-tête `AS2-From`, l’en-tête `AS2-To` et la propriété de contexte `MessageID` sont utilisés. Dans le MDN, l’en-tête `Original-Message-ID` provient de l’en-tête `Message-ID` dans le message AS2 pour lequel le MDN est la réponse. Les MDN contiennent les en-têtes suivants :

| headers | Description |
|---------|-------------|
| HTTP et AS2 | Pour plus d’informations, consultez [Paramètres des messages AS2](logic-apps-enterprise-integration-as2-message-settings.md).
| Couche de transfert | Cet en-tête inclut l’en-tête `Content-Type` qui inclut le message signé à parties multiples, l’algorithme du MIC, le protocole de formatage de la signature et les sous-en-têtes de limite de parties multiples les plus externes. |
| Première partie | La première partie d’un message signé à parties multiples est le MDN incorporé. Elle est lisible par l’homme. |
| Deuxième partie | La deuxième partie d’un message signé à parties multiples contient la signature numérique, une référence au message d’origine, le type de disposition et l’état, ainsi que la valeur du MIC. Elle est lisible par l’ordinateur. |
|||

## <a name="mic-digest"></a>Condensat MIC

Le condensat MIC ou le MIC vérifie qu’un MDN est en corrélation avec la charge utile dans le message initialement envoyé. Ce MIC est inclus dans la deuxième partie du message MDN signé à parties multiples dans le champ d’extension `Received-Content-MIC`.

Il est encodé en base64 et est déterminé à partir de la propriété **Algorithme MIC**, activée lorsque les propriétés **Envoyer un MDN** et **Envoyer un MDN signé** sont sélectionnés sur la page **Paramètres de réception** de l’accord AS2. Pour la génération du MIC, vous avez le choix parmi les algorithmes de hachage pris en charge suivants :

* SHA1
* MD5
* SHA2-256
* SHA2-384
* SHA2-512

Par exemple, la capture d’écran suivante montre les propriétés de MDN sur la page **Paramètres de réception** de l’accord AS2 :

![Portail Azure montrant les « paramètres de réception » de l’accord AS2 et les paramètres de l’accusé de réception MDN.](./media/logic-apps-enterprise-integration-as2-mdn-acknowledgment/mdn-ack-settings.png)

## <a name="next-steps"></a>Étapes suivantes

* [Paramètres des messages AS2](logic-apps-enterprise-integration-as2-message-settings.md)
