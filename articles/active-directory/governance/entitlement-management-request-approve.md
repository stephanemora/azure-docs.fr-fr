---
title: Approuver ou refuser les demandes d’accès dans la gestion des droits Azure AD (version préliminaire) - Azure Active Directory
description: Découvrez comment utiliser le portail de mon accès pour approuver ou refuser les demandes à un package d’accès dans la gestion des droits Azure Active Directory (version préliminaire).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b2d07638f6c6f153ee3640273fbee5e56df0ab2
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541524"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management-preview"></a>Approuver ou refuser les demandes d’accès dans la gestion des droits Azure AD (version préliminaire)

> [!IMPORTANT]
> Gestion des habilitations Azure Active Directory (Azure AD) est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Avec la gestion des droits Azure AD, vous pouvez configurer des stratégies pour exiger une approbation pour les packages de l’accès et choisissez un ou plusieurs approbateurs. Cet article décrit comment désignés approbateurs peuvent approuver ou refuser les demandes pour les packages de l’accès.

## <a name="open-request"></a>Demande d’ouverture

La première étape pour approuver ou refuser les demandes d’accès consiste à rechercher et ouvrir la demande d’accès en attente d’approbation. Il existe deux manières d’ouvrir la demande d’accès.

**Rôle :** Approbateur

1. Recherchez un message électronique à partir de Microsoft Azure vous demandant d’approuver ou refuser une demande. Voici un exemple d’e-mail :

    ![Approuver la demande à accéder à la messagerie de package](./media/entitlement-management-shared/email-approve-request.png)

1. Cliquez sur le **approuver ou refuser la demande** lien pour ouvrir la demande d’accès.

1. Connectez-vous au portail mon accès.

Si vous n’avez pas reçu l’e-mail, vous trouverez les demandes d’accès en attente d’approbation en suivant ces étapes.

1. Connectez-vous au portail mon accès à [ https://myaccess.microsoft.com ](https://myaccess.microsoft.com).

1. Dans le menu de gauche, cliquez sur **approbations** pour afficher la liste de demandes d’accès en attente d’approbation.

1. Sur le **en attente** , onglet de trouver la demande.

## <a name="approve-or-deny-request"></a>Approuver ou refuser la demande

Une fois que vous ouvrez une demande d’accès en attente d’approbation, vous pouvez voir les détails qui vous aideront à rendre un approuver ou refuser la décision.

**Rôle :** Approbateur

1. Cliquez sur le **vue** lien pour ouvrir le volet de requête d’accès.

1. Cliquez sur **détails** pour afficher les détails sur la demande d’accès.

    Les détails incluent le nom d’utilisateur, organisation, date de début et de fin s’il est fourni, justification professionnelle, lorsque la demande a été soumise et date d’expiration de la demande d’accès.

1. Cliquez sur **approuver** ou **refuser**.

1. Si nécessaire, entrez un motif.

    ![Mon portail d’accès - demande d’accès](./media/entitlement-management-shared/my-access-approve-request.png)

1. Cliquez sur **Submit** pour soumettre votre décision.

    Si une stratégie est configurée avec plusieurs approbateurs, qu’un seul approbateur doit prendre une décision concernant l’approbation en attente. Une fois un approbateur a envoyé leur décision à la demande d’accès, la demande est terminée et n’est plus disponible pour les autres approbateurs approuver ou refuser la demande. Les autres approbateurs peuvent voir la décision de demande et le preneur de décision dans leur portail mon accès. À ce stade, uniquement l’approbation d’une étape est pris en charge.

    Si aucun des approbateurs configurés ne peut approuver ou refuser la demande d’accès, la demande expire au bout de la durée configurée de la requête. L’utilisateur est averti que leur demande d’accès a expiré et qu’ils doivent resoumettre la demande d’accès.

## <a name="next-steps"></a>Étapes suivantes

- [Demander l’accès à un package d’accès](entitlement-management-request-access.md)
- [Demande de processus et les notifications électroniques](entitlement-management-process.md)
