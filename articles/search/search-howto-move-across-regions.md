---
title: Déplacer votre ressource de service d'une région vers une autre
titleSuffix: Azure Cognitive Search
description: Cet article explique comment déplacer vos ressources Recherche cognitive Azure d'une région vers une autre dans le cloud Azure.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/18/2020
ms.openlocfilehash: 392c86d8ea24e59d388926d4df581305ea2b531d
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599634"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Déplacer votre service Recherche cognitive Azure vers une autre région Azure

Pour déplacer votre compte de service Recherche cognitive Azure d'une région vers une autre, vous allez créer un modèle d'exportation afin de déplacer votre abonnement (ou vos abonnements). Après avoir déplacé votre abonnement, vous devrez déplacer vos données et recréer votre service.

Dans cet article, vous allez apprendre à :

> [!div class="checklist"]
> * Exporter un modèle.
> * Modifier le modèle en ajoutant la région cible, et les noms des comptes de recherche et de stockage
> * Déployer le modèle pour créer les nouveaux comptes de recherche et de stockage
> * Vérifier l'état de votre service dans la nouvelle région
> * Nettoyer les ressources dans la région source

## <a name="prerequisites"></a>Prérequis

- Vérifiez que les services et fonctionnalités utilisés par votre compte sont pris en charge dans la région cible.

- Pour les fonctionnalités en préversion, vérifiez que votre abonnement figure dans la liste verte de la région cible. Pour plus d'informations sur les fonctionnalités d'évaluation, consultez [Bases de connaissances](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [Enrichissement incrémentiel](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual) et [Point de terminaison privé](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## <a name="assessment-and-planning"></a>Évaluation et planification

Lorsque vous déplacez votre service de recherche vers la nouvelle région, vous devez [déplacer vos données vers le nouveau service de stockage](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account), puis reconstruire vos index, vos ensembles de compétences et vos bases de connaissances. Vous devez enregistrer les paramètres actuels et copier les fichiers .json pour faciliter et accélérer la reconstruction de votre service.

## <a name="moving-your-search-services-resources"></a>Déplacer les ressources de votre service de recherche

Pour commencer, vous devez exporter un modèle Resource Manager et le modifier.

### <a name="export-a-template"></a>Exporter un modèle

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Accédez à votre page Groupe de ressources.

> [!div class="mx-imgBorder"]
> ![Exemple de page Groupe de ressources](./media/search-move-resource/export-template-sample.png)

3. Sélectionnez **Toutes les ressources**.

3. Dans le menu de navigation de gauche, sélectionnez **Exporter le modèle**.

4. Choisissez **Télécharger** dans le panneau **Exporter le modèle**.

5. Localisez le fichier .zip que vous avez téléchargé à partir du portail, puis décompressez-le dans le dossier de votre choix.

Le fichier zip contient les fichiers .json qui comprennent le modèle et les scripts permettant de le déployer.

### <a name="modify-the-template"></a>Modifier le modèle

Pour modifier le modèle, changez le nom et la région des comptes de recherche et de stockage. Les noms doivent respecter les règles de chaque service et les conventions de dénomination des régions. 

Pour obtenir les codes d’emplacement des régions, consultez [Emplacements Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Le code d’une région est le nom de la région sans espace, **USA Centre** = **centralus**.

1. Dans le portail Azure, sélectionnez **Créer une ressource**.

2. Dans **Rechercher sur la Place de marché**, tapez **déploiement de modèle**, puis appuyez sur **Entrée**.

3. Sélectionnez **Déploiement de modèle**.

4. Sélectionnez **Create** (Créer).

5. Sélectionnez **Générer votre propre modèle dans l’éditeur**.

6. Sélectionnez **Charger le fichier**, puis suivez les instructions pour charger le fichier **template.json** que vous avez téléchargé et décompressé à la section précédente.

7. Dans le fichier **template.json**, définissez la valeur par défaut des noms des comptes de recherche et de stockage pour nommer les comptes. 

8. Remplacez la propriété **location** du fichier **template.json** par la région cible de vos services de recherche et de stockage. Cet exemple définit `centralus` comme région cible.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2015-08-19",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### <a name="deploy-the-template"></a>Déployer le modèle

1. Enregistrez le fichier **template.json**.

2. Entrez ou sélectionnez les valeurs de propriété :

- **Abonnement**: Sélectionnez un abonnement Azure.

- **Groupe de ressources** : Sélectionnez **Créer** et donnez un nom au groupe de ressources.

- **Emplacement** : Sélectionnez un emplacement Azure.

3. Cochez la case **J’accepte les termes et conditions mentionnés ci-dessus**, puis cliquez sur le bouton **Sélectionner un achat**.

## <a name="verifying-your-services-status-in-new-region"></a>Vérifier l'état de vos services dans la nouvelle région

Pour vérifier le déplacement, ouvrez le nouveau groupe de ressources. Vos services seront répertoriés avec la nouvelle région.

Pour déplacer vos données de votre région source vers la région cible, consultez les instructions de [déplacement de vos données vers le nouveau compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account) fournies dans cet article.

## <a name="clean-up-resources-in-your-original-region"></a>Nettoyer les ressources dans votre région d'origine

Pour valider les changements et le déplacement de votre compte de service, supprimez le compte de service source.

## <a name="next-steps"></a>Étapes suivantes

[Création d'un index](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Créer un ensemble de compétences](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Créer une base de connaissances](https://docs.microsoft.com/azure/search/knowledge-store-create-portal)

