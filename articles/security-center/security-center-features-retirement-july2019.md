---
title: Mise hors service de fonctionnalités de Security Center (juillet 2019) | Microsoft Docs
description: Cet article décrit les fonctionnalités de Security Center qui seront mises hors service le 31 juillet 2019.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 8e7456eff3062ef6667e7b0022ea9740c397a493
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679242"
---
> [!NOTE]
> Ce document détaille la liste des fonctionnalités qui ont été supprimées d’Azure Security Center le 31 juillet 2019.
>
>


# <a name="retirement-of-security-center-features-july-2019"></a>Mise hors service de fonctionnalités de Security Center (juillet 2019)

Nous avons apporté plusieurs [améliorations](https://azure.microsoft.com/updates/?product=security-center) à Azure Security Center au cours des six derniers mois.
Suite à ces améliorations de fonctionnalités, le 31 juillet 2019, nous allons retirer du Security Center certaines fonctionnalités aujourd’hui redondantes, ainsi que leurs API connexes.  

La plupart de ces fonctionnalités mises hors service seront remplacées par une nouvelle fonctionnalité dans Azure Security Center ou Azure Log Analytics. D’autres fonctionnalités peuvent être implémentées en utilisant [Azure Sentinel (préversion)](https://azure.microsoft.com/services/azure-sentinel/).

Voici une liste des fonctionnalités de Security Center que nous mettrons hors service :

- [Tableau de bord des événements](#menu_events)
- [Entrée de menu Rechercher](#menu_search)
- [Lien Afficher l’ancienne version de la page Identité et accès sur la page Identité et accès (préversion)](#menu_classicidentity)
- [Bouton Carte des événements de sécurité sur la page Carte des alertes de sécurité (préversion)](#menu_securityeventsmap)
- [Règles des alertes personnalisées (préversion)](#menu_customalerts)
- [Bouton Examiner dans les alertes de sécurité de protection contre les menaces](#menu_investigate)
- [Un sous-ensemble de Solutions de sécurité](#menu_solutions)
- [Modification des paramètres de configuration de la sécurité pour les Stratégies de sécurité](#menu_securityconfigurations)
- [Tableau de bord Sécurité et audit (initialement utilisé dans le portail OMS) pour les espaces de travail Log Analytics](#menu_securityomsdashboard)

Cet article fournit des informations détaillées pour chaque fonctionnalité mise hors service, ainsi que les étapes à suivre pour implémenter les fonctionnalités prévues pour les remplacer.

## <a name="menu_events">Tableau de bord des événements</a>

Security Center utilise Microsoft Monitoring Agent pour collecter différents événements et configurations liées à la sécurité à partir de vos machines. Il stocke ces événements dans vos espaces de travail. Le [tableau de bord des événements](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) vous permet d’afficher ces données et vous donne un point d’entrée vers Log Analytics.

Nous avons décidé de supprimer le tableau de bord des événements qui s’affiche lorsque vous sélectionnez un espace de travail :

![Tableau de bord des événements][2]

### <a name="events-dashboard---the-new-experience"></a>Nouvelle expérience utilisateur pour le tableau de bord des événements

Nous vous recommandons d’utiliser les fonctionnalités natives d’Azure Log Analytics pour afficher les événements notables sur vos espaces de travail.

Si vous avez créé des événements notables personnalisés dans Security Center, ils seront accessibles. Dans Log Analytics, accédez à **Sélectionner un espace de travail** > **Recherches enregistrées**. Vos données sont conservées telles quelles. Vous pouvez aussi accéder aux événements notables dans le même écran de Log Analytics.

![Recherches enregistrées de l’espace de travail][3]

## Entrée de menu Rechercher<a name="menu_search"></a>

Azure Security Center utilise actuellement la recherche de Journaux Azure Monitor pour récupérer et analyser vos données de sécurité. Cet écran sert de fenêtre pour la page de recherche de Log Analytics. Il permet aux utilisateurs d’exécuter des requêtes de recherche sur l’espace de travail qu’ils sélectionnent. Si vous souhaitez en savoir plus, veuillez consulter l’article [Recherche Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-search). Nous allons mettre hors service cette fenêtre de recherche :

![Page de recherche][4]

### <a name="search-menu-entry---the-new-experience"></a>Nouvelle expérience utilisateur de l’entrée de menu Rechercher

Nous vous conseillons d’utiliser les fonctionnalités natives d’Azure Log Analytics pour exécuter des requêtes de recherche sur vos espaces de travail. Accédez à Azure Log Analytics et sélectionnez **Journaux d’activité**.

![Page Journaux d’activité de Log Analytics][5]

## Identité et accès (préversion)<a name="menu_classicidentity"></a>

L’ancienne expérience utilisateur Identité et accès dans Security Center affiche actuellement un tableau de bord compilant les informations d’identité et d’accès dans Log Analytics. Pour afficher ce tableau de bord :

1. Sélectionnez **Afficher l’ancienne version de la page Identité et accès**.

   ![Page Identité][6]

1. Affichez le **tableau de bord Identité et accès**.

    ![Sélection de l’espace de travail dans la page Identité][7]

1. Sélectionnez un espace de travail pour ouvrir le tableau de bord **Identité et accès** dans Log Analytics, pour afficher l’identité et accéder aux informations sur votre espace de travail.

   ![Tableau de bord de la page Identité][8]

Nous allons mettre hors service les trois écrans présentés précédemment. Vos données restent disponibles dans la solution de sécurité Log Analytics. Elle ne seront ni modifiées ni supprimées.

### <a name="classic-identity--access-preview---the-new-experience"></a>Nouvelle expérience utilisateur pour l’ancienne version d’Identité et accès (préversion)

Le tableau de bord Log Analytics affiche des informations sur un seul espace de travail. Toutefois, les fonctionnalités natives de Security Center permettent de consulter tous les abonnements et tous les espaces de travail qui y sont associés. Vous pouvez accéder à un affichage facile à utiliser qui vous permet de vous concentrer sur les éléments importants, grâce à classement des recommandations en fonction de leur degré de sécurisation.

Vous pouvez accéder à toutes les fonctionnalités du tableau de bord **Identité et accès** de Log Analytics en sélectionnant **Identité et accès (préversion)** dans Security Center.

![Mise hors service de l’ancienne version de la page Identité][9]

## <a name="menu_securityeventsmap">Carte des événements de sécurité</a>

Security Center fournit une [Carte des événements de sécurité](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) pour vous aider à identifier les menaces de sécurité. Le bouton **Accéder à la carte des événements de sécurité** dans cette carte ouvre un tableau de bord qui vous permet de consulter les événements de sécurité bruts sur l’espace de travail sélectionné.

Nous allons retirer le bouton **Accéder à la carte des événements de sécurité** ainsi qu’à son tableau de bord, selon votre espace de travail.

![Bouton pour accéder à la carte des alertes de sécurité][10]

Lorsque vous sélectionnez bouton **Accéder à la carte des événements de sécurité**, vous ouvrez le tableau de bord Informations sur les menaces. Nous allons mettre hors service le tableau de bord Informations sur les menaces.  

![Tableau de bord Informations sur les menaces][11]

Lorsque vous sélectionnez un espace de travail pour afficher son tableau de bord Informations sur les menaces, vous ouvrez l’écran Carte des alertes de sécurité (préversion) dans Log Analytics. Nous allons retirer cet écran.

![Carte d’alertes de sécurité dans Log Analytics][12]

Vos données existantes restent disponibles dans la solution Log Analytics. Elles ne seront ni modifiées ni supprimées.

### <a name="security-events-map---the-new-experience"></a>Nouvelle expérience utilisateur pour la carte des événements de sécurité

Nous vous conseillons d’utiliser la fonctionnalité de carte des alertes intégrée à Security Center : **Carte des alertes de sécurité (préversion)** . Cette fonctionnalité optimise votre expérience utilisateur et fonctionne sur tous les abonnements et les espaces de travail qui leur sont associés. Elle fournit une vue d’ensemble de votre environnement et n’est pas restreinte à un seul espace de travail.

## Règles des alertes personnalisées (préversion)<a name="menu_customalerts"></a>

Nous avons [retiré l’expérience utilisateur des alertes personnalisées](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) depuis le 30 juin 2019, car nous avons mis hors service son infrastructure sous-jacente. En attendant, vous pouvez modifier les règles des alertes personnalisées existantes, mais vous ne pouvez pas en ajouter d’autres. Depuis la date de notre retrait, toutes les alertes personnalisées définies ne prennent plus effet. Les alertes de sécurité basées sur ces règles ne sont plus générés.
Nous vous recommandons d’activer [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) et de recréer vos alertes personnalisées. Vous pouvez également créer vos alertes en utilisant avec les alertes de journal d’Azure Monitor.

Pour conserver vos alertes existantes et les créer avec Azure Sentinel :

1. [Ouvrez Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview) et sélectionnez l’espace de travail où sont stockés vos alertes personnalisées.
1. Sélectionnez **Analytics** dans le menu.
1. Suivez les instructions du [didacticiel](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) suivant qui explique comment créer des alertes personnalisées dans Azure Sentinel.

Si vous ne souhaitez pas utiliser Azure Sentinel, vous pouvez créer vos alertes avec les alertes de journal Azure Monitor. Vous trouverez des instructions en ce sens sur les pages  [Créer, afficher et gérer des alertes de journal en utilisant Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) et [Alertes de journal dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

![Alertes personnalisées][13]

Si vous souhaitez en savoir plus sur la mise hors service des alertes personnalisées, veuillez consulter la page [Règles des alertes personnalisées dans Azure Security Center (préversion)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Examen des alertes de sécurité<a name="menu_investigate"></a>

[La fonctionnalité Examen](https://docs.microsoft.com/azure/security-center/security-center-investigation) de Security Center vous aide à trier un incident de sécurité éventuel. Cette fonctionnalité vous permet de comprendre l’étendue d’un incident et de repérer sa cause racine. Nous allons retirer cette fonctionnalité de Security Center, car nous l’avons remplacée par une expérience utilisateur améliorée dans [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/).

![Incident de sécurité][14]

Lorsque vous sélectionnez le bouton **Examiner** à partir d’un écran d’**incident de sécurité**, le tableau de bord Examen (préversion) s’affiche dans Log Analytics. Nous allons mettre hors service le tableau de bord Examen.  

Vos données existantes restent disponibles dans la solution Log Analytics. Elles ne seront ni modifiées ni supprimées.

![Tableau de bord Examen dans Log Analytics][15]

### <a name="investigation---the-new-experience"></a>Nouvelle expérience utilisateur de la fonctionnalité Examen

Nous vous conseillons de migrer vers [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) pour améliorer votre expérience utilisateur d’examen. Azure Sentinel fournit des outils puissants dédiés à la recherche et la création de requêtes pour repérer les menaces de sécurité dans les sources de données de votre organisation.  

## Sous-ensemble de solutions de sécurité<a name="menu_solutions"></a>

Security Center peut activer des [solutions de sécurité intégrées dans Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Nous allons mettre hors service les solutions de partenaires suivantes dans Security Center. Ces solutions sont activées dans [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) ainsi qu’un nombre de sources de données supplémentaires.

- [Prochaine génération de pare-feu et de solutions de pare-feu pour application web](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Intégration de solutions de sécurité qui prennent en charge le format d’événement courant (CEF)](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Après la mise hors service, vous ne pourrez ajouter ou modifier un des types de solutions mentionnées dans la liste précédente, à partir de l’interface utilisateur ou de l’API.

Si vous disposez de solutions connectées, nous vous conseillons de migrer vers Azure Sentinel.

![Solutions de centres de sécurité][16]

## Modification des paramètres de configuration de la sécurité pour les stratégies de sécurité<a name="menu_securityconfigurations"></a>

Azure Security Center supervise les configurations de sécurité en appliquant un ensemble de [plus de 150 règles recommandées](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) pour renforcer le système d’exploitation. Ces règles concernent les pare-feux, l’audit, les stratégies de mot de passe et bien plus encore. Si une configuration vulnérable est identifiée sur un ordinateur, Security Center génère une recommandation de sécurité. L’écran [Modification des paramètres de configuration](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) permet aux clients de personnaliser la configuration de sécurité par défaut de leur système d’exploitation dans Security Center.

Nous allons mettre hors service cette fonctionnalité en préversion. Si, après la date de mise hors service, vous souhaitez réinitialiser vos configurations de sécurité à leurs valeurs par défaut, vous pouvez le faire via l’API ou Powershell en suivant [ces instructions](https://aka.ms/ascresetsecurityconfigurations)

![Modifier les configurations de la sécurité][17]

### <a name="edit-security-configurations---the-new-experience"></a>Nouvelle expérience utilisateur pour la fenêtre Modifier les configurations de la sécurité

Nous souhaitons permettre à Security Center de prendre en charge l’[agent Guest configuration](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) (configuration d’invité). Une telle mise à jour permettra d’enrichir l’ensemble de fonctionnalités que nous proposons, notamment en permettant la prise en charge de plusieurs systèmes d’exploitation et l’intégration de politiques Azure intégrées pour les configurations d’invité. Une fois ces modifications activées, vous pourrez également contrôler les configurations à l’échelle et les appliquer automatiquement à de nouvelles ressources.

## Tableau de bord Sécurité et audit pour les espaces de travail Log Analytics<a name="menu_securityomsdashboard"></a>

Le tableau de bord Sécurité et audit était originellement utilisé dans le portail OMS. Dans Log Analytics, le tableau de bord fournit une vue d’ensemble des événements de sécurité notables et des menaces pour chaque espace de travail, une carte d’informations sur les menaces et une évaluation de l’identité et de l’accès des événements de sécurité enregistrés dans l’espace de travail. Nous allons mettre hors service ce tableau de bord. Comme nous avons déjà recommandé dans l’IU du tableau de bord, nous vous conseillons de migrer vers Azure Security Center.

![Tableau de bord de sécurité Log Analytics][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Nouvelle expérience utilisateur du tableau de bord Sécurité et audit

Nous vous conseillons de migrer vers Azure Security Center. Ce service fournit la même vue d’ensemble de sécurité sur plusieurs abonnements et les espaces de travail qui y sont associés, ainsi qu’un ensemble de fonctionnalités plus complet.

Vous pouvez obtenir les requêtes Log Analytics d’origine qui alimentent le tableau de bord Sécurité et audit dans le [référentiel GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) pour Security Center.

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
