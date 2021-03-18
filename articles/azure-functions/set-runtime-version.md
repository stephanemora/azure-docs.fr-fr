---
title: Comment cibler des versions du runtime Azure Functions
description: Azure Functions prend en charge plusieurs versions du runtime. Découvrez comment spécifier la version du runtime d’une application de fonction hébergée dans Azure.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: e9aa5546b5f07b724fe22bc1e20a2e97feb2aec2
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435560"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Comment cibler des versions du runtime Azure Functions

Une application de fonction s’exécute sur une version spécifique du runtime Azure Functions. Trois versions majeures sont disponibles : [3.x, 2.x et 1.x](functions-versions.md). Par défaut, les applications de fonction sont créées dans la version 3.x du runtime. Cet article explique comment configurer une application de fonction dans Azure pour qu’elle s’exécute sur la version de votre choix. Pour plus d’informations sur la façon de configurer un environnement de développement local pour une version spécifique, consultez [Coder et tester Azure Functions localement](functions-run-local.md).

La façon de cibler manuellement une version spécifique varie selon le système d’exploitation exécuté (Windows ou Linux).

## <a name="automatic-and-manual-version-updates"></a>Mises à jour de versions automatiques et manuelles

_Cette section ne s’applique pas en cas d’exécution de l’application de fonction [sur Linux](#manual-version-updates-on-linux)._

Azure Functions permet de cibler une version spécifique du runtime sur Windows en utilisant le paramètre d’application `FUNCTIONS_EXTENSION_VERSION` dans une application de fonction. L’application de fonction est conservée sur la version majeure spécifiée jusqu’à ce que vous décidiez de passer à une nouvelle version. Si vous spécifiez uniquement la version majeure, l’application de fonction est automatiquement mise à jour vers les nouvelles versions mineures du runtime quand elles deviennent disponibles. Les nouvelles versions mineures ne devraient pas introduire de changements importants. 

Si vous spécifiez une version mineure (par exemple, « 2.0.12345 »), l’application de fonction est maintenue sur cette version jusqu’à ce que vous la changiez explicitement. Les anciennes versions mineures sont régulièrement supprimées de l’environnement de production. Si votre version mineure est supprimée, votre application de fonction s’exécutera à nouveau dans la version la plus récente au lieu de la version définie dans `FUNCTIONS_EXTENSION_VERSION`. Pour cette raison, vous devez résoudre rapidement tout problème relatif à votre application de fonction qui nécessite une version mineure spécifique. Ensuite, vous pourrez de nouveau cibler la version principale. Les suppressions de versions mineures sont annoncées sur la page des [annonces App Service](https://github.com/Azure/app-service-announcements/issues).

> [!NOTE]
> Si vous l’épinglez à une version principale spécifique d’Azure Functions, puis tentez de publier sur Azure à l’aide de Visual Studio, une fenêtre de dialogue s’affiche vous invitant à mettre à jour vers la dernière version ou à annuler la publication. Pour éviter ce problème, ajoutez la propriété `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` dans votre fichier `.csproj`.

Quand une nouvelle version est disponible publiquement, une invite dans le portail vous donne la possibilité de basculer vers cette version. Après le passage à une nouvelle version, vous avez toujours la possibilité d’utiliser le paramètre d’application `FUNCTIONS_EXTENSION_VERSION` pour revenir à une version précédente.

Le tableau suivant précise les valeurs de `FUNCTIONS_EXTENSION_VERSION` pour chaque version principale, afin d’activer les mises à jour automatiques :

| Version principale | Valeur `FUNCTIONS_EXTENSION_VERSION` |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

Un changement de version du runtime provoque un redémarrage de l’application de fonction.

>[!NOTE]
>Les applications de fonction .NET épinglées à `~2.0` refuseront la mise à niveau automatique vers .NET Core 3.1. Pour plus d’informations, consultez [Considérations relatives à Functions v2.x](functions-dotnet-class-library.md#functions-v2x-considerations).  

## <a name="view-and-update-the-current-runtime-version"></a>Afficher et mettre à jour la version actuelle du runtime

_Cette section ne s’applique pas en cas d’exécution de l’application de fonction [sur Linux](#manual-version-updates-on-linux)._

Vous pouvez modifier la version du runtime utilisée par votre application de fonction. Compte tenu du risque de changements cassants, vous pouvez le faire avant d’avoir créé des fonctions dans votre application de fonction. 

> [!IMPORTANT]
> Même si la version du runtime est déterminée par le paramètre `FUNCTIONS_EXTENSION_VERSION`, vous devez uniquement effectuer cette modification sur le portail Azure et non en modifiant directement le paramètre. En effet, le portail valide les modifications et en effectue d’autres si nécessaire.

# <a name="portal"></a>[Portail](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> À l’aide du portail Azure, vous ne pouvez pas modifier la version du runtime pour une application de fonction qui contient déjà des fonctions.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Vous pouvez aussi afficher et définir l’élément `FUNCTIONS_EXTENSION_VERSION` à partir de l’interface de ligne de commande Azure.  

À l’aide de l’interface de ligne de commande Azure, affichez la version actuelle du runtime avec la commande [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings).

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

Vous pouvez mettre à jour le paramètre `FUNCTIONS_EXTENSION_VERSION` dans l’application de fonction à l’aide de la commande [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings).

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

Remplacez `<FUNCTION_APP>` par le nom de votre application de fonction. Remplacez également `<RESOURCE_GROUP>` par le nom du groupe de ressources de votre application de fonction. Remplacez également `<VERSION>` par une version spécifique, ou `~3`, `~2` ou `~1`.

Dans l’exemple de code précédent, choisissez **Essayer** pour exécuter la commande dans [Azure Cloud Shell](../cloud-shell/overview.md). Vous pouvez également exécuter [Azure CLI localement](/cli/azure/install-azure-cli) pour exécuter cette commande. Pour une exécution locale, vous devez d’abord exécuter la commande [az login](/cli/azure/reference-index#az-login) pour vous connecter.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour vérifier le runtime Azure Functions, utilisez l’applet de commande suivante : 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

Remplacez `<FUNCTION_APP>` par le nom de votre application de fonction et `<RESOURCE_GROUP>`. La valeur actuelle du paramètre `FUNCTIONS_EXTENSION_VERSION` est retournée dans la table de hachage.

Utilisez le script suivant pour modifier le runtime Functions :

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

Comme auparavant, remplacez `<FUNCTION_APP>` par le nom de votre application de fonction et `<RESOURCE_GROUP>` par le nom du groupe de ressources. Remplacez également `<VERSION>` par la version spécifique ou la version majeure, par exemple `~2` ou `~3`. Vous pouvez vérifier la valeur mise à jour du paramètre `FUNCTIONS_EXTENSION_VERSION` dans la table de hachage retournée. 

---

L’application de fonction redémarre une fois que la modification a été apportée au paramètre d’application.

## <a name="manual-version-updates-on-linux"></a>Mises à jour de versions manuelles sur Linux

Pour épingler une application de fonction Linux à une version d’hôte spécifique, on spécifie l’URL de l’image dans le champ « LinuxFxVersion » de la configuration du site. Par exemple, pour épingler une application de fonction Node 10 à la version d’hôte 3.0.13142 :

Pour les **applications Linux App Service/élastiques Premium**, définissez `LinuxFxVersion` sur `DOCKER|mcr.microsoft.com/azure-functions/node:3.0.13142-node10-appservice`.

Pour les **applications de consommation Linux**, définissez `LinuxFxVersion` sur `DOCKER|mcr.microsoft.com/azure-functions/mesh:3.0.13142-node10`.

# <a name="portal"></a>[Portail](#tab/portal)

L’affichage et la modification des paramètres de configuration de site pour les applications de fonction ne sont pas pris en charge dans le portail Azure. Utilisez plutôt Azure CLI.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Vous pouvez afficher et définir `LinuxFxVersion` dans Azure CLI.  

Pour afficher la version du runtime actuelle, utilisez la commande [az functionapp config show](/cli/azure/functionapp/config).

```azurecli-interactive
az functionapp config show --name <function_app> \
--resource-group <my_resource_group> --query 'linuxFxVersion' -o tsv
```

Dans ce code, remplacez `<function_app>` par le nom de votre application de fonction. Remplacez également `<my_resource_group>` par le nom du groupe de ressources de votre application de fonction. La valeur actuelle de `linuxFxVersion` est retournée.

Pour mettre à jour le paramètre `linuxFxVersion` dans l’application de fonction, utilisez la commande [az functionapp config set](/cli/azure/functionapp/config).

```azurecli-interactive
az functionapp config set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--linux-fx-version <LINUX_FX_VERSION>
```

Remplacez `<FUNCTION_APP>` par le nom de votre application de fonction. Remplacez également `<RESOURCE_GROUP>` par le nom du groupe de ressources de votre application de fonction. Remplacez également `<LINUX_FX_VERSION>` par la valeur d’une image, comme expliqué ci-dessus.

Vous pouvez exécuter cette commande à partir de [Azure Cloud Shell](../cloud-shell/overview.md) en choisissant **Essayer** dans l’exemple de code qui précède. Vous pouvez également utiliser [Azure CLI en local](/cli/azure/install-azure-cli) pour exécuter cette commande après avoir lancé la commande [az login](/cli/azure/reference-index#az-login) pour vous connecter.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour le moment, Azure PowerShell ne peut pas être utilisé pour définir `linuxFxVersion`. Utilisez plutôt Azure CLI.

---

L’application de fonction redémarre une fois la modification apportée à la configuration du site.

> [!NOTE]
> Pour les applications qui s’exécutent dans un plan Consommation, le fait de définir `LinuxFxVersion` sur une image donnée peut augmenter la durée des démarrages à froid. Cela est dû au fait que l’épinglage à une image donnée empêche Functions d’utiliser des optimisations de démarrage à froid. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Cibler le runtime 2.0 dans votre environnement de développement local](functions-run-local.md)

> [!div class="nextstepaction"]
> [Consulter les notes de publication pour les versions du runtime](https://github.com/Azure/azure-webjobs-sdk-script/releases)
