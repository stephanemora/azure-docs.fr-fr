---
title: Connecteur de gestion des services informatiques dans Log Analytics
description: Cet article fournit une vue d’ensemble du connecteur de gestion des services informatiques (ITSMC) et des informations sur son utilisation pour superviser et gérer des éléments de travail ITSM dans Log Analytics et résoudre rapidement les problèmes.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 717a1bc4361ba4a7366f4864c1fe44f93b6f4b5e
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127853"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Connecter Azure aux outils ITSM à l’aide du connecteur de gestion des services informatiques

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Cet article fournit des informations vous indiquant comment configurer le connecteur de gestion des services informatiques (ITSMC) dans Log Analytics pour gérer de manière centralisée vos éléments de travail.

## <a name="add-it-service-management-connector"></a>Ajouter un connecteur de gestion des services informatiques

Avant de pouvoir créer une connexion, vous devez ajouter ITSMC.

1. Dans le portail Azure, sélectionnez **Créer une ressource** :

   ![Capture d’écran montrant l’élément de menu Créer une ressource.](media/itsmc-overview/azure-add-new-resource.png)

2. Recherchez **Connecteur de gestion des services informatiques** dans la Place de Marché Azure. Sélectionnez **Créer** :

   ![Capture d’écran montrant le bouton Créer dans la Place de marché Azure.](media/itsmc-overview/add-itsmc-solution.png)

3. Dans la section **Espace de travail LA**, sélectionnez l’espace de travail Azure Log Analytics où vous voulez installer ITSMC.
   >[!NOTE]
   >
   > * ITSMC peut être installé uniquement dans les espaces de travail Log Analytics des régions suivantes : USA Est, USA Ouest 2, USA Centre Sud, USA Centre-Ouest, US Gov Arizona, US Gov Virginie, Canada Centre, Europe Ouest, Royaume-Uni Sud, Asie Sud-Est, Japon Est, Inde Centre et Australie Sud-Est.

4. Dans la section **Espace de travail Log Analytics**, sélectionnez le groupe de ressources où vous voulez créer la ressource ITSMC :

   ![Capture d’écran montrant la section Espace de travail Log Analytics.](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >Dans le cadre de la transition en cours de Microsoft Operations Management Suite (OMS) vers Azure Monitor, les espaces de travail OMS sont maintenant appelés *espaces de travail Log Analytics*.

5. Sélectionnez **OK**.

Quand la ressource ITSMC est déployée, une notification s’affiche en haut à droite de la fenêtre.

## <a name="create-an-itsm-connection"></a>Créer une connexion ITSM

Une fois que vous avez installé ITSMC, vous pouvez créer une connexion.

Pour créer une connexion, vous devez préparer votre outil ITSM afin d’autoriser la connexion à partir d’ITSMC.  

En fonction du produit ITSM auquel vous vous connectez, sélectionnez l’un des liens suivants pour obtenir des instructions :

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

Après avoir préparé vos outils ITSM, effectuez les étapes suivantes pour créer une connexion :

1. Dans **Toutes les ressources**, recherchez **ServiceDesk(*nom_de_votre_espace_de_travail*)**  :

   ![Capture d’écran montrant les ressources récentes dans le portail Azure.](media/itsmc-definition/create-new-connection-from-resource.png)

1. Sous **Sources de données de l’espace de travail** dans le volet gauche, sélectionnez **Connexions ITSM** :

   ![Capture d’écran montrant l’élément de menu Connexion ITSM.](media/itsmc-overview/add-new-itsm-connection.png)
1. Sélectionnez **Ajouter une connexion**.

1. Spécifiez les paramètres de connexion comme décrit en fonction des produits/services ITSM :

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   >
   > Par défaut, ITSMC actualise les données de configuration de la connexion toutes les 24 heures. Pour actualiser instantanément les données de votre connexion avec les éventuelles modifications ou mises à jour du modèle, sélectionnez le bouton **Synchroniser** sur le panneau de votre connexion :
   >
   > ![Capture d’écran montrant le bouton Synchroniser dans le panneau de connexion.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="use-itsmc"></a>Utiliser ITSMC

   Vous pouvez utiliser ITSM pour créer des alertes à partir des alertes Azure Monitor dans l’outil ITSM.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Créer des éléments de travail ITSM à partir d’alertes Azure

Après avoir créé votre connexion ITSM, vous pouvez créer des éléments de travail basés sur des alertes Azure dans votre outil ITSM. Pour créer les éléments de travail, vous utilisez l’action ITSM dans les groupes d’actions.

Les groupes d’actions offrent une méthode modulaire et réutilisable pour déclencher des actions pour vos alertes Azure. Vous pouvez utiliser des groupes d’actions avec des alertes de métriques, des alertes de journal d’activité et des alertes Azure Log Analytics dans le portail Azure.

> [!NOTE]
> Après avoir créé la connexion ITSM, vous devez attendre 30 minutes que le processus de synchronisation se termine.

### <a name="template-definitions"></a>Définitions des modèles

   Certains types d’éléments de travail peuvent utiliser des modèles définis par l’outil ITSM.
L’utilisation de modèles vous permet de définir des champs qui seront automatiquement renseignés en fonction de valeurs fixes définies comme faisant partie du groupe d’actions. Vous définissez les modèles dans l’outil ITSM.
Vous pouvez définir le modèle que vous souhaitez utiliser dans le cadre de la définition du groupe d’actions.

Exécutez la procédure suivante pour créer des groupes d’actions :

1. Dans le portail Azure, sélectionnez **Alertes**.
2. Dans le menu situé en haut de l’écran, sélectionnez **Gérer les actions** :

    ![Capture d’écran montrant l’élément de menu Gérer les actions.](media/itsmc-overview/action-groups-selection-big.png)

   La fenêtre **Créer un groupe d’actions** s’affiche.

3. Sélectionnez l’**Abonnement** et le **Groupe de ressources** où vous voulez créer votre groupe d’actions. Indiquez un **Nom du groupe d’actions** et un **Nom d’affichage** pour votre groupe d’actions. Sélectionnez **Suivant : Notifications**.

    ![Capture d’écran montrant la fenêtre Créer un groupe d’actions.](media/itsmc-overview/action-groups-details.png)

4. Dans la liste des notifications, sélectionnez **Suivant : Actions**.
5. Dans la liste des actions, sélectionnez **ITSM** dans la liste **Type d’action**. Spécifiez un **nom** pour l’action. Sélectionnez le bouton de stylet qui représente **Modifier les détails**.

    ![Capture d'écran montrant la définition d’un groupe d'actions.](media/itsmc-definition/action-group-pen.png)

6. Dans la liste **Abonnement**, sélectionnez l’abonnement dans lequel se trouve votre espace de travail Log Analytics. Dans la liste **Connexion**, sélectionnez le nom de votre connecteur ITSM. Il sera suivi du nom de votre espace de travail. Par exemple, MonConnecteurITSM(MonEspaceDeTravail).

7. Sélectionnez un type d’**Élément de travail**.

8. Si vous voulez remplir des champs prêts à l’emploi avec des valeurs fixes, sélectionnez **Utiliser un modèle personnalisé**. Sinon, choisissez un [modèle](#template-definitions) existant dans la liste **Modèles**, puis entrez les valeurs fixes dans les champs du modèle.

9. Dans la dernière section de la définition de groupe ITSM, vous pouvez définir le nombre d’éléments de travail à créer pour chaque alerte.

    >[!NOTE]
    >
    > * Cette section concerne uniquement les alertes de recherche dans les journaux.
    > * Pour tous les autres types d’alertes, un élément de travail est créé par alerte.

    * Dans un cas, vous sélectionnez dans la liste déroulante « Elément de travail », « Incident » ou « Alerte » : ![Capture d’écran montrant la fenêtre Incident ITSM.](media/itsmc-overview/itsm-action-configuration.png)
        * Si vous cochez la case **« Créer des éléments de travail individuels pour chaque élément de configuration »** , chaque élément de configuration de chaque alerte crée un élément de travail. En présence de plusieurs alertes pour les mêmes éléments de configuration affectés, il y aura plusieurs éléments de travail pour chaque élément de configuration.

             Exemple :
             1) L’alerte 1 avec trois éléments de configuration, A, B et C, crée trois éléments de travail.
             2) L’alerte 2 avec un élément de configuration, A, crée un élément de travail.

        * Si vous décochez la case **« Créer des éléments de travail individuels pour chaque élément de configuration** », le connecteur ITSM crée un seul élément de travail pour chaque règle d’alerte et y ajoute tous les éléments de configuration concernés. Un nouvel élément de travail est créé si le précédent est fermé.

        >[!NOTE]
        > Dans ce cas, une partie de l’alerte déclenchée ne génère pas de nouveaux éléments de travail dans l’outil ITSM.

        Exemple :
         1) L’alerte 1 avec trois éléments de configuration, A, B et C, crée un élément de travail.
         2) L’alerte 2 pour la même règle d’alerte que l’étape a avec un élément de configuration : D, D sera attaché à la liste des éléments de configuration concrnés dans l’élément de travail créé à l’étape a.
         3) L’alerte 3 pour une autre règle d’alerte avec un élément de configuration, E, crée un élément de travail.

    * Dans un cas, vous sélectionnez dans la liste déroulante « Elément de travail », « Event » : ![Capture d’écran montrant la fenêtre Événement ITSM.](media/itsmc-overview/itsm-action-configuration-event.png)

        * Si vous sélectionnez **« Créer des éléments de travail individuels pour chaque entrée de journal (le champ d’élément de configuration n’est pas rempli. Peut entraîner un grand nombre d’éléments de travail.) »** dans la sélection des cases d’option, un élément de travail est créé pour chaque ligne dans les résultats de recherche de la requête d’alerte de recherche de journal. Dans la charge utile de l’élément de travail, la propriété Description contient la ligne des résultats de la recherche.
        * Si vous sélectionnez **« Créer des éléments de travail individuels pour chaque élément de configuration »** dans la sélection des cases d’option, chaque élément de configuration de chaque alerte crée un élément de travail. Il peut y avoir plusieurs éléments de travail par élément de configuration dans le système ITSM. Le comportement est le même qu’en cochant la case dans la section Incident/alerte.

10. Sélectionnez **OK**.

Quand vous créez ou modifiez une règle d’alerte Azure, utilisez un groupe d’actions qui contient une action ITSM. Quand l’alerte se déclenche, l’élément de travail est créé ou mis à jour dans l’outil ITSM.

> [!NOTE]
>
>- Pour plus d’informations sur les tarifs de l’action ITSM, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/monitor/) pour les groupes d’actions.
>
>
>- Le champ de description courte de la définition de règle d’alerte est limité à 40 caractères quand vous l’envoyez à l’aide de l’action ITSM.

## <a name="next-steps"></a>Étapes suivantes

* [Résolution des problèmes dans le connecteur ITSM](./itsmc-resync-servicenow.md)
