---
title: Comment créer des artefacts de package de configuration d’invité personnalisés
description: Découvrez comment créer un fichier de package de configuration invité.
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: 12767e40ef99cf218666b6dc540a5ae1c2e2bffa
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772536"
---
# <a name="how-to-create-custom-guest-configuration-package-artifacts"></a>Comment créer des artefacts de package de configuration invité personnalisés

Avant de commencer, nous vous conseillons de lire la page de présentation de la [configuration invité](../concepts/guest-configuration.md).

Lors de l’audit / de la configuration Windows et Linux, la configuration invité utilise une [Configuration d’état désiré](/powershell/scripting/dsc/overview/overview) (DSC). La configuration DSC définit la condition dans laquelle la machine doit se trouver.

> [!IMPORTANT]
> Les packages personnalisés qui auditent l’état d’un environnement sont généralement disponibles, mais les packages qui appliquent des configurations sont **en préversion**. **Les limites suivantes s'appliquent :**
> 
> Pour utiliser les packages de configuration invité qui appliquent des configurations, l’extension de configuration invité de machine virtuelle Azure version **1.29.24** ou ultérieure, ou l’agent Arc version **1.10.0** ou version ultérieure, sont requis.
> 
> Pour tester la création et l’application de configurations sur Linux, le  module `GuestConfiguration` n’est disponible que sur Ubuntu 18, mais le package et les stratégies générés par le module peuvent être utilisés sur n’importe quelle version de distribution Linux prise en charge dans Azure ou arc.
>
> Les packages de test sur MacOS ne sont pas disponibles.
> 
> N’utilisez pas de secrets ni d’informations confidentielles dans des packages de contenu personnalisés.

Utilisez les actions suivantes pour créer votre propre configuration pour la gestion de l’état d’une machine Azure ou non-Azure.

## <a name="install-powershell-7-and-required-powershell-modules"></a>Installer PowerShell 7 et les modules PowerShell requis

Tout d’abord, assurez-vous d’avoir suivi toutes les étapes de la page [Comment configurer un environnement de création de configuration invité](./guest-configuration-create-setup.md) pour installer la version requise de PowerShell pour votre système d’exploitation, le module `GuestConfiguration` et, si nécessaire, le module `PSDesiredStateConfiguration`.

## <a name="author-a-configuration"></a>Créer une configuration

Avant de créer un package de configuration, créez et compilez une configuration DSC.
Si nécessaire, des exemples de configurations sont disponibles pour Windows et Linux.

> [!IMPORTANT]
> Lors de la compilation de configurations Windows, utilisez `PSDesiredStateConfiguration` version `2.0.5` (version stable). Lors de la compilation de configurations pour Linux, installez la version préliminaire`3.0.0`.

Un exemple est donnée dans le [document de Prise en main](/powershell/scripting/dsc/getting-started/wingettingstarted#define-a-configuration-and-generate-the-configuration-document) DSC pour Windows.

Pour Linux, vous devrez créer un module de ressources DSC personnalisé à l’aide des [classes PowerShell](/powershell/scripting/dsc/resources/authoringResourceClass).
Un exemple complet d’une ressource personnalisée et d’une configuration est disponible (et a été testé avec la configuration invité) dans la page docs de PowerShell [Ecriture d’une ressource DSC personnalisée avec les classes PowerShell](/powershell/scripting/dsc/resources/authoringResourceClass).

## <a name="create-a-configuration-package-artifact"></a>Créer un artefact de package de configuration

Une fois la compilation du fichier MOF terminée, les fichiers de prise en charge doivent être regroupés en un package.
Le package obtenu est utilisé par la configuration invité pour créer les définitions de la Stratégie Azure.

La cmdlet `New-GuestConfigurationPackage` crée le package. Les modules nécessaires à la configuration doivent être disponibles dans `$Env:PSModulePath` pour l’environnement de développement afin que les commandes du module puissent les ajouter au package.

Paramètres de la cmdlet `New-GuestConfigurationPackage` lors de la création du contenu Windows :

- **Nom** : nom du package de la configuration invité.
- **Configuration** : Chemin d’accès complet au document de configuration DSC compilé.
- **Chemin d’accès** : Chemin d’accès au dossier de sortie. Ce paramètre est facultatif. S’il n’est pas spécifié, le package est créé dans le répertoire actif.
- **Type** : (audit, AuditandSet) détermine si la configuration doit uniquement effectuer un audit ou si la configuration doit être appliquée et changer l’état de la machine. La valeur par défaut est « Audit ».

Cette étape ne nécessite pas d’élévation. L’applet de commande force est utilisée pour remplacer les packages existants, si vous exécutez la commande plusieurs fois.

Les commandes suivantes créent des artefacts de package :

```powershell
# Create a package that will only audit compliance
New-GuestConfigurationPackage `
  -Name 'MyConfig' `
  -Configuration './Config/MyConfig.mof' `
  -Type Audit `
  -Force
```

```powershell
# Create a package that will audit and apply the configuration (Set)
New-GuestConfigurationPackage `
  -Name 'MyConfig' `
  -Configuration './Config/MyConfig.mof' `
  -Type AuditAndSet `
  -Force
```

Un objet est retourné avec le Nom et le Chemin d’accès du package créé.

```
Name      Path                                                    
----      ----                                                    
MyConfig  /Users/.../MyConfig/MyConfig.zip
```

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Contenu attendu d’un artefact de configuration invité

Le package obtenu est utilisé par la configuration invité pour créer les définitions de la Stratégie Azure. Le package comprend les éléments suivants :

- La configuration DSC compilée au format MOF
- Dossier Modules
  - Module GuestConfiguration
  - Module DscNativeResources
  - Modules de ressources DSC requis par le MOF
- Fichier metaconfig qui stocke le package `type` et `version`

L’applet de commande PowerShell crée le package de fichier .zip. Aucun dossier de niveau racine ou dossier de version n’est requis. Le format du package doit être un fichier .zip et ne peut pas dépasser une taille totale de 100 Mo lors de la décompression.

## <a name="extending-guest-configuration-with-third-party-tools"></a>Extension de la configuration invité avec des outils tiers

Les packages d’artefacts pour la configuration invité peuvent être étendus pour inclure des outils tiers. L’extension de la configuration invité requiert le développement de deux composants.

- Une ressource Desired State Configuration (DSC) qui gère toutes les activités liées à la gestion de l’outil tiers
  - Installer
  - Appeler
  - Convertir la sortie
- Contenu dans le format correct pour l’outil à consommer en mode natif

La ressource DSC requiert un développement personnalisé d’il n’existe pas encore de solution de communauté. Les solutions de la communauté peuvent être affichées en recherchant la balise [GuestConfiguration](https://www.powershellgallery.com/packages?q=Tags%3A%22GuestConfiguration%22) dans PowerShell Gallery.

> [!NOTE]
> L’extensibilité de la configuration invité est un scénario de type BYOL (apportez votre propre licence). Veillez à respecter les conditions générales de tout outil tiers avant de l’utiliser.

Une fois la ressource DSC installée dans l’environnement de développement, utilisez le paramètre **FilesToInclude** pour `New-GuestConfigurationPackage` afin d’inclure le contenu de la plateforme tierce dans l’artefact de contenu.

## <a name="next-steps"></a>Étapes suivantes

- [Testez l’artefact de package](./guest-configuration-create-test.md) à partir de votre environnement de développement.
- [Publiez l’artefact de package](./guest-configuration-create-publish.md) afin qu’il soit accessible à vos machines.
- Utilisez le module `GuestConfiguration` afin de [créer une définition de la Stratégie Azure](./guest-configuration-create-definition.md) pour une gestion à l’échelle de votre environnement.
- [Attribuez votre définition de stratégie personnalisée](../assign-policy-portal.md) à l’aide du portail Azure.
- Découvrez comment visualiser les [informations relatives à la conformité pour les attributions de stratégie de configuration d’invité](./determine-non-compliance.md#compliance-details-for-guest-configuration).
