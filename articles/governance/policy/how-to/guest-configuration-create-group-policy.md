---
title: Comment créer une stratégie de configuration invité à partir de la Stratégie de groupe
description: Découvrez comment convertir une Stratégie de groupe en définition de stratégie
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: 12bd1c905c254f16da170cde4a4426a2aa0cb263
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772293"
---
# <a name="how-to-create-a-guest-configuration-policy-from-group-policy"></a>Comment créer une stratégie de configuration invité à partir de la Stratégie de groupe

Avant de commencer, il est recommandé de lire la page vue d’ensemble de la [configuration invité](../concepts/guest-configuration.md), ainsi que les informations relatives aux effets de la stratégie de configuration invité [Comment configurer les options de correction pour la configuration invité](../concepts/guest-configuration-policy-effects.md).

> [!IMPORTANT]
> La Conversion de la Stratégie de groupe en configuration invité est **en version préliminaire**. Tous les types de paramètres de la Stratégie de groupe n’ont pas les ressources DSC correspondantes disponibles pour PowerShell 7.
>
> Toutes les commandes de cette page doivent être exécutées dans **Windows PowerShell 5.1**.
> Les fichiers MOF de sortie résultants doivent ensuite être empaquetés à l’aide du module `GuestConfiguration` dans PowerShell 7.1.3 ou version ultérieure.
> 
> Les définitions de la stratégie de configuration invité personnalisées utilisant **AuditIfNotExists** sont Généralement disponibles, mais les définitions utilisant **DeployIfNotExists** avec la configuration invité sont **en préversion**.
> 
> L’extension de la configuration invité est requise pour les machines virtuelles Azure. Pour déployer l’extension à l’échelle sur toutes les machines, attribuez l’initiative de stratégie suivante : `Deploy prerequisites to enable guest configuration policies on
> virtual machines`
>
> N’utilisez pas de secrets ni d’informations confidentielles dans des packages de contenu personnalisés.

La communauté open source a publié le module [BaselineManagement](https://github.com/microsoft/BaselineManagement) pour convertir les modèles de [Stratégie de groupe](/support/windows-server/group-policy/group-policy-overview) exportés au format DSC PowerShell . En même temps que le module `GuestConfiguration`, vous pouvez créer un package de configuration invité pour Windows à partir d’Objets exportés de la Stratégie de groupe. Le package de configuration invité peut ensuite être utilisé pour auditer ou configurer des serveurs à l’aide de la stratégie locale, même s’ils ne sont pas joints à un domaine.

Dans ce guide, nous allons passer en revue le processus de création d’un package de configuration invité de la Stratégie Azure à partir d’un Objet de la Stratégie de groupe (GPO).

## <a name="download-required-powershell-modules"></a>Télécharger les modules PowerShell requis

Pour installer tous les modules requis dans PowerShell :

```powershell
Install-Module guestconfiguration
Install-Module baselinemanagement
```

Pour sauvegarder des Objets de la Stratégie de groupe (GPO) à partir de l’environnement d’un Répertoire actif, vous avez besoin des commandes PowerShell disponibles dans la Boîte à outils d’administration du Serveur distant  (RSAT).

Pour activer la RSAT pour la Console de gestion de la Stratégie de groupe sur Windows 10 :

```powerShell
Add-WindowsCapability -Online -Name 'Rsat.GroupPolicy.Management.Tools~~~~0.0.1.0'
Add-WindowsCapability -Online -Name 'Rsat.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0'
```

## <a name="export-and-convert-group-policy-to-guest-configuration"></a>Exporter et convertir la Stratégie de groupe vers la configuration invité

Il existe trois options pour exporter des fichiers de la Stratégie de groupe et les convertir en DSC afin de les utiliser dans la configuration invité.

- Exporter un Objet unique de la Stratégie de groupe
- Exporter les Objets fusionnés de la Stratégie de groupe pour une unité d’organisation
- Exporter les Objets fusionnés de la Stratégie de groupe à partir d’une machine

### <a name="single-group-policy-object"></a>Objet unique de la Stratégie de groupe

Identifiez le GUID de l’Objet de la Stratégie de groupe à exporter à l’aide des commandes du module `Group Policy`. Dans un environnement de grande taille, envisagez de diriger la sortie vers `where-object` et de filtrer par nom.

Exécutez ce qui suit dans un environnement **Windows PowerShell 5.1** sur une machine Windows attachée à un **domaine** :

```powershell
# List all Group Policy Objects
Get-GPO -all
```

Sauvegardez la Stratégie de groupe dans des fichiers. La commande accepte également un paramètre « Name », mais l’utilisation du GUID de la stratégie est moins sujette aux erreurs.

```powershell
Backup-GPO -Guid 'f0cf623e-ae29-4768-9bb4-406cce1f3cff' -Path C:\gpobackup\
```

```

The output of the command returns the details of the files.

ConfigurationScript                   Configuration                   Name
-------------------                   -------------                   ----
C:\convertfromgpo\myCustomPolicy1.ps1 C:\convertfromgpo\localhost.mof myCustomPolicy1
```

Examinez le script PowerShell exporté pour vérifier que tous les paramètres ont été remplis et qu’aucun message d’erreur n’a été écrit. Créez un nouveau package de configuration à l’aide du fichier MOF en suivant les instructions de la page [Comment créer des artefacts de package de configuration invité personnalisé](./guest-configuration-create.md).
Les étapes de création et de test du package de configuration invité doivent être exécutées dans un environnement PowerShell 7.

### <a name="merged-group-policy-objects-for-an-ou"></a>Exporter les Objets fusionnés de la Stratégie de groupe pour une unité d’organisation

Exportez la combinaison fusionnée des Objets de la Stratégie de groupe (similaire à un jeu de stratégie résultant) à une Unité d’organisation spécifiée. L’opération de fusion prend en compte l’état du lien, la mise en œuvre et l’accès, mais pas les filtres WMI.

```powershell
Merge-GPOsFromOU -Path C:\mergedfromou\ -OUDistinguishedName 'OU=mySubOU,OU=myOU,DC=mydomain,DC=local' -OutputConfigurationScript
```

La sortie de la commande retourne les informations des fichiers.

```powershell
Configuration                                Name    ConfigurationScript
-------------                                ----    -------------------
C:\mergedfromou\mySubOU\output\localhost.mof mySubOU C:\mergedfromou\mySubOU\output\mySubOU.ps1
```

### <a name="merged-group-policy-objects-from-within-a-machine"></a>Exporter les Objets fusionnés de la Stratégie de groupe à partir d’une machine

Vous pouvez également fusionner les stratégies appliquées à une machine spécifique, en exécutant la commande `Merge-GPOs` à partir de Windows PowerShell. Les filtres WMI sont évalués uniquement si vous effectuez une fusion à partir d’une machine.

```powershell
Merge-GPOs -OutputConfigurationScript -Path c:\mergedgpo
```

La sortie de la commande retournera les informations des fichiers.

```powershell
Configuration              Name                  ConfigurationScript                    PolicyDetails
-------------              ----                  -------------------                    -------------
C:\mergedgpo\localhost.mof MergedGroupPolicy_ws1 C:\mergedgpo\MergedGroupPolicy_ws1.ps1 {@{Name=myEnforcedPolicy; Ap...
```

## <a name="optional-download-sample-group-policy-files-for-testing"></a>FACULTATIF : Téléchargez les fichiers d’exemples de la Stratégie de groupe à des fins de test

Si vous n’êtes pas prêt à exporter les fichiers de la Stratégie de groupe à partir de l’environnement d’un Répertoire actif, vous pouvez télécharger la base de référence de sécurité du serveur Windows à partir de la Boîte à outils conforme et de sécurité Windows.

Créez un répertoire dédié et téléchargez la sécurité de base de Windows Server 2019 à partir de la boîte à outils pour la conformité de la sécurité Windows.

```azurepowershell-interactive
# Download the 2019 Baseline files from https://docs.microsoft.com/windows/security/threat-protection/security-compliance-toolkit-10
New-Item -Path 'C:\git\policyfiles\downloads' -Type Directory
Invoke-WebRequest -Uri 'https://download.microsoft.com/download/8/5/C/85C25433-A1B0-4FFA-9429-7E023E7DA8D8/Windows%2010%20Version%201909%20and%20Windows%20Server%20Version%201909%20Security%20Baseline.zip' -Out C:\git\policyfiles\downloads\Server2019Baseline.zip
```

Débloquez et développez la référence Server 2019 téléchargée.

```azurepowershell-interactive
Unblock-File C:\git\policyfiles\downloads\Server2019Baseline.zip
Expand-Archive -Path C:\git\policyfiles\downloads\Server2019Baseline.zip -DestinationPath C:\git\policyfiles\downloads\
```

Vérifiez le contenu de la référence Server 2019 à l’aide de **MapGuidsToGpoNames.ps1**.

```azurepowershell-interactive
# Show content details of downloaded GPOs
C:\git\policyfiles\downloads\Scripts\Tools\MapGuidsToGpoNames.ps1 -rootdir C:\git\policyfiles\downloads\GPOs\ -Verbose
```

## <a name="next-steps"></a>Étapes suivantes

- [Créez un artefact de package](./guest-configuration-create.md) pour la configuration invité.
- [Testez l’artefact de package](./guest-configuration-create-test.md) à partir de votre environnement de développement.
- [Publiez l’artefact de package](./guest-configuration-create-publish.md) afin qu’il soit accessible à vos machines.
- Utilisez le module `GuestConfiguration` afin de [créer une définition de la Stratégie Azure](./guest-configuration-create-definition.md) pour une gestion à l’échelle de votre environnement.
- [Attribuez votre définition de stratégie personnalisée](../assign-policy-portal.md) à l’aide du portail Azure.
- Découvrez comment visualiser les [informations relatives à la conformité pour les attributions de stratégie de configuration invité](./determine-non-compliance.md#compliance-details-for-guest-configuration).
