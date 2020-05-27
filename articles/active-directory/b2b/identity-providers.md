---
title: Fournisseurs d’identité pour les identités externes – Azure AD
description: Azure Active Directory B2B Collaboration prend en charge l’authentification multifacteur (MFA) pour un accès sélectif à vos applications d’entreprise
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 395473e70137ead2b7185d54d165f82e9b3f1f04
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595708"
---
# <a name="identity-providers-for-external-identities"></a>Fournisseurs d’identité pour les identités externes

Un *fournisseur d’identité* crée, entretient et gère les informations d’identité tout en fournissant des services d’authentification pour les applications. Lors du partage de vos applications et ressources avec des utilisateurs externes, Azure AD est le fournisseur d’identité par défaut. Cela signifie que, quand vous invitez des utilisateurs externes qui disposent déjà d’un compte Azure AD ou Microsoft, ceux-ci peuvent se connecter automatiquement sans configuration supplémentaire de votre part.

Cependant, vous pouvez leur permettre de se connecter avec différents fournisseurs d’identité. Par exemple, vous pouvez configurer une fédération avec des fournisseurs d’identité sociale pris en charge par Azure AD, dont Google et Facebook. Vous pouvez également créer une fédération avec n’importe quel fournisseur d’identité externe prenant en charge les protocoles SAML ou WS-Fed. Avec une fédération de fournisseur d’identité externe, vous pouvez offrir à des utilisateurs externes la possibilité de se connecter à vos applications avec leurs comptes de réseaux social ou d’entreprise existants.

## <a name="how-it-works"></a>Fonctionnement

Les identités externes Azure AD sont préconfigurées pour une fédération avec Google et Facebook. Pour configurer ces fournisseurs d’identité dans votre locataire Azure AD, vous devez créer une application chez chaque fournisseur d’identité et configurer des informations d’identification. Vous recevez un ID de client ou d’application et une clé secrète de client ou d’application que vous pouvez ensuite ajouter à votre locataire Azure AD.

Une fois que vous avez ajouté un fournisseur d’identité à votre locataire Azure AD :

- Lorsque vous invitez un utilisateur externe à des applications ou ressources se trouvant dans votre organisation, l’utilisateur externe peut se connecter avec son propre compte auprès de ce fournisseur d’identité.
- Lorsque vous activez l’[inscription en libre-service](self-service-sign-up-overview.md) pour vos applications, des utilisateurs externes peuvent s’inscrire pour accéder à vos applications à l’aide de leurs propres comptes auprès des fournisseurs d’identité que vous avez ajoutés. 

Quand vous échangez votre invitation ou vous inscrivez pour votre application, l’utilisateur externe a la possibilité de se connecter et de s’authentifier auprès du fournisseur d’identité sociale :

![Capture d’écran montrant l’écran de connexion avec les options Google et Facebook](media/identity-providers/sign-in-with-social-identity.png)

Pour une expérience de connexion optimale, fédérez des fournisseurs d’identité autant que possible afin de pouvoir offrir à vos invités une expérience de connexion transparente quand ils accèdent à vos applications.  

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment ajouter des fournisseurs d’identité pour la connexion à vos applications, reportez-vous aux articles suivants :

- [Ajouter Google](google-federation.md) à votre liste de fournisseurs d’identité sociale
- [Ajouter Facebook](facebook-federation.md) à votre liste de fournisseurs d’identité sociale
- [Configurer une fédération directe](direct-federation.md) avec n’importe quelle organisation dont le fournisseur d’identité prend en charge le protocole SAML 2.0 ou WS-Fed. Notez qu’une fédération directe n’est pas possible pour des flux utilisateur d’inscription en libre-service.
