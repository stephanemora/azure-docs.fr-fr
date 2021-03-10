---
title: Créer un principal du service Azure
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 2f60759fbbae2c0f712a475ef397ca260e1f0415
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473724"
---
## <a name="create-an-azure-service-principal"></a>Créer un principal de service Azure

Pour que votre application interagisse avec votre compte Azure, un principal de service Azure doit gérer les autorisations. Suivez les instructions de l'article [Créer un principal de service Azure](/powershell/azure/create-azure-service-principal-azureps).

Lorsque vous créez un principal de service, vous constatez qu'il dispose d'une valeur de secret, d'un ID et d'un ID d'application. Enregistrez l'ID d'application et le secret à un emplacement temporaire. Vous en aurez besoin ultérieurement.
