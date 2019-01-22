---
title: Créer un compte Media Services avec l’interface de ligne de commande Azure – Azure | Microsoft Docs
description: Suivez les étapes de ce démarrage rapide pour créer un compte Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 01/15/2019
ms.author: juliako
ms.openlocfilehash: 9c026eb9a74cbba2ff188a3f08be625043ede474
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352051"
---
# <a name="create-an-azure-media-services-account"></a>Créer un compte Azure Media Services

Pour commencer à chiffrer, coder, analyser, gérer et diffuser en continu du contenu multimédia dans Azure, vous devez créer un compte Media Services. En créant un compte Media Services, vous créez aussi un compte de stockage associé (ou utilisez un compte existant).  

Le compte Media Services et le compte de stockage associé doivent faire partie du même centre de données et du même groupe de ressources.

Cet article décrit les étapes à suivre pour créer un compte Azure Media Services à l’aide d’Azure CLI.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure actif. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
- Installez et utilisez l’interface CLI localement. Vous devez disposer d’Azure CLI 2.0 ou version ultérieure pour cet article. Exécutez `az --version` pour trouver la version qui est à votre disposition. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli). 

    Actuellement, les commandes [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) ne fonctionnent pas toutes dans Azure Cloud Shell. Il est recommandé d’utiliser l’interface CLI localement.

## <a name="set-the-azure-subscription"></a>Configurer l’abonnement Azure

Dans la commande suivante, fournissez l’ID d’abonnement Azure que vous souhaitez utiliser pour le compte Media Services. Vous pouvez afficher la liste des abonnements auxquels vous avez accès à en accédant à [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>Étapes suivantes

[Diffuser un fichier](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>Voir aussi

[Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

