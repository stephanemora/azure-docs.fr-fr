---
title: Résolution des erreurs courantes
description: Découvrez comment résoudre les problèmes liés à la création de définitions de stratégie, aux divers Kits de développement logiciel (SDK) et au module complémentaire pour Kubernetes.
ms.date: 05/22/2020
ms.topic: troubleshooting
ms.openlocfilehash: 6d23a148521506adf0c0fc16913a32aab5eb7a30
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135565"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>Résoudre les erreurs à l’aide d’Azure Policy

Vous pouvez rencontrer des erreurs lors de la création de définitions de stratégie, de l’utilisation du Kit de développement logiciel (SDK) ou de la configuration du module complémentaire [Azure Policy pour Kubernetes](../concepts/policy-for-kubernetes.md). Cet article décrit différentes erreurs qui peuvent se produire et explique comment les résoudre.

## <a name="finding-error-details"></a>Recherche des détails de l’erreur

L’emplacement des détails de l’erreur dépend de l’action à l’origine de l’erreur.

- Quand vous utilisez une stratégie personnalisée, essayez-la dans le portail Azure pour obtenir des commentaires linting sur le schéma ou passez en revue les [données de conformité](../how-to/get-compliance-data.md) qui en résultent pour voir comment les ressources ont été évaluées.
- Lorsque vous travaillez avec différents Kit de développement logiciel (SDK), le SDK fournit des détails sur la raison de l’échec de la fonction.
- Lorsque vous utilisez le module complémentaire pour Kubernetes, commencez par la [journalisation](../concepts/policy-for-kubernetes.md#logging) dans le cluster.

## <a name="general-errors"></a>Erreurs générales.

### <a name="scenario-alias-not-found"></a>Scénario : Alias introuvable

#### <a name="issue"></a>Problème

Azure Policy utilise des [alias](../concepts/definition-structure.md#aliases) à mapper aux propriétés Azure Resource Manager.

#### <a name="cause"></a>Cause

Un alias incorrect ou inexistant est utilisé dans une définition de stratégie.

#### <a name="resolution"></a>Résolution

Tout d’abord, vérifiez que la propriété Resource Manager est dotée d’un alias. Utilisez l’[extension Azure Policy pour Visual Studio Code](../how-to/extension-for-vscode.md), [Azure Resource Graph](../../resource-graph/samples/starter.md#distinct-alias-values) ou le Kit de développement logiciel (SDK) pour rechercher les alias disponibles. Si l’alias d’une propriété Resource Manager n’existe pas, créez un ticket de support.

### <a name="scenario-evaluation-details-not-up-to-date"></a>Scénario : Les détails de l’évaluation ne sont pas à jour

#### <a name="issue"></a>Problème

Une ressource est à l’état « Non démarré » ou les détails de compatibilité ne sont pas à jour.

#### <a name="cause"></a>Cause

L’application d’une nouvelle stratégie ou initiative prend environ 30 minutes. Les ressources nouvelles ou mises à jour dans l’étendue d’une attribution existante sont disponibles environ 15 minutes plus tard. Une analyse de conformité standard se produit toutes les 24 heures. Pour plus d’informations, consultez les [déclencheurs d’évaluation](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Résolution

Tout d’abord, attendez la durée appropriée pour que l’évaluation se termine et que les résultats de conformité soient disponibles dans le portail Azure ou le Kit de développement logiciel (SDK). Pour démarrer une nouvelle analyse d’évaluation avec Azure PowerShell ou l’API REST, consultez [Analyse d’évaluation à la demande](../how-to/get-compliance-data.md#on-demand-evaluation-scan).

### <a name="scenario-evaluation-not-as-expected"></a>Scénario : Évaluation non conforme aux attentes

#### <a name="issue"></a>Problème

Une ressource n’a pas l’état d’évaluation, _conforme_ ou _non conforme_, qui est attendu pour cette ressource.

#### <a name="cause"></a>Cause

La ressource n’est pas dans l’étendue appropriée pour l’attribution de stratégie, ou la définition de stratégie ne fonctionne pas comme prévu.

#### <a name="resolution"></a>Résolution

- Pour une ressource non conforme supposée être conforme, commencez par [déterminer les raisons de la non-conformité](../how-to/determine-non-compliance.md). La comparaison de la définition avec la valeur de propriété évaluée indique la raison pour laquelle une ressource n’est pas conforme.
- Pour une ressource conforme supposée être non conforme, lisez la définition de stratégie condition par condition et évaluez-la par rapport aux propriétés de la ressource. Vérifiez que les opérateurs logiques regroupent les conditions appropriées et que vos conditions ne sont pas inversées.

Si la conformité d’une attribution de stratégie indique `0/0` ressources, aucune ressource n’a été déterminée comme étant applicable dans l’étendue de l’attribution. Vérifiez à la fois la définition de la stratégie et l’étendue de l’attribution.

### <a name="scenario-enforcement-not-as-expected"></a>Scénario : Application non conforme aux attentes

#### <a name="issue"></a>Problème

Azure Policy n’agit pas sur une ressource sur laquelle il est supposé agir et il n’y a aucune entrée dans le [journal d’activité Azure](../../../azure-monitor/platform/platform-logs-overview.md).

#### <a name="cause"></a>Cause

L’attribution de stratégie a été configurée pour [enforcementMode](../concepts/assignment-structure.md#enforcement-mode) sur _Désactivé_. Si le mode de mise en conformité est désactivé, l’effet de stratégie n’est pas appliqué et il n’y a aucune entrée dans le journal d’activité.

#### <a name="resolution"></a>Résolution

Mettez à jour **enforcementMode** sur _Activé_. Cette modification permet à Azure Policy d’agir sur les ressources de cette attribution de stratégie et d’envoyer des entrées au journal d’activité. Si **enforcementMode** est déjà activé, consultez [Évaluation non conforme aux attentes](#scenario-evaluation-not-as-expected) pour connaître les mesures pouvant être prises.

### <a name="scenario-denied-by-azure-policy"></a>Scénario : Refusé par Azure Policy

#### <a name="issue"></a>Problème

La création ou la mise à jour d’une ressource est refusée.

#### <a name="cause"></a>Cause

Une attribution de stratégie sur l’étendue de la ressource nouvelle ou mise à jour répond aux critères d’une définition de stratégie avec un effet [Refuser](../concepts/effects.md#deny). Les ressources répondant à ces définitions ne peuvent pas être créées ni mises à jour.

#### <a name="resolution"></a>Résolution

Le message d’erreur d’une attribution de stratégie de refus comprend la définition de stratégie et les ID d’attribution de la stratégie. Si vous avez besoin de retrouver les informations d’erreur indiquées dans le message, elles sont également disponibles dans le [journal d’activité](../../../azure-monitor/platform/activity-log.md#view-the-activity-log). Utilisez ces informations pour obtenir plus de détails afin de comprendre les restrictions relatives aux ressources et d’ajuster les propriétés de ressource dans votre demande pour qu’elles correspondent aux valeurs autorisées.

## <a name="template-errors"></a>Erreurs de modèle

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Scénario : Fonctions prises en charge par Policy et traitées par le modèle

#### <a name="issue"></a>Problème

Azure Policy prend en charge un certain nombre de fonctions de modèle Azure Resource Manager (modèle ARM) et de fonctions qui sont uniquement disponibles dans une définition de stratégie. Resource Manager traite ces fonctions dans le cadre d’un déploiement plutôt que dans le cadre d’une définition de stratégie.

#### <a name="cause"></a>Cause

L’utilisation de fonctions prises en charge, telles que `parameter()` ou `resourceGroup()`, permet de traiter le résultat de la fonction au moment du déploiement au lieu de laisser la tâche à la définition de stratégie et au moteur Azure Policy.

#### <a name="resolution"></a>Résolution

Pour transmettre une fonction afin qu’elle fasse partie d’une définition de stratégie, placez dans une séquence d’échappement la chaîne entière à l’aide du caractère `[` de manière à ce que la propriété ressemble à `[[resourceGroup().tags.myTag]`. Le caractère d’échappement fait que Resource Manager traite la valeur comme une chaîne lors du traitement du modèle. Azure Policy place ensuite la fonction dans la définition de stratégie, ce qui lui permet d’être dynamique comme prévu. Pour plus d’informations, voir [Syntaxe et expressions dans les modèles Azure Resource Manager](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-installation-errors"></a>Erreurs d’installation du module complémentaire

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>Scénario : L’installation à l’aide du chart Helm échoue sur le mot de passe

#### <a name="issue"></a>Problème

Échec de la commande `helm install azure-policy-addon` avec l’un des messages suivants :

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Cause

Le mot de passe généré comprend une virgule (`,`) sur laquelle le chart Helm est fractionné.

#### <a name="resolution"></a>Résolution

Placez dans une séquence d’échappement la virgule (`,`) dans la valeur de mot de passe lors de l’exécution de `helm install azure-policy-addon` à l’aide d’une barre oblique inverse (`\`).

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>Scénario : L’installation à l’aide du chart Helm échoue, car le nom existe déjà

#### <a name="issue"></a>Problème

Échec de la commande `helm install azure-policy-addon` avec le message suivant :

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Cause

Le chart Helm portant le nom `azure-policy-addon` a déjà été installé ou partiellement installé.

#### <a name="resolution"></a>Résolution

Suivez les instructions pour [supprimer la stratégie Azure pour le module complémentaire Kubernetes](../concepts/policy-for-kubernetes.md#remove-the-add-on), puis réexécutez la commande `helm install azure-policy-addon`.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

- Obtenez des réponses d’experts par le biais de [Microsoft Q&A](/answers/topics/azure-policy.html).
- Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
- Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.
