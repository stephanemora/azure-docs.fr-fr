---
title: Forum aux questions sur Azure Dev Spaces
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Obtenez des réponses aux questions les plus fréquemment posées sur Azure Dev Spaces.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s '
ms.openlocfilehash: 285fdb7892d2da40dd50e025cb1dd7644ec17ae0
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255725"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Forum aux questions sur Azure Dev Spaces

Cet article répond aux questions fréquemment posées sur Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Quelles régions Azure proposent actuellement Azure Dev Spaces ?

Pour obtenir la liste complète des régions disponibles, consultez les [régions prises en charge][supported-regions].

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Puis-je utiliser Azure Dev Spaces avec des fichiers Dockerfile ou des graphiques Helm existants ?

Oui, si votre projet comporte déjà un fichier Dockerfile ou un graphique Helm, vous pouvez l'utiliser avec Azure Dev Spaces. Lorsque vous exécutez `azds prep`, utilisez le paramètre `--chart` et spécifiez l'emplacement du graphique. Azure Dev Spaces générera un fichier *azds.yaml* ou *Dockerfile.develop*, mais il ne remplacera ou ne modifiera pas un fichier Dockerfile ou un graphique Helm existant. Vous devrez peut-être modifier les fichiers *azds.yaml* et *Dockerfile.develop* pour que tout fonctionne correctement avec votre application existante lors de l'exécution de `azds up`.

Lorsque vous utilisez votre propre fichier Dockerfile ou graphique Helm, les limitations suivantes s'appliquent :
* Si vous n'utilisez qu'un seul fichier Dockerfile, celui-ci doit inclure tout ce dont vous avez besoin pour activer les scénarios de développement, comme le kit de développement logiciel (SDK) linguistique, et pas seulement le runtime. Si vous utilisez un fichier Dockerfile distinct pour Azure Dev Spaces, comme un fichier Dockerfile.develop, tout ce dont vous avez besoin pour activer les scénarios de développement doit être inclus dans ce fichier Dockerfile.
* Votre graphique Helm doit prendre en charge la transmission de tout ou partie de la balise d'image en tant que valeur de *values.yaml*.
* Si vous modifiez quoi que ce soit avec entrée, vous pouvez également mettre à jour votre graphique Helm pour utiliser la solution d'entrée fournie par Azure Dev Spaces.
* Si vous souhaitez utiliser les [fonctionnalités de routage fournies par Azure Dev Spaces][dev-spaces-routing], tous les services d'un projet individuel doivent tenir dans un seul espace de noms Kubernetes et être déployés sous une dénomination simple, par exemple *service-a*. Dans les graphiques Helm standard, cette mise à jour de la dénomination peut être effectuée en spécifiant une valeur pour la propriété *fullnameOverride*.

Pour comparer votre propre fichier Dockerfile ou graphique Helm à une version existante qui fonctionne avec Azure Dev Spaces, passez en revue les fichiers générés dans le [guide de démarrage rapide][quickstart-cli].


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Puis-je modifier les fichiers générés par Azure Dev Spaces ?

Oui, vous pouvez modifier le fichier *azds.yaml*, le fichier Dockerfile et le graphique Helm[ générés par Azure Dev Spaces lors de la préparation de votre projet][dev-spaces-prep]. La modification de ces fichiers modifie la façon dont le projet est généré et exécuté.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Puis-je utiliser Azure Dev Spaces sans adresse IP publique ?

Non, vous ne pouvez pas configurer Azure Dev Spaces sur un cluster AKS sans adresse IP publique. Une adresse IP publique est [nécessaire à Azure Dev Spaces pour le routage][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Puis-je utiliser ma propre entrée avec Azure Dev Spaces ?

Oui, vous pouvez configurer votre propre entrée en plus de celle créée par Azure Dev Spaces. Par exemple, vous pouvez utiliser [traefik][ingress-traefik] ou [NGINX][ingress-nginx].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Puis-je utiliser HTTPS avec Azure Dev Spaces ?

Oui, vous pouvez configurer votre propre entrée avec HTTPS à l'aide de [traefik][ingress-https-traefik] ou [NGINX][ingress-https-nginx].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Puis-je utiliser Azure Dev Spaces sur un cluster qui utilise CNI plutôt que kubenet ? 

Oui, vous pouvez utiliser Azure Dev Spaces sur un cluster AKS qui utilise CNI pour la mise en réseau. Par exemple, vous pouvez utiliser Azure Dev Spaces sur un cluster AKS avec des [conteneurs Windows existants][windows-containers], qui utilise CNI pour la mise en réseau. Des informations supplémentaires sur l’utilisation de CNI pour la mise en réseau avec Azure Dev Spaces sont disponibles [ici](configure-networking.md#using-azure-cni).

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Puis-je utiliser Azure Dev Spaces avec des conteneurs Windows ?

Actuellement, Azure Dev Spaces est conçu pour être exécuté sur des pods et des nœuds Linux uniquement, mais vous pouvez exécuter Azure Dev Spaces sur un cluster AKS avec des [conteneurs Windows existants][windows-containers].

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Puis-je utiliser Azure Dev Spaces sur des clusters AKS avec les plages d’adresses IP autorisées du serveur d’API activées ?

Oui. Vous pouvez utiliser Azure Dev Spaces sur des clusters AKS avec les [plages d’adresses IP autorisées du serveur d’API][aks-auth-range] activées. Pour plus d’informations sur l’utilisation de clusters AKS avec des plages d’adresses IP autorisées par le serveur d’API activées avec Azure Dev Spaces sont disponibles [ici](configure-networking.md#using-api-server-authorized-ip-ranges).

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Puis-je utiliser Azure Dev Spaces sur des clusters AKS avec un trafic de sortie restreint pour les nœuds de cluster ?

Oui, vous pouvez utiliser Azure Dev Spaces sur des clusters AKS avec le [trafic de sortie restreint pour les nœuds de cluster][aks-restrict-egress-traffic] activé une fois que les noms de domaine complets appropriés ont été autorisés. Pour plus d’informations sur l’utilisation de clusters AKS avec un trafic de sortie restreint pour les nœuds de cluster activé avec Azure Dev Spaces sont disponibles [ici](configure-networking.md#ingress-and-egress-network-traffic-requirements).

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>Puis-je utiliser Azure Dev Spaces sur des clusters AKS compatibles avec RBAC ?

Oui, vous pouvez utiliser Azure Dev Spaces sur des clusters AKS compatibles ou non avec RBAC.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Que se passe-t-il lorsque j’active l’entrée pour le projet dans Visual Studio ?

Lorsque vous utilisez Visual Studio pour préparer votre projet, vous avez la possibilité d’activer l’entrée pour votre service. L’activation de l’entrée crée un point de terminaison public permettant d’accéder à votre service lors de son exécution sur votre cluster AKS, ce qui est facultatif. Si vous n’activez pas l’entrée, votre service est uniquement accessible à partir de votre cluster AKS.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-prep]: how-dev-spaces-works.md#prepare-your-code
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md