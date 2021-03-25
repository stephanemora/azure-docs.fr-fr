---
title: Générer un URI de SAS pour une image de machine virtuelle – Place de marché Azure
description: Générez un URI de signature d’accès partagé (SAP) pour des disques durs virtuels (VHD) dans Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: b8f5fbc076859be9f5ff5a215f92811d543ed7e4
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800106"
---
# <a name="how-to-generate-a-sas-uri-for-a-vm-image"></a>Comment générer un URI SAS pour une image de machine virtuelle

> [!NOTE]
> Vous n’avez pas besoin d’un URI SAS pour publier votre machine virtuelle. Il vous suffit de partager une image dans l’Espace partenaires. Reportez-vous à [Créer une machine virtuelle à l’aide d’une base approuvée](https://docs.microsoft.com/azure/marketplace/azure-vm-create-using-approved-base) ou [Créer une machine virtuelle à l’aide de votre propre image](https://docs.microsoft.com/azure/marketplace/azure-vm-create-using-own-image).

La génération d’URI de SAS pour vos disques durs virtuels impose les exigences suivantes :

- Elles prennent uniquement en charge les disques durs virtuels non managés
- Seules les autorisations Liste et Lecture sont requises. Ne fournissez pas d’accès en écriture ou en suppression
- La durée d’accès (date d’expiration) doit être d’au moins trois semaines à partir de la date de création de l’URI SAS
- Pour vous protéger contre les changements de temps UTC, choisissez la veille de la date actuelle comme date de début. Par exemple, si la date actuelle est le 16 juin 2020, sélectionnez 15/6/2020

## <a name="extract-vhd-from-a-vm"></a>Extraire le disque dur virtuel à partir d’une machine virtuelle

> [!NOTE]
> Vous pouvez ignorer cette étape si vous disposez déjà d’un disque dur virtuel chargé dans un compte de stockage.

Pour extraire le disque dur virtuel de votre machine virtuelle, vous devez prendre un instantané de votre disque de machine virtuelle et extraire le disque dur virtuel de l’instantané.

Commencez par créer un instantané du disque de la machine virtuelle :

1. Connectez-vous au portail Azure.
2. À partir du coin supérieur gauche, sélectionnez Créer une ressource, puis recherchez et sélectionnez Instantané.
3. Dans le panneau Instantané, sélectionnez Créer.
4. Entrez un nom pour la capture instantanée.
5. Sélectionnez un groupe de ressources existant ou entrez le nom d’un nouveau.
6. Dans Disque source, sélectionnez le disque managé dont vous souhaitez obtenir une capture instantanée.
7. Sélectionnez le type de compte à utiliser pour stocker la capture instantanée. Utilisez Standard HDD, sauf si vous en avez besoin de la stocker sur un disque SSD hautes performances.
8. Sélectionnez Créer.

### <a name="extract-the-vhd"></a>Extraire le disque dur virtuel

Utilisez le script suivant pour exporter l’instantané dans un disque dur virtuel de votre compte de stockage.

```azurecli
#Provide the subscription Id where the snapshot is created
$subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
$resourceGroupName=myResourceGroupName

#Provide the snapshot name
$snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1
$sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. Currently, only general purpose v1 storage is supported.
$storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
$storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
$storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
$destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

$sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

### <a name="script-explanation"></a>Explication du script
Ce script utilise les commandes suivantes afin de générer l’URI SAS pour un instantané et copie le VHD sous-jacent vers un compte de stockage à l’aide de l’URI SAS. Chaque commande du tableau renvoie à une documentation spécifique.


|Commande  |Notes  |
|---------|---------|
| az disk grant-access    |     Génère l’URI SAP en lecture seule qui est utilisé pour copier le fichier de VHD sous-jacent vers un compte de stockage ou le télécharger en local    |
|  az storage blob copy start   |    Copiez un objet blob de façon asynchrone à partir d’un compte de stockage vers un autre. Utilisez az storage blob show pour vérifier l’état du nouvel objet blob.     |
|

## <a name="generate-the-sas-address"></a>Générer l’adresse de SAS

Deux outils sont couramment utilisés pour créer une adresse (URL) de SAS :

1. **Explorateur Stockage Azure** : disponible sur le Portail Azure.
2. **Azure CLI** : outil recommandé pour les systèmes d’exploitation autres que Windows et les environnements d’intégration automatisée ou continue.

### <a name="using-tool-1-azure-storage-explorer"></a>Utilisation de l’outil 1 : Explorateur de stockage Azure

1. Accédez à votre **compte de stockage**.
1. Ouvrez l’**explorateur de stockage**.

    :::image type="content" source="media/create-vm/storge-account-explorer.png" alt-text="Fenêtre du compte de stockage.":::

3. Dans le **conteneur**, cliquez avec le bouton droit sur le fichier de disque dur virtuel, puis sélectionnez **Get Share Access Signature** (Obtenir la signature de partage d’accès).
4. Dans la boîte de dialogue **Signature d’accès partagé**, renseignez les champs suivants :

    1. Heure de début : date de début de l’autorisation d’accès au disque dur virtuel. Indiquez une date correspondant à la veille de la date du jour.
    2. Heure d’expiration : date de fin de l’autorisation d’accès au disque dur virtuel. Indiquez une date correspondant au moins à trois semaines après la date du jour.
    3. Autorisations : sélectionnez les autorisations Lecture et Liste.
    4. Niveau de conteneur : cochez la case Générer un URI de signature d’accès partagé au niveau du conteneur.

    ![Boîte de dialogue Signature d’accès partagé.](media/vm/create-sas-uri-storage-explorer.png)

5. Pour créer l’URI de SAS associé à ce disque dur virtuel, sélectionnez **Créer**.
6. Copiez l’URI et enregistrez-le dans un fichier texte à un emplacement sécurisé. Cet URI de SAS généré concerne l’accès au niveau du conteneur. Pour le rendre spécifique, modifiez le fichier texte afin d’ajouter le nom du disque dur virtuel.
7. Insérez le nom de votre disque dur virtuel après la chaîne vhds dans l’URI de SAS (incluez une barre oblique). L’URI de SAS final doit ressembler à ceci :

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

8. Répétez ces étapes pour chaque disque dur virtuel dans les plans que vous allez publier.

### <a name="using-tool-2-azure-cli"></a>Utilisation de l’outil 2 : Azure CLI

1. Téléchargez et installez [Microsoft Azure CLI](/cli/azure/install-azure-cli). Des versions sont disponibles pour Windows, macOS et diverses distributions de Linux.
2. Créez un fichier PowerShell (extension .ps1), copiez le code suivant, puis enregistrez-le localement.

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <container-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. Modifiez le fichier pour utiliser les valeurs de paramètres suivantes. Indiquez des dates au format date/heure UTC, par exemple 2020-04-01T00:00:00Z.

    - account-name – nom de votre compte Stockage Azure.
    - account-key – votre clé de compte Stockage Azure.
    - start-date – date de début de l’autorisation d’accès au disque dur virtuel. Indiquez une date correspondant à la veille de la date du jour.
    - expiry-date – date de fin de l’autorisation d’accès au disque dur virtuel. Indiquez une date postérieure d’au moins trois semaines à la date actuelle.

    Voici un exemple de valeurs de paramètre appropriées (au moment de la rédaction de cet article) :

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name <container-name> -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’
    ```

1. Enregistrez les modifications.
2. En appliquant l’une des méthodes suivantes, exécutez ce script avec des privilèges d’administrateur pour créer une chaîne de connexion SAS offrant un accès au niveau du conteneur :

    - Exécutez le script à partir de la console. Dans Windows, cliquez avec le bouton droit sur le script et sélectionnez **Exécuter en tant qu’administrateur**.
    - Exécutez le script à partir d’un éditeur de script PowerShell comme [Windows PowerShell ISE](/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Cet écran illustre la création d’une chaîne de connexion SAS dans cet éditeur :

    [![création d’une chaîne de connexion SAS dans l’éditeur PowerShell](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. Copiez la chaîne de connexion SAS et enregistrez-la dans un fichier texte à un emplacement sécurisé. Modifiez cette chaîne pour ajouter les informations d’emplacement du disque dur virtuel associé afin de créer l’URI de SAS final.
7. Dans le portail Azure, accédez au stockage d’objets blob qui comprend le disque dur virtuel associé au nouvel URI.
8. Copiez l’URL du point de terminaison du service Blob :

    ![Copie de l’URL du point de terminaison du service Blob.](media/vm/create-sas-uri-blob-endpoint.png)

9. Modifiez le fichier texte avec la chaîne de connexion SAS obtenue à l’étape 6. Créez l’URI de SAS complet en utilisant le format suivant :

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>Vérifier l’URI SAS

Vérifiez l’URI de SAS avant de le publier dans l’Espace partenaires afin d’éviter tout problème lié à l’URI SAS une fois la demande effectuée. Ce processus est facultatif mais recommandé.

- L’URI comprend le nom du fichier image de votre disque dur virtuel, y compris l’extension `.vhd`.
- `Sp=rl` apparaît près du milieu de votre URI. Cette chaîne indique que l’accès Lecture et Liste est spécifié.
- Quand `sr=c` s’affiche, cela signifie que l’accès au niveau du conteneur est spécifié.
- Copiez et collez l’URI dans un navigateur afin de tester-télécharger l’objet blob (vous pouvez annuler l’opération avant la fin du téléchargement).

## <a name="next-steps"></a>Étapes suivantes

- Si vous rencontrez des problèmes, consultez la page [Messages d’échec SAS de machine virtuelle](azure-vm-sas-failure-messages.md).
- [Se connecter à l’Espace partenaires](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)
- [Créer une offre de machine virtuelle sur la Place de marché Azure](azure-vm-create.md)
