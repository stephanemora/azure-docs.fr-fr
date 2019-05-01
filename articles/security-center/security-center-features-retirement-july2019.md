---
title: Fonctionnalités de mise hors service du centre de sécurité (juillet 2019) | Microsoft Docs
description: Cet article détaille les fonctionnalités dans le centre de sécurité seront supprimées le 31 juillet 2019.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 392782310d8bc3b38a3dd1349cb1760ca287acd1
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875582"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Retrait des fonctionnalités de Security Center (2019 juillet)

Nous avons apporté plusieurs [améliorations](https://azure.microsoft.com/updates/?product=security-center) au centre de sécurité Azure au cours des 6 derniers mois.  
Avec les diverses fonctionnalités améliorées, nous supprimons un nombre de fonctionnalités redondantes, ainsi que des API connexes, à partir du centre de sécurité sur le 31 juillet 2019.  

La plupart des fonctionnalités mis hors service peut être remplacée par les nouvelles fonctionnalités dans Azure Security Center ou Analytique de journal ; et certaines fonctionnalités peuvent être implémentées à l’aide de [Azure Sentinel (version préliminaire)](https://azure.microsoft.com/services/azure-sentinel/), récemment annoncée.

La liste des fonctionnalités en cours de retrait à partir du centre de sécurité inclut :

- [Tableau de bord événements](#menu_events)
- [Entrée de menu de recherche](#menu_search)
- [Affichage classique identité et accès lien sur les identités et des accès (version préliminaire)](#menu_classicidentity)
- [Événements de sécurité mappent bouton sur la carte d’alertes de sécurité (version préliminaire)](#menu_securityeventsmap)
- [Règles d’alerte personnalisées (version préliminaire)](#menu_customalerts)
- [Examiner le bouton dans les alertes de sécurité de protection contre les menaces](#menu_investigate)
- [Un sous-ensemble de solutions de sécurité](#menu_solutions)
- [Modifier les configurations de sécurité pour les stratégies de sécurité](#menu_securityconfigurations)
- [Sécurité et audit tableau de bord (utilisé à l’origine dans le portail OMS) pour les espaces de travail Analytique de journal.](#menu_securityomsdashboard)

Le ci-dessous fournit des informations détaillées pour chaque fonctionnalité mis hors service et les étapes à suivre à l’aide des fonctionnalités de remplacement.

## Tableau de bord événements<a name="menu_events"></a>
Security Center utilise Microsoft Monitoring Agent pour collecter différents événements et configurations liées à la sécurité à partir de vos machines et stocke ces événements dans vos espaces de travail. Le [tableau de bord événements](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) permet d’afficher ces données et fournit essentiellement un autre point d’entrée pour l’Analytique de journal.

À l’avenir du tableau de bord événements vont être supprimée :

![Écran de sélection d’espace de travail événements][1]

Le tableau de bord événements qui apparaît une fois qu’un utilisateur clique sur un espace de travail seront également supprimées :

![Tableau de bord des événements][2]

### <a name="events-dashboard---new-experience"></a>Tableau de bord événements - nouvelle expérience

Les clients sont encouragés à utiliser les fonctionnalités natives des Analytique de journal pour afficher les événements notables sur leurs espaces de travail.
Si vous avez déjà créé des événements notables personnalisés à partir du centre de sécurité, ils seront accessibles via le journal analytique -> sélectionnez espace de travail -> recherches enregistrées. Vos données ne seront pas perdues ni modifiées. Événements notables natifs sont également disponibles dans le même écran.

![Espace de travail recherches enregistrée][3]

## Entrée de menu de recherche<a name="menu_search"></a>
Azure Security Center utilise actuellement la recherche de journaux Azure Monitor pour récupérer et analyser vos données de sécurité. Cet écran sert essentiellement une façade pour l’Analytique de journal «[recherche](https://docs.microsoft.com/azure/security-center/security-center-search)« page – permettant aux utilisateurs d’exécuter des requêtes de recherche sur leur espace de travail sélectionné. À l’avenir cette fenêtre de recherche va être mis hors service :

![Page de recherche][4]

### <a name="search-menu-entry---new-experience"></a>Entrée de menu - nouvelle expérience de recherche

Les clients sont encouragés à utiliser **Analytique de journal** des fonctionnalités natives pour exécuter des requêtes de recherche sur leurs espaces de travail. Pour ce faire, ils peuvent accéder à l’analytique de journal dans Azure, puis sélectionnez : « Logs » :

![Page de journaux Analytique de journal][5]

## Classique identité et accès (version préliminaire)<a name="menu_classicidentity"></a>
L’expérience d’identité et accès « Classique » dans le centre de sécurité fournissait un moyen pour les clients à consulter leur identité & accéder à des informations connexes dans l’analytique de journal. Cette opération a été effectuée en suivant les clics ci-dessous :

Cliquez sur « Identité classique de la vue & accès »

![Page de l’identité][6]

En même temps que l’écran de cette page s’ouvre, « tableau de bord identité et accès » :

![Page identité - sélection de l’espace de travail][7]

Un clic sur l’espace de travail ouvre le « Identité et accès » journal analytique tableau de bord où les clients peuvent afficher identité et accéder aux informations sur leur espace de travail :

![Page identité - tableau de bord][8]

À l’avenir tous les trois écrans ci-dessus va être mis hors service. Vos données restent disponibles dans la solution de sécurité d’analytique de journal et ne seront pas modifiées ou supprimées.

### <a name="classic-identity--access-preview---new-experience"></a>Identité et accès (version préliminaire) - nouvelle expérience classique
Alors que le tableau de bord Log analytique a fourni des insights sur un espace de travail donné uniquement, les fonctionnalités de centre de sécurité natives fournissent une visibilité sur tous les abonnements et tous les espaces de travail qui s’y rapportent, dans un simple-pour utiliser la vue qui vous permet de vous concentrer sur ce que de important, en fonction de l’indice de sécurisé de votre identité et accès recommandation.
Toutes les fonctionnalités de l’outil identity and access tableau de bord analytique de journal est accessible en sélectionnant « Identité et accès (préversion) » dans le centre de sécurité :

![Page identité - classic expérience mise hors service][9]

## Mappent des événements de sécurité<a name="menu_securityeventsmap"></a>
Security Center vous offre un [carte](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) qui vous aide à identifier les menaces de sécurité pour l’environnement. Le bouton « Atteindre la carte des événements de sécurité » dans ce mappage conduit à un tableau de bord qui permet d’afficher les événements de sécurité brute de l’espace de travail sélectionné.
Le bouton, ainsi que le tableau de bord par espace de travail sera supprimé après la désapprobation.

![Bouton de mappage - des alertes de sécurité][10]

Aujourd'hui lorsque vous cliquez sur « Accéder au mappage des événements de sécurité » du tableau de bord Threat intelligence est ouvert. Le tableau de bord threat intelligence va être mis hors service.  

![Tableau de bord Informations sur les menaces][11]

Lorsque vous choisissez un espace de travail pour afficher son tableau de bord threat intelligence, les alertes de la sécurité map(Preview) écran *dans le journal Analytique* est ouvert. Cet écran va être mis hors service.

![Mappage d’alertes de sécurité dans le journal Analytique][12]

Vos données existantes restent disponibles dans la solution de sécurité d’analytique de journal et ne seront pas modifiées ni supprimées.

### <a name="security-events-map---new-experience"></a>Carte d’événements de sécurité - nouvelle expérience
Nous encourageons nos clients d’utiliser la fonctionnalité de mappage d’alertes intégrée à Security Center - « alertes de sécurité mappent (version préliminaire) ». Cela fournit une expérience optimisée et fonctionne sur tous les abonnements et les espaces de travail associés, ce qui permet une macro afficher au sein de votre environnement et pas vous concentrer sur un seul espace de travail.

## Règles d’alerte personnalisées (version préliminaire)<a name="menu_customalerts"></a>
L’expérience des alertes personnalisées sera [retirée](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) le 30 juin 2019, en raison de la suppression de l’infrastructure sous-jacente il s’appuie sur. Dans l’intervalle de temps jusqu'à ce que de désapprobation, les utilisateurs seront en mesure de modifier les règles d’alerte personnalisées existantes mais ne pourrez pas ajouter de nouveaux. Les utilisateurs sont invités à activer [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) avec l’intégration d’un seul clic automatiquement migrer leurs alertes existantes et créer de nouveaux, ou vous pouvez également recréer leurs alertes avec Azure Monitor des alertes de journal.

Pour conserver vos alertes existantes et de les migrer vers Azure Sentinel, lancez la commande Azure Sentinel. En tant que première étape, sélectionnez l’espace de travail où sont stockés vos alertes personnalisées, puis sélectionnez l’élément de menu « Analytique » pour migrer automatiquement vos alertes.

![Alertes personnalisées][13]

Pas intéressé par l’intégration à Azure Sentinel les clients sont encouragés à recréer leurs alertes avec les alertes de journal Azure Monitor. Pour obtenir des instructions, consultez : [créer, afficher et gérer les alertes de journal à l’aide d’Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log). Pour obtenir des instructions sur la façon de créer des alertes de journal, consultez : [Alertes de journal dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

Pour plus d’informations sur la mise hors service des alertes personnalisées, consultez le [documentation sur les alertes Security Center personnalisé](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Examen des alertes de sécurité<a name="menu_investigate"></a>
[La fonctionnalité d’examen](https://docs.microsoft.com/azure/security-center/security-center-investigation) dans Security Center vous permet de trier, comprendre l’étendue et repérer la cause racine d’un incident de sécurité potentielles. Cette fonctionnalité sera supprimée à partir du centre de sécurité comme il a été remplacée par une expérience améliorée dans [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/).

![Incident de sécurité][14]

Lorsque vous cliquez sur le bouton « Examiner » ci-dessus, « Enquête Dashboard (version préliminaire) » dans le journal Analytique s’ouvre. Le tableau de bord d’Investigation va être mis hors service.  
Vos données existantes restent disponibles dans la solution de sécurité d’Analytique de journal et ne seront pas modifiées ni supprimées.

![Tableau de bord examen dans Analytique de journal][15]

### <a name="investigation---new-experience"></a>Examen - nouvelle expérience

Nous encourageons nos clients à intégrer [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) pour une expérience d’enquête riches, alimenté par les alertes chasse de fonctionnalité. Sentinel Azure propose des outils de recherche et de requête de chasse puissant permettant de recherche les menaces de sécurité de vos sources de données de votre organisation.  

## Sous-ensemble de solutions de sécurité<a name="menu_solutions"></a>

Centre de sécurité a la possibilité d’activer [des solutions de sécurité dans Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Les solutions de partenaires suivant seraient supprimées et pris en charge dans [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/), ainsi que d’autres sources de données.

- Nouvelle génération de pare-feu et solution de pare-feu d’application Web (Azure Sentinel [Documentation](https://docs.microsoft.com/azure/sentinel/connect-data-sources))
- Intégration de solutions de sécurité qui prennent en charge le format d’événement courant (CEF) (Azure Sentinel [Documentation](https://docs.microsoft.com/azure/sentinel/connect-common-event-format))
- Microsoft Advanced Threat Analytique (Sentinel Azure [Documentation](https://docs.microsoft.com/azure/sentinel/connect-azure-atp))
- Azure AD Identity Protection (Azure Sentinel [Documentation](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection))

Après la dépréciation, les utilisateurs pas pourront ajouter un nouveau ou modifier des solutions connectées existantes des types mentionnés ci-dessus, à partir de l’interface utilisateur et l’API.
Les solutions connectées existantes sont encouragées à déplacer vers Azure Sentinel à la fin de cette période.

![Solutions de centres de sécurité][16]

## Modifier les configurations de sécurité pour les stratégies de sécurité<a name="menu_securityconfigurations"></a>
Azure Security Center analyse les configurations de la sécurité en appliquant un ensemble de [plus de 150 règles recommandées](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) pour renforcer le système d’exploitation, y compris des règles relatives aux pare-feux, aux audits, aux stratégies de mot de passe et bien plus encore. Si une configuration vulnérable est identifiée sur un ordinateur, Security Center génère une recommandation de sécurité. Le [écran de configuration de sécurité de modification](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) permet aux clients de personnaliser la configuration de sécurité par défaut du système d’exploitation dans Security Center.

Cette fonctionnalité a été en version préliminaire et va être mis hors service.

![Modifier les configurations de sécurité][17]

### <a name="edit-security-configurations---new-experience"></a>Modifier les configurations de sécurité - nouvelle expérience

Security Center prend en charge la [agent dans l’invité](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) dans un avenir proche, ce qui permet un ensemble de fonctionnalités plus riche - y compris la prise en charge pour l’intégration avec la configuration invité avec les stratégies Azure (dans l’invité et d’autres systèmes d’exploitation stratégies). Cela fournit également la possibilité de contrôler à l’échelle et s’appliquent automatiquement sur les nouvelles ressources.

## Sécurité et audit tableau de bord (initialement utilisé dans le portail OMS) pour les espaces de travail Analytique de journal<a name="menu_securityomsdashboard"></a>

Le tableau de bord de sécurité dans l’analytique de journal fournit une vue d’ensemble par espace de travail d’événements de sécurité importants et les menaces, une carte d’intelligence des menaces, identité et accès évaluation des événements de sécurité enregistrés dans l’espace de travail. Le tableau de bord sera supprimé à l’avenir. Comme nous avons déjà recommandé dans l’interface utilisateur du tableau de bord, nos utilisateurs sont invités à utiliser Azure Security Center à l’avenir.

![Tableau de bord de security log analytique][18]

### <a name="security--audit-dashboard---new-experience"></a>Tableau de bord sécurité et d’audit - nouvelle expérience
Nos clients sont invités à utiliser Azure Security Center, qui fournit la même vue d’ensemble de sécurité entre plusieurs abonnements et les espaces de travail qui s’y rapportent, ainsi que d’un ensemble de fonctionnalités plus riches.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [Azure Security Center](https://docs.microsoft.com/azure/security-center/)
- En savoir plus sur [Sentinel Azure](https://docs.microsoft.com/azure/sentinel)

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
