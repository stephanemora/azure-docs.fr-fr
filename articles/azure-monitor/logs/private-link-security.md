---
title: Utiliser Azure Private Link pour connecter en toute sécurité des réseaux à Azure Monitor
description: Utiliser Azure Private Link pour connecter en toute sécurité des réseaux à Azure Monitor
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.subservice: ''
ms.openlocfilehash: 55a3cd6b02b9eeb774a084552c086acbfb9966cb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100598878"
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
Étant donné que certains points de terminaison Azure Monitor sont globaux, il est impossible de créer une connexion Private Link pour un composant ou un espace de travail spécifique. Au lieu de cela, lorsque vous configurez une connexion Private Link vers un seul composant Application Insights, vos enregistrements DNS sont mis à jour pour **tous** les composants Application Insights. Toute tentative d’ingestion ou d’interrogation d’un composant passera par la connexion Private Link et risque d’échouer. De même, si vous configurez une connexion Private Link vers un seul espace de travail, toutes les requêtes Log Analytics transitent par le point de terminaison de requête Private Link (mais pas les demandes d’ingestion, qui ont des points de terminaison spécifiques à l’espace de travail).

![Schéma des remplacements DNS dans un même réseau virtuel](./media/private-link-security/dns-overrides-single-vnet.png)

Cela est vrai non seulement pour un réseau virtuel spécifique, mais pour tous les réseaux virtuels qui partagent le même serveur DNS (voir [Le problème des remplacements DNS](#the-issue-of-dns-overrides)). Par exemple, la demande d’ingestion des journaux vers n’importe quel composant Application Insights sera toujours envoyée via l’itinéraire Private Link. Les composants qui ne sont pas liés à AMPLS ne pourront pas valider Private Link et échoueront.

> [!NOTE]
> Pour conclure : Une fois que vous avez configuré une connexion Private Link vers une ressource unique, cette configuration s’applique à toutes les ressources Azure Monitor de votre réseau, selon la règle « Tout ou rien ». Cela signifie que vous devez ajouter toutes les ressources Azure Monitor de votre réseau à votre AMPLS, ou aucune d’entre elles.

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor Private Link s’applique à l’ensemble de votre réseau
Certains réseaux sont composés de plusieurs réseaux virtuels. Si les réseaux virtuels utilisent le même serveur DNS, ils remplacent leurs mappages DNS mutuels et peuvent éventuellement rompre leur communication avec Azure Monitor (consultez [Le problème des remplacements DNS](#the-issue-of-dns-overrides)). En fin de compte, seul le dernier réseau virtuel sera en mesure de communiquer avec Azure Monitor, puisque le DNS mappera les points de terminaison Azure Monitor à des adresses IP privées à partir de cette plage de réseaux virtuels (qui peut ne pas être accessible à partir d’autres réseaux virtuels).

![Schéma des remplacements DNS dans plusieurs réseaux virtuels](./media/private-link-security/dns-overrides-multiple-vnets.png)

Dans le diagramme ci-dessus, VNet 10.0.1.x se connecte d’abord à AMPLS1 puis mappe les points de terminaison globaux Azure Monitor aux adresses IP à partir de sa plage. Par la suite, VNet 10.0.2.x se connecte à AMPLS2 et remplace le mappage DNS des *mêmes points de terminaison globaux* par les adresses IP de sa plage. Comme ces réseaux virtuels ne sont pas appairés, le premier réseau virtuel ne parvient pas à atteindre ces points de terminaison.

> [!NOTE]
> Pour conclure : La configuration AMPLS affecte tous les réseaux qui partagent les mêmes zones DNS. Pour éviter de remplacer les mappages de points de terminaison DNS de chacun des réseaux, il est préférable d’installer un point de terminaison privé unique sur un réseau appairé (par exemple, un réseau virtuel hub) ou de séparer les réseaux au niveau du DNS (par exemple à l’aide de redirecteurs DNS ou de serveurs DNS totalement distincts).

### <a name="hub-spoke-networks"></a>Réseaux hub-and-spoke
Les topologies hub-and-spoke peuvent éviter le problème des remplacements DNS en définissant une connexion Private Link sur le réseau virtuel hub (principal) au lieu de configurer une connexion Private Link pour chaque réseau virtuel séparément. Cette configuration s’avère particulièrement utile si les ressources Azure Monitor utilisées par ces réseaux virtuels spoke sont partagées. 

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

    ![Capture d’écran de la sélection Créer un point de terminaison privé2](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Vous avez maintenant créé un nouveau point de terminaison privé connecté à cet AMPLS.

## <a name="configure-log-analytics"></a>Configurer Log Analytics

Accédez au portail Azure. Dans le menu de votre ressource d’espace de travail Log Analytics se trouve une option appelée **Isolement réseau** sur le côté gauche. Vous pouvez contrôler deux états différents à partir de ce menu.

![Isolement réseau LA](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Étendues de liaison privée Azure Monitor connectées
Toutes les étendues connectées à l’espace de travail apparaissent dans cet écran. La connexion aux étendues (AMPLS) autorise le trafic réseau à partir du réseau virtuel connecté à chaque AMPLS pour atteindre cet espace de travail. La création d’une connexion de cette manière a le même effet que si la connexion partait de l’étendue, comme c’est la cas dans la section [Connecter des ressources Azure Monitor](#connect-azure-monitor-resources). Pour ajouter une nouvelle connexion, sélectionnez **Ajouter**, puis choisissez l’étendue de liaison privée Azure Monitor. Sélectionnez **Appliquer** pour la connecter. Notez qu’un espace de travail peut se connecter à 5 objets AMPLS, comme mentionné dans [Restrictions et limitations](#restrictions-and-limitations). 

### <a name="access-from-outside-of-private-links-scopes"></a>Accès depuis l’extérieur des étendues de liaisons privées
Les paramètres dans la partie inférieure de cette page contrôlent l’accès à partir de réseaux publics, ce qui signifie que les réseaux ne sont pas connectés via les étendues répertoriées ci-dessus. Si vous définissez **Autoriser l’accès au réseau public pour l’ingestion** sur **Non**, vous bloquez l’ingestion des journaux à partir des machines en dehors des étendues connectées. Si vous définissez **Autoriser l’accès au réseau public pour les requêtes** sur **Non**, vous bloquez les requêtes provenant des machines en dehors des étendues. Cela inclut les requêtes exécutées via des classeurs, les tableaux de bord, les expériences client basées sur l’API, les informations sur le portail Azure et bien plus encore. Les expériences qui s’exécutent en dehors du portail Azure et qui interrogent des données Log Analytics doivent également être exécutées au sein du réseau virtuel connecté par liaison privée.

### <a name="exceptions"></a>Exceptions
La restriction de l’accès comme expliqué ci-dessus ne s’applique pas à Azure Resource Manager et présente donc les limitations suivantes :
* Accès aux données : si des requêtes de blocage/d’autorisation à partir de réseaux publics s’appliquent à la plupart des expériences Log Analytics, certaines expériences interrogent les données via Azure Resource Manager et ne peuvent donc pas interroger les données, sauf si les paramètres de liaison privée sont également appliqués au gestionnaire des ressources (fonctionnalité disponible prochainement). Cela comprend, par exemple, les solutions Azure Monitor, les classeurs et les Insights, ainsi que le connecteur LogicApp.
* Gestion de l’espace de travail : les modifications de paramètre et de configuration, (notamment l’activation ou la désactivation de ces paramètres d’accès), sont gérées par Azure Resource Manager. Limitez l’accès à la gestion de l’espace de travail à l’aide des rôles, autorisations, contrôles réseau et audits appropriés. Pour plus d’informations, consultez [Rôles, autorisations et sécurité Azure Monitor](../roles-permissions-security.md).

> [!NOTE]
> Les journaux et les métriques chargés sur un espace de travail par le biais des [paramètres de diagnostic](../essentials/diagnostic-settings.md) passent par un canal privé sécurisé de Microsoft et ne sont pas contrôlés par ces paramètres.

### <a name="log-analytics-solution-packs-download"></a>Télécharger des packs de solutions Log Analytics

Pour permettre à l’agent Log Analytics de télécharger des packs de solutions, ajoutez les noms de domaine complets appropriés à la liste d’autorisation de votre pare-feu. 


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

Vous devrez ajouter des ressources hébergeant les charges de travail surveillées à la liaison privée. Voici de la [documentation](../../app-service/networking/private-endpoint.md) sur la façon de procéder pour les services d’application.

Cette restriction d’accès ne s’applique qu’aux données de la ressource Application Insights. Les modifications de configuration, notamment l’activation ou la désactivation de ces paramètres d’accès, sont gérées par Azure Resource Manager. Au lieu de cela, limitez l’accès à Resource Manager à l’aide des rôles, autorisations, contrôles réseau et audits appropriés. Pour plus d’informations, consultez [Rôles, autorisations et sécurité Azure Monitor](../roles-permissions-security.md).

> [!NOTE]
> Pour sécuriser entièrement un espace de travail basé sur Application Insights, vous devez verrouiller à la fois l’accès à la ressource Application Insights et à l’espace de travail Log Analytics sous-jacent.
>
> Les diagnostics au niveau du code (profileur/débogueur) nécessitent que vous fournissiez votre propre compte de stockage pour prendre en charge une liaison privée. Voici de la [documentation](../app/profiler-bring-your-own-storage.md) sur la manière de procéder.

### <a name="handling-the-all-or-nothing-nature-of-private-links"></a>Gestion du caractère « Tout ou rien » des liaisons privées
Comme expliqué dans [Planification de votre configuration Private Link](#planning-your-private-link-setup), une configuration Private Link, même pour une ressource unique, affecte toutes les ressources Azure Monitor dans ces réseaux et dans d’autres réseaux qui partagent le même DNS. Cela peut compliquer le processus d’intégration. Considérez les options suivantes :

* L’approche la plus simple et la plus sûre consiste à ajouter tous vos composants Application Insights à l’AMPLS. Pour les composants auxquels vous souhaitez toujours accéder à partir d’autres réseaux, laissez les indicateurs « Autoriser l’accès Internet à Internet pour l’ingestion/les requêtes » définis sur Oui (valeur par défaut).
* Isoler les réseaux : si vous utilisez (ou pouvez utiliser) des réseaux virtuels spoke, suivez les instructions de la section [Topologie réseau hub-and-spoke dans Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Puis configurez des paramètres de liaison privée distincts dans les réseaux virtuels spoke correspondants. Veillez également à séparer les zones DNS, car le partage de zones DNS avec d’autres réseaux spoke entraînera des [remplacements de DNS](#the-issue-of-dns-overrides).
* Utiliser des zones DNS personnalisées pour des applications spécifiques : cette solution vous permet d’accéder à des composants Application Insights via une liaison privée, tout en conservant l’ensemble du trafic sur les routes publiques.
    - Configurez une [zone DNS privée personnalisée](https://docs.microsoft.com/azure/private-link/private-endpoint-dns) et attribuez-lui un nom unique, par exemple internal.monitor.azure.com
    - Créez un AMPLS et un point de terminaison privé, puis ne choisissez **pas** l’intégration automatique avec le DNS privé
    - Accédez à Point de terminaison privé -> Configuration DNS, puis passez en revue le mappage suggéré des noms de domaine complets de la façon suivante : ![Capture d’écran de la configuration de zone DNS suggérée](./media/private-link-security/private-endpoint-fqdns.png)
    - Choisissez d’ajouter une configuration, puis sélectionnez la zone internal.monitor.azure.com que vous venez de créer
    - Ajoutez des enregistrements pour la ![capture d’écran de zone DNS configurée](./media/private-link-security/private-endpoint-global-dns-zone.png) ci-dessus
    - Accédez à votre composant Application Insights et copiez sa [chaîne de connexion](https://docs.microsoft.com/azure/azure-monitor/app/sdk-connection-string).
    - Les applications ou scripts qui souhaitent appeler ce composant via une liaison privée doivent utiliser la chaîne de connexion avec EndpointSuffix=internal.monitor.azure.com
* Mappez des points de terminaison via des fichiers hosts au lieu de DNS pour avoir un accès de liaison privée uniquement à partir d’un ordinateur ou d’une machine virtuelle spécifique de votre réseau :
    - Configurez un AMPLS et un point de terminaison privé, puis ne choisissez **pas** l’intégration automatique avec le DNS privé 
    - Configurez les enregistrements A ci-dessus sur un ordinateur qui exécute l’application dans le fichier hosts


## <a name="use-apis-and-command-line"></a>Utiliser des API et une ligne de commande

Vous pouvez automatiser le processus décrit précédemment à l’aide de modèles Azure Resource Manager, d’interfaces REST et de ligne de commande.

Pour créer et gérer des étendues de liens privés, utilisez l’[API REST](/rest/api/monitor/private%20link%20scopes%20(preview)) ou [Azure CLI (AZ Monitor private-link-scope)](/cli/azure/monitor/private-link-scope).

Pour gérer l’accès au réseau, utilisez les indicateurs `[--ingestion-access {Disabled, Enabled}]` et `[--query-access {Disabled, Enabled}]` sur les [espaces de travail Log Analytics ](/cli/azure/monitor/log-analytics/workspace) ou les [composants Application Insights](/cli/azure/ext/application-insights/monitor/app-insights/component).

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
L’[opérateur `externaldata`](https://docs.microsoft.com/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor) n’est pas pris en charge sur une liaison privée, car il lit les données à partir de comptes de stockage, mais ne garantit pas l’accès privé au stockage.

### <a name="programmatic-access"></a>Accès par programme

Pour utiliser l’API REST, [CLI](/cli/azure/monitor) ou PowerShell avec Azure Monitor sur des réseaux privés, ajoutez les [balises de service](../../virtual-network/service-tags-overview.md) **AzureActiveDirectory** et **AzureResourceManager** à votre pare-feu.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Téléchargement du Kit de développement logiciel (SDK) Application Insights à partir d’un réseau de distribution de contenu

Regroupez le code JavaScript dans votre script afin que le navigateur ne tente pas de télécharger le code à partir d’un réseau de distribution de contenu. Un exemple est disponible sur le site de [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup).

### <a name="browser-dns-settings"></a>Paramètres DNS du navigateur

Si vous vous connectez à vos ressources Azure Monitor via une liaison privée, le trafic vers ces ressources doit traverser le point de terminaison privé configuré sur votre réseau. Pour activer le point de terminaison privé, mettez à jour vos paramètres DNS en procédant de la manière décrite dans [Se connecter à un point de terminaison privé](#connect-to-a-private-endpoint). Certains navigateurs utilisent leurs propres paramètres DNS à la place de ceux que vous définissez. Le navigateur peut tenter de se connecter à des points de terminaison publics d’Azure Monitor et ignorer entièrement la liaison privée. Vérifiez que les paramètres de votre navigateur ne remplacent pas ou ne mettent pas en cache d’anciens paramètres DNS. 

## <a name="next-steps"></a>Étapes suivantes

- Découvrir le [stockage privé](private-storage.md)