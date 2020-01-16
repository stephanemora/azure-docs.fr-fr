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
ms.openlocfilehash: 4cc88e7c04d10907a9a6386b1266eb8031d60926
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552676"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Intégration d’Azure Container Registry à Security Center (préversion)

Azure Container Registry (ACR) est un service de registre Docker privé et géré qui stocke et gère vos images conteneurs pour les déploiements Azure dans un registre central. Il est basé sur le registre Docker open source 2.0.

Pour une meilleure visibilité des vulnérabilités du registre et des images, les utilisateurs du niveau de service Standard d’Azure Security Center peuvent activer l’offre groupée facultative de registres de conteneurs. Le coût d’utilisation de cette fonctionnalité est facturé par image et non par analyse. Pour plus d’informations, voir la [tarification](security-center-pricing.md). Lorsque l’offre groupée est activée, Security Center analyse automatiquement les images dans votre registre chaque fois qu’une image est envoyée (push) au registre.

> [!NOTE]
> La première analyse de Security Center d’un registre ne se produit qu’une fois que l’offre groupée de registres de conteneurs est activée et qu’une image est envoyée (push) au registre.

Une fois l’analyse terminée (généralement après environ 10 minutes), les résultats sont disponibles dans les suggestions Security Center comme suit :

[![Exemple de suggestion Azure Security Center sur les vulnérabilités découvertes dans une image hébergée Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Avantages de l’intégration

Security Center identifie les registres ACR dans votre abonnement et fournit en toute transparence les éléments suivants :

* **Analyse native des vulnérabilités dans Azure** pour toutes les images Linux envoyées. Security Center analyse l’image avec un scanneur fourni par Qualys, leader des solutions de gestion des vulnérabilités. Cette solution native est intégrée de manière transparente par défaut.

* **Recommandations de sécurité** pour les images Linux avec des vulnérabilités connues. Security Center fournit des détails sur chaque vulnérabilité signalée et une classification de gravité. En outre, il fournit des conseils sur la façon de corriger les vulnérabilités spécifiques détectées sur chaque image envoyée au registre.

![Présentation globale d’Azure Security Center et Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les fonctionnalités de sécurité des conteneurs de Security Center, consultez :

* [Azure Security Center et sécurité des conteneurs](container-security.md)

* [Intégration à Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* [Protection des machines virtuelles](security-center-virtual-machine-protection.md) : décrit les recommandations de Security Center
