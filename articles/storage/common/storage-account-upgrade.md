---
title: Mise à niveau vers un compte de stockage à usage général v2 - stockage Azure | Microsoft Docs
description: Mettre à niveau pour les comptes de stockage à usage général v2.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/26/2019
ms.author: tamram
ms.openlocfilehash: 2d6a5c96bf99439520e26fc905668835944cee29
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66115647"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Mise à niveau vers un compte de stockage à usage général v2

Les comptes de stockage à usage général v2 prend en charge les dernières fonctionnalités de stockage Azure et intègrent toutes les fonctionnalités de v1 à usage général et comptes de stockage d’objets Blob. Les comptes de stockage v2 à usage général sont adaptés à la plupart des scénarios. Pour le Stockage Azure, ce sont les comptes v2 à usage général qui offrent les tarifs de capacité par gigaoctet les plus bas. En outre, le prix des transactions est l’un des plus compétitifs du secteur.

Mise à niveau vers un compte de stockage à usage général v2 à partir de votre usage général v1 ou comptes de stockage Blob est simple. Pour ce faire, vous pouvez utiliser le Portail Azure, PowerShell ou Azure CLI.

> [!IMPORTANT]
> La mise à niveau un v1 à usage général ou un compte de stockage Blob à usage général v2 est définitive et ne peut pas être annulée.

## <a name="upgrade-using-the-azure-portal"></a>Mise à niveau à l’aide du Portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Accédez à votre compte de stockage.
3. Dans la section **Paramètres**, cliquez sur **Configuration**.
4. Sous **Type de compte**, cliquez sur **Mettre à niveau**.
5. Sous **Confirmer la mise à niveau**, saisissez le nom de votre compte.
6. Cliquez sur l’option **Mettre à niveau** figurant en bas du panneau.

    ![Type de compte de mise à niveau](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

## <a name="upgrade-with-powershell"></a>Mise à niveau à l’aide de PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pour mettre à niveau un compte v1 à usage général à un compte v2 à usage général à l’aide de PowerShell, tout d’abord mettre à jour les PowerShell pour utiliser la dernière version de la **Az.Storage** module. Pour plus d’informations sur l’installation de PowerShell, consultez l’article [Installation et configuration d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Ensuite, appelez la commande suivante pour mettre à niveau le compte, en remplaçant le nom de votre groupe de ressources et de votre compte de stockage :

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

## <a name="upgrade-with-azure-cli"></a>Mise à niveau à l’aide de l’interface de ligne de commande Azure

Pour mettre à niveau un compte v1 à usage général à un compte v2 à usage général à l’aide d’Azure CLI, d’abord installer la dernière version d’Azure CLI. Pour plus d’informations sur l’installation de l’interface de ligne de commande, consultez l’article [Installer Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Ensuite, appelez la commande suivante pour mettre à niveau le compte, en remplaçant le nom de votre groupe de ressources et de votre compte de stockage :

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
```

## <a name="specify-an-access-tier-for-blob-data"></a>Spécifier un niveau d’accès pour les données blob

Les comptes v2 à usage général prennent en charge tous les services de stockage et objets de données Azure, mais les niveaux d’accès sont uniquement disponibles pour les objets blob de blocs dans le stockage d’objets blob. Lorsque vous migrez vers un compte de stockage à usage général v2, vous pouvez spécifier un niveau d’accès pour vos données d’objet blob.

Les niveaux d’accès vous permettent de choisir la solution de stockage la plus économique en fonction des modèles d’utilisation prévus. Les objets blob de blocs peuvent être stockés dans un niveau de stockage chaud, froid ou archive. Pour plus d’informations sur les niveaux d’accès, consultez [Stockage Blob Azure : Niveaux de stockage chaud, à froid et archivage](../blobs/storage-blob-storage-tiers.md).

Par défaut, un compte de stockage est créé dans le niveau d’accès à chaud, et un compte de stockage à usage général v1 est mis à niveau vers le niveau d’accès à chaud. Si vous cherchez à déterminer le niveau d’accès à utiliser suite à la mise à niveau des données, examinez votre scénario. Il existe deux scénarios utilisateur classiques pour la migration vers un compte v2 à usage général :

* Vous disposez d’un compte de stockage à usage général v1 et que vous voulez évaluer une mise à niveau vers un compte de stockage à usage général v2, avec le niveau d’accès de stockage approprié pour les données blob.
* Vous avez décidé de les utiliser un compte de stockage à usage général v2 ou déjà un et à évaluer si vous devez utiliser le niveau d’accès de stockage chaud ou froid pour les données blob.

Dans les deux cas, la première priorité doit estimer le coût de stockage, accès et d’exploitation sur vos données stockées dans un compte de stockage à usage général v2 et comparer avec vos frais actuels.

## <a name="pricing-and-billing"></a>Tarification et facturation

La mise à niveau d’un compte de stockage v1 vers un compte v2 à usage général est gratuit. La modification du niveau d’accès de stockage peut entraîner des modifications à votre facture. 

Tous les comptes de stockage utilisent un modèle de tarification pour le stockage d’objets blob basé sur le niveau de chaque objet blob. Les considérations de facturation suivantes s’appliquent à l’utilisation des comptes de stockage :

* **Coûts de stockage** : Les coûts de stockage des données varient en fonction de la quantité de données stockées et du niveau d’accès de stockage. Le coût par gigaoctet diminue à mesure que le niveau refroidit.

* **Coûts d’accès aux données** : les frais d’accès aux données augmentent à mesure que le niveau refroidit. Pour les données des niveaux d’accès de stockage froid et archive, des frais d’accès aux données en lecture vous sont facturés par gigaoctet.

* **Coûts de transaction** : il existe des frais par transaction pour tous les niveaux, augmentant à mesure que le niveau refroidit.

* **Coûts de transfert de données de géoréplication** : ces coûts s’appliquent uniquement aux comptes pour lesquels la géoréplication est configurée, notamment GRS et RA-GRS. Le transfert de données de géoréplication implique des frais par gigaoctet.

* **Coûts de transfert de données sortantes** : Transferts de données sortantes (données transférées hors d’une région Azure) entraînent la facturation pour l’utilisation de la bande passante sur une base par gigaoctet, cohérente avec les comptes de stockage à usage général.

* **Modification du niveau d’accès de stockage** : Le passage du niveau d’accès de stockage froid au niveau d’accès de stockage chaud implique des frais correspondant à la lecture de toutes les données existant dans le compte de stockage. Par ailleurs, le passage du niveau d’accès de stockage chaud au niveau d’accès de stockage froid implique des frais correspondant à l’écriture de toutes les données dans le niveau froid (comptes GPv2 uniquement).

> [!NOTE]
> Pour plus d’informations sur le modèle de tarification des comptes de stockage, consultez la page [Tarification du stockage Azure](https://azure.microsoft.com/pricing/details/storage/). Pour plus d’informations sur les frais de transfert de données sortantes, consultez la page [Détails de la tarification – Transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Estimer les coûts de vos modèles d’utilisation actuels

Pour estimer le coût de stockage et l’accès aux données d’objet blob dans un compte de stockage à usage général v2 dans un niveau particulier, d’évaluer votre modèle d’utilisation existant ou de se rapprocher de votre modèle d’utilisation attendu. En général, vous souhaitez connaître :

* Votre consommation pour le stockage d’objets blob, en gigaoctets :
    - Quelle est la quantité des données stockées dans le compte de stockage ?
    - Comment le volume de données est-il changé tous les mois ? Les nouvelles données remplacent-elles constamment les anciennes données ?
* Le principal modèle d’accès pour vos données de stockage d’objets blob :
    - Quelle est la quantité de données lues et écrites dans le compte de stockage ?
    - Combien d’opérations de lecture et d’écriture ont lieu sur les données dans le compte de stockage ?

Pour choisir le niveau d’accès le plus adapté à vos besoins, vous devez déterminer votre capacité de données d’objet blob et la manière dont ces données sont utilisées. Pour ce faire, examinez les métriques de supervision de votre compte.

### <a name="monitoring-existing-storage-accounts"></a>Analyse des comptes de stockage existants

Pour analyser vos comptes de stockage existants et rassembler ces informations, vous pouvez utiliser Azure Storage Analytics qui assure la journalisation et fournit les données de mesure d’un compte de stockage. Storage Analytics peut stocker des métriques qui comprennent les statistiques de transactions agrégées et les données de capacité relatives aux demandes adressées à un service de stockage GPv1, GPv2 et de stockage d’objet blob. Ces données sont stockées dans des tables connues dans le même compte de stockage.

Pour plus d’informations, consultez les articles [À propos des métriques de Storage Analytics](https://msdn.microsoft.com/library/azure/hh343258.aspx) et [Schéma de table de métriques Storage Analytics](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [!NOTE]
> Les comptes de stockage d’objets blob exposent le point de terminaison du service de table uniquement pour le stockage et l’accès aux métriques associées à ce compte.

Pour analyser la consommation de stockage pour le service de stockage d’objets blob, vous devez activer les métriques de capacité.
Lorsque cette option est activée, les données de capacité sont enregistrées quotidiennement pour le service blob d’un compte de stockage comme une entrée de table écrite dans la table *$MetricsCapacityBlob* dans le même compte de stockage.

Pour analyser les modèles d’accès aux données pour le service de stockage d’objets blob, vous devez activer les métriques de transaction par heure à partir de l’API. Lorsque cette option est activée, les transactions par API sont agrégées toutes les heures et enregistrées comme une entrée de table écrite dans la table *$MetricsHourPrimaryTransactionsBlob* dans le même compte de stockage. La table *$MetricsHourSecondaryTransactionsBlob* enregistre les transactions vers le point de terminaison secondaire lorsqu’il s’agit de comptes de stockage RA-GRS.

> [!NOTE]
> Si vous avez un compte de stockage à usage général dans lequel vous avez stocké des objets BLOB de pages et les disques de machine virtuelle ou les files d’attente, des fichiers ou tables, en même temps que le bloc et d’ajout de données d’objets blob, ce processus d’estimation n’est pas applicable. Les données de capacité ne distinguent pas les objets blob de blocs des autres types et le processus ne fournit pas de données de capacité pour les autres types de données. Si vous utilisez ces types, une méthodologie alternative consiste à examiner les quantités sur votre plus récente facture.

Pour avoir une bonne estimation de votre consommation de données et de votre modèle d’accès, nous vous recommandons de sélectionner pour les métriques une période de rétention représentative de votre utilisation régulière et d’extrapoler. Une option consiste à conserver les données de métriques pendant sept jours et à collecter les données chaque semaine pour les analyser à la fin du mois. Une autre option consiste à conserver les données de métriques pendant les 30 derniers jours et à collecter et analyser les données à la fin de la période de 30 jours.

Pour plus d’informations sur l’activation, la collecte et l’affichage des données de métriques, consultez [métriques de stockage analytique](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Le stockage, l’accès et le téléchargement des données d’analyse sont également facturés comme des données utilisateur standard.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Utilisation des mesures d’utilisation pour estimer les coûts

#### <a name="capacity-costs"></a>Coûts de capacité

La dernière entrée de la table de métriques de capacité *$MetricsCapacityBlob* avec la clé de ligne *'data'* affiche la capacité de stockage utilisée par les données utilisateur. La dernière entrée de la table de métriques de capacité *$MetricsCapacityBlob* avec la clé de ligne *’analytics’* affiche la capacité de stockage utilisée par les journaux d’activité d’analyse.

Cette capacité totale utilisée par les données utilisateur et les journaux d’activité d’analyse (si l’option est activée) permet ensuite d’estimer le coût de stockage des données dans le compte de stockage. La même méthode peut également être utilisée pour estimer les coûts de stockage dans les comptes de stockage GPv1.

#### <a name="transaction-costs"></a>Coûts de transaction

La somme des entrées *'TotalBillableRequests'* d’une API dans la table de métriques de transaction indique le nombre total de transactions pour cette API. *Par exemple*, le nombre total de transactions *'GetBlob'* pendant une période donnée peut être calculé par la somme du total de demandes facturables pour toutes les entrées avec la clé de ligne *'user;GetBlob'*.

Pour estimer les frais de transaction pour les comptes de stockage d’objets blob, vous devez classer les transactions en trois groupes correspondant aux trois modèles de tarification.

* Les transactions d’écriture telles que *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* et *'CopyBlob'*.
* Les transactions de suppression telles que *'DeleteBlob'* et *'DeleteContainer'*.
* Toutes les autres transactions.

Pour estimer les frais de transaction pour les comptes de stockage GPv1, vous devez regrouper toutes les transactions, quelle que soit l’opération/l’API associée.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Coûts d’accès aux données et de transfert de données de géoréplication

La quantité de données lues et écrites dans un compte de stockage n’est pas fournie par Storage Analytics mais peut être estimée en consultant la table de métriques de transaction. La somme des entrées *'TotalIngress'* d’une API dans la table de métriques de transaction indique la quantité totale de données entrantes en octets pour cette API. De même, la somme des entrées *'TotalEgress'* indique la quantité totale des données sortantes en octets.

Pour estimer les coûts d’accès aux données pour les comptes de stockage d’objets blob, vous devez classer les transactions en deux groupes.

* La quantité de données récupérées à partir du compte de stockage peut être estimée en additionnant les entrées *'TotalEgress'* pour les opérations *'GetBlob'* et *'CopyBlob'*.

* La quantité de données écrites dans le compte de stockage peut être estimée en additionnant les entrées *'TotalIngress'* pour les opérations *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* et *'AppendBlock'*.

Le coût de transfert de données de géoréplication des comptes de stockage d’objets blob peut également être calculé en estimant la quantité de données écrites lors de l’utilisation d’un compte de stockage GRS ou RA-GRS.

> [!NOTE]
> Pour obtenir un exemple plus détaillé de calcul des coûts de l’utilisation du niveau d’accès de stockage chaud ou froid, consultez l’article *« Que sont les niveaux Froid et Chaud et comment savoir lequel utiliser ? »* sur la [page relative à la tarification Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Étapes suivantes

- [Créez un compte de stockage](storage-quickstart-create-account.md)
- [Gérer des comptes de stockage Azure](storage-account-manage.md)
