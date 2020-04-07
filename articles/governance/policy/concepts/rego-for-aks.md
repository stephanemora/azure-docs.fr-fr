---
title: Découvrir Azure Policy pour Azure Kubernetes Service
description: Découvrez comment Azure Policy utilise Rego et Open Policy Agent pour gérer les clusters sur Azure Kubernetes Service.
ms.date: 03/27/2020
ms.topic: conceptual
ms.openlocfilehash: d77c5cf94a8239f4617e563961cbe1cc40e48fe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372656"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Comprendre Azure Policy pour Azure Kubernetes Service

Azure Policy s’intègre à [Azure Kubernetes Service](../../../aks/intro-kubernetes.md) (AKS) afin d’appliquer à grande échelle des mesures et des protections sur vos clusters d’une manière centralisée et cohérente.
En étendant l’utilisation de [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3, un _webhook de contrôleur d’admission_ pour [Open Policy Agent](https://www.openpolicyagent.org/) (OPA), Azure Policy vous permet de gérer l’état de conformité de vos ressources Azure et clusters AKS et de créer des rapports à partir d’un seul endroit.

> [!IMPORTANT]
> Azure Policy pour AKS est en préversion et prend uniquement en charge les définitions de stratégie intégrées. Les stratégies intégrées se trouvent dans la catégorie **Kubernetes**. L’effet **EnforceRegoPolicy** et les stratégies de la catégorie **Kubernetes Service** associées sont _déconseillés_. Vous pouvez utiliser l’effet [EnforceOPAConstraint](./effects.md#enforceopaconstraint) mis à jour à la place.

> [!WARNING]
> Cette fonctionnalité n’est pas encore disponible dans toutes les régions. Pour connaître l’état du déploiement, consultez [Problèmes liés à AKS - Changement cassant pour le module Azure Policy](https://github.com/Azure/AKS/issues/1529).

## <a name="overview"></a>Vue d’ensemble

Pour activer et utiliser Azure Policy pour AKS avec votre cluster AKS, effectuez les étapes suivantes :

- [Adhérer aux fonctionnalités d’évaluation](#opt-in-for-preview)
- [Installer le module complémentaire Azure Policy](#installation-steps)
- [Affecter une définition de stratégie pour AKS](#built-in-policies)
- [Attendre la validation](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>S’inscrire à la préversion

Avant d’installer le module Azure Policy ou d’activer toute fonctionnalité de service, votre abonnement doit activer les fournisseurs de ressources **Microsoft.ContainerService** et **Microsoft.PolicyInsights**, puis être approuvé pour adhérer à la préversion. Pour vous inscrire à la préversion, effectuez ces étapes dans le portail Azure ou avec Azure CLI :

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

  # Provider register: Register the Azure Kubernetes Service provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  ```

## <a name="azure-policy-add-on"></a>Module complémentaire Azure Policy

Le _module complémentaire Azure Policy_ pour Kubernetes connecte le service Azure Policy au contrôleur d’admission Gatekeeper. Ce module, installé dans l’espace de noms _kube-system_, exerce les fonctions suivantes :

- Il vérifie auprès du service Azure Policy quelles sont les affectations au cluster.
- Il déploie les stratégies dans le cluster en tant que ressources personnalisées [modèle de contrainte](https://github.com/open-policy-agent/gatekeeper#constraint-templates) et [contrainte](https://github.com/open-policy-agent/gatekeeper#constraints).
- Il fournit des rapports d’audit et de conformité au service Azure Policy.

### <a name="installing-the-add-on"></a>Installation du module complémentaire

#### <a name="prerequisites"></a>Prérequis

Avant d’installer le module complémentaire dans votre cluster AKS, vous devez installer l’extension de préversion. Vous pouvez effectuer cette étape avec Azure CLI :

1. Si des stratégies Gatekeeper v2 étaient installées, supprimez le module à l’aide du bouton **Désactiver** sur votre cluster AKS dans la page **Stratégies (préversion)** .

1. Azure CLI 2.0.62 ou version ultérieure doit être installé et configuré. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

1. Le cluster AKS doit être de version _1.14_ ou ultérieure. Utilisez le script suivant pour valider la version de votre cluster AKS :

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
     > Si le bouton **Activer un module complémentaire** est grisé, l’abonnement n’a pas encore été ajouté à la préversion. Pour connaître les étapes requises, consultez [S’inscrire à la préversion](#opt-in-for-preview). Si un bouton **Désactiver** est disponible, Gatekeeper v2 est toujours installé et doit être supprimé.

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Fréquence de la validation et des rapports

Le module complémentaire contacte le service Azure Policy toutes les quinze minutes pour vérifier si des modifications ont été apportées aux affectations de stratégie.
Pendant ce cycle d’actualisation, le module complémentaire recherche d’éventuelles modifications, qui déclenchent la création, la mise à jour ou la suppression des modèles de contrainte et des contraintes.

> [!NOTE]
> Un administrateur de cluster peut être autorisé à créer et mettre à jour des modèles de contrainte et des ressources de contrainte, mais ces scénarios ne sont pas pris en charge, car les mises à jour manuelles seront remplacées.

Toutes les quinze minutes, le module complémentaire demande une analyse complète du cluster. Après la collecte des détails de l’analyse complète et les évaluations en temps réel faites par Gatekeeper des tentatives de modification du cluster, le module complémentaire renvoie les résultats au service Azure Policy pour les inclure dans les [détails de conformité](../how-to/get-compliance-data.md#portal) comme toute affectation Azure Policy. Seuls les résultats des affectations de stratégie actives sont renvoyés au cours du cycle d’audit. Les résultats d’audit peuvent également être considérés comme des [violations](https://github.com/open-policy-agent/gatekeeper#audit) indiquées dans le champ d’état de la contrainte non réussie.

## <a name="policy-language"></a>Langage de stratégie

La structure du langage Azure Policy pour la gestion de Kubernetes suit celle des stratégies existantes. L’effet _EnforceOPAConstraint_, qui permet de gérer les clusters Kubernetes, prend des propriétés « details » propres au [Framework de contraintes d’OPA](https://github.com/open-policy-agent/frameworks/tree/master/constraint) et à Gatekeeper v3. Pour plus d’informations et pour obtenir des exemples, voir l’effet [EnforceOPAConstraint](./effects.md#enforceopaconstraint).
  
Au sein des propriétés _details.constraintTemplate_ et _details.constraint_ de la définition de stratégie, Azure Policy transmet les URI de ces [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) au module complémentaire. Rego est le langage pris en charge par OPA et Gatekeeper pour valider une requête au cluster Kubernetes. Grâce à la prise en charge d’une norme existante pour la gestion de Kubernetes, Azure Policy permet de réutiliser des règles existantes et de les jumeler avec Azure Policy afin de bénéficier d’une expérience de rapports de conformité du cloud unifiée. Pour plus d’informations, consultez [What is Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="built-in-policies"></a>Stratégies prédéfinies

Pour rechercher les stratégies prédéfinies permettant de gérer un cluster avec le Portail Azure, suivez les étapes ci-dessous :

1. Démarrez le service Azure Policy dans le portail Azure. Sélectionnez Tous les services dans le volet gauche, puis recherchez et sélectionnez **Stratégie**.

1. Dans le volet gauche de la page Azure Policy, sélectionnez **Définitions**.

1. Dans la zone de liste déroulante Catégorie, utilisez Sélectionner tout pour effacer le filtre, puis sélectionnez **Kubernetes**.

1. Sélectionnez la définition de stratégie, puis sélectionnez le bouton **Affecter**.

1. Définissez **Étendue** sur le groupe de gestion, l’abonnement ou le groupe de ressources du cluster Kubernetes auquel s’appliquera l’affectation de stratégie.

   > [!NOTE]
   > Lors de l’affectation de la définition Azure Policy pour AKS, l’**Étendue** doit inclure la ressource de cluster AKS.

1. Donnez un **Nom** et une **Description** à l’affectation de stratégie, qui permettront de l’identifier facilement.

1. Définissez l’[application de stratégie](./assignment-structure.md#enforcement-mode) sur l’une des valeurs ci-dessous.

   - **Activé** - Appliquer la stratégie sur le cluster. Les demandes d’admission Kubernetes avec des violations sont refusées.
   
   - **Désactivé** - Ne pas appliquer la stratégie sur le cluster. Les demandes d’admission Kubernetes avec des violations ne sont pas refusées. Les résultats de l’évaluation de la conformité sont toujours disponibles. Lors du déploiement de nouvelles stratégies sur les clusters en cours d’exécution, l’option _Désactivé_ est utile pour tester les stratégies, étant donné que les demandes d’admission avec des violations ne sont pas refusées.

1. Sélectionnez **Suivant**.

1. Définir les **valeurs de paramètres**
   
   - Pour exclure les espaces de noms Kubernetes de l’évaluation de stratégie, répertoriez les espaces de noms dans le paramètre **Exclusions d’espaces de noms**. Il est recommandé d’exclure : _kube-system_

1. Sélectionnez **Revoir + créer**.

Vous pouvez également utiliser le guide de démarrage rapide [Attribuer une stratégie - Portail](../assign-policy-portal.md) pour rechercher et attribuer une stratégie AKS. Recherchez une définition de stratégie Kubernetes au lieu de l’exemple « audit vms ».

> [!IMPORTANT]
> Les stratégies intégrées de la catégorie **Kubernetes** sont destinées à être utilisées exclusivement avec AKS. Pour obtenir la liste des stratégies intégrées, consultez [Exemples Kubernetes](../samples/built-in-policies.md#kubernetes).

## <a name="logging"></a>Journalisation

### <a name="azure-policy-add-on-logs"></a>Journaux du module complémentaire Azure Policy

En tant que conteneur/contrôleur Kubernetes, le module complémentaire Azure Policy et Gatekeeper conservent tous les deux des journaux dans le cluster AKS. Les journaux sont exposés dans la page **Insights** du cluster AKS. Pour plus d’informations, consultez [Présentation des performances de cluster AKS avec Azure Monitor pour conteneurs](../../../azure-monitor/insights/container-insights-analyze.md).

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
