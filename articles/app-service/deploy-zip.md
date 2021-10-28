---
title: Déployer des fichiers sur le Service d’application
description: Apprenez à déployer divers packages d’application ou bibliothèques discrètes, fichiers statiques ou scripts de démarrage pour le Service d’application Azure
ms.topic: article
ms.date: 08/13/2021
ms.reviewer: sisirap
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: be58de78ba82c3c5e99475b00649e2a7ebfdf5e4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130216936"
---
# <a name="deploy-files-to-app-service"></a>Déployer des fichiers sur le Service d’application

Cet article vous montre comment déployer votre code en tant que package ZIP, WAR, JAR ou EAR pour le [Service d’application Azure ](overview.md). Il montre également comment déployer des fichiers individuels sur le e Service d’application, séparément de votre package d’application.

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes de cet article, [créez une application App Service](./index.yml) ou utilisez une application créée pour un autre didacticiel.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="deploy-a-zip-package"></a>Déployer un package ZIP

Lorsque vous déployez un package ZIP, le e Service d’application décompresse son contenu dans le chemin d’accès par défaut de votre application (`D:\home\site\wwwroot` pour Windows, `/home/site/wwwroot` pour Linux).

Ce déploiement de package ZIP utilise le même service Kudu que celui qui pilote les déploiements continus basés sur l’intégration. Kudu prend en charge les fonctionnalités suivantes pour le déploiement de package ZIP : 

- Suppression des fichiers laissés par un déploiement précédent
- Option pour activer le processus de génération par défaut, qui inclut la restauration de package
- Personnalisation du déploiement, notamment exécution de scripts de déploiement  
- Journaux d’activité de déploiement 
- La taille d’un package ne doit pas dépasser 2 048 Mo.

Pour plus d’informations, consultez la [documentation Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

> [!NOTE]
> Les fichiers du package ZIP ne sont copiés que si leurs horodatages ne correspondent pas à ce qui est déjà déployé. La génération d’un fichier zip selon un processus de build qui met en cache les sorties contribue à accélérer les déploiements. Pour plus d’informations, consultez [Déploiement à partir d’un fichier zip ou d’une URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Déployez un package ZIP sur votre application Web à l’aide de la commande [AZ webapp deploy](/cli/azure/webapp#az_webapp_deploy) . La commande CLI utilise l' [API de publication Kudu](#kudu-publish-api-reference) pour déployer les fichiers et peut être entièrement personnalisée.

L’exemple suivant envoie un package ZIP sur votre site. Spécifiez le chemin d’accès à votre package ZIP local pour `--src-path`.

```azurecli-interactive
az webapp deploy --resource-group <group-name> --name <app-name> --src-path <zip-package-path>
```

Cette commande redémarre l’application après le déploiement du package ZIP. 

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

L’exemple suivant utilise le paramètre `--src-url` pour spécifier l’URL d’un compte de stockage Azure à partir duquel le site doit extraire le fichier ZIP.

```azurecli-interactive
az webapp deploy --resource-group <grou-name> --name <app-name> --src-url "https://storagesample.blob.core.windows.net/sample-container/myapp.zip?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

L’exemple suivant utilise [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) pour charger le package .zip. Remplacez les espaces réservés `<group-name>`, `<app-name>` et `<zip-package-path>`.

```powershell
Publish-AzWebApp -ResourceGroupName Default-Web-WestUS -Name MyApp -ArchivePath <zip-package-path> 
```

# <a name="kudu-api"></a>[API Kudu](#tab/api)

L’exemple suivant utilise l’outil cURL pour déployer un package.zip. Remplacez les espaces réservés `<username>`, `<zip-package-path>` et `<app-name>`. Quand vous y êtes invité par cURL, tapez le [mot de passe du déploiement](deploy-configure-credentials.md).

```bash
curl -X POST -u <username> --data-binary @"<zip-package-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=zip
```

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

L’exemple suivant utilise le paramètre `packageUri` pour spécifier l’URL d’un compte de stockage Azure à partir duquel le site doit extraire le fichier ZIP.

```bash
curl -X POST -u <username> https://<app-name>.scm.azurewebsites.net/api/publish -d '{"packageUri": "https://storagesample.blob.core.windows.net/sample-container/myapp.zip?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3"}'
```

# <a name="kudu-ui"></a>[IU Kudu](#tab/kudu-ui)

Dans un navigateur, accédez à `https://<app_name>.scm.azurewebsites.net/ZipDeployUI`.

Télécharger le package ZIP que vous avez créé dans [Créer un package ZIP de projet](#create-a-project-zip-package) en le faisant glisser vers la zone de l’explorateur de fichiers sur la page Web.

Lorsque le déploiement est en cours, une icône dans le coin supérieur droit vous indique la progression en pourcentage. La page affiche également des messages détaillés concernant l’opération sous la zone de l’explorateur. Lorsqu’elle est terminée, le dernier message de déploiement doit indiquer `Deployment successful`.

Le point de terminaison ci-dessus ne fonctionne pas pour App Services Linux. Envisagez plutôt d’utiliser FTP ou l’[API de déploiement ZIP](./faq-app-service-linux.yml).

-----

## <a name="enable-build-automation-for-zip-deploy"></a>Activer l’automatisation de build pour le déploiement ZIP

Par défaut, le moteur de déploiement suppose qu’un package ZIP est prêt à s’exécuter en l’état et n’effectue aucune automatisation de build. Pour permettre la même automatisation de build que dans un [déploiement Git](deploy-local-git.md), définissez le paramètre d’application `SCM_DO_BUILD_DURING_DEPLOYMENT` en exécutant la commande suivante dans [Azure Cloud Shell](https://shell.azure.com) :

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Pour plus d’informations, consultez la [documentation Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="deploy-warjarear-packages"></a>Déployer des packages WAR/JAR/EAR

Vous pouvez déployer votre package [WAR](https://wikipedia.org/wiki/WAR_(file_format)), [JAR](https://wikipedia.org/wiki/JAR_(file_format))ou [EAR](https://wikipedia.org/wiki/EAR_(file_format)) sur le service d’application pour exécuter votre application Web Java à l’aide de l’interface CLI Azure, PowerShell ou l’API de publication Kudu.

Le processus de déploiement place correctement le package sur le lecteur de fichiers partagés (consultez [référence de l'API de publication Kudu](#kudu-publish-api-reference)). Pour cette raison, il est déconseillé de déployer des packages WAR/JAR/EAR à l’aide de [FTP](deploy-ftp.md) ou de WebDeploy.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Déployez un package WAR sur le protocole EAP Tomcat ou JBoss à l’aide de la commande [AZ webapp deploy](/cli/azure/webapp#az_webapp_deploy) . Spécifiez le chemin d’accès à votre package Java local pour `--src-path`.

```azurecli-interactive
az webapp deploy --resource-group <group-name> --name <app-name> --src-path ./<package-name>.war
```

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

L’exemple suivant utilise le paramètre `--src-url` pour spécifier l’URL d’un compte de Stockage Azure à partir duquel le site doit extraire le fichier ZIP.

```azurecli-interactive
az webapp deploy --resource-group <grou-name> --name <app-name> --src-url "https://storagesample.blob.core.windows.net/sample-container/myapp.war?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3
```

La commande CLI utilise l' [API de publication Kudu](#kudu-publish-api-reference) pour déployer le package et peut être entièrement personnalisée.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

L’exemple suivant utilise [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) pour charger le fichier .war. Remplacez les espaces réservés `<group-name>` , `<app-name>` et `<package-path>` (seuls les fichiers JAR sont pris en charge dans Azure PowerShell).

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <package-path>
```

# <a name="kudu-api"></a>[API Kudu](#tab/api)

L’exemple suivant utilise l’outil cURL pour déployer un fichier .war, .jar, ou .ear. Remplacez les espaces réservés `<username>`, `<file-path>`, `<app-name>` et `<package-type>` (`war`, `jar`, ou `ear`, en conséquence). Quand vous y êtes invité par cURL, tapez le [mot de passe du déploiement](deploy-configure-credentials.md).

```bash
curl -X POST -u <username> --data-binary @"<file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=<package-type>
```

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

L’exemple suivant utilise le paramètre `packageUri` pour spécifier l’URL d’un compte de Stockage Azure à partir duquel l’application Web doit extraire le fichier WAR. Le fichier WAR peut également être un fichier JAR ou EAR.

```bash
curl -X POST -u <username> https://<app-name>.scm.azurewebsites.net/api/publish -d '{"packageUri": "https://storagesample.blob.core.windows.net/sample-container/myapp.war?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3"}'
```

Pour plus d’informations, consultez la [référence de l’API de publication Kudu](#kudu-publish-api-reference).

# <a name="kudu-ui"></a>[IU Kudu](#tab/kudu-ui)

L’interface utilisateur Kudu ne prend pas en charge le déploiement des applications JAR, WAR ou EAR. Veuillez utiliser l’une des autres options.

-----

## <a name="deploy-individual-files"></a>Déployer des fichiers individuels

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Déployez un script de démarrage, une bibliothèque et un fichier statique dans votre application Web à l’aide de la commande [AZ webapp deploy](/cli/azure/webapp#az_webapp_deploy) avec le paramètre `--type`.

Si vous déployez un script de démarrage de cette manière, le Service d’application utilise automatiquement votre script pour démarrer votre application.

La commande CLI utilise l' [API de publication Kudu](#kudu-publish-api-reference) pour déployer les fichiers et peut être entièrement personnalisée.

### <a name="deploy-a-startup-script"></a>Utilisation d'un script de démarrage

```bash
az webapp deploy --resource group <group-name> --name <app-name> --src-path scripts/startup.sh --type=startup
```

### <a name="deploy-a-library-file"></a>Déployer un fichier de bibliothèque

```bash
az webapp deploy --resource group <group-name> --name <app-name> --src-path driver.jar --type=lib
```

### <a name="deploy-a-static-file"></a>Déployer un fichier statique

```bash
az webapp deploy --resource group <group-name> --name <app-name> --src-path config.json --type=static
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Non pris en charge. Visualiser l’interface CLI Azure ou l’API Kudu.

# <a name="kudu-api"></a>[API Kudu](#tab/api)

### <a name="deploy-a-startup-script"></a>Utilisation d'un script de démarrage

L’exemple suivant utilise l’outil cURL pour déployer un fichier de démarrage pour son application. Remplacez les espaces réservés `<username>`, `<startup-file-path>`, et `<app-name>`. Quand vous y êtes invité par cURL, tapez le [mot de passe du déploiement](deploy-configure-credentials.md).

```bash
curl -X POST -u <username> --data-binary @"<startup-file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=startup
```

### <a name="deploy-a-library-file"></a>Déployer un fichier de bibliothèque

L’exemple suivant utilise l’outil cURL pour déployer un fichier de bibliothèque pour son application. Remplacez les espaces réservés `<username>`, `<lib-file-path>` et `<app-name>`. Quand vous y êtes invité par cURL, tapez le [mot de passe du déploiement](deploy-configure-credentials.md).

```bash
curl -X POST -u <username> --data-binary @"<lib-file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=lib&path="/home/site/deployments/tools/my-lib.jar"
```

### <a name="deploy-a-static-file"></a>Déployer un fichier statique

L’exemple suivant utilise l’outil cURL pour déployer un fichier de configuration pour son application. Remplacez les espaces réservés `<username>`, `<config-file-path>` et `<app-name>`. Quand vous y êtes invité par cURL, tapez le [mot de passe du déploiement](deploy-configure-credentials.md).

```bash
curl -X POST -u <username> --data-binary @"<config-file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=static&path="/home/site/deployments/tools/my-config.json"
```

# <a name="kudu-ui"></a>[IU Kudu](#tab/kudu-ui)

L’interface utilisateur Kudu ne prend pas en charge le déploiement de fichiers individuels. Veuillez utiliser l’interface CLI Azure CLI ou l’API REST Kudu.

-----

## <a name="kudu-publish-api-reference"></a>référence sur l’API de publication Kudu

L'API Kudu `publish` vous permet de spécifier les mêmes paramètres depuis la commande CLI en tant que paramètres de requête d’URL. Pour vous authentifier avec l’API kudu, vous pouvez utiliser l’authentification de base avec les [informations d’identification de déploiement](deploy-configure-credentials.md#userscope) de votre application.

Le tableau ci-dessous présente les paramètres de requête disponibles, leurs valeurs autorisées et leurs descriptions.

| Clé : | Valeurs autorisées | Description | Obligatoire | Type  |
|-|-|-|-|-|
| `type` | `war`\|`jar`\|`ear`\|`lib`\|`startup`\|`static`\|`zip` | Le type de l’artefact déployé définit le chemin d’accès cible par défaut et indique à l’application Web comment le déploiement doit être géré. <br/> - `type=zip` : Déployez un package ZIP en dézippant le contenu sur `/home/site/wwwroot`. Le paramètre `path` est facultatif. <br/> - `type=war` : Déployez un package WAR. Par défaut, le package WAR est déployé sur `/home/site/wwwroot/app.war`. Le chemin d’accès cible peut être spécifié avec `path`. <br/> - `type=jar` : Déployez un package JAR sur `/home/site/wwwroot/app.jar` . Le paramètre `path` est ignoré <br/> - `type=ear` : Déployez un package EAR sur `/home/site/wwwroot/app.ear`. Le paramètre `path` est ignoré <br/> - `type=lib` : Déployez un fichier de bibliothèque JAR. Par défaut, le fichier est déployé sur `/home/site/libs`. Le chemin d’accès cible peut être spécifié avec `path`. <br/> - `type=static` : Déployez un fichier statique (par exemple, un script). Par défaut, le fichier est déployé sur `/home/site/scripts`. Le chemin d’accès cible peut être spécifié avec `path`. <br/> - `type=startup`: Déployez un script que le Service d’application utilise automatiquement comme script de démarrage pour votre application. Par défaut, le script est déployé sur `D:\home\site\scripts\<name-of-source>` pour Windows et `home/site/wwwroot/startup.sh` pour Linux. Le chemin d’accès cible peut être spécifié avec `path`. | Oui | String |
| `restart` | `true`\|`false` | Par défaut, l’API redémarre l’application après l’opération de déploiement (`restart=true`). Pour déployer plusieurs artefacts, évitez les redémarrages sur l’ensemble, sauf le déploiement final, en définissant `restart=false`. | Non  | Boolean |
| `clean` | `true`\|`false` | Spécifie s’il faut nettoyer (supprimer) le déploiement cible avant de déployer l’artefact ici. | Non  | Boolean |
| `ignorestack` | `true`\|`false` | L’API de publication utilise la variable d’environnement `WEBSITE_STACK` pour choisir les valeurs par défaut sécurisées en fonction de la pile de langue de votre site. La définition de ce paramètre sur `false` désactive les valeurs par défaut spécifiques au langage. | Non  | Boolean |
| `path` | `"<absolute-path>"` | Chemin d’accès absolu vers lequel déployer l’artefact. Par exemple, `"/home/site/deployments/tools/driver.jar"`, `"/home/site/scripts/helper.sh"`. | Non | String |

## <a name="next-steps"></a>Étapes suivantes

Pour des scénarios de déploiement plus avancés, consultez [Déploiement Git local vers Azure App Service](deploy-local-git.md). Le déploiement GIT vers Azure autorise le contrôle de version, la restauration du package, MSBuild et bien plus encore.

## <a name="more-resources"></a>Plus de ressources

* [Kudu : Deploying from a zip file (Déploiement à partir d’un fichier zip)](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Informations d’identification du déploiement d’Azure App Service](deploy-ftp.md)
* [Informations de référence sur les variables d’environnement et les paramètres d’application](reference-app-settings.md)
