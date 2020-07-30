---
title: Comment corriger manuellement les problèmes de synchronisation de ServiceNow
description: Réinitialiser la connexion à ServiceNow afin que les alertes dans Microsoft Azure puissent à nouveau appeler ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: a3382f93990612b0ab34eb0848cbf3d6577c44ff
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087932"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>Comment corriger manuellement les problèmes de synchronisation de ServiceNow

Azure Monitor peut se connecter à des fournisseurs de gestion des services informatiques (ITSM) tiers. ServiceNow est un de ces fournisseurs.

Pour des raisons de sécurité, il peut être nécessaire d’actualiser le jeton d’authentification utilisé pour votre connexion à ServiceNow.
Utilisez le processus de synchronisation suivant pour réactiver la connexion et actualiser le jeton :


1. Recherchez la solution dans la bannière de recherche du haut, puis sélectionnez les solutions appropriées.

    ![Nouvelle connexion](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. Dans l’écran des solutions, choisissez « Sélectionner tout » dans le filtre d’abonnement, puis filtrez par « ServiceDesk ».

    ![Nouvelle connexion](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Sélectionnez la solution de votre connexion ITSM.
1. Sélectionnez la connexion ITSM dans la bannière gauche.

    ![Nouvelle connexion](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Sélectionnez chaque connecteur dans la liste. 
    1. Cliquez sur le nom du connecteur pour le configurer
    1. Supprimez tous les connecteurs qui ne sont plus utilisés

    1. Mettez à jour les champs en fonction de [ces définitions](./itsmc-connections.md) selon votre type de partenaire

    1. Cliquer sur Synchroniser.

       ![Nouvelle connexion](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Cliquez sur Enregistrer.

        ![Nouvelle connexion](media/itsmc-resync-servicenow/save-8bit.png)

f.    Passez en revue les notifications pour voir si le processus s’est terminé avec succès. 

## <a name="next-steps"></a>Étapes suivantes

Découvrir plus d’informations sur les [Connexions de gestion des services informatiques](itsmc-connections.md)
