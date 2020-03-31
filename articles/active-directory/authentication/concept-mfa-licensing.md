---
title: Versions et plans de consommation d’Azure Multi-Factor Authentication
description: Informations sur le client Multi-Factor Authentication ainsi que sur les différentes méthodes et versions disponibles.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e74a7ab0c003aaf9d90211484b39f8322cd9c329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648000"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Fonctionnalités et licences pour Azure Multi-Factor Authentication

Pour protéger les comptes d’utilisateurs au sein de votre organisation, vous devez utiliser l’authentification multifacteur. Cette fonctionnalité est particulièrement importante pour les comptes administratifs qui disposent d’un accès privilégié aux ressources. Les fonctionnalités de base de l’authentification multifacteur sont disponibles pour les administrateurs Office 365 et Azure Active Directory (Azure AD) sans frais supplémentaires. Si vous souhaitez mettre à niveau les fonctionnalités pour vos administrateurs ou étendre l’authentification multifacteur au reste de vos utilisateurs, vous pouvez acheter Azure Multi-Factor Authentication de plusieurs façons.

> [!IMPORTANT]
> Cet article détaille les différentes façons d’obtenir la licence Azure Multi-Factor Authentication et de l’utiliser. Pour obtenir des détails spécifiques sur la tarification et la facturation, voir la [page de tarification d’Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versions disponibles d’Azure Multi-Factor Authentication

Vous pouvez obtenir la licence Azure Multi-Factor Authentication et l’utiliser de différentes manières en fonction des besoins de votre organisation. Vous êtes peut-être déjà en droit d’utiliser Azure Multi-Factor Authentication en vertu d’une licence Azure AD, Office 365, EMS ou Microsoft 365 que vous possédez. Le tableau suivant détaille les différentes façons d’obtenir Azure Multi-Factor Authentication ainsi que les fonctionnalités et utilisations correspondant aux différents cas.

| Si vous êtes un utilisateur de | Fonctionnalités et utilisations |
| --- | --- |
| EMS ou Microsoft 365 E3 et E5 | EMS E3 ou Microsoft 365 E3 (qui comprend EMS et Office 365) incluent Azure AD Premium P1. EMS E5 ou Microsoft 365 E5 incluent Azure AD Premium P2. Vous pouvez utiliser les fonctionnalités d’accès conditionnel énoncées dans les sections suivantes pour fournir une authentification multifacteur aux utilisateurs. |
| Azure AD Premium P1 | Vous pouvez utiliser l’[accès conditionnel Azure AD](../conditional-access/overview.md) pour inviter des utilisateurs à opter pour une authentification multifacteur dans le cadre de certains scénarios ou événements en fonction des besoins de votre entreprise. |
| Azure AD Premium P2 | Fournit la sécurité la plus forte et une expérience utilisateur améliorée. Ajoute l’[accès conditionnel en fonction des risques](../conditional-access/howto-conditional-access-policy-risk.md) aux fonctionnalités d’Azure AD Premium P1, qui s’adapte aux profils d’utilisation et minimise les invites d’authentification multifacteur. |
| Office 365 Business Premium, E3 ou E5 | Azure Multi-Factor Authentication est activé ou désactivé pour tous les utilisateurs, pour tous les événements de connexion. Il n’est pas possible d’activer l’authentification multifacteur uniquement pour un sous-ensemble d’utilisateurs ou pour certains scénarios. La gestion s’effectue via le portail Office 365. Pour une expérience utilisateur améliorée, effectuez une mise à niveau vers Azure AD Premium P1 ou P2, et utilisez un accès conditionnel. Pour plus d’informations, voir [Sécuriser les ressources Office 365 avec une authentification multifacteur](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). |
| Azure AD gratuit | Vous pouvez utiliser des [paramètres de sécurité par défaut](../fundamentals/concept-fundamentals-security-defaults.md) pour activer l’authentification multifacteur pour tous les utilisateurs à chaque demande d’authentification. Vous ne disposez pas d’un contrôle précis des utilisateurs ou des scénarios activés, mais cela offre une étape de sécurité supplémentaire.<br /> Même si des paramètres de sécurité par défaut ne sont pas utilisés pour activer l’authentification multifacteur pour tout le monde, les utilisateurs auxquels est attribué le rôle *Administrateur général Azure AD* peuvent être configurés pour utiliser l’authentification multifacteur. Cette fonctionnalité du niveau Gratuit veille à ce que les comptes d’administrateur critiques soient protégés par une authentification multifacteur. |

## <a name="feature-comparison-of-versions"></a>Comparaison des fonctionnalités suivant les versions

Le tableau suivant fournit la liste des fonctionnalités qui sont disponibles dans les différentes versions d’Azure Multi-Factor Authentication. Planifiez vos besoins en matière de sécurisation de l’authentification des utilisateurs, puis déterminez l’approche correspondant à ces exigences. Par exemple, bien qu’Azure AD Free fournisse des paramètres de sécurité par défaut incluant Azure Multi-Factor Authentication, seule l’application d’authentification mobile peut être utilisée pour l’invite d’authentification, et non un appel téléphonique ou un SMS. Cette approche peut constituer une limitation si vous ne pouvez pas vous assurer que l’application d’authentification mobile est installée sur l’appareil personnel d’un utilisateur.

| Fonctionnalité | Azure AD Free – Paramètres de sécurité par défaut | Azure AD Free – Administrateurs généraux d’Azure AD | Office 365 Business Premium, E3 ou E5 | Azure AD Premium P1 ou P2 |
| --- |:---:|:---:|:---:|:---:|
| Protection des comptes Administrateur locataire Azure AD avec MFA | ● | ● (Comptes d’*administrateur généraux d’Azure AD* uniquement) | ● | ● |
| Application mobile comme second facteur | ● | ● | ● | ● |
| Appel téléphonique comme second facteur | | ● | ● | ● |
| SMS comme second facteur | | ● | ● | ● |
| Contrôle d’administration sur les méthodes de vérification | | ● | ● | ● |
| Alerte de fraude | | | | ● |
| Rapports MFA | | | | ● |
| Messages de bienvenue personnalisés pour les appels téléphoniques | | | | ● |
| ID d’appelant personnalisé pour les appels téléphoniques | | | | ● |
| Adresses IP approuvées | | | | ● |
| Mémoriser MFA pour les appareils fiables | | ● | ● | ● |
| MFA pour les applications locales | | | | ● |

> [!IMPORTANT]
> Depuis mars 2019, les options d’appel téléphonique ne sont plus disponibles pour Azure Multi-Factor Authentication et les utilisateurs de la fonctionnalité Réinitialisation du mot de passe libre-service Azure dans les locataires d’une version gratuite ou d’évaluation d’Azure AD. Ce changement n’affecte pas les messages SMS. Les appels téléphoniques restent disponibles pour les utilisateurs dans des locataires Azure AD Premium P1 ou P2, ou qui se servent d’Office 365 Business Premium, E3 ou E5.

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>Acheter et activer Azure Multi-Factor Authentication

Pour utiliser Azure Multi-Factor Authentication, inscrivez-vous pour disposer d’un niveau Azure AD éligible ou achetez-le. Azure AD est disponible en quatre éditions : édition gratuite des applications Office 365 (pour les clients Office 365 Business Premium E3 ou E5), Premium P1 et Premium P2.

L’édition gratuite fait partie de tout abonnement Azure. Pour plus d’informations sur l’utilisation des paramètres de sécurité par défaut ou la protection des comptes avec le rôle *Administrateur général Azure AD*, voir la section [ci-dessous](#azure-ad-free-tier).

Les éditions Azure AD Premium sont disponibles via votre représentant Microsoft, le [programme de licences en volume Open](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx) et le [programme Fournisseurs de solutions cloud](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409). Les abonnés Azure et Office 365 peuvent également acheter Azure Active Directory Premium P1 et P2 en ligne. [Connectez-vous](https://portal.office.com/Commerce/Catalog.aspx) pour acheter.

> [!IMPORTANT]
> La gestion des licences basée sur la consommation n’est plus disponible pour les nouveaux clients depuis le 1er septembre 2018. Les clients existants utilisant le modèle basé sur la consommation peuvent continuer à utiliser une facturation par utilisateur activé ou par authentification.

Une fois que vous avez acheté le niveau Azure AD requis, [planifiez et déployez Azure Multi-Factor Authentication](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>Niveau Azure AD Free

Tous les utilisateurs au sein d’un locataire Azure AD Free peuvent utiliser Azure Multi-Factor Authentication au travers des paramètres de sécurité par défaut. Ces paramètres de sécurité par défaut activent Azure Multi-Factor Authentication pour tous les utilisateurs, chaque fois qu’ils se connectent. L’application d’authentification mobile est la seule méthode utilisable pour Azure Multi-Factor Authentication en cas de recours aux paramètres de sécurité par défaut d’Azure AD Free.

* [En savoir plus sur les paramètres de sécurité par défaut d’Azure AD](../fundamentals/concept-fundamentals-security-defaults.md)
* [Activer les paramètres de sécurité par défaut pour les utilisateurs d’Azure AD Free](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Si vous ne souhaitez pas activer Azure Multi-Factor Authentication pour tous les utilisateurs et tous les événements de connexion, vous pouvez protéger uniquement les comptes d’utilisateurs auxquels est attribué le rôle *Administrateur général Azure AD*. Cette approche fournit des invites d’authentification supplémentaires pour les comptes d’administrateur critiques. Vous activez Azure Multi-Factor Authentication de l’une des manières suivantes, selon le type de compte que vous utilisez :

* Si vous utilisez un compte Microsoft, [inscrivez-vous pour bénéficier de l’authentification multifacteur](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Si vous n’utilisez pas un compte Microsoft, [activez l’authentification multifacteur pour un utilisateur ou un groupe dans Azure AD](howto-mfa-userstates.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les coûts, voir la [tarification d’Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
