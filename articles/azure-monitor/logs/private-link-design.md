---
title: Concevoir votre configuration Private Link
description: Concevoir votre configuration Private Link
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 08/01/2021
ms.openlocfilehash: ccee8017bce3109cc6d47bbeb225ca7c1edf5d66
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122525884"
---
# <a name="design-your-private-link-setup"></a>Concevoir votre configuration Private Link

Avant de configurer Azure Monitor Private Link, prenez en compte la topologie de votre réseau, et en particulier votre topologie de routage DNS.
Comme nous l’avons vu dans [Fonctionnement](./private-link-security.md#how-it-works), la configuration d’une liaison privée affecte le trafic vers toutes les ressources Azure Monitor. C’est particulièrement vrai pour les ressources Application Insights. De plus, cela affecte non seulement le réseau connecté au point de terminaison privé, mais également tous les autres réseaux qui partagent le même DNS.

> [!NOTE]
> L’approche la plus simple et la plus sécurisée serait la suivante :
> 1. Créez une connexion de liaison privée unique, avec un point de terminaison privé unique et une étendue AMPLS unique. Si vos réseaux sont appairés, créez la connexion de liaison privée sur le réseau virtuel partagé (ou hub).
> 2. Ajoutez *toutes* les ressources Azure Monitor (composants Application Insights et espaces de travail Log Analytics) à cette étendue AMPLS.
> 3. Bloquez autant que possible le trafic de sortie réseau.

Si, pour une raison ou une autre, vous ne pouvez pas utiliser une liaison privée unique et une étendue AMPLS (étendue de liaison privée Azure Monitor) unique, la meilleure solution consiste à créer des connexions de liaison privée isolées pour les réseaux d’isolation. Si vous utilisez (ou pouvez vous aligner sur) des réseaux virtuels spoke, suivez les instructions fournies dans [Topologie réseau hub-and-spoke dans Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Puis configurez des paramètres de liaison privée distincts dans les réseaux virtuels spoke correspondants. **Veillez également à séparer les zones DNS**, car le partage de zones DNS avec d’autres réseaux spoke entraînera des remplacements DNS.

## <a name="plan-by-network-topology"></a>Planifier selon la topologie de réseau
### <a name="hub-spoke-networks"></a>Réseaux hub-and-spoke
Les topologies hub-and-spoke peuvent éviter le problème des remplacements DNS en définissant la liaison privée sur le réseau virtuel hub (principal) et non sur chaque réseau virtuel spoke. Cette configuration s’avère particulièrement utile si les ressources Azure Monitor utilisées par ces réseaux virtuels spoke sont partagées. 

![Point de terminaison privé hub-and-spoke](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> Vous pouvez intentionnellement préférer créer des connexions Private Link distinctes pour vos réseaux virtuels spoke, par exemple pour permettre à chaque réseau virtuel d’accéder à un ensemble limité de ressources de surveillance. Dans ce cas, vous pouvez créer un point de terminaison privé dédié et une étendue AMPLS pour chaque réseau virtuel, mais **vous devez également vérifier qu’ils ne partagent pas les mêmes zones DNS afin d’éviter les remplacements DNS**.

### <a name="peered-networks"></a>Réseaux appairés
Le peering de réseaux est utilisé dans diverses topologies, autres que hub-and-spoke. Ces réseaux peuvent partager les adresses IP des uns et des autres, et partager le même DNS. Dans ce cas, notre recommandation est similaire à l’option hub-and-spoke : sélectionnez un seul réseau accessible par tous les autres réseaux (pertinents) et définissez la connexion de liaison privée sur ce réseau. Évitez de créer plusieurs points de terminaison privés et objets AMPLS, car, au bout du compte, seul le dernier défini dans le service DNS s’applique.

### <a name="isolated-networks"></a>Réseaux isolés
#<a name="if-your-networks-arent-peered-you-must-also-separate-their-dns-in-order-to-use-private-links-once-thats-done-you-can-create-a-private-link-for-one-or-many-network-without-affecting-traffic-of-other-networks-that-means-creating-a-separate-private-endpoint-for-each-network-and-a-separate-ampls-object-your-ampls-objects-can-link-to-the-same-workspacescomponents-or-to-different-ones"></a>Si vos réseaux ne sont pas appairés, **vous devez également séparer leur DNS afin d’utiliser des liaisons privées**. Une fois cette opération effectuée, vous pouvez créer une liaison privée pour un réseau (ou plusieurs), sans affecter le trafic des autres réseaux. Cela implique de créer un point de terminaison privé distinct pour chaque réseau et un objet AMPLS distinct. Vos objets AMPLS peuvent lier aux mêmes espaces de travail/composants ou à d’autres.

### <a name="testing-locally-edit-your-machines-hosts-file-instead-of-the-dns"></a>Tests locaux : modifier le fichier hosts de votre ordinateur à la place du DNS 
En guise de contournement local du comportement de type « tout ou rien », vous pouvez choisir de ne pas mettre à jour votre DNS avec les enregistrements de liaison privée et, à la place, de modifier les fichiers hosts sur des machines sélectionnées afin que seules ces machines puissent envoyer des demandes aux points de terminaison de liaison privée.
* Configurez une liaison privée, mais lors de la connexion à un point de terminaison privé, choisissez de **ne pas** effectuer une intégration automatique au DNS (étape 5b).
* Configurez les points de terminaison appropriés sur les fichiers hosts de vos machines. Pour passer en revue les points de terminaison Azure Monitor à mapper, consultez [Examen des paramètres DNS de votre point de terminaison](./private-link-configure.md#reviewing-your-endpoints-dns-settings).

Cette approche n’est pas recommandée pour les environnements de production.

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
Vos espaces de travail Log Analytics ou vos composants Application Insights peuvent être configurés pour accepter ou bloquer l’accès à partir de réseaux publics, à savoir de réseaux non connectés à l’objet AMPLS de la ressource.
Cette précision vous permet de définir l’accès en fonction de vos besoins, par espace de travail. Par exemple, vous pouvez accepter l’ingestion uniquement via des réseaux connectés par liaison privée (c’est-à-dire des réseaux virtuels spécifiques), tout en choisissant d’accepter les requêtes de tous les réseaux, publics et privés. Notez que le blocage des requêtes provenant des réseaux publics signifie que les clients (machines, kits SDK, etc.) situés hors des étendues AMPLS connectées ne peuvent pas interroger les données de la ressource. Ces données incluent l’accès aux journaux, aux métriques et au flux de métriques en temps réel, ainsi que les expériences basées sur ces éléments, tels que les classeurs, les tableaux de bord, les expériences client basées sur l’API de requête, les insights dans le portail Azure, etc. Les expériences prenant place en dehors du portail Azure et qui interrogent les données Log Analytics sont également affectées par ce paramètre.


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
Si vous utilisez des solutions Log Analytics qui requièrent un compte Automation, comme Update Management, Change Tracking ou Inventory, vous devez également configurer une liaison privée distincte pour votre compte Automation. Pour plus d’informations, consultez [Utiliser Azure Private Link pour connecter en toute sécurité des réseaux à Azure Automation](../../automation/how-to/private-link-security.md).

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
- Découvrir comment [configurer votre liaison privée](private-link-configure.md)
- Découvrir le [stockage privé](private-storage.md)
- Découvrir [Private Link pour Automation](../../automation/how-to/private-link-security.md)