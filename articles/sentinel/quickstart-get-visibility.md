---
title: Démarrage rapide Azure Sentinel - Bien démarrer avec Azure Sentinel en préversion | Microsoft Docs
description: Démarrage rapide Azure Sentinel - Bien démarrer avec Azure Sentinel en préversion
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5a4ae93c-d648-41fb-8fb8-96a025d2f73e
ms.service: sentinel
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: 07839c6372e0048048b62fbfedd58bfa9b9942f5
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65207512"
---
# <a name="quickstart-get-started-with-azure-sentinel-preview"></a>Démarrage rapide : Bien démarrer avec Azure Sentinel en préversion

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Dans ce démarrage rapide, vous allez apprendre rapidement être capable d’afficher et de surveiller ce qui se passe au sein de votre environnement à l’aide d’Azure Sentinel. Une fois que vous avez connecté vos sources de données à Azure Sentinel, vous obtenez une visualisation et une analyse instantanées de ces données pour savoir ce qui se passe sur toutes vos sources de données connectées. Sentinel Azure vous propose des tableaux de bord qui vous donnent accès à toute la puissance des outils déjà disponibles dans Azure, ainsi que des tableaux et des graphiques intégrés pour vous fournir l’analytique pour vos journaux et requêtes. Vous pouvez utiliser les tableaux de bord intégrés ou en créer de nouveaux facilement, soit de toutes pièces ou soit en les basant sur des tableaux de bord existants. 

## <a name="get-visualization"></a>Visualisation

Pour visualiser et bénéficier d’une analyse de ce qui se passe dans votre environnement, examinons tout d’abord le tableau de bord général pour avoir une idée de la sécurité de votre organisation. Vous pouvez cliquer sur chaque élément de ces vignettes pour explorer les données brutes à partir desquelles elles ont été créées. Pour vous aider à réduire le niveau de bruit et à réduire le nombre d’alertes que vous devez examiner, Azure Sentinel utilise une technique de fusion pour mettre en corrélation les alertes et les incidents. Les **incidents** sont des groupes d’alertes liées qui, prises ensemble, constituent un incident que vous pouvez examiner et résoudre.

- Dans le portail Azure, sélectionnez Azure Sentinel, puis sélectionnez l’espace de travail que vous souhaitez surveiller.

  ![Vue d’ensemble d’Azure Sentinel](./media/qs-get-visibility/overview.png)

- La barre d’outils en haut vous indique combien d’événements vous avez reçus pendant la période sélectionnée et compare cette informations au 24 heures précédentes. La barre d’outils vous informe sur ces événements, sur les alertes qui ont été déclenchées (le petit nombre représente le changement au cours des dernières 24 heures) et elle vous indique ensuite pour ces événements combien sont ouverts, en cours et fermés. Vérifiez s’il n’y a pas d’augmentation ou de réduction importante dans le nombre d’événements. En cas de réduction importante, cela peut être dû au fait qu’une connexion a arrêté d’envoyer des informations à Azure Sentinel. En cas d’augmentation, un événement suspect peut s’être produit. Vérifiez si vous voyez de nouvelles alertes.

   ![Entonnoir Azure Sentinel](./media/qs-get-visibility/funnel.png)

Le corps de la page de vue d’ensemble donne un aperçu de l’état de la sécurité de votre espace de travail :

- **Événements et alertes au fil du temps** : présente le nombre d’événements et le nombre d’alertes créés à partir de ces événements. Si vous voyez un pic inhabituel, vous devriez voir des alertes. Si vous voyez quelque chose d’inhabituel avec un pic d’événements mais que vous ne voyez pas d’alertes, cela peut indiquer un problème.

- **Événements potentiellement malveillants** : lorsque du trafic est détecté venant de sources considérées comme malveillantes, Azure Sentinel vous alerte sur la carte. Si vous voyez du orange, il s’agit de trafic entrant : quelqu’un tente d’accéder à votre organisation depuis une adresse IP malveillante connue. Si vous voyez une activité sortante (en rouge), cela signifie que des données de votre réseau sont diffusées hors de votre organisation vers une adresse IP malveillante connue.

   ![Carte Azure Sentinel](./media/qs-get-visibility/map.png)


- **Incidents récents** : pour voir les incidents récents, leur niveau de gravité et le nombre d’alertes associés aux incidents. Si vous voyez des pics soudains dans un type spécifique d’alerte, cela peut signifier qu’une attaque est en cours. Par exemple, si vous constatez un pic soudain de 20 événements Pass-the-hash depuis Azure ATP, il est possible que quelqu’un tente actuellement une attaque.

- **Anomalies de source de données** : les analystes de données de Microsoft ont créé des modèles qui recherchent constamment les anomalies dans les données de vos sources de données. S’il n’y a pas d’anomalie, rien ne s’affiche. Si des anomalies sont détectées, vous devez allez voir ce qui s’est produit. Par exemple, cliquez sur le pic d’activité Azure. Vous pouvez cliquer sur le **graphique** pour voir quand le pic s’est produit, puis filtrer les activités qui se sont produites pendant cette période pour voir ce qui a provoqué le pic.

   ![Carte Azure Sentinel](./media/qs-get-visibility/anomolies.png)

## Utiliser les tableaux de bord intégrés<a name="dashboards"></a>

Les tableaux de bord intégrés fournissent des données intégrées à partir de vos sources de données connectées pour vous permettre de plonger dans les événements générés dans ces services. Les tableaux de bord intégrés comprennent l’ID Azure, les événements d’activité Azure et en local, qui peuvent être des données venant d’événements Windows sur les serveurs, d’alertes de premier niveau venant de tiers, par exemple les journaux de trafic des pare-feu, Office 365 et les protocoles non sécurisés basés sur les événements Windows.

1. Sous **Paramètres**, sélectionnez **Tableaux de bord**. Sous **installé**, vous pouvez voir tous les tableaux de bord installés. Sous **Tous**, vous pouvez voir la galerie complète de tableaux de bord intégrés qu’il est possible d’installer. 
2. Recherchez un tableau de bord spécifique pour afficher l’intégralité de la liste et la description de ce que chacun de ces tableaux propose. 
3. En supposant que vous utilisiez Azure AD, pour être opérationnel avec Azure Sentinel, nous vous recommandons d’installer au moins les tableaux de bord suivants :
   - **Azure AD** : Utilisez une ou plusieurs des actions suivantes :
       - **connexions Azure AD** analyse les connexions pour détecter les anomalies. Ce tableau de bord fournit les connexions ayant échoué venant des applications, des périphériques et des emplacements afin que vous puissiez vérifier en un clin d’œil s’il se produit quelque chose d’inhabituel. Faites attention lorsque plusieurs connexions échouent. 
       - **Journaux d’audit Azure AD** analyse les activités d’administration, comme la modification des utilisateurs (ajout, suppression, etc.), la création de groupe et les modifications.  

   - Ajouter un tableau de bord à votre pare-feu. Par exemple, ajoutez le tableau de bord Palo Alto. Le tableau de bord analyse le trafic de votre pare-feu et vous offre des corrélations entre les données du pare-feu et les événements concernant les menaces et met en évidence les événements suspects dans les entités. Les tableaux de bord vous fournissent des informations sur les tendances de votre trafic et vous permettent d’explorer au niveau du détail les résultats et de les filtrer. 

      ![Tableau de bord Palo Alto](./media/qs-get-visibility/palo-alto-week-query.png)


Vous pouvez personnaliser les tableaux de bord en modifiant la requête principale ![bouton](./media/qs-get-visibility/edit-query-button.png). Vous pouvez cliquer sur le bouton ![bouton](./media/qs-get-visibility/go-to-la-button.png) pour accéder à [Log Analytics et modifier la requête](../azure-monitor/log-query/get-started-portal.md). Vous pouvez sélectionner les points de suspension (...) et sélectionner **Personnaliser les données de la vignette**, pour modifier le filtre de temps ou supprimer certaines vignettes du tableau de bord.

Pour plus d’informations sur l’utilisation des requêtes, consultez [Tutoriel : Données visuelles dans Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Ajouter une nouvelle vignette

Si vous souhaitez ajouter une nouvelle vignette, vous pouvez l’ajouter à un tableau de bord existant, à un tableau de bord que vous avez créé ou à un tableau de bord intégré d’Azure Sentinel. 
1. Dans Log Analytics, créez une vignette selon les instructions figurant dans [Tutoriel : Données visuelles dans Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. Une fois la vignette créée, sous **Épingler**, sélectionnez le tableau de bord dans lequel vous voulez que la vignette apparaisse.

## <a name="create-new-dashboards"></a>Créer des tableaux de bord
Vous pouvez créer un nouveau tableau de bord de toutes pièces ou utiliser un tableau de bord intégré comme point de départ de votre nouveau tableau de bord.

1. Pour créer un tout nouveau tableau de bord, sélectionnez **Tableaux de bord**, puis **+Nouveau tableau de bord**.
2. Sélectionnez l’abonnement dans lequel est créé dans le tableau de bord et donnez-lui un nom descriptif. Chaque tableau de bord est une ressource Azure comme n’importe quelle autre et vous pouvez affecter des rôles (RBAC) pour en définir et en limiter l’accès. 
3. Pour l’activer et pour qu’il apparaisse dans vos tableaux de bord pour y épingler des visualisations, vous devez le partager. Cliquez sur **Partager**, puis sur **Gérer les utilisateurs**. 
 
1. Utilisez les options **Vérifier l’accès** et **Attributions de rôles** comme vous le feriez pour toute autre ressource Azure. Pour plus d’informations, consultez [Partager des tableaux de bord Azure avec le contrôle d’accès basé sur les rôles](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-dashboard-examples"></a>Exemples de nouveaux tableaux de bord

L’exemple de requête suivant vous permet de comparer les tendances dans le trafic entre les semaines. Vous pouvez facilement changer le fournisseur et la source de données où vous exécutez votre requête. Cet exemple utilise SecurityEvent dans Windows. Vous faire qu’il s’exécute sur AzureActivity ou CommonSecurityLog sur n’importe quel autre pare-feu.

     |where DeviceVendor = = "Palo Alto Networks":
      // week over week query
      SecurityEvent
      | where TimeGenerated > ago(14d)
      | summarize count() by bin(TimeGenerated, 1d)
      | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)


Vous souhaitez peut-être créer une requête qui incorpore des données provenant de plusieurs sources. Vous pouvez créer une requête qui examine les journaux d’audit Azure Active Directory pour contrôler les utilisateurs qui viennent d’y être créés, puis vérifie vos journaux Azure pour voir si ces utilisateurs ont commencé à apporter des modifications dans l’attribution des rôles dans les 24 heures qui suivent leur création. Ce type d’activité suspecte s’affiche sur ce tableau de bord :

    AuditLogs
    | where OperationName == "Add user"
    | project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
    | join (AzureActivity
    | where OperationName == "Create role assignment"
    | project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
    | project-away user1

Vous pouvez créer différents tableaux de bord en fonction du rôle de la personne qui examine les données et de ce que cette personne recherche. Par exemple, vous pouvez créer pour votre administrateur réseau un tableau de bord qui reprend les données du pare-feu. Vous pouvez également créer des tableaux de bord en fonction de la fréquence à laquelle vous souhaitez les examiner, par exemple si certains éléments doivent être consultés tous les jours et d’autres toutes les heures. Vous pouvez aussi par exemple examiner les connexions Azure AD toutes les heures pour y rechercher les éventuelles anomalies. 

## <a name="create-new-detections"></a>Créer de nouvelles détections

Générez des détections sur les [sources de données que vous avez connectées à Azure Sentinel](connect-data-sources.md) afin d’investiguer les menaces qui pèsent sur votre organisation.

Quand vous créez une détection, tirez parti des détections intégrées élaborées par les chercheurs en sécurité de Microsoft qui sont adaptées aux sources de données que vous avez connectées.

1. [Dans la communauté GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Detections), accédez au dossier **Detections** et sélectionnez les dossiers appropriés.
   ![dossiers appropriés](./media/qs-get-visibility/detection-folders.png)
 
3.  Accédez à l’onglet **Analytique** et sélectionnez **Ajouter**.
   ![créer la règle dans Log Analytics](./media/qs-get-visibility/query-params.png)

3.  Copiez tous les paramètres dans la règle et cliquez sur **Créer**.
   ![créer une règle d’alerte](./media/qs-get-visibility/create-alert-rule.png)

 
## <a name="next-steps"></a>Étapes suivantes
Dans ce démarrage rapide, vous avez appris à prendre en main Azure Sentinel. Passez au tutoriel pour savoir [comment détecter les menaces](tutorial-detect-threats.md).
> [!div class="nextstepaction"]
> [Détecter les menaces](tutorial-detect-threats.md) pour automatiser vos réponses aux menaces.

