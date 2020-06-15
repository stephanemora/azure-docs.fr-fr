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
ms.openlocfilehash: 33495dd5b092cb51b3421e7204f3b529077d63b3
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309031"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Chiffrement du service Visage pour les données au repos

Le service Visage chiffre automatiquement vos données lors de leur conservation dans le cloud. Le chiffrement du service Visage protège vos données et vous aide à répondre aux engagements de votre entreprise en matière de sécurité et de conformité.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Les clés gérées par le client sont disponibles uniquement au niveau tarifaire E0. Pour demander la possibilité d’utiliser des clés gérées par le client, complétez et envoyez le [formulaire de demande de clé gérée par le client du service Visage](https://aka.ms/cogsvc-cmk). Comptez environ 3 à 5 jours ouvrables pour obtenir des nouvelles sur le statut de votre demande. Selon la demande, vous pouvez être placé dans une file d’attente et approuvé lorsque de l’espace devient disponible. Une fois approuvé pour l’utilisation d’une clé CMK avec le service Visage, vous devez créer une ressource Visage et sélectionner E0 comme niveau tarifaire. Une fois que votre ressource Visage avec le niveau tarifaire E0 est créée, vous pouvez utiliser Azure Key Vault pour configurer votre identité managée.

## <a name="regional-availability"></a>Disponibilité régionale

Les clés gérées par le client sont actuellement disponibles dans les régions suivantes :

* USA Centre Sud
* USA Ouest 2
* USA Est
* Gouvernement américain - Virginie

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir la liste complète des services qui prennent en charge les clés CMK, consultez [Clés gérées par le client pour Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Qu’est-ce qu’Azure Key Vault ?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Formulaire de demande de clé gérée par le client Cognitive Services](https://aka.ms/cogsvc-cmk)
