---
title: Conception de votre configuration Private Link
description: Conception de votre configuration Private Link
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 08/01/2021
ms.openlocfilehash: 3b7316bf7d21a117c80eb49978a807b085db004b
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432537"
---
# <a name="design-your-private-link-setup"></a>Conception de votre configuration Private Link

Avant de configurer votre liaison privée Azure Monitor, prenez en compte la topologie de votre réseau, et en particulier la topologie de votre routage DNS.
Comme nous l’avons vu dans [Fonctionnement](./private-link-security.md#how-it-works), la configuration d’une liaison privée affecte le trafic vers toutes les ressources Azure Monitor. C’est particulièrement vrai pour les ressources Application Insights. De plus, cela affecte non seulement le réseau connecté au point de terminaison privé, mais également tous les autres réseaux qui partagent le même DNS.

L’approche la plus simple et la plus sécurisée serait la suivante :
1. Créez une connexion de liaison privée unique, avec un point de terminaison privé unique et une étendue AMPLS unique. Si vos réseaux sont appairés, créez la connexion de liaison privée sur le réseau virtuel partagé (ou hub).
2. Ajoutez *toutes* les ressources Azure Monitor (composants Application Insights et espaces de travail Log Analytics) à cette étendue AMPLS.
3. Bloquez autant que possible le trafic de sortie réseau.


## <a name="plan-by-network-topology"></a>Planifier selon la topologie de réseau

### <a name="guiding-principle-avoid-dns-overrides-by-using-a-single-ampls"></a>Principe directeur : évitez les remplacements DNS à l’aide d’une étendue AMPLS unique
Certains réseaux sont composés de plusieurs réseaux virtuels ou d’autres réseaux connectés. Si ces réseaux partagent le même DNS, la configuration d’une liaison privée sur l’un d’eux met à jour le DNS et affecte le trafic sur tous les réseaux.

Dans le diagramme ci-dessous, le réseau VNet 10.0.1.x se connecte d’abord à l’étendue AMPLS1, puis mappe les points de terminaison globaux Azure Monitor aux adresses IP à partir de sa plage. Par la suite, VNet 10.0.2.x se connecte à AMPLS2 et remplace le mappage DNS des **mêmes points de terminaison globaux** par les adresses IP de sa plage. Comme ces réseaux virtuels ne sont pas appairés, le premier réseau virtuel ne parvient pas à atteindre ces points de terminaison.

Pour éviter ce conflit, créez un seul objet AMPLS par DNS.

![Schéma des remplacements DNS dans plusieurs réseaux virtuels](./media/private-link-security/dns-overrides-multiple-vnets.png)


### <a name="hub-and-spoke-networks"></a>Réseaux hub-and-spoke
Les réseaux hub-and-spoke doivent utiliser une connexion de liaison privée définie sur le réseau hub (principal), et non sur chaque réseau virtuel spoke. 

![Point de terminaison privé hub-and-spoke](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> Vous pouvez intentionnellement préférer créer des connexions Private Link distinctes pour vos réseaux virtuels spoke, par exemple pour permettre à chaque réseau virtuel d’accéder à un ensemble limité de ressources de surveillance. Dans ce cas, vous pouvez créer un point de terminaison privé dédié et une étendue AMPLS pour chaque réseau virtuel, mais **vous devez également vérifier qu’ils ne partagent pas les mêmes zones DNS afin d’éviter les remplacements DNS**.

### <a name="peered-networks"></a>Réseaux appairés
Le peering de réseaux est utilisé dans diverses topologies, autres que hub-and-spoke. Ces réseaux peuvent partager les adresses IP des uns et des autres, et partager le même DNS. Dans ce cas, nous vous recommandons une nouvelle fois de créer une seule connexion de liaison privée sur un réseau accessible à vos autres réseaux. Évitez de créer plusieurs points de terminaison privés et objets AMPLS, car, au bout du compte, seul le dernier défini dans le service DNS s’applique.

### <a name="isolated-networks"></a>Réseaux isolés
Si vos réseaux ne sont pas appairés, **vous devez également séparer leur DNS afin d’utiliser des liaisons privées**. Créez ensuite un point de terminaison privé distinct pour chaque réseau et un objet AMPLS distinct. Vos objets AMPLS peuvent lier aux mêmes espaces de travail/composants ou à d’autres.

### <a name="testing-locally-edit-your-machines-hosts-file-instead-of-the-dns"></a>Tests locaux : modifier le fichier hosts de votre ordinateur à la place du DNS 
Pour tester les connexions de liaison privée localement sans affecter les autres clients sur votre réseau, veillez à ne pas mettre à jour votre DNS lorsque vous créez votre point de terminaison privé. Modifiez plutôt le fichier hosts sur votre machine pour qu’il envoie les demandes aux points de terminaison de liaison privée :
* Configurez une liaison privée, mais lors de la connexion à un point de terminaison privé, choisissez de **ne pas** effectuer une intégration automatique au DNS (étape 5b).
* Configurez les points de terminaison appropriés sur les fichiers hosts de vos machines. Pour passer en revue les points de terminaison Azure Monitor à mapper, consultez [Examen des paramètres DNS de votre point de terminaison](./private-link-configure.md#reviewing-your-endpoints-dns-settings).

Cette approche n’est pas recommandée pour les environnements de production.

## <a name="control-how-private-links-apply-to-your-networks"></a>Contrôler la façon dont les liaisons privées s’appliquent à vos réseaux
Les modes d’accès Private Link (introduits en septembre 2021) vous permettent de contrôler la façon dont les liaisons privées affectent le trafic réseau. Ces paramètres peuvent s’appliquer à votre objet AMPLS (pour affecter tous les réseaux connectés) ou à des réseaux spécifiques qui y sont connectés.

Le choix du mode d’accès approprié a des effets néfastes sur le trafic réseau. Chacun de ces modes peut être défini pour l’ingestion et les requêtes, séparément :

* Privé uniquement : autorise le réseau virtuel à atteindre uniquement les ressources de liaison privée (ressources dans l’étendue AMPLS). Ce mode de travail qui empêche l’exfiltration de données est le plus sécurisé. Le trafic vers les ressources Azure Monitor hors de l’étendue AMPLS est ainsi bloqué.
![Diagramme du mode d’accès Privé uniquement dans l’étendue AMPLS](./media/private-link-security/ampls-private-only-access-mode.png)
* Ouvert : autorise le réseau virtuel à atteindre les ressources de liaison privée et les ressources qui ne se trouvent pas dans l’étendue AMPLS (s’ils [acceptent le trafic provenant de réseaux publics](./private-link-design.md#control-network-access-to-your-resources)). Bien que le mode d’accès Ouvert n’empêche pas l’exfiltration de données, il présente quand même les autres avantages des liaisons privées. Le trafic vers les ressources de liaison privée est transmis via des points de terminaison privés, validé et envoyé via le réseau principal de Microsoft. Le mode Ouvert est utile dans le cadre d’un mode de travail mixte (qui accède à certaines ressources publiquement et à d’autres via une liaison privée), ou d’un processus d’intégration progressif.
![Diagramme représentant le mode d’accès Ouvert dans l’étendue AMPLS](./media/private-link-security/ampls-open-access-mode.png) Les modes d’accès sont définis séparément pour l’ingestion et les requêtes. Par exemple, vous pouvez définir le mode Privé uniquement pour l’ingestion et le mode Ouvert pour les requêtes.


Soyez prudent lorsque vous choisissez votre mode d’accès. L’utilisation du mode d’accès Privé uniquement a pour effet de bloquer le trafic vers les ressources ne se trouvant pas dans AMPLS sur tous les réseaux qui partagent le même DNS, quel que soit l’abonnement ou le locataire (à l’exception des demandes d’ingestion Log Analytics, comme expliqué ci-dessous). Si vous ne pouvez pas ajouter toutes les ressources Azure Monitor à AMPLS, commencez par ajouter des ressources sélectionnées en appliquant le mode d’accès Ouvert. Une fois que vous avez ajouté *toutes* les ressources Azure Monitor à votre AMPLS, basculez vers le mode « Privé uniquement » pour bénéficier d’une sécurité maximale.

> [!NOTE]
> L’ingestion Log Analytics utilise des points de terminaison spécifiques d’une ressource. Par conséquent, elle n’adhère pas aux modes d’accès AMPLS. L’ingestion dans des espaces de travail se trouvant dans AMPLS est envoyée via la liaison privée, tandis que l’ingestion dans des espaces de travail ne se trouvant pas dans AMPLS utilise les points de terminaison publics par défaut. Pour vous assurer que les demandes d’ingestion ne puissent pas accéder à des ressources extérieures à AMPLS, bloquez l’accès du réseau aux points de terminaison publics.

### <a name="setting-access-modes-for-specific-networks"></a>Définition des modes d’accès pour des réseaux spécifiques
Les modes d’accès définis sur la ressource AMPLS affectent tous les réseaux. Vous pouvez toutefois remplacer ces paramètres pour des réseaux spécifiques.

Dans le diagramme suivant, le réseau VNet1 utilise le mode Ouvert, tandis que le réseau VNet2 utilise le mode Privé uniquement. Par conséquent, les demandes de VNet1 peuvent atteindre l’espace de travail Workspace1 et le composant Component2 sur une liaison privée, et le composant Component3 sur une liaison non privée (s’il [accepte le trafic provenant de réseaux publics](./private-link-design.md#control-network-access-to-your-resources)). Toutefois, les demandes du réseau VNet2 ne peuvent pas atteindre le composant Component3. 
![Diagramme représentant des modes d’accès mixtes](./media/private-link-security/ampls-mixed-access-modes.png)


## <a name="consider-ampls-limits"></a>Envisager les limites AMPLS
L’objet AMPLS présente les limitations suivantes :
* Un réseau virtuel ne peut se connecter qu’à **un seul** objet AMPLS. Cela signifie que l’objet AMPLS doit fournir l’accès à toutes les ressources Azure Monitor auxquelles le réseau virtuel doit avoir accès.
* Un objet AMPLS peut se connecter à 50 ressources Azure Monitor au maximum.
* Une ressource Azure Monitor (un espace de travail ou un composant Application Insights) peut être connectée à cinq objets AMPLS au maximum.
* Un objet AMPLS peut se connecter à 10 points de terminaison privés au maximum.

Dans le diagramme ci-dessous :
* Chaque réseau virtuel se connecte à **un seul** objet AMPLS.
* AMPLS A se connecte à deux espaces de travail et à un composant Application Insights en utilisant 3 de ses 50 connexions de ressources Azure Monitor possibles.
* Workspace2 se connecte à AMPLS A et à AMPLS B en utilisant deux de ses cinq connexions AMPLS possibles.
* AMPLS B est connecté aux points de terminaison privés de deux réseaux virtuels (VNet2 et VNet3) en utilisant deux de ses 10 connexions de points de terminaison privés possibles.

![Diagramme des limites d’AMPLS](./media/private-link-security/ampls-limits.png)


## <a name="control-network-access-to-your-resources"></a>Contrôler l’accès réseau à vos ressources
Vos espaces de travail Log Analytics ou composants Application Insights peuvent être définis pour :
* Accepter ou bloquer l’ingestion à partir des réseaux publics (réseaux non connectés à l’étendue AMPLS de la ressource).
* Accepter ou bloquer les requêtes provenant de réseaux publics (réseaux non connectés à l’étendue AMPLS de la ressource).

Cette précision vous permet de définir l’accès en fonction de vos besoins, par espace de travail. Par exemple, vous pouvez accepter l’ingestion uniquement via des réseaux connectés par liaison privée (c’est-à-dire des réseaux virtuels spécifiques), tout en choisissant d’accepter les requêtes de tous les réseaux, publics et privés. 

Le blocage des requêtes provenant des réseaux publics signifie que les clients (machines, kits SDK, etc.) situés hors des étendues AMPLS connectées ne peuvent pas interroger les données de la ressource. Ces données incluent les journaux, les métriques et les flux de métriques temps réel. Le blocage des requêtes provenant de réseaux publics affecte toutes les expériences qui exécutent ces requêtes telles que les workbooks, tableaux de bord et insights dans le portail Azure, ainsi que les requêtes exécutées en dehors du portail Azure.

### <a name="exceptions"></a>Exceptions

#### <a name="diagnostic-logs"></a>Journaux de diagnostic
Les journaux et les métriques chargés sur un espace de travail par le biais des [paramètres de diagnostic](../essentials/diagnostic-settings.md) passent par un canal privé sécurisé de Microsoft et ne sont pas contrôlés par ces paramètres.

#### <a name="azure-resource-manager"></a>Azure Resource Manager
La restriction de l’accès comme expliqué ci-dessus s’applique aux données de la ressource. Cependant, les modifications de configuration, notamment l’activation ou la désactivation de ces paramètres d’accès, sont gérées par Azure Resource Manager. Pour contrôler ces paramètres, vous devez limiter l’accès aux ressources à l’aide des rôles, autorisations, contrôles réseau et audits appropriés. Pour plus d’informations, consultez [Rôles, autorisations et sécurité Azure Monitor](../roles-permissions-security.md).

> [!NOTE]
> Les requêtes envoyées par le biais de l’API Gestion des ressources Azure ne peuvent pas utiliser de liaisons privées Azure Monitor. Ces requêtes ne peuvent être exécutées que si la ressource cible autorise les requêtes provenant de réseaux publics (définis via le panneau Isolement réseau ou [à l’aide de l’interface CLI](./private-link-configure.md#set-resource-access-flags)).
>
> Les expériences suivantes sont connues pour exécuter des requêtes via l’API Gestion des ressources Azure :
> * Sentinel
> * Connecteur LogicApp
> * Solution Update Management
> * Solution de suivi des modifications
> * Insights de machine virtuelle
> * Container Insights
> * Panneau Récapitulatif de l’espace de travail de Log Analytics (affichant le tableau de bord de solutions)

## <a name="application-insights-considerations"></a>Considérations relatives à Application Insights
* Vous devrez ajouter des ressources hébergeant les charges de travail supervisées à une liaison privée. Par exemple, consultez [Utilisation de points de terminaison privés pour application web Azure](../../app-service/networking/private-endpoint.md).
* Les expériences de consommation hors portail doivent également être exécutées dans le réseau virtuel connecté par liaison privée qui comprend les charges de travail surveillées.
* Afin de prendre en charge les liaisons privées pour le profileur et le débogueur, vous devez [fournir votre propre compte de stockage](../app/profiler-bring-your-own-storage.md). 

> [!NOTE]
> Pour sécuriser entièrement un espace de travail basé sur Application Insights, vous devez verrouiller à la fois l’accès à la ressource Application Insights et à l’espace de travail Log Analytics sous-jacent.

## <a name="log-analytics-considerations"></a>Considérations relatives à Log Analytics

### <a name="log-analytics-solution-packs-download"></a>Télécharger des packs de solutions Log Analytics
Les agents Log Analytics doivent accéder à un compte de stockage global pour télécharger des packs de solutions. Les configurations de liaison privée créées à partir du 19 avril 2021 (ou de juin 2021 sur les clouds souverains Azure) peuvent accéder au stockage des packs de solutions des agents via la liaison privée. C’est possible grâce à une zone DNS créée pour 'blob.core.windows.net'.

Si votre installation de liaison privée a été créée avant le 19 avril 2021, elle n’atteindra pas le stockage de packs de solutions via une liaison privée. Pour gérer cela, vous pouvez :
* Recréez votre AMPLS et le point de terminaison privé qui y est connecté
* Autorisez vos agents à accéder au compte de stockage via son point de terminaison public, en ajoutant les règles suivantes à la liste d’autorisation de votre pare-feu :

    | Environnement cloud | Ressource de l'agent | Ports | Sens |
    |:--|:--|:--|:--|
    |Azure (public)     | scadvisorcontent.blob.core.windows.net         | 443 | Règle de trafic sortant
    |Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Règle de trafic sortant
    |Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Règle de trafic sortant

### <a name="collecting-custom-logs-and-iis-log-over-private-link"></a>Collecter des journaux personnalisés et un journal IIS via une liaison privée
Des comptes de stockage sont utilisés dans le processus d’ingestion de journaux personnalisés. Par défaut, des comptes de stockage gérés par le service sont utilisés. Toutefois, pour ingérer des journaux personnalisés via des liaisons privées, vous devez utiliser vos propres comptes de stockage et les associer aux espaces de travail Log Analytics. Pour plus d’informations sur la configuration de ces comptes, utilisez la [ligne de commande](/cli/azure/monitor/log-analytics/workspace/linked-storage).

Pour plus d’informations sur la connexion de votre propre compte de stockage, consultez [Comptes de stockage appartenant au client pour l’ingestion des journaux](private-storage.md).

### <a name="automation"></a>Automatisation
Si vous utilisez des solutions Log Analytics qui requièrent un compte Automation, comme Update Management, Change Tracking ou Inventory, vous devez également créer une liaison privée pour votre compte Automation. Pour plus d’informations, consultez [Utiliser Azure Private Link pour connecter en toute sécurité des réseaux à Azure Automation](../../automation/how-to/private-link-security.md).

> [!NOTE]
> Certains produits et expériences du portail Azure effectuent des requêtes de données via Azure Resource Manager et ne peuvent donc pas effectuer ces requêtes via une liaison privée, à moins que les paramètres de liaison privée soient également appliqués au Gestionnaire de ressources. Pour surmonter ce problème, vous pouvez configurer vos ressources pour qu’elles acceptent les requêtes provenant des réseaux publics, comme cela est expliqué dans [Contrôler l’accès réseau à vos ressources](./private-link-design.md#control-network-access-to-your-resources) (l’ingestion peut rester limitée aux réseaux de liaison privée).
Nous avons identifié les produits et les expériences suivants qui interrogent les espaces de travail via Azure Resource Manager :
> * Connecteur LogicApp
> * Solution Update Management
> * Solution de suivi des modifications
> * Panneau de synthèse de l’espace de travail dans le portail (affichant le tableau de bord de solutions)
> * Insights de machine virtuelle
> * Container Insights

## <a name="requirements"></a>Configuration requise
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

### <a name="programmatic-access"></a>Accès par programme
Pour utiliser l’API REST, [CLI](/cli/azure/monitor) ou PowerShell avec Azure Monitor sur des réseaux privés, ajoutez les [balises de service](../../virtual-network/service-tags-overview.md) **AzureActiveDirectory** et **AzureResourceManager** à votre pare-feu.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Téléchargement du Kit de développement logiciel (SDK) Application Insights à partir d’un réseau de distribution de contenu
Regroupez le code JavaScript dans votre script afin que le navigateur ne tente pas de télécharger le code à partir d’un réseau de distribution de contenu. Un exemple est disponible sur le site de [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup).

### <a name="browser-dns-settings"></a>Paramètres DNS du navigateur
Si vous vous connectez à vos ressources Azure Monitor via une liaison privée, le trafic vers ces ressources doit traverser le point de terminaison privé configuré sur votre réseau. Pour activer le point de terminaison privé, mettez à jour vos paramètres DNS en procédant de la manière décrite dans [Se connecter à un point de terminaison privé](./private-link-configure.md#connect-to-a-private-endpoint). Certains navigateurs utilisent leurs propres paramètres DNS à la place de ceux que vous définissez. Le navigateur peut tenter de se connecter à des points de terminaison publics d’Azure Monitor et ignorer entièrement la liaison privée. Vérifiez que les paramètres de votre navigateur ne remplacent pas ou ne mettent pas en cache d’anciens paramètres DNS. 

### <a name="querying-limitation-externaldata-operator"></a>Limitation d’interrogation : opérateur externaldata
L’[opérateur `externaldata`](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor) n’est pas pris en charge sur une liaison privée, car il lit les données à partir de comptes de stockage, mais ne garantit pas l’accès privé au stockage.

## <a name="next-steps"></a>Étapes suivantes
- Apprenez à [Configurer votre liaison privée](private-link-configure.md)
- Découvrir le [stockage privé](private-storage.md)
- Découvrir [Private Link pour Automation](../../automation/how-to/private-link-security.md)