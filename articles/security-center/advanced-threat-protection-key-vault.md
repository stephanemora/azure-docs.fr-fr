---
title: Protection contre les menaces pour Azure Key Vault
description: Cet article explique comment configurer la protection avancée contre les menaces pour Azure Key Vault dans Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 7025b091b83a56b7b3df18c3f42cb84b163560bb
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89277763"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Protection contre les menaces pour Azure Key Vault (préversion)

La protection avancée contre les menaces pour Azure Key Vault fournit une couche supplémentaire de renseignements de sécurité. Cet outil détecte les tentatives potentiellement dangereuses d’accès ou d’exploitation des comptes Key Vault. À l’aide de la protection native avancée contre les menaces dans Azure Security Center, vous pouvez répondre aux menaces sans être un expert en sécurité et sans avoir à apprendre des systèmes de surveillance de sécurité supplémentaires.

Lorsque Security Center détecte une activité anormale, il affiche des alertes. Il envoie également un e-mail à l’administrateur des abonnements avec les détails de l’activité suspecte et des recommandations sur la façon d’examiner et de corriger les menaces identifiées.

## <a name="configure-threat-protection-from-security-center"></a>Configurer la protection contre les menaces à partir de Security Center

Par défaut, la protection avancée contre les menaces est activée pour tous vos comptes Key Vault lorsque vous vous abonnez au niveau tarifaire Standard de Security Center. Pour plus d’informations, voir la [tarification](security-center-pricing.md).

Pour activer ou désactiver la protection d’un abonnement spécifique :

1. Dans le volet gauche de Security Center, sélectionnez **Tarification et paramètres**.

1. Sélectionnez l’abonnement avec les comptes de stockage pour lesquels vous souhaitez activer ou désactiver la protection contre les menaces.

1. Sélectionnez **Niveau tarifaire**.

1. À partir du groupe **Sélectionner le niveau tarifaire par type de ressource**, recherchez la ligne **Coffres de clés**, puis sélectionnez **Activé** ou **Désactivé**.

    [![Activation ou désactivation de la protection avancée contre les menaces pour Key Vault dans Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. Sélectionnez **Enregistrer**.


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à activer et à désactiver la protection avancée contre les menaces pour Azure Key Vault. 

Pour des informations connexes, consultez les articles suivants :

- [Protection contre les menaces dans Azure Security Center](threat-protection.md) : cet article décrit les sources des alertes de sécurité dans Azure Security Center.
- [Alertes de sécurité Key Vault](alerts-reference.md#alerts-azurekv) : section Key Vault de la table de référence pour toutes les alertes d’Azure Security Center.