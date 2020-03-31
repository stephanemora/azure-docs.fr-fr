---
title: Forum aux questions pour Azure Red Hat OpenShift
description: Trouvez des réponses aux questions fréquentes sur Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f468cb294d79c44f92ef95437c0d88639a78b9a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77619499"
---
# <a name="azure-red-hat-openshift-faq"></a>FAQ sur Azure Red Hat OpenShift

Cet article traite des questions fréquemment posées (FAQ) sur Microsoft Azure Red Hat OpenShift.

## <a name="which-azure-regions-are-supported"></a>Quelles sont les régions Azure prises en charge ?

Consultez [Ressources prises en charge](supported-resources.md#azure-regions) pour obtenir la liste des régions du monde où Azure Red Hat OpenShift est pris en charge.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Puis-je déployer un cluster dans un réseau virtuel existant ?

Non. Vous pouvez toutefois connecter un cluster Azure Red Hat OpenShift à un réseau virtuel existant via le peering. Consultez [Connecter le réseau virtuel d’un cluster à un réseau virtuel existant](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) pour plus d’informations.

## <a name="what-cluster-operations-are-available"></a>Quelles opérations de cluster sont disponibles ?

Vous pouvez uniquement augmenter ou réduire le nombre de nœuds de calcul. Aucune autre modification n’est autorisée sur la `Microsoft.ContainerService/openShiftManagedClusters` ressource après sa création. Le nombre maximal de nœuds de calcul est limité à 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Quelles tailles de machines virtuelles puis-je utiliser ?

Consultez [Tailles de machine virtuelle Azure Red Hat OpenShift](supported-resources.md#virtual-machine-sizes) pour obtenir la liste des tailles de machine virtuelle que vous pouvez utiliser avec un cluster Azure Red Hat OpenShift.

## <a name="is-data-on-my-cluster-encrypted"></a>Les données de mon cluster sont-elles chiffrées ?

Par défaut, le chiffrement s’effectue au repos. La plateforme de stockage Azure Storage chiffre automatiquement vos données avant de les rendre persistantes et les déchiffre avant la récupération. Voir [Azure Storage Service Encryption pour les données au repos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) pour en savoir plus.

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>Puis-je utiliser Prometheus/Grafana pour surveiller mes applications ?

Oui, vous pouvez déployer Prometheus dans votre espace de noms et surveiller les applications de ce dernier.

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Puis-je utiliser Prometheus/Grafana pour surveiller les mesures relatives à la capacité et l’intégrité du cluster ?

Non, pas pour l’instant.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Docker Registry est-il disponible en externe afin que je puisse utiliser des outils tels que Jenkins ?

Docker Registry est disponible à partir de `https://docker-registry.apps.<clustername>.<region>.azmosa.io/`, bien qu’une solide garantie en termes de durabilité du stockage ne soit pas fournie. Vous pouvez également utiliser [Azure Container Registry](https://azure.microsoft.com/services/container-registry/).

## <a name="is-cross-namespace-networking-supported"></a>La mise en réseau sur les espaces de noms est-elle prise en charge ?

Le client et les administrateurs de projet individuel peuvent personnaliser la mise en réseau sur les espaces de noms (y compris la refuser) par projet à l’aide d’objets `NetworkPolicy`.

## <a name="can-an-admin-manage-users-and-quotas"></a>Un administrateur peut-il gérer des quotas et des utilisateurs ?

Oui. Un administrateur Azure Red Hat OpenShift peut gérer des utilisateurs et des quotas et dispose en plus d’un accès à tous les projets créés par les utilisateurs.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Puis-je limiter un cluster à certains utilisateurs Azure AD seulement ?

Oui. Vous pouvez désigner les utilisateurs Azure AD pouvant se connecter à un cluster en configurant l’application Azure AD. Pour plus d’informations, consultez [Procédure : Limiter votre application à un ensemble d’utilisateurs](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-i-restrict-users-from-creating-projects"></a>Puis-je empêcher les utilisateurs de créer des projets ?

Oui. Connectez-vous à votre cluster en tant qu’administrateur Azure Red Hat OpenShift, puis exécutez la commande suivante :

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

Pour plus d’informations, consultez la documentation OpenShift sur la [désactivation du provisionnement automatique](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning).

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Un cluster peut-il disposer de nœuds de calcul dans plusieurs régions Azure ?

Non. Tous les nœuds d’un cluster Azure Red Hat OpenShift doivent provenir de la même région Azure.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Les nœuds d’infrastructure et principaux sont-ils rendus abstraits avec Azure Kubernetes Service (AKS) ?

Non. Toutes les ressources, y compris le cluster principal, s’exécutent dans votre abonnement client. Ces types de ressources sont placés dans un groupe de ressources en lecture seule.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>Open Service Broker pour Azure (OSBA) est-il pris en charge ?

Oui. Vous pouvez utiliser OSBA avec Azure Red Hat OpenShift. Consultez [Open Service Broker pour Azure](https://github.com/Azure/open-service-broker-azure#openshift-project-template) pour plus d’informations.

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>J’essaie de regarder dans le réseau virtuel d’un autre abonnement, j’obtiens une erreur `Failed to get vnet CIDR`.

Dans l’abonnement comportant le réseau virtuel, veillez à inscrire le fournisseur `Microsoft.ContainerService` avec `az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>En quoi consiste le processus de maintenance d’Azure Red Hat OpenShift (ARO) ?

Il existe trois types de maintenance pour ARO : la mise à niveau, la sauvegarde et la restauration des données etcd, et la maintenance lancée par le fournisseur de cloud.

+ Les mises à niveau sont des mises à niveau logicielles et des CVE. La correction CVE se produit au démarrage en exécutant `yum update`, et permet une atténuation immédiate.  En parallèle, une nouvelle image sera créée pour les prochaines créations de clusters.

+ La sauvegarde et la gestion des données etcd font partie d’un processus automatisé qui peut nécessiter un temps d’arrêt du cluster en fonction de l’action. Si la base de données etcd est restaurée à partir d’une sauvegarde, il y aura un temps d’arrêt. Nous sauvegardons etcd toutes les heures et conservons les 6 dernières heures de sauvegarde.

+ La maintenance lancée par le fournisseur de cloud comprend les pannes relatives au réseau et au stockage, ainsi que les pannes régionales. La maintenance est dépendante du fournisseur de cloud, notamment pour les mises à jour que celui-ci fournit.

## <a name="what-is-the-general-upgrade-process"></a>En quoi consiste le processus général de mise à niveau ?

Le processus d’exécution d’une mise à niveau doit être sécurisé et ne doit pas perturber les services de cluster. Le SRE peut déclencher le processus de mise à niveau lorsque de nouvelles versions sont disponibles ou que des CVE sont en attente.

Les mises à jour disponibles sont testées dans un environnement de préproduction, puis elles sont appliquées aux clusters de production. Lors de leur application, un nouveau nœud est temporairement ajouté, et les nœuds sont mis à jour les uns après les autres pour que les pods conservent le nombre de réplicas existants. Les bonnes pratiques permettent de réduire au maximum, voire d’éviter les temps d’arrêt.

Selon le niveau de gravité de la mise à niveau ou de la mise à jour en attente, le processus peut différer. En effet, les mises à jour peuvent être appliquées rapidement pour limiter l’exposition du service à un CVE. Une nouvelle image sera générée de façon asynchrone, testée, puis déployée en tant que mise à niveau de cluster. Autrement dit, il n’y a aucune différence entre la maintenance d’urgence et la maintenance planifiée. La maintenance planifiée n’est pas préprogrammée avec le client.

Si une communication avec le client est nécessaire, des notifications peuvent être envoyées via ICM ou par e-mail.

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>Quelle est la différence entre les fenêtres de maintenance planifiée et les fenêtres de maintenance d’urgence ?

Nous ne faisons pas la distinction entre ces deux types de maintenances. Nos équipes sont disponibles 24 h/24, 7 j/7 et 365 j/365. Elles n’utilisent pas les fenêtres de maintenance traditionnelles planifiées en dehors des heures de bureau.

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>Comment sont mis à jour le système d’exploitation hôte et le logiciel OpenShift ?

Le système d’exploitation hôte et le logiciel OpenShift sont mis à jour dans le cadre de notre processus général de mise à niveau et de création d’image.

## <a name="whats-the-process-to-reboot-the-updated-node"></a>Comment se passe le processus de redémarrage du nœud mis à jour ?

Ce redémarrage est géré dans le cadre d’une mise à niveau.

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>Les données stockées dans etcd sont-elles chiffrées sur ARO ?

Elles ne sont pas chiffrées au niveau d’etcd. Il n’est pas possible d’activer le chiffrement dans ce cas. OpenShift prend en charge cette fonctionnalité. Toutefois, des efforts d’ingénierie sont nécessaires pour suivre la feuille de route. Les données sont chiffrées au niveau du disque. Pour plus d’informations, consultez [Encrypting Data at Datastore Layer](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html).

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Les journaux des machines virtuelles sous-jacentes peuvent-ils être diffusés en continu vers un système d’analyse des journaux client ?

Les journaux Syslog, Docker, journal et dmesg sont gérés par le service managé et ne sont pas montrés aux clients.

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>Comment un client peut-il accéder aux métriques telles que l’UC ou la mémoire au niveau du nœud, en vue d’effectuer une mise à l’échelle ou un débogage, par exemple ? Je n’arrive pas à exécuter `kubectl top` sur un cluster ARO.

Les clients peuvent accéder aux indicateurs de performance de l’UC/la mémoire au niveau du nœud à l’aide de la commande `oc adm top nodes` ou `kubectl top nodes` avec le rôle de cluster client-administrateur.  Les clients peuvent également accéder aux indicateurs de performance de l’UC/la mémoire de `pods` à l’aide de la commande `oc adm top pods` ou `kubectl top pods`

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>Quelle est la configuration par défaut du planificateur pod pour ARO ?

ARO utilise le planificateur par défaut fourni avec OpenShift. Il existe quelques mécanismes supplémentaires qui ne sont pas pris en charge dans ARO. Pour plus d’informations, consultez la [documentation default scheduler](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler) et la [documentation master scheduler](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json).

La planification avancée ou personnalisée n’est pas prise en charge. Pour plus d’informations, consultez la [documentation sur la planification](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html).

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Lors du scale-up d’un déploiement, comment les domaines d’erreur Azure sont-ils mappés à l’emplacement pod pour éviter que tous les pods d’un service ne soient rendus hors d’usage par une défaillance dans un domaine d’erreur ?

Par défaut, il existe cinq domaines d’erreur lorsque vous utilisez des groupes de machines virtuelles identiques dans Azure. Chaque instance de machine virtuelle d’un groupe identique sera placée dans l’un de ces domaines d’erreur. Cela garantit que les applications déployées sur les nœuds de calcul d’un cluster seront placées dans des domaines d’erreur distincts.

Pour plus d’informations, consultez [Choisir le bon nombre de domaines d’erreur pour un groupe de machines virtuelles identiques](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains).

## <a name="is-there-a-way-to-manage-pod-placement"></a>Existe-t-il un moyen de gérer l’emplacement des pods ?

Les clients ont la possibilité d’obtenir des nœuds et d’afficher des étiquettes en tant que client-administrateur.  Cela permettra de cibler les machines virtuelles du groupe identique.

Vous devez être prudent avec certaines étiquettes :

- Vous ne devez pas utiliser le nom d’hôte. Le nom d’hôte « tourne » souvent avec les mises à niveau et les mises à jour, et est donc amené à changer.

- Si le client fait une demande concernant certaines étiquettes ou une stratégie de déploiement, il est possible d’y répondre. Toutefois, cela nécessite des efforts d’ingénierie et n’est pas pris en charge pour le moment.

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>Quel est le nombre maximal de pods que peut contenir un cluster ARO ?  Quel est le nombre maximal de pods que peut contenir un nœud dans ARO ?

 Azure Red Hat OpenShift 3.11 a une limite de 50 pods par nœud et [ARO est limité à 20 nœuds de calcul](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available). Ainsi, le nombre maximal de pods pris en charge dans un cluster ARO est de 50 x 20 = 1 000.

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Est-il possible de spécifier des plages d’adresses IP pour un déploiement sur un réseau virtuel privé, en évitant les conflits avec d’autres réseaux virtuels d’entreprise après l’appairage ?

Azure Red Hat OpenShift prend en charge le peering de réseaux virtuels et permet au client de fournir un réseau virtuel avec lequel effectuer un peering, ainsi qu’un CIDR de réseau virtuel pour le réseau OpenShift.

Le réseau virtuel créé par ARO est protégé et sa configuration ne peut pas être modifiée. Le réseau virtuel appairé est contrôlé par le client et se trouve dans son abonnement.

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>Le cluster réside-t-il dans un abonnement client ? 

L’application managée Azure réside dans un groupe de ressources verrouillé avec l’abonnement client. Le client peut voir les objets de ce groupe de ressources, mais ne peut pas les modifier.

## <a name="is-the-sdn-module-configurable"></a>Le module SDN est-il configurable ?

Le module SDN est de type openshift-ovs-networkpolicy et n’est pas configurable.

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Quels droits UNIX (dans IaaS) sont disponibles pour les nœuds principaux/d’infrastructure/d’application ?

Ceci ne s’applique pas à cette offre. L’accès aux nœuds est interdit.

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>De quels droits OCP disposons-nous ? Administrateur de cluster ? Administrateur de projet ?

Pour plus d’informations, consultez la [vue d’ensemble de l’administration des clusters](https://docs.openshift.com/aro/admin_guide/index.html) Azure Red Hat OpenShift.

## <a name="which-kind-of-federation-with-ldap"></a>Quel est le type de fédération avec LDAP ?

Cela sera possible avec l’intégration Azure AD. 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>Des éléments d’ARO sont-ils partagés avec d’autres clients ? Ou est-ce que tout est indépendant ?

Chaque cluster Azure Red Hat OpenShift est dédié à un client donné et se trouve dans l’abonnement du client. 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Est-il possible de choisir une solution de stockage persistant, comme OCS ? 

Il est possible de choisir entre deux classes de stockage : Disque Azure et Fichier Azure.

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>Comment s’effectuent les mises à jour de cluster (y compris les mises à jour majeures et mineures pour la résolution des vulnérabilités) ?

Consultez [En quoi consiste le processus général de mise à niveau ?](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process).

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>Quel équilibreur de charge Azure ARO utilise-t-il ?  Est-il de niveau Standard ou De base, et peut-il être configuré ?

ARO utilise l’équilibreur de charge Azure Standard, qui n’est pas configurable.

## <a name="can-aro-use-netapp-based-storage"></a>ARO peut-il utiliser un stockage NetApp ?

À l’heure actuelle, les seules options de stockage prises en charge sont les classes de stockage Disque Azure et Fichier Azure. 


