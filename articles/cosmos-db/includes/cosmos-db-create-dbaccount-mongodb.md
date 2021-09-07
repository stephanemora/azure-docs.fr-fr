---
title: Fichier Include
description: Fichier include
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: 3f7a37ce78e49abe1356180818a215488fe82ea3
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515614"
---
1. Dans une nouvelle fenêtre du navigateur, connectez-vous au [portail Azure](https://portal.azure.com/).

2. Dans le menu de gauche, sélectionnez **Créer une ressource**.
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-0.png" alt-text="Capture d’écran de création d’une ressource dans le portail Azure.":::
   
3. Dans la page **Nouveau**, sélectionnez **Bases de données** > **Azure Cosmos DB**.
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png" alt-text="Capture d’écran du volet Bases de données du portail Azure.":::
   
4. Dans la page **Sélectionner une option d’API**, sélectionnez **API Azure Cosmos DB pour MongoDB** > **Créer**.

   L’API détermine le type de compte à créer. Sélectionnez **API Azure Cosmos DB pour MongoDB**, car vous allez créer une collection qui fonctionne avec MongoDB dans ce guide de démarrage rapide. Pour en savoir plus, consultez [Vue d’ensemble de l’API Azure Cosmos DB pour MongoDB](../mongodb-introduction.md).

   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-select-api.png" alt-text="Capture d’écran du volet Sélectionner une option d’API.":::

5. Dans la page **Créer un compte Azure Cosmos DB**, entrez les paramètres du nouveau compte Azure Cosmos DB.

   |Paramètre|Valeur|Description |
   |---|---|---|
   |Abonnement|Nom d’abonnement|Sélectionnez l’abonnement Azure que vous souhaitez utiliser pour ce compte Azure Cosmos DB. |
   |Groupe de ressources|Nom de groupe ressources|Sélectionnez un groupe de ressources ou sélectionnez **Créer**, puis entrez un nom unique pour le nouveau groupe de ressources. |
   |Nom du compte|Entrer un nom unique|Entrez un nom unique pour identifier votre compte Azure Cosmos DB. L’URI de votre compte sera *mongo.cosmos.azure.com* apposé à votre nom de compte unique.<br><br>Le nom peut contenir uniquement des lettres minuscules, des chiffres et des traits d’union (-), et doit comporter entre 3 et 44 caractères.|
   |Emplacement|La région la plus proche de vos utilisateurs|Sélectionnez la zone géographique dans laquelle héberger votre compte Azure Cosmos DB. Utilisez l’emplacement le plus proche de vos utilisateurs pour leur donner l’accès le plus rapide possible aux données.|
   |Mode de capacité|Débit approvisionné ou serverless|Sélectionnez **Débit approvisionné** pour créer un compte dans mode de [débit approvisionné](../set-throughput.md). Sélectionnez **serverless** pour créer un compte en mode [serverless](../serverless.md).<br><br>**Remarque** : Seules les versions 4 et 3.6 de l’API MongoDB sont prises en charge par les comptes serverless. Si vous choisissez la version 3.2, le compte sera forcé en mode de débit provisionné.|
   |Appliquer la remise de niveau gratuit Azure Cosmos DB|**Appliquer** ou **Ne pas appliquer**|Avec le niveau gratuit d’Azure Cosmos DB, vous recevez gratuitement 1 000 RU/s et 25 Go de stockage dans un compte. Découvrez-en plus sur le [niveau gratuit](https://azure.microsoft.com/pricing/details/cosmos-db/).|
   | Version | Choisir la version de serveur requise | L’API Azure Cosmos DB pour MongoDB est compatible avec les versions serveur 4.0, 3.6 et 3.2. Vous pouvez [mettre à niveau ou rétrograder](../mongodb/upgrade-mongodb-version.md) un compte après sa création. |

   > [!NOTE]
   > Vous pouvez avoir un seul compte Azure Cosmos DB de niveau gratuit par abonnement Azure et vous devez vous inscrire lors de la création du compte. Si vous ne voyez pas l’option permettant d’appliquer la remise de niveau gratuit, cela signifie qu’un autre compte dans l’abonnement a déjà été activé avec le niveau gratuit.

   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png" alt-text="Capture d’écran de la page de nouveau compte pour Azure Cosmos DB."::: 

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
   * **Stratégie de sauvegarde** : configurez une stratégie de sauvegarde [périodique](../configure-periodic-backup-restore.md) ou [continue](../provision-account-continuous-backup.md).
   * **Chiffrement** : utilisez une clé gérée par le service ou une [clé gérée par le client](../how-to-setup-cmk.md#create-a-new-azure-cosmos-account).
   * **Étiquettes** : les étiquettes sont des paires nom/valeur qui vous permettent de catégoriser les ressources et d’afficher une facturation centralisée en appliquant la même étiquette à plusieurs ressources et groupes de ressources.

1. Sélectionnez **Revoir + créer**.

4. La création du compte prend quelques minutes. Attendez que le portail affiche le message **Félicitations ! Votre compte d’API Azure Cosmos DB pour MongoDB est prêt**.

   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png" alt-text="Capture d’écran du volet Notifications du portail Azure."::: 
