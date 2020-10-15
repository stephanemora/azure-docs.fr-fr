---
title: Obtenir un URI de SAS pour votre image de machine virtuelle - Place de marché Azure
description: Générez un URI de signature d’accès partagé (SAP) pour vos disques durs virtuels (VHD) dans Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: a84f287c6303e093d68dd462ccc5cecc34b463cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89144500"
---
# <a name="get-a-sas-uri-for-your-vm-image"></a>Obtenir un URI de SAS pour votre image de machine virtuelle

Pendant le processus de publication, vous devez fournir un URI de SAS (Shared Access Signature) pour chaque disque dur virtuel associé à vos plans (anciennement appelés niveaux tarifaires). Microsoft doit accéder à ces disques durs virtuels pendant le processus de certification. Vous entrerez cet URI sous l’onglet **Plans** de l’Espace partenaires.

La génération d’URI de SAS pour vos disques durs virtuels impose les exigences suivantes :

- Elles prennent uniquement en charge les disques durs virtuels non managés
- Seules les autorisations Liste et Lecture sont requises. Ne fournissez pas d’accès en écriture ou en suppression
- La durée d’accès (date d’expiration) doit être d’au moins trois semaines à partir de la date de création de l’URI SAS
- Pour vous protéger contre les changements de temps UTC, choisissez la veille de la date actuelle comme date de début. Par exemple, si la date actuelle est le 16 juin 2020, sélectionnez 15/6/2020

## <a name="generate-the-sas-address"></a>Générer l’adresse de SAS

Deux outils sont couramment utilisés pour créer une adresse (URL) de SAS :

1. **Explorateur Stockage Microsoft Azure** : outil graphique disponible pour Windows, macOS et Linux
2. **Microsoft Azure CLI** : outil recommandé pour les systèmes d’exploitation autres que Windows et les environnements d’intégration automatisée ou continue

### <a name="using-tool-1-microsoft-storage-explorer"></a>Utilisation de l’outil 1 : Explorateur de stockage Microsoft

1. Téléchargez et installez [Explorateur Stockage Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/).
2. Ouvrez l’explorateur et, dans le menu de gauche, sélectionnez **Ajouter un compte**.
3. Dans la boîte de dialogue **Se connecter à Stockage Azure**, sélectionnez **Ajouter un compte Azure** et connectez-vous à votre compte Azure.
4. Dans le volet gauche de l’Explorateur, développez le nœud **Comptes de stockage**.
5. Cliquez avec le bouton droit sur votre disque dur virtuel et sélectionnez **Obtenir une signature d’accès partagé**.
6. Dans la boîte de dialogue **Signature d’accès partagé**, renseignez les champs suivants :

    1. Heure de début : date de début de l’autorisation d’accès au disque dur virtuel. Indiquez une date correspondant à la veille de la date du jour.
    2. Heure d’expiration : date de fin de l’autorisation d’accès au disque dur virtuel. Indiquez une date correspondant au moins à trois semaines après la date du jour.
    3. Autorisations : sélectionnez les autorisations Lecture et Liste.
    4. Niveau de conteneur : cochez la case Générer un URI de signature d’accès partagé au niveau du conteneur.

    ![Boîte de dialogue Signature d’accès partagé.](media/vm/create-sas-uri-storage-explorer.png)

7. Pour créer l’URI de SAS associé à ce disque dur virtuel, sélectionnez **Créer**. La boîte de dialogue s’actualise et affiche des détails sur cette opération.

8. Copiez l’URI et enregistrez-le dans un fichier texte à un emplacement sécurisé.

    ![Copie de l’URI.](media/vm/create-sas-uri-shared-access-signature-details.png)

    Cet URI de SAS généré concerne l’accès au niveau du conteneur. Pour le rendre spécifique, modifiez le fichier texte afin d’ajouter le nom du disque dur virtuel.

9. Insérez le nom de votre disque dur virtuel après la chaîne vhds dans l’URI de SAS (incluez une barre oblique). L’URI de SAS final doit ressembler à ceci :

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

1. Répétez ces étapes pour chaque disque dur virtuel dans les plans que vous allez publier.

### <a name="using-tool-2-azure-cli"></a>Utilisation de l’outil 2 : Azure CLI

1. Téléchargez et installez [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). Des versions sont disponibles pour Windows, macOS et diverses distributions de Linux.
2. Créez un fichier PowerShell (extension .ps1), copiez le code suivant, puis enregistrez-le localement.

    ```JSON
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <vhd-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. Modifiez le fichier pour utiliser les valeurs de paramètres suivantes. Indiquez des dates au format date/heure UTC, par exemple 2020-04-01T00:00:00Z.

    - account-name – nom de votre compte Stockage Azure.
    - account-key – votre clé de compte Stockage Azure.
    - vhd-name – nom de votre disque dur virtuel.
    - start-date – date de début de l’autorisation d’accès au disque dur virtuel. Indiquez une date correspondant à la veille de la date du jour.
    - expiry-date – date de fin de l’autorisation d’accès au disque dur virtuel. Indiquez une date postérieure d’au moins trois semaines à la date actuelle.

    Voici un exemple de valeurs de paramètre appropriées (au moment de la rédaction de cet article) :

    `az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name vhds -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’`

1. Enregistrez les modifications.
2. En appliquant l’une des méthodes suivantes, exécutez ce script avec des privilèges d’administrateur pour créer une chaîne de connexion SAS offrant un accès au niveau du conteneur :

    - Exécutez le script à partir de la console. Dans Windows, cliquez avec le bouton droit sur le script et sélectionnez **Exécuter en tant qu’administrateur**.
    - Exécutez le script à partir d’un éditeur de script PowerShell comme [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Cet écran illustre la création d’une chaîne de connexion SAS dans cet éditeur :

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

## <a name="next-step"></a>Étape suivante

- Consultez [Créer une offre de machine virtuelle Azure](azure-vm-create-offer.md).
