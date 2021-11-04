---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 374fcb6470ab22bfd3531d9ef543bf3cc06e17ac
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131576760"
---
## <a name="generate-device-identity-certificates"></a>Générer des certificats d’identité d’appareil

L’approvisionnement manuel avec des certificats X.509 requiert IoT Edge version 1.0.10 ou ultérieure.

Lorsque vous configurez un appareil IoT Edge avec des certificats X.509, vous utilisez ce que l’on appelle un *certificat d’identité d’appareil*. Ce certificat est utilisé uniquement pour approvisionner un appareil IoT Edge et l’authentifier avec Azure IoT Hub. Il s’agit d’un certificat feuille qui ne signe pas d’autres certificats. Le certificat d’identité d’appareil est distinct des certificats d’autorité de certification que l’appareil IoT Edge présente aux modules ou aux appareils en aval à des fins de vérification.

Pour l’authentification par certificat X.509, les informations d’authentification de chaque appareil sont fournies sous la forme d’*empreintes numériques* extraites de vos certificats d’identité d’appareil. Ces empreintes numériques, fournies à IoT Hub au moment de l’inscription de l’appareil, permettent au service de reconnaître l’appareil qui se connecte.

Pour plus d’informations sur l’utilisation des certificats d’autorité de certification dans les appareils IoT Edge, consultez [Comprendre comment Azure IoT Edge utilise les certificats](../articles/iot-edge/iot-edge-certs.md).

Les fichiers suivants sont nécessaires pour le provisionnement manuel avec X.509 :

* Deux des certificats d’identité d’appareil avec leurs certificats de clé privée correspondants au format .cer ou .pem.

  Un ensemble de fichiers de certificat/clé est fourni au runtime IoT Edge. Quand vous créez des certificats d’identité d’appareil, définissez le nom commun du certificat (CN) avec l’ID d’appareil que l’appareil doit avoir dans votre hub IoT.

* Empreintes numériques tirées des deux certificats d’identité d’appareil.

  Les valeurs d’empreinte numérique sont constituées de 40 caractères hexadécimaux pour les hachages SHA-1 ou de 64 caractères hexadécimaux pour les hachages SHA-256. Les deux empreintes numériques sont fournies pour IoT Hub lors de l’enregistrement de l’appareil.

Si vous n’avez pas de certificats disponibles, vous pouvez [créer des certificats de démonstration pour tester les fonctionnalités de l’appareil IoT Edge](../articles/iot-edge/how-to-create-test-certificates.md). Suivez les instructions de cet article pour configurer des scripts de création de certificat, créer un certificat d’autorité de certification racine, puis créer deux certificats d’identité d’appareil IoT Edge.

L’une des méthodes permettant de récupérer l’empreinte numérique d’un certificat consiste à utiliser la commande openssl suivante :

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```
