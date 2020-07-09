---
title: Configurer l’authentification du client des appels entrants – Azure Event Grid sur IoT Edge | Microsoft Docs
description: Configurez les protocoles d’API exposés par Event Grid sur IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: a0bba9559c2a0b4ff6c8a4e5f2287692e27f8a1a
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171701"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Configurer l’authentification du client des appels entrants

Ce guide fournit des exemples de configurations d’authentification du client possibles pour le module Event Grid. Le module Event Grid prend en charge deux types d’authentifications du client :

* Signature d’accès partagé (SAP) basée sur une clé
* Basée sur un certificat

Consultez le guide [Sécurité et authentification](security-authentication.md) pour toutes les configurations possibles.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Activer l’authentification du client basé sur un certificat, ne pas autoriser les certificats auto-signés

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Activer l’authentification du client basé sur un certificat, autoriser les certificats auto-signés

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Définissez la propriété **inbound__clientAuth__clientCert__allowUnknownCA** sur **true** uniquement dans des environnements de test, car vous pouvez généralement utiliser des certificats auto-signés. Pour des charges de travail de production, nous vous recommandons de définir cette propriété sur **false** et des certificats d’une autorité de certification.

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Activer l’authentification du client basée sur un certificat et une clé SAP

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=true",
    "inbound__clientAuth__sasKeys__key1=<some-secret1-here>",
    "inbound__clientAuth__sasKeys__key2=<some-secret2-here>",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>L’authentification du client basée sur une clé SAP permet à un module non-IoT Edge d’effectuer des opérations de gestion et d’exécution en partant du principe que les ports d’API sont accessibles à l’extérieur du réseau IoT Edge.
