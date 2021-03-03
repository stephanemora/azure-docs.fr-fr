---
title: Suppression du connecteur ITSM et de l’action qui lui est associée
description: Cet article explique comment supprimer le connecteur ITSM et les groupes d’actions qui lui sont associés.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 1fbaa48d104642984e604bc66a3aa914fbfed44c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100599319"
---
# <a name="deletion-of-unused-itsm-connectors"></a>Suppression des connecteurs ITSM non utilisés

Le processus de suppression d’un connecteur inutilisé contient 2 phases :

1. Suppression des actions associées : toutes les actions associées au connecteur ITSM doivent être supprimées. Cette opération doit être effectuée pour ne pas avoir d’actions sans connecteur susceptibles de provoquer des erreurs dans votre abonnement.

2. Suppression du connecteur ITSM non utilisé.

## <a name="deletion-of-the-associated-actions"></a>Suppression des actions associées

1. Pour trouver le groupe d’actions, vous devez accéder à « Superviser ». ![Capture d’écran de la sélection de l’élément Superviser](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. Sélectionnez « Alertes ». ![Capture d’écran de la sélection de l’élément Alertes](media/itsmc-connector-deletion/itsmc-alert-selection.png)
3. Sélectionnez « Gérer les actions » ![Capture d’écran de la sélection de l’élément Gérer les actions](media/itsmc-connector-deletion/itsmc-actions-selection.png)
4. Sélectionnez tous les connecteurs ITSM connectés à Cherwell. ![Capture d’écran des connecteurs ITSM connectés à Cherwell](media/itsmc-connector-deletion/itsmc-actions-screen.png)
5. Supprimez le groupe d’actions. ![Capture d’écran de la suppression du groupe d’actions](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="deletion-of-the-unused-itsm-connector"></a>Suppression du connecteur ITSM non utilisé

1. Vous devez rechercher « ServiceDesk » dans la barre de recherche supérieure, puis le sélectionner. ![Capture d’écran de la recherche et de la sélection de « ServiceDesk »](media/itsmc-connector-deletion/itsmc-connector-selection.png)
2. Sélectionnez les « Connexions ITSM », puis sélectionnez le connecteur Cherwell. ![Capture d’écran des connecteurs ITSM Cherwell](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)
3. Sélectionnez « Supprimer ». ![Capture d’écran de la suppression du connecteur ITSM](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>Étapes suivantes

* [Résolution des problèmes dans le connecteur ITSM](./itsmc-resync-servicenow.md)
