---
title: Gérer votre compte - Azure Batch | Microsoft Docs
description: Découvrez ce que comprend un compte Azure Batch
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ecae47f6aa0ab3f179632467b7da7805f06162d6
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397260"
---
# <a name="manage-your-batch-account"></a>Gérer votre compte Batch

Un compte Batch est une entité identifiée de façon unique au sein du service Batch. Tout le traitement s’effectue via un compte Batch.

Vous pouvez créer un compte Azure Batch à l’aide du [Portail Azure](batch-account-create-portal.md) ou par programme, par exemple avec la [bibliothèque .NET de gestion Batch](batch-management-dotnet.md). Lorsque vous créez le compte, vous pouvez associer un compte de stockage Azure pour le stockage des données d’entrée et de sortie ou des applications liées au travail.

Vous pouvez exécuter plusieurs charges de travail Batch dans un compte Batch ou répartir vos charges de travail entre plusieurs comptes Batch se trouvant dans le même abonnement mais dans différentes régions Azure.

## <a name="components-of-the-batch-account"></a>Composants du compte Batch

Le compte Batch vous permet d’exécuter des programmes de traitement par lots de calcul haute performance (HPC) en parallèle, efficacement et à grande échelle dans Azure. Au sein du compte, vous gérez :

- les applications que vous exécutez

- l’allocation des pools et des nœuds au sein des pools

- le nombre et le types de tâches 

- l’entrée et la sortie des données. Aucun logiciel supplémentaire n’est requis pour gérer les tâches.

- Lorsque vous créez le compte Batch, vous êtes invité à le nommer. Ce nom est son ID ; une fois attribué, il ne peut plus être modifié.

- Pour changer le nom du compte, vous devez le supprimer et créer un nouveau compte Batch.

- Le compte est créé au sein de l’abonnement que vous souhaitez utiliser.

- Utilisez le compte pour identifier et récupérer les clés primaires et secondaires de n’importe quel compte Batch de votre abonnement.

- Le compte conserve des informations sur l’allocation de pools et les quotas de base.  

- Le compte contient les informations relatives à l’emplacement.

- Le compte identifie votre compte de stockage.

## <a name="next-steps"></a>Étapes suivantes

- Créer un compte Batch à l’aide du [portail Azure](batch-account-create-portal.md).
- Créer un compte Batch par programme, par exemple avec la [bibliothèque .NET de gestion Batch](batch-management-dotnet.md).
- [Configurer ou désactiver l’accès à distance aux nœuds de calcul dans un pool Azure Batch](pool-endpoint-configuration.md).
- [Exécuter des tâches de préparation et de validation du travail sur les nœuds de calcul Batch](batch-job-prep-release.md)

