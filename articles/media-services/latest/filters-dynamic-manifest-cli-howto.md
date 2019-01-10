---
title: Utiliser l’interface CLI pour créer des filtres avec Azure Media Services | Microsoft Docs
description: Cette rubrique montre comment utiliser l’interface CLI pour créer des filtres avec Media Services.
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
ms.date: 11/26/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 471277433f0fc9a54a28baa158f1e20f1efb613f
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000512"
---
# <a name="creating-filters-with-cli"></a>Créer des filtres avec l’interface CLI 

Quand vous transmettez votre contenu à un client (événements de streaming en direct ou vidéo à la demande), le fichier manifeste de l’actif multimédia par défaut ne permet peut-être pas au client d’interagir avec le contenu comme il le voudrait. Avec Azure Media Services, vous pouvez définir des filtres de compte et d’élément multimédia à appliquer à votre contenu. Pour plus d’informations, consultez [Filtres et manifestes dynamiques](filters-dynamic-manifest-overview.md).

Cette rubrique explique comment définir un filtre pour un actif multimédia Vidéo à la demande et comment utiliser l’interface CLI dans Media Services v3 pour créer des [filtres de compte](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) et des [filtres d’actif multimédia](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="prerequisites"></a>Prérequis 

- Installez et utilisez l’interface CLI localement. Vous devez disposer d’Azure CLI 2.0 ou version ultérieure pour cet article. Exécutez `az --version` pour trouver la version qui est à votre disposition. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli). 

    Actuellement, les commandes [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) ne fonctionnent pas toutes dans Azure Cloud Shell. Il est recommandé d’utiliser l’interface CLI localement.
- [Créer un compte Media Services](create-account-cli-how-to.md). Veillez à mémoriser le nom du groupe de ressources et le nom du compte Media Services. 
- Lire [Filtres et manifestes dynamiques](filters-dynamic-manifest-overview.md).

## <a name="define-a-filter"></a>Définir un filtre 

L’exemple suivant définit les conditions de sélection de piste qui sont ajoutées au manifeste final. Ce filtre inclut toutes les pistes audio EC-3 et toutes les pistes vidéo dont la vitesse de transmission est comprise entre 0 et 1 000 000.

Pour les filtres définis dans REST, incluez l’objet JSON de wrapper « Properties ».  

```json
[
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Audio",
                "operation": "Equal"
            },
            {
                "property": "FourCC",
                "value": "EC-3",
                "operation": "NotEqual"
            }
        ]
    },
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Video",
                "operation": "Equal"
            },
            {
                "property": "Bitrate",
                "value": "0-1000000",
                "operation": "Equal"
            }
        ]
    }
]
```

## <a name="create-account-filters"></a>Créer des filtres de compte

La commande [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) ci-dessous crée un filtre de compte qui filtre les sélections de pistes [définies précédemment](#define-a-filter). 

La commande vous permet de transmettre paramètre `--tracks` facultatif contenant un JSON qui représente les sélections de pistes.  Utilisez @{file} pour charger le JSON à partir d’un fichier. Si vous utilisez l’interface de ligne de commande Azure en local, spécifiez le chemin d’accès de la totalité du fichier :


```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @c:\tracks.json
```

Si vous utilisez Azure Cloud Shell, chargez votre fichier dans Cloud Shell (rechercher le bouton de téléchargement de fichiers en haut de la fenêtre de l’interpréteur de commandes). Vous pouvez ensuite référencer le fichier comme suit :

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Voir aussi [Exemples de filtres JSON](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Créer des filtres d’actif multimédia

La commande [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) ci-dessous crée un filtre d’actif multimédia qui filtre les sélections de pistes [définies précédemment](#define-a-filter). 

> [!TIP]
> Consultez les informations sur la spécification de l’emplacement du nom de fichier dans la section précédente.

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Voir aussi [Exemples de filtres JSON](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter).

## <a name="next-step"></a>Étape suivante

[Diffuser des vidéos en streaming](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Voir aussi

[Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
