---
title: URI de la signature d’accès partagé pour les images de machine virtuelle – Place de marché Azure
description: Générez un URI de signature d’accès partagé (SAP) pour vos disques durs virtuels (VHD) dans Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: anbene
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 569d9ce04422230c1d6b9ebb0957f01c320c5ba0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536145"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Obtenir l’URI de la signature d’accès partagé pour vos images de machine virtuelle

Cet article explique comment générer un URI (Uniform Resource Identifier) de signature d’accès partagé (SAS) pour chaque disque dur virtuel (VHD).

Pendant le processus de publication, vous devez fournir un URI pour chaque disque dur virtuel associé à vos plans (anciennement appelés niveaux tarifaires). Microsoft doit accéder à ces disques durs virtuels pendant le processus de certification. Vous entrerez cet URI sous l’onglet **Plans** de l’Espace partenaires.

Lors de la génération d’URI SAS pour vos disques durs virtuels, tenez compte des conditions suivantes :

* Seuls les disques durs virtuels non managés sont pris en charge.
* Seules les autorisations `List` et `Read` sont requises Ne fournissez pas d’accès en écriture ou en suppression
* La durée d’accès (date d’expiration) doit être d’au moins trois semaines à partir de la date de création de l’URI SAS
* Pour vous protéger contre les changements de temps UTC, choisissez la veille de la date actuelle comme date de début. Par exemple, si la date actuelle est le 6 octobre 2019, sélectionnez 5/10/2019.

## <a name="generate-the-sas-address"></a>Générer l’adresse de SAS

Deux outils sont couramment utilisés pour créer une adresse (URL) de SAS :

* **Explorateur Stockage Microsoft Azure** : outil graphique disponible pour Windows, macOS et Linux
* **Microsoft Azure CLI** : outil recommandé pour les systèmes d’exploitation autres que Windows et les environnements d’intégration automatisée ou continue

### <a name="use-microsoft-storage-explorer"></a>Utiliser l’Explorateur Stockage Microsoft Azure

1. Téléchargez et installez [Explorateur Stockage Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/).
2. Ouvrez l’explorateur et, dans le menu de gauche, sélectionnez **Ajouter un compte**. La boîte de dialogue **Connexion au stockage Azure** s’affiche.
3. Sélectionnez **Ajouter un compte Azure**, puis **Connexion**. Effectuez les étapes requises pour vous connecter à votre compte Azure.
4. Dans le volet gauche de l’**Explorateur**, accédez à vos **Comptes de stockage** et développez ce nœud.
5. Cliquez avec le bouton droit sur votre disque dur virtuel, puis sélectionnez **Obtenir la signature d’accès partagé**.
6. La boîte de dialogue **Signature d’accès partagé** s’affiche. Renseignez les champs suivants :

    * **Heure de début** : date de début de l’autorisation d’accès au disque dur virtuel. Indiquez une date correspondant à la veille de la date du jour.
    * **Heure d’expiration** : date de fin de l’autorisation d’accès au disque dur virtuel. Indiquez une date postérieure d’au moins trois semaines à la date actuelle.
    * **Autorisations** : sélectionnez les autorisations Lecture et Liste.
    * **Niveau de conteneur** : cochez la case **Générer un URI de signature d’accès partagé au niveau du conteneur**.

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="Illustre la boîte de dialogue Signature d’accès partagé":::

7. Pour créer l’URI de SAS associé à ce disque dur virtuel, sélectionnez **Créer**. La boîte de dialogue s’actualise et affiche des détails sur cette opération.
8. Copiez l’**URI** et enregistrez-le dans un fichier texte à un emplacement sécurisé.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="Illustre la fenêtre de détails de la signature d’accès partagé":::

    Cet URI de SAS généré concerne l’accès au niveau du conteneur. Pour le rendre spécifique, modifiez le fichier texte afin d’ajouter le nom du disque dur virtuel (étape suivante).

9. Insérez le nom de votre disque dur virtuel après la chaîne vhds dans l’URI de SAS (incluez une barre oblique). L’URI de SAS final doit ressembler à ceci :

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>` Par exemple, si le nom du disque dur virtuel est `TestRGVM2.vhd`, l’URI de SAS obtenue est :

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. Répétez ces étapes pour chaque disque dur virtuel dans les plans que vous allez publier.

### <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure

1. Téléchargez et installez [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). Des versions sont disponibles pour Windows, macOS et diverses distributions de Linux.
2. Créez un fichier PowerShell (extension .ps1), copiez le code suivant, puis enregistrez-le localement.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. Modifiez le fichier pour utiliser les valeurs de paramètres suivantes. Indiquez des dates au format date/heure UTC, par exemple `2020-04-01T00:00:00Z`.

    * `<account-name>` : nom de votre compte de stockage Azure
    * `<account-key>` : clé de votre compte de stockage Azure
    * `<vhd-name>` : nom de votre disque dur virtuel
    * `<start-date>` : date de début de l’autorisation d’accès au disque dur virtuel. Indiquez une date correspondant à la veille de la date du jour.
    * `<expiry-date>` : date de fin de l’autorisation d’accès au disque dur virtuel. Indiquez une date postérieure d’au moins trois semaines à la date actuelle.

    Cet exemple montre des valeurs de paramètre appropriées (au moment de la rédaction de cet article) :

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. Enregistrez les modifications.
5. En appliquant l’une des méthodes suivantes, exécutez ce script avec des privilèges d’administrateur pour créer une **chaîne de connexion SAS** offrant un accès au niveau du conteneur :

    * Exécutez le script à partir de la console. Dans Windows, cliquez avec le bouton droit sur le script et sélectionnez **Exécuter en tant qu’administrateur**.
    * Exécutez le script à partir d’un éditeur de script PowerShell comme [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Cet écran illustre la création d’une chaîne de connexion SAS dans cet éditeur :

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Illustre la création d’une chaîne de connexion SAS avec Windows PowerShell ISE":::

6. Copiez la chaîne de connexion SAS et enregistrez-la dans un fichier texte à un emplacement sécurisé. Modifiez cette chaîne pour ajouter les informations d’emplacement du disque dur virtuel associé afin de créer l’URI de SAS final.
7. Dans le portail Azure, accédez au stockage d’objets blob qui comprend le disque dur virtuel associé au nouvel URI.
8. Copiez l’URL du **point de terminaison de service BLOB**, comme indiqué dans la capture d’écran suivante.

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Illustre le point de terminaison de service BLOB":::

9. Modifiez le fichier texte avec la chaîne de connexion SAS obtenue à l’étape 6. Créez l’URI de SAS complet en utilisant le format suivant :

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    Par exemple, si le nom du disque dur virtuel est `TestRGVM2.vhd`, l’URI de SAS sera :

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Répétez ces étapes pour chaque disque dur virtuel dans les plans que vous allez publier.

## <a name="verify-the-sas-uri"></a>Vérifier l’URI SAS

Examinez chaque URI de SAS créé en vous aidant de la liste de contrôle suivante pour vérifier que :

* L’URI se présente comme suit : `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* L’URI comprend le nom du fichier image de votre disque dur virtuel, notamment l’extension « .vhd »
* `sp=rl` apparaît près du milieu de votre URI. Cette chaîne indique que les accès `Read` et `List` sont spécifiés
* Quand `sr=c` s’affiche, cela signifie que l’accès au niveau du conteneur est spécifié.
* Copiez et collez l’URI dans un navigateur afin de tester-télécharger l’objet blob (vous pouvez annuler l’opération avant la fin du téléchargement).

## <a name="next-step"></a>Étape suivante

Si vous rencontrez des difficultés lors de la création d’un URI de SAS, consultez [Problèmes courants liés aux URL de SAS](common-sas-uri-issues.md). Sinon, enregistrez le(s) URI SAS à un emplacement sécurisé en vue d’une utilisation ultérieure. Vous en aurez besoin pour publier votre offre de machine virtuelle dans l’Espace partenaires.

* [Créer une offre de machine virtuelle Azure](azure-vm-create-offer.md)
