---
title: Sécurisation de messages B2B à l’aide de certificats - Azure Logic Apps | Microsoft Docs
description: Ajouter des certificats pour sécuriser les messages B2B dans Azure Logic Apps avec le pack Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
manager: jeconnoc
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.suite: integration
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: 38bc1615c0849a33ddfa5790a66fc05d681ce339
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244927"
---
# <a name="secure-b2b-messages-with-certificates"></a>Sécuriser les messages B2B à l’aide de certificats

Lorsque vous avez besoin de préserver la confidentialité des communications B2B, vous pouvez les sécuriser pour les applications d’intégration de votre entreprise, notamment les applications logiques, en ajoutant des certificats à votre compte d’intégration. Les certificats sont des documents numériques qui vérifient l’identité des participants aux communications électroniques et permettent de sécuriser la communication des façons suivantes :

* Chiffrement du contenu du message.
* Signature numérique des messages. 

Vous pouvez utiliser ces certificats dans vos applications Enterprise Integration :

* [Certificats publics](https://en.wikipedia.org/wiki/Public_key_certificate), que vous devez acheter auprès d’une [autorité de certification (AC)](https://en.wikipedia.org/wiki/Certificate_authority), mais qui ne nécessite pas de clé. 

* Certificats privés ou [*certificats auto-signés*](https://en.wikipedia.org/wiki/Self-signed_certificate), que vous créez et émettez vous-même, mais qui nécessitent aussi des clés privées. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Téléchargement d’un certificat public

Pour utiliser un *certificat public* dans vos applications logiques avec fonctionnalités B2B, vous devez tout d’abord télécharger le certificat dans votre compte d’intégration. Une fois que vous avez défini les propriétés dans les [contrats](logic-apps-enterprise-integration-agreements.md) que vous créez, le certificat est disponible pour vous aider à sécuriser vos messages B2B.

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Dans le menu principal Azure, sélectionnez **Toutes les ressources**. Dans la zone de recherche, saisissez le nom de votre compte d’intégration, puis sélectionnez le compte d’intégration de votre choix.

   ![Trouvez et sélectionnez votre compte d’intégration](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. Sous **Composants**, choisissez la mosaïque **Certificats**.

   ![Choisir « Certificats »](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. Sous **Certificats**, choisissez **Ajouter**. Sous **Ajouter un certificat**, fournissez les détails de votre certificat. Une fois que vous avez terminé, sélectionnez **OK**.

   | Propriété | Valeur | Description | 
   |----------|-------|-------------|
   | **Nom** | <*certificate-name*> | Le nom de votre certificat, « publicCert » dans cet exemple | 
   | **Type de certificat** | Public | Le type de votre certificat |
   | **Certificate** | <*certificate-file-name*> | Pour trouver et sélectionner le fichier de certificat que vous souhaitez télécharger, cliquez sur l’icône de dossier en regard de la case **Certificat**. |
   ||||

   ![Sélectionnez « Ajouter », indiquez les détails du certificat.](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Azure charge votre certificat après avoir validé votre choix.

   ![Azure affiche le nouveau certificat](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Téléchargement d’un certificat privé

Pour utiliser un *certificat privé* dans vos applications logiques avec fonctionnalités B2B, vous devez tout d’abord télécharger le certificat dans votre compte d’intégration. Vous devez également disposer d’une clé privée que vous ajoutez d’abord à [Azure Key Vault](../key-vault/key-vault-get-started.md). 

Une fois que vous avez défini les propriétés dans les [contrats](logic-apps-enterprise-integration-agreements.md) que vous créez, le certificat est disponible pour vous aider à sécuriser vos messages B2B.

> [!NOTE]
> Pour les certificats privés, n’oubliez pas d’ajouter un certificat public correspondant qui apparaît dans les paramètres **Envoyer et Recevoir** du [contrat AS2](logic-apps-enterprise-integration-as2.md) pour la signature et le chiffrement des messages.

1. [Ajoutez votre clé privée dans Azure Key Vault](../key-vault/certificate-scenarios.md#import-a-certificate) et indiquez un **nom de clé**.
   
2. Vous devez autoriser Azure Logic Apps à effectuer des opérations sur Azure Key Vault. Pour accorder l’accès au principal de service Logic Apps, utilisez la commande PowerShell [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), par exemple :

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Connectez-vous au [Portail Azure](https://portal.azure.com). Dans le menu principal Azure, sélectionnez **Toutes les ressources**. Dans la zone de recherche, saisissez le nom de votre compte d’intégration, puis sélectionnez le compte d’intégration de votre choix.

   ![Recherche du compte d’intégration](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. Sous **Composants**, choisissez la mosaïque **Certificats**.  

   ![Choisir la mosaïque Certificats](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. Sous **Certificats**, choisissez **Ajouter**. Sous **Ajouter un certificat**, fournissez les détails de votre certificat. Une fois que vous avez terminé, sélectionnez **OK**.

   | Propriété | Valeur | Description | 
   |----------|-------|-------------|
   | **Nom** | <*certificate-name*> | Le nom de votre certificat, « privateCert » dans cet exemple | 
   | **Type de certificat** | Privé | Le type de votre certificat |
   | **Certificate** | <*certificate-file-name*> | Pour trouver et sélectionner le fichier de certificat que vous souhaitez télécharger, cliquez sur l’icône de dossier en regard de la case **Certificat**. | 
   | **Groupe de ressources** | <*integration-account-resource-group*> | Le groupe de ressources de votre compte d’intégration, « MyResourceGroup » dans cet exemple | 
   | **Key Vault** | <*key-vault-name*> | Le nom de votre coffre de clés Azure |
   | **Nom de la clé** | <*key-name*> | Le nom de votre clé |
   ||||

   ![Sélectionnez « Ajouter », indiquez les détails du certificat.](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Azure charge votre certificat après avoir validé votre choix.

   ![Azure affiche le nouveau certificat](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Étapes suivantes

* [Créer un contrat B2B](logic-apps-enterprise-integration-agreements.md)
