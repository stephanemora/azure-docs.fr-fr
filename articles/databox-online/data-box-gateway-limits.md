---
title: Limites d’Azure Data Box Gateway | Microsoft Docs
description: Décrit les limites système et les tailles recommandées pour Microsoft Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/15/2019
ms.author: alkohli
ms.openlocfilehash: f01fb88bf0ea726b421111a262bdfdd68cd3d38c
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54388422"
---
# <a name="azure-data-box-gateway-limits-preview"></a>Limites d’Azure Data Box Gateway (préversion)


Tenez compte de ces limites quand vous déployez et utilisez votre solution Microsoft Azure Data Box Gateway. 

> [!IMPORTANT] 
> Data Box Gateway est disponible en préversion. Veuillez lire les [conditions d’utilisation de la préversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de déployer cette solution. 


## <a name="data-box-gateway-service-limits"></a>Limites de service Data Box Gateway

- Dans cette version, le service est disponible uniquement dans certaines régions des États-Unis, d’Europe et d’Asie-Pacifique. Pour plus d’informations, consultez [Region availability](#data-box-gateway-overview#region-availability) (Disponibilité des régions). Le compte de stockage doit être physiquement plus proche de la région où l’appareil est déployé (qui peut être différente de la zone géographique du service).
- Le déplacement d’une ressource Data Box Gateway vers un abonnement ou groupe de ressources différent n’est pas pris en charge. Pour plus d’informations, consultez [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

## <a name="data-box-gateway-device-limits"></a>Limites d’appareil Data Box Gateway

Le tableau suivant décrit les limites de l’appareil Data Box Gateway.

| Description | Valeur |
|---|---|
| Non. de fichiers par appareil |100 millions <br> La limite est d’environ 25 millions de fichiers pour 2 To d’espace disque avec une limite maximale à 100 millions |
| Non. de partages par appareil |24 |
|Taille de fichier maximale écrite sur un partage|Pour un appareil virtuel de 2 To, la taille de fichier maximale est de 500 Go. <br> La taille maximale de fichier augmente proportionnellement avec la taille du disque de données, jusqu’à atteindre la taille maximale de 5 To. |

## <a name="azure-storage-limits"></a>Limites du stockage Azure

Cette section décrit les limites du service Stockage Azure et les conventions d’affectation de noms requises pour les fichiers Azure, les objets blob de blocs Azure et les objets blob de pages Azure, telles qu’applicables au service Data Box Gateway/Data Box Edge. Examinez soigneusement les limites de stockage et suivez toutes les recommandations.

Pour les informations les plus récentes sur les limites du service de stockage Azure et les bonnes pratiques pour nommer les partages, les conteneurs et les fichiers, accédez à :

- [Affectation de noms et de références aux conteneurs](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Affectation de noms et de références aux partages](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Conventions d’objets blob de blocs et d’objets blob de pages](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> S’il existe des fichiers ou des répertoires qui dépassent les limites du service Stockage Azure, ou qui ne sont pas conformes aux conventions d’affectation de noms des fichiers/objets blob Azure, ces fichiers ou répertoires ne sont pas ingérés dans Stockage Azure par le biais du service Data Box Gateway/Data Box Edge.

## <a name="data-upload-caveats"></a>Mises en garde relatives au chargement de données

Les mises en garde suivantes s’appliquent pour les déplacements des données dans Azure.

- Nous vous suggérons de ne pas autoriser plusieurs appareils à écrire dans le même conteneur.
- Si un objet Azure existant (tel qu’un objet blob ou un fichier) présent dans le cloud a le même nom que l’objet qui est en train d’être copié, l’appareil remplace le fichier dans le cloud.
- Une hiérarchie de répertoires vides (sans fichiers) créée sous les dossiers de partage n’est pas chargée dans les conteneurs d’objets blob.
- Si vous copiez des fichiers dont la taille est supérieure à celle de l’appareil, nous vous recommandons d’utiliser *Robocopy* ou *rsync* pour éviter qu’aucun échec ne se produise.

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limites de taille d’objet et de compte de stockage Azure

Voici les limites de la taille des données qui sont copiées dans le compte de stockage. Assurez-vous que les données que vous chargez sont conformes à ces limites. Pour consulter les dernières informations relatives à ces limites, accédez à [Objectifs de mise à l’échelle du stockage Blob Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) et [Objectifs de mise à l’échelle Azure Files](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Taille des données copiées dans le compte de stockage Azure                      | Limite par défaut          |
|---------------------------------------------------------------------|------------------------|
| Objet blob de blocs et objet blob de pages                                            | 500 To par compte de stockage|


## <a name="azure-object-size-limits"></a>Limites de taille des objets Azure

Voici les tailles des objets Azure qui peuvent être écrits. Assurez-vous que tous les fichiers chargés sont en conformité avec ces limites.

| Type d’objet Azure | Limite par défaut                                             |
|-------------------|-----------------------------------------------------------|
| Objet blob de blocs        | ~ 8 To                                                 |
| Objet blob de pages         | 1 To <br> Chaque fichier chargé au format d’objet blob de pages doit être de 512 octets alignés (un multiple entier), sinon le chargement échoue. <br> Les disques VHD et VHDX sont de 512 octets alignés. |


## <a name="next-steps"></a>Étapes suivantes

- [Préparer le déploiement d’Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
