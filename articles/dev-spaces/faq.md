---
title: Forum aux questions sur Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/25/2019
ms.topic: conceptual
description: Obtenez des réponses aux questions les plus fréquemment posées sur Azure Dev Spaces.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s '
ms.openlocfilehash: de44bf323b300a4467dc8b30555798f557898494
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71305912"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Forum aux questions sur Azure Dev Spaces

Cet article répond aux questions fréquemment posées sur Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Quelles régions Azure proposent actuellement Azure Dev Spaces ?

Pour obtenir la liste complète des régions disponibles, consultez [Régions et configuration prises en charge][supported-regions].

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Puis-je utiliser Azure Dev Spaces sans adresse IP publique ?

Non, vous ne pouvez pas configurer Azure Dev Spaces sur un cluster AKS sans adresse IP publique. Une adresse IP publique est [nécessaire à Azure Dev Spaces pour le routage][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Puis-je utiliser ma propre entrée avec Azure Dev Spaces ?

Oui, vous pouvez configurer votre propre entrée en plus de celle créée par Azure Dev Spaces. Par exemple, vous pouvez utiliser [traefik][ingress-traefik].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Puis-je utiliser HTTPS avec Azure Dev Spaces ?

Oui, vous pouvez configurer votre propre entrée avec HTTPS à l’aide de [traefik][ingress-https-traefik].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Puis-je utiliser Azure Dev Spaces sur un cluster qui utilise CNI plutôt que kubenet ? 

Oui, vous pouvez utiliser Azure Dev Spaces sur un cluster AKS qui utilise CNI pour la mise en réseau. Par exemple, vous pouvez utiliser Azure Dev Spaces sur un cluster AKS avec des [conteneurs Windows existants][windows-containers], qui utilise CNI pour la mise en réseau.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Puis-je utiliser Azure Dev Spaces avec des conteneurs Windows ?

Actuellement, Azure Dev Spaces est conçu pour être exécuté sur des pods et des nœuds Linux uniquement, mais vous pouvez exécuter Azure Dev Spaces sur un cluster AKS avec des [conteneurs Windows existants][windows-containers].


[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md