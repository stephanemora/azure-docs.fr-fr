---
title: Découvrir Azure Policy pour AKS Engine
description: Découvrez comment Azure Policy utilise CustomResourceDefinitions et Open Policy Agent à partir de Gatekeeper v3 pour gérer les clusters avec AKS Engine.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: c41a9d84dfe43e356e9a4a17af523a37209c2933
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436421"
---
# <a name="understand-azure-policy-for-aks-engine"></a>Comprendre Azure Policy pour AKS Engine

Azure Policy s’intègre à [AKS Engine](https://github.com/Azure/aks-engine/blob/master/docs/README.md), un système qui offre des outils pratiques permettant de démarrer rapidement un cluster Kubernetes autogéré sur Azure. Cette intégration permet une mise en œuvre et une protection centralisées et cohérentes à grande échelle sur des clusters autogérés AKS Engine. En étendant l’utilisation [d’Open Policy Agent](https://www.openpolicyagent.org/) (OPA) [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3 (bêta), un _webhook contrôleur d’admission_ pour Kubernetes, Azure Policy vous permet de gérer et de signaler l’état de conformité de vos ressources Azure et clusters autogérés AKS Engine en un seul endroit.

> [!NOTE]
> Azure Policy pour AKS Engine est en préversion publique et n’a pas de contrat SLA. Gatekeeper v3 est en version bêta et est pris en charge par la communauté open source. Le service ne gère que les définitions de stratégies intégrées et un seul cluster AKS Engine par groupe de ressources configuré avec un principal de service.

> [!IMPORTANT]
> Pour obtenir un support sur Azure Policy pour AKS Engine, AKS Engine ou Gatekeeper v3, créez un [nouveau problème](https://github.com/Azure/aks-engine/issues/new/choose) dans le référentiel GitHub AKS Engine.

## <a name="overview"></a>Vue d’ensemble

Pour activer et utiliser Azure Policy pour AKS Engine avec votre cluster Kubernetes autogéré sur Azure, effectuez les actions suivantes :

- [Composants requis](#prerequisites)
- [Installer le module complémentaire Azure Policy](#installing-the-add-on)
- [Affecter une définition de stratégie pour AKS Engine](#built-in-policies)
- [Attendre la validation](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>Conditions préalables requises

Avant d’installer le module complémentaire Azure Policy ou d’activer des fonctionnalités du service, vous devez activer le fournisseur de ressources **Microsoft.PolicyInsights** et créer une attribution de rôle pour le principal de service du cluster dans votre abonnement. 

1. Pour activer le fournisseur de ressources, suivez les étapes décrites dans [Fournisseurs de ressources et types](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) ou exécutez la commande Azure CLI ou Azure PowerShell :

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell
     
     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell
   
     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell
   
     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Créer une attribution de rôle pour le principal de service du cluster

   - Si vous ne connaissez pas l’ID de l’application du principal de service du cluster, cherchez-le avec la commande suivante.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Affectez l’attribution de rôle « Policy Insights Data Writer (Preview) » à l’ID de l’application du principal de service du cluster (valeur _aadClientID_ de l’étape précédente) avec Azure CLI. Remplacez `<subscriptionId>` par votre ID d’abonnement et `<aks engine cluster resource group>` par le groupe de ressources dans lequel se trouve le cluster Kubernetes autogéré AKS Engine.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Module complémentaire Azure Policy

Le _module complémentaire Azure Policy_ pour Kubernetes connecte le service Azure Policy au contrôleur d’admission Gatekeeper. Ce module, installé dans l’espace de noms _kube-system_, exerce les fonctions suivantes :

- Il vérifie auprès d’Azure Policy quelles sont les affectations au cluster AKS Engine
- Il télécharge et installe les détails de la stratégie, les modèles de contrainte et les contraintes
- Il exécute une analyse complète afin de vérifier la conformité sur le cluster AKS Engine
- Il fournit des rapports d’audit et de conformité à Azure Policy

### <a name="installing-the-add-on"></a>Installation du module complémentaire

Une fois les prérequis remplis, le module complémentaire Azure Policy peut être installé. L’installation peut être effectuée pendant le cycle de création ou de mise à jour d’un moteur AKS Engine ou comme action indépendante sur un cluster existant.

- Effectuer l’installation pendant le cycle de création ou de mise à jour

  Pour activer le module complémentaire Azure Policy lors de la création d’un cluster autogéré ou comme mise à jour d’un cluster existant, ajoutez la définition du cluster de propriétés **addons** pour AKS Engine.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true,
      "config": {
          "auditInterval": "30",
          "constraintViolationsLimit": "20"
      }
  }]
  ```

  Pour plus d’informations à ce sujet, voir le guide externe [Définition du cluster AKS Engine](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Installer un cluster existant avec Helm Charts

  Suivez les étapes ci-dessous pour préparer le cluster et installer le module complémentaire :

  1. Installez Gatekeeper sur l’espace de noms _gatekeeper-system_.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. Ajoutez l’étiquette _control-plane_ à _kube-system_. Elle exclut l’audit des pods et services _Kube-System_ par Gatekeeper et le module complémentaire Azure Policy.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Synchronisez les données Kubernetes (espace de noms, pod, entrée, service) avec OPA.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     Pour plus d’informations, voir [OPA – Répliquer des données](https://github.com/open-policy-agent/gatekeeper#replicating-data).

  1. Ajoutez le référentiel Azure Policy à Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Pour plus d’informations, voir [Chart Helm – Guide de démarrage rapide](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Installez le module complémentaire avec un chart Helm. Remplacez `<subscriptionId>` par votre ID d’abonnement et `<aks engine cluster resource group>` par le groupe de ressources dans lequel se trouve le cluster Kubernetes autogéré AKS Engine.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Pour plus d’informations sur les composants installés par le module complémentaire Helm Chart, voir la [Définition du module complémentaire Helm Chart Azure Policy](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) sur GitHub.

     > [!NOTE]
     > En raison de la relation entre le module complémentaire Azure Policy et l’ID du groupe de ressources, Azure Policy ne prend en charge qu’un seul cluster AKS Engine par groupe de ressources.

Pour vérifier que l’installation du module complémentaire a réussi et que le pod _azure-policy_ est en cours d’exécution, exécutez la commande suivante :

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>Fréquence de la validation et des rapports

Le module complémentaire contacte Azure Policy toutes les cinq minutes pour vérifier si des modifications ont été apportées aux affectations de stratégie. Pendant ce cycle d’actualisation, le module complémentaire recherche d’éventuelles modifications, qui déclenchent la création, la mise à jour ou la suppression des modèles de contrainte et des contraintes.

> [!NOTE]
> Si un _administrateur de cluster_ peut avoir l’autorisation d’apporter des modifications aux modèles de contrainte et aux contraintes, il n’est en revanche pas recommandé ni possible d’apporter des modifications aux modèles de contrainte ou aux contraintes créés par Azure Policy. Toute modification manuelle apportée est perdue lors du cycle d’actualisation.

Toutes les cinq minutes, le module complémentaire demande une analyse complète du cluster. Après la collecte des détails de l’analyse complète et les évaluations en temps réel faites par Gatekeeper des tentatives de modification du cluster, le module complémentaire renvoie les résultats à Azure Policy pour les inclure dans les [détails de conformité](../how-to/get-compliance-data.md) comme toute affectation Azure Policy. Seuls les résultats des affectations de stratégie actives sont renvoyés au cours du cycle d’audit. Les résultats d’audit peuvent également être considérés comme des violations indiquées dans le champ d’état de la contrainte non réussie.

## <a name="policy-language"></a>Langage de stratégie

La structure du langage Azure Policy pour la gestion d’AKS Engine suit celle des stratégies existantes. L’effet _EnforceOPAConstraint_, qui permet de gérer les clusters AKS Engine, prend des propriétés _details_ propres au [Framework de contraintes d’OPA](https://github.com/open-policy-agent/frameworks/tree/master/constraint) et à Gatekeeper v3. Pour plus d’informations et pour obtenir des exemples, voir l’effet [EnforceOPAConstraint](effects.md#enforceopaconstraint).

Au sein des propriétés _details.constraintTemplate_ et _details.constraint_ de la définition de stratégie, Azure Policy transmet les URI de ces [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) au module complémentaire. Rego est le langage pris en charge par OPA et Gatekeeper pour valider une requête au cluster Kubernetes. Grâce à la prise en charge d’une norme existante pour la gestion de Kubernetes, Azure Policy permet de réutiliser des règles existantes et de les jumeler avec Azure Policy afin de bénéficier d’une expérience de rapports de conformité du cloud unifiée. Pour plus d’informations, consultez [What is Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Stratégies prédéfinies

Pour rechercher les stratégies prédéfinies permettant de gérer un cluster AKS Engine avec le Portail Azure, suivez les étapes ci-dessous :

1. Démarrez le service Azure Policy dans le portail Azure. Sélectionnez **Tous les services** dans le volet gauche, puis recherchez et sélectionnez **Stratégie**.

1. Dans le volet gauche de la page Azure Policy, sélectionnez **Définitions**.

1. Dans la zone de liste déroulante Catégorie, utilisez **Sélectionner tout** pour effacer le filtre, puis sélectionnez **Kubernetes**.

1. Sélectionnez la définition de stratégie, puis sélectionnez le bouton **Affecter**.

> [!NOTE]
> Dans le cadre de l’affectation de la définition Azure Policy pour AKS Engine, **l’Étendue** doit être le groupe de ressources du cluster AKS Engine.

Vous pouvez également utiliser le démarrage rapide [Affecter une stratégie – Portail](../assign-policy-portal.md) pour trouver et attribuer une stratégie AKS Engine. Recherchez une définition de stratégie AKS Engine au lieu de l’exemple « audit vms ».

> [!IMPORTANT]
> Les stratégies intégrées de la catégorie **Kubernetes** sont destinées à être utilisées exclusivement avec AKS Engine.

## <a name="logging"></a>Journalisation

### <a name="azure-policy-add-on-logs"></a>Journaux du module complémentaire Azure Policy

Le module complémentaire Azure Policy étant un contrôleur/conteneur Kubernetes, il conserve les journaux dans le cluster AKS Engine.

Pour consulter les journaux du module complémentaire Azure Policy, utilisez `kubectl` :

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>Journaux Gatekeeper

Le pod Gatekeeper, _gatekeeper-controller-manager-0_, se trouve généralement dans l’espace de noms `gatekeeper-system` ou `kube-system`, mais il peut apparaître dans un autre espace de noms selon la façon dont il est déployé.

Pour afficher les journaux Gatekeeper, utilisez `kubectl` :

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

Pour plus d’informations, voir [Déboguer Gatekeeper](https://github.com/open-policy-agent/gatekeeper#debugging) dans la documentation OPA.

## <a name="remove-the-add-on"></a>Supprimer le module complémentaire

Pour supprimer le module complémentaire Azure Policy et Gatekeeper de votre cluster AKS Engine, utilisez la méthode correspondant à la façon dont le module a été installé :

- S’il a été installé en définissant la propriété **addons** dans la définition du cluster pour AKS Engine :

  Redéployez la définition du cluster sur AKS Engine après avoir modifié la propriété **addons** pour _azure-policy_ en lui donnant la valeur false :


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- S’il a été installé avec Helm Charts :

  1. Supprimez les anciennes contraintes.

     Actuellement, le mécanisme de désinstallation supprime uniquement le système Gatekeeper, et non les ressources _ConstraintTemplate_, _Constraint_et _Config_ créées par l’utilisateur, ni les _CRD_ qui les accompagnent.

     Lorsque Gatekeeper est en cours d’exécution, il est possible de supprimer les contraintes indésirables :

     - Supprimez toutes les instances de la ressource de contrainte.
     - Supprimez la ressource _ConstraintTemplate_, ce qui devrait nettoyer automatiquement le _CRD_.
     - Le fait de supprimer la ressource _Config_ a pour effet de supprimer les finaliseurs sur les ressources synchronisées.

  1. Désinstaller le module complémentaire Azure Policy
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. Désinstaller Gatekeeper
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
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