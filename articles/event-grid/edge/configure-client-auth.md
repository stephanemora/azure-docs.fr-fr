---
title: Configurer l’authentification du client des appels entrants – Azure Event Grid sur IoT Edge | Microsoft Docs
description: Configurez les protocoles d’API exposés par Event Grid sur IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: b5456130e89bf77e2c2ba41880323e38f6b27f4c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991838"
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
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Activer l’authentification du client basé sur un certificat, autoriser les certificats auto-signés

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Définissez la propriété **inbound:clientAuth:clientCert:allowUnknownCA** sur **true** uniquement dans des environnements de test, car vous pouvez généralement utiliser des certificats auto-signés. Pour des charges de travail de production, nous vous recommandons de définir cette propriété sur **false** et des certificats d’une autorité de certification.

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Activer l’authentification du client basée sur un certificat et une clé SAP

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=true",
    "inbound:clientAuth:sasKeys:key1=<some-secret1-here>",
    "inbound:clientAuth:sasKeys:key2=<some-secret2-here>",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>L’authentification du client basée sur une clé SAP permet à un module non-IoT Edge d’effectuer des opérations de gestion et d’exécution en partant du principe que les ports d’API sont accessibles à l’extérieur du réseau IoT Edge.
