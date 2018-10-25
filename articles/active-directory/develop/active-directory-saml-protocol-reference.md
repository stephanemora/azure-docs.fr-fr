---
title: Utilisation du protocole SAML par Azure AD | Microsoft Docs
description: Cet article fournit une vue d’ensemble des profils SAML d’authentification unique et de déconnexion unique dans Azure Active Directory.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: b9ae7cf9d6fd383c7cdfa3957b5d9b94c7207d50
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900653"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Utilisation du protocole SAML par Azure AD

Azure Active Directory (Azure AD) utilise le protocole SAML 2.0 pour permettre aux applications de fournir une expérience d’authentification unique à leurs utilisateurs. Les profils SAML [Authentification unique](single-sign-on-saml-protocol.md) et [Déconnexion unique](single-sign-out-saml-protocol.md) d’Azure AD expliquent comment les assertions, les protocoles et les liaisons SAML sont utilisées dans le service de fournisseur d’identité.

Le protocole SAML a besoin du fournisseur d’identité (Azure AD) et du fournisseur de services (l’application) pour échanger des informations à leur sujet.

Lorsqu’une application est enregistrée auprès d’Azure AD, le développeur d’applications enregistre les informations liées à la fédération auprès d’Azure AD. Ces informations englobent notamment **l’URI de redirection** et **l’URI des métadonnées** de l’application.

Azure AD utilise **l’URI des métadonnées** du service cloud pour récupérer la clé de signature et l’URI de déconnexion. Si l’application ne prend pas en charge l’URI des métadonnées, le développeur doit contacter le support Microsoft pour obtenir l’URI de déconnexion et la clé de signature.

Azure Active Directory expose les points de terminaison d’authentification unique et de déconnexion unique communs (indépendants du client) et spécifiques au client. Ces URL représentent les emplacements adressables et ne jouent pas simplement le rôle d’identificateurs. Vous pouvez donc accéder au point de terminaison pour lire les métadonnées.

* Le point de terminaison propre au locataire se trouve à l’adresse `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. L’espace réservé *<TenantDomainName>* représente un nom de domaine inscrit ou le GUID TenantID d’un locataire Azure AD. Par exemple, les métadonnées de fédération du client contoso.com sont sur : https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Le point de terminaison indépendant du locataire se trouve à l’adresse `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. Dans cette adresse de point de terminaison, l’élément **common** remplace le nom de domaine ou l’ID du locataire.

Pour plus d’informations sur les documents de métadonnées de fédération publiés par Azure AD, consultez la page [Métadonnées de fédération](azure-ad-federation-metadata.md).
