---
title: Utiliser Data Lake Store avec Hadoop dans Azure HDInsight
description: Apprenez à interroger des données depuis Azure Data Lake Store et à stocker les résultats de votre analyse.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 69a2e189fc425369e357fd52685c2f48609e947b
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53386925"
---
# <a name="use-data-lake-store-with-azure-hdinsight-clusters"></a>Utiliser Data Lake Store avec des clusters Azure HDInsight

Pour analyser des données dans un cluster HDInsight, vous pouvez stocker les données dans le [stockage Azure](../storage/common/storage-introduction.md), [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) ou les deux. Les deux options de stockage vous permettent de supprimer des clusters HDInsight servant aux calculs, sans perte de données utilisateur.

Dans cet article, vous découvrez le fonctionnement de Data Lake Store avec des clusters HDInsight. Pour savoir comment le stockage Azure fonctionne avec les clusters HDInsight, consultez [Use Azure Storage with Azure HDInsight clusters](hdinsight-hadoop-use-blob-storage.md) (Utiliser le stockage Azure. avec des clusters HDInsight). Pour plus d’informations sur la création d’un cluster HDInsight, consultez [Créer des clusters Apache Hadoop dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> Data Lake Store est toujours accessible par le biais d’un canal sécurisé, donc il y a aucun nom de schéma de système de fichiers `adls`. Vous utilisez toujours `adl`.


## <a name="availability-for-hdinsight-clusters"></a>Disponibilité pour les clusters HDInsight

Apache Hadoop prend en charge une notion de système de fichiers par défaut. Le système de fichiers par défaut implique un schéma et une autorité par défaut. Il peut également être utilisé pour résoudre les chemins d'accès relatifs. Pendant le processus de création du cluster HDInsight, vous pouvez spécifier un conteneur d’objets blob dans le stockage Azure comme système de fichiers par défaut, ou, avec HDInsight 3.5 et des versions plus récentes, vous pouvez sélectionner le stockage Azure ou Azure Data Lake Store en tant que système de fichiers par défaut avec quelques exceptions. 

Les clusters HDInsight peuvent utiliser Data Lake Store de deux manières :

* Comme stockage par défaut
* Comme stockage supplémentaire, avec Azure Storage Blob comme stockage par défaut.

À partir de maintenant, seules certain(e)s versions/types de cluster HDInsight prennent en charge l’utilisation de stockage de Data Lake Store comme stockage par défaut et comptes de stockage supplémentaires :

| Type de cluster HDInsight | Data Lake Store comme stockage par défaut | Data Lake Store comme stockage supplémentaire| Notes |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight version 3.6 | OUI | OUI | With the exception of HBase|
| HDInsight version 3.5 | OUI | OUI | With the exception of HBase|
| HDInsight version 3.4 | Non | OUI | |
| HDInsight version 3.3 | Non | Non | |
| HDInsight version 3.2 | Non | OUI | |
| Storm | | |Vous pouvez utiliser Data Lake Store pour écrire des données à partir d’une topologie Storm. Vous pouvez également vous en servir pour stocker des données de référence qui peuvent ensuite être lues par une topologie Storm.|

> [!WARNING]  
> HDInsight HBase n’est pas pris en charge avec Azure Data Lake Storage Gen 1

L’utilisation de Data Lake Store en tant que compte de stockage supplémentaire n’affecte pas les performances ni la capacité de lecture ou d’écriture sur le stockage Azure à partir du cluster.
## <a name="use-data-lake-store-as-default-storage"></a>Utiliser Data Lake Store comme stockage par défaut

Lorsque HDInsight est déployé avec Data Lake Store en tant que stockage par défaut, les fichiers associés au cluster sont stockés dans Data Lake Store à l’emplacement suivant :

    adl://mydatalakestore/<cluster_root_path>/

où `<cluster_root_path>` est le nom d’un dossier que vous créez dans Data Lake Store. En spécifiant un chemin d’accès racine pour chaque cluster, vous pouvez utiliser le même compte Data Lake Store pour plusieurs clusters. Par conséquent, vous pouvez avoir une configuration dans laquelle :

* Cluster1 peut utiliser le chemin d’accès `adl://mydatalakestore/cluster1storage`
* Cluster2 peut utiliser le chemin d’accès `adl://mydatalakestore/cluster2storage`

Notez que les deux clusters utilisent le même compte Data Lake Store **mydatalakestore**. Chaque cluster a accès à son propre système de fichiers racine dans Data Lake Store. L’expérience de déploiement avec le portail Azure en particulier vous invite à utiliser un nom de dossier comme **/clusters/\<nom de cluster>** pour le chemin d’accès racine.

Pour être en mesure d’utiliser Data Lake Store en tant que stockage par défaut, vous devez accorder l’accès du principal de service aux chemins d’accès suivants :

- Racine du compte Data Lake Store.  Par exemple : adl://mydatalakestore/.
- Dossier pour tous les dossiers de cluster.  Par exemple : adl://mydatalakestore/clusters.
- Dossier pour le cluster.  Par exemple : adl://mydatalakestore/clusters/cluster1storage.

Pour plus d’informations sur la création du principal de service et l’octroi de l’accès, consultez [Configure Data Lake store access](#configure-data-lake-store-access) (Configurer l’accès à Data Lake Store).


## <a name="use-data-lake-store-as-additional-storage"></a>Utiliser Data Lake Store comme stockage supplémentaire

Vous pouvez également utiliser Data Lake Store en tant que stockage supplémentaire pour le cluster. Dans ce cas, le stockage de cluster par défaut peut être un compte Azure Storage Blob ou Data Lake Store. Si vous exécutez des tâches HDInsight sur des données stockées dans Data Lake Store en tant que stockage supplémentaire, vous devez utiliser le chemin d’accès complet aux fichiers. Par exemple :

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Notez qu’il n’y a encore aucun **cluster_root_path** dans l’URL. La raison est que Data Lake Store n’est pas un stockage par défaut dans ce cas et qu’il vous suffit donc de fournir le chemin d’accès aux fichiers.

Pour être en mesure d’utiliser Data Lake Store comme stockage supplémentaire, il suffit d’accorder l’accès du principal de service aux chemins d’accès où sont stockés vos fichiers.  Par exemple :

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Pour plus d’informations sur la création du principal de service et l’octroi de l’accès, consultez [Configure Data Lake store access](#configure-data-lake-store-access) (Configurer l’accès à Data Lake Store).


## <a name="use-more-than-one-data-lake-store-accounts"></a>Utiliser plusieurs comptes Data Lake Store

L’ajout d’un compte Data Lake Store comme compte supplémentaire et de plusieurs comptes Data Lake Store s’effectue en donnant au cluster HDInsight l’autorisation d’accéder aux données de plusieurs comptes Data Lake Store. Consultez [Configurer l’accès à Data Lake Store](#configure-data-lake-store-access).

## <a name="configure-data-lake-store-access"></a>Consultez Configurer l’accès à Data Lake Store

Pour configurer l’accès à Data Lake store à partir de votre cluster HDInsight, vous devez disposer d’un principal de service Azure Active directory (Azure AD). Vous pouvez créer un principal de service uniquement si vous être administrateur Azure AD. Le principal de service doit être créé avec un certificat. Pour plus d’informations, consultez [Démarrage rapide : Configurer des clusters dans HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md), et [Create service principal with self-signed-certificate](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate) (Créer un principal du service avec un certificat auto-signé).

> [!NOTE]  
> Si vous vous apprêtez à utiliser Azure Data Lake Store en tant que stockage supplémentaire pour le cluster HDInsight, nous vous recommandons vivement de procéder ainsi lorsque vous créez le cluster comme décrit dans cet article. L’ajout d’Azure Data Lake Store en tant que stockage supplémentaire à un cluster HDInsight existant n’est pas pris en charge.
>

## <a name="access-files-from-the-cluster"></a>Accès aux fichiers à partir du cluster

Il existe plusieurs méthodes pour accéder aux fichiers dans Data Lake Store à partir d’un cluster HDInsight.

* **Utilisation du nom complet**. Avec cette approche, vous fournissez le chemin d’accès complet au fichier auquel vous souhaitez accéder.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **Utilisation du format de chemin d’accès raccourci**. Avec cette approche, vous remplacez le chemin d’accès à la racine du cluster par adl:///. Donc dans l’exemple ci-dessus, vous pouvez remplacer `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` par `adl:///`.

        adl:///<file path>

* **Utilisation du chemin d’accès relatif**. Avec cette approche, vous fournissez uniquement le chemin d’accès relatif au fichier auquel vous souhaitez accéder. Par exemple, si le chemin d’accès complet au fichier est :

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    Vous pouvez accéder au même exemple de fichier .log à l’aide de ce chemin d’accès relatif à la place.

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-store"></a>Créer des clusters HDInsight ayant accès à Data Lake Store

Suivez les liens ci-dessous pour obtenir des instructions détaillées sur la manière de créer des clusters HDInsight avec accès au Data Lake Store.

* [Utilisation du portail](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Utilisation de PowerShell (avec Data Lake Store en tant que stockage par défaut)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Utilisation de PowerShell (avec Data Lake Store en tant que stockage supplémentaire)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Utilisation de modèles Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-store-access"></a>Actualiser le certificat HDInsight pour l’accès à Data Lake Store

L’exemple de code PowerShell suivant lit un fichier de certificat local et met à jour votre cluster HDInsight avec le nouveau certificat pour accéder à Azure Data Lake Store. Fournissez votre propre nom de cluster HDInsight, nom de groupe de ressources, ID d’abonnement, ID d’application et chemin local du certificat. Tapez le mot de passe quand vous y êtes invité.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $subscriptionId

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 30 seconds for the permissions to get propagated"
    Start-Sleep -s 30
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à utiliser Azure Data Lake Store compatible HDFS avec HDInsight. Ceci vous permet de créer des solutions à long terme et évolutives d’acquisition et d’archivage de données et d’utiliser HDInsight pour déverrouiller les informations des données structurées et non structurées stockées.

Pour plus d'informations, consultez les pages suivantes :

* [Prise en main d’Azure HDInsight][hdinsight-get-started]
* [Démarrage rapide : Configurer des clusters dans HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Création d’un cluster HDInsight pour utiliser Data Lake Store à l’aide d’Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Téléchargement de données vers HDInsight][hdinsight-upload-data]
* [Utilisation d’Apache Hive avec HDInsight][hdinsight-use-hive]
* [Utilisation d’Apache Pig avec HDInsight][hdinsight-use-pig]
* [Utilisation des signatures d’accès partagé Azure Storage pour restreindre l’accès aux données avec HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: https://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
