---
title: Configurer une galerie d’images partagé dans Azure DevTest Labs | Microsoft Docs
description: Découvrez comment configurer une galerie d’images partagé dans Azure DevTest Labs
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
ms.openlocfilehash: fba969b70ae052c928f33888d3c93eb7683ae9f7
ms.sourcegitcommit: ec7b0bf593645c0d1ef401a3350f162e02c7e9b8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2019
ms.locfileid: "66455719"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Configurer une galerie d’images partagées dans Azure DevTest Labs
DevTest Labs prend désormais en charge la [Galerie d’images partagé](/virtual-machines/windows/shared-image-galleries.md) fonctionnalité. Celle-ci permet aux utilisateurs de lab d’accéder à des provenant d’un emplacement partagé lors de la création de ressources de lab. Elle vous permet également de structurer et d’organiser vos images de machine virtuelle managées personnalisées. La fonctionnalité de la galerie d’images partagé prend en charge :

- Réplication mondiale et managée des images
- Création de versions et de groupes d’images pour faciliter la gestion
- Créez des images hautement disponibles avec les comptes ZRS (Zone Redundant Storage) dans les régions qui prennent en charge les zones de disponibilité. Le stockage redondant interzone (ZRS) offre une meilleure résilience en cas de défaillances de zones.
- Partage entre différents abonnements et même entre locataires à l’aide du contrôle d’accès en fonction du rôle (RBAC).

Pour plus d’informations, consultez [documentation de la galerie d’images partagé](../virtual-machines/windows/shared-image-galleries.md). 
 
Si vous avez un grand nombre d’images managées à gérer et que vous voulez qu’elles soient disponibles pour toute l’entreprise, vous pouvez utiliser une galerie d’images partagées comme un dépôt qui facilite la mise à jour et le partage de vos images. En tant que propriétaire d’un lab, vous pouvez attacher une galerie d’images partagées existante à votre lab. Une fois que la galerie est attachée, les utilisateurs du lab peuvent créer des machines à partir de ces images les plus récentes. Un avantage clé de cette fonctionnalité est que DevTest Labs peut maintenant tirer parti du partage d’images dans l’ensemble des labs, abonnements et régions. 

## <a name="considerations"></a>Considérations
- Vous pouvez uniquement attacher une galerie d’images partagé à un laboratoire à la fois. Si vous souhaitez attacher une autre bibliothèque, vous devez détacher un existant et ajouter une autre. 
- Actuellement, DevTest Labs ne prend pas en charge durant le chargement d’images dans la galerie par le biais du laboratoire. 
- Lorsque vous créez une machine virtuelle à l’aide d’une image de galerie d’image partagée, DevTest Labs utilise toujours la dernière version publiée de cette image.
- Bien que dev/test effectue automatiquement une tentative de meilleures pour garantir la galerie d’images partagé réplique des images à la région dans lequel se trouve le laboratoire, il n’est pas toujours possible. Pour éviter les utilisateurs ayant des problèmes de création de machines virtuelles à partir de ces images, vérifiez que les images sont déjà répliquées vers la région du laboratoire. »

## <a name="use-azure-portal"></a>Utiliser le portail Azure
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Sélectionnez **tous les Services** dans le menu de navigation gauche.
1. Sélectionnez **dev/test** dans la liste.
1. Dans la liste des laboratoires, sélectionnez votre **lab**.
1. Sélectionnez **Configuration et stratégies** dans le **paramètres** section dans le menu de gauche.
1. Sélectionnez **galeries d’images partagé** sous **bases de machine virtuelle** sur le menu de gauche.

    ![Menu des galeries d’images partagé](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Attacher une galerie d’images partagé existant à votre laboratoire en cliquant sur le **attacher** bouton et en sélectionnant votre galerie dans la liste déroulante.

    ![Joindre](./media/configure-shared-image-gallery/attach-options.png)
1. Accédez à la galerie attachée et configurez votre galerie sur **activer ou désactiver** d’images pour la création de machines virtuelles partagées.

    ![Activer ou désactiver](./media/configure-shared-image-gallery/enable-disable.png)
1. Les utilisateurs de laboratoire peuvent ensuite créer une machine virtuelle avec les images est activées en cliquant sur **+ ajouter** et la recherche de l’image dans le **choisir votre base** page.

    ![Utilisateurs de labo](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Utilisation d’un modèle Azure Resource Manager

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Attacher une galerie d’images partagé à votre laboratoire
Si vous utilisez un modèle Azure Resource Manager pour attacher une galerie d’images partagé à votre laboratoire, vous devez l’ajouter sous la section de ressources de votre modèle Resource Manager, comme indiqué dans l’exemple suivant :

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

Pour un exemple de modèle Resource Manager complet, consultez ces exemples de modèles Resource Manager dans notre référentiel GitHub public : [Configurer une galerie d’images partagé lors de la création d’un laboratoire](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

### <a name="create-a-vm-using-an-image-from-the-shared-image-gallery"></a>Créer une machine virtuelle à l’aide d’une image à partir de la galerie d’images partagé
Si vous utilisez un modèle Azure Resource Manager pour créer une machine virtuelle à l’aide d’une image de galerie d’image partagée, utilisez l’exemple suivant :

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

Pour plus d’informations, consultez ces exemples de modèles Resource Manager sur notre GitHub public.
[Créer une machine virtuelle à l’aide d’une image de galerie d’image partagée](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage).

## <a name="use-api"></a>Utiliser l’API

- Utilisez la version API 2018-10-15-preview.
- Pour attacher votre galerie, envoyez la demande, comme indiqué dans l’extrait suivant :
    
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
- Pour afficher toutes les images dans votre galerie d’images partagé, vous pouvez répertorier toutes les images partagées, ainsi que leurs ID de ressource par

    ```
    GET [Lab Resource Id]/SharedGalleries/mySharedGallery/SharedImages
    ````
- Pour créer une machine virtuelle à l’aide d’images partagées, vous pouvez effectuer une commande PUT sur les machines virtuelles et dans les propriétés de la machine virtuelle, transmettez l’ID des images partagés que vous avez obtenu à partir de l’appel précédent. Pour les propriétés. SharedImageId


## <a name="next-steps"></a>Étapes suivantes
Sur les artefacts, consultez les articles suivants :

- [Spécifier des artefacts obligatoires pour votre laboratoire](devtest-lab-mandatory-artifacts.md)
- [Créer des artefacts personnalisés](devtest-lab-artifact-author.md)
- [Ajouter un référentiel d’artefacts à un laboratoire](devtest-lab-artifact-author.md)
- [Diagnostiquer les échecs d’artefact](devtest-lab-troubleshoot-artifact-failure.md)
