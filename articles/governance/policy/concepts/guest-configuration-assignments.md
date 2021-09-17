---
title: Comprendre les ressources d’affectation de la configuration invité
description: La configuration invité crée des ressources d’extension nommées affectations de la configuration invité qui mappent les configurations aux machines.
ms.date: 08/15/2021
ms.topic: conceptual
ms.openlocfilehash: c106ca492166cc604607bfc8da3f9c7d7b3bff2d
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773047"
---
# <a name="understand-guest-configuration-assignment-resources"></a>Comprendre les ressources d’affectation de la configuration invité

Lorsqu’une instance de la Stratégie Azure est affectée, si elle se trouve dans la catégorie « Configuration invité », alors des métadonnées sont incluses pour décrire une affectation d’invité.

[Un guide vidéo de ce document est disponible](https://youtu.be/DmCphySEB7A).

Vous pouvez considérer une affectation d’invité comme un lien entre une machine et un scénario de Stratégie Azure. Par exemple, l’extrait de code suivant associe la configuration de base Windows Azure avec une version minimale `1.0.0` à n’importe quel ordinateur dans l’étendue de la stratégie.

```json
"metadata": {
    "category": "Guest Configuration",
    "guestConfiguration": {
        "name": "AzureWindowsBaseline",
        "version": "1.*"
    }
//additional metadata properties exist
```

## <a name="how-azure-policy-uses-guest-configuration-assignments"></a>Comment la Stratégie Azure utilise-t-elle les affectations de la configuration invité ?

Les informations de métadonnées sont utilisées par le service de la configuration invité afin de créer automatiquement une ressource d’audit pour les définitions avec les effets de stratégie **AuditIfNotExists** ou **DeployIfNotExists**. Le type de ressource, est `Microsoft.GuestConfiguration/guestConfigurationAssignments`. La Stratégie Azure utilise la propriété **complianceStatus** de l’affectation de la ressource invité pour signaler l’état de conformité. Pour plus d’informations, consultez [Obtention de données de conformité](../how-to/get-compliance-data.md).

### <a name="deletion-of-guest-assignments-from-azure-policy"></a>Suppression des affectations d’invités de la Stratégie Azure

Lors de la suppression d’une affectation de la Stratégie Azure, si une affectation de la configuration invité a été créée par la stratégie, alors cette affectation de la configuration d’invité est également supprimée.

## <a name="manually-creating-guest-configuration-assignments"></a>Création manuelle des affectations de la configuration invité

Les ressources de l’affectation invité dans le Gestionnaire de ressource Azure peuvent être créées par la Stratégie Azure ou n’importe quel kit de développement logiciel (SDK) client.

Exemple de modèle de déploiement :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2021-01-25",
      "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
      "name": "myMachine/Microsoft.GuestConfiguration/myConfig",
      "location": "westus2",
      "properties": {
        "guestConfiguration": {
          "name": "myConfig",
          "contentUri": "https://mystorageaccount.blob.core.windows.net/mystoragecontainer/myConfig.zip?sv=SASTOKEN",
          "contentHash": "SHA256HASH",
          "version": "1.0.0",
          "assignmentType": "ApplyAndMonitor",
          "configurationParameter": {}
        }
      }
    }    
  ]
}
```

Le tableau suivant décrit chaque propriété des ressources d’affectation de l’invité.

| Propriété | Description |
|-|-|
| name | Nom de la configuration dans le fichier MOF du package de contenu. |
| contentUri | Chemin d’accès de l’URI HTTPS au package de contenu (.zip). |
| contentHash | Valeur de hachage SHA256 du package de contenu, utilisée pour vérifier qu’elle n’a pas changé. |
| version | Version du package de contenu. Utilisé uniquement pour les packages intégrés et non utilisé pour les packages de contenu personnalisés. |
| assignmentType | Comportement de l’affectation. Valeurs autorisées : `Audit`, `ApplyandMonitor` et `ApplyandAutoCorrect`. |
| configurationParameter | Liste du type de ressource DSC, du nom et de la valeur dans le fichier MOF du package de contenu à remplacer après son téléchargement sur la machine. |

### <a name="deletion-of-manually-created-guest-configuration-assignments"></a>Suppression des affectations de la configuration invité créées manuellement

Les affectations de la configuration invité créées par le biais d’une approche manuelle (par exemple, un déploiement de modèle du Gestionnaire de ressource Azure) doivent être supprimées manuellement.
La suppression de la ressource parente (machine virtuelle ou prenant en charge l’ARC) supprimera également l’affectation de la configuration invité.

Pour supprimer manuellement une affectation de la configuration invité, utilisez l’exemple suivant. Veillez à remplacer tous les exemples de chaînes, indiqués par des guillemets «\<\> ».

```PowerShell
# First get details about the guest configuration assignment
$resourceDetails = @{
  ResourceGroupName = '<myResourceGroupName>'
  ResourceType      = 'Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments/'
  ResourceName      = '<myVMName>/Microsoft.GuestConfiguration'
  ApiVersion        = '2020-06-25'
}
$guestAssignment = Get-AzResource @resourceDetails

# Review details of the guest configuration assignment
$guestAssignment

# After reviewing properties of $guestAssignment to confirm
$guestAssignment | Remove-AzResource
```

## <a name="next-steps"></a>Étapes suivantes

- Consultez la [Présentation de la configuration invité](./guest-configuration.md).
- Configurer un [environnement de développement](../how-to/guest-configuration-create-setup.md)de package de la configuration invité personnalisé.
- [Créez un artefact de package](../how-to/guest-configuration-create.md) pour la configuration invité.
- [Testez l’artefact de package](../how-to/guest-configuration-create-test.md) à partir de votre environnement de développement.
- Utilisez le module`GuestConfiguration` pour [créer une définition de la Stratégie Azure ](../how-to/guest-configuration-create-definition.md) pour une gestion à l’échelle de votre environnement.
- [Affectez votre définition de stratégie personnalisée](../assign-policy-portal.md) à l’aide du portail Azure.
- Découvrez comment visualiser les [informations relatives à la conformité pour les affectations de stratégie de la configuration invité](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration) .
