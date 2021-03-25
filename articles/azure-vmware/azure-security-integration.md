---
title: Protégez vos machines virtuelles Azure VMware Solution avec l’intégration Azure Security Center
description: Protégez vos machines virtuelles Azure VMware Solution avec les outils de sécurité natifs d’Azure à partir du tableau de bord Azure Security Center.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: 7292ea4486a61f5b0cfd8f656d2763a3ce655e79
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578259"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>Protégez vos machines virtuelles Azure VMware Solution avec l’intégration Azure Security Center

Les outils de sécurité natifs d’Azure offrent une protection pour un environnement hybride d’Azure, Azure VMware Solution et des machines virtuelles locales. Cet article explique la procédure de configuration des outils Azure pour la sécurité de l’environnement hybride. Vous utilisez ces outils pour identifier et traiter différentes menaces.

## <a name="azure-native-services"></a>Services natifs Azure

Voici un résumé rapide des services Azure natifs :

- **Espace de travail Log Analytics :** L’espace de travail Log Analytics est un environnement unique pour stocker des données de journal. Chaque espace de travail a son propre dépôt de données et sa propre configuration. Les solutions et sources de données sont configurées pour stocker leurs données dans un espace de travail spécifique.
- **Azure Security Center :** Azure Security Center est un système unifié d'administration de la sécurité de l’infrastructure. Il renforce la sécurité des centres de données et offre une protection avancée contre les menaces dans les charges de travail hybrides dans le cloud ou en local.
- **Azure Sentinel :** Azure Sentinel est une solution SIEM (Security Information and Event Management) native cloud. Elle fournit une analyse de la sécurité, une détection des alertes et une réponse automatisée aux menaces dans un environnement.

## <a name="topology"></a>Topologie

![Diagramme illustrant l’architecture de la sécurité intégrée d’Azure.](media/azure-security-integration/azure-integrated-security-architecture.png)

L’agent Log Analytics active la collecte de données de journal à partir d’Azure, d’Azure VMware Solution et de machines virtuelles locales. Les données de journal sont envoyées aux journaux Azure Monitor et stockées dans un espace de travail Log Analytics. Vous pouvez déployer l’agent Log Analytics à l’aide de serveurs compatibles avec Arc [prenant en charge les extensions de machine virtuelle](../azure-arc/servers/manage-vm-extensions.md) pour les machines virtuelles nouvelles et existantes. 

Une fois les journaux collectés par l’espace de travail Log Analytics, vous pouvez configurer l’espace de travail Log Analytics avec Azure Security Center. Azure Security Center évalue l’état des vulnérabilités des machines virtuelles Azure VMware Solution et déclenche une alerte pour toute vulnérabilité critique. Il évalue par exemple les correctifs de système d’exploitation manquants, les configurations de sécurité incorrectes et la [protection des points de terminaison](../security-center/security-center-services.md).

Vous pouvez configurer l’espace de travail Log Analytics avec Azure Sentinel pour la détection des alertes, la visibilité des menaces, la chasse et la réponse aux menaces. Dans le diagramme précédent, Azure Security Center est connecté à Azure Sentinel à l’aide d’un connecteur Azure Security Center. Azure Security Center transmet la vulnérabilité de l’environnement à Azure Sentinel pour créer un incident et la mappe à d’autres menaces. Vous pouvez également créer la requête de règles planifiées pour détecter les activités indésirables et les convertir en incidents.

## <a name="benefits"></a>Avantages

- Vous pouvez utiliser les services natifs d’Azure pour la sécurité de l’environnement hybride dans Azure, Azure VMware Solution et les services locaux.
- Avec un espace de travail Log Analytics, vous pouvez collecter les données ou les journaux sur un point unique et présenter les mêmes données à différents services Azure natifs.
- Azure Security Center offre de nombreuses fonctionnalités, notamment :
    - Monitoring d’intégrité de fichier
    - Détection des attaques sans fichier
    - Évaluation de correctif du système d’exploitation 
    - Évaluation des erreurs de configuration de la sécurité
    - Évaluation de la protection des points de terminaison
- Azure Sentinel vous permet d’effectuer les opérations suivantes :
    - collecter des données à l’échelle du cloud sur l’ensemble des utilisateurs, appareils, applications et infrastructures, tant locaux que dans plusieurs clouds ;
    - détecter les menaces précédemment non détectées ;
    - Investiguez les menaces à l’aide de l’intelligence artificielle et repérez les activités suspectes à grande échelle.
    - Répondez aux incidents rapidement avec une orchestration et une automatisation intégrées des tâches courantes.

## <a name="create-a-log-analytics-workspace"></a>Créer un espace de travail Log Analytics

Vous aurez besoin d’un espace de travail Log Analytics pour collecter des données à partir de différentes sources. Pour plus d’informations, consultez [Créer un espace de travail Log Analytics à partir du portail Azure](../azure-monitor/logs/quick-create-workspace.md). 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>Déployer Security Center et configurer des machines virtuelles Azure VMware Solution

Azure Security Center est un outil préconfiguré ne nécessitant pas de déploiement. Dans le portail Azure, recherchez et sélectionnez **Security Center**.

### <a name="enable-azure-defender"></a>Activer Azure Defender

Azure Defender étend la protection avancée contre les menaces d’Azure Security Center à vos charges de travail hybrides en local et dans le cloud. Ainsi, pour protéger vos machines virtuelles Azure VMware Solution, vous devez activer Azure Defender. 

1. Dans Security Center, sélectionnez **Bien démarrer**.

2. Sélectionnez l’onglet **Mettre à niveau**, puis sélectionnez votre abonnement ou votre espace de travail. 

3. Sélectionnez **Mettre à niveau** pour activer Azure Defender.

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Ajoutez des machines virtuelles Azure VMware Solution à Security Center

1. Dans le portail Azure, recherchez et sélectionnez **Azure Arc**.

2. Sous Ressources, sélectionnez **Serveurs**, puis **+Ajouter**.

    :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Capture d’écran montrant la page des serveurs Azure Arc pour l’ajout d’une machine virtuelle Azure VMware Solution à Azure.":::

3. Sélectionnez **Générer un script**.
 
    :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="Capture d’écran de la page Azure Arc montrant l’option d’ajout d’un serveur à l’aide d’un script interactif."::: 
 
4. Sous l’onglet **Configuration requise**, sélectionnez **Suivant**.

5. Sous l’onglet **Détails des ressources**, renseignez les informations suivantes : 
    - Abonnement
    - Resource group
    - Région 
    - Système d’exploitation
    - Détails du serveur proxy
    
    Ensuite, sélectionnez **Next: Balises**.

6. Sous l’onglet **Balises**, sélectionnez **Suivant**.

7. Sous l’onglet **Télécharger et exécuter le script**, sélectionnez **Télécharger**.

8. Spécifiez votre système d’exploitation et exécutez le script sur votre machine virtuelle Azure VMware Solution.

## <a name="view-recommendations-and-passed-assessments"></a>Afficher les recommandations et les évaluations réussies

1. Dans Azure Security Center, sélectionnez **Stock** dans le volet gauche.

2. Pour Type de ressource, sélectionnez **Serveurs - Azure Arc**.
 
     :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="Capture d’écran de la page Stock d’Azure Security Center montrant l’option Serveurs - Azure Arc sélectionnée sous Type de ressource.":::

3. Sélectionnez le nom de votre ressource. Une page s’ouvre et affiche les détails de l’intégrité de la sécurité de votre ressource.

4. Sous **Liste de suggestions**, sélectionnez les onglets **Suggestions**, **Évaluations réussies** et **Évaluations non disponibles** pour afficher ces détails.

    :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="Capture d’écran d’Azure Security Center montrant des suggestions de sécurité et les évaluations.":::

## <a name="deploy-an-azure-sentinel-workspace"></a>Déployer un espace de travail Azure Sentinel

Azure Sentinel est basé sur un espace de travail Log Analytics. Votre première étape de l’intégration d’Azure Sentinel consiste à sélectionner l’espace de travail Log Analytics que vous souhaitez utiliser à cet effet.

1. Dans le portail Azure, recherchez et sélectionnez **Azure Sentinel**.

2. Sur la page Espaces de travail Azure Sentinel, sélectionnez **+Ajouter**.

3. Sélectionnez l’espace de travail Log Analytics, puis **Ajouter**.

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>Activer le collecteur de données pour les événements de sécurité sur les machines virtuelles Azure VMware Solution

Vous êtes maintenant prêt à vous connecter à Azure Sentinel avec vos sources de données, en l’occurrence, des événements de sécurité.

1. Sur la page Espaces de travail Azure Sentinel, sélectionnez l’espace de travail configuré.

2. Sous Configuration, sélectionnez **Connecteurs de données**.

3. Dans la colonne Nom du connecteur, sélectionnez **Événements de sécurité** dans la liste, puis sélectionnez **Ouvrir la page du connecteur**.

4. Sur la page du connecteur, sélectionnez les événements que vous souhaitez diffuser, puis sélectionnez **Appliquer les modifications**.

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Capture d’écran de la page Événements de sécurité dans Azure Sentinel, où vous pouvez sélectionner les événements à diffuser.":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>Connecter Azure Sentinel à Azure Security Center  

1. Sur la page Espace de travail Azure Sentinel, sélectionnez l’espace de travail configuré.

2. Sous Configuration, sélectionnez **Connecteurs de données**.

3. Sélectionnez **Azure Security Center** dans la liste, puis sélectionnez **Ouvrir la page du connecteur**.

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Capture d’écran de la page Connecteurs de données dans Azure Sentinel montrant la sélection qui permet de se connecter à Azure Security Center avec Azure Sentinel.":::

4. Sélectionnez **Se connecter** pour vous connecter à Azure Security Center avec Azure Sentinel.

5. Activez **Créer un incident** pour générer un incident pour Azure Security Center.

## <a name="create-rules-to-identify-security-threats"></a>Créer des règles pour identifier les menaces de sécurité

Après avoir connecté des sources de données à Azure Sentinel, vous pouvez créer des règles afin de générer des alertes pour les menaces détectées. Dans l’exemple suivant, nous allons créer une règle pour les tentatives de connexion à Windows Server avec un mot de passe incorrect.

1. Sur la page Vue d’ensemble d’Azure Sentinel, sous Configurations, sélectionnez **Analytics**.

2. Sous Configurations, sélectionnez **Analytics**.

3. Sélectionne **+Créer** dans la liste déroulante, puis **Règle de requête planifiée**.

4. Sous l’onglet **Général**, entrez les informations nécessaires.

    - Nom
    - Description
    - Tactique
    - Gravité
    - Statut

    Sélectionnez **Suivant : Définir la logique de la règle >** .

5. Sous l’onglet **Définir la logique de la règle**, entrez les informations nécessaires.

    - Requête de règle (ici avec notre exemple de requête)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - Entités de mappage
    - Planification de la requête
    - Seuil d’alerte
    - Regroupement d'événements
    - Suppression

    Sélectionnez **Suivant**.

6. Sous l’onglet **Paramètres d’incident**, activez **Créer des incidents à partir d’alertes déclenchées par cette règle analytique** et sélectionnez **Suivant : Réponse automatisée >** .
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="Capture d’écran de l’Assistant Règle analytique pour la création d’une règle dans Azure Sentinel. Affiche l’option Créer des incidents à partir d’alertes déclenchées par cette règle comme étant activée.":::

7. Sélectionnez **Suivant : Révision >** .

8. Sous l’onglet **Examiner et créer**, vérifiez les informations et sélectionnez **Créer**.

Après l’échec de la troisième tentative de connexion à Windows Server, la règle créée déclenche un incident pour chaque tentative infructueuse.

## <a name="view-alerts"></a>Afficher les alertes

Vous pouvez afficher les incidents générés avec Azure Sentinel. Vous pouvez également attribuer des incidents et les clôturer une fois qu’ils sont résolus, le tout à partir d’Azure Sentinel.

1. Accédez à la page Vue d’ensemble d’Azure Sentinel.

2. Sous Gestion des menaces, sélectionnez **Incidents**.

3. Sélectionnez un incident. Vous pouvez ensuite attribuer l’incident à une équipe pour qu’elle puisse résoudre le problème.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="Capture d’écran de la page d’incidents Azure Sentinel avec l’incident sélectionné et l’option permettant d’attribuer l’incident pour le résoudre.":::

    Après avoir résolu le problème, vous pouvez le clôturer.

## <a name="hunt-security-threats-with-queries"></a>Rechercher des menaces de sécurité avec des requêtes

Vous pouvez créer des requêtes ou utiliser la requête prédéfinie disponible dans Azure Sentinel pour identifier les menaces dans votre environnement. Les étapes suivantes exécutent une requête prédéfinie.

1. Accédez à la page Vue d’ensemble d’Azure Sentinel.

2. Sous Gestion des menaces, sélectionnez **Chasse**. Une liste de requêtes prédéfinies s’affiche.

3. Sélectionnez une requête, puis sélectionnez **Exécuter la requête**.

4. Sélectionnez **Afficher les résultats** pour vérifier les résultats.

### <a name="create-a-new-query"></a>Créer une requête

1.  Sous Gestion des menaces, sélectionnez **Chasse**, puis **+Nouvelle requête**.

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="Capture d’écran de la page Chasse Azure Sentinel avec l’option +Nouvelle requête en surbrillance.":::

2. Entrez les informations suivantes pour créer une requête personnalisée.

    - Nom
    - Description
    - Requête personnalisée
    - Mappage d’entrée
    - Tactique
    
3. Sélectionnez **Create** (Créer). Vous pouvez ensuite sélectionner la requête créée, **Exécuter la requête** et **Afficher les résultats**.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez abordé les méthodes de protection de vos machines virtuelles Azure VMware Solution, vous pouvez en apprendre davantage sur les sujets suivants :

- Utilisation du [tableau de bord Azure Defender](../security-center/azure-defender-dashboard.md).
- [Détection avancée des attaques multiphases dans Azure Sentinel](../azure-monitor/logs/quick-create-workspace.md).
- [Gestion du cycle de vie des machines virtuelles du service Azure VMware Solution](lifecycle-management-of-azure-vmware-solution-vms.md).
