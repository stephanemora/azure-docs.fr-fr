---
title: Génération et transfert de clés protégées par HSM pour Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Utilisez les informations présentes dans cette rubrique pour planifier, générer, puis transférer vos propres clés protégées par le module de sécurité matériel à utiliser avec le coffre de clés Azure. Cette méthode est également appelée BYOK (Bring Your Own Key, ou apportez votre propre clé).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 048e5072c592cf2de32e533014c99034572a1c47
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082895"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importer des clés protégées par HSM dans un coffre de clés

Pour une meilleure garantie, lorsque vous utilisez le coffre de clés Azure, vous pouvez importer ou générer des clés dans des modules de sécurité matériels (HSM) qui ne franchissent jamais les limites HSM. Ce scénario est souvent appelé *Apportez votre propre clé*ou désigné par l’acronyme BYOK. Azure Key Vault utilise la famille nShield de modules HSM (FIPS 140-2 niveau 2 validé) de nCipher pour protéger vos clés.

Cette fonctionnalité n’est pas disponible pour Azure Chine 21Vianet.

> [!NOTE]
> Pour plus d’informations sur le coffre de clés Azure, consultez la page [Présentation du coffre de clés Azure](key-vault-overview.md)  
> Pour voir un didacticiel de mise en route incluant un coffre de clés pour les clés protégées par HSM, consultez la section [ Présentation d’Azure Key Vault](key-vault-overview.md).

## <a name="supported-hsms"></a>Modules HSM pris en charge

Le transfert de clés protégées par HSM vers un coffre de clés est pris en charge par deux méthodes. La méthode à employer dépend des modules HSM que vous utilisez. Utilisez le tableau ci-dessous pour déterminer la méthode à utiliser pour vos modules HSM afin de générer, puis transférer vos propres clés protégées par HSM, que vous utiliserez avec Azure Key Vault. 

|Nom du fournisseur|Type de fournisseur|Modèles HSM pris en charge|Méthode de transfert de clé HSM prise en charge|
|---|---|---|---|
|nCipher|Fabricant|<ul><li>Famille nShield de modules HSM</li></ul>|[Utiliser la méthode BYOK héritée](hsm-protected-keys-legacy.md)|
|Thales|Fabricant|<ul><li>Famille SafeNet Luna HSM 7 avec la version de microprogramme 7.3 ou une version ultérieure</li></ul>| [Utiliser la nouvelle méthode BYOK (préversion)](hsm-protected-keys-vendor-agnostic-byok.md)|
|Fortanix|HSM en tant que service|<ul><li>Service de gestion de clés à protection automatique (SDKMS)</li></ul>|[Utiliser la nouvelle méthode BYOK (préversion)](hsm-protected-keys-vendor-agnostic-byok.md)|










## <a name="next-steps"></a>Étapes suivantes

Appliquez les [bonnes pratiques relatives à Key Vault](key-vault-best-practices.md) pour assurer la sécurité, la durabilité et la supervision de vos clés.
