---
title: Chiffrement des données au repos du service Personalizer
titleSuffix: Azure Cognitive Services
description: Chiffrement des données au repos du service Personalizer.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 10eb627a340b45c93b2cfb2973e294d8d5d7c7e5
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84307806"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>Chiffrement des données au repos du service Personalizer

Le service Personalizer chiffre automatiquement vos données quand celles-ci sont conservées dans le cloud. Le chiffrement du service Personalizer protège vos données et vous aide à répondre aux engagements de votre entreprise en matière de sécurité et de conformité.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Les clés gérées par le client sont disponibles uniquement au niveau tarifaire E0. Pour demander la possibilité d’utiliser des clés gérées par le client, complétez et envoyez le [formulaire de demande de clé gérée par le client du service Personalizer](https://aka.ms/cogsvc-cmk). Comptez environ 3 à 5 jours ouvrables pour obtenir des nouvelles sur le statut de votre demande. Selon la demande, vous pouvez être placé dans une file d’attente et approuvé lorsque de l’espace devient disponible. Une fois approuvé pour l’utilisation d’une clé CMK avec le service Personalizer, vous devez créer une ressource Personalizer et sélectionner E0 comme niveau tarifaire. Une fois que votre ressource Personalizer avec le niveau tarifaire E0 est créée, vous pouvez utiliser Azure Key Vault pour configurer votre identité managée.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Formulaire de demande de clé gérée par le client du service Personalizer](https://aka.ms/cogsvc-cmk)
* [En savoir plus sur Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
