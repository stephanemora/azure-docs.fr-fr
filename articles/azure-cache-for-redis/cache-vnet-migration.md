---
title: Migrer des caches d’injection VNet vers des caches Private Link
description: Découvrez comment migrer votre Azure Cache pour les caches de réseau virtuel (VNet) Redis vers des caches Private Link.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: 8ab6acaf1ff6742db0b6dc750e39803123829742
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525200"
---
# <a name="migrate-from-vnet-injection-caches-to-private-link-caches"></a>Migrer des caches d’injection VNet vers des caches Private Link
Cet article décrit un certain nombre d’approches pour migrer un Azure Cache pour les instances de cache injectées du Réseau virtuel Redis vers Azure Cache pour les instances de cache Private Link Redis. 

[Azure Private Link](../private-link/private-link-overview.md) simplifie l’architecture réseau et sécurise la connexion entre points de terminaison dans Azure. Vous pouvez vous connecter à une instance de Cache Azure depuis votre réseau virtuel via un point de terminaison privé, auquel est attribuée une adresse IP privée dans un sous-réseau au sein du réseau virtuel. Les avantages de l’utilisation de Private Link Azure pour Azure Cache pour Redis incluent : 

* **Flexibilité du niveau** – Private Link Azure est pris en charge sur tous nos niveaux. de Base, Standard, Premium, Entreprise et Entreprise Flash. Par rapport à l’injection de Réseau virtuel, lequel est uniquement disponible sur notre niveau Premium.   

* **Gestion des règles de Groupe de sécurité réseau simplifiée (NSG)** - les règles NSG n’ont pas besoin d’être configurées pour adhérer aux exigences d’Azure Cache pour Redis.

* **Prise en charge de la Stratégie Azure**  – Assurez-vous que tous les caches de votre organisation sont créés avec Private Link et contrôlez les caches existants de votre organisation afin de vérifier qu’ils utilisent tous Private Link.

## <a name="migration-options"></a>Options de migration

Vous pouvez passer d’une injection VNet à Private Link à l’aide de différentes méthodes. Selon l’emplacement de votre cache et la manière dont votre application interagit avec, une méthode sera plus utile que les autres. Certaines stratégies de migration fréquemment utilisées sont détaillées ci-dessous. 

### <a name="if-youre-using-any-combination-of-geo-replication-clustering-or-arm-vnet"></a>Si vous utilisez une combinaison de géo-réplication, de clustering ou de réseau virtuel ARM :

   | Option       | Avantages | Inconvénients |
   | ------------ | ---------- | ------------- |
   | Double-écriture de données dans deux caches | Aucune perte de données, ni aucun temps d’arrêt. Opérations ininterrompues du cache existant. Test plus facile du nouveau cache. | Nécessite deux caches pendant une période prolongée. | 
   | Créer un cache | Plus simple à implémenter. | Vous devez remplir de nouveau le cache, ce qui peut ne pas fonctionner avec de nombreuses applications. |
   | Exporter et importer des données via un fichier RDB | La migration des données est obligatoire. | Certaines données peuvent être perdues si elles sont écrites dans le cache existant après la génération du fichier RDB. | 
   | Migrer des données par programme | Contrôle total sur la façon dont les données sont déplacées. | Requiert du code personnalisé. | 

### <a name="write-to-two-redis-caches-simultaneously-during-migration-period"></a>Écrire simultanément dans deux caches Redis au cours de la période de migration

Au lieu de déplacer les données directement entre les caches, vous pouvez utiliser votre application pour écrire des données dans un cache existant et un nouveau cache que vous configurez. L’application continuera à lire les données du cache existant. Lorsque le nouveau cache disposera des données nécessaires, vous basculerez l’application vers ce cache et mettrez l’ancien hors service. Supposons, par exemple, que vous utilisez Redis comme magasin de sessions et que les sessions d’application sont valides pendant sept jours. Après avoir écrit dans les deux caches pendant une semaine, vous avez la certitude que le nouveau cache contient toutes les informations de session non expirées. Vous pouvez, en toute sécurité, l’utiliser à partir de ce point, sans vous soucier des pertes de données.

Les étapes générales pour implémenter cette option sont les suivantes :

1. Créez un nouveau [Azure Cache pour l’instance Redis avec des points de terminaison privés](cache-private-link.md) de la même taille (ou une taille plus grande) que le cache existant.

2. Modifiez le code de l’application pour écrire à la fois dans la nouvelle instance et celle d’origine.

3. Continuez la lecture des données à partir de l’instance d’origine jusqu’à ce que la nouvelle instance soit suffisamment remplie avec des données.

4. Mettez à jour le code de l’application pour qu’elle lise et écrive à partir de la nouvelle instance uniquement.

5. Supprimez l'instance d'origine.

### <a name="create-a-new-azure-cache-for-redis"></a>Créer une instance Azure Cache pour Redis

Cette approche n’est techniquement pas une migration. Si la perte de données n’est pas un problème, le moyen le plus simple de passer à Azure Cache pour Redis consiste à créer une instance de cache et à y connecter votre application. Par exemple, si vous utilisez Redis comme cache de recherche d’enregistrements de base de données, vous pouvez facilement reconstruire le cache à partir de zéro.

Les étapes générales pour implémenter cette option sont les suivantes :

1. Créer un nouveau [Azure Cache pour l’instance Redis avec des points de terminaison privé](cache-private-link.md).

2. Mettez à jour votre application pour utiliser la nouvelle instance.

3. Supprimez l’ancienne instance de Redis.

### <a name="export-data-to-an-rdb-file-and-import-it-into-azure-cache-for-redis-premium-tier-only"></a>Exporter des données vers un fichier RDB et les importer dans Azure Cache pour Redis (niveau Premium uniquement)

Redis open source définit un mécanisme standard pour la capture d’un instantané du jeu de données en mémoire d’un cache et son enregistrement dans un fichier. Ce fichier, appelé RDB, peut être lu par un autre cache Redis. [Le niveau Premium d’Azure Cache pour Redis](cache-overview.md#service-tiers) prend en charge l’importation de données dans une instance de cache par le biais de fichiers RDB. Vous pouvez utiliser un fichier RDB pour transférer des données à partir d’un cache existant vers Azure Cache pour Redis.

> [!IMPORTANT]
> Le format de fichier RDB peut changer entre les versions de Redis et risque de ne pas assurer la compatibilité descendante. La version de Redis du cache à partir duquel vous exportez doit être inférieure ou égale à la version fournie par Azure Cache pour Redis.
>

Les étapes générales pour implémenter cette option sont les suivantes :

1. Créez un nouveau [Azure Cache pour l’instance Redis avec des points de terminaison privés](cache-private-link.md) dans le niveau Premium qui soit de la même taille (ou une taille plus grande) que le cache existant.

2. Enregistrez un instantané du cache Redis existant. Vous pouvez [configurer des instructions Redis pour enregistrer régulièrement des instantanés](https://redis.io/topics/persistence) ou exécuter le processus manuellement à l’aide des commandes [SAVE](https://redis.io/commands/save) et [BGSAVE](https://redis.io/commands/bgsave). Par défaut, le fichier RDB est nommé « dump.rdb » et se trouve dans le chemin d’accès spécifié dans le fichier de configuration *redis.conf*.

    > [!NOTE]
    > Si vous migrez des données dans Azure Cache pour Redis, consultez [ces instructions sur la façon d’exporter un fichier RDB](cache-how-to-import-export-data.md) ou utilisez [l’applet de commande Export de PowerShell](/powershell/module/azurerm.rediscache/export-azurermrediscache) à la place.
    >

3. Copiez le fichier RDB dans un compte de stockage Azure dans la région où se trouve votre nouveau cache. Pour ce faire, vous pouvez utiliser AzCopy.

4. Importez le fichier RDB dans le nouveau cache à l’aide de ces [instructions d’importation](cache-how-to-import-export-data.md) ou de l’[applet de commande Import de PowerShell](/powershell/module/azurerm.rediscache/import-azurermrediscache).

5. Mettez à jour votre application pour utiliser la nouvelle instance de cache.

### <a name="migrate-programmatically"></a>Migrer par programme

Créez un processus de migration personnalisé en lisant par programmation les données d’un cache existant et en les écrivant dans Azure Cache pour Redis. Cet [outil open source](https://github.com/deepakverma/redis-copy) peut être utilisé pour copier des données d’une instance Azure Cache pour Redis vers une autre. Cet outil est utile pour déplacer des données entre des instances de cache dans différentes régions Azure Cache. Une [version compilée](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip) est également disponible. Vous pourriez également trouver le code source utile pour l’écriture de votre propre outil de migration.

> [!NOTE]
> Cet outil n’est pas officiellement pris en charge par Microsoft. 
>

Les étapes générales pour implémenter cette option sont les suivantes :

1. Créez une machine virtuelle dans la région où se trouve le cache existant. Si votre jeu de données est volumineux, choisissez une machine virtuelle relativement puissante pour réduire le temps de copie.

2. Créer un nouveau [Cache Azure pour l’instance Redis avec des points de terminaison privé](cache-private-link.md)

3. Videz les données du nouveau cache pour vous assurer qu’il est vide. Cette étape est requise, car l’outil de copie lui-même ne remplace aucune clé existante dans le cache cible.

    > [!IMPORTANT]
    > Veillez à ne pas vider le cache source.
    >

4. Utilisez une application telle que l’outil open source ci-dessus pour automatiser la copie des données du cache source vers le cache cible. N’oubliez pas que le processus de copie peut prendre un certain temps en fonction de la taille de votre jeu de données.


## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur les [options d’isolement de réseau](cache-network-isolation.md). 
* Découvrez comment [configurer des points de terminaison privés pour tous les niveaux d’Azure Cache pour Redis](cache-private-link.md).