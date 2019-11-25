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
ms.openlocfilehash: 336b6157128468169264d6ffa9564da4d9338aae
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991818"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Configurer l’identité du module Event Grid

Cet article vous donne des exemples de configurations d’identité possibles pour un module Event Grid. Par défaut, le module Event Grid présente son certificat d’identité tel qu’il est configuré par le démon de sécurité IoT. Un certificat d’identité est présenté par le module Event Grid sur ses appels sortants, lorsqu’il remet des événements. L’abonné à un événement Event Grid peut alors choisir de valider que c’est bien le module Event Grid qui a envoyé l’événement avant d’accepter celui-ci.

Consultez le guide [Sécurité et authentification](security-authentication.md) pour toutes les configurations possibles.

## <a name="always-present-identity-certificate"></a>Toujours présenter le certificat d’identité
Voici un exemple de configuration pour toujours présenter un certificat d’identité sur les appels sortants. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Ne pas présenter le certificat d’identité
Voici un exemple de configuration pour ne pas présenter un certificat d’identité sur les appels sortants. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=false"
  ]
}
 ```
