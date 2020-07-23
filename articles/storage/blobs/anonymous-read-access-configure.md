---
title: Configurer l’accès en lecture public anonyme pour les conteneurs et les objets blob
titleSuffix: Azure Storage
description: Découvrez comment activer ou désactiver l’accès anonyme aux données d’objet blob pour le compte de stockage. Définissez le paramètre accès public du conteneur pour rendre les conteneurs et les objets blob disponibles pour l’accès anonyme.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: af589874021baaf04a423b7bbaa0e36528eda93c
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86208984"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Configurer l’accès en lecture public anonyme pour les conteneurs et les objets blob

Le Stockage Azure prend en charge l’accès en lecture public anonyme pour les conteneurs et les objets blob. Par défaut, toutes les requêtes adressées à un conteneur et à ses objets blob doivent être autorisées à l’aide d’Azure Active Directory (Azure AD) ou d’une autorisation de clé partagée. Quand vous configurez le paramètre de niveau d’accès public d’un conteneur pour autoriser l’accès anonyme, les clients peuvent lire les données dans ce conteneur sans autoriser la requête.

> [!WARNING]
> Lorsqu’un conteneur est configuré pour un accès public, n’importe quel client peut lire les données de ce conteneur. L’accès public présente un risque de sécurité potentiel. Par conséquent, si votre scénario ne l’exige pas, Microsoft vous recommande de le désactiver pour le compte de stockage. Pour en savoir plus, consultez la section [Configure anonymous public read access for containers and blobs](anonymous-read-access-prevent.md) (Empêcher l’accès en lecture publique anonyme aux conteneurs et aux objets blob).

Pour configurer l’accès public pour un conteneur, vous devez effectuer deux étapes :

1. Activer l’accès public pour le compte de stockage
1. Configurer le paramètre d'accès public du conteneur

Cet article explique comment configurer l’accès en lecture public anonyme pour un conteneur et ses objets blob. Pour plus d’informations sur l’accès anonyme aux données d’objet blob à partir d’une application cliente, consultez [Accéder à des conteneurs publics et à des objets blob anonymement avec .NET](anonymous-read-access-client.md).

## <a name="enable-or-disable-public-read-access-for-a-storage-account"></a>Activer ou désactiver l’accès en lecture public à un compte de stockage

Par défaut, l’accès public est activé pour un compte de stockage. La désactivation de l’accès public empêche tout accès anonyme aux conteneurs et aux objets blob de ce compte. Pour améliorer la sécurité, Microsoft vous recommande de désactiver l’accès public à vos comptes de stockage, sauf si votre scénario requiert que les utilisateurs accèdent aux ressources d’objets blob de façon anonyme.

> [!WARNING]
> La désactivation de l’accès public pour un compte de stockage remplace les paramètres d’accès public pour tous les conteneurs appartenant à ce compte de stockage. Quand l’accès public est désactivé pour le compte de stockage, toute demande anonyme ultérieure adressée à ce compte échoue.

Pour activer ou désactiver l’accès public pour un compte de stockage, utilisez le portail Azure ou Azure CLI pour configurer la propriété **blobPublicAccess** du compte. Cette propriété est disponible pour tous les comptes de stockage créés avec le modèle de déploiement Azure Resource Manager. Pour plus d’informations, consultez [Vue d’ensemble des comptes de stockage](../common/storage-account-overview.md).

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Pour activer ou désactiver l’accès public à un compte de stockage dans le portail Azure, procédez comme suit :

1. Accédez à votre compte de stockage dans le portail Azure.
1. Localisez le paramètre **Configuration** sous **Paramètres**.
1. Définissez **Accès public aux objets blob** sur **Désactivé** ou **Activé**.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Capture d’écran montrant comment activer ou désactiver l’accès public aux objets blob pour le compte":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour activer ou désactiver l’accès public à un compte de stockage avec Azure CLI, commencez par obtenir l’ID de ressource de votre compte de stockage en appelant la commande [az resource show](/cli/azure/resource#az-resource-show). Ensuite, appelez la commande [az resource update](/cli/azure/resource#az-resource-update) pour définir la propriété **allowBlobPublicAccess** pour le compte de stockage. Pour activer l’accès public, affectez la valeur true à la propriété **allowBlobPublicAccess**. Pour le désactiver, affectez-lui la valeur **false**.

L’exemple suivant désactive l’accès aux objets blob publics pour le compte de stockage. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli-interactive
storage_account_id=$(az resource show \
    --name anonpublicaccess \
    --resource-group storagesamples-rg \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowBlobPublicAccess=false
    ```
```

Pour vérifier si l’accès public est activé avec Azure CLI, appelez la commande [az resource show](/cli/azure/resource#az-resource-show) et la requête de la propriété **allowBlobPublicAccess** :

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

---

> [!NOTE]
> La désactivation de l’accès public à un compte de stockage n’affecte aucun site web statique hébergé dans ce compte de stockage. Le conteneur **$web** est toujours accessible publiquement.

## <a name="check-the-public-access-setting-for-a-storage-account"></a>Vérifier le paramètre d’accès public d’un compte de stockage

Pour vérifier le paramètre d’accès public d’un compte de stockage, récupérez la valeur de la propriété **allowBlobPublicAccess**. Pour vérifier cette propriété pour un grand nombre de comptes de stockage à la fois, utilisez l’Explorateur Azure Resource Graph.

La propriété **allowBlobPublicAccess** n’est pas définie par défaut et ne retourne pas de valeur tant que vous ne la définissez pas explicitement. Par défaut, le compte de stockage autorise l’accès public lorsque la valeur de la propriété est Null.

### <a name="check-the-public-access-setting-for-a-single-storage-account"></a>Vérifier le paramètre d’accès public d’un seul compte de stockage

Pour vérifier le paramètre d’accès public d’un seul compte de stockage spécifique à l’aide d’Azure CLI, appelez la commande [az resource show](/cli/azure/resource#az-resource-show) et interrogez la propriété **allowBlobPublicAccess** :

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

### <a name="check-the-public-access-setting-for-a-set-of-storage-accounts"></a>Vérifier le paramètre d’accès public d’un ensemble de comptes de stockage

Pour vérifier le paramètre d’accès public d’un ensemble de comptes de stockage avec des performances optimales, vous pouvez utiliser l’Explorateur Azure Resource Graph dans le portail Azure. Pour en savoir plus sur l’utilisation de l’Explorateur Resource Graph, consultez [Démarrage rapide : exécuter votre première requête Resource Graph à l’aide de l’Explorateur Azure Resource Graph](/azure/governance/resource-graph/first-query-portal).

L’exécution de la requête suivante dans l’Explorateur Resource Graph retourne une liste de comptes de stockage et affiche la valeur de la propriété **allowBlobPublicAccess** pour chaque compte :

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
| order by subscriptionId, resourceGroup, name asc
```

## <a name="set-the-public-access-level-for-a-container"></a>Définir le niveau d’accès public pour un conteneur

Pour octroyer aux utilisateurs anonymes un accès en lecture à un conteneur et ses objets blob, commencez par activer l’accès public au compte de stockage, puis définissez le niveau d’accès public du conteneur. Si l’accès public est désactivé pour le compte de stockage, vous ne pouvez pas créer un accès public pour un conteneur.

Lorsque l’accès public est activé pour un compte de stockage, vous pouvez configurer un conteneur avec les autorisations suivantes :

- **Aucun accès en lecture public :** le conteneur et ses objets blob ne sont accessibles qu’avec une requête autorisée. Cette option est la configuration par défaut de tous les nouveaux conteneurs.
- **Accès en lecture public pour les objets blob uniquement :** les objets blob à l’intérieur du conteneur peuvent être lus via une requête anonyme, mais les données du conteneur ne sont pas disponibles anonymement. Les clients anonymes ne peuvent pas énumérer les objets blob présents dans le conteneur.
- **Public read access for container and its blobs (Accès en lecture public pour le conteneur et ses objets blob) :** Les données de conteneur et d’objet blob peuvent être lues par une requête anonyme, à l’exception des paramètres d’autorisation et des métadonnées du conteneur. Les clients peuvent énumérer les objets blob présents dans le conteneur par une demande anonyme, mais ne peuvent pas énumérer les conteneurs présents dans le compte de stockage.

Vous ne pouvez pas modifier le niveau d’accès public pour un objet blob individuel. Le niveau d’accès public est défini uniquement au niveau du conteneur.

Pour définir le niveau d’accès public d’un conteneur, utilisez le portail Azure ou Azure CLI. Vous pouvez définir le niveau d’accès public du conteneur lors de la création du conteneur, ou mettre à jour ce paramètre sur un conteneur existant.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Pour mettre à jour le niveau d’accès public pour un ou plusieurs conteneurs du portail Azure, procédez comme suit :

1. Accédez à la vue d’ensemble de votre compte de stockage dans le portail Azure.
1. Sous l’élément **service blob** du panneau de menu, sélectionnez **Conteneurs**.
1. Sélectionnez les conteneurs pour lesquels vous souhaitez définir le niveau d’accès public.
1. Utilisez le bouton **Modifier le niveau d’accès** pour afficher les paramètres d’accès public.
1. Sélectionnez le niveau d’accès public souhaité à partir du menu déroulant **Niveau d’accès public** et cliquez sur le bouton OK pour appliquer la modification aux conteneurs sélectionnés.

    ![Capture d’écran illustrant la façon de définir le niveau d’accès public dans le portail](./media/anonymous-read-access-configure/configure-public-access-container.png)

Lorsque l’accès public est désactivé pour le compte de stockage, le niveau d’accès public d’un conteneur ne peut pas être défini. Si vous essayez de définir le niveau d'accès public du conteneur, vous verrez que le paramètre est désactivé, car l’accès public est interdit pour le compte.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Capture d’écran montrant que la définition du niveau d’accès public du conteneur est bloquée lorsque l’accès public est désactivé":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour mettre à jour le niveau d’accès public d’un ou de plusieurs conteneurs avec Azure CLI, appelez la commande [az storage container set permission](/cli/azure/storage/container#az-storage-container-set-permission). Autorisez cette opération en transmettant votre clé de compte, une chaîne de connexion ou une signature d’accès partagé (SAP). L’opération [Définir l’ACL du conteneur](/rest/api/storageservices/set-container-acl) qui définit le niveau d’accès public du conteneur ne prend pas en charge l’autorisation avec Azure AD. Pour plus d’informations, consultez [Autorisations pour l’appel d’opérations de données d’objet BLOB et de file d’attente](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

L’exemple suivant définit le paramètre d’accès public pour un conteneur afin d’activer l’accès anonyme au conteneur et à ses objets blob. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli-interactive
az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key
```

Lorsque l’accès public est désactivé pour le compte de stockage, le niveau d’accès public d’un conteneur ne peut pas être défini. Si vous tentez de définir le niveau d’accès public du conteneur, une erreur se produit, indiquant que l’accès public n’est pas autorisé sur le compte de stockage.

---

## <a name="check-the-container-public-access-setting"></a>Vérifier le paramètre d'accès public du conteneur

Pour vérifier le paramètre d’accès public d’un ou de plusieurs conteneurs, vous pouvez utiliser le portail Azure, PowerShell, Azure CLI, l’une des bibliothèques clientes de stockage Azure ou le fournisseur de ressources de stockage Azure. Pour voir quelques exemples, consultez les sections suivantes.  

### <a name="check-the-public-access-setting-for-a-single-container"></a>Vérifier le paramètre d’accès public d’un seul conteneur

Pour obtenir le niveau d’accès public d’un ou de plusieurs conteneurs avec Azure CLI, appelez la commande [az storage container show permission](/cli/azure/storage/container#az-storage-container-show-permission). Autorisez cette opération en transmettant votre clé de compte, une chaîne de connexion ou une signature d’accès partagé (SAP). L’opération [Obtenir l’ACL du conteneur](/rest/api/storageservices/get-container-acl) qui retourne le niveau d’accès public du conteneur ne prend pas en charge l’autorisation avec Azure AD. Pour plus d’informations, consultez [Autorisations pour l’appel d’opérations de données d’objet BLOB et de file d’attente](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

L’exemple suivant lit le paramètre d’accès public pour un conteneur. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli-interactive
az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key>
    --auth-mode key
```

### <a name="check-the-public-access-setting-for-a-set-of-containers"></a>Vérifier le paramètre d’accès public d’un ensemble de conteneurs

Il est possible de vérifier quels conteneurs d’un ou de plusieurs comptes de stockage sont configurés pour l’accès public en répertoriant les conteneurs et en vérifiant le paramètre d’accès public. Cette approche est pratique lorsqu’un compte de stockage ne contient pas un grand nombre de conteneurs, ou lorsque vous vérifiez le paramètre sur un petit nombre de comptes de stockage. Toutefois, les performances peuvent être dégradées si vous tentez d’énumérer un grand nombre de conteneurs.

L’exemple suivant utilise PowerShell pour obtenir le paramètre d’accès public de tous les conteneurs d’un compte de stockage. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>Étapes suivantes

- [Empêcher l’accès en lecture public anonyme aux conteneurs et aux objets blob](anonymous-read-access-prevent.md)
- [Accéder anonymement aux conteneurs et objets blob publics avec .NET](anonymous-read-access-client.md)
- [Autorisation d’accès au Stockage Azure](../common/storage-auth.md)