---
title: Sécuriser Azure AD B2C avec Azure Sentinel
titleSuffix: Azure AD B2C
description: Dans ce tutoriel, vous allez utiliser Azure Sentinel pour effectuer l’analytique de sécurité des données Azure Active Directory B2C.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 08/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 157d643ffa2b5af97f05b7272bb3a8c4232dc0c4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131436970"
---
# <a name="tutorial-configure-security-analytics-for-azure-active-directory-b2c-data-with-azure-sentinel"></a>Didacticiel : configurer l’analytique de sécurité pour les données Azure Active Directory B2C avec Azure Sentinel

Vous pouvez sécuriser davantage votre environnement Azure Active Directory B2C (Azure AD B2C) en routant les journaux et les informations d’audit vers Azure Sentinel. Azure Sentinel est une solution native cloud SIEM (Security Information and Event Management) et SOAR (Security Orchestration, Automation and Response). Azure Sentinel fournit une détection des alertes, une visibilité des menaces, une chasse proactive et une réponse aux menaces pour Azure AD B2C.

En utilisant Azure Sentinel avec Azure AD B2C, vous pouvez :

- Détecter les menaces non identifiées et réduire les faux positifs à l’aide des fonctionnalités d’analytique et de renseignement sur les menaces de Microsoft.
- Investiguer les menaces avec l’IA. Repérez les activités suspectes à grande échelle et tirez parti de plusieurs années de travail en cybersécurité chez Microsoft.
- Répondez aux incidents rapidement avec une orchestration et une automatisation intégrées des tâches courantes.
- Respectez les exigences de sécurité et de conformité de votre organisation.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Transférer les journaux Azure AD B2C vers un espace de travail Log Analytics.
> * Activer Azure Sentinel dans un espace de travail Log Analytics.
> * Créer un exemple de règle dans Azure Sentinel qui déclenche un incident.
> * Configurer une réponse automatisée.

## <a name="configure-azure-ad-b2c-with-azure-monitor-log-analytics"></a>Configurer Azure AD B2C avec Azure Monitor Log Analytics

Pour définir l’emplacement d’envoi des journaux et des métriques d’une ressource, activez les **Paramètres de diagnostic** dans Azure AD au sein de votre locataire Azure AD B2C. Ensuite, [configurez Azure AD B2C pour envoyer les journaux à Azure Monitor](./azure-monitor.md).

## <a name="deploy-an-azure-sentinel-instance"></a>Déployer un espace de travail Azure Sentinel

Une fois que vous avez configuré votre instance d’Azure AD B2C pour envoyer les journaux à Azure Monitor, vous devez activer une instance d’Azure Sentinel.

>[!IMPORTANT]
>Pour activer Azure Sentinel, vous avez besoin des autorisations des contributeurs concernant l’abonnement dans lequel réside l’espace de travail Azure Sentinel. Pour utiliser Azure Sentinel, vous avez besoin des autorisations des contributeurs ou des lecteurs sur le groupe de ressources auquel appartient l’espace de travail.

1. Accédez au [portail Azure](https://portal.azure.com). Sélectionnez l’abonnement dans lequel l’espace de travail Log Analytics est créé.

2. Recherchez et sélectionnez **Azure Sentinel**.

   ![Capture d’écran montrant la recherche d’Azure Sentinel dans le portail Azure.](./media/azure-sentinel/azure-sentinel-add.png)

3. Sélectionnez **Ajouter**.

4. Sélectionnez le nouvel espace de travail.

   ![Capture d’écran montrant où sélectionner un espace de travail Azure Sentinel.](./media/azure-sentinel/create-new-workspace.png)

5. Sélectionnez **Ajouter Azure Sentinel**.

>[!NOTE]
>Vous pouvez [exécuter Azure Sentinel](../sentinel/quickstart-onboard.md) sur plusieurs espaces de travail, mais les données sont isolées sur un seul espace de travail.

## <a name="create-an-azure-sentinel-rule"></a>Créer une règle Azure Sentinel

Maintenant que vous avez activé Azure Sentinel, soyez averti en cas de problème suspect dans votre locataire Azure AD B2C.

Vous pouvez créer des [règles analytiques personnalisées](../sentinel/detect-threats-custom.md) pour découvrir les menaces et les comportements anormaux dans votre environnement. Ces règles recherchent des événements ou des ensembles d’événements spécifiques, et vous alertent quand certains seuils d’événements ou certaines conditions sont atteints. Elles génèrent ensuite des incidents pour permettre d’approfondir l’investigation.

>[!NOTE]
>Azure Sentinel fournit des modèles intégrés, conçus par l’équipe d’experts et d’analystes en sécurité de Microsoft, qui vous aident à créer des règles de détection des menaces. Les règles créées à partir de ces modèles rechercheront automatiquement toute activité qui semble suspecte sur l’ensemble de vos données. Aucun connecteur Azure AD B2C natif n’est disponible pour l’instant. Pour l’exemple de ce didacticiel, nous allons créer notre propre règle.

Dans l’exemple suivant, vous recevez une notification si quelqu’un tente de forcer l’accès à votre environnement mais sans succès. Cela peut indiquer une attaque par force brute. Vous souhaitez être notifié en cas d’échec de deux connexions ou plus en l’espace de 60 secondes.

1. Dans le menu de gauche d’Azure Sentinel, sélectionnez **Analytics**.

2. Dans la barre d’action située en haut, sélectionnez **+ Créer** > **Règle de requête planifiée**. 

    ![Capture d’écran montrant les sélections permettant de créer une règle de requête planifiée.](./media/azure-sentinel/create-scheduled-rule.png)

3. Dans l’Assistant Règle analytique, accédez à l’onglet **Général**, puis entrez les informations suivantes :

    | Champ | Valeur |
    |:--|:--|
    |**Nom** | Entrez un nom approprié pour les connexions non réussies à Azure AD B2C. |
    |**Description** | Entrez une description indiquant que la règle notifiera au moins deux connexions non réussies en moins de 60 secondes. |
    | **Tactique** | Choisissez parmi les catégories d’attaques par lesquelles classer la règle. Ces catégories reposent sur les tactiques du framework [MITRE ATT&CK](https://attack.mitre.org/).<BR>Pour notre exemple, nous allons choisir **PreAttack**. <BR> MITRE ATT&CK est une base de connaissances accessible à l’échelle mondiale regroupant les tactiques et les techniques des auteurs d’attaques en fonction des observations du monde réel. Cette base de connaissances sert de fondement au développement de modèles et de méthodologies spécifiques aux menaces.
    | **Gravité** | Sélectionnez un niveau de gravité approprié. |
    | **État** | Quand vous créez la règle, elle est à l’état **Activé** par défaut. Cet état signifie que la règle s’exécutera immédiatement une fois que vous aurez fini de la créer. Si vous ne souhaitez pas qu’elle s’exécute immédiatement, sélectionnez **Désactivé**. La règle est ensuite ajoutée à votre onglet **Règles actives**. Vous pourrez l’activer à partir de cet emplacement quand vous en aurez besoin.|

    ![Capture d’écran montrant les propriétés de base de la règle.](./media/azure-sentinel/create-new-rule.png)

4. Pour définir la logique de requête de la règle et configurer les paramètres, sous l’onglet **Définir la logique de la règle**, écrivez une requête directement dans la zone **Requête de règle**. 

    ![Capture d’écran montrant l’entrée de la requête de règle sous l’onglet de définition de la logique de règle.](./media/azure-sentinel/rule-query.png)

    Cette requête vous alerte quand il existe au moins deux échecs de connexion à votre locataire Azure AD B2C en moins de 60 secondes. Elle organise les connexions par `UserPrincipalName`.

5. Dans la section **Planification de la requête**, définissez les paramètres suivants :

    ![Capture d’écran montrant la définition des paramètres de planification de la requête.](./media/azure-sentinel/query-scheduling.png)

6. Sélectionnez **Suivant : Paramètres de l’incident (préversion)** . Vous allez configurer et ajouter la réponse automatisée plus tard.

7. Accédez à **Examiner et créer** pour examiner tous les paramètres de votre nouvelle règle d'alerte. Lorsque le message **Validation réussie** apparaît, sélectionnez **Créer** pour initialiser votre règle d'alerte.

    ![Capture d’écran montrant l’onglet permettant de passer en revue et de créer une règle.](./media/azure-sentinel/review-create.png)

8. Visualisez la règle et les incidents qu’elle génère. Trouvez la règle personnalisée de type **Planifiée** que vous venez de créer dans le tableau situé sous l'onglet **Règles actives** de l'écran **Analytique** principal. Dans cette liste, vous pouvez modifier, activer, désactiver ou supprimer des règles à l’aide des boutons correspondants.

    ![Capture d’écran montrant les règles actives avec des options de modification, d’activation, de désactivation ou de suppression.](./media/azure-sentinel/rule-crud.png)

9. Visualisez les résultats de votre nouvelle règle pour les connexions non réussies à Azure AD B2C. Accédez à la page **Incidents**, dans laquelle vous pouvez trier, examiner et corriger les menaces. 

    Un incident peut inclure plusieurs alertes. C’est une agrégation de toutes les preuves pertinentes pour une investigation spécifique. Vous pouvez définir des propriétés telles que la gravité et l’état au niveau incident.

    > [!NOTE]
    > Une fonctionnalité clé d’Azure Sentinel est l'[investigation d’un incident.](../sentinel/investigate-cases.md)
    
10. Pour commencer un examen, sélectionnez un incident spécifique. 

    Sur la droite, vous pouvez voir des informations détaillées sur l’incident. Ces informations incluent la gravité, les entités impliquées, les événements bruts qui ont déclenché l’incident et l’ID unique de l’incident.

    ![Capture d’écran montrant les informations relatives aux incidents.](./media/azure-sentinel/select-incident.png)

11. Sélectionnez **Afficher tous les détails** dans le volet des incidents. Passez en revue les onglets qui récapitulent les informations relatives aux incidents et fournissent des détails supplémentaires.

    ![Capture d’écran montrant les onglets des informations relatives aux incidents.](./media/azure-sentinel/full-details.png)

12. Sélectionnez **Preuve** > **Événements** > **Lien vers l’analytique des journaux d'activité**. Le résultat affiche la valeur `UserPrincipalName` de l’identité qui tente de se connecter ainsi que le nombre de tentatives.

    ![Capture d’écran montrant tous les détails de l’incident sélectionné.](./media/azure-sentinel/logs.png)

## <a name="automated-response"></a>Réponse automatisée

Azure Sentinel offre une [capacité de dépassement plus robuste](../sentinel/automation-in-azure-sentinel.md). Les actions automatisées, regroupées sous l’appellation *guide opérationnel* dans Azure Sentinel, peuvent être attachées à des règles analytiques pour répondre à vos besoins.

Dans cet exemple, nous ajoutons une notification par e-mail pour un incident créé par la règle. Pour accomplir cette tâche, utilisez un [guide opérationnel existant du dépôt GitHub d’Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Incident-Email-Notification). Une fois le guide opérationnel configuré, modifiez la règle existante, puis sélectionnez ce guide opérationnel sous l’onglet **Réponse automatisée**.

![Capture d’écran montrant la configuration de la réponse automatisée associée à une règle.](./media/azure-sentinel/automation-tab.png)

## <a name="related-information"></a>Informations connexes

Pour plus d’informations sur Azure Sentinel et Azure AD B2C, consultez :

- [Exemples de classeurs](https://github.com/azure-ad-b2c/siem#workbooks)

- [Documentation Azure Sentinel](../sentinel/index.yml)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer les faux positifs dans Azure Sentinel](../sentinel/false-positives.md)
