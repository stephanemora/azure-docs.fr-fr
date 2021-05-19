---
title: Résolution de l’erreur Azure IoT Hub 401003 IoTHubUnauthorized
description: Comprendre comment corriger l’erreur 401003 IoTHubUnauthorized
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 23bd1ce63e3d697a40125c79e802ad3e01915f49
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109487490"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

Cet article décrit les causes et solutions des erreurs **401003 IoTHubUnauthorized**.

## <a name="symptoms"></a>Symptômes

### <a name="symptom-1"></a>Symptôme 1

Dans les journaux, vous constatez que les appareils se déconnectent en générant l'erreur **401003 IoTHubUnauthorized**, suivie de l'erreur **404104 DeviceConnectionClosedRemotely**, avant de se connecter avec succès peu de temps après.

### <a name="symptom-2"></a>Symptôme 2

Les demandes adressées à IoT Hub échouent en générant l’un des messages d’erreur suivants :

* En-tête d’autorisation manquant
* L’IotHub « \* » ne contient pas l’appareil spécifié « \* »
* La règle d’autorisation « \* » n’autorise pas l’accès pour « \* »
* L’authentification a échoué pour cet appareil. Renouvelez le jeton ou le certificat, puis recommencez la connexion
* L’empreinte numérique ne correspond pas à la configuration : Empreinte : SHA1Hash=\*, SHA2Hash=\*; Configuration : PrimaryThumbprint=\*, SecondaryThumbprint=\*
* Le principal user@example.com n’est pas autorisé pour GET sur/exampleOperation en raison de l’absence d’autorisations affectées

## <a name="cause"></a>Cause

### <a name="cause-1"></a>Cause 1

Pour MQTT, certains kits de développement logiciel (SDK) s’appuient sur l’IoT Hub pour déclenchent la déconnexion quand le jeton SAP expire pour savoir quand l’actualiser. Ainsi,

1. le jeton SAP expire
1. L’IoT Hub constate l’expiration et déconnecte l’appareil en générant l’erreur **401003 IoTHubUnauthorized**
1. L’appareil se déconnecte en générant l’erreur **404104 DeviceConnectionClosedRemotely**
1. Le Kit de développement logiciel (SDK) IoT génère un nouveau jeton SAP
1. L’appareil se reconnecte correctement à l’IoT Hub

### <a name="cause-2"></a>Cause 2

L’IoT Hub n’a pas pu authentifier l’en-tête, la règle ou la clé d’autorisation. Peuvent être en cause toutes les raisons citées dans les symptômes.

## <a name="solution"></a>Solution

### <a name="solution-1"></a>Solution 1

Aucune action n’est nécessaire si vous utilisez le Kit de développement logiciel (SDK) IoT pour la connexion à l’aide de la chaîne de connexion de l’appareil. Le Kit de développement logiciel (SDK) IoT régénère le nouveau jeton pour se reconnecter lors de l’expiration du jeton SAP.

La durée de vie par défaut des jetons est de 60 minutes dans tous les kits de développement logiciel. Toutefois, pour certains d’entre eux, la durée de vie du jeton et le seuil de renouvellement de celui-ci sont configurables. En outre, les erreurs générées quand un appareil se déconnecte et se reconnecte lors d’un renouvellement de jeton diffèrent pour chaque Kit de développement logiciel (SDK). Pour plus d’informations sur la façon de déterminer le Kit de développement logiciel (SDK) que votre appareil utilise dans les journaux, consultez [Comportement de déconnexion d’appareil MQTT avec les kits de développement logiciel (SDK) Azure IoT](iot-hub-troubleshoot-connectivity.md#mqtt-device-disconnect-behavior-with-azure-iot-sdks).

Pour les développeurs d’appareils, si le volume des erreurs pose problème, basculez vers le Kit de développement logiciel (SDK) C qui renouvelle le jeton SAP avant expiration. Pour AMQP, le jeton SAP peut s’actualiser sans déconnexion.

### <a name="solution-2"></a>Solution 2

En général, le message d’erreur présenté doit expliquer comment corriger l’erreur. Si, pour une raison quelconque, vous n’avez pas accès au détail du message d’erreur, assurez-vous que :

- Le jeton de signature d’accès partagé (SAP) ou un autre jeton de sécurité que vous utilisez ne sont pas arrivés à expiration.
- Pour l’authentification par certificat X.509, le certificat d’appareil ou le certificat d’autorité de certification associé à l’appareil n’est pas arrivé à expiration. Pour savoir comment inscrire des certificats d’autorité de certification X.509 auprès de IoT Hub, consultez [Configuration de la sécurité X.509 dans un hub IoT Azure](./tutorial-x509-scripts.md).
- Pour l’authentification par empreinte de certificat X.509, l’empreinte numérique du certificat d’appareil est inscrite auprès de IoT Hub.
- Les informations d’identification d’autorisation sont bien formées pour le protocole que vous utilisez. Pour plus d’informations, consultez [Contrôle de l’accès à IoT Hub](iot-hub-devguide-security.md).
- La règle d’autorisation utilisée a l’autorisation pour l’opération demandée.
- Pour les derniers messages d’erreur commençant par « Le principal... », cette erreur peut être résolue en affectant le niveau d’autorisation Azure RBAC approprié à l’utilisateur. Par exemple, un propriétaire sur IoT Hub peut attribuer le rôle « Propriétaire des données IoT Hub », qui accorde toutes les autorisations. Essayez ce rôle pour résoudre le problème d’autorisation insuffisante.

## <a name="next-steps"></a>Étapes suivantes

- Pour faciliter l’authentification auprès d’IoT Hub, nous vous recommandons d’utiliser des [Kits de développement logiciel (SDK) Azure IoT](iot-hub-devguide-sdks.md).
- Pour plus d’informations sur l’authentification avec IoT Hub, consultez [Contrôle de l’accès à IoT Hub](iot-hub-devguide-security.md).