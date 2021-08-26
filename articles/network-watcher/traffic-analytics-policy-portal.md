---
title: Déployer et gérer Traffic Analytics à l’aide d’Azure Policy
titleSuffix: Azure Network Watcher
description: Cet article explique comment utiliser les stratégies intégrées pour gérer le déploiement de Traffic Analytics
services: network-watcher
documentationcenter: na
author: moagra
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2021
ms.author: moagra
ms.openlocfilehash: 3be6d5b30d270c9687b7100c07ee675268cee5c0
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122609147"
---
# <a name="deploy-and-manage-traffic-analytics-using-azure-policy"></a>Déployer et gérer Traffic Analytics à l’aide d’Azure Policy 

Azure Policy aide à appliquer les normes organisationnelles et à évaluer la conformité à l’échelle. Les cas d’usage courants pour Azure Policy incluent la mise en œuvre de la gouvernance pour la cohérence des ressources, la conformité réglementaire, la sécurité, le coût et la gestion. Dans cet article, nous allons traiter trois stratégies intégrées disponibles pour [Traffic Analytics](./traffic-analytics.md) afin de gérer votre configuration.

Si vous créez une définition Azure Policy pour la première fois, vous pouvez lire : 
- [Vue d’ensemble d’Azure Policy](../governance/policy/overview.md) 
- [Tutoriel sur la création d’une attribution Azure Policy](../governance/policy/assign-policy-portal.md#create-a-policy-assignment).


## <a name="locate-the-policies"></a>Rechercher les stratégies
1. Accédez au Portail Azure – [portal.azure.com](https://portal.azure.com) 

Accédez à la page d’Azure Policy en recherchant Stratégie dans la barre de recherche supérieure ![Page d’accueil de Stratégie](./media/network-watcher-builtin-policy/1_policy-search.png)

2. Accédez à l’onglet **Attributions** à partir du volet gauche

![onglet Attributions](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. Cliquez sur le bouton **Attribuer une stratégie** 

![Bouton Attribuer une stratégie](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. Cliquez sur le menu à trois points sous « Définitions de stratégie » pour afficher les stratégies disponibles

5. Utilisez le filtre Type et choisissez « Intégré ». Recherchez ensuite « traffic analytics »

Vous devez voir les trois stratégies intégrées dans ![Liste des stratégies pour Traffic Analytics](./media/traffic-analytics/policy-filtered-view.png)

6. Choisissez la stratégie que vous souhaitez attribuer

- *« Les journaux de flux Network Watcher doivent avoir Traffic Analytics activé »* est la stratégie d’audit qui signale les journaux de flux non conformes, c’est-à-dire les journaux de flux pour lesquels Traffic Analytics n’est pas activé
- *« Configurer des groupes de sécurité réseau pour utiliser un espace de travail spécifique pour Traffic Analytics »* et *« Configurer des groupes de sécurité réseau pour activer Traffic Analytics »* sont les stratégies avec une action de déploiement. Elles activent Traffic Analytics sur tous les groupes de sécurité réseau, en remplaçant ou pas les paramètres déjà configurés en fonction de la stratégie activée.

Les instructions distinctes ci-dessous s’appliquent pour chaque stratégie.  

## <a name="audit-policy"></a>Stratégie d’audit 

### <a name="network-watcher-flow-logs-should-have-traffic-analytics-enabled"></a>Traffic Analytics doit être activé pour les journaux de flux Network Watcher

La stratégie audite tous les objets Azure Resource Manager existants de type « Microsoft.Network/networkWatchers/flowLogs » et vérifie si Traffic Analytics est activé via la propriété « networkWatcherFlowAnalyticsConfiguration.enabled » de la ressource des journaux de flux. Elle signale la ressource des journaux de flux dont la propriété a la valeur False.

Si vous souhaitez voir la définition complète de la stratégie, accédez à [l’onglet Définitions](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) et recherchez « traffic analytics » pour trouver la stratégie.

### <a name="assignment"></a>Affectation

1. Renseignez les détails de votre stratégie

- Étendue : il peut s’agir d’un abonnement ou d’un groupe de ressources. Dans ce dernier cas, sélectionnez le groupe de ressources qui contient la ressource des journaux de flux (et non le groupe de sécurité réseau).
- Définition de stratégie : Doit être choisie comme indiqué dans la section « Rechercher les stratégies ».
- AssignmentName : Choisissez un nom descriptif 

2. Cliquez sur « Vérifier + créer » pour passer en revue votre attribution

La stratégie ne requiert aucun paramètre. Lorsque vous affectez une stratégie d’audit, vous n’avez pas besoin de renseigner les détails dans l’onglet « Correction ».  

![Révision de la stratégie d’audit - Traffic Analytics](./media/traffic-analytics/policy-one-assign.png)

### <a name="results"></a>Résultats

Pour vérifier les résultats, ouvrez l’onglet Conformité et recherchez le nom de votre attribution.
Une fois votre stratégie exécutée, vous devriez voir quelque chose de similaire à la capture d’écran suivante. Si votre stratégie n’a pas été exécutée, patientez quelques instants. 

![Résultats de la stratégie d’audit - Traffic Analytics](./media/traffic-analytics/policy-one-results.png)

## <a name="deploy-if-not-exists-policy"></a>Stratégie de déploiement si inexistant 

### <a name="configure-network-security-groups-to-use-specific-workspace-for-traffic-analytics"></a>Configurer les groupes de sécurité réseau afin d’utiliser un espace de travail spécifique pour Traffic Analytics 

Elle signale le groupe de sécurité réseau pour lequel Traffic Analytics n’est pas activé. Cela signifie que pour le groupe de sécurité réseau signalé, la ressource des journaux de flux correspondante n’existe pas ou la ressource des journaux de flux existe, mais Traffic Analytics n’est pas activé sur celle-ci. Vous pouvez créer une tâche de correction si vous souhaitez que la stratégie affecte les ressources existantes.
Network Watcher est un service régional, donc cette stratégie s’applique aux groupes de sécurité réseau appartenant à une région spécifique uniquement dans l’étendue sélectionnée. (Pour une autre région, créez une autre attribution de stratégie.)
 
La correction peut être attribuée lors de l’attribution de la stratégie ou une fois que la stratégie a été attribuée et évaluée. La correction active Traffic Analytics sur toutes les ressources signalées avec les paramètres fournis. Notez que si un groupe de sécurité réseau a déjà des journaux de flux activés dans un ID de stockage spécifique, mais qu’il n’a pas Traffic Analytics activé, alors la correction activera Traffic Analytics sur ce groupe de sécurité réseau avec les paramètres fournis. Si pour le groupe de sécurité réseau signalé, l’ID de stockage fourni dans les paramètres est différent de celui déjà activé pour les journaux de flux, alors ce dernier est remplacé par l’ID de stockage fourni dans la tâche de correction. Si vous ne souhaitez pas le remplacer, utilisez la stratégie *« Configurer des groupes de sécurité réseau pour activer Traffic Analytics »* décrite ci-dessous.

Si vous souhaitez voir la définition complète de la stratégie, accédez à [l’onglet Définitions](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) et recherchez « Traffic Analytics » pour trouver la stratégie. 

### <a name="configure-network-security-groups-to-enable-traffic-analytics"></a>Configurer des groupes de sécurité réseau pour activer Traffic Analytics

Elle est identique à la stratégie ci-dessus, sauf qu’au cours de la correction, elle ne remplace pas les paramètres des journaux de flux sur les groupes de sécurité réseau signalés où les journaux de flux sont activés, mais Traffic Analytics est désactivé, par le paramètre fourni dans l’attribution de stratégie.

### <a name="assignment"></a>Affectation

1. Renseignez les détails de votre stratégie

- Étendue : il peut s’agir d’un abonnement ou d’un groupe de ressources  
- Définition de stratégie : Doit être choisie comme indiqué dans la section « Rechercher les stratégies ».
- AssignmentName : Choisissez un nom descriptif 

2. Ajoutez des paramètres de stratégie 

- Région NSG : Régions Azure sur lesquelles la stratégie est ciblée
- ID de stockage : ID de ressource complet du compte de stockage. Ce compte de stockage doit se trouver dans la même région que le NSG.
- Network Watchers RG : Nom du groupe de ressources qui contient votre ressource Network Watcher. Si vous ne l’avez pas renommé, vous pouvez entrer « NetworkWatcherRG », qui est la valeur par défaut.
- Nom de Network Watcher : Nom du service d’observateur de réseau régional. Format: NetworkWatcher_NomRégion. Exemple : NetworkWatcher_centralus.
- ID de ressource d’espace de travail : ID de ressource de l’espace de travail dans lequel Traffic Analytics doit être activé. Le format est « /subscriptions/<SubscriptionID>/resourceGroups/<ResouceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName> »
- WorkspaceID : GUID de l’espace de travail
- WorkspaceRegion : région de l’espace de travail (notez qu’elle n’a pas besoin d’être identique à la région du NSG)
- TimeInterval : fréquence à laquelle les journaux traités sont envoyés dans l’espace de travail. Les valeurs actuellement autorisées sont 60 minutes et 10 minutes. La valeur par défaut est de 60 minutes.
- Effet : DeployIfNotExists (valeur déjà attribuée)

3. Ajoutez des détails de la correction

- Activez la case à cocher *« Créer une tâche de correction »* si vous souhaitez que la stratégie affecte les ressources existantes
- L’option *« Créer une identité managée »* doit être déjà cochée
- Le même emplacement que le précédent pour votre identité managée doit être sélectionné
- Vous aurez besoin de l’autorisation Contributeur ou Propriétaire pour utiliser cette stratégie. Si vous disposez de ces autorisations, vous ne devriez pas voir d’erreurs.

4. Cliquez sur « Vérifier + créer » pour passer en revue votre attribution. Vous devriez voir un résultat similaire à la capture d’écran suivante.

![Révision de stratégie DINE - Traffic Analytics](./media/traffic-analytics/policy-two-review.png) 


### <a name="results"></a>Résultats

Pour vérifier les résultats, ouvrez l’onglet Conformité et recherchez le nom de votre attribution.
Une fois votre stratégie exécutée, vous devez voir quelque chose de semblable à la capture d’écran suivante. Si votre stratégie n’a pas été exécutée, patientez quelques instants.

![Résultats de stratégie DINE - Traffic Analytics](./media/traffic-analytics/policy-two-results.png)  

### <a name="remediation"></a>Correction

Pour corriger manuellement, sélectionnez *« Créer une tâche de correction »* sous l’onglet de conformité, comme illustré ci-dessus

![Corriger la stratégie DINE - Traffic Analytics](./media/traffic-analytics/policy-two-remediate.png) 


## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="remediation-task-fails-with-policyauthorizationfailed-error-code"></a>La tâche de correction échoue avec le code d’erreur « PolicyAuthorizationFailed ».

Exemple d’erreur « The policy assignment '/subscriptions/123ds-fdf3657-fdjjjskms638/resourceGroups/DummyRG/providers/Microsoft.Authorization/policyAssignments/b67334e8770a4afc92e7a929/' resource identity does not have the necessary permissions to create deployment. » (L’identité de la ressource '/subscriptions/123ds-fdf3657-fdjjjskms638/resourceGroups/DummyRG/providers/Microsoft.Authorization/policyAssignments/b67334e8770a4afc92e7a929/' de l’attribution de stratégie ne dispose pas des autorisations nécessaires pour créer un déploiement).

Dans de tels scénarios, l’autorisation d’accès à l’identité managée de l’attribution doit être accordée manuellement. Accédez au groupe de ressources/à l’abonnement approprié (contenant les ressources fournies dans les paramètres de la stratégie) et accordez l’accès Contributeur à l’identité managée créée par la stratégie. Dans l’exemple ci-dessus, « b67334e8770a4afc92e7a929 » doit être le contributeur.


## <a name="next-steps"></a>Étapes suivantes 

-   En savoir plus sur les [Stratégies intégrées des journaux de flux NSG](./nsg-flow-logs-policy-portal.md)
-   En savoir plus sur [Traffic Analytics](./traffic-analytics.md)
-   En savoir plus sur [Network Watcher](./index.yml)
