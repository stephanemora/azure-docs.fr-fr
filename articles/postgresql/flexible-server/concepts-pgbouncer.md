---
title: PgBouncer - Azure Database pour PostgreSQL - Serveur flexible
description: Cet article fournit une vue d’ensemble de l’extension PgBouncer intégrée.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: 92068911c2df95a835de45ea6bb0ba786baf596f
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107989438"
---
# <a name="pgbouncer-in-azure-database-for-postgresql---flexible-server"></a>PgBouncer dans Azure Database pour PostgreSQL - Serveur flexible

> [!IMPORTANT]
> Le serveur flexible Azure Database pour PostgreSQL est en préversion

Azure Database pour PostgreSQL - Serveur flexible offre une solution de regroupement de connexions intégrée à [PgBouncer](https://github.com/pgbouncer/pgbouncer). Il s’agit d’un service facultatif qui peut être activé sur la base d’un serveur par base de données et qui est pris en charge avec un accès public et privé. PgBouncer s’exécute sur la même machine virtuelle que le serveur de base de données Postgres. Postgres utilise un modèle basé sur le processus pour les connexions, ce qui rend coûteuse la maintenance de nombreuses connexions inactives. Par conséquent, Postgres lui-même s’exécute dans des contraintes de ressources une fois que le serveur a exécuté plus de quelques milliers de connexions. Le principal avantage de PgBouncer est qu’il améliore les connexions inactives et les connexions à courte durée de vie sur le serveur de base de données.

PgBouncer utilise un modèle plus léger qui utilise des E/S asynchrones et uniquement les connexions Postgres réelles quand cela est nécessaire, c’est-à-dire à l’intérieur d’une transaction ouverte ou lorsqu’une requête est active. Ce modèle peut prendre en charge des milliers de connexions plus facilement avec une faible surcharge et permet la mise à l’échelle de jusqu’à 10 000 connexions avec une faible surcharge.

Lorsque cette option est activée, PgBouncer s’exécute sur le port 6432 de votre serveur de base de données. Vous pouvez modifier la configuration de la connexion de base de données de votre application pour qu’elle utilise le même nom d’hôte, mais modifiez le port sur 6432 pour commencer à utiliser PgBouncer et tirer parti de la mise à l’échelle améliorée des connexions inactives.

> [!Note]
> PgBouncer est pris en charge uniquement sur les niveaux de calcul Usage général et Mémoire optimisée.

## <a name="enabling-and-configuring-pgbouncer"></a>Activation et configuration de PgBouncer

Pour activer PgBouncer, vous pouvez accéder au panneau « Paramètres du serveur » du portail Azure et rechercher « PgBouncer », puis définir le paramètre pgbouncer.enabled sur « true » pour que PgBouncer soit activé. Il est inutile de redémarrer votre serveur. Toutefois, pour définir d’autres paramètres PgBouncer, consultez la section Limitations.

Vous pouvez configurer les paramètres de PgBouncer comme suit :

| Nom du paramètre             | Description | Default | 
|----------------------|--------|-------------|
| pgbouncer.default_pool_size | Définir la valeur de ce paramètre sur le nombre de connexions par paire utilisateur/base de données      | 50       | 
| pgBouncer.max_client_conn | Définissez la valeur de ce paramètre sur le plus grand nombre de connexions clientes à PgBouncer que vous souhaitez prendre en charge      | 5 000     | 
| pgBouncer.pool_mode | Définissez cette valeur de paramètre sur TRANSACTION pour le regroupement de transactions (ce qui est le paramètre recommandé pour la plupart des charges de travail).      | TRANSACTION     |
| pgBouncer.min_pool_size | Ajoutez plus de connexions au serveur pour le pool si le nombre actuel est inférieur à celui défini.    |   0 (désactivé)   |
| pgBouncer.stats_users | facultatif. Définissez la valeur de ce paramètre sur le nom d’un utilisateur existant, pour pouvoir vous connecter à la base de données de statistiques spéciale PgBouncer (nommée « PgBouncer »)    |      |

> [!Note] 
> La mise à niveau de PgBouncer sera gérée par Azure.

## <a name="switching-your-application-to-use-pgbouncer"></a>Basculement de votre application pour utiliser PgBouncer

Pour commencer à utiliser PgBouncer, procédez comme suit :
1. Connectez-vous à votre serveur de base de données, mais utilisez le port **6432** au lieu du port normal 5432. Vérifiez que cette connexion fonctionne
```azurecli-interactive
psql "host=myPgServer.postgres.database.azure.com port=6432 dbname=postgres user=myUser password=myPassword sslmode=require"
```
2. Testez votre application dans un environnement d’assurance qualité sur PgBouncer pour vous assurer que vous n’avez pas de problème de compatibilité. Le projet PgBouncer fournit une matrice de compatibilité, et nous vous recommandons d’utiliser le **regroupement de transactions** pour la plupart des utilisateurs : https://www.PgBouncer.org/features.html#sql-feature-map-for-pooling-modes.
3. Modifiez votre application de production pour qu’elle se connecte au port **6432** au lieu de **5432**, et surveillez toutes les erreurs de l’application susceptibles de pointer vers des problèmes de compatibilité.

> [!Note] 
> Même si vous avez activé PgBouncer, vous pouvez toujours vous connecter au serveur de base de données directement sur le port 5432 à l’aide du même nom d’hôte.

## <a name="pgbouncer-in-zone-redundant-high-availability"></a>PgBouncer et haute disponibilité redondante interzone

Dans les serveurs configurés pour la haute disponibilité redondante interzone, le serveur principal exécute PgBouncer. Vous pouvez vous connecter au serveur principal PgBouncer sur le port 6432. Après un basculement, PgBouncer est redémarré sur le serveur de secours récemment promu, qui est le nouveau serveur principal. Par conséquent, la chaîne de connexion de votre application reste la même après le basculement. 

## <a name="using-pgbouncer-with-other-connection-pools"></a>Utilisation de PgBouncer avec d’autres pools de connexions

Dans certains cas, vous disposez peut-être déjà d’un pool de connexions côté application, ou vous avez configuré PgBouncer sur votre application, par exemple en annexe d’AKS. Dans ce cas, il peut toujours être utile d’utiliser le PgBouncer intégré, car il offre des avantages en matière de mise à l’échelle des connexions inactives.

L’utilisation d’un pool côté application avec PgBouncer sur le serveur de base de données peut être bénéfique. Ici, le pool côté application offre l’avantage d’une latence de connexion initiale réduite (car l’aller-retour initial pour initialiser la connexion est beaucoup plus rapide), et le PgBouncer côté base de données fournit la mise à l’échelle des connexions inactives.

## <a name="limitations"></a>Limites
 
* PgBouncer n’est actuellement pas pris en charge avec le niveau de calcul de serveur Burstable. 
* Si vous modifiez le niveau de calcul d’Usage général ou Mémoire optimisée sur Burstable, vous perdez la capacité PgBouncer.
* À chaque redémarrage du serveur lors des opérations de mise à l’échelle, du basculement haute disponibilité ou du redémarrage, PgBouncer est également redémarré avec la machine virtuelle du serveur. Par conséquent, les connexions existantes doivent être rétablies.
* En raison d’un problème connu, le portail n’affiche pas tous les paramètres de PgBouncer. Une fois que vous avez activé PgBouncer et enregistré le paramètre, vous devez quitter l’écran des paramètres (par exemple, cliquez sur Vue d’ensemble), puis revenir à la page Paramètres. 
  
## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [concepts de mise en réseau](./concepts-networking.md)
- Serveur flexible - [Vue d’ensemble](./overview.md)
