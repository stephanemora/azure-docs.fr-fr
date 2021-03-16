---
title: Déployer du contenu avec FTP/S
description: Découvrez comment déployer votre application dans Azure App Service avec FTP ou FTPS. Améliorez la sécurité d’un site web en désactivant le protocole FTP non chiffré.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 02/26/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: c7427a1f8f528fdf405b22c4e91941ea7a915ffa
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045800"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Déployer votre application dans Azure App Service avec FTP/S

Cet article vous explique comment utiliser FTP ou FTPS pour déployer votre application web, votre backend d’applications mobiles ou votre application API dans [Azure App Service](./overview.md).

Le point de terminaison FTP/S de votre application est déjà actif. Aucune configuration n’est nécessaire pour activer le déploiement FTP/S.

> [!NOTE]
> La page **Centre de développement (classique)** dans le portail Azure, qui est l’ancienne expérience de déploiement, sera déconseillée en mars 2021. Cette modification n’affecte aucun paramètre de déploiement existant dans votre application, et vous pouvez continuer à gérer le déploiement d’applications dans la page du **Centre de déploiement** .

## <a name="get-deployment-credentials"></a>Obtenir les informations d’identification de déploiement

1. Suivez les instructions de la rubrique [Configurer les informations d’identification de déploiement pour Azure App Service](deploy-configure-credentials.md) afin de copier les informations d’identification pour la portée de l’application ou de l’utilisateur. Vous pouvez vous connecter au point de terminaison FTP/S de votre application à l’aide de ces informations d’identification.

1. Élaborez le nom d’utilisateur FTP au format suivant, en fonction de votre choix de portée des informations d’identification :

    | Portée de l’application | Portée de l’utilisateur |
    | - | - |
    |`<app-name>\$<app-name>`|`<app-name>\<deployment-user>`|

    ---

    Dans App Service, le point de terminaison FTP/S est partagé entre les applications. Étant donné que les informations d’identification pour la portée de l’utilisateur ne sont pas liées à une ressource spécifique, vous devez ajouter devant le nom d’utilisateur pour la portée de l’utilisateur le nom de l’application, comme ci-dessus.

## <a name="get-ftps-endpoint"></a>Obtenir le point de terminaison FTP/S
    
# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Dans la page de gestion de votre application dans laquelle vous avez copié les informations d’identification (**Centre de déploiement** > **Informations d’identification FTP**), copiez le **Point de terminaison FTPS**.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Exécutez la commande [az webapp deployment list-publishing-profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles). L’exemple suivant utilise un [chemin d’accès JMES](https://jmespath.org/) pour extraire les points de terminaison FTP/S de la sortie.

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app-name> --resource-group <group-name> --query "[?ends_with(profileName, 'FTP')].{profileName: profileName, publishUrl: publishUrl}"
```

Chaque application a deux points de terminaison FTP/S. L’un est accessible en lecture-écriture, tandis que l’autre est en lecture seule (`profileName` contient `ReadOnly`) et destiné aux scénarios de récupération de données. Pour déployer des fichiers avec FTP, copiez l’URL du point de terminaison en lecture-écriture.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Exécutez la commande [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile). L’exemple suivant extrait le point de terminaison FTP/S de la sortie XML.

```azurepowershell-interactive
$xml = [xml](Get-AzWebAppPublishingProfile -Name <app-name> -ResourceGroupName <group-name> -OutputFile null)
$xml.SelectNodes("//publishProfile[@publishMethod=`"FTP`"]/@publishUrl").value
```

-----

## <a name="deploy-files-to-azure"></a>Déployer des fichiers sur Azure

1. Dans votre client FTP (par exemple, [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/) ou [WinSCP](https://winscp.net/index.php)), utilisez les informations de connexion que vous avez recueillies pour vous connecter à votre application.
2. Copiez vos fichiers et la structure de répertoire qui leur correspond dans le répertoire [ **/site/wwwroot** dans Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (ou dans le répertoire **/site/wwwroot/App_Data/Jobs/** pour WebJobs).
3. Accédez à l’URL de votre application pour vérifier que l’application s’exécute correctement. 

> [!NOTE] 
> Contrairement aux [déploiements basés sur Git](deploy-local-git.md) et au [déploiement Zip](deploy-zip.md), un déploiement FTP ne prend pas en charge l’automatisation de la génération, par exemple : 
>
> - restauration de dépendances (par exemple, des automatisations NuGet, NPM, PIP et Composer)
> - compilation de fichiers binaires .NET
> - génération du fichier web.config (dont voici un [exemple Node.js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Générez ces fichiers requis manuellement sur votre ordinateur local, puis déployez-les avec votre application.
>

## <a name="enforce-ftps"></a>Appliquer le protocole FTPS

Pour une sécurité renforcée, vous devez autoriser FTP sur TLS/SSL uniquement. Vous pouvez également désactiver FTP et FTPS si vous n’utilisez pas de déploiement FTP.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Dans la page des ressources de votre application dans le [portail Azure](https://portal.azure.com), sélectionnez **Configuration** > **Paramètres généraux** dans le volet de navigation gauche.

2. Pour désactiver le protocole FTP non chiffré, sélectionnez **FTPS uniquement** dans **État FTP**. Pour désactiver complètement les protocoles FTP et FTPS, sélectionnez **Désactivé**. Lorsque vous avez terminé, cliquez sur **Enregistrer**. Si vous utilisez **FTPS uniquement**, appliquez TLS 1.2 ou version ultérieure en accédant au panneau **Paramètres TLS/SSL** de votre application web. TLS 1.0 et 1.1 ne sont pas pris en charge avec **FTPS uniquement**.

    ![Désactiver le protocole FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Exécutez la commande [az webapp config set](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) avec l’argument `--ftps-state`.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <group-name> --ftps-state FtpsOnly
```

Les valeurs possibles pour `--ftps-state` sont `AllAllowed` (FTP et FTPS activés), `Disabled` (FTP et FTPS désactivés) et `FtpsOnly` (FTPS uniquement).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Exécutez la commande [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) avec le paramètre `-FtpsState`.

```azurepowershell-interactive
Set-AzWebApp -Name <app-name> -ResourceGroupName <group-name> -FtpsState FtpsOnly
```

Les valeurs possibles pour `--ftps-state` sont `AllAllowed` (FTP et FTPS activés), `Disabled` (FTP et FTPS désactivés) et `FtpsOnly` (FTPS uniquement).

-----

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Résoudre les problèmes d’un déploiement FTP

- [Comment puis-je résoudre les problèmes d’un déploiement FTP ?](#how-can-i-troubleshoot-ftp-deployment)
- [Je ne peux pas envoyer par FTP et publier mon code. Comment puis-je résoudre le problème ?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Comment puis-je me connecter à FTP dans Azure App Service via le mode passif ?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

#### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Comment puis-je résoudre les problèmes d’un déploiement FTP ?

La première étape de résolution des problèmes d’un déploiement FTP consiste à isoler un problème de déploiement d’un problème d’application à l’exécution.

Un problème de déploiement se produit généralement si aucun fichier ou des fichiers incorrects sont déployés sur votre application. Vous pouvez le résoudre en examinant votre déploiement FTP ou en sélectionnant un autre chemin d’accès de déploiement (comme un contrôle de code source).

Un problème d’application à l’exécution se produit généralement si l’ensemble de fichiers approprié est déployé sur votre application, mais que le comportement de l’application est incorrect. Vous pouvez le résoudre en vous concentrant sur le comportement du code lors de l’exécution et en examinant des chemins d’accès d’échec spécifiques.

Pour déterminer un problème de déploiement ou d’exécution, consultez [Deployment vs. runtime issues](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues) (Problèmes de déploiement et d’exécution).

#### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Je ne peux pas envoyer par FTP et publier mon code. Comment puis-je résoudre le problème ?
Vérifiez que vous avez entré le [nom d’hôte](#get-ftps-endpoint) et les [informations d’identification](#get-deployment-credentials) corrects. Vérifiez également que les ports FTP suivants sur votre machine ne sont pas bloqués par un pare-feu :

- Port de connexion de contrôle FTP :
- Port de connexion de données FTP : 989, 10001-10300
 
#### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Comment puis-je me connecter à FTP dans Azure App Service via le mode passif ?
Azure App Service prend en charge la connexion via les modes actif et passif. Le mode passif est recommandé, car vos machines de déploiement se trouvent généralement derrière un pare-feu (dans le système d’exploitation ou faisant partie d’un réseau domestique ou d’entreprise). Consultez un [exemple dans la documentation de WinSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="more-resources"></a>Plus de ressources

* [Déploiement Git local vers Azure App Service](deploy-local-git.md)
* [Informations d’identification du déploiement d’Azure App Service](deploy-configure-credentials.md)
* [Exemple : Créer une application web et déployer des fichiers avec FTP (Azure CLI)](./scripts/cli-deploy-ftp.md).
* [Exemple : Charger des fichiers vers une application web avec FTP (PowerShell)](./scripts/powershell-deploy-ftp.md).
