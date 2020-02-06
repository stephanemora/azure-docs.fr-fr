---
title: Récupérer un tableau de bord supprimé dans le Portail Azure | Microsoft Docs
description: Si vous supprimez un tableau de bord publié dans le Portail Azure, vous pouvez le récupérer.
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: ead9dab61e0cb60505aed9db43c61eb73a0e22a9
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760196"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Récupérer un tableau de bord supprimé dans le Portail Azure

Si vous supprimez un tableau de bord _publié_ dans le Portail Azure, vous pouvez le récupérer dans les 14 jours suivant la suppression. Si le tableau de bord n’est pas publié, vous ne pouvez pas le récupérer et vous devez le régénérer. Pour plus d’informations sur la publication d’un tableau de bord, consultez [Publier un tableau de bord](azure-portal-dashboard-share-access.md#publish-dashboard). Pour récupérer un tableau de bord publié, procédez comme suit :

1. Dans le menu du Portail Azure, sélectionnez **Groupes de ressources**, puis sélectionnez le groupe de ressources dans lequel vous avez publié le tableau de bord (par défaut, il est nommé **dashboards**).

1. Sous **Journal d’activité**, développez l’opération **Supprimer le tableau de bord**. Sélectionnez l’onglet **Historique des modifications**, puis sélectionnez **\<deleted resource\>** .

    ![Capture d’écran de l’onglet Historique des modifications](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Sélectionnez et copiez le contenu du volet gauche, puis enregistrez-le dans un fichier texte avec une extension de fichier _.json_. Le portail utilise le fichier JSON pour recréer le tableau de bord.

    ![Capture d’écran de l’éditeur de différences de l’historique des modifications](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. Dans le menu du Portail Azure, sélectionnez **Tableaux de bord**, puis sélectionnez **Charger**.

    ![Capture d’écran du chargement du tableau de bord](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Sélectionnez le fichier JSON que vous avez enregistré. Le portail recrée le tableau de bord avec le même nom et les mêmes éléments que le tableau de bord supprimé.

1. Sélectionnez **Partager** pour publier le tableau de bord et rétablir le contrôle d’accès approprié.

    ![Capture d’écran du partage de tableau de bord](media/recover-shared-deleted-dashboard/dashboard-share.png)
