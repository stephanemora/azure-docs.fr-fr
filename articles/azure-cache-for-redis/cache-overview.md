---
title: Présentation du cache Azure pour Redis
description: Découvrez Azure Cache pour Redis et comment mettre en œuvre le modèle cache-aside, la mise en cache du contenu, la mise en cache des sessions utilisateur, la mise en file d’attente des travaux et des messages, et les transactions distribuées.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 03/11/2020
ms.openlocfilehash: cd4e7c8e2693c25f3fc092fb53874a97cfd62434
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82113202"
---
# <a name="azure-cache-for-redis-description"></a>Description du cache Azure pour Redis

Azure Cache pour Redis fournit un magasin de données en mémoire basé sur le logiciel open source [Redis](https://redis.io/). Utilisé comme cache, Redis améliore les performances et la scalabilité des systèmes qui reposent grandement sur des magasins de données back-end. Les performances sont améliorées grâce à la copie des données fréquemment sollicitées dans un stockage rapide situé près de l’application. Avec Azure Cache pour Redis, ce stockage rapide est localisé en mémoire et non chargé à partir d’un disque par une base de données.

Azure Cache pour Redis peut être utilisé comme magasin de structure de données en mémoire, comme base de données non relationnelle distribuée et comme répartiteur de messages. Les performances des applications sont améliorées en tirant parti des performances à faible latence et à débit élevé du moteur Redis.

Azure Cache pour Redis permet d’accéder à un cache Redis dédié et sécurisé. Le cache Azure pour Redis est géré par Microsoft, hébergé dans Azure et accessible à toute application à l’intérieur ou à l’extérieur d’Azure. De plus, Azure Cache pour Redis utilise la stratégie de réplication sans disque, ce qui améliore la compatibilité avec le secteur des cartes de paiement.

## <a name="using-azure-cache-for-redis"></a>Utilisation du cache Azure pour Redis

Azure Cache pour Redis améliore les performances des applications grâce à la prise en charge de modèles d’architecture d’application courants. notamment les modèles suivants :

| Modèle      | Description                                        |
| ------------ | -------------------------------------------------- |
| [Cache-Aside](cache-web-app-cache-aside-leaderboard.md) | Les bases de données sont souvent trop grandes pour être chargées directement dans un cache. Le modèle [cache-aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) est couramment utilisé pour charger des données dans le cache uniquement en cas de besoin. Quand le système modifie les données, il peut également mettre à jour le cache, qui est alors distribué à d’autres clients. En outre, le système peut définir un délai d’expiration pour les données ou utiliser une stratégie d’éviction pour déclencher les mises à jour de données dans le cache.|
| [Mise en cache du contenu](cache-aspnet-output-cache-provider.md) | De nombreuses pages web sont générées à partir de modèles qui utilisent du contenu statique comme des en-têtes, des pieds de page ou des bannières. Ces éléments statiques ne doivent pas changer souvent. Par rapport aux magasins de données back-end, l’utilisation d’un cache en mémoire permet d’accéder rapidement au contenu statique. Ce modèle réduit le temps de traitement et la charge du serveur, améliorant la réactivité des serveurs web. Il peut vous permettre de réduire le nombre de serveurs nécessaires au traitement des charges. Azure Cache pour Redis propose le fournisseur de caches de sortie Redis pour la prise en charge de ce modèle avec ASP.NET.|
| [Mise en cache des sessions utilisateur](cache-aspnet-session-state-provider.md) | Ce modèle est souvent utilisé avec les paniers d’achat et d’autres données d’historique utilisateur qu’une application web peut associer avec les cookies des utilisateurs. Le stockage de trop nombreuses informations dans un cookie peut avoir un impact négatif sur les performances à mesure que la taille du cookie augmente, ce dernier devant être transmis et validé à chaque requête. Une solution classique consiste à utiliser le cookie comme clé pour interroger les données dans une base de données. L’utilisation d’un cache en mémoire tel que le cache Azure pour Redis pour associer des informations à un utilisateur est beaucoup plus rapide que l’interaction avec une base de données relationnelle complète. |
| Mise en file d’attente des travaux et des messages | Les applications ajoutent fréquemment des tâches à une file d’attente quand l’exécution des opérations associées à la requête prend un certain temps. Les opérations dont l’exécution est plus longue sont mises en file d’attente pour être traitées en séquence, souvent par un autre serveur.  Cette méthode est appelée « mise en file d’attente des tâches ». Azure Cache pour Redis fournit une file d’attente distribuée pour activer ce modèle dans votre application.|
| Transactions distribuées | Les applications nécessitent parfois une série de commandes sur un magasin de données back-end pour s’exécuter comme opération atomique unique. Toutes les commandes doivent réussir, ou elles devront toutes être restaurées à leur état initial. Azure Cache pour Redis prend en charge l’exécution d’un lot de commandes comme [transaction](https://redis.io/topics/transactions) unique. |

## <a name="azure-cache-for-redis-offerings"></a>Offres du cache Azure pour Redis

Les niveaux suivants sont proposés pour le cache Azure pour Redis :

| Niveau | Description |
|---|---|
De base | Cache incluant un seul nœud. Ce niveau prend en charge plusieurs tailles de mémoire (250 Mo-53 Go). Il est idéal pour les charges de travail de développement/test et non critiques. Aucun contrat de niveau de service (SLA) ne couvre le niveau De base. |
| standard | Cache répliqué dans une configuration primaire/secondaire à deux nœuds, géré par Azure et assorti d’un contrat SLA garantissant une haute disponibilité (99,9 %) |
| Premium | Le niveau Premium est le niveau adapté aux entreprises. Les caches de niveau Premium prennent en charge un plus grand nombre de fonctionnalités et assurent un débit plus élevé avec une latence plus faible. Les caches du niveau Premium sont déployés sur du matériel plus puissant offrant de meilleures performances par rapport au niveau De base ou Standard. Par conséquent, le débit d’un cache de même taille est plus élevé avec le niveau Premium qu’avec le niveau Standard. |

> [!TIP]
> Pour plus d’informations sur la taille, le débit et la bande passante des caches Premium, consultez le [Forum aux questions sur le cache Azure pour Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).
>

Vous pouvez mettre à l’échelle votre cache vers un niveau supérieur après l’avoir créé. En revanche, il n’est pas possible de le mettre à l’échelle vers un niveau inférieur. Pour connaître la procédure de mise à l’échelle, consultez [Guide pratique pour mettre à l’échelle le cache Azure pour Redis](cache-how-to-scale.md) et [Guide pratique pour automatiser une opération de mise à l’échelle](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparison"></a>Comparaison des fonctionnalités

La page [Tarifs du cache Azure pour Redis](https://azure.microsoft.com/pricing/details/cache/) fournit une comparaison détaillée des différents niveaux. Le tableau suivant décrit quelques-unes des fonctionnalités prises en charge par niveau :

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

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide d’application web ASP.NET](cache-web-app-howto.md) Créer une application web ASP.NET simple qui utilise un cache Azure pour Redis.
* [Démarrage rapide .NET](cache-dotnet-how-to-use-azure-redis-cache.md) Créer une application .NET qui utilise un cache Azure pour Redis.
* [Démarrage rapide .NET Core](cache-dotnet-core-quickstart.md) Créer une application .NET Core qui utilise un cache Azure pour Redis.
* [Démarrage rapide Node.js](cache-nodejs-get-started.md) Créer une application Node.js simple qui utilise un cache Azure pour Redis.
* [Démarrage rapide Java](cache-java-get-started.md) Créer une application Java simple qui utilise un cache Azure pour Redis.
* [Démarrage rapide Python](cache-python-get-started.md) Créer une application Python qui utilise un cache Azure pour Redis.
