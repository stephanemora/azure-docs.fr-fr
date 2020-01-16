---
title: Tutoriel - Sauvegarder des partages de fichiers Azure Files
description: Dans ce didacticiel, vous allez apprendre à utiliser le Portail Azure pour configurer un coffre Recovery Services et sauvegarder des partages de fichiers Azure.
ms.date: 06/10/2019
ms.topic: tutorial
ms.openlocfilehash: b002d1ea092d2d0507dc761f56ca7835f1521fb3
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75921644"
---
# <a name="back-up-azure-file-shares-in-the-azure-portal"></a>Sauvegarder des partages de fichiers Azure dans le portail Azure

Ce tutoriel explique comment utiliser le portail Azure pour sauvegarder des [partages de fichiers Azure](../storage/files/storage-files-introduction.md).

Dans ce guide, vous apprendrez comment :
> [!div class="checklist"]
>
> * Configurer un coffre Recovery Services pour sauvegarder des fichiers Azure
> * Exécuter un travail de sauvegarde à la demande pour créer un point de restauration

## <a name="prerequisites"></a>Conditions préalables requises

Avant de sauvegarder un partage de fichiers Azure, assurez-vous qu’il est présent dans l’un des [types de compte de stockage pris en charge](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview). Après avoir vérifié ce point, vous pouvez protéger vos partages de fichiers.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Limites des sauvegardes de partage de fichiers Azure dans la préversion

La sauvegarde de partages de fichiers Azure est disponible en préversion. Les partages de fichiers Azure dans les comptes de stockage v1 et v2 universels sont pris en charge. Les scénarios de sauvegarde suivants ne sont pas pris en charge pour les partages de fichiers Azure :

* Il n’y a aucune interface CLI disponible pour la protection d’Azure Files à l’aide de Sauvegarde Azure.
* Vous pouvez effectuer une seule sauvegarde planifiée par jour.
* Vous pouvez effectuer quatre sauvegardes à la demande par jour maximum.
* Utilisez les [verrous de ressources](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) sur le compte de stockage pour empêcher la suppression accidentelle des sauvegardes de votre coffre Recovery Services.
* Ne supprimez pas les instantanés créés par Sauvegarde Azure. La suppression d’instantanés peut provoquer une perte de points de récupération et/ou des échecs de restauration.
* Ne supprimez pas les partages de fichiers qui sont protégés par Sauvegarde Azure. La solution actuelle supprimera toutes les captures instantanées prises par Sauvegarde Azure une fois que le partage de fichiers aura été supprimé et qu’il aura donc perdu tous ses points de restauration.

La sauvegarde des partages de fichiers Azure dans les comptes de stockage avec réplication de [stockage redondant interzone](../storage/common/storage-redundancy-zrs.md) (ZRS) est actuellement disponible uniquement dans les régions USA Centre (CUS), USA Est (EUS), USA Est 2 (EUS2), Europe Nord (NE), Asie Sud-Est (SEA), Europe Ouest (WE) et USA Ouest 2 (WUS2).

## <a name="configuring-backup-for-an-azure-file-share"></a>Configuration de la sauvegarde d’un partage de fichiers Azure

Ce didacticiel suppose que vous ayez déjà établi un partage de fichiers Azure. Pour sauvegarder votre partage de fichiers Azure :

1. Créez un coffre Recovery Services dans la même région que votre partage de fichiers. Si vous disposez déjà d’un coffre, ouvrez la page de vue d’ensemble de votre coffre et cliquez sur **Sauvegarde**.

    ![Cliquez sur la sauvegarde dans la page de vue d’ensemble de votre coffre](./media/backup-file-shares/overview-backup-page.png)

2. Dans le menu de l’**objectif de sauvegarde**, à partir de **Que voulez-vous sauvegarder ?** , choisissez le partage de fichiers Azure.

    ![Choisir le partage de fichiers Azure comme objectif de sauvegarde](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. Cliquez sur **Sauvegarde** pour configurer le partage de fichiers Azure pour votre coffre Recovery Services.

   ![Cliquez sur Sauvegarde pour associer le partage de fichiers Azure au coffre](./media/backup-file-shares/set-backup-goal.png)

    Une fois le coffre associé au partage de fichiers Azure, le menu de sauvegarde s’ouvre et vous demande de sélectionner un compte de stockage. Le menu affiche tous les comptes de stockage pris en charge dans la région où se trouve votre coffre, et qui ne sont pas déjà associés à un coffre Recovery Services.

   ![Sélectionner votre compte de stockage](./media/backup-file-shares/list-of-storage-accounts.png)

4. Dans la liste Comptes de stockage, sélectionnez un compte et cliquez sur **OK**. Azure recherche le compte de stockage pour les partages de fichiers qui peuvent être sauvegardés. Si vous avez récemment ajouté vos partages de fichiers et que vous ne les voyez pas dans la liste, laissez un peu de temps pour qu’ils s’affichent.

   ![Les partages de fichiers sont découverts](./media/backup-file-shares/discover-file-shares.png)

5. À partir de la liste **Partages de fichiers**, sélectionnez un ou plusieurs des partages de fichiers que vous souhaitez sauvegarder, puis cliquez sur **OK**.

6. Après avoir choisi vos partages de fichiers, le menu de sauvegarde bascule vers la **stratégie de sauvegarde**. Dans ce menu, sélectionnez une stratégie de sauvegarde existante, ou bien créez-en une, puis cliquez sur **Activer la sauvegarde**.

   ![Sélectionnez une stratégie de sauvegarde ou créez-en une nouvelle](./media/backup-file-shares/apply-backup-policy.png)

    Une fois la stratégie de sauvegarde établie, un instantané des partages de fichiers sera effectué à l’heure planifiée, et le point de récupération est conservé pour la période définie.

## <a name="create-an-on-demand-backup"></a>Créer une sauvegarde à la demande

Après avoir configuré la stratégie de sauvegarde, vous souhaiterez créer une sauvegarde à la demande pour garantir la protection de vos données jusqu'à la prochaine sauvegarde planifiée.

### <a name="to-create-an-on-demand-backup"></a>Pour créer une sauvegarde à la demande

1. Ouvrez le coffre Recovery Services contenant les points de récupération du partage de fichiers, puis cliquez sur **Éléments de sauvegarde**. La liste des types d’éléments de sauvegarde s’affiche.

   ![Liste des éléments de sauvegarde](./media/backup-file-shares/list-of-backup-items.png)

2. Dans la liste, sélectionnez **Stockage Azure (fichiers Azure)** . La liste des partages de fichiers Azure s’affiche.

   ![Répertorier les partages de fichiers Azure](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. Dans la liste des partages de fichiers Azure, sélectionnez le partage de fichiers désiré. Le menu de l’élément de sauvegarde pour le partage de fichiers sélectionné s’ouvre.

   ![Menu de l’élément de sauvegarde pour le partage de fichiers sélectionné](./media/backup-file-shares/backup-item-menu.png)

4. Dans le menu de l’élément de sauvegarde, cliquez sur **Sauvegarder maintenant**. Comme il s’agit d’un travail de sauvegarde à la demande, il n’existe aucune stratégie de rétention associée au point de récupération. La boîte de dialogue **Sauvegarder maintenant** s’ouvre. Spécifiez le dernier jour que vous souhaitez conserver dans le point de récupération.

   ![Choisir une date pour la rétention des points de récupération](./media/backup-file-shares/backup-now-menu.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez utilisé le portail Azure pour effectuer les opérations suivantes :

> [!div class="checklist"]
>
> * Configurer un coffre Recovery Services pour sauvegarder des fichiers Azure
> * Exécuter un travail de sauvegarde à la demande pour créer un point de restauration

Passez à l’article suivant pour restaurer à partir d’une sauvegarde d’un partage de fichiers Azure.

> [!div class="nextstepaction"]
> [Restauration à partir d’une sauvegarde de partage de fichiers Azure](./backup-azure-files.md#restore-from-backup-of-azure-file-share)
