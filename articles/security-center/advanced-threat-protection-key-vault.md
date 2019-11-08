---
title: Comment configurer la protection avancée contre les menaces pour Azure Key Vault | Microsoft Docs
description: Cet article explique comment configurer la protection avancée contre les menaces pour Azure Key Vault dans Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: b28f03f0997b6bb2c494c35cee9695a478906c47
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521455"
---
# <a name="how-to-set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>Comment configurer la protection avancée contre les menaces pour Azure Key Vault (préversion)

La protection avancée contre les menaces pour Azure Key Vault fournit une couche supplémentaire de renseignements de sécurité. Cet outil détecte les tentatives potentiellement dangereuses d’accès ou d’exploitation des comptes Key Vault. Grâce à l’utilisation de la protection avancée contre les menaces de Security Center, vous pouvez répondre aux menaces sans être un expert en sécurité et sans avoir à apprendre des systèmes de surveillance de sécurité supplémentaires.

Lorsque Security Center détecte une activité anormale, il affiche des alertes. Il envoie également un e-mail à l’administrateur des abonnements avec les détails de l’activité suspecte et des recommandations sur la façon d’examiner et de corriger les menaces identifiées. 

> [!NOTE]
> La protection avancée contre les menaces pour Azure Key Vault est actuellement disponible uniquement dans les régions d’Amérique du Nord.

## <a name="to-set-up-advanced-threat-protection-from-azure-security-center"></a>Configurer la protection avancée contre les menaces à partir d’Azure Security Center

Par défaut, la protection avancée contre les menaces est activée pour tous vos comptes Key Vault lorsque vous vous abonnez au niveau Standard de Security Center (consultez la [tarification](security-center-pricing.md)). 

Pour activer ou désactiver la protection d’un abonnement spécifique :

1. Dans la barre latérale de Security Center, cliquez sur **Tarification et paramètres**.
1. Sélectionnez l’abonnement avec les comptes de stockage pour lesquels vous souhaitez activer ou désactiver la protection contre les menaces.
1. Cliquez sur **Niveau tarifaire**.
1. À partir du groupe **Sélectionner le niveau tarifaire par type de ressource**, recherchez la ligne Key Vaults, puis cliquez sur **Activé** ou **Désactivé**.
    [![Activation ou désactivation de la protection avancée contre les menaces pour Key Vault dans Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. Cliquez sur **Enregistrer**.


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à activer et à désactiver la protection avancée contre les menaces pour Azure Key Vault. 

Pour d’autres informations connexes, consultez l’article suivant :

- [Détection des menaces pour les couches des services Azure dans Security Center](security-center-alerts-service-layer.md) : cet article décrit les alertes liées à la protection avancée contre les menaces pour Azure Key Vault