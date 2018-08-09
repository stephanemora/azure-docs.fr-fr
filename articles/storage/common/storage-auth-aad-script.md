---
title: Exécuter les commandes Azure CLI ou PowerShell sous une identité Azure AD pour accéder à Stockage Azure (préversion) | Documents Microsoft
description: Azure CLI et PowerShell prennent en charge la connexion avec une identité Azure AD pour l’exécution de commandes sur des conteneurs et des files d’attente Stockage Azure, ainsi que sur leurs données. Un jeton d’accès est fourni pour la session et utilisé pour autoriser les opérations d’appel. Les autorisations dépendent du rôle attribué à l’identité Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/30/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 253edccef064e729e96bceac619458cf4c585ae4
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522483"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell-preview"></a>Utiliser une identité Azure AD pour accéder à Stockage Azure avec CLI ou PowerShell (préversion)

Stockage Azure fournit des extensions en préversion pour Azure CLI et PowerShell qui vous permettent de vous connecter et d’exécuter des commandes de script sous une identité Azure Active Directory (Azure AD). L’identité Azure AD peut être un utilisateur, un groupe ou un principal du service d’application, ou il peut s’agir d’une [identité de service managée](../../active-directory/managed-service-identity/overview.md). Vous pouvez affecter des autorisations d’accès aux ressources de stockage à l’identité Azure AD via le contrôle d’accès en fonction du rôle (RBAC). Pour plus d’informations sur les rôles RBAC dans Stockage Azure, consultez [Gérer les droits d’accès aux données Stockage Azure avec RBAC (préversion)](storage-auth-aad-rbac.md).

Lorsque vous vous connectez à Azure CLI ou PowerShell avec une identité Azure AD, un jeton d’accès est retourné pour accéder à Stockage Azure sous cette identité. Ce jeton est ensuite utilisé automatiquement par CLI ou PowerShell pour autoriser les opérations dans Stockage Azure. Pour les opérations prises en charge, vous n’avez plus besoin de fournir une clé de compte ou le jeton SAP avec la commande.

> [!IMPORTANT]
> Cette préversion est destinée à une utilisation hors production uniquement. Les contrats SLA (contrats de niveau de service) de production ne sont pas disponibles tant que l’intégration d’Azure AD pour le Stockage Azure n’est pas officiellement disponible de manière générale. Si l’intégration d’Azure AD n’est pas encore prise en charge pour votre scénario, continuez à utiliser l’autorisation basée sur une clé partagée ou les jetons SAP dans vos applications. Pour plus d’informations sur la préversion, consultez [Authentifier l’accès au Stockage Azure à l’aide d’Azure Active Directory (préversion)](storage-auth-aad.md).
>
> Pendant la préversion, la propagation des attributions de rôles RBAC peut prendre jusqu’à cinq minutes.
>
> L’intégration d’Azure AD avec Stockage Azure nécessite l’utilisation d’HTTPS pour les opérations de Stockage Azure.

## <a name="supported-operations"></a>Opérations prises en charge

Les extensions en préversion sont prises en charge pour les opérations sur les conteneurs et les files d’attente. Les opérations que vous pouvez appeler dépendent des autorisations accordées à l’identité Azure AD avec laquelle vous vous connectez à Azure CLI ou PowerShell. Les autorisations sur les conteneurs ou les files d’attente Stockage Azure sont attribuées via le contrôle d’accès en fonction du rôle (RBAC). Par exemple, si un rôle de lecteur de données est attribué à l’identité, vous pouvez exécuter des commandes de script qui lisent les données d’un conteneur ou d’une file d’attente. Si un rôle de contributeur de données est attribué à l’identité, vous pouvez exécuter des commandes de script qui lisent, écrivent ou suppriment un conteneur ou une file d’attente, ou les données qu’ils contiennent. 

Pour plus d’informations sur les autorisations requises pour chaque opération Stockage Azure sur un conteneur ou une file d’attente, consultez [Autorisations pour appeler les opérations REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).  

## <a name="call-cli-commands-with-an-azure-ad-identity"></a>Appeler des commandes CLI avec une identité Azure AD

Pour installer l’extension en préversion pour Azure CLI :

1. Assurez-vous qu’Azure CLI version 2.0.32 ou ultérieure est installé. Exécutez `az --version` pour vérifier la version installée.
2. Exécutez la commande suivante pour installer l’extension en préversion : 

    ```azurecli
    az extension add -n storage-preview
    ```

L’extension en préversion ajoute un nouveau paramètre `--auth-mode` aux commandes prises en charge :

- Définissez le paramètre `--auth-mode` sur `login` pour vous connecter à l’aide d’une identité Azure AD.
- Définissez le paramètre `--auth-mode` sur la valeur `key` héritée pour essayer de demander une clé de compte si aucun paramètre d’authentification n’est fourni pour le compte. 

Par exemple, pour télécharger un objet blob dans Azure CLI à l’aide d’une identité Azure AD, exécutez d’abord `az login`, puis appelez la commande avec `--auth-mode` défini sur `login` :

```azurecli
az login
az storage blob download --account-name storagesamples --container sample-container --name myblob.txt --file myfile.txt --auth-mode login 
```

La variable d’environnement associée au paramètre `--auth-mode` est `AZURE_STORAGE_AUTH_MODE`.

## <a name="call-powershell-commands-with-an-azure-ad-identity"></a>Appeler des commandes PowerShell avec une identité Azure AD

Pour utiliser Azure PowerShell pour vous connecter avec une identité Azure AD :

1. Vérifiez que la dernière version de PowerShellGet est installée. Exécutez la commande suivante pour installer la dernière version :
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

2. Désinstallez les installations précédentes d’Azure PowerShell.
3. Installez AzureRM :

    ```powershell
    Install-Module AzureRM –Repository PSGallery –AllowClobber
    ```

4. Installez le module en préversion :

    ```powershell
    Install-Module -Name Azure.Storage -AllowPrerelease –AllowClobber 
    ```

5. Appelez l’applet de commande [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext) pour créer un contexte et spécifiez le paramètre `-UseConnectedAccount`. 
6. Pour appeler une applet de commande avec une identité Azure AD, passez le contexte à l’applet de commande.

L’exemple suivant montre comment dresser la liste des objets blob d’un conteneur à partir d’Azure PowerShell à l’aide d’une identité Azure AD : 

```powershell
$ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -UseConnectedAccount 
Get-AzureStorageBlob -Container $sample-container -Context $ctx 
```

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les rôles RBAC relatifs au stockage Azure, consultez [Gérer les droits d’accès aux données de stockage avec RBAC (préversion)](storage-auth-aad-rbac.md).
- Pour plus d’informations sur l’utilisation de Managed Service Identity avec Stockage Azure, consultez [S’authentifier avec Azure AD à partir d’une identité de service managée Azure (préversion)](storage-auth-aad-msi.md).
- Pour savoir comment autoriser l’accès aux conteneurs et aux files d’attente depuis vos applications de stockage, consultez [Utiliser Azure AD avec les applications de stockage](storage-auth-aad-app.md).
- Pour plus d’informations sur l’intégration d’Azure AD pour les objets blob et les files d’attente Azure, consultez le billet de blog de l’équipe Stockage Azure intitulé [Announcing the Preview of Azure AD Authentication for Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
