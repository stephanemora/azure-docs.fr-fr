---
title: Chiffrement Custom Vision des données au repos
titleSuffix: Azure Cognitive Services
description: Microsoft propose des clés de chiffrement gérées par Microsoft et vous permet également de gérer vos abonnements Cognitive Services à l’aide de vos propres clés, appelées clés gérées par le client (CMK). Cet article traite du chiffrement des données au repos pour Custom Vision et de l’activation et de la gestion de CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 39257419f179bdce8c94f2ddb3a7cd8f5ac2d34f
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077753"
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

* Pour obtenir la liste complète des services qui prennent en charge les clés CMK, consultez [Clés gérées par le client pour Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Qu’est-ce qu’Azure Key Vault ?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Formulaire de demande de clé gérée par le client Cognitive Services](https://aka.ms/cogsvc-cmk)
