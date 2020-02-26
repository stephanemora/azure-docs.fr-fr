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
ms.openlocfilehash: 2f5269587d222be9a1628b72c1f3f0dc1b105f3c
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461740"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Importer des clés protégées par HSM dans Key Vault (préversion)

> [!NOTE]
> Cette fonctionnalité est en préversion et n’est disponible que dans les régions **EUAP USA Est 2** et **EUAP USA Centre**. 

Pour une meilleure garantie, quand vous utilisez Azure Key Vault, vous pouvez importer ou générer des clés dans des modules de sécurité matériels (HSM) qui ne franchissent jamais les limites HSM. Ce scénario est souvent appelé *Apportez votre propre clé*ou désigné par l’acronyme BYOK. Azure Key Vault utilise la famille nShield de modules HSM (FIPS 140-2 niveau 2 validé) de nCipher pour protéger vos clés.

Les informations de cette rubrique vous aident à planifier, à générer puis transférer vos propres clés protégées par HSM à utiliser avec Azure Key Vault.

> [!NOTE]
> Cette fonctionnalité n’est pas disponible pour Azure Chine 21Vianet. 
> 
> Cette méthode d’importation n’est disponible que pour les [modules HSM pris en charge](#supported-hsms). 

Pour plus d’informations sur le coffre de clés Azure, consultez la page [Présentation du coffre de clés Azure](key-vault-overview.md)  Pour voir un didacticiel de mise en route incluant un coffre de clés pour les clés protégées par HSM, consultez la section [ Présentation d’Azure Key Vault](key-vault-overview.md).

## <a name="overview"></a>Vue d’ensemble

* Générez une clé KEK (Key Exchange Key) dans le coffre de clés. Il doit s’agir d’une clé RSA-HSM avec « import » comme seule opération de clé. Seule la référence SKU Premium du coffre de clés prend en charge les clés RSA-HSM.
* Télécharger la clé KEK publique sous forme de fichier .pem
* Transférez la clé KEK publique à votre station de travail hors connexion connectée à un module HSM local.
* À partir de votre station de travail hors connexion, utilisez l’outil BYOK fourni par votre fournisseur de modules HSM pour créer un fichier BYOK. 
* La clé cible est chiffrée à l’aide d’une clé KEK, qui reste chiffrée jusqu’à ce qu’elle soit transférée vers les modules HSM du coffre de clés Azure. Seule la version chiffrée de la clé quitte le module HSM local.
* La clé KEK générée dans les modules HSM du coffre de clés Azure et n’est pas exportable. Les modules HSM garantissent qu’aucune version en clair de la clé KEK ne se trouve hors des modules HSM du coffre de clés.
* La clé KEK doit se trouver dans le coffre de clés dans lequel la clé cible doit être importée.
* Quand le fichier BYOK est chargé sur le coffre de clés, les modules HSM du coffre de clés utilisent la clé KEK privée pour déchiffrer les éléments de la clé cible et l’importer comme clé HSM. Cette opération se produit entièrement dans les modules HSM du coffre de clés et la clé cible reste toujours dans la limite de protection des modules HSM.

## <a name="prerequisites"></a>Prérequis

Consultez le tableau qui suit pour connaître les conditions requises pour apporter votre propre clé (BYOK) dans le coffre de clés Azure.

| Condition requise | Informations complémentaires |
| --- | --- |
| Abonnement à Azure |Pour créer un coffre de clés Azure, vous avez besoin d’un abonnement Azure : [Inscrivez-vous pour un essai gratuit](https://azure.microsoft.com/pricing/free-trial/) |
| Coffre de clés (référence SKU Premium) pour l’importation de clés protégées par HSM |Pour plus d’informations sur les niveaux de service et les capacités du coffre de clés Azure, consultez le site web [Tarifs Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Module HSM figurant dans la liste des modules HSM pris en charge avec l’outil BYOK et les instructions fournies par votre fournisseur de modules HSM | Vous devez disposer d’un accès au module de sécurité matérielle et de quelques connaissances de base concernant vos modules HSM. Consultez [Modules HSM pris en charge](#supported-hsms). |
| Azure CLI version 2.1.0 ou ultérieure | Pour plus d’informations, consultez [Installer Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).|

## <a name="supported-hsms"></a>Modules HSM pris en charge

|Nom du fournisseur de modules HSM|Modèles HSM pris en charge|Informations supplémentaires|
|---|---|---|
|Thales|Famille SafeNet Luna HSM 7 avec la version de microprogramme 7.3 ou une version ultérieure| [Outil BYOK SafeNet Luna et documentation](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|


> [!NOTE]
> Pour importer des clés protégées par HSM à partir de la famille nShield de modules HSM de nCipher, [utilisez la procédure BYOK héritée](hsm-protected-keys-legacy.md).


## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Générez et transférez votre clé sur le module de sécurité matériel du coffre de clés Azure

Pour générer et transférer votre clé vers un module HSM du coffre de clés Azure, suivez les étapes ci-après :

* [Étape 1 : Générer une clé KEK](#step-1-generate-a-kek)
* [Étape 2 : Télécharger la clé KEK publique](#step-2-download-kek-public-key)
* [Étape 3 : Générer votre clé et la préparer pour le transfert](#step-3-generate-and-prepare-your-key-for-transfer)
* [Étape 4 : Transférer votre clé vers Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Étape 1 : Générer une clé KEK

La clé KEK (Key Exchange Key) est une clé RSA générée dans le module HSM du coffre de clés. Cette clé est utilisée pour chiffrer la clé à importer (clé cible).

La clé KEK doit être :
1. Une clé **RSA-HSM** (2 048, 3 072 ou 4 096 bits)
2. Générée dans le coffre de clés dans lequel vous envisagez d’importer la clé cible
3. Créée avec les opérations de clé autorisées définies sur **import**

Pour créer une clé KEK avec les opérations de clé définies sur import, utilisez la commande [az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create). Notez l’identificateur de clé « kid » retourné à partir de la commande ci-dessous. Vous en aurez besoin à l’[étape 3](#step-3-generate-and-prepare-your-key-for-transfer).


```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-kek-public-key"></a>Étape 2 : Télécharger la clé KEK publique

Utilisez la commande [az keyvault key download](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) pour télécharger la clé KEK publique dans un fichier .pem. La clé cible que vous importez est chiffrée à l’aide de la clé KEK publique.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Transférez le fichier KEKforBYOK.publickey.pem sur votre station de travail hors connexion. Vous aurez besoin de ce fichier à l’étape suivante.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Étape 3 : Générer votre clé et la préparer pour le transfert

Reportez-vous à la documentation de votre fournisseur de modules HSM pour télécharger et installer l’outil BYOK. Suivez les instructions de votre fournisseur de modules HSM pour générer une clé cible, puis créez un package de transfert de clé (fichier BYOK). L’outil BYOK utilise l’identificateur de clé obtenu à l’[étape 1](#step-1-generate-a-kek) et le fichier KEKforBYOK.publickey.pem que vous avez téléchargé à l’[étape 2](#step-2-download-kek-public-key) pour générer une clé cible chiffrée dans un fichier BYOK.

Transférez le fichier BYOK sur votre station de travail connectée.

> [!NOTE] 
> La clé cible doit être une clé RSA d’une taille de 2 048, 3 072 ou 4 096 bits. L’importation de clés à courbe elliptique n’est pas prise en charge pour l’instant.
> <br/><strong>Problème connu :</strong> échec de l’importation d’une clé cible RSA 4K à partir de modules HSM SafeNet Luna. Ce document sera mis à jour quand le problème sera résolu.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Étape 4 : Transférer votre clé vers Azure Key Vault

Pour cette dernière étape, transférez le package de transfert de clé (fichier BYOK) de la station de travail déconnectée vers la station de travail connectée à Internet, puis utilisez la commande [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) pour charger le fichier BYOK vers le module HSM du coffre de clés Azure afin de terminer l’importation de la clé.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Si le chargement réussit, les propriétés de la clé que vous venez d’importer s’affichent.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez maintenant utiliser cette clé protégée HSM dans votre coffre de clés. Pour plus d’informations, rendez-vous sur le prix et utilisez la fonctionnalité [comparaison](https://azure.microsoft.com/pricing/details/key-vault/).
