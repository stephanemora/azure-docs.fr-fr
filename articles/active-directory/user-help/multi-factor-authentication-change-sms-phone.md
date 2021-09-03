---
title: Problèmes courants lors de l’utilisation de la vérification en deux étapes pour les messages texte-Azure Active Directory | Microsoft Docs
description: Découvrez comment configurer un appareil mobile différent comme méthode de vérification à deux facteurs.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 07/28/2021
ms.author: curtand
ms.openlocfilehash: 2589fee3b974940a4ca60ad2e2f9861d8a5f69d5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532780"
---
# <a name="common-problems-with-text-message-two-step-verification"></a>Problèmes courants liés à la vérification en deux étapes pour les messages texte

La réception d’un code de vérification dans un message texte est une méthode de vérification courante pour la vérification en deux étapes. Si vous ne vous attendiez pas à recevoir un code ou si vous avez reçu un code sur le mauvais téléphone, procédez comme suit pour résoudre ce problème.  

> [!Note]
> Si votre organisation ne vous autorise pas à recevoir un message texte à des fins de vérification, vous devez sélectionner une autre méthode ou contacter votre administrateur pour obtenir de l’aide.

## <a name="if-you-received-the-code-on-the-wrong-phone"></a>Si vous avez reçu le code sur le mauvais téléphone

1. Connectez-vous à **Mes informations de sécurité** pour gérer vos informations de sécurité.

1. Sur la page **Informations de sécurité**, sélectionnez le numéro de téléphone que vous souhaitez modifier dans votre liste de méthodes d'authentification enregistrées, puis sélectionnez **Modifier**.

1. Sélectionnez votre pays ou votre région pour votre nouveau numéro, puis entrez le numéro de téléphone de votre appareil mobile.

1. Sélectionnez **Envoyez-moi un code pour recevoir des messages texte à des fins de vérification**, puis sélectionnez **Suivant**.

1. Tapez le code de vérification du SMS envoyé par Microsoft une fois demandé, puis sélectionnez **Suivant**.

1. Lorsque vous êtes informé que votre téléphone a été inscrit avec succès, sélectionnez **Terminé**.

## <a name="if-you-receive-a-code-unexpectedly"></a>Si vous recevez un code de façon inattendue

### <a name="if-you-already-registered-your-phone-number-for-two-step-verification"></a>Si vous avez déjà enregistré votre numéro de téléphone pour la vérification en deux étapes

La réception d’un message texte inattendu peut signifier que quelqu’un connaît votre mot de passe et tente de prendre le contrôle de votre compte. Modifiez votre mot de passe immédiatement et avertissez l’administrateur de votre organisation de ce qui s’est passé.

### <a name="if-you-never-registered-your-phone-number-for-two-step-verification"></a>Si vous n’avez jamais enregistré votre numéro de téléphone pour la vérification en deux étapes

Vous pouvez répondre au message texte avec `STOP` dans le corps du message texte. Ce message empêche le fournisseur d’envoyer des messages à votre numéro de téléphone à l’avenir. Vous devrez peut-être répondre à des messages similaires avec des codes différents.  

Toutefois, si vous utilisez déjà la vérification en deux étapes, l’envoi de ce message vous empêche d’utiliser ce numéro de téléphone pour vous connecter. Si vous souhaitez commencer à recevoir des messages texte, répondez au message texte initial avec `START` dans le corps.

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir de l’aide pour la vérification en deux étapes](multi-factor-authentication-end-user-troubleshoot.md)
- [Configurer un téléphone mobile comme méthode de vérification en deux étapes](multi-factor-authentication-setup-phone-number.md)