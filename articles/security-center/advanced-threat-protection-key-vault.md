---
title: Configurer la protection avancée contre les menaces pour Azure Key Vault | Microsoft Docs
description: Cet article explique comment configurer la protection avancée contre les menaces pour Azure Key Vault dans Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 2375d8ee92d9c04c287b7fca793fcdc236e1e8f4
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720036"
---
# <a name="set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>Configurer la protection avancée contre les menaces pour Azure Key Vault (préversion)

La protection avancée contre les menaces pour Azure Key Vault fournit une couche supplémentaire de renseignements de sécurité. Cet outil détecte les tentatives potentiellement dangereuses d’accès ou d’exploitation des comptes Key Vault. À l’aide de la protection native avancée contre les menaces dans Azure Security Center, vous pouvez répondre aux menaces sans être un expert en sécurité et sans avoir à apprendre des systèmes de surveillance de sécurité supplémentaires.

Lorsque Security Center détecte une activité anormale, il affiche des alertes. Il envoie également un e-mail à l’administrateur des abonnements avec les détails de l’activité suspecte et des recommandations sur la façon d’examiner et de corriger les menaces identifiées.

> [!NOTE]
> La protection avancée contre les menaces pour Azure Key Vault est actuellement disponible uniquement dans les régions d’Amérique du Nord.

## <a name="set-up-advanced-threat-protection-from-azure-security-center"></a>Configurer la protection avancée contre les menaces à partir d’Azure Security Center

Par défaut, la protection avancée contre les menaces est activée pour tous vos comptes Key Vault lorsque vous vous abonnez au niveau de service Standard de Security Center. Pour plus d’informations, voir la [tarification](security-center-pricing.md).

Pour activer ou désactiver la protection d’un abonnement spécifique, procédez comme suit :

1. Dans le volet gauche de Security Center, sélectionnez **Tarification et paramètres**.
1. Sélectionnez l’abonnement avec les comptes de stockage pour lesquels vous souhaitez activer ou désactiver la protection contre les menaces.
1. Sélectionnez **Niveau tarifaire**.
1. À partir du groupe **Sélectionner le niveau tarifaire par type de ressource**, recherchez la ligne **Coffres de clés**, puis sélectionnez **Activé** ou **Désactivé**.

    [![Activation ou désactivation de la protection avancée contre les menaces pour Key Vault dans Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. Sélectionnez **Enregistrer**.


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à activer et à désactiver la protection avancée contre les menaces pour Azure Key Vault. 

Pour d’autres informations connexes, consultez l’article suivant :

- [Détection des menaces pour les couches de services Azure dans Azure Security Center](security-center-alerts-service-layer.md) : Cet article décrit les alertes liées à la protection avancée contre les menaces pour Azure Key Vault.
