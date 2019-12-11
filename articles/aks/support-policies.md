---
title: Stratégies de support pour Azure Kubernetes Service (AKS)
description: Découvrez les stratégies de support Azure Kubernetes Service (AKS), la responsabilité partagée et les fonctionnalités disponibles en préversion (ou alpha ou bêta).
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: c018e511bbeed41bc9caf721562349a37ad0e748
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707218"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Stratégies de support pour Azure Kubernetes Service

Cet article fournit des informations sur les stratégies de support technique et les limitations pour Azure Kubernetes Service (AKS). Il détaille également la gestion des nœuds Worker, les composants de plan de contrôle géré, les composants open source tiers et la gestion de la sécurité ou des correctifs.

## <a name="service-updates-and-releases"></a>Versions et mises à jour du service

* Pour des informations sur les versions, consultez les [notes de publication relatives à AKS](https://github.com/Azure/AKS/releases).
* Pour des informations sur les fonctionnalités en préversion, consultez [AKS Preview Features and Related Projects](https://github.com/Azure/AKS/blob/master/previews.md) (Fonctionnalités d’évaluation AKS et projets associés).

## <a name="managed-features-in-aks"></a>Fonctionnalités gérées dans AKS

Les composants cloud infrastructure as a service (IaaS) de base, tels que les composants réseau ou de calcul, permettent aux utilisateurs d’accéder à des contrôles de niveau inférieur et à des options de personnalisation. En revanche, AKS fournit un déploiement clé en main de Kubernetes qui offre aux clients l’ensemble courant de configurations et fonctionnalités dont ils ont besoin. Les clients AKS bénéficient de fonctions de personnalisation et de déploiement limitées, ainsi que d’autres options. Ces clients n’ont pas à se soucier des clusters Kubernetes, ni à les gérer directement.

Avec AKS, le client dispose d’un *plan de contrôle* entièrement géré. Le plan de contrôle contient tous les composants et services dont le client a besoin pour faire fonctionner et proposer des clusters Kubernetes aux utilisateurs finaux. Tous les composants Kubernetes sont conservés et gérés par Microsoft.

Microsoft gère et surveille les composants suivants via le panneau de contrôle :

* Kubelet ou serveurs d’API Kubernetes
* Etcd ou un stockage clé-valeur compatible assurant la Qualité de service (QoS), l’extensibilité et le runtime
* Services DNS (par exemple, kube-dns ou CoreDNS)
* Proxy Kubernetes ou réseau

AKS n’est pas une solution de cluster entièrement gérée. Certains composants, tels que des nœuds Worker ont *la responsabilité partagée*, où les utilisateurs doivent participer à la gestion du cluster AKS. L’entrée utilisateur est nécessaire, par exemple, pour appliquer un correctif de sécurité du système d’exploitation de nœud Worker.

Les services sont *gérés* de façon que Microsoft et l’équipe AKS déploient, gèrent et soient responsables de l’opérationnalité et de la disponibilité du service. Les clients ne peuvent pas modifier ces composants gérés. Microsoft limite la personnalisation, afin de garantir une expérience utilisateur cohérente et évolutive. Pour une solution entièrement personnalisable, consultez [AKS Engine](https://github.com/Azure/aks-engine) (Moteur AKS).

> [!NOTE]
> Les nœuds Worker AKS sont affichés dans le portail Azure en tant que ressources Azure IaaS standard. Toutefois, ces machines virtuelles sont déployées dans un groupe de ressources Azure personnalisé (portant le préfixe MC\\\*). Il est possible de modifier des nœuds Worker AKS. Par exemple, vous pouvez utiliser Secure Shell (SSH) pour modifier les nœuds Worker AKS de la même façon que si vous apportiez des modifications à des machines virtuelles normales (néanmoins, vous ne pouvez pas changer l’image de système d’exploitation de base, et les modifications peuvent ne pas être conservées suite à une mise à jour ou un redémarrage). En outre, vous pouvez associer d’autres ressources Azure aux nœuds Worker AKS. Toutefois, lorsque vous apportez des modifications de *personnalisation et de gestion en dehors du réseau*, le cluster AKS peut ne plus être supporté. Évitez de modifier des nœuds Worker, sauf si le Support Microsoft vous demande d’apporter ces modifications.

## <a name="shared-responsibility"></a>Responsabilité partagée

Lors de la création d’un cluster, le client définit les nœuds Worker Kubernets générés par AKS. Les charges de travail du client sont exécutées sur ces nœuds. Les clients possèdent et peuvent afficher ou modifier les nœuds Worker.

Étant donné que les nœuds de cluster du client exécutent du code privé et stockent des données sensibles, le Support Microsoft peut y accéder de manière limitée uniquement. Le Support Microsoft ne peut pas se connecter, exécuter des commandes ou afficher les journaux de ces nœuds sans autorisation expresse du client ou de l’assistance.

Étant donné que les nœuds Worker sont fragiles, Microsoft fait preuve de toute la prudence nécessaire pour limiter sa gestion en arrière-plan. Dans de nombreux cas, votre charge de travail continue de s’exécuter même si les nœuds master Kubernetes, etcd et d’autres composants gérés par Microsoft échouent. Apporter des modifications aux nœuds Worker sans faire preuve de prudence peut conduire à la perte de données et de charges de travail et ne plus garantir le support du cluster.

## <a name="aks-support-coverage"></a>Couverture du support AKS

Microsoft fournit un support technique pour les éléments suivants :

* Connectivité à tous les composants Kubernetes que le service Kubernetes fournit et prend en charge, tel que le serveur d’API.
* Gestion, durée de fonctionnement, QoS et opérations des services de plan de contrôle Kubernetes (nœuds master Kubernetes, serveur d’API, etcd et kube-dns, par exemple).
* Etcd. Le support inclut des sauvegardes automatisées et transparentes de l’ensemble des données etcd toutes les 30 minutes pour la planification de la récupération d’urgence et la restauration de l’état du cluster. Ces sauvegardes ne sont pas directement disponibles pour les clients ou utilisateurs. Elles garantissent la cohérence et la fiabilité des données.
* Points d’intégration dans le pilote du fournisseur cloud Azure pour Kubernetes. Ceux-ci incluent des intégrations à d’autres services Azure tels que les équilibreurs de charge, les volumes persistants ou la mise en réseau (Kubernetes et Azure CNI).
* Questions ou problèmes relatifs à la personnalisation des composants du plan de contrôle tels que le serveur d’API Kubernetes, etcd et kube-dns.
* Problèmes relatifs au réseau, tels qu’Azure CNI, que kubenet, ou d’autres problématiques en lien avec des fonctionnalités ou l’accès réseau. Les problèmes peuvent inclure la résolution DNS, la perte de paquets, le routage, etc. Microsoft prend en charge différents scénarios de mise en réseau :
  * Mise en réseau Kubenet (basique) et avancée (Azure CNI) au sein du cluster et des composants associés
  * Connectivité à d’autres services et applications Azure
  * Contrôleurs d’entrée et configurations d’équilibreur de charge ou d’entrée
  * Performances et latence réseau

Microsoft ne fournit pas de support technique pour les éléments suivants :

* Questions sur l’utilisation de Kubernetes. Par exemple, le Support Microsoft ne fournit pas de conseils sur la façon dont les contrôleurs d’entrée doivent être créés, sur l’utilisation des charges de travail d’application ou sur l’application d’outils ou de packages logiciels open source ou tiers.
  > [!NOTE]
  > Le Support Microsoft peut vous conseiller sur la fonctionnalité de cluster AKS, la personnalisation et le paramétrage (par exemple, les procédures et problèmes relatifs aux opérations Kubernetes).
* Les projets open source tiers qui ne sont pas fournis dans le cadre du plan de contrôle Kubernetes ou déployés avec des clusters AKS. Ces projets peuvent inclure Istio, Helm, Envoy, etc.
  > [!NOTE]
  > Microsoft fera de son mieux pour assurer le support des projets open source tiers tels que Helm et Kured. Quand l’outil open source tiers est associé au fournisseur cloud Azure Kubernetes ou à d’autres bogues spécifiques à AKS, Microsoft fournit des exemples et applications issus de sa documentation.
* Logiciels fermés tiers. Ces logiciels peuvent inclure des outils d’analyse de sécurité et des logiciels ou appareils réseau.
* Problèmes de solutions multifournisseur ou multicloud. Par exemple, Microsoft n’assure pas le support des problèmes liés à l’exécution d’une solution de fournisseur cloud multipublic fédérée.
* Personnalisations réseau autres que celles répertoriées dans la [documentation AKS](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > Microsoft n’assure pas le support des problèmes et bogues liés aux groupes de sécurité réseau. Par exemple, le Support Microsoft peut répondre à des questions sur un échec de mise à jour d’un groupe de sécurité réseau ou un comportement d’équilibreur de charge ou de groupe de sécurité réseau inattendu.

## <a name="aks-support-coverage-for-worker-nodes"></a>Couverture du support AKS pour les nœuds Worker

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Responsabilités de Microsoft pour les nœuds Worker AKS

Microsoft et les clients partagent la responsabilité relative aux nœuds Worker Kubernetes quand :

* L’image du système d’exploitation de base a requis des ajouts (par exemple, des agents réseau et de surveillance).
* Les nœuds Worker reçoivent automatiquement des correctifs de système d’exploitation.
* Des problèmes en lien avec les composants du plan de contrôle Kubernetes qui s’exécutent sur les nœuds Worker sont automatiquement corrigés. Les composants incluent :
  * Proxy Kube
  * Tunnels de mise en réseau qui fournissent des chemins d’accès de communication vers les composants master Kubernetes
  * Kubelet
  * Démon Docker ou Moby

> [!NOTE]
> Sur un nœud Worker, si un composant de plan de contrôle n’est pas opérationnel, l’équipe AKS devra peut-être redémarrer des composants individuels ou l’intégralité du nœud Worker. Ces opérations de redémarrage sont automatisées et assurent une résolution automatique des problèmes courants. Ces redémarrages se produisent uniquement au niveau du _nœud_ et non du cluster, sauf dans le cas d’une opération de maintenance urgente ou d’une panne.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Responsabilités du client pour les nœuds Worker AKS

Microsoft ne redémarre pas automatiquement les nœuds Worker pour appliquer des correctifs de système d’exploitation. Même si les correctifs de système d’exploitation sont transmises aux nœuds Worker, le *client* est responsable du reémarrage des nœuds Worker pour appliquer les modifications. Bibliothèques partagées, démons tels que des disques hybrides SSD (SSHD) et d’autres composants au niveau du système ou du système d’exploitation sont corrigés automatiquement.

Les clients sont responsables de l’exécution des mises à niveau Kubernetes. Ils peuvent exécuter des mises à niveau via le Panneau de configuration Azure ou Azure CLI. Cela s’applique pour les mises à jour qui contiennent des améliorations en matière de sécurité ou de fonctionnalités pour Kubernetes.

> [!NOTE]
> Étant donné qu’AKS est un *service géré*, ses objectifs finaux comprennent la suppression de responsabilité pour les correctifs, les mises à jour et la collecte des journaux afin d’étoffer la gestion du service et de proposer une solution automatique. À mesure de l’augmentation de la capacité du service pour la gestion de bout en bout, les versions futures peuvent omettre certaines fonctions (par exemple, le redémarrage de nœud et l’application de correctifs automatique).

### <a name="security-issues-and-patching"></a>Problèmes de sécurité et application de correctifs

Si une faille de sécurité se trouve dans un ou plusieurs composants d’AKS, l’équipe AKS appliquera des correctifs à tous les clusters affectés pour atténuer le problème. L’équipe peut également donner des conseils en matière de mise à niveau aux utilisateurs.

Pour les nœuds Worker affectés par une faille de sécurité, si un correctif sans interruption de service est disponible, l’équipe AKS l’appliquera et informera les utilisateurs de la modification.

Lorsqu’un correctif de sécurité nécessite un redémarrage du nœud Worker, Microsoft informe les clients de cette situation. Il incombe au client de redémarrer ou d’effectuer la mise à jour pour obtenir le correctif du cluster. Si les utilisateurs n’appliquent pas les correctifs conformément aux instructions AKS, leur cluster continuera d’être vulnérable au problème de sécurité.

### <a name="node-maintenance-and-access"></a>Accès et maintenance des nœuds

Les nœuds Worker font l’objet d’une responsabilité partagée et sont détenus par les clients. C’est pour cette raison que les clients ont la possibilité de se connecter à leurs nœuds Worker et d’y apporter des modifications potentiellement dangereuses, telles que des mises à jour du noyau et l’installation ou la suppression de packages.

Si les clients apportent des modifications nuisibles ou provoquent la panne ou la mise hors connexion des composants du plan de contrôle, AKS détectera ce problème et restaurera automatiquement le nœud Worker à son état de fonctionnement précédent.

Même si les clients peuvent se connecter aux nœuds Worker et les modifier, ces opérations sont déconseillées, car ces modifications peuvent affecter le support du cluster.

## <a name="network-ports-access-and-nsgs"></a>Ports réseau, accès et groupes de sécurité réseau

En tant que service géré, AKS a des exigences spécifiques en termes de mise en réseau et de connectivité. Ces exigences sont moins souples que celles des composants IaaS standard. Dans AKS, les opérations telles que la personnalisation de règles de groupe de sécurité réseau, le blocage d’un port spécifique (par exemple, à l’aide de règles de pare-feu qui bloquent le port sortant 443) et la mise sur liste verte d’URL peuvent affecter le support de votre cluster.

> [!NOTE]
> Actuellement, AKS ne vous permet pas de verrouiller intégralement le trafic de sortie depuis votre cluster. Pour contrôler la liste des URL et des ports que votre cluster peut utiliser pour le trafic sortant, consultez la section [Limiter le trafic de sortie](limit-egress-traffic.md).

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Fonctionnalités Kubernetes alpha et bêta non supportées

AKS supporte uniquement les fonctionnalités stables au sein du projet Kubernetes en amont. Sauf mention contraire, AKS ne supporte pas les fonctionnalités alpha et bêta qui sont disponibles dans le projet Kubernetes en amont.

Dans ces deux scénarios, les fonctionnalités alpha ou bêta peuvent être déployées avant qu’elles ne soient mises à la disposition générale :

* Les clients ont rencontré les équipes d’ingénierie, de support ou produit AKS et ont demandé à essayer ces nouvelles fonctionnalités.
* Ces fonctionnalités ont été [activées par un indicateur de fonctionnalité](https://github.com/Azure/AKS/blob/master/previews.md). Les clients doivent explicitement choisir d’utiliser ces fonctionnalités.

## <a name="preview-features-or-feature-flags"></a>Fonctionnalités d’évaluation ou indicateurs de fonctionnalités

Pour les fonctionnalités et fonctions qui requièrent des tests étendus et des commentaires des utilisateurs, Microsoft publie de nouvelles fonctionnalités d’évaluation ou des fonctionnalités appartenant à un indicateur de fonctionnalité. Considérez ces fonctionnalités comme des fonctionnalités bêta ou en version préliminaire.

Les fonctionnalités d’évaluation ou fonctionnalités avec indicateur de fonctionnalité ne sont pas destinées aux environnements de production. Les modifications continues apportées aux API et aux comportements, corrections de bogues et autres types de modifications peuvent entraîner des temps d’arrêt et un manque de stabilité des clusters.

Les fonctionnalités en préversion publique font l’objet d’un support relatif, puisque ces fonctionnalités ne sont pas destinées à la production et sont supportées par les équipe du support technique AKS pendant les heures de bureau uniquement. Pour toute information supplémentaire, consultez les documents suivants :

* [FAQ du support Azure](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> Les fonctionnalités d’évaluation s’appliquent au niveau de *l’abonnement* Azure uniquement. N’installez pas les fonctionnalités d’évaluation sur un abonnement de production. Sur un abonnement de production, les fonctionnalités d’évaluation peuvent modifier le comportement par défaut de l’API et affecter les opérations normales.

## <a name="upstream-bugs-and-issues"></a>Problèmes et bogues en amont

Étant donné la vitesse de développement dans le projet Kubernetes en amont, des bogues surviennent invariablement. Certains de ces bogues ne peuvent pas être corrigés ni contournés au sein du système AKS. Au lieu de cela, des corrections de bogues exigent des correctifs plus importants pour les projets en amont (comme Kubernetes, systèmes d’exploitation Worker ou de nœud et noyaux). Pour les composants appartenant à Microsoft (par exemple, le fournisseur cloud Azure), AKS et le personnel Azure s’engagent à corriger les problèmes en amont dans la communauté.

Lorsqu’un problème de support technique est la cause racine d’un ou de plusieurs bogues en amont, les équipes d’ingénierie et de support AKS procèdent comme suit :

* Ils identifient et associent les bogues en amont aux détails de support afin d’expliquer pourquoi ce problème affecte votre cluster ou charge de travail. Les clients reçoivent des liens vers les référentiels nécessaires afin de pouvoir étudier les problèmes et voir quand une nouvelle version fournira des correctifs.
* Ils proposent des atténuations ou solutions alternatives éventuelles. Si le problème peut être atténué, un [problème connu](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) est consigné dans le référentiel AKS. La consignation du problème connu décrit :
  * Le problème, avec des liens vers des bogues en amont.
  * La solution alternative et des informations sur une mise à jour ou une autre persistance de la solution.
  * Des chronologies approximatives pour l’inclusion du problème, en fonction de la cadence des versions en amont.
