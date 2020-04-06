---
title: Chiffrement des données au repos de Content Moderator
titleSuffix: Azure Cognitive Services
description: Chiffrement des données au repos de Content Moderator.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: b41d822791f61fce274f628b87c478c3a986f4c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372010"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Chiffrement des données au repos de Content Moderator

Content Moderator chiffre automatiquement vos données lorsqu'elles sont conservées dans le cloud, ce qui vous permet de satisfaire aux exigences de votre organisation en matière de sécurité et de conformité.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Les clés gérées par le client sont disponibles uniquement au niveau tarifaire E0. Pour demander la possibilité d’utiliser des clés gérées par le client, complétez et envoyez le [formulaire de demande de clé gérée par le client Content Moderator](https://aka.ms/cogsvc-cmk). Comptez environ 3 à 5 jours ouvrables pour obtenir des nouvelles sur le statut de votre demande. Selon la demande, vous pouvez être placé dans une file d’attente et approuvé lorsque de l’espace devient disponible. Une fois approuvé pour l’utilisation d’une clé CMK avec le service Content Moderator, vous devez créer une ressource Content Moderator et sélectionner E0 comme niveau tarifaire. Une fois que votre ressource Content Moderator avec le niveau tarifaire E0 est créée, vous pouvez utiliser Azure Key Vault pour configurer votre identité managée.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Activer le chiffrement des données pour votre équipe Content Moderator

Afin d’activer le chiffrement des données pour votre équipe de révision Content Moderator, consultez [Démarrage rapide : Essayer Content Moderator sur le web](quick-start.md#create-a-review-team).  

> [!NOTE]
> Vous devez fournir un _ID de ressource_ avec le niveau tarifaire Content Moderator E0.


## <a name="next-steps"></a>Étapes suivantes

* [Formulaire de demande de clé gérée par le client Content Moderator](https://aka.ms/cogsvc-cmk)
* [En savoir plus sur Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
