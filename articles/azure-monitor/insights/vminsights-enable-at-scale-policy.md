---
title: Activer Azure Monitor pour les machines virtuelles à l’aide d’Azure Policy | Microsoft Docs
description: Cet article décrit comment activer des Azure Monitor pour les machines virtuelles pour plusieurs machines virtuelles ou des machines virtuelles identiques à l’aide d’Azure Policy.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: magoedte
ms.openlocfilehash: 9664ad5272ffeb55bd85e3d4c4f4b70d05e97702
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524144"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-policy"></a>Activer Azure Monitor pour les machines virtuelles (version préliminaire) à l’aide d’Azure Policy

Cet article explique comment activer Azure Monitor pour les machines virtuelles (version préliminaire) pour les machines virtuelles ou des machines virtuelles identiques à l’aide d’Azure Policy. À la fin de ce processus, vous aura correctement configuré l’activation de l’Analytique de journal et les agents de dépendances et identifié les machines virtuelles qui ne sont pas conformes.

Pour découvrir, gérer et activer Azure Monitor pour les machines virtuelles pour toutes vos machines virtuelles ou les machines virtuelles identiques, vous pouvez utiliser la stratégie de Azure ou Azure PowerShell. Azure Policy est la méthode recommandée, car vous pouvez gérer des définitions de stratégie pour gérer efficacement vos abonnements pour garantir la conformité cohérente et mise en service automatique de qui vient d’être mis en service des machines virtuelles. Ces définitions de stratégie :

* Déployer Log Analytics Agent et Dependency Agent.
* Créer un rapport sur les résultats de conformité.
* Corriger les machines virtuelles non conformes.

Si vous vous intéressez dans cette tâche avec le modèle Azure PowerShell ou Azure Resource Manager, consultez [activer à l’aide d’Azure PowerShell ou Resource Manager modèle](vminsights-enable-at-scale-powershell.md). 

## <a name="manage-policy-coverage-feature-overview"></a>Gérer la vue d’ensemble des fonctionnalités de couverture de la stratégie

L’expérience avec Azure Policy pour gérer et déployer les définitions de stratégie pour Azure Monitor pour les machines virtuelles a été effectuée à l’origine exclusivement à partir d’Azure Policy. Avec le **couverture de la stratégie gérer** fonctionnalité, il rend plus simple et plus facile de découvrir, gérer et permettre à l’échelle le **activer Azure Monitor pour les machines virtuelles** initiative, qui inclut les définitions de stratégie mentionné précédemment. Vous pouvez accéder à cette nouvelle fonctionnalité à partir de la **prise en main** onglet dans Azure Monitor pour les machines virtuelles en sélectionnant **couverture de la stratégie gérer**. Il ouvre la page de couverture de la stratégie des machines virtuelles. 

![Azure Monitor à partir de l’onglet machines virtuelles prise en main](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

À partir de là vous pouvez vérifier et gérer la couverture de l’initiative de vos groupes d’administration et les abonnements, ainsi comprendre combien de machines virtuelles existent dans tous les groupes d’administration et abonnements et leur état de conformité.   

![Azure Monitor pour la page de stratégie de gestion de machines virtuelles](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Ces informations sont utiles pour planifier et exécuter votre scénario de gouvernance pour Azure Monitor pour les machines virtuelles à partir d’un emplacement central. Alors que la stratégie Azure fournit une vue de compatibilité lorsqu’une initiative/de la stratégie est affectée à une étendue, avec cette nouvelle page, vous pouvez découvrir où l’initiative/de la stratégie n’est pas affectée et affectez-le sur place. Toutes les actions (affecter, afficher, modifier) rediriger directement à Azure Policy. Azure Monitor pour la page de couverture de la stratégie des machines virtuelles est une expérience intégrée et développée pour uniquement l’initiative **activer Azure Monitor pour les machines virtuelles**. 

À partir de cette page vous pouvez également configurer votre espace de travail Analytique de journal pour Azure Monitor pour les machines virtuelles, qui effectue les opérations suivantes :

- Installe les solutions de l’installation de la carte de Service et Infrastructure Insights
- Active les compteurs de performance de système d’exploitation utilisés par les graphiques de performances, les classeurs, vos requêtes de journal personnalisées et les alertes.

![Azure Monitor pour les machines virtuelles configurer l’espace de travail](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Cette option n’est pas liée à des actions de stratégie et est disponible pour fournir un moyen simple pour satisfaire le [conditions préalables](vminsights-enable-overview.md) nécessaire à l’activation d’Azure Monitor pour les machines virtuelles.  

### <a name="what-information-is-available-on-this-page"></a>Quelles informations sont disponibles sur cette page ?
Le tableau suivant fournit une décomposition de quelles informations sont présentées dans la page de couverture de stratégie et comment interpréter.

| Fonction | Description  | 
|----------|-------------| 
| **Portée** | Groupe d’administration et les abonnements auxquels vous avez ou hérité l’accès pour la hiérarchie de groupes avec la possibilité d’Explorer la gestion.|
| **Rôle** | Votre rôle dans la portée, peut être propriétaire du lecteur ou contributeur. Dans certains cas, il peut sembler vide pour indiquer que vous pouvez avoir accès à l’abonnement mais pas au groupe d’administration il appartient. Informations dans d’autres colonnes varient selon votre rôle, car il s’agit de déterminer quelles données vous pouvez voir et les actions que vous pouvez effectuer en termes d’affectation d’initiative/stratégie (propriétaire), les modifier ou affichage de la conformité. |
| **Totale des machines virtuelles** | Nombre de machines virtuelles dans cette portée. Pour un groupe d’administration, il est la somme des machines virtuelles imbriquées sous les abonnements et/ou d’un groupe d’administration enfant. |
| **Couverture de l’affectation** | Pourcentage de machines virtuelles qui sont couverts par la stratégie/initiative. |
| **État de l’affectation** | Sous cette colonne, vous pouvez trouver des informations sur l’état de votre stratégie / initiative affectation. |
| **Machines virtuelles compatibles** | Nombre de machines virtuelles qui sont conformes à la stratégie/initiative. Pour l’initiative **activer Azure Monitor pour les machines virtuelles** Ceci est le nombre de machines virtuelles qui ont l’agent d’Analytique de journal et agent de dépendances. Dans certains cas, il peut sembler vide car aucune assignation, ou aucune machine virtuelle ou pas assez d’autorisations. Vous trouverez des informations sous l’état de conformité. |
| **Conformité** | Le nombre total de la conformité est la somme des ressources distinctes qui sont conformes divisée par la somme de toutes les ressources distinctes. |
| **État de conformité** | Pour plus d’informations sur l’état de conformité pour votre stratégie / initiative affectation.|

Lorsque vous affectez la stratégie/initiative, l’étendue sélectionnée dans l’attribution peut-être l’étendue répertoriés ou un sous-ensemble de celle-ci. Par exemple, vous avez créé une attribution pour un abonnement (étendue de la stratégie) et pas un groupe d’administration (étendue de la couverture). Dans ce cas, la valeur de **affectation couverture** indiquerait étendue de machines virtuelles dans la stratégie (ou une initiative) divisée par les machines virtuelles dans l’étendue de la couverture. Dans un autre cas, vous pouvez avez exclu certaines machines virtuelles, groupes de ressources ou un abonnement étendue de la stratégie. Si la valeur est vide, il indique que la stratégie/initiative n’existe pas ou vous n’êtes pas autorisé (les informations sont fournies dans l’état d’attribution).

## <a name="enable-using-azure-policy"></a>Activer à l’aide d’Azure Policy

Pour activer Azure Monitor pour machines virtuelles à l’aide d’Azure Policy dans votre tenant :

- Assigner l’initiative à une étendue : groupe d’administration, abonnement ou groupe de ressources
- Examiner et corriger les résultats de conformité

Pour plus d’informations sur l’affectation d’Azure Policy, consultez [Vue d’ensemble d’Azure Policy](../../governance/policy/overview.md#policy-assignment) et examinez la [présentation des groupes d’administration](../../governance/management-groups/index.md) avant de continuer.

### <a name="policies-for-azure-vms"></a>Stratégies pour les machines virtuelles Azure

Les définitions de stratégie pour une machine virtuelle Azure sont répertoriées dans le tableau suivant :

|Nom |Description |Type |
|-----|------------|-----|
|[Préversion] : Activer Azure Monitor pour machines virtuelles |Activer Azure Monitor pour machines virtuelles dans l’étendue spécifiée (groupe d’administration, abonnement ou groupe de ressources). Utilise l’espace de travail Log Analytics comme paramètre. |Initiative |
|[Préversion] : Vérifier le déploiement de Dependency Agent : image de machine virtuelle (OS) non listée |Présente les machines virtuelles comme non conformes si l’image de machine virtuelle (OS) n’est pas définie dans la liste et si l’agent n’est pas installé. |Stratégie |
|[Préversion] : Vérifier le déploiement de Log Analytics Agent : image de machine virtuelle (OS) non listée |Présente les machines virtuelles comme non conformes si l’image de machine virtuelle (OS) n’est pas définie dans la liste et si l’agent n’est pas installé. |Stratégie |
|[Préversion] : Déployer Dependency Agent pour les machines virtuelles Linux |Déployer Dependency Agent pour les machines virtuelles Linux si l’image de machine virtuelle (OS) est définie dans la liste et si l’agent n’est pas installé. |Stratégie |
|[Préversion] : Déployer Dependency Agent pour les machines virtuelles Windows |Déployer Dependency Agent pour les machines virtuelles Windows si l’image de machine virtuelle (OS) est définie dans la liste et si l’agent n’est pas installé. |Stratégie |
|[Préversion] : Déployer Log Analytics Agent pour les machines virtuelles Linux |Déployer Log Analytics Agent pour les machines virtuelles Linux si l’image de machine virtuelle (OS) est définie dans la liste et si l’agent n’est pas installé. |Stratégie |
|[Préversion] : Déployer Log Analytics Agent pour les machines virtuelles Windows |Déployer Log Analytics Agent pour les machines virtuelles Windows si l’image de machine virtuelle (OS) est définie dans la liste et si l’agent n’est pas installé. |Stratégie |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Stratégies pour les machines virtuelles Azure identiques

Les définitions de stratégie pour un jeu de mise à l’échelle de machine virtuelle Azure sont répertoriées dans le tableau suivant :

|Nom |Description |Type |
|-----|------------|-----|
|[Préversion] : Activer Azure Monitor pour les machines virtuelles identiques (VMSS) |Activer Azure Monitor pour les machines virtuelles identiques dans la portée spécifiée (groupe gestion, abonnement ou groupe de ressources). Utilise l’espace de travail Log Analytics comme paramètre. Remarque : Si votre upgradePolicy de jeu de mise à l’échelle est défini sur manuel, vous devez appliquer l’extension à toutes les machines virtuelles dans le jeu en appelant la mise à niveau sur ces derniers. Dans l’interface CLI, ce serait az vmss update-instances. |Initiative |
|[Préversion] : Déploiement de l’Agent de dépendance dans le VMSS – machine virtuelle Image (système d’exploitation) retirée de la liste de l’audit |Rapports machines virtuelles identiques comme non conforme si l’Image de machine virtuelle (OS) n’est pas définie dans la liste et l’agent n’est pas installé. |Stratégie |
|[Préversion] : Déploiement de l’Agent d’Analytique de journal d’audit dans le VMSS – machine virtuelle Image (système d’exploitation) non répertorié |Rapports machines virtuelles identiques comme non conforme si l’Image de machine virtuelle (OS) n’est pas définie dans la liste et l’agent n’est pas installé. |Stratégie |
|[Préversion] : Déployer l’Agent de dépendances Linux identiques de machines virtuelles (VMSS) |Déployer l’Agent de dépendances pour Linux à l’échelle de machine virtuelle définit si l’Image de machine virtuelle (se) est défini dans la liste et l’agent n’est pas installé. |Stratégie |
|[Préversion] : Déployer l’Agent de dépendances pour les machines virtuelles Windows identiques (VMSS) |Déployer des machines virtuelles identiques si l’Image de machine virtuelle (se) est défini dans la liste et l’agent n’est pas installé l’Agent pour Windows dépendance. |Stratégie |
|[Préversion] : Déployer Log Analytics Agent pour Linux VM Scale Sets (VMSS) |Déployer l’Agent d’Analytique de journal pour Linux à l’échelle de machine virtuelle définit si l’Image de machine virtuelle (se) est défini dans la liste et l’agent n’est pas installé. |Stratégie |
|[Préversion] : Déployer Log Analytics Agent pour Windows VM Scale Sets (VMSS) |Déployer le journal Analytique Agent pour Windows machines virtuelles identiques si l’Image de machine virtuelle (se) est défini dans la liste et l’agent n’est pas installé. |Stratégie |

La stratégie autonome (non incluse avec l’initiative) est décrite ici :

|Nom |Description |Type |
|-----|------------|-----|
|[Préversion] : Vérifier les machines virtuelles de l’espace de travail Log Analytics - Signaler les incompatibilités |Signale les machines virtuelles comme non conformes si elles n’alimentent pas de journal dans l’espace de travail Log Analytics spécifié dans l’attribution de stratégie/d’initiative. |Stratégie |

### <a name="assign-the-azure-monitor-initiative"></a>Assigner l’initiative Azure Monitor
Pour créer l’attribution de stratégie à partir de l’analyse Azure pour la page de couverture de la stratégie des machines virtuelles, procédez comme suit. Pour savoir comment effectuer ces étapes, consultez  [Créer une affectation de stratégie à partir du portail Azure](../../governance/policy/assign-policy-portal.md).

Lorsque vous affectez la stratégie/initiative, l’étendue sélectionnée dans l’attribution peut-être l’étendue répertoriés ici ou un sous-ensemble de celle-ci. Par exemple, vous avez créé d’affectation pour l’abonnement (étendue de la stratégie) et pas le groupe d’administration (étendue de la couverture) dans lequel la couverture du cas % indiquerait étendue de machines virtuelles dans la stratégie (ou une initiative) divisée par les machines virtuelles dans l’étendue de la couverture du. Dans d’autres cas, vous pourrez avez exclu des machines virtuelles, RGs ou l’abonnement à partir de l’étendue de la stratégie.  Au cas où il est vide, cela indique que soit la stratégie / initiative n’existe pas ou vous ne disposez pas des autorisations (les informations fournies dans l’état d’attribution)  

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Dans le Portail Azure, sélectionnez **Surveiller**. 

3. Choisissez **Machines virtuelles (préversion)** dans la section **Insights**.
 
4. Sélectionnez le **prise en main** et sur la page Sélectionnez **couverture de la stratégie gérer**.

5. Sélectionnez un groupe d’administration ou un abonnement à partir de la table, puis **étendue** en cliquant sur l’ellipse (...).      Dans cet exemple, une étendue limite l’affectation de stratégie à un regroupement de machines virtuelles pour la mise en conformité.

6. Sur le **attribution de stratégie Azure** page, elle est préremplie avec l’initiative **activer Azure Monitor pour les machines virtuelles**. 
    Le **nom de l’affectation** zone est automatiquement renseignée avec le nom d’initiative, mais vous pouvez le modifier. Vous pouvez également ajouter une description (facultatif). Le champ **Attribué par** est rempli automatiquement en fonction de l’utilisateur connecté. Cette valeur est facultative.

7. (Facultatif) Pour supprimer une ou plusieurs ressources à partir de l’étendue, sélectionnez **Exclusions**.

8. Dans la liste déroulante **Espace de travail Log Analytics** de la région prise en charge, sélectionnez un espace de travail.

   > [!NOTE]
   > Si l’espace de travail se trouve en dehors de l’étendue de l’affectation, accordez des autorisations de *Contributeur Log Analytics* à l’ID du principal de l’affectation de stratégie. Si vous ne le faites pas, vous risquez d’obtenir un message d’échec de déploiement du type : `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` Pour accorder l’accès, reportez-vous à [Configurer manuellement l’identité managée](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  La case à cocher **Identité managée** est sélectionnée, car l’initiative affectée inclut une stratégie avec l’effet *deployIfNotExists*.
    
9. Dans la liste déroulante **Gérer l’emplacement d’identité**, sélectionnez la région appropriée.

10. Sélectionnez **Attribuer**.

Une fois que vous créez l’attribution, page de couverture de la stratégie des machines virtuelles sur le moniteur Azure met à jour la couverture d’attribution, état d’attribution, machines virtuelles de la conformité et état de conformité pour refléter les modifications. 

La matrice suivante mappe chaque état de conformité possibles pour l’initiative.  

| État de conformité | Description  | 
|------------------|-------------|
| **Conforme** | Toutes les machines virtuelles dans l’étendue ont les agents Log Analytique et de dépendance déployées sur eux.|
| **Non conforme** | Pas toutes les machines virtuelles dans l’étendue ont l’Analytique de journal et les agents de dépendances déploiement sur eux et nécessitent la mise à jour.|
| **Non commencé** | Une affectation a été ajoutée. |
| **Lock** | Vous n’avez pas de privilèges suffisants pour le groupe d’administration. <sup>1</sup> | 
| **Vide** | Aucune stratégie n’est affectée. | 

<sup>1</sup> si vous n’avez pas accès au groupe d’administration, demandez à un propriétaire pour fournir un accès ou d’afficher la conformité et de gérer des attributions via les groupes d’administration enfants ou des abonnements. 

Le tableau suivant mappe chaque état d’attribution possibles pour l’initiative.

| État de l'attribution | Description  | 
|------------------|-------------|
| **Success** | Toutes les machines virtuelles dans l’étendue ont les agents Log Analytique et de dépendance déployées sur eux.|
| **Avertissement** | L’abonnement n’est pas sous un groupe d’administration.|
| **Non commencé** | Une affectation a été ajoutée. |
| **Lock** | Vous n’avez pas de privilèges suffisants pour le groupe d’administration. <sup>1</sup> | 
| **Vide** | Aucune machine virtuelle n’existe ou la stratégie n’est pas affectée. | 
| **Action** | Affecter une stratégie ou modifier l’affectation | 

<sup>1</sup> si vous n’avez pas accès au groupe d’administration, demandez à un propriétaire pour fournir un accès ou d’afficher la conformité et de gérer des attributions via les groupes d’administration enfants ou des abonnements. 

## <a name="review-and-remediate-the-compliance-results"></a>Examiner et corriger les résultats de conformité

L’exemple suivant est pour une machine virtuelle Azure, mais s’applique également aux machines virtuelles identiques. Pour savoir comment examiner les résultats de conformité, vous pouvez consulter [Identifier les ressources non conformes](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). Pour la page de couverture de la stratégie des machines virtuelles de l’analyse Azure, sélectionnez un groupe d’administration ou un abonnement à partir de la table, puis **afficher la conformité** en cliquant sur l’ellipse (...).   

![Conformité de stratégie pour les machines virtuelles Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

Sur la base des résultats des stratégies incluses avec l’initiative, les machines virtuelles sont présentées comme étant non conformes dans les scénarios suivants :

* Log Analytics Agent ou Dependency Agent n’est pas déployé.  
    Il s’agit d’un cas courant pour une étendue comportant des machines virtuelles existantes. Pour limiter ce risque, déployez les agents requis en [créant des tâches de correction](../../governance/policy/how-to/remediate-resources.md) sur une stratégie non conforme.  
    - [Préversion]: Deploy Dependency Agent for Linux VMs
    - [Préversion]: Deploy Dependency Agent for Windows VMs
    - [Préversion]: Deploy Log Analytics Agent for Linux VMs
    - [Préversion]: Deploy Log Analytics Agent for Windows VMs

* L’image de machine virtuelle (OS) n’est pas identifiée dans la définition de stratégie.  
    Les critères de la stratégie de déploiement incluent uniquement les machines virtuelles qui sont déployées à partir d’images de machine virtuelle Azure connues. Consultez la documentation pour savoir si le système d’exploitation de la machine virtuelle est pris en charge. Si ce n’est pas le cas, dupliquez la stratégie de déploiement et mettez-la à jour ou modifiez-la pour rendre l’image conforme.  
    - [Préversion] : Vérifier le déploiement de Dependency Agent : image de machine virtuelle (OS) non listée
    - [Préversion] : Vérifier le déploiement de Log Analytics Agent : image de machine virtuelle (OS) non listée

* Les machines virtuelles ne sont pas connectées à l’espace de travail Log Analytics spécifié.  
    Il est possible que certaines machines virtuelles de l’étendue de l’initiative soient connectées à un espace de travail Log Analytics différent de celui spécifié dans l’affectation de stratégie. Cette stratégie est un outil qui permet d’identifier les machines virtuelles qui relèvent d’un espace de travail non conforme.  
    - [Préversion]: Audit Log Analytics Workspace for VM - Report Mismatch

## <a name="edit-initiative-assignment"></a>Modifier l’affectation d’initiative

À tout moment après avoir attribué une initiative vers un groupe d’administration ou d’un abonnement, vous pouvez le modifier pour modifier les propriétés suivantes :

- Nom de l'affectation
- Description 
- Affectée par
- Espace de travail Log Analytics
- Exceptions

## <a name="next-steps"></a>Étapes suivantes

Maintenant que l’analyse est activée pour vos machines virtuelles, ces informations sont disponibles pour l’analyse avec Azure Monitor pour les machines virtuelles. Pour savoir comment utiliser la fonctionnalité de contrôle de l’intégrité, consultez [Comprendre l’intégrité de vos machines virtuelles Azure grâce à Azure Monitor pour machines virtuelles](vminsights-health.md). Pour afficher les dépendances des applications détectées, consultez [Utilisation de la fonctionnalité Map d’Azure Monitor pour machines virtuelles dans le but de comprendre les composants d’application](vminsights-maps.md). Pour identifier les goulots d’étranglement et les performances d’utilisation globale avec vos machines virtuelles, consultez l’article indiquant comment [afficher les performances avec Azure Monitor pour les machines virtuelles](vminsights-performance.md). Pour visualiser les dépendances d’application découvertes, consultez l’article expliquant comment [afficher la fonctionnalité Map d’Azure Monitor pour les machines virtuelles](vminsights-maps.md).