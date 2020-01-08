---
title: Intégrer des solutions de sécurité dans Azure Security Center | Microsoft Docs
description: Découvrez comment Azure Security Center s’intègre avec les partenaires pour améliorer la sécurité globale de vos ressources Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 7174003485d51cf582c798c4b18404b1b72de0fb
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530950"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Intégrer des solutions de sécurité dans Azure Security Center
Ce document vous aide à gérer les solutions de sécurité déjà connectées à Azure Security Center et à en ajouter de nouvelles.

> [!NOTE]
> Un sous-ensemble de solutions de sécurité a été supprimé le 31 juillet 2019. Pour plus d’informations et connaître les alternatives, consultez [Retrait des fonctionnalités de Security Center (juillet 2019).](security-center-features-retirement-july2019.md#menu_solutions)

## <a name="integrated-azure-security-solutions"></a>Solutions de sécurité Azure intégrées
Security Center simplifie l’activation des solutions de sécurité intégrées dans Azure. Voici les avantages :

- **Déploiement simplifié** : Security Center permet un provisionnement simplifié des solutions de partenaire intégrées. Pour les solutions telles que les logiciels anti-programme malveillant et l’évaluation des vulnérabilités, Security Center peut provisionner l’agent sur vos machines virtuelles. Pour les appliances de pare-feu, Security Center peut prendre en charge une grande partie de la configuration réseau requise.
- **Détections intégrées** : les événements de sécurité des solutions de partenaire sont automatiquement collectés, agrégés et affichés dans le cadre des alertes et des incidents de Security Center. Ces événements sont également fusionnés avec les détections d’autres sources pour fournir des fonctions de détection de menaces avancées.
- **Gestion et surveillance unifiées de l’intégrité** : Les clients peuvent utiliser des événements d’intégrité intégrés pour surveiller facilement les solutions des partenaires. La gestion de base offre un accès facile à la configuration avancée avec la solution de partenaire.

Les solutions de sécurité intégrées incluent l’évaluation des vulnérabilités par [Qualys](https://www.qualys.com/public-cloud/#azure) et [Rapid7](https://www.rapid7.com/products/insightvm/), et le pare-feu d’applications web Application Gateway de Microsoft.

> [!NOTE]
> Security Center n’installe pas Microsoft Monitoring Agent sur les appliances virtuelles de partenaire, car la plupart des fournisseurs de sécurité n’autorisent pas l’exécution d’agents externes sur leurs appliances.
>
>

## <a name="how-security-solutions-are-integrated"></a>Comment sont intégrées les solutions de sécurité
Les solutions de sécurité Azure déployées à partir de Security Center sont automatiquement connectées. Vous pouvez également connecter d’autres sources de données de sécurité, notamment des ordinateurs s’exécutant en local ou dans d’autres clouds.

![Intégration de solutions de partenaire](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gérer des solutions de sécurité Azure intégrées et d’autres sources de données

1. Connectez-vous au [portail Azure](https://azure.microsoft.com/features/azure-portal/).

2. Dans le **menu Microsoft Azure**, sélectionnez **Security Center**. La fenêtre **Security Center - Vue d’ensemble** s’ouvre.

3. Dans le menu de Security Center, sélectionnez **Solutions de sécurité**.

   ![Vue d’ensemble de Security Center](./media/security-center-partner-integration/overview.png)

Dans **Solutions de sécurité**, vous pouvez voir le fonctionnement des solutions de sécurité Azure intégrées et exécuter des tâches de gestion de base.

### <a name="connected-solutions"></a>Solutions connectées

La section **Solutions connectées** inclut les solutions de sécurité qui sont connectées à Security Center. Elle indique également l’état d’intégrité de chaque solution.  

![Solutions connectées](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

L’état d’une solution de partenaire peut être :

* Sain (vert) : aucun problème d’intégrité.
* Non sain (rouge) : problème d’intégrité nécessitant une action immédiate.
* Problèmes d’intégrité (orange) : état d’intégrité non signalé par la solution.
* Non signalé (gris) : la solution n’a encore rien signalé et aucune donnée d’intégrité n’est disponible. L’état d’une solution peut être non signalé si celle-ci a été récemment connectée et qu’elle est toujours en cours de déploiement.

> [!NOTE]
> Si les données sur l’état d’intégrité ne sont pas disponibles, Security Center affiche la date et l’heure du dernier événement reçu pour indiquer si la solution signale ou non des données. Si aucune donnée d’intégrité n’est disponible et qu’aucune alerte n’a été reçue au cours des 14 derniers jours, Security Center indique que la solution est non saine ou qu’elle ne signale pas de données.
>
>

1. Sélectionnez **AFFICHAGE** pour obtenir des informations et des options supplémentaires, par exemple :

   - **Console de solution**. Ouvre l’expérience de gestion pour cette solution.
   - **Associer la machine virtuelle**. Ouvre la page Associer les applications. Il vous permet de connecter des ressources à la solution de partenaire.
   - **Supprimer la solution**.
   - **Configurer**.

   ![Détail de la solution partenaire](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Solutions découvertes

Security Center découvre automatiquement les solutions exécutées dans Azure mais qui ne sont pas connectées à Security Center. Elles sont ensuite affichées dans la section **Solutions découvertes**. Ces solutions incluent les solutions Azure, telles qu’[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), et les solutions partenaires.

> [!NOTE]
> Le niveau Standard de Security Center est requis au niveau de l’abonnement pour la fonctionnalité de découverte des solutions. Consultez [Tarification](security-center-pricing.md) pour en savoir plus sur les niveaux tarifaires.
>
>

Sélectionnez **CONNECTER** sous une solution pour l’intégrer à Security Center et être notifié des alertes de sécurité.

### <a name="add-data-sources"></a>Ajouter des sources de données

La section **Ajouter des sources de données** comprend d’autres sources de données disponibles qui peuvent être connectées. Pour obtenir des instructions sur l’ajout de données à partir d’une de ces sources, cliquez sur **AJOUTER**.

![Sources de données](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Exportation de données vers un serveur SIEM

> [!NOTE]
> Pour plus d’informations sur une méthode plus simple (actuellement en préversion) pour l’exportation de données vers un SIEM, consultez [Exporter les alertes et recommandations de sécurité (préversion)](continuous-export.md). La nouvelle méthode n’utilise pas le journal d’activité comme intermédiation et autorise l’exportation directe de Security Center vers Event Hubs (puis vers votre SIEM). Elle prend également en charge l’exportation de recommandations de sécurité.


Vous pouvez configurer vos serveurs SIEM ou d’autres outils de supervision pour recevoir des événements d’Azure Security Center.

Tous les événements d’Azure Security Center sont publiés dans le [journal d’activité](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) Azure d’Azure Monitor. Azure Monitor utilise un [pipeline centralisé](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) pour acheminer en streaming les données vers un hub d’événements où elles peuvent ensuite être extraites dans votre outil de supervision.

Les sections suivantes expliquent comment configurer les données à diffuser vers un hub d’événements. Les étapes partent du principe qu’Azure Security Center est déjà configuré dans votre abonnement Azure.

### <a name="high-level-overview"></a>Vue d’ensemble globale

![Vue d’ensemble globale](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Quelles sont les données de sécurité Azure exposées à SIEM ?

Dans cette version, nous exposons les [alertes de sécurité](../security-center/security-center-managing-and-responding-alerts.md). Dans les versions à venir, nous enrichirons le jeu de données avec des recommandations de sécurité.

### <a name="how-to-set-up-the-pipeline"></a>Comment configurer le pipeline

#### <a name="create-an-event-hub"></a>Création d’un concentrateur d’événements

Avant de commencer, [créez un espace de noms Event Hubs](../event-hubs/event-hubs-create.md), la destination de toutes vos données de supervision.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Diffuser en continu le journal des activités Azure sur les Event Hubs

Consultez l’article [Acheminer le journal des activités Azure vers Event Hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md).

#### <a name="install-a-partner-siem-connector"></a>Installer un connecteur SIEM partenaire 

Le routage de vos données de monitoring vers un hub d’événement avec Azure Monitor vous permet d’intégrer facilement des systèmes SIEM et des outils de monitoring partenaires.

Pour connaître la liste des systèmes SIEM pris en charge, consultez [cet article](../azure-monitor/platform/resource-logs-stream-event-hubs.md#what-you-can-do-with-platform-logs-sent-to-an-event-hub).

### <a name="example-for-querying-data"></a>Exemple d’interrogation de données 

Voici quelques requêtes Splunk que vous pouvez utiliser pour extraire des données d’alerte :

| **Description de la requête** | **Requête** |
|----|----|
| Toutes les alertes| index=main Microsoft.Security/locations/alerts|
| Résumer le nombre d’opérations par leur nom| index=main sourcetype="amal:security" \| table operationName \| stats count by operationName|
| Obtenir les informations des alertes : heure, nom, état, ID et abonnement | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à intégrer des solutions de partenaires dans Security Center. Pour plus d’informations sur Security Center, consultez les articles suivants :

* [Surveillance de l’intégrité de la sécurité dans Security Center](security-center-monitoring.md). découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Questions fréquentes : Azure Security Center](security-center-faq.md). Obtenez des réponses aux questions fréquentes concernant l’utilisation de Security Center.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/). accédez à des billets de blog sur la sécurité et la conformité Azure.
