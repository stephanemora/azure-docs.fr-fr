---
title: Accéder et personnaliser le nouveau portail des développeurs - gestion des API Azure | Microsoft Docs
description: Découvrez comment utiliser le nouveau portail des développeurs dans Gestion des API.
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
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743574"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>Accéder et personnaliser le nouveau portail des développeurs dans Gestion des API Azure

Cet article vous montre comment accéder au nouveau portail de développeurs de gestion des API Azure. Il vous guide dans l’expérience de l’éditeur visual - Ajout et modification de contenu, ainsi que la personnalisation de l’apparence du site Web.

![Nouveau portail des développeurs gestion des API](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a>Conditions préalables

- Suivez ce guide de démarrage rapide : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
- Importez et publiez une instance Gestion des API Azure. Pour plus d’informations, consultez [Importer et publier](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> Le nouveau portail des développeurs est actuellement en version préliminaire.

## <a name="managed-and-self-hosted-versions"></a>Versions auto-hébergé et non managées

Vous pouvez générer votre portail des développeurs de deux manières :

- **Version gérée** - par modification et la personnalisation du portail intégrés à votre instance gestion des API et accessible via l’URL `<your-api-management-instance-name>.developer.azure-api.net`.
- **Version auto-hébergée** : en déployant et en l’auto-hébergement de votre portail en dehors d’une instance de la gestion des API. Cette approche vous permet de modifier le portail codebase et étendre la fonctionnalité principale fournie. Pour plus d’informations et obtenir des instructions, reportez-vous à la [référentiel GitHub avec le code source du portail][1].

## <a name="access-the-managed-version-of-the-portal"></a>Accéder à la version gérée du portail

Suivez les étapes ci-dessous pour accéder à la version gérée du portail.

1. Accédez à votre instance de service de gestion des API dans le portail Azure.
1. Cliquez sur le **nouveau portail des développeurs (version préliminaire)** dans la barre de navigation supérieure. Un nouvel onglet de navigateur avec une version d’administration du portail s’ouvre. Si vous accédez au portail pour la première fois, le contenu par défaut est automatiquement configuré.

## <a name="edit-and-customize-the-managed-version-of-the-portal"></a>Modifier et personnaliser la version gérée du portail

Dans la vidéo ci-dessous, nous montrons comment modifier le contenu du portail, de personnaliser l’aspect du site Web et publier les modifications.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le nouveau portail des développeurs :

- [Référentiel GitHub avec le code source][1]
- [Obtenir des instructions sur le portail de l’auto-hébergement][2]
- [Calendrier de lancement public du projet][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects