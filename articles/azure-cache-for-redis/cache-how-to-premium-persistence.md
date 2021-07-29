---
title: Configurer la persistance des données - Azure Cache pour Redis Premium
description: Découvrez comment configurer et gérer la persistance des données pour vos instances de Cache Redis Azure de niveau Premium
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: d115495f56a9e64672682a92d5837db48dbf052d
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110099808"
---
# <a name="configure-data-persistence-for-a-premium-azure-cache-for-redis-instance"></a>Configurer la persistance des données pour une instance Azure Cache pour Redis Premium

La [persistance Redis](https://redis.io/topics/persistence) vous permet de conserver les données stockées dans Redis. Vous pouvez également prendre des instantanés et sauvegarder les données. En cas de défaillance matérielle, vous chargez les données. La possibilité de rendre des données persistantes constitue un énorme avantage par rapport aux niveaux De base ou Standard où toutes les données sont stockées en mémoire. Une perte de données est possible si une défaillance se produit là où les nœuds de cache sont en panne.

Azure Cache pour Redis offre une persistance de Redis à l’aide de la base de données Redis (RDB) et du fichier en ajout seul (AOF) :

* **Persistance RDB** : lorsque vous utilisez la persistance RDB, Azure Cache pour Redis conserve un instantané d’Azure Cache pour Redis dans un Redis sur disque au format binaire. L’instantané est enregistré dans un compte de stockage Azure. La fréquence de sauvegarde configurable détermine la fréquence de conservation de l’instantané. Si un événement catastrophique se produit et provoque la désactivation du cache principal et du réplica, le cache est reconstruit à l’aide de l’instantané le plus récent. Découvrez-en plus sur les [avantages](https://redis.io/topics/persistence#rdb-advantages) et les [inconvénients](https://redis.io/topics/persistence#rdb-disadvantages) de la persistance RDB.
* **Persistance AOF** : lorsque vous utilisez la persistance AOF, Azure Cache pour Redis enregistre chaque opération d’écriture dans un journal. Le journal est enregistré au moins une fois par seconde dans un compte de stockage Azure. Si un événement catastrophique se produit et provoque la désactivation du cache principal et du réplica, le cache est reconstruit à l’aide des opérations d’écriture stockées. Découvrez-en plus sur les [avantages](https://redis.io/topics/persistence#aof-advantages) et les [inconvénients](https://redis.io/topics/persistence#aof-disadvantages) de la persistance AOF.

La persistance écrit des données Redis dans un compte Stockage Azure que vous possédez et gérez. Vous configurez la **nouvelle instance Azure Cache pour Redis** sur la gauche lors de la création du cache. Pour les caches Premium existants, utilisez le **menu Ressource**.

> [!NOTE]
>
> Stockage Azure chiffre automatiquement vos données lors de leur conservation. Vous pouvez utiliser vos propres clés de chiffrement. Pour plus d’informations, consultez [Clés gérées par le client avec Azure Key Vault](../storage/common/storage-service-encryption.md).
>
>

## <a name="set-up-data-persistence"></a>Configurer la persistance des données

1. Pour créer un cache Premium, connectez-vous au [portail Azure](https://portal.azure.com), puis sélectionnez **Créer une ressource**. Vous pouvez créer des caches dans le portail Azure. Vous pouvez également les créer à l’aide de modèles Resource Manager, de PowerShell ou d’Azure CLI. Pour plus d’informations sur la création d’un cache Azure pour Redis, consultez la section [Création d’un cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Créer une ressource.":::
  
2. Dans la page **Nouvelle**, sélectionnez **Bases de données**, puis **Azure Cache pour Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Sélectionnez Azure Cache pour Redis.":::

3. Dans la page **Nouveau cache Redis**, configurez les paramètres du nouveau cache Premium.
  
   | Paramètre      | Valeur suggérée  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nom DNS** | Entrez un nom globalement unique. | Le nom du cache doit être une chaîne de 1 à 63 caractères ne contenant que des chiffres, des lettres ou des traits d’union. Le nom doit commencer et se terminer par un chiffre ou une lettre, et ne peut pas contenir de traits d’union consécutifs. Le *nom d’hôte* de votre instance de cache sera *\<DNS name>.redis.cache.windows.net*. |
   | **Abonnement** | Dans la liste déroulante, sélectionnez votre abonnement. | Abonnement sous lequel créer cette nouvelle instance d’Azure Cache pour Redis. |
   | **Groupe de ressources** | Dans la liste déroulante, sélectionnez un groupe de ressources ou choisissez **Créer nouveau**, puis entrez un nouveau nom de groupe de ressources. | Nom du groupe de ressources dans lequel créer votre cache et d’autres ressources. En plaçant toutes les ressources de votre application dans un seul groupe de ressources, vous pouvez facilement les gérer ou les supprimer ensemble. |
   | **Lieu** | Dans la liste déroulante, sélectionnez un emplacement. | Choisissez une [Région](https://azure.microsoft.com/regions/) proche d’autres services qui utiliseront votre cache. |
   | **Type de cache** | Dans la liste déroulante, sélectionnez un cache Premium pour configurer les fonctionnalités Premium. Pour plus d’informations, consultez la page [Tarification Azure Cache pour Redis](https://azure.microsoft.com/pricing/details/cache/). |  Le niveau tarifaire détermine la taille, les performances et les fonctionnalités disponibles pour le cache. Pour plus d’informations, consultez [Présentation du cache Azure pour Redis](cache-overview.md). |

4. Sélectionnez l’onglet **Réseau** ou sélectionnez le bouton **Réseau** au bas de la page.

5. Sous l’onglet **Réseau**, sélectionnez votre méthode de connectivité. Pour les instances de cache Premium, vous connectez de manière publique, via des adresses IP ou des points de terminaison de service publics. Vous vous connectez en privé à l’aide d’un point de terminaison privé.

6. Sélectionnez le bouton **Suivant : Avancé** ou sélectionnez le bouton **Suivant : Avancé** en bas de la page.

7. Sous l’onglet **Avancé** d’une instance de cache Premium, configurez les paramètres pour le port non TLS, le clustering et la persistance des données. Pour la persistance des données, vous pouvez choisir la persistance **RDB** ou **AOF**.

8. Pour activer la persistance RDB, sélectionnez **RDB** et configurez les paramètres.
  
   | Paramètre      | Valeur suggérée  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Fréquence de sauvegarde** | Utilisez la liste déroulante et sélectionnez un intervalle de sauvegarde. Vous avez le choix entre **15 minutes**, **30 minutes**, **60 minutes**, **6 heures**, **12 heures** et **24 heures**. | Cet intervalle débute au moment où l’opération de sauvegarde précédente s’est terminée correctement. Une fois l’intervalle écoulé, une nouvelle sauvegarde démarre. |
   | **Compte de stockage** | Dans la liste déroulante, sélectionnez votre compte de stockage. | Choisissez un compte de stockage situé dans la même région et le même abonnement que le cache. Un compte **Stockage Premium** est recommandé, car ce type de stockage offre un débit plus élevé.  |
   | **Clé de stockage** | Dans la liste déroulante, choisissez la **clé primaire** ou la **clé secondaire** à utiliser. | Si la clé de stockage pour votre compte de persistance est régénérée, vous devez reconfigurer la clé dans la liste déroulante **Clé de stockage**. |

    La première sauvegarde démarre une fois que l’intervalle de fréquence de sauvegarde est écoulé.
  
   > [!NOTE]
   > Lorsque les fichiers RDB sont sauvegardés dans le stockage, ils sont stockés sous forme d’objets blob de pages.
  
9. Pour activer la persistance AOF, sélectionnez **AOF** et configurez les paramètres.

   | Paramètre      | Valeur suggérée  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Premier compte de stockage** | Dans la liste déroulante, sélectionnez votre compte de stockage. | Ce compte de stockage doit se trouver dans la même région et le même abonnement que le cache. Un compte **Stockage Premium** est recommandé, en raison du début plus élevé de ce type de stockage. |
   | **Première clé de stockage** | Dans la liste déroulante, choisissez la **clé primaire** ou la **clé secondaire** à utiliser. | Si la clé de stockage pour votre compte de persistance est régénérée, vous devez reconfigurer la clé dans la liste déroulante **Clé de stockage**. |
   | **Second compte de stockage** | (Facultatif) Dans la liste déroulante, sélectionnez votre compte de stockage secondaire. | Vous pouvez éventuellement configurer un autre compte de stockage. Si un deuxième compte de stockage est configuré, les opérations d’écriture dans le cache de réplica sont enregistrées dans ce deuxième compte de stockage. |
   | **Seconde clé de stockage** | (Facultatif) Dans la liste déroulante, choisissez la **clé primaire** ou la **clé secondaire** à utiliser. | Si la clé de stockage pour votre compte de persistance est régénérée, vous devez reconfigurer la clé dans la liste déroulante **Clé de stockage**. |

    Lorsque la persistance AOF est activée, les opérations d’écriture dans le cache sont enregistrées dans le compte de stockage nommé (ou les comptes si vous avez configuré un deuxième compte de stockage). En cas de défaillance catastrophique affectant à la fois le cache principal et le réplica, le journal AOF stocké est utilisé pour reconstruire le cache.

10. Sélectionnez l’onglet **Suivant : Avancé** ou sélectionnez le bouton **Suivant : Étiquettes** au bas de la page.

11. Si vous le voulez, sous l’onglet **Étiquettes**, entrez le nom et la valeur si vous souhaitez catégoriser la ressource.

12. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers l’onglet Vérifier + créer où Azure valide votre configuration.

13. Une fois que le message vert Validation réussie s’affiche, sélectionnez **Créer**.

La création du cache prend un certain temps. Vous pouvez surveiller la progression dans la page **Vue d’ensemble** du Azure Cache pour Redis. Lorsque **État** indique **En cours d’exécution**, le cache est prêt pour utilisation.

## <a name="persistence-faq"></a>Forum aux questions sur la persistance

La liste suivante présente différentes réponses aux questions les plus fréquemment posées sur la persistance du Cache Redis Azure.

* [Puis-je activer la persistance sur un cache créé précédemment ?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Puis-je activer la persistance AOF et RDB en même temps ?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Quel modèle de persistance dois-je choisir ?](#which-persistence-model-should-i-choose)
* [Que se passe-t-il si j’ai mis à l’échelle vers une taille différente et si une sauvegarde antérieure à l’opération de mise à l’échelle, est restaurée ?](#what-happens-if-ive-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)
* [Puis-je utiliser le même compte de stockage pour une persistance dans deux caches différents ?](#can-i-use-the-same-storage-account-for-persistence-across-two-different-caches)
* [Suis-je facturé pour le stockage utilisé dans la persistance des données](#will-i-be-charged-for-the-storage-being-used-in-data-persistence)

### <a name="rdb-persistence"></a>Persistance RDB

* [Puis-je modifier la fréquence de sauvegarde RDB après avoir créé le cache ?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Pourquoi y a-t-il un délai supérieur à 60 minutes entre les sauvegardes si la fréquence de sauvegarde RDB est de 60 minutes ?](#why-is-there-more-than-60-minutes-between-backups-when-i-have-an-rdb-backup-frequency-of-60-minutes)
* [Qu’advient-il des anciennes sauvegardes RDB quand une nouvelle sauvegarde est effectuée ?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>Persistance AOF

* [Quand dois-je utiliser un deuxième compte de stockage ?](#when-should-i-use-a-second-storage-account)
* [La persistance AOF affecte-t-elle le débit, la latence ou les performances de mon cache ?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Comment puis-je supprimer le deuxième compte de stockage ?](#how-can-i-remove-the-second-storage-account)
* [Qu’est-ce qu’une réécriture et comment affecte-t-elle mon cache ?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [À quoi dois-je attendre lors de la mise à l’échelle d’un cache avec la persistance AOF activée ?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Comment sont organisées mes données AOF dans le stockage ?](#how-is-my-aof-data-organized-in-storage)

### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Puis-je activer la persistance sur un cache créé précédemment ?

Oui, la persistance Redis peut être configurée lors de la création du cache ou sur les caches Premium existants.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Puis-je activer la persistance AOF et RDB en même temps ?

Non, vous pouvez activer RDB ou AOF, mais pas les deux en même temps.

### <a name="which-persistence-model-should-i-choose"></a>Quel modèle de persistance dois-je choisir ?

La persistance AOF enregistre chaque écriture dans un journal, ce qui a un impact significatif sur le débit. par rapport à la persistance AOF avec RDB, qui enregistre les sauvegardes selon l’intervalle de sauvegarde configuré avec un impact minimal sur les performances. Choisissez la persistance AOF si votre objectif principal est de minimiser la perte de données et si vous pouvez gérer un débit inférieur pour votre cache. Choisissez la persistance RDB si vous souhaitez maintenir un débit optimal de votre cache mais avez quand même besoin d’un mécanisme de récupération de données.

* Découvrez-en plus sur les [avantages](https://redis.io/topics/persistence#rdb-advantages) et les [inconvénients](https://redis.io/topics/persistence#rdb-disadvantages) de la persistance RDB.
* Découvrez-en plus sur les [avantages](https://redis.io/topics/persistence#aof-advantages) et les [inconvénients](https://redis.io/topics/persistence#aof-disadvantages) de la persistance AOF.

Pour plus d’informations sur les performances lors de l’utilisation de persistance AOF, consultez [La persistance affecte-t-elle le débit, la latence ou les performances de mon cache ?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-ive-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Que se passe-t-il si j’ai mis à l’échelle vers une taille différente et si une sauvegarde antérieure à l’opération de mise à l’échelle, est restaurée ?

Pour la persistance RDB et AOF :

* Si vous avez effectué une mise à l’échelle vers une plus grande taille, il n’y a aucun effet.
* Si vous avez mis à l’échelle vers une taille plus petite et que vous avez un paramètre personnalisé de [bases de données](cache-configure.md#databases) supérieur à la [limite des bases de données](cache-configure.md#databases) pour votre nouvelle taille, les données de ces bases de données ne sont pas restaurées. Pour en savoir plus, voir [Les paramètres personnalisés de mes bases de données sont-ils affectés au cours de la mise à l’échelle ?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Si vous avez mis à l’échelle vers une plus petite taille et que l’espace est insuffisant pour contenir toutes les données issues de la dernière sauvegarde, les clés sont supprimées lors du processus de restauration.  En général, les clés sont éliminées avec la stratégie d’éviction [allkeys-lru](https://redis.io/topics/lru-cache).

### <a name="can-i-use-the-same-storage-account-for-persistence-across-two-different-caches"></a>Puis-je utiliser le même compte de stockage pour la persistance dans deux caches différents ?

Oui, vous pouvez utiliser le même compte de stockage pour la persistance dans deux caches différents

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Puis-je modifier la fréquence de sauvegarde RDB après avoir créé le cache ?

Oui, vous pouvez modifier la fréquence de sauvegarde pour la persistance RDB dans **Persistance des données** sur la gauche. Pour obtenir des instructions, consultez la page Configuration de la persistance Redis.

### <a name="why-is-there-more-than-60-minutes-between-backups-when-i-have-an-rdb-backup-frequency-of-60-minutes"></a>Pourquoi, si la fréquence de sauvegarde RDB est de 60 minutes, y a-t-il un délai supérieur à 60 minutes entre les sauvegardes ?

L’intervalle de fréquence de sauvegarde avec la persistance RDB ne démarre qu’une fois le processus de sauvegarde précédent terminé. Si la fréquence de sauvegarde est de 60 minutes et que l’exécution d’un processus de sauvegarde prend 15 minutes, la sauvegarde suivante ne démarre que 75 minutes après l’heure de début de la sauvegarde précédente.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Qu’advient-il des anciennes sauvegardes RDB quand une nouvelle sauvegarde est effectuée ?

Toutes les sauvegardes avec la persistance RDB à l’exception de la plus récente sont supprimées automatiquement. Cette suppression peut ne pas avoir lieu immédiatement, mais les anciennes sauvegardes ne sont pas conservées indéfiniment.

### <a name="when-should-i-use-a-second-storage-account"></a>Quand dois-je utiliser un deuxième compte de stockage ?

Utilisez un deuxième compte de stockage pour la persistance AOF lorsque vous pensez que vous avez plus d’opérations que prévu définies sur le cache.  La configuration du deuxième compte de stockage permet de vous assurer que votre cache n’atteindra pas les limites de bande passante de stockage.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>La persistance AOF affecte-t-elle le débit, la latence ou les performances de mon cache ?

La persistance AOF affecte le débit d’environ 15 à 20 % lorsque le cache n’a pas atteint la charge maximale (charges de l’UC et du serveur inférieures à 90 %). Il ne devrait pas y avoir de problèmes de latence lorsque le cache se trouve dans ces limites. Toutefois, le cache atteindra plus rapidement ces limites avec la persistance AOF activée.

### <a name="how-can-i-remove-the-second-storage-account"></a>Comment puis-je supprimer le deuxième compte de stockage ?

Vous pouvez supprimer le compte de stockage secondaire pour la persistance AOF en définissant le deuxième compte de stockage de manière à ce qu’il soit identique au premier compte de stockage. Pour les caches existants, **Persistance des données** sur la gauche est accessible à partir du **menu Ressources** de votre cache. Pour désactiver la persistance AOF, sélectionnez **Désactivé**.

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Qu’est-ce qu’une réécriture et comment affecte-t-elle mon cache ?

Lorsque le fichier AOF devient suffisamment volumineux, une réécriture est automatiquement mise en file d’attente dans le cache. La réécriture redimensionne le fichier AOF avec l’ensemble minimal d’opérations nécessaires pour créer le jeu de données en cours. Durant les réécritures, vous pouvez vous attendre à atteindre plus rapidement les limites de performances, en particulier lors du traitement de grands jeux de données. Les réécritures s’effectueront moins souvent au fur et à mesure que le fichier AOF deviendra volumineux, mais elles prendront un temps considérable le cas échéant.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>À quoi dois-je attendre lors de la mise à l’échelle d’un cache avec la persistance AOF activée ?

Si le fichier AOF est particulièrement volumineux au moment de la mise à l’échelle, attendez-vous à ce que l’opération de mise à l’échelle soit plus longue que prévu, étant donné qu’elle rechargera le fichier une fois la mise à l’échelle terminée.

Pour en savoir plus sur la mise à l’échelle, consultez [Que se passe-t-il si j’ai mis à l’échelle vers une taille différente et si une sauvegarde antérieure à l’opération de mise à l’échelle est restaurée ?](#what-happens-if-ive-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Comment sont organisées mes données AOF dans le stockage ?

Les données stockées dans des fichiers AOF sont divisées en plusieurs objets blob de pages par nœud afin d’augmenter les performances d’enregistrement des données dans le stockage. Le tableau suivant montre comment de nombreux objets blob de pages sont utilisés pour chaque niveau tarifaire :

| Niveau Premium | Objets blob |
|--------------|-------|
| P1           | 4 par partition    |
| P2           | 8 par partition    |
| P3           | 16 par partition   |
| P4           | 20 par partition   |

Lorsque le clustering est activé, chaque partition dans le cache a son propre ensemble d’objets blob de pages, comme indiqué dans le tableau précédent. Par exemple, un cache P2 avec trois partitions distribue son fichier AOF entre 24 objets blob de pages (huit objets blob par partition, avec trois partitions).

Après une réécriture, deux jeux de fichiers AOF se trouvent dans le stockage. Les réécritures se produisent en arrière-plan et s’ajoutent au premier jeu de fichiers. Les opérations de définition, envoyées au cache lors de la réécriture, sont ajoutées au second jeu. Une sauvegarde est stockée temporairement pendant la réécriture en cas de défaillance. La sauvegarde est rapidement supprimée après la fin d’une réécriture.

### <a name="will-i-be-charged-for-the-storage-being-used-in-data-persistence"></a>Suis-je facturé pour le stockage utilisé dans la persistance des données ?

Oui, vous serez facturé pour le stockage utilisé selon le modèle de tarification du compte de stockage utilisé.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les fonctionnalités d’Azure Cache pour Redis.

* [Niveaux de service Premium Azure Cache pour Redis](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
