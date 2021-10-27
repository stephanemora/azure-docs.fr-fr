---
title: Transfert d’un abonnement Azure vers un Contrat Entreprise
description: Cet article explique la procédure de transfert d’un Contrat client Microsoft ou d’un abonnement MOSP vers un Contrat Entreprise.
author: bandersmsft
ms.reviewer: jatracey
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/12/2021
ms.author: banders
ms.custom: ''
ms.openlocfilehash: 4350a36c63e8ea6be4284baeaba3a60d1de9099b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007454"
---
# <a name="transfer-an-azure-subscription-to-an-enterprise-agreement-ea"></a>Transfert d’un abonnement Azure vers un Contrat Entreprise (EA, Enterprise Agreement)

Cet article explique la procédure à suivre pour transférer un Contrat client Microsoft ou un abonnement MOSP (paiement à l'utilisation) vers un contrat EA. Le transfert n’implique pas de temps d’arrêt, mais il existe de nombreuses étapes à suivre pour le mener à bien.

> [!NOTE]
> Le processus de transfert ne change pas les informations relatives aux répertoires Azure AD auxquels les abonnements sont liés. Si vous souhaitez changer de répertoire Azure AD, consultez [Transfert d’un abonnement Azure vers un autre répertoire Azure AD](../../role-based-access-control/transfer-subscription.md).

## <a name="prerequisites"></a>Prérequis

Les sections suivantes vous permettent d’identifier l’administrateur de compte de votre abonnement et l’accès requis au contrat EA de destination, et de définir le niveau d’authentification EA.

### <a name="identify-the-subscription-account-admin"></a>Identification de l’administrateur de compte de l’abonnement

Identifiez l’administrateur de compte de l’abonnement en vous connectant au Portail Azure avec un compte doté au moins du rôle de lecteur RBAC Azure dans l’abonnement. Accédez ensuite à **Abonnements** et sélectionnez l’abonnement, puis **Propriétés**. **L’administrateur de compte** de l’abonnement s’affiche. Prenez note des informations sur l’utilisateur.

:::image type="content" source="./media/mosp-ea-transfer/subscription-account-admin.png" alt-text="Image montrant les propriétés de l’abonnement et permettant d’afficher l’administrateur de compte" lightbox="./media/mosp-ea-transfer/subscription-account-admin.png" :::

> [!NOTE]
> Par la suite, vous devez vous connecter à EA Portal avec le même compte d’utilisateur Administrateur de compte (cf. informations précédentes). Si vous n’avez pas accès au compte, vous devez l’obtenir avant de continuer. Pour plus d’informations, consultez [Transfert de la propriété de facturation d’un abonnement Azure à un autre compte](billing-subscription-transfer.md).

### <a name="access-to-the-destination-ea"></a>Accès au contrat EA de destination

Vous devez avoir accès au contrat EA de destination pour pouvoir créer un propriétaire du compte EA. Un service (existant ou nouveau) est requis. C’est à cet endroit que l’abonnement est placé lors de son transfert. Le propriétaire du compte EA est propriétaire de l’abonnement qui est mis dans le service.

Vous devez disposer de l’un des rôles suivants pour créer un propriétaire du compte EA. Pour plus d’informations sur les rôles EA, consultez [Gestion des rôles Contrat Entreprise Azure](understand-ea-roles.md).

- Administrateur d’entreprise
- Administrateur de service

### <a name="set-ea-authentication-level"></a>Définition du niveau d'authentification EA

Dans les contrats EA, le niveau d’authentification défini détermine les types d’utilisateurs qui peuvent être ajoutés comme propriétaires du compte EA pour l’inscription. Il existe quatre niveaux d’authentification disponibles (cf. [Types de niveaux d’authentification](ea-portal-troubleshoot.md#authentication-level-types)).

Veillez à ce que le niveau d’authentification défini pour le contrat EA vous permette de créer un nouveau propriétaire du compte EA à l’aide de l’administrateur de compte de l’abonnement noté précédemment. Par exemple :

- Si l’administrateur de compte de l’abonnement dispose du domaine d’adresse e-mail `@outlook.com`, le niveau d’authentification du contrat EA doit être défini sur **Compte Microsoft uniquement** ou **Compte mixte**.
- Si l’administrateur de compte de l’abonnement dispose du domaine d’adresse e-mail `@<YourAzureADTenantPrimaryDomain.com>`, le niveau d’authentification du contrat EA doit être défini sur **Compte professionnel ou scolaire** ou **Compte professionnel ou scolaire multilocataire**. La possibilité de créer un nouveau propriétaire du compte EA varie selon que le domaine par défaut du compte EA est le même que le domaine d’adresse e-mail de l’administrateur de compte de l’abonnement.

> [!NOTE]
> Quand le niveau d’authentification est défini correctement, sa modification n’a aucun impact sur le processus de transfert. Pour plus d’informations, consultez [Types de niveaux d’authentification](ea-portal-troubleshoot.md#authentication-level-types).

## <a name="transfer-the-subscription-to-the-ea"></a>Transfert de l’abonnement vers le contrat EA

Une fois que vous avez consulté et rempli les [prérequis](#prerequisites), vous pouvez commencer le processus de transfert de l’abonnement vers le contrat EA.

### <a name="create-an-ea-account-owner"></a>Création d’un propriétaire du compte EA

Comme nous l’avons vu dans la section [prérequis](#access-to-the-destination-ea), vous devez créer un propriétaire du compte EA dans le cadre d’un service EA (existant ou nouveau).

1. Connectez-vous à EA Portal à l’adresse https://ea.azure.com en tant qu’administrateur d’entreprise ou de service.
1. Pour créer un service, consultez [Création d’un service Azure Enterprise](ea-portal-administration.md#create-an-azure-enterprise-department).
1. Pour créer le propriétaire du compte EA, consultez [Ajout d’un compte](ea-portal-administration.md#add-an-account). Lorsque **l’Adresse e-mail du propriétaire du compte** vous est demandée, entrez l’adresse e-mail de **l’Administrateur de compte** de l’abonnement que vous avez identifié.
    > [!NOTE]
    > Si le niveau d’authentification EA est mal défini, la création du propriétaire du compte EA échoue, et une erreur s’affiche sur EA Portal.

Une fois le propriétaire du compte EA créé, l’administrateur de compte de l’abonnement, qui est également le propriétaire du compte EA, reçoit un e-mail d’Azure EA Portal. Cet e-mail avertit l’utilisateur qu’il est désormais propriétaire du compte EA. Si l’utilisateur n’a pas accès à une boîte aux lettres électronique associée au compte spécifié, ce n’est pas un problème. L’e-mail ne constitue qu’une notification. Les informations qu’il contient ne sont pas nécessaires pour continuer. Toutefois, une boîte aux lettres électronique est conseillée pour les futures notifications relatives à l’abonnement.

### <a name="complete-the-subscription-transfer"></a>Réalisation du transfert d’abonnement

Maintenant que l’administrateur de compte de l’abonnement est également propriétaire du compte EA, il peut créer des abonnements sous le contrat EA.

> [!IMPORTANT]
> Avant de continuer, vous devez comprendre ce qui se passe la première fois qu’un nouveau propriétaire du compte EA se connecte à EA Portal. Lisez et assimilez les informations suivantes avant de vous connecter en tant qu’administrateur de compte de l’abonnement sur EA Portal.

La première fois qu’un nouveau propriétaire du compte EA se connecte à EA Portal, l’avertissement suivant s’affiche :

```
WARNING

You are about to associate your account (email address) to the following enrollment:

Enrollment Name: <EnrollmentName>
Enrollment Number: <EnrollmentNumber>

All Enrollment Administrators can gain access to all of your subscriptions if you proceed.
Additionally, all Azure subscriptions for which you are the account owner will be converted to your Enterprise Agreement.
This includes subscriptions which include a monthly credit (e.g. Visual Studio, MPN, BizSpart, etc.) meaning you will lose the monthly credit by proceeding.
All subscriptions based on a Visual Studio subscriber offer (monthly credit for Visual Studio subscribers or Pay-As-You-Go Dev/Test) will be converted to use the Enterprise Dev/Test usage rates and be billed against this enrollment from today onwards.
If you wish to retain the monthly credits currently associated with any of your subscriptions, please cancel.
Please see additional details.

Cancel  Continue
```

L’avertissement donne les informations suivantes, comme l’indique [l’administration d’Azure EA Portal](ea-portal-administration.md#enterprise-devtest-offer) :

***Lorsqu’un utilisateur est ajouté comme propriétaire du compte par le biais d’Azure EA Portal, tous les abonnements Azure associés au propriétaire du compte, qu’ils soient basés sur l’offre Dev/Test MOSP (PAYG) ou sur les offres mensuelles de crédit pour abonnés Visual Studio, sont convertis en une offre EA Dev/Test. Les abonnements associés au propriétaire du compte et basés sur les autres types d’offres, par exemple MOSP (PAYG), sont convertis en une offre d’abonnement EA standard.***

Si l’utilisateur comprend les conséquences de l’avertissement, sélectionnez **Continuer**. Les abonnements associés à son compte sont alors transférés vers le contrat EA.

## <a name="more-help"></a>Aide supplémentaire

Les informations ci-dessus vous ont normalement permis de transférer l’abonnement vers le contrat EA. Si vous avez besoin d’une aide supplémentaire, créez une [demande de support de facturation](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-step"></a>Étape suivante

- [Bien démarrer avec le portail Azure Enterprise](ea-portal-get-started.md)
