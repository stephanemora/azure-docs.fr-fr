---
title: Créer une machine virtuelle Linux avec le générateur d’images Azure (préversion)
description: Créez une machine virtuelle Linux avec le générateur d’images Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: 854645af95d780053d94668921e41ac189bbbfb7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65159509"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Aperçu : Créer une machine virtuelle Linux avec le générateur d’images Azure

Cet article vous montre comment vous pouvez créer une image Linux personnalisée à l’aide du Générateur d’images Azure et de l’interface de ligne de commande Azure. L’exemple de cet article utilise trois différents [personnalisateurs](image-builder-json.md#properties-customize) pour la personnalisation de l’image :

- Shell (ScriptUri) - télécharge et exécute un [script shell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Shell (inline) - exécute des commandes spécifiques. Dans cet exemple, les commandes inline incluent la création d’un répertoire et la mise à jour du système d’exploitation.
- Fichier - copie un [fichier de GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) dans un répertoire sur la machine virtuelle.

Pour configurer l’image, nous allons utiliser un exemple de modèle .json. Le fichier .json que nous utilisons est : [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

> [!IMPORTANT]
> Le Générateur d’images Azure est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Inscrire les fonctionnalités
Pour utiliser le Générateur d’images Azure durant la phase préliminaire, vous devez inscrire la nouvelle fonctionnalité.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Vérifiez l’état d’inscription de la fonctionnalité.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Vérifiez votre inscription.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Si elle n’est pas inscrite, exécutez la commande suivante :

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Nous allons utiliser certains éléments d’information à plusieurs reprises, donc nous allons créer des variables pour les stocker.


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

Créez une variable pour votre ID d’abonnement. Vous pouvez l’obtenir avec `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Créez le groupe de ressources.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```


Accordez au Générateur d’images l’autorisation de créer des ressources dans ce groupe de ressources. La valeur `--assignee` est l’ID d’inscription de l’application pour le service Générateur d’images. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-json-example"></a>Télécharger l’exemple de fichier .json

Téléchargez l’exemple de fichier .json et configurez-le avec les variables que vous avez créées.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

## <a name="create-the-image"></a>Création de l’image
Envoyer la configuration de l’image au service Générateur d’images de votre machine virtuelle

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Démarrez la génération de l’image.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Attendez que la compilation soit terminée. Cela peut durer environ 15 minutes.


## <a name="create-the-vm"></a>Création de la machine virtuelle

Créez la machine virtuelle avec l’image que vous avez créée.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Obtenez l’adresse IP à partir de la sortie de la création de la machine virtuelle et utilisez-la pour établir une connexion SSH à la machine virtuelle.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Vous devriez voir que l’image a été personnalisée avec un Message du jour dès que votre connexion SSH est établie !

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Saisissez `exit` lorsque vous avez fini pour fermer la connexion SSH.

## <a name="check-the-source"></a>Vérification de la source

Dans le modèle de Générateur d’images, dans les « Propriétés », vous verrez l’image source, le script de personnalisation qu’il exécute, et où elle est distribuée.

```azurecli-interactive
cat helloImageTemplateLinux.json
```

Pour plus d’informations sur ce fichier .json, consultez la [référence du modèle Générateur d’Images](image-builder-json.md)

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

Pour en savoir plus sur les composants du fichier .json utilisé dans cet article, consultez la [référence du modèle Générateur d’images](image-builder-json.md).