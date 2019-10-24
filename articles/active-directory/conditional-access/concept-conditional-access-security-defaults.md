---
title: Paramètres de sécurité par défaut Azure Active Directory
description: Stratégies de sécurité par défaut conçues pour protéger les entreprises contre les attaques courantes
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4921f0605f16a601f6e2ee9a15b71b50d253201
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72452923"
---
# <a name="what-are-security-defaults"></a>Présentation des paramètres de sécurité par défaut

La gestion de la sécurité peut s’avérer ardue lorsque les attaques courantes liées aux identités, telles que la pulvérisation de mot de passe, la relecture et le hameçonnage, deviennent de plus en plus monnaie courante. La création d’une méthode plus simple pour renforcer la sécurité de votre organisation contre ces attaques courantes est l’objectif des paramètres de sécurité par défaut dans Azure Active Directory (AD). Les paramètres de sécurité par défaut facilitent la sécurisation et contribuent à la protection de votre organisation contre les attaques courantes. Les paramètres de sécurité par défaut contiennent des paramètres de sécurité préconfigurés pour ces attaques courantes. Microsoft met les paramètres de sécurité par défaut à la disposition de tous. Le but est de s’assurer que toutes les organisations bénéficient d’un niveau de sécurité de base activé, sans coût supplémentaire.

![Capture d’écran de la nouvelle expérience utilisateur des paramètres de sécurité par défaut](./media/concept-conditional-access-security-defaults/security-defaults-azure-ad-portal.png)
 
Les configurations de sécurité suivantes seront activées dans votre locataire. 

## <a name="unified-mfa-registration"></a>Inscription MFA unifiée

Tous les utilisateurs de votre locataire seront tenus de s’inscrire à Azure Multi-Factor Authentication (MFA). Les utilisateurs disposeront de 14 jours pour s’inscrire à Azure MFA à l’aide de l’application Microsoft Authenticator. Au bout de ces 14 jours, l’utilisateur ne sera plus en mesure de se connecter, à moins que l’inscription MFA n’ait été effectuée.

Nous comprenons que certains utilisateurs puissent être absents du bureau et/ou ne pas se connecter dans les 14 jours suivant immédiatement l’activation des paramètres de sécurité par défaut. Afin de s’assurer que chaque utilisateur dispose de suffisamment de temps pour s’inscrire à MFA, cette période de 14 jours est propre à chaque utilisateur. Ainsi, la période de 14 jours d’un utilisateur commence après la première connexion interactive réussie, une fois la sécurité par défaut activée.

## <a name="mfa-enforcement"></a>Mise en application de MFA

### <a name="protecting-administrators"></a>Protection des administrateurs

Les utilisateurs ayant accès à des comptes privilégiés ont un accès plus large à votre environnement. En raison de l’importance de ces comptes, vous devez leur accorder une attention particulière. Une méthode courante pour améliorer la protection de comptes privilégiés consiste à demander une forme de vérification de compte plus sévère lorsqu’ils sont utilisés pour se connecter. Dans Azure Active Directory, vous pouvez obtenir une vérification plus sévère des comptes en exigeant une authentification multifacteur.

Une fois l’inscription MFA effectuée, les neuf rôles d’administrateur Azure AD suivants sont nécessaires pour procéder à une authentification MFA à chaque tentative de connexion.

- Administrateur général
- Administrateur SharePoint
- Administrateur Exchange
- Administrateur de l’accès conditionnel
- Administrateur de sécurité
- Administrateur de mot de passe/Administrateur du support technique
- Administrateur de facturation
- Administrateur d’utilisateurs
- Administrateur d’authentification

### <a name="protecting-all-users"></a>Protection de tous les utilisateurs

Nous avons tendance à considérer que les comptes administrateur sont les seuls qui nécessitent une protection par authentification multifacteur (MFA). Les administrateurs ont largement accès à des informations sensibles et peuvent modifier des paramètres à l’échelle d’un abonnement. Pourtant, les cybercriminels ont tendance à cibler les utilisateurs finaux. Après s’être introduits, ils peuvent demander l’accès à des informations confidentielles au nom du titulaire du compte d’origine, ou télécharger l’ensemble du répertoire pour effectuer une attaque par hameçonnage dans toute l’organisation. Une méthode courante pour améliorer la protection de tous les utilisateurs finals consiste à demander une forme de vérification de compte plus stricte, telle qu’une authentification multifacteur (MFA) pour tous. Une fois l’inscription MFA effectuée, les utilisateurs sont invités à procéder à l’authentification MFA, chaque fois que c’est nécessaire.

### <a name="blocking-legacy-authentication"></a>Blocage de l’authentification héritée

Pour permettre à vos utilisateurs d’accéder facilement à vos applications cloud, Azure Active Directory (Azure AD) prend en charge un large éventail de protocoles d’authentification, notamment l’authentification héritée. L’authentification héritée est un terme qui fait référence à une requête d’authentification effectuée par :

- D’anciennes versions de clients Office qui n’utilisent pas l’authentification moderne (par exemple, le client Office 2010)
- Tout client qui utilise des protocoles de messagerie hérités tels que IMAP/SMTP/POP3

Aujourd’hui, la majorité des tentatives de connexion compromettantes ont pour origine l’authentification héritée. L’authentification héritée ne prend pas en charge l’authentification multifacteur (MFA). Même si l’une de vos stratégies d’authentification multifacteur est activée dans votre annuaire, un individu mal intentionné peut s’authentifier à l’aide d’un protocole hérité et contourner l’authentification multifacteur. Lorsque les paramètres de sécurité par défaut sont activés dans votre locataire, toutes les demandes d’authentification effectuées par un protocole hérité sont bloquées. Les paramètres de sécurité par défaut ne bloquent pas Exchange ActiveSync.

### <a name="protecting-privileged-actions"></a>Protection des actions privilégiées

Les organisations utilisent divers services Azure managés par le biais de l’API Azure Resource Manager, entre autres :

- Portail Azure 
- Azure PowerShell 
- D’Azure CLI

Utiliser Azure Resource Manager pour gérer vos services est une action très privilégiée. Azure Resource Manager peut modifier des configurations à l’échelle du locataire, telles que les paramètres de service et la facturation de l’abonnement. L’authentification à facteur unique est vulnérable à diverses attaques comme le hameçonnage et la pulvérisation de mots de passe. Par conséquent, il est important de vérifier l’identité des utilisateurs qui veulent accéder à Azure Resource Manager et mettre à jour des configurations. Pour cela, exigez une authentification multifacteur avant d’accorder l’accès.

Une fois les paramètres de sécurité par défaut activés dans votre locataire, tout utilisateur qui accède au portail Azure, à Azure PowerShell ou à Azure CLI devra se soumettre à l’authentification multifacteur. Cette stratégie s’applique à tous les utilisateurs accédant à Azure Resource Manager, qu’ils soient administrateur ou utilisateur. Si l’utilisateur n’est pas inscrit pour une authentification MFA, celui-ci devra s’inscrire à l’aide de l’application d’authentification Microsoft Authenticator pour continuer. Aucune période d’inscription à l’authentification MFA d’une durée de 14 jours ne sera fournie.

## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement

Voici quelques considérations supplémentaires concernant le déploiement des paramètres de sécurité par défaut pour votre locataire.

### <a name="legacy-protocols"></a>Protocoles hérités

Les protocoles d’authentification hérités (IMAP, SMTP, POP3, etc.) sont utilisés par les clients de messagerie pour effectuer des demandes d’authentification. Ces protocoles ne prennent pas en charge l’authentification multifacteur. La plupart des problèmes de compromission de comptes détectés par Microsoft sont causés par des utilisateurs malveillants, qui s’attaquent à des protocoles hérités pour tenter de contourner l’authentification multifacteur. Afin de s’assurer que, d’une part, l’authentification MFA est bien demandée au moment de la connexion à un compte Administrateur et que, d’autre part, les utilisateurs malveillants ne peuvent pas la contourner, les paramètres de sécurité par défaut bloquent toute demande d’authentification effectuée auprès de comptes Administrateur à partir de protocoles hérités.

> [!WARNING]
> Avant d’activer ce paramètre, assurez-vous que vos administrateurs n’utilisent aucun protocole d’authentification hérité. Pour plus d’informations, consultez l’article  [Comment se passer de l’authentification héritée](concept-conditional-access-block-legacy-authentication.md).

### <a name="conditional-access"></a>Accès conditionnel

L’accès conditionnel peut servir à configurer des stratégies qui fournissent le même comportement que celui activé par les paramètres de sécurité par défaut. Si vous utilisez l’accès conditionnel et que des stratégies d’accès conditionnel sont activées dans votre environnement, les paramètres de sécurité par défaut ne seront pas disponibles pour vous. Si vous disposez d’une licence qui fournit un accès conditionnel, mais que vous n’avez activé aucune stratégie d’accès conditionnel dans votre environnement, vous êtes invité à utiliser les paramètres de sécurité par défaut, tant que vous activez pas des stratégies d’accès conditionnel.

![Paramètres de sécurité par défaut ou accès conditionnel, pas les deux](./media/concept-conditional-access-security-defaults/security-defaults-conditional-access.png)

Voici des guides pas à pas sur la façon dont l’accès conditionnel peut être utilisé pour configurer des stratégies équivalentes :

- [Exiger MFA pour les administrateurs](howto-conditional-access-policy-admin-mfa.md)
- [Exiger MFA pour la gestion Azure](howto-conditional-access-policy-azure-management.md)
- [Bloquer l’authentification héritée](howto-conditional-access-policy-block-legacy.md)

## <a name="enabling-security-defaults"></a>Activation des paramètres de sécurité par défaut

Pour activer les paramètres de sécurité par défaut dans votre annuaire :

1. Connectez-vous au  [portail Azure](https://portal.azure.com)  en tant qu’administrateur de la sécurité, administrateur de l’accès conditionnel ou administrateur général.
1. Accédez à  **Azure Active Directory** > **Propriétés**.
1. Sélectionnez **Gérer les paramètres de sécurité par défaut**.
1. Définissez **Activer les paramètres de sécurité par défaut** sur **Oui** à l’aide du bouton bascule.
1. Cliquez sur Enregistrer.

## <a name="next-steps"></a>Étapes suivantes

[Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)

[Qu’est-ce que l’accès conditionnel ?](overview.md)
