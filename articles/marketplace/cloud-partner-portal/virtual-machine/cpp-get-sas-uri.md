---
title: Obtenir l’URI de signature d’accès partagé pour votre image de machine virtuelle Microsoft Azure | Place de marché Azure
description: Explique comment obtenir l’URI de la signature d’accès partagé de vos images de machine virtuelle.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 2fdbc2a11bd963057b465a629757f2be51ae4061
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273849"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Obtenir l’URI de la signature d’accès partagé pour vos images de machine virtuelle

> [!IMPORTANT]
> Depuis le 13 avril 2020, nous avons commencé à déplacer la gestion de vos offres de machines virtuelles Azure vers l’Espace partenaires. Après la migration, vous créerez et gérerez vos offres dans l’Espace partenaires. Suivez les instructions fournies dans [Obtenir l’URI de la signature d’accès partagé pour vos images de machine virtuelle](https://aka.ms/GetSASURI) pour gérer vos offres migrées.

Pendant le processus de publication, vous devez fournir un identificateur de ressource uniforme (URI) pour chaque disque dur virtuel (VHD) associé à vos références SKU. Microsoft doit accéder à ces disques durs virtuels pendant le processus de certification. Cet article explique comment générer un URI de signature d’accès partagé (SAS) pour chaque disque dur virtuel. Vous allez entrer cet URI dans l’onglet **Références** du portail Microsoft Cloud Partner.

Lors de la génération des URI SAS pour vos disques durs virtuels, respectez les conditions suivantes :

- Seuls les disques durs virtuels non managés sont pris en charge.
- Les autorisations `List` et `Read`­ suffisent. Ne fournissez *pas* l’accès `Write` ou `Delete`.
- La durée d’accès (*date d’expiration*) doit être d’au minimum trois semaines à partir de la date de création de l’URI SAS.
- Pour vous protéger contre les variations de temps UTC, choisissez la veille de la date du jour comme date de début. Par exemple, si la date actuelle est le 6 octobre 2014, sélectionnez 5/10/2014.

## <a name="generate-the-sas-url"></a>Générer l’URL SAS

L’URL SAS peut être générée des deux manières courantes à l’aide des outils suivants :

- Explorateur Stockage Azure - Outil graphique disponible pour Windows, macOS et Linux
- Microsoft Azure CLI - Outil recommandé pour les systèmes d’exploitation autres que Windows et les environnements d’intégration automatisée ou continue

### <a name="azure-cli"></a>Azure CLI

Pour générer un URI SAS avec Azure CLI, procédez comme suit.

1. Téléchargez et installez [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). Des versions sont disponibles pour Windows, macOS et diverses distributions de Linux.
2. Créez un fichier PowerShell (extension `.ps1`), copiez le code suivant, puis enregistrez-le en local.

   ```azurecli-interactive
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
   ```

3. Modifiez le fichier pour fournir les paramètres suivants.  Les dates doivent être fournies au format d’horodatage UTC, par exemple `2016-10-25T00:00:00Z`.
   - `<account-name>` : nom de votre compte de stockage Azure
   - `<account-key>` : clé de votre compte de stockage Azure
   - `<vhd-name>` : nom de votre disque dur virtuel
   - `<start-date>` : date de début de l’autorisation d’accès au disque dur virtuel. Indiquez une date correspondant à la veille de la date du jour.
   - `<expiry-date>` : date de fin de l’autorisation d’accès au disque dur virtuel.  Indiquez une date correspondant au moins à trois semaines après la date du jour.

   L’exemple suivant montre les valeurs de paramètre appropriées (au moment de la rédaction de cet article).

   ```azurecli-interactive
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
   ```

4. Enregistrez les modifications apportées à ce script PowerShell.
5. Exécutez ce script à l’aide de privilèges d’administrateur, pour générer une *chaîne de connexion SAS* offrant un accès au niveau du conteneur.  Deux approches sont possibles :
   - Exécutez le script à partir de la console.  Par exemple, dans Windows, cliquez avec le bouton droit de la souris sur le script et sélectionnez **Exécuter en tant qu’administrateur**.
   - Exécutez le script à partir d’un éditeur de script PowerShell, comme [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise), à l’aide de privilèges d’administrateur.
     Le code suivant montre une chaîne de connexion SAS générée à l’aide de cet éditeur.

     ![Génération d’URI SAS dans PowerShell ISE](./media/publishvm_032.png)

6. Copiez la chaîne de connexion SAS obtenue et enregistrez-la dans un fichier texte à un emplacement sécurisé.  Vous allez ajouter à cette chaîne les informations d’emplacement du disque dur virtuel associé pour créer l’URI SAS final.
7. Dans le portail Microsoft Azure, accédez au stockage blob qui contient le disque dur virtuel associé à l’URI généré à l’instant.
8. Copiez la valeur du **point de terminaison de service Blob** dans l’URL, comme illustré ci-dessous.

    ![Point de terminaison de service blob dans le portail Azure](./media/publishvm_033.png)

9. Modifiez le fichier texte avec la chaîne de connexion SAS obtenue à l’étape 6.  Vous allez créer l’URI SAS complète avec le format suivant :

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Par exemple, si le nom du disque dur virtuel est `TestRGVM2.vhd`, l’URI SAS obtenue est :

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Répétez ces étapes pour chaque disque dur virtuel dans les références SKU que vous prévoyez de publier.

### <a name="microsoft-storage-explorer"></a>Explorateur de stockage Microsoft

Pour générer une URL SAS avec l’Explorateur Stockage Microsoft Azure, procédez comme suit.

1. Télécharger et installer l’[Explorateur de Stockage Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/).
2. Ouvrez l’explorateur et, dans la barre de menus de gauche, cliquez sur l’icône **Ajouter un compte**.  La boîte de dialogue **Connexion au stockage Azure** s’affiche.
3. Sélectionnez **Ajouter un compte Azure**, puis cliquez sur **Connexion**.  Continuez les étapes requises pour vous connecter à votre compte Azure.
4. Dans le volet gauche de l’**Explorateur**, accédez à vos **comptes de stockage** et développez ce nœud.
5. Cliquez avec le bouton droit de la souris sur votre disque dur virtuel, puis sélectionnez **Obtenir la signature d’accès partagé** dans le menu contextuel.

    ![Obtenir l’élément SAS dans l’Explorateur Azure](./media/publishvm_034.png)

6. La boîte de dialogue **Signature d’accès partagé** s’affiche. Entrez des valeurs dans les champs suivants :
   - **Heure de début** : date de début de l’autorisation d’accès au disque dur virtuel. Indiquez une date correspondant à la veille de la date du jour.
   - **Heure d’expiration** : date de fin de l’autorisation d’accès au disque dur virtuel.  Indiquez une date correspondant au moins à trois semaines après la date du jour.
   - **Autorisations** : sélectionnez les autorisations `Read` et `List`.

     ![Boîte de dialogue SAS dans l’Explorateur Azure](./media/publishvm_035.png)

7. Cliquez sur **Créer** pour créer l’URI SAS associé à ce disque dur virtuel.  La boîte de dialogue affiche maintenant les détails de cette opération.
8. Copiez la valeur de l’**URL** et enregistrez-la dans un fichier texte dans un emplacement sécurisé.

    ![Création d’URI SAS dans l’Explorateur Azure](./media/publishvm_036.png)

    Cette URL SAS générée concerne l’accès au niveau du conteneur.  Pour la rendre spécifique, le nom du disque dur virtuel associé doit lui être ajouté.

9. Modifiez le fichier texte. Insérez le nom de votre disque dur virtuel après la chaîne `vhds` dans l’URL SAS (inclure une barre oblique).  L’URI SAS finale doit être au format :

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Par exemple, si le nom du disque dur virtuel est `TestRGVM2.vhd`, l’URI SAS obtenue est :

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Répétez ces étapes pour chaque disque dur virtuel dans les références SKU que vous prévoyez de publier.

## <a name="verify-the-sas-uri"></a>Vérifier l’URI SAS

Examinez et vérifiez chaque URI SAS généré à l’aide de la liste de vérification suivante.  Vérifiez que :

- L’URI est au format :       `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
- L’URI contient le nom du fichier image de votre disque dur virtuel, notamment l’extension « .vhd ».
- Vers le milieu de l’URI apparaît `sp=rl`. Cette chaîne indique que les accès `Read` et `List` sont spécifiés.
- Après ce point, `sr=c` apparaît également. Cette chaîne indique que l’accès au niveau du conteneur est spécifié.
- Copiez et collez l’URI dans un navigateur pour commencer à télécharger l’objet blob associé.  (Vous pouvez annuler l’opération avant la fin du téléchargement.)

## <a name="next-steps"></a>Étapes suivantes

Si vous rencontrez des problèmes pour générer un URI SAS, consultez [Problèmes fréquents d’URL SAS et correctifs](./cpp-common-sas-url-issues.md).  Sinon, enregistrez le(s) URI SAS à un emplacement sécurisé en vue d’une utilisation ultérieure. Cette opération est nécessaire pour [publier votre offre de machine virtuelle](./cpp-publish-offer.md) dans le portail Microsoft Cloud Partner.
