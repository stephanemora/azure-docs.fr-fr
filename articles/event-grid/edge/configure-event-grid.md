---
title: Configuration – Azure Event Grid IoT Edge | Microsoft Docs
description: Configuration dans Event Grid sur IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a9bac9a84e1ba034b011691ae82a1cb67eb71af0
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991814"
---
# <a name="event-grid-configuration"></a>Configuration d’Event Grid

Event Grid fournit de nombreuses configurations modifiables environnement par environnement. La section suivante référence toutes les options disponibles et leurs valeurs par défaut.

## <a name="tls-configuration"></a>Configuration TLS

Pour plus d’informations sur l’authentification client en général, voir [Sécurité et authentification](security-authentication.md). Vous trouverez des exemples de son utilisation dans [cet article](configure-api-protocol.md).

| Nom de la propriété | Description |
| ---------------- | ------------ |
|`inbound:serverAuth:tlsPolicy`| Stratégie TLS du module Event Grid. Valeur par défaut : HTTPS uniquement.
|`inbound:serverAuth:serverCert:source`| Source du certificat de serveur utilisée par le module Event Grid pour sa configuration TLS. Valeur par défaut : IoT Edge.

## <a name="incoming-client-authentication"></a>Authentification de client entrant

Pour plus d’informations sur l’authentification client en général, voir [Sécurité et authentification](security-authentication.md). Vous trouverez des exemples dans [cet article](configure-client-auth.md).

| Nom de la propriété | Description |
| ---------------- | ------------ |
|`inbound:clientAuth:clientCert:enabled`| Propriété permettant d’activer et de désactiver l’authentification client par certificat. La valeur par défaut est true.
|`inbound:clientAuth:clientCert:source`| Source permettant de valider les certificats clients. Valeur par défaut : IoT Edge.
|`inbound:clientAuth:clientCert:allowUnknownCA`| Stratégie permettant d’autoriser un certificat client auto-signé. La valeur par défaut est true.
|`inbound:clientAuth:sasKeys:enabled`| Propriété permettant d’activer et de désactiver l’authentification client par clé SAP. Valeur par défaut : désactivée.
|`inbound:clientAuth:sasKeys:key1`| L’une des valeurs permettant de valider les demandes entrantes.
|`inbound:clientAuth:sasKeys:key2`| Deuxième valeur facultative permettant de valider les demandes entrantes.

## <a name="outgoing-client-authentication"></a>Authentification de client sortant
Pour plus d’informations sur l’authentification client en général, voir [Sécurité et authentification](security-authentication.md). Vous trouverez des exemples dans [cet article](configure-identity-auth.md).

| Nom de la propriété | Description |
| ---------------- | ------------ |
|`outbound:clientAuth:clientCert:enabled`| Propriété permettant d’activer/de désactiver l’attachement d’un certificat d’identité pour les demandes sortantes. La valeur par défaut est true.
|`outbound:clientAuth:clientCert:source`| Source permettant de récupérer le certificat sortant du module Event Grid. Valeur par défaut : IoT Edge.

## <a name="webhook-event-handlers"></a>Gestionnaires d’événements de webhook

Pour plus d’informations sur l’authentification client en général, voir [Sécurité et authentification](security-authentication.md). Vous trouverez des exemples dans [cet article](configure-webhook-subscriber-auth.md).

| Nom de la propriété | Description |
| ---------------- | ------------ |
|`outbound:webhook:httpsOnly`| Stratégie permettant de contrôler si seuls les abonnés HTTPS sont autorisés. Valeur par défaut : true (HTTPS uniquement).
|`outbound:webhook:skipServerCertValidation`| Indicateur permettant de contrôler s’il faut valider le certificat de l’abonné. La valeur par défaut est true.
|`outbound:webhook:allowUnknownCA`| Stratégie permettant de contrôler si l’abonné peut présenter un certificat auto-signé. La valeur par défaut est true. 

## <a name="delivery-and-retry"></a>Livraison et nouvelle tentative

Pour plus d’informations sur cette fonctionnalité en général, voir [Remise et nouvelle tentative](delivery-retry.md).

| Nom de la propriété | Description |
| ---------------- | ------------ |
| `broker:defaultMaxDeliveryAttempts` | Nombre maximal de tentatives de remise d’un événement. La valeur par défaut est 30.
| `broker:defaultEventTimeToLiveInSeconds` | Durée de vie (TTL) en secondes après laquelle un événement sera supprimé s’il n’est pas remis. Valeur par défaut : **7 200** secondes.

## <a name="output-batching"></a>Traitement par lot des sorties

Pour plus d’informations sur cette fonctionnalité en général, voir [Remise et traitement par lot de sortie](delivery-output-batching.md).

| Nom de la propriété | Description |
| ---------------- | ------------ |
| `api:deliveryPolicyLimits:maxBatchSizeInBytes` | Valeur maximale autorisée pour le bouton `ApproxBatchSizeInBytes`. La valeur par défaut est `1_058_576`.
| `api:deliveryPolicyLimits:maxEventsPerBatch` | Valeur maximale autorisée pour le bouton `MaxEventsPerBatch`. La valeur par défaut est `50`.
| `broker:defaultMaxBatchSizeInBytes` | Taille maximale de la demande de remise lorsque seul `MaxEventsPerBatch` est spécifié. La valeur par défaut est `1_058_576`.
| `broker:defaultMaxEventsPerBatch` | Nombre maximal d’événements à ajouter à un lot lorsque seul `MaxBatchSizeInBytes` est spécifié. La valeur par défaut est `10`.
