---
title: Créer une offre de machine virtuelle Azure sur la Place de marché Azure à l’aide de votre propre image
description: Découvrez comment publier une offre de machine virtuelle sur la Place de marché Azure à l’aide de votre propre image.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 4711ea76af83594ec529cfda13a308fbe6646398
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200459"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Création d’une machine virtuelle à l’aide de votre propre image

Cet article explique comment créer et déployer une image de machine virtuelle fournie par l’utilisateur.

> [!NOTE]
> Avant de commencer cette procédure, passez en revue les [exigences techniques](marketplace-virtual-machines.md#technical-requirements) pour les offres de machine virtuelle Azure, y compris les exigences de disque dur virtuel (VHD).

Pour utiliser une image de base approuvée à la place, suivez les instructions fournies dans [Créer une image de machine virtuelle à partir d’une base approuvée](azure-vm-create-using-approved-base.md).

## <a name="configure-the-vm"></a>Configurer la machine virtuelle

Cette section décrit comment dimensionner, mettre à jour et généraliser une machine virtuelle Azure. Ces étapes sont nécessaires pour préparer le déploiement de votre machine virtuelle sur la Place de marché Microsoft Azure.

### <a name="size-the-vhds"></a>Taille des disques durs virtuels

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>Installer les dernières mises à jour

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-more-security-checks"></a>Effectuer des vérifications de sécurité supplémentaires

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Exécuter des tâches planifiées de configuration personnalisée

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

### <a name="generalize-the-image"></a>Généraliser l’image

Toutes les images dans Azure Marketplace doivent être réutilisables de façon générale. Pour autoriser cela, le VHD du système d’exploitation doit être généralisé. Cette opération consiste à supprimer d’une machine virtuelle tous les pilotes logiciels et identificateurs propres à une instance.

## <a name="bring-your-image-into-azure"></a>Intégrer votre image dans Azure

Il existe trois façons d’intégrer votre image dans Azure :

1. Chargez le disque dur virtuel dans Shared Image Gallery (SIG).
1. Chargez le disque dur virtuel dans un compte de stockage Azure.
1. Extrayez le disque dur virtuel d’une image managée (si vous utilisez les services de création d’images).

Les trois sections suivantes décrivent ces options.

### <a name="option-1-upload-the-vhd-as-shared-image-gallery"></a>Option 1 : charger le disque dur virtuel en tant que Shared Image Gallery

1. Chargez le ou les disques durs virtuels dans le compte de stockage.
2. Dans le Portail Azure, recherchez **Déployer un modèle personnalisé**.
3. Sélectionnez **Générer votre propre modèle dans l’éditeur**.
4. Copiez le modèle Azure Resource Manager (ARM) suivant.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sourceStorageAccountResourceId": {
          "type": "string",
          "metadata": {
            "description": "Resource ID of the source storage account that the blob vhd resides in."
          }
        },
        "sourceBlobUri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk0Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk1Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "galleryName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Shared Image Gallery."
          }
        },
        "galleryImageDefinitionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Definition."
          }
        },
        "galleryImageVersionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Version - should follow <MajorVersion>.<MinorVersion>.<Patch>."
          }
        }
      },
      "resources": [
        {
          "type": "Microsoft.Compute/galleries/images/versions",
          "name": "[concat(parameters('galleryName'), '/', parameters('galleryImageDefinitionName'), '/', parameters('galleryImageVersionName'))]",
          "apiVersion": "2020-09-30",
          "location": "[resourceGroup().location]",
          "properties": {
            "storageProfile": {
              "osDiskImage": {
                "source": {
                  "id": "[parameters('sourceStorageAccountResourceId')]",
                  "uri": "[parameters('sourceBlobUri')]"
                }
              },
    
              "dataDiskImages": [
                {
                  "lun": 0,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk0Uri')]"
                  }
                },
                {
                  "lun": 1,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk1Uri')]"
                  }
                }
              ]
            }
          }
        }
      ]
    }
    
    ```

5. Collez le modèle dans l’éditeur.

    :::image type="content" source="media/create-vm/vm-sample-code-screen.png" alt-text="Exemple d’écran de code pour machine virtuelle.":::

1. Sélectionnez **Enregistrer**.
1. Utilisez les paramètres de ce tableau pour renseigner les champs de l’écran qui suit.

| Paramètres | Description |
| --- | --- |
| sourceStorageAccountResourceId | ID de ressource du compte de stockage source dans lequel réside le disque dur virtuel blob.<br><br>Pour récupérer l’ID de ressource, accédez à votre **compte de stockage** sur **Portail Azure**, accédez à **Propriétés**, puis copiez la valeur **ResourceId**. |
| sourceBlobUri | URI d’objet blob de l’objet blob de disque dur virtuel du système d’exploitation (doit se trouver dans le compte de stockage fourni).<br><br>Pour récupérer l’URL de l’objet blob, accédez à votre **Compte de stockage** sur le **Portail Azure**, accédez à votre **objet blob**, puis copiez la valeur de l’**URL**. |
| sourceBlobDataDisk0Uri | URI d’objet blob de l’objet blob de disque dur virtuel de données (doit se trouver dans le compte de stockage fourni). Si vous n’avez pas de disque de données, supprimez ce paramètre du modèle.<br><br>Pour récupérer l’URL de l’objet blob, accédez à votre **Compte de stockage** sur le **Portail Azure**, accédez à votre **objet blob**, puis copiez la valeur de l’**URL**. |
| sourceBlobDataDisk1Uri | URI d’objet blob de l’objet blob de disque dur virtuel de données supplémentaire (doit se trouver dans le compte de stockage fourni). Si vous n’avez pas de disque de données supplémentaires, supprimez ce paramètre du modèle.<br><br>Pour récupérer l’URL de l’objet blob, accédez à votre **Compte de stockage** sur le **Portail Azure**, accédez à votre **objet blob**, puis copiez la valeur de l’**URL**. |
| galleryName | Nom de Shared Image Gallery |
| galleryImageDefinitionName | Nom de la définition d’image |
| galleryImageVersionName | Nom de la version de l’image à créer, au format suivant : `<MajorVersion>.<MinorVersion>.<Patch>` |
|

:::image type="content" source="media/create-vm/custom-deployment-window.png" alt-text="Affiche la fenêtre de déploiement personnalisée.":::

8. Sélectionnez **Revoir + créer**. Une fois la validation réussie, sélectionnez **Créer**.

> [!TIP]
> Le compte de l’éditeur doit avoir un accès « propriétaire » pour publier l’image SIG. Si nécessaire, suivez les étapes ci-dessous pour accorder l’accès :
>
> 1. Accédez à Shared Image Gallery (SIG).
> 2. Sélectionnez **Contrôle d’accès** (IAM) dans le volet gauche.
> 3. Sélectionnez **Ajouter**, puis **Ajouter une attribution de rôle**.
> 4. Sous **Rôle**, sélectionnez **Propriétaire**.
> 5. Pour **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou principal du service**.
> 6. Entrez l’adresse e-mail Azure de la personne qui publiera l’image.
> 7. Sélectionnez **Enregistrer**.<br><br>
> :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="La fenêtre Ajouter une attribution de rôle s’affiche":::.

### <a name="option-2-upload-the-vhd-to-a-storage-account"></a>Option 2 : charger le disque dur virtuel dans un compte de stockage

Configurez et préparez la machine virtuelle à télécharger comme décrit dans [Préparer un disque dur virtuel Windows ou un VHDX à charger sur Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) ou [Créer et Charger un VHD Linux](../virtual-machines/linux/create-upload-generic.md).

### <a name="option-3-extract-the-vhd-from-managed-image-if-using-image-building-services"></a>Option 3 : extraire le disque dur virtuel d’une image managée (si vous utilisez les services de création d’images)

Si vous utilisez un service de génération d’images, comme [Packer](https://www.packer.io/), vous devrez peut-être extraire le disque dur virtuel de l’image. Il n’existe pas de méthode directe pour effectuer cette opération. Vous devez créer une machine virtuelle et extraire le disque dur virtuel à partir du disque de machine virtuelle.

## <a name="create-the-vm-on-the-azure-portal"></a>Créez la machine virtuelle sur le Portail Azure

Effectuez les étapes suivantes pour créer l’image de machine virtuelle de base sur le [Portail Azure](https://ms.portal.azure.com/).

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/).
2. Sélectionnez **Machines virtuelles**.
3. Sélectionnez **+ ajouter**  pour ouvrir l’écran **Créer une machine virtuelle** .
4. Sélectionnez l’image dans la liste déroulante ou sélectionnez **Parcourir toutes les images publiques et privées** pour rechercher ou parcourir toutes les images de machines virtuelles disponibles.
5. Pour créer une machine virtuelle **Gen 2** , accédez à l’onglet **avancé** et sélectionnez l’option **Gen 2** .

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Sélectionnez GEN 1 ou Gen 2.":::

6. Sélectionnez la taille de la machine virtuelle à déployer.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Sélectionnez une taille de machine virtuelle recommandée pour l’image sélectionnée.":::

7. Fournissez les autres informations requises pour créer la machine virtuelle.
8. Sélectionnez **Vérifier + créer** pour passer en revue vos choix. Lorsque le message **Validation réussie** s’affiche, sélectionnez **Créer**.

Azure commence le provisionnement de la machine virtuelle que vous avez spécifiée. Suivez sa progression en sélectionnant l’onglet **Machines virtuelles** dans le menu de gauche. Une fois créé, l’état des modifications de la machine virtuelle est modifié pour **en cours d’exécution**.

## <a name="connect-to-your-vm"></a>Connexion à votre machine virtuelle

Reportez-vous à la documentation suivante pour vous connecter à votre machine virtuelle [Windows](../virtual-machines/windows/connect-logon.md) ou [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) .

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Testez votre image de machine virtuelle](azure-vm-image-test.md) pour vous assurer qu’elle répond aux exigences de publication de la Place de marché Azure. Cette étape est facultative.
- Si vous ne souhaitez pas tester votre image de machine virtuelle, connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/) et publiez l’image SIG (option n° 1).
- Si vous avez suivi l’option n° 2 ou n° 3, [Générez l’URI SAS](azure-vm-get-sas-uri.md).
- Si vous rencontrez des difficultés lors de la création de votre nouveau disque dur virtuel basé sur Azure, consultez [Forum aux questions sur la Place de marché Microsoft Azure](azure-vm-create-faq.md).
