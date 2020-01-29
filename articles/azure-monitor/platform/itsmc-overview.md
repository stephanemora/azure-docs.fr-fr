---
title: IT Service Management Connector dans Azure Log Analytics | Microsoft Docs
description: Cet article fournit une vue d’ensemble du connecteur de gestion des services informatiques (ITSMC) et des informations sur l’utilisation de cette solution pour surveiller et gérer de manière centralisée les éléments de travail ITSM dans Azure Log Analytics et résoudre rapidement les problèmes éventuels.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: JYOTHIRMAISURI
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: 83d5b7ffb49a08d02d5dd34ad561ce725ead7e0e
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289133"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Connecter Azure aux outils ITSM à l’aide du connecteur de gestion des services informatiques

![Symbole d’IT Service Management Connector](media/itsmc-overview/itsmc-symbol.png)

Le connecteur de gestion des services informatiques (ITSMC) vous permet de connecter Azure et un produit/service ITSM pris en charge.

Des services Azure comme Log Analytics et Azure Monitor fournissent des outils pour détecter, analyser et résoudre les problèmes liés à vos ressources Azure et non-Azure. Toutefois, les éléments de travail liés à un problème résident en général dans un service ou produit ITSM. Le connecteur ITSM fournit une connexion bidirectionnelle entre Azure et les outils ITSM pour vous aider à résoudre les problèmes plus rapidement.

ITSMC prend en charge les connexions avec les outils ITSM suivants :

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

Avec ITSMC, vous pouvez :

-  Créer des éléments de travail dans l’outil ITSM, en fonction de vos alertes Azure (alertes de métriques, alertes de journal d’activité et alertes Log Analytics).
-  Si vous le souhaitez, vous pouvez synchroniser les données d’incidents et de demandes de modification de vos outils ITSM vers un espace de travail Azure Log Analytics.

En savoir plus sur les [conditions légales et la politique de confidentialité](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Vous pouvez commencer à utiliser le connecteur ITSM en effectuant les étapes suivantes :

1.  [Ajouter la solution de connecteur ITSM](#adding-the-it-service-management-connector-solution)
2.  Créer une connexion ITSM
3.  [Utiliser la connexion](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Ajout de la solution IT Service Management Connector

Avant de pouvoir créer une connexion, vous devez ajouter la solution de connecteur ITSM.

1. Dans le portail Azure, cliquez sur l’icône **+ Nouveau**.

   ![Nouvelle ressource Azure](media/itsmc-overview/azure-add-new-resource.png)

2. Recherchez **IT Service Management Connector** dans la Place de Marché et cliquez sur **Créer**.

   ![Ajouter la solution ITSMC](media/itsmc-overview/add-itsmc-solution.png)

3. Dans la section **Espace de travail OMS**, sélectionnez l’espace de travail Azure Log Analytics où vous souhaitez installer la solution.
   >[!NOTE]
   > * Dans le cadre de la transition en cours depuis Microsoft Operations Management Suite (OMS) vers Azure Monitor, les espaces de travail OMS sont maintenant appelé espaces de travail Log Analytics.
   > * Le connecteur ITSM peut uniquement être installé dans les espaces de travail Log Analytics des régions suivantes : USA Est, USA Ouest 2, USA Centre Sud, USA Centre-Ouest, Canada Centre, Europe Ouest, Royaume-Uni Sud, Asie Sud-Est, Japon Est, Inde Centre, Australie Sud-Est.

4. Dans la section **Paramètres de l’espace de travail OMS**, sélectionnez le groupe de ressources où vous souhaitez créer la ressource de la solution.

   ![Espace de travail ITSMC](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >Dans le cadre de la transition en cours depuis Microsoft Operations Management Suite (OMS) vers Azure Monitor, les espaces de travail OMS sont maintenant appelé espaces de travail Log Analytics.

5. Cliquez sur **Créer**.

Quand la ressource de la solution est déployée, une notification s’affiche en haut à droite de la fenêtre.


## <a name="creating-an-itsm--connection"></a>Création d’une connexion ITSM

Une fois que vous avez installé la solution, vous pouvez créer une connexion.

Pour créer une connexion, vous devez préparer votre outil ITSM afin d’autoriser la connexion à partir de la solution Connecteur ITSM.  

Selon le produit ITSM auquel vous vous connectez, utilisez les étapes suivantes :

- [System Center Service Manager (SCSM)](../../azure-monitor/platform/itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](../../azure-monitor/platform/itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](../../azure-monitor/platform/itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](../../azure-monitor/platform/itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Une fois que vous avez préparé votre outils ITSM, suivez les étapes ci-dessous pour créer une connexion :

1. Accédez à **Toutes les ressources** et recherchez **ServiceDesk(nom_de_votre_espace_de_travail)** .
2. Sous **Sources de données de l’espace de travail** dans le volet gauche, cliquez sur **Connexions ITSM**.
   ![Connexions ITSM](media/itsmc-overview/itsm-connections.png)

   Cette page affiche la liste des connexions.
3. Cliquez sur **Ajouter une connexion**.

   ![Ajouter une connexion ITSM](media/itsmc-overview/add-new-itsm-connection.png)

4. Spécifiez les paramètres de connexion comme décrit dans l’article [Configuration de la connexion ITSM avec vos produits/services ITSM](../../azure-monitor/platform/itsmc-connections.md).

   > [!NOTE]
   >
   > Par défaut, ITSMC actualise les données de configuration de la connexion toutes les 24 heures. Pour actualiser instantanément les données de votre connexion avec vos modifications ou mises à jour du modèle, cliquez sur le bouton **Synchroniser** sur le panneau de votre connexion.

   ![Actualisation de la connexion](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>Utilisation de la solution
   Grâce au connecteur ITSM, vous pouvez créer des éléments de travail à partir d’alertes Azure, d’alertes Log Analytics et d’enregistrements de journal Log Analytics.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Créer des éléments de travail ITSM à partir d’alertes Azure

Une fois votre connexion ITSM créée, vous pouvez créer dans votre outil ITSM des éléments de travail basés sur des alertes Azure, à l’aide de **l’Action ITSM** dans **Groupes d’actions**.

Les groupes d’actions offrent une méthode modulaire et réutilisable pour déclencher des actions pour vos alertes Azure. Vous pouvez utiliser des groupes d’actions avec des alertes de métriques, des alertes de journal d’activité et des alertes Azure Log Analytics dans le portail Azure.

Procédez comme suit :

1. Dans le portail Azure, cliquez sur **Moniteur**.
2. Dans le volet gauche, cliquez sur **Groupes d’actions**. La fenêtre **Ajouter un groupe d’actions** s’affiche.

    ![Groupes d’actions](media/itsmc-overview/action-groups.png)

3. Attribuez un **Nom** et un **Nom court** à votre groupe d’actions. Sélectionnez le **Groupe de ressources** et l’**Abonnement** pour lesquels vous voulez créer votre groupe d’actions.

    ![Détails du groupe d’actions](media/itsmc-overview/action-groups-details.png)

4. Dans la liste d’actions, sélectionnez **ITSM** dans le menu déroulant **Type d’action**. Entrez un **Nom** pour l’action et cliquez sur **Modifier les détails**.
5. Sélectionnez l’**Abonnement** dans lequel se trouve votre espace de travail Log Analytics. Sélectionnez le nom de **Connexion** (le nom de votre connecteur ITSM) suivi du nom de votre espace de travail. Par exemple, « MaSolutionTSMMConnector(MonEspaceDeTravail) ».

    ![Détails de l’action ITSM](media/itsmc-overview/itsm-action-details.png)

6. Sélectionnez le type d’**Élément de travail** dans le menu déroulant.
   Choisissez d’utiliser un modèle existant ou renseignez les champs requis par votre produit ITSM.
7. Cliquez sur **OK**.

Lorsque vous créez/modifiez une règle d’alerte Azure, utilisez un groupe d’actions qui contient une action ITSM. Quand l’alerte se déclenche, l’élément de travail est créé/mis à jour dans l’outil ITSM.

> [!NOTE]
>
> Pour plus d’informations sur la tarification de l’action ITSM, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/monitor/) pour les groupes d’actions.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Visualiser et analyser les données d’incident et de demande de modification

En fonction de la configuration choisie lors de l’établissement d’une connexion, le connecteur ITSM peut synchroniser jusqu’à 120 jours de données d’incidents et de demande de modification. Le schéma d’enregistrement de journal pour ces données est fourni dans la [section suivante](#additional-information).

Vous pouvez visualiser les données d’incidents et de demande de modification à l’aide du tableau de bord Connecteur ITSM dans la solution.

![Écran Log Analytics](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Le tableau de bord fournit également des informations sur l’état du connecteur, que vous pouvez utiliser comme point de départ pour analyser les problèmes au niveau de vos connexions.

Vous pouvez également visualiser les incidents synchronisés avec les ordinateurs concernés, dans la solution Service Map.

La solution Carte de service détecte automatiquement les composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services. Elle vous permet d’afficher les serveurs comme des systèmes interconnectés qui fournissent des services critiques. Carte de service affiche les connexions entre les serveurs, les processus et les ports sur n’importe quelle architecture connectée à TCP, sans configuration requise autre que l’installation d’un agent. [Plus d’informations](../../azure-monitor/insights/service-map.md)

Si vous utilisez la solution Service Map, vous pouvez afficher les éléments de service d’assistance créés dans les solutions ITSM comme dans l’exemple suivant :

![Écran Log Analytics](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

Plus d’informations : [Service Map](../../azure-monitor/insights/service-map.md)


## <a name="additional-information"></a>Informations supplémentaires

### <a name="data-synced-from-itsm-product"></a>Données synchronisées à partir du produit ITSM
Les incidents et les demandes de modification sont synchronisés entre votre produit ITSM et votre espace de travail Log Analytics en fonction de la configuration de la connexion.

Les informations suivantes présentent des exemples de données collectées par ITSM :

> [!NOTE]
>
> Selon le type d’élément de travail importé dans Log Analytics, l’événement **ServiceDesk_CL** contient les champs suivants :

**Élément de travail :** **Incidents**  
ServiceDeskWorkItemType_s="Incident"

**Fields**

- ServiceDeskConnectionName
- ID du service d’assistance
- State
- Urgence
- Impact
- Priority
- Escalade
- Créé par
- Résolu par
- Fermé par
- Source
- Affecté à
- Category
- Intitulé
- Description
- Date de création
- Date de fermeture
- Date de résolution
- Date de dernière modification
- Computer


**Élément de travail :** **Demandes de modification**

ServiceDeskWorkItemType_s="ChangeRequest"

**Fields**
- ServiceDeskConnectionName
- ID du service d’assistance
- Créé par
- Fermé par
- Source
- Affecté à
- Intitulé
- Type
- Category
- State
- Escalade
- État conflictuel
- Urgence
- Priority
- Risque
- Impact
- Affecté à
- Date de création
- Date de fermeture
- Date de dernière modification
- Date de la demande
- Date de début prévue
- Date de fin prévue
- Date de début du travail
- Date de fin du travail
- Description
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>Données de sortie pour un incident ServiceNow

| Champ Log Analytics | Champ ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | State |
| Urgency_s |Urgence |
| Impact_s |Impact|
| Priority_s | Priority |
| CreatedBy_s | Ouvert par |
| ResolvedBy_s | Résolu par|
| ClosedBy_s  | Fermé par |
| Source_s| Type de contact |
| AssignedTo_s | Affecté à  |
| Category_s | Category |
| Title_s|  Description courte |
| Description_s|  Notes |
| CreatedDate_t|  Ouvert |
| ClosedDate_t| Fermé|
| ResolvedDate_t|Résolu|
| Computer  | Élément de configuration |

## <a name="output-data-for-a-servicenow-change-request"></a>Données de sortie pour une demande de modification ServiceNow

| Log Analytics | Champ ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | Demandé par |
| ClosedBy_s | Fermé par |
| AssignedTo_s | Affecté à  |
| Title_s|  Description courte |
| Type_s|  Type |
| Category_s|  Category |
| CRState_s|  State|
| Urgency_s|  Urgence |
| Priority_s| Priority|
| Risk_s| Risque|
| Impact_s| Impact|
| RequestedDate_t  | Date demandée |
| ClosedDate_t | Date de fermeture |
| PlannedStartDate_t  |     Date de début prévue |
| PlannedEndDate_t  |   Date de fin prévue |
| WorkStartDate_t  | Date de début réelle |
| WorkEndDate_t | Date de fin réelle|
| Description_s | Description |
| Computer  | Élément de configuration |


## <a name="troubleshoot-itsm-connections"></a>Dépanner les connexions ITSM
1. Si la connexion échoue à partir de l’interface utilisateur de la source connectée avec un message **Erreur lors de l’enregistrement de la connexion**, procédez comme suit :
   - Pour les connexions ServiceNow, Cherwell et Provance,  
   - vérifiez que vous avez correctement entré le nom d’utilisateur, le mot de passe, l’ID client et la clé secrète client pour chacune des connexions.  
   - Vérifiez que vous disposez de privilèges suffisants dans le produit ITSM correspondant pour établir la connexion.  
   - Pour les connexions Service Manager,  
   - vérifiez que l’application web est correctement déployée et que la connexion hybride est créée. Pour vérifier que la connexion est établie avec l’ordinateur Service Manager local, accédez à l’URL de l’application web, comme décrit dans la documentation concernant l’établissement d’une [connexion hybride](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection).  

2. Si les données de ServiceNow ne sont pas synchronisées dans Log Analytics, vérifiez que l’instance ServiceNow n’est pas en état de veille. Parfois, les instances de développement ServiceNow entrent en veille quand elles restent longtemps inactives. Autrement, signalez le problème.
3. Si des alertes Log Analytics se déclenchent mais qu’aucun élément de travail n’est créé dans le produit ITSM ou qu’aucun élément de configuration n’est créé/lié à des éléments de travail ou pour obtenir d’autres informations génériques, examinez les emplacements suivants :
   -  ITSMC : la solution affiche un résumé des connexions, éléments de travail, ordinateurs, etc. Cliquez sur la vignette indiquant **État du connecteur**. Vous accédez alors à **Recherche dans les journaux** avec la requête appropriée. Pour plus d’informations, consultez les enregistrements de journal pour lesquels LogType_S a la valeur ERROR.
   - Page **Recherche dans les journaux** : examinez les erreurs/informations connexes directement en utilisant la requête `*`ServiceDeskLog_CL`*`.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Résoudre les problèmes de déploiement de l’application web Service Manager
1.  En cas de problèmes de déploiement d’application web, vérifiez que vous disposez des autorisations suffisantes dans l’abonnement mentionné pour créer/déployer des ressources.
2.  Si vous obtenez l’erreur **« Référence d’objet non définie sur une instance d’un objet »** pendant l’exécution du [script](itsmc-service-manager-script.md), vérifiez que vous avez entré des valeurs valides sous la section **Configuration utilisateur**.
3.  Si vous échouez à créer l’espace de noms du relais Service Bus, vérifiez que le fournisseur de ressources requis est inscrit dans l’abonnement. S’il n’est pas inscrit, créez manuellement l’espace de noms Service Bus Relay à partir du portail Azure. Vous pouvez également le créer lors de la [création de la connexion hybride](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection) à partir du portail Azure.


## <a name="contact-us"></a>Nous contacter

Pour toute question ou tout commentaire à propos de la solution IT Service Management Connector, contactez-nous à l’adresse [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Étapes suivantes
[Ajouter des produits/services ITSM à IT Service Management Connector](../../azure-monitor/platform/itsmc-connections.md).
