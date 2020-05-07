---
title: Créer des stratégies Guest Configuration pour Windows
description: Découvrez comment créer une stratégie Guest Configuration pour des machines virtuelles Windows.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: a75525b25945dd9548d7c293d5965cc67eb463dc
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509616"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Créer des stratégies Guest Configuration pour Windows

Avant de créer des stratégies personnalisées, il est judicieux de lire les informations conceptuelles sur la page [Azure Policy Guest Configuration](../concepts/guest-configuration.md).
 
Pour en savoir plus sur la création de stratégies Guest Configuration pour Linux, consultez la page [Créer des stratégies Guest Configuration pour Linux](./guest-configuration-create-linux.md)

Lors de l’audit Windows, Guest Configuration utilise un module de ressources [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) pour créer le fichier de configuration. La configuration DSC définit la condition dans laquelle la machine doit se trouver.
Si l’évaluation de la configuration échoue, l’**auditIfNotExists** d’effet de stratégie est déclenché et la machine est considérée comme **non conforme**.

La [configuration d’invité Azure Policy](../concepts/guest-configuration.md) peut être utilisée uniquement pour auditer les paramètres à l’intérieur des machines. La correction des paramètres à l’intérieur des machines n’est pas encore disponible.

Utilisez les actions suivantes pour créer votre propre configuration pour la validation de l’état d’une machine Azure ou non-Azure.

> [!IMPORTANT]
> Les stratégies personnalisées avec Guest Configuration sont une fonctionnalité en préversion.
>
> L’extension Guest Configuration (Configuration d’invité) est requise pour effectuer des audits sur des machines virtuelles Azure.
> Pour déployer l’extension à grande échelle sur tous les ordinateurs Windows, attribuez les définitions de stratégie suivantes :
>   - [Déployer les prérequis pour activer la stratégie de configuration d’invité sur les machines virtuelles Windows.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)

## <a name="install-the-powershell-module"></a>Installer le module PowerShell

La création d’un artefact de Guest Configuration, le test automatisé de l’artefact, la création d’une définition de stratégie et la publication de cette stratégie sont entièrement automatisables à l’aide du module Guest Configuration dans PowerShell. Ce module peut être installé sur un ordinateur exécutant Windows, macOS ou Linux avec PowerShell 6.2 ou une version ultérieure exécutée localement, ou avec [Azure Cloud Shell](https://shell.azure.com), ou avec l’[image Azure PowerShell Core Docker](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> La compilation des configurations n’est pas encore prise en charge sur Linux.

### <a name="base-requirements"></a>Configuration de base requise

Systèmes d’exploitation sur lesquels le module peut être installé :

- Linux
- macOS
-  Windows

Le module de ressource Guest Configuration nécessite les logiciels suivants :

- PowerShell 6.2 ou ultérieur. S’il n’est pas installé, suivez [ces instructions](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 ou ultérieur. S’il n’est pas installé, suivez [ces instructions](/powershell/azure/install-az-ps).
  - Seuls les modules AZ (Az.Accounts et Az.Resources) sont requis.

### <a name="install-the-module"></a>Installer le module

Pour installer le module **GuestConfiguration** dans PowerShell :

1. À partir d’une invite de commandes PowerShell, exécutez la commande suivante :

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Vérifiez que le module a été importé :

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Artefacts Guest Configuration et stratégie pour Windows

Guest Configuration utilise Desired State Configuration de PowerShell comme abstraction de langage pour l’écriture des éléments à auditer dans Windows. L’agent charge une instance autonome de PowerShell 6.2 afin qu’il n’y ait pas de conflit avec l’utilisation de PowerShell DSC dans Windows PowerShell 5.1 et qu’il ne soit pas nécessaire de préinstaller PowerShell 6.2 ou une version ultérieure.

Pour obtenir une vue d’ensemble des concepts et de la terminologie DSC, consultez [Vue d’ensemble PowerShell DSC](/powershell/scripting/dsc/overview/overview).

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>Différences entre les modules Guest Configuration et les modules DSC Windows PowerShell

Quand Guest Configuration audite un ordinateur :

1. L’agent exécute d’abord `Test-TargetResource` pour déterminer si la configuration est dans l’état approprié.
1. La valeur booléenne retournée par la fonction détermine si l’état Azure Resource Manager de l’affectation d’invité doit être conforme ou non conforme.
1. Le fournisseur exécute `Get-TargetResource` pour retourner l’état actuel de chaque paramètre, afin que des détails soient disponibles à la fois sur la raison pour laquelle un ordinateur n’est pas conforme et pour confirmer que l’état actuel est conforme.

### <a name="get-targetresource-requirements"></a>Exigences Get-TargetResource

La fonction `Get-TargetResource` a des exigences pour Guest Configuration qu’elle n’a pas pour la configuration d’état souhaité Windows.

- La table de hachage qui est retournée doit inclure une propriété nommée **Reasons**.
- La propriété Reasons doit être un tableau.
- Chaque élément du tableau doit être une table de hachage avec les clés **Code** et **Phrase**.

La propriété Reasons est utilisée par le service pour normaliser la manière dont les informations sont présentées quand un ordinateur n’est pas conforme. Vous pouvez considérer chaque élément de Reasons comme une « raison » pour laquelle la ressource n’est pas conforme. La propriété est un tableau, car une ressource peut ne pas être conforme pour plusieurs raisons.

Les propriétés **Code** et **Phrase** sont attendues par le service. Lorsque vous créez une ressource personnalisée, définissez le texte (en général stdout) que vous souhaitez afficher pour expliquer pourquoi la ressource n’est pas conforme à la valeur de **Phrase**. **Code** a des exigences de mise en forme spécifiques, visant à afficher clairement les informations sur la ressource utilisée pour effectuer l’audit. Cette solution rend Guest Configuration extensible. Vous pouvez exécuter n’importe quelle commande tant que la sortie peut être retournée sous forme de valeur de chaîne pour la propriété **Phrase**.

- **Code** (chaîne) : nom de la ressource, répété, puis un nom abrégé sans espaces comme identificateur de la raison. Ces trois valeurs doivent être séparées par des points-virgules, sans espaces.
  - Par exemple, `registry:registry:keynotpresent`.
- **Phrase** (chaîne) : texte lisible par l’utilisateur expliquant pourquoi le paramètre n’est pas conforme.
  - Par exemple, `The registry key $key is not present on the machine.`.

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

La propriété Reasons doit également être ajoutée au schéma MOF de la ressource en tant que classe incorporée.

```mof
[ClassVersion("1.0.0.0")] 
class Reason
{
    [Read] String Phrase;
    [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
    [Key, Description("Example description")] String Example;
    [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

### <a name="configuration-requirements"></a>Exigences de configuration

Le nom de la configuration personnalisée doit être cohérent partout. Le nom du fichier .zip du package de contenu, le nom de la configuration dans le fichier MOF et le nom de l’affectation d’invité dans le modèle Resource Manager doivent être identiques.

### <a name="scaffolding-a-guest-configuration-project"></a>Structuration d’un projet Guest Configuration

Les développeurs qui souhaitent accélérer la mise en œuvre et travailler à partir de l’exemple de code peuvent installer un projet de la communauté appelé **Guest Configuration Project**. Le projet installe un modèle pour le module PowerShell [Plaster](https://github.com/powershell/plaster). Cet outil peut être utilisé pour structurer un projet, (y compris une configuration de travail et un exemple de ressource), ainsi qu’un ensemble de tests [Pester](https://github.com/pester/pester) pour valider le projet. Le modèle comprend également des exécuteurs de tâches pour Visual Studio Code permettant d’automatiser la création et la validation du package Guest Configuration. Pour plus d’informations, consultez le projet GitHub [Guest Configuration Project](https://github.com/microsoft/guestconfigurationproject).

Pour plus d’informations sur l’utilisation des configurations, consultez [Écrire, compiler et appliquer une configuration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Contenu attendu d’un artefact Guest Configuration

Le package obtenu est utilisé par Guest Configuration pour créer les définitions d’Azure Policy. Le package comprend les éléments suivants :

- La configuration DSC compilée au format MOF
- Dossier Modules
  - Module GuestConfiguration
  - Module DscNativeResources
  - Modules de ressources DSC (Windows) requis par MOF

Les cmdlets PowerShell aident à créer le package.
Aucun dossier de niveau racine ou dossier de version n’est requis.
Le format du package doit être un fichier .zip.

### <a name="storing-guest-configuration-artifacts"></a>Stockage des artefacts Guest Configuration

Le package .zip doit être stocké dans un emplacement accessible par les machines virtuelles gérées.
Exemples : référentiels GitHub, référentiel Azure ou stockage Azure. Si vous préférez ne pas rendre le package public, vous pouvez inclure un [jeton SAS](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) dans l’URL.
Vous pouvez également implémenter le [point de terminaison de service](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) pour les machines dans un réseau privé, bien que cette configuration s’applique uniquement à l’accès au package et ne communique pas avec le service.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Étape par étape, création d’une stratégie d’audit Guest Configuration personnalisée pour Windows

Créez une configuration DSC pour auditer les paramètres. L’exemple de script PowerShell suivant crée une configuration appelée **AuditBitLocker**, importe le module de ressources **PsDscResources** et utilise la ressource `Service` pour faire l’audit d’un service en cours d’exécution. Le script de configuration peut être exécuté à partir d’un ordinateur Windows ou macOS.

```powershell
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker ./Config
```

Enregistrez ce fichier sous le nom `config.ps1` dans le dossier du projet. Exécutez-le dans PowerShell en exécutant `./config.ps1` dans le terminal. Un fichier mof est créé.

La commande `Node AuditBitlocker` n’est pas techniquement obligatoire, mais elle produit un fichier `AuditBitlocker.mof` plutôt que `localhost.mof`par défaut. Le fait d’avoir le nom de fichier. mof à la suite de la configuration permet d’organiser facilement de nombreux fichiers à grande échelle.

Une fois la compilation du fichier MOF terminée, les fichiers de prise en charge doivent être regroupés en un package. Le package obtenu est utilisé par Guest Configuration pour créer les définitions d’Azure Policy.

La cmdlet `New-GuestConfigurationPackage` crée le package. Les modules nécessaires à la configuration doivent être disponibles dans `$Env:PSModulePath`. Paramètres de la cmdlet `New-GuestConfigurationPackage` lors de la création du contenu Windows :

- **Name** : Nom du package Guest Configuration.
- **Configuration** : Chemin d’accès complet au document de configuration DSC compilé.
- **Chemin d’accès** : Chemin d’accès au dossier de sortie. Ce paramètre est facultatif. S’il n’est pas spécifié, le package est créé dans le répertoire actif.

Exécutez la commande suivante pour créer un package à l’aide de la configuration fournie à l’étape précédente :

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

Après avoir créé le package de configuration et avant de le publier sur Azure, vous pouvez tester le package à partir de votre station de travail ou de votre environnement d’intégration CI/CD. La cmdlet GuestConfiguration `Test-GuestConfigurationPackage` comprend le même agent dans votre environnement de développement que celui utilisé dans les machines Azure. Via cette solution, vous pouvez effectuer un test d’intégration en local avant la publication dans les environnements cloud facturés.

Étant donné que l’agent évalue l’environnement local, vous devez, dans la plupart des cas, vous devez exécuter la cmdlet Test- sur la même plateforme de système d’exploitation que celle que vous envisagez d’auditer. Le test utilise uniquement les modules qui sont inclus dans le package de contenu.

Paramètres de la cmdlet `Test-GuestConfigurationPackage` :

- **Name** : Nom de la stratégie Guest Configuration.
- **Paramètre** : Paramètres de stratégie fournis au format Hashtable.
- **Chemin d’accès** : Chemin d’accès complet du package Guest Configuration.

Exécutez la commande suivante pour tester le package créé par l’étape précédente :

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

La cmdlet prend aussi en charge l’entrée depuis le pipeline PowerShell. Dirige la sortie de la cmdlet `New-GuestConfigurationPackage` vers la cmdlet `Test-GuestConfigurationPackage`.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

L’étape suivante consiste à publier le fichier dans le stockage Blob. Le script ci-dessous contient une fonction que vous pouvez utiliser pour automatiser cette tâche. Les commandes utilisées dans la fonction `publish` requièrent le module `Az.Storage`.

```azurepowershell-interactive
function publish {
    param(
    [Parameter(Mandatory=$true)]
    $resourceGroup,
    [Parameter(Mandatory=$true)]
    $storageAccountName,
    [Parameter(Mandatory=$true)]
    $storageContainerName,
    [Parameter(Mandatory=$true)]
    $filePath,
    [Parameter(Mandatory=$true)]
    $blobName
    )

    # Get Storage Context
    $Context = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
        -Name $storageAccountName | `
        ForEach-Object { $_.Context }

    # Upload file
    $Blob = Set-AzStorageBlobContent -Context $Context `
        -Container $storageContainerName `
        -File $filePath `
        -Blob $blobName `
        -Force

    # Get url with SAS token
    $StartTime = (Get-Date)
    $ExpiryTime = $StartTime.AddYears('3')  # THREE YEAR EXPIRATION
    $SAS = New-AzStorageBlobSASToken -Context $Context `
        -Container $storageContainerName `
        -Blob $blobName `
        -StartTime $StartTime `
        -ExpiryTime $ExpiryTime `
        -Permission rl `
        -FullUri

    # Output
    return $SAS
}

# replace the $storageAccountName value below, it must be globally unique
$resourceGroup        = 'policyfiles'
$storageAccountName   = 'youraccountname'
$storageContainerName = 'artifacts'

$uri = publish `
  -resourceGroup $resourceGroup `
  -storageAccountName $storageAccountName `
  -storageContainerName $storageContainerName `
  -filePath ./AuditBitlocker.zip `
  -blobName 'AuditBitlocker'
```

Une fois qu’un package de stratégie personnalisée Guest Configuration a été créé et chargé, créez la définition de la stratégie Guest Configuration. La cmdlet `New-GuestConfigurationPolicy` prend un package de stratégie personnalisé et crée une définition de stratégie.

Paramètres de la cmdlet `New-GuestConfigurationPolicy` :

- **ContentUri** : URI http(s) publique du package de contenu Guest Configuration.
- **DisplayName** : Nom d'affichage de la stratégie.
- **Description** : Description de la stratégie.
- **Paramètre** : Paramètres de stratégie fournis au format Hashtable.
- **Version** : Version de stratégie.
- **Chemin d’accès** : Chemin de destination où les définitions de stratégie sont créées.
- **Plateforme** : Plateforme cible (Windows/Linux) pour la stratégie et le package de contenu Guest Configuration.

L’exemple suivant crée les définitions de stratégie dans le chemin d’accès spécifié à partir d’un package de stratégie personnalisé :

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

Les fichiers suivants sont créés par `New-GuestConfigurationPolicy` :

- **auditIfNotExists.json**
- **deployIfNotExists.json**
- **Initiative.json**

La sortie de la cmdlet retourne un objet contenant le nom complet de l’initiative et le chemin d’accès aux fichiers de stratégie.

Enfin, publiez les définitions de stratégie à l’aide de la cmdlet `Publish-GuestConfigurationPolicy`. La cmdlet ne dispose que du paramètre **Path** qui pointe vers l’emplacement des trois fichiers JSON créés par `New-GuestConfigurationPolicy`.

Pour exécuter la commande Publish, vous devez avoir accès à la création de stratégies dans Azure. Les exigences spécifiques en matière d’autorisations sont documentées dans la page [vue d’ensemble d’Azure Policy](../overview.md). Le meilleur rôle intégré est le rôle **Contributeur de la stratégie de ressource**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

La cmdlet `Publish-GuestConfigurationPolicy` accepte le chemin à partir du pipeline PowerShell. Cette fonctionnalité signifie que vous pouvez créer les fichiers de stratégie et les publier dans un ensemble unique de commandes redirigées.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

Avec la stratégie créée dans Azure, la dernière étape consiste à assigner l’initiative. Découvrez comment assigner l’initiative avec le [portail](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md) et [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Les stratégies Guest Configuration doivent **toujours** être assignées via l’initiative qui combine les stratégies _AuditIfNotExists_ et _DeployIfNotExists_. Si seule la stratégie _AuditIfNotExists_ est assignée, les prérequis ne sont pas déployés et la stratégie montre toujours que « 0 » serveur est conforme.

L’affectation d’une définition de stratégie avec l’effet _DeployIfNotExists_ requiert un niveau d’accès supplémentaire. Pour accorder le privilège le plus bas, vous pouvez créer une définition de rôle personnalisée qui étend le rôle **Contributeur de stratégie de ressource**. L’exemple ci-dessous crée un rôle appelé **Contributeur de stratégie de ressource DINE** avec l’autorisation supplémentaire _Microsoft.Authorization/roleAssignments/write_.

```azurepowershell-interactive
$subscriptionid = '00000000-0000-0000-0000-000000000000'
$role = Get-AzRoleDefinition "Resource Policy Contributor"
$role.Id = $null
$role.Name = "Resource Policy Contributor DINE"
$role.Description = "Can assign Policies that require remediation."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/$subscriptionid")
New-AzRoleDefinition -Role $role
```

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Utilisation de paramètres dans des stratégies personnalisées Guest Configuration

Guest Configuration prend en charge la substitution des propriétés d’une configuration lors d’une exécution. Cette fonctionnalité signifie que les valeurs du fichier MOF dans le package n’ont pas à être considérées comme statiques. Les valeurs de substitution sont fournies via Azure Policy et n’impactent pas la création ni la compilation des configurations.

Les cmdlets `New-GuestConfigurationPolicy` et `Test-GuestConfigurationPolicyPackage` incluent un paramètre nommé **Paramètres**. Ce paramètre utilise une définition hashtable qui comprend toutes les informations concernant chaque paramètre et crée les sections nécessaires de chaque fichier de la définition Azure Policy.

L’exemple suivant crée une définition de stratégie pour auditer un service, que l’utilisateur sélectionne dans une liste au moment de l’attribution de la stratégie.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="policy-lifecycle"></a>Cycle de vie de la stratégie

Si vous souhaitez publier une mise à jour de la stratégie, deux champs sont importants.

- **Version** : Lorsque vous exécutez l’applet de commande `New-GuestConfigurationPolicy`, vous devez spécifier un numéro de version supérieur à celui actuellement publié. Cette propriété met à jour la version de l’attribution Guest Configuration pour que l’agent reconnaisse le package mis à jour.
- **contentHash** : Cette propriété est automatiquement mise à jour par l’applet de commande `New-GuestConfigurationPolicy`. Il s’agit d’une valeur de hachage du package créé par `New-GuestConfigurationPackage`. Cette propriété doit être correcte pour le fichier `.zip` que vous publiez. Si seule la propriété **contentUri** est mise à jour, l’extension n’accepte pas le package de contenu.

Le moyen le plus simple de publier un package mis à jour consiste à répéter le processus décrit dans cet article et à fournir un numéro de version mis à jour. Ce processus garantit que toutes les propriétés ont été correctement mises à jour.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Conversion de contenu de stratégie de groupe Windows en configuration d’invité Azure Policy

La configuration d’invité, lors de l’audit de machines Windows, est une implémentation de la syntaxe de Desired State Configuration PowerShell. La communauté DSC a publié des outils pour convertir les modèles de stratégie de groupe exportés au format DSC. En utilisant cet outil avec les cmdlets de configuration d’invité décrites ci-dessus, vous pouvez convertir du contenu de stratégie de groupe Windows et le compresser/publier pour Azure Policy à des fins d’audit. Pour plus d’informations sur l’utilisation de l’outil, consultez l’article [Démarrage rapide : Convertir une stratégie de groupe en DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).
Une fois que le contenu a été converti, les étapes ci-dessus pour créer un package et le publier en tant qu’Azure Policy sont les mêmes que pour tout contenu DSC.

## <a name="optional-signing-guest-configuration-packages"></a>Facultatif : Signature des packages Guest Configuration

Les stratégies personnalisées Guest Configuration utilisent le hachage SHA256 pour vérifier que le package de stratégie n’a pas changé.
Si vous le souhaitez, les clients peuvent également utiliser un certificat pour signer des packages et forcer l’extension Guest Configuration à autoriser uniquement le contenu signé.

Pour activer ce scénario, vous devez effectuer deux étapes. Exécutez l’applet de commande pour signer le package de contenu et ajoutez une balise aux machines qui doivent nécessiter du code pour être signées.

Pour utiliser la fonctionnalité de validation de signature, exécutez la cmdlet `Protect-GuestConfigurationPackage` pour signer le package avant sa publication. Cette cmdlet nécessite un certificat de « signature du code ».

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Paramètres de la cmdlet `Protect-GuestConfigurationPackage` :

- **Chemin d’accès** : Chemin d’accès complet du package Guest Configuration.
- **Certificat** : Certificat de signature de code pour signer le package. Ce paramètre est uniquement pris en charge lors de la signature de contenu pour Windows.

L’agent GuestConfiguration s’attend à trouver la clé publique du certificat dans « Autorités de certification racines de confiance » sur des machines Windows et dans le chemin `/usr/local/share/ca-certificates/extra` sur des machines Linux. Pour que le nœud vérifie le contenu signé, installez la clé publique du certificat sur la machine avant d’appliquer la stratégie personnalisée. Ce processus peut être effectué à l’aide de n’importe quelle technique à l’intérieur de la machine virtuelle ou à l’aide d’Azure Policy. Vous trouverez un exemple modèle en suivant [ce lien](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
La stratégie d’accès Key Vault doit autoriser le fournisseur de ressources de calcul à accéder aux certificats lors des déploiements. Pour les étapes détaillées, consultez [Configurer Key Vault pour des machines virtuelles dans Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Voici un exemple d’exportation de la clé publique à partir d’un certificat de signature, à importer vers la machine.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Une fois votre contenu publié, ajoutez une balise nommée `GuestConfigPolicyCertificateValidation` et avec une valeur `enabled` à toutes les machines virtuelles où la signature du code doit être requise. Pour plus d'informations sur la façon dont les balises peuvent être délivrées à grande échelle à l'aide d'Azure Policy, consultez les [Exemples de balises](../samples/built-in-policies.md#tags). Une fois cette balise en place, la définition de stratégie générée via la cmdlet `New-GuestConfigurationPolicy` met en œuvre l’exigence via l’extension Guest Configuration.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Résolution des problèmes liés aux attributions de stratégie Guest Configuration (préversion)

Un outil est disponible en préversion pour favoriser la résolution des problèmes liés aux attributions de configuration d’invité Azure Policy. L’outil est en préversion et a été publié sur PowerShell Gallery avec le nom de module [Guest Configuration Troubleshooter](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Pour plus d’informations sur les applets de commande de cet outil, utilisez la commande Get-Help dans PowerShell pour afficher les conseils intégrés. Comme l’outil fait l’objet de mises à jour fréquentes, c’est la meilleure façon d’obtenir les informations les plus récentes.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l’audit des machines virtuelles avec [Guest Configuration](../concepts/guest-configuration.md).
- Découvrez comment [créer des stratégies par programmation](programmatically-create.md).
- Découvrez comment [obtenir des données de conformité](get-compliance-data.md).
