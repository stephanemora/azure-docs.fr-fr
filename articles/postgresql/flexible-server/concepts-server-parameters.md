---
title: Paramètres serveur - Azure Database pour PostgreSQL - Serveur flexible
description: Décrit les paramètres serveur dans Azure Database pour PostgreSQL - Serveur flexible
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 526a06c1ffd110fa02fd3d412ab62882bd74f9fa
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129387370"
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
| **require_secure_transport** | Si votre application ne prend pas en charge la connectivité SSL au serveur, vous pouvez éventuellement désactiver le transport sécurisé à partir de votre client en modifiant la valeur de ce paramètre sur `OFF`. |
 
## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les extensions PostgrSQL prises en charge, consultez [le document Extensions](concepts-extensions.md).
