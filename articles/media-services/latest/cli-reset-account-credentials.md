---
title: Exemple de script Azure CLI - Réinitialiser vos informations d’identification de compte | Microsoft Docs
description: Utilisez le script Azure CLI pour réinitialiser vos informations d’identification de compte et récupérer les paramètres du fichier app.config.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/20/2019
ms.author: juliako
ms.openlocfilehash: 63f2abe7a3890efbaf4c79186467a3eb20d8afda
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382985"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Exemple Azure CLI : Réinitialiser les informations d’identification

Le script Azure CLI de cet article vous montre comment réinitialiser vos informations d’identification de compte et récupérer les paramètres du fichier app.config.

## <a name="prerequisites"></a>Prérequis

[Créer un compte Media Services](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Exemple de script

```azurecli-interactive
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup
 ```

## <a name="next-steps"></a>Étapes suivantes

* [az ams](/cli/azure/ams)
* [Réinitialiser les informations d’identification](/cli/azure/ams/account/sp#az-ams-account-sp-reset-credentials)
