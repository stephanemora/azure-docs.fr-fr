---
title: Gérer les mises à jour et les correctifs pour vos machines virtuelles dans Azure Automation
description: Cet article explique comment utiliser Update Management pour gérer les mises à jour et les correctifs pour vos machines virtuelles Azure et non-Azure.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: e4f630931e3cd79a46a539955b45d72e5b573271
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87449532"
---
# <a name="manage-updates-and-patches-for-your-vms"></a>Gérer les mises à jour et les correctifs pour vos machines virtuelles

Les mises à jour de logiciel dans Azure Automation Update Management offrent un ensemble d’outils et de ressources qui peuvent faciliter la gestion de la tâche complexe que représentent le suivi et l’application des mises à jour de logiciel sur des machines dans le cloud Azure et le cloud hybride. Il est essentiel de disposer d’un processus efficace de gestion des mises à jour de logiciel pour assurer le fonctionnement des opérations, résoudre les problèmes de sécurité et réduire les risques de menaces accrues en matière de cybersécurité. Toutefois, la gestion des mises à jour logicielles nécessite une attention constante et continue du fait que les technologies évoluent rapidement et que de nouvelles menaces de sécurité émergent constamment.

> [!NOTE]
> Update Management prend en charge le déploiement des mises à jour internes et leur prétéléchargement. Cette prise en charge nécessite la modification de certains paramètres sur les systèmes mis à jour. Pour découvrir comment configurer ces paramètres sur vos systèmes, consultez [Configurer les paramètres de Windows Update pour Azure Automation Update Management](update-mgmt-configure-wuagent.md).

Avant de tenter de gérer les mises à jour de vos machines virtuelles, assurez-vous d’avoir activé Update Management sur ces dernières à l’aide de l’une des méthodes suivantes :

* [Activer Update Management à partir d’un compte Automation](update-mgmt-enable-automation-account.md)
* [Activer Update Management à partir du portail Azure](update-mgmt-enable-portal.md)
* [Activer Update Management à partir d’un runbook](update-mgmt-enable-runbook.md)
* [Activer Update Management à partir d’une machine virtuelle Azure](update-mgmt-enable-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Limiter l’étendue du déploiement

Update Management utilise une configuration d’étendue au sein de l’espace de travail pour cibler les ordinateurs devant recevoir la mise à jour. Pour plus d’informations, consultez [Utiliser des configurations d’étendue pour Update Management](update-mgmt-scope-configuration.md).

## <a name="compliance-assessment"></a>Évaluation de la conformité

Avant de déployer des mises à jour de logiciel sur vos machines, examinez les résultats de l’évaluation de la conformité des mises à jour pour les machines concernées. Pour chaque mise à jour de logiciel, son état de conformité est enregistré, puis, une fois l’évaluation terminée, celui-ci est collecté et transféré en bloc dans les journaux d’activité Azure Monitor.

Sur une machine Windows, l’analyse de conformité est effectuée toutes les 12 heures par défaut. En plus de l’analyse planifiée, l’analyse de conformité de mise à jour est lancée dans les 15 minutes qui suivent le redémarrage de l’agent Log Analytics pour Windows, avant et après l’installation de la mise à jour. Il est également important de consulter nos recommandations sur la façon de [configurer le client Windows Update](update-mgmt-configure-wuagent.md) avec Update Management afin d’éviter tout problème qui empêcherait de le gérer correctement.

Sur une machine Linux, l’analyse de conformité est effectuée toutes les heures par défaut. Si l’agent Log Analytics pour Linux est redémarré, une analyse de conformité est lancée dans les 15 minutes.

Les résultats de conformité sont présentés dans Update Management pour chaque machine évaluée. Pour une nouvelle machine dont la gestion est activée, le tableau de bord peut prendre jusqu’à 30 minutes pour afficher les données mises à jour.

Pour savoir comment afficher les résultats de compatibilité, consultez [Surveiller les mises à jour de logiciel](update-mgmt-view-update-assessments.md).

## <a name="deploy-updates"></a>Déployer des mises à jour

Après examen des résultats de conformité, la phase de déploiement de mises à jour de logiciel consiste à déployer ces dernières. Pour installer les mises à jour, planifiez un déploiement qui s’aligne avec votre fenêtre de planification et de maintenance des versions. Vous pouvez choisir les types de mises à jour à inclure dans le déploiement. Par exemple, vous pouvez inclure des mises à jour critiques ou de sécurité et exclure des correctifs cumulatifs.

Pour savoir comment planifier un déploiement de mises à jour, consultez [Déployer des mises à jour de logiciel](update-mgmt-deploy-updates.md).

## <a name="review-update-deployments"></a>Examiner les déploiements de mises à jour

Une fois le déploiement terminé, passez en revue le processus pour déterminer la réussite du déploiement des mises à jour par machine ou par groupe cible. Pour savoir comment surveiller l’état du déploiement, consultez [Examiner l’état du déploiement](update-mgmt-deploy-updates.md#check-deployment-status).

## <a name="next-steps"></a>Étapes suivantes

* Pour savoir comment créer des alertes pour vous informer des résultats du déploiement des mises à jour, consultez [Créer des alertes pour Update Management](update-mgmt-configure-alerts.md).

* Vous pouvez [interroger les journaux d’Azure Monitor](update-mgmt-query-logs.md) pour analyser les évaluations de mise à jour, les déploiements et autres tâches de gestion associées. Vous y trouverez des requêtes prédéfinies pour faciliter la prise en main.