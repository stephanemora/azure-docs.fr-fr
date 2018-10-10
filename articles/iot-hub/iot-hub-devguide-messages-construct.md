---
title: Présentation du format des messages Azure IoT Hub | Microsoft Docs
description: Le guide du développeur décrit le format et le contenu attendu des messages IoT Hub.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: edea20343c2a261902c082dbc5c96b78db6b470d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973213"
---
# <a name="create-and-read-iot-hub-messages"></a>Créer et lire des messages IoT Hub

Pour prendre en charge une interopérabilité transparente entre les différents protocoles, IoT Hub définit un format de message commun pour tous les protocoles d’appareil. Ce format de message est utilisé pour les messages [de routage appareil-à-cloud](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) et [cloud-à-appareil](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-c2d). 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub implémente les messages appareil-à-cloud à l’aide d’un modèle de messagerie en streaming. Les messages appareil-à-cloud d’IoT Hub s’apparentent plus à des *événements* [Event Hubs](https://docs.microsoft.com/azure/event-hubs/) qu’à des *messages* [Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/), dans la mesure où de nombreux événements passant à travers le service peuvent être lus par plusieurs lecteurs.

Un message IoT Hub comprend les éléments suivants :
* Un ensemble prédéfini de *propriétés système*, telles que répertoriées ci-dessous.
* Un ensemble de *propriétés de l’application*. Dictionnaire de propriétés de chaîne que l’application peut définir et auquel elle peut accéder sans avoir à désérialiser le corps du message. IoT Hub ne modifie jamais ces propriétés.
* Un corps binaire opaque.

Les valeurs et les noms de propriétés peuvent contenir seulement des caractères alphanumériques ASCII, plus les caractères ```{'!', '#', '$', '%, '&', "'", '*', '+', '-', '.', '^', '_', '`', '|', '~'}``` quand vous envoyez des messages appareil-à-cloud avec le protocole HTTPS ou quand vous envoyez des messages cloud-à-appareil.

La messagerie appareil-à-cloud avec IoT Hub présente les caractéristiques suivantes :

* Les messages appareil-à-cloud sont durables et sont conservés dans le point de terminaison **messages/events** par défaut d’un hub IoT jusqu’à sept jours.
* Les messages appareil-à-cloud ne doivent pas dépasser 256 Ko et peuvent être groupés en lots pour optimiser les envois. Les lots ne peuvent pas dépasser 256 Ko.
* IoT Hub n’autorise pas le partitionnement arbitraire. Les messages appareil-à-cloud sont partitionnés selon leur **deviceId**d’origine.
* Comme expliqué dans la section [Contrôler l’accès à IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security), IoT Hub permet l’authentification et le contrôle d’accès par appareil.

Pour plus d’informations sur l’encodage et le décodage des messages envoyés avec différents protocoles, consultez [SDK Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks).

Le tableau suivant répertorie l’ensemble des propriétés système dans les messages IoT Hub.

| Propriété | Description | L’utilisateur est-il définissable ? |
| --- | --- | --- |
| MessageId |Identificateur correspondant au message défini par l’utilisateur utilisé pour les modèles demande-réponse. Format : une chaîne qui respecte la casse (jusqu’à 128 caractères) de caractères alphanumériques 7 bits ASCII + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. | Oui |
| Numéro de séquence |Un numéro (unique par file d’attente d’appareil) affecté par IoT Hub à chaque message cloud-à-appareil. | Non pour les messages C2D. Oui dans le cas contraire. |
| À |Une destination spécifiée dans les messages [Cloud-à-appareil][lnk-c2d]. | Non pour les messages C2D. Oui dans le cas contraire. |
| ExpiryTimeUtc |Date et heure d’expiration du message. | Oui |
| EnqueuedTime |Date et heure de réception du message [Cloud-à-appareil][lnk-c2d] par IoT Hub. | Non pour les messages C2D. Oui dans le cas contraire. |
| CorrelationId |Une propriété de chaîne d’un message de réponse qui contient généralement l'ID du message de la demande dans les modèles demande-réponse. | Oui |
| UserId |Un ID utilisé pour spécifier l’origine des messages. Lorsque des messages sont générés par IoT Hub, la propriété est définie sur `{iot hub name}`. | Non  |
| Ack |Un générateur de messages de commentaires. Cette propriété est utilisée dans les messages cloud-à-appareil pour demander à IoT Hub de générer des messages de commentaires à la suite de la consommation du message par l’appareil. Valeurs possibles : **none** (par défaut) : aucun message de commentaires n’est généré ; **positive** : recevoir un message de commentaires si le message est achevé ; **negative** : recevoir un message de commentaires si le message a expiré (ou si le nombre maximal de remises a été atteint) sans être achevé par l’appareil, ou **full** : propriétés à la fois positive et négative. Pour plus d’informations, voir [Commentaires de messages][lnk-feedback]. | Oui |
| ConnectionDeviceId |Un ID défini par IoT Hub sur les messages appareil-à-cloud. Elle contient la propriété **deviceId** de l’appareil qui a envoyé le message. | Non pour les messages D2C. Oui dans le cas contraire. |
| ConnectionDeviceGenerationId |Un ID défini par IoT Hub sur les messages appareil vers cloud. Il contient la propriété **generationId** (conformément aux [Propriétés d’identité des appareils][lnk-device-properties]) de l’appareil qui a envoyé le message. | Non pour les messages D2C. Oui dans le cas contraire. |
| ConnectionAuthMethod |Une méthode d’authentification définie par IoT Hub sur les messages appareil-à-cloud. Cette propriété contient des informations sur la méthode d’authentification utilisée pour authentifier l’appareil qui a envoyé le message. Pour plus d’informations, consultez [Prévention d’usurpation d’identité entre un appareil et le cloud][lnk-antispoofing]. | Non pour les messages D2C. Oui dans le cas contraire. |
| CreationTimeUtc | Date et heure de création du message sur un appareil. Un appareil doit définir cette valeur explicitement. | Oui |

## <a name="message-size"></a>Taille des messages

IoT Hub mesure la taille des messages d’une façon indépendante du protocole, en considérant uniquement la charge utile réelle. La taille en octets est calculée comme la somme des éléments suivants :

* La taille du corps en octets.
* La taille en octets de toutes les valeurs des propriétés système du message.
* La taille en octets de tous les noms et valeurs des propriétés des utilisateurs.

Les noms et les valeurs des propriétés sont limités aux caractères ASCII ; ainsi, la longueur des chaînes est égale à la taille en octets.

## <a name="anti-spoofing-properties"></a>Propriétés de détection d’usurpation d’identité

Pour éviter l’usurpation d’appareil dans les messages appareil-à-cloud, IoT Hub marque tous les messages avec les propriétés suivantes :

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Les deux premières propriétés contiennent le **deviceId** et le **generationId** de l’appareil d’origine, conformément aux [Propriétés d’identité des appareils](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-identity-registry#device-identity-properties).

La propriété **ConnectionAuthMethod** contient un objet sérialisé JSON avec les propriétés suivantes :

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les limites de taille des messages dans IoT Hub, consultez [Quotas et limitations IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-quotas-throttling).
* Pour découvrir comment créer et lire des messages IoT Hub dans différents langages de programmation, consultez les [Démarrages rapides](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-node).