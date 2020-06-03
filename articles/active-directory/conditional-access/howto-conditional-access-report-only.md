---
title: Configurer une stratégie d’accès conditionnel en mode Rapport uniquement – Azure Active Directory
description: Utilisation du mode Rapport uniquement dans l’accès conditionnel pour faciliter l’adoption
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46a00d55c58992be1009da1de5441ebe4e589a70
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83994967"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode"></a>Configurer une stratégie d’accès conditionnel en mode Rapport seul

Pour configurer une stratégie d’accès conditionnel en mode Rapport uniquement :

> [!IMPORTANT]
> Si votre organisation ne l’a pas encore fait, [configurez l’intégration d’Azure Monitor avec Azure AD](#set-up-azure-monitor-integration-with-azure-ad). Ce processus doit avoir lieu avant que les données soient disponibles pour la révision.

1. Connectez-vous au **portail Azure** en tant qu’administrateur de l’accès conditionnel, administrateur de la sécurité ou administrateur général.
1. Accédez à **Azure Active Directory** > **Sécurité** > **Accès conditionnel.**
1. Sélectionnez **Nouvelle stratégie**.
1. Configurez les conditions de stratégie et les contrôles d’octroi requis en fonction des besoins.
1. Sous **Activer la stratégie**, définissez le mode **Rapport uniquement**.
1. Sélectionnez **Enregistrer**.

> [!TIP]
> Vous pouvez modifier l’état **Activer la stratégie** d’une stratégie de **Activé** en **Rapport uniquement**, mais cela désactive l’application de la stratégie. 

Affichez le résultat de Rapport uniquement dans les journaux d’activité de connexion d’Azure AD.

Pour afficher le résultat d’une stratégie Rapport uniquement pour une connexion particulière :

1. Connectez-vous au **Portail Azure** en tant que lecteur de rapport, lecteur de sécurité, administrateur de la sécurité ou administrateur général.
1. Accédez à **Azure Active Directory** > **Connexions**.
1. Sélectionnez une connexion ou ajoutez des filtres pour affiner les résultats.
1. Dans le tiroir **Détails**, sélectionnez l’onglet **Rapport seul** pour afficher les stratégies évaluées lors de la connexion.

> [!NOTE]
> Lorsque vous téléchargez les journaux de connexion, choisissez le format JSON pour inclure les résultats en mode rapport uniquement de la stratégie d’accès conditionnel.

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>Configurer l’intégration d’Azure Monitor avec Azure AD

Pour afficher l’impact agrégé des stratégies d’accès conditionnel à l’aide du nouveau classeur Insights sur l’accès conditionnel, vous devez intégrer Azure Monitor avec Azure AD et exporter les journaux de connexion. La configuration de cette intégration se fait en deux étapes : 

1. [Souscrivez un abonnement Azure Monitor et créez un espace de travail](/azure/azure-monitor/learn/quick-create-workspace).
1. [Exportez les journaux de connexion d’Azure AD vers Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Pour plus d’informations sur la tarification d’Azure Monitor, voir la [page de tarification d’Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). Les ressources pour estimer les coûts, définir un plafond quotidien ou personnaliser la période de conservation des données, sont mentionnées dans l’article [Gérer l’utilisation et les coûts avec les journaux Azure Monitor](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment).

## <a name="view-conditional-access-insights-workbook"></a>Afficher le classeur Insights sur l’accès conditionnel

Après avoir intégré vos journaux Azure AD avec Azure Monitor, vous pouvez surveiller l’impact des stratégies d’accès conditionnel à l’aide des nouveaux classeurs Insights sur l’accès conditionnel.

1. Connectez-vous au **Portail Azure** en tant qu’administrateur de la sécurité ou administrateur général.
1. Accédez à **Azure Active Directory** > **Classeurs**.
1. Sélectionnez **Insights sur l’accès conditionnel**.
1. Sélectionnez une ou plusieurs stratégies dans la liste déroulante **Stratégie d’accès conditionnel**. Toutes les stratégies activées sont sélectionnées par défaut.
1. Sélectionnez un intervalle de temps (si celui-ci dépasse le jeu de données disponible, le rapport affiche toutes les données disponibles). Une fois que vous avez défini les paramètres **Stratégie d’accès conditionnel** et **Intervalle de temps**, le rapport est chargé.
   1. Vous pouvez éventuellement rechercher des **Utilisateurs** ou **Applications** individuels pour limiter l’étendue du rapport.
1. Choisissez entre l’affichage des données dans l’intervalle de temps par le nombre d’utilisateurs ou le nombre de connexions.
1. Selon la **Vue de données**, le **Résumé de l’impact** affiche le nombre d’utilisateurs ou de connexions dans l’étendue des paramètres choisis, regroupés par Nombre total, **Réussite**, **Échec**, **Action de l’utilisateur nécessaire** et **Non appliqué**. Sélectionnez une vignette pour examiner les connexions d’un type de résultat particulier. 
   1. Si vous avez modifié les paramètres du classeur, vous pouvez choisir d’en enregistrer une copie en vue d’une utilisation ultérieure. Sélectionnez l’icône Enregistrer en haut du rapport, et spécifiez un nom et un emplacement pour l’enregistrement.
1. Faites défiler pour afficher la répartition des connexions correspondant à chaque condition.
1. Affichez les **Détails de connexion** en bas du rapport pour examiner les événements de connexion individuels filtrés par les sélections ci-dessus.

> [!TIP] 
> Vous devez examiner en profondeur une requête spécifique ou exporter les détails de la connexion ? Sélectionnez le bouton à droite de tout requête pour ouvrir celle-ci dans Log Analytics. Sélectionnez Exporter pour exporter au format CSV ou Power BI.

## <a name="common-problems-and-solutions"></a>Problèmes courants et solutions

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Pourquoi les requêtes dans le classeur échouent-elles ?

Des clients ont remarqué que des requêtes échouent parfois si un espace de travail erroné ou plusieurs espaces de travail sont associés au classeur. Pour résoudre ce problème, cliquez sur **Modifier** en haut du classeur, puis sur l’engrenage paramètres. Sélectionnez et supprimez les espaces de travail qui ne sont pas associés au classeur. Il ne doit y avoir qu’un seul espace de travail associé à chaque classeur.

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>Pourquoi le paramètre de liste déroulante Stratégies d’accès conditionnel ne contient-il pas mes stratégies ?

La liste déroulante Stratégies d’accès conditionnel est établie en interrogeant les connexions les plus récentes sur une période de 4 heures. Si un locataire n’a pas établi de connexion au cours des 4 dernières heures, il est possible que la liste déroulante soit vide. Si ce retard constitue un problème persistant, par exemple pour de petits locataires avec des connexions peu fréquentes, les administrateurs peuvent modifier la requête de la liste déroulante Stratégies d’accès conditionnel en définissant une durée supérieure à 4 heures.

## <a name="next-steps"></a>Étapes suivantes

[Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)

Pour plus d’informations sur les classeurs Azure AD, voir l’article[Comment utiliser des classeurs Azure Monitor pour créer des rapports Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md).
