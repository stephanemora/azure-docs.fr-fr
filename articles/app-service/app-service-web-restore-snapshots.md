---
title: Restaurer une application à partir d’un instantané
description: Découvrez comment restaurer votre application à partir d’un instantané. Récupérez après une perte de données inattendue dans le niveau Premium grâce aux clichés instantanés automatiques.
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.topic: article
ms.date: 09/02/2021
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: 48b3d859a73a2d63644e1d5881c3505cee93f9d7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679500"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Restauration d’une application dans Azure à partir d’un instantané
Cet article explique comment restaurer une application dans [Azure App Service](../app-service/overview.md) à partir d’un instantané. Vous pouvez utiliser l’un des instantanés de votre application pour restaurer cette dernière à un état antérieur. Il n’est pas nécessaire d’activer des instantanés : la plateforme enregistre automatiquement un instantané de toutes les applications dans le cadre de la récupération de données.

Les instantanés sont des clichés instantanés incrémentiels de votre application App Service. Lorsque votre application se trouve dans le niveau Premium ou une version ultérieure, App Service prend des instantanés périodiques du contenu de l’application et de sa configuration. Ceux-ci offrent plusieurs avantages par rapport aux [sauvegardes standard](manage-backup.md) :

- Aucune erreur de copie de fichier imputable au verrouillage de fichier.
- Taille d’instantané maximale supérieure (30 Go).
- Aucune configuration n’est requise pour les niveaux tarifaires pris en charge.
- Les instantanés peuvent être restaurés vers une nouvelle application App Service dans n’importe quelle région Azure.

La restauration à partir d’instantanés est disponible pour des applications exécutées au niveau **Premium** ou supérieur. Pour en savoir plus sur la mise à l’échelle de votre application, consultez [Mise à l’échelle d’une application web dans Microsoft Azure App Service](manage-scale-up.md).

## <a name="limitations"></a>Limites

- La taille maximale prise en charge pour la restauration d’instantané est de 30 Go. La restauration d’instantané échoue si votre taille de stockage est supérieure à 30 Go. Pour réduire la taille de stockage, envisagez de déplacer des fichiers tels que des journaux, des images, des audios et des vidéos vers [Stockage Azure](../storage/index.yml) par exemple.
- Les bases de données connectées prises en charge par la [sauvegarde standard](manage-backup.md#what-gets-backed-up) ou le [stockage Azure monté](configure-connect-to-azure-storage.md?pivots=container-windows) n’est *pas* inclus dans l’instantané. Envisagez d’utiliser les fonctionnalités de sauvegarde native du service Azure connecté (par exemple, [SQL Database](../azure-sql/database/automated-backups-overview.md) et [Azure Files](../storage/files/storage-snapshots-files.md)).
- App Service arrête l’application cible ou l’emplacement cible pendant la restauration d’un instantané. Pour réduire le temps d’arrêt de l’application de production, restaurez d’abord l’instantané sur un [emplacement de préproduction](deploy-staging-slots.md), puis basculez-le en production.
- Des instantanés sont disponibles pour les 30 derniers jours. La période de rétention et la fréquence des instantanés ne sont pas configurables.
- Les instances App Services exécutées dans App Service Environment ne prennent pas en charge les instantanés.

## <a name="restore-an-app-from-a-snapshot"></a>Restauration d’une application à partir d’un instantané

1. Sur la page **Paramètres** de votre application dans le [portail Azure](https://portal.azure.com), cliquez sur **Sauvegardes** pour afficher la page **Sauvegardes**. Cliquez ensuite sur **Restaurer** sous la section **Instantané (préversion)** .
   
    ![Capture d’écran montrant comment restaurer une application à partir d’un instantané.](./media/app-service-web-restore-snapshots/1.png)

2. Sur la page **Restaurer**, sélectionnez l’instantané à restaurer.
   
    ![Capture d’écran montrant comment sélectionner l’instantané à restaurer. ](./media/app-service-web-restore-snapshots/2.png)
   
3. Indiquez à quelle destination restaurer l’application dans **Destination de restauration**.
   
    ![Capture d’écran montrant comment spécifier la destination de la restauration.](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > En guise de meilleure pratique, nous vous recommandons d’effectuer la restauration vers un nouvel emplacement, puis d’effectuer un échange. Si vous choisissez **Remplacer**, toutes les données existantes du système de fichiers actuel de votre application seront effacées et remplacées. Avant de cliquer sur **OK**, vérifiez qu’il s’agit bien de l’opération que vous souhaitez exécuter.
   > 
   > 
      
   > [!Note]
   > Les limites techniques actuelles ne vous permettent de restaurer que vers des applications se trouvant dans la même unité d’échelle. Cette limite sera supprimée dans une prochaine version.
   > 
   > 
   
    Vous pouvez sélectionner **Application existante** pour effectuer la restauration vers un emplacement. Avant d’utiliser cette option, vous devez avoir déjà créé un emplacement dans votre application.

4. Vous pouvez choisir de restaurer la configuration de votre site.
   
    ![Capture d’écran montrant comment restaurer la configuration d’un site.](./media/app-service-web-restore-snapshots/4.png)

5. Cliquez sur **OK**.
