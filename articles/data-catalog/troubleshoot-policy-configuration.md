---
title: Comment configurer la stratégie Azure Active Directory pour Azure Data Catalog
description: Vous pouvez rencontrer une situation où vous pouvez vous connecter au portail Azure Data Catalog, mais lorsque vous tentez de vous connecter à l’outil d’inscription de sources de données, vous rencontrez un message d’erreur.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/06/2019
ms.openlocfilehash: e69a7e3bd104d0fb82b248b6560d4fd082c88426
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116599"
---
# <a name="azure-active-directory-policy-configuration"></a>Configuration de la stratégie Azure Active Directory

Il arrive que vous puissiez vous connecter au portail d’Azure Data Catalog, mais que lorsque vous tentez de vous connecter à l’outil de référencement de la source de données, un message d’erreur s’affiche et vous empêche de vous connecter. Cette erreur peut se produire lorsque vous êtes sur le réseau d’entreprise ou lorsque vous vous connectez à partir de l’extérieur du réseau d’entreprise.

## <a name="registration-tool"></a>Outil d’inscription

L’outil de référencement utilise *l’authentification par formulaire* pour valider les connexions des utilisateurs avec Azure Active Directory. Pour une connexion réussie, un administrateur Azure Active Directory doit activer l’authentification par formulaire dans la *stratégie d’authentification globale*.

La stratégie d’authentification globale vous permet d’activer séparément des méthodes d’authentification pour les connexions intranet et extranet, comme illustré dans l’image suivante. Erreurs de connexion peuvent survenir si l’authentification par formulaire n’est pas activée pour le réseau à partir duquel vous vous connectez.

 ![Stratégie d’authentification globale Azure Active Directory](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Pour plus d’informations, consultez [Configuration des stratégies d’authentification](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Étapes suivantes

* [Créer un catalogue de données Azure](data-catalog-get-started.md)