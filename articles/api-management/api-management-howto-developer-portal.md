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
ms.topic: article
ms.date: 06/12/2019
ms.author: apimpm
ms.openlocfilehash: c015b1afbc61e1501e656aaa480ee2a4e19ba094
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672786"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>Accès et personnalisation du nouveau portail des développeurs pour la gestion des API Azure

Cet article explique comment accéder au nouveau portail des développeurs dédié à la gestion des API Azure. Il vous guide tout au long de l’expérience d’utilisation de l’éditeur visuel, lors de l’ajout et de la modification de contenu, et de la personnalisation de l’apparence du site web.

![Nouveau portail des développeurs pour la gestion des API](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a> Conditions préalables

- Suivez ce guide de démarrage rapide : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
- Importez et publiez une instance Gestion des API Azure. Pour plus d’informations, consultez [Importer et publier](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> Le nouveau portail des développeurs est actuellement en préversion.

## <a name="managed-vs-self-hosted"></a> Version managée et version auto-hébergée

Vous pouvez générer votre portail des développeurs de deux manières :

- **Version managée**, via la modification et la personnalisation du portail : intégrée dans votre instance de Gestion des API et accessible via l’URL `<your-api-management-instance-name>.developer.azure-api.net` ;
- **version auto-hébergée**, via le déploiement et l’auto-hébergement de votre portail en dehors d’une instance de gestion des API. Cette approche vous permet de modifier la base de code du portail et d’étendre la fonctionnalité principale fournie. Pour en savoir plus et accéder à des instructions, reportez-vous au [référentiel GitHub avec le code source du portail][1].

## <a name="managed-access"></a> Accéder à la version managée du portail

Suivez les étapes ci-dessous pour accéder à la version managée du portail.

1. Accédez à votre instance du service Gestion des API dans le Portail Microsoft Azure.
1. Cliquez sur le **nouveau portail des développeurs (préversion)** dans la barre de navigation supérieure. Un nouvel onglet de navigateur s’ouvre, en affichant une version d’administration du portail. Si vous accédez au portail pour la première fois, le contenu par défaut est automatiquement approvisionné.

## <a name="managed-tutorial"></a> Modifier et personnaliser la version managée du portail

Dans la vidéo ci-dessous, nous expliquons comment modifier le contenu du portail, personnaliser l’aspect du site web et publier les modifications.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="faq"></a> Forum aux questions

Dans cette section, nous répondons aux questions courantes sur le nouveau portail des développeurs. Pour toute question spécifique à la version auto-hébergée, reportez-vous à la [section wiki du référentiel GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-content-from-the-old-developer-portal-to-the-new-one"></a>Comment migrer le contenu de l’ancien portail des développeurs vers le nouveau ?

Désolé... Cette migration est impossible. Les portails sont incompatibles.

### <a name="when-will-the-portal-become-generally-available"></a>Quand le portail sera-t-il mis à la disposition générale ?

Le portail est actuellement en version préliminaire et sera mis à la disposition générale dès la fin de l’année civile (2019). La version préliminaire ne doit pas être utilisée à des fins de production.

### <a name="will-the-old-portal-be-deprecated"></a>L’ancien portail sera-t-il déconseillé ?

Oui, il sera déconseillé après la disponibilité générale du nouveau. Si vous rencontrez des problèmes, signalez-les dans une publication [sur le site GitHub dédié](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>Le nouveau portail a-t-il toutes les fonctionnalités de l’ancien portail ?

L’objectif de la disponibilité générale est de fournir une parité de fonctionnalité basée sur les scénarios de l’ancien portail. Jusqu’à présent, les fonctionnalités sélectionnées ne sont peut-être pas encore implémentées dans la version préliminaire.

Les exceptions concernent les sections *Applications* et les *Problèmes* de l’ancien portail, qui ne seront pas disponibles dans le nouveau portail. Si vous utilisez la section *Problèmes* de l’ancien portail et que vous en avez besoin dans le nouveau, publiez un commentaire [sur le site GitHub dédié](https://github.com/Azure/api-management-developer-portal/issues/122).

### <a name="ive-found-bugs-andor-id-like-to-request-a-feature"></a>J'ai trouvé des bogues et/ou j'aimerais demander une fonctionnalité.

Parfait ! Vous pouvez nous faire part de vos commentaires, soumettre une demande de fonctionnalité ou déposer un rapport de bogue via [la section Problèmes du référentiel GitHub](https://github.com/Azure/api-management-developer-portal/issues). Et tant que vous y êtes, n'hésitez pas à nous donner votre avis sur les problèmes accompagnés de l'étiquette `community`.

### <a name="i-want-to-move-the-content-of-the-new-portal-between-environments-how-can-i-do-that-and-do-i-need-to-go-with-the-self-hosted-version"></a>Je souhaite déplacer le contenu du nouveau portail entre des environnements. Comment puis-je effectuer cette opération ? Dois-je utiliser la version auto-hébergée ?

Vous pouvez le faire dans les deux versions du portail, managée et auto-hébergée. Le nouveau portail des développeurs prend en charge l’extraction de contenu via l’API de gestion de votre service Gestion des API. Les API sont documentées [dans la section wiki du référentiel GitHub](https://github.com/Azure/api-management-developer-portal/wiki/). Nous avons également écrit [un script](https://github.com/Azure/api-management-developer-portal/blob/master/scripts/migrate.bat), qui peut vous aider à démarrer.

Nous travaillons toujours sur l’alignement de ce processus avec le kit de ressources DevOps de Gestion des API.

### <a name="what-do-i-need-to-configure-for-the-new-portal-to-work-in-my-api-management-service-in-vnet"></a>Que dois-je configurer pour que le nouveau portail fonctionne dans mon service Gestion des API dans le réseau virtuel ?

Pendant que le nouveau portail des développeurs est en préversion, vous devez autoriser la connectivité aux services de stockage Azure dans la région USA Ouest afin que le portail géré fonctionne dans un service Gestion des API dans le réseau virtuel. Vous trouverez plus d’informations dans la [documentation sur le stockage](../storage/common/storage-network-security.md#available-virtual-network-regions).

La configuration ci-dessus ne sera plus nécessaire une fois que le nouveau portail sera en disponibilité générale.

La version auto-hébergée du portail peut nécessiter une définition de connectivité supplémentaire, en fonction de votre configuration.

### <a name="how-can-i-select-a-layout-when-creating-a-new-page"></a>Comment puis-je sélectionner une *disposition* lors de la création d’une *page* ?

Une *disposition* est appliquée à une page en faisant correspondre son modèle d’URL à l’URL de la *page*. Par exemple, la *disposition* avec un modèle d’URL `/wiki/*` sera appliquée à chaque *page* avec le segment `/wiki/` : `/wiki/getting-started`, `/wiki/styles`, etc.

### <a name="why-doesnt-the-interactive-developer-console-work"></a>Pourquoi la console de développement interactive ne fonctionne-t-elle pas ?

Ce problème est probablement lié à CORS. La console interactive effectue une requête d’API côté client à partir du navigateur. Vous pouvez résoudre le problème CORS en ajoutant une [stratégie CORS ](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS)sur vos API. Vous pouvez spécifier tous les paramètres manuellement (par exemple, https://contoso.com) comme origine, ou utiliser une valeur de caractère générique `*`.

## <a name="next-steps"></a>Étapes suivantes

Découvrez le nouveau portail des développeurs :

- [Référentiel GitHub avec le code source][1]
- [Instructions sur l’auto-hébergement du portail][2]
- [Calendrier de lancement public du projet][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects