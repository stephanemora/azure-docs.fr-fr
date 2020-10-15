---
title: Configurer une instance et l’authentification (CLI)
titleSuffix: Azure Digital Twins
description: Découvrez comment configurer une instance du service Azure Digital Twins à l’aide de l’interface CLI
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0dfc86503f1b3aa648cb8c7cefe14fbd123f1459
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047503"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Configurer une instance Azure Digital Twins et l’authentification (interface CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Cet article explique comment **configurer une nouvelle instance Azure Digital Twins**, notamment la création de l’instance et la configuration de l’authentification. À l’issue de cet article, vous aurez une instance Azure Digital Twins prête pour la programmation.

Cette version de cet article suit ces étapes manuellement, une par une, à l’aide de l’interface CLI.
* Pour suivre ces étapes manuellement à l’aide du portail Azure, consultez la version de cet article relative au portail : [*Guide pratique : Configurer une instance et l’authentification (portail)* ](how-to-set-up-instance-portal.md).
* Pour exécuter une configuration automatisée à l’aide d’un exemple de script de déploiement, consultez la version avec script de cet article : [*Guide pratique : Configurer une instance et l’authentification (procédure scriptée)* ](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>Configurer une session Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Créer l’instance Azure Digital Twins

Dans cette section, vous allez **créer une nouvelle instance d’Azure Digital Twins** à l’aide de la commande Cloud Shell. Vous devrez fournir les éléments suivants :
* Un groupe de ressources dans lequel la déployer. Si vous n’avez pas encore choisi un groupe de ressources existant, vous pouvez en créer un maintenant avec cette commande :
    ```azurecli
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* Une région pour le déploiement. Pour connaître les régions qui prennent en charge Azure Digital Twins, consultez [*Produits Azure disponibles par région*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Un nom pour votre instance. Le nom de la nouvelle instance doit être unique dans la région pour votre abonnement (ce qui signifie que si votre abonnement a une autre instance Azure Digital Twins dans cette région, qui utilise déjà le nom que vous choisissez, vous devrez choisir un autre nom).

Utilisez ces valeurs dans la commande suivante pour créer l’instance :

```azurecli
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>Vérifier la réussite de l’exécution et collecter les valeurs importantes

Si l’instance a été créée avec succès, le résultat dans Cloud Shell ressemble à ceci, générant des informations sur la ressource que vous avez créée :

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="Fenêtre Commande montrant la création réussie d’un groupe de ressources et d’une instance Azure Digital Twins":::

Notez les valeurs *hostName*, *name* et *resourceGroup* de l’instance Azure Digital Twins fournies dans la sortie. Il s’agit de toutes les valeurs importantes dont vous pouvez avoir besoin quand vous continuez à travailler avec votre instance Azure Digital Twins pour configurer l’authentification et les ressources Azure associées. Si d’autres utilisateurs doivent programmer pour l’instance, vous devez partager ces valeurs avec eux.

> [!TIP]
> Vous pouvez afficher ces propriétés, ainsi que toutes les propriétés de votre instance, à tout moment en exécutant `az dt show --dt-name <your-Azure-Digital-Twins-instance>`.

Vous disposez maintenant d’une instance Azure Digital Twins opérationnelle. Ensuite, vous allez accorder les autorisations utilisateur Azure appropriées pour la gérer.

## <a name="set-up-user-access-permissions"></a>Configurer les autorisations d’accès utilisateur

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Utilisez la commande suivante pour attribuer le rôle (doit être exécutée par un utilisateur avec les [autorisations suffisantes](#prerequisites-permission-requirements) dans l’abonnement Azure). La commande nécessite de passer le *nom d’utilisateur principal* sur le compte Azure AD de l’utilisateur auquel le rôle doit être attribué. Dans la plupart des cas, cela correspond à l’adresse e-mail de l’utilisateur sur le compte Azure AD.

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

Les résultats de cette commande décrivent l’attribution de rôle que vous avez créée.

> [!NOTE]
> Si cette commande renvoie une erreur indiquant que l’interface CLI **ne trouve pas l’utilisateur ou le principal du service dans la base de données de graphes** :
>
> Attribuez le rôle en utilisant à la place l’*ID d’objet* de l’utilisateur. Cela peut se produire pour les utilisateurs disposant de [comptes Microsoft (MSA)](https://account.microsoft.com/account) personnels. 
>
> Utilisez la [page du portail Azure des utilisateurs Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) pour sélectionner le compte d’utilisateur et afficher ses détails. Copiez la valeur *ObjectID* de l’utilisateur :
>
> :::image type="content" source="media/includes/user-id.png" alt-text="Fenêtre Commande montrant la création réussie d’un groupe de ressources et d’une instance Azure Digital Twins" lightbox="media/includes/user-id.png":::
>
> Ensuite, répétez la commande 'role assignment list' en utilisant l’*ID d’objet* de l’utilisateur pour le paramètre `assignee` ci-dessus.

### <a name="verify-success"></a>Vérifier la réussite de l’exécution

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Vous disposez maintenant d’une instance Azure Digital Twins prête à l’emploi et des autorisations pour la gérer. Ensuite, vous allez configurer des autorisations pour qu’une application cliente y accède.

## <a name="set-up-access-permissions-for-client-applications"></a>Configurer des autorisations d’accès pour les applications clientes

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

Pour créer une inscription d’application, vous devez fournir les ID de ressource aux API d’Azure Digital Twins et les autorisations de base pour l’API.

Dans votre répertoire de travail, créez un fichier et entrez l’extrait de code JSON suivant pour configurer les informations suivantes : 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

Enregistrez ce fichier sous _**manifest.json**_.

> [!NOTE] 
> Il existe certains endroits où une chaîne « conviviale », lisible `https://digitaltwins.azure.net` peut être utilisée pour l’ID d’application de ressource Azure Digital Twins à la place du GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0`. Par exemple, de nombreux exemples dans cette documentation utilisent l’authentification avec la bibliothèque MSAL, et la chaîne conviviale peut être utilisée pour cela. Toutefois, au cours de cette étape de création de l’inscription de l’application, la forme GUID de l’ID est obligatoire, comme indiqué ci-dessus. 

Ensuite, vous allez télécharger ce fichier dans Cloud Shell. Dans la fenêtre de Cloud Shell, cliquez sur l’icône « Charger/Télécharger des fichiers », puis choisissez « Charger ».

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Fenêtre Commande montrant la création réussie d’un groupe de ressources et d’une instance Azure Digital Twins":::
Accédez au fichier *manifest.JSON* que vous venez de créer et appuyez sur « Ouvrir ».

Ensuite, exécutez la commande suivante pour créer une inscription d’application, avec une URL de réponse *Client public/natif (mobile et bureau)* de `http://localhost`. Remplacez les espaces réservés le cas échéant :

```azurecli
az ad app create --display-name <name-for-your-app-registration> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

Voici un extrait de la sortie de cette commande, qui montre des informations sur l’inscription que vous avez créée :

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/new-app-registration.png" alt-text="Fenêtre Commande montrant la création réussie d’un groupe de ressources et d’une instance Azure Digital Twins":::

### <a name="verify-success"></a>Vérifier la réussite de l’exécution

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Ensuite, vérifiez que les paramètres de votre fichier *manifest.json* téléchargé ont été correctement définis sur l’inscription. Pour ce faire, sélectionnez *Manifeste* dans la barre de menus pour afficher le code du manifeste de l’inscription de l’application. Faites défiler la fenêtre de code jusqu’en bas et recherchez les champs de votre fichier *manifest.json* sous `requiredResourceAccess` :

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

### <a name="collect-important-values"></a>Collecter les valeurs importantes

Ensuite, sélectionnez *Vue d’ensemble* dans la barre de menus pour afficher les détails de l’inscription de l’application :

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Fenêtre Commande montrant la création réussie d’un groupe de ressources et d’une instance Azure Digital Twins":::

Prenez note de *l’ID d’application (client)* et de *l’ID de répertoire (locataire)* affichés sur **votre** page. Ces valeurs seront requises ultérieurement pour [authentifier une application cliente par rapport aux API Azure Digital Twins](how-to-authenticate-client.md). Si vous n’êtes pas la personne chargée d’écrire du code pour de telles applications, vous devez partager ces valeurs avec la personne qui en sera chargée.

### <a name="other-possible-steps-for-your-organization"></a>Autres étapes possibles pour votre organisation

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Étapes suivantes

Testez les appels d’API REST individuels sur votre instance à l’aide des commandes CLI d’Azure Digital Twins : 
* [az dt reference](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*Guide pratique : Utiliser l’interface CLI d’Azure Digital Twins*](how-to-use-cli.md)

Vous pouvez également découvrir comment connecter votre application cliente à votre instance en écrivant le code d’authentification de l’application cliente :
* [*Guide pratique : Écrire le code d’authentification de l’application*](how-to-authenticate-client.md)