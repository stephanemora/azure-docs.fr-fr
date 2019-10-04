---
title: Exporter les informations générales de vos abonnements Azure | Microsoft Docs
description: Décrit comment vous pouvez visualiser tous les ID d’abonnement Azure associés à votre compte.
keywords: ''
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 8a3d1a3a6b1dce1d729942715bbe5962837ff093
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "60918659"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Exporter et visualiser les informations générales de vos abonnements
Si vous avez besoin de visualiser l’ensemble des ID d’abonnement associés à vos informations d’identification utilisateur, [téléchargez un fichier .json avec les informations de vos abonnements à partir du Centre des comptes Azure](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Le fichier .json téléchargé fournit les informations suivantes :
- E-mail : adresse e-mail associée à votre compte.
- Puid : identificateur unique associé à votre compte de facturation.
- SubscriptionIds : liste des abonnements qui appartiennent à votre compte, énumérés par ID d’abonnement.

### <a name="subscriptionsjson-sample"></a>Exemple de fichier subscriptions.json

```json
{
  "Email":"admin@contoso.com",
  "Puid":"00052xxxxxxxxxxx",
  "SubscriptionIds":[
    "38124d4d-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "7c8308f1-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "39a25f2b-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "52ec2489-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "e42384b2-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "90757cdc-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  ]
}
```
