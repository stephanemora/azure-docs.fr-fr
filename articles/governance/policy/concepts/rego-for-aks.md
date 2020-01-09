---
title: Découvrir Azure Policy pour Azure Kubernetes Service
description: Découvrez comment Azure Policy utilise Rego et Open Policy Agent pour gérer les clusters sur Azure Kubernetes Service.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 9a4dd6bbc71c66c3ff37200ed57859b309909ae9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436391"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Comprendre Azure Policy pour Azure Kubernetes Service

Azure Policy s’intègre à [Azure Kubernetes Service](../../../aks/intro-kubernetes.md) (AKS) afin d’appliquer à grande échelle des mesures et des protections sur vos clusters d’une manière centralisée et cohérente.
En étendant l’utilisation de [Gatekeeper](https://github.com/open-policy-agent/gatekeeper/tree/master/deprecated) v2, un _webhook de contrôleur d’admission_ pour [Open Policy Agent](https://www.openpolicyagent.org/) (OPA), Azure Policy vous permet de gérer l’état de conformité de vos ressources Azure et clusters AKS et de créer des rapports à partir d’un seul endroit.

> [!NOTE]
> Azure Policy pour AKS est en préversion limitée et prend uniquement en charge les définitions de stratégie intégrées.

## <a name="overview"></a>Vue d’ensemble

Pour activer et utiliser Azure Policy pour AKS avec votre cluster AKS, effectuez les étapes suivantes :

- [Adhérer aux fonctionnalités d’évaluation](#opt-in-for-preview)
- [Installer le module complémentaire Azure Policy](#installation-steps)
- [Affecter une définition de stratégie pour AKS](#built-in-policies)
- [Attendre la validation](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>S’inscrire à la préversion

Avant d’installer le module Azure Policy ou d’activer toute fonctionnalité de service, votre abonnement doit activer le fournisseurs de ressources **Microsoft.ContainerService** et **Microsoft.PolicyInsights**, puis être approuvé pour adhérer à la préversion. Pour vous inscrire à la préversion, effectuez ces étapes dans le portail Azure ou avec Azure CLI :

- Portail Azure :

  1. Inscrivez les fournisseurs de ressources **Microsoft.ContainerService** et **Microsoft.PolicyInsights**. Pour connaître les étapes, consultez [Types et fournisseurs de ressources](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

  1. Lancez le service Azure Policy dans le portail Azure en cliquant sur **Tous les services**, puis en recherchant et en cliquant sur **Stratégie**.

     ![Rechercher Stratégie dans Tous les services](../media/rego-for-aks/search-policy.png)

  1. Sélectionnez **S’inscrire à la préversion** sur le côté gauche de la page Azure Policy.

     ![S’inscrire à la préversion Policy pour AKS](../media/rego-for-aks/join-aks-preview.png)

  1. Sélectionnez la ligne de l’abonnement que vous souhaitez ajouter à la préversion.

  1. Sélectionnez le bouton **Abonnement** en haut de la liste des abonnements.

- Azure CLI :

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataplaneAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.PolicyInsights/AKS-DataPlaneAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.PolicyInsights
  
  ```

## <a name="azure-policy-add-on"></a>Module complémentaire Azure Policy

Le _module complémentaire Azure Policy_ pour Kubernetes connecte le service Azure Policy au contrôleur d’admission Gatekeeper. Le module complémentaire, qui est installé dans l’espace de noms _azure-policy_, effectue les opérations suivantes :

- Il vérifie auprès d’Azure Policy quelles sont les affectations au cluster AKS
- Il télécharge et met en cache les détails de la stratégie, notamment la définition de stratégie _rego_, en tant que **configmaps**
- Il exécute une analyse complète afin de vérifier la conformité sur le cluster AKS
- Il fournit des rapports d’audit et de conformité à Azure Policy

### <a name="installing-the-add-on"></a>Installation du module complémentaire

#### <a name="prerequisites"></a>Conditions préalables requises

Avant d’installer le module complémentaire dans votre cluster AKS, vous devez installer l’extension de préversion. Vous pouvez effectuer cette étape avec Azure CLI :

1. Azure CLI 2.0.62 ou version ultérieure doit être installé et configuré. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

1. Le cluster AKS doit être de version _1.10_ ou ultérieure. Utilisez le script suivant pour valider la version de votre cluster AKS :

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Installez la version _0.4.0_ de l’extension de préversion Azure CLI pour AKS, `aks-preview` :

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Si vous avez installé précédemment l’extension _aks-preview_, installez toutes les mises à jour à l’aide de la commande `az extension update --name aks-preview`.

#### <a name="installation-steps"></a>Procédure d’installation :

Une fois les prérequis satisfaits, installez le module complémentaire Azure Policy dans le cluster AKS que vous souhaitez gérer.

- Portail Azure

  1. Lancez le service AKS dans le portail Azure en cliquant sur **Tous les services**, puis en recherchant et en cliquant sur **services Kubernetes**.

  1. Sélectionnez l’un de vos clusters AKS.

  1. Sélectionnez **Stratégies (préversion)** sur le côté gauche de la page du service Kubernetes.

     ![Stratégies du cluster AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Dans la page principale, sélectionnez le bouton **Activer un module complémentaire**.

     ![Activer le module complémentaire Azure Policy pour AKS](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Si le bouton **Activer un module complémentaire** est grisé, l’abonnement n’a pas encore été ajouté à la préversion. Pour connaître les étapes requises, consultez [S’inscrire à la préversion](#opt-in-for-preview).

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Fréquence de la validation et des rapports

Le module complémentaire contacte Azure Policy toutes les cinq minutes pour vérifier si des modifications ont été apportées aux affectations de stratégie. Pendant ce cycle d’actualisation, le module complémentaire supprime tous les _configmaps_ dans l’espace de noms _azure-policy_, puis recrée les _configmaps_ pour une utilisation par Gatekeeper.

> [!NOTE]
> Bien qu’un _administrateur de cluster_ puisse être autorisé à accéder à l’espace de noms _azure-policy_, les modifications de l’espace de noms ne sont ni recommandées ni prises en charge. Toute modification manuelle apportée est perdue lors du cycle d’actualisation.

Toutes les cinq minutes, le module complémentaire demande une analyse complète du cluster. Après la collecte des détails de l’analyse complète et les évaluations en temps réel faites par Gatekeeper des tentatives de modification du cluster, le module complémentaire renvoie les résultats à Azure Policy pour les inclure dans les [détails de conformité](../how-to/get-compliance-data.md) comme toute affectation Azure Policy. Seuls les résultats des affectations de stratégie actives sont renvoyés au cours du cycle d’audit.

## <a name="policy-language"></a>Langage de stratégie

La structure du langage Azure Policy pour la gestion d’AKS suit celle des stratégies existantes. L’effet _EnforceRegoPolicy_ est utilisé pour gérer vos clusters AKS et prend des propriétés _details_ propres à l’utilisation d’OPA et de Gatekeeper v2. Pour plus d’informations et pour obtenir des exemples, consultez l’effet [EnforceRegoPolicy](effects.md#enforceregopolicy).

Dans le cadre de la propriété _details.policy_ dans la définition de stratégie, Azure Policy transmet l’URI d’une stratégie rego au module complémentaire. Rego est le langage pris en charge par OPA et Gatekeeper pour valider ou muter une requête au cluster Kubernetes. Grâce à la prise en charge d’une norme existante pour la gestion de Kubernetes, Azure Policy permet de réutiliser des règles existantes et de les jumeler avec Azure Policy afin de bénéficier d’une expérience de rapports de conformité du cloud unifiée. Pour plus d’informations, consultez [What is Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="built-in-policies"></a>Stratégies prédéfinies

Pour rechercher les stratégies prédéfinies pour la gestion d’AKS à l’aide du portail Azure, effectuez les étapes suivantes :

1. Démarrez le service Azure Policy dans le portail Azure. Sélectionnez **Tous les services** dans le volet gauche, puis recherchez et sélectionnez **Stratégie**.

1. Dans le volet gauche de la page Azure Policy, sélectionnez **Définitions**.

1. Dans la zone de liste déroulante Catégorie, utilisez **Sélectionner tout** pour effacer le filtre, puis sélectionnez **service Kubernetes**.

1. Sélectionnez la définition de stratégie, puis sélectionnez le bouton **Affecter**.

> [!NOTE]
> Lors de l’affectation de la définition Azure Policy pour AKS, l’**Étendue** doit inclure la ressource de cluster AKS.

Vous pouvez également utiliser le guide de démarrage rapide [Attribuer une stratégie - Portail](../assign-policy-portal.md) pour rechercher et attribuer une stratégie AKS. Recherchez une définition de stratégie Kubernetes au lieu de l’exemple « audit vms ».

> [!IMPORTANT]
> Les stratégies intégrées de la catégorie **service Kubernetes** sont uniquement destinées à être utilisées avec AKS.

## <a name="logging"></a>Journalisation

### <a name="azure-policy-add-on-logs"></a>Journaux du module complémentaire Azure Policy

En tant que conteneur/contrôleur Kubernetes, le module complémentaire Azure Policy conserve des journaux dans le cluster AKS. Les journaux sont exposés dans la page **Insights** du cluster AKS. Pour plus d’informations, consultez [Présentation des performances de cluster AKS avec Azure Monitor pour conteneurs](../../../azure-monitor/insights/container-insights-analyze.md).

### <a name="gatekeeper-logs"></a>Journaux Gatekeeper

Pour activer les journaux Gatekeeper pour les nouvelles demandes de ressources, suivez les étapes indiquées dans l’article [Activer et consulter les journaux du nœud principal Kubernetes dans AKS](../../../aks/view-master-logs.md).
Voici un exemple de requête pour afficher les événements refusés sur les nouvelles demandes de ressources :

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

Pour afficher les journaux des conteneurs Gatekeeper, suivez les étapes indiquées dans l’article [Activer et consulter les journaux du nœud principal Kubernetes dans AKS](../../../aks/view-master-logs.md) et cochez l’option _kube-apiserver_ dans le volet **Paramètres de diagnostic**.

## <a name="remove-the-add-on"></a>Supprimer le module complémentaire

Pour supprimer le module complémentaire Azure Policy de votre cluster AKS, utilisez le Portail Azure ou Azure CLI :

- Portail Azure

  1. Lancez le service AKS dans le portail Azure en cliquant sur **Tous les services**, puis en recherchant et en cliquant sur **services Kubernetes**.

  1. Sélectionnez le cluster AKS dans lequel vous souhaitez désactiver le module complémentaire Azure Policy.

  1. Sélectionnez **Stratégies (préversion)** sur le côté gauche de la page du service Kubernetes.

     ![Stratégies du cluster AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Dans la page principale, sélectionnez le bouton **Désactiver un module complémentaire**.

     ![Désactiver le module complémentaire Azure Policy pour AKS](../media/rego-for-aks/disable-policy-add-on.png)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Données de diagnostic collectées par le module complémentaire Azure Policy

Le module complémentaire Azure Policy pour Kubernetes collecte une quantité limitée de données de diagnostics de cluster. Ces données de diagnostic sont des données techniques vitales concernant les logiciels et le niveau de performance. Elles sont utilisées de différentes manières :

- Tenir à jour le module complémentaire Azure Policy
- Maintenir la sécurité, la fiabilité et le niveau de performance du module complémentaire Azure Policy
- Améliorer le module complémentaire Azure Policy à travers l’analyse globale de son utilisation

Les informations collectées par le module complémentaire ne sont pas des données personnelles. Les détails suivants sont actuellement recueillis :

- Version de l’agent du module complémentaire Azure Policy
- Type de cluster
- Région du cluster
- Groupe de ressources de cluster
- ID de la ressource de cluster
- ID de l’abonnement du cluster
- Système d’exploitation du cluster (exemple : Linux)
- Ville du cluster (exemple : Seattle)
- État ou province du cluster (exemple : Washington)
- Pays ou région du cluster (exemple : États-Unis)
- Exceptions/erreurs rencontrées par le module complémentaire Azure Policy lors de l’installation de l’agent concernant l’évaluation de la stratégie
- Nombre de stratégies Gatekeeper non installées par le module complémentaire Azure Policy

## <a name="next-steps"></a>Étapes suivantes

- Consultez des exemples à la page [Exemples Azure Policy](../samples/index.md).
- Consultez la page [Structure de définition Azure Policy](definition-structure.md).
- Consultez la page [Compréhension des effets de Policy](effects.md).
- Découvrez comment [créer des stratégies par programmation](../how-to/programmatically-create.md).
- Découvrez comment [obtenir des données de conformité](../how-to/get-compliance-data.md).
- Découvrez comment [corriger des ressources non conformes](../how-to/remediate-resources.md).
- Pour en savoir plus sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](../../management-groups/overview.md).