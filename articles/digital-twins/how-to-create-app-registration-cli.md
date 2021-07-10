---
title: Créer une inscription d’application (interface CLI)
titleSuffix: Azure Digital Twins
description: Découvrez comment créer une inscription d’application Azure AD en tant qu’option d’authentification pour les applications clientes en utilisant l’interface CLI.
author: baanders
ms.author: baanders
ms.date: 5/13/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 80d34833397e8ca1cb5cb14acd869597decd2d48
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110989461"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins-cli"></a>Créer une inscription d’application à utiliser avec Azure Digital Twins (interface CLI)

[!INCLUDE [digital-twins-create-app-registration-selector.md](../../includes/digital-twins-create-app-registration-selector.md)]

Lors de l’utilisation d’une instance Azure Digital Twins, il est courant d’interagir avec cette instance par le biais d’applications clientes, comme une application cliente personnalisée ou un exemple d’application comme [Azure Digital Twins Explorer](quickstart-azure-digital-twins-explorer.md). Ces applications doivent s’authentifier auprès d’Azure Digital Twins pour interagir avec, et certains des [mécanismes d’authentification](how-to-authenticate-client.md) que les applications peuvent utiliser impliquent une **inscription d’application** [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md).

Cela n’est pas obligatoire pour tous les scénarios d’authentification. Toutefois, si vous utilisez une stratégie d’authentification ou un exemple de code qui nécessite une inscription d’application, cet article vous montre comment en définir une avec [Azure CLI](/cli/azure/what-is-azure-cli). Il explique également comment [collecter les valeurs importantes](#collect-important-values) dont vous aurez besoin pour utiliser l’inscription de l’application afin de vous authentifier.

## <a name="azure-ad-app-registrations"></a>Inscriptions d’applications Azure AD

[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) est le service Microsoft basé sur le cloud qui gère les identités et les accès. La configuration d’une **inscription d’application** dans Azure AD est un moyen d’accorder à une application cliente l’accès à Azure Digital Twins.

Cette inscription d’application est l’emplacement où vous configurez les autorisations d’accès aux [API d’Azure Digital Twins](concepts-apis-sdks.md). Plus tard, les applications clientes peuvent s’authentifier auprès de l’inscription d’application à l’aide des **valeurs d’ID client et de locataire** de l’inscription, et ainsi recevoir les autorisations d’accès configurées aux API.

>[!TIP]
> Vous préférerez peut-être configurer une nouvelle inscription d’application chaque fois que vous en aurez besoin, *ou* pour effectuer cette opération une seule fois, en établissant une inscription d’application unique qui sera partagée entre tous les scénarios qui l’exigent.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-manifest"></a>Créer un manifeste

Tout d’abord, créez un fichier contenant certaines informations de service dont votre inscription d’application aura besoin pour accéder aux API Azure Digital Twins. Plus tard, vous transmettrez ce fichier lors de la création de l’inscription d’application, pour configurer les autorisations Azure Digital Twins.

Créez sur votre ordinateur un fichier .json nommé **manifest.json**. Copiez ce texte dans le fichier :

```json
[
    {
        "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
        "resourceAccess": [
            {
                "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
                "type": "Scope"
            }
        ]
    }
]
```

La valeur statique `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` est l’ID de ressource pour le point de terminaison du service Azure Digital Twins, dont votre inscription d’application aura besoin pour accéder aux API Azure Digital Twins.
 
Enregistrez le fichier terminé.

### <a name="upload-to-cloud-shell"></a>Upload to Cloud Shell (Charger sur Cloud Shell)

Ensuite, chargez le fichier manifeste que vous venez de créer dans Cloud Shell, afin de pouvoir y accéder dans les commandes Cloud Shell lors de la configuration de l’inscription d’application.

Pour charger le fichier, accédez à la fenêtre Cloud Shell dans votre navigateur. Sélectionnez l’icône « Charger/Télécharger des fichiers » et choisissez « Charger ».

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Capture d’écran d’Azure Cloud Shell. L’icône de chargement est mise en évidence.":::

Accédez au fichier **manifest.json** sur votre machine et sélectionnez « Ouvrir ». Cela chargera le fichier à la racine de votre stockage Cloud Shell.

## <a name="create-the-registration"></a>Création de l’inscription

Dans cette section, vous allez exécuter une commande Cloud Shell pour créer une inscription d’application avec les paramètres suivants :
* Nom de votre choix
* Disponible uniquement pour les comptes dans l’annuaire par défaut (un seul locataire)
* URL de réponse web de `http://localhost`
* Autorisations de lecture/écriture sur les API Azure Digital Twins

Exécutez la commande suivante pour créer l’inscription :

```azurecli-interactive
az ad app create --display-name <app-registration-name> --available-to-other-tenants false --reply-urls http://localhost --native-app --required-resource-accesses "@manifest.json"
```

La sortie de la commande contient des informations sur l’inscription d’application que vous avez créée. 

## <a name="verify-success"></a>Vérifier la réussite de l’exécution

Vous pouvez vérifier que les autorisations Azure Digital Twins ont été accordées en recherchant les champs suivants dans la sortie de la commande `az ad app create` et en confirmant que leurs valeurs correspondent à ce qui est indiqué dans la capture d’écran ci-dessous :

:::image type="content" source="media/how-to-create-app-registration/cli-required-resource-access.png" alt-text="Capture d’écran de la sortie de Cloud Shell de la commande de création d’inscription d’application. Les éléments sous « requiredResourceAccess » sont mis en évidence : il existe une valeur « resourceAppId » de 0b07f429-9f4b-4714-9392-cc5e8e80c8b0 et une valeur « resourceAccess > id » de 4589bd03-58cb-4e6c-b17f-b580e39652f8.":::

Vous pouvez également vérifier que l’inscription d’application a été correctement créée à l’aide du portail Azure. Pour obtenir des instructions pour le portail, consultez [Vérifier la réussite (portail)](how-to-create-app-registration-portal.md#verify-success).

## <a name="collect-important-values"></a>Collecter les valeurs importantes

Ensuite, collectez certaines valeurs importantes sur l’inscription d’application dont vous aurez besoin pour utiliser l’inscription d’application afin d’authentifier une application cliente. Ces valeurs incluent :
* **Nom de la ressource**
* **ID client**
* **ID locataire**
* **Clé secrète client**

Pour utiliser Azure Digital Twins, le **nom de la ressource** est `http://digitaltwins.azure.net`.

Les sections suivantes décrivent comment trouver les autres valeurs.

### <a name="collect-client-id-and-tenant-id"></a>Collecter l’ID de client et l’ID de locataire

Pour utiliser l’inscription d’application pour l’authentification, vous devrez peut-être fournir son **ID d’application (client)** et son **ID d’annuaire (locataire)** . Dans cette section, vous allez collecter ces valeurs afin de  pouvoir les enregistrer et les utiliser chaque fois qu’elles sont nécessaires.

Vous trouverez ces deux valeurs dans la sortie de la commande `az ad app create`.

ID d’application (client) :

:::image type="content" source="media/how-to-create-app-registration/cli-app-id.png" alt-text="Capture d’écran de la sortie de Cloud Shell de la commande de création d’inscription d’application. La valeur appId est mise en évidence.":::

ID d’annuaire (locataire) :

:::image type="content" source="media/how-to-create-app-registration/cli-tenant-id.png" alt-text="Capture d’écran de la sortie de Cloud Shell de la commande de création d’inscription d’application. La valeur GUID dans odata.metadata est mise en évidence.":::

### <a name="collect-client-secret"></a>Collecter le secret client

Pour créer un **secret client** pour votre inscription d’application, vous avez besoin de la valeur d’ID client de votre inscription d’application indiquée dans la section précédente. Utilisez la valeur de la commande CLI suivante pour créer un nouveau secret :

```azurecli-interactive
az ad app credential reset --id <client-ID> --append
```

Vous pouvez également ajouter des paramètres facultatifs à cette commande pour spécifier une description des informations d’identification, une date de fin et d’autres détails. Pour plus d’informations sur la commande et ses paramètres supplémentaires, consultez la [documentation sur az ad app credential reset](/cli/azure/ad/app/credential?view=azure-cli-latest&preserve-view=true#az_ad_app_credential_reset).

Les informations générées par cette commande décrivent le secret client que vous avez créé. Copiez la valeur de `password` à utiliser lorsque vous avez besoin du secret client pour l’authentification.

:::image type="content" source="media/how-to-create-app-registration/cli-client-secret.png" alt-text="Capture d’écran de la sortie de Cloud Shell de la commande de création d’inscription d’application. La valeur password est mise en évidence.":::

>[!IMPORTANT]
>Veillez à copier la valeur maintenant et à la stocker en lieu sûr, car vous ne pourrez plus y accéder par la suite. Si vous perdez cette valeur, vous devrez recréer un nouveau secret.

## <a name="other-possible-steps-for-your-organization"></a>Autres étapes possibles pour votre organisation

Il est possible que votre organisation exige des actions supplémentaires de la part des propriétaires/administrateurs d’abonnement pour configurer correctement une inscription d’application. Les étapes requises peuvent varier en fonction des paramètres spécifiques de votre organisation.

Voici quelques activités courantes qu’un propriétaire ou administrateur pour l’abonnement peut devoir effectuer.
* Accordez le consentement administrateur pour l’inscription d’application. Votre organisation peut avoir activé globalement l’option **Consentement administrateur requis** dans Azure AD pour toutes les inscriptions d’applications au sein de votre abonnement. Dans ce cas, le propriétaire/administrateur peut avoir besoin d’accorder des autorisations d’application ou déléguées supplémentaires.
* Activez l’accès client public en ajoutant `--set publicClient=true` à une commande de création ou de mise à jour pour l’inscription.
* Définissez des URL de réponse spécifiques pour l’accès web et au bureau à l’aide du paramètre `--reply-urls`. Pour plus d’informations sur l’utilisation de ce paramètre avec des commandes `az ad`, consultez la [documentation sur az ad app](/cli/azure/ad/app?view=azure-cli-latest&preserve-view=true).
* Autorisez les flux d’authentification OAuth2 implicites à l’aide du paramètre `--oauth2-allow-implicit-flow`. Pour plus d’informations sur l’utilisation de ce paramètre avec des commandes `az ad`, consultez la [documentation sur az ad app](/cli/azure/ad/app?view=azure-cli-latest&preserve-view=true).

Pour plus d’informations sur l’inscription d’applications et ses différentes options d’installation, consultez [Inscrire une application avec la plateforme d’identités Microsoft](/graph/auth-register-app-v2).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous configurez une inscription d’application Azure AD qui peut être utilisée pour authentifier des applications clientes avec les API Azure Digital Twins.

Vous lirez ensuite des informations sur les mécanismes d’authentification, y compris ceux qui utilisent les inscriptions d’applications et d’autres qui ne le font pas :
* [Guide pratique : Écrire le code d’authentification de l’application](how-to-authenticate-client.md)