---
title: Options de transfert de données vers Azure à l’aide d’une appliance | Microsoft Docs
description: Découvrez comment choisir l’appliance appropriée pour transférer des données vers Azure
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 11/12/2018
ms.author: alkohli
ms.openlocfilehash: c28eaf22d05bfda5085f9e269bda85ca0d46a7d3
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578113"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Comparer StorSimple avec les options de transfert de données Azure File Sync et Data Box Edge 
 
Ce document fournit une vue d’ensemble des options pour le transfert de données locales vers Azure, en comparant les solutions Data Box Edge, Azure File Sync (AFS) et Série StorSimple 8000.

- **[Data Box Edge](/azure/databox-online/data-box-edge-overview.md)**  : la solution Data Box Edge consiste en un appareil réseau local permettant d’échanger des données avec Azure, et disposant d’une capacité de computing en périphérie utilisant l’intelligence artificielle pour prétraiter les données durant leurs chargement. Annoncée dans le cadre de la Conférence Ignite 2018, elle est disponible en préversion publique. Data Box Gateway est une version virtuelle de l’appareil, offrant les mêmes fonctionnalités de transfert de données.
- **[Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide.md)**  : Azure File Sync permet de centraliser les partages de fichiers de votre organisation dans Azure Files, tout en conservant la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Azure File Sync transforme Windows Server en un cache rapide de votre partage de fichiers Azure. La disponibilité générale d’Azure File Sync a été annoncée précédemment en 2018.
- **[StorSimple](/azure/storsimple/storsimple-overview.md)**  : StorSimple est un appareil hybride qui aide les entreprises à consolider leur infrastructure de stockage pour le stockage principal, la protection des données, l’archivage et la récupération d’urgence sur une solution unique en s’intégrant étroitement avec Stockage Azure. Vous pouvez trouver le cycle de vie du produit pour StorSimple [ici](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).

## <a name="comparison-summary"></a>Résumé de la comparaison

|                           |StorSimple 8000   |Azure File Sync   |Data Box Edge (préversion)           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|Vue d’ensemble         |Stockage et archivage hybrides hiérarchisés|Stockage sur serveur de fichiers général avec hiérarchisation cloud et synchronisation multisite.  |Solution de stockage pour prétraiter les données et les envoyer via réseau vers Azure.        |
|Scénarios        |Serveur de fichiers, archivage et cible de sauvegarde |Serveur de fichiers, archivage (multisite)   |Transfert de données, pré-traitement de données incluant inférence de Machine Learning, IoT, archivage    |
|Computing en périphérie     |Non disponible |Non disponible |Prend en charge l’exécution de conteneurs à l’aide d’Azure IoT Edge    |
|Facteur de forme      |Appareil physique   |Agent installé sur Windows Server |Appareil physique   |
|Matériel         |Appareil physique fourni par Microsoft dans le cadre du service | Fourni par le client |Appareil physique fourni par Microsoft dans le cadre du service  |
|Format de données      |Format personnalisé   |Fichiers         |Objets blob ou fichiers    |
|Prise en charge du protocole |iSCSI          |SMB, NFS    | SMB ou NFS      |
|Tarifs          |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Étapes suivantes

- Découvrir [Azure Data Box Edge](/azure/databox-online/data-box-edge-overview) et [Azure Data Box Gateway](/azure/databox-online/data-box-gateway-overview)
- Découvrir [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide)
