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
ms.openlocfilehash: 2f055d8fe6608d328421e1916c5855d300c49dcc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725483"
---
1. Dans une nouvelle fenêtre du navigateur, connectez-vous au [portail Azure](https://portal.azure.com/).

2. Dans le menu de gauche, sélectionnez **Créer une ressource**.
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-0.png" alt-text="Créer une ressource dans le Portail Azure":::   

3. Dans la page **Nouveau**, sélectionnez **Bases de données** > **Azure Cosmos DB**.

   :::image type="content" source="./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png" alt-text="Volet Bases de données du Portail Azure":::      

3. Dans la page **Créer un compte Azure Cosmos DB**, entrez les paramètres du nouveau compte Azure Cosmos DB. 
 
   |Paramètre|Valeur|Description |
   |---|---|---|
   |Abonnement|Nom d’abonnement|Sélectionnez l’abonnement Azure à utiliser pour ce compte Azure Cosmos. |
   |Groupe de ressources|Nom de groupe ressources|Sélectionnez un groupe de ressources ou sélectionnez **Créer**, puis entrez un nom unique pour le nouveau groupe de ressources. |
   |Nom du compte|Entrer un nom unique|Entrez un nom unique pour identifier votre compte Azure Cosmos DB. L’URI de votre compte sera *gremlin.azure.com* apposé à votre nom de compte unique.<br><br>Le nom peut contenir uniquement des lettres minuscules, des chiffres et des traits d’union (-), et doit comporter entre 3 et 44 caractères.|
   API|Gremlin (graphe)|L’API détermine le type de compte à créer. Azure Cosmos DB fournit cinq API : Core (SQL) pour les bases de données de documents, Gremlin pour les bases de données de graphes, MongoDB pour les bases de données de documents, Table Azure et Cassandra. Vous devez créer un compte distinct pour chaque API. <br><br>Sélectionnez **Gremlin (graphe)** car, dans ce guide de démarrage rapide, vous créez une table qui fonctionne avec l’API Gremlin. <br><br>[Découvrez plus d’informations sur l’API Gremlin](../graph-introduction.md).|
   |Emplacement|La région la plus proche de vos utilisateurs|Sélectionnez la zone géographique dans laquelle héberger votre compte Azure Cosmos DB. Utilisez l’emplacement le plus proche de vos utilisateurs pour leur donner l’accès le plus rapide possible aux données.|
   |Mode de capacité|Débit approvisionné ou serverless|Sélectionnez **Débit approvisionné** pour créer un compte dans mode de [débit approvisionné](../set-throughput.md). Sélectionnez **serverless** pour créer un compte en mode [serverless](../serverless.md).|
   |Appliquer la remise de niveau gratuit Azure Cosmos DB|**Appliquer** ou **Ne pas appliquer**|Avec le niveau gratuit d’Azure Cosmos DB, vous recevez gratuitement 1 000 RU/s et 25 Go de stockage dans un compte. Découvrez-en plus sur le [niveau gratuit](https://azure.microsoft.com/pricing/details/cosmos-db/).|

   > [!NOTE]
   > Vous pouvez avoir un seul compte Azure Cosmos DB de niveau gratuit par abonnement Azure et vous devez vous inscrire lors de la création du compte. Si vous ne voyez pas l’option permettant d’appliquer la remise de niveau gratuit, cela signifie qu’un autre compte dans l’abonnement a déjà été activé avec le niveau gratuit.

   :::image type="content" source="./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-create-new-account.png" alt-text="Page de nouveau compte pour Azure Cosmos DB":::   

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

1. La création du compte prend quelques minutes. Attendez que le portail affiche la page **Félicitations ! Votre compte Azure Cosmos DB a été créé**.

   :::image type="content" source="./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-graph-created.png" alt-text="Page Compte Azure Cosmos DB créé":::   