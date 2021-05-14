---
title: Installer Update 5.1 sur un appareil de la gamme StorSimple 8000 | Microsoft Docs
description: Explique comment installer Update 5.1 pour la gamme d’appareils StorSimple 8000 sur votre appareil de la gamme StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/21/2021
ms.author: alkohli
ms.openlocfilehash: 289ffbbd3dc23be0060ee5dbe488bf80323214e2
ms.sourcegitcommit: 19dcad80aa7df4d288d40dc28cb0a5157b401ac4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107897214"
---
# <a name="install-update-51-on-your-storsimple-device"></a>Installer Update 5.1 sur votre appareil StorSimple

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel explique comment installer Update 5.1 sur un appareil StorSimple exécutant une version logicielle antérieure par le biais du portail Azure et à l’aide de la méthode du correctif logiciel.

Update 5.1 comprend des mises à jour de sécurité non perturbatrices qui peuvent être appliquées via le portail Azure ou par la méthode du correctif logiciel.

Si vous appliquez Update 5.1 depuis le portail Azure, un ensemble de vérifications préalables manuelles et automatiques sont effectuées pour déterminer l’intégrité de l’appareil sur le plan de l’état du matériel et de la connectivité réseau. Ces vérifications préalables sont effectuées uniquement si vous appliquez les mises à jour à partir du portail Azure.

Si vous préférez utiliser la méthode de correctif logiciel, nous vous recommandons vivement d’installer Update 5 en suivant les instructions de la procédure [Installer Update 5 sur votre appareil StorSimple](storsimple-8000-install-update-5.md). Suivez ensuite les étapes de la section [Installer Update 5.1 en tant que correctif logiciel](#install-update-51-as-a-hotfix), ci-dessous, pour installer Update 5.1.

L’installation des mises à jour de sécurité d’Update 5.1 prend environ 30 minutes.

> [!IMPORTANT]
> * Update 5.1 est une mise à jour obligatoire à installer immédiatement. Pour plus d’informations, consultez [Notes de version pour Update 5.1](storsimple-update51-release-notes.md).
> * Update 5 est une version prise en charge au minimum.

> [!NOTE]
> * Nous vous recommandons d’installer les mises à jour du logiciel et d’autres mises à jour régulières au moyen du portail Azure. <!--You should only go to the Windows PowerShell interface of the device (to install updates) if the pre-update gateway check fails in the portal. Depending upon the version you are updating from, the updates may take 4 hours (or greater) to install. The maintenance mode updates must be installed through the Windows PowerShell interface of the device. As maintenance mode updates are disruptive updates, these result in a down time for your device. - N/A FOR 5.1? No maintenance mode updates, and the security updates go quickly.-->
> * Si vous envisagez d’installer à l’aide de la méthode du correctif logiciel, vous devez contacter le [Support Microsoft](mailto:support@microsoft.com) avant de commencer l’installation.

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-51-through-the-azure-portal"></a>Installer Update 5.1 à l’aide du portail Azure

Suivez la procédure ci-dessous pour mettre à jour votre appareil vers [Update 5.1](storsimple-update51-release-notes.md).

> [!NOTE]
> Microsoft extrait des informations de diagnostic supplémentaires de l’appareil. Par conséquent, lorsque notre équipe en charge des opérations identifie des appareils qui ont un problème, nous sommes plus à même de collecter des informations à partir de ces appareils et de diagnostiquer les problèmes.

#### <a name="to-install-an-update-from-the-azure-portal"></a>Pour installer une mise à jour à partir du portail Azure

1. Dans la page du service StorSimple, sélectionnez votre appareil.

    ![Sélectionnez l’appareil](./media/storsimple-8000-install-update-51/update1.png)

2. Accédez à **Paramètres de l’appareil** > **Mises à jour de l’appareil**.

    ![Capture d’écran du panneau Paramètres avec l’option Mises à jour de l’appareil en évidence.](./media/storsimple-8000-install-update-51/update2.png)

3. Une notification s’affiche si de nouvelles mises à jour sont disponibles. Dans le panneau **Mises à jour de l’appareil**, vous pouvez également cliquer sur **Rechercher les mises à jour**. Une tâche est créée pour rechercher les mises à jour disponibles. Un message s’affiche lorsque la tâche est terminée.

    ![Capture d’écran du panneau Paramètres avec l’option Mises à jour de l’appareil en évidence et le panneau Mises à jour de l’appareil avec le message De nouvelles mises à jour régulières sont disponibles en évidence.](./media/storsimple-8000-install-update-51/update3.png)

4. Nous vous recommandons de consulter les notes de publication avant d’appliquer une mise à jour sur votre appareil. Pour appliquer les mises à jour, cliquez sur **Installer les mises à jour**. Dans le panneau **Confirmer les mises à jour standard**, passez en revue les conditions préalables à remplir avant d’appliquer les mises à jour. Cochez la case pour indiquer que vous êtes prêt à mettre à jour l’appareil, puis cliquez sur **Installer**.

    ![Capture d’écran du panneau Mises à jour de l’appareil avec l’option Installer les mises à jour en évidence et l’option Confirmer les mises à jour standard avec les options Accepter et Installer en évidence.](./media/storsimple-8000-install-update-51/update4.png)

5. Un ensemble de vérifications préalables démarre. Ces vérifications incluent :
   
   * **Contrôles d’intégrité des contrôleurs** , destinés à vérifier que les contrôleurs d’appareil sont en bon état et en ligne.
   * **Contrôles d’intégrité des composants matériels** , utilisés pour vérifier l’état des composants matériels de votre appareil StorSimple.
   * **Contrôles DATA 0** , afin de s’assurer de l’activation de DATA 0 sur votre appareil. Si cette interface n’est pas activée, vous devez l’activer, puis réessayer.

     La mise à jour est téléchargée et installée uniquement si toutes les vérifications sont effectuées avec succès. Un message s’affiche lorsque les vérifications sont en cours. Si les pré-vérifications échouent, les causes de cet échec vous sont communiquées. Résolvez ces problèmes, puis retentez l’opération. Vous devrez peut-être contacter le support technique Microsoft si vous ne pouvez pas résoudre ces problèmes vous-même.

6. Une fois les vérifications préalables terminées, une tâche de mise à jour est créée. Un message s’affiche une fois la tâche de mise à jour créée.
   
    ![Capture d’écran de la notification indiquant « Démarrage du travail de mise à jour logicielle ».](./media/storsimple-8000-install-update-51/update6.png)
   
    La mise à jour est ensuite appliquée à votre appareil.

7. La mise à jour prend quelques heures. Sélectionnez la tâche de mise à jour et cliquez sur **Détails** pour afficher les détails de la tâche à tout moment.

    ![Capture d’écran du panneau Mises à jour de l’appareil avec l’option Télécharger et installer les mises à jour logicielles en cours en évidence et le panneau Installer les mises à jour.](./media/storsimple-8000-install-update-51/update8.png)

     Vous pouvez également surveiller la progression de la tâche de mise à jour à partir de **Paramètres de l’appareil > Travaux**. Le panneau **Travaux** indique la progression de la mise à jour.

     ![Capture d’écran du panneau Paramètres avec Travaux en évidence et le panneau Travaux montrant la progression de la mise à jour.](./media/storsimple-8000-install-update-51/update7.png)

8. Une fois la tâche terminée, accédez à **Paramètres de l’appareil > Mises à jour de l’appareil**. La version du logiciel doit maintenant être mise à jour.

   Vérifiez que votre appareil exécute **StorSimple 8000 Series Update 5.1 (6.3.9600.17885)** et que la date de la **Dernière mise à jour** est la date du jour.

## <a name="install-update-51-as-a-hotfix"></a>Installer Update 5.1 en tant que correctif logiciel

Si vous souhaitez installer Update 5.1 en tant que correctif logiciel, effectuez les étapes suivantes avant de commencer l’installation :

* Installez Update 5 avant d’installer Update 5.1. Pour obtenir des instructions, consultez [Installer Update 5 sur votre appareil StorSimple](storsimple-8000-install-update-5.md).
* Avant de commencer l’installation du correctif, contactez le [Support Microsoft](mailto:support@microsoft.com).

La méthode du correctif logiciel implique les étapes suivantes :

1. Télécharger les correctifs logiciels à partir du catalogue Microsoft Update.
2. Installer et vérifier les correctifs logiciels en mode Normal.
3. Installer et vérifier les correctifs logiciels en mode Maintenance

#### <a name="download-updates-for-your-device"></a>Télécharger des mises à jour pour votre appareil

Vous devez télécharger et installer les correctifs logiciels suivants dans les dossiers suggérés et l’ordre indiqué.

| Commande | Ko       | Description | Type de mise à jour | Durée d’installation |Installer dans le dossier|
|-------|----------|------------ |-------------|--------------|----- |
|1.     |KB4542887|Mise à jour logicielle<br>Télécharger les deux fichiers _HcsSoftwareUpdate.exe_ et _CisMSDAgent.exe_ |Normal <br></br>sans interruption de service |~ 25 minutes |FirstOrderUpdate|
|3.<sup>1, 2</sup>     |KB4037263|Microprogramme de disque|Maintenance <br></br>Interruption de service|~ 30 minutes|ThirdOrderUpdate|

<sup>1</sup> Il n’y a pas de mises à jour de deuxième ordre dans la Update 5.1.

<sup>2</sup> Installez les mises à jour de troisième ordre si vous n’avez pas installé les mises à jour du microprogramme de disque en plus des mises à jour de correctifs pour Update 5.

Procédez comme suit pour télécharger et importer les correctifs logiciels.

#### <a name="download-hotfixes"></a>Télécharger les correctifs logiciels

Pour télécharger les correctifs, consultez [Télécharger les correctifs logiciels](storsimple-8000-install-update-5.md#to-download-hotfixes).

#### <a name="install-and-verify-device-updates"></a>Installer et vérifier les mises à jour de l’appareil

Installez les mises à jour de l’appareil dans KB4542887 en suivant les étapes de la section [Pour installer et vérifier les correctifs logiciels en mode Normal](storsimple-8000-install-update-5.md#to-install-and-verify-regular-mode-hotfixes) dans **Installer Update 5 sur votre appareil StorSimple**.

> [!IMPORTANT]
> Si vous n’avez pas encore contacté le [Support Microsoft](mailto:support@microsoft.com), vous devez le faire maintenant avant d’installer les correctifs logiciels.

Suivez les étapes pour installer les mises à jour de premier ordre. Il n’y a pas de mises à jour de deuxième ordre dans la Update 5.1.

Pour Update 5.1, vérifiez les versions de logiciel suivantes après l’installation :

 * FriendlySoftwareVersion : StorSimple 8000 Series Update 5.1
 * HcsSoftwareVersion : 6.3.9600.17885
 * CisAgentVersion : 1.0.9777.0
 * MdsAgentVersion : 35.2.2.0
 * Lsisas2Version : 2.0.78.00

#### <a name="install-and-verify-disk-firmware-updates"></a>Installer et vérifier les mises à jour du microprogramme de disque

Si vous n’avez pas installé les mises à jour du microprogramme de disque lorsque vous avez installé Update 5, installez les mises à jour du microprogramme de disque dans KB4037263 en suivant les étapes de la section [Pour installer et vérifier les correctifs logiciels en mode Normal](storsimple-8000-install-update-5.md#to-install-and-verify-maintenance-mode-hotfixes) dans **Installer Update 5 sur votre appareil StorSimple**.

Vous n’avez pas besoin d’installer les mises à jour du microprogramme de disque si vous exécutez ces versions de microprogramme : `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003` ou `0107`.

Pour vérifier si vous avez besoin des mises à jour du microprogramme de disque, vous pouvez exécuter la cmdlet `Get-HcsFirmwareVersion`.

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail la [version Update 5.1](storsimple-update51-release-notes.md).
