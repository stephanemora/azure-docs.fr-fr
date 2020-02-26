---
title: Génération et transfert de clés protégées par HSM pour Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Utilisez les informations présentes dans cette rubrique pour planifier, générer, puis transférer vos propres clés protégées par le module de sécurité matériel à utiliser avec le coffre de clés Azure. Cette méthode est également appelée BYOK (Bring Your Own Key, ou apportez votre propre clé).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 00d2d38801929454110b41be88d133e3af232af7
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425746"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importer des clés protégées par HSM dans un coffre de clés

Pour une meilleure garantie, lorsque vous utilisez le coffre de clés Azure, vous pouvez importer ou générer des clés dans des modules de sécurité matériels (HSM) qui ne franchissent jamais les limites HSM. Ce scénario est souvent appelé *Apportez votre propre clé*ou désigné par l’acronyme BYOK. Azure Key Vault utilise la famille nShield de modules HSM (FIPS 140-2 niveau 2 validé) de nCipher pour protéger vos clés.

Cette fonctionnalité n’est pas disponible pour Azure Chine 21Vianet.

> [!NOTE]
> Pour plus d’informations sur le coffre de clés Azure, consultez la page [Présentation du coffre de clés Azure](key-vault-overview.md)  
> Pour voir un didacticiel de mise en route incluant un coffre de clés pour les clés protégées par HSM, consultez la section [ Présentation d’Azure Key Vault](key-vault-overview.md).

## <a name="supported-hsms"></a>Modules HSM pris en charge

Le transfert de clés protégées par HSM vers un coffre de clés est pris en charge par deux méthodes. La méthode à employer dépend des modules HSM que vous utilisez. Utilisez le tableau ci-dessous pour déterminer la méthode à utiliser pour vos modules HSM afin de générer, puis transférer vos propres clés protégées par HSM, que vous utiliserez avec Azure Key Vault. 

|Nom du fournisseur de modules HSM|Modèles HSM pris en charge|Méthode de transfert de clé HSM prise en charge|
|---|---|---|
|Thales|<ul><li>Famille SafeNet Luna HSM 7 avec la version de microprogramme 7.3 ou une version ultérieure</li></ul>| [Utiliser la nouvelle méthode BYOK (préversion)](hsm-protected-keys-vendor-agnostic-byok.md)|
|nCipher|<ul><li>Famille nShield de modules HSM</li></ul>|[Utiliser la méthode BYOK héritée](hsm-protected-keys-legacy.md)|


## <a name="next-steps"></a>Étapes suivantes

Appliquez les [bonnes pratiques relatives à Key Vault](key-vault-best-practices.md) pour assurer la sécurité, la durabilité et la supervision de vos clés.
