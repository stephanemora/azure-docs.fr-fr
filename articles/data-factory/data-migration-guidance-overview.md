---
title: Utiliser Azure Data Factory pour migrer des données de votre lac de données et de votre entrepôt de données vers Azure | Microsoft Docs
description: Utilisez Azure Data Factory pour migrer des données de votre lac de données et de votre entrepôt de données vers Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 7/30/2019
ms.openlocfilehash: 937a076b3e0e3c5170779d3449776f0aa1cf5b49
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259002"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Utilisez Azure Data Factory pour migrer des données de votre lac de données ou de votre entrepôt de données vers Azure 

Azure Data Factory peut être votre outil de migration des données lorsque vous souhaitez migrer votre lac de données ou votre entrepôt de données d’entreprise (EDW) vers Azure. La migration du lac de données et la migration de l’entrepôt de données sont liées aux scénarios suivants : 

- Migration de charges de travail Big Data depuis AWS S3, système de fichiers local Hadoop sur Azure. 
- Migration EDW d’Oracle Netezza, Teradata, AWS Redshift vers Azure. 

Azure Data Factory peut déplacer des PB de données pour la migration d’un lac de données et des dizaines de données To pour la migration de l’entrepôt de données. 

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Pourquoi Azure Data Factory peut être utilisé pour la migration de données 

- Azure Data Factory peut facilement augmenter la quantité de puissance nécessaire pour déplacer efficacement des données serverless et fournir une résilience et une évolutivité élevées, tout en ne vous faisant payer que pour ce que vous utilisez.  
  - Azure Data Factory n’a aucune limitation sur le volume de données et le nombre de fichiers.
  - Azure Data Factory peut utiliser votre réseau et la bande passante de stockage dans leur intégralité pour obtenir le débit de déplacement de données le plus élevé dans votre environnement.   
  - Azure Data Factory suit la stratégie de paiement à l’utilisation, de sorte que vous ne payez que pour le moment où vous utilisez Azure Data Factory pour effectuer la migration des données vers Azure.  
- Azure Data Factory a la possibilité d’effectuer une charge historique unique et une charge incrémentielle planifiée. 
- Azure Data Factory utilise Azure IR pour déplacer des données entre des points de terminaison de lac/entrepôt de données accessibles au public, ou utiliser le runtime d’intégration auto-hébergé pour déplacer des données pour des points de terminaison de lac/entrepôt de données au sein d’un réseau virtuel ou derrière un pare-feu. 
- Azure Data Factory offre une sécurité à l’échelle de l’entreprise : utilisez MSI ou l’identité de service pour une intégration de service à service sécurisée, ou bien tirez parti des Azure Key Vault pour la gestion des informations d’identification. 
- Azure Data Factory fournit une expérience de création sans code et un tableau de bord de supervision intégré enrichi.  

## <a name="online-vs-offline-data-migration"></a>Migration des données en ligne et hors connexion

Azure Data Factory est un outil de migration de données en ligne classique pour transférer des données sur un réseau (Internet, ER ou VPN), sur lequel la migration des données hors connexion permet aux utilisateurs d’expédier physiquement des appareils de transfert de données de votre organisation vers le centre de données Azure.  

Il existe trois points importants à prendre en compte lorsque vous devez choisir entre migrer des données en ligne ou hors connexion :  

- La taille des données à migrer. 
- La bande passante réseau. 
- La fenêtre de migration.   

Si vous souhaitez effectuer la migration des données dans les deux semaines (fenêtre de migration), vous pouvez voir une ligne de coupe dans l’image ci-dessous pour indiquer quand il est préférable d’utiliser l’outil de migration en ligne (Azure Data Factory) avec une taille de données et une bande passante réseau différentes.   

![en ligne et hors connexion](media/data-migration-guidance-overview/online-offline.png)

> [!NOTE]
> La migration de données en ligne vous permet de charger à la fois les données historiques et les flux incrémentiels avec un seul outil.  En procédant ainsi, les données peuvent être synchronisées entre les magasins existants et nouveaux au cours de la totalité de la fenêtre de migration, ce qui vous permet de reconstruire votre logique ETL sur le nouveau magasin avec les données actualisées. 


## <a name="next-steps"></a>Étapes suivantes

- [Migrer des données d’AWS S3 vers Azure](data-migration-guidance-s3-azure-storage.md)
- [Migrer des données du cluster Hadoop local vers Azure](data-migration-guidance-hdfs-azure-storage.md)
- [Migrer des données du serveur Netezza local vers Azure](data-migration-guidance-netezza-azure-sqldw.md)
