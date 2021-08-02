---
title: Intégrer Azure Security Center avec Azure VMware Solution
description: Découvrez comment protéger vos machines virtuelles Azure VMware Solution avec les outils de sécurité natifs d’Azure à partir du tableau de bord Azure Security Center.
ms.topic: how-to
ms.date: 06/14/2021
ms.openlocfilehash: 6060be11ada028234b11e74f56de8c9741fc4cd4
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111754238"
---
# <a name="integrate-azure-security-center-with-azure-vmware-solution"></a>Intégrer Azure Security Center avec Azure VMware Solution 

Azure Security Center offre une protection avancée contre les menaces sur vos machines virtuelles Azure VMware Solution et locales. Il évalue la vulnérabilité des machines virtuelles Azure VMware Solution et déclenche des alertes si nécessaire. Ces alertes de sécurité peuvent être transférées à Azure Monitor à des fins de résolution. Vous pouvez définir des stratégies de sécurité dans Azure Security Center. Pour plus d’informations, consultez [Utilisation des stratégies de sécurité](../security-center/tutorial-security-policy.md). 

Azure Security Center offre de nombreuses fonctionnalités, notamment :
- Monitoring d’intégrité de fichier
- Détection des attaques sans fichier
- Évaluation de correctif du système d’exploitation 
- Évaluation des erreurs de configuration de la sécurité
- Évaluation de la protection des points de terminaison

Le diagramme illustre l’architecture de surveillance intégrée de la sécurité intégrée pour les machines virtuelles du service Azure VMware Solution.
 
:::image type="content" source="media/azure-security-integration/azure-integrated-security-architecture.png" alt-text="Diagramme de l’architecture de la sécurité intégrée Azure." border="false":::


## <a name="prerequisites"></a>Prérequis

- [Planifiez l’utilisation optimisée de Security Center](../security-center/security-center-planning-and-operations-guide.md).

- [Passez en revue les plateformes prises en charge dans Security Center](../security-center/security-center-os-coverage.md).

- [Créez un espace de travail Log Analytics](../azure-monitor/logs/quick-create-workspace.md) pour collecter des données issues de différentes sources.

- [Activez Azure Security Center dans votre abonnement](../security-center/security-center-get-started.md). 

   >[!NOTE]
   >Azure Security Center est un outil préconfiguré qui ne nécessite aucun déploiement, mais vous devez l’activer dans le portail Azure.

- [Activez Azure Defender](../security-center/enable-azure-defender.md). 


## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Ajoutez des machines virtuelles Azure VMware Solution à Security Center

1. Dans le portail Azure, recherchez et sélectionnez **Azure Arc**.

2. Sous Ressources, sélectionnez **Serveurs**, puis **+Ajouter**.

   :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Capture d’écran montrant la page des serveurs Azure Arc pour l’ajout d’une machine virtuelle Azure VMware Solution à Azure.":::

3. Sélectionnez **Générer un script**.
 
   :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="Capture d’écran de la page Azure Arc montrant l’option d’ajout d’un serveur à l’aide d’un script interactif."::: 
 
4. Sous l’onglet **Configuration requise**, sélectionnez **Suivant**.

5. Sous l’onglet **Détails de la ressource**, renseignez les informations suivantes, puis sélectionnez **Suivant : Balises**. 

    - Abonnement

    - Resource group

    - Région 

    - Système d’exploitation

    - Détails du serveur proxy
    
6. Sous l’onglet **Balises**, sélectionnez **Suivant**.

7. Sous l’onglet **Télécharger et exécuter le script**, sélectionnez **Télécharger**.

8. Spécifiez votre système d’exploitation et exécutez le script sur votre machine virtuelle Azure VMware Solution.

## <a name="view-recommendations-and-passed-assessments"></a>Afficher les recommandations et les évaluations réussies

Vous disposez ainsi des détails relatifs à l’intégrité de la sécurité de votre ressource. 

1. Dans Azure Security Center, sélectionnez **Stock** dans le volet gauche.

2. Pour Type de ressource, sélectionnez **Serveurs - Azure Arc**.
 
   :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="Capture d’écran de la page Stock d’Azure Security Center montrant l’option Serveurs - Azure Arc sélectionnée sous Type de ressource.":::

3. Sélectionnez le nom de votre ressource. Une page s’ouvre et affiche les détails de l’intégrité de la sécurité de votre ressource.

4. Sous **Liste de suggestions**, sélectionnez les onglets **Suggestions**, **Évaluations réussies** et **Évaluations non disponibles** pour afficher ces détails.

   :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="Capture d’écran d’Azure Security Center montrant des suggestions de sécurité et les évaluations.":::

## <a name="deploy-an-azure-sentinel-workspace"></a>Déployer un espace de travail Azure Sentinel

Azure Sentinel est basé sur un espace de travail Log Analytics et dès lors, il vous suffit de sélectionner l’espace de travail Log Analytics que vous souhaitez utiliser.

1. Dans le portail Azure, recherchez et sélectionnez **Azure Sentinel**.

2. Sur la page Espaces de travail Azure Sentinel, sélectionnez **+Ajouter**.

3. Sélectionnez l’espace de travail Log Analytics, puis **Ajouter**.

## <a name="enable-data-collector-for-security-events"></a>Activer le collecteur de données pour les événements de sécurité

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

4. Sous l’onglet **Général**, entrez les informations requises, puis sélectionnez **Suivant : Définir la logique de la règle**.

    - Nom

    - Description

    - Tactique

    - Gravité

    - Statut

5. Sous l’onglet **Définir la logique de la règle**, entrez les informations requises, puis sélectionnez **Suivant**.

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


6. Sous l’onglet **Paramètres d’incident**, activez **Créer des incidents à partir d’alertes déclenchées par cette règle analytique** et sélectionnez **Suivant : Réponse automatisée**.
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="Capture d’écran de l’Assistant Règle analytique pour la création d’une règle dans Azure Sentinel. Affiche l’option Créer des incidents à partir d’alertes déclenchées par cette règle comme étant activée.":::

7. Sélectionnez **Suivant : Vérification**).

8. Sous l’onglet **Examiner et créer**, vérifiez les informations et sélectionnez **Créer**.

>[!TIP]
>Après l’échec de la troisième tentative de connexion à Windows Server, la règle créée déclenche un incident pour chaque tentative infructueuse.

## <a name="view-alerts"></a>Afficher les alertes

Vous pouvez afficher les incidents générés avec Azure Sentinel. Vous pouvez également attribuer des incidents et les clôturer une fois qu’ils sont résolus, le tout à partir d’Azure Sentinel.

1. Accédez à la page Vue d’ensemble d’Azure Sentinel.

2. Sous Gestion des menaces, sélectionnez **Incidents**.

3. Sélectionnez un incident, puis attribuez-le à une équipe à des fins de résolution.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="Capture d’écran de la page d’incidents Azure Sentinel avec l’incident sélectionné et l’option permettant d’attribuer l’incident pour le résoudre.":::

>[!TIP]
>Après avoir résolu le problème, vous pouvez le clôturer.

## <a name="hunt-security-threats-with-queries"></a>Rechercher des menaces de sécurité avec des requêtes

Vous pouvez créer des requêtes ou utiliser la requête prédéfinie disponible dans Azure Sentinel pour identifier les menaces dans votre environnement. Les étapes suivantes exécutent une requête prédéfinie.

1. Sur la page Vue d’ensemble d’Azure Sentinel, sous Gestion des menaces, sélectionnez **Chasse**. Une liste de requêtes prédéfinies s’affiche.

   >[!TIP]
   >Vous pouvez également créer une requête en sélectionnant **+Nouvelle requête**. 
   >
   >:::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="Capture d’écran de la page Chasse Azure Sentinel avec l’option +Nouvelle requête en surbrillance.":::

3. Sélectionnez une requête, puis sélectionnez **Exécuter la requête**.

4. Sélectionnez **Afficher les résultats** pour vérifier les résultats.



## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez abordé les méthodes de protection de vos machines virtuelles Azure VMware Solution, vous pouvez en apprendre davantage sur les sujets suivants :

- [Utilisation du tableau de bord Azure Defender](../security-center/azure-defender-dashboard.md)
- [Détection avancée des attaques multiphases dans Azure Sentinel](../azure-monitor/logs/quick-create-workspace.md)
- [Intégration des services natifs Azure dans Azure VMware Solution](integrate-azure-native-services.md)
