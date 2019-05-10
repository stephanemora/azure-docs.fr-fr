---
title: Stratégies de support pour Azure Kubernetes Service (AKS)
description: En savoir plus sur les stratégies de prise en charge Azure Kubernetes Service (AKS), une responsabilité partagée et les fonctionnalités en version préliminaire (ou alpha ou beta).
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: 9b779021eca11638e8ee52ec11d082e5b0e89cd4
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506685"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Stratégies de prise en charge pour Azure Kubernetes Service

Cet article fournit des détails sur les stratégies de support technique et des limitations pour Azure Kubernetes Service (AKS). L’article détaille également la gestion des nœuds de travail, composants de plan de contrôle géré, des composants tiers open source et gestion de sécurité ou correctifs.

## <a name="service-updates-and-releases"></a>Versions et mises à jour de service

* Pour plus d’informations de version, consultez [AKS notes de publication](https://github.com/Azure/AKS/releases).
* Pour plus d’informations sur les fonctionnalités en version préliminaire, consultez [AKS afficher un aperçu des fonctionnalités et des projets connexes](https://github.com/Azure/AKS/blob/master/previews.md).

## <a name="managed-features-in-aks"></a>Gérés des fonctionnalités dans ACS

Infrastructure de base comme un service (IaaS) cloud des composants, tels que compute ou des composants réseau, donner aux utilisateurs l’accès aux contrôles de bas niveau et des options de personnalisation. En revanche, AKS fournit un déploiement clé en main de Kubernetes qui fournit aux clients l’ensemble commun de configurations et les capacités que dont ils ont besoin. Les clients d’ACS ont limité personnalisation, de déploiement et d’autres options. Ces clients ne doivent s’inquiéter ou gérer des clusters Kubernetes directement.

Avec AKS, le client obtient une solution entièrement gérée *plan de contrôle*. Le plan de contrôle contient tous les composants et services que le client a besoin pour faire fonctionner et fournir des clusters Kubernetes aux utilisateurs finaux. Tous les composants Kubernetes sont conservées et gérés par Microsoft.

Microsoft gère et surveille les composants suivants via le panneau de contrôle :

* Serveurs Kubelet ou API Kubernetes
* ETCD ou un magasin clé-valeur compatible, en fournissant une qualité de Service (QoS), d’évolutivité et de runtime
* Services DNS (par exemple, kube-dns ou CoreDNS)
* Proxy de Kubernetes ou de mise en réseau

ACS n’est pas une solution de cluster entièrement géré. Certains composants, tels que des nœuds de travail ont *la responsabilité partagée*, où les utilisateurs doivent vous aider à gérer le cluster AKS. L’entrée d’utilisateur est nécessaire, par exemple, pour appliquer un correctif de sécurité du système d’exploitation (se) de nœud worker.

Les services sont *gérés* en ce sens que Microsoft et l’équipe AKS déploie, fonctionne et est responsables de la disponibilité du service et de fonctionnalités. Les clients ne peuvent pas modifier ces composants managés. Microsoft limite personnalisation afin de garantir une expérience utilisateur cohérente et évolutive. Pour une solution entièrement personnalisable, consultez [AKS moteur](https://github.com/Azure/aks-engine).

> [!NOTE]
> Nœuds de travail ACS apparaissent dans le portail Azure en tant que ressources Azure IaaS standard. Mais ces machines virtuelles sont déployées dans un groupe de ressources Azure personnalisée (préfixe MC\\*). Il est possible de modifier des nœuds de travail AKS. Par exemple, vous pouvez utiliser Secure Shell (SSH) pour modifier les nœuds de travail AKS la façon de vous faire basculer les machines virtuelles normales (Toutefois, vous ne pouvez pas modifier l’image du système d’exploitation de base, et les modifications ne peuvent pas persister d’une mise à jour ou redémarrer), et vous pouvez attacher des autres ressources Azure à AKS nœuds de travail. Mais lorsque vous apportez des modifications *gestion hors bande et personnalisation,* le cluster AKS peut devenir difficile. Évitez de modifier des nœuds de travail, sauf si le Support Microsoft vous indique comment apporter des modifications.

## <a name="shared-responsibility"></a>Responsabilité partagée

Lors de la création d’un cluster, le client définit les nœuds de travail de Kubernetes AKS crée. Charges de travail client sont exécutées sur ces nœuds. Les clients possèdent et peuvent afficher ou modifier les nœuds de travail.

Étant donné que les nœuds de cluster de client d’exécuter du code privé et stockent des données sensibles, le Support Microsoft peut y accéder de manière limitée uniquement. Support Microsoft ne peut pas se connecter, exécuter des commandes dans ou afficher les journaux pour ces nœuds sans autorisation expresse de client ou de l’assistance.

Étant donné que les nœuds de travail sont sensibles, Microsoft prend la prudence pour limiter leur gestion en arrière-plan. Dans de nombreux cas, votre charge de travail continue de s’exécuter même si les Kubernetes maître nœuds, etcd et autres échouer les composants gérés par Microsoft. Nœuds de travail négligemment modifié peuvent entraîner des pertes de données et charges de travail et peuvent rendre difficile le cluster.

## <a name="aks-support-coverage"></a>AKS prend en charge de la couverture

Microsoft fournit un support technique pour les éléments suivants :

* Connectivité à tous les composants de Kubernetes qui fournit le service Kubernetes et qui prend en charge, tels que le serveur d’API.
* Gestion, disponibilité, QoS et les opérations de Kubernetes contrôlent des services de plan (nœuds maîtres de Kubernetes, un serveur API, etcd et kube-dns, par exemple).
* ETCD. Prise en charge inclut des sauvegardes automatisées, transparents de toutes les données etcd toutes les 30 minutes pour la restauration d’état cluster et de planification d’urgence. Ces sauvegardes ne sont pas directement disponibles aux clients ou utilisateurs. Elles garantissent la cohérence et la fiabilité des données.
* Les points d’intégration dans le pilote du fournisseur de cloud Azure pour Kubernetes. Ceux-ci incluent des intégrations à d’autres services Azure tels que les équilibreurs de charge, les volumes persistants ou mise en réseau (Kubernetes et Azure CNI).
* Questions ou des problèmes sur la personnalisation du contrôle du plan de composants tels que le serveur d’API Kubernetes, etcd et kube-dns.
* Remarque concernant la mise en réseau, tels que Azure CNI, kubenet, ou autres accès réseau et les fonctionnalités de problèmes. Problèmes peut inclure DNS résolution, perte de paquets, routage et ainsi de suite. Microsoft prend en charge différents scénarios de mise en réseau :
  * Kubenet (basic) et avancées de mise en réseau (Azure CNI) au sein du cluster et composants associés
  * Connectivité à d’autres services et applications Azure
  * Les contrôleurs d’entrée et les configurations d’équilibrage entrée ou la charge
  * Performances du réseau et la latence

Microsoft ne fournit pas un support technique pour les éléments suivants :

* Questions sur l’utilisation de Kubernetes. Par exemple, le Support Microsoft ne fournit pas des conseils sur la façon de créer les contrôleurs d’entrée personnalisé, utilisez des charges de travail ou appliquer des packages de logiciels tiers ou open source ou d’outils.
  > [!NOTE]
  > Support Microsoft peut vous conseiller sur la fonctionnalité de cluster AKS, personnalisation et paramétrage (par exemple, les problèmes d’opérations de Kubernetes et les procédures).
* Projets open source tiers qui ne sont pas fournis dans le cadre de la Kubernetes contrôlent le plan ou déploiement avec les clusters AKS. Ces projets peuvent inclure Istio, Helm, Envoy ou autres utilisateurs.
  > [!NOTE]
  > Microsoft peut fournir prise en charge du meilleur effort pour les projets open source tiers tels que Helm et Kured. Où l’outil open source tiers s’intègre avec le fournisseur de cloud Kubernetes Azure ou d’autres bogues spécifiques AKS, Microsoft prend en charge des exemples et des applications à partir de la documentation de Microsoft.
* Logiciels de source fermé par des tiers. Ce logiciel peut inclure des outils d’analyse sécurité et mise en réseau des périphériques ou des logiciels.
* Problèmes multicloud ou multi-fournisseur aboutissants de build. Par exemple, Microsoft ne prend en charge les problèmes liés à l’exécution d’une solution de fournisseur de cloud multipublic fédéré.
* Réseau de personnalisations autres que ceux répertoriés dans le [documentation AKS](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > Microsoft ne prend pas en charge les problèmes et les bogues liés aux groupes de sécurité réseau (NSG). Par exemple, Support Microsoft peut répondre aux questions sur une défaillance du groupe de sécurité réseau pour mettre à jour ou un groupe de sécurité réseau ou charge équilibrage un comportement inattendu.

## <a name="aks-support-coverage-for-worker-nodes"></a>AKS prend en charge de couverture pour les nœuds de travail

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Responsabilités de Microsoft pour les nœuds de travail ACS

Microsoft et les clients partagent la responsabilité de nœuds de travail Kubernetes où :

* L’image du système d’exploitation de base a requis des ajouts (par exemple, la surveillance et mise en réseau des agents).
* Les nœuds de travail reçoivent automatiquement des correctifs de système d’exploitation.
* Problèmes avec le Kubernetes contrôlent le plan de composants qui s’exécutent sur les nœuds de travail sont résolus automatiquement. Composants sont les suivants :
  * Kube-proxy
  * tunnels de réseau qui fournissent des chemins de communication vers le Kubernetes maître des composants
  * kubelet
  * Démon docker ou Moby

> [!NOTE]
> Sur un nœud worker, si un composant de plan de contrôle n’est pas opérationnel, l’équipe AKS devrez peut-être redémarrer le nœud de travail dans son ensemble. En raison de leur accès restreint aux charges de travail actif du client et les données, l’équipe AKS redémarre un nœud de travail uniquement si le client fait remonter le problème. Dans la mesure du possible, l’équipe AKS fonctionne pour éviter un redémarrage obligatoire d’affecter l’application.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Responsabilités du client pour les nœuds de travail ACS

Microsoft ne redémarre pas automatiquement les nœuds de travail pour appliquer des correctifs de système d’exploitation. Même si les correctifs du système d’exploitation sont transmises aux nœuds de travail, le *client* est responsable de la redémarrer les nœuds de travail pour appliquer les modifications. Bibliothèques partagées, démons tels que lecteur SSD hybride (SSHD) et autres composants au niveau du système ou du système d’exploitation sont corrigées automatiquement.

Les clients sont responsables de l’exécution de mises à niveau Kubernetes. Ils peuvent exécuter des mises à niveau par le biais du Panneau de commande Azure ou Azure CLI. Cela s’applique des mises à jour qui contiennent la sécurité ou des améliorations de fonctionnalités pour Kubernetes.

> [!NOTE]
> Étant donné que AKS est un *service géré*, ses objectifs finaux comprennent la suppression de responsabilité pour les correctifs, mises à jour et collecte pour améliorer la gestion de service complète et plus sans intervention des journaux. L’augmentation de capacité du service pour la gestion de bout en bout, les versions futures peuvent omettre certaines fonctions (par exemple, le redémarrage de nœud et la mise à jour corrective automatique).

### <a name="security-issues-and-patching"></a>Problèmes de sécurité et de mise à jour corrective

Si une faille de sécurité se trouve dans un ou plusieurs composants de AKS, l’équipe AKS corrigera tous les clusters affectés pour atténuer le problème. Vous pouvez également l’équipe offrira aux utilisateurs des conseils de mise à niveau.

Pour les nœuds de travail qu’un affecte de faille de sécurité, si un correctif sans interruption de service est disponible, l’équipe AKS appliquer ce correctif et avertir les utilisateurs de la modification.

Lorsqu’un correctif de sécurité nécessite un redémarrage du nœud worker, Microsoft informe les clients de cette exigence. Il incombe au client pour le redémarrage ou la mise à jour pour obtenir le correctif logiciel de cluster. Si les utilisateurs ne s’appliquent pas les correctifs en fonction des conseils AKS, leur cluster continue à être vulnérables au problème de sécurité.

### <a name="node-maintenance-and-access"></a>Accès et la maintenance de nœud

Nœuds de travail sont une responsabilité partagée et sont détenus par les clients. Pour cette raison, les clients ont la possibilité de se connecter à leurs nœuds worker et apportez des modifications potentiellement dangereuses telles que les mises à jour du noyau et l’installation ou la suppression des packages.

Si les clients apporter des modifications destructrices ou provoquent contrôle les composants plan mis hors connexion ou se glisse, AKS détecte cet échec et restaurer automatiquement le nœud worker à l’état de travail précédent.

Bien que les clients peuvent se connecter à et modifier des nœuds de travail, cette opération est déconseillée, car les modifications peuvent rendre un cluster réintègre.

## <a name="network-ports-access-and-nsgs"></a>Ports réseau, les accès et les groupes de sécurité réseau

En tant que service géré, AKS a des exigences spécifiques sur la mise en réseau et de connectivité. Ces exigences sont moins souples que la configuration requise pour les composants d’IaaS normales. Dans ACS, opérations telles que la personnalisation des règles de groupe de sécurité réseau bloque un port spécifique (par exemple, à l’aide de règles de pare-feu qui bloquent le port sortant 443), et les URL de mise en liste verte peuvent rendre votre cluster réintègre.

> [!NOTE]
> Actuellement, AKS ne vous permet de verrouiller complètement de sortie à partir de votre cluster (par exemple, domaine explicite ou mise en liste verte de port). La liste des URL et les ports est susceptible de changer sans avertissement. Vous pouvez obtenir la liste mise à jour en créant un ticket de Support Azure. La liste est uniquement pour les clients qui sont prêts à accepter que leur disponibilité du cluster peut avoir une incidence *à tout moment.*

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Non pris en charge les fonctionnalités de Kubernetes alpha et bêta

AKS prend en charge uniquement les fonctionnalités stables au sein du projet Kubernetes en amont. Sauf mention contraire, AKS ne prennent en charge les fonctionnalités alpha et bêta sont disponibles dans le projet de Kubernetes en amont.

Dans les deux scénarios, alpha ou beta fonctionnalités peuvent être déployées avant qu’ils sont généralement disponibles :

* Les clients sont remplies avec le produit AKS, prise en charge ou équipes d’ingénierie et ont été demandés pour essayer ces nouvelles fonctionnalités.
* Ces fonctionnalités ont été [activé par un indicateur de fonctionnalité](https://github.com/Azure/AKS/blob/master/previews.md). Les clients doivent explicitement choisir d’utiliser ces fonctionnalités.

## <a name="preview-features-or-feature-flags"></a>Fonctionnalités en version préliminaire ou indicateurs de fonctionnalités

Pour les fonctionnalités et fonctions qui requièrent des tests étendus et des commentaires des utilisateurs, Microsoft publie de nouvelles fonctionnalités en version préliminaire ou fonctionnalités derrière un indicateur de fonctionnalité. Considérez ces fonctionnalités en tant que fonctionnalités de la version préliminaire ou bêta.

Fonctionnalités en version préliminaire ou fonctionnalités de l’indicateur de fonctionnalité ne sont pas censées pour la production. Modifications apportées en continu dans les API et de comportement, de correctifs de bogues et d’autres modifications peuvent entraîner des temps d’arrêt et de clusters instables.

Fonctionnalités en version préliminaire publique sont chute sous « meilleur effort » la prise en charge ces fonctionnalités sont en version préliminaire et pas conçu pour la production, et sont pris en charge par les équipes de support technique ACS pendant les heures ouvrables uniquement. Pour plus d’informations, consultez :

* [FAQ du support Azure](https://azure.microsoft.com/en-us/support/faq/)

> [!NOTE]
> Fonctionnalités en version préliminaire en vigueur au Azure *abonnement* niveau. N’installez pas les fonctionnalités en version préliminaire sur un abonnement de production. Sur un abonnement de production, les fonctionnalités en version préliminaire peuvent modifier le comportement par défaut de l’API et affectent les opérations normales.

## <a name="upstream-bugs-and-issues"></a>Problèmes et les bogues en amont

Étant donné la vitesse de développement dans le projet de Kubernetes en amont, bogues invariablement surviennent. Certains de ces bogues ne peut pas être corrigée ou contourné au sein du système AKS. Au lieu de cela, des correctifs de bogues nécessitent des correctifs volumineux aux projets en amont (par exemple, Kubernetes, les systèmes d’exploitation nœud ou de travail et les noyaux). Pour les composants appartenant à Microsoft (par exemple, le fournisseur de cloud Azure), AKS et le personnel Azure est validé pour la résolution des problèmes en amont dans la Communauté.

Lorsqu’un problème de support technique est la cause racine par un ou plusieurs bogues en amont, AKS prise en charge et l’ingénierie, les équipes vont :

* Identifiez et lier les bogues en amont avec les détails de prise en charge afin d’expliquer pourquoi ce problème affecte votre cluster ou la charge de travail. Les clients reçoivent des liens vers les dépôts requis afin de pouvoir regarder les problèmes et voir quand une nouvelle version fournira des correctifs.
* Fournir le potentiel des solutions de contournement ou des atténuations. Si le problème peut être atténué, un [problème connu](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) sont créés dans le référentiel AKS. Le dépôt de problème connu, et explique :
  * Le problème, notamment des liens vers des bogues en amont.
  * La solution de contournement et plus d’informations sur une mise à niveau ou d’une autre persistance de la solution.
  * Approximatif chronologies pour l’inclusion du problème, selon la cadence de publication en amont.
