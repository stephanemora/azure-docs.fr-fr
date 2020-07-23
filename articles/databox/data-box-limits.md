---
title: Limites d’Azure Data Box | Microsoft Docs
description: Décrit les limites du système et les tailles recommandées pour les connexions et les composants de Microsoft Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 7d699fc47fa0a0cb57d103ff42ff17bdc0f3a82b
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86202685"
---
# <a name="azure-data-box-limits"></a>Limites d’Azure Data Box

Tenez compte de ces limites quand vous déployez et utilisez votre solution Microsoft Azure Data Box. Le tableau suivant décrit ces limites pour Data Box.

## <a name="data-box-service-limits"></a>Limites du service Data Box

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-limits"></a>Limites du service Data Box

- Le service Data Box peut stocker jusqu’à 500 millions de fichiers à des fins d’importation et d’exportation.
- Data Box prend en charge un maximum de 512 conteneurs ou partages dans le cloud. Les répertoires de niveau supérieur au sein du partage utilisateur deviennent des conteneurs ou des partages de fichiers Azure dans le cloud. 
- La capacité d’utilisation de Data Box peut être inférieure à 80 To en raison de la consommation d’espace de métadonnées ReFS.

## <a name="azure-storage-limits"></a>Limites du stockage Azure

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>Mises en garde relatives au chargement de données


### <a name="for-import-order"></a>Pour un ordre d’importation

Les mises en garde Data Box pour un ordre d’importation incluent :

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

## <a name="for-export-order"></a>Pour un ordre d’exportation

Les mises en garde Data Box pour un ordre d’exportation incluent :

- Data Box est un appareil Windows et ne prend pas en charge les noms de fichiers sensibles à la casse. Par exemple, vous pouvez disposer de deux fichiers différents dans Azure avec des noms dont seule la casse diffère. N’utilisez pas Data Box pour exporter de tels fichiers car ils seront remplacés sur l’appareil.
- En présence de balises en double dans les fichiers d’entrée ou de balises faisant référence aux mêmes données, l’exportation Data Box peut ignorer ou remplacer les fichiers. Le nombre de fichiers et la taille des données affichés sur le portail Azure peuvent différer de la taille réelle des données présentes sur l’appareil. 
- Data Box exporte des données vers un système Windows sur SMB et fait l’objet des limitations SMB pour les fichiers et les dossiers. Les fichiers et dossiers dont les noms ne sont pas pris en charge ne sont pas exportés.
- Il existe un mappage de 1:1 entre le préfixe et le conteneur.
- La limite de nom de fichier est de 1 024 caractères. Les noms de fichiers qui dépassent cette longueur ne sont pas exportés.
- Les préfixes dupliqués dans le fichier *xml* (chargé lors de la création de l’ordre) sont exportés. Les préfixes dupliqués ne sont pas ignorés.
- Les objets blob de pages et les noms de conteneurs respectent la casse. Dès lors, si la casse ne correspond pas, l’objet blob et/ou le conteneur sont introuvables.
 

## <a name="azure-storage-account-size-limits"></a>Limites de la taille du compte de stockage Azure

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Limites de taille des objets Azure

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Conventions de nommage des objets blob de blocs,des objets blob de pages et des fichiers Azure

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
