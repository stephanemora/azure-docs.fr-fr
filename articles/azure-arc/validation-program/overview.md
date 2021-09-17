---
title: Vue d’ensemble de la validation des services avec Azure Arc
description: Explique le processus de validation Azure Arc pour se conformer aux extensions Kubernetes, Data Services et de cluster compatibles avec Arc.
ms.date: 07/30/2021
ms.topic: overview
ms.openlocfilehash: 1cf208b9a476f4a2a16df157ebfa0bb7661cfc1c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785382"
---
# <a name="overview-of-azure-arc-enabled-service-validation"></a>Vue d’ensemble de la validation de service avec Azure Arc

Microsoft recommande d’exécuter des services avec Azure Arc sur des plateformes validées. Cet article vous dirige vers le contenu pour expliquer comment les différents composants avec Azure Arc sont validés. 

Actuellement, les solutions validées sont disponibles auprès des partenaires pour Kubernetes et les services de données.

## <a name="kubernetes"></a>Kubernetes

Kubernetes avec Azure Arc fonctionne pour tous les clusters Kubernetes certifiés CNCF (Cloud Native Computing Foundation). L’équipe d’Azure Arc a collaboré avec des fournisseurs clés du secteur pour valider Kubernetes avec Azure Arc par rapport à leurs [distributions Kubernetes](../kubernetes/validation-program.md). Les prochaines versions majeures et mineures des distributions Kubernetes publiées par ces fournisseurs seront validées au niveau de leur compatibilité par rapport à Kubernetes avec Azure Arc.

## <a name="data-services"></a>Services de données

Nous sommes également associés aux partenaires OEM (Original Equipment Manufacturer) et aux fournisseurs de stockage pour valider les solutions de [services de données avec Azure Arc](../data/validation-program.md).

## <a name="validation-process"></a>Processus de validation

Le processus de validation Azure Arc est disponible dans GitHub. Pour plus d’informations sur le mode de validation de votre offre avec Azure Arc, l’atelier de test et la stratégie, reportez-vous au [processus de validation Azure Arc](https://github.com/Azure/azure-arc-validation/) dans GitHub.

## <a name="next-steps"></a>Étapes suivantes

* [Distributions Kubernetes validées](../kubernetes/validation-program.md?toc=/azure/azure-arc/toc.json&bc=/azure/azure-arc/breadcrumb/toc.json)

* [Distributions Kubernetes validées pour les services de données](../data/validation-program.md?toc=/azure/azure-arc/toc.json&bc=/azure/azure-arc/breadcrumb/toc.json)
