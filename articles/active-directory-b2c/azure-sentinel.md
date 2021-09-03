---
title: Sécuriser Azure AD B2C avec Azure Sentinel
titleSuffix: Azure AD B2C
description: Ce didacticiel montre comment effectuer des analytiques de sécurité pour Azure AD B2C avec Azure Sentinel.
services: active-directory-b2c
author: agabrielle
manager: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: agher
ms.subservice: B2C
ms.date: 07/19/2021
ms.openlocfilehash: a405cb97a021c05a3b0c6aa004d3f92ce4657d24
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122533110"
---
# <a name="tutorial-how-to-perform-security-analytics-for-azure-ad-b2c-data-with-azure-sentinel"></a>Didacticiel : Comment effectuer des analyses de sécurité pour Azure AD B2C avec Azure Sentinel

Vous pouvez renforcer la sécurité de votre environnement Azure AD B2C en acheminant les journaux d’activité et les informations d’audit vers Azure Sentinel. Azure Sentinel est une solution native cloud de types **SIEM (Security Information and Event Management) et SOAR (Security Orchestrated Automated Response)** . Azure Sentinel fournit une détection des alertes, une visibilité des menaces, une chasse proactive et une réponse aux menaces pour **Azure AD B2C**.

En utilisant Azure Sentinel conjointement avec Azure AD B2C, vous pouvez :

- Détectez les menaces non détectées précédemment et réduisez les faux positifs en vous appuyant sur l’analytique et les systèmes de renseignement incomparables sur les menaces fournis par Microsoft.
- Investiguez les menaces à l’aide de l’intelligence artificielle et recherchez les activités suspectes à grande échelle en profitant des années de travail que Microsoft a consacrées à la cybersécurité.
- Répondez aux incidents rapidement avec une orchestration et une automatisation intégrées des tâches courantes.
- Respectez les exigences de sécurité et de conformité de votre organisation.

Dans ce didacticiel, vous allez apprendre ce qui suit :

1. Transfert des journaux d’activité B2C dans l’espace de travail des journaux d’activités Azure Monitor.
1. Activez **Azure Sentinel** sur un espace de travail Log Analytics.
1. Créez un exemple de règle dans Sentinel qui déclenchera un incident.
1. Enfin, configurez une réponse automatisée.

## <a name="configure-aad-b2c-with-azure-monitor-logs-analytics"></a>Configurer AAD B2C avec l’analytique des journaux d’activité Azure Monitor

Les étapes suivants vous guideront tout au long du processus pour activer les **_paramètres de diagnostic_** dans Azure Active Directory au sein de votre locataire Azure AD B2C.
Les paramètres de diagnostic définissent où les journaux et les métriques d’une ressource doivent être envoyés.

Suivez les étapes **1 à 5** de la section [Superviser Azure AD B2C avec Azure Monitor](./azure-monitor.md) pour configurer Azure AD B2C pour envoyer des journaux d’activités à Azure Monitor.

## <a name="deploy-an-azure-sentinel-instance"></a>Déployer un espace de travail Azure Sentinel

> [!IMPORTANT]
> Pour activer Azure Sentinel, vous avez besoin d’**autorisations des contributeurs** concernant l’abonnement dans lequel réside l’espace de travail Azure Sentinel. Pour utiliser Azure Sentinel, vous avez besoin des autorisations des contributeurs ou des lecteurs sur le groupe de ressources auquel appartient l’espace de travail.

Une fois que vous avez configuré votre instance Azure AD B2C pour envoyer des journaux d'activité à Azure Monitor, vous devez activer une instance Azure Sentinel.

1. Connectez-vous au portail Azure. Assurez-vous que l’abonnement où LA (Log Analytics) est l’espace de travail créé à l’étape précédente est sélectionné.

2. Recherchez et sélectionnez **Azure Sentinel**.

3. Sélectionnez **Ajouter**.

   :::image type="content" source="./media/azure-sentinel/azure-sentinel-add.png" alt-text="rechercher Azure Sentinel dans le portail Azure":::

4. Sélectionnez l’espace de travail utilisé à l’étape précédente.

   :::image type="content" source="./media/azure-sentinel/create-new-workspace.png" alt-text="Sélectionner l’espace de travail Sentinel":::

5. Sélectionnez **Ajouter Azure Sentinel**.

   > [!NOTE]
   > Vous pouvez exécuter Azure Sentinel sur plusieurs espaces de travail, mais les données sont isolées sur un seul espace de travail. Pour plus d’informations sur l’activation de Sentinel, consultez ce guide de [démarrage rapide](../sentinel/quickstart-onboard.md).

## <a name="create-a-sentinel-rule"></a>Créer une règle Sentinel

> [!NOTE]
> Azure Sentinel fournit des modèles intégrés prêts à l’emploi pour vous aider à créer des règles de détection des menaces conçues par l’équipe d’experts et d’analystes de la sécurité de Microsoft. Les règles créées à partir de ces modèles rechercheront automatiquement toute activité qui semble suspecte sur l’ensemble de vos données. Étant donné qu’il n’existe aujourd’hui aucun connecteur Azure AD B2C natif, nous n’utiliserons pas de règles natives dans notre exemple. Pour ce didacticiel, nous allons créer notre propre règle.

Maintenant que vous avez activé Sentinel, vous souhaiterez être averti en cas de problème suspect dans votre locataire B2C.

Vous pouvez créer des règles d’analytique personnalisées pour vous aider à détecter les menaces et les comportements anormaux dans votre environnement. Ces règles recherchent des événements ou des ensembles d’événements spécifiques, vous alerte lorsque certains seuils d’événement ou certaines conditions sont atteints, puis génèrent des incidents pour plus d’investigation.

> [!NOTE]
> Pour une révision détaillée des règles analytiques, vous pouvez consulter ce [didacticiel](/azure/active-directory-b2c/articles/sentinel/detect-threats-custom.md).

Dans notre scénario, nous souhaitons recevoir une notification si une personne tente de forcer l’accès à notre environnement, mais qu’elle ne réussit pas, cela peut signifier une attaque en force brute, nous souhaitons être notifiés pour **_2 ou plusieurs connexions non réussies dans les 60 s_**

1. Dans le menu de navigation d'Azure Sentinel, sélectionnez **Analytique**.
2. Sur la barre d'action du haut de l'écran, sélectionnez **+Créer**, puis **Règle de requête planifiée**. Cela entraîne l’ouverture de l’**Assistant de règle analytique**.

   :::image type="content" source="./media/azure-sentinel/create-scheduled-rule.png" alt-text="sélectionner créer une règle de requête planifiée":::

3. Assistant de règle analytique - Onglet Général

   - Fournir un **Nom** unique et une **Description**
     - **Nom** : _Connexions B2C non réussies_ **Description** : _Notifier deux ou plus de connexions non réussies dans les 60 s_
   - Dans le champ **Tactique**, vous pouvez choisir les catégories d’attaque en fonction desquelles la règle doit être classifiée. Celles-ci reposent sur les tactiques du framework [MITRE ATT&CK](https://attack.mitre.org/).

     - Pour notre exemple, nous choisirons _PreAttack_

       > [!Tip]
       > MITRE ATT&CK® est une base de connaissances mondialement accessible regroupant les tactiques et les techniques des adversaires basées sur des observations réelles. La base de connaissances ATT&CK es utilisée comme base pour le développement de modèles de menaces et de méthodologies spécifiques.

   - Choisissez la **Gravité** d'alerte qui convient.
     - Comme cela est notre première règle, nous choisirons _Élevée_. Nous pouvons modifier ultérieurement la règle
   - Quand vous créez la règle, son **État** est **Activé** par défaut. Cela signifie qu’elle va s’exécuter dès que vous aurez fini de la créer. Si vous ne souhaitez pas qu’elle s’exécute immédiatement, sélectionnez **Désactivé**. Dans ce cas, la règle est ajoutée à votre onglet **Règles actives**, à partir duquel vous pouvez l’activer quand vous en avez besoin.

     :::image type="content" source="./media/azure-sentinel/create-new-rule.png" alt-text="fournir les propriétés de la règle de base":::

4. Définir la logique de requête de règle et configurer les paramètres

   Dans l’onglet **Définir la logique de la règle**, nous écrirons une requête directement dans le champ **Requête de règle**. Cette requête vous avertit lorsqu’il y a au moins deux connexions non réussies dans les 60 s à votre locataire B2C et s’organise par \_userPrincipalName_

   ```kusto
   SigninLogs
   | where ResultType != "0"
   | summarize Count = count() by bin(TimeGenerated, 60s), UserPrincipalName
   | project Count = toint(Count), UserPrincipalName
   | where Count >= 1
   ```

   :::image type="content" source="./media/azure-sentinel/rule-query.png" alt-text="entrer la requête de règle dans l’onglet logique":::

   Dans la section Planification de la requête, définissez les paramètres suivants :

   :::image type="content" source="./media/azure-sentinel/query-scheduling.png" alt-text="définir les paramètres de planification des requêtes":::

5. Cliquez sur Suivant dans **Paramètres d’incident (préversion)** et dans **Réponse automatisée**. Vous allez configurer et ajouter la réponse automatisée ultérieurement.

6. Ensuite, accédez à **Examiner et créer** pour examiner tous les paramètres de votre nouvelle règle d'alerte. Lorsque le message « validation réussie » apparaît, sélectionnez **Créer** pour initialiser votre règle d'alerte.

   :::image type="content" source="./media/azure-sentinel/review-create.png" alt-text="vérifier et créer la règle":::

7. Affichez la règle et les incidents générés.

   Vous trouverez la règle personnalisée (de type « Planifiée ») que vous venez de créer dans le tableau situé sous l'onglet **Règles actives** de l'écran **Analytique** principal. Dans cette liste, vous pouvez **_modifier_**, **_activer_**, **_désactiver_** ou **_supprimer_** les règles.

   :::image type="content" source="./media/azure-sentinel/rule-crud.png" alt-text="écran d’analyse présentant les options permettant de modifier, d’activer, de désactiver ou de supprimer des règles":::

   Pour voir les résultats de notre nouvelle règle de connexion non réussie B2C, accédez à la page **Incidents**, où vous pouvez ensuite trier, examiner les incidents et remédier aux menaces.

   Un incident peut inclure plusieurs alertes. C’est une agrégation de toutes les preuves pertinentes pour une investigation spécifique. Vous pouvez définir des propriétés telles que la gravité et l’état au niveau incident.

   > [!NOTE]
   > Pour une analyse détaillée de l’investigation d’incident, consultez [ce didacticiel](/azure/active-directory-b2c/articles/sentinel/investigate-cases.md)

   Pour commencer une investigation, sélectionnez un incident spécifique. Sur la droite, vous pouvez voir des informations détaillées sur l’incident, notamment sa gravité, les entités impliquées, les événements bruts qui ont déclenché cet incident et l’ID unique de l’incident.

   :::image type="content" source="./media/azure-sentinel/select-incident.png" alt-text="écran incident":::

   Pour afficher plus de détails sur les alertes et les entités de l’incident, sélectionnez **Afficher tous les détails** dans la page d’incident et passez en revue les onglets pertinents qui résument les informations sur l’incident.

   :::image type="content" source="./media/azure-sentinel/full-details.png" alt-text="règle 73":::

   Pour examiner plus de détails sur l’incident, vous pouvez sélectionner **Preuve->Evénements** ou **Evenements -> Lier à Log Analytics**

   Les résultats afficheront le _UserPrincipalName_ de l’identité essayant de se connecter dans le _nombre_ de tentatives.

   :::image type="content" source="./media/azure-sentinel/logs.png" alt-text="détails de l’incident sélectionné":::

## <a name="automated-response"></a>Réponse automatisée

Azure Sentinel fournit également une fonctionnalité SOAR robuste et vous trouverez des informations supplémentaires dans la documentation Sentinel officielle [ici](../sentinel/automation-in-azure-sentinel.md).

Les actions automatisées, appelées guide opérationnel dans Sentinel, peuvent être attachées à des règles d’analytique pour répondre à vos exigences.

Dans notre exemple, nous allons ajouter une notification par e-mail lors d’un incident créé par notre règle.

Pour accomplir notre tâche, nous utiliserons un guide opérationnel existant à partir du référentiel GitHub Sentinel [Incident-Email-Notification](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Incident-Email-Notification)

Une fois le guide opérationnel configuré, vous devez simplement modifier la règle existante et sélectionner le guide opérationnel dans l’onglet Automatisation :

:::image type="content" source="./media/azure-sentinel/automation-tab.png" alt-text="écran de configuration de la réponse automatisée associée à une règle":::

## <a name="next-steps"></a>Étapes suivantes

- Comme aucune règle n’est parfaite, si nécessaire, vous pouvez mettre à jour la requête de règle pour exclure les faux positifs. Pour plus d’informations, consultez [Gérer les faux positifs dans Azure Sentinel](../sentinel/false-positives.md)

- Pour faciliter l’analyse des données et la création de rapports visuels enrichis, choisissez-en un et téléchargez-le à partir d’une galerie de classeurs créés par des experts, qui révèlent des insights en fonction de vos données. [Ces classeurs](https://github.com/azure-ad-b2c/siem#workbooks) peuvent aisément être adaptés à vos besoins.

- En savoir plus sur Sentinel dans la [documentation Azure Sentinel](../sentinel/index.yml)