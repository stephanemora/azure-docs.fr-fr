---
title: Configurer une authentification d’abonné webhook - Azure Event Grid IoT Edge | Microsoft Docs
description: Configurer une authentification d’abonné webhook
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 97ed1e2ad84d895e9da0d96cd070e14acb46385d
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991830"
---
# <a name="configure-webhook-subscriber-authentication"></a>Configurer une authentification d’abonné webhook

Ce guide fournit des exemples de configurations possibles d’abonnés webhook pour un module Event Grid. Par défaut, seuls les points de terminaison HTTPs sont acceptés pour les abonnés webhook. Le module Event Grid est rejeté si l’abonné présente un certificat auto-signé.

## <a name="allow-only-https-subscriber"></a>Autoriser uniquement l’abonné HTTPS

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Autoriser l’abonné HTTPS avec un certificat auto-signé

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Affectez à la propriété `outbound:webhook:allowUnknownCA` la valeur `true` uniquement dans les environnements de test, car vous pouvez généralement utiliser des certificats auto-signés. Pour les charges de travail de production, nous vous recommandons de les définir sur **false**.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>Autoriser l’abonné HTTPS mais ignorer la validation du certificat

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=true",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Affectez à la propriété `outbound:webhook:skipServerCertValidation` la valeur `true` uniquement dans les environnements de test, car vous ne présentez peut-être pas un certificat qui doit être authentifié. Pour les charges de travail de production, nous vous recommandons de les définir sur **false**

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Autoriser à la fois HTTP et HTTPs avec des certificats auto-signés

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=false",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Affectez à la propriété `outbound:webhook:httpsOnly` la valeur `false` uniquement dans les environnements de test, car vous souhaiterez peut-être afficher d’abord un abonné HTTP. Pour les charges de travail de production, nous vous recommandons de les définir sur **true**
