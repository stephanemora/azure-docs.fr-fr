---
title: Créer une VM Linux avec le Générateur d’images Azure (version préliminaire)
description: Créer une machine virtuelle Linux avec le Générateur d’images Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: 854645af95d780053d94668921e41ac189bbbfb7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159509"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Aperçu : Créer une machine virtuelle Linux avec le Générateur d’images Azure

Cet article vous montre comment vous pouvez créer une image Linux personnalisée à l’aide du Générateur d’images Azure et Azure CLI. L’exemple de cet article utilise trois différents [personnalisateurs](image-builder-json.md#properties-customize) pour la personnalisation de l’image :

- Interpréteur de commandes (ScriptUri) - téléchargements et exécute un [script shell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Interpréteur de commandes (inline) - exécute les commandes spécifiques. Dans cet exemple, les commandes inline incluent la création d’un répertoire et de la mise à jour le système d’exploitation.
- Fichier - copies un [fichier à partir de GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) dans un répertoire sur la machine virtuelle.

Pour configurer l’image, nous allons utiliser un exemple de modèle .json. Le fichier .json que nous utilisons est ici : [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

> [!IMPORTANT]
> Générateur d’images Azure est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Inscrire les fonctionnalités
Pour utiliser le Générateur d’images Azure durant la phase préliminaire, vous devez inscrire la nouvelle fonctionnalité.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Vérifiez l’état de l’inscription de fonctionnalité.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Vérifier votre inscription.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Si elles ne dites pas inscrit, exécutez la commande suivante :

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Nous allons utiliser certains éléments d’information à plusieurs reprises, donc nous allons créer des variables pour stocker ces informations.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

Créer une variable pour votre ID d’abonnement. Vous pouvez obtenir à l’aide de cette `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Créez le groupe de ressources.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```


Accorder l’autorisation d’Image Builder pour créer des ressources dans ce groupe de ressources. Le `--assignee` valeur est l’ID d’inscription d’application pour le service de générateur d’images. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-json-example"></a>Téléchargez l’exemple .json

Télécharger l’exemple de fichier .json et configurez-le avec les variables que vous avez créé.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

## <a name="create-the-image"></a>Création de l’image
Envoyer la configuration de l’image pour le service de générateur d’images de machine virtuelle

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Démarrer la génération de l’image.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Attendez que la build est terminée. Cela peut prendre environ 15 minutes.


## <a name="create-the-vm"></a>Création de la machine virtuelle

Créer la machine virtuelle à l’aide de l’image que vous avez créé.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Obtenir l’adresse IP à partir de la sortie de la création de la machine virtuelle et l’utiliser SSH à la machine virtuelle.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Vous devez voir que l’image a été personnalisé avec un Message de la journée dès que votre connexion SSH est établie !

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Type `exit` lorsque vous avez terminé pour fermer la connexion SSH.

## <a name="check-the-source"></a>Vérification de la source

Dans le modèle de générateur d’Image, dans les « propriétés », vous verrez l’image source, la personnalisation créez un script s’exécute, et où elle est distribuée.

```azurecli-interactive
cat helloImageTemplateLinux.json
```

Pour plus d’informations sur ce fichier .json, consultez [référence du modèle Générateur d’Image](image-builder-json.md)

## <a name="clean-up"></a>Nettoyer

Lorsque vous avez terminé, supprimez les ressources.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01

az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les composants du fichier .json utilisé dans cet article, consultez [référence du modèle Générateur d’images](image-builder-json.md).