---
title: Comment cibler des versions du runtime Azure Functions
description: Azure Functions prend en charge plusieurs versions du runtime. Découvrez comment spécifier la version du runtime d’une application de fonction hébergée dans Azure.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: glenga
ms.openlocfilehash: 6d89746c0a2d4642e5025789d352803195c0a3b9
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886805"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Comment cibler des versions du runtime Azure Functions

Une application de fonction s’exécute sur une version spécifique du runtime Azure Functions. Il existe deux versions majeures : [1.x et 2.x](functions-versions.md). Par défaut, les applications de fonction sont créées avec version 2.x du runtime. Cet article explique comment configurer une application de fonction dans Azure pour qu’elle s’exécute sur la version de votre choix. Pour plus d’informations sur la façon de configurer un environnement de développement local pour une version spécifique, consultez [Coder et tester Azure Functions localement](functions-run-local.md).

> [!NOTE]
> Vous ne pouvez pas modifier la version du runtime pour une application de fonction qui a une ou plusieurs fonctions. Vous devez utiliser le portail Azure pour modifier la version du runtime.

## <a name="automatic-and-manual-version-updates"></a>Mises à jour de versions automatiques et manuelles

Azure Functions vous permet de cibler une version spécifique du runtime en utilisant le paramètre d’application `FUNCTIONS_EXTENSION_VERSION` dans une application de fonction. L’application de fonction est conservée sur la version majeure spécifiée jusqu’à ce que vous décidiez de passer à une nouvelle version.

Si vous spécifiez uniquement la version majeure (« ~2 » pour 2.x ou « ~1 » pour 1.x), l’application de fonction est automatiquement mise à jour vers les nouvelles versions mineures du runtime quand elles deviennent disponibles. Les nouvelles versions mineures n’introduisent pas de changements importants. Si vous spécifiez une version mineure (par exemple, « 2.0.12345 »), l’application de fonction est maintenue sur cette version jusqu’à ce que vous la changiez explicitement.

Quand une nouvelle version est disponible publiquement, une invite dans le portail vous donne la possibilité de basculer vers cette version. Après le passage à une nouvelle version, vous avez toujours la possibilité d’utiliser le paramètre d’application `FUNCTIONS_EXTENSION_VERSION` pour revenir à une version précédente.

Un changement de version du runtime provoque un redémarrage de l’application de fonction.

Les valeurs que vous pouvez définir dans le paramètre d’application `FUNCTIONS_EXTENSION_VERSION` pour activer les mises à jour automatiques sont actuellement « ~1 » pour le runtime 1.x et « ~2 » pour le runtime 2.x.

## <a name="view-and-update-the-current-runtime-version"></a>Afficher et mettre à jour la version actuelle du runtime

Appliquez la procédure suivante pour afficher la version du runtime utilisée actuellement par une application de fonction.

1. Dans le [portail Azure](https://portal.azure.com), accédez à une application de fonction.

1. Sous **Fonctionnalités configurées**, choisissez **Paramètres de l’application de fonction**.

    ![Sélectionnez Paramètres de l’application de fonction](./media/set-runtime-version/add-update-app-setting.png)

1. Dans l’onglet **Paramètres de l’application de fonction**, trouvez la **Version du runtime**. Prenez note de la version spécifique du runtime ainsi que de la version majeure souhaitée. Dans l’exemple ci-dessous, la version est définie sur `~2`.

   ![Sélectionnez Paramètres de l’application de fonction](./media/set-runtime-version/function-app-view-version.png)

1. Pour conserver votre application de fonction sur la version 1.x du runtime, choisissez  **~1** sous **Version du runtime**. Ce commutateur est désactivé lorsque votre application comporte des fonctions.

1. Si vous avez modifié la version du runtime, revenez à l’onglet **Vue d’ensemble** et choisissez l’option **Redémarrer** pour relancer l’application.  L’application de fonction redémarre avec la version 1.x du runtime, et les modèles de la version 1.x sont utilisés lorsque vous créez des fonctions.

## <a name="view-and-update-the-runtime-version-using-azure-cli"></a>Afficher et mettre à jour la version du runtime à l’aide de l’interface de ligne de commande Azure

Vous pouvez aussi afficher et définir l’élément `FUNCTIONS_EXTENSION_VERSION` à partir de l’interface de ligne de commande Azure.

>[!NOTE]
>Étant donné que la version du runtime peut influer sur les autres paramètres, vous devez modifier la version dans le portail. Le portail crée automatiquement les mises à jour nécessaires (par exemple, pile runtime et version de Node.js) lorsque vous modifiez des versions du runtime.  

À l’aide de l’interface de ligne de commande Azure, affichez la version actuelle du runtime avec la commande [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

Dans ce code, remplacez `<function_app>` par le nom de votre application de fonction. Remplacez également `<my_resource_group>` par le nom du groupe de ressources de votre application de fonction. 

Observez `FUNCTIONS_EXTENSION_VERSION` dans la sortie suivante, tronquée pour une meilleure lisibilité :

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

Vous pouvez mettre à jour le paramètre `FUNCTIONS_EXTENSION_VERSION` dans l’application de fonction à l’aide de la commande [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Remplacez `<function_app>` par le nom de votre application de fonction. Remplacez également `<my_resource_group>` par le nom du groupe de ressources de votre application de fonction. Par ailleurs, remplacez `<version>` par une version valide du runtime 1.x ou `~2` pour la version 2.x.

Vous pouvez exécuter cette commande à partir de [Azure Cloud Shell](../cloud-shell/overview.md) en choisissant **Essayer** dans l’exemple de code qui précède. Vous pouvez également utiliser [Azure CLI en local](/cli/azure/install-azure-cli) pour exécuter cette commande après avoir lancé la commande [az login](/cli/azure/reference-index#az-login) pour vous connecter.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Cibler le runtime 2.0 dans votre environnement de développement local](functions-run-local.md)

> [!div class="nextstepaction"]
> [Consulter les notes de publication pour les versions du runtime](https://github.com/Azure/azure-webjobs-sdk-script/releases)
