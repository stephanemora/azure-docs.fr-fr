---
title: Sécuriser un service lié
description: Découvrez comment provisionner et sécuriser un service lié avec un réseau virtuel managé
services: synapse-analytics
author: acomet
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 1ce127dbfd9984b3fb18e518701cbbd3a87f5988
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387237"
---
# <a name="secure-a-linked-service-with-private-links"></a>Sécuriser un service lié à l’aide de Liaisons privées (Private Links) 

Dans cet article, vous allez apprendre à sécuriser un service lié dans Synapse à l’aide d’un point de terminaison privé.

## <a name="prerequisites"></a>Prérequis

* **Abonnement Azure** : Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Compte Stockage Azure** : Vous utilisez Azure Data Lake Gen 2 comme magasin de données *source*. Si vous ne possédez pas de compte de stockage, consultez l’article [Créer un compte Stockage Azure](../../storage/blobs/data-lake-storage-quickstart-create-account.md) pour découvrir comment en créer un. Assurez-vous que le compte de stockage dispose du filtre IP Synapse Studio pour y accéder et que vous n’autorisez que les **réseaux sélectionnés** à accéder au compte de stockage. Le paramètre sous le panneau **Pare-feux et réseaux virtuels** doit ressembler à l’image ci-dessous.

![Compte de stockage sécurisé](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Créer un service lié à l’aide de Liaisons privées (Private Links)

Dans Azure Synapse Analytics, un service lié vous permet de définir vos informations de connexion à d’autres services. Dans cette section, vous allez ajouter Azure Synapse Analytics et Azure Data Lake Gen 2 en tant que services liés.

1. Ouvrez Azure Synapse Studio et accédez à l’onglet **Gérer**.
1. Sous **Connexions externes**, sélectionnez **Services liés**.
1. Pour ajouter un service lié, cliquez sur **Nouveau**.
1. Sélectionnez la vignette Azure Data Lake Storage Gen2 dans la liste, puis cliquez sur **Continuer**.
1. Veillez à activer **Création interactive**. L’activation peut prendre environ 1 minute. 
1. Entrez vos informations d’authentification. Les types d’authentification actuellement pris en charge sont : clé de compte, principal de service et identité managée. Cliquez sur Tester la connexion pour vérifier que vos informations sont correctes.
1. Sélectionnez **Tester la connexion**. Cela doit échouer, car le compte de stockage n’y autorise pas l’accès sans la création et l’approbation d’un point de terminaison privé. Dans le message d’erreur, vous devez voir un lien pour créer un **point de terminaison privé** que vous pouvez suivre pour passer à la partie suivante. Si vous suivez ce lien, ignorez la partie suivante.
1. Lorsque vous avez terminé, sélectionnez **Créer**.

## <a name="create-a-managed-private-endpoint"></a>Créer un point de terminaison privé managé

Si vous n’avez pas cliqué sur le lien hypertexte lors du test de la connexion ci-dessus, suivez le chemin suivant. Vous devez maintenant créer un point de terminaison privé managé que vous connecterez au service lié créé ci-dessus.

1. Accédez à l’onglet **Gérer**.
1. Accédez à la section **Réseaux virtuels managés**.
1. Sélectionnez **+ Nouveau** sous Point de terminaison privé managé.
1. Sélectionnez la vignette Azure Data Lake Storage Gen2 dans la liste, puis sélectionnez **Continuer**.
1. Entrez le nom du compte Stockage Azure créé ci-dessus.
1. Sélectionnez **Créer**
1. Après avoir attendu quelques secondes, vous devriez voir que la liaison privée créée nécessite une approbation.

## <a name="private-link-approval"></a>Approbation de liaison privée
1. Sélectionnez le point de terminaison privé que vous avez créé précédemment. Vous pouvez voir un lien hypertexte qui vous permet d’approuver le point de terminaison privé au niveau du compte de stockage. *Une alternative consiste à accéder directement au compte de stockage sur le portail Azure et à accéder au panneau **Connexions de point de terminaison privé**.*
1. Cochez le point de terminaison privé que vous avez créé dans Studio, puis sélectionnez **Approuver**.
1. Ajoutez une description et cliquez sur **Oui**.
1. Revenez à Synapse Studio dans la section **Réseaux virtuels managés** de l’onglet **Gérer**.
1. L’approbation de votre point de terminaison privé doit prendre environ 1 minute.

## <a name="check-the-connection-works"></a>Vérifier que la connexion fonctionne
1. Accédez à l’onglet **Gérer** et sélectionnez le service lié que vous avez créé.
1. Assurez-vous que l’option **Création interactive** est active.
1. Sélectionnez **Tester la connexion**. La connexion doit s’afficher comme réussie.

Vous avez maintenant établi une connexion sécurisée et privée entre Synapse et votre service lié.

## <a name="next-steps"></a>Étapes suivantes

Pour approfondir votre compréhension du point de terminaison privé managé dans Synapse Analytics, consultez l’article [Concept autour du point de terminaison privé managé Synapse](data-integration-data-lake.md).

Pour plus d’informations sur l’intégration de données pour Synapse Analytics, consultez l’article [Ingestion de données dans un Data Lake](data-integration-data-lake.md).
