---
title: Planifier du déploiement de rapports et de la supervision - Azure AD
description: Explique comment planifier et exécuter l’implémentation de la création de rapports et de la supervision.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad84b8910e8d4f8af9845c33c22d128e317dedc
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232105"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Planifier un déploiement de création de rapports et de supervision Azure Active Directory

Votre solution de supervision et de création de rapports Azure Active Directory (Azure AD) dépend de vos exigences juridiques, sécuritaires et opérationnelles, ainsi que de votre environnement et de vos processus existants. Cet article présente les différentes options de conception et vous aide à choisir la stratégie de déploiement qui convient.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Avantages de la création de rapports et de la supervision Azure AD

La création de rapports Azure AD fournit une vue complète des activités Azure AD qui se déroulent dans votre environnement, y compris les événements de connexion, les événements d’audit et les modifications apportées à votre annuaire.

Les données fournies vous permettent de :

* Déterminer la façon dont les applications et les services sont utilisés

* Détecter les risques potentiels affectant l’intégrité de votre environnement

* Résoudre les problèmes empêchant les utilisateurs d’effectuer leur travail

* Obtenir des insights en consultant les événements d’audit relatifs aux modifications apportées à votre annuaire Azure AD

> [!IMPORTANT]
> La supervision Azure AD vous permet de router les journaux générés par la création de rapports Azure AD vers différents systèmes cibles. Vous pouvez ensuite la conserver pour une utilisation à long terme ou l’intégrer à des outils SIEM (Security Information and Event Management) tiers pour obtenir davantage d’informations sur votre environnement.

Avec la supervision Azure AD, vous pouvez router les journaux vers :

* Un compte de stockage Azure, à des fins d’archivage
* Des journaux Azure Monitor (autrefois appelés « espaces de travail Azure Log Analytics »), dans lesquels vous pouvez analyser les données, créer des tableaux de bord et signaler des événements.
* Un hub d’événements Azure, dans lequel vous pouvez intégrer vos outils SIEM existants tels que Splunk, Sumologic ou QRadar.

> [!NOTE]
Désormais, nous n’utilisons plus le terme « Log Analytics », mais le terme « journaux Azure Monitor ». Les données de journal sont toujours stockées dans un espace de travail Log Analytics, et elles sont toujours collectées et analysées par le même service Log Analytics. Nous mettons la terminologie à jour pour mieux refléter le rôle des [journaux d’activité dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection). Pour plus d'informations, consultez [Modifications de la terminologie d'Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/azure-monitor-rebrand).

[En savoir plus sur nos stratégies de conservation des rapports](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention)

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Licences et prérequis pour les fonctionnalités de création de rapports et de supervision Azure AD

Vous avez besoin d’une licence Azure AD Premium pour accéder aux journaux de connexion Azure AD.

Pour obtenir des informations détaillées sur les fonctionnalités et les licences, consultez le [guide des tarifs Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Pour déployer les fonctionnalités de supervision et de création de rapports Azure AD, vous avez besoin d’un utilisateur qui dispose du rôle d’administrateur général ou d’administrateur de la sécurité sur le locataire Azure AD.

En fonction de la destination finale de vos données de journal, vous aurez besoin de l’un des éléments suivants :

* Un compte de stockage Azure doté des autorisations ListKeys. Nous vous recommandons d’utiliser un compte de stockage général et pas un compte de stockage blob. Pour plus d’informations sur la tarification du stockage, utilisez la [Calculatrice de prix pour le stockage Azure](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Un espace de noms Azure Event Hubs pour intégrer avec des solutions SIEM tierces.

* Un espace de travail Azure Log Analytics pour l’envoi de journaux d’activité aux journaux d’activité Azure Monitor.

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Planifier un projet de déploiement pour la création de rapports et la supervision Azure

Dans ce projet, vous allez définir le public qui doit utiliser et superviser les rapports, puis vous allez définir votre architecture de supervision Azure AD.

### <a name="engage-the-right-stakeholders"></a>Impliquer les parties prenantes appropriées

Lorsque des projets technologiques échouent, cela est généralement dû à des attentes qui ne correspondent pas à l’impact, aux résultats et aux responsabilités réels. Pour éviter un tel cas de figure, [veillez à faire appel aux bonnes personnes](https://aka.ms/deploymentplans). Veillez également à ce qu’elles comprennent bien leur rôle dans le projet, en dressant la liste de leurs contributions et de leurs responsabilités.

### <a name="plan-communications"></a>Planifier les communications

La communication est essentielle à la réussite de tout nouveau service. Communiquez de manière proactive avec vos utilisateurs sur ce qui va changer, à quel moment les changements seront appliqués et comment ils peuvent obtenir de l’aide en cas de problème.

### <a name="document-your-current-infrastructure-and-policies"></a>Documenter votre infrastructure et vos stratégies existantes

Votre infrastructure et vos stratégies existantes vont déterminer la conception de vos fonctionnalités de création de rapports et de supervision. Veillez à connaître :

* Les outils SIEM que vous utilisez, le cas échéant.

* Votre infrastructure Azure, y compris les comptes de stockage existants et la supervision utilisée.

* Les stratégies de conservation de votre organisation qui sont appliquées aux journaux, y compris les frameworks de conformité applicables nécessaires. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Planifier un déploiement pour la création de rapports et la supervision Azure AD

La création de rapports et la supervision permettent de répondre aux exigences de votre entreprise, d’obtenir des insights sur les modèles d’utilisation et d’améliorer la sécurité de votre organisation.

### <a name="business-use-cases"></a>Cas d’usage métier

* Obligatoire pour que la solution réponde aux exigences de l’entreprise
* Recommandé pour que la solution réponde aux exigences de l’entreprise
* Non applicable

|Domaine |Description |
|-|-|
|Rétention| **Conservation des journaux pendant plus de 30 jours**. ‎En raison des exigences juridiques ou métier, il est nécessaire de stocker les journaux d’audit et les journaux des connexions Azure AD pendant plus de 30 jours. |
|Analytics| **Les journaux doivent pouvoir faire l’objet de recherches**. ‎Les journaux stockés doivent pouvoir faire l’objet de recherches à l’aide des outils d’analytique. |
| Operational Insights| **Insights pour les différentes équipes**. Nécessité d’accorder l’accès à différents utilisateurs pour obtenir des insights opérationnels, comme l’utilisation des applications, les erreurs de connexion, l’utilisation en libre-service, les tendances, etc. |
| Insights sur la sécurité| **Insights pour les différentes équipes**. Nécessité d’accorder l’accès à différents utilisateurs pour obtenir des insights opérationnels, comme l’utilisation des applications, les erreurs de connexion, l’utilisation en libre-service, les tendances, etc. |
| Intégration aux systèmes SIEM      | **Intégration SIEM**. ‎Nécessité de diffuser en streaming des journaux de connexion et des journaux d’audit Azure AD vers les systèmes SIEM existants et de les y intégrer. |

### <a name="choose-a-monitoring-solution-architecture"></a>Choisir une architecture de solution de supervision

Avec la supervision Azure AD, vous pouvez router vos journaux d’activité Azure AD vers le système qui répond le mieux aux besoins de votre entreprise. Vous pouvez les conserver afin de créer des rapports et d’effectuer des analyses dans le long terme, en vue d’obtenir des insights sur votre environnement et de les intégrer à vos outils SIEM.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sectionsmediareporting-deployment-plandeploy-reporting-flow-diagrampng"></a>Organigramme décisionnel![Image montrant ce qui est décrit dans les sections suivantes.](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>Archiver des journaux dans un compte de stockage

Si vous routez les journaux vers un compte de stockage Azure, vous pouvez les conserver plus longtemps que la période de conservation par défaut décrite dans nos [stratégies de conservation](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention). Utilisez cette méthode si vous avez besoin d’archiver vos journaux, mais n’avez pas besoin de les intégrer à un système SIEM ni d’effectuer des requêtes et des analyses en continu. Vous pouvez toujours effectuer des recherches à la demande.

Découvrez comment [acheminer les données vers votre compte de stockage](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account).

#### <a name="send-logs-to-azure-monitor-logs"></a>Envoyer des journaux d’activité aux journaux d’activité Azure Monitor

Les [journaux Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) consolident les données de supervision provenant de différentes sources. Ils fournissent un langage de requête et un moteur d’analytique permettant d’obtenir des insights sur le fonctionnement de vos applications et l’utilisation de vos ressources. Si vous envoyez les journaux d’activité Azure AD aux journaux Azure Monitor, vous pouvez rapidement récupérer, superviser et signaler les données collectées. Utilisez cette méthode si vous n’avez pas de solution SIEM existante à laquelle envoyer vos données directement, mais souhaitez effectuer des requêtes et des analyses. Une fois que vos données se trouvent dans les journaux Azure Monitor, vous pouvez les envoyer au hub d’événements, puis au système SIEM de votre choix.

Découvrez comment [envoyer des données aux journaux d’activité Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Vous pouvez également installer les vues prédéfinies des journaux d’activité d’Azure AD afin de superviser les scénarios courants qui impliquent des événements de connexion et d’audit.

Découvrez comment [installer et utiliser les vues Log Analytics pour les journaux d’activité Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views).

#### <a name="stream-logs-to-your-azure-event-hub"></a>Diffuser en streaming des journaux vers votre hub d’événements Azure

Le routage des journaux vers un hub d’événements Azure permet leur intégration à des outils SIEM tiers. Cette intégration vous permet d’associer les données du journal d’activité d’Azure AD à d’autres données gérées par votre SIEM pour offrir des insights plus fournis sur votre environnement. 

Découvrez comment [transmettre les journaux d’activité à un Event Hub](https://docs.microsoft.com//azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub).

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Planifier les opérations et la sécurité pour la création de rapports et la supervision Azure AD

Les parties prenantes doivent accéder aux journaux Azure AD pour obtenir des insights opérationnels. Les utilisateurs les plus susceptibles d’utiliser ces journaux sont les membres de l’équipe de sécurité, les auditeurs internes ou externes, ainsi que les membres de l’équipe chargée des opérations de gestion des accès et des identités.

Les rôles Azure AD vous permettent de déléguer la configuration et l’affichage des rapports Azure AD en fonction de votre rôle. Identifiez les personnes de votre organisation qui ont besoin de l’autorisation pour lire les rapports Azure AD et déterminez quel rôle leur conviendrait le mieux. 

Les rôles suivants peuvent lire les rapports Azure AD :

* Administrateur général

* Administrateur de la sécurité

* Lecteur de sécurité

* Lecteur de rapport

En savoir plus sur les [rôles d’administration d’Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

*Appliquez toujours le principe des privilèges minimum pour réduire le risque de compromission des comptes*. Envisagez d’implémenter [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) pour renforcer la sécurité de votre organisation.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Déployer les fonctionnalités de création de rapports et de la supervision Azure AD

Selon les décisions que vous avez prises précédemment en vous aidant des conseils de conception ci-dessus, cette section vous indiquera la documentation qui correspond aux options de déploiement que vous avez choisies.

### <a name="consume-and-archive-azure-ad-logs"></a>Consommer et archiver les journaux Azure AD

[Trouver les rapports d’activité sur le Portail Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-find-activity-reports)

[Archiver des journaux Azure AD dans un compte de stockage Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)

### <a name="implement-monitoring-and-analytics"></a>Implémenter la supervision et l’analytique

[Envoyer des journaux à Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

[Installer et utiliser les vues Log Analytics pour Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)

[Analyser les journaux d’activité Azure AD avec les journaux d’activité Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

* [Interpréter le schéma des journaux d’audit dans Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

* [Interpréter le schéma des journaux de connexion dans Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema)

 * [Envoyer en streaming les journaux Azure AD à un hub d’événements Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)

* [Intégrer les journaux d’activité Azure AD à Splunk à l’aide d’Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-integrate-activity-logs-with-splunk)

* [Intégrer des journaux d’activité Azure AD avec SumoLogic à l’aide d’Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic)

 

 

## <a name="next-steps"></a>Étapes suivantes

Envisagez d’implémenter [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure). 

Envisagez d’implémenter l’[utilisation du contrôle d’accès en fonction du rôle (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview).

 
