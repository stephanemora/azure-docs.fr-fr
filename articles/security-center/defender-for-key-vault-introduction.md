---
title: 'Azure Defender pour Key Vault : avantages et fonctionnalités'
description: Découvrez les avantages et les fonctionnalités d’Azure Defender pour Key Vault.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 06818e443568918e2ee87bbfbec81836ea85648b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90930623"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>Présentation d’Azure Defender pour Key Vault

Azure Key Vault est un service cloud qui protège les clés et secrets de chiffrement comme les certificats, chaînes de connexion et mots de passe. 

Activez **Azure Defender pour Key Vault** pour bénéficier d’une protection native Azure avancée contre les menaces pour Azure Key Vault, apportant une couche supplémentaire de renseignements de sécurité. 

## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale (GA)|
|Prix :|**Azure Defender pour Key Vault** est facturé conformément aux indications de la [page des tarifs](security-center-pricing.md)|
|Clouds :|![Oui](./media/icons/yes-icon.png) Clouds commerciaux<br>![Non](./media/icons/no-icon.png) National/souverain (US Gov, Chine Gov, autres Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>Quels sont les avantages d’Azure Defender pour Key Vault ?

Azure Defender détecte les tentatives inhabituelles et potentiellement dangereuses d’accès ou d’exploitation des comptes Key Vault. Cette couche de protection vous permet de traiter efficacement les menaces sans pour autant être un expert en sécurité ni avoir besoin de gérer des systèmes tiers de supervision de la sécurité.  

Quand des activités anormales se produisent, Azure Defender affiche des alertes et les envoie éventuellement par e-mail aux membres concernés de votre organisation. Ces alertes fournissent des détails sur l’activité suspecte, ainsi que des suggestions pour examiner et corriger les menaces. 

## <a name="azure-defender-for-key-vault-alerts"></a>Alertes Azure Defender pour Key Vault
Lorsque vous recevez une alerte d’Azure Defender pour Key Vault, nous vous recommandons de l’examiner et d’y répondre de la façon décrite dans [Répondre aux alertes Azure Defender pour Key Vault](defender-for-key-vault-usage.md). Azure Defender pour Key Vault protège les applications et les informations d’identification. Ainsi, même si vous connaissez l’application ou l’utilisateur qui a déclenché l’alerte, il est important de vérifier la situation dans laquelle s’inscrit cette dernière.

Les alertes s’affichent dans la page **Sécurité** de Key Vault, le tableau de bord Azure Defender, et la page des alertes de Security Center.

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Page de sécurité d’Azure Key Vault":::

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert Azure Defender pour Key Vault.

Pour des informations connexes, consultez les articles suivants : 

- [Alertes de sécurité Key Vault](alerts-reference.md#alerts-azurekv) : section Key Vault de la table de référence pour toutes les alertes d’Azure Security Center.
- [Exportation d’alertes vers un système SIEM](continuous-export.md)
- [Supprimer les alertes d’Azure Defender](alerts-suppression-rules.md)