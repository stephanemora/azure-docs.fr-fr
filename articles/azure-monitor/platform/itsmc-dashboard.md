---
title: Examen d’erreur à l’aide du tableau de bord
description: Ce document contient des informations sur l’examen des erreurs courantes à l’aide du tableau de bord.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 9a43318ed935dd1e71b6da75c9f078fbebbd29eb
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599020"
---
# <a name="error-investigation-using-the-dashboard"></a>Recherche d’erreurs à l’aide du tableau de bord

Cette page contient des informations sur le tableau de bord du connecteur ITSM. Ce tableau de bord vous permet d’examiner l’état de votre connecteur ITSM.

## <a name="how-to-view-the-dashboard"></a>Comment afficher le tableau de bord

Pour afficher les erreurs dans le tableau de bord, vous devez suivre les étapes suivantes :

1. Dans **Toutes les ressources**, recherchez **ServiceDesk(*nom_de_votre_espace_de_travail*)**  :

   ![Capture d’écran montrant les ressources récentes dans le portail Azure.](media/itsmc-definition/create-new-connection-from-resource.png)

2. Sous **Sources de données de l’espace de travail** dans le volet gauche, sélectionnez **Connexions ITSM** :

   ![Capture d’écran montrant l’élément de menu Connexion ITSM.](media/itsmc-overview/add-new-itsm-connection.png)

3. Sous **Résumé** dans la zone de gauche **Connecteur de gestion des services informatiques**, sélectionnez **Afficher le résumé** :

    ![Capture d’écran montrant la vue Résumé.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. Sous **Résumé** dans la zone de gauche **Connecteur de gestion des services informatiques**, cliquez sur le graphique :

    ![Capture d’écran montrant le clic sur le graphique.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. À l’aide de ce tableau de bord, vous serez en mesure de vérifier l’état et les erreurs de votre connecteur.
    ![Capture d’écran montrant l’état du connecteur.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="dashboard-elements"></a>Éléments du tableau de bord

Le tableau de bord contient des informations sur les alertes qui ont été envoyées à l’outil ITSM à l’aide de ce connecteur.
Le tableau de bord est divisé en quatre parties :

1. Élément de travail créé : Le graphique et le tableau ci-dessous contiennent le nombre d’éléments de travail par type. Si vous cliquez sur le graphique ou sur le tableau, vous pouvez obtenir plus de détails sur les éléments de travail.
    ![Capture d’écran montrant comment créer l’élément de travail.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
2. Ordinateurs affectés : Les tableaux contiennent des détails sur les éléments de configuration qui ont créé des éléments de configuration.
    En cliquant sur les des tableaux, vous pouvez obtenir plus des informations supplémentaires sur les éléments de configuration.
    Le tableau contient un nombre limité de lignes. Si vous souhaitez afficher l’ensemble de la liste, vous pouvez cliquer sur « Afficher tout ».
    ![Capture d’écran montrant les ordinateurs concernés.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
3. État du connecteur : Le graphique et le tableau ci-dessous contiennent des messages relatifs à l’état du connecteur. En cliquant sur le graphique sur les lignes du tableau, vous pouvez obtenir plus d’informations sur les messages relatifs à l’état du connecteur.
    Le tableau contient un nombre limité de lignes. Si vous souhaitez afficher l’ensemble de la liste, vous pouvez cliquer sur « Afficher tout ».

    Vous pouvez afficher des détails sur les messages dans le tableau [ici](itsmc-dashboard-errors.md).

    ![Capture d’écran montrant l’état du connecteur.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
4. Règles d'alerte : Les tableaux contiennent des informations sur le nombre de règles d’alerte détectées.
    En cliquant sur les lignes des tableaux, vous pouvez obtenir des détails supplémentaires sur les règles détectées.
    Le tableau contient un nombre limité de lignes. Si vous souhaitez afficher l’ensemble de la liste, vous pouvez cliquer sur « Afficher tout ».
    ![Capture d’écran montrant des règles d’alerte.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)