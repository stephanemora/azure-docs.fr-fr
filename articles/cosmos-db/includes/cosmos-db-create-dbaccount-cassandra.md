---
title: Fichier include
description: Fichier include
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 07/02/2021
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: fe9d735955d0a83b5959035f0c51faeab26fd4f7
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113590516"
---
1. Dans le menu du portail Azure ou dans la **page d’accueil**, sélectionnez **Créer une ressource**.

1. Dans la page **Nouveau**, recherchez et sélectionnez **Azure Cosmos DB**.

1. Dans la page **Azure Cosmos DB**, sélectionnez **Créer**.

1. Dans la page **Créer un compte Azure Cosmos DB**, entrez les paramètres de base du nouveau compte Azure Cosmos.

   |Paramètre|Valeur|Description |
   |---|---|---|
   | Abonnement|Votre abonnement|Sélectionnez l’abonnement Azure que vous souhaitez utiliser pour ce compte Azure Cosmos DB. |
   | Groupe de ressources|Création<br><br>Entrez ensuite le même nom que le nom du compte.|Sélectionnez **Créer nouveau**. Entrez ensuite le nom du nouveau groupe de ressources pour votre compte. Pour rester simple, utilisez le nom de votre compte Azure Cosmos. |
   | Nom du compte|Entrer un nom unique|Entrez un nom unique pour identifier votre compte Azure Cosmos DB. L’URI de votre compte sera *cassandra.cosmos.azure.com* apposé à votre nom de compte unique.<br><br>Le nom peut contenir uniquement des lettres minuscules, des chiffres et des traits d’union (-), et doit comporter entre 3 et 31 caractères.|
   | API|Cassandra|L’API détermine le type de compte à créer. Azure Cosmos DB fournit cinq API : Core (SQL) pour les bases de données de documents, Gremlin pour les bases de données de graphes, MongoDB pour les bases de données de documents, Table Azure et Cassandra. Vous devez créer un compte distinct pour chaque API. <br><br>Sélectionnez **Cassandra** car, dans ce guide de démarrage rapide, vous créez une table qui fonctionne avec l’API Cassandra. <br><br>[Découvrez plus d’informations sur l’API Cassandra](../cassandra-introduction.md).|
   |Emplacement|La région la plus proche de vos utilisateurs|Sélectionnez la zone géographique dans laquelle héberger votre compte Azure Cosmos DB. Utilisez l’emplacement le plus proche de vos utilisateurs pour leur donner l’accès le plus rapide possible aux données.|
   |Mode de capacité|Débit approvisionné ou serverless|Sélectionnez **Débit approvisionné** pour créer un compte dans mode de [débit approvisionné](../set-throughput.md). Sélectionnez **serverless** pour créer un compte en mode [serverless](../serverless.md).|
   |Appliquer la remise de niveau gratuit Azure Cosmos DB|**Appliquer** ou **Ne pas appliquer**|Avec le niveau gratuit d’Azure Cosmos DB, vous recevez gratuitement 1 000 RU/s et 25 Go de stockage dans un compte. Découvrez-en plus sur le [niveau gratuit](https://azure.microsoft.com/pricing/details/cosmos-db/).|

   > [!NOTE]
   > Vous pouvez avoir un seul compte Azure Cosmos DB de niveau gratuit par abonnement Azure et vous devez vous inscrire lors de la création du compte. Si vous ne voyez pas l’option permettant d’appliquer la remise de niveau gratuit, cela signifie qu’un autre compte dans l’abonnement a déjà été activé avec le niveau gratuit.

   :::image type="content" source="./media/cosmos-db-create-dbaccount-cassandra/azure-cosmos-db-create-new-account.png" alt-text="Page de nouveau compte pour l’API Cassandra Azure Cosmos DB":::

1. Sous l’onglet **Distribution globale**, configurez les informations suivantes. Dans le cadre de ce guide de démarrage rapide, vous pouvez conserver les valeurs par défaut :

   |Paramètre|Valeur|Description |
   |---|---|---|
   |Géoredondance|Désactiver|Activez ou désactivez la diffusion mondiale sur votre compte en appairant votre région avec une région correspondante. Vous pourrez ajouter d’autres régions à votre compte ultérieurement.|
   |Écritures multirégions|Désactiver|La fonctionnalité d’écritures multirégions vous permet de tirer parti du débit provisionné pour vos bases de données et conteneurs à travers le monde.|

   > [!NOTE]
   > Les options suivantes ne sont pas disponibles si vous sélectionnez **Serverless** comme **Mode de capacité** :
   > - Appliquer la remise de niveau gratuit
   > - Géo-redondance
   > - Écritures multirégions

1. Si vous le souhaitez, vous pouvez configurer des informations supplémentaires sous les onglets suivants :

   * **Réseau** :configurez l’[accès à partir d’un réseau virtuel](../how-to-configure-vnet-service-endpoint.md).
   * **Stratégie de sauvegarde** : configurez une stratégie de sauvegarde [périodique](../configure-periodic-backup-restore.md) ou [continue](../continuous-backup-restore-portal.md).
   * **Chiffrement** : utilisez une clé gérée par le service ou une [clé gérée par le client](../how-to-setup-cmk.md#create-a-new-azure-cosmos-account).
   * **Étiquettes** : les étiquettes sont des paires nom/valeur qui vous permettent de catégoriser les ressources et d’afficher une facturation centralisée en appliquant la même étiquette à plusieurs ressources et groupes de ressources.

1. Sélectionnez **Revoir + créer**.

1. Passez en revue les paramètres du compte, puis sélectionnez **Créer**. La création du compte prend quelques minutes. Attendez que la page du portail affiche **Votre déploiement est terminé**.

   :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png" alt-text="Volet Notifications du portail Azure":::

1. Sélectionnez **Accéder à la ressource** pour accéder à la page du compte Azure Cosmos DB. 
