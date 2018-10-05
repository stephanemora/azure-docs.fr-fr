---
title: Mise à niveau vers un compte de stockage v2 à usage général - Stockage Azure | Microsoft Docs
description: Mettez à niveau vers des comptes de stockage v2 à usage général.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/11/2018
ms.author: tamram
ms.openlocfilehash: 6e77c4836531a7efd0b52b9a411ac40ff6a613fa
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224492"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Mettre à niveau vers un compte de stockage v2 à usage général

Les comptes de stockage v2 à usage général prennent en charge les dernières fonctionnalités du Stockage Azure, et intègrent toutes les fonctionnalités des comptes de stockage v1 à usage général et des comptes de stockage d’objets blob. Les comptes de stockage v2 à usage général sont adaptés à la plupart des scénarios. Pour le Stockage Azure, ce sont les comptes v2 à usage général qui offrent les tarifs de capacité par gigaoctet les plus bas. En outre, le prix des transactions est l’un des plus compétitifs du secteur.

La mise à niveau vers un compte de stockage v2 à usage général à partir d’un compte de stockage v1 à usage général ou d’un compte de stockage d’objets blob est une opération très simple. Pour ce faire, vous pouvez utiliser le Portail Azure, PowerShell ou Azure CLI. La mise à niveau d’un compte est définitive et peut entraîner la facturation de frais.

## <a name="upgrade-using-the-azure-portal"></a>Mise à niveau à l’aide du Portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Accédez à votre compte de stockage.
3. Dans la section **Paramètres**, cliquez sur **Configuration**.
4. Sous **Type de compte**, cliquez sur **Mettre à niveau**.
5. Sous **Confirmer la mise à niveau**, saisissez le nom de votre compte. 
6. Cliquez sur l’option **Mettre à niveau** figurant en bas du panneau.

## <a name="upgrade-with-powershell"></a>Mise à niveau à l’aide de PowerShell

Pour mettre à niveau un compte v1 à usage général vers un compte v2 à usage général à l’aide de PowerShell, commencez par mettre à jour PowerShell afin d’utiliser la dernière version du module **AzureRm.Storage**. Pour plus d’informations sur l’installation de PowerShell, consultez l’article [Installation et configuration d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). 

Ensuite, appelez la commande suivante pour mettre à niveau le compte, en remplaçant le nom de votre groupe de ressources et de votre compte de stockage :

```powershell
Set-AzureRmStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

## <a name="upgrade-with-azure-cli"></a>Mise à niveau à l’aide de l’interface de ligne de commande Azure

Pour mettre à niveau un compte v1 à usage général vers un compte v2 à usage général à l’aide d’Azure CLI, commencez par installer la dernière version d’Azure CLI. Pour plus d’informations sur l’installation de l’interface de ligne de commande, consultez l’article [Installer Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

Ensuite, appelez la commande suivante pour mettre à niveau le compte, en remplaçant le nom de votre groupe de ressources et de votre compte de stockage :

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
``` 

## <a name="specify-an-access-tier-for-blob-data"></a>Spécifier un niveau d’accès pour les données blob

Les comptes v2 à usage général prennent en charge tous les services de stockage et objets de données Azure, mais les niveaux d’accès sont uniquement disponibles pour les objets blob de blocs dans le stockage d’objets blob. Lorsque vous passez à un compte de stockage v2 à usage général, vous pouvez spécifier un niveau d’accès pour vos données blob. 

Les niveaux d’accès vous permettent de choisir la solution de stockage la plus économique en fonction des modèles d’utilisation prévus. Les objets blob de blocs peuvent être stockés dans un niveau de stockage chaud, froid ou archive. Pour plus d’informations, consultez l’article [Stockage Blob Azure : niveaux de stockage chaud, froid et archive](../blobs/storage-blob-storage-tiers.md).

Par défaut, un compte de stockage est créé dans le niveau d’accès chaud, et un compte de stockage v1 à usage général est mis à niveau vers le niveau d’accès chaud. Si vous cherchez à déterminer le niveau d’accès à utiliser suite à la mise à niveau des données, examinez votre scénario. Les utilisateurs se trouvent généralement face à deux scénarios lors de la migration vers un compte v2 à usage général :

* Vous disposez d’un compte de stockage v1 à usage général et envisagez de passer à un compte de stockage v2 à usage général, accompagné du niveau de stockage adapté pour les données blob.
* Vous souhaitez utiliser un compte de stockage v2 à usage général ou vous disposez déjà d’un tel compte et souhaitez savoir si vous devez utiliser le niveau de stockage chaud ou froid pour vos données blob.

Dans les deux cas, la priorité est d’estimer les coûts de stockage, d’exploitation des données stockées dans un compte de stockage v2 à usage général et d’accès à celles-ci pour les comparer avec vos frais actuels.

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Estimer les coûts de vos modèles d’utilisation actuels

Pour estimer le coût de stockage et d’accès aux données blob stockées dans un compte de stockage v2 à usage général (et avec un niveau particulier), vous devez évaluer votre modèle d’utilisation existant ou faire une estimation du modèle d’utilisation souhaité. En général, vous souhaitez connaître :

* Votre consommation pour le stockage d’objets blob, en gigaoctets :
    - Quelle est la quantité des données stockées dans le compte de stockage ?
    - Comment le volume de données est-il changé tous les mois ? Les nouvelles données remplacent-elles constamment les anciennes données ?
* Le principal modèle d’accès pour vos données de stockage d’objets blob :
    - Quelle est la quantité de données lues et écrites dans le compte de stockage ? 
    - Combien d’opérations de lecture et d’écriture ont lieu sur les données dans le compte de stockage ?

Pour choisir le niveau d’accès le plus adapté à vos besoins, vous devrez certainement déterminer la capacité actuelle requise par vos données blob et la manière dont ces données sont utilisées. 

Pour collecter des données d’utilisation pour votre compte de stockage avant la migration, vous pouvez surveiller le compte de stockage à l’aide d’[Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Azure Monitor effectue la journalisation et fournit des données de métriques pour les services Azure, y compris le Stockage Azure. 

Pour surveiller les données de consommation pour les objets blob dans votre compte de stockage, activez les métriques de capacité dans Azure Monitor. Les métriques de capacité enregistrent des données à propos de la quantité de stockage utilisée quotidiennement par les objets blob dans votre compte. Les métriques de capacité peuvent servir à estimer le coût du stockage de données dans le compte de stockage. Pour connaître la tarification de la capacité de stockage d’objets blob pour chaque type de compte, consultez l’article [Tarification des objets blob de bloc](https://azure.microsoft.com/pricing/details/storage/blobs/).

Pour surveiller les modèles d’accès aux données pour le service de stockage d’objets blob, activez les métriques de transaction dans Azure Monitor. Vous pouvez filtrer les différentes opérations du Stockage Azure pour estimer la fréquence d’appel de chacun d’entre eux. Pour connaître la tarification des différents types de transactions par bloc et ajouter des objets blob pour chaque type de compte, consultez l’article [Tarification des objets blob de bloc](https://azure.microsoft.com/pricing/details/storage/blobs/).  

Pour plus d’informations sur la collecte des métriques dans Azure Monitor, consultez l’article [Métriques du Stockage Azure dans Azure Monitor](storage-metrics-in-azure-monitor.md).

## <a name="next-steps"></a>Étapes suivantes

- [Créez un compte de stockage](storage-quickstart-create-account.md)
- [Gérer des comptes de stockage Azure](storage-account-manage.md)