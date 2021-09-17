---
title: Comment installer le module de création de configuration invité
description: Découvrez comment installer le module PowerShell pour créer et tester des définitions et des affectations de stratégie de configuration invité.
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: c32c405cffb71527e61b68f7fb532487d695743f
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773082"
---
# <a name="how-to-setup-a-guest-configuration-authoring-environment"></a>Comment configurer un environnement de création de configuration invité

Le module PowerShell `GuestConfiguration` automatise le processus de création de contenu personnalisé, notamment :

- La création d’un artefact de contenu de configuration invité (.zip)
- La validation du package conforme aux exigences
- L’installation locale de l’agent de configuration invité à des fins de test
- La validation du package pouvant être utilisée pour auditer les paramètres d’une machine
- La validation du package pouvant être utilisée pour configurer les paramètres d’une machine
- La publication du package dans le stockage Azure
- La création d’une définition de stratégie
- La publication de la stratégie

La prise en charge de l’application de configurations via la configuration invité est introduite dans la version `3.4.2`.

> [!IMPORTANT]
> Les packages personnalisés qui auditent l’état d’un environnement sont généralement disponibles, mais les packages qui appliquent des configurations sont **en préversion**. **Les limites suivantes s'appliquent :**
> 
> Pour tester la création et l’application de configurations sur Linux, le module `GuestConfiguration` n’est disponible que sur Ubuntu 18, mais le package et les définitions de stratégie générés par le module peuvent être utilisés sur n’importe quelle version de distribution Linux prise en charge dans Azure ou Arc.
>
> Les packages de test sur MacOS ne sont pas disponibles.

### <a name="base-requirements"></a>Configuration de base requise

Systèmes d’exploitation sur lesquels le module peut être installé :

- Ubuntu 18
- Windows

Le module peut être installé sur une machine exécutant PowerShell 7. Installez les versions de PowerShell répertoriées ci-dessous.

| Système d’exploitation | Version de PowerShell |
|-|-|
|Windows|[PowerShell 7.1.3](https://github.com/PowerShell/PowerShell/releases/tag/v7.1.3)|
|Ubuntu 18|[PowerShell 7.2.0-preview.6](https://github.com/PowerShell/PowerShell/releases/tag/v7.2.0-preview.6)|

Le module `GuestConfiguration` requiert les logiciels suivants :

- Azure PowerShell 5.9.0 ou ultérieur. Les modules Az requis sont installés automatiquement avec le module `GuestConfiguration`, sinon vous pouvez suivre [ces instructions](/powershell/azure/install-az-ps).
  - Seuls les modules Az « Az.Accounts », « Az.Resources » et « Az.Storage » sont requis.
- module `PSDesiredStateConfiguration`.

### <a name="install-the-module-from-the-powershell-gallery"></a>Installer le module à partir de PowerShell Gallery

Pour installer le module `GuestConfiguration` sur Windows ou Linux, exécutez la commande suivante dans PowerShell 7.

```powershell
# Install the guest configuration DSC resource module from PowerShell Gallery
Install-Module -Name GuestConfiguration
```

Vérifiez que le module a été importé :

```powershell
# Get a list of commands for the imported GuestConfiguration module
Get-Command -Module 'GuestConfiguration'
```

## <a name="update-and-import-the-psdesiredstateconfiguration-module-on-linux"></a>Mettre à jour et importer le module PSDesiredStateConfiguration sur Linux

À compter de PowerShell 7.2 Preview 6, DSC est publié indépendamment de PowerShell en tant que module dans PowerShell Gallery. Pour installer DSC version 3 dans votre environnement PowerShell sur Linux, exécutez la commande ci-dessous.

```powershell
# Install the DSC module before compiling using the Configuration keyword
Install-Module PSDesiredStateConfiguration -AllowPreRelease -Force
```

## <a name="next-steps"></a>Étapes suivantes

- [Créez un artefact de package](./guest-configuration-create.md) pour la configuration invité.
- [Testez l’artefact de package](./guest-configuration-create-test.md) à partir de votre environnement de développement.
- Utilisez le module `GuestConfiguration` afin de [créer une définition d’Azure Policy](./guest-configuration-create-definition.md) pour une gestion à l’échelle de votre environnement.
- [Attribuez votre définition de stratégie personnalisée](../assign-policy-portal.md) à l’aide du Portail Azure.
- Découvrez comment visualiser les [informations relatives à la conformité pour les attributions de stratégie de la configuration invité](./determine-non-compliance.md#compliance-details-for-guest-configuration).
