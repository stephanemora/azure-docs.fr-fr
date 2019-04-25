---
title: Configurer des revendications de groupe pour les applications avec Azure Active Directory | Microsoft Docs
description: Informations sur la façon de configurer des revendications de groupe pour une utilisation avec Azure AD.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.component: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: 622a3ce0f80bd09bd09fa7ff097f68155318142d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60351242"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Configurer des revendications de groupe pour les applications avec Azure Active Directory (version préliminaire publique)

Azure Active Directory peut fournir une informations d’appartenance de groupe aux utilisateurs dans les jetons pour une utilisation au sein d’applications.  Deux principaux modes sont pris en charge :

- Groupes identifiés par leur identificateur d’objet (OID) (à la disposition générale) Azure Active Directory
- Groupes identifiés par SAMAccountName ou GroupSID pour Active Directory (AD) synchronisé des groupes et utilisateurs (préversion publique)

> [!Note]
> Prise en charge pour l’utilisation de noms et en local des identificateurs de sécurité (SID) est conçu pour permettre le déplacement des applications existantes à partir d’AD FS.    Groupes gérés dans Azure AD ne contiennent pas les attributs nécessaires pour émettre ces revendications.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-idps"></a>Revendications de groupe pour les applications migrant à partir d’AD FS et d’autres fournisseurs d’identité

De nombreuses applications qui sont configurées pour s’authentifier avec AD FS s’appuient sur les informations d’appartenance de groupe sous la forme d’attributs de groupe Windows Active Directory.   Ces attributs sont le groupe SAMAccountName, ce qui peut être qualifié par-nom de domaine ou le SID de groupe Windows.  Lorsque l’application est fédérée avec AD FS, ADFS utilise la fonction de TokenGroups pour récupérer les appartenances de groupe pour l’utilisateur.

Pour faire correspondre le jeton d’une application recevrait d’AD FS, les revendications de groupe et le rôle peuvent être émises contenant le domaine qualifié SAMAccountName au lieu des ID d’objet du groupe Azure Active Directory.

Les formats pris en charge pour les revendications de groupe sont :

- **Azure GroupObjectId Directory Active** (disponible pour tous les groupes)
- **SAMAccountName** (disponible pour les groupes synchronisés à partir d’Active Directory)
- **NetbiosDomain\samAccountName** (disponible pour les groupes synchronisés à partir d’Active Directory)
- **DNSDomainName\samAccountName** (disponible pour les groupes synchronisés à partir d’Active Directory)

> [!NOTE]
> Attributs SAMAccountName et OnPremisesGroupSID sont uniquement disponibles sur les objets de groupe synchronisés à partir d’Active Directory.   Ils ne sont pas disponibles sur les groupes créés dans Azure Active Directory ou Office 365.   Les applications qui dépendent des attributs de groupe en local les obtenir uniquement les groupes synchronisés.

## <a name="options-for-applications-to-consume-group-information"></a>Options pour les applications d’utiliser les informations de groupe

Un pour les applications obtenir des informations de groupe consiste à appeler le point de terminaison de groupes de graphique afin de récupérer l’appartenance au groupe pour l’utilisateur authentifié. Cet appel garantit que tous les groupes dont un utilisateur est membre sont disponibles même lorsqu’il existe un grand nombre de groupes impliqués et l’application a besoin pour énumérer tous les groupes de l’utilisateur est membre.  Énumération de groupe est ensuite indépendante de limitations de taille de jeton.

Toutefois, si une application existante déjà s’attend à consommer les informations de groupe via des revendications dans le jeton qu’il reçoit, Azure Active Directory peut être configuré avec un nombre d’options des revendications différentes pour répondre aux besoins de l’application.  Examinez les options suivantes :

- Lorsque vous utilisez l’appartenance au groupe pour un objectif de d’autorisation d’application (si l’appartenance au groupe est obtenue à partir du jeton ou de graphique), il est préférable d’utiliser l’ID d’objet groupe, qui est immuable et unique dans Azure Active Directory et disponibles pour tous les groupes .
- Si vous utilisez le groupe de SAMAccountName pour l’autorisation, utilisez des noms de domaine qualifié ;  Il y a réduit le risque de situations qui se produisent avait un conflit de noms. SAMAccountName sur son propre peut être unique au sein d’un domaine Active Directory, mais si plusieurs domaines Active Directory est synchronisé avec un locataire Azure Active Directory il est possible pour plusieurs groupes d’avoir le même nom.
- Envisagez d’utiliser [rôles d’Application](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) pour fournir une couche d’indirection entre l’appartenance au groupe et l’application.   Puis l’application prend des décisions d’autorisation interne selon palourdes de rôle dans le jeton.
- Si l’application est configurée pour obtenir les attributs de groupe qui ont été synchronisés à partir d’Active Directory et un groupe ne contient pas ces attributs que ne seront pas incluses dans les revendications.
- Revendications de groupe dans les jetons incluent les groupes imbriqués.   Si un utilisateur est membre du groupe b dénotant un et annonçant est un membre d’a, les revendications de groupe pour l’utilisateur contiendra a et le groupe b. Pour les organisations avec une utilisation élevée de groupes imbriqués et les utilisateurs avec un grand nombre d’appartenances de groupe, le nombre de groupes répertoriés dans le jeton peut augmenter la taille de jeton.   Azure Active Directory limite le nombre de groupes, qu'il émet un jeton à 150 pour les assertions SAML et 200 pour JSON.

> Conditions préalables à l’aide des attributs de groupe synchronisés à partir d’Active Directory :   Les groupes doivent être synchronisées à partir d’Active Directory à l’aide d’Azure AD Connect.

Il existe deux étapes de configuration d’Azure Active Directory afin qu’il émette des noms de groupe pour les groupes Active Directory.

1. **Synchronisation des noms de groupe à partir d’Active Directory** avant d’Azure Active Directory peut émettre les noms de groupe ou sur le groupe de site revendications SID de groupe ou du rôle, les attributs requis doivent être synchronisés à partir d’Active Directory.  Vous devez exécuter Azure AD Connect version 1.2.70 ou version ultérieure.   Avant la version 1.2.70 Azure AD Connect synchronise les objets de groupe th à partir d’Active Directory, mais n’inclure pas les attributs de nom de groupe requis par défaut.  Vous devez mettre à niveau vers la version actuelle.

2. **Configurer l’inscription de l’application dans Azure Active Directory pour inclure des revendications de groupe dans les jetons** revendications de groupe peuvent être configuré dans la section Applications d’entreprise du portail pour une application de galerie ou hors galerie SAML SSO, ou à l’aide du manifeste d’Application dans la section des inscriptions d’applications.  Pour configurer des revendications de groupe dans l’application manifeste, consultez « Configuration d’Azure Active Directory inscription de l’Application pour les attributs de groupe » ci-dessous.

## <a name="configure-group-claims-for-saml-applications-using-sso-configuration"></a>Configurer des revendications de groupe pour les applications SAML à l’aide de la configuration de l’authentification unique

Pour configurer les revendications de groupe pour une application de galerie ou hors galerie SAML, ouvrir des Applications d’entreprise, cliquez sur l’application dans la liste et sélectionnez la configuration de l’authentification unique.

Sélectionnez l’icône Modifier en regard de « Groupes retournés dans le jeton »

![revendications de l’interface utilisateur](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Utilisez les cases pour sélectionner les groupes qui doivent être inclus dans le jeton

![revendications de l’interface utilisateur](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Sélection | Description |
|----------|-------------|
| **Tous les groupes** | Émet des groupes de sécurité et de distribution répertorie.   Elle entraîne également l’utilisateur est affecté à émettre dans une revendication « wids » rôles d’annuaire et les rôles d’application que l’utilisateur est affecté à émettre dans la revendication rôles. |
| **Groupes de sécurité** | Émet des groupes de sécurité que l’utilisateur est membre dans la revendication groupes |
| **Liste de distribution** | Émet des listes de distribution de que l’utilisateur est un membre |
| **Rôle d’annuaire** | Si l’utilisateur est affecté des rôles d’annuaire, ils sont émis comme un « wids' revendication (groupes de revendication ne sont pas être émise) |

Par exemple, pour émettre tous les groupes de sécurité que l’utilisateur est un membre de, sélectionnez les groupes de sécurité

![revendications de l’interface utilisateur](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

### <a name="advanced-options"></a>Options avancées

Le moyen de revendications de groupe sont émises peut être modifié par les paramètres sous les options avancées

Personnaliser le nom de la revendication de groupe :  Si sélectionné, un type de revendication différent peut être spécifié pour les revendications de groupe.   Entrez le type de revendication dans le champ de nom et de l’espace de noms facultatif pour la revendication dans le champ espace de noms.

![revendications de l’interface utilisateur](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Pour émettre des groupes à l’aide d’Active Directory attributs au lieu d’Azure AD objectIDs Cochez la case 'Return groupes en tant que noms au lieu de l’ID' et sélectionner le format dans la liste déroulante.  Cela remplace l’ID d’objet dans les revendications avec des valeurs de chaîne contenant les noms de groupe.   Seuls les groupes synchronisés à partir d’Active Directory seront inclus dans les revendications.

![revendications de l’interface utilisateur](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Certaines applications requièrent des informations d’appartenance de groupe apparaissent dans la revendication « rôle ». Vous pouvez éventuellement émettre de groupes de l’utilisateur en tant que rôles en cochant la case émettre des revendications d’un rôle de groupes.

![revendications de l’interface utilisateur](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Si l’option pour émettre des données de groupe en tant que rôles est utilisée, seuls les groupes seront affiche dans la revendication de rôle.  Les rôles d’Application attribué à l’utilisateur n’apparaît pas dans la revendication de rôle.

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Configurer l’inscription d’Application Azure AD pour les attributs de groupe

Revendications de groupe peuvent également être configurées dans le [revendications facultatives](../../active-directory/develop/active-directory-optional-claims.md) section de la [manifeste d’Application](../../active-directory/develop/reference-app-manifest.md).

1. Dans le portail -> Azure Active Directory -> Application inscriptions -> sélectionnez Application -> manifeste

2. Activer les revendications de l’appartenance au groupe en modifiant le groupMembershipClaim

   Les valeurs valides sont :

   - « Tout »
   - « SecurityGroup »
   - « DistributionList »
   - « DirectoryRole »

   Par exemple : 

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Valeur de revendication par défaut Qu'objectid du groupe sera émise dans le groupe.  Pour modifier la valeur de revendication pour contenir des attributs de groupe local, ou pour modifier le type de revendication de rôle, utilisez OptionalClaims configuration comme suit :

3. Définir des revendications de groupe nom configuration facultatives.

   Si vous souhaitez à des groupes dans le jeton contiennent les locaux des attributs de groupe AD dans la section de revendications facultatives spécifient quels revendication facultative de type de jeton doit être appliquée à, le nom de la revendication facultative demandé et toutes les propriétés que vous le souhaitez.  Vous pouvez spécifier plusieurs types de jetons :

   - idToken pour le jeton d’ID de OIDC
   - accessToken pour le jeton d’accès OAuth/OIDC
   - Saml2Token pour les jetons SAML.

   > [!NOTE]
   > Le type Saml2Token s’applique à la fois SAML1.1 et SAML2.0 jetons de format

   Pour chaque type de jeton approprié, modifiez la revendication groupes à utiliser la section OptionalClaims dans le manifeste. Le schéma OptionalClaims est comme suit :

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Schéma de revendications facultatives | Valeur |
   |----------|-------------|
   | **Nom :** | Doit être « groupes » |
   | **Source :** | Non utilisé. Omettez ou spécifiez la valeur null |
   | **essentielles :** | Non utilisé. Omettez ou spécifiez la valeur false |
   | **additionalProperties :** | Liste de propriétés supplémentaires.  Les options valides sont « sam_account_name », « dns_domain_and_sam_account_name », « netbios_domain_and_sam_account_name », « emit_as_roles » |

   Dans additionalProperties seul « sam_account_name », « dns_domain_and_sam_account_name », « netbios_domain_and_sam_account_name » sont nécessaires.  Si plusieurs instances sont présente, la première est utilisée et tous les autres ignorée.

   Certaines applications requièrent des informations de groupe sur l’utilisateur dans la revendication de rôle.  Pour modifier le type de revendication pour à partir d’une revendication de groupe pour une revendication de rôle, ajoutez « emit_as_roles » à des propriétés supplémentaires.  Les valeurs de groupe seront émises dans la revendication de rôle.

   > [!NOTE]
   > Si « emit_as_roles » est utilisé des rôles d’Application configuré que l’utilisateur est affecté sera n’apparaît pas dans la revendication de rôle

### <a name="examples"></a>Exemples

Émettre des groupes en tant que noms de groupe dans les jetons d’accès OAuth au format de dnsDomainName\SAMAccountName

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Pour émettre les noms de groupe à retourner dans le format de netbiosDomain\samAccountName comme les rôles de revendication SAML et jetons d’ID OIDC :

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }
 ```

## <a name="next-steps"></a>Étapes suivantes

[Qu’est-ce que l’identité hybride ?](whatis-hybrid-identity.md)