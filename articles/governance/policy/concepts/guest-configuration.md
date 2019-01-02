---
title: Comprendre comment effectuer des audits à l’intérieur d’une machine virtuelle
description: Découvrez comment Azure Policy utilise la configuration d’invité pour auditer les paramètres à l’intérieur d’une machine virtuelle Azure.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 1ea87dc01048a2747a668db7a5b1f22b37ed9213
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310060"
---
# <a name="understand-azure-policys-guest-configuration"></a>Comprendre la configuration d’invité d’Azure Policy

En plus de l’audit et de la [correction](../how-to/remediate-resources.md) des ressources Azure, Azure Policy est capable d’auditer les paramètres à l’intérieur d’une machine virtuelle. La validation est effectuée par le client et l’extension de configuration d’invité. L’extension, via le client, valide les paramètres tels que la configuration du système d’exploitation, la présence ou la configuration de l’application, les paramètres d’environnement et bien plus encore.

> [!IMPORTANT]
> Actuellement, seules les stratégies **intégrées** sont prises en charge avec la configuration d’invité.

## <a name="extension-and-client"></a>Extension et client

Pour auditer les paramètres à l’intérieur d’une machine virtuelle, une [extension de machine virtuelle](../../../virtual-machines/extensions/overview.md) est activée. L’extension télécharge l’attribution de stratégie applicable et la définition de configuration correspondante.

### <a name="register-guest-configuration-resource-provider"></a>Inscrire le fournisseur de ressources de configuration d’invité

Avant de pouvoir utiliser la configuration d’invité, vous devez inscrire le fournisseur de ressources. Pour ce faire, vous pouvez utiliser le portail ou PowerShell.

#### <a name="registration---portal"></a>Inscription - portail

Pour inscrire le fournisseur de ressources pour la configuration d’invité via le portail Azure, effectuez les étapes suivantes :

1. Lancez le portail Azure et cliquez sur **Tous les services**. Recherchez et sélectionnez **Abonnements**.

1. Recherchez l’abonnement pour lequel vous souhaitez activer la configuration d’invité et cliquez dessus.

1. Dans le menu de gauche de la page **Abonnement**, cliquez sur **Fournisseurs de ressources**.

1. Filtrez ou faites défiler jusqu’à ce que vous localisiez **Microsoft.GuestConfiguration**, puis cliquez sur **Inscrire** sur la même ligne.

#### <a name="registration---powershell"></a>Inscription - PowerShell

Pour inscrire le fournisseur de ressources pour la configuration d’invité via PowerShell, exécutez la commande suivante :

```azurepowershell-interactive
# Login first with Connect-AzureRmAccount if not using Cloud Shell
Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

### <a name="validation-tools"></a>Outils de validation

À l’intérieur de la machine virtuelle, le client de configuration d’invité utilise des outils locaux pour exécuter l’audit.

Le tableau suivant affiche une liste des outils locaux utilisés sur chaque système d’exploitation pris en charge :

|Système d’exploitation|Outil de validation|Notes|
|-|-|-|
| Windows|[Configuration de l’état souhaité (DSC) Microsoft](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ruby et Python sont installés par l’extension de configuration d’invité. |

### <a name="supported-client-types"></a>Types de clients pris en charge

Le tableau suivant affiche une liste des systèmes d’exploitation pris en charge sur des images Azure :

|Publisher|NOM|Versions|
|-|-|-|
|Canonical|Serveur Ubuntu|14.04, 16.04, 18.04|
|Credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|SUSE|SLES|12 SP3|

> [!IMPORTANT]
> La configuration d’invité n’est pas prise en charge sur les images de machine virtuelle personnalisées.

### <a name="unsupported-client-types"></a>Types de clients non pris en charge

Le tableau suivant répertorie les systèmes d’exploitation qui ne sont pas pris en charge :

|Système d’exploitation|Notes|
|-|-|
|Client Windows | Les systèmes d’exploitation client (par exemple, Windows 7 et Windows 10) ne sont pas pris en charge.
|Windows Server 2016 Nano Server | Non pris en charge.|

## <a name="guest-configuration-definition-requirements"></a>Exigences de définition de la configuration d’invité

Chaque audit exécuté par la configuration d’invité nécessite deux définitions de stratégies : **DeployIfNotExists** et **AuditIfNotExists**. **DeployIfNotExists** sert à préparer la machine virtuelle avec l’agent de configuration d’invité et d’autres composants pour prendre en charge les [outils de validation](#validation-tools).

La définition de stratégie **DeployIfNotExists** valide et corrige les éléments suivants :

- Validez qu’une configuration à évaluer a été attribuée à la machine virtuelle. Si aucune attribution n’est actuellement présente, procurez-vous cette attribution et préparez la machine virtuelle en effectuant les opérations suivantes :
  - Authentification auprès de la machine virtuelle en utilisant une [identité managée](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Installation de la dernière version de l’extension **Microsoft.GuestConfiguration**
  - Installation des [outils de validation](#validation-tools) et des dépendances, si nécessaire

Une fois **DeployIfNotExists** conforme, la définition de stratégie **AuditIfNotExists** utilise les outils de validation locaux pour déterminer si l’attribution de configuration est conforme ou non conforme. L’outil de validation fournit les résultats au client de configuration d’invité. Le client transmet à l’extension invité les résultats pour les rendre disponibles via le fournisseur de ressources de la configuration d’invité.

Azure Policy utilise la propriété **complianceStatus** des fournisseurs de ressources de configuration d’invité pour signaler la conformité dans le nœud **Conformité**. Pour plus d’informations, consultez [Obtention de données de conformité](../how-to/getting-compliance-data.md).

> [!NOTE]
> Pour chaque définition de la configuration d’invité, les définitions de stratégies **DeployIfNotExists** et **AuditIfNotExists** doivent toutes deux exister.

Toutes les stratégies intégrées pour la configuration d’invité sont incluses dans une initiative pour regrouper les définitions à utiliser dans les attributions. L’initiative intégré nommée *[Préversion] : Auditer les paramètres de sécurité de mot de passe dans les machines virtuelles Linux et Windows* contient 18 stratégies. Il existe six paires **DeployIfNotExists** et **AuditIfNotExists** pour Windows et trois paires pour Linux. Dans chaque cas, la logique à l’intérieur de la définition permet de garantir que seul le système d’exploitation cible est évalué en fonction de la définition de [règle de stratégie](definition-structure.md#policy-rule).

## <a name="next-steps"></a>Étapes suivantes

- Consulter des exemples à la page [Exemples Azure Policy](../samples/index.md)
- Consulter la page [Structure de définition Azure Policy](definition-structure.md)
- Consulter la page [Compréhension des effets d’Azure Policy](effects.md)
- Savoir comment [créer des stratégies par programmation](../how-to/programmatically-create.md)
- Découvrir comment [obtenir des données de conformité](../how-to/getting-compliance-data.md)
- Découvrir comment [corriger les ressources non conformes](../how-to/remediate-resources.md)
- Pour en savoir plus sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](../../management-groups/index.md).