---
title: Sécuriser Azure AD B2C avec Azure Sentinel
titleSuffix: Azure AD B2C
description: Didacticiel pour effectuer l’analytique de sécurité pour les données Azure Active Directory B2C avec Azure Sentinel
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: ec68ca976b52c50c09bf86c90c56304f05051b66
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124740263"
---
# <a name="tutorial-configure-security-analytics-for-azure-active-directory-b2c-data-with-azure-sentinel"></a>Didacticiel : configurer l’analytique de sécurité pour les données Azure Active Directory B2C avec Azure Sentinel

Vous pouvez renforcer la sécurité de votre environnement Azure Active Directory (AD) B2C en acheminant les journaux d’activité et les informations d’audit vers Azure Sentinel. Azure Sentinel est une solution native cloud de types SIEM (Security Information and Event Management) et SOAR (Security Orchestrated Automated Response). Azure Sentinel fournit une détection des alertes, une visibilité des menaces, une chasse proactive et une réponse aux menaces pour Azure AD B2C.

En utilisant Azure Sentinel avec Azure AD B2C, vous pouvez :

- Détecter les menaces non détectées précédemment et réduire les faux positifs en vous appuyant sur l’analytique et les systèmes de renseignement incomparables sur les menaces fournis par Microsoft.
- Examiner les menaces à l’aide de l’intelligence artificielle. Pour rechercher les activités suspectes à grande échelle, explorez des années de travaux liés à la cybersécurité chez Microsoft.
- Répondez aux incidents rapidement avec une orchestration et une automatisation intégrées des tâches courantes.
- Respectez les exigences de sécurité et de conformité de votre organisation.

Dans ce tutoriel, vous allez découvrir comment :

1. [Transférer les journaux d’activité B2C dans l’espace de travail des journaux d’activités Azure Monitor](#configure-azure-ad-b2c-with-azure-monitor-logs-analytics)
2. [Activer Azure Sentinel sur un espace de travail Log Analytics](#deploy-an-azure-sentinel-instance)
3. [Créez un exemple de règle dans Azure Sentinel qui déclenchera un incident](#create-an-azure-sentinel-rule)
4. [Configurer une réponse automatisée](#automated-response)

## <a name="configure-azure-ad-b2c-with-azure-monitor-logs-analytics"></a>Configurer Azure AD B2C avec l’analytique des journaux d’activité Azure Monitor

Activez les **paramètres de diagnostic** dans Azure AD au sein de votre locataire Azure AD B2C pour définir l’emplacement d’envoi des journaux d’activité et des indicateurs d’une ressource.

Ensuite, [configurez Azure AD B2C pour envoyer les journaux d’activité à Azure Monitor](./azure-monitor.md).

## <a name="deploy-an-azure-sentinel-instance"></a>Déployer un espace de travail Azure Sentinel

>[!IMPORTANT]
>Pour activer Azure Sentinel, vous avez besoin d’**autorisations des contributeurs** concernant l’abonnement dans lequel réside l’espace de travail Azure Sentinel. Pour utiliser Azure Sentinel, vous avez besoin des autorisations des contributeurs ou des lecteurs sur le groupe de ressources auquel appartient l’espace de travail.

Une fois que vous avez configuré votre instance Azure AD B2C pour envoyer des journaux d'activité à Azure Monitor, vous devez activer une instance Azure Sentinel.

1. Accédez au [portail Azure](https://portal.azure.com). Sélectionnez l’abonnement dans lequel est créé votre espace de travail Log Analytics.

2. Recherchez et sélectionnez **Azure Sentinel**.

3. Sélectionnez **Ajouter**.

![L’image affiche une recherche d’Azure Sentinel dans le Portail Azure](./media/azure-sentinel/azure-sentinel-add.png)

4. Sélectionnez le nouvel espace de travail.

![image illustrant la sélection de l’espace de travail sentinel](./media/azure-sentinel/create-new-workspace.png)

5. Sélectionnez **Ajouter Azure Sentinel**.

>[!NOTE]
>Vous pouvez [exécuter Azure Sentinel](../sentinel/quickstart-onboard.md) sur plusieurs espaces de travail, mais les données sont isolées sur un seul espace de travail.

## <a name="create-an-azure-sentinel-rule"></a>Créer une règle Azure Sentinel

>[!NOTE]
>Azure Sentinel fournit des modèles intégrés prêts à l’emploi pour vous aider à créer des règles de détection des menaces conçues par l’équipe d’experts et d’analystes de la sécurité de Microsoft. Les règles créées à partir de ces modèles rechercheront automatiquement toute activité qui semble suspecte sur l’ensemble de vos données. Aucun connecteur Azure AD B2C natif n’est disponible pour l’instant. Pour l’exemple de ce didacticiel, nous allons créer notre propre règle.

Maintenant que vous avez activé Azure Sentinel, soyez averti en cas de problème suspect dans votre locataire Azure AD B2C.

Vous pouvez créer des [règles d’analytique personnalisées](../sentinel/detect-threats-custom.md) pour vous aider à détecter les menaces et les comportements anormaux dans votre environnement. Ces règles recherchent des événements ou des ensembles d’événements spécifiques, vous alertent lorsque certains seuils d’événement ou certaines conditions sont atteints, puis génèrent des incidents pour plus d’investigation.

Dans l’exemple suivant, nous expliquons le scénario dans lequel vous recevez une notification si quelqu’un tente de forcer l’accès à votre environnement, mais qu’il n’y parvient pas. Il peut s’agir d’une attaque par force brute. Vous souhaitez recevoir une notification pour au moins deux tentatives de connexion infructueuses en 60 secondes.

1. Dans le menu de navigation d'Azure Sentinel, sélectionnez **Analytique**.

2. Sur la barre d'action du haut de l'écran, sélectionnez **+Créer**, puis **Règle de requête planifiée**. Cette opération ouvre l’**Assistant Règle analytique**.

![image montrant la sélection d’une règle de création de requête planifiée](./media/azure-sentinel/create-scheduled-rule.png)

3. Dans l’Assistant Règle analytique, accédez à l’onglet **Général**.

   | Champ | Valeur |
   |:--|:--|
   |Nom | Tentatives de connexion B2C infructueuses |
   |Description | Notifier pour au moins deux tentatives de connexion infructueuses en 60 secondes |
   | Tactique | Choisissez parmi les catégories d’attaques par lesquelles classer la règle. Ces catégories reposent sur les tactiques du framework [MITRE ATT&CK](https://attack.mitre.org/).<BR>Pour notre exemple, nous allons choisir `PreAttack` <BR> MITRE ATT&CK® est une base de connaissances mondialement accessible regroupant les tactiques et les techniques des adversaires basées sur des observations réelles. La base de connaissances ATT&CK es utilisée comme base pour le développement de modèles de menaces et de méthodologies spécifiques.
   | Gravité | Selon le cas |
   | Statut | Quand vous créez la règle, son État est `Enabled` par défaut. Cela signifie qu’elle va s’exécuter dès que vous aurez fini de la créer. Si vous ne souhaitez pas qu’elle s’exécute immédiatement, sélectionnez `Disabled`. Dans ce cas, la règle est ajoutée à votre onglet Règles actives, à partir duquel vous pouvez l’activer quand vous en avez besoin.|

![image montrant les propriétés de règle de base](./media/azure-sentinel/create-new-rule.png)

4.  Pour définir la logique de requête de règle et configurer les paramètres, sous l’onglet **Définir la logique de règle**, écrivez une requête directement dans le champ **Requête de règle**. Cette requête vous avertit lorsqu’il y a au moins deux tentatives de connexion infructueuses en 60 secondes à votre locataire Azure AD B2C et s’organise par `UserPrincipalName`.

![image montrant la saisie de la requête de règle dans l’onglet de logique](./media/azure-sentinel/rule-query.png)

Dans la section Planification de la requête, définissez les paramètres suivants :

![image montrant la définition des paramètres de planification des requêtes](./media/azure-sentinel/query-scheduling.png)

5. Sélectionnez  **Suivant:Paramètres d’incident (préversion)** . Vous allez configurer et ajouter la réponse automatisée ultérieurement.

6. Accédez à **Examiner et créer** pour examiner tous les paramètres de votre nouvelle règle d'alerte. Lorsque le message **Validation réussie** apparaît, sélectionnez **Créer** pour initialiser votre règle d'alerte.

![image montrant la vérification et la création de la règle](./media/azure-sentinel/review-create.png)

7. Affichez la règle et les incidents générés. Trouvez la règle personnalisée de type **Planifiée** que vous venez de créer dans le tableau situé sous l'onglet **Règles actives** de l'écran **Analytique** principal. Dans cette liste, vous pouvez **modifier**, **activer**, **désactiver** ou **supprimer** des règles.

![image montrant l’écran d’analytique présentant les options permettant de modifier, d’activer, de désactiver ou de supprimer des règles](./media/azure-sentinel/rule-crud.png)

8. Affichez les résultats de votre nouvelle règle de tentatives de connexion Azure AD B2C infructueuses. Accédez à la page **Incidents**, dans laquelle vous pouvez trier, examiner et corriger les menaces. Un incident peut inclure plusieurs alertes. C’est une agrégation de toutes les preuves pertinentes pour une investigation spécifique. Vous pouvez définir des propriétés telles que la gravité et l’état au niveau incident.

>[!Note]
>Une fonctionnalité clé d’Azure Sentinel est l'[investigation d’un incident.](../sentinel/investigate-cases.md)

9. Pour commencer une investigation, sélectionnez un incident spécifique. Sur la droite, vous pouvez voir des informations détaillées sur l’incident, notamment sa gravité, les entités impliquées, les événements bruts qui ont déclenché cet incident et l’ID unique de l’incident.

![image alt-text="écran d’incident](./media/azure-sentinel/select-incident.png)

10. Sélectionnez **Afficher tous les détails** dans la page d’incident et passez en revue les onglets pertinents qui résument les informations sur l’incident.

![image règle 73](./media/azure-sentinel/full-details.png)

11. Sélectionnez **Preuve** > **Événements** > **Lien vers l’analytique des journaux d'activité**. Les résultats afficheront le `UserPrincipalName` de l’identité essayant de se connecter dans le nombre de tentatives.

![image montrant les détails de l’incident sélectionné](./media/azure-sentinel/logs.png)

## <a name="automated-response"></a>Réponse automatisée

Azure Sentinel offre une [capacité de dépassement plus robuste](../sentinel/automation-in-azure-sentinel.md). Les actions automatisées, appelées guide opérationnel dans Azure Sentinel, peuvent être attachées à des règles d’analytique pour répondre à vos exigences.

Dans cet exemple, nous ajoutons une notification par e-mail lors d’un incident créé par la règle. utilisez un [manuel existant à partir du référentiel Sentinel GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Incident-Email-Notification) pour accomplir cette tâche. Une fois le guide opérationnel configuré, modifiez la règle existante et sélectionnez le guide opérationnel dans l’onglet Automatisation.

![image de l’écran de configuration de la réponse automatisée associée à une règle](./media/azure-sentinel/automation-tab.png)

## <a name="next-steps"></a>Étapes suivantes

- [Gérer les faux positifs dans Azure Sentinel](../sentinel/false-positives.md)

- [Exemples de classeurs](https://github.com/azure-ad-b2c/siem#workbooks)

- [Documentation Azure Sentinel](../sentinel/index.yml)