---
title: Schémas de suivi AS2 pour les messages B2B - Azure Logic Apps | Microsoft Docs
description: Créer des schémas de suivi AS2 qui surveillent les messages B2B dans les comptes d’intégration pour Azure Logic Apps avec Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.date: 01/27/2017
ms.openlocfilehash: 180d90450497b38f107f3601944385a003f50282
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193507"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>Créer des schémas pour le suivi des messages AS2 et des MDN dans les comptes d’intégration pour Azure Logic Apps

Pour surveiller le succès, les erreurs et les propriétés des messages lors de transactions interentreprises (B2B), vous pouvez utiliser ces schémas de suivi AS2 dans votre compte d’intégration :

* Schéma de suivi de message AS2
* Schéma de suivi de MDN AS2

## <a name="as2-message-tracking-schema"></a>Schéma de suivi de message AS2

```json
{
   "agreementProperties": {  
      "senderPartnerName": "",  
      "receiverPartnerName": "",  
      "as2To": "",  
      "as2From": "",  
      "agreementName": ""  
   },  
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "dispositionType": "",
      "fileName": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isMessageEncrypted": "",
      "isMessageCompressed": "",
      "correlationMessageId": "",
      "incomingHeaders": {
       },
      "outgoingHeaders": {
       },
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Propriété | Type | Description |
| --- | --- | --- |
| senderPartnerName | Chaîne | Nom de partenaire de l’expéditeur du message AS2. (facultatif) |
| receiverPartnerName | Chaîne | Nom de partenaire du destinataire du message AS2. (facultatif) |
| as2To | Chaîne | Nom du destinataire du message AS2 dans les en-têtes du message AS2. (obligatoire) |
| as2From | Chaîne | Nom de l’expéditeur du message AS2 dans les en-têtes du message AS2. (obligatoire) |
| agreementName | Chaîne | Nom du contrat AS2 dans lequel les messages sont résolus. (facultatif) |
| direction | Chaîne | Direction du flux de messages (envoi ou réception). (obligatoire) |
| messageId | Chaîne | ID du message AS2 dans les en-têtes du message AS2 (facultatif) |
| dispositionType |Chaîne | Valeur de type de disposition MDN (notification de réception du message). (facultatif) |
| fileName | Chaîne | Nom de fichier, de l’en-tête du message AS2. (facultatif) |
| isMessageFailed |Booléen | Indique si le message AS2 a échoué. (obligatoire) |
| isMessageSigned | Booléen | Indique si le message AS2 a été signé. (obligatoire) |
| isMessageEncrypted | Booléen | Indique si le message AS2 a été chiffré. (obligatoire) |
| isMessageCompressed |Booléen | Indique si le message AS2 a été compressé. (obligatoire) |
| correlationMessageId | Chaîne | ID de message AS2 permettant de mettre en corrélation les messages avec des MDN. (facultatif) |
| incomingHeaders |Dictionnaire de JToken | Détails de l’en-tête de message AS2 entrant. (facultatif) |
| outgoingHeaders |Dictionnaire de JToken | Détails de l’en-tête de message AS2 sortant. (facultatif) |
| isNrrEnabled | Booléen | Utiliser la valeur par défaut si cette valeur est inconnue. (obligatoire) |
| isMdnExpected | Boolean | Utiliser la valeur par défaut si cette valeur est inconnue. (obligatoire) |
| mdnType | Enum | Les valeurs autorisées sont **NotConfigured**, **Sync** ou **Async**. (obligatoire) |
||||

## <a name="as2-mdn-tracking-schema"></a>Schéma de suivi de MDN AS2

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": "g"
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "originalMessageId": "",
      "dispositionType": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isNrrEnabled": "",
      "statusCode": "",
      "micVerificationStatus": "",
      "correlationMessageId": "",
      "incomingHeaders": {
      },
      "outgoingHeaders": {
      }
   }
}
```

| Propriété | Type | Description |
| --- | --- | --- |
| senderPartnerName | Chaîne | Nom de partenaire de l’expéditeur du message AS2. (facultatif) |
| receiverPartnerName | Chaîne | Nom de partenaire du destinataire du message AS2. (facultatif) |
| as2To | Chaîne | Nom du partenaire qui reçoit le message AS2. (obligatoire) |
| as2From | Chaîne | Nom du partenaire qui envoie le message AS2. (obligatoire) |
| agreementName | Chaîne | Nom du contrat AS2 dans lequel les messages sont résolus. (facultatif) |
| direction |Chaîne | Direction du flux de messages (envoi ou réception). (obligatoire) |
| messageId | Chaîne | ID du message AS2. (facultatif) |
| originalMessageId |Chaîne | ID du message AS2 d’origine. (facultatif) |
| dispositionType | Chaîne | Valeur du type de disposition MDN. (facultatif) |
| isMessageFailed |Booléen | Indique si le message AS2 a échoué. (obligatoire) |
| isMessageSigned |Booléen | Indique si le message AS2 a été signé. (obligatoire) |
| isNrrEnabled | Booléen | Utiliser la valeur par défaut si cette valeur est inconnue. (obligatoire) |
| statusCode | Enum | Les valeurs autorisées sont **Accepted**, **Rejected** et **AcceptedWithErrors**. (obligatoire) |
| micVerificationStatus | Enum | Les valeurs autorisées sont **NotApplicable**, **Succeeded** et **Failed**. (obligatoire) |
| correlationMessageId | Chaîne | ID de corrélation. ID du message d’origine (ID de message du message pour lequel MDN est configurée). (facultatif) |
| incomingHeaders | Dictionnaire de JToken | Indique les détails de l’en-tête de message entrant. (facultatif) |
| outgoingHeaders |Dictionnaire de JToken | Indique les détails de l’en-tête de message sortant. (facultatif) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Schémas de suivi de protocole B2B

Pour plus d’informations sur les schémas de suivi de protocole B2B, consultez :

* [Schémas de suivi X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Schémas de suivi B2B personnalisés](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [la surveillance des messages B2B](logic-apps-monitor-b2b-message.md)
* En savoir plus sur [le suivi des messages B2B dans les journaux Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)