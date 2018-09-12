---
title: Présentation du format des messages Azure IoT Hub | Microsoft Docs
description: Le guide du développeur décrit le format et le contenu attendu des messages IoT Hub.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 7c08848698f07d64bbbff429682c18525659f7bf
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286515"
---
# <a name="create-and-read-iot-hub-messages"></a>Créer et lire des messages IoT Hub

Pour prendre en charge une interopérabilité transparente entre les différents protocoles, IoT Hub définit un format de message commun pour tous les protocoles d’appareil. Ce format est utilisé pour les messages [appareil-à-cloud][lnk-d2c] et [cloud-à-appareil][lnk-c2d]. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Un [message IoT Hub][lnk-messaging] comprend les éléments suivants :

* Un ensemble prédéfini de *propriétés système*, telles que répertoriées ci-dessous.
* Un ensemble de *propriétés de l’application*. Dictionnaire de propriétés de chaîne que l’application peut définir et auquel elle peut accéder sans avoir à désérialiser le corps du message. IoT Hub ne modifie jamais ces propriétés.
* Un corps binaire opaque.

Les valeurs et les noms de propriétés peuvent contenir uniquement des caractères alphanumériques ASCII, plus les caractères ```{'!', '#', '$', '%, '&', "'", '*', '+', '-', '.', '^', '_', '`', '|', '~'}```, quand vous effectuez les opérations suivantes :  

* Envoyer des messages appareil-à-cloud à l’aide du protocole HTTPS.
* Envoyer des messages cloud-à-appareil.

Pour plus d’informations sur l’encodage et le décodage des messages envoyés à l’aide de différents protocoles, consultez [Kits de développement logiciel (SDK) Azure IoT][lnk-sdks].

Le tableau suivant répertorie l’ensemble des propriétés système dans les messages IoT Hub.

| Propriété | Description | L’utilisateur est-il définissable ? |
| --- | --- | --- |
| MessageId |Identificateur correspondant au message défini par l’utilisateur utilisé pour les modèles demande-réponse. Format : une chaîne qui respecte la casse (jusqu’à 128 caractères) de caractères alphanumériques 7 bits ASCII + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. | Oui |
| Numéro de séquence |Un numéro (unique par file d’attente d’appareil) affecté par IoT Hub à chaque message cloud-à-appareil. | Non pour les messages C2D. Oui dans le cas contraire. |
| À |Une destination spécifiée dans les messages [cloud-à-appareil][lnk-c2d]. | Non pour les messages C2D. Oui dans le cas contraire. |
| ExpiryTimeUtc |Date et heure d’expiration du message. | Oui |
| EnqueuedTime |Date et heure de réception du message [Cloud-à-appareil][lnk-c2d] par IoT Hub. | Non pour les messages C2D. Oui dans le cas contraire. |
| CorrelationId |Une propriété de chaîne d’un message de réponse qui contient généralement l'ID du message de la demande dans les modèles demande-réponse. | Oui |
| UserId |Un ID utilisé pour spécifier l’origine des messages. Lorsque des messages sont générés par IoT Hub, la propriété est définie sur `{iot hub name}`. | Non  |
| Ack |Un générateur de messages de commentaires. Cette propriété est utilisée dans les messages cloud-à-appareil pour demander à IoT Hub de générer des messages de commentaires à la suite de la consommation du message par l’appareil. Valeurs possibles : **none** (par défaut) : aucun message de commentaires n’est généré ; **positive** : recevoir un message de commentaires si le message est achevé ; **negative** : recevoir un message de commentaires si le message a expiré (ou si le nombre maximal de remises a été atteint) sans être achevé par l’appareil, ou **full** : propriétés à la fois positive et négative. Pour plus d’informations, consultez [Commentaires sur les messages][lnk-feedback]. | Oui |
| ConnectionDeviceId |Un ID défini par IoT Hub sur les messages appareil-à-cloud. Elle contient la propriété **deviceId** de l’appareil qui a envoyé le message. | Non pour les messages D2C. Oui dans le cas contraire. |
| ConnectionDeviceGenerationId |Un ID défini par IoT Hub sur les messages appareil vers cloud. Elle contient la propriété **generationId** (conformément aux [Propriétés d’identité des appareils][lnk-device-properties]) de l’appareil qui a envoyé le message. | Non pour les messages D2C. Oui dans le cas contraire. |
| ConnectionAuthMethod |Une méthode d’authentification définie par IoT Hub sur les messages appareil-à-cloud. Cette propriété contient des informations sur la méthode d’authentification utilisée pour authentifier l’appareil qui a envoyé le message. Pour plus d’informations, consultez [Prévention d’usurpation d’identité entre un appareil et le cloud][lnk-antispoofing]. | Non pour les messages D2C. Oui dans le cas contraire. |
| CreationTimeUtc | Date et heure de création du message sur un appareil. Un appareil doit définir cette valeur explicitement. | Oui |

## <a name="message-size"></a>Taille des messages

IoT Hub mesure la taille des messages d’une façon indépendante du protocole, en considérant uniquement la charge utile réelle. La taille en octets est calculée comme la somme des éléments suivants :

* La taille du corps en octets.
* La taille en octets de toutes les valeurs des propriétés système du message.
* La taille en octets de tous les noms et valeurs des propriétés des utilisateurs.

Les noms et les valeurs des propriétés sont limités aux caractères ASCII ; ainsi, la longueur des chaînes est égale à la taille en octets.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les limites de taille des messages dans IoT Hub, consultez [Quotas et limitation IoT Hub][lnk-quotas].

Pour découvrir comment créer et lire des messages IoT Hub dans divers langages de programmation, consultez les [guides de démarrage rapide][lnk-get-started].

[lnk-messaging]: iot-hub-devguide-messaging.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-get-started]: quickstart-send-telemetry-node.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-feedback]: iot-hub-devguide-messages-c2d.md#message-feedback
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-antispoofing]: iot-hub-devguide-messages-d2c.md#anti-spoofing-properties
