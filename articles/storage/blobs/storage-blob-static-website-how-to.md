---
title: Héberger un site Web statique dans le stockage Azure
description: Apprenez à du contenu statique (HTML, CSS, JavaScript et les fichiers image) directement à partir d’un conteneur dans un compte GPv2 de stockage Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: 7a1aef14a2a32266c893933482527c361f17d7fb
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428559"
---
# <a name="host-a-static-website-in-azure-storage"></a>Héberger un site Web statique dans le stockage Azure

Vous pouvez distribuer du contenu statique (HTML, CSS, JavaScript et les fichiers image) directement à partir d’un conteneur dans un compte GPv2 de stockage Azure. Pour plus d’informations, consultez [hébergement de site Web statique dans le stockage Azure](storage-blob-static-website.md).

Cet article vous montre comment activer l’hébergement de site Web statique à l’aide du portail Azure, l’interface CLI ou PowerShell.

<a id="portal" />

## <a name="use-the-azure-portal"></a>Utilisation du portail Azure

Pour obtenir un didacticiel pas à pas, consultez [didacticiel : Héberger un site web statique sur le Stockage Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

Une fois que vous activez l’hébergement de sites Web statique, vous pouvez afficher les pages de votre site à partir d’un navigateur à l’aide de l’URL publique du site Web.

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>Rechercher l’URL du site Web à l’aide du portail Azure

Dans le volet qui s’affiche en regard de la page de vue d’ensemble du compte de votre compte de stockage, sélectionnez **site Web statique**. L’URL de votre site s’affiche dans le **point de terminaison principal** champ.

![Métrique des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="use-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Microsoft Azure

Vous pouvez activer l’hébergement de site Web statique à l’aide de la [les Interface de ligne de commande (CLI) Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Tout d’abord, ouvrez le [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), ou si vous avez [installé](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) l’interface CLI localement, ouvrez une application de console de commande, telles que Windows PowerShell.

2. À partir de la fenêtre de commande que vous avez ouvert, installez l’extension de version préliminaire de stockage.

   ```azurecli-interactive
   az extension add --name storage-preview
   ```

3. Si votre identité est associée à plusieurs abonnements, définissez votre actif abonnement à du compte de stockage qui hébergera votre site Web statique.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Remplacez le `<subscription-id>` valeur d’espace réservé par l’ID de votre abonnement.

4. Activer l’hébergement de site Web statique.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Remplacez la valeur d’espace réservé `<storage-account-name>` par le nom de votre compte de stockage.

   * Remplacez le `<error-document-name>` espace réservé par le nom du document d’erreur qui s’affiche aux utilisateurs lorsqu’un navigateur demande une page de votre site n’existe pas.

   * Remplacez le `<index-document-name>` espace réservé par le nom du document d’index. Ce document est généralement « index.html ».

5. Chargez des objets dans le conteneur *$web* à partir d’un répertoire source.

   > [!NOTE]
   > Si vous utilisez Azure Cloud Shell, veillez à ajouter un `\` caractère d’échappement lorsqu’elle fait référence à la `$web` conteneur (par exemple : `\$web`). Si vous utilisez une installation locale de l’interface CLI, vous ne devez utiliser le caractère d’échappement.

   Cet exemple suppose que vous exécutez des commandes à partir de la session Azure Cloud Shell.

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * Remplacez la valeur d’espace réservé `<storage-account-name>` par le nom de votre compte de stockage.

   * Remplacez le `<source-path>` espace réservé avec un chemin d’accès à l’emplacement des fichiers que vous souhaitez télécharger.

   > [!NOTE]
   > Si vous utilisez un emplacement d’installation d’Azure CLI, vous pouvez utiliser le chemin d’accès pour n’importe quel emplacement sur votre ordinateur local (par exemple : `C:\myFolder`.
   >
   > Si vous utilisez Azure Cloud Shell, vous devrez faire référence à un partage de fichiers qui est visible pour le Cloud Shell. Cet emplacement peut être le partage de fichiers du Cloud partager lui-même ou un partage de fichiers existant vous monter à partir de l’interpréteur de commandes Cloud. Pour savoir comment procéder, consultez [conserver des fichiers dans Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>Rechercher l’URL du site Web à l’aide de l’interface CLI Azure

Vous pouvez afficher le contenu à partir d’un navigateur à l’aide de l’URL publique du site Web.

Trouver l’URL à l’aide de la commande suivante :

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Remplacez la valeur d’espace réservé `<storage-account-name>` par le nom de votre compte de stockage.

* Remplacez le `<resource-group-name>` valeur d’espace réservé par le nom de votre groupe de ressources.

<a id="powershell" />

## <a name="use-powershell"></a>Utiliser PowerShell

Vous pouvez activer l’hébergement de site Web statique à l’aide du module Azure PowerShell.

1. Ouvrez une fenêtre de commande Windows PowerShell.

2. Vérifiez que vous disposez d’Azure PowerShell module Az 0,7 ou version ultérieure.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps).

3. Connectez-vous à votre abonnement Azure avec la commande `Connect-AzAccount` et suivez les instructions à l’écran.

   ```powershell
   Connect-AzAccount
   ```

4. Si votre identité est associée à plusieurs abonnements, définissez votre actif abonnement à du compte de stockage qui hébergera votre site Web statique.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Remplacez le `<subscription-id>` valeur d’espace réservé par l’ID de votre abonnement.

5. Obtenir le contexte du compte de stockage qui définit le compte de stockage que vous souhaitez utiliser.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Remplacez le `<resource-group-name>` valeur d’espace réservé par le nom de votre groupe de ressources.

   * Remplacez la valeur d’espace réservé `<storage-account-name>` par le nom de votre compte de stockage.

6. Activer l’hébergement de site Web statique.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Remplacez le `<error-document-name>` espace réservé par le nom du document d’erreur qui s’affiche aux utilisateurs lorsqu’un navigateur demande une page de votre site n’existe pas.

   * Remplacez le `<index-document-name>` espace réservé par le nom du document d’index. Ce document est généralement « index.html ».

7. Chargez des objets dans le conteneur *$web* à partir d’un répertoire source.

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * Remplacez le `<path-to-file>` valeur d’espace réservé avec le chemin d’accès complet au fichier que vous souhaitez charger (par exemple : `C:\temp\index.html`).

   * Remplacez le `<blob-name>` valeur d’espace réservé par le nom que vous souhaitez donner à l’objet blob qui en résulte (par exemple : `index.html`).

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>Rechercher l’URL du site Web à l’aide de PowerShell

Vous pouvez afficher le contenu à partir d’un navigateur à l’aide de l’URL publique du site Web.

Trouver l’URL à l’aide de la commande suivante :

```powershell
$context = Get-AzSubscription -SubscriptionId <subscription-d>
Set-AzContext $context
```

Remplacez le `<subscription-id>` valeur d’espace réservé par l’ID de votre abonnement.

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Activer les métriques sur les pages du site Web statique

Une fois que vous avez activé les métriques, les statistiques sur les fichiers de trafic le **$web** conteneur sont signalés dans le tableau de bord des métriques.

1. Cliquez sur **paramètres** > **surveillance** > **métriques**.

   Les données de métriques sont générées par raccordement aux différentes API de métriques. Le portail affiche uniquement les membres d’API utilisés dans un délai d’exécution donné afin de se focaliser exclusivement sur les membres qui renvoient des données. Afin de garantir que vous êtes en mesure de sélectionner le membre d’API nécessaires, la première étape consiste à développer l’intervalle de temps.

2. Cliquez sur le bouton de l’intervalle de temps et sélectionnez **dernières 24 heures** puis cliquez sur **appliquer**.

   ![Intervalle de temps des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Sélectionnez **Blob** à partir de la *Namespace* liste déroulante.

   ![Espace de noms des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Ensuite, sélectionnez la métrique **Sortie**.

   ![Métrique des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Dans le sélecteur **Agrégation**, sélectionnez *Somme*.

   ![Agrégation des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Cliquez sur le **ajouter un filtre** bouton et choisissez **nom de l’API** à partir de la *propriété* sélecteur.

   ![Nom d’API des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. la case à cocher à côté **GetWebContent** dans le *valeurs* sélecteur pour remplir le rapport des métriques.

   ![Valeur GetWebContent des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>Étapes suivantes

* [Hébergement de sites web statiques dans le service Stockage Azure](storage-blob-static-website.md)
* [Utiliser Azure CDN pour accéder aux objets BLOB avec des domaines personnalisés via HTTPS](storage-https-custom-domain-cdn.md)
* [Configurer un nom de domaine personnalisé pour le point de terminaison de votre objet blob ou web](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Générer votre première application web sans serveur](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Tutoriel : Héberger votre domaine dans Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
