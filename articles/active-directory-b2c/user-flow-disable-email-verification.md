---
title: Désactiver la vérification par e-mail lors de l’inscription du client
titleSuffix: Azure AD B2C
description: Découvrez comment désactiver la vérification par e-mail lors de l’inscription de client dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c598664ef721103740716ad6215ddaea53bc635f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183072"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Désactiver la vérification par e-mail lors de l’inscription de client dans Azure Active Directory B2C

Par défaut, Azure Active Directory B2C (Azure AD B2C) vérifie l’adresse e-mail de votre client pour les comptes locaux (comptes des utilisateurs qui s’inscrivent avec une adresse e-mail ou un nom d’utilisateur). Azure AD B2C vérifie la validité de l’adresse e-mail en demandant au client de la confirmer pendant le processus d’inscription. Il empêche également tout acteur malveillant d’utiliser des processus automatisés pour générer des comptes frauduleux dans vos applications.

Certains développeurs d’applications préfèrent ignorer la vérification par e-mail pendant le processus d’inscription et amener les clients à vérifier l’adresse e-mail ultérieurement. Pour ce faire, Azure Active Directory B2C peut être configuré afin de désactiver la vérification par e-mail. Cette opération crée un processus de connexion plus simple et offre aux développeurs la possibilité de différencier les clients qui ont vérifié leur adresse e-mail de ceux qui ne l’ont pas encore fait.

Pour désactiver la vérification par e-mail, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com)
1. Utilisez le filtre **Annuaire + abonnement** dans le menu du haut pour sélectionner l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Flux d’utilisateurs**.
1. Sélectionnez le flux d’utilisateur pour lequel vous souhaitez désactiver la vérification par e-mail. Par exemple, *B2C_1_signinsignup*.
1. Sélectionnez **Mises en page**.
1. Sélectionnez **Page d’inscription à un compte Local**.
1. Sous **Attributs utilisateur**, sélectionnez **Adresse e-mail**.
1. Dans la liste déroulante **Requiert une vérification**, sélectionnez **Non**.
1. Sélectionnez **Enregistrer**. La vérification par e-mail est maintenant désactivée pour ce flux d’utilisateur.

> [!WARNING]
> La désactivation de la vérification par e-mail dans le processus d’inscription peut entraîner la réception de courrier indésirable. Si vous désactivez la vérification par e-mail par défaut d’Azure AD B2C, nous vous recommandons d’implémenter un système de vérification de remplacement.
