---
title: Définir des partenaires commerciaux pour les workflows
description: Ajoutez des partenaires commerciaux à votre compte d’intégration pour les workflows dans Azure Logic Apps à l’aide de l’Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/16/2021
ms.openlocfilehash: f0e0fed5bf7e3354cffa8d6799c4d3e5d39595d9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128549832"
---
# <a name="add-trading-partners-to-integration-accounts-for-workflows-in-azure-logic-apps"></a>Ajouter des partenaires commerciaux à des comptes d’intégration pour les workflows dans Azure Logic Apps

Pour représenter votre organisation et d’autres dans les workflows d’intégration d’entreprise B2B, créez un *partenaire commercial* dans votre [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) pour représenter chaque participant dans une relation commerciale. Les partenaires sont des entités professionnelles qui participent aux transactions B2B et qui échangent des messages entre eux.

> [!IMPORTANT]
> Avant de définir ces partenaires, contactez vos partenaires pour déterminer comment identifier et valider les messages que vous vous envoyez. Pour participer à un accord et échanger des messages entre eux, les partenaires de votre compte d’intégration doivent utiliser les mêmes *qualificateurs d’entreprise* ou des qualificateurs compatibles. Après vous être mis d’accord sur ces détails, vous êtes prêt à créer des partenaires dans votre compte d’intégration.

Cet article explique la procédure de création et de gestion des partenaires, que vous pouvez utiliser ultérieurement pour créer des accords qui définissent le protocole standard spécifique pour l’échange de messages entre partenaires.

Si vous débutez avec les applications logiques, voir [Qu’est-ce qu’Azure Logic Apps](logic-apps-overview.md) ? Pour plus d’informations sur l’intégration d’entreprise B2B, consultez [Workflows d’intégration d’entreprise B2B avec Azure Logic Apps](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource de compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) dans laquelle vous définissez et stockez les artefacts, comme les partenaires commerciaux, les contrats, les certificats, etc., à utiliser dans vos workflows d’intégration d’entreprise et B2B. Cette ressource doit remplir les conditions suivantes :

  * Associé au même abonnement Azure que votre ressource d’application logique.

  * Existe dans le même emplacement ou la même région Azure que votre ressource d’application logique.

  * Si vous utilisez le [type de ressource **Application logique (Consommation)** ](logic-apps-overview.md#resource-type-and-host-environment-differences), votre compte d’intégration requiert un [lien vers votre ressource d’application logique](logic-apps-enterprise-integration-create-integration-account.md#link-account) avant de pouvoir utiliser des artefacts dans votre workflow.

  * Si vous utilisez le [type de ressource **Application logique (Standard)** ](logic-apps-overview.md#resource-type-and-host-environment-differences), votre compte d’intégration n’a pas besoin d’un lien vers votre ressource d’application logique, mais il est toujours nécessaire pour stocker d’autres artefacts, comme les partenaires, contrats et certificats, ainsi que pour utiliser les opérations [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) et [EDIFACT](logic-apps-enterprise-integration-edifact.md). Votre compte d’intégration doit toujours répondre à d’autres exigences, comme l’utilisation du même abonnement Azure et la présence dans le même emplacement que votre ressource d’application logique.

  > [!NOTE]
  > Actuellement, seul le type de ressource **Application logique (Consommation)** prend en charge les opérations [RosettaNet](logic-apps-enterprise-integration-rosettanet.md). Le type de ressource **Application logique (Standard)** n’inclut pas les opérations [RosettaNet](logic-apps-enterprise-integration-rosettanet.md).

<a name="add-partner"></a>

## <a name="add-a-partner"></a>Ajouter un partenaire

1. Dans la zone de recherche du [portail Azure](https://portal.azure.com), entrez `integration accounts` et sélectionnez **Comptes d’intégration**.

1. Sous **Comptes d’intégration**, sélectionnez le compte d’intégration dans lequel vous souhaitez ajouter vos partenaires.

1. Dans le menu du compte d’intégration, sous **Paramètres**, sélectionnez **Partenaires**.

1. Dans le volet **Partenaires**, sélectionnez **Ajouter**.

1. Dans le volet **Ajouter un partenaire** , fournissez les informations suivantes sur le partenaire :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom** | Oui | Nom du partenaire |
   | **Qualificateur** | Oui | Corps d’authentification qui fournit des identités d’entreprise uniques aux organisations, par exemple **D-U-N-S (Dun & Bradstreet)** . <p>Les partenaires peuvent opter pour une identité d’entreprise définie mutuellement. Pour ces scénarios, sélectionnez **Défini mutuellement** pour EDIFACT ou **Défini mutuellement (X12)** pour X12. <p>Pour RosettaNet, sélectionnez uniquement **DUNS**, qui est la norme. <p>**Important** : pour que les partenaires de votre compte d’intégration puissent participer à un accord et échanger des messages entre eux, ils doivent utiliser le même qualificateur ou un qualificateur compatible. |
   | **Valeur** | Oui | Valeur qui identifie les documents que vos applications logiques reçoivent. <p>Pour que les partenaires utilisent RosettaNet, cette valeur doit être un nombre à neuf chiffres qui correspond au numéro DUNS. Vous pouvez fournir plus d’informations pour les partenaires RosettaNet, par exemple leur classification et leurs coordonnées, en créant d’abord les partenaires, puis en [modifiant ensuite leurs définitions](#edit-partner). |
   ||||

1. Quand vous avez terminé, sélectionnez **OK**.

   Votre partenaire apparaît maintenant dans la liste **Partenaires**.

<a name="edit-partner"></a>

## <a name="edit-a-partner"></a>Modifier un partenaire

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre compte d’intégration.

1. Dans le menu du compte d’intégration, sous **Paramètres**, sélectionnez **Partenaires**.

1. Dans le volet **Partenaires** , sélectionnez le partenaire, sélectionnez **Modifier**, puis apportez vos modifications.

   Pour les partenaires qui utilisent RosettaNet, sous **Propriétés du partenaire RosettaNet**, vous pouvez fournir plus d’informations, comme décrit dans le tableau suivant :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Classification partenaire** | Non | Type d’organisation du partenaire |
   | **Code de la chaîne d’approvisionnement** | Non | Code de la chaîne d’approvisionnement du partenaire, par exemple « Technologies de l’information » ou « Composants électroniques » |
   | **Nom du contact** | Non | Nom de contact du partenaire |
   | **E-mail** | Non | Adresse e-mail du partenaire |
   | **Fax** | Non | Numéro de télécopie du partenaire |
   | **Téléphone** | Non | Numéro de téléphone du partenaire |
   ||||

1. Quand vous avez terminé, sélectionnez **OK**.

<a name="delete-partner"></a>

## <a name="delete-a-partner"></a>Supprimer un partenaire

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre compte d’intégration.

1. Dans le menu du compte d’intégration, sous **Paramètres**, sélectionnez **Partenaires**.

1. Dans le volet **Partenaires** , sélectionnez le partenaire à supprimer, puis sélectionnez **Supprimer**.

1. Sélectionnez **Oui** pour confirmer que vous souhaitez supprimer le partenaire.

## <a name="next-steps"></a>Étapes suivantes

* [Ajouter des accords entre partenaires](logic-apps-enterprise-integration-agreements.md)