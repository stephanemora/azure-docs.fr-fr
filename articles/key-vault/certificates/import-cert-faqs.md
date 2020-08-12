---
title: Questions fréquentes (FAQ) – Importation de certificat Azure Key Vault
description: Questions fréquentes (FAQ) – Importation de certificat Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 493c77a8f875018627bfe3167e66addeaf65d089
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87445778"
---
# <a name="frequently-asked-questions---azure-key-vault-certificate-import"></a>Questions fréquentes (FAQ) – Importation de certificat Azure Key Vault

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Comment importer un certificat dans Azure Key Vault ?

Importer un certificat – Pour une opération d’importation, Azure Key Vault accepte deux formats de certificat, PEM et PFX. Bien qu’il existe des fichiers PEM avec uniquement la partie publique, Azure Key Vault requiert et accepte uniquement un fichier PEM ou PFX dans un dossier de fichiers et avec une clé privée. Suivez le [tutoriel pour importer un certificat](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-import-certificate#import-a-certificate-to-key-vault).

### <a name="after-importing-password-protected-certificate-into-the-key-vault-and-then-downloading-it-i-am-not-able-to-see-the-password-associated-with-the-certificate"></a>Après avoir importé un certificat protégé par mot de passe dans le coffre de clés, puis l’avoir téléchargé, je ne peux pas voir le mot de passe associé au certificat.
    
Le certificat protégé chargé après le stockage dans le coffre de clés n’enregistre pas le mot de passe qui lui est associé. Cela n’est requis qu’une seule fois pendant l’opération d’importation. Bien qu’il s’agisse d’un concept de conception, vous pouvez toujours obtenir le certificat en tant que secret et convertir de Base64 en PFX en ajoutant le mot de passe précédent via [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

### <a name="how-can-i-resolve-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-in-key-vault"></a>Comment résoudre le problème « Erreur de paramètre incorrect » ? Quels sont les formats de certificat pris en charge pour l’importation dans Key Vault ?

Quand vous importez le certificat, vous devez vérifier que la clé est incluse dans le fichier. Si vous avez la clé privée ailleurs et dans un autre format, vous devez associer la clé au certificat. Certaines autorités de certification fournissent des certificats dans différents formats. Avant d’importer le certificat, vous devez donc vérifier qu’il est au format .pem ou .pfx, et que la clé utilisée est RSA ou ECC. Reportez-vous à ces rubriques pour passer en revue les [conditions requises des certificats](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#formats-of-import-we-support) et les [conditions requises relatives aux clés de certificat](https://docs.microsoft.com/azure/key-vault/keys/about-keys#cryptographic-protection).

### <a name="error-when-importing-certificate-via-portal-something-went-wrong-how-can-i-investigate-further"></a>Erreur « Un problème s’est produit » lors de l’importation du certificat via le portail. Comment approfondir les investigations ?
    
Pour voir une erreur plus descriptive, importez le fichier de certificat via [Azure CLI](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) ou [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0).

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Comment résoudre le problème « Type d’erreur : Accès refusé ou l’utilisateur n’est pas autorisé à importer un certificat » ?
    
Cette opération exige l’autorisation de certificates/d’importation. Accédez à l’emplacement du coffre de clés. Vous devrez accorder les autorisations appropriées à l’utilisateur sous Stratégies d’accès. Accédez à Coffre de clés > Stratégies d’accès > Ajouter une stratégie d’accès, sélectionnez Autorisations de certificat (ou l’option correspondant aux autorisations souhaitées) > Principal, recherchez l’adresse e-mail de l’utilisateur, puis ajoutez-la. [En savoir plus sur les stratégies d’accès associées aux certificats](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#certificate-access-control)


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Comment résoudre le problème « Type d’erreur : Conflit lors de la création d’un certificat » ?
    
Le nom du certificat doit être unique. Un certificat portant le même nom peut être dans un état de suppression réversible. En outre, lors de la [composition d’un certificat](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate) dans Azure Key Vault, s’il existe une autre clé ou un autre secret dans Key Vault portant le même nom que celui que vous essayez de spécifier pour votre certificat, l’opération échoue et vous devez supprimer cette clé ou ce secret, ou utiliser un autre nom pour votre certificat. [Visualisez le certificat supprimé](https://docs.microsoft.com/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate).

### <a name="why-am-i-getting-the-error-type-char-length-is-too-long"></a>Pourquoi l’erreur « Type d’erreur : la longueur des caractères est trop longue » s’affiche-t-elle ?
    
* La longueur du nom de l’objet du certificat est limitée à 200 caractères.
* La longueur du mot de passe du certificat est limitée à 200 caractères.

### <a name="can-i-import-an-expired-certificate-in-azure-key-vault"></a>Puis-je importer un certificat arrivé à expiration dans Azure Key Vault ?
    
Non, les certificats PFX arrivés à expiration ne peuvent pas être importés dans Azure Key Vault.

### <a name="how-can-i-convert-my-certificate-to-proper-format"></a>Comment puis-je convertir mon certificat au format approprié ?

Vous pouvez demander à votre autorité de certification de fournir le certificat au format requis. Il existe également des outils tiers qui peuvent vous aider à convertir le certificat au format approprié. Outre cela, Microsoft ne peut pas vous fournir d’autres conseils sur la façon d’obtenir le certificat au format souhaité.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>Puis-je importer des certificats d’autorités de certification non-partenaires ?
Oui, vous pouvez importer des certificats à partir de n’importe quelle autorité de certification, mais Key Vault ne pourra pas renouveler automatiquement ces certificats. Vous pouvez définir des notifications par e-mail pour être informé au sujet de l’expiration du certificat.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-auto-renew-feature-still-work"></a>Si j’importe un certificat d’une autorité de certification partenaire, la fonctionnalité de renouvellement automatique fonctionnera-t-elle toujours ?
Oui, vous devez vous assurer qu’une fois le téléchargement effectué, vous spécifiez la rotation automatique dans la stratégie d’émission du certificat. En outre, les modifications sont reflétées jusqu’à la version suivante du cycle ou du certificat.

### <a name="unable-to-see-the-app-service-certificate-imported-to-key-vault"></a>Vous ne parvenez pas à voir le certificat App Service Certificate importé dans Key Vault ? 
Si le certificat a été importé avec succès, vérifiez dans le panneau Secrets.


## <a name="next-steps"></a>Étapes suivantes

- [Certificats Azure Key Vault](/azure/key-vault/certificates/about-certificates)
