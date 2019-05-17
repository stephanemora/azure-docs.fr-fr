---
title: Fonctionnalités de mise hors service du centre de sécurité (juillet 2019) | Microsoft Docs
description: Cet article décrit les fonctionnalités dans le centre de sécurité seront supprimées le 31 juillet 2019.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 5ac6603be087e887e3304f6a7e14aa063863e1dd
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796466"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Retrait des fonctionnalités de Security Center (2019 juillet)

Nous avons apporté plusieurs [améliorations](https://azure.microsoft.com/updates/?product=security-center) à Azure Security Center au cours des six derniers mois.
Avec ces fonctionnalités améliorées, nous allons supprimer des fonctionnalités redondantes et des API connexes à partir du centre de sécurité sur le 31 juillet 2019.  

La plupart de ces fonctionnalités retraite peut être remplacée par la nouvelle fonctionnalité dans Azure Security Center ou Azure Log Analytique. Autres fonctionnalités peuvent être implémentées à l’aide de [Azure Sentinel (version préliminaire)](https://azure.microsoft.com/services/azure-sentinel/).

Fonctionnalités de Security Center pour être mis hors service :

- [Tableau de bord événements](#menu_events)
- [Entrée de menu de recherche](#menu_search)
- [Affichage classique identité et accès lien sur les identités et des accès (version préliminaire)](#menu_classicidentity)
- [Événements de sécurité mappent bouton sur la carte d’alertes de sécurité (version préliminaire)](#menu_securityeventsmap)
- [Règles d’alerte personnalisées (version préliminaire)](#menu_customalerts)
- [Examiner le bouton dans les alertes de sécurité de protection contre les menaces](#menu_investigate)
- [Un sous-ensemble de solutions de sécurité](#menu_solutions)
- [Modifier les configurations de sécurité pour les stratégies de sécurité](#menu_securityconfigurations)
- [Sécurité et audit tableau de bord (initialement utilisé dans le portail OMS) pour les espaces de travail Analytique de journal](#menu_securityomsdashboard)

Cet article fournit des informations détaillées pour chaque fonctionnalité mis hors service et les étapes que vous pouvez prendre pour implémenter des fonctions de remplacement.

## Tableau de bord événements<a name="menu_events"></a>

Security Center utilise Microsoft Monitoring Agent pour collecter plusieurs configurations liées à la sécurité et les événements à partir de vos machines. Elle stocke ces événements dans vos espaces de travail. Le [tableau de bord événements](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) vous permet d’afficher ces données et vous donne un point d’entrée pour l’Analytique de journal.

Nous avons décidé de supprimer le tableau de bord événements qui s’affiche lorsque vous sélectionnez un espace de travail :

![Tableau de bord des événements][2]

### <a name="events-dashboard---the-new-experience"></a>Tableau de bord événements - la nouvelle expérience

Nous vous recommandons d’utiliser les fonctionnalités natives d’Analytique de journal Azure pour afficher les événements notables sur vos espaces de travail.

Si vous avez créé des événements notables personnalisés dans Security Center, ils serez accessibles. Dans le journal Analytique, accédez à **sélectionnez l’espace de travail** > **recherches enregistrées**. Vos données ne sont pas perdues ou modifiées. Les événements notables natifs sont également disponibles dans le même écran dans le journal Analytique.

![Espace de travail recherches enregistrée][3]

## Entrée de menu de recherche<a name="menu_search"></a>

Azure Security Center utilise actuellement la recherche de journaux Azure Monitor pour récupérer et analyser vos données de sécurité. Cet écran sert d’une fenêtre de la page de recherche Analytique de journal et permet aux utilisateurs d’exécuter des requêtes de recherche sur leur espace de travail sélectionné. Pour plus d’informations, consultez [recherche Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-search). Nous avons décidé de supprimer cette fenêtre de recherche :

![Page de recherche][4]

### <a name="search-menu-entry---the-new-experience"></a>Entrée de menu de recherche - la nouvelle expérience

Nous vous encourageons à utiliser les fonctionnalités natives d’Analytique de journal Azure pour effectuer des requêtes de recherche sur vos espaces de travail. Accédez à Azure Log Analytique et sélectionnez **journaux**.

![Page de journaux Analytique de journal][5]

## Classique identité et accès (version préliminaire)<a name="menu_classicidentity"></a>

L’expérience classique d’identité et accès dans Security Center affiche actuellement un tableau de bord des informations d’identité et accès dans le journal Analytique. Pour afficher ce tableau de bord :

1. Sélectionnez **afficher identité et accès classique**.

   ![Page de l’identité][6]

1. Afficher le **tableau de bord identité et accès**.

    ![Page identité - sélection de l’espace de travail][7]

1. Sélectionnez un espace de travail pour ouvrir le **identité et accès** tableau de bord dans l’Analytique de journal pour afficher l’identité et d’accéder aux informations sur votre espace de travail.

   ![Page identité - tableau de bord][8]

Nous avons décidé de supprimer tous les trois écrans indiqués dans les étapes précédentes. Vos données restent disponibles dans la solution de sécurité d’Analytique de journal et ne peut être ni modifiées ou supprimées.

### <a name="classic-identity--access-preview---the-new-experience"></a>Identité et accès (version préliminaire) - la nouvelle expérience classique

Le tableau de bord d’Analytique de journal a montré des insights sur un seul espace de travail. Toutefois, les fonctionnalités natives du centre de sécurité fournissent une visibilité sur tous les abonnements et tous les espaces de travail qui s’y rapportent. Vous pouvez accéder à un simple-pour utiliser la vue qui vous permet de vous concentrer sur ce qui est important, avec des recommandations classées en fonction de leur score sécurisé.

Toutes les fonctionnalités de la **identité et accès** tableau de bord dans le journal Analytique peut être atteint en sélectionnant **identité et accès (aperçu)** dans Security Center.

![Page identité - classic expérience mise hors service][9]

## Mappent des événements de sécurité<a name="menu_securityeventsmap"></a>

Security Center vous offre un [mappage des alertes de sécurité](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) pour aider à identifier les menaces de sécurité. Le **accéder au mappage des événements de sécurité** bouton dans ce mappage permet d’ouvrir un tableau de bord qui vous permet d’afficher les événements de sécurité brute de l’espace de travail sélectionné.

Nous allons supprimer la **accéder au mappage des événements de sécurité** bouton et le tableau de bord par espace de travail.

![Bouton de mappage - des alertes de sécurité][10]

Lorsque vous sélectionnez le **accéder au mappage des événements de sécurité** bouton, vous ouvrez le tableau de bord threat intelligence. Nous avons décidé de supprimer le tableau de bord threat intelligence.  

![Tableau de bord Threat intelligence][11]

Lorsque vous choisissez un espace de travail pour afficher son tableau de bord threat intelligence, vous ouvrez l’écran de mappage (version préliminaire) des alertes de sécurité dans le journal Analytique. Nous avons décidé de supprimer cet écran.

![Mappage d’alertes de sécurité dans le journal Analytique][12]

Vos données existantes restent disponibles dans la solution de sécurité d’Analytique de journal et ne sont pas modifiées ni supprimées.

### <a name="security-events-map---the-new-experience"></a>Carte d’événements de sécurité - la nouvelle expérience

Nous vous encourageons à utiliser la fonctionnalité de mappage d’alertes intégrée à Security Center : **Mappage d’alertes de sécurité (version préliminaire)**. Cette fonctionnalité fournit une expérience optimisée et fonctionne sur tous les abonnements et les espaces de travail associés. Il vous donne une vue d’ensemble au sein de votre environnement et n’est pas axé sur un seul espace de travail.

## Règles d’alerte personnalisées (version préliminaire)<a name="menu_customalerts"></a>

Nous sommes [mise hors service personnalisé expérience alertes](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) sur le 30 juin 2019, car son infrastructure sous-jacente est mise hors service. En attendant, vous pouvez modifier les règles d’alerte personnalisées existantes, mais vous ne pouvez pas ajouter de nouveaux. Nous vous recommandons d’activer [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) pour migrer vos alertes existants et créer de nouveaux automatiquement. Vous pouvez également créer vos alertes avec les alertes de journal Azure Monitor.

Pour conserver vos alertes existantes et de les migrer vers Azure Sentinel :

1. Ouvrez Azure Sentinel et sélectionnez l’espace de travail où sont stockés vos alertes personnalisées.
1. Sélectionnez **Analytique** dans le menu pour migrer automatiquement vos alertes.

![Alertes personnalisées][13]

Si vous n’êtes pas intéressé de la transition vers Azure Sentinel, nous vous encourageons à créer vos alertes avec les alertes de journal Azure Monitor. Pour obtenir des instructions, consultez [créer, afficher et gérer des alertes de journal à l’aide d’Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) et [alertes de journal dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

Pour plus d’informations sur la mise hors service des alertes personnalisées, consultez [des règles d’alerte personnalisées dans Azure Security Center (version préliminaire)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Examen des alertes de sécurité<a name="menu_investigate"></a>

[La fonctionnalité d’examen](https://docs.microsoft.com/azure/security-center/security-center-investigation) dans Security Center vous aide à vous triez un incident de sécurité potentielles. La fonctionnalité vous permet de comprendre l’étendue d’un incident et de repérer la cause racine. Nous allons supprimer cette fonctionnalité à partir du centre de sécurité, car elle est été remplacée par une expérience améliorée dans [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/).

![Incident de sécurité][14]

Lorsque vous sélectionnez le **examiner** bouton à partir d’un **incident de sécurité** écran, vous ouvrez le tableau de bord d’Investigation (version préliminaire) dans le journal Analytique. Nous avons décidé de supprimer le tableau de bord d’Investigation.  

Vos données existantes restent disponibles dans la solution de sécurité d’Analytique de journal et ne sont pas modifiées ni supprimées.

![Tableau de bord examen dans Analytique de journal][15]

### <a name="investigation---the-new-experience"></a>Examen - la nouvelle expérience

Nous vous encourageons à passer à [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) pour une expérience d’enquête riches. Sentinel Azure fournit des outils puissants de recherche et de requête de recherche les menaces de sécurité de vos sources de données de votre organisation.  

## Sous-ensemble de solutions de sécurité<a name="menu_solutions"></a>

Security Center peut activer [des solutions de sécurité dans Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Nous retirons les solutions partenaires suivantes à partir du centre de sécurité. Ces solutions sont activées dans [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) ainsi que d’un nombre de sources de données supplémentaires.

- [Prochaine génération pare-feu et web application solutions de pare-feu](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Intégration de solutions de sécurité qui prennent en charge le Format d’événement commun (CEF)](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytique](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Après la mise hors service, vous ne pourrez ajouter ou modifier un des types de solutions mentionnées dans la liste précédente, à partir de l’interface utilisateur ou de l’API.

Si vous disposez de solutions connectées, nous vous encourageons à déplacer vers Azure Sentinel.

![Solutions de centres de sécurité][16]

## Modifier les configurations de sécurité pour les stratégies de sécurité<a name="menu_securityconfigurations"></a>

Azure Security Center surveille les configurations de sécurité en appliquant un ensemble de [plus de 150 règles recommandées](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Pour renforcer le système d’exploitation. Ces règles concernent les pare-feux, l’audit, les stratégies de mot de passe et bien plus encore. Si une configuration vulnérable est identifiée sur un ordinateur, Security Center génère une recommandation de sécurité. Le [écran de configuration de sécurité de modification](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) permet aux clients de personnaliser la configuration de sécurité par défaut du système d’exploitation dans Security Center.

Nous avons décidé de supprimer cette fonctionnalité en préversion.

![Modifier les configurations de sécurité][17]

### <a name="edit-security-configurations---the-new-experience"></a>Modifier les configurations de sécurité - la nouvelle expérience

Nous souhaitons activer Security Center prendre en charge la [agent invité de la configuration](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration). Une mise à jour permettra à un ensemble de fonctionnalités plus riche, y compris la prise en charge de plusieurs systèmes d’exploitation et l’intégration de stratégies intégrées Azure pour les configurations de l’invité. Une fois ces modifications sont activées, vous aurez également la possibilité de contrôler les configurations à l’échelle et de les appliquer automatiquement à de nouvelles ressources.

## Tableau de bord sécurité et d’audit pour les espaces de travail Analytique de journal<a name="menu_securityomsdashboard"></a>

Le tableau de bord de sécurité et d’audit a été utilisé à l’origine dans le portail OMS. Dans le journal Analytique, le tableau de bord fournit une vue d’ensemble de par espace de travail des événements de sécurité importants et les menaces, une carte d’intelligence des menaces et une évaluation de l’identité et accès d’événements de sécurité enregistrés dans l’espace de travail. Nous allons supprimer le tableau de bord. Comme nous avons déjà recommandé dans le tableau de bord de l’interface utilisateur, nous vous conseillons de qui vous permettent de transition vers Azure Security Center.

![Tableau de bord de security log Analytique][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Tableau de bord sécurité et d’audit - la nouvelle expérience

Nous vous conseillons de passer à Azure Security Center. Il fournit la même vue d’ensemble de sécurité entre plusieurs abonnements et les espaces de travail qui s’y rapportent, ainsi que définie une fonctionnalité plus riche.

Vous pouvez obtenir les requêtes d’Analytique de journal d’origine qui remplissent le tableau de bord de sécurité et d’audit dans le [référentiel GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) pour le centre de sécurité.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus en détail [Azure Security Center](https://docs.microsoft.com/azure/security-center/).
- En savoir plus sur [Azure Sentinel](https://docs.microsoft.com/azure/sentinel).

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png
