---
title: Présentation de Microsoft Azure Data Box Gateway | Microsoft Docs
description: Décrit Azure Data Box Gateway, une solution de stockage d’appliance virtuelle qui vous permet de transférer des données vers Azure
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: overview
ms.custom: ''
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 9c12674a66582ede04b4cf9d311238d61816afec
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969544"
---
# <a name="what-is-azure-data-box-gateway-preview"></a>Qu’est-ce qu’Azure Data Box Gateway (préversion) ? 

Azure Data Box Gateway est une solution de stockage qui vous permet d’envoyer en toute transparence des données vers Azure. Cet article vous fournit une vue d’ensemble de la solution Azure Data Box Gateway, ses avantages, ses principales fonctionnalités et les scénarios dans lesquels vous pouvez déployer cet appareil. 

Data Box Gateway est un appareil virtuel basé sur une machine virtuelle provisionnée dans votre hyperviseur ou environnement virtualisé. L’appareil virtuel réside dans votre environnement local et vous écrivez des données à l’aide des protocoles NFS et SMB. L’appareil transfère ensuite vos données vers l’objet blob de blocs Azure, l’objet blob de pages ou Azure Files. 

> [!IMPORTANT]
> Data Box Gateway est disponible en préversion. Veuillez lire les [conditions d’utilisation de la préversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de déployer cette solution.

## <a name="use-cases"></a>Cas d'utilisation

Data Box Gateway peut servir à transférer des données vers le cloud, par exemple pour l’archivage dans la cloud, la récupération d’urgence, ou lorsqu’il est nécessaire de traiter vos données à l’échelle du cloud. Voici les différents scénarios où Data Box Gateway peut être utilisé pour le transfert de données.

- **Archivage dans le cloud** : copiez des centaines de téraoctets de données vers Stockage Azure à l’aide Data Box Gateway, de manière sécurisée et efficace. Les données peuvent être ingérées une seule fois ou de manière continue pour les scénarios d’archivage.

- **Agrégation des données** : agrégez des données provenant de plusieurs sources dans un emplacement unique de Stockage Azure pour le traitement et l’analyse des données.  

- **Intégration à des charges de travail locales** : intégrez vos données à des charges de travail locales telles que la sauvegarde et restauration, qui utilisent le stockage cloud et nécessitent un accès local aux fichiers couramment utilisés. 

## <a name="benefits"></a>Avantages

Data Box Gateway offre les avantages suivants :

- **Transfert de données facile** : transférez des données vers/depuis Stockage Azure aussi facilement qu’avec un partage réseau local.  
- **Hautes performances** : facilite le transport des données réseau grâce à des transferts hautes performances vers et depuis Azure. 
- **Accès rapide** : met en cache les fichiers les plus récents pour un accès rapide aux fichiers locaux.  
- **Utilisation de la bande passante limitée** : les données peuvent être écrites dans Azure même lorsque le réseau est restreint afin de limiter l’utilisation pendant les heures de pointe.  

## <a name="key-capabilities"></a>Fonctionnalités clés

Data Box Gateway intègre les fonctionnalités suivantes :

|Fonctionnalité |Description  |
|---------|---------|
|Vitesse     | Transfert de données et bande passante entièrement automatisés et hautement optimisés.|
|Protocoles pris en charge     | Prise en charge des protocoles SMB et NFS standard pour l’ingestion des données. <br> Pour plus d’informations sur les versions prises en charge, consultez [Conditions requises pour Data Box Gateway](data-box-gateway-system-requirements.md).|
|Accès aux données     | Accès direct aux données à partir d’objets blob Stockage Azure et Azure Files à l’aide des API cloud pour le traitement d’autres données dans le cloud.|
|Accès rapide     | Cache local sur l’appareil pour un accès rapide aux derniers fichiers utilisés.|
|Chargement hors connexion     | Un mode déconnecté prend en charge les scénarios de chargement hors connexion.|
|Actualisation des données     | Possibilité d’actualiser les fichiers locaux avec la dernière version issue du cloud.|
|Chiffrement    | Prise en charge de BitLocker pour chiffrer les données localement et transférer de façon sécurisée des données vers le cloud via *https*       |
|Résilience     | Résilience réseau intégrée        |


## <a name="specifications"></a>Spécifications

L’appareil virtuel Data Box Gateway présente les spécifications suivantes :

| Spécifications                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Processeurs virtuels (cœurs)   | 4 minimum |            
| Mémoire  | 8 Go minimum|
| Disponibilité|Nœud unique|
| Disques| Disque du système d'exploitation : 250 Go <br> Disque de données : 2 To minimum, allocation dynamique et sauvegarde sur SSD|
| Interfaces réseau|1 ou plusieurs interfaces réseau virtuelles|
| Protocoles de partage de fichiers natifs|SMB et NFS  |
| Sécurité| Authentification pour déverrouiller l’accès à l’appareil et aux données <br> Chiffrement des données en transit à l’aide du chiffrement AES 256 bits|
| gestion| Interface utilisateur web locale : configuration initiale, diagnostics et gestion de l’alimentation de l’appareil <br> Portail Azure : gestion quotidienne des appareils Data Box Gateway       |


## <a name="components"></a>Composants

La solution Data Box Gateway se compose d’une ressource Data Box Gateway, d’un appareil virtuel Data Box Gateway et d’une interface utilisateur web locale.

* **Appareil virtuel Data Box Gateway** : un appareil basé sur une machine virtuelle provisionnée dans votre hyperviseur ou environnement virtualisé, qui vous permet d’envoyer des données à Azure. 
    
* **Ressource Data Box Gateway** : ressource du portail Azure, qui vous permet de gérer un appareil Data Box Gateway à partir d’une interface web accessible depuis divers emplacements géographiques. Utilisez la ressource Data Box Gateway pour créer et gérer des ressources, afficher et gérer les appareils et les alertes, et gérer les partages.  

    <!--![The Data Box Gateway service in Azure portal](media/data-box-overview/data-box-Gateway-service1.png)-->

    <!--For more information, go to [Use the Data Box Gateway service to administer your Data Box Gateway device](data-box-gateway-portal-ui-admin.md).-->

* **Interface utilisateur web locale Data Box** : utilisez l’interface utilisateur web locale pour exécuter des diagnostics, arrêter et redémarrer l’appareil Data Box Gateway, afficher les journaux de copie, et contactez le support Microsoft pour créer une demande de service.

    <!--![The Data Box Gateway local web UI](media/data-box-gateway-overview/data-box-gateway-local-web-ui.png)-->

    <!-- For information about using the web-based UI, go to [Use the web-based UI to administer your Data Box](data-box-gateway-portal-ui-admin.md).-->


## <a name="region-availability"></a>Disponibilité des régions

L’appareil physique Data Box, la ressource Azure et le compte de stockage cible vers lequel vous transférez des données n’ont pas besoin de se trouver dans la même région.

- **Disponibilité des ressources** : pour cette version, la ressource Data Box Edge est disponible dans les régions suivantes :
    - **United States** : Ouest des États-Unis 2 et Est des États-Unis
    - **Union européenne** : Europe de l’Ouest
    - **Asie-Pacifique** : Sud-est de l’Asie

- **Comptes de stockage de destination** : les comptes de stockage qui stockent les données sont disponibles dans toutes les régions Azure. 

    Régions où les comptes de stockage qui stockent les données Data Box doivent se trouver à proximité de l’appareil pour garantir des performances optimales. Un compte de stockage situé dans un emplacement éloigné entraîne des temps de latence longs et une baisse des performances. 


## <a name="next-steps"></a>Étapes suivantes

- Passer en revue les [Conditions requises pour le système Data Box Gateway](data-box-gateway-system-requirements.md).
- Comprendre les [limites de Data Box Gateway](data-box-gateway-limits.md).
- Déployer [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) sur le portail Azure.




