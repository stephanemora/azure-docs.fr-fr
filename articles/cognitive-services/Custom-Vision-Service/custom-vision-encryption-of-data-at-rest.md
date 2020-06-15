---
title: Chiffrement Custom Vision des données au repos
titleSuffix: Azure Cognitive Services
description: Chiffrement Custom Vision des données au repos.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 85cf251db69d33f02e928eaea6a848f20ae7d923
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310289"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Chiffrement Custom Vision des données au repos

Le service Azure Custom Vision chiffre automatiquement vos données lors de leur conservation dans le cloud. Le chiffrement Custom Vision protège vos données et vous aide à répondre aux engagements de votre entreprise en matière de sécurité et de conformité.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Les clés gérées par le client sont des ressources disponibles créées après le 11 mai 2020 uniquement. Pour utiliser une clé gérée par le client (CMK, Customer-Managed Key) avec Custom Vision, vous devez créer une ressource Custom Vision. Une fois la ressource créée, vous pouvez utiliser Azure Key Vault pour configurer votre identité managée.

## <a name="regional-availability"></a>Disponibilité régionale

Les clés gérées par le client sont actuellement disponibles dans les régions suivantes :

* USA Centre Sud
* USA Ouest 2
* USA Est
* Gouvernement américain - Virginie

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir la liste complète des services qui prennent en charge CMK, consultez [Clés gérées par le client pour Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Qu’est-ce qu’Azure Key Vault ?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Formulaire de demande de clé gérée par le client Cognitive Services](https://aka.ms/cogsvc-cmk)
