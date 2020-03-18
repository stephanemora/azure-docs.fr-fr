---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126747"
---
## <a name="disable-email-verification"></a>Désactiver la vérification e-mail

Par défaut, Azure Active Directory B2C (Azure AD B2C) vérifie l’adresse e-mail de votre client pour les comptes locaux (comptes des utilisateurs qui s’inscrivent avec une adresse e-mail ou un nom d’utilisateur). Azure AD B2C vérifie la validité de l’adresse e-mail en demandant au client de la confirmer pendant le processus d’inscription. Il empêche également des acteurs malveillants d’utiliser des processus automatisés pour générer des comptes frauduleux dans vos applications.

Certains développeurs d’applications préfèrent ignorer la vérification par e-mail pendant le processus d’inscription et amener les clients à vérifier l’adresse e-mail ultérieurement. Pour ce faire, Azure Active Directory B2C peut être configuré afin de désactiver la vérification par e-mail. Cette opération crée un processus de connexion plus simple et offre aux développeurs la possibilité de différencier les clients qui ont vérifié leur adresse e-mail de ceux qui ne l’ont pas encore fait.

> [!WARNING]
> La désactivation de la vérification par e-mail dans le processus d’inscription peut entraîner la réception de courrier indésirable. Si vous désactivez la vérification par e-mail par défaut d’Azure AD B2C, nous vous recommandons d’implémenter un système de vérification de remplacement.
