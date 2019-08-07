---
title: Héberger un site web statique dans Stockage Azure
description: Découvrez comment servir du contenu statique (fichiers HTML, CSS, JavaScript et images) directement à partir d’un conteneur dans un compte GPv2 Stockage Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: e53f8dd832b7f19692b9095e913330848e5b0330
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385764"
---
# <a name="host-a-static-website-in-azure-storage"></a>Héberger un site web statique dans Stockage Azure

Vous pouvez servir du contenu statique (fichiers HTML, CSS, JavaScript et images) directement à partir d’un conteneur dans un compte GPv2 Stockage Azure. Pour en savoir plus, voir [Hébergement de site web statique dans Stockage Azure](storage-blob-static-website.md).

Cet article explique comment activer l’hébergement de site web statique avec le Portail Azure, Azure CLI ou PowerShell.

<a id="portal" />

## <a name="use-the-azure-portal"></a>Utilisation du portail Azure

Pour un didacticiel pas à pas, voir [Didacticiel : Héberger un site web statique sur le Stockage Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

Après avoir activé l’hébergement de site web statique, vous pouvez afficher les pages de votre dans un navigateur en utilisant l’URL publique du site web.

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>Trouver l’URL du site web à l’aide du portail Azure

Dans le volet qui s’affiche en regard de la page de Vue d’ensemble du compte de votre compte de stockage, sélectionnez **Site web statique**. L’URL de votre site s’affiche dans le champ **Point de terminaison principal**.

![Métrique des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="use-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Microsoft Azure

Vous pouvez activer l’hébergement de site web statique à l’aide de l’[interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) (Azure CLI).

1. Commencez par ouvrir [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) ou, si vous avez [installé](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI localement, ouvrez une application console de commandes telle que Windows PowerShell.

2. Si votre identité est associée à plusieurs abonnements, définissez comme abonnement actif l’abonnement du compte de stockage qui doit héberger votre site web statique.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Remplacez la valeur d’espace réservé `<subscription-id>` par l’ID de votre abonnement.

3. Activez l’hébergement de site web statique.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Remplacez la valeur d’espace réservé `<storage-account-name>` par le nom de votre compte de stockage.

   * Remplacez l’espace réservé `<error-document-name>` par le nom du document d’erreur qui s’affiche quand un navigateur demande sur votre site une page inexistante.

   * Remplacez la valeur d’espace réservé `<index-document-name>` par le nom du document d’index. Ce document est généralement « index.html ».

4. Chargez des objets dans le conteneur *$web* à partir d’un répertoire source.

   > [!NOTE]
   > Si vous utilisez Azure Cloud Shell, veillez à ajouter un `\`caractère d’échappement lorsque vous faites référence au`$web` conteneur (par exemple, `\$web`). Si vous utilisez une installation locale d’Azure CLI, vous ne devez pas utiliser le caractère d’échappement.

   Cet exemple part du principe que vous exécutez des commandes à partir d’une session Azure Cloud Shell.

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * Remplacez la valeur d’espace réservé `<storage-account-name>` par le nom de votre compte de stockage.

   * Remplacez la valeur d’espace réservé `<source-path>` par le chemin d’accès de l’emplacement où se trouvent les fichiers que vous voulez charger.

   > [!NOTE]
   > Si vous utilisez l’emplacement d’installation d’Azure CLI, vous pouvez utiliser le chemin d’accès de tout emplacement sur votre ordinateur local. Par exemple : `C:\myFolder`.
   >
   > Si vous utilisez Azure Cloud Shell, vous devez faire référence à un partage de fichiers visible pour Cloud Shell. Cet emplacement peut être le partage de fichiers du partage cloud ou un partage de fichiers existant que vous montez à partir du Cloud Shell. Pour savoir comment procéder, voir [Conserver des fichiers dans Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>Trouver l’URL du site web à l’aide d’Azure CLI

Vous pouvez afficher le contenu dans un navigateur en utilisant l’URL publique du site web.

Recherchez l’URL à l’aide de la commande suivante :

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Remplacez la valeur d’espace réservé `<storage-account-name>` par le nom de votre compte de stockage.

* Remplacez la valeur d’espace réservé `<resource-group-name>` par le nom de votre groupe de ressources.

<a id="powershell" />

## <a name="use-powershell"></a>Utiliser PowerShell

Vous pouvez activer l’hébergement de site web statique à l’aide du module Azure PowerShell.

1. Ouvrez une fenêtre Commande Windows PowerShell.

2. Vérifiez que vous disposez du module Azure PowerShell Az version 0.7 ou ultérieure.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps).

3. Connectez-vous à votre abonnement Azure avec la commande `Connect-AzAccount` et suivez les instructions à l’écran.

   ```powershell
   Connect-AzAccount
   ```

4. Si votre identité est associée à plusieurs abonnements, définissez comme abonnement actif l’abonnement du compte de stockage qui doit héberger votre site web statique.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Remplacez la valeur d’espace réservé `<subscription-id>` par l’ID de votre abonnement.

5. Obtenez le contexte du compte de stockage qui définit le compte de stockage à utiliser.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Remplacez la valeur d’espace réservé `<resource-group-name>` par le nom de votre groupe de ressources.

   * Remplacez la valeur d’espace réservé `<storage-account-name>` par le nom de votre compte de stockage.

6. Activez l’hébergement de site web statique.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Remplacez l’espace réservé `<error-document-name>` par le nom du document d’erreur qui s’affiche quand un navigateur demande sur votre site une page inexistante.

   * Remplacez la valeur d’espace réservé `<index-document-name>` par le nom du document d’index. Ce document est généralement « index.html ».

7. Chargez des objets dans le conteneur *$web* à partir d’un répertoire source.

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * Remplacez la valeur d’espace réservé `<path-to-file>` par le chemin d’accès complet du fichier que vous souhaitez charger (par exemple, `C:\temp\index.html`).

   * Remplacez la valeur d’espace réservé `<blob-name>` par le nom que vous souhaitez attribuer à l’objet blob obtenu (par exemple, `index.html`).

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>Trouver l’URL du site web à l’aide de PowerShell

Vous pouvez afficher le contenu dans un navigateur en utilisant l’URL publique du site web.

Recherchez l’URL à l’aide de la commande suivante :

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Remplacez la valeur d’espace réservé `<resource-group-name>` par le nom de votre groupe de ressources.

* Remplacez la valeur d’espace réservé `<storage-account-name>` par le nom de votre compte de stockage.

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Activer les métriques sur des pages de site web statiques

Une fois les métriques activées, les statistiques de trafic relatives aux fichiers du conteneur **$web** sont signalées dans le tableau de bord des métriques.

1. Cliquez sur **Paramètres** > **Surveillance** > **Métriques**.

   Les données de métriques sont générées par raccordement aux différentes API de métriques. Le portail affiche uniquement les membres d’API utilisés dans un délai d’exécution donné afin de se focaliser exclusivement sur les membres qui renvoient des données. Pour garantir votre capacité à sélectionner le membre d’API nécessaire, la première étape consiste à étendre le délai d’exécution.

2. Cliquez sur le bouton de délai d’exécution, sélectionnez **Dernières 24 heures**, puis cliquez sur **Appliquer**.

   ![Intervalle de temps des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Sélectionnez **Blob** dans la liste déroulante *Espace de noms*.

   ![Espace de noms des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Ensuite, sélectionnez la métrique **Sortie**.

   ![Métrique des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Dans le sélecteur **Agrégation**, sélectionnez *Somme*.

   ![Agrégation des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Cliquez sur le bouton **Ajouter un filtre**, puis choisissez **Nom API** dans le sélecteur *Propriété*.

   ![Nom d’API des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Cochez la case en regard de **GetWebContent** dans le sélecteur *Valeurs* pour remplir le rapport des métriques.

   ![Valeur GetWebContent des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>Étapes suivantes

* [Hébergement de sites web statiques dans le service Stockage Azure](storage-blob-static-website.md)
* [Utiliser Azure CDN pour accéder aux objets Blob avec des domaines personnalisés via HTTPs](storage-https-custom-domain-cdn.md)
* [Configurer un nom de domaine personnalisé pour le point de terminaison de votre objet blob ou web](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Générer votre première application web sans serveur](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Tutoriel : Héberger votre domaine dans Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
