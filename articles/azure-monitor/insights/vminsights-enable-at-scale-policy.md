---
title: Activer Azure Monitor pour machines virtuelles à l’aide d’Azure Policy
description: Cet article décrit l’activation d’Azure Monitor pour machines virtuelles sur plusieurs groupes de machines virtuelles identiques ou plusieurs machines virtuelles Azure à l’aide d’Azure Policy.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 73c18d45136eea90ad29dc1bd40c4539dddc0ee6
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767260"
---
# <a name="enable-azure-monitor-for-vms-by-using-azure-policy"></a>Activer Azure Monitor pour machines virtuelles à l’aide d’Azure Policy

Cet article explique comment activer Azure Monitor pour machines virtuelles sur des groupes de machines virtuelles identiques ou des machines virtuelles Azure à l’aide d’Azure Policy. À la fin de ce processus, vous aurez configuré correctement l’activation de l’agent Log Analytics et de l’agent de dépendances, et identifié les machines virtuelles qui ne sont pas conformes.

Pour découvrir, gérer et activer Azure Monitor pour machines virtuelles pour l’ensemble de vos groupes de machines virtuelles identiques ou machines virtuelles Azure, vous pouvez utiliser Azure Policy ou Azure PowerShell. Azure Policy est la méthode que nous recommandons, car vous pouvez gérer des définitions de stratégie pour régir efficacement vos abonnements afin de garantir la conformité cohérente et l’activation automatique des machines virtuelles nouvellement mises en service. Ces définitions de stratégie :

* Déployer Log Analytics Agent et Dependency Agent.
* Créer un rapport sur les résultats de conformité.
* Corriger les machines virtuelles non conformes.

Si vous souhaitez accomplir ces tâches avec Azure PowerShell ou un modèle Azure Resource Manager, consultez [Activer Azure Monitor pour machines virtuelles à l’aide du modèle Azure PowerShell ou Resource Manager](vminsights-enable-at-scale-powershell.md).

## <a name="prerequisites"></a>Prérequis
Avant d’utiliser la stratégie pour intégrer vos machines virtuelles Azure et vos groupes de machines virtuelles identiques à Azure Monitor pour machines virtuelles, vous devez activer la solution VMInsights sur l’espace de travail que vous allez utiliser pour stocker vos données d’analyse. Cette tâche peut être effectuée à partir de la page **Prise en main** dans Azure Monitor sous l’onglet **Autres options d’intégration**.  Sélectionnez **Configurer un espace de travail**, ce qui vous invite à sélectionner l’espace de travail à configurer.

![Configurer l’espace de travail](media/vminsights-enable-at-scale-policy/configure-workspace.png)

Vous pouvez également configurer votre espace de travail en choisissant **Activer l'utilisation de la stratégie** puis en sélectionnant le bouton **Configurer l’espace de travail** dans la barre d’outils.  Cela installe la solution VMInsights sur l’espace de travail sélectionné, ce qui permet à l’espace de travail de stocker les données d’analyse envoyées par les machines virtuelles et les groupes de machines virtuelles identiques que vous activez à l’aide de la stratégie. 

![Activer l'utilisation de la stratégie](media/vminsights-enable-at-scale-policy/enable-using-policy.png)

## <a name="manage-policy-coverage-feature-overview"></a>Présentation de la fonctionnalité Gérer la couverture de stratégie

Azure Monitor pour machines virtuelles facilite la découverte, la gestion et l’activation à grande échelle de l’initiative **Activer Azure Monitor pour machines virtuelles**, qui inclut les définitions de stratégie mentionnées précédemment. Pour accéder à cette fonctionnalité, sélectionnez **Autres options d’intégration** à partir de l’onglet **Prise en main** dans Azure Monitor pour machines virtuelles. Sélectionnez **Gérer la couverture de stratégie** pour afficher la page **Azure Monitor for VMs Policy Coverage (Couverture de stratégie Azure Monitor pour machines virtuelles)** .

![Onglet Prise en main d’Azure Monitor pour machines virtuelles](./media/vminsights-enable-at-scale-policy/get-started-page.png)

À ce stade, vous pouvez vérifier et gérer la couverture pour l’initiative sur l’ensemble de vos groupes d’administration et abonnements. Vous pouvez comprendre combien de machines virtuelles existent dans chacun des groupes d’administration et des abonnements, ainsi que leur état de conformité.

![Page de gestion de stratégie d’Azure Monitor pour machines virtuelles](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Ces informations sont utiles pour vous aider à planifier et exécuter votre scénario de gouvernance pour Azure Monitor pour machines virtuelles à partir d’un emplacement central. Alors qu’Azure Policy fournit une vue de conformité lorsqu’une stratégie ou une initiative est affectée à une étendue, avec cette nouvelle page, vous pouvez découvrir où la stratégie ou l’initiative n’est pas affectée et l’y assigner. Toutes les actions comme l’affectation, l’affichage et la modification redirigent directement vers Azure Policy. La page **Azure Monitor for VMs Policy Coverage (Couverture de stratégie Azure Monitor pour machines virtuelles)** est une expérience étendue et intégrée pour l’initiative **Activer Azure Monitor pour machines virtuelles** uniquement.

À partir de cette page, vous pouvez également configurer votre espace de travail Log Analytics pour Azure Monitor pour machines virtuelles, qui :

- Installe la solution Service Map.
- Active les compteurs de performances de système d’exploitation utilisés par les graphiques de performances, les classeurs et vos alertes et requêtes de journal personnalisées.

![Espace de travail de configuration d’Azure Monitor pour machines virtuelles](media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Cette option n’est associée à aucune action de stratégie. Elle est disponible pour fournir un moyen simple de satisfaire les [conditions préalables](vminsights-enable-overview.md) nécessaires à l’activation d’Azure Monitor pour machines virtuelles.  

### <a name="what-information-is-available-on-this-page"></a>Quelles sont les informations disponibles sur cette page ?

Le tableau suivant fournit le détail des informations présentées sur la page de couverture de stratégie et la façon de les interpréter.

| Fonction | Description | 
|----------|-------------| 
| **Portée** | Groupe d’administration et abonnements auxquels vous avez accès ou vous avez hérité l’accès avec la possibilité de descendre dans la hiérarchie du groupe d’administration.|
| **Rôle** | Votre rôle dans l’étendue, qui peut être lecteur, propriétaire ou contributeur. Dans certains cas, il peut apparaître vide pour indiquer que vous pouvez avoir accès à l’abonnement, mais pas au groupe d’administration auquel il appartient. Les informations figurant dans les autres colonnes varient en fonction de votre rôle. Le rôle est la clé permettant de déterminer les données que vous pouvez voir et les actions que vous pouvez effectuer quant à l’affectation des stratégies ou des initiatives (propriétaire), leur modification ou l’affichage de la conformité. |
| **Nombre total de machines virtuelles** | Nombre de machines virtuelles figurant dans cette étendue. Pour un groupe d’administration, il s’agit de la somme des machines virtuelles imbriquées sous les abonnements ou le groupe d’administration enfant. |
| **Couverture d’attribution** | Pourcentage de machines virtuelles qui sont couvertes par la stratégie ou l’initiative. |
| **État de l’attribution** | Informations sur l’état de l’attribution de votre stratégie ou initiative. |
| **Machines virtuelles conformes** | Nombre de machines virtuelles conformes à la stratégie ou à l’initiative. Pour l’initiative **Activer Azure Monitor pour machines virtuelles**, il s’agit du nombre de machines virtuelles qui ont à la fois l’agent Log Analytics et l’agent de dépendances. Dans certains cas, il peut être vide en l’absence d’attributions, de machines virtuelles ou d’un nombre d’autorisations suffisant. Des informations sont fournies sous **État de conformité**. |
| **Conformité** | La conformité globale est la somme des ressources distinctes conformes divisée par la somme de toutes les ressources distinctes. |
| **État de conformité** | Informations sur l’état de conformité de votre attribution de stratégie ou d’initiative.|

Lorsque vous attribuez la stratégie ou l’initiative, l’étendue sélectionnée dans l’attribution peut être l’étendue répertoriée ou un sous-ensemble de celle-ci. Par exemple, vous avez peut-être créé une attribution pour un abonnement (étendue de la stratégie) et non pas un groupe d’administration (étendue de la couverture). Dans ce cas, la valeur de **Couverture d’attribution** indique les machines virtuelles dans l’étendue de stratégie ou d’initiative divisée par les machines virtuelles dans l’étendue de couverture. Dans un autre cas, vous pouvez avoir exclu des machines virtuelles, des groupes de ressources ou un abonnement de l’étendue de stratégie. Si la valeur est vide, elle indique que la stratégie ou l’initiative n’existe pas ou que vous n’avez pas l’autorisation. Des informations sont fournies sous **État de l’attribution**.

## <a name="enable-by-using-azure-policy"></a>Activer à l’aide d’Azure Policy

Pour activer Azure Monitor pour machines virtuelles à l’aide d’Azure Policy dans votre tenant :

- Attribuez l’initiative à une étendue : groupe d’administration, abonnement ou groupe de ressources.
- Examinez et corrigez les résultats de conformité.

Pour plus d’informations sur l’affectation d’Azure Policy, consultez [Vue d’ensemble d’Azure Policy](../../governance/policy/overview.md#assignments) et examinez la [présentation des groupes d’administration](../../governance/management-groups/overview.md) avant de continuer.

### <a name="policies-for-azure-vms"></a>Stratégies pour les machines virtuelles Azure

Les définitions de stratégie pour une machine virtuelle Azure sont listées dans le tableau ci-dessous.

|Nom |Description |Type |
|-----|------------|-----|
|Activer Azure Monitor pour machines virtuelles |Activez Azure Monitor pour machines virtuelles dans l’étendue spécifiée (groupe d’administration, abonnement ou groupe de ressources). Utilise l’espace de travail Log Analytics comme paramètre. |Initiative |
|Vérifier le déploiement de l’agent de dépendances - Image de machine virtuelle (système d’exploitation) non listée |Présente les machines virtuelles comme non conformes si l’image de machine virtuelle (système d’exploitation) n’est pas définie dans la liste et si l’agent n’est pas installé. |Stratégie |
|Auditer le déploiement de l’agent Log Analytics - Image de machine virtuelle (système d’exploitation) non listée |Présente les machines virtuelles comme non conformes si l’image de machine virtuelle (système d’exploitation) n’est pas définie dans la liste et si l’agent n’est pas installé. |Stratégie |
|Déployer l’agent de dépendances pour les machines virtuelles Linux |Déployez l’agent de dépendances pour les machines virtuelles Linux si l'image de machine virtuelle (système d’exploitation) est définie dans la liste et que l'agent n'est pas installé. |Stratégie |
|Déployer l’agent de dépendances pour les machines virtuelles Windows |Déployez l’agent de dépendances pour les machines virtuelles Windows si l’image de machine virtuelle (système d’exploitation) est définie dans la liste et si l’agent n’est pas installé. |Stratégie |
|Déployer l’agent Log Analytics pour les machines virtuelles Linux |Déployez l’agent Log Analytics pour les machines virtuelles Linux si l’image de machine virtuelle (système d’exploitation) est définie dans la liste et si l’agent n’est pas installé. |Stratégie |
|Déployer l’agent Log Analytics pour les machines virtuelles Windows |Déployez l’agent Log Analytics pour les machines virtuelles Windows si l’image de machine virtuelle (système d’exploitation) est définie dans la liste et si l’agent n’est pas installé. |Stratégie |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Stratégies pour les groupes de machines virtuelles identiques Azure

Les définitions de stratégie pour un groupe de machines virtuelles identiques Azure sont listées dans le tableau ci-dessous.

|Nom |Description |Type |
|-----|------------|-----|
|Activer Azure Monitor pour les groupes de machines virtuelles identiques |Activez Azure Monitor pour les groupes de machines virtuelles identiques dans l’étendue spécifiée (groupe d’administration, abonnement ou groupe de ressources). Utilise l’espace de travail Log Analytics comme paramètre. Remarque : Si la stratégie de mise à niveau du groupe identique est définie sur Manuelle, appliquez l’extension à toutes les machines virtuelles du groupe en appelant une mise à niveau. Dans l’interface de ligne de commande, il s’agit de `az vmss update-instances`. |Initiative |
|Auditer le déploiement de l’agent de dépendances dans des groupes de machines virtuelles identiques - Image de machine virtuelle (système d’exploitation) non listée |Présente le groupe de machines virtuelles identiques comme non conforme si l’image de machine virtuelle (système d’exploitation) n’est pas définie dans la liste et si l’agent n’est pas installé. |Stratégie |
|Auditer le déploiement de l’agent Log Analytics dans des groupes de machines virtuelles identiques - Image de machine virtuelle (système d’exploitation) non listée |Présente le groupe de machines virtuelles identiques comme non conforme si l’image de machine virtuelle (système d’exploitation) n’est pas définie dans la liste et si l’agent n’est pas installé. |Stratégie |
|Déployer l’agent de dépendances pour les groupes de machines virtuelles identiques Linux |Déployez l’agent de dépendances pour les groupes de machines virtuelles identiques Linux si l’image de machine virtuelle (système d’exploitation) est définie dans la liste et si l’agent n’est pas installé. |Stratégie |
|Déployer l’agent de dépendances pour les groupes de machines virtuelles identiques Windows |Déployez l’agent de dépendances pour les groupes de machines virtuelles identiques Linux si l’image de machine virtuelle (système d’exploitation) est définie dans la liste et si l’agent n’est pas installé. |Stratégie |
|Déployer l’agent Log Analytics pour les groupes de machines virtuelles identiques Linux |Déployez l’agent Log Analytics pour les groupes de machines virtuelles identiques Linux si l’image de machine virtuelle (système d’exploitation) est définie dans la liste et si l’agent n’est pas installé. |Stratégie |
|Déployer l’agent Log Analytics pour les groupes de machines virtuelles identiques Windows |Déployez l’agent Log Analytics pour les groupes de machines virtuelles identiques Windows si l’image de machine virtuelle (système d’exploitation) est définie dans la liste et si l’agent n’est pas installé. |Stratégie |

La stratégie autonome (non incluse avec l’initiative) est décrite ici :

|Nom |Description |Type |
|-----|------------|-----|
|Auditer les machines virtuelles de l’espace de travail Log Analytics - Signaler les incompatibilités |Signalez les machines virtuelles comme non conformes si elles ne se connectent pas à l’espace de travail Log Analytics spécifié dans l’attribution de stratégie ou d’initiative. |Stratégie |

### <a name="assign-the-azure-monitor-initiative"></a>Assigner l’initiative Azure Monitor

Pour créer l’attribution de stratégie à partir de la page **Azure Monitor for VMs Policy Coverage (Couverture de stratégie Azure Monitor pour machines virtuelles)** , procédez comme suit. Pour savoir comment effectuer ces étapes, consultez  [Créer une affectation de stratégie à partir du portail Azure](../../governance/policy/assign-policy-portal.md).

Lorsque vous attribuez la stratégie ou l’initiative, l’étendue sélectionnée dans l’attribution peut être l’étendue répertoriée ici ou un sous-ensemble de celle-ci. Par exemple, vous avez peut-être créé une attribution pour l’abonnement (étendue de la stratégie) et non pas le groupe d’administration (étendue de la couverture). Dans ce cas, le pourcentage de couverture indique les machines virtuelles dans l’étendue de stratégie ou d’initiative divisée par les machines virtuelles dans l’étendue de couverture. Dans un autre cas, vous pouvez avoir exclu des machines virtuelles, des groupes de ressources ou un abonnement de l’étendue de stratégie. Si la valeur est vide, elle indique que la stratégie ou l’initiative n’existe pas ou que vous n’avez pas d’autorisation. Des informations sont fournies sous **État de l’attribution**.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le Portail Azure, sélectionnez **Surveiller**. 

3. Choisissez **Machines virtuelles** dans la section **Insights**.
 
4. Sélectionnez l’onglet **Prise en main**. Dans la page, sélectionnez **Gérer la couverture de stratégie**.

5. Sélectionnez un groupe d’administration ou un abonnement à partir du tableau. Sélectionnez **Étendue** en sélectionnant les points de suspension (...). Dans cet exemple, une étendue limite l’attribution de stratégie à un regroupement de machines virtuelles pour leur application.

6. Dans la page **Attribution de stratégie Azure**, elle est préremplie avec l’initiative **Activer Azure Monitor pour machines virtuelles**. 
    La zone **Nom de l’attribution** est automatiquement remplie avec le nom d’initiative, mais vous pouvez le modifier. Vous pouvez également ajouter une description facultative. La zone **Attribué par** est rempli automatiquement en fonction de l’utilisateur connecté. Cette valeur est facultative.

7. (Facultatif) Pour supprimer une ou plusieurs ressources à partir de l’étendue, sélectionnez **Exclusions**.

8. Dans la liste déroulante **Espace de travail Log Analytics** de la région prise en charge, sélectionnez un espace de travail.

   > [!NOTE]
   > Si l’espace de travail se trouve en dehors de l’étendue de l’affectation, accordez des autorisations de *Contributeur Log Analytics* à l’ID du principal de l’affectation de stratégie. Si vous ne le faites pas, vous risquez d’obtenir un message d’échec de déploiement du type `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` Pour accorder l’accès, consultez [Configurer manuellement l’identité managée](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  La case à cocher **Identité managée** est sélectionnée, car l’initiative en cours d’attribution inclut une stratégie avec l’effet *deployIfNotExists*.
    
9. Dans la liste déroulante **Gérer l’emplacement d’identité**, sélectionnez la région appropriée.

10. Sélectionnez **Attribuer**.

Une fois que vous avez créé l’attribution, la page **Azure Monitor for VMs Policy Coverage (Couverture de stratégie Azure Monitor pour machines virtuelles)** met à jour **Couverture d’attribution**, **État de l’attribution**, **Machines virtuelles conformes** et **État de conformité** pour refléter les modifications. 

La matrice suivante mappe chaque état de conformité possible pour l’initiative.  

| État de conformité | Description | 
|------------------|-------------|
| **Conforme** | L’agent Log Analytics et l’agent de dépendances sont déployés sur toutes les machines virtuelles dans l’étendue.|
| **Non conforme** | L’agent Log Analytics et l’agent de dépendances ne sont pas déployés sur toutes les machines virtuelles dans l’étendue et certaines machines virtuelles peuvent nécessiter une correction.|
| **Non démarré** | Une nouvelle attribution a été ajoutée. |
| **Verrouiller** | Vous ne disposez pas de privilèges suffisants sur le groupe d'administration.<sup>1</sup> | 
| **Vide** | Aucune stratégie n’est attribuée. | 

<sup>1</sup> Si vous n’avez pas accès au groupe d’administration, demandez à un propriétaire de vous fournir l’accès. Ou, affichez la conformité et gérez les attributions via les abonnements ou les groupes d’administration enfants. 

Le tableau suivant mappe chaque état d’attribution possible pour l’initiative.

| État de l’attribution | Description | 
|------------------|-------------|
| **Success** | L’agent Log Analytics et l’agent de dépendances sont déployés sur toutes les machines virtuelles dans l’étendue.|
| **Avertissement** | L’abonnement n’est pas sous un groupe d’administration.|
| **Non démarré** | Une nouvelle attribution a été ajoutée. |
| **Verrouiller** | Vous ne disposez pas de privilèges suffisants sur le groupe d'administration.<sup>1</sup> | 
| **Vide** | Aucune machine virtuelle n’existe ou une stratégie n’est pas attribuée. | 
| **Action** | Attribuez une stratégie ou modifiez une attribution. | 

<sup>1</sup> Si vous n’avez pas accès au groupe d’administration, demandez à un propriétaire de vous fournir l’accès. Ou, affichez la conformité et gérez les attributions via les abonnements ou les groupes d’administration enfants.

## <a name="review-and-remediate-the-compliance-results"></a>Examiner et corriger les résultats de conformité

L’exemple suivant est pour une machine virtuelle Azure, mais il s’applique également aux groupes de machines virtuelles identiques. Pour savoir comment examiner les résultats de conformité, consultez [Identifier les résultats non conformes](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). Sur la page **Azure Monitor for VMs Policy Coverage (Couverture de stratégie Azure Monitor pour machines virtuelles)** , sélectionnez un groupe d’administration ou un abonnement à partir du tableau. Sélectionnez **Afficher la conformité** en sélectionnant les points de suspension (...).   

![Conformité de stratégie pour les machines virtuelles Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)

Sur la base des résultats des stratégies incluses avec l’initiative, les machines virtuelles sont présentées comme étant non conformes dans les scénarios suivants :

* L’agent Log Analytics ou l’agent de dépendances n’est pas déployé.  
    Il s’agit d’un cas courant pour une étendue comportant des machines virtuelles existantes. Pour limiter ce risque, déployez les agents requis en [créant des tâches de correction](../../governance/policy/how-to/remediate-resources.md) sur une stratégie non conforme.  
    - Déployer l’agent de dépendances pour les machines virtuelles Linux
    - Déployer l’agent de dépendances pour les machines virtuelles Windows
    - Déployer l’agent Log Analytics pour les machines virtuelles Linux
    - Déployer l’agent Log Analytics pour les machines virtuelles Windows

* L’image de machine virtuelle (système d’exploitation) n’est pas identifiée dans la définition de stratégie.  
    Les critères de la stratégie de déploiement incluent uniquement les machines virtuelles qui sont déployées à partir d’images de machine virtuelle Azure connues. Consultez la documentation pour savoir si le système d’exploitation de la machine virtuelle est pris en charge. Si ce n’est pas le cas, dupliquez la stratégie de déploiement et mettez-la à jour ou modifiez-la pour rendre l’image conforme.  
    - Vérifier le déploiement de l’agent de dépendances - Image de machine virtuelle (système d’exploitation) non listée
    - Auditer le déploiement de l’agent Log Analytics - Image de machine virtuelle (système d’exploitation) non listée

* Les machines virtuelles ne sont pas connectées à l’espace de travail Log Analytics spécifié.  
    Il est possible que certaines machines virtuelles de l’étendue de l’initiative soient connectées à un espace de travail Log Analytics différent de celui spécifié dans l’affectation de stratégie. Cette stratégie est un outil qui permet d’identifier les machines virtuelles qui relèvent d’un espace de travail non conforme.  
    - Auditer les machines virtuelles de l’espace de travail Log Analytics - Signaler les incompatibilités

## <a name="edit-an-initiative-assignment"></a>Modifier une attribution d’initiative

À tout moment après avoir attribué une initiative à un groupe d’administration ou un abonnement, vous pouvez en modifier les propriétés suivantes :

- Nom de l’attribution
- Description
- Attribué par
- Espace de travail Log Analytics
- Exceptions

## <a name="next-steps"></a>Étapes suivantes

Une fois la supervision activée pour vos machines virtuelles, ces informations peuvent être analysées par Azure Monitor pour machines virtuelles. 

- Pour afficher les dépendances des applications détectées, consultez [Utilisation de la fonctionnalité Map d’Azure Monitor pour machines virtuelles dans le but de comprendre les composants d’application](vminsights-maps.md). 

- Pour identifier les goulots d’étranglement et l’utilisation globale avec les performances de votre machine virtuelle, consultez [Consulter les performances des machines virtuelles Azure](vminsights-performance.md). 
