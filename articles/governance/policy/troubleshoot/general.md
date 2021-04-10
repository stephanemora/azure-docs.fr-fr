---
title: Résolution des erreurs courantes
description: Découvrez comment résoudre les problèmes liés à la création de définitions de stratégie, aux divers kits de développement logiciel (SDK) et au module complémentaire pour Kubernetes.
ms.date: 01/26/2021
ms.topic: troubleshooting
ms.openlocfilehash: 6e0e4067f07266bae9c87fd4443d27314cc28c0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100592614"
---
# <a name="troubleshoot-errors-with-using-azure-policy"></a>Résolution des erreurs liées à Azure Policy

Lors de la création de définitions de stratégie, de l’utilisation des kits de développement logiciel (SDK) ou de la configuration du module complémentaire [Azure Policy pour Kubernetes](../concepts/policy-for-kubernetes.md), des erreurs peuvent survenir. Cet article décrit différentes erreurs générales susceptibles de se produire, et propose des moyens de les résoudre.

## <a name="find-error-details"></a>Localisation des détails de l’erreur

L’emplacement des détails de l’erreur dépend de l’aspect d’Azure Policy dont il est question.

- Si vous utilisez une stratégie personnalisée, accédez au Portail Azure pour obtenir des commentaires de linting sur le schéma ou passez en revue les [données de conformité](../how-to/get-compliance-data.md) qui en résultent pour voir comment les ressources ont été évaluées.
- Si vous utilisez l’un des kits SDK, celui-ci donne des détails sur la raison de l’échec de la fonction.
- Si vous utilisez le module complémentaire pour Kubernetes, commencez par la [journalisation](../concepts/policy-for-kubernetes.md#logging) dans le cluster.

## <a name="general-errors"></a>Erreurs générales.

### <a name="scenario-alias-not-found"></a>Scénario : Alias introuvable

#### <a name="issue"></a>Problème

Un alias incorrect ou inexistant est utilisé dans une définition de stratégie. Azure Policy utilise des [alias](../concepts/definition-structure.md#aliases) à mapper aux propriétés Azure Resource Manager.

#### <a name="cause"></a>Cause

Un alias incorrect ou inexistant est utilisé dans une définition de stratégie.

#### <a name="resolution"></a>Résolution

Tout d’abord, vérifiez que la propriété Resource Manager est dotée d’un alias. Pour rechercher les alias disponibles, accédez à [l’extension Azure Policy pour Visual Studio Code](../how-to/extension-for-vscode.md) ou au kit SDK.
Si l’alias d’une propriété Resource Manager n’existe pas, créez un ticket de support.

### <a name="scenario-evaluation-details-arent-up-to-date"></a>Scénario : Les détails de l’évaluation ne sont pas à jour

#### <a name="issue"></a>Problème

Une ressource est à l’état _Non démarré_, ou les détails de compatibilité ne sont pas à jour.

#### <a name="cause"></a>Cause

L’application d’une nouvelle attribution de stratégie ou d’initiative prend environ 30 minutes. Les nouvelles ressources et les ressources mises à jour dans l’étendue d’une attribution existante sont disponibles au bout d’environ 15 minutes. Une analyse de conformité standard se produit toutes les 24 heures. Pour plus d’informations, consultez les [déclencheurs d’évaluation](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Résolution

Tout d’abord, attendez le temps nécessaire pour qu’une évaluation se termine et que les résultats de conformité soient disponibles sur le Portail Azure ou dans le kit SDK. Pour commencer une nouvelle analyse d’évaluation avec Azure PowerShell ou l’API REST, consultez [Analyse d’évaluation à la demande](../how-to/get-compliance-data.md#on-demand-evaluation-scan).

### <a name="scenario-compliance-isnt-as-expected"></a>Scénario : État de conformité non attendu

#### <a name="issue"></a>Problème

Une ressource ne présente pas l’état d’évaluation _Conforme_ ou _Non conforme_ attendu.

#### <a name="cause"></a>Cause

La ressource ne se trouve pas dans la bonne étendue de l’attribution de stratégie, ou la définition de stratégie ne fonctionne pas comme prévu.

#### <a name="resolution"></a>Résolution

Pour résoudre les problèmes liés à votre définition de stratégie, procédez comme suit :

1. Tout d’abord, attendez le temps nécessaire pour qu’une évaluation se termine et que les résultats de conformité soient disponibles sur le Portail Azure ou dans le kit SDK. 

1. Pour commencer une nouvelle analyse d’évaluation avec Azure PowerShell ou l’API REST, consultez [Analyse d’évaluation à la demande](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Vérifiez que les paramètres et l’étendue d’attribution sont correctement définis.
1. Vérifiez le [mode de définition de la stratégie](../concepts/definition-structure.md#mode) :
   - Le mode doit être `all` pour tous les types de ressources.
   - Le mode doit être `indexed` si la définition de stratégie recherche des balises ou un emplacement.
1. Vérifiez que l’étendue de la ressource n’est ni [exclue](../concepts/assignment-structure.md#excluded-scopes) ni [exempte](../concepts/exemption-structure.md).
1. Si la conformité d’une attribution de stratégie indique `0/0` ressources, aucune ressource n’a été déterminée comme étant applicable dans l’étendue de l’attribution. Vérifiez à la fois la définition de la stratégie et l’étendue de l’attribution.
1. Pour une ressource non conforme alors qu’elle est censée l’être, consultez [Détermination des raisons de la non-conformité](../how-to/determine-non-compliance.md). La comparaison de la définition avec la valeur de propriété évaluée indique la raison pour laquelle une ressource n’est pas conforme.
   - Si la **valeur cible** est incorrecte, révisez la définition de la stratégie.
   - Si la **valeur actuelle** est incorrecte, validez la charge utile de la ressource par le biais de `resources.azure.com`.
1. Pour d’autres problèmes et solutions courants, consultez [Résolution des problèmes : Mise en œuvre non conforme aux attentes](#scenario-enforcement-not-as-expected).

Si vous rencontrez toujours un problème avec votre définition de stratégie intégrée dupliquée et personnalisée ou une définition personnalisée, créez un ticket de support sous **Création d’une stratégie** pour acheminer le problème correctement.

### <a name="scenario-enforcement-not-as-expected"></a>Scénario : Application non conforme aux attentes

#### <a name="issue"></a>Problème

Azure Policy n’agit pas sur une ressource sur laquelle il est censé le faire, et il n’y a aucune entrée dans le [journal d’activité Azure](../../../azure-monitor/essentials/platform-logs-overview.md).

#### <a name="cause"></a>Cause

L’attribution de stratégie a été configurée pour un paramètre [**enforcementMode**](../concepts/assignment-structure.md#enforcement-mode) défini sur _Désactivé_.
Si **enforcementMode** est désactivé, l’effet de stratégie n’est pas appliqué, et il n’y a aucune entrée dans le journal d’activité.

#### <a name="resolution"></a>Résolution

Pour résoudre les problèmes liés à la mise en œuvre de l’attribution de votre stratégie, procédez comme suit :

1. Tout d’abord, attendez le temps nécessaire pour qu’une évaluation se termine et que les résultats de conformité soient disponibles sur le Portail Azure ou dans le kit SDK. 

1. Pour commencer une nouvelle analyse d’évaluation avec Azure PowerShell ou l’API REST, consultez [Analyse d’évaluation à la demande](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Vérifiez que les paramètres et l’étendue d’attribution sont correctement définis et que **enforcementMode** est _Activé_.
1. Vérifiez le [mode de définition de la stratégie](../concepts/definition-structure.md#mode) :
   - Le mode doit être `all` pour tous les types de ressources.
   - Le mode doit être `indexed` si la définition de stratégie recherche des balises ou un emplacement.
1. Vérifiez que l’étendue de la ressource n’est ni [exclue](../concepts/assignment-structure.md#excluded-scopes) ni [exempte](../concepts/exemption-structure.md).
1. Vérifiez que la charge utile de la ressource correspond à la logique de la stratégie. Pour cela, vous pouvez [capturer une trace d’archive HTTP (HAR)](../../../azure-portal/capture-browser-trace.md) ou passer en revue les propriétés du modèle Azure Resource Manager (ARM).
1. Pour d’autres problèmes et solutions courants, consultez [Résolution des problèmes : État de conformité non attendu](#scenario-compliance-isnt-as-expected).

Si vous rencontrez toujours un problème avec votre définition de stratégie intégrée dupliquée et personnalisée ou une définition personnalisée, créez un ticket de support sous **Création d’une stratégie** pour acheminer le problème correctement.

### <a name="scenario-denied-by-azure-policy"></a>Scénario : Refusé par Azure Policy

#### <a name="issue"></a>Problème

La création ou la mise à jour d’une ressource est refusée.

#### <a name="cause"></a>Cause

Une attribution de stratégie définie sur l’étendue de la nouvelle ressource ou de la ressource mise à jour répond aux critères d’une définition de stratégie dont l’effet est [Refuser](../concepts/effects.md#deny). Les ressources qui correspondent à ces définitions ne peuvent pas être créées ni mises à jour.

#### <a name="resolution"></a>Résolution

Le message d’erreur d’une attribution de stratégie de refus comprend la définition de stratégie et les ID d’attribution de la stratégie. Si vous avez besoin de retrouver les informations d’erreur indiquées dans le message, elles sont également disponibles dans le [journal d’activité](../../../azure-monitor/essentials/activity-log.md#view-the-activity-log). Utilisez ces informations pour obtenir plus de détails afin de comprendre les restrictions relatives aux ressources et d’ajuster les propriétés de ressource dans votre demande pour qu’elles correspondent aux valeurs autorisées.

## <a name="template-errors"></a>Erreurs de modèle

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Scénario : Fonctions prises en charge par Policy et traitées par le modèle

#### <a name="issue"></a>Problème

Azure Policy prend en charge un certain nombre de fonctions de modèle ARM et de fonctions qui ne sont disponibles que dans une définition de stratégie. Resource Manager traite ces fonctions dans le cadre d’un déploiement plutôt que dans le cadre d’une définition de stratégie.

#### <a name="cause"></a>Cause

L’utilisation de fonctions prises en charge (par exemple `parameter()` et `resourceGroup()`) permet de traiter le résultat de la fonction au moment du déploiement au lieu de laisser la tâche à la définition de stratégie et au moteur Azure Policy.

#### <a name="resolution"></a>Résolution

Pour transmettre une fonction au sein d’une définition de stratégie, placez toute la chaîne dans une séquence d’échappement à l’aide du caractère `[` de sorte que la propriété se présente sous la forme `[[resourceGroup().tags.myTag]`. Le caractère d’échappement conduit Resource Manager à considérer la valeur comme une chaîne lorsqu’il traite le modèle. Azure Policy place ensuite la fonction dans la définition de stratégie, ce qui lui permet d’être dynamique comme prévu. Pour plus d’informations, voir [Syntaxe et expressions dans les modèles Azure Resource Manager](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-for-kubernetes-installation-errors"></a>Module complémentaire pour les erreurs d’installation de Kubernetes

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-of-a-password-error"></a>Scénario : L’installation à l’aide d’un chart Helm échoue en raison d’une erreur de mot de passe

#### <a name="issue"></a>Problème

La commande `helm install azure-policy-addon` échoue et retourne l’une des erreurs suivantes :

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Cause

Le mot de passe généré comprend une virgule (`,`) sur laquelle le chart Helm est fractionné.

#### <a name="resolution"></a>Résolution

Lorsque vous exécutez `helm install azure-policy-addon`, placez la virgule (`,`) dans une séquence d’échappement à l’aide d’une barre oblique inverse (`\`) dans la valeur du mot de passe.

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-the-name-already-exists"></a>Scénario : L’installation à l’aide d’un chart Helm échoue, car le nom existe déjà

#### <a name="issue"></a>Problème

La commande `helm install azure-policy-addon` échoue et retourne l’erreur suivante :

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Cause

Le chart Helm portant le nom `azure-policy-addon` a déjà été installé ou partiellement installé.

#### <a name="resolution"></a>Résolution

Suivez les instructions pour [supprimer le module complémentaire Azure Policy pour Kubernetes](../concepts/policy-for-kubernetes.md#remove-the-add-on), puis réexécutez la commande `helm install azure-policy-addon`.

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Scénario : Les identités attribuées par l’utilisateur de la machine virtuelle Azure sont remplacées par des identités managées attribuées par le système

#### <a name="issue"></a>Problème

Une fois que vous avez attribué des initiatives de stratégie Guest Configuration pour auditer les paramètres d’un ordinateur, les identités managées affectées par l’utilisateur à l’ordinateur ne sont plus affectées. Seule une identité managée attribuée par le système est attribuée.

#### <a name="cause"></a>Cause

Les définitions de stratégie précédemment utilisées dans les définitions DeployIfNotExists de Guest Configuration permettaient de faire en sorte qu’une identité affectée par le système soit affectée à l’ordinateur, mais supprimaient aussi les attributions d’identité affectées par l’utilisateur.

#### <a name="resolution"></a>Résolution

Les définitions qui provoquaient ce problème apparaissent comme _\[Déconseillées\]_ et sont remplacées par des définitions de stratégie qui gèrent les prérequis sans supprimer les identités managées affectées par l’utilisateur. Une étape manuelle est nécessaire. Supprimez les attributions de stratégies marquées comme _\[Déconseillées\]_ et remplacez-les par les définitions de stratégie et d’initiative de stratégie requises et mises à jour qui portent le même nom que l’original.

Pour une description détaillée, consultez le billet de blog [Publication d’une modification importante pour les stratégies d’audit de Guest Configuration](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316).

## <a name="add-on-for-kubernetes-general-errors"></a>Module complémentaire pour les erreurs générales de Kubernetes

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-egress-restrictions"></a>Scénario : Le module complémentaire ne parvient pas à joindre le point de terminaison de service Azure Policy en raison de restrictions de sortie

#### <a name="issue"></a>Problème

Le module complémentaire ne parvient pas à joindre le point de terminaison de service Azure Policy et retourne l’une des erreurs suivantes :

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Cause

Ce problème se produit lorsqu’une sortie de cluster est verrouillée.

#### <a name="resolution"></a>Résolution

Vérifiez que les domaines et les ports mentionnés dans les articles suivants sont ouverts :

- [Règles de réseau sortantes et noms de domaine complet (FQDN) requis pour les clusters AKS](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Installation du module complémentaire Azure Policy pour Kubernetes avec Azure Arc (préversion)](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-the-aad-pod-identity-configuration"></a>Scénario : Le module complémentaire ne parvient pas à joindre le point de terminaison de service Azure Policy en raison de la configuration aad-pod-identity

#### <a name="issue"></a>Problème

Le module complémentaire ne parvient pas à joindre le point de terminaison de service Azure Policy et retourne l’une des erreurs suivantes :

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Cause

Cette erreur se produit lorsque _add-pod-identity_ est installé sur le cluster et que les pods _kube-system_ ne sont pas exclus dans _aad-pod-identity_.

Les pods NMI (Node Managed Identity) du composant _aad-pod-identity_ modifient les iptables des nœuds pour intercepter les appels au point de terminaison Azure Instance Metadata. Dans cette configuration, toutes les requêtes adressées au point de terminaison des métadonnées sont interceptées par NMI, même si le pod n’utilise pas _aad-pod-identity_.
La définition CRD (CustomResourceDefinition) _AzurePodIdentityException_ peut être configurée de manière à informer _aad-pod-identity_ que toutes les requêtes adressées à un point de terminaison des métadonnées provenant d’un pod et correspondant aux étiquettes définies dans la définition CRD doivent être redirigées via proxy sans aucun traitement dans NMI.

#### <a name="resolution"></a>Résolution

Excluez les pods système comportant l’étiquette `kubernetes.azure.com/managedby: aks` dans l’espace de noms _kube-system_ dans _aad-pod-identity_ en configurant la définition CRD _AzurePodIdentityException_.

Pour plus d’informations, consultez [Désactivation de l’identité de pod Azure Active Directory (Azure AD) pour un pod ou une application spécifique](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).

Pour configurer une exception, suivez cet exemple :

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: mic-exception
  namespace: default
spec:
  podLabels:
    app: mic
    component: mic
---
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: aks-addon-exception
  namespace: kube-system
spec:
  podLabels:
    kubernetes.azure.com/managedby: aks
```

### <a name="scenario-the-resource-provider-isnt-registered"></a>Scénario : Le fournisseur de ressources n’est pas inscrit

#### <a name="issue"></a>Problème

Le module complémentaire parvient à joindre le point de terminaison de service Azure Policy, mais les journaux du module complémentaire présentent l’une des erreurs suivantes :

- `The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
  https://aka.ms/policy-register-subscription for how to register subscriptions.`

- `policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
  StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
  Code="InternalServerError" Message="Encountered an internal server error.`

#### <a name="cause"></a>Cause

Le fournisseur de ressources « Microsoft.PolicyInsights » n’est pas inscrit. Il doit l’être pour que le module complémentaire récupère les définitions de stratégie et retourne les données de conformité.

#### <a name="resolution"></a>Résolution

Inscrivez le fournisseur de ressources « Microsoft.PolicyInsights » dans l’abonnement du cluster. Pour obtenir des instructions, consultez [Inscription d’un fournisseur de ressources](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

### <a name="scenario-the-subscription-is-disabled"></a>Scénario : L'abonnement est désactivé

#### <a name="issue"></a>Problème

Le module complémentaire parvient à joindre le point de terminaison de service Azure Policy, mais l’erreur suivante s’affiche :

`The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.`

#### <a name="cause"></a>Cause

Cette erreur signifie que l’abonnement a été identifié comme problématique, et que l’indicateur de fonctionnalité `Microsoft.PolicyInsights/DataPlaneBlocked` a été ajouté pour le bloquer.

#### <a name="resolution"></a>Résolution

Pour examiner et résoudre ce problème, [contactez l’équipe chargée des fonctionnalités](mailto:azuredg@microsoft.com).

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne pouvez pas le résoudre, accédez au support en visitant l’un des canaux suivants :

- Obtenez des réponses d’experts par le biais de [Microsoft Q&A](/answers/topics/azure-policy.html).
- Contactez [@AzureSupport](https://twitter.com/azuresupport). Cette ressource Microsoft Azure officielle sur Twitter aide à améliorer l’expérience client en orientant la communauté Azure vers les réponses, le support et les experts appropriés.
- Si vous avez encore besoin d’aide, accédez au [site de support Azure](https://azure.microsoft.com/support/options/) et sélectionnez **Soumettre une demande de support**.
