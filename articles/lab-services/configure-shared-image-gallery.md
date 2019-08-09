---
title: Configurer une galerie d’images partagées dans Azure DevTest Labs | Microsoft Docs
description: Découvrir comment configurer une galerie d’images partagées dans Azure DevTest Labs
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: spelluru
ms.openlocfilehash: de857498aeb51c9b3711c90338d983e85b61cb70
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065429"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Configurer une galerie d’images partagées dans Azure DevTest Labs
DevTest Labs prend à présent en charge la fonctionnalité [Shared Image Gallery](../virtual-machines/windows/shared-image-galleries.md). Elle permet aux utilisateurs de labo d’accéder à des images provenant d’un emplacement partagé lors de la création de ressources de labo. Elle vous permet également de structurer et d’organiser vos images de machines virtuelles managées personnalisées. La fonctionnalité Shared Image Gallery prend en charge les opérations suivantes :

- Réplication globale et managée des images
- Gestion des versions et regroupement d’images pour une gestion simplifiée
- Rendre vos images hautement disponibles avec les comptes ZRS (stockage redondant interzone) dans les régions qui prennent en charge les zones de disponibilité. Le stockage redondant interzone (ZRS) offre une meilleure résilience en cas de défaillances de zones.
- Partage entre différents abonnements, et même entre locataires, à l’aide du contrôle d’accès en fonction du rôle (RBAC).

Pour plus d’informations, consultez la [documentation sur Shared Image Gallery](../virtual-machines/windows/shared-image-galleries.md). 
 
Si vous avez un grand nombre d’images managées à gérer et que vous voulez qu’elles soient disponibles pour toute l’entreprise, vous pouvez utiliser une galerie d’images partagées comme un dépôt qui facilite la mise à jour et le partage de vos images. En tant que propriétaire d’un labo, vous pouvez attacher une galerie d’images partagées existante à votre labo. Une fois la galerie attachée, les utilisateurs du labo peuvent créer des machines à partir de ces images les plus récentes. Un avantage clé de cette fonctionnalité est que DevTest Labs peut maintenant tirer parti du partage d’images dans différents labos, différents abonnements et différentes régions. 

## <a name="considerations"></a>Considérations
- Vous pouvez attacher une seule galerie d’images partagées à un labo à la fois. Si vous voulez attacher une autre galerie, vous devez détacher celle existante, puis en attacher une autre. 
- Actuellement, DevTest abs ne prend pas en charge le chargement d’images dans la galerie par le biais du labo. 
- Lors de la création d’une machine virtuelle à l’aide d’une image de galerie d’images partagées, DevTest Labs utilise toujours la dernière version publiée de cette image.
- Bien que DevTest Labs s’efforce automatiquement de faire en sorte que la galerie d’images partagées réplique des images dans la région dans laquelle se trouve le labo, ce n’est pas toujours possible. Pour éviter que les utilisateurs rencontrent des problèmes lors de la création de machines virtuelles à partir de ces images, vérifiez que les images sont déjà répliquées dans la région du labo.

## <a name="use-azure-portal"></a>Utiliser le portail Azure
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Dans le menu de navigation de gauche, sélectionnez **Tous les services**.
1. Sélectionnez **DevTest Labs** dans la liste.
1. Dans la liste de labos, sélectionnez votre **labo**.
1. Sélectionnez **Configuration et stratégies** dans la section **Paramètres** du menu de gauche.
1. Sélectionnez **Galeries d’images partagées** sous **Bases de machine virtuelle** dans le menu de gauche.

    ![Menu Galeries d’images partagées](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Attachez une galerie d’images partagées existante à votre labo en cliquant sur le bouton **Attacher**, puis en sélectionnant votre galerie dans la liste déroulante.

    ![Joindre](./media/configure-shared-image-gallery/attach-options.png)
1. Accédez à la galerie attachée et configurez votre galerie pour **activer ou désactiver** les images partagées pour la création de machines virtuelles.

    ![Activer ou désactiver](./media/configure-shared-image-gallery/enable-disable.png)
1. Les utilisateurs du labo peuvent alors créer une machine virtuelle avec les images activées en cliquant sur **+Ajouter**, puis en recherchant l’image dans la page de **choix de votre base**.

    ![Utilisateurs de labo](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Utilisation d’un modèle Azure Resource Manager

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Attacher une galerie d’images partagées à votre labo
Si vous utilisez un modèle Azure Resource Manager pour attacher une galerie d’images partagées à votre labo, vous devez l’ajouter sous la section de ressources de votre modèle Resource Manager, comme indiqué dans l’exemple suivant :

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "[parameters('newLabName')]",
    "location": "[resourceGroup (). location]",
    "resources": [
    {
        "apiVersion": "2018-10-15-preview",
        "name": "[variables('labVirtualNetworkName')]",
        "type": "virtualNetworks",
        "dependsOn": [
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    },
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"[parameters('existingSharedGalleryId')]",
            "allowAllImages": "Enabled"
        },
        "dependsOn":[
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    }
    ]
} 

```

Pour obtenir un exemple de modèle Resource Manager complet, consultez ces exemples de modèles Resource Manager dans notre dépôt GitHub public : [Configurer une galerie d’images partagées lors de la création d’un labo](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

### <a name="create-a-vm-using-an-image-from-the-shared-image-gallery"></a>Créer une machine virtuelle à l’aide d’une image de la galerie d’images partagées
Si vous utilisez un modèle Azure Resource Manager pour créer une machine virtuelle à l’aide d’une image de galerie d’images partagées, utilisez l’exemple suivant :

```json

"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs/virtualMachines",
    "name": "[variables('resourceName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "sharedImageId": "[parameters('existingSharedImageId')]",
        "size": "[parameters('newVMSize')]",
        "isAuthenticationWithSshKey": false,
        "userName": "[parameters('userName')]",
        "sshKey": "",
        "password": "[parameters('password')]",
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "labSubnetName": "[variables('labSubnetName')]"
    }
}
],

```

Pour en savoir plus, consultez ces exemples de modèles Resource Manager sur notre dépôt GitHub public.
[Créer une machine virtuelle à l’aide d’une image de galerie d’images partagées](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage).

## <a name="use-api"></a>Utiliser une API

- Utilisez l’API version 2018_10_15_preview.
- Pour attacher votre galerie, envoyez la requête comme indiqué dans l’extrait de code suivant :
    
    ``` 
    PUT [Lab Resource Id]/SharedGalleries/[newGalleryName]
    Body: 
    {
        “properties”:{
            “galleryId”: “[Shared Image Gallery resource Id]”,
            “allowAllImages”:”Enabled”
        }
    }
    ```
- Pour voir toutes les images de votre galerie d’images partagées, vous pouvez lister toutes les images partagées avec leur ID de ressource.

    ```
    GET [Lab Resource Id]/SharedGalleries/mySharedGallery/SharedImages
    ````
- Pour créer une machine virtuelle à l’aide d’images partagées, vous pouvez effectuer une opération PUT sur les machines virtuelles et dans les propriétés de ces dernières, en passant l’ID des images partagées que vous avez obtenu de l’appel précédent à properties.SharedImageId.


## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants relatifs aux artefacts :

- [Spécifier des artefacts obligatoires pour votre labo](devtest-lab-mandatory-artifacts.md)
- [Créer des artefacts personnalisés](devtest-lab-artifact-author.md)
- [Ajouter un dépôt d’artefacts à un labo](devtest-lab-artifact-author.md)
- [Diagnostiquer les échecs d’artefact](devtest-lab-troubleshoot-artifact-failure.md)
