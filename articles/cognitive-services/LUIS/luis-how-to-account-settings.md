---
title: Gérer le compte et les clés
titleSuffix: Language Understanding - Azure Cognitive Services
description: Les deux informations clés d’un compte LUIS sont le compte d’utilisateur et la clé de création. Vos informations de connexion sont gérées à l’adresse account.microsoft.com. Votre clé de création est gérée à partir de la page Paramètres du portail LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: d7d63ad642ab2d3b336e15dcca606077762ceb9d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58116620"
---
# <a name="manage-account-and-authoring-key"></a>Gérer le compte et la clé de création

Les deux informations clés d’un compte LUIS sont le compte d’utilisateur et la clé de création. Vos informations de connexion sont gérées sur [account.microsoft.com](https://account.microsoft.com). Votre clé de création est gérée à partir de la [LUIS](luis-reference-regions.md) portal **paramètres** page.

## <a name="authoring-key"></a>Clé de création

Cette création unique, spécifiques à une région clés, sur le **paramètres** vous permet de créer toutes vos applications à partir de la page le [LUIS](luis-reference-regions.md) portail ainsi que les [API de création](https://aka.ms/luis-authoring-api). Chaque mois, pour des raisons pratiques, la clé de création est autorisée à effectuer un nombre [limité](luis-boundaries.md) de requêtes de point de terminaison.

[Page Paramètres LUIS![](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

La clé de création peut être utilisée pour chacune de vos applications ainsi que pour toutes les applications que vous avez listées comme collaborateur.

## <a name="authoring-key-regions"></a>Régions de clé de création

La clé de création est spécifique à la [région de création](luis-reference-regions.md#publishing-regions). La clé ne fonctionne pas dans une autre région.

## <a name="reset-authoring-key"></a>Réinitialiser la clé de création

Si votre clé de création est compromise, réinitialisez la clé. La clé est réinitialisée sur toutes vos applications dans le [LUIS](luis-reference-regions.md) portail. Si vous éditez vos applications par le biais des API de création, vous devez changer la valeur de `Ocp-Apim-Subscription-Key` pour la nouvelle clé.

## <a name="delete-account"></a>Supprimer le compte

Consultez la page [Stockage et suppression de données](luis-concept-data-storage.md#accounts) pour savoir quelles données sont supprimées lorsque vous supprimez votre compte.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur votre [clé de création](luis-concept-keys.md#authoring-key).

