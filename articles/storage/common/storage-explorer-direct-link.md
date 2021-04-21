---
title: Lien direct Explorateur Stockage Azure | Microsoft Docs
description: Documentation relative au lien direct Explorateur Stockage Azure
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 121df1e1c3ab6d0741d3e4da1144a86938da70ed
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582039"
---
# <a name="azure-storage-explorer-direct-link"></a>Lien direct Explorateur Stockage Azure

Sur Windows et macOS, Explorateur Stockage prend en charge les URI avec le protocole `storageexplorer://`. Ces URI sont appelés des liens directs. Un lien direct pointe vers une ressource de stockage Azure dans Explorateur Stockage. En suivant un lien direct, vous ouvrez Explorateur Stockage et accédez à la ressource vers laquelle il pointe. Cet article décrit le fonctionnement des liens directs et la façon dont vous pouvez les utiliser.

## <a name="how-direct-links-work"></a>Fonctionnement des liens directs

Explorateur Stockage utilise l’arborescence pour visualiser les ressources dans Azure. Un lien direct contient les informations hiérarchiques du nœud de ressource lié dans l’arborescence. Lorsqu’un lien direct est suivi, Explorateur Stockage s’ouvre et reçoit les paramètres du lien direct. Explorateur Stockage utilise ensuite ces paramètres pour accéder à la ressource liée dans l’arborescence.

> [!IMPORTANT]
> Vous devez être connecté et avoir les autorisations nécessaires pour accéder à la ressource liée pour qu’un lien direct fonctionne.

## <a name="parameters"></a>Paramètres

Un lien direct Explorateur Stockage commence toujours par le protocole `storageexplorer://`. Le tableau suivant décrit chacun des paramètres possibles dans un lien direct.

Paramètre | Description
:---------| :---------
`v`         | Version du protocole de lien direct.
`accountid` | ID de ressource Azure Resource Manager du compte de stockage pour la ressource liée. Si la ressource liée est un compte de stockage, cet ID est l’ID de ressource Azure Resource Manager de ce compte de stockage. Dans le cas contraire, cet ID sera l’ID de ressource Azure Resource Manager du compte de stockage auquel appartient la ressource liée.
`resourcetype` | facultatif. Utilisé uniquement lorsque la ressource liée est un conteneur de blobs, un partage de fichiers, une file d’attente ou une table. Doit avoir l’une des valeurs suivantes : « Azure.BlobContainer », « Azure.FileShare », « Azure.Queue », « Azure.FileShare ».
`resourcename` | facultatif. Utilisé uniquement lorsque la ressource liée est un conteneur de blobs, un partage de fichiers, une file d’attente ou une table. Nom de la ressource liée.

Voici un exemple de lien direct vers un conteneur de blobs. 
`storageexplorer://v=1&accountid=/subscriptions/the_subscription_id/resourceGroups/the_resource_group_name/providers/Microsoft.Storage/storageAccounts/the_storage_account_name&subscriptionid=the_subscription_id&resourcetype=Azure.BlobContainer&resourcename=the_blob_container_name`

## <a name="get-a-direct-link-from-storage-explorer"></a>Obtenir un lien direct à partir d’Explorateur Stockage

Vous pouvez utiliser Explorateur Stockage pour obtenir un lien direct pour une ressource. Ouvrez le menu contextuel du nœud de la ressource dans l’arborescence. Utilisez ensuite l’action « Copier le lien direct » pour copier son lien direct dans le Presse-papiers.

## <a name="direct-link-limitations"></a>Limites des liens directs

Les liens directs sont uniquement pris en charge pour les ressources sous des nœuds d’abonnement. En outre, seuls les types de ressources suivants sont pris en charge :

- Comptes de stockage
- Conteneurs d’objets blob
- Files d’attente
- Partages de fichiers
- Tables
