---
title: Supprimer des connecteurs ITSM non utilisés
description: Cet article explique comment supprimer des connecteurs ITSM et les groupes d’actions qui leur sont associés.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 3dc84ea6def9b762527226dbeb3e2eaab78ec200
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387936"
---
# <a name="delete-unused-itsm-connectors"></a>Supprimer des connecteurs ITSM non utilisés

Le processus de suppression des connecteurs ITSM (gestion des services informatiques) non utilisés comporte deux phases. Vous supprimez toutes les actions associées à un connecteur ITSM, puis vous supprimez le connecteur lui-même. Vous supprimez d’abord les actions, car des actions sans connecteur risquent de provoquer des erreurs dans votre abonnement.

## <a name="delete-associated-actions"></a>Supprimer les actions associées

1. Dans le Portail Azure, sélectionnez **Surveiller**.
  
    ![Capture d’écran de la sélection de Superviser.](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. Sélectionnez **Alertes**.
   
    ![Capture d’écran de la sélection d’Alertes.](media/itsmc-connector-deletion/itsmc-alert-selection.png)

3. Sélectionnez **Gérer les actions**.
   
    ![Capture d’écran de la sélection de Gérer les actions.](media/itsmc-connector-deletion/itsmc-actions-selection.png)

4. Sélectionnez un groupe d’actions associé au connecteur ITSM que vous souhaitez supprimer. Cet article utilise l’exemple d’un connecteur Cherwell.
   
    ![Capture d’écran des actions associées au connecteur Cherwell.](media/itsmc-connector-deletion/itsmc-actions-screen.png)

5. Passez en revue les informations, puis sélectionnez **Supprimer le groupe d’actions**.

    ![Capture d’écran des informations de groupe d’actions et du bouton de suppression du groupe.](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="delete-the-connector"></a>Supprimer le connecteur

1. Dans la barre de recherche, recherchez **servicedesk**. Sélectionnez **ServiceDesk** dans la liste des ressources.

    ![Capture d’écran de la recherche et de la sélection de ServiceDesk.](media/itsmc-connector-deletion/itsmc-connector-selection.png)

2. Sélectionnez **Connexions ITSM**, puis sélectionnez le connecteur Cherwell.

    ![Capture d’écran du connecteur ITSM Cherwell.](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)

3. Sélectionnez **Supprimer**.

    ![Capture d’écran du bouton Supprimer pour le connecteur ITSM.](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>Étapes suivantes

* [Résoudre les problèmes dans un connecteur ITSM](./itsmc-resync-servicenow.md)
