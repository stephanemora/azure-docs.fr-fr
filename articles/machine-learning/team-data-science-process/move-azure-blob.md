---
title: Déplacer des données vers et depuis Stockage Blob Azure - Processus TDSP (Team Data Science Process)
description: Déplacez des données vers et depuis le stockage d’objets blob Azure à l’aide de l’explorateur de stockage Azure, AzCopy, Python et SSIS.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fc58651bcb3b266b981fb953fd7341427d47fb2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "76717572"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Déplacer des données vers et depuis Stockage Blob Azure

Le processus Team Data Science nécessite que les données soient reçues ou chargées dans différents environnements de stockage pour y être traitées ou analysées de la façon la plus appropriée à chaque étape du processus.

## <a name="different-technologies-for-moving-data"></a>Différentes technologies de déplacement des données

Les articles suivants décrivent la procédure de déplacement des données vers et depuis le stockage Azure Blob en utilisant des technologies différentes.

* [Explorateur d’Azure Storage](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AZCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

La méthode la mieux adaptée à vos besoins dépend de votre scénario. L’article sur les [Scénarios d’analyses avancées dans Azure Machine Learning](plan-sample-scenarios.md) vous aide à déterminer les ressources dont vous avez besoin pour les différents flux de travail utilisés dans le processus d’analyse avancée.

> [!NOTE]
> Pour une présentation complète du stockage d’objets blob Azure, consultez les articles [Fonctionnalités de base des objets blob Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) et [Service Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="using-azure-data-factory"></a>Utilisation d’Azure Data Factory

Comme alternative, vous pouvez utiliser [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) pour : 

* créer et planifier un pipeline qui télécharge les données à partir du stockage d’objets blob Azure, 
* transférer ces données vers un service web Azure Machine Learning publié, 
* recevoir les résultats d’analyse prédictifs, et 
* télécharger les résultats dans le stockage. 

Pour plus d’informations, consultez la page [Créer des pipelines prédictifs à l’aide d’Azure Data Factory et Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Prérequis
Cet article suppose que vous disposez d’un abonnement Azure, d’un compte de stockage et de la clé de stockage correspondante pour ce compte. Avant de charger ou télécharger des données, vous devez connaître le nom et la clé de votre compte Stockage Azure.

* Pour configurer un abonnement Azure, consultez [Essai gratuit pendant un mois](https://azure.microsoft.com/pricing/free-trial/).
* Pour obtenir des instructions sur la création d'un compte de stockage ainsi que des informations sur le compte et la clé, consultez [À propos des comptes Stockage Azure](../../storage/common/storage-create-storage-account.md).

