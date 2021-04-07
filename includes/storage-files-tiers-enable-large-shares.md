---
title: Fichier include
description: Fichier include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a086aae35c9a800c6a4cfc3e872a34438bc84095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99569549"
---
Par défaut, les partages de fichiers standard peuvent couvrir jusqu’à 5 Tio seulement. Cependant, vous pouvez augmenter la limite de partage jusqu’à 100 Tio. Pour augmenter votre limite de partage, activez **Partages de fichiers volumineux** dans votre compte de stockage. Les comptes de stockage Premium (comptes de stockage *FileStorage*) ne disposent pas de l’indicateur de fonctionnalité de partage de fichiers volumineux, car tous les partages de fichiers Premium sont déjà activés pour le provisionnement jusqu’à une capacité totale de 100 Tio.

Vous pouvez uniquement activer les partages de fichiers volumineux sur des comptes de stockage standard localement redondants ou redondants dans une zone. Une fois que vous avez activé l’indicateur de fonctionnalité de partage de fichiers volumineux, vous ne pouvez pas modifier le niveau de redondance en stockage géoredondant ni géoredondant interzone.

Pour activer les partages de fichiers volumineux dans un compte de stockage existant, accédez à **Partages de fichiers** dans la table des matières du compte de stockage.
Dans ce panneau, sélectionnez **Capacité de partage**, remplacez la capacité par **100 Tio**, puis sélectionnez **Enregistrer**.

:::image type="content" source="media/storage-files-tiers-enable-large-shares/enable-lfs.png" alt-text="Capture d’écran du paramètre d’activation des partages de fichiers volumineux dans le portail Azure." lightbox="media/storage-files-tiers-enable-large-shares/increase-share-capacity.png":::

Vous pouvez également activer les partages de fichiers de 100 Tio à l’aide de l’applet de commande PowerShell [`Set-AzStorageAccount`](/powershell/module/az.storage/set-azstorageaccount) et de la commande Azure CLI [`az storage account update`](/cli/azure/storage/account#az-storage-account-update). Pour des instructions détaillées sur l’activation des partages de fichiers volumineux, consultez [Activer et créer des partages de fichiers volumineux](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Pour en savoir plus sur la création de partages de fichiers sur de nouveaux comptes de stockage, consultez [Création d’un partage de fichiers Azure](../articles/storage/files/storage-how-to-create-file-share.md).