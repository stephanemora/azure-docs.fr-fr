---
title: Créer une image de machine virtuelle et utiliser une identité managée affectée par l’utilisateur pour accéder aux fichiers dans le stockage Azure (préversion)
description: Créez l’image de machine virtuelle à l’aide du générateur d’images Azure, qui peut accéder aux fichiers stockés dans le stockage Azure à l’aide d’identités managées affectées par l’utilisateur.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 36016e462e3f4906c4dfe8c58501c82fd554f3bd
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720586"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Créer une image et utiliser une identité managée affectée par l’utilisateur pour accéder aux fichiers dans le stockage Azure 

Le générateur d’images Azure prend en charge l’utilisation de scripts ou la copie de fichiers à partir de plusieurs emplacements, tels que GitHub et le stockage Azure etc. Pour utiliser ces modèles, ceux-ci doivent avoir été accessibles en externe au générateur d’images Azure, toutefois vous pouvez protéger les objets Blob Azure Storage à l’aide de jetons SAS.

Cet article montre comment créer une image personnalisée à l’aide d’Azure VM Image Builder, où le service utilise une [identité managée attribuée par l’utilisateur](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pour accéder aux fichiers dans le stockage Azure pour la personnalisation de l’image, sans avoir à rendre les fichiers publiquement accessibles, ou pour la configuration de jetons SAS.

Dans l’exemple ci-dessous, vous créez deux groupes de ressources, dont l’un est utilisé pour l’image personnalisée et l’autre héberge un compte de stockage Azure contenant un fichier de script. Cela simule un scénario réel, où vous pouvez avoir des artefacts de build, ou des fichiers image dans différents comptes de stockage, en dehors du générateur d’images. Vous allez créer une identité affectée par l’utilisateur, puis accorder ces autorisations de lecture sur le fichier de script. Toutefois, vous ne définirez pas d’accès public à ce fichier. Vous allez ensuite utiliser l’outil de personnalisation de l’interpréteur de commandes pour télécharger et exécuter ce script à partir du compte de stockage.


> [!IMPORTANT]
> Le Générateur d’images Azure est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Inscrire les fonctionnalités
Pour utiliser le Générateur d’images Azure pendant la préversion, vous devez inscrire la nouvelle fonctionnalité.

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
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

Créez une variable pour votre ID d’abonnement. Vous pouvez l’obtenir avec `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Créez les groupes de ressources pour le stockage de script et d’images.

```azurecli-interactive
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


Créez le stockage et copiez-y l’exemple de script à partir de GitHub.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```



Accordez au générateur d’images l’autorisation de créer des ressources dans le groupe de ressources d’image. La valeur `--assignee` est l’ID d’inscription d’application du service Générateur d’images. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>Créer une identité managée affectée par l’utilisateur

Créer l’identité et affecter des autorisations pour le compte de stockage de script. Pour plus d’informations, voir [Identité managée affectée par l’utilisateur](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

```azurecli-interactive
# Create the user assigned identity 
identityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $identityName
# assign the identity permissions to the storage account, so it can read the script blob
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
# create the user identity URI
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName
```


## <a name="modify-the-example"></a>Modifier l’exemple

Téléchargez l’exemple de fichier .json et configurez-le avec les variables que vous avez créées.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>Création de l’image

Envoyez la configuration de l’image au service Générateur d’images Azure.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Lancez la génération de l’image.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Attendez la fin de la génération. Cela peut durer environ 15 minutes.

## <a name="create-a-vm"></a>Créer une machine virtuelle

Créez une machine virtuelle à partir de l’image. 

```bash
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Une fois la machine virtuelle créée, démarrez une session SSH avec la machine virtuelle.

```azurecli-interactive
ssh aibuser@<publicIp>
```

Vous devriez voir que l’image a été personnalisée avec un Message du jour dès que votre connexion SSH est établie !

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Nettoyer

Lorsque vous avez terminé, vous pouvez supprimer les ressources si elles ne sont plus nécessaires.

```azurecli-interactive
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Si vous avez des problèmes d’utilisation de générateur d’images Azure, consultez [Résolution des problèmes](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json).