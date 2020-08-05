---
title: À propos du renouvellement des certificats Azure Key Vault
description: À propos du renouvellement des certificats Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: c6999b67a5c0a0f4ca7cb943ae8de3afd8b6a11e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87094207"
---
# <a name="about-azure-key-vault-certificate-renewal"></a>À propos du renouvellement des certificats Azure Key Vault

Azure Key Vault vous permet de provisionner, gérer et déployer facilement des certificats numériques pour votre réseau et de sécuriser les communications pour les applications. Pour plus d’informations générales sur les certificats, consultez [Certificats Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates).

Le fait d’avoir un certificat à courte durée de vie ou d’augmenter la fréquence de rotation du certificat limite la portée de l’adversaire pour d’éventuels dommages.

Il existe trois catégories de création de certificats dans Key Vault. Ce guide va vous aider à comprendre comment le renouvellement des certificats peut être réalisé.
-   Certificats créés avec l’autorité de certification intégrée (DigiCert ou GlobalSign)
-   Certificats créés avec une autorité de certification non intégrée
-   Certificats auto-signés

## <a name="renewal-of-integrated-ca-certificate"></a>Renouvellement d’un certificat d’une autorité de certification intégrée 
Bonne nouvelle ! Azure Key Vault gère la maintenance de bout en bout des certificats émis par les autorités de certification approuvées par Microsoft, telles que DigiCert et GlobalSign. Découvrez comment [intégrer une autorité de certification approuvée avec Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority).

## <a name="renewal-of-non-integrated-ca-certificate"></a>Renouvellement d’un certificat d’une autorité de certification non intégrée 
Azure Key Vault offre à ses utilisateurs l’avantage d’importer des certificats à partir de n’importe quelle autorité de certification pour permettre à ses utilisateurs de s’intégrer à plusieurs ressources Azure et de faciliter le déploiement. Si vous vous inquiétez de perdre la trace de votre certificat arrivant à expiration ou, pire, si vous avez découvert que votre certificat a déjà expiré, Key Vault peut vous aider à rester à jour. Dans le cas d’un certificat d’autorité de certification non intégrée, le coffre de clés permet à son utilisateur de configurer des notifications par e-mail d’expiration proche. Ces notifications peuvent également être définies pour plusieurs utilisateurs.

Pour renouveler un certificat, il est important de comprendre qu’un certificat Azure Key Vault est un objet versionné. Si la version actuelle arrive à expiration, vous devez créer une nouvelle version. Conceptuellement, chaque nouvelle version constitue globalement un nouveau certificat composé d’une clé et d’un objet blob qui lie cette clé à une identité. Lorsque vous utilisez une autorité de certification non partenaire, le coffre de clés génère une paire clé-valeur et retourne la demande de signature de certificat.

**Étapes à suivre dans le portail Azure :-**
1.  Ouvrez le certificat à renouveler.
2.  Sélectionnez le bouton **+ Nouvelle version** sur l’écran du certificat.
3.  Sélectionnez **Opération de certificat**.
4.  Sélectionnez **Télécharger CSR**. Cela permet de télécharger un fichier .csr sur votre disque local.
5.  Amenez la demande de signature de certificat jusqu’à votre choix d’autorité de certification pour signer la demande.
6.  Ramenez la demande signée et sélectionnez **Fusionner CSR** sur le même écran Opération de certificat.

> [!NOTE]
> Il est important de fusionner la demande de signature de certificat signée avec la même demande CSR qui a été créée ; sinon, la clé ne correspond pas.

Les étapes sont similaires à la création d’un nouveau certificat et sont documentées plus en détail [ici]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal).

## <a name="renewal-of-self-signed-certificate"></a>Renouvellement d’un certificat auto-signé

Autre bonne nouvelle ! Azure Key Vault prend également en charge le renouvellement automatique des certificats auto-signés pour ses utilisateurs. Pour en savoir plus sur le changement de la stratégie d’émission et la mise à jour des attributs d’action de la durée de vie d’un certificat, découvrez plus d’informations [ici](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate).

### <a name="troubleshoot"></a>Dépanner
Si le certificat émis est à l’état « désactivé » dans le portail Azure, examinez le message d’erreur pour ce certificat dans Opération de certificat.

### <a name="see-also"></a>Voir aussi
*   [Intégration de Key Vault à l’autorité de certification DigiCert](how-to-integrate-certificate-authority.md)
*   [Tutoriel : Configurer la rotation automatique d’un certificat dans Key Vault](tutorial-rotate-certificates.md)