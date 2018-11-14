---
title: Désactiver la vérification par e-mail lors de l’inscription du consommateur dans Azure Active Directory B2C | Microsoft Docs
description: Rubrique expliquant comment désactiver la vérification par e-mail lors de l’inscription du consommateur à Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 2/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e36dd19aa020b8cb2a623cda904cf7fa8a0b26da
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51004590"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>Désactiver la vérification par e-mail lors de l’inscription du consommateur à Azure Active Directory B2C 
Lorsqu'il est activé, Azure Active Directory (Azure AD) B2C permet à un consommateur de s’inscrire à des applications en fournissant une adresse e-mail et en créant un compte local. Azure Active Directory B2C assure la validité des adresses e-mail en demandant aux consommateurs de les vérifier pendant le processus d’inscription. Cela empêche un processus malveillant automatisé de générer des faux comptes pour les applications.

Certains développeurs d’applications préfèrent ignorer la vérification par e-mail pendant le processus d’inscription et demander aux consommateurs de vérifier l’adresse e-mail ultérieurement. Pour ce faire, Azure Active Directory B2C peut être configuré afin de désactiver la vérification par e-mail. Cette opération crée un processus de connexion plus simple et offre aux développeurs la possibilité de différencier les consommateurs qui ont vérifié leur e-mail de ceux qui ne l'ont pas encore fait.

Par défaut, la vérification est activée pour les stratégies d’inscription. Pour la désactiver, procédez comme suit :

1. Cliquez sur les **stratégies d’inscription** ou les **stratégies d’inscription ou de connexion** selon la configuration de l'inscription.
2. Ouvrez votre inscription (par exemple, « B2C_1_SiUp ») en cliquant dessus. 
3. Cliquez sur **Modifier** dans la partie supérieure du panneau.
4. Cliquez sur la fonctionnalité de **personnalisation de l’interface utilisateur de page**.
5. Cliquez sur **page d’inscription à un compte Local**.
6. Cliquez sur **Adresse e-mail** dans la colonne **Nom** colonne sous la section des **attributs d’abonnement**.
7. Définissez l’option **Exiger la vérification** sur **non**.
8. Cliquez sur **OK** en bas jusqu'à atteindre le panneau **Modifier une stratégie**.
9. Cliquez sur **Enregistrer** dans la partie supérieure du panneau. Vous avez terminé !

> [!NOTE]
> La désactivation de la vérification par e-mail dans le processus d’inscription peut entraîner la réception de courrier indésirable. Si vous désactivez la vérification par défaut, nous vous recommandons d’ajouter votre propre système de vérification.
> 
>