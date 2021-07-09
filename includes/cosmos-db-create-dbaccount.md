---
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 05/19/2021
ms.custom: include file
ms.openlocfilehash: 5ce100c6853649fd870ea0ee5e3b434868f270cd
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110456970"
---
1. Dans le menu du portail Azure ou dans la **page d’accueil**, sélectionnez **Créer une ressource**.

1. Dans la page **Nouveau**, recherchez et sélectionnez **Azure Cosmos DB**.

1. Dans la page **Azure Cosmos DB**, sélectionnez **Créer**.

1. Dans la page **Créer un compte Azure Cosmos DB**, entrez les paramètres de base du nouveau compte Azure Cosmos.

   |Paramètre|Valeur|Description |
   |---|---|---|
   |Abonnement|Nom d’abonnement|Sélectionnez l’abonnement Azure à utiliser pour ce compte Azure Cosmos. |
   |Groupe de ressources|Nom de groupe ressources|Sélectionnez un groupe de ressources ou sélectionnez **Créer**, puis entrez un nom unique pour le nouveau groupe de ressources. |
   |Nom du compte|Un nom unique|Entrez un nom pour identifier votre compte Azure Cosmos. Étant donné que *documents.azure.com* est ajouté au nom que vous fournissez pour créer votre URI, utilisez un nom unique.<br><br>Le nom peut uniquement contenir des lettres minuscules, des chiffres et le caractère de trait d’union (-). Sa longueur doit être comprise entre 3 et 31 caractères.|
   |API|Type de compte à créer|Sélectionnez **Core (SQL)** pour créer une base de données orientée document et effectuez des requêtes à l’aide de la syntaxe SQL. <br><br>L’API détermine le type de compte à créer. Azure Cosmos DB fournit cinq API : Core (SQL) et MongoDB pour les données de document, Gremlin pour les données de graphe, Table Azure et Cassandra. Actuellement, vous devez créer un compte distinct pour chaque API. <br><br>[En savoir plus sur l’API SQL](../articles/cosmos-db/introduction.md).|
   |Emplacement|La région la plus proche de vos utilisateurs|Sélectionnez la zone géographique dans laquelle héberger votre compte Azure Cosmos DB. Utilisez l’emplacement le plus proche de vos utilisateurs pour leur donner l’accès le plus rapide possible aux données.|
   |Mode de capacité|Débit approvisionné ou serverless|Sélectionnez **Débit approvisionné** pour créer un compte dans mode de [débit approvisionné](../articles/cosmos-db/set-throughput.md). Sélectionnez **serverless** pour créer un compte en mode [serverless](../articles/cosmos-db/serverless.md).|
   |Appliquer la remise de niveau gratuit Azure Cosmos DB|**Appliquer** ou **Ne pas appliquer**|Avec le niveau gratuit d’Azure Cosmos DB, vous recevez gratuitement 1 000 RU/s et 25 Go de stockage dans un compte. Découvrez-en plus sur le [niveau gratuit](https://azure.microsoft.com/pricing/details/cosmos-db/).|

   > [!NOTE]
   > Vous pouvez avoir un seul compte Azure Cosmos DB de niveau gratuit par abonnement Azure et vous devez vous inscrire lors de la création du compte. Si vous ne voyez pas l’option permettant d’appliquer la remise de niveau gratuit, cela signifie qu’un autre compte dans l’abonnement a déjà été activé avec le niveau gratuit.

   :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-detail.png" alt-text="Page de nouveau compte pour Azure Cosmos DB":::

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

   * **Réseau** :configurez l’[accès à partir d’un réseau virtuel](../articles/cosmos-db/how-to-configure-vnet-service-endpoint.md).
   * **Stratégie de sauvegarde** : configurez une stratégie de sauvegarde [périodique](../articles/cosmos-db/configure-periodic-backup-restore.md) ou [continue](../articles/cosmos-db/continuous-backup-restore-portal.md).
   * **Chiffrement** : utilisez une clé gérée par le service ou une [clé gérée par le client](../articles/cosmos-db/how-to-setup-cmk.md#create-a-new-azure-cosmos-account).
   * **Étiquettes** : les étiquettes sont des paires nom/valeur qui vous permettent de catégoriser les ressources et d’afficher une facturation centralisée en appliquant la même étiquette à plusieurs ressources et groupes de ressources.

1. Sélectionnez **Revoir + créer**.

1. Passez en revue les paramètres du compte, puis sélectionnez **Créer**. La création du compte prend quelques minutes. Attendez que la page du portail affiche **Votre déploiement est terminé**.

   :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png" alt-text="Volet Notifications du portail Azure":::

1. Sélectionnez **Accéder à la ressource** pour accéder à la page du compte Azure Cosmos DB. 

   :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png" alt-text="Page du compte Azure Cosmos DB":::