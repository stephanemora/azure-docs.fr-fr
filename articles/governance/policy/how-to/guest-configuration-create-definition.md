---
title: Créer des définitions de stratégie personnalisées Configuration invité
description: Découvrez comment créer une stratégie Configuration invité.
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: 1dd1620d0ef41bf28a276cfe2412ca4bdc09d183
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644901"
---
# <a name="how-to-create-custom-guest-configuration-policy-definitions"></a>Créer des définitions de stratégie personnalisées Configuration invité

Avant de commencer, il est recommandé de lire la page Vue d’ensemble présentant la [Configuration invité](../concepts/guest-configuration.md), ainsi que les informations relatives aux effets de la stratégie Configuration invité [Comment configurer les options de correction pour la configuration invité](../concepts/guest-configuration-policy-effects.md).

> [!IMPORTANT]
> L’extension de configuration invité est requise pour les machines virtuelles Azure. Pour déployer l’extension à grande échelle sur toutes les machines, attribuez l’initiative de stratégie suivante : `Deploy prerequisites to enable guest configuration policies on
> virtual machines`
> 
> Pour utiliser les packages de configuration d’invité qui appliquent des configurations, l’extension de configuration d’invité de machine virtuelle Azure version **1.29.24** ou ultérieure, ou l’agent Arc version **1.10.0** ou version ultérieure, sont requis.
>
> Les définitions de stratégie de configuration invité personnalisées utilisant **AuditIfNotExists** sont généralement disponibles, mais les définitions utilisant **DeployIfNotExists** avec la configuration invité sont **en préversion**.

Utilisez les étapes suivantes pour créer vos propres stratégies qui auditent la conformité ou gèrent l’état des machines Azure ou Arc.

## <a name="install-powershell-7-and-required-powershell-modules"></a>Installer PowerShell 7 et les modules PowerShell requis

Tout d’abord, assurez-vous d’avoir suivi toutes les étapes de la page [Comment configurer un environnement de création de configuration invité](./guest-configuration-create-setup.md) pour installer la version requise de PowerShell pour votre système d’exploitation et le module `GuestConfiguration`.

## <a name="create-and-publish-a-guest-configuration-package-artifact"></a>Créer et publier un artefact de package de configuration invité

Si vous ne l’avez pas encore fait, suivez toutes les étapes de la page [Comment créer des artefacts de package de configuration invité personnalisés](./guest-configuration-create.md) pour créer et publier un package de configuration invité personnalisé et de la page [Comment tester les artefacts de package de configuration invité](./guest-configuration-create-test.md) pour valider le package de configuration invité localement dans votre environnement de développement.

## <a name="policy-requirements-for-guest-configuration"></a>Exigences de la stratégie pour la configuration invité

La section `metadata` sur la définition d’une stratégie doit inclure deux propriétés pour le service de configuration invité afin d’automatiser l’approvisionnement et la création de rapports sur les attributions de configuration invité. La propriété `category` doit être définie sur « Guest Configuration » et une section nommée `guestConfiguration` doit contenir des informations sur l’attribution de configuration invité. L’applet de commande `New-GuestConfigurationPolicy` crée automatiquement ce texte.

L’exemple suivant illustre la section `metadata` qui est créée automatiquement par `New-GuestConfigurationPolicy`.

```json
    "metadata": {
      "category": "Guest Configuration",
      "guestConfiguration": {
        "name": "test",
        "version": "1.0.0",
        "contentType": "Custom",
        "contentUri": "CUSTOM-URI-HERE",
        "contentHash": "CUSTOM-HASH-VALUE-HERE",
        "configurationParameter": {}
      }
    },
```

La propriété `category` doit être définie sur Guest Configuration. Si l’effet de la définition est défini sur « DeployIfNotExists », la section `then` doit contenir des détails sur le déploiement d’une attribution de configuration invité. L’applet de commande `New-GuestConfigurationPolicy` crée automatiquement ce texte.

### <a name="create-an-azure-policy-definition"></a>Créer une définition Azure Policy

Une fois qu’un package de stratégie personnalisée de configuration invité a été créé et chargé, créez la définition de la stratégie de configuration invité. La cmdlet `New-GuestConfigurationPolicy` prend un package de stratégie personnalisé et crée une définition de stratégie.

Le paramètre **PolicyId** de `New-GuestConfigurationPolicy` requiert une chaîne unique. Un identificateur global unique (GUID) est recommandé. Pour les nouvelles définitions, générez un nouveau GUID à l’aide de la cmdlet `New-GUID`. Lorsque vous effectuez des mises à jour de la définition, utilisez la même chaîne unique pour **PolicyId** afin de vous assurer que la définition correcte est mise à jour.

Paramètres de la cmdlet `New-GuestConfigurationPolicy` :

- **PolicyId** : GUID ou autre chaîne unique qui identifie la définition.
- **ContentUri** : URI HTTP(S) public du package de contenu Guest Configuration.
- **DisplayName** : Nom d'affichage de la stratégie.
- **Description** : Description de la stratégie.
- **Paramètre** : Paramètres de stratégie fournis au format Hashtable.
- **Version** : Version de stratégie.
- **Chemin d’accès** : Chemin de destination où les définitions de stratégie sont créées.
- **Plateforme** : plateforme cible (Windows/Linux) pour la stratégie et le package de contenu de configuration invité.
- **Mode** : (ApplyAndMonitor, ApplyAndAutoCorrect, Audit) choisissez si la stratégie doit auditer ou déployer la configuration. La valeur par défaut est « Audit ».
- **Tag** ajoute un ou plusieurs filtres de balise à la définition de stratégie
- **Category** définit le champ de métadonnées catégorie dans la définition de stratégie

Pour plus d’informations sur le paramètre « Mode », consultez la page [Comment configurer les options de correction pour la configuration invité](../concepts/guest-configuration-policy-effects.md).

Créer une définition de stratégie qui audite à l’aide d’un package de configuration personnalisé, dans un chemin d’accès spécifié :

```powershell
New-GuestConfigurationPolicy `
  -PolicyId 'My GUID' `
  -ContentUri '<paste the ContentUri output from the Publish command>' `
  -DisplayName 'My audit policy.' `
  -Description 'Details about my policy.' `
  -Path './policies' `
  -Platform 'Windows' `
  -Version 1.0.0 `
  -Verbose
```

Créer une définition de stratégie qui déploie une configuration à l’aide d’un package de configuration personnalisé, dans un chemin d’accès spécifié :

```powershell
New-GuestConfigurationPolicy `
  -PolicyId 'My GUID' `
  -ContentUri '<paste the ContentUri output from the Publish command>' `
  -DisplayName 'My audit policy.' `
  -Description 'Details about my policy.' `
  -Path './policies' `
  -Platform 'Windows' `
  -Version 1.0.0 `
  -Mode 'ApplyAndAutoCorrect' `
  -Verbose
```

La sortie de la cmdlet retourne un objet contenant le nom complet de la définition et le chemin d’accès aux fichiers de stratégie. Les fichiers JSON de définition qui créent des définitions de stratégie d’audit portent le nom **auditIfNotExists.js** et les fichiers qui créent des définitions de stratégie pour appliquer des configurations portent le nom **deployIfNotExists.js**.

#### <a name="filtering-guest-configuration-policies-using-tags"></a>Filtrage des stratégies de configuration invité à l’aide de balises

Les définitions de stratégie créées par les cmdlets dans le module Configuration invité peuvent éventuellement inclure un filtre pour les balises. Le paramètre **Tag** de `New-GuestConfigurationPolicy` prend en charge un groupe de tables de hachage contenant des balises individuelles. Les balises sont ajoutées à la section `If` de la définition de stratégie et ne peuvent pas être modifiées par une attribution de stratégie.

Vous trouverez ci-dessous un exemple d’extrait de définition de stratégie permettant de filtrer les balises.

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original guest configuration content
    }
  ]
}
```

#### <a name="using-parameters-in-custom-guest-configuration-policy-definitions"></a>Utilisation de paramètres dans des définitions de stratégie personnalisées de configuration invité

La configuration invité prend en charge la substitution des propriétés d’une configuration lors d’une exécution. Cette fonctionnalité signifie que les valeurs du fichier MOF dans le package n’ont pas à être considérées comme statiques. Les valeurs de substitution sont fournies via Azure Policy et ne modifient pas la création ni la compilation des configurations.

Les cmdlets `New-GuestConfigurationPolicy` et `Get-GuestConfigurationPacakgeComplianceStatus ` incluent un paramètre nommé **Paramètre**. Ce paramètre utilise une définition hashtable qui comprend toutes les informations concernant chaque paramètre et crée les sections nécessaires de chaque fichier de la définition Azure Policy.

L’exemple suivant crée une définition de stratégie pour auditer un service, que l’utilisateur sélectionne dans une liste au moment de l’attribution de la stratégie.

```powershell
# This DSC Resource text:
Service 'UserSelectedNameExample'
  {
    Name = 'ParameterValue'
    Ensure = 'Present'
    State = 'Running'
  }`

# Would require the following hashtable:
$PolicyParameterInfo = @(
  @{
    Name = 'ServiceName'                                           # Policy parameter name (mandatory)
    DisplayName = 'windows service name.'                          # Policy parameter display name (mandatory)
    Description = 'Name of the windows service to be audited.'     # Policy parameter description (optional)
    ResourceType = 'Service'                                       # DSC configuration resource type (mandatory)
    ResourceId = 'UserSelectedNameExample'                         # DSC configuration resource id (mandatory)
    ResourcePropertyName = 'Name'                                  # DSC configuration resource property name (mandatory)
    DefaultValue = 'winrm'                                         # Policy parameter default value (optional)
    AllowedValues = @('BDESVC','TermService','wuauserv','winrm')   # Policy parameter allowed values (optional)
  }
)

New-GuestConfigurationPolicy `
  -PolicyId 'My GUID' `
  -ContentUri '<paste the ContentUri output from the Publish command>' `
  -DisplayName 'Audit Windows Service.' `
  -Description 'Audit if a Windows Service isn't enabled on Windows machine.' `
  -Path '.\policies' `
  -Parameter $PolicyParameterInfo `
  -Version 1.0.0
```

### <a name="publish-the-azure-policy-definition"></a>Publier la définition Azure Policy

Enfin, publiez les définitions de stratégie à l’aide de la cmdlet `Publish-GuestConfigurationPolicy`. La cmdlet ne dispose que du paramètre **Path** qui pointe vers l’emplacement des trois fichiers JSON créés par `New-GuestConfigurationPolicy`.

Pour exécuter la commande Publish, vous devez avoir accès à la création de définitions de stratégies dans Azure. Les exigences spécifiques en matière d’autorisations sont documentées dans la page [vue d’ensemble d’Azure Policy](../overview.md). Le rôle intégré recommandé est le rôle **Contributeur de la stratégie de ressource**.

```powershell
Publish-GuestConfigurationPolicy -Path '.\policies'
```

Avec la définition de stratégie créée dans Azure, la dernière étape consiste à attribuer la définition. Apprenez à attribuer la définition à l'aide du [portail](../assign-policy-portal.md), d'[Azure CLI](../assign-policy-azurecli.md) et d'[Azure PowerShell](../assign-policy-powershell.md).

### <a name="optional-piping-output-from-each-command-to-the-next"></a>Facultatif : canalisation de la sortie de chaque commande vers la suivante

Les commandes dans la configuration invité prennent en charge les paramètres de pipeline par nom.
Vous pouvez utiliser l’opérateur `|` pour canaliser la sortie de chaque commande vers la suivante.
La canalisation est utile dans les environnements de développement lorsqu’il s’agit d’une itération rapide, car vous n’avez pas besoin de copier/coller la sortie de chaque commande.

Pour exécuter la séquence à l’aide de l’opérateur `|` :

```powershell
# End to end flow piping output of each command to the next
$ConfigName         = myConfigName
$ResourceGroupName  = myResourceGroupName
$StorageAccountName = myStorageAccountName
$DisplayName        = 'Configure Linux machine per my scenario.'
$Description        = 'Details about my policy.'
New-GuestConfigurationPackage -Name $ConfigName -Configuration ./$ConfigName.mof -Path ./package/ -Type AuditAndSet -Force |
Publish-GuestConfigurationPackage -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName -Force |
New-GuestConfigurationPolicy -PolicyId 'My GUID' -DisplayName $DisplayName -Description $Description -Path './policies' -Platform 'Linux' -Version 1.0.0 -Mode 'ApplyAndAutoCorrect' |
Publish-GuestConfigurationPolicy
```

## <a name="policy-lifecycle"></a>Cycle de vie de la stratégie

Si vous souhaitez publier une mise à jour de la définition de stratégie, effectuez la modification pour le package de configuration invité et les détails de la définition Azure Policy.

> [!NOTE]
> La propriété `version` de l’affectation de configuration invité n’a d’influence que sur les packages qui sont hébergés par Microsoft. La meilleure pratique pour le contenu personnalisé du contrôle de version consiste à inclure la version dans le nom de fichier.

Tout d’abord, lorsque vous exécutez `New-GuestConfigurationPackage`, spécifiez un nom qui rende le package unique par rapport aux versions précédentes. Vous pouvez inclure un numéro de version dans le nom, par exemple `PackageName_1.0.0`. Le numéro dans cet exemple ne sert qu’à rendre le package unique, et non à spécifier que le package doit être considéré comme plus récent ou plus ancien que les autres.

Ensuite, mettez à jour les paramètres utilisés avec la cmdlet `New-GuestConfigurationPolicy` en suivant chacune des explications suivantes.

- **Version** : Lorsque vous exécutez l’applet de commande `New-GuestConfigurationPolicy`, vous devez spécifier un numéro de version supérieur à celui actuellement publié.
- **contentUri** : Lorsque vous exécutez la cmdlet `New-GuestConfigurationPolicy`, vous devez spécifier un URI vers l’emplacement du package. L’inclusion d’une version de package dans le nom de fichier garantit que la valeur de cette propriété change dans chaque version.
- **contentHash** : Cette propriété est automatiquement mise à jour par l’applet de commande `New-GuestConfigurationPolicy`. Il s’agit d’une valeur de hachage du package créé par `New-GuestConfigurationPackage`. Cette propriété doit être correcte pour le fichier `.zip` que vous publiez. Si seule la propriété **contentUri** est mise à jour, l’extension n’accepte pas le package de contenu.

Le moyen le plus simple de publier un package mis à jour consiste à répéter le processus décrit dans cet article et à fournir un numéro de version mis à jour. Ce processus garantit que toutes les propriétés ont été correctement mises à jour.

## <a name="next-steps"></a>Étapes suivantes

- [Attribuez votre définition de stratégie personnalisée](../assign-policy-portal.md) à l’aide du portail Azure.
- Découvrez comment visualiser les [informations relatives à la conformité pour les attributions de stratégie de configuration d’invité](./determine-non-compliance.md#compliance-details-for-guest-configuration).
