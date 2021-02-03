---
title: Créer un principal du service Azure
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: dc5297a3abb913c7bef7c1669fa319d01a0c0203
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947458"
---
## <a name="create-an-azure-service-principal"></a>Créer un principal de service Azure

Pour que votre application interagisse avec votre compte Azure, un principal de service Azure doit gérer les autorisations. Suivez les instructions de l'article [Créer un principal de service Azure](/powershell/azure/create-azure-service-principal-azureps?viewFallbackFrom=azps-3.3.0).

Lorsque vous créez un principal de service, vous constatez qu'il dispose d'une valeur de secret, d'un ID et d'un ID d'application. Enregistrez l'ID d'application et le secret à un emplacement temporaire. Vous en aurez besoin ultérieurement.
