---
title: Options de correction pour la configuration d’invité
description: La fonctionnalité de configuration d’invité d’Azure Policy offre des options de correction continue ou de contrôle à l’aide de tâches de correction.
ms.date: 07/12/2021
ms.topic: how-to
ms.openlocfilehash: 6c16f4a2b20ea753b1ded4e8d389babc613b4b36
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773040"
---
# <a name="remediation-options-for-guest-configuration"></a>Options de correction pour la configuration d’invité

Avant de commencer, nous vous conseillons de lire la page de présentation de la [configuration d’invité](../concepts/guest-configuration.md).

> [!IMPORTANT]
> L’extension de configuration d’invité est requise pour les machines virtuelles Azure. Pour déployer l’extension à grande échelle sur toutes les machines, attribuez l’initiative de stratégie suivante : `Deploy prerequisites to enable guest configuration policies on
> virtual machines`
> 
> Pour utiliser les packages de configuration d’invité qui appliquent des configurations, l’extension de configuration d’invité de machine virtuelle Azure version **1.29.24** ou ultérieure, ou l’agent Arc version **1.10.0** ou version ultérieure, sont requis.
>
> Les définitions de stratégie de configuration d’invité personnalisées utilisant **AuditIfNotExists** sont généralement disponibles, mais les définitions utilisant **DeployIfNotExists** avec la configuration d’invité sont **en préversion**.

## <a name="how-remediation-set-is-managed-by-guest-configuration"></a>Comment la correction (Set) est gérée par la configuration d’invité

La configuration d’invité utilise l’effet de stratégie [DeployIfNotExists](../concepts/effects.md#deployifnotexists) pour les définitions qui apportent des modifications à l’intérieur des machines.
Définissez les propriétés d’une attribution de stratégie pour contrôler la façon dont une [évaluation](../concepts/effects.md#deployifnotexists-evaluation) fournit des configurations automatiquement ou à la demande.

[Un guide vidéo de ce document est disponible](https://youtu.be/rjAk1eNmDLk).

### <a name="guest-configuration-assignment-types"></a>Types d’attributions de configuration d’invités

Trois types d’attribution sont disponibles lors de la création d’attributions d’invité.
La propriété est disponible en tant que paramètre des définitions de configuration d’invité qui prennent en charge **DeployIfNotExists**.

| Type d’attribution | Comportement |
|-|-|
| Audit | Rendre compte de l’état de la machine, mais ne pas apporter de modifications. |
| ApplyandMonitor | Appliqué une fois à la machine, puis analysé pour suivre les modifications. Si la configuration dérive et devient non conforme, elle n’est pas corrigée automatiquement, sauf si une correction est déclenchée. |
| ApplyandAutoCorrect | Appliqué à la machine. En cas de dérive, le service local à l’intérieur de la machine effectue une correction lors de l’évaluation suivante. |

Dans chacun des trois types d’attributions, quand une nouvelle stratégie est attribuée à une machine existante, une attribution d’invité est automatiquement créée pour vérifier l’état de la configuration, qui fournit des informations permettant de prendre des décisions concernant les machines nécessitant une correction.

## <a name="remediation-on-demand-applyandmonitor"></a>Correction à la demande (ApplyAndMonitor)

Par défaut, les attributions de configuration d’invité fonctionnent dans un scénario de « correction à la demande ». La configuration est appliquée, puis autorisée à dériver par rapport à la conformité. L’état de conformité de l’attribution d’invité est « conforme », sauf si une erreur se produit lors de l’application de la configuration ou si, lors de l’évaluation suivante, la machine n’est plus dans l’état souhaité. L’agent signale l’état « non conforme » et ne corrige pas automatiquement le problème.

Pour activer ce comportement, définissez la [propriété assignmentType](/rest/api/guestconfiguration/guest-configuration-assignments/get#assignmenttype) de l’attribution de configuration d’invité sur « ApplyandMonitor ». Chaque fois que l’attribution est traitée au sein de la machine, pour chaque ressource, la méthode [Test](/powershell/scripting/dsc/resources/get-test-set#test) retourne « true » et l’agent signale « Compliant » ou, si la méthode retourne « false », l’agent signale « NonCompliant ».

## <a name="continuous-remediation-autocorrect"></a>Correction continue (AutoCorrect)

La configuration d’invité prend en charge le concept de « correction continue ». Si la machine dérive de la conformité pour une configuration, lors de l’évaluation suivante, la configuration est corrigée automatiquement. Si aucune erreur ne se produit, la machine signale toujours l’état comme étant « Compliant » pour la configuration. Lors de l’utilisation de la correction continue, il n’existe aucun moyen de signaler qu’une dérive a été corrigée automatiquement.

Pour activer ce comportement, définissez la [propriété assignmentType](/rest/api/guestconfiguration/guest-configuration-assignments/get#assignmenttype) de l’attribution de configuration d’invité sur « AppyandAutocirrect ». Chaque fois que l’attribution est traitée à l’intérieur de la machine, pour chaque ressource, la méthode [Test](/powershell/scripting/dsc/resources/get-test-set#test) retourne « false », et la méthode [Set](/powershell/scripting/dsc/resources/get-test-set#set) s’exécute automatiquement.

## <a name="disable-remediation"></a>Désactiver la correction

Lorsque la propriété `assignmentType` est définie sur « Audit », l’agent effectue uniquement un audit de la machine et ne tente pas de corriger la configuration si elle n’est pas conforme.

### <a name="disable-remediation-of-custom-content"></a>Désactiver la correction du contenu personnalisé

Vous pouvez remplacer la propriété de type d’attribution pour les packages de contenu personnalisé en ajoutant une étiquette à la machine avec le nom **CustomGuestConfigurationSetPolicy** et la valeur **disable**. L’ajout de l’étiquette désactive la correction des packages de contenu personnalisé uniquement, pas le contenu intégré fourni par Microsoft.

## <a name="azure-policy-enforcement"></a>Mise en conformité d’Azure Policy

Les attributions d’Azure Policy incluent un [Mode de mise en conformité](../concepts/assignment-structure.md#enforcement-mode) de propriété requise qui détermine le comportement des ressources nouvelles et existantes.
Utilisez cette propriété pour contrôler si les configurations sont automatiquement appliquées aux machines.

**Par défaut, la mise en conformité est « Activée ».** Lors du déploiement d’une nouvelle machine ou de la **mise à jour des propriétés d’un machine**, si la machine se trouve dans l’étendue d’une attribution d’Azure Policy avec une définition de stratégie dans la catégorie « Configuration d’invité », Azure Policy applique automatiquement la configuration. **Les opérations de mise à jour incluent des actions qui se produisent dans Azure Resource Manager**, telles que l’ajout ou la modification d’une étiquette, et, pour les machines virtuelles, des modifications telles qu’un redimensionnement ou l’attachement d’un disque. Laissez la mise en conformité activée si la configuration doit être corrigée lorsque des modifications sont apportées à la ressource machine dans Azure. Les modifications qui se produisent à l’intérieur de la machine ne déclenchent pas de correction automatique tant qu’elles ne modifient pas la ressource machine dans Azure Resource Manager.

Si la mise en conformité a la valeur « Désactivée », l’attribution de configuration vérifie l’état de la machine jusqu’à ce que le comportement soit modifié par une [tâche de correction](../how-to/remediate-resources.md). Par défaut, les définitions de configuration d’invité mettent à jour la [propriété assignmentType](/rest/api/guestconfiguration/guest-configuration-assignments/get#assignmenttype) d’« Audit » à « ApplyandMonitor » de sorte que la configuration soit appliquée une seule fois, puis ne s’applique plus tant qu’une correction n’a pas été déclenchée.

## <a name="optional-remediate-all-existing-machines"></a>FACULTATIF : Corriger tous les machines existants

Si une attribution d’Azure Policy est créée à partir du portail Azure, sous l’onglet « Correction », une case à cocher « Créer une tâche de correction » est disponible. Quand la case est activée, une fois l’attribution de stratégie créée, toutes les ressources dont la valeur est « NonCompliant » sont automatiquement corrigées par des tâches de correction.

L’effet de ce paramètre pour la configuration d’invité est que vous pouvez déployer une configuration sur plusieurs machines simplement en attribuant une stratégie. Vous n’aurez pas besoin d’exécuter la tâche de correction manuellement pour les machines qui ne sont pas conformes.

## <a name="manually-trigger-remediation-outside-of-azure-policy"></a>Déclencher manuellement une correction en dehors d’Azure Policy

Il est également possible d’orchestrer une correction en dehors d’Azure Policy en mettant à jour une ressource d’attribution d’invité, même si la mise à jour n’apporte pas de modification aux propriétés de la ressource.

Lors de la création d’une attribution de configuration d’invité, la [propriété complianceStatus](/rest/api/guestconfiguration/guest-configuration-assignments/get#compliancestatus) est définie sur « Pending ».
Le service de configuration d’invité à l’intérieur de la machine (fourni aux machines virtuelles Azure par l’[extension de configuration d’invité](../../../virtual-machines/extensions/guest-configuration.md) et inclus dans les serveurs avec Arc) demande une liste d’attributions toutes les 5 minutes.
Si l’attribution de configuration d’invité a les deux exigences, `complianceStatus` « Pending » et `configurationMode` « ApplyandMonitor » ou « ApplyandAutoCorrect », le service dans la machine applique la configuration. Une fois la configuration appliquée, à l’[intervalle suivant](./guest-configuration.md#validation-frequency), le mode de configuration dicte si le comportement consiste uniquement à rendre compte de l’état de conformité et à autoriser la dérive, ou à corriger automatiquement.

## <a name="understanding-combinations-of-settings"></a>Compréhension des combinaisons de paramètres

|~| Audit | ApplyandMonitor | ApplyandAutoCorrect |
|-|-|-|-|
| Mise en conformité activée | État des rapports uniquement | Configuration appliquée lors de la création de la machine virtuelle **et réappliquée lors de la mise à jour**. Autrement dérive autorisée. | Configuration appliquée lors de la création de la machine virtuelle et réappliquée lors de la mise à jour, puis corrigée à l’intervalle suivant en cas de dérive. |
| Mise en conformité désactivée | État des rapports uniquement | Configuration appliquée mais dérive autorisée | Configuration appliquée lors de la création ou de la mise jour de la machine virtuelle, puis corrigée à l’intervalle suivant en cas de dérive. |

## <a name="next-steps"></a>Étapes suivantes

- Lisez la [Présentation de la configuration d’invité](./guest-configuration.md).
- Configurez un [environnement de développement](../how-to/guest-configuration-create-setup.md) de package de configuration d’invité personnalisé.
- [Créez un artefact de package](../how-to/guest-configuration-create.md) pour la configuration d’invité.
- [Testez l’artefact de package](../how-to/guest-configuration-create-test.md) à partir de votre environnement de développement.
- Utilisez le module `GuestConfiguration` afin de [créer une définition Azure Policy](../how-to/guest-configuration-create-definition.md) pour une gestion à grande échelle de votre environnement.
- [Attribuez votre définition de stratégie personnalisée](../assign-policy-portal.md) à l’aide du portail Azure.
- Découvrez comment visualiser les [informations relatives à la conformité pour les attributions de stratégie de configuration d’invité](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration).
