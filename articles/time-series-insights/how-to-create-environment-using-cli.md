---
title: Créer un environnement Azure Time Series Insights Gen2 à l’aide de l’interface Azure CLI – Azure Time Series Insights Gen2 | Microsoft Docs
description: Découvrez comment configurer un environnement dans Azure Time Series Insights Gen2 à l’aide de l’interface Azure CLI.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 150bf38ebb248f15bf1ed82186c16b6b3f7ac40b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728723"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-cli"></a>Créer un environnement Azure Time Series Insights Gen2 à l’aide de l’interface Azure CLI

Ce document vous explique comment créer un nouvel environnement Time Series Insights Gen2.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prérequis

* Créer un compte de stockage Azure pour le [magasin de stockage froid](./concepts-storage.md#cold-store) de votre environnement. Ce compte sert à la conservation à long terme et à l’analyse des données historiques.

> [!NOTE]
> Dans votre code, remplacez `mytsicoldstore` par un nom unique pour votre compte de stockage froid.

Commencez par créer le compte de stockage :

```azurecli-interactive
storage=mytsicoldstore
rg=-my-resource-group-name
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv
```

## <a name="creating-the-environment"></a>Création de l’environnement

Maintenant que le compte de stockage est créé et que son nom et sa clé de gestion sont affectés aux variables, exécutez la commande ci-dessous pour créer l’environnement Azure Time Series Insights :

> [!NOTE]
> Dans votre code, remplacez les éléments suivants par des noms uniques pour votre scénario :
>
> * `my-tsi-env` par le nom de votre environnement.
> * `my-ts-id-prop` par le nom de votre propriété ID Time Series.

> [!IMPORTANT]
> L’ID Time Series de votre environnement est semblable à une clé de partition de base de données. L’ID Time Series sert également de clé principale pour votre modèle Time Series.
>
> Pour plus d’informations, consultez [Meilleures pratiques pour choisir un ID Time Series](./how-to-select-tsid.md).

```azurecli-interactive
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=my-ts-id-prop type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

## <a name="remove-an-azure-time-series-insights-environment"></a>Supprimer un environnement Azure Time Series Insights

Vous pouvez utiliser l’interface Azure CLI pour supprimer une ressource individuelle, telle qu’un environnement Time Series Insights, ou supprimer un groupe de ressources et toutes ses ressources, y compris les environnements Time Series Insights.

Pour [supprimer un environnement Time Series Insights](/cli/azure/ext/timeseriesinsights/tsi/environment#ext_timeseriesinsights_az_tsi_environment_delete), exécutez la commande suivante :

```azurecli-interactive
az tsi environment delete --name "my-tsi-env" --resource-group $rg
```

Pour [supprimer le compte de stockage](/cli/azure/storage/account#az_storage_account_delete), exécutez la commande suivante :

```azurecli-interactive
az storage account delete --name $storage --resource-group $rg
```

Pour [supprimer un groupe de ressources](/cli/azure/group#az-group-delete) et toutes ses ressources, exécutez la commande suivante :

```azurecli-interactive
az group delete --name $rg
```

## <a name="next-steps"></a>Étapes suivantes

* Découvrez les [Sources d'événements d'ingestion de streaming](./concepts-streaming-ingestion-event-sources.md) pour votre environnement Azure Time Series Insights Gen2.
* Découvrez comment vous connecter à un [IoT Hub](./how-to-ingest-data-iot-hub.md)
