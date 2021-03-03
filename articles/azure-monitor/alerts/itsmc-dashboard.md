---
title: Examiner les erreurs à l’aide du tableau de bord d’ITSMC
description: Découvrez comment utiliser le tableau de bord Connecteur de gestion des services informatiques pour examiner les erreurs.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 5cbd501b9ccb408ee23cb5c8ed9cde2689ef79fb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100599306"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>Examiner les erreurs à l’aide du tableau de bord d’ITSMC

Cet article contient des informations sur le tableau de bord de Connecteur de gestion des services informatiques (ITSMC). Le tableau de bord vous permet d’examiner l’état de votre connecteur.

## <a name="view-errors"></a>Afficher les erreurs

Pour afficher les erreurs dans le tableau de bord :

1. Sélectionnez **Toutes les ressources**, puis recherchez **ServiceDesk(*nom de votre espace de travail*)** .

   ![Capture d’écran montrant les ressources dans les services Azure.](media/itsmc-definition/create-new-connection-from-resource.png)

2. Sous **Sources de données de l’espace de travail** dans le volet gauche, sélectionnez **Connexions ITSM** :

   ![Capture d’écran montrant la sélection des connexions ITSM sous Sources de données de l’espace de travail.](media/itsmc-overview/add-new-itsm-connection.png)

3. Sous **Résumé**, dans la zone **Connecteur de gestion des services informatiques**, sélectionnez **Afficher le résumé** :

   ![Capture d’écran montrant le bouton Afficher le résumé.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. Quand un graphique apparaît dans la zone **Connecteur de gestion des services informatiques**, sélectionnez-le :

   ![Capture d’écran présentant la sélection d’un graphique.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. Le tableau de bord s’affiche. Utilisez-le pour vérifier l’état et les erreurs de votre connecteur.
   
   ![Capture d’écran montrant l’état du connecteur sur le tableau de bord.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>Comprendre les éléments du tableau de bord

Le tableau de bord contient des informations sur les alertes qui ont été envoyées à l’outil ITSM par le biais de ce connecteur. Le tableau de bord est divisé en quatre parties.

### <a name="created-work-items"></a>Éléments de travail créés 

Dans la zone **ÉLÉMENTS DE TRAVAIL CRÉÉS**, le graphique et le tableau ci-dessous contiennent le nombre d’éléments de travail par type. Si vous sélectionnez le graphique ou le tableau, vous pouvez voir plus de détails sur les éléments de travail.

![Capture d’écran montrant un élément de travail créé.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)

### <a name="affected-computers"></a>Ordinateurs concernés 

Dans la zone **ORDINATEURS CONCERNÉS**, le tableau répertorie les ordinateurs et leurs éléments de travail associés. En sélectionnant des lignes dans les tableaux, vous pouvez voir plus de détails sur les ordinateurs.

La table contient un nombre limité de lignes. Si vous souhaitez afficher toutes les lignes, sélectionnez **Afficher tout**.

![Capture d’écran montrant les ordinateurs concernés.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)

### <a name="connector-status"></a>État du connecteur 

Dans la zone **ÉTAT DU CONNECTEUR**, le graphique et le tableau ci-dessous contiennent des messages relatifs à l’état du connecteur. En sélectionnant le graphique ou les lignes dans le tableau, vous pouvez voir plus de détails sur les messages.

La table contient un nombre limité de lignes. Si vous souhaitez afficher toutes les lignes, sélectionnez **Afficher tout**.

Pour en savoir plus sur les messages du tableau, consultez [cet article](itsmc-dashboard-errors.md).

![Capture d’écran montrant l’état du connecteur.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)

### <a name="alert-rules"></a>Règles d'alerte 

Dans la zone **RÈGLES D’ALERTE**, le tableau contient des informations sur le nombre de règles d’alerte détectées. En sélectionnant des lignes dans le tableau, vous pouvez voir plus de détails sur les règles détectées.
    
La table contient un nombre limité de lignes. Si vous souhaitez afficher toutes les lignes, sélectionnez **Afficher tout**.

![Capture d’écran montrant des règles d’alerte.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)
