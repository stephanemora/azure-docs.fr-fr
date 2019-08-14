---
title: Comprendre comment auditer le contenu d’une machine virtuelle
description: Découvrez comment Azure Policy utilise la configuration d’invité pour auditer les paramètres à l’intérieur d’une machine virtuelle Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 74e36d944450e1ce2c61481b2cb7e345860212af
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326882"
---
# <a name="understand-azure-policys-guest-configuration"></a>Comprendre la configuration d’invité d’Azure Policy

En plus de l’audit et de la [correction](../how-to/remediate-resources.md) des ressources Azure, Azure Policy peut auditer les paramètres internes d’une machine virtuelle. La validation est effectuée par le client et l’extension de configuration d’invité. L’extension, via le client, valide les paramètres tels que la configuration du système d’exploitation, la présence ou la configuration de l’application, les paramètres d’environnement et bien plus encore.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="extension-and-client"></a>Extension et client

Pour auditer les paramètres à l’intérieur d’une machine virtuelle, une [extension de machine virtuelle](../../../virtual-machines/extensions/overview.md) est activée. L’extension télécharge l’attribution de stratégie applicable et la définition de configuration correspondante.

### <a name="register-guest-configuration-resource-provider"></a>Inscrire le fournisseur de ressources de configuration d’invité

Avant de pouvoir utiliser la configuration d’invité, vous devez inscrire le fournisseur de ressources. Pour ce faire, vous pouvez utiliser le portail ou PowerShell. Le fournisseur de ressources est inscrit automatiquement si l’affectation d’une stratégie de configuration d’invité est effectuée via le portail.

#### <a name="registration---portal"></a>Inscription - portail

Pour inscrire le fournisseur de ressources pour la configuration d’invité via le portail Azure, effectuez les étapes suivantes :

1. Lancez le portail Azure et cliquez sur **Tous les services**. Recherchez et sélectionnez **Abonnements**.

1. Recherchez l’abonnement pour lequel vous souhaitez activer la configuration d’invité et cliquez dessus.

1. Dans le menu de gauche de la page **Abonnement**, cliquez sur **Fournisseurs de ressources**.

1. Filtrez ou faites défiler jusqu’à ce que vous localisiez **Microsoft.GuestConfiguration**, puis cliquez sur **Inscrire** sur la même ligne.

#### <a name="registration---powershell"></a>Inscription - PowerShell

Pour inscrire le fournisseur de ressources pour la configuration d’invité via PowerShell, exécutez la commande suivante :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

### <a name="validation-tools"></a>Outils de validation

À l’intérieur de la machine virtuelle, le client de configuration d’invité utilise des outils locaux pour exécuter l’audit.

Le tableau suivant affiche une liste des outils locaux utilisés sur chaque système d’exploitation pris en charge :

|Système d’exploitation|Outil de validation|Notes|
|-|-|-|
|Windows|[Configuration de l’état souhaité (DSC) Microsoft](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ruby et Python sont installés par l’extension de configuration d’invité. |

### <a name="validation-frequency"></a>Fréquence de validation

Le client de configuration d'invité vérifie le nouveau contenu toutes les 5 minutes. Une fois l'affectation d'invité reçue, les paramètres sont vérifiés à intervalle de 15 minutes. Les résultats sont envoyés au fournisseur de ressources de configuration d’invité dès la fin de l’audit. Lorsqu'un [déclencheur d’évaluation](../how-to/get-compliance-data.md#evaluation-triggers) de stratégie intervient, l'état de la machine est consigné dans le fournisseur de ressources de configuration d'invité. Azure Policy évalue alors les propriétés Azure Resource Manager. Une évaluation Azure Policy à la demande récupère la valeur la plus récente du fournisseur de ressources de configuration d'invité. Cela étant, elle ne déclenche pas de nouvel audit de la configuration de la machine virtuelle.

### <a name="supported-client-types"></a>Types de clients pris en charge

Le tableau suivant affiche une liste des systèmes d’exploitation pris en charge sur des images Azure :

|Publisher|Nom|Versions|
|-|-|-|
|Canonical|Serveur Ubuntu|14.04, 16.04, 18.04|
|Credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Client Windows|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|SUSE|SLES|12 SP3|

> [!IMPORTANT]
> La configuration d’invité peut auditer les nœuds exécutant un système d’exploitation pris en charge. Si vous souhaitez auditer des machines virtuelles qui utilisent une image personnalisée, vous devez dupliquer la définition **DeployIfNotExists** et modifier la section **If** pour inclure les propriétés de votre image.

### <a name="unsupported-client-types"></a>Types de clients non pris en charge

Windows Server Nano Server n’est pris en charge dans aucune version.

### <a name="guest-configuration-extension-network-requirements"></a>Configuration réseau requise pour l’extension de configuration d’invité

Pour communiquer avec le fournisseur de ressources de configuration d’invité dans Azure, les machines virtuelles nécessitent un accès sortant vers des centres de données Azure sur le port **443**. Si vous utilisez un réseau privé virtuel dans Azure sans autoriser le trafic sortant, vous devez configurer les exceptions à l’aide des règles du [groupe de sécurité réseau](../../../virtual-network/manage-network-security-group.md#create-a-security-rule). Il n’existe pas actuellement d’étiquette de service pour la configuration d’invité Azure Policy.

Pour les listes d’adresses IP, vous pouvez télécharger les [plages d’adresses IP du centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Ce fichier, qui est mis à jour chaque semaine, possède les plages actuellement déployées et tous les changements à venir des plages d’adresses IP. Vous avez uniquement besoin d’autoriser l’accès sortant vers les adresses IP dans les régions où vos machines virtuelles seront déployées.

> [!NOTE]
> Le fichier XML Azure Datacenter IP address répertorie les plages d’adresses IP qui sont utilisées dans les centres de données Microsoft Azure. Le fichier comprend les plages Compute, SQL et Stockage. Un fichier mis à jour est publié chaque semaine. Le fichier reflète les plages déployées et toutes les modifications à venir dans les plages IP. Les nouvelles plages figurant dans le fichier ne sont pas utilisées dans les centres de données avant une semaine minimum. Pensez à télécharger le nouveau fichier XML chaque semaine. Ensuite, mettez à jour votre site afin qu’il identifie correctement les services en cours d’exécution dans Azure. Les utilisateurs d’Azure ExpressRoute doivent noter que ce fichier est utilisé pour mettre à jour la publication BGP (Border Gateway Protocol) de l’espace Azure la première semaine de chaque mois.

## <a name="guest-configuration-definition-requirements"></a>Exigences de définition de la configuration d’invité

Chaque audit exécuté par la configuration d’invité nécessite deux définitions de stratégies : une définition **DeployIfNotExists** et une définition **Audit**. La définition **DeployIfNotExists** sert à préparer la machine virtuelle avec l’agent de configuration d’invité et d’autres composants pour prendre en charge les [outils de validation](#validation-tools).

La définition de stratégie **DeployIfNotExists** valide et corrige les éléments suivants :

- Validez qu’une configuration à évaluer a été attribuée à la machine virtuelle. Si aucune attribution n’est actuellement présente, procurez-vous cette attribution et préparez la machine virtuelle en effectuant les opérations suivantes :
  - Authentification auprès de la machine virtuelle en utilisant une [identité managée](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Installation de la dernière version de l’extension **Microsoft.GuestConfiguration**
  - Installation des [outils de validation](#validation-tools) et des dépendances, si nécessaire

Si l’attribution de **DeployIfNotExists** n’est pas conforme, une [tâche de correction](../how-to/remediate-resources.md#create-a-remediation-task) peut être utilisée.

Une fois que l’attribution de **DeployIfNotExists** est conforme, l’attribution de la stratégie **Audit** utilise les outils de validation locaux pour déterminer si l’attribution de configuration est conforme ou non conforme.
L’outil de validation fournit les résultats au client de configuration d’invité. Le client transmet à l’extension invité les résultats pour les rendre disponibles via le fournisseur de ressources de la configuration d’invité.

Azure Policy utilise la propriété **complianceStatus** des fournisseurs de ressources de configuration d’invité pour signaler la conformité dans le nœud **Conformité**. Pour plus d’informations, consultez [Obtention de données de conformité](../how-to/getting-compliance-data.md).

> [!NOTE]
> La stratégie **DeployIfNotExists** est requise pour que la stratégie **Audit** retourne les résultats.
> Sans la stratégie **DeployIfNotExists**, la stratégie **Audit** affiche « 0 sur 0 » ressource comme état.

Toutes les stratégies intégrées pour la configuration d’invité sont incluses dans une initiative pour regrouper les définitions à utiliser dans les attributions. L’initiative intégré nommée *[Préversion] : Auditer les paramètres de sécurité de mot de passe dans les machines virtuelles Linux et Windows* contient 18 stratégies. Il existe six paires **DeployIfNotExists** et **Audit** pour Windows et trois paires pour Linux. Dans chaque cas, la logique à l’intérieur de la définition permet de garantir que seul le système d’exploitation cible est évalué en fonction de la définition de [règle de stratégie](definition-structure.md#policy-rule).

## <a name="multiple-assignments"></a>Affectations multiples

Actuellement, les stratégies de configuration d’invité prennent en charge l’affectation d’une seule affectation d’invité par machine virtuelle, même si l’affectation de stratégie utilise des paramètres différents.

## <a name="client-log-files"></a>Fichiers journaux du client

L’extension de configuration d’invité écrit les fichiers journaux aux emplacements suivants :

Windows : `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux : `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Où `<version>` indique le numéro de la version actuelle.

## <a name="guest-configuration-samples"></a>Exemples de la configuration d’invité

Des exemples de la configuration d’invité Azure Policy sont disponibles aux emplacements suivants :

- [Index d’exemples - Configuration d’invité](../samples/index.md#guest-configuration)
- [Exemples Azure Policy - Dépôt GitHub](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration).

## <a name="next-steps"></a>Étapes suivantes

- Consultez des exemples à la page [Exemples Azure Policy](../samples/index.md).
- Consultez la [Structure de définition Azure Policy](definition-structure.md).
- Consultez la page [Compréhension des effets de Policy](effects.md).
- Découvrez comment [créer des stratégies par programmation](../how-to/programmatically-create.md).
- Découvrez comment [obtenir des données de conformité](../how-to/getting-compliance-data.md).
- Découvrez comment [corriger des ressources non conformes](../how-to/remediate-resources.md).
- Pour en savoir plus sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](../../management-groups/index.md).
