---
title: Azure Security Center et Azure Container Registry | Microsoft Docs
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
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: f0de56f968488f0e5d551ad705cc6f8ca6e7bc47
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521451"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Intégration d’Azure Container Registry à Security Center (préversion)

Azure Container Registry (ACR) est un service de registre Docker privé et géré qui stocke et gère vos images conteneurs pour les déploiements Azure dans un registre central. Il est basé sur le registre Docker open source 2.0.

Lorsque vous utilisez ACR avec le niveau Standard d’Azure Security Center (voir la [tarification](security-center-pricing.md)), vous bénéficiez d’une visibilité plus poussée sur les vulnérabilités du registre et des images.

[![Recommandations Azure Container Registry (ACR) à l’intérieur d’Azure Security Center](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

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