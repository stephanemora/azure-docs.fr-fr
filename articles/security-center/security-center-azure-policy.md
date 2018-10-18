---
title: Définition de stratégies de sécurité Azure Security Center de manière individuelle ou dans le cadre de stratégies Azure Policy | Microsoft Docs
description: Ce document vous aide à définir des stratégies dans Azure Security Center ou dans Azure Policy.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/5/2018
ms.author: terrylan
ms.openlocfilehash: 3c198ea44953c0b2e72a544cd0e83b6592d9a81f
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47032070"
---
# <a name="setting-security-policies-in-security-center-or-in-azure-policy"></a>Définition de stratégies de sécurité dans Security Center ou dans Azure Policy

Cet article vous aide à configurer des stratégies de sécurité Azure Security Center. Les stratégies Azure Security Center s’intègrent avec les stratégies Azure Policy. Vous pouvez donc les définir soit dans Security Center sur un abonnement spécifique, soit dans [Azure Policy](../azure-policy/azure-policy-introduction.md), qui vous permet de définir des stratégies sur plusieurs groupes d’administration et plusieurs abonnements.

## <a name="what-are-security-policies"></a>Que sont les stratégies de sécurité ?
Une stratégie de sécurité définit la configuration souhaitée de vos charges de travail, tout en garantissant leur conformité aux exigences de sécurité réglementaires. Dans Azure Security Center, vous pouvez définir les stratégies de vos abonnements Azure, et les adapter à votre type de charge de travail ou à la sensibilité de vos données. Par exemple, les applications qui utilisent des données réglementées, telles que les informations d’identification personnelle, peuvent nécessiter un niveau de sécurité plus élevé que d’autres charges de travail. Pour définir des stratégies sur des abonnements ou des groupes d’administration, configurez-les dans [Azure Policy](../azure-policy/azure-policy-introduction.md).

> [!NOTE]
> Si vous avez préalablement configuré des stratégies de sécurité sur un abonnement qui fait partie d’un groupe d’administration, ou qui a plusieurs affectations de stratégies, ces stratégies apparaissent grisées dans Security Center afin que vous puissiez gérer la stratégie au niveau du groupe d’administration à partir de la page Azure Policy. 

## <a name="how-security-policies-work"></a>Fonctionnement des stratégies de sécurité
Security Center crée automatiquement une stratégie de sécurité par défaut, pour chacun de vos abonnements Azure. Vous pouvez modifier les stratégies dans Security Center ou utiliser Azure Policy pour effectuer les opérations suivantes :
- Créer de nouvelles définitions de stratégie.
- Affecter des stratégies pour des groupes d’administration et des abonnements, qui peuvent représenter une organisation entière ou une unité commerciale au sein de l’organisation.
- Surveiller la conformité à la stratégie.

Pour plus d’informations sur Azure Policy, consultez [Créer et gérer des stratégies pour mettre en vigueur la conformité](../azure-policy/create-manage-policy.md).

Une stratégie Azure est constituée des composants suivants :

- Une **stratégie** est une règle
- Une **initiative** est une collection de stratégies
- Une **affectation** est une application d’une initiative ou d’une stratégie à une étendue spécifique (groupe d’administration, abonnement ou groupe de ressources)

Une ressource est évaluée par rapport aux stratégies qui lui sont affectées, et reçoit un taux de conformité en fonction du nombre de stratégies avec lesquelles la ressource est conforme.

## <a name="who-can-edit-security-policies"></a>Qui peut modifier des stratégies de sécurité ?
Security Center utilise le contrôle d’accès en fonction du rôle (RBAC) qui fournit des rôles intégrés susceptibles d’être assignés à des utilisateurs, des groupes et des services dans Azure. Lorsque les utilisateurs ouvrent Security Center, ils ne voient que les informations associées aux ressources auxquelles ils ont accès. Autrement dit, les utilisateurs se voient assigner le rôle de propriétaire, contributeur ou lecteur, pour l’abonnement ou le groupe de ressources auquel appartiennent les ressources. Outre ces rôles, il existe deux rôles propres à Security Center :

- Lecteur Sécurité : l’utilisateur ayant ce rôle a des droits d’affichage dans Security Center. Il peut afficher les suggestions, les alertes, la stratégie et l’intégrité, mais il ne peut pas apporter de modifications.
- Administrateur Sécurité : l’utilisateur ayant ce rôle a les mêmes droits d’affichage que le lecteur Sécurité, mais il peut en plus mettre à jour la stratégie de sécurité, et ignorer les alertes et les suggestions.

## <a name="edit-security-policies"></a>Modifier des stratégies de sécurité
Vous pouvez modifier la stratégie de sécurité par défaut pour chacun de vos groupes d’administration et abonnements Azure dans Security Center. Pour modifier une stratégie de sécurité, vous devez avoir le rôle d’administrateur de la sécurité, de propriétaire ou de collaborateur pour l’abonnement concerné ou le groupe de gestion dans laquelle elle se trouve. Pour afficher vos stratégies de sécurité dans Security Center :

> [!NOTE]
> Les stratégies définies sur un abonnement qui fait partie d’un groupe d’administration, ou qui a plusieurs affectations de stratégies, apparaissent grisées dans Security Center. Vous pouvez modifier ces stratégies dans [Azure Policy](../azure-policy/azure-policy-introduction.md). 

1. Dans le tableau de bord **Security Center**, sous **STRATÉGIE ET CONFORMITÉ**, sélectionnez **Stratégie de sécurité**. **Gestion de stratégie** s’ouvre.

    ![Le volet Gestion des stratégies](./media/security-center-azure-policy/security-center-policies-fig10.png)

  Gestion de stratégie affiche le nombre de groupes d’administration, d’abonnements et d’espaces de travail, ainsi que votre structure de groupes d’administration.

  > [!NOTE]
  > Le tableau de bord Security Center peut afficher sous **Couverture de l’abonnement** un nombre d’abonnements plus élevé que celui indiqué sous **Gestion de stratégie**. Couverture de l’abonnement montre le nombre d’abonnements Standard, Gratuits et « Non couverts ». Security Center n’est pas activé pour les abonnements « Non couverts », et ceux-ci ne sont pas affichés sous **Gestion de stratégie**.
  >
  >

  Le tableau contient les colonnes suivantes :

 - Affectation d’initiative de stratégie – Stratégies et initiatives intégrées Security Center qui sont affectées à un groupe d’administration ou un abonnement.
 - Conformité – Score général de conformité pour un groupe d’administration, un abonnement ou un espace de travail. Le score est la moyenne pondérée des affectations. La moyenne pondérée prend en compte le nombre de stratégies dans une seule affectation et le nombre de ressources auxquelles l’affectation s’applique.

 Par exemple, si votre abonnement a deux machines virtuelles et qu’une initiative avec cinq stratégies lui est affectée, vous avez 10 évaluations dans votre abonnement. Si l’une des machines virtuelles n’est pas conforme à deux des stratégies, le score général de conformité de l’affectation de votre abonnement est de 80 %.

 - Couverture – Identifie le niveau tarifaire (Gratuit ou Standard) sur lequel s’exécute le groupe d’administration, l’abonnement ou l’espace de travail.  Consultez [Tarification](security-center-pricing.md) pour en savoir plus sur les niveaux tarifaires de Security Center.
 - Paramètres – Les abonnements disposent du lien **Modifier les paramètres**. Sélectionner **Modifier les paramètres** vous permet de mettre à jour vos paramètres d’abonnement tels que la collecte de données, le niveau tarifaire et les notifications par e-mail.

2. Sélectionnez l’abonnement ou le groupe d’administration pour lequel vous souhaitez activer une stratégie de sécurité. La fenêtre **Stratégie de sécurité** s’ouvre.

3.  Sous **Stratégie de sécurité**, sélectionnez les contrôles que vous souhaitez que Security Center surveille et pour lesquels il doit fournir des recommandations en sélectionnant **Activé**.  Sélectionnez **Désactivé** si vous ne souhaitez pas que Security Center surveille ce contrôle.

    ![Composants de la stratégie](./media/security-center-azure-policy/security-policy.png)

4. Sélectionnez **Enregistrer**.

## <a name="available-security-policy-definitions"></a>Définitions de stratégie de sécurité disponibles

Référez-vous au tableau suivant afin de comprendre les définitions de stratégies qui sont disponibles dans la stratégie de sécurité par défaut :

| Stratégie | Ce que fait la stratégie activée |
| --- | --- |
| Mises à jour système |Une liste quotidienne des mises à jour de sécurité et critiques disponibles est récupérée à partir de Windows Update ou de Windows Server Update Services. La liste récupérée dépend du service configuré pour vos machines virtuelles et recommande d’appliquer les mises à jour manquantes. Pour les systèmes Linux, la stratégie utilise le système de gestion des packages fournis par un distributeur afin de déterminer pour quels packages des mises à jour sont disponibles. Elle vérifie également la disponibilité des mises à jour de sécurité et critiques à partir des machines virtuelles des [Services cloud Azure](../cloud-services/cloud-services-how-to-configure-portal.md). |
| Configurations de sécurité |Analyse les configurations de système d’exploitation tous les jours afin d’identifier les problèmes susceptibles de rendre la machine virtuelle vulnérable aux attaques. Cette stratégie recommande également des modifications de configuration pour résoudre ces vulnérabilités. Consultez la [liste des règles de base recommandées](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) pour plus d’informations sur les configurations spécifiques surveillées. (À ce stade, Windows Server 2016 n’est pas entièrement pris en charge.) |
| Protection du point de terminaison |Recommande la configuration de la protection du point de terminaison pour toutes les machines virtuelles (VM) Windows afin d’identifier et de supprimer les virus, les logiciels espions et autres logiciels malveillants. |
| Chiffrement de disque |Recommande d’activer le chiffrement de disque dans toutes les machines virtuelles pour améliorer la protection des données au repos. |
| Groupes de sécurité réseau |Recommande la configuration de [Groupes de sécurité réseau](../virtual-network/security-overview.md) pour contrôler le trafic entrant et sortant vers les machines virtuelles dotées de points de terminaison publics. Les groupes de sécurité réseau configurés pour un sous-réseau sont hérités par toutes les interfaces réseau de machine virtuelle, sauf indication contraire. En plus de vérifier si un groupe de sécurité réseau a été configuré, cette stratégie évalue les règles de sécurité de trafic entrant afin d’identifier les règles autorisant le trafic entrant. |
| Pare-feu d’application web |Recommande la configuration d’un pare-feu d’applications web sur les machines virtuelles quand l’une des conditions suivantes est remplie : <ul><li>Une [adresse IP publique de niveau d’instance](../virtual-network/virtual-networks-instance-level-public-ip.md) est utilisée et les règles de sécurité entrantes pour le groupe de sécurité réseau associé sont configurées pour autoriser l’accès au port 80/443.</li><li>Une adresse IP à équilibrage de charge est utilisée et les règles associées d’équilibrage de charge et NAT (Network Access Translation) de trafic entrant sont configurées pour autoriser l’accès au port 80 ou 443. Pour plus d’informations, consultez [Prise en charge d’un équilibreur de charge par Azure Resource Manager](../load-balancer/load-balancer-arm.md).</li> |
| Pare-feu de nouvelle génération |Étend les protections du réseau au-delà des groupes de sécurité réseau intégrés à Azure. Security Center détecte les déploiements pour lesquels un pare-feu de nouvelle génération est recommandé et vous permet ensuite de configurer une appliance virtuelle. |
| Audit SQL et détection des menaces |Recommande l’activation de l’audit de l’accès à Azure SQL Database à des fins de conformité et de détection avancée des menaces et d’examen. |
| Chiffrement SQL |Recommande l’activation du chiffrement au repos pour votre base de données Azure SQL Database, ainsi que pour les sauvegardes associées et les fichiers journaux de transaction. Même si vos données font l’objet d’une violation de sécurité, elles ne sont pas lisibles. |
| Évaluation des vulnérabilités |Recommande d’installer une solution d’évaluation des vulnérabilités sur votre machine virtuelle. |
| Chiffrement du stockage |Cette fonctionnalité est actuellement disponible pour le stockage d’objets blob et les fichiers Azure. Après l’activation du chiffrement de service de stockage, seules les nouvelles données sont chiffrées et tous les fichiers existants dans ce compte de stockage restent non chiffrés. |
| Accès réseau JIT |Lorsque la fonctionnalité d’accès réseau Juste à temps est activée, Security Center verrouille le trafic entrant vers vos machines virtuelles Azure en créant une règle de groupe de sécurité réseau. Vous sélectionnez les ports de la machine virtuelle pour lesquels le trafic entrant doit être verrouillé. Pour plus d’informations, consultez [Gérer l’accès Juste à temps à la machine virtuelle](https://docs.microsoft.com/azure/security-center/security-center-just-in-time). |

## <a name="management-groups"></a>Groupes d’administration
Si votre organisation dispose de plusieurs abonnements, vous pouvez avoir besoin d’un moyen de gérer efficacement l’accès, les stratégies et la conformité de ces abonnements. Les groupes d’administration Azure fournissent un niveau d’étendue au-dessus des abonnements. Vous organisez les abonnements en conteneurs appelés « groupes d’administration » et vous appliquez vos stratégies de gouvernance aux groupes d’administration. Tous les abonnements d’un groupe d’administration héritent automatiquement des stratégies appliquées à ce groupe d’administration. Chaque annuaire reçoit un groupe d’administration de niveau supérieur unique appelé groupe d’administration « racine ». Ce groupe d’administration racine est intégré à la hiérarchie et contient tous les groupes d’administration et abonnements. Il permet d’appliquer des stratégies globales et des affectations RBAC au niveau de l’annuaire. Pour configurer des groupes d’administration pour une utilisation avec Azure Security Center, suivez les instructions de l’article [Gagner en visibilité au niveau locataire dans Azure Security Center](security-center-management-groups.md). 

> [!NOTE]
> Il est important de bien comprendre la hiérarchie des groupes d’administration et des abonnements. Pour en savoir plus sur les groupes d’administration, l’administration racine et l’accès aux groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](../governance/management-groups/index.md#root-management-group-for-each-directory).
>
>


## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à configurer des stratégies de sécurité dans le Security Center. Pour plus d’informations sur Security Center, consultez les articles suivants :

* [Guide des opérations et de planification d’Azure Security Center](security-center-planning-and-operations-guide.md) : découvrez comment planifier l’adoption d’Azure Security Center et prenez connaissance des considérations relatives à la conception.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaire avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
* [Gagner en visibilité au niveau locataire dans Azure Security Center](security-center-management-groups.md) : Découvrez comment configurer des groupes d’administration pour Azure Security Center. 
* [FAQ Azure Security Center](security-center-faq.md) : obtenez des réponses aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.

Pour en savoir plus sur Azure, consultez [Présentation d’Azure Policy](../azure-policy/azure-policy-introduction.md).
