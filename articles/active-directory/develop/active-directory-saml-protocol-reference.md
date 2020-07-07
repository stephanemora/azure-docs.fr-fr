---
title: Utilisation du protocole SAML par Azure AD
description: Cet article fournit une vue d’ensemble des profils SAML d’authentification unique et de déconnexion unique dans Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 44d06030d8015d2df9499ce903eb9cb06e1ef27a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885648"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Utilisation du protocole SAML par Azure AD

Azure Active Directory (Azure AD) utilise le protocole SAML 2.0 pour permettre aux applications de fournir une expérience d’authentification unique à leurs utilisateurs. Les profils SAML [Authentification unique](single-sign-on-saml-protocol.md) et [Déconnexion unique](single-sign-out-saml-protocol.md) d’Azure AD expliquent comment les assertions, les protocoles et les liaisons SAML sont utilisées dans le service de fournisseur d’identité.

Le protocole SAML a besoin du fournisseur d’identité (Azure AD) et du fournisseur de services (l’application) pour échanger des informations à leur sujet.

Lorsqu’une application est enregistrée auprès d’Azure AD, le développeur d’applications enregistre les informations liées à la fédération auprès d’Azure AD. Ces informations englobent notamment **l’URI de redirection** et **l’URI des métadonnées** de l’application.

Azure AD utilise **l’URI des métadonnées** du service cloud pour récupérer la clé de signature et l’URI de déconnexion. Le client peut ouvrir l’application dans **Azure AD -> Inscription de l’application** puis, dans **Paramètres -> Propriétés**, il peut mettre à jour l’URL de déconnexion. Ainsi, Azure AD peut envoyer la réponse à l’URL correcte. 

Azure Active Directory expose les points de terminaison d’authentification unique et de déconnexion unique communs (indépendants du client) et spécifiques au client. Ces URL représentent les emplacements adressables et ne jouent pas simplement le rôle d’identificateurs. Vous pouvez donc accéder au point de terminaison pour lire les métadonnées.

* Le point de terminaison propre au locataire se trouve à l’adresse `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. L’espace réservé *\<TenantDomainName>* représente un nom de domaine inscrit ou le GUID TenantID d’un locataire Azure AD. Par exemple, les métadonnées de fédération du client contoso.com sont sur : https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Le point de terminaison indépendant du locataire se trouve à l’adresse `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. Dans cette adresse de point de terminaison, l’élément **common** remplace le nom de domaine ou l’ID du locataire.

Pour plus d’informations sur les documents de métadonnées de fédération publiés par Azure AD, consultez la page [Métadonnées de fédération](../azuread-dev/azure-ad-federation-metadata.md).
