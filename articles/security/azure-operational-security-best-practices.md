---
title: Meilleures pratiques de sécurité pour protéger vos ressources, Microsoft Azure
description: Cet article fournit un ensemble de meilleures pratiques pour protéger vos données, applications et autres ressources dans Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 4a4677b5db730001df75d201d8e6d3149cb928e6
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409842"
---
# <a name="azure-operational-security-best-practices"></a>Bonnes pratiques pour Azure Operational Security
Cet article fournit un ensemble de meilleures pratiques pour protéger vos données, applications et autres ressources dans Azure.

Ces meilleures pratiques font l’objet d’un consensus et sont compatibles avec les capacités et fonctionnalités actuelles de la plateforme Azure. Les opinions et avis évoluent au fil du temps, et cet article est mis à jour régulièrement afin de refléter ces modifications.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Définir et déployer des pratiques de sécurité opérationnelle renforcée
La sécurité opérationnelle Azure fait référence aux services, contrôles et fonctionnalités auxquels les utilisateurs ont accès pour protéger leurs données, leurs applications et d’autres ressources dans Azure. La sécurité opérationnelle Azure repose sur un framework qui intègre les connaissances acquises via des fonctionnalités propres à Microsoft, notamment [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl), le programme [Centre de réponse aux problèmes de sécurité Microsoft](https://www.microsoft.com/msrc?rtc=1) et une connaissance approfondie du paysage des cybermenaces.

## <a name="manage-and-monitor-user-passwords"></a>Gérer et surveiller des mots de passe utilisateur
Le tableau suivant répertorie certaines meilleures pratiques relatives à la gestion des mots de passe utilisateur :

**Bonne pratique** : Assurez-vous de qu'avoir le niveau de protection de mot de passe approprié dans le cloud.   
**Détail** : Suivez les instructions de [conseils de mot de passe Microsoft](https://www.microsoft.com/research/publication/password-guidance/), qui est limité aux utilisateurs des plateformes d’identité Microsoft (compte Azure Active Directory, Active Directory et Microsoft).

**Bonne pratique** : Analyse des actions suspectes liées aux comptes des utilisateurs.   
**Détail** : Surveiller pour [les utilisateurs à risque](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk) et [risquées](../active-directory/reports-monitoring/concept-risk-events.md) en utilisant la sécurité d’Azure AD rapports.

**Bonne pratique** : Automatiquement détecter et corriger les mots de passe à haut risque.   
**Détail** : [Azure AD Identity Protection](../active-directory/identity-protection/overview.md) est une fonctionnalité de l’édition Azure AD Premium P2 qui vous permet de :

- Détecter les vulnérabilités potentielles qui affectent les identités de votre organisation
- Configurer des réponses automatiques aux actions suspectes détectées qui sont liées aux identités de votre organisation
- Examiner les incidents suspects et prendre les mesures appropriées pour les résoudre

## <a name="receive-incident-notifications-from-microsoft"></a>Recevoir des notifications d’incident de Microsoft
Assurez-vous que votre équipe d’opérations de sécurité reçoit des notifications d’incidents Azure de Microsoft. Permet d’une notification d’incident connaître de votre équipe de sécurité vous avez compromis des ressources Azure afin de pouvoir rapidement répondre aux et remédier aux risques de sécurité potentiels.

Dans le portail d’inscription Azure, vous pouvez vérifier les informations de contact admin incluent des détails qui informent les opérations de sécurité. Les informations de contact correspondent à une adresse électronique et à un numéro de téléphone.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Organisez les abonnements Azure en groupes d’administration
Si votre organisation dispose de plusieurs abonnements, vous pouvez avoir besoin d’un moyen de gérer efficacement l’accès, les stratégies et la conformité de ces abonnements. [Groupes d’administration Azure](../governance/management-groups/create.md) fournissent un niveau de portée qui est au-dessus des abonnements. Vous organisez les abonnements en conteneurs appelés groupes d’administration et que vous appliquez vos conditions de gouvernance aux groupes d’administration. Tous les abonnements d’un groupe d’administration héritent automatiquement des conditions appliquées à ce groupe d’administration.

Vous pouvez créer une structure flexible de groupes d’administration et des abonnements dans un répertoire. Chaque annuaire reçoit un groupe d’administration de niveau supérieur unique appelé le groupe d’administration racine. Ce groupe d’administration racine est intégré à la hiérarchie et contient tous les groupes d’administration et abonnements. Le groupe d’administration racine permet des stratégies globales et les affectations RBAC à appliquer au niveau du répertoire.

Voici quelques meilleures pratiques pour l’utilisation de groupes d’administration :

**Bonne pratique** : Assurez-vous que nouveaux abonnements ne s’applique les éléments de gouvernance tels que les stratégies et les autorisations qu’ils sont ajoutés.   
**Détail** : Utiliser le groupe d’administration racine pour affecter des éléments de sécurité d’entreprise qui s’appliquent à toutes les ressources Azure. Stratégies et les autorisations sont des exemples d’éléments.

**Bonne pratique** : Aligner les niveaux supérieurs des groupes d’administration avec la stratégie de segmentation pour fournir un point de contrôle et la stratégie de cohérence au sein de chaque segment.   
**Détail** : Créer un groupe d’administration unique pour chaque segment sous le groupe d’administration racine. Ne créez pas les autres groupes d’administration sous la racine.

**Bonne pratique** : Limiter la profondeur de groupe d’administration pour éviter toute confusion qui ralentit les opérations et sécurité.   
**Détail** : Limiter votre hiérarchie à trois niveaux, y compris la racine.

**Bonne pratique** : Sélectionnez avec soin les éléments à appliquer à toute l’entreprise avec le groupe d’administration racine.   
**Détail** : Vérifiez les éléments de groupe d’administration racine en ayez vraiment besoin de s’appliquer à chaque ressource et qu’ils sont peu d’impact.

Bons candidats sont les suivantes :

- Exigences réglementaires qui ont un impact professionnel clair (par exemple, les restrictions liées à la souveraineté des données)
- Affectent les exigences avec potentiel proche de zéro négatif sur les opérations, comme la stratégie avec le résultat d’audit ou RBAC assignations d’autorisations qui ont été révisées avec soin

**Bonne pratique** : Planifier et de tester scrupuleusement toutes les modifications d’échelle de l’entreprise sur le groupe d’administration racine avant de les appliquer (stratégie, modèle RBAC, etc.).   
**Détail** : Modifications dans le groupe d’administration racine peuvent affecter toutes les ressources sur Azure. Tout en fournissant un moyen puissant de garantir la cohérence au sein de l’entreprise, les erreurs ou une utilisation incorrecte peut nuire aux opérations de production. Toutes les modifications apportées au groupe d’administration racine de test dans un laboratoire de test ou de production pilote.

## <a name="streamline-environment-creation-with-blueprints"></a>Création simplifiée de l’environnement avec des plans
[Les plans Azure](../governance/blueprints/overview.md) service permet aux architectes de cloud et groupes de technologie d’informations centrale définir un ensemble reproductible de ressources Azure qui implémente et sont conformes aux normes, les modèles et les exigences d’une organisation. Plans Azure rend possible pour les équipes de développement pour rapidement créer et mettre en place des environnements avec un ensemble de composants intégrés et la confiance que qu’ils créent ces environnements au sein de la conformité de l’organisation.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Superviser les services de stockage pour détecter des changements inattendus du comportement
Il peut s’avérer plus complexe de diagnostiquer et de résoudre des problèmes dans une application distribuée hébergée dans un environnement cloud que dans des environnements traditionnels. Les applications peuvent être déployées dans une infrastructure PaaS ou IaaS, localement, sur un appareil mobile ou dans une combinaison de ces environnements. Le trafic réseau de votre application peut parcourir les réseaux publics et privés, et votre application peut utiliser différentes technologies de stockage.

Vous devez superviser en permanence les services de stockage utilisés par votre application afin de détecter tout changement de comportement inattendu (par exemple, des temps de réponse plus longs). Utilisez la journalisation pour collecter des données plus détaillées et analyser un problème en profondeur. Les informations de diagnostic obtenues par le biais de la supervision et de la journalisation vous aident à déterminer la cause première du problème rencontré par votre application. Vous pouvez alors résoudre le problème et déterminer la procédure appropriée pour y remédier.

[Azure Storage Analytics](../storage/storage-analytics.md) effectue la journalisation et fournit les données de métriques d’un compte de stockage Azure. Nous vous recommandons d’utiliser ces données pour suivre les requêtes, analyser les tendances d’utilisation et diagnostiquer les problèmes liés à votre compte de stockage.

## <a name="prevent-detect-and-respond-to-threats"></a>Prévenir, détecter et traiter les menaces
[Azure Security Center](../security-center/security-center-intro.md) vous aide à prévenir, détecter et répondre aux menaces en fournissant une meilleure visibilité sur (et contrôler) la sécurité de vos ressources Azure. Il fournit une supervision de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements Azure, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste ensemble de solutions de sécurité.

Le niveau gratuit de Security Center offre une sécurité limitée pour seulement vos ressources Azure. Le niveau Standard étend ces fonctions aux ressources locales et à d’autres clouds. Security Center Standard vous permet de rechercher et corriger les vulnérabilités de sécurité, appliquer des contrôles d’accès et d’application pour bloquer les activités malveillantes, détecter les menaces à l’aide d’analytique et analyse décisionnelle et répondre rapidement cas d’attaque. Vous pouvez essayer Security Center Standard gratuitement pendant 60 jours. Il est recommandé que vous [mettre à niveau votre abonnement Azure à Security Center Standard](../security-center/security-center-get-started.md).

Utiliser le centre de sécurité pour obtenir une vue centralisée de l’état de sécurité de toutes vos ressources Azure. Vérifiez d’un coup d’œil que les contrôles de sécurité appropriés sont en place et configurés correctement, et identifiez rapidement les ressources nécessitant votre attention.

Security Center s’intègre également avec [Windows Defender Advanced Threat Protection (ATP)](../security-center/security-center-wdatp.md), qui fournit des fonctionnalités complètes de détection du point de terminaison et de réponse (EDR). L’intégration de Windows Defender ATP vous permet de repérer les anomalies. Vous pouvez également détecter et répondre aux attaques avancées sur les points de terminaison serveurs surveillés par Security Center.

Presque toutes les entreprises ont un système sécurité de gestion (SIEM) événements et des informations pour aider à identifier les menaces émergentes en consolidant les informations du journal à partir de la collecte des signaux de divers appareils. Les journaux sont ensuite analysées par un système d’analytique de données pour aider à identifier ce qui est « intéressant » à partir du bruit est inévitable dans tous les journaux de collecte et les solutions d’analytique.

[Azure Sentinel](../sentinel/overview.md) est une solution de réponse (décoller) d’orchestration automatisée de sécurité et évolutive, cloud natives, événements et des informations gestion de la sécurité (SIEM). Sentinel Azure fournit intelligence analytique et les menaces de sécurité intelligentes via la détection des alertes, visibilité des menaces, chasse proactive et réponse aux menaces automatisée.

Voici quelques recommandations pour prévenir, détecter et répondre aux menaces :

**Bonne pratique** : Augmenter la vitesse et l’évolutivité de votre solution SIEM à l’aide d’un serveur SIEM de cloud.   
**Détail** : Examiner les fonctionnalités et capacités de [Azure Sentinel](../sentinel/overview.md) et de les comparer avec les capacités de ce que vous êtes actuellement à l’aide en local. Envisagez d’adopter Azure Sentinel si elle répond aux exigences de SIEM de votre organisation.

**Bonne pratique** : Rechercher les vulnérabilités de sécurité les plus graves avant de hiérarchiser investigation.   
**Détail** : Examinez votre [score sécurisé Azure](../security-center/security-center-secure-score.md) pour afficher les recommandations résultant les stratégies Azure et les initiatives intégrées à Azure Security Center. Ces recommandations peuvent aider aux risques principaux d’adresse tels que les mises à jour de sécurité, la protection de point de terminaison, chiffrement, configurations de sécurité, WAF manquant, machines virtuelles connectées à internet et bien plus encore.

Le score sécurisé, qui est basé sur le Centre pour les contrôles de Internet Security (CIS), vous permet d’évaluer la sécurité Azure de votre organisation par rapport à des sources externes. Validation externe permet de valider et d’enrichir la stratégie de sécurité de votre équipe.

**Bonne pratique** : Surveiller l’état de sécurité de machines, les réseaux, les services de stockage et de données et les applications de découvrir et de hiérarchiser les problèmes potentiels de sécurité.  
**Détail** : Suivez le [recommandations de sécurité](../security-center/security-center-recommendations.md) lors du démarrage de Security Center, avec les éléments de priorité la plus élevées.

**Bonne pratique** : Permet d’intégrer des alertes de Security Center dans votre solution de gestion (SIEM) des événements et des informations de sécurité.   
**Détail** : La plupart des organisations avec un serveur SIEM utilisez-le comme un centre d’échanges central pour les alertes de sécurité qui nécessitent une réponse de l’analyste. Événements traités produits par Security Center sont publiées dans le journal d’activité Azure, un des journaux disponibles via Azure Monitor. Azure Monitor offre un pipeline centralisé pour router les données de monitoring dans un outil SIEM. Consultez [intégrer des solutions de sécurité dans Security Center](../security-center/security-center-partner-integration.md#exporting-data-to-a-siem) pour obtenir des instructions. Si vous utilisez Azure Sentinel, consultez [Connect Azure Security Center](../sentinel/connect-azure-security-center.md).

**Bonne pratique** : Intégrer Azure journaux avec votre serveur SIEM.   
**Détail** : Utilisez [Azure Monitor pour collecter et exporter des données](../azure-monitor/overview.md#integrate-and-export-data). Cette pratique est critique pour l’activation d’investigation aux incidents de sécurité et de rétention du journal en ligne est limitée. Si vous utilisez Azure Sentinel, consultez [connecter des sources de données](../sentinel/connect-data-sources.md).

**Bonne pratique** : Accélérer votre investigation et les processus de chasse et réduire les faux positifs en intégrant des fonctionnalités de détection de point de terminaison et de réponse (EDR) dans votre examen de l’attaque.   
**Détail** : [Activer l’intégration de Windows Defender ATP](../security-center/security-center-wdatp.md#enable-windows-defender-atp-integration) par le biais de votre stratégie de sécurité de Security Center. Envisagez d’utiliser Azure Sentinel pour chasse de menace et de réponse aux incidents.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Superviser le réseau selon un scénario de bout en bout
Les clients créent un réseau de bout en bout dans Azure en combinant des ressources réseau comme un réseau virtuel, ExpressRoute, Application Gateway et des équilibreurs de charge. La surveillance est disponible sur chacune des ressources réseau.

[Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) est un service régional. Utilisez ses outils de diagnostic et de visualisation pour superviser et diagnostiquer les conditions au niveau d’un réseau, dans, vers et à partir d’Azure.

Voici des bonnes pratiques pour la supervision du réseau et les outils disponibles.

**Bonne pratique** : Automatisez la surveillance réseau à distance avec la capture de paquets.  
**Détail** : Surveillez et diagnostiquez les problèmes réseau sans vous connecter à vos machines virtuelles à l’aide de Network Watcher. Déclenchez la [capture de paquets](../network-watcher/network-watcher-alert-triggered-packet-capture.md) en définissant des alertes et bénéficiez d’un accès à des informations en temps réel sur le niveau de performance au niveau du paquet. Quand vous identifiez un problème, vous pouvez l’examiner en détail pour effectuer de meilleurs diagnostics.

**Bonne pratique** : Obtenez des insights sur votre trafic réseau en utilisant des journaux de flux.  
**Détail** : Développez une meilleure compréhension de vos modèles de trafic réseau à l’aide des [journaux de flux des groupes de sécurité réseau](../network-watcher/network-watcher-nsg-flow-logging-overview.md). Les informations contenues dans les journaux de flux vous aident à recueillir des données sur la conformité, l’audit et la supervision de votre profil de sécurité réseau.

**Bonne pratique** : Diagnostiquez les problèmes de connectivité d’un VPN.  
**Détail** : Utilisez Network Watcher pour [diagnostiquer les problèmes les plus courants liés aux connexions et à la passerelle VPN](../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Vous pouvez non seulement identifier le problème, mais également utiliser des journaux d’activité détaillés pour approfondir vos recherches.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Sécuriser le déploiement à l’aide d’outils DevOps éprouvés
Utilisez les bonnes pratiques DevOps suivantes pour garantir la productivité et l’efficacité de votre entreprise et de vos équipes.

**Bonne pratique** : Automatisez la génération et le déploiement des services.  
**Détail** : [L’infrastructure en tant que code](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) est un ensemble de techniques et de pratiques qui aident les professionnels de l’informatique à supprimer la charge de travail que représentent la génération et la gestion quotidiennes d’une infrastructure modulaire. Elle permet aux professionnels de l’informatique de générer et de gérer leur environnement serveur moderne d’une façon similaire à celle dont les développeurs de logiciels génèrent et gèrent le code de l’application.

Vous pouvez utiliser [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) pour provisionner vos applications à l’aide d’un modèle déclaratif. Dans un modèle unique, vous pouvez déployer plusieurs services ainsi que leurs dépendances. Le même modèle vous permet de déployer plusieurs fois votre application à chaque phase du cycle de vie de l’application.

**Bonne pratique** : Générez et déployez automatiquement des applications web Azure ou des services cloud.  
**Détail** : Vous pouvez configurer vos projets Azure DevOps à [générer et déployer automatiquement](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) pour les applications web Azure ou services cloud. Azure DevOps déploie automatiquement les fichiers binaires après avoir effectué une génération vers Azure après chaque archivage de code. Le processus de génération de package est équivalent à la commande Package de Visual Studio, et les étapes de la publication sont identiques à la commande Publier dans Visual Studio.

**Bonne pratique** : Automatiser la gestion de version.  
**Détail** : [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) est une solution pour automatiser le déploiement en plusieurs étapes et pour gérer le processus de mise en production. Créez des pipelines de déploiement gérés et continus pour des publications rapides, faciles et fréquentes. Avec Azure Pipelines, vous pouvez automatiser votre processus de mise en production et avoir des workflows d’approbation prédéfinis. Déployez localement et dans le cloud, étendez et personnalisez en fonction de vos besoins.

**Bonne pratique** : Vérifiez les performances de votre application avant de la lancer ou de déployer des mises à jour en production.  
**Détail** : Exécutez nuage [les tests de charge](https://docs.microsoft.com/azure/devops/test/load-test/overview.md?view=azure-devops#alternatives) à :

- Rechercher des problèmes de performances dans votre application.
- Améliorer la qualité du déploiement.
- Garantir que votre application est toujours disponible.
- Garantir que votre application peut gérer le trafic de votre prochaine campagne de lancement ou marketing.

[Apache JMeter](https://jmeter.apache.org/) est un outil gratuit, populaires ouvrir la source avec une forte Communauté sauvegarde.

**Bonne pratique** : Surveillez les performances des applications.  
**Détail** : [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) est un service extensible de gestion des performances des applications destiné aux développeurs web sur de multiples plateformes. Utilisez Application Insights pour superviser votre application web en direct. Il détecte automatiquement les problèmes de performances. Il intègre des outils d’analyse pour vous aider à diagnostiquer les problèmes et à comprendre ce que font les utilisateurs avec votre application. Il a été conçu pour vous permettre d’améliorer continuellement les performances et la convivialité.

## <a name="mitigate-and-protect-against-ddos"></a>Prévenir les attaques DDoS et s’en protéger
DDoS (Distributed Denial of Service, déni de service distribué) est un type d’attaque qui tente d’épuiser les ressources d’une application. Son objectif est d’affecter la disponibilité de l’application et sa capacité à gérer des demandes légitimes. Ces attaques de plus en plus sophistiquées gagnent en importance et en impact. Elles peuvent être ciblées sur n’importe quel point de terminaison qui est publiquement accessible via Internet.

La conception et la génération d’une résilience DDoS nécessitent la planification et la conception de divers modes d’échec. Voici les bonnes pratiques relatives à la création de services résistants aux attaques DDoS sur Azure.

**Bonne pratique** : Assurez-vous que la sécurité est une priorité tout au long du cycle de vie d’une application, de la conception et de l’implémentation au déploiement et aux opérations. Les applications peuvent contenir des bogues qui laissent un volume relativement faible de requêtes conçues utiliser beaucoup de ressources, ce qui entraîne une interruption de service.  
**Détail** : Pour permettre de protéger un service fonctionnant sur Microsoft Azure, vous devez bien comprendre l’architecture de votre application et respecter les [cinq piliers de la qualité logicielle](https://docs.microsoft.com/azure/architecture/guide/pillars). Vous devez avoir connaissance des volumes de trafic habituels, du modèle de connectivité entre l’application et d’autres applications, et des points de terminaison de service exposés à l’Internet public.

Il est extrêmement important que vous conceviez une application suffisamment résiliente pour surmonter une attaque ciblée par déni de service. La plateforme Azure intègre des fonctionnalités de sécurité et de confidentialité, à commencer par [SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl). SDL tient compte de la sécurité à chaque phase de développement et vérifie qu’Azure est continuellement mis à jour pour le rendre encore plus sécurisé.

**Bonne pratique** : Concevez vos applications de sorte qu’elles puissent être [mises à l’échelle horizontalement](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) pour répondre à la demande d’une charge amplifiée, en particulier dans le cadre d’une attaque DDoS. Si votre application repose sur une seule instance unique d’un service, cela crée un point de défaillance unique. L’approvisionnement de plusieurs instances rend votre système plus résilient et plus évolutif.  
**Détail** : Pour [Azure App Service](../app-service/app-service-value-prop-what-is.md), sélectionnez un [plan App Service](../app-service/overview-hosting-plans.md) qui offre plusieurs instances.

Pour les Azure Cloud Services, configurez chacun de vos rôles de manière à utiliser [plusieurs instances](../cloud-services/cloud-services-choose-me.md).

Pour [Machines virtuelles Azure](../virtual-machines/windows/overview.md), vérifiez que votre architecture de machine virtuelle inclut plusieurs machines virtuelles et que chaque machine virtuelle est dans un [groupe à haute disponibilité](../virtual-machines/virtual-machines-windows-manage-availability.md). Nous vous recommandons d’utiliser des groupes de machines virtuelles identiques pour les fonctionnalités de mise à l’échelle automatique.

**Bonne pratique** : Le fait de superposer des défenses dans une application réduit les chances de réussite d’une attaque. Implémentez des conceptions sécurisées pour vos applications à l’aide des fonctionnalités intégrées à la plateforme Azure.  
**Détail** : Plus la taille (surface d’exposition) de l’application est importante, plus le risque d’attaque est élevé. Vous pouvez réduire la surface d’exposition en créant des listes vertes permettant de limiter l’espace d’adressage IP exposé et les ports d’écoute qui ne sont pas nécessaires sur les équilibreurs de charge ([Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) et [Azure Application Gateway](../application-gateway/application-gateway-create-probe-portal.md)).

Les [groupes de sécurité réseau](../virtual-network/security-overview.md) permettent également de réduire la surface d’attaque. Vous pouvez utiliser des [balises de service](../virtual-network/security-overview.md#service-tags) et des [groupes de sécurité d’application](../virtual-network/security-overview.md#application-security-groups) pour simplifier la création de règles de sécurité et configurer la sécurité réseau comme prolongement naturel de la structure d’une application.

Vous devez déployer les services Azure dans un [réseau virtuel](../virtual-network/virtual-networks-overview.md) dans la mesure du possible. Les ressources de service peuvent ainsi communiquer par le biais d’adresses IP privées. Le trafic du service Azure à partir d’un réseau virtuel utilise des adresses IP publiques comme adresses IP source par défaut.

Le fait d’utiliser des [points de terminaison de service](../virtual-network/virtual-network-service-endpoints-overview.md) force le trafic de service à utiliser des adresses privées de réseau virtuel comme adresses IP sources lors de l’accès au service Azure à partir d’un réseau virtuel.

Il arrive fréquemment que les ressources locales d’un client soient attaquées en même temps que ses ressources dans Azure. Si vous connectez un environnement local à Azure, réduisez l’exposition des ressources locales à l’Internet public.

Azure propose deux [offres de service](../virtual-network/ddos-protection-overview.md) DDoS qui fournissent une protection contre les attaques réseau :

- La protection de base est intégrée à Azure par défaut sans coût supplémentaire. De par son échelle et sa capacité, le réseau Azure déployé à l’échelle mondiale assure une défense contre les attaques courantes de la couche réseau. Cette défense est par ailleurs renforcée par le monitoring continu du trafic et l’atténuation en temps réel. La protection de base ne nécessite aucun changement de la part de l’utilisateur au niveau de configuration ou de l’application et permet de protéger tous les services Azure, notamment les services PaaS comme Azure DNS.
- La protection standard fournit des fonctions d’atténuation DDoS avancées contre les attaques réseau. Cette solution s’adapte automatiquement pour protéger vos ressources Azure spécifiques. La protection est facile à activer pendant la création de réseaux virtuels. Vous pouvez également l’activer après la création ; aucun changement au niveau de l’application ou de la ressource ne s’impose.

## <a name="enable-azure-policy"></a>Activer la stratégie Azure
[Azure Policy](../governance/policy/overview.md) est un service dans Azure qui vous permet de créer, assigner et gérer les stratégies. Ces règles appliquent les règles et des effets sur vos ressources, afin que ces ressources restent conformes à vos normes de l’entreprise et les contrats de niveau de service. en évaluant vos ressources pour vérifier leur conformité par rapport aux stratégies affectées.

Activer la stratégie Azure surveiller et appliquer la stratégie écrite de votre organisation. Cela garantit la conformité avec votre entreprise ou des exigences de sécurité réglementaires par la gestion centralisée des stratégies de sécurité sur vos charges de travail de cloud hybride. Découvrez comment [créer et gérer des stratégies pour appliquer la conformité](../governance/policy/tutorials/create-and-manage.md). Consultez [structure de définition Azure Policy](../governance/policy/concepts/definition-structure.md) pour une vue d’ensemble des éléments d’une stratégie.

Voici quelques meilleures pratiques à suivre une fois que vous adoptez Azure Policy :

**Bonne pratique** : Stratégie prend en charge plusieurs types d’effets. Vous pouvez lire les concernant dans [structure de définition Azure Policy](../governance/policy/concepts/definition-structure.md#policy-rule). Opérations de l’entreprise peuvent être affectées par la **refuser** effet et le **corriger** effet, donc commencer par le **d’audit** effet pour limiter le risque d’impact négatif à partir de stratégie.   
**Détail** : [Démarrer les déploiements de stratégie en mode audit](../governance/policy/concepts/definition-structure.md#policy-rule) , puis passer ultérieurement à **refuser** ou **corriger**. Tester et passez en revue les résultats de l’effet d’audit avant de passer à **refuser** ou **corriger**.

Pour plus d’informations, consultez [créer et gérer des stratégies pour appliquer la conformité](../governance/policy/tutorials/create-and-manage.md).

**Bonne pratique** : Identifier les rôles de responsables pour l’analyse des violations de stratégie et en garantissant l’action corrective appropriée est effectuée rapidement.   
**Détail** : Ayant la conformité de moniteur de rôle attribué par le biais du [portail Azure](../governance/policy/how-to/get-compliance-data.md#portal) ou via le [ligne de commande](../governance/policy/how-to/get-compliance-data.md#command-line).

**Bonne pratique** : Azure Policy est une représentation sous forme de technique de stratégies écrites d’une organisation. Mappez toutes les stratégies Azure des stratégies d’organisation pour éviter la confusion et améliorer la cohérence.   
**Détail** : Mappage de document dans la documentation de votre organisation ou dans la stratégie Azure en ajoutant une référence à la stratégie d’organisation dans Azure [description de la stratégie](../governance/policy/concepts/definition-structure.md#display-name-and-description) ou la stratégie Azure [initiative](../governance/policy/concepts/definition-structure.md#initiatives) Description.

## <a name="monitor-azure-ad-risk-reports"></a>Rapports de risque d’analyse Azure AD
La grande majorité des violations de sécurité ont lieu lorsque des cybercriminels parviennent à accéder à un environnement en volant l’identité d’un utilisateur. Détecter les identités compromises n’est pas chose aisée. Azure AD utilise les algorithmes Machine Learning et des modèles heuristiques adaptatifs pour détecter les actions suspectes liées aux comptes de votre utilisateur. Chaque action suspecte détectée est stockée dans un enregistrement appelé [événement à risque](../active-directory/reports-monitoring/concept-risk-events.md). Événements à risque sont enregistrés dans la sécurité Azure AD rapports. Pour plus d’informations, consultez le [sur le rapport de sécurité des utilisateurs](../active-directory/reports-monitoring/concept-user-at-risk.md) et le [rapport de sécurité des connexions risquées](../active-directory/reports-monitoring/concept-risky-sign-ins.md).

## <a name="next-steps"></a>Étapes suivantes
Consultez l’article [Bonnes pratiques et tendances Azure relatives à la sécurité](security-best-practices-and-patterns.md) pour découvrir d’autres bonnes pratiques en matière de sécurité à appliquer dans le cadre de la conception, du déploiement et de la gestion de vos solutions cloud avec Azure.

Les ressources suivantes fournissent des informations générales sur la sécurité Azure et les services Microsoft associés :
* [Blog de l’équipe de sécurité Azure](https://blogs.msdn.microsoft.com/azuresecurity/) : pour obtenir des informations à jour sur les dernières actualités sur la sécurité Azure
* [Centre de réponse aux problèmes de sécurité Microsoft](https://technet.microsoft.com/library/dn440717.aspx) : emplacement où les vulnérabilités de sécurité Microsoft, dont les problèmes rencontrés avec Azure, peuvent être rapportées ou signalées par e-mail à l’adresse secure@microsoft.com
