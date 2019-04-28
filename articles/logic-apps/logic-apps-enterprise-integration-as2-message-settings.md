---
title: Paramètres de message AS2 - Azure Logic Apps
description: Guide de référence pour AS2 envoyer et recevoir des paramètres dans Azure Logic Apps avec Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ead92094b9af1dff56ff68e1ff58a3a4cdd9dca5
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769450"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Référence pour les paramètres de message AS2 dans Azure Logic Apps avec Enterprise Integration Pack

Cette référence décrit les propriétés que vous pouvez définir pour spécifier la façon dont un accord AS2 gère les messages envoyés et reçus entre partenaires commerciaux. Configurez ces propriétés selon le contrat conclu avec le partenaire qui échange des messages avec vous.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>Paramètres de réception AS2

![Sélectionnez « Paramètres de réception »](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Propriété | Obligatoire | Description |
|----------|----------|-------------|
| **Remplacer les propriétés du message** | Non  | Remplace les propriétés sur les messages entrants avec vos paramètres de propriété. |
| **Le message doit être signé.** | Non  | Spécifie si tous les messages entrants doivent être signés numériquement. Si vous avez besoin de signature, à partir de la **certificat** , sélectionnez un certificat public existant invité partenaire pour valider la signature dans les messages. Si vous n’avez pas un certificat, en savoir plus sur [l’ajout de certificats](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Message doit être chiffré.** | Non  | Spécifie si tous les messages entrants doivent être chiffrés. Les messages non chiffrés sont rejetés. Si vous avez besoin de chiffrement, à partir de la **certificat** , sélectionnez un existant hôte certificat privé du partenaire pour le déchiffrement des messages entrants. Si vous n’avez pas un certificat, en savoir plus sur [l’ajout de certificats](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Le message doit être compressé.** | Non  | Spécifie si tous les messages entrants doivent être compressées. Les messages non compressés sont rejetés. |
| **Interdire les ID de Message en double** | Non  | Spécifie s’il faut autoriser les messages avec des ID dupliqués. Si vous interdisez ID en double, sélectionnez le nombre de jours entre les vérifications. Vous pouvez également choisir s’il faut interrompre les doublons. |
| **Texte du MDN** | Non  | Spécifie le par défaut message disposition notification (MDN) que vous souhaitez envoyé à l’expéditeur du message. |
| **Envoyer un MDN** | Non  | Spécifie s’il faut envoyer un MDN synchrone pour les messages reçus.  |
| **Envoyer un MDN signé** | Non  | Spécifie s’il faut envoyer un MDN signé pour les messages reçus. Si vous avez besoin de signature, à partir de la **algorithme MIC** , sélectionnez l’algorithme à utiliser pour signer les messages. |
| **Envoyer un MDN asynchrone** | Non  | Spécifie s’il faut envoyer un MDN asynchrone. Si vous sélectionnez des MDN asynchrones, dans le **URL** zone, spécifiez l’URL à laquelle envoyer les MDN. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>Paramètres d’envoi AS2

![Sélectionnez « Paramètres d’envoi »](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Propriété | Obligatoire | Description |
|----------|----------|-------------|
| **Activer la signature de message** | Non  | Spécifie si tous les messages sortants doivent être signés numériquement. Si vous avez besoin de signature, sélectionnez ces valeurs : <p>-À partir de la **algorithme de signature** , sélectionnez l’algorithme à utiliser pour signer les messages. <br>-À partir de la **certificat** , sélectionnez un existant hôte certificat privé du partenaire pour signer les messages. Si vous n’avez pas un certificat, en savoir plus sur [l’ajout de certificats](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Activer le chiffrement de message** | Non  | Spécifie si tous les messages sortants doivent être chiffrés. Si vous avez besoin de chiffrement, sélectionnez ces valeurs : <p>-À partir de la **algorithme de chiffrement** , sélectionnez l’algorithme de certificat public du partenaire invité à utiliser pour chiffrer les messages. <br>-À partir de la **certificat** , sélectionnez un existant invité certificat privé du partenaire pour le chiffrement des messages sortants. Si vous n’avez pas un certificat, en savoir plus sur [l’ajout de certificats](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Activer la compression des messages** | Non  | Spécifie si tous les messages sortants doivent être compressées. |
| **Dérouler les en-têtes HTTP** | Non  | Place le HTTP `content-type` en-tête sur une seule ligne. |
| **Transmettre le nom de fichier dans l’en-tête MIME** | Non  | Spécifie s’il faut inclure le nom de fichier dans l’en-tête MIME. |
| **Exiger le MDN** | Non  | Spécifie si vous souhaitez recevoir des notifications de disposition de message (MDN) pour tous les messages sortants. |
| **Exiger le MDN signé** | Non  | Spécifie si vous souhaitez recevoir des MDN signé pour tous les messages sortants. Si vous avez besoin de signature, à partir de la **algorithme MIC** , sélectionnez l’algorithme à utiliser pour signer les messages. |
| **Exiger le MDN asynchrone** | Non  | Spécifie s’il faut recevoir des MDN de manière asynchrone. Si vous sélectionnez des MDN asynchrones, dans le **URL** zone, spécifiez l’URL à laquelle envoyer les MDN. |
| **Activer NRR** | Non  | Spécifie s’il faut exiger la réception de non-répudiation (NRR). Cet attribut de communication fournit une preuve que la réception des données comme résolus. |
| **Format de l’algorithme SHA2** | Non  | Spécifie le format de l’algorithme MIC à utiliser pour la signature dans les en-têtes pour les messages AS2 sortants ou les MDN |
||||

## <a name="next-steps"></a>Étapes suivantes

[Échanger des messages AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)
