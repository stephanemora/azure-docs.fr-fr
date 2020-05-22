---
title: Marquer une application comme vérifiée par l'éditeur - Plateforme d'identités Microsoft | Azure
description: Décrit comment marquer une application avec la mention « éditeur vérifié ». Lorsqu'une application est accompagnée de la mention « éditeur vérifié », cela signifie que l'éditeur a validé son identité à l'aide d'un compte Microsoft Partner Network auquel le processus de vérification a été appliqué jusqu'à son terme et qu'il a associé ce compte MPN à l'inscription de l'application.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 0c1f279e49b938fb391223bec47d23326e34b2ea
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595688"
---
# <a name="mark-your-app-as-publisher-verified-preview"></a>Marquer votre application avec la mention « éditeur vérifié » (préversion)

Lorsqu'une application est marquée avec la mention « éditeur vérifié », cela signifie que l'éditeur a validé son identité à l'aide de son compte Microsoft Partner Network (MPN) et a associé ce compte MPN à l'inscription de l'application. Cet article explique comment mener à bien le processus de [ validation de l’éditeur (préversion)](publisher-verification-overview.md).

## <a name="quickstart"></a>Démarrage rapide
Si vous êtes déjà inscrit à Microsoft Partner Network (MPN) et que vous remplissez les [conditions préalables](publisher-verification-overview.md#requirements), vous pouvez commencer immédiatement : 

1. Accédez au [portail d'inscription des applications](https://aka.ms/PublisherVerificationPreview) (préversion).

1. Choisissez une application, puis cliquez sur **Personnalisation**. 

1. Cliquez sur **Ajouter l’ID MPN pour valider l'éditeur** et passez en revue les conditions requises.

1. Entrez votre ID MPN, puis cliquez sur **Vérifier et enregistrer**.

Pour plus d’informations sur les avantages spécifiques, les conditions requises et les questions fréquemment posées, consultez la [vue d'ensemble](publisher-verification-overview.md).


## <a name="mark-your-app-as-publisher-verified"></a>Marquer votre application avec la mention « éditeur vérifié »
Assurez-vous d'avoir respecté les [conditions préalables](publisher-verification-overview.md#requirements), puis procédez comme suit pour marquer vos applications avec la mention « éditeur vérifié ».  

1. Vérifiez que vous êtes connecté avec un compte d’organisation (Azure AD) autorisé à apporter des modifications aux applications que vous souhaitez marquer avec la mention « éditeur vérifié » et sur le compte MPN dans l’Espace partenaires. 

    - Dans Azure AD, cet utilisateur doit être le propriétaire de l'application ou disposer d'un des rôles suivants : Administrateur d'application, Administrateur d'application cloud, Administrateur général. 

    - Dans l'Espace partenaires, cet utilisateur doit disposer des rôles suivants : Administrateur MPN, Administrateur de comptes ou Administrateur général (rôle partagé maîtrisé dans Azure AD). 

1. Accédez à la version en préversion du portail d'inscription des applications.  

1. Cliquez sur une application que vous souhaitez marquer avec la mention « éditeur vérifié » et ouvrez le panneau Personnalisation. 

1. Vérifiez que le domaine de l’éditeur de l’application est correctement défini. Ce domaine doit : 

    - Être ajouté au locataire Azure AD en tant que domaine personnalisé vérifié par DNS,  

    - Correspondre au domaine de l’adresse de messagerie utilisée lors du processus de vérification de votre compte MPN. 

1. Cliquez sur **Ajouter l'ID MPN pour vérifier l'éditeur** en bas de la page. 

1. Entrez votre **ID MPN**. Cet ID MPN doit correspondre à ce qui suit : 

    - Un compte Microsoft Partner Network valide auquel le processus de vérification a été appliqué jusqu'à son terme.  

    - Le compte global partenaire (PGA) de votre organisation. 

1. Cliquez sur **Vérifier et enregistrer**. 

1. Attendez le traitement de la requête, ce qui peut prendre quelques minutes. 

1. Si la vérification aboutit, la fenêtre de vérification de l'éditeur se ferme et vous revenez au panneau Personnalisation. Vous notez alors la présence d'un badge bleu vérifié en regard de votre **nom d'affichage d'éditeur** vérifié. 

1. Les utilisateurs invités à donner leur consentement à votre application voient votre badge une fois le processus terminé avec succès, même si sa réplication dans le système peut prendre un certain temps. 

1. Testez cette fonctionnalité en vous connectant à votre application et en vérifiant que le badge vérifié s’affiche dans l’écran de consentement. Si vous êtes connecté en tant qu’utilisateur ayant donné son consentement à l’application, vous pouvez utiliser le paramètre de requête *prompt=consent* pour forcer une invite de consentement. 

1. Répétez ce processus si nécessaire pour toutes les applications supplémentaires pour lesquelles vous souhaitez afficher le badge. Vous pouvez utiliser Microsoft Graph pour effectuer cette opération en bloc. Des cmdlets PowerShell seront prochainement disponibles. Pour plus d’informations, consultez [Création d'appels Microsoft API Graph](troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls). 

Et voilà ! Faites-nous part de vos commentaires sur le processus, les résultats ou la fonctionnalité en général. 

## <a name="next-steps"></a>Étapes suivantes
En cas de problèmes, consultez les [informations relatives à la résolution des problèmes](troubleshoot-publisher-verification.md).