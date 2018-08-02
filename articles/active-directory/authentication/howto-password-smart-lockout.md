---
title: Prévention des attaques par force brute avec le verrouillage intelligent Azure AD
description: Le verrouillage intelligent d’Azure Active Directory permet de protéger votre organisation contre les attaques par force brute visant à deviner vos mots de passe.
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: b0fded9f5543d151091955c0b0d645bf9db16b7d
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158581"
---
# <a name="azure-active-directory-smart-lockout"></a>Verrouillage intelligent Azure Active Directory

Le verrouillage intelligent utilise l’intelligence cloud pour empêcher les pirates de deviner vos mots de passe ou d’utiliser des méthodes de force brute pour rentrer dans vos systèmes. Cette intelligence est capable de reconnaître les connexions provenant des utilisateurs validés, et les traite différemment de celles des pirates et autres utilisateurs inconnus. Le verrouillage intelligent empêche les attaquants de pénétrer dans le système, tout en permettant à vos utilisateurs d’accéder à leurs comptes et de travailler.

Par défaut, le verrouillage intelligent empêche les tentatives de connexion au compte pendant une minute après dix tentatives infructueuses. Le compte se verrouille à nouveau après chaque échec de connexion consécutif. Le premier verrouillage dure une minute, les suivants durent plus longtemps.

Le verrouillage intelligent est activé en permanence pour les clients Azure AD disposant des paramètres par défaut qui offrent la combinaison idéale de sécurité et de facilité d’utilisation. Pour personnaliser les paramètres de verrouillage intelligent en vue de répondre aux besoins de votre organisation, vos utilisateurs doivent disposer d’une licence Azure AD Basic ou plus élevée.

Le verrouillage intelligent peut être intégré aux déploiements hybrides à l’aide de la synchronisation du hachage de mot de passe ou de l’authentification directe, en vue d’empêcher les comptes Active Directory locaux d’être verrouillés par les attaquants. En définissant les stratégies de verrouillage intelligent nécessaires dans Azure AD, vous pouvez bloquer les attaques avant même qu’elles atteignent l’instance locale d’Active Directory.

Lorsque vous utilisez [l’authentification directe](../connect/active-directory-aadconnect-pass-through-authentication.md), vous devez vérifier que :

   * Le seuil de verrouillage d’Azure AD est **inférieur** au seuil de verrouillage de compte Active Directory. Définissez les valeurs de sorte que le seuil de verrouillage de compte Active Directory soit au moins deux ou trois fois supérieur au seuil de verrouillage d’Azure AD. 
   * La durée de verrouillage d’Azure AD, **exprimée en secondes**, est **plus longue** que la durée après laquelle réinitialiser le compteur de verrouillage de compte Active Directory, **exprimée en minutes**.

> [!IMPORTANT]
> Actuellement, un administrateur ne peut pas déverrouiller les comptes cloud des utilisateurs si ceux-ci ont été verrouillés à l’aide de la fonctionnalité Verrouillage intelligent. L’administrateur doit attendre que la durée de verrouillage expire.

## <a name="verify-on-premises-account-lockout-policy"></a>Vérifier la stratégie de verrouillage d’un compte local

Utilisez les instructions suivantes pour vérifier les stratégies de verrouillage de votre compte local Active Directory :

1. Ouvrez l’outil de gestion de stratégie de groupe.
2. Modifiez la stratégie de groupe qui comprend la stratégie de verrouillage de compte de votre organisation, par exemple, la **stratégie de domaine par défaut**.
3. Accédez à **Configuration de l’ordinateur** > **Stratégies** > **Paramètres Windows** > **Paramètres de sécurité** > **Stratégies de compte** > **Stratégie de verrouillage de compte**.
4. Vérifiez vos valeurs de **Seuil de verrouillage de compte** et **Réinitialiser le compteur de verrouillage de compte après**.

![Modifier la stratégie de verrouillage des comptes locaux Active Directory à l’aide d’un objet de stratégie de groupe](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Gérer les valeurs du verrouillage intelligent Azure AD

En fonction des besoins de votre organisation, les valeurs de verrouillage intelligent peuvent nécessiter une personnalisation. Pour personnaliser les paramètres de verrouillage intelligent en vue de répondre aux besoins de votre organisation, vos utilisateurs doivent disposer d’une licence Azure AD Basic ou plus élevée.

Pour vérifier ou modifier les valeurs de verrouillage intelligent de votre organisation, procédez aux étapes suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com), et cliquez sur **Azure Active Directory**, puis sur **Méthodes d’authentification**.
1. Définissez le **Seuil de verrouillage**, c’est-à-dire le nombre d’échecs de connexions autorisé avant qu’un compte ne soit verrouillé. La valeur par défaut est de 10.
1. Définissez la **Durée du verrouillage en secondes** sur la durée en secondes souhaitée de chaque verrouillage. La valeur par défaut est 60 secondes (une minute).

> [!NOTE]
> Si la première connexion après verrouillage échoue également, le compte est de nouveau verrouillé. Si un compte est verrouillé à plusieurs reprises, la durée de verrouillage augmente.

![Personnaliser la stratégie de verrouillage intelligent Azure AD dans le portail Azure](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)
## <a name="next-steps"></a>Étapes suivantes

[Découvrez comment interdire les mots de passe incorrects dans votre organisation à l’aide d’Azure AD](howto-password-ban-bad.md)

[Configurez la réinitialisation de mot de passe libre-service pour permettre aux utilisateurs de déverrouiller leur compte.](quickstart-sspr.md)