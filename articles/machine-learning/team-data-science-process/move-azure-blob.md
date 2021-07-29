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
ms.openlocfilehash: 151375a37b80567aecf100ec3cd576882d06e3f3
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111902246"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Déplacer des données vers et depuis Stockage Blob Azure

Le processus Team Data Science nécessite que les données soient reçues ou chargées dans différents environnements de stockage pour y être traitées ou analysées de la façon la plus appropriée à chaque étape du processus.

## <a name="different-technologies-for-moving-data"></a>Différentes technologies de déplacement des données

Les articles suivants décrivent la procédure de déplacement des données vers et depuis le stockage Azure Blob en utilisant des technologies différentes.

* [Explorateur d’Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
  * L’Explorateur Stockage est un outil Microsoft gratuit qui vous permet d’exploiter les données Azure Storage sous Windows, macOS et Linux.
  * Si vous utilisez une machine virtuelle qui a été configurée avec les scripts fournis par les machines virtuelles de science des données dans Azure, cela signifie que l’Explorateur Azure Storage est déjà installé sur la machine virtuelle.
* [AZCopy](../../storage/common/storage-use-azcopy-v10.md)
  * AzCopy est un utilitaire de ligne de commande que vous pouvez utiliser pour copier des blobs ou des fichiers vers ou depuis un compte de stockage. 
* [Kit de développement logiciel (SDK) Python](../../storage/blobs/storage-quickstart-blobs-python.md)
  * Utilisez la bibliothèque de client Stockage blob Azure pour que Python déplace les objets blob.
* [Feature Pack SQL Server Integration Services (SSIS) pour Azure](/sql/integration-services/azure-feature-pack-for-integration-services-ssis)
  * SSIS fournit des composants permettant de se connecter à Azure, de transférer des données entre des sources de données Azure et des sources de données sur site et de traiter les données stockées dans Azure. Pour une présentation de scénarios canoniques utilisant SSIS pour répondre aux besoins de l’entreprise communs dans les scénarios d’intégration hybrides, reportez-vous à la section [En faire plus avec le Feature Pack SQL Server Integration Services pour Azure](https://techcommunity.microsoft.com/t5/sql-server-integration-services/doing-more-with-sql-server-integration-services-feature-pack-for/ba-p/388238).
  * Pour les supports de formation sur SSIS, consultez la [formation pratique SSIS](https://www.microsoft.com/sql-server/training-certification).
  * Pour plus d’informations sur la mise en service à l'aide de SISS pour la création des packages d'extraction, de transformation et de chargement (ETL), consultez [Didacticiel SSIS : création d’un Package ETL Simple](/sql/integration-services/ssis-how-to-create-an-etl-package).

La méthode la mieux adaptée à vos besoins dépend de votre scénario. L’article sur les [Scénarios d’analyses avancées dans Azure Machine Learning](plan-sample-scenarios.md) vous aide à déterminer les ressources dont vous avez besoin pour les différents flux de travail utilisés dans un processus d’analyse avancée.

> [!NOTE]
> Pour une présentation complète du stockage d’objets blob Azure, consultez les articles [Fonctionnalités de base des objets blob Azure](../../storage/blobs/storage-quickstart-blobs-dotnet.md) et [Service Blob Azure](/rest/api/storageservices/Blob-Service-Concepts).
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
* Pour obtenir des instructions sur la création d'un compte de stockage ainsi que des informations sur le compte et la clé, consultez [À propos des comptes Stockage Azure](../../storage/common/storage-account-create.md).