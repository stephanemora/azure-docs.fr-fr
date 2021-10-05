---
title: Planifier le déploiement des services de données Azure Arc
description: Cet article explique les considérations relatives à la planification du déploiement des services de données Azure Arc
services: azure-arc
ms.service: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 0d9ae624ddc0a4e5a2f5d9ac38428f4f3c10a01e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124832398"
---
# <a name="plan-to-deploy-azure-arc-enabled-data-services"></a>Planifier le déploiement des services de données Azure Arc

Cet article explique comment planifier le déploiement des services de données compatibles Azure Arc.


Tout d’abord, le déploiement des services de données Azure Arc implique une bonne compréhension des charges de travail de base de données et des besoins de l’entreprise pour ces charges de travail. Par exemple, envisagez des choses telles que la disponibilité, la continuité des activités et les besoins en capacité pour la mémoire, le processeur et le stockage de ces charges de travail. Deuxièmement, l’infrastructure destinée à prendre en charge les charges de travail de base de données doit être préparée en fonction des besoins de l’entreprise.

## <a name="prerequisites"></a>Configuration requise

Avant de déployer les services de données Azure Arc, il est important de comprendre les conditions préalables et de disposer de toutes les informations nécessaires : l’environnement d’infrastructure configuré avec un niveau d’accès approprié, la capacité de stockage, le processeur et la mémoire. Cela vous permettra de réussir votre déploiement.

Consultez les sections suivantes :
- [Guide de dimensionnement](sizing-guidance.md)
- [Configuration du stockage](storage-configuration.md)
- [Modes et exigences de connectivité](connectivity.md)

Veillez à disposer des éléments suivants :
- Une [`arcdata`extension CLI](install-arcdata-extension.md) installée.
- Les autres [outils clients](install-client-tools.md) installés.
- Un accès au cluster Kubernetes.
- Votre fichier `kubeconfig` configuré. Il doit pointer vers le cluster Kubernetes sur lequel vous souhaitez effectuer le déploiement. Exécutez la commande suivante pour vérifier le contexte actuel de votre cluster sur lequel vous allez effectuer le déploiement :
   ```console
   kubectl cluster-info
   ```
- un abonnement Azure auquel seront projetées et facturées des ressources telles que le contrôleur de données Azure Arc, l’instance gérée SQL compatible Azure Arc ou un serveur hyperscale PostgreSQL Azure Arc.

Une fois l’infrastructure préparée, déployez les services de données Azure Arc de la façon suivante :
1. Créez un contrôleur de données compatible Azure Arc sur l’une des distributions validées d’un cluster Kubernetes
1. Créez une instance gérée SQL compatible Azure Arc et/ou un groupe de serveur Hyperscale PostgreSQL Azure Arc.

> [!CAUTION]
> Certains des niveaux et modes des services de données sont [généralement disponibles (GA)](release-notes.md), tandis que d’autres sont en préversion. Nous vous recommandons de ne pas mélanger les services GA et en préversion sur le même contrôleur de données. Si vous mélangez les services GA et en préversion sur le même contrôleur de données, vous ne pouvez pas opérer de mise à niveau sur place. Dans ce scénario, lorsque vous souhaitez opérer une mise à niveau, vous devez supprimer et recréer le contrôleur de données et les services de données.

## <a name="overview-create-the-azure-arc-enabled-data-controller"></a>Présentation : Créer le contrôleur de données compatible Azure Arc

Vous pouvez créer des services de données compatibles Azure Arc sur différents types de clusters Kubernetes et de services Kubernetes managés par le biais d'approches différentes.

Actuellement, la liste des services Kubernetes et distributions validée est la suivante :


- AWS Elastic Kubernetes Service (EKS)
- Azure Kubernetes Service (AKS)
- Service Azure Kubernetes sur Azure Stack HCI
- Azure RedHat OpenShift (ARO)
- Moteur Kubernetes Google Cloud (GKE)
- Kubernetes open source en amont, généralement déployé à l’aide de kubeadm
- OpenShift Container Platform (OCP)

> [!IMPORTANT]
> * La version minimale prise en charge de Kubernetes est la version v1.19. Consultez [Problèmes connus](./release-notes.md#known-issues) pour plus d’informations.
> * La version minimale prise en charge d’OCP est 4.7.
> * si vous utilisez Azure Container Service, la taille de machine virtuelle du nœud Worker de votre cluster doit être au moins **Standard_D8s_v3** et utiliser des **disques Premium**. 
> * Le cluster ne doit pas s’étendre sur plusieurs zones de disponibilité. 
> * Consultez [Problèmes connus](./release-notes.md#known-issues) pour plus d’informations.

Quelle que soit l'option choisie, pendant le processus de création, vous devez fournir les informations suivantes :

- **Nom du contrôleur de données** : nom descriptif de votre contrôleur de données, par exemple, « production-dc », « seattle-dc ». Le nom doit respecter les [normes de dénomination de Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/).
- **nom d’utilisateur** : nom d’utilisateur de l’administrateur de Kibana/Grafana.
- **mot de passe** : mot de passe de l’administrateur de Kibana/Grafana.
- **Nom de votre espace de noms Kubernetes** : nom de l'espace de noms Kubernetes dans lequel vous souhaitez créer le contrôleur de données.
- **Mode de connectivité** : le mode de connectivité détermine le degré de connectivité de votre environnement de services de données Azure Arc vers Azure. Le mode de connexion indirecte est généralement disponible. Le mode de connexion directe est en préversion.  Le choix du mode de connectivité détermine les options des méthodes de déploiement.  Pour plus d’informations, consultez [mode de connectivité](./connectivity.md).
- **ID d'abonnement Azure** : GUID de l'abonnement Azure dans lequel vous souhaitez que soit créée la ressource de contrôleur de données dans Azure.  Toutes les SQL Managed Instances compatibles Azure SQL et les groupes de serveurs Hyperscale PostgreSQL seront également créés dans cet abonnement et facturés à cet abonnement.
- **Nom du groupe de ressources Azure** : nom du groupe de ressources dans lequel vous souhaitez que soit créée la ressource de contrôleur de données dans Azure.  Toutes les SQL Managed Instances compatibles Azure Arc et les groupes de serveurs Hyperscale PostgreSQL seront également créés dans ce groupe de ressources.
- **Emplacement Azure** : emplacement Azure où les métadonnées de la ressource de contrôleur de données seront stockées. Pour obtenir la liste des régions disponibles, consultez [Infrastructure globale Azure / Produits par région](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc). Les métadonnées et les informations de facturation relatives aux ressources Azure gérées par le contrôleur de données que vous déployez sont stockées uniquement à l’emplacement dans Azure que vous spécifiez comme paramètre d’emplacement. Si vous déployez en mode connecté directement, le paramètre d’emplacement du contrôleur de données sera le même que l’emplacement de la ressource d’emplacement personnalisée que vous ciblez.
- **Informations du principal de service** : comme décrit dans l’article [Prérequis pour le chargement](upload-metrics-and-logs-to-azure-monitor.md), vous aurez besoin des informations du principal de service pendant la création du contrôleur de données Azure Arc lors du déploiement en mode de connectivité *directe*. Pour le mode de connectivité *indirecte*, le principal du service est toujours nécessaire pour l’exportation et le chargement manuels, mais après la création du contrôleur de données Azure Arc.
- **Infrastructure** : à des fins de facturation, il est nécessaire d’indiquer l’infrastructure sur laquelle vous exécutez les services de données avec Azure Arc.  Les options sont : `alibaba`, `aws`, `azure`, `gcp`, `onpremises` ou `other`.

## <a name="additional-concepts-for-direct-connected-mode"></a>Concepts supplémentaires pour le mode connecté direct

Comme décrit dans les [modes de connectivité](./connectivity.md), le contrôleur de données Azure Arc peut être déployé en modes de connectivité **directe** ou **indirecte**. Le déploiement des services de données Azure Arc en mode de connexion **directe** demande une bonne compréhension de certains concepts et considérations supplémentaires.
Tout d’abord, le cluster Kubernetes dans lequel les services de données avec Azure Arc seront déployés doit être un [cluster Kubernetes avec Azure Arc](../kubernetes/overview.md). L’intégration du cluster Kubernetes à Azure Arc fournit une connectivité Azure qui est exploitée pour des fonctionnalités telles que le chargement automatique des informations d’utilisation, les journaux, les mesures, etc. La connexion de votre cluster Kubernetes à Azure vous permet également de déployer et de gérer les services de données Azure Arc sur votre cluster directement à partir du portail Azure.

La connexion de votre cluster Kubernetes à Azure implique les étapes suivantes :
- [Connecter votre cluster à Azure](../kubernetes/quickstart-connect-cluster.md)

Ensuite, une fois que le cluster Kubernetes est intégré à Azure Arc, le déploiement des services de données compatibles Azure Arc sur un cluster Kubernetes compatible Azure Arc implique les opérations suivantes :
- Créer l’extension de services de données Arc, en savoir plus sur les [extensions de cluster](../kubernetes/conceptual-extensions.md)
- Créer un emplacement personnalisé, en savoir plus sur les [emplacements personnalisés](../kubernetes/conceptual-custom-locations.md)
- Créer le contrôleur de données Azure Arc

Ces trois étapes peuvent être exécutées en une seule étape à l’aide de l’Assistant Création de contrôleur de données Azure Arc dans le portail Azure.

Une fois le contrôleur de données Azure Arc installé, il est possible de créer des services de données tels qu’une SQL Managed Instance compatible Azure Arc ou un groupe de serveurs Hyperscale PostgreSQL.


## <a name="next-steps"></a>Étapes suivantes

Plusieurs options sont disponibles pour créer le contrôleur de données Azure Arc :

> **Vous voulez juste l’essayer ?**
> Démarrez rapidement avec [Démarrage rapide d'Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) sur Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou sur une machine virtuelle Azure.
>
- [Créez un contrôleur de données en mode de connexion directe avec le portail Azure](create-data-controller-direct-prerequisites.md)
- [Créez un contrôleur de données en mode de connexion indirecte avec l’interface CLI](create-data-controller-indirect-cli.md)
- [Créez un contrôleur de données en mode de connexion indirecte avec Azure Data Studio](create-data-controller-indirect-azure-data-studio.md)
- [Créez un contrôleur de données en mode de connexion indirecte à partir du portail Azure via un notebook Jupyter dans Azure Data Studio](create-data-controller-indirect-azure-portal.md)
- [Créez un contrôleur de données en mode de connexion indirecte avec des outils Kubernetes tels que kubectl ou oc](create-data-controller-using-kubernetes-native-tools.md)
