---
title: Utiliser Azure Private Link pour connecter en toute sécurité des réseaux à Azure Monitor
description: Utiliser Azure Private Link pour connecter en toute sécurité des réseaux à Azure Monitor
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: cf1e471144ec901f82cae1217921ad0eac29b2ae
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111953254"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Utiliser Azure Private Link pour connecter en toute sécurité des réseaux à Azure Monitor

[Azure Private Link](../../private-link/private-link-overview.md) vous permet de lier en toute sécurité les services PaaS Azure à votre réseau virtuel à l’aide de points de terminaison privés. Pour de nombreux services, il vous suffit de configurer un point de terminaison par ressource. Toutefois, Azure Monitor est une constellation de différents services interconnectés qui fonctionnent ensemble pour surveiller vos charges de travail. Nous avons ainsi créé une ressource appelée Azure Monitor Private Link Scope (AMPLS). AMPLS vous permet de définir les limites de votre réseau de surveillance et de vous connecter à votre réseau virtuel. Cet article explique quand utiliser et comment configurer une étendue de liaison privée Azure Monitor.

## <a name="advantages"></a>Avantages

Grâce à Azure Private Link, vous pouvez :

- Vous connecter en privé à Azure Monitor sans ouvrir d’accès au réseau public
- Vérifier que vos données de surveillance sont accessibles uniquement par le biais de réseaux privés autorisés
- Empêcher l’exfiltration de données à partir de vos réseaux privés en définissant les ressources Azure Monitor spécifiques qui se connectent via votre point de terminaison privé
- Connecter en toute sécurité votre réseau local privé à Azure Monitor à l’aide d’ExpressRoute et d’Azure Private Link
- Conserver tout le trafic au sein du réseau principal Microsoft Azure

Pour plus d’informations, consultez [Principaux avantages d’Azure Private Link](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Fonctionnement

Azure Monitor Private Link Scope (AMPLS) connecte des points de terminaison privés (et les réseaux virtuels dans lesquels ils se trouvent) à une ou plusieurs ressources Azure Monitor : des espaces de travail Log Analytics et des composants Application Insights.

![Schéma de la topologie de base des ressources](./media/private-link-security/private-link-basic-topology.png)

* Le point de terminaison privé de votre réseau virtuel lui permet d’atteindre des points de terminaison Azure Monitor par le biais d’adresses IP privées à partir du pool de votre réseau, au lieu d’utiliser les adresses IP publiques de ces points de terminaison. Cela vous permet de continuer à utiliser vos ressources Azure Monitor sans ouvrir votre réseau virtuel à un trafic sortant non requis. 
* Le trafic du point de terminaison privé vers vos ressources Azure Monitor passe par le réseau principal Microsoft Azure et n’est pas acheminé vers des réseaux publics. 
* Vous pouvez configurer chacun de vos espaces de travail ou composants afin d’autoriser ou de refuser l’ingestion et les requêtes provenant de réseaux publics. Vous bénéficiez ainsi d’une protection au niveau des ressources, ce qui vous permet de contrôler le trafic vers des ressources spécifiques.

> [!NOTE]
> Une seule ressource Azure Monitor peut appartenir à plusieurs AMPLS, mais vous ne pouvez pas connecter un réseau virtuel unique à plusieurs AMPLS. 

## <a name="planning-your-private-link-setup"></a>Planification de votre configuration Private Link

Avant de configurer Azure Monitor Private Link, prenez en compte la topologie de votre réseau, et en particulier votre topologie de routage DNS. 

### <a name="the-issue-of-dns-overrides"></a>Le problème des remplacements DNS
Certains services Azure Monitor utilisent des points de terminaison globaux, ce qui signifie qu’ils traitent les demandes ciblant n’importe quel espace de travail/composant. Voici deux exemples : le point de terminaison d’ingestion Application Insights, et le point de terminaison Application Insights et Log Analytics.

Lorsque vous configurez une connexion Private Link, votre DNS est mis à jour pour mapper des points de terminaison Azure Monitor à des adresses IP privées à partir de la plage d’adresses IP de votre réseau virtuel. Cette modification remplace tout mappage précédent de ces points de terminaison, ce qui peut avoir des implications significatives, présentées ci-dessous. 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Monitor Private Link s’applique à toutes les ressources Azure Monitor, avec la règle « Tout ou rien »
Étant donné que certains points de terminaison Azure Monitor sont globaux, il est impossible de créer une connexion Private Link pour un composant ou un espace de travail spécifique. Au lieu de cela, lorsque vous configurez Private Link vers un seul composant Application Insights ou un espace de travail Log Analytics, vos enregistrements DNS sont mis à jour pour **tous** les composants Application Insights. Toute tentative d’ingestion ou d’interrogation d’un composant passera par la connexion Private Link et risque d’échouer. En ce qui concerne Log Analytics, les points de terminaison d’ingestion et de configuration sont spécifiques à l’espace de travail, ce qui signifie que la configuration de la liaison privée s’applique uniquement aux espaces de travail spécifiés. L’ingestion et la configuration d’autres espaces de travail seront dirigées vers les points de terminaison Log Analytics publics par défaut.

![Schéma des remplacements DNS dans un même réseau virtuel](./media/private-link-security/dns-overrides-single-vnet.png)

Cela est vrai non seulement pour un réseau virtuel spécifique, mais pour tous les réseaux virtuels qui partagent le même serveur DNS (voir [Le problème des remplacements DNS](#the-issue-of-dns-overrides)). Par exemple, la demande d’ingestion des journaux vers n’importe quel composant Application Insights sera toujours envoyée via l’itinéraire Private Link. Les composants qui ne sont pas liés à AMPLS ne pourront pas valider Private Link et échoueront.

> [!NOTE]
> Pour conclure : une fois que vous avez configuré une connexion Private Link vers une ressource unique, cette configuration s’applique à toutes les ressources Azure Monitor de votre réseau. Pour les ressources Application Insights, il s’agit de « tout ou rien ». Cela signifie que vous devez ajouter toutes les ressources Azure Monitor de votre réseau à votre AMPLS ou aucune d’entre elles.
> 
> Pour gérer les risques liés d’exfiltration des données, nous vous recommandons d’ajouter toutes les ressources Application Insights et Log Analytics à votre AMPLS et de bloquer le trafic de sortie de vos réseaux autant que possible.

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor Private Link s’applique à l’ensemble de votre réseau
Certains réseaux sont composés de plusieurs réseaux virtuels. Si les réseaux virtuels utilisent le même serveur DNS, ils remplacent leurs mappages DNS mutuels et peuvent éventuellement rompre leur communication avec Azure Monitor (consultez [Le problème des remplacements DNS](#the-issue-of-dns-overrides)). En fin de compte, seul le dernier réseau virtuel sera en mesure de communiquer avec Azure Monitor, puisque le DNS mappera les points de terminaison Azure Monitor à des adresses IP privées de la plage de ce réseau virtuel (qui peuvent ne pas être accessibles à partir d’autres réseaux virtuels).

![Schéma des remplacements DNS dans plusieurs réseaux virtuels](./media/private-link-security/dns-overrides-multiple-vnets.png)

Dans le diagramme ci-dessus, VNet 10.0.1.x se connecte d’abord à AMPLS1 puis mappe les points de terminaison globaux Azure Monitor aux adresses IP à partir de sa plage. Par la suite, VNet 10.0.2.x se connecte à AMPLS2 et remplace le mappage DNS des *mêmes points de terminaison globaux* par les adresses IP de sa plage. Comme ces réseaux virtuels ne sont pas appairés, le premier réseau virtuel ne parvient pas à atteindre ces points de terminaison.

> [!NOTE]
> Pour conclure : La configuration AMPLS affecte tous les réseaux qui partagent les mêmes zones DNS. Pour éviter de remplacer les mappages de points de terminaison DNS de chacun des réseaux, il est préférable d’installer un point de terminaison privé unique sur un réseau appairé (par exemple, un réseau virtuel hub) ou de séparer les réseaux au niveau du DNS (par exemple à l’aide de redirecteurs DNS ou de serveurs DNS totalement distincts).

### <a name="hub-spoke-networks"></a>Réseaux hub-and-spoke
Les topologies hub-and-spoke peuvent éviter le problème des remplacements DNS en définissant la liaison privée sur le réseau virtuel hub (principal) et non sur chaque réseau virtuel spoke. Cette configuration s’avère particulièrement utile si les ressources Azure Monitor utilisées par ces réseaux virtuels spoke sont partagées. 

![Point de terminaison privé hub-and-spoke](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> Vous pouvez intentionnellement préférer créer des connexions Private Link distinctes pour vos réseaux virtuels spoke, par exemple pour permettre à chaque réseau virtuel d’accéder à un ensemble limité de ressources de surveillance. Dans ce cas, vous pouvez créer un point de terminaison privé dédié et un AMPLS pour chaque réseau virtuel, mais vous devez également vérifier qu’ils ne partagent pas les mêmes zones DNS afin d’éviter les remplacements DNS.


### <a name="consider-limits"></a>Tenir compte des limites

Comme indiqué dans les [restrictions et limitations](#restrictions-and-limitations), l’objet AMPLS comporte un certain nombre de limites, comme indiqué dans la topologie ci-dessous :
* Chaque réseau virtuel se connecte uniquement à **1** objet AMPLS.
* AMPLS B est connecté aux points de terminaison privés de deux réseaux virtuels (VNet2 et VNet3) en utilisant 2 de ses 10 connexions de points de terminaison privés possibles.
* AMPLS A se connecte à deux espaces de travail et à un composant Application Insights en utilisant 3 de ses 50 connexions de ressources Azure Monitor possibles.
* Workspace2 se connecte à AMPLS A et AMPLS B : il utilise 2 de ses 5 connexions AMPLS possibles.

![Diagramme des limites d’AMPLS](./media/private-link-security/ampls-limits.png)

> [!NOTE]
> Si vous utilisez des solutions Log Analytics qui requièrent un compte Automation, comme Update Management, Change Tracking ou Inventory, vous devez également configurer une liaison privée distincte pour votre compte Automation. Pour plus d’informations, consultez [Utiliser Azure Private Link pour connecter en toute sécurité des réseaux à Azure Automation](../../automation/how-to/private-link-security.md).


## <a name="example-connection"></a>Exemple de connexion

Commencez par créer une ressource d’étendue de liaison privée Azure Monitor.

1. Accédez à **Créer une ressource** dans le portail Azure et recherchez **Étendue de liaison privée Azure Monitor**.

   ![Rechercher l’étendue de liaison privée Azure Monitor](./media/private-link-security/ampls-find-1c.png)

2. Sélectionnez **Créer**.
3. Choisissez un abonnement et un groupe de ressources.
4. Donnez un nom à l’AMPLS. Il est préférable d’utiliser un nom explicite et clair, par exemple « AppServerProdTelem ».
5. Sélectionnez **Vérifier + créer**. 

   ![Créer une étendue de liaison privée Azure Monitor](./media/private-link-security/ampls-create-1d.png)

6. Laissez le processus de validation se terminer, puis sélectionnez **Créer**.

### <a name="connect-azure-monitor-resources"></a>Connecter des ressources Azure Monitor

Connectez des ressources Azure Monitor (espaces de travail Log Analytics et composants Application Insights) à votre AMPLS.

1. Dans votre étendue de liaison privée Azure Monitor, sélectionnez **Ressources Azure Monitor** dans le menu de gauche. Sélectionnez le bouton **Ajouter**.
2. Ajoutez l’espace de travail ou le composant. Le fait de sélectionner sur le bouton **Ajouter** affiche une boîte de dialogue dans laquelle vous pouvez choisir des ressources Azure Monitor. Vous pouvez parcourir vos abonnements et groupes de ressources, ou vous pouvez saisir leur nom pour les filtrer. Choisissez l’espace de travail ou le composant, puis sélectionnez **Appliquer** pour les ajouter à votre étendue.

    ![Capture d’écran de l’expérience utilisateur Sélectionner une étendue](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Pour supprimer des ressources Azure Monitor, vous devez d’abord les déconnecter des objets AMPLS auxquels elles sont connectées. Vous ne pouvez pas supprimer des ressources connectées à un AMPLS.

### <a name="connect-to-a-private-endpoint"></a>Se connecter à un point de terminaison privé

Maintenant que vous disposez de ressources connectées à votre AMPLS, créez un point de terminaison privé pour connecter notre réseau. Vous pouvez effectuer cette tâche dans le [centre Azure Private Link sur le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints) ou au sein même de votre étendue de liaison privée Azure Monitor, comme dans cet exemple.

1. Dans votre ressource d’étendue, sélectionnez **Connexions de point de terminaison privé** dans le menu gauche de la ressource. Sélectionnez **Point de terminaison privé** pour démarrer le processus de création du point de terminaison. Vous pouvez également approuver ici des connexions qui ont été démarrées dans le centre Azure Private Link en les sélectionnant puis en choisissant **Approuver**.

    ![Capture d’écran de l’expérience utilisateur Connexions de point de terminaison privés](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Choisissez l’abonnement, le groupe de ressources et le nom du point de terminaison, ainsi que la région dans laquelle il doit résider. La région doit être la même que celle du réseau virtuel auquel vous vous connectez.

3. Sélectionnez **Suivant : Ressource**. 

4. Dans l’écran Ressource :

   a. Sélectionnez l’**abonnement** qui contient votre ressource d’étendue de liaison privée Azure Monitor. 

   b. Pour le **type de ressource**, choisissez **Microsoft.insights/privateLinkScopes**. 

   c. Dans la liste déroulante des **ressources**, choisissez l’étendue de liaison privée que vous avez créée précédemment. 

   d. Sélectionnez **Suivant : Configuration >** .
      ![Screenshot of select Create Private Endpoint](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. Dans le volet configuration :

   a.    Choisissez le **réseau virtuel** et le **sous-réseau** que vous souhaitez connecter à vos ressources Azure Monitor. 
 
   b.    Choisissez **Oui** pour **Intégrer à une zone DNS privée**, et laissez-le créer automatiquement une nouvelle zone DNS privée. Les zones DNS réelles peuvent différer de ce que montre la capture d’écran ci-dessous. 
   > [!NOTE]
   > Si vous choisissez **Non** et préférez gérer les enregistrements DNS manuellement, commencez par configurer votre liaison privée, y compris ce point de terminaison privé et la configuration AMPLS. Ensuite, configurez votre DNS conformément aux instructions de la rubrique [Configuration DNS des points de terminaison privés Azure](../../private-link/private-endpoint-dns.md). Veillez à ne pas créer d’enregistrements vides en préparation de votre configuration de liaison privée. Les enregistrements DNS que vous créez peuvent remplacer les paramètres existants et affecter votre connectivité avec Azure Monitor.
 
   c.    Sélectionnez **Revoir + créer**.
 
   d.    Laissez le processus de validation se terminer. 
 
   e.    Sélectionnez **Create** (Créer). 

    ![Capture d’écran de détails de la création d’un point de terminaison privé.](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Vous avez maintenant créé un nouveau point de terminaison privé connecté à cet AMPLS.

## <a name="review-and-validate-your-private-link-setup"></a>Réviser et valider la configuration de votre liaison privée

### <a name="reviewing-your-endpoints-dns-settings"></a>Examen des paramètres DNS de votre point de terminaison
Le point de terminaison privé que vous avez créé devrait maintenant avoir cinq zones DNS configurées :

* privatelink-monitor-azure-com
* privatelink-oms-opinsights-azure-com
* privatelink-ods-opinsights-azure-com
* privatelink-agentsvc-azure-automation-net
* privatelink-blob-core-windows-net

> [!NOTE]
> Chacune de ces zones mappe des points de terminaison Azure Monitor spécifiques à des adresses IP privées à partir du pool d’adresses IP du réseau virtuel. Les adresses IP affichées dans les images ci-dessous ne sont que des exemples. À la place, votre configuration devrait afficher des adresses IP privées de votre propre réseau.

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-monitor-azure-com
Cette zone couvre les points de terminaison globaux qu’Azure Monitor utilise, ce qui signifie que ces points de terminaison servent les demandes en prenant en compte toutes les ressources, pas une ressource spécifique. Cette zone devrait avoir des points de terminaison mappés pour :
* `in.ai` : point de terminaison d’ingestion Application Insights (une entrée mondiale et une entrée régionale)
* `api` : point de terminaison d’API Application Insights et Log Analytics
* `live` : point de terminaison de métriques en direct Application Insights
* `profiler` : point de terminaison du profileur Application Insights
* `snapshot` : point de terminaison de capture instantanée Application Insights[![Capture d’écran de la zone DNS privée monitor-azure-com.](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-oms-opinsights-azure-com
Cette zone couvre le mappage spécifique de l’espace de travail aux points de terminaison OMS. Vous devriez voir une entrée pour chaque espace de travail lié à l’AMPLS connecté avec ce point de terminaison privé.
[![Capture d’écran de la zone DNS privée oms-opinsights-azure-com.](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ods-opinsights-azure-com
Cette zone couvre le mappage spécifique de l’espace de travail aux points de terminaison ODS, point de terminaison d’ingestion de Log Analytics. Vous devriez voir une entrée pour chaque espace de travail lié à l’AMPLS connecté avec ce point de terminaison privé.
[![Capture d’écran de la zone DNS privée ods-opinsights-azure-com.](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentsvc-azure-automation-net
Cette zone couvre le mappage spécifique de l’espace de travail aux points de terminaison d’automatisation du service agent. Vous devriez voir une entrée pour chaque espace de travail lié à l’AMPLS connecté avec ce point de terminaison privé.
[![Capture d’écran de l’agent de zone DNS privée svc-azure-automation-net.](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

#### <a name="privatelink-blob-core-windows-net"></a>privatelink-blob-core-windows-net
Cette zone configure la connectivité au compte de stockage des packs de solutions des agents globaux. Par son intermédiaire, les agents peuvent télécharger des packs de solutions (également appelés packs d’administration) nouveaux ou mis à jour. Une seule entrée est requise pour gérer les agents Log Analytics, quel que soit le nombre d’espaces de travail utilisés.
[![Capture d’écran de la zone DNS privée blob-core-windows-net.](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net.png)](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net-expanded.png#lightbox)
> [!NOTE]
> Cette entrée est uniquement ajoutée aux configurations Private Link créées à partir du 19 avril 2021 (ou de juin 2021 sur les clouds souverains Azure).


### <a name="validating-you-are-communicating-over-a-private-link"></a>Validation que vous communiquez sur une liaison privée
* Pour valider le fait que vos demandes sont désormais envoyées via le point de terminaison privé, vous pouvez les examiner avec un outil de suivi du réseau ou même votre navigateur. Par exemple, lorsque vous tentez d’interroger votre espace de travail ou votre application, assurez-vous que la demande est envoyée à l’adresse IP privée mappée au point de terminaison de l’API, dans cet exemple, *172.17.0.9*.

    Remarque : certains navigateurs peuvent utiliser d’autres paramètres DNS (consultez [Paramètres DNS du navigateur](#browser-dns-settings)). Vérifiez que vos paramètres DNS s’appliquent.

* Pour vous assurer que votre espace de travail ou composant ne reçoivent pas de demandes de réseaux publics (non connectés via AMPLS), définissez les indicateurs de requête et d’ingestion publique de la ressource sur *Non*, comme expliqué dans [Gérer l’accès depuis l’extérieur des étendues de liaisons privées](#manage-access-from-outside-of-private-links-scopes).

* À partir d’un client sur votre réseau protégé, utilisez `nslookup` sur l’un des points de terminaison répertoriés dans vos zones DNS. La demande devrait être résolue par votre serveur DNS en adresses IP privées mappées au lieu des adresses IP publiques utilisées par défaut.


## <a name="configure-log-analytics"></a>Configurer Log Analytics

Accédez au portail Azure. Dans le menu de votre ressource d’espace de travail Log Analytics se trouve une option appelée **Isolement réseau** sur le côté gauche. Vous pouvez contrôler deux états différents à partir de ce menu.

![Isolement réseau LA](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Étendues de liaison privée Azure Monitor connectées
Toutes les étendues connectées à l’espace de travail apparaissent dans cet écran. La connexion aux étendues (AMPLS) autorise le trafic réseau à partir du réseau virtuel connecté à chaque AMPLS pour atteindre cet espace de travail. La création d’une connexion de cette manière a le même effet que si la connexion partait de l’étendue, comme c’est la cas dans la section [Connecter des ressources Azure Monitor](#connect-azure-monitor-resources). Pour ajouter une nouvelle connexion, sélectionnez **Ajouter**, puis choisissez l’étendue de liaison privée Azure Monitor. Sélectionnez **Appliquer** pour la connecter. Notez qu’un espace de travail peut se connecter à 5 objets AMPLS, comme mentionné dans [Restrictions et limitations](#restrictions-and-limitations). 

### <a name="manage-access-from-outside-of-private-links-scopes"></a>Gérer l’accès depuis l’extérieur d’étendues de liaisons privées
Les paramètres de la partie inférieure de cette page contrôlent l'accès depuis les réseaux publics, c'est-à-dire les réseaux non connectés aux étendues répertoriées. Si vous définissez **Autoriser l’accès au réseau public pour l’ingestion** sur **Non**, vous bloquez l’ingestion des journaux à partir des machines en dehors des étendues connectées. Si vous définissez **Autoriser l’accès au réseau public pour les requêtes** sur **Non**, vous bloquez les requêtes provenant des machines en dehors des étendues. Cela inclut les requêtes exécutées via des classeurs, les tableaux de bord, les expériences client basées sur l’API, les informations sur le portail Azure et bien plus encore. Les expériences qui s’exécutent en dehors du portail Azure et qui interrogent des données Log Analytics doivent également être exécutées au sein du réseau virtuel connecté par liaison privée.

### <a name="exceptions"></a>Exceptions
La restriction de l’accès comme expliqué ci-dessus ne s’applique pas à Azure Resource Manager et présente donc les limitations suivantes :
* Accès aux données : si des requêtes de blocage/d’autorisation à partir de réseaux publics s’appliquent à la plupart des expériences Log Analytics, certaines expériences interrogent les données via Azure Resource Manager et ne peuvent donc pas interroger les données, sauf si les paramètres de liaison privée sont également appliqués au gestionnaire des ressources (fonctionnalité disponible prochainement). Il s’agit, par exemple, des solutions Azure Monitor, des classeurs et insights, et du connecteur LogicApp.
* Gestion de l’espace de travail : les modifications de paramètre et de configuration, (notamment l’activation ou la désactivation de ces paramètres d’accès), sont gérées par Azure Resource Manager. Limitez l’accès à la gestion de l’espace de travail à l’aide des rôles, autorisations, contrôles réseau et audits appropriés. Pour plus d’informations, consultez [Rôles, autorisations et sécurité Azure Monitor](../roles-permissions-security.md).

> [!NOTE]
> Les journaux et les métriques chargés sur un espace de travail par le biais des [paramètres de diagnostic](../essentials/diagnostic-settings.md) passent par un canal privé sécurisé de Microsoft et ne sont pas contrôlés par ces paramètres.

### <a name="log-analytics-solution-packs-download"></a>Télécharger des packs de solutions Log Analytics
Les agents Log Analytics doivent accéder à un compte de stockage global pour télécharger des packs de solutions. Les configurations Private Link créées à partir du 19 avril 2021 (ou de juin 2021 sur les clouds souverains Azure) peuvent accéder au stockage des packs de solutions des agents via la liaison privée. Cela est possible grâce à la nouvelle zone DNS créée pour [blob.core.windows.net](#privatelink-blob-core-windows-net).

Si votre installation de liaison privée a été créée avant le 19 avril 2021, elle n’atteindra pas le stockage de packs de solutions via une liaison privée. Pour gérer cela, vous pouvez effectuer l’une des actions suivantes :
* Recréez votre AMPLS et le point de terminaison privé qui y est connecté
* Autorisez vos agents à accéder au compte de stockage via son point de terminaison public, en ajoutant les règles suivantes à la liste d’autorisation de votre pare-feu :

    | Environnement cloud | Ressource de l'agent | Ports | Sens |
    |:--|:--|:--|:--|
    |Azure (public)     | scadvisorcontent.blob.core.windows.net         | 443 | Règle de trafic sortant
    |Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Règle de trafic sortant
    |Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Règle de trafic sortant


## <a name="configure-application-insights"></a>Configurer Application Insights

Accédez au portail Azure. Dans votre ressource de composant Application Insights Azure Monitor se trouve un élément de menu **Isolement réseau** sur le côté gauche. Vous pouvez contrôler deux états différents à partir de ce menu.

![Isolement réseau AI](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Tout d’abord, vous pouvez connecter cette ressource Application Insights aux étendues de liaison privée Azure Monitor auxquelles vous avez accès. Sélectionnez **Ajouter** puis l’**étendue de liaison privée Azure Monitor**. Sélectionnez Appliquer pour la connecter. Toutes les étendues connectées s’affichent dans cet écran. Cette connexion permet au trafic des réseaux virtuels connectés d’atteindre ce composant, et elle a le même effet que si elle partait de l’étendue, comme c’est la cas dans la section [Connecter des ressources Azure Monitor](#connect-azure-monitor-resources). 

Ensuite, vous pouvez contrôler la façon dont cette ressource peut être atteinte en dehors des étendues de liaison privée (AMPLS) mentionnées précédemment. Si vous définissez **Autoriser l’accès au réseau public pour l’ingestion** sur **Non**, les machines ou les Kits de développement logiciel (SDK) en dehors des étendues connectées ne peuvent pas charger de données dans ce composant. Si vous définissez **Autoriser l’accès au réseau public pour les requêtes** sur **Non**, les machines en dehors des étendues ne peuvent pas accéder aux données de cette ressource Application Insights. Ces données incluent l’accès aux journaux APM, aux métriques et au flux de métriques en temps réel, ainsi que des expériences basées sur des classeurs, des tableaux de bord, des expériences client basées sur l’API de requête, des informations sur le portail Azure et bien plus encore. 

> [!NOTE]
> Les expériences de consommation hors portail doivent également être exécutées dans le réseau virtuel connecté par liaison privée qui comprend les charges de travail surveillées.

Vous devrez ajouter des ressources hébergeant les charges de travail surveillées à la liaison privée. Par exemple, consultez [Utilisation de points de terminaison privés pour application web Azure](../../app-service/networking/private-endpoint.md).

Cette restriction d’accès ne s’applique qu’aux données de la ressource Application Insights. Cependant, les modifications de configuration, notamment l’activation ou la désactivation de ces paramètres d’accès, sont gérées par Azure Resource Manager. Limitez plutôt l’accès à Resource Manager à l’aide des rôles, autorisations, contrôles réseau et audits appropriés. Pour plus d’informations, consultez [Rôles, autorisations et sécurité Azure Monitor](../roles-permissions-security.md).

> [!NOTE]
> Pour sécuriser entièrement un espace de travail basé sur Application Insights, vous devez verrouiller à la fois l’accès à la ressource Application Insights et à l’espace de travail Log Analytics sous-jacent.
>
> Les diagnostics au niveau du code (profileur/débogueur) nécessitent que vous [fournissiez votre propre compte de stockage](../app/profiler-bring-your-own-storage.md) pour prendre en charge une liaison privée.

### <a name="handling-the-all-or-nothing-nature-of-private-links"></a>Gestion du caractère « Tout ou rien » des liaisons privées
Comme expliqué dans [Planification de votre configuration Private Link](#planning-your-private-link-setup), une configuration Private Link, même pour une ressource unique, affecte toutes les ressources Azure Monitor dans ces réseaux et dans d’autres réseaux qui partagent le même DNS. Ce comportement peut compliquer le processus d’intégration. Considérez les options suivantes :

* L’approche la plus simple et la plus sûre consiste à ajouter tous vos composants Application Insights à l’AMPLS. Pour les composants auxquels vous souhaitez toujours accéder à partir d’autres réseaux, laissez les indicateurs « Autoriser l’accès Internet à Internet pour l’ingestion/les requêtes » définis sur Oui (valeur par défaut).
* Isoler les réseaux : si vous utilisez (ou pouvez utiliser) des réseaux virtuels spoke, suivez les instructions de la section [Topologie réseau hub-and-spoke dans Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Puis configurez des paramètres de liaison privée distincts dans les réseaux virtuels spoke correspondants. Veillez également à séparer les zones DNS, car le partage de zones DNS avec d’autres réseaux spoke entraînera des [remplacements de DNS](#the-issue-of-dns-overrides).
* Utiliser des zones DNS personnalisées pour des applications spécifiques : cette solution vous permet d’accéder à des composants Application Insights via une liaison privée, tout en conservant l’ensemble du trafic sur les routes publiques.
    - Configurez une [zone DNS privée personnalisée](../../private-link/private-endpoint-dns.md) et attribuez-lui un nom unique, par exemple internal.monitor.azure.com
    - Créez un AMPLS et un point de terminaison privé, puis ne choisissez **pas** l’intégration automatique avec le DNS privé
    - Accédez à Point de terminaison privé -> Configuration DNS, puis passez en revue le mappage suggéré des noms de domaine complets.
    - Choisissez d’ajouter une configuration, puis sélectionnez la zone internal.monitor.azure.com que vous venez de créer
    - Ajoutez des enregistrements pour la ![capture d’écran de zone DNS configurée](./media/private-link-security/private-endpoint-global-dns-zone.png) ci-dessus
    - Accédez à votre composant Application Insights et copiez sa [chaîne de connexion](../app/sdk-connection-string.md).
    - Les applications ou scripts qui souhaitent appeler ce composant via une liaison privée doivent utiliser la chaîne de connexion avec EndpointSuffix=internal.monitor.azure.com
* Mappez des points de terminaison via des fichiers hosts au lieu de DNS pour avoir un accès de liaison privée uniquement à partir d’un ordinateur ou d’une machine virtuelle spécifique de votre réseau :
    - Configurez un AMPLS et un point de terminaison privé, puis ne choisissez **pas** l’intégration automatique avec le DNS privé 
    - Configurez les enregistrements A ci-dessus sur un ordinateur qui exécute l’application dans le fichier hosts


## <a name="use-apis-and-command-line"></a>Utiliser des API et une ligne de commande

Vous pouvez automatiser le processus décrit précédemment à l’aide de modèles Azure Resource Manager, d’interfaces REST et de ligne de commande.

Pour créer et gérer des étendues de liens privés, utilisez l’[API REST](/rest/api/monitor/privatelinkscopes(preview)/private%20link%20scoped%20resources%20(preview)) ou [Azure CLI (AZ Monitor private-link-scope)](/cli/azure/monitor/private-link-scope).

Pour gérer l’indicateur d’accès au réseau sur votre espace de travail ou composant, utilisez les indicateurs `[--ingestion-access {Disabled, Enabled}]` et `[--query-access {Disabled, Enabled}]` sur les [espaces de travail Log Analytics ](/cli/azure/monitor/log-analytics/workspace) ou les [composants Application Insights](/cli/azure/ext/application-insights/monitor/app-insights/component).

### <a name="example-arm-template"></a>Exemple de modèle Resource Manager
Le modèle ARM ci-dessous crée :
* Une étendue de liaison privée (AMPLS) nommée « my-scope »
* Un espace de travail Log Analytics nommé « my-workspace »
* Une ressource à l’étendue de l’AMPLS « my-scope », nommée « my-workspace-connection »

```
{
    "$schema": https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#,
    "contentVersion": "1.0.0.0",
    "parameters": {
        "private_link_scope_name": {
            "defaultValue": "my-scope",
            "type": "String"
        },
        "workspace_name": {
            "defaultValue": "my-workspace",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.insights/privatelinkscopes",
            "apiVersion": "2019-10-17-preview",
            "name": "[parameters('private_link_scope_name')]",
            "location": "global",
            "properties": {}
        },
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-10-01",
            "name": "[parameters('workspace_name')]",
            "location": "westeurope",
            "properties": {
                "sku": {
                    "name": "pergb2018"
                },
                "publicNetworkAccessForIngestion": "Enabled",
                "publicNetworkAccessForQuery": "Enabled"
            }
        },
        {
            "type": "microsoft.insights/privatelinkscopes/scopedresources",
            "apiVersion": "2019-10-17-preview",
            "name": "[concat(parameters('private_link_scope_name'), '/', concat(parameters('workspace_name'), '-connection'))]",
            "dependsOn": [
                "[resourceId('microsoft.insights/privatelinkscopes', parameters('private_link_scope_name'))]",
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspace_name'))]"
            ],
            "properties": {
                "linkedResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspace_name'))]"
            }
        }
    ]
}
```

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>Collecter des journaux personnalisés et un journal IIS via une liaison privée

Des comptes de stockage sont utilisés dans le processus d’ingestion de journaux personnalisés. Par défaut, des comptes de stockage gérés par le service sont utilisés. Toutefois, pour ingérer des journaux personnalisés via des liaisons privées, vous devez utiliser vos propres comptes de stockage et les associer aux espaces de travail Log Analytics. Pour plus d’informations sur la configuration de ces comptes, utilisez la [ligne de commande](/cli/azure/monitor/log-analytics/workspace/linked-storage).

Pour plus d’informations sur l’intégration de votre propre compte de stockage, consultez [Comptes de stockage appartenant au client pour l’ingestion des journaux](private-storage.md).

## <a name="restrictions-and-limitations"></a>Limitations et restrictions

### <a name="ampls"></a>AMPLS
L’objet AMPLS contient un certain nombre de limites à prendre en compte lorsque vous planifiez votre installation Azure Private Link :

* Un réseau virtuel ne peut se connecter qu’à un seul objet AMPLS. Cela signifie que l’objet AMPLS doit fournir l’accès à toutes les ressources Azure Monitor auxquelles le réseau virtuel doit avoir accès.
* Une ressource Azure Monitor (un espace de travail ou un composant Application Insights) peut être connectée à cinq objets AMPLS au maximum.
* Un objet AMPLS peut se connecter à 50 ressources Azure Monitor au maximum.
* Un objet AMPLS peut se connecter à 10 points de terminaison privés au maximum.

Consultez [Tenir compte des limites](#consider-limits) pour une analyse plus approfondie de ces limites.

### <a name="agents"></a>Agents

Les versions les plus récentes des agents Windows et Linux doivent être utilisées pour permettre une ingestion sécurisée vers les espaces de travail Log Analytics. Les versions antérieures ne peuvent pas charger les données d’analyse via un réseau privé.

**Agent Log Analytics pour Windows**

Utilisez la version 10.20.18038.0 ou ultérieure de l’agent Log Analytics.

**Agent Log Analytics pour Linux**

Utilisez la version 1.12.25 ou une version ultérieure de l’agent. Si vous ne pouvez pas, exécutez les commandes suivantes sur votre machine virtuelle.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Portail Azure

Pour utiliser les expérience du portail Azure Monitor, comme Application Insights et Log Analytics, vous devez autoriser l’accès au portail Azure et aux extensions Azure Monitor sur les réseaux privés. Ajoutez les [étiquettes de service](../../firewall/service-tags.md) **AzureActiveDirectory**, **AzureResourceManager**, **AzureFrontDoor.FirstParty** et **AzureFrontdoor.Frontend** à votre groupe de sécurité réseau.

### <a name="querying-data"></a>Interrogation des données
L’[opérateur `externaldata`](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor) n’est pas pris en charge sur une liaison privée, car il lit les données à partir de comptes de stockage, mais ne garantit pas l’accès privé au stockage.

### <a name="programmatic-access"></a>Accès par programme

Pour utiliser l’API REST, [CLI](/cli/azure/monitor) ou PowerShell avec Azure Monitor sur des réseaux privés, ajoutez les [balises de service](../../virtual-network/service-tags-overview.md) **AzureActiveDirectory** et **AzureResourceManager** à votre pare-feu.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Téléchargement du Kit de développement logiciel (SDK) Application Insights à partir d’un réseau de distribution de contenu

Regroupez le code JavaScript dans votre script afin que le navigateur ne tente pas de télécharger le code à partir d’un réseau de distribution de contenu. Un exemple est disponible sur le site de [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup).

### <a name="browser-dns-settings"></a>Paramètres DNS du navigateur

Si vous vous connectez à vos ressources Azure Monitor via une liaison privée, le trafic vers ces ressources doit traverser le point de terminaison privé configuré sur votre réseau. Pour activer le point de terminaison privé, mettez à jour vos paramètres DNS en procédant de la manière décrite dans [Se connecter à un point de terminaison privé](#connect-to-a-private-endpoint). Certains navigateurs utilisent leurs propres paramètres DNS à la place de ceux que vous définissez. Le navigateur peut tenter de se connecter à des points de terminaison publics d’Azure Monitor et ignorer entièrement la liaison privée. Vérifiez que les paramètres de votre navigateur ne remplacent pas ou ne mettent pas en cache d’anciens paramètres DNS. 

## <a name="next-steps"></a>Étapes suivantes

- Découvrir le [stockage privé](private-storage.md)