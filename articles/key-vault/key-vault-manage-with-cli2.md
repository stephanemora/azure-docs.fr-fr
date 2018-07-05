---
title: Gestion de Azure Key Vault à l’aide de l’interface de ligne de commande (CLI) | Microsoft Docs
description: Utilisez cet article pour automatiser les tâches courantes dans Key Vault à l’aide de CLI 2.0
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: ''
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: barclayn
ms.openlocfilehash: 8c2501b5e89e81709de074c0b0c93b317ecebd7b
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316592"
---
# <a name="manage-key-vault-using-cli-20"></a>Gestion de Key Vault à l’aide de l’interface de ligne de commande (CLI) 2.0

Cet article explique comment commencer à utiliser Azure Key Vault à l'aide d'Azure CLI 2.0. Vous obtenez des informations sur :
- Guide pratique pour créer un conteneur renforcé (un coffre) dans Azure
- Guide pratique pour stocker et gérer les clés de chiffrement et les secrets dans Azure. 
- Utilisation d’Azure CLI pour créer un coffre.
- Création d’une clé ou d’un mot de passe que vous pouvez ensuite utiliser avec une application Azure. 
- Comment une application peut utiliser la clé ou le mot de passe créés.

Azure Key Vault est disponible dans la plupart des régions. Pour plus d’informations, consultez la [page de tarification de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).


> [!NOTE]
> Cet article n’inclut pas d’instructions sur l’écriture de l’application Azure abordée dans une des étapes, qui montre comment autoriser une application à utiliser une clé ou un secret dans le coffre de clés.
>

Pour une vue d'ensemble d'Azure Key Vault, consultez [Présentation d’Azure Key Vault](key-vault-whatis.md)

## <a name="prerequisites"></a>Prérequis
Pour utiliser les commandes Azure CLI de cet article, vous devez disposer des éléments suivants :

* Un abonnement à Microsoft Azure. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/pricing/free-trial).
* Interface de ligne de commande Azure, version 2.0 ou ultérieure. Pour installer la dernière version, consultez la page [Installer et configurer l’interface de ligne de commande multiplateforme Azure 2.0](/cli/azure/install-azure-cli).
* Une application configurée pour utiliser la clé ou le mot de passe que vous créez dans cet article. Un exemple d’application est disponible dans le [Centre de téléchargement Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Pour obtenir des instructions, consultez le fichier Lisez-moi inclus.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Obtention d’aide avec l’interface de ligne de commande interplateforme Azure
Cet article suppose que vous êtes familiarisé avec l’interface de ligne de commande (Bash, Terminal, invite de commandes).

Le paramètre --help ou -h peut être utilisé pour afficher l'aide relative à des commandes particulières. Le format Azure help [commande] [options] peut également être utilisé. Si vous avez des doutes sur les paramètres exigés par une commande, reportez-vous à l'aide. Les commandes suivantes, par exemple, renvoient les mêmes informations :

```azurecli-interactive
az account set --help
az account set -h
```

Consultez également les articles suivants afin de vous familiariser avec Azure Resource Manager dans l’interface de ligne de commande multiplateforme Azure :

* [Installation de l’interface de ligne de commande Azure](/cli/azure/install-azure-cli)
* [Prise en main d’Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)

## <a name="connect-to-your-subscriptions"></a>Connexion à vos abonnements

Pour se connecter de manière interactive, utilisez la commande suivante :

```azurecli
az login
```
Pour vous connecter à l’aide d’un compte professionnel, vous pouvez utiliser votre nom d’utilisateur et votre mot de passe.

```azurecli
az login -u username@domain.com -p password
```

Si vous disposez de plusieurs abonnements et souhaitez spécifier un abonnement particulier à utiliser, saisissez ce qui suit pour afficher les abonnements de votre compte :

```azurecli
az account list
```

Spécifiez un abonnement avec le paramètre d’abonnement.

```azurecli
az account set --subscription <subscription name or ID>
```

Pour plus d’informations sur la configuration de l’interface de ligne de commande multiplateforme Azure, consultez la page [Installation de l’interface de ligne de commande multiplateforme Azure](/cli/azure/install-azure-cli).

## <a name="create-a-new-resource-group"></a>Création d’un groupe de ressources
Lorsque vous utilisez Azure Resource Manager, toutes les ressources associées sont créées au sein d’un groupe de ressources. Vous pouvez créer un coffre de clés dans un groupe de ressources existant. Si vous souhaitez utiliser un groupe de ressources, vous pouvez en créer un.

```azurecli
az group create -n 'ContosoResourceGroup' -l 'East Asia'
```

Le premier paramètre est le nom de groupe de ressources et le deuxième paramètre est l’emplacement. Pour obtenir une liste de tous les emplacements possibles, tapez :

```azurecli
az account list-locations
``` 

## <a name="register-the-key-vault-resource-provider"></a>Inscription du fournisseur de ressources Key Vault
 Il est possible que vous receviez le message d’erreur « L’abonnement n’est pas inscrit pour utiliser l’espace de noms 'Microsoft.KeyVault' » lorsque vous essayez de créer un coffre de clés. Si ce message apparaît, assurez-vous que le fournisseur de ressources Key Vault est inscrit dans votre abonnement. Cette opération n’est à effectuer qu’une fois par abonnement.

```azurecli
az provider register -n Microsoft.KeyVault
```


## <a name="create-a-key-vault"></a>Création d’un coffre de clés

Utilisez la commande `az keyvault create` pour créer un coffre de clés. Ce script a trois paramètres obligatoires : un nom de groupe de ressources, un nom de coffre de clés et l’emplacement géographique.

Pour créer un coffre avec le nom **ContosoKeyVault**, dans le groupe de ressources **ContosoResourceGroup** résidant à l’emplacement **Asie Est**, tapez : 

```azurecli
az keyvault create --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

La sortie de cette commande affiche les propriétés du coffre de clés que vous avez créé. Les deux propriétés les plus importantes sont :

* **name** : dans l’exemple, le nom est ContosoKeyVault. Vous allez utiliser ce nom pour d’autres commandes Key Vault.
* **vaultUri** : dans l’exemple, il s’agit de https://contosokeyvault.vault.azure.net. Les applications qui utilisent votre coffre via son API REST doivent utiliser cet URI.

Votre compte Azure est pour l’instant le seul autorisé à effectuer des opérations sur ce À l’heure actuelle, personne d’autre n’y est autorisé.

## <a name="add-a-key-secret-or-certificate-to-the-key-vault"></a>Ajouter une clé, un secret ou un certificat au coffre de clés

Si vous souhaitez qu’Azure Key Vault crée une clé protégée par logiciel, utilisez la commande `az key create`.

```azurecli
az keyvault key create --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --protection software
```

Si vous avez une clé dans un fichier .pem, vous pouvez le télécharger dans Azure Key Vault. Vous pouvez choisir de protéger la clé avec un logiciel ou le HSM. Pour importer la clé à partir du fichier .pem et la protéger avec un logiciel, utilisez les éléments suivants :

```azurecli
az keyvault key import --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --pem-file './softkey.pem' --pem-password 'Pa$$w0rd' --protection software
```

Vous pouvez maintenant référencer la clé que vous avez créée ou téléchargée dans Azure Key Vault à l’aide de son URI. Utilisez **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** pour toujours obtenir la version actuelle. Utilisez https://[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id] pour obtenir cette version spécifique. Par exemple : **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**. 

Ajoutez un secret dans le coffre, c’est-à-dire un mot de passe nommé SQLPassword avec la valeur Pa$$w0rd dans Azure Key Vault. 

```azurecli
az keyvault secret set --vault-name 'ContosoKeyVault' --name 'SQLPassword' --value 'Pa$$w0rd'
```

Faire référence à ce mot de passe à l’aide de son URI. Utilisez **https://ContosoVault.vault.azure.net/secrets/SQLPassword** pour toujours obtenir la version actuelle et https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id] pour obtenir cette version spécifique. Par exemple : **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**.

Importer un certificat dans le coffre en utilisant un fichier .pem ou .pfx.

```azurecli
az keyvault certificate import --vault-name 'ContosoKeyVault' --file 'c:\cert\cert.pfx' --name 'ContosoCert' --password 'Pa$$w0rd'
```

Examinons la clé, le secret ou le certificat que vous avez créé :

* Pour afficher vos clés, tapez : 

```azurecli
az keyvault key list --vault-name 'ContosoKeyVault'
```

* Pour afficher vos secrets, tapez : 

```azurecli
az keyvault secret list --vault-name 'ContosoKeyVault'
```

* Pour afficher des certificats, tapez : 

```azurecli
az keyvault certificate list --vault-name 'ContosoKeyVault'
```

## <a name="register-an-application-with-azure-active-directory"></a>Inscription d’une application auprès d’Azure Active Directory
Cette étape est généralement effectuée par un développeur et sur un ordinateur distinct. Bien que non spécifique à Azure Key Vault, elle est incluse ici par souci d'exhaustivité. Pour terminer l’inscription de l’application, votre compte, le coffre et l’application doivent être dans le même répertoire Azure.

Les applications qui utilisent un coffre de clés doivent s’authentifier à l’aide d’un jeton à partir d’Azure Active Directory.  Le propriétaire de l’application doit d’abord l’inscrire dans Azure Active Directory. À la fin de l’inscription, le propriétaire de l’application obtient les valeurs suivantes :

- Un **ID d’application** (également appelé ID de Client AAD ou appID)
- Une **clé d’authentification** (également appelée secret partagé). 

L’application doit présenter ces deux valeurs à Azure Active Directory afin d’obtenir un jeton. La configuration d’une application pour obtenir un jeton dépend de l’application. Pour [l’exemple d’application Key Vault](https://www.microsoft.com/download/details.aspx?id=45343), le propriétaire de l’application définit ces valeurs dans le fichier app.config.

Pour obtenir des instructions détaillées sur l’inscription d’une application auprès d’Azure Active Directory, consultez les articles [Intégration d’applications dans Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md), [Utiliser le portail pour créer une application et un principal du service Azure Active Directory pouvant accéder aux ressources](../azure-resource-manager/resource-group-create-service-principal-portal.md) et [Créer un principal du service Azure avec Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli).

Pour inscrire une application à Azure Active Directory :

```azurecli
az ad sp create-for-rbac -n "MyApp" --password 'Pa$$w0rd' --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorisation de l’application à utiliser la clé ou le secret

Pour autoriser l’application à accéder à la clé ou au secret dans le coffre, utilisez la commande `az keyvault set-policy` .

Par exemple, si le nom de votre coffre est ContosoKeyVault, que l'application a un ID d’application 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed et que vous souhaitez autoriser l'application à déchiffrer et à signer avec des clés dans le coffre, exécutez la commande suivante :

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Pour autoriser cette même application à lire les secrets de votre coffre, saisissez la commande suivante :

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="bkmk_KVperCLI"></a>Définissez les stratégies d'accès avancé du coffre de clés 
Utilisez [mise à jour du paramètre keyvault az](/cli/azure/keyvault#az-keyvault-update) pour activer des stratégies avancées pour le coffre de clés. 

 Activez Key Vault pour le déploiement : autorise les machines virtuelles à récupérer des certificats stockés en tant que secrets dans le coffre.
 ```azurecli
 az keyvault update --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --enabled-for-deployment 'true'
 ``` 

Activer Key Vault pour le chiffrement de disque : requis lors de l’utilisation du coffre pour le chiffrement du disque Azure.

 ```azurecli
 az keyvault update --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --enabled-for-disk-encryption 'true'
 ```  

Activer Key Vault pour le déploiement d’un modèle : permet au gestionnaire des ressources de récupérer des secrets dans le coffre.
 ```azurecli 
 az keyvault update --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --enabled-for-template-deployment 'true'
 ```

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Si vous souhaitez utiliser un module de sécurité matériel (HSM)

Pour une meilleure garantie, vous pouvez importer ou générer des clés à partir des modules de sécurité matériels (HSM) qui ne franchissent jamais les limites HSM. Les modules HSM bénéficient d’une validation FIPS 140-2 de niveau 2. Si cette exigence ne s’applique pas à vous, ignorez cette section et accédez à [Supprimer le coffre de clés et les clés et secrets associés](#delete-the-key-vault-and-associated-keys-and-secrets).

Pour créer ces clés protégées par HSM, vous devez disposer d'un abonnement de coffre qui prend en charge les clés protégées par HSM.

Lorsque vous créez le coffre de clés, ajoutez le paramètre « SKU » :

```azurecli
az keyvault create --name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```

Vous pouvez ajouter des clés protégées par logiciel (comme indiqué plus haut) et des clés protégées par HSM dans ce coffre. Pour créer une clé protégée par HSM, définissez le paramètre Destination sur « HSM » :

```azurecli
az keyvault key create --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --protection 'hsm'
```

Vous pouvez utiliser la commande suivante pour importer une clé à partir d’un fichier pem sur votre ordinateur. Cette commande importe la clé dans les modules de sécurité matériels dans le service Key Vault :

```azurecli
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --protection 'hsm' --pem-password 'PaSSWORD'
```

La commande suivante importe un package BYOK (Bring Your Own Key, Apporter votre propre clé). Cela vous permet de générer votre clé dans votre module de sécurité matériel local et de la transférer vers les modules de sécurité matériels du service Key Vault, sans que la clé quitte la limite HSM :

```azurecli
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --protection 'hsm'
```

Pour plus d’instructions sur la génération de ce package BYOK, consultez la page [Génération et transfert de clés protégées par HSM pour le coffre de clés Azure](key-vault-hsm-protected-keys.md).

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Supprimer le coffre de clés et les clés et secrets associés

Si vous n’avez plus besoin ni du coffre de clés, ni des clés ou des secrets qu’il contient, vous pouvez le supprimer à l’aide de la commande `az keyvault delete` :

```azurecli
az keyvault delete --name 'ContosoKeyVault'
```

Vous pouvez également supprimer un groupe de ressources Azure, qui inclut le coffre de clés et les autres ressources incluses dans ce groupe :

```azurecli
az group delete --name 'ContosoResourceGroup'
```

## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Autres interfaces de ligne de commande interplateformes Azure

Autres commandes pouvant être utiles pour la gestion d’Azure Key Vault.

Cette commande permet d’obtenir un affichage sous forme de tableau de l’ensemble des clés et des propriétés sélectionnées :

```azurecli
az keyvault key list --vault-name 'ContosoKeyVault'
```

Cette commande affiche la liste complète des propriétés pour la clé spécifiée :

```azurecli
az keyvault key show --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'
```

Cette commande permet d’obtenir un affichage sous forme de tableau de l’ensemble des secrets et des propriétés sélectionnées.

```azurecli
az keyvault secret list --vault-name 'ContosoKeyVault'
```

Voici un exemple montrant comment supprimer une clé spécifique :

```azurecli
az keyvault key delete --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'
```

Voici un exemple montrant comment supprimer un secret spécifique :

```azurecli
az keyvault secret delete --vault-name 'ContosoKeyVault' --name 'SQLPassword'
```

## <a name="next-steps"></a>Étapes suivantes

- Pour accéder à une documentation de référence complète sur l’interface Azure CLI pour les commandes Key Vault, consultez le document de [référence sur l’interface de ligne de commande Key Vault](/cli/azure/keyvault).

- Pour les références de programmation, consultez le [guide du développeur de coffre de clés Azure](key-vault-developers-guide.md).

- Pour plus d'informations sur Azure Key Vault et les modules HSM, consultez [Utilisation de clés protégées par HSM avec Azure Key Vault](key-vault-hsm-protected-keys.md).
