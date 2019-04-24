---
title: Configurer un domaine d’application serveur de publication | Azure
description: Découvrez comment configurer un domaine d’application serveur de publication pour permettre aux utilisateurs de savoir où leurs informations sont envoyées.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: celested
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: efbf448770bfcf797d6bf01cd3c28dc98023adff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60299765"
---
# <a name="how-to-configure-an-applications-publisher-domain-preview"></a>Activation Configurer un domaine d’application serveur de publication (version préliminaire)

Domaine du serveur de publication d’une application est présentée aux utilisateurs sur le [invite de consentement de l’application](application-consent-experience.md) pour permettre aux utilisateurs de savoir où leurs informations sont envoyées. Les applications mutualisées qui sont inscrits après le 21 mai 2019 qui n’ont pas un domaine de l’éditeur s’affichera en tant que **non vérifiés**. Les applications mutualisées sont des applications qui prennent en charge des comptes en dehors d’un seul annuaire d’organisation ; par exemple, prend en charge de tous les comptes Azure AD, ou prennent en charge tous les comptes Azure AD et les comptes Microsoft personnels.

## <a name="new-applications"></a>Nouvelles applications

Lorsque vous inscrivez une nouvelle application, le domaine du serveur de publication de votre application peut être défini à une valeur par défaut. La valeur dépend où l’application est inscrite, en particulier si l’application est inscrite dans un locataire et indique si le client a locataire des domaines vérifiés.

S’il existe des domaines vérifiés de locataire, domaine de l’application de l’éditeur principal domaine vérifié du locataire par défaut. S’il existe aucun locataire domaines ne vérifiés (ce qui est le cas lorsque l’application n’est pas enregistrée dans un locataire), le domaine de l’éditeur de l’application sera définie sur null.

Le tableau suivant récapitule le comportement par défaut de la valeur de domaine du serveur de publication.  

| Domaines vérifiés de locataire | Valeur par défaut du domaine du serveur de publication |
|-------------------------|----------------------------|
| null | null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| -*. onmicrosoft.com<br/>- domain1.com<br/>-domain2.com (principal) | domain2.com |

Si le domaine du serveur de publication d’une application mutualisée n’est pas défini, ou si elle est définie sur un domaine qui se terminent par. onmicrosoft.com, invite de consentement de l’application affichera **non vérifiés** à la place du domaine du serveur de publication.

## <a name="grandfathered-applications"></a>Droits acquis des applications

Si votre application a été inscrite avant le 21 mai 2019, invite de consentement de votre application n’affichera pas **non vérifiés** si vous n’avez pas défini un domaine de l’éditeur. Nous vous recommandons de définir le serveur de publication la valeur de domaine afin que les utilisateurs peuvent voir ces informations sur l’invite de consentement de votre application.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Configurer le domaine de l’éditeur à l’aide du portail Azure

Pour définir le domaine de l’éditeur de votre application, procédez comme suit.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.

1. Si votre compte est présent dans plusieurs locataires Azure AD :
   1. Sélectionnez votre profil dans le menu situé en haut à droite de la page, puis **changer de répertoire**.
   1. Modifier votre session au locataire Azure AD où vous souhaitez créer votre application.

1. Accédez à [Azure Active Directory > inscriptions](https://go.microsoft.com/fwlink/?linkid=2083908) pour rechercher et sélectionner l’application que vous souhaitez configurer.

   Une fois que vous avez sélectionné l’application, vous verrez l’application **vue d’ensemble** page.

1. À partir de l’application **vue d’ensemble** page, sélectionnez le **Branding** section.

1. Rechercher la **domaine de l’éditeur** champ et sélectionnez une des options suivantes :

   - Sélectionnez **configurer un domaine** si vous n’avez pas configuré un domaine déjà.
   - Sélectionnez **domaine de mise à jour** si un domaine a déjà été configuré.

Si votre application est inscrite dans un locataire, vous verrez deux onglets pour sélectionner à partir de : **Sélectionnez un domaine vérifié** et **vérifier un nouveau domaine**.

Si votre application n’est pas inscrite dans un locataire, vous verrez uniquement la possibilité de vérifier un nouveau domaine pour votre application.

### <a name="to-verify-a-new-domain-for-your-app"></a>Pour vérifier un nouveau domaine pour votre application

1. Créez un fichier nommé `microsoft-identity-association.json` et collez l’extrait de code JSON suivant.

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. Remplacez l’espace réservé *{YOUR-application-ID-HERE}* avec l’ID d’application (client) qui correspond à votre application.

1. Héberger le fichier à : `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`. Remplacez l’espace réservé *{YOUR-domaine-ici}* pour correspondre au domaine vérifié.

1. Cliquez sur le **Vérifiez et enregistrez le domaine** bouton.

### <a name="to-select-a-verified-domain"></a>Pour sélectionner un domaine vérifié

- Si votre client a des domaines vérifiés, sélectionnez un des domaines à partir de la **sélectionner un domaine vérifié** liste déroulante.

## <a name="implications-on-the-app-consent-prompt"></a>Invite de consentement de conséquences sur l’application

Configurer le domaine du serveur de publication a un impact sur ce que les utilisateurs voient à l’invite de consentement de l’application. Pour bien comprendre les composants de l’invite de consentement, consultez [comprendre le consentement de l’application des expériences](application-consent-experience.md).

Le tableau suivant décrit le comportement pour les applications créées avant le 21 mai 2019.

![Invite de consentement pour les applications créées avant le 21 mai 2019](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

Le comportement pour les nouvelles applications créés après le 21 mai 2019 varie selon le domaine du serveur de publication et le type d’application. Le tableau suivant décrit les modifications que vous devriez voir les différentes combinaisons de configurations.

![Invite de consentement pour les applications créées après le 21 mai 2019](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Implications sur les URI de redirection

Les applications qui connectent les utilisateurs à n’importe quel compte professionnel ou scolaire ou des comptes personnels Microsoft ([mutualisée](single-and-multi-tenant-apps.md)) sont soumis à quelques restrictions lors de la spécification URI de redirection.

### <a name="single-root-domain-restriction"></a>Restriction de domaine racine unique

Lorsque la valeur de domaine de serveur de publication pour les applications d’architecture mutualisées est définie à null, applications sont limitées à partager un domaine racine unique pour l’URI de redirection. Par exemple, la combinaison de valeurs suivante n’est pas autorisée, car le domaine racine, contoso.com, fabrikam.com ne correspond pas.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Restrictions de sous-domaine

Sous-domaines sont autorisés, mais vous devez inscrire explicitement le domaine racine. Par exemple, tandis que les URI suivants partagent un domaine racine unique, la combinaison n’est pas autorisée.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

Toutefois, si le développeur ajoute explicitement le domaine racine, la combinaison est autorisée.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Exceptions

Les cas suivants ne sont pas soumises à la limitation du domaine racine unique :

- Applications à locataire unique, ou les applications qui ciblent des comptes dans un répertoire unique
- Utilisation de l’hôte local comme URI de redirection
- URI de redirection avec des schémas personnalisés (non-HTTP ou HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Configurer le domaine de l’éditeur par programmation

Il n’existe actuellement aucune prise en charge de l’API REST ou PowerShell pour configurer le domaine de l’éditeur par programmation.
