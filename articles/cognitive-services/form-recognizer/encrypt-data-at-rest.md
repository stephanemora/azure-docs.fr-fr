---
title: Chiffrement Form Recognizer des données au repos
titleSuffix: Azure Applied AI Services
description: Microsoft propose des clés de chiffrement gérées par Microsoft et vous permet également de gérer vos abonnements Cognitive Services à l’aide de vos propres clés, appelées clés gérées par le client (CMK). Cet article traite du chiffrement des données au repos pour Form Recognizer et de l’activation et de la gestion de CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 16da5e11db26f416950852b0d9f5378ca2df6fab
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110374541"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Chiffrement Form Recognizer des données au repos

Azure Form Recognizer chiffre automatiquement vos données lors de leur conservation dans le cloud. Le chiffrement Form Recognizer protège vos données et vous aide à répondre aux engagements de votre organisation en matière de sécurité et de conformité.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Les clés gérées par le client sont des ressources disponibles créées après le 11 mai 2020 uniquement. Pour utiliser une clé gérée par le client (CMK, Customer-Managed Key) avec Form Recognizer, vous devez créer une ressource Form Recognizer. Une fois la ressource créée, vous pouvez utiliser Azure Key Vault pour configurer votre identité managée.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Formulaire de demande de clé gérée par le client du service Form Recognizer](https://aka.ms/cogsvc-cmk)
* [En savoir plus sur Azure Key Vault](../../key-vault/general/overview.md)