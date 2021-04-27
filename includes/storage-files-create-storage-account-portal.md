---
title: storage-files-create-storage-account-portal
description: Créez un compte de stockage pour Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33159ab6dd014a153c8fd317fd291aeca033d6e9
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717832"
---
Un compte de stockage est un pool partagé de stockage dans lequel vous pouvez déployer un partage de fichiers Azure, ou d’autres ressources de stockage comme les objets blob ou les files d’attente. Un compte de stockage peut contenir un nombre illimité de partages. Un partage peut stocker un nombre illimité de fichiers, dans les limites de capacité du compte de stockage.

Pour créer un compte de stockage :

1. Dans le menu de gauche, sélectionnez **+** pour créer une ressource.
1. Sélectionnez **Compte de stockage** pour créer un compte de stockage.

    :::image type="content" source="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png" alt-text="Capture d’écran de l’option Compte de stockage dans le panneau Créer une ressource." lightbox="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png":::

1. Dans **Nom**, tapez *mystorageacct* suivi de quelques nombres au hasard jusqu’à obtenir une marque verte, signe que le nom est unique. Le nom du compte de stockage doit être écrit entièrement en minuscules et être unique. Notez le nom de votre compte de stockage. Vous le réutiliserez ultérieurement. 
1. Dans **Performances**, conservez la valeur par défaut (**Standard**).
1. Dans **Réplication**, sélectionnez **Stockage localement redondant (LRS)** .
1. Dans **Abonnement**, sélectionnez l’abonnement utilisé pour créer le compte de stockage. Si vous n’avez qu’un abonnement, il est proposé par défaut.
1. Dans **Groupe de ressources**, sélectionnez **Créer**. Entrez le nom *myResourceGroup*.
1. Dans **Emplacement**, sélectionnez **USA Est**.
1. Lorsque vous avez terminé, sélectionnez **Créer** pour commencer le déploiement.