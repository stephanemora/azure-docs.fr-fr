---
title: Tutoriel sur la rotation des ressources avec deux jeux d’informations d’identification
description: Utilisez ce tutoriel pour découvrir comment automatiser la rotation d’un secret pour des ressources qui utilisent deux jeux d’informations d’authentification.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: 5da31d45e068f414c8afa38bcb46cdf1f790a9e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843275"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-two-sets-of-authentication-credentials"></a>Automatiser la rotation d’un secret pour des ressources dotées de deux jeux d’informations d’authentification

La meilleure façon de s’authentifier auprès des services Azure consiste à utiliser une [identité managée](../general/authentication.md). Il existe toutefois des scénarios dans lesquels cette approche est inappropriée. Dans ces cas-là, des clés d’accès ou des mots de passe sont utilisés. Les clés d’accès et les mots de passe doivent fréquemment faire l’objet d’une rotation.

Ce tutoriel montre comment automatiser la rotation régulière des secrets pour les bases de données et les services qui utilisent deux jeux d’informations d’authentification. Plus précisément, ce tutoriel permute les clés de compte de stockage Azure stockées dans Azure Key Vault en tant que secrets à l’aide d’une fonction déclenchée par une notification Azure Event Grid. :

> [!NOTE]
> Les clés de compte de stockage peuvent être gérées automatiquement dans Key Vault en fournissant des jetons de signature d’accès partagé pour l’accès délégué au compte de stockage. Certains services requièrent une chaîne de connexion de compte de stockage avec clé d’accès et, pour ce scénario, cette solution est recommandée.

![Diagramme de la solution de permutation](../media/secrets/rotation-dual/rotation-diagram.png)

Dans la solution ci-dessus, Azure Key Vault stocke les clés d’accès individuelles de compte de stockage sous la forme de versions du même secret en alternant entre la clé primaire et la clé secondaire dans les versions qui se suivent. Comme une clé d’accès est stockée dans la dernière version du secret, la clé secondaire est régénérée et ajoutée à Key Vault en tant que nouvelle et dernière version du secret. Cette solution fournit aux applications un cycle de rotation complet pour actualiser jusqu’à la clé régénérée la plus récente. 

1. 30 jours avant la date d’expiration d’un secret, Key Vault publie l’événement « Expiration proche » sur Event Grid.
1. Event Grid vérifie les abonnements aux événements et utilise HTTP POST pour appeler le point de terminaison de l’application de fonction abonné à l’événement.
1. L’application de fonction identifie la clé secondaire (autre que la dernière clé) et appelle le compte de stockage pour la régénérer.
1. L’application de fonction ajoute la nouvelle clé régénérée à Azure Key Vault en tant que nouvelle version du secret.

## <a name="prerequisites"></a>Prérequis
* Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure Key Vault
* Deux comptes de stockage Azure

Vous pouvez utiliser le lien de déploiement ci-dessous, si vous n’avez pas de coffre de clés ou de comptes de stockage existants :

[![Image représentant un bouton intitulé « Déployer sur Azure »](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. Sous **Groupe de ressources**, sélectionnez **Créer**. Nommez le groupe **akvrotation** et cliquez sur **OK**.
1. Sélectionnez **Vérifier + créer**.
1. Sélectionnez **Créer**

    ![Créer un groupe de ressources](../media/secrets/rotation-dual/dual-rotation-1.png)

Vous disposez maintenant d’un coffre de clés et de deux comptes de stockage. Vous pouvez vérifier cette configuration dans l’interface de ligne de commande (CLI) Azure en exécutant la commande suivante :

```azurecli
az resource list -o table -g akvrotation
```

Le résultat doit ressembler à la sortie suivante :

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-storage-account-key-rotation-function"></a>Créer et déployer la fonction de rotation de clés de compte de stockage
> [!IMPORTANT]
> Le modèle ci-dessous nécessite que Key Vault, le compte Stockage Azure et Azure Function se trouvent dans le même groupe de ressources

Ensuite, créez une application de fonction avec une identité managée par le système, en plus des autres composants nécessaires, et déployez les fonctions de rotation de clés de compte de stockage.

Les fonctions de rotation de l’application de fonction requièrent les composants et la configuration suivants :
- Un plan Azure App Service
- Un compte de stockage requis pour la gestion des déclencheurs de l’application de fonction
- Une stratégie d’accès pour accéder aux secrets dans Key Vault
- Affectation du rôle de service d’opérateur de clé de compte de stockage à l’application de fonction pour accéder aux clés d’accès de compte de stockage
- Fonctions de rotation de clés de compte de stockage avec déclencheur d’événement et déclencheur http (rotation à la demande)
- Abonnement aux événements EventGrid pour l’événement **SecretNearExpiry**

1. Sélectionnez le lien de déploiement d’un modèle Azure : 

   [![Image représentant un bouton intitulé « Déployer sur Azure »](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json)

1. Dans la liste **Groupe de ressources**, sélectionnez **akvrotation**.
1. Dans **Nom du compte de stockage**, tapez le nom du compte de stockage doté des clés d’accès auxquelles appliquer la rotation.
1. Dans **Nom du coffre de clés**, tapez le nom du coffre de clés.
1. Dans **Nom de l’application de fonction**, tapez le nom de l’application de fonction.
1. Dans **Nom du secret**, tapez le nom du secret dans lequel les clés d’accès seront stockées.
1. Dans **URL du référentiel**, tapez l’emplacement GitHub du code de fonction ( **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** ).
1. Sélectionnez **Vérifier + créer**.
1. Sélectionnez **Créer**

   ![Vérification et création du premier compte de stockage](../media/secrets/rotation-dual/dual-rotation-2.png)

Après avoir effectué les étapes précédentes, vous disposez d’un compte de stockage, d’une batterie de serveurs, d’une application de fonction et d’insights sur l’application. Une fois le déploiement terminé, vous devriez voir l’écran ci-dessous : ![Déploiement terminé](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> En cas de problèmes, vous pouvez cliquer sur **Redéployer** pour terminer le déploiement des composants restants.


Les modèles de déploiement et le code des fonctions de rotation se trouvent sur [GitHub](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-storage-account-access-key-to-key-vault"></a>Ajouter une clé d’accès de compte de stockage dans Key Vault

Commencez par définir votre stratégie d’accès pour accorder des autorisations *Gérer les secrets* aux utilisateurs :

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Vous pouvez maintenant créer un secret avec une clé d’accès de compte de stockage comme valeur. Vous avez également besoin de l’ID de ressource du compte de stockage, de la période de validité du secret et de l’ID de clé à ajouter au secret, afin que la fonction de rotation puisse régénérer la clé dans le compte de stockage.

Récupérez l’ID de ressource du compte de stockage. La valeur se trouve sous la propriété `id`.
```azurecli
az storage account show -n akvrotationstorage
```

Listez les clés d’accès de compte de stockage pour récupérer les valeurs des clés.

```azurecli
az storage account keys list -n akvrotationstorage 
```

Renseignez les valeurs récupérées pour **key1Value** et **storageAccountResourceId**.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

La création d’un secret avec une date d’expiration proche entraîne la publication d’un événement `SecretNearExpiry` dans un délai de quelques minutes, qui déclenche à son tour la fonction de rotation du secret.

Vous pouvez vérifier que les clés d’accès sont régénérées en récupérant et en comparant les clés de compte de stockage et le secret Key Vault.

Vous pouvez afficher les informations secrètes à l’aide de la commande ci-dessous :
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
Comme vous pouvez le constater, `CredentialId` est mis à jour (autre `keyName`) et `value` est régénéré. ![Sortie de az keyvault secret show pour le premier compte de stockage](../media/secrets/rotation-dual/dual-rotation-4.png)

Récupérez les clés d’accès pour valider la valeur.
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Sortie de az storage account keys list pour le premier compte de stockage](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-additional-storage-accounts-for-rotation"></a>Ajouter des comptes de stockage supplémentaires pour la rotation

La même application de fonction peut être réutilisée pour assurer la rotation de plusieurs comptes de stockage. 

L’ajout de clés de compte de stockage supplémentaires pour la rotation à une fonction existante requiert :
- L’affectation du rôle de service d’opérateur de clé de compte de stockage à l’application de fonction pour accéder aux clés d’accès de compte de stockage
- L’abonnement aux événements EventGrid pour l’événement **SecretNearExpiry**

1. Sélectionnez le lien de déploiement d’un modèle Azure : 

   [![Image représentant un bouton intitulé « Déployer sur Azure »](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscription%2Fazuredeploy.json)

1. Dans la liste **Groupe de ressources**, sélectionnez **akvrotation**.
1. Dans **Nom du compte de stockage**, tapez le nom du compte de stockage doté des clés d’accès auxquelles appliquer la rotation.
1. Dans **Nom du coffre de clés**, tapez le nom du coffre de clés.
1. Dans **Nom de l’application de fonction**, tapez le nom de l’application de fonction.
1. Dans **Nom du secret**, tapez le nom du secret dans lequel les clés d’accès seront stockées.
1. Sélectionnez **Vérifier + créer**.
1. Sélectionnez **Créer**

   ![Vérification et création du deuxième compte de stockage](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Ajouter une autre clé d’accès de compte de stockage dans Key Vault

Récupérez l’ID de ressource du compte de stockage. La valeur se trouve sous la propriété `id`.
```azurecli
az storage account show -n akvrotationstorage2
```

Listez les clés d’accès de compte de stockage pour récupérer la valeur key2.

```azurecli
az storage account keys list -n akvrotationstorage2 
```

Renseignez les valeurs récupérées pour **key2Value** et **storageAccountResourceId**.

```azurecli
tomorrowDate=`date -d tomorrow -Iseconds -u | awk -F'+' '{print $1"Z"}'`
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Affichez les informations de secret à l’aide de la commande ci-dessous :
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
Comme vous pouvez le constater, `CredentialId` est mis à jour (autre `keyName`) et `value` est régénéré. ![Sortie de az keyvault secret show pour le deuxième compte de stockage](../media/secrets/rotation-dual/dual-rotation-8.png)

Récupérez les clés d’accès pour valider la valeur.
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Sortie de az storage account keys list pour le deuxième compte de stockage](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="available-key-vault-dual-credential-rotation-functions"></a>Fonctions de rotation des informations d’identification doubles Key Vault disponibles

- [Compte de stockage](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Cache Redis](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="learn-more"></a>En savoir plus
- Présentation : [Supervision de Key Vault avec Azure Event Grid](../general/event-grid-overview.md)
- Procédure : [Créer votre première fonction sur le Portail Azure](../../azure-functions/functions-create-first-azure-function.md)
- Procédure : [Recevoir un e-mail en cas de changement d’un secret de coffre de clés](../general/event-grid-logicapps.md)
- [Schéma des événements Azure Event Grid pour Azure Key Vault](../../event-grid/event-schema-key-vault.md)
