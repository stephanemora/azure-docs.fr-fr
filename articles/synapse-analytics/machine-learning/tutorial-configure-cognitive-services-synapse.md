---
title: 'Tutoriel : Prérequis de Cognitive Services dans Azure Synapse Analytics'
description: Découvrez comment configurer les prérequis de l’utilisation de Cognitive Services dans Azure Synapse.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 3ab861caca0ef6f58c2c1bc722412774deb725ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98936685"
---
# <a name="tutorial-prerequisites-for-using-cognitive-services-in-azure-synapse-analytics"></a>Tutoriel : Prérequis de l’utilisation de Cognitive Services dans Azure Synapse Analytics

Dans ce tutoriel, vous allez apprendre à configurer les prérequis de l’utilisation sécurisée d’Azure Cognitive Services dans Azure Synapse Analytics.

Ce tutoriel contient les sections suivantes :
> [!div class="checklist"]
> - Créer une ressource Cognitive Services comme Analyse de texte ou Détecteur d’anomalies.
> - Stocker une clé d’authentification auprès des ressources Cognitive Services sous la forme de secrets dans Azure Key Vault et configurer l’accès pour un espace de travail Azure Synapse Analytics.
> - Créer un service lié Azure Key Vault dans votre espace de travail Azure Synapse Analytics.

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit avant de commencer](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prérequis

- [Espace de travail Azure Synapse Analytics](../get-started-create-workspace.md) avec un compte de stockage Azure Data Lake Storage Gen2 configuré comme stockage par défaut. Vous devez être le *contributeur aux données Blob de stockage* du système de fichiers Azure Data Lake Storage Gen2 que vous utilisez.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-a-cognitive-services-resource"></a>Créer une ressource Cognitive Services

[Azure Cognitive Services](../../cognitive-services/index.yml) comprend de nombreux types de services. Analyse de texte et Détecteur d’anomalies sont deux exemples dans les tutoriels Azure Synapse.

Vous pouvez créer une ressource [Analyse de texte](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) dans le portail Azure :

![Capture d’écran qui montre Analyse de texte dans le portail, avec le bouton Créer](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

Vous pouvez créer une ressource [Détecteur d’anomalies](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) dans le portail Azure :

![Capture d’écran qui montre Détecteur d’anomalies dans le portail, avec le bouton Créer](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

## <a name="create-a-key-vault-and-configure-secrets-and-access"></a>Créer un coffre de clés et configurer des secrets et l’accès

1. Créez un [coffre de clés](https://ms.portal.azure.com/#create/Microsoft.KeyVault) dans le portail Azure.
2. Accédez à **Coffre de clés** > **Stratégies d’accès**, puis accordez à l’[Identité de service managée (MSI) de l’espace de travail Azure Synapse](../security/synapse-workspace-managed-identity.md) des autorisations pour lire des secrets à partir d’Azure Key Vault.

   > [!NOTE]
   > Vérifiez que les changements de stratégie sont enregistrés. Il est facile d’oublier cette étape.

   ![Capture d’écran montrant les sélections pour ajouter une stratégie d’accès](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Accédez à votre ressource Cognitive Services. Par exemple, accédez à **Détecteur d’anomalies** > **Clés et point de terminaison**. Copiez ensuite l’une des deux clés dans le Presse-papiers.

4. Accédez à **Coffre de clés** > **Secret** pour créer un secret. Spécifiez le nom du secret, puis collez la clé de l’étape précédente dans le champ **Valeur**. Pour finir, sélectionnez **Créer**.

   ![Capture d’écran montrant les sélections pour la création d’un secret](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

   > [!IMPORTANT]
   > Veillez à mémoriser ou à noter le nom de ce secret. Vous l’utiliserez ultérieurement quand vous vous connecterez à Cognitive Services à partir d’Azure Synapse Studio.

## <a name="create-an-azure-key-vault-linked-service-in-azure-synapse"></a>Créer un service lié Azure Key Vault dans Azure Synapse

1. Ouvrez votre espace de travail dans Azure Synapse Studio. 
2. Accédez à **Gérer** > **Services liés**. Créez un service lié **Azure Key Vault** en pointant vers le coffre de clés que vous venez de créer. 
3. Vérifiez la connexion en sélectionnant le bouton **Tester la connexion**. Si la connexion est verte, sélectionnez **Créer**, puis **Publier tout** pour enregistrer vos modifications.

![Capture d’écran qui montre Azure Key Vault en tant que nouveau service lié](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

Vous êtes maintenant prêt à passer à l’un des tutoriels concernant l’utilisation de l’expérience Azure Cognitive Services dans Azure Synapse Studio.

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Analyse des sentiments avec Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Tutoriel : Détection d’anomalie avec Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Tutoriel : Scoring de modèle Machine Learning dans des pools SQL dédiés Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md).
- [Fonctionnalités de Machine Learning dans Azure Synapse Analytics](what-is-machine-learning.md)