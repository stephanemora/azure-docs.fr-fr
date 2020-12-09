---
title: Comment corriger manuellement les problèmes de synchronisation de ServiceNow
description: Réinitialiser la connexion à ServiceNow afin que les alertes dans Microsoft Azure puissent à nouveau appeler ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: f7d69c4e112ded678c70a516202492d37ee8f60a
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436859"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>Résolution des problèmes liés au connecteur ITSM

## <a name="how-to-manually-fix-servicenow-sync-problems"></a>Comment corriger manuellement les problèmes de synchronisation de ServiceNow

Azure Monitor peut se connecter à des fournisseurs de gestion des services informatiques (ITSM) tiers. ServiceNow est un de ces fournisseurs.

Pour des raisons de sécurité, il peut être nécessaire d’actualiser le jeton d’authentification utilisé pour votre connexion à ServiceNow.
Utilisez le processus de synchronisation suivant pour réactiver la connexion et actualiser le jeton :


1. Recherchez la solution dans la bannière de recherche du haut, puis sélectionnez les solutions appropriées.

    ![Capture d’écran montrant la bannière de recherche supérieure et où sélectionner les solutions appropriées.](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. Dans l’écran des solutions, choisissez « Sélectionner tout » dans le filtre d’abonnement, puis filtrez par « ServiceDesk ».

    ![Capture d’écran montrant où choisir Sélectionner tout et où filtrer par ServiceDesk.](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Sélectionnez la solution de votre connexion ITSM.
1. Sélectionnez la connexion ITSM dans la bannière gauche.

    ![Capture d’écran montrant où sélectionner Connexions ITSM.](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Sélectionnez chaque connecteur dans la liste. 
    1. Cliquez sur le nom du connecteur pour le configurer
    1. Supprimez tous les connecteurs qui ne sont plus utilisés

    1. Mettez à jour les champs en fonction de [ces définitions](./itsmc-connections.md) selon votre type de partenaire

    1. Cliquer sur Synchroniser.

       ![Capture d’écran mettant en évidence le bouton Synchroniser.](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Cliquez sur Enregistrer.

        ![Nouvelle connexion](media/itsmc-resync-servicenow/save-8bit.png)

f.    Passez en revue les notifications pour voir si le processus s’est terminé avec succès. 

## <a name="next-steps"></a>Étapes suivantes

Découvrir plus d’informations sur les [Connexions de gestion des services informatiques](itsmc-connections.md)
