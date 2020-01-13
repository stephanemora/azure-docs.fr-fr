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
ms.openlocfilehash: e537014f741196024c24dd6ee98af0d8af2e1b31
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475931"
---
# <a name="hdinsight-cluster-creation-errors"></a>HDInsight : Erreurs lors de la création du cluster

Cet article décrit les solutions aux erreurs rencontrées lors de la création de clusters. 

> [!NOTE]
> Les trois premières erreurs de la liste ci-dessous se produisent en raison d’erreurs de validation lorsque la classe CsmDocument_2_0 est utilisée par un produit HDInsight.



## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Code d’erreur : La validation a échoué pour le document CsmDocument_2_0

### <a name="error-script-action-location-cannot-be-accessed-uriscript-action-url"></a>**Erreur** : Impossible d’accéder à l’URI de l’emplacement de l’action de script URI : \<URL D’ACTION DE SCRIPT\>

**Message d’erreur : « Le serveur distant a retourné une erreur : 404 Introuvable. »**

### <a name="cause"></a>Cause :
L’URL d’action de script fournie dans le cadre de la demande de création de cluster n’est pas accessible à partir du service HDInsight. Nous recevons le message « ErrorMessage » lorsque nous tentons d’accéder à l’action de script.

### <a name="resolution"></a>Résolution 
  - Pour une URL http/https, vous pouvez vérifier en tentant d’ouvrir l’URL à partir d’une fenêtre de navigateur privée. 
  - Pour une URL WASB, assurez-vous que le script existe dans le compte de stockage indiqué dans la demande. Assurez-vous que la clé de stockage pour ce compte de stockage est exacte. 
  - Pour une URL ADLS, assurez-vous que le script existe dans le compte de stockage.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Code d’erreur : La validation a échoué pour le document CsmDocument_2_0

### <a name="error-script-action-location-cannot-be-accessed-uri-script-action-url"></a>**Erreur** : Impossible d’accéder à l’emplacement de l’action de script URI : \<URL D’ACTION DE SCRIPT\>

**Message d’erreur : L’URI de script donnée \<URI DE SCRIPT\> se trouve dans ADLS, mais ce cluster n’a pas de principal de stockage Data Lake**

### <a name="cause"></a>Cause :
L’URL d’action de script fournie dans le cadre de la demande de création de cluster n’est pas accessible à partir du service HDInsight. Nous recevons le message « ErrorMessage » lorsque nous tentons d’accéder à l’action de script. 

### <a name="resolution"></a>Résolution

Assurez-vous que le compte Azure Data Lake Store Gen 1 correspondant est ajouté au cluster. Le principal du service utilisé pour accéder au compte Azure Data Lake Store Gen 1 est également ajouté au cluster. 

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Code d’erreur : La validation a échoué pour le document CsmDocument_2_0

### <a name="error-vm-size-customer-specified-vm-size-provided-in-the-request-is-invalid-or-not-supported-for-role-role-valid-values-are-valid-vm-size-for-role"></a>**Erreur** : La taille de machine virtuelle \<Taille de machine virtuelle spécifiée par le client\> fournie dans la demande n’est pas valide ou n’est pas prise en charge pour le rôle \<RÔLE\>. Les valeurs autorisées sont : \<TAILLE DE MACHINE VIRTUELLE VALIDE POUR LE RÔLE\>.

### <a name="cause"></a>Cause :
Les tailles de machine virtuelle spécifiées par le client ne sont pas autorisées pour le rôle. Cela peut être vrai, car la valeur de taille de machine virtuelle ne fonctionne pas comme prévu ou n’est pas appropriée pour le rôle d’ordinateur. 

### <a name="resolution"></a>Résolution
Le message d’erreur répertorie les valeurs valides pour la taille de la machine virtuelle. Sélectionnez l’une de ces valeurs valides, puis réessayez d’exécuter la demande de création de cluster. 

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Code d’erreur : InvalidVirtualNetworkId  

### <a name="error-the-virtualnetworkid-is-not-valid-virtualnetworkid-user_virtualnetworkid"></a>**Erreur** : L’ID de réseau virtuel n’est pas valide. VirtualNetworkId'\<USER_VIRTUALNETWORKID\>' *

### <a name="cause"></a>Cause :
La valeur **VirtualNetworkId** spécifiée pendant la création du cluster n’est pas au format approprié. 

### <a name="resolution"></a>Résolution
Assurez-vous que **VirtualNetworkId** et le sous-réseau sont dans le format approprié. Pour obtenir la valeur **VirtualnetworkId**, accédez au Portail Azure, sélectionnez votre réseau virtuel, puis sélectionnez **Propriétés** dans le menu. La propriété **ResourceID** correspond à la valeur **VirtualNetworkId**. 

Voici un exemple d’ID de réseau virtuel : 

“/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet” 

---

## <a name="error-code-customizationfailederrorcode"></a>Code d’erreur : CustomizationFailedErrorCode

### <a name="error-cluster-deployment-failed-due-to-an-error-in-the-custom-script-action-failed-actions-script_name-please-go-to-ambari-ui-to-further-debug-the-failure"></a>**Erreur** : Échec du déploiement du cluster en raison d’une erreur dans l’action du script personnalisé. Actions ayant échoué : \<SCRIPT_NAME\>, accédez à l’interface utilisateur Ambari pour continuer le débogage de l’échec.

### <a name="cause"></a>Cause :
Le script personnalisé de l’utilisateur qui a été fourni lors de la demande de création de cluster est exécuté une fois que le cluster a été déployé avec succès. Ce code d’erreur indique qu’une erreur s’est produite lors de l’exécution de ce script personnalisé avec le nom \<SCRIPT_NAME\>.   

### <a name="resolution"></a>Résolution
Comme il s’agit du script personnalisé de l’utilisateur, ce dernier doit résoudre le problème et réexécuter le script si nécessaire. Pour résoudre les problèmes d’échec de script, examinez les journaux dans le dossier /var/lib/ambari-agent/*. Vous pouvez ouvrir la page Opérations dans l’interface utilisateur Ambari, puis sélectionner l’opération **run_customscriptionaction** pour afficher les détails de l’erreur. 

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Code d’erreur : InvalidDocumentErrorCode

### <a name="error-the-meta_store_type-metastore-schema-version-metastore_major_version-in-database-database_name-is-incompatible-with-cluster-version-cluster_version"></a>**Erreur** : La version de schéma \<META_STORE_TYPE\> \<METASTORE_MAJOR_VERSION\> dans la base de données \<database_name\> n’est pas compatible avec la version de cluster \<CLUSTER_VERSION\>

### <a name="cause"></a>Cause :
Le metastore personnalisé est incompatible avec la version de cluster HDInsight sélectionnée. Actuellement, le cluster HDInsight 4.0 prend en charge uniquement Metastore 3.*x* et HDInsight 3.6 ne prend pas en charge Metastore version 3.*x* ou ultérieure. 

### <a name="resolution"></a>Résolution
Veillez à utiliser uniquement les versions Metastore qui sont prises en charge par chaque version de cluster HDInsight. Notez que si aucun metastore personnalisé n’est spécifié, HDInsight en crée un en interne. Toutefois, ce metastore est automatiquement supprimé lors de la suppression du cluster. 

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Code d’erreur : FailedToConnectWithClusterErrorCode 

### <a name="error-unable-to-connect-to-cluster-management-endpoint-to-perform-scaling-operation-verify-that-network-security-rules-are-not-blocking-external-access-to-the-cluster-and-that-the-cluster-manager-ambari-ui-can-be-successfully-accessed"></a>**Erreur** : Impossible de se connecter au point de terminaison de gestion du cluster pour effectuer une opération de mise à l’échelle. Vérifiez que les règles de sécurité réseau ne bloquent pas l’accès externe au cluster, et que l’interface utilisateur du gestionnaire de cluster (ambari) est accessible.

### <a name="cause"></a>Cause :
Vous disposez d’une règle de pare-feu sur votre groupe de sécurité réseau qui bloque la communication entre les clusters et les services d’intégrité et de gestion Azure critiques. 

### <a name="resolution"></a>Résolution
Si vous envisagez d’utiliser des  **groupes de sécurité réseau** pour contrôler le trafic réseau, effectuez les actions suivantes avant d’installer HDInsight : 
  - Identifiez la région Azure que vous projetez d’utiliser pour HDInsight. 
  - Identifiez les adresses IP que HDInsight requiert. Pour plus d’informations, consultez [Adresses IP de gestion HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses). 
    - Créez ou modifiez les groupes de sécurité réseau pour le sous-réseau dans lequel vous prévoyez d’installer HDInsight. 
    - **Groupes de sécurité réseau :** Autorisez **le trafic** entrant sur le port **443**  à partir de l’adresse IP. Cela garantit que les services de gestion HDInsight peuvent atteindre le cluster depuis un point en dehors du réseau virtuel. 

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Code d’erreur : StoragePermissionsBlockedForMsi  

### <a name="error-the-managed-identity-does-not-have-permissions-on-the-storage-account-please-verify-that-storage-blob-data-owner-role-is-assigned-to-the-managed-identity-for-the-storage-account-storage-subscriptions-subscription-id-resourcegroups-resource-group-name-providersmicrosoftstoragestorageaccounts-storage-account-name-managed-identity-subscriptions-subscription-id-resourcegroups--resource-group-name-providersmicrosoftmanagedidentityuserassignedidentities-user-managed-identity-name"></a>**Erreur** : L’identité managée n’a pas les autorisations sur le compte de stockage. Vérifiez que le rôle Propriétaire des données Blob du stockage est attribué à l’identité managée sur le compte de stockage. Storage: /subscriptions/ \<Subscription ID\> /resourceGroups/\< Resource Group Name\> /providers/Microsoft.Storage/storageAccounts/ \<Storage Account Name\>, Managed Identity: /subscriptions/ \<Subscription ID\> /resourceGroups/ /\< Resource Group Name\> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/ \<User Managed Identity Name\>

### <a name="cause"></a>Cause :
Les autorisations requises n’ont pas été fournies à l’**identité managée**. L’**identité managée attribuée par l’utilisateur** n’avait pas le rôle Contributeur aux données Blob du stockage sur le compte de stockage ADLS Gen2. 

### <a name="resolution"></a>Résolution

Ouvrez le portail Azure, accédez à votre compte de stockage et, sous **Contrôle d’accès (IAM)** , assurez-vous que le rôle Contributeur aux données Blob du stockage ou Propriétaire des données Blob du stockage dispose d’un accès attribué à l’ **identité managée affectée par l’utilisateur** pour l’abonnement. Pour plus d’informations, consultez [Définir les autorisations de l’identité managée sur le compte Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md). 

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Code d’erreur : InvalidNetworkSecurityGroupSecurityRules  

### <a name="error-the-security-rules-in-the-network-security-group-subscriptionssubscriptionidresourcegroupsresource-group-name-defaultprovidersmicrosoftnetworknetworksecuritygroupsnetwork-security-group-name-configured-with-subnet-subscriptionssubscriptionidresourcegroupsresource-group-name-rg-westeurope-vnet-tomtom-defaultprovidersmicrosoftnetworkvirtualnetworksvirtual-network-namesubnetssubnet-name"></a>**Erreur** : Les règles de sécurité dans le groupe de sécurité réseau /subscriptions/\<SubscriptionID>\/resourceGroups/<Resource Group name> default/providers/Microsoft.Network/networkSecurityGroups/\<Network Security Group Name\> configuré avec l’abonnement /subscriptions/\<SubscriptionID>\/resourceGroups/\<Resource Group name> RG-westeurope-vnet-tomtom-default\/providers/Microsoft.Network/virtualNetworks/\<Virtual Network Name>\/subnets/\<Subnet Name\> 
n’autorisent pas la connectivité entrante et/ou sortante requise. Pour plus d’informations, consultez [Planifier un réseau virtuel pour Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment) ou contactez le support.**

### <a name="cause"></a>Cause :
Si vous utilisez des groupes de sécurité réseau ou des itinéraires définis par l’utilisateur pour contrôler le trafic vers votre cluster HDInsight, assurez-vous que votre cluster peut communiquer avec les services de gestion et d’intégrité Azure.

### <a name="resolution"></a>Résolution
Si vous envisagez d’utiliser des  **groupes de sécurité réseau** pour contrôler le trafic réseau, effectuez les actions suivantes avant d’installer HDInsight : 
  - Identifiez la région Azure que vous prévoyez d’utiliser pour HDInsight et créez une liste sécurisée des adresses IP pour votre région : [Services de gestion et d’intégrité : régions spécifiques](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions).
  - Identifiez les adresses IP que HDInsight requiert. Pour plus d’informations, consultez  [Adresses IP de gestion HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses). 
  - Créez ou modifiez les groupes de sécurité réseau pour le sous-réseau dans lequel vous prévoyez d’installer HDInsight. **Groupes de sécurité réseau** : autorisez le trafic entrant sur le port **443** à partir des adresses IP. Cela garantit que les services de gestion HDInsight peuvent atteindre le cluster depuis un point en dehors du réseau virtuel.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Code d’erreur : Le programme d’installation du cluster n’a pas pu installer les composants sur un ou plusieurs hôtes

###  <a name="errorcluster-setup-failed-to-install-components-on-one-or-more-hosts-please-retry-your-request"></a>**Erreur** : Le programme d’installation du cluster n’a pas pu installer les composants sur un ou plusieurs hôtes. Relancez la requête. 

### <a name="cause"></a>Cause : 
En général, cette erreur est générée en cas de problème temporaire ou de panne Azure 

### <a name="resolution"></a>Résolution

Consultez la page [Statut Azure](https://status.azure.com/status) pour identifier les interruptions potentielles d’Azure susceptibles d’avoir un impact sur le déploiement du cluster. En l’absence de panne, réessayez le déploiement du cluster. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la résolution des erreurs de création de cluster, consultez [Résoudre les problèmes de création de cluster avec Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails).
