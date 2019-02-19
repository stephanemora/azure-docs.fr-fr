---
title: Restaurer à partir d'une sauvegarde - Azure App Service
description: Découvrez comment restaurer votre application à partir d’un instantané.
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.topic: article
ms.date: 04/04/2018
ms.author: aelnably;nicking
ms.custom: seodec18
ms.openlocfilehash: 8d4290f1411749e2d8d3d27fbd792ceeeea47ef7
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100489"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Restauration d’une application dans Azure à partir d’un instantané
Cet article explique comment restaurer une application dans [Azure App Service](../app-service/overview.md) à partir d’un instantané. Vous pouvez utiliser l’un des instantanés de votre application pour restaurer cette dernière à un état antérieur. Il n’est pas nécessaire d’activer la sauvegarde d’instantanés : la plateforme enregistre automatiquement un instantané de toutes les applications dans le cadre de la récupération de données.

Les instantanés sont des copies fantômes incrémentielles qui offrent plusieurs avantages par rapport à des [sauvegardes](manage-backup.md) classiques :
- Aucune erreur de copie de fichier imputable au verrouillage de fichier.
- Aucune limite de taille de stockage.
- Aucune configuration nécessaire.

La restauration à partir d’instantanés est disponible pour des applications exécutées au niveau **Premium** ou supérieur. Pour en savoir plus sur la mise à l’échelle de votre application, consultez [Mise à l’échelle d’une application web dans Microsoft Azure App Service](web-sites-scale.md).

## <a name="limitations"></a>Limites

- Actuellement, cette fonctionnalité est uniquement disponible en tant que préversion.
- Vous pouvez effectuer une restauration uniquement vers la même application ou à vers emplacement appartenant à cette application.
- App Service arrête l’application cible ou l’emplacement cible pendant la restauration.
- App Service conserve l’équivalent de trois mois d’instantanés pour la récupération de données de la plateforme.
- Vous ne pouvez restaurer des instantanés qu’au cours des 30 derniers jours.
- Les instances d'App Service exécutées dans App Service Environment ne prennent pas en charge les captures instantanées.
 

## <a name="restore-an-app-from-a-snapshot"></a>Restauration d’une application à partir d’un instantané

1. Sur la page **Paramètres** de votre application dans le [portail Azure](https://portal.azure.com), cliquez sur **Sauvegardes** pour afficher la page **Sauvegardes**. Cliquez ensuite sur **Restaurer** sous la section **Instantané (préversion)**.
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. Sur la page **Restaurer**, sélectionnez l’instantané à restaurer.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Indiquez à quelle destination restaurer l’application dans **Destination de restauration**.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Si vous choisissez **Remplacer**, toutes les données existantes du système de fichiers actuel de votre application seront effacées et remplacées. Avant de cliquer sur **OK**, vérifiez qu’il s’agit bien de l’opération que vous souhaitez exécuter.
   > 
   > 
      
   > [!Note]
   > Les limites techniques actuelles ne vous permettent de restaurer que vers des applications se trouvant dans la même unité d’échelle. Cette limite sera supprimée dans une prochaine version.
   > 
   > 
   
    Vous pouvez sélectionner **Application existante** pour effectuer la restauration vers un emplacement. Avant d’utiliser cette option, vous devez avoir déjà créé un emplacement dans votre application.

4. Vous pouvez choisir de restaurer la configuration de votre site.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Cliquez sur **OK**.
