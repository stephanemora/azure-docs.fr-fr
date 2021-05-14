---
title: 'Azure Peering Service : Guide pratique pour accéder aux données de télémétrie des connexions '
description: Dans ce tutoriel, découvrez comment accéder aux données de télémétrie des connexions.
services: peering-service
author: gthareja
ms.service: peering-service
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: gatharej
ms.openlocfilehash: 2b019596c87ef3beca1ff26a9366250d188bdfbc
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108202674"
---
# <a name="tutorial-accessing-peering-service-connection-telemetry"></a>Tutoriel : Accès aux données de télémétrie des connexions Peering Service

 Dans ce tutoriel, vous allez découvrir comment accéder aux données de télémétrie relatives à vos connexions Peering Service.
 
 La télémétrie des connexions fournit des insights collectés au sujet de la connectivité entre l’endroit où se trouve le client et le réseau Microsoft. Cet article explique comment consulter le rapport de latence et les états de préfixe d’une connexion Azure Peering Service spécifique. 

Pour accéder aux données de télémétrie des connexions Peering Service, vous devez créer une connexion Peering Service dans le portail Azure. Pour découvrir comment créer une connexion, consultez [Créer une connexion Peering Service - Portail Azure](azure-portal.md).


## <a name="view-a-latency-report"></a>Afficher un rapport de latence

Pour afficher un rapport de latence au sujet d’une connexion Peering Service spécifique, effectuez ces étapes.

1. Sélectionnez **Toutes les ressources** dans le volet gauche, puis sélectionnez la connexion Peering Service. Sélectionnez ensuite **Ouvrir** sous **Préfixes**. 

   ![Sélectionner la connexion Peering Service](./media/peering-service-measure/peering-service-measure-menu.png)

2. Une page du rapport de latence pour tous les préfixes associés à cette connexion Peering Service s’affiche. **Les connexions Peering Service prennent en charge les données de latence pour les préfixes /24 ou supérieurs uniquement.**

      ![Page du rapport de latence](./media/peering-service-measure/peering-service-latency-report.png)

3. Par défaut, le rapport est mis à jour toutes les heures qui sont indiquées dans cette page. Si vous souhaitez afficher le rapport selon une autre chronologie, choisissez l’option appropriée dans **Afficher les données du/de la dernier(ère)** . 

## <a name="view-prefix-state-report"></a>Afficher le rapport d’état de préfixe

1. Pour voir les événements liés à un préfixe particulier, sélectionnez le nom du préfixe et sélectionnez **Événements de préfixe** dans le volet gauche. Les événements capturés s’affichent.


   ![Événements de préfixe](./media/peering-service-measure/peering-service-prefix-event.png)

 Voici quelques événements possibles qui peuvent être capturés dans la liste **Événements de préfixe**.

| **Événements de préfixe** | **Type d’événement**|**Raisonnement**|
|-----------|---------|---------|
| PrefixAnnouncementEvent |Information|Réception de l’annonce du préfixe|
| PrefixWithdrawalEvent|Avertissement| Réception du retrait du préfixe |
| PrefixBackupRouteAnnouncementEvent |Information|Réception de l’annonce de la route de sauvegarde du préfixe |
| PrefixBackupRouteWithdrawalEvent|Avertissement|Réception du retrait de la route de sauvegarde du préfixe |
| PrefixActivePath |Information| Route active du préfixe actuel   |
| PrefixBackupPath | Information|Route de sauvegarde du préfixe actuel   |
| PrefixOriginAsChangeEvent|Critique| Réception du préfixe exact avec un numéro de système autonome d’origine différent (pour la route active)| 
| PrefixBackupRouteOriginAsChangeEvent  | Error|Réception du préfixe avec un numéro de système autonome d’origine différent (pour la route de sauvegarde)  |

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la connexion Peering Service, consultez [Connexion Peering Service](connection.md).
- Pour en savoir plus sur la télémétrie des connexions Peering Service, consultez [Télémétrie des connexions Peering Service](connection-telemetry.md).
