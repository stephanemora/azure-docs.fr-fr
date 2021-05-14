---
title: Fournisseurs d’identité pour les identités externes – Azure AD
description: Découvrez comment utiliser Azure AD comme fournisseur d’identité par défaut pour le partage avec des utilisateurs externes.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 184ef66a0175c301497fa536c86eddc169995698
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108315360"
---
# <a name="identity-providers-for-external-identities"></a>Fournisseurs d’identité pour les identités externes

> [!NOTE]
> Certaines des fonctionnalités mentionnées dans cet article sont des fonctionnalités en préversion publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Un *fournisseur d’identité* crée, entretient et gère les informations d’identité tout en fournissant des services d’authentification pour les applications. Lors du partage de vos applications et ressources avec des utilisateurs externes, Azure AD est le fournisseur d’identité par défaut. Cela signifie que, quand vous invitez des utilisateurs externes qui disposent déjà d’un compte Azure AD ou Microsoft, ceux-ci peuvent se connecter automatiquement sans configuration supplémentaire de votre part.

Outre des comptes Azure AD, la solution External Identities offre un vaste éventail de fournisseurs d’identité.

- **Comptes Microsoft** (préversion) : les utilisateurs invités peuvent utiliser leur compte Microsoft (MSA) personnel pour accepter vos invitations B2B Collaboration. Lors de la configuration d’un flux utilisateur d’inscription en libre-service, vous pouvez ajouter un [Compte Microsoft (préversion)](microsoft-account.md) en tant que fournisseur d’identité autorisé. Aucune configuration supplémentaire n’est nécessaire pour rendre ce fournisseur d’identité disponible pour les flux utilisateur.

- **Code secret à usage unique d’e-mail** (préversion) : lors de l’acceptation d’une invitation ou de l’accès à une ressource partagée, un utilisateur invité peut demander un code temporaire qui est envoyé à son adresse e-mail. Puis, il entre ce code pour se connecter. La fonctionnalité de code secret à usage unique d’e-mail authentifie les utilisateurs invités B2B quand ceux-ci ne peuvent pas s’authentifier par d’autres moyens. Lors de la configuration d’un flux utilisateur d’inscription en libre-service, vous pouvez ajouter un **Code secret à usage unique d’e-mail (préversion)** en tant que fournisseur d’identité autorisé. Une configuration est requise. Consultez [Authentification par envoi d’un code secret à usage unique par e-mail](one-time-passcode.md).

- **Google** : la fédération des services Google permet à des utilisateurs externes de donner suite à des invitations que vous leur avez envoyées en se connectant à vos applications avec leurs propres comptes Gmail. Vous pouvez également utiliser la fédération des services Google dans vos flux d’utilisateurs d’inscription en libre-service. Découvrez comment [ajouter Google comme fournisseur d’identité](google-federation.md).
   > [!IMPORTANT]
   > **À partir du second semestre 2021**, Google [déprécie la prise en charge de la connexion aux vues web](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Si vous utilisez la fédération Google pour les invitations B2B ou [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md), ou bien si vous utilisez l’inscription en libre-service avec Gmail, les utilisateurs de Google Gmail ne pourront pas se connecter si vos applications effectuent l’authentification des utilisateurs via une vue web incorporée. [Plus d’informations](google-federation.md#deprecation-of-web-view-sign-in-support)

- **Facebook** : lors de la création d’une application, vous pouvez configurer l’inscription en libre-service et activer un fédération Facebook afin que les utilisateurs puissent s’inscrire à votre application en utilisant leurs propres comptes Facebook. Facebook ne peut être utilisé que pour des flux d’utilisateurs d’inscription en libre-service, et n’est pas disponible en tant qu’option de connexion lorsque des utilisateurs donnent suite à des invitations de votre part. Découvrez comment [ajouter Facebook comme fournisseur d’identité](facebook-federation.md).

- **Fédération directe** : vous pouvez également configurer une fédération avec n’importe quel fournisseur d’identité externe prenant en charge les protocoles SAML ou WS-Fed. Une fédération directe permet à des utilisateurs externes de donner suite à des invitations que vous leur avez envoyées en se connectant à vos applications avec leurs comptes professionnels ou de réseaux sociaux existants. Découvrez comment [configurer la fédération directe](direct-federation.md).
   > [!NOTE]
   > Vous ne pouvez pas utiliser des fournisseurs d’identité de fédération directe dans vos flux d’utilisateurs d’inscription en libre-service.

## <a name="adding-social-identity-providers"></a>Ajout de fournisseurs d’identité sociale

Azure AD étant activé par défaut pour l’inscription en libre-service, les utilisateurs ont toujours la possibilité de s’inscrire à l’aide d’un compte Azure AD. Toutefois, vous pouvez activer d’autres fournisseurs d’identité, dont des fournisseurs d’identité sociale tels que Google ou Facebook. Pour configurer des fournisseurs d’identité sociale dans votre locataire Azure AD, vous devez créer une application au niveau du fournisseur d’identité et configurer des informations d’identification. Vous recevez un ID de client ou d’application et une clé secrète de client ou d’application que vous pouvez ensuite ajouter à votre locataire Azure AD.

Une fois que vous avez ajouté un fournisseur d’identité à votre locataire Azure AD :

- Lorsque vous invitez un utilisateur externe à des applications ou ressources se trouvant dans votre organisation, l’utilisateur externe peut se connecter avec son propre compte auprès de ce fournisseur d’identité.
- Lorsque vous activez l’[inscription en libre-service](self-service-sign-up-overview.md) pour vos applications, des utilisateurs externes peuvent s’inscrire pour accéder à vos applications à l’aide de leurs propres comptes auprès des fournisseurs d’identité que vous avez ajoutés. Ils peuvent choisir parmi les options de fournisseurs d’identité sociale que vous avez mises à disposition sur la page d’inscription :

   ![Capture d’écran montrant l’écran de connexion avec les options Google et Facebook](media/identity-providers/sign-in-with-social-identity.png)

Pour une expérience de connexion optimale, fédérez des fournisseurs d’identité autant que possible afin de pouvoir offrir à vos invités une expérience de connexion transparente quand ils accèdent à vos applications.  

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment ajouter des fournisseurs d’identité pour la connexion à vos applications, reportez-vous aux articles suivants :

- [Ajouter une authentification par envoi d’un code secret à usage unique par e-mail](one-time-passcode.md)
- [Ajouter Google](google-federation.md) en tant que fournisseur d’identité sociale autorisé
- [Ajouter Facebook](facebook-federation.md) en tant que fournisseur d’identité sociale autorisé
- [Configurer une fédération directe](direct-federation.md) avec n’importe quelle organisation dont le fournisseur d’identité prend en charge le protocole SAML 2.0 ou WS-Fed. Notez qu’une fédération directe n’est pas possible pour des flux utilisateur d’inscription en libre-service.
