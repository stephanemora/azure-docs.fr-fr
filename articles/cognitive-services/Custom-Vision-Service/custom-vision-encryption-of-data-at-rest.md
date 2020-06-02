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
ms.openlocfilehash: 287344b325237843a549973ae61f569eae6dac93
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202228"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Chiffrement Custom Vision des données au repos

Le service Azure Custom Vision chiffre automatiquement vos données lors de leur conservation dans le cloud. Le chiffrement Custom Vision protège vos données et vous aide à répondre aux engagements de votre entreprise en matière de sécurité et de conformité.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Les clés gérées par le client sont des ressources disponibles créées après le 11 mai 2020 uniquement. Pour utiliser une clé gérée par le client (CMK, Customer-Managed Key) avec Custom Vision, vous devez créer une ressource Custom Vision. Une fois la ressource créée, vous pouvez utiliser Azure Key Vault pour configurer votre identité managée.

### <a name="regional-availability"></a>Disponibilité régionale

Les clés gérées par le client sont actuellement disponibles dans les régions suivantes :

* USA Centre Sud
* USA Ouest 2
* USA Est
* Gouvernement américain - Virginie

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Formulaire de demande de clé gérée par le client du service Custom Vision](https://aka.ms/cogsvc-cmk)
* [En savoir plus sur Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


