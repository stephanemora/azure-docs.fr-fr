---
title: Chiffrement Form Recognizer des données au repos
titleSuffix: Azure Cognitive Services
description: Chiffrement Form Recognizer des données au repos.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: b897de94ad8cdb628520f9386b076f762a5cc230
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537950"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Chiffrement Form Recognizer des données au repos

Azure Form Recognizer chiffre automatiquement vos données lors de leur conservation dans le cloud. Le chiffrement Form Recognizer protège vos données et vous aide à répondre aux engagements de votre entreprise en matière de sécurité et de conformité.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Les clés gérées par le client sont des ressources disponibles créées après le 11 mai 2020 uniquement. Pour utiliser une clé gérée par le client (CMK, Customer-Managed Key) avec Form Recognizer, vous devez créer une ressource Form Recognizer. Une fois la ressource créée, vous pouvez utiliser Azure Key Vault pour configurer votre identité managée.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Formulaire de demande de clé gérée par le client du service Form Recognizer](https://aka.ms/cogsvc-cmk)
* [En savoir plus sur Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


