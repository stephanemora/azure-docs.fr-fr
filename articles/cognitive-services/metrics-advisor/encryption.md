---
title: Chiffrement du service Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Chiffrement des données au repos du service Metrics Advisor.
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 782314b39a3ab6deda7011b38d75778e5441b32f
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111891191"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>Chiffrement des données au repos du service Metrics Advisor

Le service Metrics Advisor chiffre automatiquement vos données quand celles-ci sont conservées dans le cloud. Le chiffrement du service Metrics Advisor protège vos données et vous aide à répondre aux engagements de votre entreprise en matière de sécurité et de conformité.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Les clés gérées par le client sont disponibles uniquement au niveau tarifaire E0. Pour demander la possibilité d’utiliser des clés gérées par le client, complétez et envoyez le [formulaire de demande de clé gérée par le client du service Metrics Advisor](https://aka.ms/cogsvc-cmk). Comptez environ 3 à 5 jours ouvrables pour obtenir des nouvelles sur le statut de votre demande. Selon la demande, vous pouvez être placé dans une file d’attente et approuvé lorsque de l’espace devient disponible. Une fois approuvé pour l’utilisation d’une clé gérée par le client avec le service Metrics Advisor, vous devez créer une ressource Metrics Advisor et sélectionner E0 comme Niveau tarifaire. Une fois que votre ressource Metrics Advisor avec le niveau tarifaire E0 est créée, vous pouvez utiliser Azure Key Vault pour configurer votre identité managée.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Formulaire de demande de clé gérée par le client du service Metrics Advisor](https://aka.ms/cogsvc-cmk)
* [En savoir plus sur Azure Key Vault](../../key-vault/general/overview.md)