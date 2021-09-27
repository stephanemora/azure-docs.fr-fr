---
title: Exporter les informations générales de vos abonnements Azure
description: Décrit comment vous pouvez visualiser tous les ID d’abonnement Azure associés à votre compte.
author: bandersmsft
ms.reviewer: matrive
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 34b6e7be93cf42ab4d3ef80110b81fee8a403ed7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561418"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Exporter et visualiser les informations générales de vos abonnements
Si vous avez besoin de visualiser l’ensemble des ID d’abonnement associés à vos informations d’identification utilisateur, [téléchargez un fichier .json avec les informations de vos abonnements à partir du Centre des comptes Azure](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

Le fichier .json téléchargé fournit les informations suivantes :
- Email : adresse e-mail associée à votre compte.
- Puid : identificateur unique associé à votre compte de facturation.
- SubscriptionIds : liste des abonnements qui appartiennent à votre compte, énumérés par ID d’abonnement.

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
