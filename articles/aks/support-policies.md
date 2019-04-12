---
title: Politiques de support Azure Kubernetes Service (AKS)
description: En savoir plus sur les stratégies de prise en charge d’Azure Kubernetes Service (AKS), une responsabilité partagée, les fonctionnalités en version préliminaire/Alpha/bêta.
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: f173fc7c794729eae8c60cceefa88d153800a816
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502172"
---
# <a name="azure-kubernetes-service-aks-support-policies"></a>Politiques de support Azure Kubernetes Service (AKS)

Cet article fournit des informations sur les politiques de support technique AKS, limitations, et contrôlent les composants de plan, les composants open source tiers et la sécurité de détails, notamment la gestion de nœud de travail, gérée / gestion des correctifs.

## <a name="service-updates--releases"></a>Versions et mises à jour de service

* Pour la mise en production d’informations, consultez le [AKS Release Notes][1]
* Pour les fonctionnalités en version préliminaire publique, consultez [préliminaire du service AKS et projets connexes][2]

## <a name="what-is-managed"></a>Ce qui est « géré »

Contrairement à tels que compute ou de mise en réseau, les composants cloud IaaS base qui exposent les contrôles de bas niveau et les options de personnalisation pour les utilisateurs de tirer parti, le service AKS fournit un déploiement Kubernetes clé en main qui représente l’ensemble commun des configurations et fonctionnalités nécessaires pour Kubernetes. Les clients qui utilisent ce service ont un nombre limité de personnalisations, de déploiement et d’autres options. Cela signifie également que les clients est inutile de vous inquiétez pas, ou gérer les clusters Kubernetes directement.

Avec AKS, le client obtient une solution entièrement gérée **plan de contrôle** – où le plan de contrôle contenue tous les composants et les services requis pour faire fonctionner et fournir des clusters Kubernetes aux utilisateurs finaux. Tous les composants Kubernetes sont conservées et gérés par Microsoft.

Avec managé **plan de contrôle** les composants suivants sont gérés et surveillés par Microsoft :

* Kubelet / API Kubernetes serveur (s)
* ETCD ou un magasin de clé/valeur compatible – y compris la qualité de service, l’évolutivité et runtime
* Les services DNS (par exemple, kube-dns / CoreDNS)
* Kubernetes Proxy/mise en réseau

ACS n’est pas 100 % gérée **cluster** solution. Certains composants (par exemple, les nœuds worker) disposer de certaines **partage des responsabilités** cas où des actions à mettre à jour le cluster AKS requièrent d’interaction utilisateur. Par exemple, nœud de travail application de correctifs de sécurité du système d’exploitation.

 **Managed**, signifie que Microsoft et l’équipe AKS déploie, fonctionne et est responsable de la disponibilité et les fonctionnalités de ces services. **Les clients ne peuvent pas modifier ces composants**. La personnalisation est limitée pour garantir une expérience utilisateur cohérente et évolutive. Pour une solution entièrement personnalisable, consultez [ACS-Engine](https://github.com/Azure/aks-engine).

> [!NOTE]
> Il est important de comprendre que les nœuds de travail Azure Kubernetes Service apparaissent dans le portail Azure en tant que des ressources IaaS Azure standard, bien que ces Machines virtuelles sont déployées dans un groupe de ressources Azure personnalisée (préfixe MC\\*). Un utilisateur peut modifier, SSH dans ceux-ci comme des machines virtuelles normales (Toutefois, vous ne pouvez pas modifier l’image du système d’exploitation de base, et les modifications ne peuvent pas persister d’une mise à jour ou redémarrer), et vous pouvez y attacher autre ressource Azure, ou les modifier dans le cas contraire. **Toutefois, cela de la personnalisation et la gestion hors bande signifie que le cluster AKS lui-même peut devenir difficile. Évitez la modification du nœud worker, sauf sur indication par le Support Microsoft.**

## <a name="what-is-shared-responsibility"></a>Quelle est la responsabilité partagée

Au moment de la création du cluster AKS crée un nombre de nœuds de travail Kubernetes définies par le client. Ces nœuds, comme indiqué sont où sont exécutées les charges de travail client. Les clients possèdent et peuvent afficher ou modifier ces nœuds de travail.

Étant donné que ces nœuds doivent exécuter du code privé et stockent des données sensibles, prise en charge de Microsoft a **d’un accès limité** au client tous les nœuds du cluster. Prise en charge de Microsoft ne peut pas se connecter à, exécuter des commandes ou afficher les journaux pour ces nœuds sans autorisation expresse client et/ou une assistance pour exécuter des commandes de débogage comme indiqué par l’équipe de support.

En raison de la nature sensible de ces nœuds worker, Microsoft prend la prudence pour limiter la gestion « dans les coulisses » de ces nœuds. Même si les Kubernetes maître nœuds, etcd et autres composants échec (gérée par Microsoft) votre charge de travail continue à s’exécuter dans de nombreux cas. Si les nœuds de travail sont modifiés sans soins, il peut entraîner une perte de données et/ou de la charge de travail et rendre le cluster réintègre.

## <a name="azure-kubernetes-service-support-coverage"></a>Couverture de prise en charge du Service Kubernetes Azure

**Microsoft fournit un support technique pour les éléments suivants :**

* Connectivité à tous les composants Kubernetes fournie et prise en charge par le service Kubernetes (par exemple, le serveur d’API)
* Gestion, disponibilité, QoS et les opérations de Kubernetes contrôlent des services de plan (etcd de nœuds, serveur d’API, de serveur maître de Kubernetes, kube-dns, par exemple.
* Prise en charge de l’ETCD inclut des sauvegardes automatisées, transparents de toutes les données etcd toutes les 30 minutes pour la restauration d’état cluster et de planification d’urgence. Ces sauvegardes ne sont pas directement accessibles aux clients/utilisateurs et sont utilisés pour garantir la cohérence et la fiabilité des données
* Les points d’intégration dans le pilote fournisseur de Cloud Azure pour Kubernetes tels que des intégrations d’autres Azure services tels que les équilibreurs de charge, les Volumes persistants, mise en réseau (Kubernetes et Azure CNI)
* Questions ou problèmes liés à la personnalisation du contrôle du plan de composants tels que le serveur d’API Kubernetes, etcd et kube-dns.
* Problèmes rubriques concernant la mise en réseau, tels que Azure CNI, Kubenet ou un autre réseau de problèmes d’accès et les fonctionnalités (DNS résolution, perte de paquets, routage et ainsi de suite).
  * Scénarios de mise en réseau pris en charge incluent Kubenet (Basic) et avancées de mise en réseau (Azure CNI) dans le cluster et de composants associés, de connectivité à d’autres services et applications Azure. En outre, les contrôleurs d’entrée et de configuration d’équilibrage de charge de l’entrée /, de performances du réseau et de latence sont pris en charge par Microsoft.

**Microsoft ne fournit pas de support technique pour les éléments suivants :**

* Conseil / utilisation « Procédure » Kubernetes des questions, par exemple la création de contrôleurs d’entrée personnalisé, questions de la charge de travail d’application, et les packages tiers-tiers/OSS ou certains outils sont hors de portée.
  * Tickets avis pour AKS cluster fonctionnalités, personnalisation, les opérations de Kubernetes p. ex. problèmes/how-tos trouvent dans l’étendue : de paramétrage.
* Projets d’open source tiers n’est fournis dans le cadre de la Kubernetes contrôlent le plan ou déploiement avec les clusters AKS, tels que Istio, Helm, Envoy et d’autres.
  * Pour les projets open source tiers, tels que Helm et Kured, meilleure prise en charge de travail est fourni pour des exemples et des applications fournies dans la documentation Microsoft et où cet outil open source de tiers s’intègre avec le fournisseur de cloud Kubernetes Azure ou autres bogues AKS spécifiques.
* Le logiciel tiers source fermé-cela peut inclure des outils, mise en réseau de périphériques ou les logiciels d’analyse de sécurité.
* Problèmes de « cloud multi » ou multifournisseur aboutissants de build ne sont pas pris en charge, par exemple en cours d’exécution une solution de fournisseur de cloud public multi fédérés n’est pas pris en charge.
* Personnalisations de réseau spécifiques, autres que celles décrites dans le fonctionnaire [documentation AKS][3].
  > [!NOTE]
  > Problèmes et bogues autour des groupes de sécurité réseau est pris en charge. Par exemple, prise en charge peut répondre à des questions concernant les groupes de sécurité réseau ne parvient pas à mettre à jour ou un comportement inattendu NSG ou équilibreur de charge.

## <a name="azure-kubernetes-service-support-coverage-worker-nodes"></a>Azure Kubernetes Service couverture (nœuds Worker)

### <a name="microsoft-responsibilities-for-azure-kubernetes-service-worker-nodes"></a>Responsabilités de Microsoft pour les nœuds de travail Azure Kubernetes Service

Comme indiqué dans la `shared responsibility` section, Kubernetes, nœuds de travail appartiennent à un modèle de responsabilité conjointe, où :

* Fournit l’image de système d’exploitation avec les ajouts requis (par exemple, la surveillance et mise en réseau des agents)
* Diffusion automatique des correctifs de système d’exploitation pour les nœuds de travail
* Mise à jour automatique des problèmes avec Kubernetes contrôler les composants de plan qui s’exécutent sur les nœuds de travail, telles que :
  * kube-proxy
  * tunnels de réseau qui fournissent des chemins de communication vers le Kubernetes maître des composants
  * kubelet
  * démon de docker/moby

> [!NOTE]
> Si un composant de plan de contrôle ne fonctionne pas sur un nœud de travail, l’équipe AKS devrez peut-être redémarrer le nœud de travail dans son ensemble pour résoudre le problème. Cela est effectué à la place d’un utilisateur et pas effectuée, sauf si une escalade de verrous du client est effectuée (en raison de l’accès à la charge de travail active les clients et les données). Chaque fois que possible AKS collaborent pour rendre un obligatoire redémarrer ayant un impact sur non applicatifs.

### <a name="customer-responsibilities-for-azure-kubernetes-service-worker-nodes"></a>Responsabilités du client pour les nœuds de travail Azure Kubernetes Service

**Microsoft est pas le cas :**

- Automatiquement les nœuds de travail de redémarrage pour système d’exploitation niveau correctifs prennent effet **(actuellement, voir ci-dessous)**

Correctifs de système d’exploitation sont remis aux nœuds de travail, toutefois il est le **responsabilité du client** redémarrer les nœuds de travail pour que les modifications entrent en vigueur. Cette automatique-mise à jour corrective inclut des bibliothèques partagées, démons telles que SSHD et autres composants de niveau système/système d’exploitation.

Pour les mises à niveau Kubernetes, **les clients sont responsables de l’exécution de la mise à niveau** via le panneau de configuration Azure ou Azure CLI. Cela s’applique des mises à jour contenant la sécurité ou des améliorations de fonctionnalités pour Kubernetes.

> [!NOTE]
> Comme ACS est un `managed service` nos objectifs de fin du service incluent la suppression de responsabilité pour les correctifs, mises à jour, la collecte de journaux, etc. pour le rendre un service entièrement géré et sans intervention plus. Ces éléments (redémarrage du nœud, la mise à jour corrective automatique) peuvent être supprimés dans les versions futures, comme augmentent nos fonctions de bout en bout de gestion.

### <a name="security-issues-and-patching"></a>Problèmes de sécurité et de mise à jour corrective

Dans certains cas (par exemple, Exposures), une faille de sécurité peut être trouvée dans un ou plusieurs des composants du service ACS. Dans de tels scénarios, AKS patch tous les clusters concernés pour atténuer le problème si possible ou fournir des conseils de mise à niveau aux utilisateurs.

Pour les nœuds de travail affectés par ce type une faille de sécurité, si un correctif sans interruption de service du problème est disponible, l’équipe AKS appliquer ce correctif et avertir les utilisateurs de la modification.

Si un correctif de sécurité nécessite nœud worker redémarre, Microsoft envoie une notification client de cette exigence, et il incombe au client pour exécuter le redémarrage ou de mettre à jour pour obtenir le correctif logiciel pour leur cluster. Si les utilisateurs ne s’appliquent pas les correctifs selon les recommandations de AKS, leur cluster continue à être vulnérable au problème (s).

### <a name="node-maintenance-and-access"></a>Accès et la maintenance de nœud

Étant donné que les nœuds de travail sont une responsabilité partagée et sous la propriété de clients, les clients peuvent se connecter à ces utilisateurs et effectuer des modifications potentiellement dangereuses, telles que des mises à jour du noyau, suppression de packages et de l’installation de nouveaux packages.

Si les clients effectuent des actions de destruction ou d’actions qui déclenchent des composants de plan de contrôle pour passer en mode hors connexion ou sinon deviennent non fonctionnel, le service ACS détecter cette défaillance et effectuer la mise à jour automatique pour restaurer le nœud worker sur l’utilisation de la précédente état.

Bien que les clients peuvent se connecter à et modifier les nœuds de travail, il est *déconseillée* , car cela peut rendre difficile votre cluster.

## <a name="network-ports-access-and-network-security-groups"></a>Ports réseau, les accès et les groupes de sécurité réseau

En tant que service géré, AKS a des exigences spécifiques sur la mise en réseau et de connectivité. Ces exigences sont moins souples que les composants d’IaaS normales. Contrairement à d’autres composants d’IaaS, certaines opérations (telles que la personnalisation des règles de groupe de sécurité réseau, le blocage des ports spécifiques, URL mise en liste verte et ainsi de suite) peuvent rendre votre cluster réintègre (par exemple, les règles de pare-feu bloque le port sortant 443).

> [!NOTE]
> Verrouiller complètement de sortie (par exemple, domaine/port explicite mise en liste verte) à partir de votre cluster n’est pas un scénario AKS pris en charge pour l’instant. La liste des URL et les Ports est susceptible de changer sans avertissement et peut être fournie par le support technique Azure via un ticket. La liste fournie est uniquement pour les clients qui sont prêts à accepter qui *la disponibilité de votre cluster pourrait être affectée à tout moment.*

## <a name="alphabeta-kubernetes-features-not-supported"></a>Fonctionnalités alpha/bêta Kubernetes (non pris en charge)

AKS prend uniquement en charge des fonctionnalités stables dans le projet de Kubernetes en amont. Fonctionnalités alpha/bêta disponibles en amont ne sont pas pris en charge sauf stipulation contraire communiqué et documentées.

Il existe deux cas où les fonctionnalités Alpha ou Beta peuvent être déployées avant la disponibilité générale :

* Les clients sont remplies avec le produit AKS, prise en charge ou équipes d’ingénierie et ont été explicitement demandés pour essayer ces nouvelles fonctionnalités.
* Ces fonctionnalités ont été [activée via un indicateur de fonctionnalité] [ 2] (explicite opt-in)

## <a name="preview-features--feature-flags"></a>Fonctionnalités en version préliminaire / indicateurs de fonctionnalités

Pour les fonctionnalités qui nécessitent des étendues de communauté de test et les commentaires des utilisateurs, Microsoft publie des nouvelles fonctionnalités préliminaires ou des fonctionnalités derrière un indicateur de fonctionnalité. Ces fonctionnalités doivent être considéré comme en version préliminaire / bêta et sont exposées aux utilisateurs une occasion d’essayer ces nouvelles fonctionnalités.

Toutefois, ces aperçu / indicateur de fonctionnalité fonctionnalités ne sont pas destinés à des fins de production : API, changement de comportement, des correctifs de bogues et autres modifications peuvent devenir qui peut entraîner des temps d’arrêt et de clusters instables. Prise en charge de ces fonctionnalités est limité à la création de rapports de bogue/problème. N’activez pas ces fonctionnalités sur les systèmes de production ou des abonnements.

> [!NOTE]
> L’activation des fonctionnalités en version préliminaire entre en vigueur au Azure **abonnement** niveau. N’installez pas de fonctionnalités en version préliminaire sur l’abonnement de production car elle peut modifier le comportement par défaut de l’API ayant un impact sur les opérations régulières.

## <a name="upstream-bugs-and-issues"></a>Problèmes et les bogues en amont

Étant donné la vitesse de développement dans le projet de Kubernetes en amont, il existe invariablement bogues qui ne peut pas être corrigées ou travaillant autour au sein du système AKS et à la place nécessitent des correctifs volumineux aux projets en amont (par exemple, Kubernetes, systèmes d’exploitation du nœud/de travail et les noyaux). Pour les composants que nous possédons (par exemple, le fournisseur de Cloud Azure), le personnel ACS/Azure est validé pour résoudre le problème en amont dans la Communauté.

Pour les cas où un problème de support technique est la cause racine pour un ou plusieurs bogues en amont, AKS prend en charge et ingénierie effectuera les éléments suivants :

* Identifiez et lier les bogues en amont avec les détails de prise en charge quant à la raison pour laquelle il a un impact sur votre cluster et/ou de la charge de travail. Les clients doivent être fournies avec des liens vers les dépôts requis/problèmes de regarder les problèmes et de voir quand une nouvelle version de Kubernetes/autres inclura le correctif (s)
* Des solutions de contournement ou atténuations potentiels : Dans certains cas, il est possible de contourner le problème : dans ce cas, un «[-problème connu](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)» sont créés sur le référentiel d’AKS explique :
  * Le problème et un lien vers les bogues en amont
  * La solution de contournement et plus d’informations sur la mise à niveau/autre persistance de la solution
  * Chronologies approximatives pour inclusion selon la cadence de publication en amont

[1]: https://github.com/Azure/AKS/releases
[2]: https://github.com/Azure/AKS/blob/master/previews.md
[3]: https://docs.microsoft.com/azure/aks/
