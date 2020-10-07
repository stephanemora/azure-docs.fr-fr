---
title: Créer un principal du service Azure
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 5cff103d17138608f4932e36444847437f6da236
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "89321571"
---
## <a name="create-an-azure-service-principal"></a>Créer un principal de service Azure

Pour que votre application interagisse avec votre compte Azure, un principal de service Azure doit gérer les autorisations. Suivez les instructions de l'article [Créer un principal de service Azure](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

Lorsque vous créez un principal de service, vous constatez qu'il dispose d'une valeur de secret, d'un ID et d'un ID d'application. Enregistrez l'ID d'application et le secret à un emplacement temporaire. Vous en aurez besoin ultérieurement.