---
title: Configurer l’identité-Azure Event Grid IoT Edge | Microsoft Docs
description: Configurer l’identité du module Event Grid
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0aedeea2a6ad08e1627c2d1a6ebde6c91a4d02d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841763"
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
