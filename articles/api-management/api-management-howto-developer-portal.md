---
title: Accès et personnalisation du nouveau portail des développeurs - Gestion des API Azure | Microsoft Docs
description: Découvrez comment utiliser le nouveau portail des développeurs pour la gestion des API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2019
ms.author: apimpm
ms.openlocfilehash: deb3fdf3069aaad4982d71806c209fe516e3c3d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66743574"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>Accès et personnalisation du nouveau portail des développeurs pour la gestion des API Azure

Cet article explique comment accéder au nouveau portail des développeurs dédié à la gestion des API Azure. Il vous guide tout au long de l’expérience d’utilisation de l’éditeur visuel, lors de l’ajout et de la modification de contenu, et de la personnalisation de l’apparence du site web.

![Nouveau portail des développeurs pour la gestion des API](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a>Prérequis

- Suivez ce guide de démarrage rapide : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
- Importez et publiez une instance Gestion des API Azure. Pour plus d’informations, consultez [Importer et publier](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> Le nouveau portail des développeurs est actuellement en préversion.

## <a name="managed-and-self-hosted-versions"></a>Version managée et version auto-hébergée

Vous pouvez générer votre portail des développeurs de deux manières :

- **version managée**, via la modification et la personnalisation du portail intégré dans votre instance de gestion des API et accessible via l’URL `<your-api-management-instance-name>.developer.azure-api.net` ;
- **version auto-hébergée**, via le déploiement et l’auto-hébergement de votre portail en dehors d’une instance de gestion des API. Cette approche vous permet de modifier la base de code du portail et d’étendre la fonctionnalité principale fournie. Pour en savoir plus et accéder à des instructions, reportez-vous au [référentiel GitHub avec le code source du portail][1].

## <a name="access-the-managed-version-of-the-portal"></a>Accéder à la version managée du portail

Suivez les étapes ci-dessous pour accéder à la version managée du portail.

1. Accédez à votre instance du service Gestion des API dans le Portail Microsoft Azure.
1. Cliquez sur le **nouveau portail des développeurs (préversion)** dans la barre de navigation supérieure. Un nouvel onglet de navigateur s’ouvre, en affichant une version d’administration du portail. Si vous accédez au portail pour la première fois, le contenu par défaut est automatiquement approvisionné.

## <a name="edit-and-customize-the-managed-version-of-the-portal"></a>Modifier et personnaliser la version managée du portail

Dans la vidéo ci-dessous, nous expliquons comment modifier le contenu du portail, personnaliser l’aspect du site web et publier les modifications.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="next-steps"></a>Étapes suivantes

Découvrez le nouveau portail des développeurs :

- [Référentiel GitHub avec le code source][1]
- [Instructions sur l’auto-hébergement du portail][2]
- [Calendrier de lancement public du projet][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects