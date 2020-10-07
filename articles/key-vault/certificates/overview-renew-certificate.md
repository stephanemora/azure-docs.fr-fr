---
title: À propos du renouvellement des certificats Azure Key Vault
description: Cet article explique comment renouveler des certificats Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 3809fa9e1ce17a5a0c3cf333ac20ef543db4b5a7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "88588801"
---
# <a name="renew-your-azure-key-vault-certificates"></a>Renouveler des certificats Azure Key Vault

Avec Azure Key Vault, vous pouvez facilement provisionner, gérer et déployer des certificats numériques pour votre réseau, et sécuriser les communications pour vos applications. Pour plus d’informations sur les certificats, consultez [À propos des certificats Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates).

En utilisant des certificats à courte durée de vie ou en augmentant la fréquence de rotation des certificats, vous pouvez empêcher les utilisateurs non autorisés d’accéder à vos applications.

Cet article explique comment renouveler vos certificats Azure Key Vault.

## <a name="get-notified-about-certificate-expirations"></a>Recevoir des notifications concernant les expirations de certificats
Pour recevoir une notification lorsque vos certificats sont sur le point d’expirer, effectuez les étapes suivantes :

Tout d’abord, ajoutez un contact de certificat à votre coffre de clés à l’aide de l’applet de commande PowerShell [Add-AzureKeyVaultCertificateContact](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact?view=azurermps-6.13.0).

Ensuite, configurez le moment où vous souhaitez être averti de l’expiration du certificat. Pour configurer les attributs de cycle de vie du certificat, consultez [Configurer la rotation automatique d’un certificat dans Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate).

Dans Key Vault, il existe trois catégories de certificats :
-   Certificats créés à l’aide d’une autorité de certification intégrée, telle que DigiCert ou GlobalSign
-   Certificats créés avec une autorité de certification non intégrée
-   Certificats auto-signés

## <a name="renew-an-integrated-ca-certificate"></a>Renouveler un certificat d’une autorité de certification intégrée 
Azure Key Vault gère la maintenance de bout en bout des certificats émis par les autorités de certification Microsoft approuvées DigiCert et GlobalSign. Découvrez comment [intégrer une autorité de certification approuvée avec Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority).

## <a name="renew-a-nonintegrated-ca-certificate"></a>Renouveler un certificat d’autorité de certification non intégrée 
En utilisant Azure Key Vault, vous pouvez importer des certificats à partir de n’importe quelle autorité de certification, ce qui vous permet de bénéficier d’une intégration avec plusieurs ressources Azure et de simplifier le déploiement. Si vous vous inquiétez de perdre la trace des dates d’expiration de vos certificats ou, pire, si vous avez découvert qu’un certificat a déjà expiré, votre coffre de clés peut vous aider à rester à jour. Pour les certificats d’autorité de certification non intégrée, le coffre de clés vous permet de configurer des notifications par e-mail juste avant l’expiration. Ces notifications peuvent également être définies pour plusieurs utilisateurs.

> [!IMPORTANT]
> Un certificat est un objet avec version. Si la version actuelle arrive à expiration, vous devez créer une nouvelle version. Conceptuellement, chaque nouvelle version est un nouveau certificat composé d’une clé et d’un objet blob qui lie cette clé à une identité. Lorsque vous utilisez une autorité de certification non partenaire, le coffre de clés génère une paire clé/valeur et retourne une demande de signature de certificat (CSR).

Pour renouveler un certificat d’autorité de certification non intégrée, effectuez les étapes suivantes :

1. Connectez-vous au portail Azure, puis ouvrez le certificat que vous souhaitez renouveler.
1. Dans le volet du certificat, sélectionnez **Nouvelle version**.
1. Sélectionnez **Opération de certificat**.
1. Sélectionnez **Télécharger CSR** pour télécharger un fichier CSR sur votre disque local.
1. Envoyez la demande de signature de certificat à l’autorité de certification de votre choix pour signer la demande.
1. Ramenez la demande signée et sélectionnez **Fusionner CSR** dans le même volet d’opération de certificat.

> [!NOTE]
> Il est important de fusionner la demande de signature de certificat signée avec celle que vous avez créée, sinon la clé ne correspondra pas.

Pour plus d’informations sur la création d’une nouvelle demande de signature de certificat, consultez [Création et fusion d’une demande de signature de certificat dans Key Vault]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal).

## <a name="renew-a-self-signed-certificate"></a>Renouveler un certificat auto-signé

Azure Key Vault gère également le renouvellement automatique des certificats auto-signés. Pour en savoir plus sur la modification de la stratégie d’émission et sur la mise à jour des attributs de cycle de vie d’un certificat, consultez [Configurer la rotation automatique d’un certificat dans Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate).

## <a name="troubleshoot"></a>Dépanner
Si le certificat émis est à l’état *Désactivé* dans le portail Azure, accédez à **Opération de certificat** pour afficher le message d’erreur du certificat.

## <a name="frequently-asked-questions"></a>Forum aux questions

**Comment faire pour tester la fonctionnalité de rotation automatique du certificat ?**

Créez un certificat avec une validité de **1 mois**, puis définissez l’action de durée de vie pour la rotation à **1 %** . Avec ce paramètre, il y aura une rotation du certificat toutes les 7,2 heures.
  
**Les étiquettes seront-elles répliquées après le renouvellement automatique du certificat ?**

Oui, les étiquettes sont répliquées après le renouvellement automatique.

## <a name="next-steps"></a>Étapes suivantes
*   [Intégrer Key Vault à l’autorité de certification DigiCert](how-to-integrate-certificate-authority.md)
*   [Tutoriel : Configurer la rotation automatique d’un certificat dans Key Vault](tutorial-rotate-certificates.md)
