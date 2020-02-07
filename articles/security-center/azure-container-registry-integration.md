---
title: Azure Security Center et Azure Container Registry
description: Découvrez comment intégrer Azure Security Center à Azure Container Registry
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/19/2019
ms.author: memildin
ms.openlocfilehash: bb004d536d30d62116866581daeef9b42c23d470
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76936361"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Intégration d’Azure Container Registry à Security Center (préversion)

Azure Container Registry (ACR) est un service de registre Docker privé et géré qui stocke et gère vos images conteneurs pour les déploiements Azure dans un registre central. Il est basé sur le registre Docker open source 2.0.

Si vous êtes sur le niveau standard d’Azure Security Center, vous pouvez ajouter le pack de registres de conteneurs. Cette fonctionnalité facultative apporte une meilleure visibilité des vulnérabilités des images de vos registres basés sur ARM. Activez ou désactivez le pack au niveau de l’abonnement pour couvrir tous les registres d’un abonnement. Cette fonctionnalité est facturée par image et non par analyse comme indiquée sur la [page des tarifs](security-center-pricing.md). 

L’activation du pack de registres de conteneur garantit que Security Center est prêt à analyser les images qui sont envoyées au registre. Les analyses ont lieu au niveau de l’image : Security Center n’analyse pas votre registre, il analyse les images stockées dans le registre. 

Chaque fois qu’une image est envoyée à votre registre, Security Center analyse automatiquement cette image. Pour déclencher l’analyse d’une image, poussez-la vers votre référentiel.

Une fois l’analyse terminée (généralement après environ 10 minutes), les résultats sont disponibles dans des suggestions de Security Center telles que la suivante :

[![Exemple de suggestion Azure Security Center sur les vulnérabilités découvertes dans une image hébergée Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Avantages de l’intégration

Security Center identifie les registres ACR basés sur ARM dans votre abonnement, et fournit en toute transparence les éléments suivants :

* **Analyse native des vulnérabilités dans Azure** pour toutes les images Linux envoyées. Security Center analyse l’image avec un scanneur fourni par Qualys, leader des solutions de gestion des vulnérabilités. Cette solution native est intégrée de manière transparente par défaut.

* **Recommandations de sécurité** pour les images Linux avec des vulnérabilités connues. Security Center fournit des détails sur chaque vulnérabilité signalée et une classification de gravité. En outre, il fournit des conseils sur la façon de corriger les vulnérabilités spécifiques détectées sur chaque image envoyée au registre.

![Présentation globale d’Azure Security Center et Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les fonctionnalités de sécurité des conteneurs de Security Center, consultez :

* [Azure Security Center et sécurité des conteneurs](container-security.md)

* [Intégration à Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* [Protection des machines virtuelles](security-center-virtual-machine-protection.md) : décrit les recommandations de Security Center
