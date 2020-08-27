---
title: Charger du contenu sur un actif multimédia Azure Media Services avec Azure CLI
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
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 65b0b276dcda58810f44494c14f06dd412d633f6
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719289"
---
# <a name="create-an-asset"></a>Créer une ressource

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

## <a name="using-postman"></a>Utilisation de Postman

[!INCLUDE[Create an asset with Postman](./includes/task-create-asset-postman.md)]

## <a name="net"></a>[.NET](#tab/net/)

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-dotnet.md)]

---

## <a name="next-steps"></a>Étapes suivantes

[Présentation de Media Services](media-services-overview.md)
