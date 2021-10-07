---
title: Ajouter des certificats pour sécuriser les messages B2B dans les workflows
description: Ajouter des certificats pour sécuriser les messages B2B pour les workflows dans Azure Logic Apps à l’aide du pack Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/23/2021
ms.openlocfilehash: e2d2bc58c5a523667eef62b21791e953701f7bc1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128550091"
---
# <a name="secure-messages-using-certificates-for-workflows-in-azure-logic-apps"></a>Sécuriser les messages à l’aide de certificats pour les flux de travail dans Azure Logic Apps

Lorsque vous devez échanger des messages confidentiels dans un workflow B2B (Business-to-Business) d’application logique, vous pouvez augmenter la sécurité de cette communication à l’aide de certificats. Un certificat est un document numérique qui permet de sécuriser la communication à l’aide des manières suivantes :

* Vérifie les identités des participants dans des communications électroniques.

* Chiffre le contenu des messages.

* Signe numériquement des messages.

Vous pouvez utiliser les types de certificat suivants dans vos flux de travail :

* [Certificats publics](https://en.wikipedia.org/wiki/Public_key_certificate), que vous devez acheter auprès d’une [autorité de certification (AC)](https://en.wikipedia.org/wiki/Certificate_authority). Ces certificats ne nécessitent aucune clé.

* Certificats privés ou [*certificats auto-signés*](https://en.wikipedia.org/wiki/Self-signed_certificate), que vous créez et émettez vous-même. Toutefois, ces certificats requièrent des clés privées.

Si vous débutez avec les applications logiques, voir [Qu’est-ce qu’Azure Logic Apps](logic-apps-overview.md) ? Pour plus d’informations sur l’intégration d’entreprise B2B, consultez [Solutions d’intégration d’entreprise B2B avec Azure Logic Apps et Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource de compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) dans laquelle vous définissez et stockez les artefacts, comme les parties, les contrats, les certificats, etc., à utiliser dans vos flux de travail d’intégration d’entreprise et B2B. Cette ressource doit remplir les conditions suivantes :

  * Associé au même abonnement Azure que votre ressource d’application logique.

  * Existe dans le même emplacement ou la même région Azure que votre ressource d’application logique.

  * Si vous utilisez le [**type de ressource Application logique (Consommation)** ](logic-apps-overview.md#resource-type-and-host-environment-differences), vous devez [lier votre compte d’intégration à votre ressource d’application logique](logic-apps-enterprise-integration-create-integration-account.md#link-account) avant de pouvoir utiliser vos artefacts dans votre workflow.

    Pour l’instant, vous n’avez pas besoin d’une ressource d’application logique pour créer et ajouter des certificats à utiliser dans les workflows **d’application logique (Consommation)** . Toutefois, lorsque vous êtes prêt à utiliser ces certificats dans vos workflows, votre ressource d’application logique requiert un compte d’intégration lié pour les stocker.

  * Si vous utilisez le [type de ressource **Application logique (Standard)** ](logic-apps-overview.md#resource-type-and-host-environment-differences), votre compte d’intégration n’a pas besoin d’un lien vers votre ressource d’application logique, mais il est toujours nécessaire pour stocker d’autres artefacts, comme les partenaires, contrats et certificats, ainsi que pour utiliser les opérations [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) et [EDIFACT](logic-apps-enterprise-integration-edifact.md). Votre compte d’intégration doit encore répondre à d’autres exigences, telles que l’utilisation du même abonnement Azure et le même emplacement que votre ressource d’application logique.

    > [!NOTE]
    > Actuellement, seul le type de ressource **Application logique (Consommation)** prend en charge les opérations [RosettaNet](logic-apps-enterprise-integration-rosettanet.md). Le type de ressource **Application logique (Standard)** n’inclut pas les opérations [RosettaNet](logic-apps-enterprise-integration-rosettanet.md).

* Pour les certificats privés, vous devez remplir les conditions préalables suivantes :

  * Ajoutez une clé privée dans [Azure Key Vault](../key-vault/general/overview.md) et utilisez le **Nom de la clé**. Pour plus d’informations, consultez [Ajouter votre clé privée à Azure Key Vault](../key-vault/certificates/certificate-scenarios.md#import-a-certificate).

  * Autorisez le service Azure Logic Apps à effectuer des opérations sur votre coffre de clés. Pour accorder l’accès au principal de service Azure Logic Apps, utilisez la commande PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), par exemple :

    `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`

    [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

  * [Ajoutez un certificat public correspondant](#add-public-certificate) à votre coffre de clés. Ce certificat apparaît dans les [paramètres d'**envoi** et de **réception** de votre accord pour la signature et le chiffrement des messages](logic-apps-enterprise-integration-agreements.md). Par exemple, consultez les informations [Référence pour les paramètres des messages AS2 dans Azure Logic Apps](logic-apps-enterprise-integration-as2-message-settings.md).

* Au moins deux [partenaires commerciaux](logic-apps-enterprise-integration-partners.md) et un [accord entre ces partenaires](logic-apps-enterprise-integration-agreements.md) dans votre compte d’intégration. Un accord requiert un partenaire hôte et un partenaire invité. En outre, un accord requiert que les deux partenaires utilisent le même qualificateur d'*identité d’entreprise* ou un qualificateur compatible et approprié pour un accord AS2, X12, EDIFACT ou RosettaNet.

* Il peut aussi s’agir de la ressource d’application logique et le flux de travail dans lesquels vous souhaitez utiliser le certificat. Le workflow nécessite un déclencheur qui démarre le workflow de votre application logique. Si vous n’avez pas encore créé de flux de travail d’application logique, consultez [Démarrage rapide : Créer votre premier workflow d’application logique](quickstart-create-first-logic-app-workflow.md).

<a name="add-public-certificate"></a>

## <a name="add-a-public-certificate"></a>Ajouter un certificat public

Pour utiliser un *certificat public* dans votre workflow, vous devez d’abord ajouter le certificat à votre compte d’intégration.

1. Dans la zone de recherche du [portail Azure](https://portal.azure.com), entrez `integration accounts` et sélectionnez **Comptes d’intégration**.

1. Sous **Comptes d’intégration**, sélectionnez le compte d’intégration dans lequel vous souhaitez ajouter le certificat.

1. Dans le menu du compte d’intégration, sous **Paramètres**, sélectionnez **Certificats**.

1. Dans le volet **Certificats** , sélectionnez **Ajouter**.

1. Dans le volet **Ajouter un certificat**, fournissez les informations suivantes sur le certificat :

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **Nom** | Oui | <*certificate-name*> | Le nom de votre certificat, `publicCert` dans cet exemple |
   | **Type de certificat** | Oui | **Public** | Le type de votre certificat |
   | **Certificate** | Oui | <*certificate-file-name*> | Pour rechercher le fichier de certificat que vous souhaitez ajouter, sélectionnez l’icône de dossier en regard de la case **Certificat**. |
   |||||

   ![Capture d’écran montrant le Portail Azure et le compte d’intégration avec l’option « Ajouter » sélectionnée et le volet « Ajouter un certificat » avec les détails du certificat public.](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

1. Quand vous avez terminé, sélectionnez **OK**.

   Azure charge votre certificat après avoir validé votre choix.

   ![Capture d’écran montrant le Portail Azure et le compte d’intégration avec le certificat public dans la liste « Certificats ».](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png)

<a name="add-public-certificate"></a>

## <a name="add-a-private-certificate"></a>Téléchargement d’un certificat privé

Pour utiliser un *certificat privé* dans votre workflow, vous devez d’abord ajouter le certificat à votre compte d’intégration. Assurez-vous que vous avez également rempli les [conditions préalables requises pour les certificats privés](#prerequisites).

1. Dans la zone de recherche du [portail Azure](https://portal.azure.com), entrez `integration accounts` et sélectionnez **Comptes d’intégration**.

1. Sous **Comptes d’intégration**, sélectionnez le compte d’intégration dans lequel vous souhaitez ajouter le certificat.

1. Dans le menu du compte d’intégration, sous **Paramètres**, sélectionnez **Certificats**.

1. Dans le volet **Certificats** , sélectionnez **Ajouter**.

1. Dans le volet **Ajouter un certificat**, fournissez les informations suivantes sur le certificat :

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **Nom** | Oui | <*certificate-name*> | Le nom de votre certificat, `privateCert` dans cet exemple |
   | **Type de certificat** | Oui | **Privé** | Le type de votre certificat |
   | **Certificate** | Oui | <*certificate-file-name*> | Pour rechercher le fichier de certificat que vous souhaitez ajouter, sélectionnez l’icône de dossier en regard de la case **Certificat**. Dans le coffre de clés qui contient votre clé privée, le fichier que vous ajoutez est le certificat public. |
   | **Groupe de ressources** | Oui | <*integration-account-resource-group*> | Le groupe de ressources de votre compte d’intégration, `Integration-Account-RG` dans cet exemple |
   | **Key Vault** | Oui | <*key-vault-name*> | Nom de votre coffre de clés |
   | **Nom de la clé** | Oui | <*key-name*> | Nom de votre clé |
   |||||

   ![Capture d’écran montrant le Portail Azure et le compte d’intégration avec l’option « Ajouter » sélectionnée et le volet « Ajouter un certificat » avec les détails du certificat privé.](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

1. Quand vous avez terminé, sélectionnez **OK**.

   Azure charge votre certificat après avoir validé votre choix.

   ![Capture d’écran montrant le Portail Azure et le compte d’intégration avec le certificat privé dans la liste « Certificats ».](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png)

## <a name="next-steps"></a>Étapes suivantes

* [Échanger des messages AS2](logic-apps-enterprise-integration-as2.md)
* [Échanger des messages EDIFACT](logic-apps-enterprise-integration-edifact.md)
* [Échanger des messages X12](logic-apps-enterprise-integration-x12.md)
* [Messages Exchange RosettaNet](logic-apps-enterprise-integration-rosettanet.md)