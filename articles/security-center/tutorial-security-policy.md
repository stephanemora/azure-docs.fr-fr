---
title: Utilisation de stratégies de sécurité | Microsoft Docs
description: Cet article décrit comment utiliser des stratégies de sécurité dans Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/18/2019
ms.author: v-mohabe
ms.openlocfilehash: 1820c31053c13a0f3039812b31df415bc9fa4fdc
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910374"
---
# <a name="working-with-security-policies"></a>Utilisation de stratégies de sécurité

Cet article explique comment configurer des stratégies de sécurité et comment les afficher dans Security Center. Azure Security Center attribue automatiquement ses [stratégies de sécurité intégrées](security-center-policy-definitions.md) sur chaque abonnement qui est intégré. Vous pouvez les configurer dans [Azure Policy](../governance/policy/overview.md), ce qui vous permet également de définir des stratégies pour plusieurs groupes d’administration et abonnements.

Pour obtenir des instructions sur la façon de définir des stratégies à l’aide de PowerShell, consultez [Démarrage rapide : Créer une affectation de stratégie pour identifier les ressources non conformes à l’aide du module Azure PowerShell](../governance/policy/assign-policy-powershell.md).

>[!NOTE]
> L'intégration de Security Center avec Azure Policy a débuté. Les clients existants migreront automatiquement vers la nouvelle initiative intégrée dans Azure Policy, plutôt que vers les stratégies de sécurité précédentes dans Security Center. Cette modification n’affectera ni vos ressources ni votre environnement, exception faite de la présence de la nouvelle initiative dans Azure Policy.

## <a name="what-are-security-policies"></a>Que sont les stratégies de sécurité ?
Une stratégie de sécurité définit la configuration souhaitée de vos charges de travail, tout en garantissant leur conformité aux exigences de sécurité réglementaires. Dans Azure Policy, vous pouvez définir des stratégies pour vos abonnements Azure, et les adapter à votre type de charge de travail ou à la sensibilité de vos données. Par exemple, les applications qui utilisent des données réglementées, telles que les données personnelles ou client, peuvent nécessiter un niveau de sécurité plus élevé que d’autres charges de travail. Pour définir des stratégies sur des abonnements ou des groupes d’administration, configurez-les dans [Azure Policy](../governance/policy/overview.md).

Vos stratégies de sécurité sont à la source des suggestions de sécurité que vous obtenez dans Azure Security Center. Vous pouvez surveiller la conformité avec elles pour mieux identifier les vulnérabilités potentielles et atténuer les menaces. Pour plus d’informations sur la façon de déterminer l’option adaptée à votre situation, consultez la liste des [stratégies de sécurité intégrées](security-center-policy-definitions.md).

Lorsque vous activez Security Center, la stratégie de sécurité intégrée à Security Center est reflétée dans Azure Policy en tant qu’initiative intégrée sous la catégorie Security Center. L’initiative intégrée est automatiquement affectée à tous les abonnements Security Center inscrits (niveaux Gratuit ou Standard). L’initiative intégrée contient uniquement les stratégies d’audit.


### <a name="management-groups"></a>Groupes d’administration
Si votre organisation dispose de plusieurs abonnements, vous pouvez avoir besoin d’un moyen de gérer efficacement l’accès, les stratégies et la conformité de ces abonnements. Les groupes d’administration Azure fournissent un niveau d’étendue au-dessus des abonnements. Vous organisez les abonnements en conteneurs appelés « groupes d’administration » et vous appliquez vos stratégies de gouvernance aux groupes d’administration. Tous les abonnements d’un groupe d’administration héritent automatiquement des stratégies appliquées à ce groupe d’administration. Chaque annuaire reçoit un groupe d’administration de niveau supérieur unique appelé groupe d’administration « racine ». Ce groupe d’administration racine est intégré à la hiérarchie et contient tous les groupes d’administration et abonnements. Il permet d’appliquer des stratégies globales et des affectations RBAC au niveau de l’annuaire. Pour configurer des groupes d’administration à utiliser avec Azure Security Center, suivez les instructions fournies dans [Gagner en visibilité au niveau locataire dans Azure Security Center](security-center-management-groups.md).

> [!NOTE]
> Il est important de bien comprendre la hiérarchie des groupes d’administration et des abonnements. Pour en savoir plus sur les groupes d’administration, l’administration racine et l’accès aux groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](../governance/management-groups/index.md#root-management-group-for-each-directory).
>

## <a name="how-security-policies-work"></a>Fonctionnement des stratégies de sécurité
Security Center crée automatiquement une stratégie de sécurité par défaut, pour chacun de vos abonnements Azure. Vous pouvez modifier les stratégies dans Azure Policy pour effectuer les opérations suivantes :
- Créer de nouvelles définitions de stratégie.
- Affecter des stratégies pour des groupes d’administration et des abonnements, qui peuvent représenter une organisation entière ou une unité commerciale au sein de l’organisation.
- Surveiller la conformité à la stratégie.

Pour plus d’informations sur Azure Policy, consultez [Créer et gérer des stratégies pour mettre en vigueur la conformité](../governance/policy/tutorials/create-and-manage.md).

Une stratégie Azure est constituée des composants suivants :

- Une **stratégie** est une règle.
- Une **initiative** est une collection de stratégies.
- Une **affectation** est l’application d’une initiative ou d’une stratégie à une étendue spécifique (groupe d’administration, abonnement ou groupe de ressources).

## <a name="view-security-policies"></a>Afficher les stratégies de sécurité

Pour afficher vos stratégies de sécurité dans Security Center :

1. Dans le tableau de bord **Security Center**, sélectionnez **Stratégie de sécurité**.

    ![Le volet Gestion des stratégies](./media/security-center-policies/security-center-policy-mgt.png)

   Dans l’écran **Gestion de stratégie**, vous pouvez voir le nombre de groupes d’administration, d’abonnements et d’espaces de travail, ainsi que votre structure de groupes d’administration.

   > [!NOTE]
   > Le tableau de bord Security Center peut afficher sous **Couverture de l’abonnement** un nombre d’abonnements plus élevé que celui indiqué sous **Gestion de stratégie**. Couverture de l’abonnement montre le nombre d’abonnements Standard, Gratuits et « Non couverts ». Security Center n’est pas activé pour les abonnements « Non couverts », et ceux-ci ne sont pas affichés sous **Gestion de stratégie**.
   >

2. Sélectionnez le groupe d’administration ou l’abonnement dont vous souhaitez afficher les stratégies.

   - L’écran **Stratégie de sécurité** reflète l’action effectuée par les stratégies affectées sur le groupe d’administration ou l’abonnement que vous avez sélectionné.
   - En haut, utilisez les liens fournis pour ouvrir chaque **affectation** de stratégie qui s’applique au groupe d’administration ou à l’abonnement. Vous pouvez utiliser ces liens pour accéder à l’affectation et modifier ou désactiver la stratégie. Par exemple, si vous voyez qu’une affectation de stratégie particulière refuse effectivement la protection du point de terminaison, vous pouvez utiliser le lien pour accéder à la stratégie et la modifier ou la désactiver.
   - Dans la liste des stratégies, vous pouvez voir l’application effective de la stratégie sur votre groupe d’administration ou abonnement. Cela signifie que les paramètres de chaque stratégie qui s’appliquent à la portée sont pris en compte et que le résultat cumulé de l’action entreprise par la stratégie vous est fourni. Par exemple, si la stratégie est désactivée dans une affectation, mais qu’elle est définie sur AuditIfNotExist dans une autre, l’effet cumulatif applique AuditIfNotExist. L’effet le plus actif est toujours prioritaire.
   - L’effet des stratégies peut être : Append, Audit, AuditIfNotExists, Deny, DeployIfNotExists, Disabled. Pour plus d’informations sur la manière dont les effets sont appliqués, consultez [Comprendre les effets de Policy](../governance/policy/concepts/effects.md).

   ![Écran des stratégies](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> Lorsque vous affichez les stratégies attribuées, vous pouvez voir plusieurs affectations et comment chacune d’elles est configurée individuellement.

## <a name="edit-security-policies"></a>Modifier des stratégies de sécurité
Vous pouvez modifier la stratégie de sécurité par défaut pour chacun de vos groupes d’administration et abonnements Azure dans [Azure Policy](../governance/policy/tutorials/create-and-manage.md). Pour modifier une stratégie de sécurité, vous devez avoir le rôle de propriétaire ou d’administrateur de la sécurité pour l’abonnement concerné ou le groupe de gestion dans laquelle elle se trouve.

Pour obtenir des instructions sur la façon de modifier une stratégie de sécurité dans Azure Policy, consultez [Créer et gérer des stratégies pour appliquer la conformité](../governance/policy/tutorials/create-and-manage.md).

Vous pouvez modifier les stratégies de sécurité sur le portail Azure Policy, par l’intermédiaire de l’API REST ou à l’aide de Windows PowerShell. L’exemple suivant fournit des instructions pour la modification à l’aide de l’API REST.


## <a name="disable-security-policies"></a>Désactiver des stratégies de sécurité
Si la stratégie de sécurité par défaut génère une suggestion qui n’est pas adaptée à votre environnement, vous pouvez l’arrêter en désactivant la définition de stratégie qui envoie la suggestion.
Pour plus d’informations sur les recommandations, consultez [Gestion des recommandations de sécurité](security-center-recommendations.md).

1. Dans la section **Politique et conformité** de Security Center, cliquez sur **Stratégie de sécurité**.

   ![gestion des stratégies](./media/tutorial-security-policy/policy-management.png)

2. Cliquez sur le groupe d’abonnements ou d’administration pour lequel vous souhaitez désactiver la suggestion.

   > [!Note]
   > N’oubliez pas qu’un groupe d’administration applique ses stratégies à ses abonnements. Par conséquent, si vous désactivez la stratégie d’un abonnement, et que l’abonnement appartient à un groupe d’administration qui utilise toujours la même stratégie, vous continuerez à recevoir les suggestion de la stratégie. La stratégie sera toujours appliquée à partir du niveau d’administration, et les suggestions seront toujours générées.

1. Cliquez sur la stratégie affectée.

   ![désactiver une stratégie](./media/tutorial-security-policy/security-policy.png)

1. Dans la section **PARAMÈTRES**, recherchez la stratégie qui appelle la suggestion que vous souhaitez désactiver, puis sélectionnez **Désactiver** dans la liste déroulante.

   ![désactiver une stratégie](./media/tutorial-security-policy/disable-policy.png)
1. Cliquez sur **Enregistrer**.
   > [!Note]
   > La désactivation d’une stratégie peut prendre jusqu’à 12 heures pour entrer en vigueur.


### <a name="configure-a-security-policy-using-the-rest-api"></a>Configurer une stratégie de sécurité à l’aide de l’API REST

Dans le cadre de l’intégration native à Azure Policy, Azure Security Center vous permet de tirer parti de l’API REST d’Azure Policy pour créer des affectations de stratégie. Les instructions suivantes vous guident tout au long de la création d’affectations de stratégies, ainsi que de la personnalisation d’affectations existantes. 

Concepts importants utilisés dans Azure Policy : 

- Une **définition de stratégie** est une règle 

- Une **initiative** est une collection de définitions de stratégie (règles) 

- Une **affectation** est l’application d’une initiative ou d’une stratégie à une étendue spécifique (groupe d’administration, abonnement, etc.) 

Security Center dispose d’une initiative intégrée qui inclut toutes ses stratégies de sécurité. Afin d’évaluer les stratégies de Security Center sur vos ressources Azure, vous devez créer une affectation sur le groupe d’administration ou un abonnement que vous voulez évaluer.

L’initiative intégrée a toutes les stratégies de Security Center activées par défaut. Vous pouvez choisir de désactiver certaines stratégies de l’initiative intégrée. Par exemple, vous pouvez appliquer toutes les stratégies de Security Center à l’exception du **pare-feu d’applications web** en définissant la valeur du paramètre d’effet de la stratégie sur **Désactivé**. 

### <a name="api-examples"></a>Exemples d'API

Dans les exemples suivants, remplacez les variables suivantes :

- **{scope}** Entrez le nom du groupe d’administration ou de l’abonnement auquel vous appliquez la stratégie.
- **{policyAssignmentName}** Entrez le [nom de l’affectation de stratégie appropriée](#policy-names).
- **{name}** Entrez votre nom ou le nom de l’administrateur qui a approuvé le changement de stratégie.

Cet exemple vous montre comment affecter l’initiative Security Center intégrée sur un abonnement ou un groupe d’administration :
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

Cet exemple vous montre comment affecter l’initiative Security Center intégrée sur un abonnement, avec les stratégies suivantes désactivées : 

- Mises à jour système (“systemUpdatesMonitoringEffect”) 

- Configurations de sécurité (« systemConfigurationsMonitoringEffect ») 

- Protection du point de terminaison (« endpointProtectionMonitoringEffect ») 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
Cet exemple vous montre comment supprimer une affectation :
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

### Informations de référence sur les noms de stratégies <a name="policy-names"></a>

|Nom de stratégie dans Security Center|Nom de stratégie affiché dans Azure Policy |Nom du paramètre d’effet de la stratégie|
|----|----|----|
|Chiffrement SQL |Superviser les bases de données SQL non chiffrées dans Azure Security Center |sqlEncryptionMonitoringEffect| 
|Audit SQL |Superviser les bases de données SQL non auditées dans Azure Security Center |sqlAuditingMonitoringEffect|
|Mises à jour système |Superviser les mises à jour système manquantes dans Azure Security Center |systemUpdatesMonitoringEffect|
|Chiffrement du stockage |Auditer le chiffrement d’objet blob manquant pour les comptes de stockage |storageEncryptionMonitoringEffect|
|Accès réseau JIT |Superviser l’éventuel accès juste-à-temps (JIT) au réseau dans Azure Security Center |jitNetworkAccessMonitoringEffect |
|Contrôles d’application adaptative |Superviser la configuration éventuelle d’une liste verte d’applications dans Azure Security Center |adaptiveApplicationControlsMonitoringEffect|
|Groupes de sécurité réseau |Superviser l’accès réseau permissif dans Azure Security Center |networkSecurityGroupsMonitoringEffect| 
|Configurations de sécurité |Superviser les vulnérabilités du système d’exploitation dans Azure Security Center |systemConfigurationsMonitoringEffect| 
|Protection du point de terminaison |Superviser les agents Endpoint Protection manquants dans Azure Security Center |endpointProtectionMonitoringEffect |
|Chiffrement de disque |Superviser les disques de machine virtuelle non chiffrés dans Azure Security Center |diskEncryptionMonitoringEffect|
|Évaluation des vulnérabilités |Superviser les vulnérabilités de machine virtuelle dans Azure Security Center |vulnerabilityAssessmentMonitoringEffect|
|Pare-feu d’application web |Superviser les applications web non protégées dans Azure Security Center |webApplicationFirewallMonitoringEffect |
|Pare-feu de nouvelle génération |Superviser les points de terminaison réseau non protégés dans Azure Security Center| |


### <a name="who-can-edit-security-policies"></a>Qui peut modifier des stratégies de sécurité ?
Security Center utilise le contrôle d’accès en fonction du rôle (RBAC) qui fournit des rôles intégrés susceptibles d’être assignés à des utilisateurs, des groupes et des services dans Azure. Lorsque les utilisateurs ouvrent Security Center, ils ne voient que les informations associées aux ressources auxquelles ils ont accès. Autrement dit, les utilisateurs se voient assigner le rôle de propriétaire, contributeur ou lecteur, pour l’abonnement ou le groupe de ressources auquel appartiennent les ressources. Outre ces rôles, il existe deux rôles propres à Security Center :

- Lecteur Sécurité : l’utilisateur ayant ce rôle a des droits d’affichage dans Security Center. Il peut afficher les suggestions, les alertes, la stratégie et l’intégrité, mais il ne peut pas apporter de changements.
- Administrateur de la sécurité : l’utilisateur ayant ce rôle a les mêmes droits d’affichage que le lecteur Sécurité, mais il peut en plus mettre à jour la stratégie de sécurité, et ignorer les alertes et les suggestions.



## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à modifier les stratégies de sécurité dans Azure Policy. Pour plus d’informations sur Security Center, consultez les articles suivants :

* [Guide des opérations et de planification d’Azure Security Center](security-center-planning-and-operations-guide.md) : découvrez comment planifier la conception d’Azure Security Center et en étudier le processus.
* [Supervision de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gérer et résoudre les alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Superviser les solutions de partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
* [Gagner en visibilité à l’échelle du locataire dans Azure Security Center](security-center-management-groups.md) : découvrez comment configurer des groupes d’administration pour Azure Security Center.
* [FAQ d’Azure Security Center](security-center-faq.md) : Obtenez des réponses aux questions fréquentes concernant l’utilisation du service.
* [Blog sur la sécurité Azure](https://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.

Pour en savoir plus sur Azure, consultez [Présentation d’Azure Policy](../governance/policy/overview.md).
