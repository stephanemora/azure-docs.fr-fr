---
title: Gérer les données utilisateur dans Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment supprimer ou exporter des données utilisateur dans Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: dacff48be3fbf16fc719f5a0395937b1f5acc979
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712536"
---
# <a name="manage-user-data-in-azure-ad-b2c"></a>Gérer les données utilisateur dans Azure AD B2C

 Cet article fournit des informations sur la façon dont vous pouvez gérer les données utilisateur dans Azure Active Directory (AD) B2C en utilisant les opérations fournies par l’[API Graph Azure Active Directory](https://msdn.microsoft.com/en-us/library/azure/ad/graph/api/api-catalog). La gestion des données utilisateur inclut la possibilité de supprimer ou d’exporter des données des journaux d’audit.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Supprimer des données utilisateur

Les données utilisateur sont stockées dans l’annuaire Azure AD B2C et dans les journaux d’audit. Toutes les données d’audit utilisateur sont conservées pendant 30 jours dans Azure AD B2C. Si vous souhaitez supprimer des données utilisateur avant l’expiration de ces 30 jours, vous pouvez utiliser l’opération [Supprimer un utilisateur](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#DeleteUser). Une opération DELETE est requise pour chacun des clients Azure AD B2C susceptibles de contenir des données. 

Un ID objet est attribué à chaque utilisateur dans Azure AD B2C. L’ID objet constitue un identificateur non ambigu que vous pouvez utiliser pour supprimer des données utilisateur dans Azure AD B2C.  En fonction de votre architecture, l’ID objet peut être un identificateur de corrélation utile pour d’autres services, tels que des bases de données de gestion des finances, du marketing et de la relation client.  

La manière la plus précise d’obtenir l’ID objet pour un utilisateur consiste à le faire dans le cadre d’un parcours d’authentification auprès d’Azure AD B2C.  Si une demande de données valide est reçue d’un utilisateur utilisant d’autres méthodes, un processus hors connexion, tel qu’une recherche effectuée par un agent de service clientèle, peut être nécessaire pour trouver l’utilisateur et noter l’ID objet associé. 

L’exemple suivant montre un flux de suppression de données possible :

1. L’utilisateur se connecte et sélectionne **Supprimer mes données**.
2. L’application offre une option permettant de supprimer les données dans une Section Administration.
3. L’application force une authentification auprès d’Azure AD B2C. Azure AD B2C retourne un jeton avec l’ID objet de l’utilisateur à l’application. 
4. L’application reçoit le jeton et l’ID objet est utilisé pour supprimer les données utilisateur via un appel à l’API Azure AD Graph. L’API Azure AD Graph supprime les données utilisateur et retourne un code d’état 200 OK.
5. L’application orchestre la suppression des données utilisateur dans d’autres systèmes organisationnels en fonction des besoins à l’aide de l’ID objet ou d’autres identificateurs.
6. L’application confirme la suppression des données et indique les étapes suivantes à l’utilisateur.

## <a name="export-customer-data"></a>Exporter des données client

Le processus d’exportation de données client à partir d’Azure AD B2C est similaire au processus de suppression.

Les données d’utilisateur d’Azure AD B2C sont limitées à ce qui suit :

- **Données stockées dans Azure Active Directory** : les données peuvent être récupérées dans un parcours utilisateur d’authentification Azure AD B2C à l’aide de l’ID objet ou de tout ID de connexion tel qu’un e-mail ou un nom d’utilisateur.  
- **Rapport d’événements d’audit spécifiques de l’utilisateur** : les données sont indexées à l’aide de l’ID objet.

Dans l’exemple suivant de flux de données d’exportation, les étapes décrites comme étant effectuées par l’application peuvent également être effectuées par un processus principal ou par un utilisateur avec un rôle d’administrateur dans l’annuaire :

1. L’utilisateur se connecte à l’application. Azure AD B2C applique une authentification avec une autorisation multifacteur si nécessaire.
2. L’application utilise les informations d’identification de l’utilisateur pour appeler une opération de l’API Azure AD Graph afin de récupérer les attributs utilisateur. L’API Azure AD Graph fournit les données d’attribut au format JSON. Selon le schéma, le contenu du jeton d’identité peut être défini pour inclure toutes les données personnelles d’un utilisateur.
3. L’application récupère l’activité d’audit de l’utilisateur final. L’API Azure AD Graph fournit les données d’événement à l’application.
4. L’application agrège les données et les met à la disposition de l’utilisateur.

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment gérer la manière dont les utilisateurs peuvent accéder à votre application, voir [Gérer l’accès utilisateur](manage-user-access.md).




