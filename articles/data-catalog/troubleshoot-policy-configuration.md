---
title: Configurer la stratégie Azure Active Directory pour Azure Data Catalog
description: Il peut arriver que vous puissiez vous connecter au portail Azure Data Catalog, mais qu'en tentant de vous connecter à l'outil de référencement de la source de données, un message d'erreur s'affiche.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/06/2019
ms.openlocfilehash: e69a7e3bd104d0fb82b248b6560d4fd082c88426
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "62116599"
---
# <a name="azure-active-directory-policy-configuration"></a>Configuration de la stratégie Azure Active Directory

Il arrive que vous puissiez vous connecter au portail d’Azure Data Catalog, mais que lorsque vous tentez de vous connecter à l’outil de référencement de la source de données, un message d’erreur s’affiche et vous empêche de vous connecter. Cette erreur peut se produire lorsque vous êtes sur le réseau d'entreprise ou lorsque vous vous connectez depuis l'extérieur de celui-ci.

## <a name="registration-tool"></a>Outil de référencement

L’outil de référencement utilise *l’authentification par formulaire* pour valider les connexions des utilisateurs avec Azure Active Directory. Pour une connexion réussie, un administrateur Azure Active Directory doit activer l’authentification par formulaire dans la *stratégie d’authentification globale*.

La stratégie d’authentification globale vous permet d’activer séparément des méthodes d’authentification pour les connexions intranet et extranet, comme illustré dans l’image suivante. Des erreurs de connexion peuvent survenir si l'authentification par formulaire n'est pas activée pour le réseau à partir duquel vous vous connectez.

 ![Stratégie d’authentification globale Azure Active Directory](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Pour plus d’informations, consultez [Configuration des stratégies d’authentification](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Étapes suivantes

* [Créer un catalogue Azure Data Catalog](data-catalog-get-started.md)