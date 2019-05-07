---
title: Créer un modèle de générateur d’images Azure (version préliminaire)
description: Découvrez comment créer un modèle à utiliser avec le Générateur d’images Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: b4646879eb7eeecf41852baab7ab64e4053b05e1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159599"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Aperçu : Créer un modèle de générateur d’images Azure 

Générateur d’images Azure utilise un fichier .json pour passer des informations dans le service de générateur d’images. Dans cet article nous allons vous présenter les sections du fichier json, vous pouvez donc créer votre propre. Pour voir des exemples de fichiers .json complète, consultez le [GitHub de générateur d’Image Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Il s’agit du format de modèle de base :

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "<build timeout in minutes>": {}, 
        "build": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Version de type et des API

Le `type` est le type de ressource, qui doit être `"Microsoft.VirtualMachineImages/imageTemplates"`. Le `apiVersion` change au fil du temps en tant que les modifications de l’API, mais doit être `"2019-05-01-preview"` pour la version préliminaire.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Lieu

L’emplacement est la région où l’image personnalisée doit être créée. L’aperçu du Générateur d’images, les régions suivantes sont prises en charge :

- USA Est
- USA Est 2
- USA Centre-Ouest
- USA Ouest
- USA Ouest 2


```json
    "location": "<region>",
```
    
## <a name="depends-on-optional"></a>Dépend (facultatif)

Cette section facultative peut être utilisée pour vous assurer que les dépendances sont terminées avant de continuer. 

```json
    "dependsOn": [],
```

Pour plus d’informations, consultez [définir des dépendances de ressource](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Identité
Par défaut, prend en charge du Générateur d’images à l’aide de scripts ou de la copie des fichiers à partir de plusieurs emplacements, tels que GitHub et le stockage Azure. Pour utiliser ces modèles, il doivent être accessibles publiquement.

Vous pouvez également utiliser une identité Azure User-Assigned gérés, vous avez définis, pour autoriser l’accès au Générateur d’Image de stockage Azure, tant que l’identité a reçu un minimum de « Lecteur de données Blob de stockage » sur le compte de stockage Azure. Cela signifie que vous n’avez pas besoin de rendre les objets BLOB de stockage accessible en externe, ou des jetons de SAP le programme d’installation.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Pour obtenir un exemple complet, consultez [ utiliser une identité gérée de Azure User-Assigned pour accéder aux fichiers dans le stockage Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Prise en charge du Générateur de rapports pour une identité affectée de l’image : • une identité unique prend uniquement en charge • ne prend pas en charge les noms de domaine personnalisés

Pour plus d’informations, consultez [What ' s des identités gérées pour les ressources Azure ?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Pour plus d’informations sur le déploiement de cette fonctionnalité, consultez [configurer managed identités pour les ressources Azure sur une machine virtuelle Azure à l’aide d’Azure CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Propriétés : source

Le `source` section contient des informations sur l’image source qui sera utilisé par le Générateur d’images.

L’API nécessite un « SourceType » qui définit la source pour la génération d’image, actuellement, il existe trois types :
- ISO - Utilisez cette option lorsque la source est un fichier ISO RHEL.
- PlatformImage - indique que l’image source est une image de place de marché.
- ManagedImage - utiliser cela lors du démarrage à partir d’une image managée régulière.
- SharedImageVersion - il est utilisé lorsque vous utilisez une version de l’image dans une galerie d’images partagé comme source.

### <a name="iso-source"></a>Source de l’ISO

Générateur d’images Azure prend uniquement en charge à l’aide publiée Red Hat Enterprise Linux 7.x binaire DVD ISO, pour la version préliminaire. Générateur d’images prend en charge :
- RHEL 7.3 
- RHEL 7.4 
- RHEL 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

Pour obtenir le `sourceURI` et `sha256Checksum` valeurs, accédez à `https://access.redhat.com/downloads` puis sélectionnez le produit **Red Hat Enterprise Linux**et une version prise en charge. 

Dans la liste des **programmes d’installation et des Images pour Red Hat Enterprise Linux Server**, vous devez copier le lien pour Red Hat Enterprise Linux 7.x binaire DVD et la somme de contrôle.

> [!NOTE]
> Les jetons d’accès des liens sont actualisées à intervalles fréquents, chaque fois que vous souhaitez envoyer un modèle, vous devez donc vérifier si le RH liez adresse a été modifiée.
 
### <a name="platformimage-source"></a>PlatformImage source 
Générateur d’images Azure prend en charge les images de place de marché Azure suivantes :
* Ubuntu 18.04
* Ubuntu 16.04
* RHEL 7.6
* CentOS 7.6
* Windows 2016
* Windows 2019

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "18.04.201903060"
        },
```


Les propriétés ici sont les mêmes que servant à créer des machines virtuelles, à l’aide de CLI AZ, exécutez le ci-dessous pour obtenir les propriétés : 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> Version ne peut pas être « latest », vous devez utiliser la commande ci-dessus pour obtenir un numéro de version. 

### <a name="managedimage-source"></a>ManagedImage source

Définit l’image source comme une image managée existante d’une machine virtuelle ou un disque dur virtuel généralisé. L’image managée source doit être d’un système d’exploitation pris en charge et se trouver dans la même région que votre modèle de générateur d’images Azure. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

Le `imageId` doit être l’ID de ressource de l’image managée. Utilisez `az image list` pour répertorier les images disponibles.


### <a name="sharedimageversion-source"></a>SharedImageVersion source
Définit l’image source une version existante de l’image dans une galerie d’images partagé. La version de l’image doit être d’un système d’exploitation pris en charge, et l’image doit être répliqué sur la même région que votre modèle de générateur d’images Azure. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

Le `imageVersionId` doit être l’ID de ressource de la version de l’image. Utilisez [liste de version de l’image az sig](/cli/azure/sig/image-version#az-sig-image-version-list) à la liste des versions d’image.

## <a name="properties-customize"></a>Propriétés : personnaliser


Générateur d’images prend en charge plusieurs « personnalisateurs ». Personnalisateurs sont des fonctions qui permettent de personnaliser votre image, telles que l’exécution de scripts, ou le redémarrage des serveurs. 

Lorsque vous utilisez `customize`: 
- Vous pouvez utiliser plusieurs personnalisateurs, mais elles doivent avoir une valeur unique `name`.
- Personnalisateurs de s’exécuter dans l’ordre spécifié dans le modèle.
- Si un personnalisateur échoue, le composant de personnalisation entière échoue et l’état d’une erreur.
- Envisagez de combien de temps votre build d’image nécessitent et ajuster la propriété 'buildTimeoutInMinutes' pour autoriser Générateur d’images suffisamment de temps pour terminer.
- Il est fortement recommandé de que tester le script soigneusement avant de l’utiliser dans un modèle. Il sera plus facile de déboguer le script sur votre propre machine virtuelle.
- Ne placez pas les données sensibles dans les scripts. 
- Les emplacements de script doivent être publiquement accessibles, sauf si vous utilisez [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
La section de personnalisation est un tableau. Générateur d’images Azure s’exécute via les personnalisateurs dans un ordre séquentiel. Tout échec dans n’importe quel Personnalisateur échoue le processus de génération. 
 
 
### <a name="shell-customizer"></a>Personnalisation de l’interpréteur de commandes

Le Personnalisateur shell prend en charge les scripts de shell en cours d’exécution, il doivent être accessibles publiquement pour la phase INBUILD pour y accéder.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>"        
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

Prise en charge du système d’exploitation : Linux 
 
Personnaliser les propriétés :

- **type** – Shell 
- **nom** - nom pour le suivi de la personnalisation 
- **scriptUri** -URI vers l’emplacement du fichier 
- **inline** -tableau de commandes d’environnement, séparées par des virgules.
 
> [!NOTE]
> Lors de l’exécution de la personnalisation de l’interpréteur de commandes avec la source de RHEL ISO, vous devez vous assurer de votre première handles de shell personnalisation inscrire avec un serveur de droits de Red Hat avant toute personnalisation se produit. Une fois que la personnalisation est terminée, le script doit annuler l’inscription auprès du serveur de rapports associés.

### <a name="windows-restart-customizer"></a>Personnalisateur de redémarrage de Windows 
La personnalisation de redémarrage vous permet de redémarrer une machine virtuelle Windows et attendez qu’elle reviennent en ligne, ce qui permet d’installer le logiciel qui nécessite un redémarrage.  

```json 
     "customize": [ 
            "type{ ": "WindowsRestart", 
            "restartCommand": "shutdown /r /f /t 0 /c", 
            "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > buildArtifacts/azureImageBuilderRestart.txt",
            "restartTimeout": "5m"
         }],
```

Prise en charge du système d’exploitation : Windows
 
Personnaliser les propriétés :
- **Type** : WindowsRestart
- **restartCommand** -commande à exécuter le redémarrage (facultatif). Par défaut, il s’agit de `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – commande pour vérifier si le redémarrage a réussi (facultatif). 
- **restartTimeout** -redémarrez le délai d’attente spécifié sous forme de chaîne de magnitude et d’unité. Par exemple, `5m` (5 minutes) ou `2h` (2 heures). La valeur par défaut est : '5m'


### <a name="powershell-customizer"></a>Personnalisateur de PowerShell 
Le Personnalisateur shell prend en charge en cours d’exécution des scripts PowerShell et les commandes inline, les scripts doivent être accessibles publiquement pour la phase INBUILD pour y accéder.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "valid_exit_codes": "<exit code>" 
         } 
    ], 
```

Prise en charge du système d’exploitation : Windows et Linux

Personnaliser les propriétés :

- **type** – PowerShell.
- **scriptUri** -URI vers l’emplacement du fichier de script PowerShell. 
- **inline** – Inline des commandes à exécuter, séparés par des virgules.
- **valid_exit_codes** – facultatif, des codes valides qui peuvent être retournées par le script/en ligne de commande, cela permet d’éviter signalé l’échec du script en ligne.

### <a name="file-customizer"></a>Personnalisation du fichier

La personnalisation du fichier Générateur d’images permet de télécharger un fichier à partir d’un GitHub ou le stockage Azure. Si vous disposez d’un pipeline de génération d’image qui s’appuie sur les artefacts de build, vous pouvez définir la personnalisation de fichier à télécharger à partir du partage de build et déplacer les artefacts dans l’image.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>" 
         }
     ]
```

Prise en charge du système d’exploitation : Linux et Windows 

Propriétés de personnalisation du fichier :

- **sourceUri** -un point de terminaison de stockage accessible, il peut s’agir GitHub ou le stockage Azure. Vous pouvez uniquement télécharger un fichier, pas un répertoire complet. Si vous devez télécharger un répertoire, utilisez un fichier compressé, puis à l’aide de l’interpréteur de commandes ou de PowerShell personnalisateurs décompresser. 
- **destination** – c’est le nom de chemin d’accès et le fichier de destination complet. N’importe quel chemin d’accès référencé et sous-répertoires doit existe, utilisez les personnalisateurs de l’interpréteur de commandes ou de PowerShell pour les définir au préalable. Vous pouvez utiliser les personnalisateurs de script pour créer le chemin d’accès. 

Cela est pris en charge par les répertoires de Windows et les chemins d’accès de Linux, mais il existe quelques différences : 
- Système d’exploitation Linux – le seul chemin Image builder peut écrire dans est /tmp.
- Windows : aucune restriction de chemin d’accès, mais le chemin d’accès doit exister.
 
 
S’il existe une erreur tente de télécharger le fichier, ou le placer dans un répertoire spécifié, l’étape de personnalisation échoue, et il s’agit dans le customization.log.

Fichiers dans la personnalisation du fichier peuvent être téléchargés depuis le stockage Azure à l’aide de [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="generalize"></a>Généraliser 
Par défaut, le Générateur d’images Azure s’exécutent également « annuler » le déploiement d’un code à la fin de chaque phase de personnalisation d’image, pour « généraliser » l’image. La généralisation est un processus dans lequel l’image est configuré afin qu’il peut être réutilisé pour créer plusieurs machines virtuelles. Pour les machines virtuelles Windows, le Générateur d’images Azure utilise Sysprep. Pour Linux, le Générateur d’images Azure s’exécute « waagent-deprovision ». 

Les commandes aux utilisateurs du Générateur d’Image afin de généraliser peut ne pas convenir pour chaque situation, afin de générateur d’images Azure vous permettra de personnaliser cette commande, si nécessaire. 

Si vous migrez une personnalisation existante, et que vous utilisez des commandes Sysprep/waagent différentes, vous pouvez en utilisant les commandes génériques du Générateur d’images et si la création de la machine virtuelle échoue, utilisez vos propres commandes de Sysprep ou waagent.

Si le Générateur d’images Azure crée une image personnalisée de Windows avec succès et que vous créez une machine virtuelle à partir, puis recherchez qui la création de la machine virtuelle échoue ou ne se termine pas correctement, vous devez consulter la documentation de Windows Server Sysprep ou déclencher une demande de support avec le Équipe Windows Server Sysprep Services de support technique, qui peut résoudre les problèmes et conseils sur l’utilisation correcte de Sysprep.


#### <a name="default-sysprep-command"></a>Valeur par défaut de la commande Sysprep
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Commande de mise hors service de Linux par défaut

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Substituer les commandes
Pour remplacer les commandes, utilisez les fournisseurs de script PowerShell ou l’interpréteur de commandes pour créer les fichiers de commandes avec le nom exact du fichier et les placer dans les répertoires appropriés :

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Générateur d’images lira ces commandes, celles-ci sont écrites dans les journaux AIB, « customization.log ». Consultez [dépannage](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) sur la façon de collecter les journaux.
 
## <a name="properties-distribute"></a>Propriétés : distribuer

Générateur d’images Azure prend en charge trois cibles de distribution : 

- **managedImage** - géré image.
- **sharedImage** -Galerie d’images partagé.
- **Disque dur virtuel** -disque dur virtuel dans un compte de stockage.

Vous pouvez distribuer une image à la fois à des types de cibles dans la même configuration, consultez la rubrique [exemples](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Étant donné que vous pouvez avoir plusieurs cibles à distribuer aux, Générateur d’images gère un état pour chaque cible de distribution qui sont accessibles en interrogeant le `runOutputName`.  Le `runOutputName` est un objet que vous pouvez interroger valider de distribution pour plus d’informations sur cette distribution. Par exemple, vous pouvez interroger l’emplacement du disque dur virtuel ou de régions où la version de l’image ont été répliquée vers. Il s’agit d’une propriété de chaque cible de distribution. Le `runOutputName` doit être unique pour chaque cible de distribution.
 
### <a name="distribute-managedimage"></a>Distribuer : managedImage

La sortie de l’image correspond à une ressource d’image managée.

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
Distribuer des propriétés :
- **type** – managedImage 
- **imageId** – ID de ressource de l’image de destination, de format attendu : /subscriptions/<subscriptionId>/resourceGroups/<destinationResourceGroupName>/providers/Microsoft.Compute/images/<imageName>
- **emplacement** -emplacement de l’image managée.  
- **runOutputName** – unique nom d’identification de la distribution.  
- **artifactTags** -balises de paire de valeur de clé spécifié par l’utilisateur facultative.
 
 
> [!NOTE]
> Le groupe de ressources de destination doit exister.
> Si vous souhaitez que l’image distribuée vers une autre région, cela augmente le temps de déploiement. 

### <a name="distribute-sharedimage"></a>Distribuer : sharedImage 
La galerie d’images Azure partagé est un nouveau service de gestion des images qui permet la gestion de la réplication de la région d’image, le contrôle de version et le partage des images personnalisées. Générateur d’images Azure prend en charge la distribution de ce service, donc vous pouvez distribuer des images pour les régions prises en charge par les galeries d’images partagé. 
 
Une galerie d’images partagé est constituée par : 
 
- Galerie : conteneur pour plusieurs images partagées. Une galerie est déployée dans une région.
- Définitions de l’image - un regroupement conceptuel pour les images. 
- Versions de l’image : il s’agit d’un type d’image utilisé pour le déploiement d’un ensemble de la machine virtuelle ou de mise à l’échelle. Version d’image peut être répliquées vers d’autres régions où les machines virtuelles doivent être déployées.
 
Avant de pouvoir distribuer à la galerie d’images, vous devez créer une galerie et une définition de l’image, consultez [images partagées](shared-images.md). 

```json
{
     "type": "sharedImage",
     "galleryImageId": “<resource ID>”,
     "runOutputName": "<name>",
     "artifactTags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
     "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]}
``` 

Distribuer des propriétés pour les galeries d’images partagé :

- **type** -sharedImage  
- **galleryImageId** – ID de la galerie d’images partagé. Le format est : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>.
- **runOutputName** – unique nom d’identification de la distribution.  
- **artifactTags** -balises de paire de valeur de clé spécifié par l’utilisateur facultative.
- **replicationRegions** -tableau de régions pour la réplication. L’une des régions doit être la région où la galerie est déployée.
 
> [!NOTE]
> Vous pouvez utiliser le Générateur d’images Azure dans une autre région à la galerie, mais le service de générateur d’images Azure devra transférer l’image entre les centres de données, et cela prendra plus de temps. Générateur d’images rétablit automatiquement la version l’image, basée sur un entier monotone, vous ne pouvez pas actuellement le spécifier. 

### <a name="distribute-vhd"></a>Distribuer : Disque dur virtuel (VHD)   
Vous pouvez générer pour un disque dur virtuel. Vous pouvez ensuite copier le disque dur virtuel et l’utiliser pour publier sur Azure MarketPlace ou utiliser avec Azure Stack.  

```json
 { 
     "type": "VHD",
     "runOutputName": "<VHD name>",
     "tags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
 }
```
 
Prise en charge du système d’exploitation : Windows et Linux

Distribuer les paramètres de disque dur virtuel :

- **type** -disque dur virtuel.
- **runOutputName** – unique nom d’identification de la distribution.  
- **balises** -balises de paire de valeur de clé spécifié par l’utilisateur facultative.
 
Générateur d’images Azure n’autorise pas l’utilisateur de spécifier un emplacement du compte de stockage, mais vous pouvez interroger l’état de la `runOutputs` pour obtenir l’emplacement.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Une fois que le disque dur virtuel a été créé, copiez-le dans un emplacement différent, dès que possible. Le disque dur virtuel est stocké dans un compte de stockage dans le groupe de ressources temporaire créé lorsque le modèle d’image est envoyé au service de générateur d’images Azure. Si vous supprimez le modèle d’image, vous allez perdre le disque dur virtuel. 
 
## <a name="next-steps"></a>Étapes suivantes

Il existe des exemples de fichiers .json pour différents scénarios dans le [GitHub de générateur d’Image Azure](https://github.com/danielsollondon/azvmimagebuilder).
 
 
 
 
 
 
 
 
 
 
