---
title: Créer un appareil de passerelle transparent - Azure IoT Edge | Microsoft Docs
description: Utiliser un appareil Azure IoT Edge en tant que passerelle transparente pouvant traiter des informations d’appareils en aval
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/03/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3b3aeff595671c5f924d01599b572b6b938ef09d
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666662"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configurer un appareil IoT Edge en tant que passerelle transparente

Cet article donne des instructions détaillées pour configurer un appareil IoT Edge comme passerelle transparente permettant à d’autres appareils de communiquer avec IoT Hub. Cet article utilise le terme *passerelle ioT Edge* pour désigner un appareil IoT Edge configuré comme passerelle transparente. Pour plus d’informations, consultez [Guide pratique pour utiliser un appareil IoT Edge en tant que passerelle](./iot-edge-as-gateway.md).

>[!NOTE]
>Actuellement :
>
> * Les appareils Edge ne peuvent pas se connecter aux passerelles IoT Edge.
> * Les appareils en aval ne peuvent pas utiliser le chargement de fichier.

La configuration d’une connexion de passerelle transparente s’effectue en trois grandes étapes. Cet article décrit la première étape :

1. **L’appareil de passerelle doit être en mesure de se connecter aux appareils en aval, de recevoir des communications de ces appareils et de router les messages vers la destination appropriée, tout cela en toute sécurité.**
2. L’appareil en aval doit avoir une identité d’appareil pour s’authentifier auprès d’IoT Hub et savoir comment communiquer par le biais de l’appareil de passerelle associé. Pour plus d’informations, consultez [Authentifier un appareil en aval auprès d’Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. L’appareil en aval doit pouvoir se connecter de façon sécurisée à son appareil de passerelle. Pour plus d’informations, voir [Connecter un appareil en aval à une passerelle Azure IoT Edge](how-to-connect-downstream-device.md).

Pour qu’un appareil fonctionne comme une passerelle, il doit être capable de se connecter de manière sécurisée aux appareils en aval associés. Azure IoT Edge vous permet d’utiliser une infrastructure à clé publique (PKI) pour configurer des connexions sécurisées entre les appareils. Dans ce cas, nous autorisons un appareil en aval à se connecter à un appareil IoT Edge faisant office de passerelle transparente. Pour assurer un niveau raisonnable de sécurité, l’appareil en aval doit confirmer l’identité de l’appareil de passerelle. Cette vérification d’identité empêche vos appareils de se connecter à des passerelles potentiellement malveillantes.

Un appareil en aval dans un scénario de passerelle transparente peut être n’importe quelle application ou plateforme dont l’identité a été créée avec le service cloud [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub). Dans de nombreux cas, ces applications utilisent [Azure IoT device SDK](../iot-hub/iot-hub-devguide-sdks.md). Pour des raisons pratiques, un appareil en aval peut même être une application fonctionnant sur l’appareil de passerelle IoT Edge proprement dit. Cependant, un appareil IoT Edge ne peut pas se trouver en aval d’une passerelle IoT Edge.

Vous pouvez créer n’importe quelle infrastructure de certificat permettant l’approbation requise pour votre topologie de l’appareil à la passerelle. Dans cet article, nous nous basons sur la même configuration de certificat que vous utiliseriez pour activer la [sécurité d’autorité de certification X.509](../iot-hub/iot-hub-x509ca-overview.md) dans IoT Hub, ce qui implique d’avoir un certificat d’autorité de certification X.509 associé à un hub IoT spécifique (l’autorité de certification racine du hub IoT), une série de certificats signés par cette autorité de certification ainsi qu’une autorité de certification pour l’appareil IoT Edge.

![Configuration du certificat de la passerelle](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Le terme « autorité de certification racine » utilisé dans cet article désigne le certificat public de l’autorité la plus haute dans la hiérarchie de certificats PKI, et pas obligatoirement le certificat racine d’une autorité de certification syndiquée. La plupart du temps, il s’agit en fait d’un certificat public d’une autorité de certification intermédiaire.

Le démon de sécurité IoT Edge utilise le certificat d’autorité de certification de l’appareil IoT Edge pour signer un certificat d’autorité de certification de charge de travail, qui à son tour signe un certificat de serveur pour le hub IoT Edge. La passerelle présente son certificat de serveur à l’appareil en aval lors de l’établissement de la connexion. L’appareil en aval vérifie que le certificat de serveur fait partie d’une chaîne de certificats qui remonte jusqu’au certificat d’autorité de certification racine. Ce processus permet à l’appareil en aval de garantir que la passerelle provient d’une source approuvée. Pour plus d’informations, consultez [Comprendre comment Azure IoT Edge utilise les certificats](iot-edge-certs.md).

Les étapes suivantes vous guident tout au long du processus de création des certificats et de leur installation aux bons emplacements sur la passerelle. Vous pouvez utiliser n’importe quel ordinateur pour générer les certificats, puis les copier sur votre appareil IoT Edge.

## <a name="prerequisites"></a>Prérequis

Un appareil Azure IoT Edge, configuré avec des [certificats de production](how-to-manage-device-certificates.md).

## <a name="deploy-edgehub-to-the-gateway"></a>Déployer edgeHub sur la passerelle

À la première installation d’IoT Edge sur un appareil, un seul module système démarre automatiquement : l’agent IoT Edge. Une fois que vous avez créé le premier déploiement d’un appareil, le deuxième module système, le concentrateur IoT Edge, démarre également.

Le hub IoT Edge est chargé de recevoir les messages entrants des appareils en aval et de les acheminer vers la destination suivante. Si le module **edgeHub** ne fonctionne pas sur votre appareil, créez un déploiement initial pour votre appareil. Le déploiement apparaîtra vide, car vous n’ajoutez aucun module, mais il garantit que les deux modules système sont démarrés.

Vous pouvez voir quels modules fonctionnent sur un appareil en vérifiant les détails de l’appareil dans le Portail Azure, en affichant l’état de l’appareil dans Visual Studio ou dans Visual Studio Code, ou en exécutant la commande `iotedge list` sur l’appareil proprement dit.

Si le module **edgeAgent** fonctionne sans le module **edgeHub**, procédez comme suit :

1. Accédez à votre hub IoT dans le portail Azure.

2. Accédez à **IoT Edge** et sélectionnez l’appareil IoT Edge à utiliser comme passerelle.

3. Sélectionnez **Définir modules**.

4. Sélectionnez **Suivant**.

5. La page **Spécifier des itinéraires** devrait comporter un itinéraire par défaut qui envoie à IoT Hub tous les messages de tous les modules. Si ce n’est pas le cas, ajoutez le code suivant, puis sélectionnez **Suivant**.

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. Sur la page **Vérifier le modèle**, sélectionnez **Envoyer**.

## <a name="open-ports-on-gateway-device"></a>Ouvrir des ports sur l’appareil de passerelle

Les appareils IoT Edge standard peuvent fonctionner sans connexions entrantes, car toutes les communications avec le hub IoT s’effectuent par le biais de connexions sortantes. Les appareils de passerelle fonctionnent différemment, car ils doivent recevoir les messages de leurs appareils en aval. S’il y a un pare-feu entre les appareils en aval et l’appareil de passerelle, les communications doivent également pouvoir se faire par le pare-feu.

Dans un scénario de passerelle opérationnel, au moins un des protocoles pris en charge par le hub IoT Edge doit être ouvert pour recevoir le trafic entrant des appareils en aval. Les protocoles pris en charge sont les suivants : MQTT, AMQP, HTTPS, MQTT sur Websockets et AMQP sur WebSockets.

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS |

## <a name="route-messages-from-downstream-devices"></a>Acheminer les messages à partir des appareils en aval

Le runtime IoT Edge peut acheminer les messages envoyés à partir des appareils en aval comme les messages envoyés par les modules. Cette fonctionnalité vous permet d’effectuer une analytique dans un module s’exécutant sur la passerelle avant d’envoyer des données vers le cloud.

Actuellement, la façon d’acheminer les messages envoyés par les appareils en aval consiste à les différencier des messages envoyés par les modules. Les messages envoyés par les modules contiennent tous une propriété système appelée **connectionModuleId** mais pas les messages envoyés par les appareils en aval. Vous pouvez utiliser la clause WHERE de l’itinéraire pour exclure tout message contenant cette propriété système.

L’exemple de route ci-dessous envoie des messages d’un appareil en aval à un module nommé `ai_insights`, puis du module `ai_insights` à IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

Pour plus d’informations sur le routage des messages, consultez [Déployer des modules et établir des itinéraires](./module-composition.md#declare-routes).

## <a name="enable-extended-offline-operation"></a>Activer des opérations hors connexion étendues

Depuis la [version v1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) du runtime IoT Edge, l’appareil de passerelle et les appareils en aval qui y sont connectés peuvent être configurés pour permettre les opérations hors connexion étendues.

Avec cette fonctionnalité, les modules locaux et les appareils en aval peuvent se réauthentifier auprès de l’appareil IoT Edge en cas de besoin, et communiquer entre eux à l’aide de messages et de méthodes même quand ils sont déconnectés du hub IoT. Pour plus d’informations, consultez [Introduction aux fonctionnalités hors connexion étendues pour les appareils, modules et appareils enfants IoT Edge](offline-capabilities.md).

Pour activer les fonctionnalités hors connexion étendues, établissez une relation parent-enfant entre un appareil de passerelle IoT Edge et les appareils en aval qui y seront connectés. Ces étapes sont expliquées plus en détail dans [Authentifier un appareil en aval auprès d’Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que votre appareil IoT Edge fonctionne comme une passerelle transparente, il vous faut configurer vos appareils en aval de façon à ce qu’ils approuvent la passerelle et lui envoient des messages. Pour poursuivre la configuration de votre scénario de passerelle transparente, suivez les étapes décrites dans [Authentifier un appareil en aval auprès d’Azure IoT Hub](how-to-authenticate-downstream-device.md).
