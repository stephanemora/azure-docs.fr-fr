---
title: Vue d’ensemble du portail des développeurs Gestion des API Azure – Gestion des API Azure | Microsoft Docs
description: Découvrez le portail des développeurs dans la Gestion des API Azure.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 6bf8c8690977ef1036c853d8c1c01a3a366b50df
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74011481"
---
# <a name="azure-api-management-developer-portal-overview"></a>Vue d’ensemble du portail des développeurs Gestion des API Azure

Le portail des développeurs est un site web généré automatiquement et entièrement personnalisable avec la documentation de vos API. C’est là que les consommateurs d’API peuvent découvrir vos API, apprendre à les utiliser, y demander l’accès et les essayer.

Cet article décrit les différences entre la version auto-hébergée et la version managée du portail des développeurs dans la Gestion des API. Il explique également son architecture et répond aux questions fréquentes.

> [!WARNING]
> Le nouveau portail des développeurs est actuellement en cours de déploiement dans les services Gestion des API.
> Si votre service vient d’être créé ou s’il s’agit d’un service de niveau développeur, vous disposez normalement déjà de la dernière version. Dans le cas contraire, il se peut que vous rencontriez des problèmes (par exemple, avec la fonctionnalité de publication). Le lancement des fonctionnalités devrait être effectué avant le vendredi 22 novembre 2019.
>
> [Découvrez comment migrer de la préversion à la version en disponibilité générale](#preview-to-ga) du portail des développeurs.

![Portail des développeurs Gestion des API](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-vs-self-hosted"></a> Version managée et version auto-hébergée

Vous pouvez générer votre portail des développeurs de deux manières :

- **Version managée**, via la modification et la personnalisation du portail : intégrée dans votre instance de Gestion des API et accessible via l’URL `<your-api-management-instance-name>.developer.azure-api.net` ; Pour savoir comment accéder au portail managé et le personnaliser, voir [cet article de documentation](api-management-howto-developer-portal-customize.md).
- **version auto-hébergée**, via le déploiement et l’auto-hébergement de votre portail en dehors d’une instance de gestion des API. Cette approche vous permet de modifier la base de code du portail et d’étendre la fonctionnalité principale fournie. Vous devez également passer à la dernière version du portail par vous-même. Pour en savoir plus et accéder à des instructions, reportez-vous au [référentiel GitHub avec le code source du portail][1]. Le [tutoriel de la version managée](api-management-howto-developer-portal-customize.md) décrit le panneau d’administration du portail, qui est également proposé dans la version auto-hébergée.

## <a name="portal-architectural-concepts"></a>Concepts architecturaux du portail

Les composants du portail se divisent en deux catégories logiques : le *code* et le *contenu*.

Le *code*, conservé dans le [référentiel GitHub][1], comprend les éléments suivants :

- les widgets, qui représentent des éléments visuels et associent du code HTML, du code JavaScript, des possibilités de stylisation, des paramètres et un mappage du contenu (exemples : image, paragraphe de texte, formulaire, liste d’API, etc.) ;
- les définitions de style, qui spécifient les styles possibles des widgets ;
- le moteur, écrit en JavaScript, qui génère des pages web statiques à partir du contenu du portail ;
- l’éditeur visuel, qui offre une expérience de personnalisation et de création au sein du navigateur.

Le *contenu* est divisé en deux sous-catégories : le *contenu du portail* et le *contenu de la Gestion des API*.

Le *contenu du portail*, propre au portail, comprend les éléments suivants :

- les pages (exemples : page de destination, tutoriels sur les API, billets de blog) ;
- les contenus multimédias (exemples : images, animations et autres contenus basés sur des fichiers) ;
- les dispositions : modèles qui sont confrontés avec une URL et définissent le mode d’affichage des pages ;
- les styles : valeurs des définitions de style (exemples : polices, couleurs, bordures) ;
- les paramètres : configuration (exemples : favicon, métadonnées du site web).

Le *contenu du portail*, à l’exception des contenus multimédias, est exprimé sous la forme de documents JSON.

Le *contenu Gestion des API* comprend des entités telles que les API, les Opérations, les Produits et les Abonnements.

Le portail est basé sur une fourche adaptée du [framework Paperbits](https://paperbits.io/). La fonctionnalité Paperbits d’origine a été étendue de façon à fournir des widgets propres à la Gestion des API (par exemple, une liste d’API ou une liste de Produits) et un connecteur vers le service Gestion des API permettant d’enregistrer et de récupérer du contenu.

## <a name="faq"></a> Forum aux questions

Dans cette section, nous répondons aux questions courantes d’ordre général sur le nouveau portail des développeurs. Pour toute question spécifique à la version auto-hébergée, reportez-vous à la [section wiki du référentiel GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="a-idpreview-to-ga-how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/> Comment migrer à partir de la préversion du portail ?

En utilisant la préversion du portail des développeurs, vous avez configuré le contenu en préversion dans votre service Gestion des API. Le contenu par défaut a été sensiblement modifié dans la version en disponibilité générale afin d’offrir une meilleure expérience utilisateur. Il comprend également de nouveaux widgets.

Si vous utilisez la version managée, réinitialisez le contenu du portail en cliquant sur **Réinitialiser le contenu** dans la section de menu **Opérations**. Une fois confirmée, cette opération supprimera tout le contenu du portail et configurera le nouveau contenu par défaut. Le moteur du portail a été automatiquement mis à jour dans votre service Gestion des API.

![Réinitialiser le contenu du portail](media/api-management-howto-developer-portal/reset-content.png)

Si vous utilisez la version auto-hébergée, utilisez les fichiers `scripts/cleanup.bat` et `scripts/generate.bat` du référentiel GitHub pour supprimer le contenu actuel et configurer un nouveau contenu. Veillez au préalable à passer à la dernière version du code de votre portail à partir du référentiel GitHub.

Si vous ne souhaitez pas réinitialiser le contenu du portail, vous pouvez utiliser les nouveaux widgets sur toutes vos pages. Les widgets d’origine ont été automatiquement mis à jour vers les dernières versions.

Si votre portail a été configuré après l’annonce de la mise à la disposition générale, il dispose normalement déjà du nouveau contenu par défaut. Aucune action de votre part n’est nécessaire.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-new-developer-portal"></a>Comment migrer de l’ancien portail des développeurs au nouveau ?

Les portails étant incompatibles, vous devez migrer le contenu manuellement.

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>Le nouveau portail a-t-il toutes les fonctionnalités de l’ancien portail ?

Le nouveau portail des développeurs ne prend pas en charge les *Applications* et les *Problèmes*. Si vous avez utilisé les *Problèmes* sur l’ancien portail et que vous en avez besoin sur le nouveau, publiez un commentaire sur un [problème GitHub dédié](https://github.com/Azure/api-management-developer-portal/issues/122).

### <a name="has-the-old-portal-been-deprecated"></a>L’ancien portail est-il déconseillé ?

L’ancien portail des développeurs et l’ancien portail des éditeurs sont désormais des fonctionnalités *héritées*, qui ne recevront que des mises à jour de sécurité. Les nouvelles fonctionnalités ne seront implémentées que dans le nouveau portail des développeurs.

La dépréciation des portails hérités sera annoncée séparément. Si vous avez des questions, des doutes ou des commentaires, signalez-les dans un [problème GitHub dédié](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="how-can-i-automate-portal-deployments"></a>Comment automatiser les déploiements de portails ?

Vous pouvez accéder programmatiquement au contenu du portail des développeurs et le gérer avec l’API REST, que vous utilisiez une version managée ou auto-hébergée.

L’API est documentée dans la [section wiki du référentiel GitHub][2]. Elle peut également servir à automatiser des migrations du contenu du portail entre différents environnements, par exemple d’un environnement de test vers l’environnement de production. Pour plus d’informations sur ce processus, lisez [cet article de documentation](https://aka.ms/apimdocs/migrateportal) sur GitHub.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Le portail prend-il en charge les modèles Azure Resource Manager ? Est-il compatible avec le kit de ressources DevOps Gestion des API ?

Non.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Faut-il activer une connectivité de réseau virtuel supplémentaire pour les dépendances du portail managé ?

Non.

### <a name="im-getting-a-cors-error-when-using-the-interactive-console-what-should-i-do"></a>J’obtiens une erreur CORS lorsque j’utilise la console interactive. Que dois-je faire ?

La console interactive effectue une requête d’API côté client à partir du navigateur. Vous pouvez résoudre le problème CORS en ajoutant une [stratégie CORS ](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS)sur vos API. Vous pouvez spécifier tous les paramètres manuellement ou utiliser la valeur de caractère générique `*`. Par exemple :

```XML
<cors>
    <allowed-origins>
        <origin>*</origin>
    </allowed-origins>
    <allowed-methods>
        <method>GET</method>
        <method>POST</method>
        <method>PUT</method>
        <method>DELETE</method>
        <method>HEAD</method>
        <method>OPTIONS</method>
        <method>PATCH</method>
        <method>TRACE</method>
    </allowed-methods>
    <allowed-headers>
        <header>*</header>
    </allowed-headers>
    <expose-headers>
        <header>*</header>
    </expose-headers>
</cors>
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez le nouveau portail des développeurs :

- [Accéder au portail managé des développeurs et le personnaliser](api-management-howto-developer-portal-customize.md)
- [Configurer la version auto-hébergée du portail][2]

Parcourez d’autres ressources :

- [Référentiel GitHub avec le code source][1]
- [Calendrier de lancement public du projet][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects