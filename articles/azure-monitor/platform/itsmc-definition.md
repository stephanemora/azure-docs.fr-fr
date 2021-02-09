---
title: Connecteur de gestion des services informatiques dans Log Analytics
description: Cet article fournit une vue d’ensemble du connecteur de gestion des services informatiques (ITSMC) et des informations sur son utilisation pour superviser et gérer des éléments de travail ITSM dans Log Analytics et résoudre rapidement les problèmes.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: ba32cfa4bc5cd0b41a210cf88fb598afc3064495
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492550"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Connecter Azure aux outils ITSM à l’aide du connecteur de gestion des services informatiques

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Cet article fournit des informations vous indiquant comment configurer Connecteur de gestion des services informatiques (ITSMC) dans Log Analytics pour gérer de manière centralisée vos éléments de travail Gestion des services informatiques (ITSM).

## <a name="add-it-service-management-connector"></a>Ajouter un connecteur de gestion des services informatiques

Avant de pouvoir créer une connexion, vous devez installer ITSMC.

1. Dans le portail Azure, sélectionnez **Créer une ressource** :

   ![Capture d’écran montrant l’élément de menu pour la création d’une ressource.](media/itsmc-overview/azure-add-new-resource.png)

2. Recherchez **Connecteur de gestion des services informatiques** dans la Place de Marché Azure. Sélectionnez ensuite **Créer** :

   ![Capture d’écran montrant le bouton Créer dans la Place de marché Azure.](media/itsmc-overview/add-itsmc-solution.png)

3. Dans la section **Espace de travail LA**, sélectionnez l’espace de travail Log Analytics où vous voulez installer ITSMC.
   > [!NOTE]
   > Vous pouvez installer ITSMC dans des espaces de travail Log Analytics uniquement dans les régions suivantes : USA Est, USA Ouest 2, USA Centre Sud, USA Centre-Ouest, US Gov Arizona, US Gov Virginie, Canada Centre, Europe Ouest, Royaume-Uni Sud, Asie Sud-Est, Japon Est, Inde Centre et Australie Sud-Est.

4. Dans la section **Espace de travail Log Analytics**, sélectionnez le groupe de ressources où vous voulez créer la ressource ITSMC :

   ![Capture d’écran montrant la section Espace de travail Log Analytics.](media/itsmc-overview/itsmc-solution-workspace.png)
   
   > [!NOTE]
   > Dans le cadre de la transition en cours de Microsoft Operations Management Suite (OMS) vers Azure Monitor, les espaces de travail OMS sont maintenant appelés *espaces de travail Log Analytics*.

5. Sélectionnez **OK**.

Quand la ressource ITSMC est déployée, une notification s’affiche en haut à droite de la fenêtre.

## <a name="create-an-itsm-connection"></a>Créer une connexion ITSM

Une fois que vous avez installé ITSMC, vous devez préparer votre outil ITSM pour autoriser la connexion à partir d’ITSMC. En fonction du produit ITSM auquel vous vous connectez, sélectionnez l’un des liens suivants pour obtenir des instructions :

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

Après avoir préparé votre outil ITSM, effectuez les étapes suivantes pour créer une connexion :

1. Dans **Toutes les ressources**, recherchez **ServiceDesk(*nom_de_votre_espace_de_travail*)**  :

   ![Capture d’écran montrant les ressources récentes dans le portail Azure.](media/itsmc-definition/create-new-connection-from-resource.png)

1. Sous **Sources de données de l’espace de travail** dans le volet gauche, sélectionnez **Connexions ITSM** :

   ![Capture d’écran montrant l’élément de menu Connexion ITSM.](media/itsmc-overview/add-new-itsm-connection.png)

1. Sélectionnez **Ajouter une connexion**.

1. Spécifiez les paramètres de connexion en fonction du produit ITSM que vous utilisez :

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   > Par défaut, ITSMC actualise les données de configuration de la connexion toutes les 24 heures. Pour actualiser instantanément les données de votre connexion avec les éventuelles modifications ou mises à jour du modèle, sélectionnez le bouton **Synchroniser** sur le volet de votre connexion :
   >
   > ![Capture d’écran montrant le bouton Synchroniser dans le volet de la connexion.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="create-itsm-work-items-from-azure-alerts"></a>Créer des éléments de travail ITSM à partir d’alertes Azure

Après avoir créé votre connexion ITSM, vous pouvez utiliser ITSMC pour créer des éléments de travail basés sur des alertes Azure dans votre outil ITSM. Pour créer les éléments de travail, vous utilisez l’action ITSM dans les groupes d’actions.

Les groupes d’actions offrent une méthode modulaire et réutilisable pour déclencher des actions pour vos alertes Azure. Vous pouvez utiliser des groupes d’actions avec des alertes de métriques, des alertes de journal d’activité et des alertes Log Analytics dans le portail Azure.

> [!NOTE]
> Après avoir créé la connexion ITSM, vous devez attendre 30 minutes que le processus de synchronisation se termine.

## <a name="define-a-template"></a>Définir un modèle

Certains types d’éléments de travail peuvent utiliser des modèles que vous définissez dans l’outil ITSM. L’utilisation de modèles vous permet de définir des champs qui seront automatiquement renseignés en fonction de valeurs fixes d’un groupe d’actions. Vous pouvez définir le modèle que vous souhaitez utiliser dans le cadre de la définition d’un groupe d’actions.

Pour créer un groupe d’actions :

1. Dans le portail Azure, sélectionnez **Alertes**.
2. Dans le menu situé en haut de l’écran, sélectionnez **Gérer les actions** :

    ![Capture d’écran montrant l’élément de menu Gérer les actions.](media/itsmc-overview/action-groups-selection-big.png)

   La fenêtre **Créer un groupe d’actions** s’affiche.

3. Sélectionnez l’**Abonnement** et le **Groupe de ressources** où vous voulez créer votre groupe d’actions. Indiquez les valeurs dans **Nom du groupe d’actions** et **Nom d’affichage** pour votre groupe d’actions. Ensuite, sélectionnez **Next: Notifications**.

    ![Capture d’écran montrant la fenêtre Créer un groupe d’actions.](media/itsmc-overview/action-groups-details.png)

4. Sous l’onglet **Notifications**, sélectionnez **Suivant : Actions**.
5. Sous l’onglet **Actions**, sélectionnez **ITSM** dans la liste **Type d’action**. Pour **Nom**, attribuez un nom à l’action. Sélectionnez ensuite le bouton de stylet qui représente **Modifier les détails**.

    ![Capture d’écran montrant les sélections pour la création d’un groupe d’actions.](media/itsmc-definition/action-group-pen.png)

6. Dans la liste **Abonnement**, sélectionnez l’abonnement qui contient votre espace de travail Log Analytics. Dans la liste **Connexion**, sélectionnez le nom de votre connecteur ITSM. Il sera suivi du nom de votre espace de travail. *MyITSMConnector(MyWorkspace)* en est un exemple.

7. Sélectionnez un type d’**Élément de travail**.

8. Si vous voulez remplir des champs prêts à l’emploi avec des valeurs fixes, sélectionnez **Utiliser un modèle personnalisé**. Sinon, choisissez un [modèle](#define-a-template) existant dans la liste **Modèles**, puis entrez les valeurs fixes dans les champs du modèle.

9. Dans la dernière section de l’interface de création d’un groupe d’actions ITSM, vous pouvez définir le nombre d’éléments de travail à créer pour chaque alerte.

   > [!NOTE]
   > Cette section concerne uniquement les alertes de recherche dans les journaux. Pour tous les autres types d’alerte, vous allez créer un élément de travail par alerte.

   * Si vous avez sélectionné **Incident** ou **Alerte** dans la liste déroulante **Élément de travail**, vous avez la possibilité de créer des éléments de travail individuels pour chaque élément de configuration.
    
     ![Capture d’écran montrant la zone de ticket ITSM avec Incident sélectionné comme élément de travail.](media/itsmc-overview/itsm-action-configuration.png)
    
     * Si vous cochez la case **Créer des éléments de travail individuels pour chaque élément de configuration**, chaque élément de configuration de chaque alerte crée un élément de travail. Étant donné que plusieurs alertes se produisent pour les mêmes éléments de configuration affectés, il y aura plus d’un élément de travail pour chaque élément de configuration.

       Par exemple, une alerte qui contient trois éléments de configuration créera trois éléments de travail. Une alerte qui contient un élément de configuration créera un élément de travail.
        
     * Si vous décochez la case **Créer des éléments de travail individuels pour chaque élément de configuration**, ITSMC crée un seul élément de travail pour chaque règle d’alerte et y ajoute tous les éléments de configuration concernés. Un nouvel élément de travail est créé si le précédent est fermé.

       >[!NOTE]
       > Dans ce cas, certaines des alertes déclenchées ne génèrent pas de nouveaux éléments de travail dans l’outil ITSM.

       Par exemple, une alerte qui contient trois éléments de configuration créera un élément de travail. Si une alerte pour la même règle d’alerte que l’exemple précédent comporte un élément de configuration, cet élément de configuration sera joint à la liste des éléments de configuration affectés dans l’élément de travail créé. Une alerte pour une règle d’alerte différente qui comporte un élément de configuration créera un élément de travail.

   * Si vous avez sélectionné **Événement** dans la liste déroulante **Élément de travail**, vous pouvez choisir de créer des éléments de travail individuels pour chaque entrée de journal ou pour chaque élément de configuration.
    
     ![Capture d’écran montrant la zone de ticket ITSM avec Événement sélectionné comme élément de travail.](media/itsmc-overview/itsm-action-configuration-event.png)

     * Si vous sélectionnez **Créer des éléments de travail individuels pour chaque entrée de journal (Le champ Élément de configuration n’est pas rempli. Peut entraîner un grand nombre d’éléments de travail.)** , un élément de travail est créé pour chaque ligne dans les résultats de recherche de la requête d’alerte de recherche de journal. Dans la charge utile de l’élément de travail, la propriété Description contient la ligne des résultats de la recherche.
      
     * Si vous sélectionnez **Créer des éléments de travail individuels pour chaque élément de configuration**, chaque élément de configuration de chaque alerte crée un élément de travail. Chaque élément de configuration peut avoir plusieurs éléments de travail dans le système ITSM. Cette option est la même que la case à cocher qui apparaît une fois que vous avez sélectionné **Incident** comme type d’élément de travail.

10. Sélectionnez **OK**.

Quand vous créez ou modifiez une règle d’alerte Azure, utilisez un groupe d’actions qui contient une action ITSM. Quand l’alerte se déclenche, l’élément de travail est créé ou mis à jour dans l’outil ITSM.

> [!NOTE]
> Pour plus d’informations sur les tarifs de l’action ITSM, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/monitor/) pour les groupes d’actions.
>
> Le champ de description courte de la définition de règle d’alerte est limité à 40 caractères quand vous l’envoyez à l’aide de l’action ITSM.

## <a name="next-steps"></a>Étapes suivantes

* [Résoudre les problèmes dans ITSMC](./itsmc-resync-servicenow.md)
