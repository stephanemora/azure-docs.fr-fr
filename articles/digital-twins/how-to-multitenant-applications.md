---
title: Applications multilocataires avec Azure Digital Twins | Microsoft Docs
description: Présentation de l’inscription des locataires Azure Active Directory de vos clients avec Azure Digital Twins
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: mavoge
ms.openlocfilehash: a2d9ece119003c341f49ee03d735d5636b179a32
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259885"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Applications multilocataires avec Azure Digital Twins

Les développeurs qui utilisent Azure Digital Twins souhaitent probablement créer des applications multilocataires. Une *application multilocataires* est une instance approvisionnée qui prend en charge plusieurs locataires. Chaque client possède ses propres données et privilèges.

Ce document explique en détail comment créer une application multilocataire Azure Digital Twins prenant en charge plusieurs locataires et clients Azure Active Directory (Azure AD).

## <a name="scenario-summary"></a>Résumé du scénario

Dans ce scénario, vous avez un développeur D et un client C :

- Le développeur D dispose d’un abonnement Azure avec un locataire Azure AD.
- Le développeur D a déployé une instance Azure Digital Twins dans son abonnement Azure.
- Les utilisateurs du locataire AD Azure du développeur D peuvent obtenir des jetons auprès du service Azure Digital Twins, puisqu’Azure AD a créé un principal de service dans le locataire Azure AD du développeur D.
- Le développeur D crée une application mobile qui s’intègre directement à l’API de gestion d’Azure Digital Twins.
- Le développeur D autorise le client C à utiliser l’application mobile.
- Le client C doit être autorisé à utiliser l’API de gestion Azure Digital Twins dans l’application du développeur D.

  > [!IMPORTANT]
  > - Lorsque le client C se connecte à l’application du développeur D, celle-ci ne peut plus acquérir de jetons permettant aux utilisateurs du client C de communiquer avec l’API de gestion.
  > - Azure AD génère une erreur indiquant qu’Azure Digital Twins na pas été reconnu dans l’annuaire du client C.

## <a name="solution"></a>Solution

Pour le scénario précédent, les actions suivantes doivent être effectuées afin de créer un principal de service Azure Digital Twins dans le locataire Azure AD du client C :

- Si le client C n’a pas encore d’abonnement Azure dans le locataire Azure AD :

  - L’administrateur de locataires Azure AD du client C doit acquérir un [abonnement Azure avec paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - L’administrateur de locataires Azure AD du client C doit ensuite [lier son locataire au nouvel abonnement](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

- Dans le [portail Azure](https://portal.azure.com), l’administrateur de locataires Azure AD du client C doit ensuite :

  1. Ouvrir **Abonnements**
  1. Sélectionner l’abonnement qui comprend le locataire Azure AD à utiliser dans l’application du développeur D.
  1. Sélectionner les **Fournisseurs de ressources**
  1. Rechercher **Microsoft.IoTSpaces**
  1. Sélectionnez **Inscription**.
  
## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de fonctions définies par l’utilisateur avec Azure Digital Twins, consultez [Fonctions définies par l’utilisateur Azure Digital Twins](how-to-user-defined-functions.md).

Pour savoir comment utiliser le contrôle d’accès en fonction du rôle dans le but de renforcer la sécurité de l’application avec des attributions de rôles, consultez [Contrôle d’accès en fonction du rôle Azure Digital Twins](security-create-manage-role-assignments.md).
