---
title: Configurer l’identité-Azure Event Grid IoT Edge | Microsoft Docs
description: Configurer l’identité du module Event Grid
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: f6449866d47b20d22912e3803ac1737528aa1dbc
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110370597"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Configurer l’identité du module Event Grid

Cet article explique comment configurer l’identité de Grid sur Edge. Par défaut, le module Event Grid présente son certificat d’identité tel qu’il est configuré par le démon de sécurité IoT. Event Grid sur Edge présente son certificat d’identité avec ses appels sortants lorsqu’il livre des événements. Un abonné peut ensuite confirmer que c’est le module Event Grid qui a envoyé l’événement avant d’accepter.

Consultez le guide [Sécurité et authentification](security-authentication.md) pour toutes les configurations possibles.

## <a name="always-present-identity-certificate"></a>Toujours présenter le certificat d’identité
Voici un exemple de configuration pour toujours présenter un certificat d’identité sur les appels sortants. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Ne pas présenter le certificat d’identité
Voici un exemple de configuration pour ne pas présenter un certificat d’identité sur les appels sortants. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
