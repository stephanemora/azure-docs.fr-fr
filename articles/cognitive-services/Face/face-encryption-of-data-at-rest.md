---
title: Chiffrement du service Visage pour les données au repos
titleSuffix: Azure Cognitive Services
description: Chiffrement du service Visage pour les données au repos.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 1e0275c91b2243132650be7af256071589091c4b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201944"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Chiffrement du service Visage pour les données au repos

Le service Visage chiffre automatiquement vos données lors de leur conservation dans le cloud. Le chiffrement du service Visage protège vos données et vous aide à répondre aux engagements de votre entreprise en matière de sécurité et de conformité.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Les clés gérées par le client sont disponibles uniquement au niveau tarifaire E0. Pour demander la possibilité d’utiliser des clés gérées par le client, complétez et envoyez le [formulaire de demande de clé gérée par le client du service Visage](https://aka.ms/cogsvc-cmk). Comptez environ 3 à 5 jours ouvrables pour obtenir des nouvelles sur le statut de votre demande. Selon la demande, vous pouvez être placé dans une file d’attente et approuvé lorsque de l’espace devient disponible. Une fois approuvé pour l’utilisation d’une clé CMK avec le service Visage, vous devez créer une ressource Visage et sélectionner E0 comme niveau tarifaire. Une fois que votre ressource Visage avec le niveau tarifaire E0 est créée, vous pouvez utiliser Azure Key Vault pour configurer votre identité managée.

### <a name="regional-availability"></a>Disponibilité régionale

Les clés gérées par le client sont actuellement disponibles dans les régions suivantes :

* USA Centre Sud
* USA Ouest 2
* USA Est
* Gouvernement américain - Virginie

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Formulaire de demande de clé gérée par le client du service Visage](https://aka.ms/cogsvc-cmk)
* [En savoir plus sur Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


