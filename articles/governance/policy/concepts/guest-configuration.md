---
title: Découvrez comment auditer le contenu des machines virtuelles
description: Découvrez comment Azure Policy utilise l’agent Configuration d’invité pour auditer les paramètres à l’intérieur des machines virtuelles.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 89f7cc3931971d70b441490f77b67ace89434c2b
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025218"
---
# <a name="understand-azure-policys-guest-configuration"></a>Comprendre la configuration d’invité d’Azure Policy

En plus de l’audit et de la [correction](../how-to/remediate-resources.md) des ressources Azure, Azure Policy peut auditer les paramètres internes d’une machine. La validation est effectuée par le client et l’extension de configuration d’invité. L’extension, via le client, valide des paramètres tels que :

- La configuration du système d’exploitation
- La configuration ou la présence de l’application
- Paramètres d'environnement

À ce stade, la plupart des stratégies de configuration d’invité Azure Policy effectue uniquement un audit des paramètres à l’intérieur de la machine. Elles n’appliquent pas de configurations. L’exception est une stratégie intégrée [référencée ci-dessous](#applying-configurations-using-guest-configuration).

## <a name="resource-provider"></a>Fournisseur de ressources

Avant de pouvoir utiliser la configuration d’invité, vous devez inscrire le fournisseur de ressources. Le fournisseur de ressources est inscrit automatiquement si l’affectation d’une stratégie de configuration d’invité est effectuée via le portail. Vous pouvez l’inscrire manuellement via le [portail](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) ou [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).

## <a name="extension-and-client"></a>Extension et client

Pour auditer les paramètres à l’intérieur d’une machine, une [extension de machine virtuelle](../../../virtual-machines/extensions/overview.md) est activée. L’extension télécharge l’attribution de stratégie applicable et la définition de configuration correspondante.

> [!Important]
> L’extension Guest Configuration est requise pour effectuer des audits sur des machines virtuelles Azure.
> Pour déployer l’extension à grande échelle, attribuez les définitions de stratégie suivantes :
>   - [Déployer les prérequis pour activer la stratégie de configuration d’invité sur les machines virtuelles Windows.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)
>   - [Déployer les prérequis pour activer la stratégie de configuration d’invité sur les machines virtuelles Linux.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb27e9e0-526e-4ae1-89f2-a2a0bf0f8a50)

### <a name="limits-set-on-the-extension"></a>Limites définies sur l’extension

Pour limiter l’impact de l’extension sur les applications qui s’exécutent à l’intérieur de la machine, la configuration d’invité ne peut pas dépasser plus de 5 % de la capacité du processeur. Cette limitation existe à la fois pour les définitions intégrées et personnalisées.

### <a name="validation-tools"></a>Outils de validation

À l’intérieur de la machine, le client de configuration d’invité utilise des outils locaux pour exécuter l’audit.

Le tableau suivant affiche une liste des outils locaux utilisés sur chaque système d’exploitation pris en charge :

|Système d’exploitation|Outil de validation|Notes|
|-|-|-|
|Windows|[Configuration d’état souhaité Windows PowerShell](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Si Ruby et Python ne sont pas présents sur l’ordinateur, ils sont installés par l’extension de configuration d’invité. |

### <a name="validation-frequency"></a>Fréquence de validation

Le client de configuration d'invité vérifie le nouveau contenu toutes les 5 minutes. Une fois l’affectation d’invité reçue, les paramètres de cette configuration sont revérifiés à intervalle de 15 minutes.
Les résultats sont envoyés au fournisseur de ressources de configuration d’invité dès la fin de l’audit. Lorsqu'un [déclencheur d’évaluation](../how-to/get-compliance-data.md#evaluation-triggers) de stratégie intervient, l'état de la machine est consigné dans le fournisseur de ressources de configuration d'invité. Avec cette mise à jour, Azure Policy évalue alors les propriétés Azure Resource Manager. Une évaluation Azure Policy à la demande récupère la valeur la plus récente du fournisseur de ressources de configuration d'invité. Cela étant, elle ne déclenche pas de nouvel audit de la configuration de la machine.

## <a name="supported-client-types"></a>Types de clients pris en charge

Les stratégies de configuration d’invité sont incluses dans les nouvelles versions. Les versions antérieures des systèmes d’exploitation disponibles dans la Place de marché Azure sont exclues si l’agent de configuration d'invité n’est pas compatible. Le tableau suivant affiche une liste des systèmes d’exploitation pris en charge sur des images Azure :

|Serveur de publication|Nom|Versions|
|-|-|-|
|Canonical|Serveur Ubuntu|14.04 ou version ultérieure|
|Credativ|Debian|8 ou version ultérieure|
|Microsoft|Windows Server|2012 ou version ultérieure|
|Microsoft|Client Windows|Windows 10|
|OpenLogic|CentOS|7.3 ou version ultérieure|
|Red Hat|Red Hat Enterprise Linux|7.4 ou version ultérieure|
|SUSE|SLES|12 SP3 ou version ultérieure|

Les images de machine virtuelle personnalisées sont prises en charge par les stratégies de configuration d'invité, dans la mesure où il s’agit d'un des systèmes d’exploitation répertoriés dans le tableau ci-dessus.

### <a name="unsupported-client-types"></a>Types de clients non pris en charge

Windows Server Nano Server n’est pris en charge dans aucune version.

## <a name="guest-configuration-extension-network-requirements"></a>Configuration réseau requise pour l’extension de configuration d’invité

Pour communiquer avec le fournisseur de ressources de configuration d’invité dans Azure, les machines nécessitent un accès sortant vers des centres de données Azure sur le port **443**. Si un réseau dans Azure n’autorise pas le trafic sortant, configurez des exceptions à l’aide de règles du [Groupe de sécurité réseau](../../../virtual-network/manage-network-security-group.md#create-a-security-rule).
L’[étiquette de service](../../../virtual-network/service-tags-overview.md) « GuestAndHybridManagement » peut être utilisée pour référencer le service Guest Configuration.

## <a name="azure-managed-identity-requirements"></a>Exigences relatives à l’identité managée Azure

Les stratégies **DeployIfNotExists** qui ajoutent l’extension à des machines virtuelles activent également une identité managée affectée par le système, s’il n’en existe pas.

> [!WARNING]
> Évitez d’activer l’identité managée affectée par l’utilisateur à des machines virtuelles situées dans l’étendue pour des stratégies qui autorisent une identité managée affectée par le système. L’identité affectée par l’utilisateur sera remplacée et la machine pourrait cesser de répondre.

## <a name="guest-configuration-definition-requirements"></a>Exigences de définition de la configuration d’invité

Chaque audit exécuté par la configuration d’invité nécessite deux définitions de stratégies : une définition **DeployIfNotExists** et une définition **AuditIfNotExists**. 

La définition de stratégie **DeployIfNotExists** valide et corrige les éléments suivants :

- Validez qu’une configuration à évaluer a été attribuée à la machine. Si aucune attribution n’est actuellement présente, procurez-vous cette attribution et préparez la machine en effectuant les opérations suivantes :
  - Authentification auprès de la machine en utilisant une [identité managée](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Installation de la dernière version de l’extension **Microsoft.GuestConfiguration**
  - Installation des [outils de validation](#validation-tools) et des dépendances, si nécessaire

Si l’attribution de **DeployIfNotExists** n’est pas conforme, une [tâche de correction](../how-to/remediate-resources.md#create-a-remediation-task) peut être utilisée.

Une fois que l’attribution de **DeployIfNotExists** est conforme, l’attribution de la stratégie **AuditIfNotExists** détermine si l’affection d’invité est conforme ou non. L’outil de validation fournit les résultats au client de configuration d’invité. Le client transmet à l’extension invité les résultats pour les rendre disponibles via le fournisseur de ressources de la configuration d’invité.

Azure Policy utilise la propriété **complianceStatus** des fournisseurs de ressources de configuration d’invité pour signaler la conformité dans le nœud **Conformité**. Pour plus d’informations, consultez [Obtention de données de conformité](../how-to/get-compliance-data.md).

> [!NOTE]
> La stratégie **DeployIfNotExists** est requise pour que la stratégie **AuditIfNotExists** retourne des résultats. Sans la stratégie **DeployIfNotExists**, la stratégie **AuditIfNotExists** affiche « 0 sur 0 » ressource comme état.

Toutes les stratégies intégrées pour la configuration d’invité sont incluses dans une initiative pour regrouper les définitions à utiliser dans les attributions. L’initiative intégré nommée _\[Préversion\] : Auditer les paramètres de sécurité de mot de passe dans les machines Linux et Windows_ contient 18 stratégies. Il existe six paires **DeployIfNotExists** et **AuditIfNotExists** pour Windows et trois paires pour Linux. La logique de [définition de stratégie](definition-structure.md#policy-rule) valide que seul le système d’exploitation cible est évalué.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Audit des paramètres du système d’exploitation conformément aux lignes de base du secteur

Une initiative dans Azure Policy permet d’auditer les paramètres de système d’exploitation en suivant un « planning de référence ». La définition, _\[Préversion\] : Auditer les machines virtuelles Windows qui ne correspondent pas aux paramètres de la base de référence de sécurité Azure_ comprend un ensemble de règles basées sur une stratégie de groupe Active Directory.

La plupart des paramètres sont disponibles en tant que tels. Les paramètres vous permettent de personnaliser ce qui est audité. Alignez la stratégie sur vos besoins ou mappez la stratégie à des informations tierces, telles que les normes réglementaires sectorielles.

Certains paramètres prennent en charge une plage de valeurs entières. Par exemple, le paramètre Antériorité maximale du mot de passe peut auditer le paramètre Stratégie de groupe effectif. Une plage de « 1,70 » confirme que les utilisateurs doivent modifier leur mot de passe au moins tous les 70 jours, mais pas plus d’une fois par jour.

Si vous attribuer la stratégie à l’aide d’un modèle de déploiement Azure Resource Manager, utilisez un fichier de paramètres pour gérer les exceptions. Archivez les fichiers dans un système de contrôle de version tel que Git. Les commentaires sur les modifications de fichiers fournissent une preuve de la raison pour laquelle une affectation est une exception à la valeur attendue.

#### <a name="applying-configurations-using-guest-configuration"></a>Application de configurations à l’aide de Guest Configuration

La dernière fonctionnalité d’Azure Policy configure les paramètres à l’intérieur des machines. La définition _Configurer le fuseau horaire sur les machines Windows_ apporte des modifications à la machine en configurant le fuseau horaire.

Lorsque vous attribuez des définitions qui commencent par _Configurer_, vous devez également attribuer la définition _Déployer les composants requis pour activer la stratégie de configuration d’invité sur des machines virtuelles Windows_. Vous pouvez combiner ces définitions dans une initiative.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Attribution de stratégies à des machines en dehors d’Azure

Les stratégies d’audit disponibles pour Guest Configuration incluent le type de ressource **Microsoft.HybridCompute/machines**. Toutes les machines intégrées à [Azure Arc pour les serveurs](../../../azure-arc/servers/overview.md) qui se trouvent dans l’étendue de l’attribution de stratégie sont automatiquement incluses.

### <a name="multiple-assignments"></a>Affectations multiples

Actuellement, les stratégies de configuration d’invité prennent en charge l’affectation d’une seule affectation d’invité par machine, même si l’affectation de stratégie utilise des paramètres différents.

## <a name="client-log-files"></a>Fichiers journaux du client

L’extension de configuration d’invité écrit les fichiers journaux aux emplacements suivants :

Windows : `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux : `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Où `<version>` indique le numéro de la version actuelle.

### <a name="collecting-logs-remotely"></a>Collecte des journaux à distance

La première étape de résolution des problèmes des configurations ou modules Guest Configuration doit être d’utiliser la cmdlet `Test-GuestConfigurationPackage` suivant la procédure pour [créer une stratégie d’audit Guest Configuration personnalisée pour Windows](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows).
Si cela ne réussit pas, la collecte des journaux des clients peut vous aider à diagnostiquer les problèmes.

#### <a name="windows"></a>Windows

Capturez des informations de fichiers journaux à l’aide de la [commande Run de la machine virtuelle Azure](../../../virtual-machines/windows/run-command.md). L’exemple de script PowerShell suivant peut être utile.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Capturez des informations de fichiers journaux à l’aide de la [commande Run de la machine virtuelle Azure](../../../virtual-machines/linux/run-command.md). L’exemple de script Bash suivant peut être utile.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Exemples de la configuration d’invité

Des exemples de stratégie intégrée de configuration d’invité sont disponibles dans les emplacements suivants :

- [Définitions de stratégie intégrées - Configuration d’invité](../samples/built-in-policies.md#guest-configuration)
- [Initiatives intégrées - Configuration d’invité](../samples/built-in-initiatives.md#guest-configuration)
- [Exemples Azure Policy - Dépôt GitHub](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment afficher les détails de chaque paramètre à partir de la [vue de conformité de la configuration d’invité](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)
- Consultez des exemples à la page [Exemples Azure Policy](../samples/index.md).
- Consultez la [Structure de définition Azure Policy](definition-structure.md).
- Consultez la page [Compréhension des effets de Policy](effects.md).
- Découvrez comment [créer des stratégies par programmation](../how-to/programmatically-create.md).
- Découvrez comment [obtenir des données de conformité](../how-to/get-compliance-data.md).
- Découvrez comment [corriger des ressources non conformes](../how-to/remediate-resources.md).
- Pour en savoir plus sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](../../management-groups/overview.md).
