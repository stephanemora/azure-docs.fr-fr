---
title: Forum aux questions sur Azure Red Hat OpenShift | Microsoft Docs
description: Voici les réponses aux questions courantes sur Microsoft Azure Red Hat OpenShift
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: 881734caf855ccfc4f001693fe261b8448b49bc4
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466196"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift Forum aux questions

Cet article traite des questions fréquemment posées (FAQ) sur Microsoft Azure Red Hat OpenShift.

## <a name="which-azure-regions-are-supported"></a>Quelles régions Azure sont pris en charge ?

Consultez [ressources prises en charge](supported-resources.md#azure-regions) pour obtenir la liste des régions du monde où Azure Red Hat OpenShift est pris en charge.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Puis-je déployer un cluster dans un réseau virtuel existant ?

Non. Mais vous pouvez vous connecter à un cluster Azure Red Hat OpenShift à un réseau virtuel existant via l’appairage. Consultez [connecter un réseau virtuel d’un cluster à un réseau virtuel existant ](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) pour plus d’informations.

## <a name="what-cluster-operations-are-available"></a>Les opérations de cluster sont disponibles ?

Vous pouvez uniquement augmenter ou réduire le nombre de nœuds de calcul. Aucune autre modification n’est autorisées au `Microsoft.ContainerService/openShiftManagedClusters` ressource après sa création. Le nombre maximal de nœuds de calcul est limité à 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Les tailles de machines virtuelles puis-je utiliser ?

Consultez [tailles de machine virtuelle Azure Red Hat OpenShift](supported-resources.md#virtual-machine-sizes) pour obtenir la liste des tailles de machine virtuelle que vous pouvez utiliser avec un cluster Azure Red Hat OpenShift.

## <a name="is-data-on-my-cluster-encrypted"></a>Est des données sur mon cluster chiffrée ?

Par défaut, il est le chiffrement au repos. La plateforme de stockage Azure chiffre automatiquement vos données avant de rendre persistantes et déchiffre les données avant leur récupération. Consultez [Azure Storage Service Encryption pour les données au repos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) pour plus d’informations.

## <a name="can-i-use-prometheusgrafana-to-monitor-containers-and-manage-capacity"></a>Puis-je utiliser Prometheus/Grafana pour surveiller les conteneurs et de gérer la capacité ?

Non, pas à l’heure actuelle.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Étant le Registre Docker disponibles en externe puis-je utiliser des outils tels que Jenkins ?

Le Registre Docker est disponible à partir de `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` Toutefois, une garantie de durabilité du stockage fort n’est pas fournie. Vous pouvez également utiliser [Azure Container Registry](https://azure.microsoft.com/services/container-registry/).

## <a name="is-cross-namespace-networking-supported"></a>Mise en réseau de cross-espace de noms est prise en charge ?

Client et les administrateurs de projet individuel peuvent personnaliser la mise en réseau de cross-espace de noms (y compris les refus il) par projet à l’aide `NetworkPolicy` objets.

## <a name="can-an-admin-manage-users-and-quotas"></a>Un administrateur peut gérer de quotas et les utilisateurs ?

Oui. Un administrateur Azure Red Hat OpenShift peut gérer les utilisateurs et des quotas en plus de l’accès à un utilisateur de tous les projets créés.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Puis-je restreindre un cluster à seulement certains utilisateurs Azure AD ?

Oui. Vous pouvez restreindre le Azure AD les utilisateurs peuvent se connecter à un cluster en configurant l’Application Azure AD. Pour plus d’informations, consultez [Comment : Limiter votre application à un ensemble d’utilisateurs](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Pouvez un cluster ont des nœuds de calcul dans plusieurs régions Azure ?

Non. Tous les nœuds dans un cluster Azure Red Hat OpenShift doivent provenir de la même région Azure.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Sont des nœuds master et infrastructure disparaît tels qu’ils sont avec Azure Kubernetes Service (AKS) ?

Non. Toutes les ressources, y compris le maître du cluster, exécutent dans votre abonnement client. Ces types de ressources sont placées dans un groupe de ressources en lecture seule.
