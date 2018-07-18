---
title: Exporter et supprimer vos données de Machine Learning Studio - Azure | Microsoft Docs
description: Les données intégrées au produit stockées par Azure Machine Learning Studio sont disponibles pour l’exportation et la suppression via le portail Azure, ainsi que via des API REST authentifiées. Les données de télémétrie sont accessibles via le portail de confidentialité Azure. Cet article vous montre comment procéder.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
manager: cgronlun
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.date: 05/25/2018
ms.openlocfilehash: 2ebd777a9723732de6ebbdf07020802190cb4b61
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969280"
---
# <a name="export-and-delete-in-product-user-data-from-machine-learning-studio"></a>Exporter et supprimer de Machine Learning Studio des données utilisateur intégrées dans le produit

Vous pouvez supprimer ou exporter des données intégrées au produit stockées par Azure Machine Learning Studio en utilisant le portail Azure, l’interface de Studio, PowerShell et des API REST authentifiées. Cet article vous explique comment procéder. 

Les données de télémétrie sont accessibles via le portail de confidentialité Azure. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-collect"></a>Quels sont les types de données utilisateur collectées par Studio ?

Pour ce service, les données utilisateur sont constituées d’informations sur les utilisateurs autorisés à accéder aux espaces de travail et d’enregistrements de télémétrie des interactions utilisateur avec le service.

Il existe deux types de données utilisateur dans Machine Learning Studio :
- **Données de compte personnel :** ID de compte et adresses e-mail associées à un compte.
- **Données client :** données que vous avez chargées pour les analyser.

## <a name="studio-account-types-and-how-data-is-stored"></a>Types de compte Studio et stockage des données

Il existe trois types de comptes dans Machine Learning Studio. Le type de compte que vous avez détermine la façon dont vos données sont stockées, et comment vous pouvez les supprimer ou les exporter.

- A **espace de travail d’invité** est un compte gratuit et anonyme. Vous vous inscrivez sans fournir d’informations d’identification, comme une adresse e-mail ou un mot de passe.
    -  Les données sont effacées après l’expiration de l’espace de travail d’invité.
    - Les utilisateurs invités peuvent exporter des données client via l’interface utilisateur, des API REST ou le package PowerShell.
- Un **espace de travail gratuit** est un compte gratuit auquel vous vous connectez avec des informations d’identification Microsoft : une adresse e-mail et un mot de passe.
    - Vous pouvez exporter et supprimer des données personnelles et client, qui sont soumises à des demandes de droits de la personne concernée (DSR, Data Subject Rights).
    - Vous pouvez exporter des données client via l’interface utilisateur, des API REST ou le package PowerShell.
    - Pour les espaces de travail gratuits n’utilisant pas de comptes Azure AD, la télémétrie peut être exportée via le portail de confidentialité.
    - Quand vous supprimez l’espace de travail, vous supprimez toutes les données client personnelles.
- Un **espace de travail standard** est un compte payant auquel vous accédez avec des informations d’identification de connexion.
    - Vous pouvez exporter et supprimer des données client personnelles, qui sont soumises à des demandes de droits DSR.
    - Vous pouvez accéder aux données via le portail de confidentialité Azure.
    - Vous pouvez exporter des données personnelles et client via l’interface utilisateur, des API REST ou le package PowerShell.
    - Vous pouvez supprimer vos données dans le portail Azure.

## <a name="delete-workspace-data-in-studio"></a>Supprimer les données d’un espace de travail dans Studio 

### <a name="delete-individual-assets"></a>Supprimer des ressources individuelles

Les utilisateurs peuvent supprimer des ressources dans un espace de travail en les sélectionnant, puis en sélectionnant le bouton Supprimer.

![Supprimer des ressources dans Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Supprimer tout un espace de travail

Les utilisateurs peuvent également supprimer tout leur espace de travail :
- Espace de travail payant : supprimez-le via le portail Azure.
- Espace de travail gratuit : utilisez le bouton Supprimer dans le volet **Paramètres**.

![Supprimer un espace de travail gratuit dans Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-data-with-powershell"></a>Exporter des données de Studio avec PowerShell
Utilisez PowerShell pour exporter toutes les informations à un format portable à partir d’Azure Machine Learning Studio en utilisant des commandes. Pour plus d’informations, consultez l’article [Module PowerShell pour Azure Machine Learning](powershell-module.md).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une documentation portant sur les services web et la tarification du plan d’engagement, consultez [Informations de référence sur l’API REST d’Azure Machine Learning](https://docs.microsoft.com/rest/api/machinelearning/). 
