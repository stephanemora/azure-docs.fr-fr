---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2e8fa35bb99a11e51d634219e54296c5be831b3a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097688"
---
## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)

## <a name="create-a-new-azure-resource-and-azure-blob-storage-account"></a>Créer une ressource Azure et un compte Stockage Blob Azure

Avant de pouvoir utiliser un modèle de NER personnalisée, vous devez créer une ressource Azure Language, qui vous permettra de disposer des informations d’identification nécessaires pour créer un projet et commencer à entraîner un modèle. Vous aurez également besoin d’un compte Stockage Azure, où vous pourrez charger le jeu de données à utiliser pour générer votre modèle.

> [!IMPORTANT]
> Pour bien démarrer rapidement, nous vous recommandons de créer une ressource Azure Language en suivant les étapes ci-dessous. Ainsi, vous pourrez créer la ressource et configurer en même temps un compte de stockage, ce qui est plus facile à faire maintenant que plus tard.
>
> Si vous disposez d’une ressource préexistante à utiliser, vous devez la configurer séparément du compte de stockage. Pour plus d’informations, consultez les détails relatifs à la [création d’un projet](../../how-to/create-project.md#using-a-pre-existing-azure-resource).

1. Accédez au [portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) pour créer une ressource Azure Language. Si vous êtes invité à sélectionner des fonctionnalités supplémentaires, sélectionnez **Classification de texte personnalisée et NER personnalisée**. Quand vous créez la ressource, vérifiez qu’elle comporte les paramètres suivants.

    |Paramètres nécessaires à la ressource Azure  |Valeur requise  |
    |---------|---------|
    |Location | « USA Ouest 2 » ou « Europe Ouest »         |
    |Niveau tarifaire     | Niveau tarifaire standard (**S**)        |

2. Dans la section **Reconnaissance d’entité nommée (NER) personnalisée et Classification personnalisée (préversion)** , sélectionnez un compte de stockage existant, ou sélectionnez **Créer un compte de stockage**. Notez que ces valeurs concernent ce guide de démarrage rapide, et pas nécessairement les [valeurs du compte de stockage](/azure/storage/common/storage-account-overview) à utiliser dans les environnements de production.

    |Valeur du compte de stockage  |Valeur recommandée  |
    |---------|---------|
    | Nom | Nom quelconque |
    | Performances | Standard |
    | Type de compte| Stockage (v1 universel) |
    | Réplication | Stockage localement redondant (LRS)
    |Location | N’importe quel lieu le plus proche de vous, pour une meilleure latence.        |

## <a name="upload-sample-data-to-blob-container"></a>Charger les exemples de données dans un conteneur d’objets blob

Une fois que vous avez créé un compte Stockage Azure et que vous l’avez lié à votre ressource Language, vous devez charger les exemples de fichiers de ce guide de démarrage rapide. Ces fichiers seront utilisés plus tard pour entraîner votre modèle.

1. [Téléchargez les exemples de données](https://go.microsoft.com/fwlink/?linkid=2175226) pour ce guide de démarrage rapide à partir de GitHub.

2. Accédez à votre compte Stockage Azure dans le [portail Azure](https://ms.portal.azure.com). Accédez à votre compte, puis chargez les exemples de données vers celui-ci.

L’exemple de jeu de données fourni contient 20 contrats de prêt. Chaque contrat comprend deux parties : un prêteur et un emprunteur. Vous pouvez utiliser l’exemple de fichier fourni pour extraire les informations pertinentes relatives aux deux parties, la date du contrat, le montant du prêt et le taux d’intérêt.

## <a name="create-a-custom-named-entity-recognition-project"></a>Créer un projet de reconnaissance d’entité nommée personnalisée

Une fois votre ressource et votre conteneur de stockage configurés, créez un projet basé sur une NER conversationnelle. Un projet est une zone de travail qui vous permet de créer des modèles IA personnalisés en fonction de vos données. Votre projet est uniquement accessible à vous-même et aux autres personnes disposant d’un accès de contributeur à la ressource Azure utilisée.

1. Connectez-vous au [portail Language Studio](https://aka.ms/languageStudio). Une fenêtre apparaît pour vous permettre de sélectionner votre abonnement et votre ressource Language. Sélectionnez la ressource que vous avez créée à l’étape ci-dessus.

2. Recherchez la section **Extraction d’entité**, puis sélectionnez **Reconnaissance d’entité nommée personnalisée** parmi les services disponibles.

3. Sélectionnez **Créer un projet** dans le menu supérieur de la page des projets. La création d’un projet vous permet d’étiqueter les données, d’entraîner, d’évaluer, d’améliorer et de déployer vos modèles. 

    
    :::image type="content" source="../../media/create-project.png" alt-text="Capture d’écran de la page de création de projet." lightbox="../../media/create-project.png":::


4.  Une fois que vous avez cliqué sur **Créer un projet**, un écran apparaît pour vous permettre de connecter votre compte de stockage. Si vous ne trouvez pas votre compte de stockage, vérifiez que vous avez créé une ressource en suivant les étapes ci-dessus. 

    >[!NOTE]
    > * Vous ne devez effectuer cette étape qu’une seule fois pour chaque nouvelle ressource utilisée. 
    > * Ce processus est irréversible. Si vous connectez un compte de stockage à votre ressource, vous ne pourrez pas le déconnecter par la suite.
    > * Vous ne pouvez connecter votre ressource qu’à un seul compte de stockage.
    > * Si vous avez déjà connecté un compte de stockage, vous voyez s’afficher à la place l’écran **Enter basic information** (Entrer les informations de base). Consultez l’étape suivante.
    
    :::image type="content" source="../../media/connect-storage.png" alt-text="Capture d’écran montrant l’écran de connexion du stockage." lightbox="../../media/connect-storage.png":::

<!--If you're using a preexisting resource, see [creating Azure resources](../concepts/use-azure-resources.md). When you are done, select **Next**.--> 

5. Entrez les informations relatives au projet, notamment son nom, sa description et la langue des fichiers qu’il contient. Vous ne pourrez pas changer le nom de votre projet par la suite. 

6. Sélectionnez le conteneur dans lequel vous avez chargé vos données. Pour ce guide de démarrage rapide, nous allons utiliser le fichier d’étiquettes existant disponible dans le conteneur. Cliquez ensuite sur **Suivant**.

7. Passez en revue les données entrées, puis sélectionnez **Créer un projet**.

## <a name="train-your-model"></a>Entraîner votre modèle

En règle générale, après avoir créé un projet, vous importez les données et vous commencez à [étiqueter les entités](../../how-to/tag-data.md) contenues pour entraîner le modèle de classification. Pour ce guide de démarrage rapide, vous allez utiliser l’exemple de fichier de données étiqueté que vous avez téléchargé et stocké dans votre compte Stockage Azure.

Un modèle est un objet de machine learning, qui est entraîné pour classifier du texte. Votre modèle apprend à partir des exemples de données. Ainsi, il pourra ensuite classifier les tickets de support technique.

Pour commencer à entraîner votre modèle :

1. Dans le menu de gauche, sélectionnez **Entraîner**.

2. Sélectionnez **Entraîner un nouveau modèle**, puis tapez le nom du modèle dans la zone de texte ci-dessous.

    :::image type="content" source="../../media/train-model.png" alt-text="Capture d’écran montrant la page de sélection du modèle à entraîner" lightbox="../../media/train-model.png":::

3. Cliquez au bas de la page sur le bouton **Entraîner**.

    > [!NOTE]
    > * Durant l’entraînement, les données sont découpées en 2 ensembles : 80 % pour l’entraînement et 20 % pour les tests. Pour en savoir plus sur le découpage des données, cliquez [ici](../../how-to/train-model.md#data-split)
    > * L’entraînement peut prendre quelques heures.

## <a name="deploy-your-model"></a>Déployer votre modèle

En règle générale, après l’entraînement d’un modèle, vous passez en revue les [détails de l’évaluation](../../how-to/view-model-evaluation.md) et vous [apportez des améliorations](../../how-to/improve-model.md) si nécessaire. Dans ce guide de démarrage rapide, vous allez simplement déployer votre modèle et le rendre disponible à des fins de test.

Une fois le modèle entraîné, vous pouvez le déployer. Le déploiement du modèle vous permet de commencer à l’utiliser pour extraire des entités nommées à l’aide de l’[API Analyser](https://aka.ms/ct-runtime-swagger).

1. Dans le menu de gauche, sélectionnez **Déployer le modèle**.

2. Sélectionnez le modèle à déployer, puis sélectionnez **Déployer le modèle**.

## <a name="test-your-model"></a>Tester votre modèle

Une fois votre modèle déployé, vous pouvez commencer à l’utiliser pour l’extraction d’entité. Suivez les étapes ci-dessous pour envoyer votre première requête d’extraction d’entité.

1. Dans le menu de gauche, sélectionnez **Tester le modèle**.

2. Sélectionnez le modèle à tester.

3. Ajoutez votre texte à la zone de texte, vous pouvez également charger un fichier `.txt`. 

4. Cliquez sur **Exécuter le test**.

5. Sous l’onglet **Résultat**, vous pouvez voir les entités extraites à partir de votre texte, et leurs types. Vous pouvez également voir la réponse JSON sous l’onglet **JSON**.

    :::image type="content" source="../../media/test-model-results.png" alt-text="Voir les résultats des tests" lightbox="../../media/test-model-results.png":::


## <a name="clean-up-resources"></a>Nettoyer les ressources

Une fois que vous n’avez plus besoin de votre projet, vous pouvez le supprimer à l’aide de [Language Studio](https://aka.ms/custom-extraction). Sélectionnez **Reconnaissance d’entité nommée (NER) personnalisée** dans le menu de navigation de gauche, sélectionnez le projet à supprimer, puis cliquez sur **Supprimer**.
