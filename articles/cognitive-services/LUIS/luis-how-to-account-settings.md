---
title: Gérer les paramètres de votre compte dans LUIS | Microsoft Docs
description: Utilisez le site web LUIS pour gérer les paramètres de votre compte.
titleSuffix: Azure
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/08/2018
ms.author: diberry
ms.openlocfilehash: f3086f09e29664b816ba709fc5cda75d7b11d1b4
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035247"
---
# <a name="manage-account-and-authoring-key"></a>Gérer le compte et la clé de création
Les deux informations clés d’un compte LUIS sont le compte d’utilisateur et la clé de création. Vos informations de connexion sont gérées sur [account.microsoft.com](https://account.microsoft.com). Votre clé de création est gérée depuis le site web [LUIS](luis-reference-regions.md), page **Paramètres**. 

## <a name="authoring-key"></a>Clé de création

Dans la page **Paramètres**, cette unique clé de création régionale vous permet d’éditer toutes vos applications depuis le site web [LUIS](luis-reference-regions.md), ainsi que vos [API de création](https://aka.ms/luis-authoring-api). Chaque mois, pour des raisons pratiques, la clé de création est autorisée à effectuer un nombre [limité](luis-boundaries.md) de requêtes de point de terminaison. 

![Page Paramètres LUIS](./media/luis-how-to-account-settings/account-settings.png)

La clé de création peut être utilisée pour chacune de vos applications ainsi que pour toutes les applications que vous avez listées comme collaborateur.

## <a name="authoring-key-regions"></a>Régions de clé de création
La clé de création est spécifique à la [région de création](luis-reference-regions.md#publishing-regions). La clé ne fonctionne pas dans une autre région. 

## <a name="reset-authoring-key"></a>Réinitialiser la clé de création
Si votre clé de création est compromise, réinitialisez la clé. La clé est réinitialisée sur toutes vos applications depuis le site web [LUIS](luis-reference-regions.md). Si vous éditez vos applications par le biais des API de création, vous devez changer la valeur de `Ocp-Apim-Subscription-Key` pour la nouvelle clé. 

## <a name="delete-account"></a>Supprimer le compte
Consultez la page [Stockage et suppression de données](luis-concept-data-storage.md#accounts) pour savoir quelles données sont supprimées lorsque vous supprimez votre compte. 

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur votre [clé de création](luis-concept-keys.md#authoring-key). 

