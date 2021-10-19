---
title: Paramètres serveur - Azure Database pour PostgreSQL - Serveur flexible
description: Décrit les paramètres serveur dans Azure Database pour PostgreSQL - Serveur flexible
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: d8690bd33f10744b6d9cc9cd671e1ee46b102793
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711896"
---
# <a name="server-parameters-in-azure-database-for-postgresql---flexible-server"></a>Paramètres serveur dans Azure Database pour PostgreSQL - Serveur flexible

> [!IMPORTANT]
> Azure Database pour PostgreSQL - Serveur flexible est en préversion

Azure Database pour PostgreSQL fournit un sous-ensemble de paramètres configurables pour chaque serveur. Pour plus d’informations sur les paramètres Postgres, consultez la [Documentation PostgreSQL](https://www.postgresql.org/docs/13/config-setting.html).

## <a name="an-overview-of-postgresql-parameters"></a>Vue d’ensemble des paramètres PostgreSQL 

Le serveur Azure Database pour PostgreSQL est pré-configuré avec des valeurs par défaut optimales pour chaque paramètre lors de la création. Les paramètres statiques requièrent un redémarrage du serveur et les paramètres qui requièrent l’accès au superutilisateur ne peuvent pas être configurés par l’utilisateur. 

Pour passer en revue les paramètres disponibles à des fins d’affichage ou de modification, nous vous recommandons d’accéder au Portail Azure et à la page Paramètres du serveur. Vous pouvez également configurer des paramètres par utilisateur ou par base de données à l’aide des commandes `ALTER DATABASE` ou `ALTER ROLE`.

>[!NOTE]
> Étant donné qu’Azure Database pour PostgreSQL est un service de base de données managée, les utilisateurs ne disposent pas d’un accès à l’hôte ou au système d’exploitation pour afficher ou modifier les fichiers de configuration tels que `postgresql.conf`. Le contenu du fichier est automatiquement mis à jour en fonction des modifications apportées aux paramètres de la page Paramètres du serveur.

:::image type="content" source="./media/concepts-server-parameters/server-parameters.png" alt-text="Page Paramètres du serveur - portail":::

Voici la liste de certains des paramètres :


   | Nom du paramètre             | Description |
|----------------------|--------|
| **max_connections** | Vous pouvez paramétrer max_connections sur un serveur Postgre flexible, où il peut être défini sur 5 000 connexions. Pour plus d’informations, consultez la [documentation relative aux limites](concepts-limits.md). | 
| **shared_buffers**    | Le paramètre « shared_buffers » change en fonction de la référence SKU sélectionnée (la référence SKU détermine la mémoire disponible). Les serveurs à usage général ont des mémoires tampons partagées de 2 Go pour 2 vCores ; les serveurs à mémoire optimisée ont des mémoires tampons partagées de 4 Go pour 2 vCores. Le paramètre shared_buffers est mis à l’échelle de façon linéaire (approximativement) au fil de l’augmentation des vCores dans un niveau. | 
| **shared_preload_libraries** | Ce paramètre est disponible pour la configuration avec un ensemble prédéfini d’extensions prises en charge. Notez que nous chargeons toujours l'extension `azure` (utilisée pour les tâches de maintenance), ainsi que l'extension `pg_stat_statements` (vous pouvez utiliser le paramètre pg_stat_statements.track pour contrôler si l’extension est active). |
| **connection_throttling** | Vous pouvez activer ou désactiver la limitation de connexion temporaire par adresse IP à la suite d’un trop grand nombre de connexions infructueuses avec des mots de passe non valides. |
 | **work_mem** | Ce paramètre spécifie la quantité de mémoire à utiliser par les opérations de tri internes et les tables de hachage avant d’écrire dans des fichiers des disques temporaires. Si votre charge de travail comporte peu de requêtes avec un tri complexe et que vous disposez d’une grande quantité de mémoire disponible, l’augmentation de ce paramètre peut permettre à Postgres d’effectuer des analyses plus volumineuses en mémoire par rapport au débordement sur le disque, ce qui est plus rapide.  Toutefois, soyez prudent, car une requête complexe peut avoir un certain nombre d’opérations de hachage qui s’exécutent simultanément. Chacune de ces opérations utilisera autant de mémoire que la valeur l’autorise avant de commencer à écrire sur des fichiers temporaires sur disque. Par conséquent, sur un système relativement occupé, l’utilisation totale de la mémoire équivaudra de nombreuses fois au paramètre work_mem individuel. Si vous décidez d’ajuster cette valeur globalement, vous pouvez utiliser la formule RAM totale * 0,25 / max_connections comme valeur initiale. Azure Database pour PostgreSQL - Serveur flexible prend en charge une plage de 4096-2097151 octets pour ce paramètre.|
| **effective_cache_size** |Le paramètre effective_cache_size estime la quantité de mémoire disponible pour la mise en cache du disque par le système d’exploitation et dans la base de données elle-même. Le planificateur de requêtes PostgreSQL décide s’il est fixe dans la mémoire RAM. Les analyses d’index sont susceptibles d’être utilisées avec des valeurs plus élevées. Sinon, les analyses séquentielles sont utilisées si la valeur est faible. Nous recommandons de définir Effective_cache_size sur 50 % de la mémoire RAM totale de l’ordinateur. |
| **maintenance_work_mem** | Le paramètre maintenance_work_mem fournit en fait la quantité maximale de mémoire à utiliser par les opérations de maintenance telles que les opérations de clé étrangère vaccum, create index et alter table add.  La valeur par défaut de ce paramètre est 64 Ko. Il est recommandé de définir cette valeur sur une valeur supérieure à work_mem. Cela peut améliorer les performances de nettoyage. |
| **effective_io_concurrency** | Définit le nombre d’opérations d’E/S de disque simultanées attendues par PostgreSQL, pouvant être exécutées simultanément. L’augmentation de cette valeur augmente le nombre d’opérations d’E/S que toute session PostgreSQL individuelle tente de lancer en parallèle. La plage autorisée est comprise entre 1 et 1000, ou zéro pour désactiver l’émission de demandes d’E/S asynchrones. Actuellement, ce paramètre affecte uniquement les analyses de segment de mémoire bitmap. |
 |**require_secure_transport** | Si votre application ne prend pas en charge la connectivité SSL au serveur, vous pouvez éventuellement désactiver le transport sécurisé à partir de votre client en modifiant la valeur de ce paramètre sur `OFF`. |

>[!NOTE]
> Lorsque vous effectuez un scale-up ou un scale-down des références SKU Azure Database pour PostgreSQL - Serveur flexible, en affectant la mémoire disponible au serveur, vous pouvez ajuster vos paramètres globaux de mémoire, tels que work_mem ou effective_cache_size en conséquence, en fonction des informations ci-dessus. 

 
## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les extensions PostgrSQL prises en charge, consultez [le document Extensions](concepts-extensions.md).
