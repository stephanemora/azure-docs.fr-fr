---
title: Prévention des attaques avec le verrouillage intelligent - Azure Active Directory
description: Le verrouillage intelligent d’Azure Active Directory permet de protéger votre organisation contre les attaques par force brute visant à deviner vos mots de passe.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30006d0dcccdd1b160289c72769340d141198d3c
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847590"
---
# <a name="azure-active-directory-smart-lockout"></a>Verrouillage intelligent Azure Active Directory

Le verrouillage intelligent empêche les personnes malveillantes de deviner vos mots de passe ou d’utiliser des méthodes de force brute pour rentrer dans vos systèmes. Il peut reconnaître les connexions provenant d’utilisateurs validés et les traiter différemment de celles des attaquants et autres sources inconnues. Le verrouillage intelligent empêche les attaquants de pénétrer dans le système, tout en permettant à vos utilisateurs d’accéder à leurs comptes et de travailler.

Par défaut, le verrouillage intelligent empêche les tentatives de connexion au compte pendant une minute après 10 tentatives infructueuses. Le compte se verrouille à nouveau après chaque échec de connexion consécutif. Le premier verrouillage dure une minute, les suivants durent plus longtemps.

Le verrouillage intelligent suit les trois derniers hachages de mots de passe incorrects afin d'éviter d'incrémenter le compteur de verrouillages pour le même mot de passe. Si un utilisateur entre plusieurs fois le même mot de passe incorrect, le compte n'est pas verrouillé.

 > [!NOTE]
 > La fonctionnalité de suivi du hachage n’est pas disponible pour les clients sur lesquels l’authentification directe est activée dans la mesure où l’authentification est effectuée localement et pas dans le cloud.

Les déploiements fédérés utilisant AD FS 2016 et AD FS 2019 peuvent bénéficier d’avantages similaires à ceux fournis par l’utilisation du [verrouillage extranet et du verrouillage intelligent extranet AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection).

Le verrouillage intelligent est activé en permanence pour les clients Azure AD disposant des paramètres par défaut qui offrent la combinaison idéale de sécurité et de facilité d’utilisation. Pour personnaliser les paramètres de verrouillage intelligent en vue de répondre aux besoins de votre organisation, vos utilisateurs doivent disposer d’une licence Azure AD payante.

L'utilisation du verrouillage intelligent ne garantit pas qu'un véritable utilisateur ne sera jamais verrouillé. Lorsque le verrouillage intelligent verrouille un compte d'utilisateur, nous mettons tout en œuvre pour ne pas verrouiller le véritable utilisateur. Le service de verrouillage veille à ce que des personnes mal intentionnées n’aient pas accès au compte d’un véritable utilisateur.  

* Chaque centre de données Azure Active Directory assure indépendamment le suivi des verrouillages. Un utilisateur disposera d'un nombre de tentatives de (seuil_limite x nombre_de_centres_de_données) s'il atteint chaque centre de données.
* Le verrouillage intelligent utilise un emplacement familier par opposition à un emplacement inconnu pour faire la différence entre une personne mal intentionnée et un véritable utilisateur. Les emplacements familiers et inconnus disposent tous deux de compteurs de verrouillages distincts.

Le verrouillage intelligent peut être intégré aux déploiements hybrides à l’aide de la synchronisation du hachage de mot de passe ou de l’authentification directe, en vue d’empêcher les comptes Active Directory locaux d’être verrouillés par les attaquants. En définissant les stratégies de verrouillage intelligent nécessaires dans Azure AD, vous pouvez bloquer les attaques avant même qu’elles atteignent l’instance locale d’Active Directory.

Lorsque vous utilisez [l’authentification directe](../hybrid/how-to-connect-pta.md), vous devez vérifier que :

* Le seuil de verrouillage d’Azure AD est **inférieur** au seuil de verrouillage de compte Active Directory. Définissez les valeurs de sorte que le seuil de verrouillage de compte Active Directory soit au moins deux ou trois fois supérieur au seuil de verrouillage d’Azure AD. 
* La durée de verrouillage d’Azure AD doit être plus longue que la durée après laquelle réinitialiser le compteur de verrouillage de compte Active Directory. N’oubliez pas que la durée d’Azure AD est définie en secondes, tandis que la durée d’AD est définie en minutes. 

Par exemple, si vous souhaitez que votre compteur Azure AD soit supérieur à celui d’AD, Azure AD doit être défini sur 120 secondes (2 minutes) tandis que votre AD local doit être défini sur 1 minute (60 secondes).

> [!IMPORTANT]
> Actuellement, un administrateur ne peut pas déverrouiller les comptes cloud des utilisateurs s’ils ont été verrouillés à l’aide de la fonctionnalité Verrouillage intelligent. L’administrateur doit attendre que la durée de verrouillage expire. En revanche, l’utilisateur peut procéder à un déverrouillage en utilisant la réinitialisation de mot de passe en libre-service (SSPR) à partir d’un appareil ou d’un emplacement de confiance.

## <a name="verify-on-premises-account-lockout-policy"></a>Vérifier la stratégie de verrouillage d’un compte local

Utilisez les instructions suivantes pour vérifier les stratégies de verrouillage de votre compte local Active Directory :

1. Ouvrez l’outil de gestion de stratégie de groupe.
2. Modifiez la stratégie de groupe qui comprend la stratégie de verrouillage de compte de votre organisation, par exemple, la **stratégie de domaine par défaut**.
3. Accédez à **Configuration de l’ordinateur** > **Stratégies** > **Paramètres Windows** > **Paramètres de sécurité** > **Stratégies de compte** > **Stratégie de verrouillage de compte**.
4. Vérifiez vos valeurs de **Seuil de verrouillage de compte** et **Réinitialiser le compteur de verrouillage de compte après**.

![Modifier la stratégie de verrouillage des comptes locaux Active Directory](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Gérer les valeurs du verrouillage intelligent Azure AD

En fonction des besoins de votre organisation, les valeurs de verrouillage intelligent peuvent nécessiter une personnalisation. Pour personnaliser les paramètres de verrouillage intelligent en vue de répondre aux besoins de votre organisation, vos utilisateurs doivent disposer d’une licence Azure AD payante.

Pour vérifier ou modifier les valeurs de verrouillage intelligent de votre organisation, procédez aux étapes suivantes :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Recherchez et sélectionnez *Azure Active Directory*. Sélectionnez **Méthodes d’authentification** > **Protection par mot de passe**.
1. Définissez le **Seuil de verrouillage**, c’est-à-dire le nombre d’échecs de connexions autorisé avant qu’un compte ne soit verrouillé. La valeur par défaut est de 10.
1. Définissez la **Durée du verrouillage en secondes** sur la durée en secondes souhaitée de chaque verrouillage. La valeur par défaut est 60 secondes (une minute).

> [!NOTE]
> Si la première connexion après verrouillage échoue également, le compte est de nouveau verrouillé. Si un compte est verrouillé à plusieurs reprises, la durée de verrouillage augmente.

![Personnaliser la stratégie de verrouillage intelligent Azure AD dans le portail Azure](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Vérifier le bon fonctionnement de la fonctionnalité Verrouillage intelligent

Lorsque le seuil de verrouillage intelligent est déclenché, le message suivant s'affiche en cas de verrouillage du compte :

**Votre compte est verrouillé de façon temporaire afin d'éviter toute utilisation non autorisée. Réessayez plus tard. Si le problème persiste, contactez votre administrateur.**

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment interdire les mots de passe incorrects dans votre organisation à l’aide d’Azure AD](howto-password-ban-bad.md)
* [Configurez la réinitialisation de mot de passe libre-service pour permettre aux utilisateurs de déverrouiller leur compte.](quickstart-sspr.md)
