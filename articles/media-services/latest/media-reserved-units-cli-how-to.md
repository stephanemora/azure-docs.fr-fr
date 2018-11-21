---
title: Mise à l’échelle des unités réservées au multimédia - Azure | Microsoft Docs
description: Cette rubrique est une présentation de la mise à l’échelle du traitement multimédia avec Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: db1915f23c33b5cc0d504f8fcc21b9533228247f
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634394"
---
# <a name="scaling-media-processing"></a>Mise à l’échelle du traitement multimédia

Azure Media Services vous permet de mettre à l’échelle le traitement multimédia dans votre compte en gérant les unités réservées au multimédia (MRU). Pour une présentation détaillée, consultez [Mise à l’échelle du traitement multimédia](../previous/media-services-scale-media-processing-overview.md). Cet article explique comment utiliser [l’interface CLI Media Services v3](https://aka.ms/ams-v3-cli-ref) pour mettre à l’échelle vos MRU.

> [!IMPORTANT]
> Passez en revue les considérations décrites dans [cette section](#considerations).  
> 
>

## <a name="prerequisites"></a>Prérequis 

- Installez et utilisez l’interface CLI localement. Vous devez disposer d’Azure CLI 2.0 ou version ultérieure pour cet article. Exécutez `az --version` pour trouver la version qui est à votre disposition. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli). 

    Actuellement, les commandes [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) ne fonctionnent pas toutes dans Azure Cloud Shell. Il est recommandé d’utiliser l’interface CLI localement.

- [Créer un compte Media Services](create-account-cli-how-to.md).

## <a name="scale-media-reserved-units-with-cli"></a>Mettre à l’échelle les unités réservées au multimédia avec l’interface CLI

La commande [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) suivante définit les unités réservées au multimédia sur le compte « amsaccount » à l’aide des paramètres **count** et **type**.

```azurecli
az account set mru -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="considerations"></a>Considérations

- Pour les travaux d’analyse audio et vidéo déclenchés par Media Services v3 ou Video Indexer, nous vous recommandons de provisionner votre compte avec des MRU 10 S3.
- Si vous avez besoin de plus de 10 MRU S3, ouvrez un ticket de support à l’aide du [Portail Azure](https://portal.azure.com/).

## <a name="billing"></a>Facturation

Vous êtes facturé en fonction du nombre, du type et de la durée pendant laquelle les MRU sont provisionnées dans votre compte. Des frais s’appliquent, que vous exécutiez des tâches ou non. Pour plus d’informations, consultez la section FAQ de la page [Tarification Media Services](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="next-step"></a>Étape suivante

[Analyser des vidéos](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Voir aussi

[Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
