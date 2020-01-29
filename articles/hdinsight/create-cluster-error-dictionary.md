---
title: Créer un dictionnaire d’erreurs de cluster
description: Découvrez comment créer un dictionnaire d’erreurs de cluster.
ms.reviewer: hrasheed
author: karkrish
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: troubleshooting
ms.date: 11/19/2019
ms.author: v-todmc
ms.openlocfilehash: ee9ed5374b12c3130d952770a4be6a759e37339a
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156860"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight : Erreurs lors de la création du cluster

Cet article décrit les solutions aux erreurs que vous pouvez rencontrer lors de la création de clusters.

> [!NOTE]
> Les trois premières erreurs décrites dans cet article sont des erreurs de validation. Ils peuvent se produire lorsqu’un produit Azure HDInsight utilise la classe **CsmDocument_2_0**.

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Code d’erreur : La validation a échoué pour le document CsmDocument_2_0

### <a name="error"></a>Error

« Impossible d’accéder à l’URI de l’emplacement de l’action de script URI : \<URL D’ACTION DE SCRIPT\> »

#### <a name="error-message"></a>Message d’erreur

« Le serveur distant a retourné une erreur : (404) introuvable. »

### <a name="cause"></a>Cause :

Le service HDInsight ne peut pas accéder à l’URL d’action de script que vous avez fournie dans le cadre de la requête de création de cluster. Le service reçoit le message d’erreur précédent lorsqu’il tente d’accéder à l’action de script.

### <a name="resolution"></a>Résolution

- Pour une URL HTTP ou HTTPs, vérifiez l’URL en tentant d’accéder à celle-ci à partir d’une fenêtre de navigateur incognito.
- Pour une URL WASB, assurez-vous que le script existe dans le compte de stockage que vous fournissez dans la requête. Assurez-vous également que la clé de stockage pour ce compte de stockage est correcte.
- Pour une URL ADLS, assurez-vous que le script existe dans le compte de stockage.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Code d’erreur : La validation a échoué pour le document CsmDocument_2_0

### <a name="error"></a>Error

« Impossible d’accéder à l’emplacement de l’action de script URI : \<SCRIPT_ACTION_URL\>"

#### <a name="error-message"></a>Message d’erreur

« L’URI de script donné \<SCRIPT_URI\> se trouve dans ADLS, mais ce cluster n’a pas de principal de stockage Data Lake »

### <a name="cause"></a>Cause :

Le service HDInsight ne peut pas accéder à l’URL d’action de script que vous avez fournie dans le cadre de la requête de création de cluster. Le service reçoit le message d’erreur précédent lorsqu’il tente d’accéder à l’action de script.

### <a name="resolution"></a>Résolution

Ajoutez le compte Azure Data Lake Storage GEN 1 correspondant au cluster. Ajoutez également le principal du service qui accède au compte Data Lake Storage GEN 1 au cluster.

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Code d’erreur : La validation a échoué pour le document CsmDocument_2_0

### <a name="error"></a>Error

« La taille de machine virtuelle "\<CUSTOMER_SPECIFIED_VM_SIZE\>" fournie dans la requête n’est pas valide ou n’est pas prise en charge pour le rôle "\<ROLE\> ». Les valeurs autorisées sont : \<VALID_VM_SIZE_FOR_ROLE\>».

### <a name="cause"></a>Cause :

La taille de machine virtuelle que vous avez spécifiée n’est pas autorisée pour le rôle. Cette erreur peut se produire si la valeur de la taille de la machine virtuelle ne fonctionne pas comme prévu ou n’est pas adaptée au rôle de l’ordinateur.

### <a name="resolution"></a>Résolution

Le message d’erreur répertorie les valeurs valides pour la taille de la machine virtuelle. Sélectionnez l’une de ces valeurs et réessayez d’exécuter la requête de création de cluster.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Code d’erreur : InvalidVirtualNetworkId  

### <a name="error"></a>Error

« L’identifiant de réseau virtuel n’est pas valide. VirtualNetworkId ’\<USER_VIRTUALNETWORKID\>’ * »

### <a name="cause"></a>Cause :

La valeur **VirtualNetworkId** que vous avez spécifiée lors de la création du cluster n’est pas au format approprié.

### <a name="resolution"></a>Résolution

Assurez-vous que les **VirtualNetworkId** et les valeurs de sous-réseau sont dans le format approprié. Pour récupérer la valeur de la **VirtualNetworkId** :

1. Accédez au portail Azure.
1. Sélectionnez votre réseau virtuel.
1. Sélectionnez l’élément de menu **Propriétés**. La propriété **ResourceID** correspond à la valeur **VirtualNetworkId**.

Voici un exemple d’identifiant de réseau virtuel :

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Code d’erreur : CustomizationFailedErrorCode

### <a name="error"></a>Error

« Échec du déploiement du cluster en raison d’une erreur dans l’action du script personnalisé. Actions ayant échoué : \<SCRIPT_NAME\>, veuillez vous rendre sur l’interface utilisateur Ambari pour continuer de déboguer l’erreur. »

### <a name="cause"></a>Cause :

Le script personnalisé que vous avez fourni lors de la requête de création de cluster est exécuté après que le cluster a été déployé avec succès. Ce code d’erreur indique qu’une erreur s’est produite lors de l’exécution du script personnalisé nommé \<SCRIPT_NAME\>.

### <a name="resolution"></a>Résolution

Étant donné que le script est votre script personnalisé, nous vous recommandons de résoudre le problème et de réexécuter le script si nécessaire. Pour résoudre les problèmes d’échec de script, examinez les journaux dans le dossier /var/lib/ambari-agent/*. Vous pouvez ouvrir la page **Operations** dans l’interface utilisateur Ambari, puis sélectionner l’opération **run_customscriptaction** pour afficher les détails de l’erreur.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Code d’erreur : InvalidDocumentErrorCode

### <a name="error"></a>Error

« La version de schéma \<META_STORE_TYPE\> \<METASTORE_MAJOR_VERSION\> dans la base de données \<database_name\> n’est pas compatible avec la version de cluster \<CLUSTER_VERSION\> »

### <a name="cause"></a>Cause :

Le metastore personnalisé est incompatible avec la version de cluster HDInsight sélectionnée. Actuellement, les clusters HDInsight 4,0 prennent uniquement en charge les versions 3.0 et ultérieures de la version de, tandis que les clusters HDInsight 3.6 ne prennent pas en charge la version 3.0 et les versions ultérieures de la bibliothèque

### <a name="resolution"></a>Résolution

Utilisez uniquement les versions Metastore qui sont prises en charge par votre version de cluster HDInsight. Si vous ne spécifiez pas de Metastore personnalisé, HDInsight en crée un en interne, puis le supprime lors de la suppression du cluster.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Code d’erreur : FailedToConnectWithClusterErrorCode 

### <a name="error"></a>Error

« Impossible de se connecter au point de terminaison de gestion du cluster pour effectuer une opération de mise à l’échelle. Vérifiez que les règles de sécurité réseau ne bloquent pas l’accès externe au cluster, et que l’interface utilisateur du gestionnaire de cluster (Ambari) est accessible. »

### <a name="cause"></a>Cause :

Une règle de pare-feu de votre groupe de sécurité réseau bloque les communications de cluster avec les services d’intégrité et de gestion Azure critiques.

### <a name="resolution"></a>Résolution

Si vous envisagez d’utiliser des groupes de sécurité réseau pour contrôler le trafic réseau, effectuez les actions suivantes avant d’installer HDInsight :

- Identifiez la région Azure que vous projetez d’utiliser pour HDInsight.
- Identifiez les adresses IP que HDInsight requiert. Pour plus d’informations, consultez [Adresses IP de gestion HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
  - Créez ou modifiez les groupes de sécurité réseau pour le sous-réseau dans lequel vous prévoyez d’installer HDInsight.
  - Pour les groupes de sécurité réseau, autorisez le trafic entrant sur le port 443 à partir des adresses IP. Cette configuration garantit que les services de gestion HDInsight peuvent accéder au cluster depuis l’extérieur du réseau virtuel.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Code d’erreur : StoragePermissionsBlockedForMsi  

### <a name="error"></a>Error

« L’identité gérée ne dispose pas d’autorisations sur le compte de stockage. Vérifiez que le rôle Propriétaire des données Blob du stockage est attribué à l’identité managée sur le compte de stockage. Storage: /subscriptions/ \<Subscription ID\> /resourceGroups/\< Resource Group Name\> /providers/Microsoft.Storage/storageAccounts/ \<Storage Account Name\>, Managed Identity: /subscriptions/ \<Subscription ID\> /resourceGroups/ /\< Resource Group Name\> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/ \<User Managed Identity Name\> »

### <a name="cause"></a>Cause :

Vous n’avez pas fourni les autorisations nécessaires pour gérer l’identité. L’identité gérée affectée par l’utilisateur n’a pas le rôle de contributeur de stockage d’objets BLOB sur le compte de stockage Azure Data Lake Storage Gen2.

### <a name="resolution"></a>Résolution

1. Ouvrez le portail Azure.
1. Accédez à votre compte de stockage.
1. Cherchez sous **Contrôle d’accès**.
1. Assurez-vous que le rôle de collaborateur de données de stockage BLOB ou le rôle de propriétaire des données d’objet BLOB de stockage dispose d’un accès « affecté » à l’identité gérée affectée par l’utilisateur pour l’abonnement.

Pour plus d’informations, consultez [Définir les autorisations de l’identité managée sur le compte Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Code d’erreur : InvalidNetworkSecurityGroupSecurityRules  

### <a name="error"></a>Error

« Les règles de sécurité dans le groupe de sécurité réseau /subscriptions/\<SubscriptionID\>/resourceGroups/<Resource Group name\> default/providers/Microsoft.Network/networkSecurityGroups/\<Network Security Group Name\> configured with subnet /subscriptions/\<SubscriptionID\>/resourceGroups/\<Resource Group name\> RG-westeurope-vnet-tomtom-default/providers/Microsoft.Network/virtualNetworks/\<Virtual Network Name\>/subnets/\<Subnet Name\> n’autorise pas les connectivités entrantes ou sortantes. Pour plus d’informations, consultez [Planifier un réseau virtuel pour Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment) ou contactez le support. »

### <a name="cause"></a>Cause :

Si les groupes de sécurité réseau ou les itinéraires définis par l’utilisateur contrôlent le trafic entrant vers votre cluster HDInsight, assurez-vous que votre cluster peut communiquer avec les services d’intégrité et de gestion Azure critiques.

### <a name="resolution"></a>Résolution

Si vous envisagez d’utiliser des groupes de sécurité réseau pour contrôler le trafic réseau, effectuez les actions suivantes avant d’installer HDInsight :

- Déterminez la région Azure que vous prévoyez d’utiliser pour HDInsight et créez une liste sécurisée des adresses IP pour votre région. Pour plus d’informations, consultez les [Services de gestion et d’intégrité : régions spécifiques](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions).
- Identifiez les adresses IP requises par HDInsight. Pour plus d’informations, consultez  [Adresses IP de gestion HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
- Créez ou modifiez les groupes de sécurité réseau pour le sous-réseau dans lequel vous prévoyez d’installer HDInsight. Pour les groupes de sécurité réseau, autorisez le trafic entrant sur le port 443 à partir des adresses IP. Cette configuration garantit que les services de gestion HDInsight peuvent accéder au cluster depuis l’extérieur du réseau virtuel.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Code d’erreur : Le programme d’installation du cluster n’a pas pu installer les composants sur un ou plusieurs hôtes

###  <a name="error"></a>Error

« Le programme d’installation du cluster n’a pas pu installer les composants sur un ou plusieurs ordinateurs hôtes. Veuillez relancez la requête. »

### <a name="cause"></a>Cause : 

En règle générale, cette erreur est générée en cas de problème temporaire ou une panne d’Azure.

### <a name="resolution"></a>Résolution

Consultez la page [Statut Azure](https://status.azure.com/status) pour les pannes d’Azure susceptibles d’avoir une incidence sur le déploiement du cluster. En cas d’absence de panne, réessayez le déploiement du cluster.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la résolution des erreurs lors de la création de clusters, consultez [Résoudre les problèmes de création de cluster avec Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails).
