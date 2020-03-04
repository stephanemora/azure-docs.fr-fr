---
title: Utiliser des scripts de déploiement dans des modèles | Microsoft Docs
description: Utilisez des scripts de déploiement dans des modèles Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: jgao
ms.openlocfilehash: e881cde36bc56c175004e8d6adb9b7b85e9b5454
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616313"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Utiliser des scripts de déploiement dans des modèles (Préversion)

Découvrez comment utiliser des scripts de déploiement dans des modèles de ressource Azure. Avec un nouveau type de ressource appelé `Microsoft.Resources/deploymentScripts`, les utilisateurs peuvent exécuter des scripts de déploiement dans les déploiements de modèle et examiner les résultats de l’exécution. Ces scripts peuvent être utilisés pour effectuer des étapes personnalisées, comme :

- ajouter des utilisateurs à un annuaire ;
- créer une inscription d’application ;
- effectuer des opérations de plan de données, par exemple, copier des objets blob ou alimenter une base de données ;
- rechercher et valider une clé de licence ;
- créer un certificat auto-signé ;
- créer un objet dans Azure AD ;
- rechercher des blocs d’adresses IP à partir du système personnalisé ;

Les avantages du script de déploiement :

- Facile à coder, utiliser et déboguer. Vous pouvez développer des scripts de déploiement dans vos environnements de développement préférés. Les scripts peuvent être incorporés aux modèles ou dans des fichiers de script externe.
- Vous pouvez spécifier le langage de script et la plateforme. À l’heure actuelle, les scripts de déploiement Azure PowerShell et Azure CLI dans l’environnement Linux sont pris en charge.
- Permet la spécification des identités utilisées pour exécuter les scripts. Actuellement, seule l’[identité managée affectée par l’utilisateur](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) est prise en charge.
- Permet la transmission des arguments de ligne de commande au script.
- Peut spécifier des sorties de script et les renvoyer au déploiement.

> [!NOTE]
> Le script de déploiement est actuellement disponible en préversion. Pour l’utiliser, vous devez [vous inscrire à la préversion](https://aka.ms/armtemplatepreviews).

> [!IMPORTANT]
> Deux ressources de script de déploiement, un compte de stockage et une instance de conteneur, sont créées dans le même groupe de ressources pour l’exécution du script et la résolution de problèmes. Ces ressources sont généralement supprimées par le service de script lorsque l’exécution du script de déploiement arrive à un état terminal. Vous êtes facturé pour les ressources jusqu’à ce qu’elles soient supprimées. Pour plus d’informations, consultez [Nettoyer les ressources de script de déploiement](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Prérequis

- **Une identité managée affectée par l’utilisateur avec le rôle de contributeur au groupe de ressources cible**. Cette identité est utilisée pour exécuter les scripts de déploiement. Pour effectuer des opérations en dehors du groupe de ressources, vous devez accorder des autorisations supplémentaires. Par exemple, attribuez l’identité au niveau de l’abonnement si vous souhaitez créer un groupe de ressources.

  > [!NOTE]
  > Le moteur de script de déploiement doit créer un compte de stockage et une instance de conteneur en arrière-plan.  Une identité managée affectée par l’utilisateur dotée du rôle de contributeur au niveau de l’abonnement est requise si l’abonnement n’a pas inscrit le compte de stockage Azure (Microsoft.Storage) ni les fournisseurs de ressources d’instance de conteneur Azure (Microsoft.ContainerInstance).

  Pour créer une identité, consultez [Créer une identité managée affectée par l’utilisateur à l’aide du Portail Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [d’Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) ou [d’Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). Vous avez besoin de l’ID d’identité lorsque vous déployez le modèle. Le format de l’identité est le suivant :

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Utilisez le script PowerShell ou CLI suivant pour obtenir l’ID en fournissant le nom du groupe de ressources et le nom de l’identité.

  # <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g jgaoidentity1008rg -n jgaouami --query id
  ```

  # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```
  ---

- **Azure PowerShell version 3.0.0, 2.8.0 ou 2.7.0** ou **Azure CLI version 2.0.80, 2.0.79, 2.0.78 ou 2.0.77**. Vous n’avez pas besoin de ces versions pour déployer des modèles. Par contre, ces versions sont nécessaires pour tester les scripts de déploiement localement. Consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Vous pouvez utiliser une image Docker préconfigurée.  Consultez [Configurer l’environnement de développement](#configure-development-environment).

## <a name="sample-templates"></a>Exemples de modèles

L’extrait json ci-dessous est un exemple.  Le schéma de modèle le plus récent est disponible [ici](/azure/templates/microsoft.resources/deploymentscripts).

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80"
    "arguments": "[concat('-name ', parameters('name'))]",
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> L’exemple ne sert qu’à des fins de démonstration.  **scriptContent** et **primaryScriptUris** ne peuvent pas coexister dans un modèle.

Détails des valeurs de propriété :

- **Identité** : le service de script de déploiement utilise une identité managée affectée par l’utilisateur pour exécuter les scripts. Actuellement, seule l’identité managée affectée par l’utilisateur est prise en charge.
- **kind** : spécifie le type de script. Actuellement, les scripts Azure PowerShell et Azure CLI sont pris en charge. Les valeurs sont **AzurePowerShell** et **AzureCLI**.
- **forceUpdateTag** : la modification de cette valeur entre les déploiements de modèle force le script de déploiement à s’exécuter de nouveau. Utilisez la fonction newGuid() ou utcNow() qui doit être définie comme defaultValue d’un paramètre. Pour plus d’informations, consultez [Exécuter le script plusieurs fois](#run-script-more-than-once).
- **azPowerShellVersion**/**azCliVersion** : spécifie la version du module à utiliser. Le script de déploiement prend actuellement en charge Azure PowerShell version 2.7.0, 2.8.0 et 3.0.0 Azure CLI version 2.0.80, 2.0.79, 2.0.78 et 2.0.77.
- **arguments** : Spécifiez les valeurs de paramètre. Les valeurs sont séparées par des espaces.
- **scriptContent** : précise le contenu du script. Pour exécuter un script externe, utilisez plutôt `primaryScriptUri`. Pour obtenir des exemples, consultez [Utiliser un script inclus](#use-inline-scripts) et [Utiliser un script externe](#use-external-scripts).
- **primaryScriptUri** : spécifie une URL accessible publiquement pour le script de déploiement principal avec les extensions de fichier prises en charge.
- **supportingScriptUris** : spécifie un tableau d’URL accessibles publiquement pour les fichiers de prise en charge qui seront appelés dans `ScriptContent` ou `PrimaryScriptUri`.
- **timeout** : précise la durée d’exécution maximale autorisée du script, définie au format [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). La valeur par défaut est **P1D**.
- **cleanupPreference** : indique la préférence de nettoyage des ressources de déploiement lorsque l’exécution du script arrive à un état terminal. Le paramètre par défaut est **Always**, ce qui signifie que les ressources sont supprimées malgré l’état terminal (Succeeded, Failed, Canceled). Pour plus d’informations, consultez [Nettoyer les ressources de script de déploiement](#clean-up-deployment-script-resources).
- **retentionInterval** : spécifie l’intervalle pendant lequel le service conserve les ressources du script de déploiement une fois que l’exécution du script a atteint un état terminal. Les ressources de script de déploiement sont supprimées à la fin de cette durée. La durée est basée sur le [modèle ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). La valeur par défaut est **P1D**, ce qui signifie sept jours. Cette propriété est utilisée lorsque cleanupPreference a la valeur *OnExpiration*. La propriété *OnExpiration* n’est pas activée actuellement. Pour plus d’informations, consultez [Nettoyer les ressources de script de déploiement](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Exemples supplémentaires

- [créer et attribuer un certificat à un coffre de clés](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- [créer et attribuer une identité managée affectée par l’utilisateur à un groupe de ressources, puis exécuter un script de déploiement](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json)

> [!NOTE]
> Il est recommandé de créer une identité affectée par l’utilisateur et d’accorder des autorisations à l’avance. Vous risquez d’obtenir des erreurs de connexion et d’autorisation si vous créez l’identité et accordez les autorisations dans le même modèle que celui où vous exécutez les scripts de déploiement. Il faut un certain temps avant que les autorisations prennent effet.

## <a name="use-inline-scripts"></a>Utiliser des scripts inclus

Le modèle suivant dispose d’une ressource définie avec le type `Microsoft.Resources/deploymentScripts`.

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json?range=1-54)]

> [!NOTE]
> Étant donné que les scripts de déploiement inclus sont placés entre guillemets doubles, les chaînes contenues dans les scripts de déploiement doivent être mises entre guillemets simples. Le caractère d’échappement pour PowerShell est **&#92;** . Vous pouvez également envisager d’utiliser la substitution de chaîne, comme montré dans l’exemple JSON précédent. Regardez la valeur par défaut du paramètre name.

Le script accepte un paramètre, et génère la valeur du paramètre. **DeploymentScriptOutputs** s’utilise pour stocker les sorties.  À la section outputs, la ligne **value** montre comment accéder aux valeurs stockées. `Write-Output` s’utilise pour le débogage. Pour savoir comment accéder au fichier de sortie, consultez [Déboguer les scripts de déploiement](#debug-deployment-scripts).  Pour obtenir les descriptions des propriétés, consultez [Exemples de modèles](#sample-templates).

Afin d’exécuter le script, sélectionnez **Try it** (Essayer) pour ouvrir Azure Cloud Shell, puis collez le code suivant dans le volet de l’interpréteur de commandes.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

Le résultat se présente ainsi :

![Sortie hello world du script de déploiement du modèle Resource Manager](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Utiliser des scripts externes

En plus des scripts inclus, vous pouvez utiliser des fichiers de script externe. Seuls les scripts PowerShell principaux dotés de l’extension de fichier **ps1** sont pris en charge. Pour les scripts CLI, les scripts principaux peuvent avoir n’importe quelle extension (ou n’avoir aucune extension), à condition que les scripts soient des scripts Bash valides. Pour utiliser les fichiers de script externe, remplacez `scriptContent` par `primaryScriptUri`. Par exemple :

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Pour voir un exemple, sélectionnez [ici](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

Les fichiers de script externe doivent être accessibles.  Pour sécuriser vos fichiers de script qui sont stockés dans les comptes de stockage Azure, consultez [Tutoriel : Sécuriser des artefacts dans les déploiements de modèles Azure Resource Manager](./template-tutorial-secure-artifacts.md).

## <a name="use-supporting-scripts"></a>Utiliser des scripts de prise en charge

Vous pouvez séparer les logiques complexes en un ou plusieurs fichiers de script de prise en charge. La propriété `supportingScriptURI` vous permet de fournir un tableau d’URI pour les fichiers de script de prise en charge, le cas échéant :

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

Les fichiers de script de prise en charge peuvent être appelés à partir de scripts inclus et de fichiers de script principal. Les fichiers de script de prise en charge n’ont aucune restriction quant à l’extension de fichier.

Les fichiers de prise en charge sont copiés dans azscripts/azscriptinput au moment de l’exécution. Utilisez un chemin relatif pour référencer les fichiers de prise en charge à partir de scripts inclus et de fichiers de script principal.

## <a name="work-with-outputs-from-powershell-script"></a>Travailler avec les sorties du script PowerShell

Le modèle suivant montre comment passer des valeurs entre deux ressources deploymentScripts :

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-basic.json?range=1-84)]

Dans la première ressource, vous définissez une variable appelée **$DeploymentScriptOutputs** et l’utilisez pour stocker les valeurs de sortie. Pour accéder à la valeur de sortie à partir d’une autre ressource dans le modèle, utilisez :

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Travailler avec les sorties du script CLI

À la différence du script de déploiement PowerShell, la prise en charge de CLI/Bash n’expose pas de variable courante permettant de stocker les sorties de script. Au lieu de cela, il existe une variable d’environnement appelée **AZ_SCRIPTS_OUTPUT_PATH** qui stocke l’emplacement où se trouve le fichier des sorties du script. Si un script de déploiement est exécuté à partir d’un modèle Resource Manager, cette variable d’environnement est automatiquement définie pour vous par l’interpréteur de commandes Bash.

Les sorties de script de déploiement doivent être enregistrées à l’emplacement AZ_SCRIPTS_OUTPUT_PATH et être un objet de chaîne JSON valide. Le contenu du fichier doit être enregistré sous la forme d’une paire clé-valeur. Par exemple, un tableau de chaînes est stocké sous la forme { “MyResult”: [ “foo”, “bar”] }.  Le stockage des résultats du tableau uniquement, par exemple [ “foo”, “bar” ], n’est pas valide.

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json?range=1-44)]

[jq](https://stedolan.github.io/jq/) est utilisé dans l’exemple précédent. Il est fourni avec les images conteneurs. Consultez [Configurer l’environnement de développement](#configure-development-environment).

## <a name="handle-non-terminating-errors"></a>Gérer les erreurs sans fin d’exécution

Vous pouvez contrôler la façon dont PowerShell répond aux erreurs sans fin d’exécution à l’aide de la variable [ **$ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
) dans votre script de déploiement. Le moteur de script de déploiement ne définit ni ne modifie la valeur.  En dépit de la valeur que vous définissez pour $ErrorActionPreference, le script de déploiement définit l’état d’approvisionnement des ressources sur *Failed* (Échec) lorsque le script rencontre une erreur.

## <a name="debug-deployment-scripts"></a>Déboguer les scripts de déploiement

Le service de script crée un [compte de stockage](../../storage/common/storage-account-overview.md) et une [instance de conteneur](../../container-instances/container-instances-overview.md) pour l’exécution d’un script. Les deux ressources affichent le suffixe **azscripts** dans les noms de ressource.

![Noms de ressource de script de déploiement d’un modèle Azure Resource Manager](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Le script utilisateur, les résultats de l’exécution et le fichier stdout sont stockés dans les partages de fichiers du compte de stockage. Il existe un dossier nommé **azscripts**. Dans celui-ci, se trouvent deux dossiers supplémentaires pour les fichiers d’entrée et de sortie : **azscriptinput** et **azscriptoutput**.

Le dossier output contient un fichier **executionresult.json** et le fichier de sortie du script. Vous pouvez voir le message d’erreur de l’exécution du script dans **executionresult.json**. Le fichier de sortie est créé uniquement lorsque le script est exécuté correctement. Le dossier input contient un fichier de script PowerShell système et les fichiers de script de déploiement utilisateur. Vous pouvez remplacer le fichier de script de déploiement utilisateur par un fichier révisé, puis réexécuter le script de déploiement à partir de l’instance de conteneur Azure.

Vous pouvez récupérer les informations de déploiement de la ressource de script de déploiement au niveau du groupe de ressources et du niveau de l’abonnement à l’aide de l’API REST :

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

L’exemple suivant utilise [ARMClient](https://github.com/projectkudu/ARMClient) :

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

Le résultat ressemble à ce qui suit :

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-status.json?range=1-48)]

La sortie renseigne sur l’état du déploiement, et les ID de ressource du script de déploiement.

L’API REST suivante retourne le journal :

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Elle fonctionne uniquement avant la suppression des ressources de script de déploiement.

Pour afficher la ressource deploymentScripts dans le portail, sélectionnez **Afficher les types masqués** :

![Portail d’un script de déploiement de modèle Resource Manager avec l’option Afficher les types masqués](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Nettoyer les ressources de script de déploiement

Le script de déploiement crée un compte de stockage et une instance de conteneur qui sont utilisés pour l’exécution de scripts de déploiement et le stockage des informations de débogage. Ces deux ressources sont créées dans le même groupe de ressources que les ressources provisionnées, et sont supprimées par le service de script lorsque le script expire. Vous pouvez contrôler le cycle de vie de ces ressources.  Tant qu’elles ne sont pas supprimées, vous êtes facturé pour les deux ressources. Pour plus d’informations sur les prix, consultez [Tarifs Container Instances](https://azure.microsoft.com/pricing/details/container-instances/) et [Tarifs Stockage Azure](https://azure.microsoft.com/pricing/details/storage/).

Le cycle de vie de ces ressources est contrôlé par les propriétés suivantes dans le modèle :

- **cleanupPreference** : nettoie la préférence lorsque l’exécution du script arrive à un état terminal.  Les valeurs prises en charge sont les suivantes :

  - **Always** : supprime les ressources une fois que l’exécution du script a atteint un état terminal. Étant donné que la ressource deploymentScripts peut encore être présente après le nettoyage des ressources, le script système copie les résultats de l’exécution du script (par exemple, stdout, outputs, return value, etc.) dans la base de donnes avant la suppression des ressources.
  - **OnSuccess** : supprime les ressources uniquement lorsque l’exécution du script s’est déroulée correctement. Vous pouvez toujours accéder aux ressources pour rechercher les informations de débogage.
  - **OnExpiration** : supprime les ressources uniquement lorsque le paramètre **retentionInterval** est arrivé à expiration. Cette propriété est actuellement désactivée.

- **retentionInterval** : spécifie l’intervalle de temps pendant lequel une ressource de script est conservée, et après lequel elle expire et est supprimée.

> [!NOTE]
> Il est déconseillé d’utiliser les ressources de script de déploiement à d’autres fins.

## <a name="run-script-more-than-once"></a>Exécuter le script plusieurs fois

L’exécution d’un script de déploiement est une opération idempotente. Si aucune des propriétés de la ressource deploymentScripts (y compris le script inclus) n’est modifiée, le script n’est pas exécuté lorsque vous redéployez le modèle. Le service de script de déploiement compare les noms des ressource du modèle aux ressources existantes dans le même groupe de ressources. Vous avez deux options à votre disposition si vous souhaitez exécuter le même script de déploiement plusieurs fois :

- Modifiez le nom de votre ressource deploymentScripts. Par exemple, utilisez la fonction de modèle [utcNow](./template-functions-string.md#utcnow) comme nom de ressource, ou comme partie du nom de la ressource. La modification du nom de la ressource crée une nouvelle ressource deploymentScripts. C’est un moyen pratique pour conserver un historique de l’exécution des scripts.

    > [!NOTE]
    > La fonction utcNow ne peut être utilisée que dans la valeur par défaut d’un paramètre.

- Spécifiez une autre valeur dans la propriété de modèle `forceUpdateTag`.  Par exemple, utilisez utcNow comme valeur.

> [!NOTE]
> Écrivez les scripts de déploiement qui sont idempotents. Cela garantit que, s’ils sont exécutés accidentellement, cela n’entraînera de modifications du système. Par exemple, si le script de déploiement est utilisé pour créer une ressource Azure, vérifiez que la ressource n’existe pas avant de la créer, afin que le script aboutisse ou que vous ne recréiez pas la ressource.

## <a name="configure-development-environment"></a>Configurer l’environnement de développement

Vous pouvez utiliser une image conteneur d’ancrage préconfigurée comme environnement de développement de script de déploiement. La procédure suivante montre comment configurer l’image Docker sur Windows. Pour Linux et Mac, vous pouvez trouver les informations sur Internet.

1. Installez [Docker Desktop](https://www.docker.com/products/docker-desktop) sur votre ordinateur de développement.
1. Ouvrez Docker Desktop.
1. Sélectionnez l’icône Docker Desktop dans la barre des tâches, puis sélectionnez **Settings** (Paramètres).
1. Sélectionnez **Shared Drives** (Lecteurs partagés), puis sélectionnez un lecteur local à mettre à la disposition de vos conteneurs et sélectionnez **Apply**.

    ![Lecteur Docker pour un script de déploiement de modèle Resource Manager](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. À l’invite, entrez vos informations d’identification Windows.
1. Ouvrez une fenêtre de terminal, soit l’invite de commandes, soit Windows PowerShell (mais n’utilisez pas PowerShell ISE).
1. Extrayez l’image conteneur du script de déploiement pour la mettre sur l’ordinateur local :

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    L’exemple utilise la version 2.7.0 de PowerShell.

    Pour tirer (pull) une image CLI d’un registre Microsoft Container Registry (MCR) :

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    Cet exemple utilise la version 2.0.80 de CLI. Le script de déploiement utilise les images conteneurs CLI par défaut trouvées [ici](https://hub.docker.com/_/microsoft-azure-cli).

1. Exécutez l’image Docker localement.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Remplacez **&lt;host driver letter>** (lettre de lecteur hôte) et **&lt;host directory name>** (nom de répertoire hôte) par un dossier existant sur le lecteur partagé.  Le dossier est alors mappé au dossier **/data** dans le conteneur. Par exemple, pour mapper D:\docker :

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-it** indique de conserver l’image conteneur active.

    Exemple CLI :

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Sélectionnez **Share it** (Le partager) quand vous recevez une invite.
1. La capture d’écran suivante illustre comment exécuter un script PowerShell, étant donné que vous avez un fichier helloworld.ps1 dans le dossier d:\docker.

    ![Commande Docker pour un script de déploiement de modèle Resource Manager](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Dès lors que le script a été testé avec succès, vous pouvez l’utiliser en tant que script de déploiement.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser des scripts de déploiement. Pour suivre un tutoriel sur les scripts de déploiement :

> [!div class="nextstepaction"]
> [Tutoriel : Utiliser des scripts de déploiement dans des modèles Azure Resource Manager](./template-tutorial-deployment-script.md)