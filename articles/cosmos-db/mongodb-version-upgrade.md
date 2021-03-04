---
title: Mettre à niveau la version Mongo de votre API Azure Cosmos DB pour le compte MongoDB
description: Comment mettre à niveau la version de protocole filaire MongoDB pour l’API existante Azure Cosmos DB pour les comptes MongoDB
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: chrande
ms.openlocfilehash: 1818838a68c2712336a3515b2a82b5fdd518d237
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661169"
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

1. À partir du portail Azure, accédez au panneau de vue d’ensemble de votre compte d’API Azure Cosmos DB pour MongoDB. Vérifiez que la version actuelle du serveur correspond à celle que vous voulez.

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="Portail Azure avec vue d'ensemble du compte MongoDB" border="false":::

2. Dans les options sur la gauche, sélectionnez le panneau `Features`. Cela permet d’afficher les fonctionnalités de niveau compte disponibles pour votre compte de base de données.

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="Portail Azure avec vue d’ensemble du compte MongoDB avec le panneau des fonctionnalités mis en surbrillance" border="false":::

3. Cliquez sur la ligne `Upgrade Mongo server version`. Si vous ne voyez pas cette option, votre compte peut ne pas être éligible pour cette mise à niveau. Créez un [ticket de support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) si c’est le cas.

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="Panneau de fonctionnalités avec options." border="false":::

4. Passez en revue les informations relatives à la mise à niveau. Cliquez sur `Enable` dès que vous êtes prêt à démarrer le processus.

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="Guide de mise à niveau développé." border="false":::

5. Après le démarrage du processus, le menu `Features` affiche l’état de la mise à niveau. L’état passe de `Pending` à `In Progress`, puis à `Upgraded`. Ce processus n’affecte pas les fonctionnalités ou opérations existantes du compte de base de données.

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="État de la mise à niveau après le lancement." border="false":::

6. Une fois la mise à niveau terminée, l’état s’affiche comme `Upgraded`. Cliquez dessus pour en savoir plus sur les étapes et les actions que vous devez suivre pour finaliser le processus. Veuillez [contacter le support technique](https://azure.microsoft.com/en-us/support/create-ticket/) en cas de problème lors du traitement de votre demande.

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="État du compte mis à niveau." border="false":::

7. 
    1. Si vous avez effectué une mise à niveau à partir de la version 3.2, revenez au panneau `Overview` et copiez la nouvelle chaîne de connexion à utiliser dans votre application. L’ancienne chaîne de connexion utilisée pour la version 3.2 continuera de fonctionner. Pour garantir une expérience cohérente, toutes vos applications doivent utiliser le nouveau point de terminaison.
    2. Si vous avez effectué une mise à niveau à partir de la version 3.6, votre chaîne de connexion existante sera mise à niveau vers la version spécifiée et devra continuer à être utilisée.

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="Nouveau panneau de vue d’ensemble." border="false":::


## <a name="how-to-downgrade"></a>Passage à une version antérieure
Vous pouvez également passer votre compte de la version 4.0 à la version 3.6 en suivant les mêmes étapes que celles décrites dans la section « Mise à niveau ». 

Si vous avez effectué une mise à niveau de la version 3.2 vers la version 4.0 ou 3.6 et que vous souhaitez repasser à votre version 3.2 antérieure, il vous suffit de réutiliser votre chaîne de connexion (3.2) précédente avec l’hôte `accountname.documents.azure.com` qui reste la version active après la mise à niveau de la version 3.2.


## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [fonctionnalités de MongoDB version 4.0](mongodb-feature-support-40.md) prises en charge et non prises en charge.
- Découvrez les [fonctionnalités prises en charge et non prises en charge de MongoDB version 3.6](mongodb-feature-support-36.md).
- Pour plus d’informations, consultez [Fonctionnalités de Mongo version 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
