---
title: Planification de la migration d’Azure Automation State Configuration vers la configuration invité
description: Cet article propose des procédures et des conseils techniques pour les clients désireux de passer de la version 2 de DSC dans Azure Automation à la version 3 dans Azure Policy.
ms.date: 07/1/2021
ms.topic: how-to
ms.openlocfilehash: ae387e59abe5cbff335b43cdd78030f32f6e6f8c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773084"
---
# <a name="azure-automation-state-configuration-to-guest-configuration-migration-planning"></a>Planification de la migration d’Azure Automation State Configuration vers la configuration invité

La configuration invité est la dernière implémentation des fonctionnalités fournies par Azure Automation State Configuration (également appelé Azure Automation Desired State Configuration ou AADSC).
Dans la mesure du possible, vous devez planifier le déplacement de votre contenu et de vos machines vers le nouveau service.
Cet article fournit des conseils sur le développement d’une stratégie de migration d’Azure Automation vers la configuration invité.

Les nouvelles fonctionnalités de la configuration invité répondent aux principales demandes des clients :

- Taille maximale des configurations augmentée (100 Mo)
- Rapports avancés grâce à Azure Resource Graph, y compris l'ID et l'état des ressources
- Gérer plusieurs configurations pour la même machine
- Lorsque des machines dévient de l’état souhaité, vous contrôlez à quel moment la correction a lieu
- Linux et Windows utilisent tous deux des ressources DSC basées sur PowerShell

Avant de commencer, il est judicieux de lire les informations conceptuelles sur la page [Azure Policy Guest Configuration](../concepts/guest-configuration.md).

## <a name="understand-migration"></a>Comprendre la migration

La meilleure approche de la migration consiste à redéployer le contenu en premier, puis à migrer les machines. Les étapes prévues pour la migration sont décrites ci-après.

- Exporter des configurations à partir d’Azure Automation
- Découvrir les exigences des modules et les charger dans votre environnement
- Compiler les configurations
- Créer et publier des packages de configuration invité
- Tester des packages de configuration invité
- Intégrer des machines hybrides à Azure Arc
- Désinscrire des serveurs d’Azure Automation State Configuration
- Attribuer des configurations aux serveurs à l’aide de la configuration invité

La configuration invité utilise DSC version 3 avec PowerShell version 7. DSC version 3 peut coexister avec des versions plus anciennes de DSC sous [Windows](/powershell/scripting/dsc/getting-started/wingettingstarted) et [Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).
Les implémentations sont séparées. Toutefois, il n’y a aucune détection de conflit.

La configuration invité ne nécessite pas la publication de modules ou de configurations dans un service, ni la compilation dans un service. Au lieu de cela, le contenu est développé et testé à l’aide d’outils spécialement conçus et publié partout où la machine peut accéder via HTTPS (généralement Stockage Blob Azure).

Si vous décidez que, pour votre migration, il est judicieux d’avoir des machines dans les deux services pendant un certain temps, même si cela peut être difficile à gérer, il n’y pas d’obstacles techniques. Les deux services sont indépendants.

## <a name="export-content-from-azure-automation"></a>Exporter du contenu à partir d’Azure Automation

Commencez par découvrir et exporter le contenu d’Azure Automation State Configuration dans un environnement de développement dans lequel vous créez, testez et publiez des packages de contenu pour la configuration invité.

### <a name="configurations"></a>Configurations

Seuls les scripts de configuration peuvent être exportés à partir d’Azure Automation. Il n’est pas possible d’exporter des « configurations de nœud » ou des fichiers MOF compilés.
Si vous avez publié des fichiers MOF directement dans le compte Automation et que vous n’avez plus accès au fichier d’origine, vous devez exécuter une recompilation à partir de vos scripts de configuration privés, ou éventuellement recréer la configuration si l’original est introuvable.

Pour exporter des scripts de configuration à partir d’Azure Automation, identifiez d’abord le compte Azure Automation contenant les configurations, ainsi que le nom du groupe de ressources dans lequel le compte Automation est déployé.

Installez le module PowerShell « AZ. Automation ».

```powershell
Install-Module Az.Automation
```

Ensuite, utilisez la commande « AzAutomationAccount » pour identifier vos comptes Automation et le groupe de ressources dans lequel ils sont déployés.
Les propriétés « ResourceGroupName » et « AutomationAccountName » sont importantes pour les étapes suivantes.

```powershell
Get-AzAutomationAccount

SubscriptionId        : <your subscription id>
ResourceGroupName     : <your resource group name>
AutomationAccountName : <your automation account name>
Location              : centralus
State                 :
Plan                  :
CreationTime          : 6/30/2021 11:56:17 AM -05:00
LastModifiedTime      : 6/30/2021 11:56:17 AM -05:00
LastModifiedBy        :
Tags                  : {}
```

Découvrez les configurations dans votre compte Automation. La sortie contient une entrée par configuration. Si vous en avez plusieurs, stockez les informations en tant que variable pour faciliter leur utilisation.

```powershell
Get-AzAutomationDscConfiguration -ResourceGroupName <your resource group name> -AutomationAccountName <your automation account name>

ResourceGroupName     : <your resource group name>
AutomationAccountName : <your automation account name>
Location              : centralus
State                 : Published
Name                  : <your configuration name>
Tags                  : {}
CreationTime          : 6/30/2021 12:18:26 PM -05:00
LastModifiedTime      : 6/30/2021 12:18:26 PM -05:00
Description           :
Parameters            : {}
LogVerbose            : False
```

Enfin, exportez chaque configuration dans un fichier de script local à l’aide de la commande « Export-AzAutomationDscConfiguration ». Le nom de fichier résultant utilise le modèle `\ConfigurationName.ps1`.

```powershell
Export-AzAutomationDscConfiguration -OutputFolder /<location on your machine> -ResourceGroupName <your resource group name> -AutomationAccountName <your automation account name> -name <your configuration name>

UnixMode   User             Group                 LastWriteTime           Size Name
--------   ----             -----                 -------------           ---- ----
                                               12/31/1600 18:09
```

#### <a name="export-configurations-using-the-powershell-pipeline"></a>Exporter des configurations à l’aide du pipeline PowerShell

Une fois que vous avez découvert vos comptes et le nombre de configurations, vous souhaiterez peut-être exporter toutes les configurations dans un dossier local sur votre machine.
Pour automatiser ce processus, chaînez la sortie de chaque commande ci-dessus pour qu’elle alimente la suivante.

L’exemple exporte 5 configurations. Le modèle de sortie est la seule indication de réussite.

```powershell
Get-AzAutomationAccount | Get-AzAutomationDscConfiguration | Export-AzAutomationDSCConfiguration -OutputFolder /<location on your machine>

UnixMode   User             Group                 LastWriteTime           Size Name
--------   ----             -----                 -------------           ---- ----
                                               12/31/1600 18:09
                                               12/31/1600 18:09
                                               12/31/1600 18:09
                                               12/31/1600 18:09
                                               12/31/1600 18:09
```

#### <a name="consider-decomposing-complex-configuration-files"></a>Envisager de décomposer les fichiers de configuration complexes

La configuration invité peut gérer plusieurs configurations par machine.
De nombreuses configurations écrites pour Azure Automation State Configuration supposaient la limitation de la gestion d’une seule configuration par machine. Pour tirer parti des fonctionnalités étendues offertes par la configuration invité, il est possible de diviser les fichiers de configuration volumineux en plusieurs configurations plus petites, chacune gérant un scénario spécifique.

Il n’existe pas d’orchestration dans la configuration invité pour contrôler l’ordre de tri des configurations. Par conséquent, conservez les étapes d’une configuration dans un même package si elles doivent se produire de manière séquentielle.

### <a name="modules"></a>Modules

Il n’est pas possible d’exporter des modules à partir d’Azure Automation ou de mettre automatiquement en corrélation les configurations qui nécessitent tel ou tel module ou version. Vous devez disposer des modules dans votre environnement local pour créer un nouveau package de configuration invité. Pour créer une liste des modules dont vous avez besoin pour la migration, utilisez PowerShell pour interroger Azure Automation concernant le nom et la version des modules.

Si vous utilisez des modules personnalisés qui n’existent que dans votre environnement de développement privé, il n’est pas possible de les exporter à partir d’Azure Automation.

Si un module personnalisé est requis pour une configuration, qu’il se trouve dans le compte, mais que vous ne pouvez pas le trouver dans votre environnement, vous ne pourrez pas compiler la configuration, ce qui signifie que vous ne pourrez pas migrer la configuration.

#### <a name="list-modules-imported-in-azure-automation"></a>Répertorier les modules importés dans Azure Automation

Pour récupérer une liste de tous les modules installés dans votre compte Automation, utilisez la commande `Get-AzAutomationModule`. La propriété « IsGlobal » vous indique si le module est toujours intégré à Azure Automation, ou s’il a été publié sur le compte.

Par exemple, pour créer une liste de tous les modules publiés sur l’un de vos comptes.

```powershell
Get-AzAutomationAccount | Get-AzAutomationModule | ? IsGlobal -eq $false
```

Vous pouvez également utiliser PowerShell Gallery pour vous aider à trouver des détails sur les modules qui sont disponibles publiquement. Par exemple, la liste des modules qui sont intégrés aux nouveaux comptes Automation et qui contiennent des ressources DSC, est générée par l’exemple suivant.

```powershell
Get-AzAutomationAccount | Get-AzAutomationModule | ? IsGlobal -eq $true | Find-Module -erroraction silentlycontinue | ? {'' -ne $_.Includes.DscResource} | Select Name, Version -Unique | format-table -AutoSize

Name                       Version
----                       -------
AuditPolicyDsc             1.4.0
ComputerManagementDsc      8.4.0
PSDscResources             2.12.0
SecurityPolicyDsc          2.10.0
xDSCDomainjoin             1.2.23
xPowerShellExecutionPolicy 3.1.0.0
xRemoteDesktopAdmin        1.1.0.0
```

#### <a name="download-modules-from-powershell-gallery-or-powershellget-repository"></a>Télécharger des modules à partir du référentiel PowerShellGet ou PowerShell Gallery

Si les modules ont été importés à partir de PowerShell Gallery, vous pouvez chaîner la sortie de `Find-Module` directement dans `Install-Module`. La chaînage de la sortie entre les commandes est une solution qui permet de charger un environnement de développement avec tous les modules actuellement dans un compte Automation qui sont accessibles publiquement dans PowerShell Gallery.

La même approche peut être utilisée pour extraire des modules d’un flux NuGet personnalisé, si le flux est inscrit dans votre environnement local en tant que [référentiel PowerShellGet](/powershell/scripting/gallery/how-to/working-with-local-psrepositories).

Dans l’exemple, la commande `Find-Module` ne supprime pas les erreurs, ce qui signifie que tous les modules introuvables dans la galerie renvoient un message d’erreur.

```powershell
Get-AzAutomationAccount | Get-AzAutomationModule | ? IsGlobal -eq $false | Find-Module | ? {'' -ne $_.Includes.DscResource} | Install-Module

  Installing package xWebAdministration'

    [                                                                                        ]
```

#### <a name="inspecting-configuration-scripts-for-module-requirements"></a>Inspection des scripts de configuration pour les exigences des modules

Si vous avez exporté des scripts de configuration à partir d’Azure Automation, vous pouvez également consulter le contenu pour plus d’informations sur les modules requis pour compiler chaque configuration dans un fichier MOF. Cette approche n’est nécessaire que si vous trouvez des configurations dans vos comptes Automation où les modules ont été supprimés.
Les configurations ne sont plus utiles pour les machines, mais elles peuvent toujours figurer dans le compte.

Vers le haut de chaque fichier, recherchez une ligne qui inclut « Import-DscResource ».
Cette commande s’applique uniquement à l’intérieur d’une configuration et est utilisée pour charger les modules au moment de la compilation.

Par exemple, la configuration « WindowsIISServerConfig » dans PowerShell Gallery contient les lignes de cet exemple.

```powershell
configuration WindowsIISServerConfig
{

Import-DscResource -ModuleName @{ModuleName = 'xWebAdministration';ModuleVersion = '1.19.0.0'}
Import-DscResource -ModuleName 'PSDesiredStateConfiguration'
```

La configuration nécessite que vous disposiez du module « xWebAdministration » version « 1.19.0.0 » et du module « PSDesiredStateConfiguration ».

### <a name="test-content-in-azure-guest-configuration"></a>Tester le contenu dans la configuration invité Azure

La meilleure façon d’évaluer si votre contenu issu d’Azure Automation State Configuration peut être utilisé avec la configuration invité consiste à suivre le didacticiel pas à pas de la page [Comment créer des artefacts de package de configuration invité personnalisés](./guest-configuration-create.md).

Quand vous atteignez l’étape [Créer une configuration](./guest-configuration-create.md#author-a-configuration), le script de configuration qui génère un fichier MOF doit être l’un de ceux que vous avez exportés à partir d’Azure Automation State Configuration. Vous devez avoir installé les modules PowerShell nécessaires dans votre environnement avant de pouvoir compiler la configuration dans un fichier MOF et créer un package de configuration invité.

#### <a name="what-if-a-module-does-not-work-with-guest-configuration"></a>Que se passe-t-il si un module ne fonctionne pas avec la configuration invité ?

Certains modules peuvent rencontrer des problèmes de compatibilité avec la configuration invité. Les problèmes les plus courants sont liés à .NET Framework et à .NET Core. Des informations techniques détaillées sont disponibles sur la page, [Différences entre Windows PowerShell 5.1 et PowerShell (core) 7.x](/powershell/scripting/whats-new/differences-from-windows-powershell)

Une option pour résoudre les problèmes de compatibilité consiste à exécuter des commandes dans Windows PowerShell à partir d’un module importé dans PowerShell 7, en exécutant `powershell.exe`.
Vous pouvez consulter un exemple de module qui utilise cette technique dans le référentiel Azure-Policy où il est utilisé pour auditer l’état de [Windows DSC Configuration](https://github.com/Azure/azure-policy/blob/bbfc60104c2c5b7fa6dd5b784b5d4713ddd55218/samples/GuestConfiguration/package-samples/resource-modules/WindowsDscConfiguration/DscResources/WindowsDscConfiguration/WindowsDscConfiguration.psm1#L97).

L’exemple illustre également une petite preuve de concept.

```powershell
# example function that could be loaded from module
function New-TaskResolvedInPWSH7 {
  # runs the fictitious command 'Get-myNotCompatibleCommand' in Windows PowerShell
  $compatObject = & powershell.exe -noprofile -NonInteractive -command { Get-myNotCompatibleCommand }
  # resulting object can be used in PowerShell 7
  return $compatObject
}
```

#### <a name="will-i-have-to-add-reasons-property-to-get-targetresource-in-all-modules-i-migrate"></a>Dois-je ajouter la propriété « Reasons » à Get-TargetResource dans tous les modules que je migre ?

L’implémentation de la [propriété « Reasons »](../concepts/guest-configuration-custom.md#special-requirements-for-get) offre une meilleure expérience lors de l’affichage des résultats d’une attribution de configuration à partir du Portail Azure. Si la méthode `Get` d’un module n’inclut pas la propriété « Reasons », la sortie générique est retournée avec les détails des propriétés retournées par la méthode `Get`. Par conséquent, cette propriété est facultative pour la migration.

## <a name="machines"></a>virtuelles

Une fois que vous avez fini de tester le contenu d’Azure Automation State Configuration dans la configuration invité, développez un plan pour la migration des machines.

Azure Automation State Configuration est disponible à la fois pour les machines virtuelles dans Azure et les machines hybrides en dehors d’Azure. Vous devez planifier chacun de ces scénarios à l’aide de différentes étapes.

### <a name="azure-vms"></a>Machines virtuelles Azure

Les machines virtuelles Azure disposent déjà d’une [ressource](../../../azure-resource-manager/management/overview.md#terminology) dans Azure, ce qui signifie qu’elles sont prêtes pour les affectations de configuration invité qui les associent à une configuration. Les tâches de haut niveau pour la migration des machines virtuelles Azure consistent à les supprimer d’Azure Automation State Configuration, puis à affecter des configurations à l’aide de la configuration invité.

Pour supprimer une machine d’Azure Automation State Configuration, suivez les étapes de la page [Comment supprimer une configuration et un nœud d’Automation State Configuration](../../../automation/state-configuration/remove-node-and-configuration-package.md).

Pour affecter des configurations à l’aide de la configuration invité, suivez les étapes des guides de démarrage rapide Azure Policy, tels que [Démarrage rapide : créer une affectation de stratégie pour identifier les ressources non conformes](../assign-policy-portal.md).
À l’étape 6 lors de la sélection d’une définition de stratégie, choisissez la définition qui applique une configuration que vous avez migrée à partir d’Azure Automation State Configuration.

### <a name="hybrid-machines"></a>Machines hybrides

Les machines en dehors d’Azure [peuvent être inscrites à Azure Automation State Configuration](../../../automation/automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines), mais elles n’ont pas de ressource de machine dans Azure. La connexion à Azure Automation est gérée par le service Configuration Manager local à l’intérieur de la machine et l’enregistrement du nœud est géré en tant que ressource dans le type de fournisseur Azure Automation.

Avant de supprimer une machine d’Azure Automation State Configuration, intégrez chaque nœud en tant que [serveur Azure Arc](../../../azure-arc/servers/overview.md).
L’intégration à Azure Arc crée une ressource de machine dans Azure afin que la machine puisse être gérée par Azure Policy. La machine peut être intégrée à Azure Arc à tout moment, mais vous pouvez utiliser Azure Automation State Configuration pour automatiser le processus.

Vous pouvez inscrire une machine auprès de serveurs Azure Arc à l’aide de PowerShell DSC.
Pour plus d’informations, consultez la page [Guide pratique pour installer l’agent Connected Machine à l’aide de Windows PowerShell DSC](../../../azure-arc/servers/onboard-dsc.md).
Toutefois, n’oubliez pas qu’Azure Automation State Configuration ne peut gérer qu’une seule configuration par machine, par compte Automation. Cela signifie que vous avez la possibilité d’exporter, de tester et de préparer votre contenu pour la configuration invité, puis de « basculer » la configuration du nœud dans Azure Automation pour une intégration à Azure Arc. Au cours de la dernière étape, vous supprimez l’inscription de nœud d’Azure Automation State Configuration et vous ne gérez plus que l’état de la machine via la configuration invité.

## <a name="troubleshooting-issues-when-exporting-content"></a>Résolution des problèmes lors de l’exportation de contenu

Des détails sur les problèmes connus sont fournis

### <a name="exporting-configurations-results-in--character-in-file-name"></a>L’exportation de configurations entraîne l’insertion du caractère « \\ » dans le nom du fichier

Lorsque vous utilisez PowerShell sur MacOS/Linux, vous rencontrez des problèmes liés aux noms de fichiers générés par `Export-AzAutomationDSCConfiguration`.

Comme solution, un module nommé [AADSCConfigContent](https://www.powershellgallery.com/packages/AADSCConfigContent/) a été publié dans PowerShell Gallery.
Le module n’a qu’une seule commande, qui exporte le contenu d’une configuration stockée dans Azure Automation en créant une requête REST au service.

## <a name="next-steps"></a>Étapes suivantes

- [Créez un artefact de package](./guest-configuration-create.md) pour la configuration invité.
- [Testez l’artefact de package](./guest-configuration-create-test.md) à partir de votre environnement de développement.
- [Publiez l’artefact de package](./guest-configuration-create-publish.md) afin qu’il soit accessible à vos machines.
- Utilisez le module `GuestConfiguration` afin de [créer une définition Azure Policy](./guest-configuration-create-definition.md) pour une gestion à l’échelle de votre environnement.
- [Attribuez votre définition de stratégie personnalisée](../assign-policy-portal.md) à l’aide du Portail Azure.
- Découvrez comment visualiser les [informations relatives à la conformité pour les attributions de stratégie de la configuration invité](./determine-non-compliance.md#compliance-details-for-guest-configuration).
