---
title: Charger du contenu dans une interface CLI de ressource
description: Le script Azure CLI de cette rubrique montre comment créer un élément multimédia Azure Media Services sur lequel charger du contenu.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/16/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3923394e76d5ce4001d3652ae9cbc263df08e4e2
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545613"
---
# <a name="create-an-asset"></a>Créer une ressource

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Cet article explique comment créer une ressource Media Services.  Vous allez utiliser une ressource pour stocker le contenu multimédia pour l’encodage et la diffusion en continu.  Pour en savoir plus sur les ressources Media Services, consultez [Actifs multimédias dans Azure Media Service v3](assets-concept.md)

## <a name="prerequisites"></a>Prérequis

Suivez les étapes décrites dans [Créer un compte Media Services](./create-account-howto.md) pour créer le compte Media Services et le groupe de ressources nécessaires à la création d’un élément multimédia.

## <a name="methods"></a>Méthodes

## <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="example-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="rest"></a>[REST](#tab/rest/)

### <a name="using-rest"></a>Utilisation de REST

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-rest.md)]

### <a name="using-curl"></a>Utilisation de cURL

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-curl.md)]

## <a name="net"></a>[.NET](#tab/net/)

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-dotnet.md)]

---

## <a name="next-steps"></a>Étapes suivantes

[Présentation de Media Services](media-services-overview.md)
