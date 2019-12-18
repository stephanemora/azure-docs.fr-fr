---
title: Disponibilité des régions et résidence des données
titleSuffix: Azure AD B2C
description: Disponibilité des régions, résidence des données et informations sur les locataires Azure Active Directory B2C en préversion.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1b09eb85df6748fed042731ac90ebbf20c65b702
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950508"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C : Disponibilité des régions et résidence des données

La disponibilité régionale et la résidence des données sont deux concepts très différents qui ne s’appliquent pas à Azure Active Directory B2C de la même façon qu’à Azure. Cet article explique les différences entre ces deux concepts et compare la manière dont ils s’appliquent à Azure et Azure Active Directory B2C.

Azure Active Directory B2C est **généralement disponible dans le monde entier** avec l’option de **résidence des données** dans les régions **États-Unis, Europe ou Asie-Pacifique**.

La [disponibilité régionale](#region-availability) fait référence à l’endroit où se trouve le service pour utilisation.

La [résidence des données](#data-residency) fait référence à l’endroit où sont stockées les données des utilisateurs.

## <a name="region-availability"></a>Disponibilité des régions

Azure Active Directory B2C est disponible dans le monde entier via le cloud public Azure.

Cela diffère du modèle suivi par la plupart des autres services Azure, qui associent généralement la *disponibilité* à la *résidence des données*. C’est le cas par exemple dans la page [Produits disponibles par région](https://azure.microsoft.com/regions/services/) et la [calculatrice de tarification Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Résidence des données

Azure AD B2C stocke les données utilisateur dans la région États-Unis, Europe ou Asie-Pacifique.

La résidence des données est déterminée par le pays ou la région que vous sélectionnez quand vous [créez un locataire Azure AD B2C](active-directory-b2c-get-started.md) :

![Capture d’écran d’un client de la préversion](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

Les données des pays/régions suivants sont conservées aux **États-Unis** :

> États-Unis, Canada, Costa Rica, République dominicaine, Salvador, Guatemala, Mexique, Panama, Porto Rico et Trinité-et-Tobago

Les données des pays/régions suivants sont conservées en **Europe** :

> Algérie, Autriche, Azerbaïdjan, Bahreïn, Bélarus, Belgique, Bulgarie, Croatie, Chypre, République tchèque, Danemark, Égypte, Estonie, Finlande, France, Allemagne, Grèce, Hongrie, Islande, Irlande, Israël, Italie, Jordanie, Kazakhstan, Kenya, Koweït, Lettonie, Liban, Liechtenstein, Lituanie, Luxembourg, Macédoine du Nord, Malte, Monténégro, Maroc, Pays-Bas, Nigeria, Norvège, Oman, Pakistan, Pologne, Portugal, Qatar, Roumanie, Russie, Arabie saoudite, Serbie, Slovaquie, Slovénie, Afrique du Sud, Espagne, Suède, Suisse, Tunisie, Turquie, Ukraine, Émirats Arabes Unis et Royaume-Uni.

Les données des pays/régions suivants sont conservées en **Asie-Pacifique** :

> Afghanistan, Hong Kong (R.A.S.), Inde, Indonésie, Japon, Corée, Malaisie, Philippines, Singapour, Sri Lanka, Taïwan et Thaïlande.

Les pays/régions suivants sont en cours d’ajout à cette liste. Pour le moment, vous pouvez toujours utiliser Azure Active Directory B2C en choisissant l’un des pays ci-dessus.

> Argentine, Australie, Brésil, Chili, Colombie, Équateur, Iraq, Nouvelle-Zélande, Paraguay, Pérou, Uruguay et Venezuela.

## <a name="preview-tenant"></a>Client de la version préliminaire

Si vous avez créé un locataire B2C pendant la période de préversion d’Azure AD B2C, il est probable que votre **type de locataire** indique **Aperçu du client**.

Si c’est le cas, vous devez utiliser votre locataire UNIQUEMENT à des fins de développement et de test. N’UTILISEZ PAS un locataire en préversion pour les applications de production.

**Il n’existe aucun chemin de migration** à partir d’un locataire B2C en préversion vers un locataire B2C à l’échelle de la production. Vous devez créer un locataire B2C pour vos applications de production.

Il existe des problèmes connus liés à la suppression d’un locataire B2C en préversion et à la recréation d’un locataire B2C à l’échelle de la production portant le même nom de domaine. *Vous devez créer un locataire B2C à l’échelle de la production portant un nom de domaine différent*.

![Capture d’écran d’un client de la préversion](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)