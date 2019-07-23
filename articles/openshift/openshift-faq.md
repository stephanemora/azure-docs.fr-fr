---
title: Forum aux questions pour Azure Red Hat OpenShift | Microsoft Docs
description: Trouvez des réponses aux questions fréquentes sur Azure Red Hat OpenShift
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: 32eb2c47ed46aed8e2e3755a83437a21391295c5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122964"
---
# <a name="azure-red-hat-openshift-faq"></a>FAQ sur Azure Red Hat OpenShift

Cet article traite des questions fréquemment posées (FAQ) sur Microsoft Azure Red Hat OpenShift.

## <a name="how-do-i-get-started"></a>Comment faire pour démarrer ?

Avant de pouvoir utiliser Azure Red Hat OpenShift, vous devez acheter au minimum 4 nœuds d’application Azure Red Hat OpenShift réservés.

Si vous êtes un client Azure, [achetez les instances réservées Azure Red Hat OpenShift](https://aka.ms/openshift/buy) via le portail Azure. Après l’achat, votre abonnement est activé dans les 24 heures, après quoi, vous serez en mesure d’approvisionner des clusters.

Si vous n’êtes pas client Azure, [contactez le service commercial](https://aka.ms/openshift/contact-sales) et remplissez le formulaire de vente en bas de la page pour démarrer le processus.

Consultez la [page de tarification Azure Red Hat OpenShift](https://aka.ms/openshift/pricing) pour plus d’informations.

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

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Un cluster peut-il disposer de nœuds de calcul dans plusieurs régions Azure ?

Non. Tous les nœuds d’un cluster Azure Red Hat OpenShift doivent provenir de la même région Azure.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Les nœuds d’infrastructure et principaux sont-ils rendus abstraits avec Azure Kubernetes Service (AKS) ?

Non. Toutes les ressources, y compris le cluster principal, s’exécutent dans votre abonnement client. Ces types de ressources sont placés dans un groupe de ressources en lecture seule.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>Open Service Broker pour Azure (OSBA) est-il pris en charge ?

Oui. Vous pouvez utiliser OSBA avec Azure Red Hat OpenShift. Consultez [Open Service Broker pour Azure](https://github.com/Azure/open-service-broker-azure#openshift-project-template) pour plus d’informations.

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>J’essaie de regarder dans le réseau virtuel d’un autre abonnement, j’obtiens une erreur `Failed to get vnet CIDR`.

Dans l’abonnement comportant le réseau virtuel, veillez à inscrire le fournisseur `Microsoft.ContainerService` avec `az provider register -n Microsoft.ContainerService --wait` 
