---
title: Mettre à niveau la version Mongo de votre API Azure Cosmos DB pour le compte MongoDB
description: Comment mettre à niveau la version de protocole filaire MongoDB pour l’API existante Azure Cosmos DB pour les comptes MongoDB
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2021
ms.author: chrande
ms.openlocfilehash: 8865a16c2840b65f432de679c6dd63b285b1f760
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771801"
---
# <a name="upgrade-the-api-version-of-your-azure-cosmos-db-api-for-mongodb-account"></a>Mettre à niveau la version de l’API de votre compte API Azure Cosmos DB pour MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Cet article décrit comment mettre à niveau la version de l’API de votre compte API Azure Cosmos DB pour MongoDB. Après avoir effectué la mise à niveau, vous pourrez utiliser les fonctionnalités les plus récentes de l’API Azure Cosmos DB pour MongoDB. Le processus de mise à niveau n’interrompt pas la disponibilité de votre compte et ne consomme pas de RU/s, et ne réduit à aucun moment la capacité de la base de données. Les index ou données existants ne seront pas affectés par ce processus. 

Lors de la mise à niveau vers une nouvelle version d’API, mettez à niveau les charges de travail de développement/test avant les charges de travail de production. Il est important de mettre à niveau vos clients vers une version compatible avec la version de l’API vers laquelle vous effectuez la mise à niveau avant de mettre à niveau votre compte API Azure Cosmos DB pour MongoDB.

>[!Note]
> Actuellement, seuls les comptes éligibles utilisant la version serveur 3.2 peuvent être mis à niveau vers la version 3.6 ou 4.0. Si votre compte n’affiche pas l’option de mise à niveau, [entrez un ticket de support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-to-40-or-36"></a>Mise à niveau vers la version 4.0 ou 3.6

### <a name="benefits-of-upgrading-to-version-40"></a>Avantages de la mise à niveau vers la version 4.0

Voici les nouvelles fonctionnalités de la version 4.0 :
- Prise en charge des transactions multidocuments dans les collections non partitionnées.
- Nouveaux opérateurs d’agrégation
- Performances d’analyse améliorées
- Stockage plus rapide et plus performant

### <a name="benefits-of-upgrading-to-version-36"></a>Avantages de la mise à niveau vers la version 3.6

Voici les nouvelles fonctionnalités de la version 3.6 :
- Amélioration de la stabilité et des performances
- Prise en charge de nouvelles commandes de base de données
- Prise en charge du pipeline d’agrégation par défaut et de nouvelles étapes d’agrégation
- Prise en charge de la modification de flux
- Prise en charge des index composés
- Prise en charge inter-partitions pour les opérations suivantes : mise à jour, suppression, décompte et tri
- Amélioration des performances pour les opérations d’agrégation suivantes : $count, $skip, $limit et $group
- L’indexation de caractères génériques est maintenant prise en charge

### <a name="changes-from-version-32"></a>Modifications par rapport à la version 3.2

- Par défaut, la fonctionnalité [Server Side Retry (SSR)](prevent-rate-limiting-errors.md) est activée, ce qui évite à l’application cliente de retourner des erreurs 16500. Au lieu de cela, les requêtes reprennent jusqu’à ce qu’elles se terminent ou qu’elles expirent au terme du délai de 60 secondes.
- Le délai d’expiration par requête est de 60 secondes.
- Les collections MongoDB créées sur la nouvelle version du protocole auront uniquement la propriété `_id` indexée par défaut.

### <a name="action-required-when-upgrading-from-32"></a>Action obligatoire lors de la mise à niveau de la version 3.2

Si vous effectuez une mise à niveau à partir de la version 3.2, le suffixe du point de terminaison du compte de base de données sera mis à jour au format suivant :

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Si vous effectuez une mise à niveau à partir de la version 3.2, vous devrez remplacer le point de terminaison existant dans vos applications et pilotes qui se connectent à ce compte de base de données. **Seules les connexions qui utilisent le nouveau point de terminaison auront accès aux fonctionnalités fournies dans la nouvelle version de l’API**. Le point de terminaison de la version 3.2 précédente doit avoir le suffixe `.documents.azure.com`.

>[!Note]
> Le point de terminaison peut être légèrement différent si votre compte se trouve dans un cloud Azure souverain, gouvernemental ou limité.

## <a name="how-to-upgrade"></a>Mise à niveau

1. Connectez-vous au [portail Azure.](https://portal.azure.com/)

1. Accédez à votre API Azure Cosmos DB pour le compte MongoDB. Ouvrez le volet **Vue d’ensemble** et vérifiez que votre **version de serveur** actuelle est 3.2 ou 3.6.

    :::image type="content" source="./media/mongodb-version-upgrade/check-current-version.png" alt-text="Vérifiez la version actuelle de votre compte MongoDB à partir du portail Azure." border="true":::

1. Dans le menu de gauche, ouvrez le volet `Features`. Cela volet affiche les fonctionnalités de niveau compte disponibles pour votre compte de base de données.

1. Sélectionnez la ligne `Upgrade MongoDB server version`. Si vous ne voyez pas cette option, votre compte peut ne pas être éligible pour cette mise à niveau. Créez un [ticket de support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) si c’est le cas.

    :::image type="content" source="./media/mongodb-version-upgrade/upgrade-server-version.png" alt-text="Ouvrez le panneau Fonctionnalités et mettez à niveau votre compte." border="true":::

1. Passez en revue les informations relatives à la mise à niveau. Sélectionnez `Set server version to 4.0` (ou 3.6 en fonction de votre version actuelle).

    :::image type="content" source="./media/mongodb-version-upgrade/select-upgrade.png" alt-text="Examinez le guide de mise à niveau et sélectionnez Mettre à niveau." border="true":::

1. Après le démarrage de la mise à niveau, le menu **Fonctionnalité** est grisé et l’état est défini sur *En attente*. La mise à niveau prend environ 15 minutes : Ce processus n’affecte pas les fonctionnalités ou opérations existantes de votre compte de base de données. Une fois l’opération terminée, l’état **Mettre à jour la version de serveur MongoDB** indique la version mise à niveau. Veuillez [contacter le support technique](https://azure.microsoft.com/en-us/support/create-ticket/) en cas de problème lors du traitement de votre demande.

1. Voici quelques points à prendre en considération après la mise à niveau de votre compte :

    1. Si vous avez effectué une mise à niveau à partir de la version 3.2, revenez au panneau **Vue d’ensemble** et copiez la nouvelle chaîne de connexion à utiliser dans votre application. L’ancienne chaîne de connexion utilisée pour la version 3.2 continuera de fonctionner. Pour garantir une expérience cohérente, toutes vos applications doivent utiliser le nouveau point de terminaison.

    1. Si vous avez effectué une mise à niveau à partir de la version 3.6, votre chaîne de connexion existante sera mise à niveau vers la version spécifiée et devra continuer à être utilisée.

## <a name="how-to-downgrade"></a>Passage à une version antérieure

Vous pouvez également passer votre compte de la version 4.0 à la version 3.6 en suivant les mêmes étapes que celles décrites dans la section « Mise à niveau ».

Si vous avez effectué une mise à niveau de la version 3.2 vers la version 4.0 ou 3.6 et que vous souhaitez repasser à votre version 3.2 antérieure, il vous suffit de réutiliser votre chaîne de connexion (3.2) précédente avec l’hôte `accountname.documents.azure.com` qui reste la version active après la mise à niveau de la version 3.2.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [fonctionnalités de MongoDB version 4.0](mongodb-feature-support-40.md) prises en charge et non prises en charge.
- Découvrez les [fonctionnalités prises en charge et non prises en charge de MongoDB version 3.6](mongodb-feature-support-36.md).
- Pour plus d’informations, consultez [Fonctionnalités de Mongo version 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
