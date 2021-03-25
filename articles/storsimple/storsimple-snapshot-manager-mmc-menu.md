---
title: Actions de menu MMC du Gestionnaire d’instantanés StorSimple | Microsoft Docs
description: Explique comment utiliser les actions de menu standard de la console Microsoft Management Console (MMC) du gestionnaire d’instantanés StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 01064c3668b673baea7aedd9a65c92b03c48dc10
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90056001"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Utilisez les actions du menu MMC dans Gestionnaire d’instantanés StorSimple

## <a name="overview"></a>Vue d’ensemble
Dans le Gestionnaire d’instantanés StorSimple, les actions suivantes figureront sur tous les menus d’action et toutes les variations du volet **Actions** .

* Affichage
* Nouvelle fenêtre à partir d’ici 
* Actualiser 
* Exporter la liste 
* Help 

Ces actions font partie de la Microsoft Management Console (MMC) et ne sont pas spécifiques au gestionnaire d’instantanés StorSimple. Ce didacticiel décrit ces actions et explique comment utiliser chacune d’elles dans le Gestionnaire d’instantanés StorSimple.

## <a name="view"></a>Affichage
Vous pouvez utiliser l'option **Affichage** pour modifier l'affichage du volet **Résultats** et celui de la fenêtre de la console. 

#### <a name="to-change-the-results-pane-view"></a>Pour modifier l’affichage du volet Résultats
1. Cliquez sur l’icône de bureau pour démarrer le Gestionnaire d’instantanés StorSimple.
2. Dans le volet **Étendue**, cliquez avec le bouton droit sur le nœud de votre choix, ou développez ce même nœud et cliquez avec le bouton droit sur un élément dans le volet **Résultats**, puis cliquez sur l'option **Affichage**. 
3. Pour ajouter ou supprimer des colonnes qui s'affichent dans le volet **Résultats**, cliquez sur **Ajout/Suppression de colonnes**. La boîte de dialogue **Ajout/Suppression de colonnes** s'affiche.
   
    ![Ajouter ou supprimer des colonnes dans le volet Résultats](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Remplissez le formulaire comme suit :
   
   * Sélectionnez des éléments dans la liste de colonnes **Disponible** et cliquez sur **Ajouter** pour les ajouter à la liste **Colonnes affichées**. 
   * Cliquez sur des éléments dans la liste des **Colonnes affichées**, puis cliquez sur **Supprimer** pour les supprimer de la liste. 
   * Sélectionnez un élément dans la liste de colonnes **Affichées** et cliquez sur **Monter** ou **Descendre** pour déplacer l’élément vers le haut ou vers le bas dans la liste. 
   * Cliquez sur **Paramètres par défaut** pour revenir à la configuration par défaut du volet **Résultats**. 
5. Lorsque vous avez terminé votre sélection, cliquez sur **OK**. 

#### <a name="to-change-the-console-window-view"></a>Pour modifier l’affichage de la fenêtre de console
1. Cliquez sur l’icône de bureau pour démarrer le Gestionnaire d’instantanés StorSimple.
2. Dans le volet **Étendue**, cliquez avec le bouton droit sur un nœud, cliquez sur **Affichage**, puis sur **Personnaliser**. La boîte de dialogue **Personnaliser** s'affiche.
   
    ![Personnaliser la fenêtre de console](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Activez ou désactivez les cases à cocher pour afficher ou masquer des éléments dans la fenêtre de console. Lorsque vous avez terminé votre sélection, cliquez sur **OK**.

## <a name="new-window-from-here"></a>Nouvelle fenêtre à partir d’ici
Vous pouvez utiliser l'option **Nouvelle fenêtre** pour ouvrir une nouvelle fenêtre de console.

#### <a name="to-open-a-new-console-window"></a>Ouvrir une nouvelle fenêtre de console
1. Cliquez sur l’icône de bureau pour démarrer le Gestionnaire d’instantanés StorSimple.
2. Dans le volet **Étendue**, cliquez avec le bouton droit sur un nœud, cliquez sur **Nouvelle fenêtre**. 
   
    Une nouvelle fenêtre indiquant uniquement l’étendue que vous avez sélectionnée s’affiche. Par exemple, si vous cliquez avec le bouton droit sur le nœud **Stratégies de sauvegarde**, la nouvelle fenêtre affiche uniquement le nœud **Stratégies de sauvegarde** dans le volet **Étendue**, ainsi qu'une liste de stratégies de sauvegarde définies dans le volet **Résultats**. Consultez l’exemple qui suit.
   
    ![Nouvelle fenêtre à partir d’ici](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Actualiser
Vous pouvez utiliser l'action **Actualiser** pour mettre à jour la fenêtre de la console.

#### <a name="to-update-the-console-window"></a>Pour mettre à jour la fenêtre de console
1. Cliquez sur l’icône de bureau pour démarrer le Gestionnaire d’instantanés StorSimple.
2. Dans le volet **Étendue**, cliquez avec le bouton droit sur le nœud de votre choix, ou développez ce même nœud et cliquez avec le bouton droit sur un élément dans le volet **Résultats**, puis cliquez sur **Actualiser**. 

## <a name="export-list"></a>Exporter la liste
Vous pouvez utiliser l'action **Exporter la liste** pour enregistrer une liste dans un fichier .CSV (valeurs séparées par des virgules). Vous pouvez par exemple exporter la liste des stratégies de sauvegarde ou le catalogue de sauvegarde. Vous pouvez ensuite importer le fichier CSV dans une application de tableur pour analyse.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Pour enregistrer une liste dans un fichier CSV (fichier de valeurs séparées par des virgules)
1. Cliquez sur l’icône de bureau pour démarrer le Gestionnaire d’instantanés StorSimple. 
2. Dans le volet **Étendue**, cliquez avec le bouton droit sur le nœud de votre choix, ou développez ce même nœud et cliquez avec le bouton droit sur un élément dans le volet **Résultats**, puis cliquez sur **Exporter la liste**. 
3. La boîte de dialogue **Exporter la liste** s'affiche. Remplissez le formulaire comme suit : 
   
   1. Dans le champ **Nom de fichier**, entrez un nom pour le fichier CSV ou cliquez sur la flèche pour en sélectionner un dans la liste déroulante.
   2. Dans la boîte de dialogue **Type de fichier**, cliquez sur la flèche et sélectionnez un type de fichier dans la liste déroulante.
   3. Pour enregistrer uniquement les éléments de votre choix, sélectionnez les lignes, puis activez la case à cocher **Enregistrer uniquement les lignes sélectionnées**. Pour enregistrer toutes les listes exportées, désactivez la case à cocher **Enregistrer uniquement les lignes sélectionnées** .
   4. Cliquez sur **Enregistrer**.
      
      ![Exporter la liste dans un fichier de valeurs séparées par des virgules](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Help
Utilisez le menu **Aide** pour consulter l’aide en ligne disponible du Gestionnaire d’instantanés StorSimple et de MMC .

#### <a name="to-view-available-online-help"></a>Pour afficher l’aide en ligne disponible
1. Cliquez sur l’icône de bureau pour démarrer le Gestionnaire d’instantanés StorSimple.
2. Dans le volet **Étendue**, cliquez avec le bouton droit sur le nœud de votre choix, ou développez ce même nœud et cliquez avec le bouton droit sur un élément dans le volet **Résultats**, puis cliquez sur **Aide**. 

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur [l’interface utilisateur du Gestionnaire d’instantanés StorSimple](storsimple-use-snapshot-manager.md).
* En savoir plus sur [l’utilisation du Gestionnaire d’instantanés StorSimple pour gérer votre solution StorSimple](storsimple-snapshot-manager-admin.md).

