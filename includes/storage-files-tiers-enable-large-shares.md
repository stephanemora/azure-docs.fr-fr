---
title: Fichier include
description: Fichier include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cd7b889560acbe484581f065b641375c222f7ca8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536480"
---
Par défaut, les partages de fichiers standard peuvent couvrir jusqu’à 5 Tio seulement, même si la limite de partage peut être augmentée jusqu’à 100 Tio. Pour ce faire, la fonctionnalité de *partage de fichiers volumineux* doit être activée au niveau du compte de stockage. Les comptes de stockage Premium (comptes de stockage *FileStorage*) ne disposent pas de l’indicateur de fonctionnalité de partage de fichiers volumineux, car tous les partages de fichiers Premium sont déjà activés pour le provisionnement jusqu’à la capacité totale de 100 Tio.

Vous pouvez uniquement activer les partages de fichiers volumineux sur des comptes de stockage standard localement redondants ou redondants dans une zone. Une fois que vous avez activé l’indicateur de fonctionnalité de partage de fichiers volumineux, vous ne pouvez pas modifier le niveau de redondance en stockage géoredondant ni géoredondant interzone.

Pour activer les partages de fichiers volumineux sur un compte de stockage existant, accédez à la vue **Configuration** dans la table des matières du compte de stockage, puis définissez le commutateur à bascule de partage de fichiers volumineux sur Activé :

![Capture d’écran du commutateur à bascule de partage de fichiers volumineux activé dans le portail Azure](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

Vous pouvez également activer les partages de fichiers de 100 Tio à l’aide de l’applet de commande PowerShell [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) et de la commande Azure CLI [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update). Pour des instructions détaillées sur l’activation des partages de fichiers volumineux, consultez [Activer et créer des partages de fichiers volumineux](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Pour en savoir plus sur la création de partages de fichiers sur de nouveaux comptes de stockage, consultez [Création d’un partage de fichiers Azure](../articles/storage/files/storage-how-to-create-file-share.md).
