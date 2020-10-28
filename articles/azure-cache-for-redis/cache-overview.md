---
title: Présentation du cache Azure pour Redis
description: Découvrez Azure Cache pour Redis et comment mettre en œuvre le modèle cache-aside, la mise en cache du contenu, la mise en cache des sessions utilisateur, la mise en file d’attente des travaux et des messages, et les transactions distribuées.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 05/12/2020
ms.openlocfilehash: 5a665b2f2aeb41ee55ee41287d2800ebdbf4ec08
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537443"
---
# <a name="azure-cache-for-redis"></a>Cache Azure pour Redis
Azure Cache pour Redis fournit un magasin de données en mémoire basé sur le logiciel open source [Redis](https://redis.io/). Redis améliore les performances et l’extensibilité d’une application qui utilise abondamment les magasins de données principaux. Il peut traiter des volumes conséquents de demandes d’applications en conservant les données fréquemment utilisées dans la mémoire du serveur, qui peut être écrite et lue rapidement. Redis constitue une solution de stockage de données à faible latence et à haut débit critique pour les applications modernes.

Azure Cache pour Redis offre Redis en tant que service managé. Elle fournit une instance de serveur Redis sécurisée et dédiée, ainsi qu’une compatibilité complète avec l’API Redis. Le service est géré par Microsoft, hébergé sur Azure et accessible à toute application à l’intérieur ou à l’extérieur d’Azure.

Azure Cache pour Redis peut être utilisé comme cache de données ou de contenu distribué, magasin de sessions, répartiteur de messages, et bien plus encore. Il est possible de le déployer en tant que solution autonome ou parallèlement à d’autres services de base de données Azure, tels qu’Azure SQL ou Cosmos DB.

## <a name="key-scenarios"></a>Principaux scénarios
Azure Cache pour Redis améliore les performances des applications grâce à la prise en charge de modèles d’architecture d’application courants. notamment les modèles suivants :

| Modèle      | Description                                        |
| ------------ | -------------------------------------------------- |
| [Cache de données](cache-web-app-cache-aside-leaderboard.md) | Les bases de données sont souvent trop grandes pour être chargées directement dans un cache. Le modèle [cache-aside](/azure/architecture/patterns/cache-aside) est couramment utilisé pour charger des données dans le cache uniquement en cas de besoin. Quand le système modifie les données, il peut également mettre à jour le cache, qui est alors distribué à d’autres clients. En outre, le système peut définir un délai d’expiration pour les données ou utiliser une stratégie d’éviction pour déclencher les mises à jour de données dans le cache.|
| [Cache de contenu](cache-aspnet-output-cache-provider.md) | De nombreuses pages web sont générées à partir de modèles qui utilisent du contenu statique comme des en-têtes, des pieds de page ou des bannières. Ces éléments statiques ne doivent pas changer souvent. Par rapport aux magasins de données back-end, l’utilisation d’un cache en mémoire permet d’accéder rapidement au contenu statique. Ce modèle réduit le temps de traitement et la charge du serveur, améliorant la réactivité des serveurs web. Il peut vous permettre de réduire le nombre de serveurs nécessaires au traitement des charges. Azure Cache pour Redis propose le fournisseur de caches de sortie Redis pour la prise en charge de ce modèle avec ASP.NET.|
| [Magasin de sessions](cache-aspnet-session-state-provider.md) | Ce modèle est souvent utilisé avec les paniers d’achat et d’autres données d’historique utilisateur qu’une application web peut associer avec les cookies des utilisateurs. Le stockage de trop nombreuses informations dans un cookie peut avoir un impact négatif sur les performances à mesure que la taille du cookie augmente, ce dernier devant être transmis et validé à chaque requête. Une solution classique consiste à utiliser le cookie comme clé pour interroger les données dans une base de données. L’utilisation d’un cache en mémoire tel que le cache Azure pour Redis pour associer des informations à un utilisateur est beaucoup plus rapide que l’interaction avec une base de données relationnelle complète. |
| Mise en file d’attente des travaux et des messages | Les applications ajoutent fréquemment des tâches à une file d’attente quand l’exécution des opérations associées à la requête prend un certain temps. Les opérations dont l’exécution est plus longue sont mises en file d’attente pour être traitées en séquence, souvent par un autre serveur.  Cette méthode est appelée « mise en file d’attente des tâches ». Azure Cache pour Redis fournit une file d’attente distribuée pour activer ce modèle dans votre application.|
| Transactions distribuées | Les applications nécessitent parfois une série de commandes sur un magasin de données back-end pour s’exécuter comme opération atomique unique. Toutes les commandes doivent réussir, ou elles devront toutes être restaurées à leur état initial. Azure Cache pour Redis prend en charge l’exécution d’un lot de commandes comme [transaction](https://redis.io/topics/transactions) unique. |

## <a name="redis-versions"></a>Versions Redis

Azure Cache pour Redis prend en charge Redis version 4.x et, en tant que préversion, 6.0. Nous avons décidé d’ignorer Redis 5.0 pour vous apporter la version la plus récente. Avant, Azure Cache pour Redis ne gérait qu’une seule version de Redis. Maintenant, il fournit une mise à niveau de version majeure plus récente et au moins une version stable plus ancienne. Vous pouvez [choisir la version](cache-how-to-version.md) qui convient le mieux à votre application.

> [!NOTE]
> Redis 6.0 est disponible en préversion : [contactez-nous](mailto:azurecache@microsoft.com) si vous êtes intéressé. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="service-tiers"></a>Niveaux de service
Les niveaux suivants sont proposés pour le cache Azure pour Redis :

| Niveau | Description |
|---|---|
| De base | Cache incluant un seul nœud. Ce niveau prend en charge plusieurs tailles de mémoire (250 Mo-53 Go). Il est idéal pour les charges de travail de développement/test et non critiques. Aucun contrat de niveau de service (SLA) ne couvre le niveau De base. |
| Standard | Cache répliqué dans une configuration primaire ou un réplica à deux nœuds, managé par Azure et assorti d’un [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) garantissant une haute disponibilité. |
| Premium | Le niveau Premium est le niveau adapté aux entreprises. Les caches de niveau Premium prennent en charge un plus grand nombre de fonctionnalités et assurent un débit plus élevé avec une latence plus faible. Les caches du niveau Premium sont déployés sur du matériel plus puissant offrant de meilleures performances par rapport au niveau De base ou Standard. Par conséquent, le débit d’un cache de même taille est plus élevé avec le niveau Premium qu’avec le niveau Standard. |

### <a name="feature-comparison"></a>Comparaison des fonctionnalités
La page [Tarifs d’Azure Cache pour Redis](https://azure.microsoft.com/pricing/details/cache/) fournit une comparaison détaillée des différents niveaux. Le tableau suivant décrit quelques-unes des fonctionnalités prises en charge par niveau :

| Description de la fonctionnalité | Premium | standard | De base |
| ------------------- | :-----: | :------: | :---: |
| [Contrat de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Persistance des données Redis](cache-how-to-premium-persistence.md) |✔|-|-|
| [Cluster Redis](cache-how-to-premium-clustering.md) |✔|-|-|
| [Sécurité via les règles de pare-feu](cache-configure.md#firewall) |✔|✔|✔|
| Chiffrement en transit |✔|✔|✔|
| [Amélioration de la sécurité et de l’isolation avec VNet](cache-how-to-premium-vnet.md) |✔|-|-|
| [Import/Export](cache-how-to-import-export-data.md) |✔|-|-|
| [Mises à jour planifiées](cache-administration.md#schedule-updates) |✔|✔|✔|
| [Géoréplication](cache-how-to-geo-replication.md) |✔|-|-|
| [Redémarrage](cache-administration.md#reboot) |✔|✔|✔|

### <a name="choosing-the-right-tier"></a>Choix du niveau approprié
Lors du choix d’un niveau d’Azure Cache pour Redis, vous devez tenir compte des aspects suivants.

* **Mémoire** : Les niveaux De base et Standard offrent respectivement 250 Mo et 53 Go. Le niveau Premium offre jusqu’à 1,2 To (en tant que cluster) ou 120 Go (non-cluster). Pour plus d’informations, consultez [Tarification du Cache Azure pour Redis](https://azure.microsoft.com/pricing/details/cache/).
* **Performances du réseau**  : Si vous avez une charge de travail qui nécessite un débit élevé, le niveau Premium offre davantage de bande passante par rapport au niveau Standard ou De base. En outre, à chaque niveau les caches de taille supérieure offrent davantage de bande passante en raison de la machine virtuelle sous-jacente qui héberge le cache. Pour plus d’informations, consultez [Performances d’Azure Cache pour Redis](cache-planning-faq.md#azure-cache-for-redis-performance).
* **Débit** : Le niveau Premium offre le débit maximal disponible. Si le client ou le serveur de cache atteint la limite de bande passante, vous risquez de recevoir des erreurs d’expiration du côté client. Pour plus d’informations, consultez le tableau suivant.
* **Haute disponibilité**  : Azure Cache pour Redis fournit plusieurs options de [haute disponibilité](cache-high-availability.md). Il garantit la disponibilité d’un cache Standard/Premium conformément à notre [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Celui-ci couvre uniquement la connectivité aux points de terminaison du cache. Le contrat SLA ne couvre pas la protection contre la perte de données. Nous vous recommandons d’utiliser la fonctionnalité de persistance des données Redis disponible au niveau Premium pour accroître la résilience contre la perte de données.
* **Persistance des données Redis** : Le niveau Premium vous permet de conserver les données du cache dans un compte de stockage Azure. Dans un cache De base/Standard, toutes les données sont stockées uniquement dans la mémoire. Les problèmes d’infrastructure sous-jacents peuvent entraîner une éventuelle perte de données. Nous vous recommandons d’utiliser la fonctionnalité de persistance des données Redis disponible au niveau Premium pour accroître la résilience contre la perte de données. Azure Cache pour Redis propose les options RDB et AOF (préversion) de persistance Redis. Pour plus d’informations, consultez la page [Comment configurer la persistance pour un Cache Azure pour Redis Premium](cache-how-to-premium-persistence.md).
* **Cluster Redis** : Pour créer des caches de plus de 120 Go ou partager des données parmi plusieurs nœuds Redis, vous pouvez utiliser le clustering Redis disponible au niveau Premium. Chaque nœud se compose d’une paire de caches principal/réplica pour la haute disponibilité. Pour plus d’informations, consultez la page [Comment configurer le clustering Redis pour un cache Azure pour Redis Premium](cache-how-to-premium-clustering.md).
* **Sécurité et isolation réseau accrues** : Le déploiement Réseau virtuel Azure fournit une sécurité et une isolation améliorées pour le cache Azure pour Redis, ainsi que des sous-réseaux, des stratégies de contrôle d’accès et d’autres fonctionnalités permettant de restreindre davantage l’accès. Pour plus d’informations, consultez [Comment configurer la prise en charge des réseaux virtuels pour un Cache Azure pour Redis Premium](cache-how-to-premium-vnet.md).
* **Configurer Redis** : Aux niveaux Standard et Premium, vous pouvez configurer Redis pour les notifications d’espace de clés.
* **Nombre maximal de connexions client** : Le niveau Premium offre le nombre maximal de clients pouvant se connecter à Redis, avec un nombre de connexions plus élevé pour les caches de taille supérieure. Le clustering n’augmente pas le nombre de connexions disponibles pour un cache en cluster. Pour plus d’informations, consultez [Tarification du Cache Azure pour Redis](https://azure.microsoft.com/pricing/details/cache/).
* **Noyau dédié pour le serveur Redis** : Au niveau Premium, toutes les tailles de cache ont un noyau dédié pour Redis. Aux niveaux De base/Standard, les tailles C1 et supérieures ont un noyau dédié pour le serveur Redis.
* **Traitement à thread unique**  : Par conception, Redis n’utilise qu’un seul thread pour le traitement des commandes. Azure Cache pour Redis utilise des cœurs supplémentaires pour le traitement des E/S. Le fait d’avoir plus de cœurs améliore les performances de débit, même ci cela risque de pas produire une mise à l’échelle linéaire. En outre, des tailles de machine virtuelle plus volumineuses sont généralement associées à des limites de bande passante supérieures. Cela vous permet d’éviter une saturation du réseau entraînant des délais d’attente dans votre application.
* **Amélioration des performances** : Les caches au niveau Premium sont déployés sur du matériel qui dispose de processeurs plus rapides et offrent de meilleures performances par rapport au niveau De base ou Standard. Les caches de niveau Premium ont un débit supérieur et des latences moindres. Pour plus d’informations, consultez [Performances d’Azure Cache pour Redis](cache-planning-faq.md#azure-cache-for-redis-performance).

Vous pouvez mettre à l’échelle votre cache vers un niveau supérieur après l’avoir créé. En revanche, il n’est pas possible de le mettre à l’échelle vers un niveau inférieur. Pour connaître la procédure de mise à l’échelle, consultez [Guide pratique pour mettre à l’échelle le cache Azure pour Redis](cache-how-to-scale.md) et [Guide pratique pour automatiser une opération de mise à l’échelle](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Étapes suivantes
* [Démarrage rapide d’application web ASP.NET](cache-web-app-howto.md) Créer une application web ASP.NET simple qui utilise un cache Azure pour Redis.
* [Démarrage rapide .NET](cache-dotnet-how-to-use-azure-redis-cache.md) Créer une application .NET qui utilise un cache Azure pour Redis.
* [Démarrage rapide .NET Core](cache-dotnet-core-quickstart.md) Créer une application .NET Core qui utilise un cache Azure pour Redis.
* [Démarrage rapide Node.js](cache-nodejs-get-started.md) Créer une application Node.js simple qui utilise un cache Azure pour Redis.
* [Démarrage rapide Java](cache-java-get-started.md) Créer une application Java simple qui utilise un cache Azure pour Redis.
* [Démarrage rapide Python](cache-python-get-started.md) Créer une application Python qui utilise un cache Azure pour Redis.