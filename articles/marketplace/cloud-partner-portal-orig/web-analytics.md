---
title: Web Analytics
description: Cet article vous fournit des instructions sur la façon d’apprendre et d’utiliser Web Analytics pour développer au mieux votre entreprise.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 446e579a7205f0e785d7e940c1dbdd36cff8f370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285331"
---
<a name="web-analytics"></a>Web Analytics
=============

Cet article vous fournit des instructions sur la façon d’apprendre et d’utiliser Web Analytics pour développer au mieux votre entreprise. Actuellement, l’onglet Insights est disponible pour toutes les offres AppSource.

Maintenant que vous avez créé et publié votre offre, votre parcours consiste à suivre et mesurer sa\' réussite. Avec **Web Analytics**, nous avons ajouté la possibilité de voir exactement comment chacune de vos offres se comporte sur le marché. Pour commencer votre parcours, accédez à la page Insights sur le côté gauche du Portail Microsoft Cloud Partner pour voir le nouvel onglet Analytics.

![Page Web Analytics](./media/si-getting-started/WebAnalytics1.png)

Construit avec Microsoft Power BI, le tableau de bord aux nombreuses options qui s’affiche est associé à votre ID éditeur et vous permet de voir les données individuelles de vos offres. Ces données sont actualisées quotidiennement.

<a name="microsoft-campaigns"></a>**Campagnes Microsoft**
-----------------------

Pour augmenter vos offres et effectuer le suivi de la croissance de vos offres, nous vous donnons la possibilité d’utiliser les **campagnes Microsoft** sur le portail Cloud Partner. Les campagnes constituent une nouvelle fonctionnalité prise en charge pour la place de marché afin de vous permettre de suivre les différents canaux qui envoient des clients vers la page de détails de votre application.

### <a name="how-to-make-a-campaign"></a>**Comment créer une campagne**

Une campagne consiste simplement pour vous à ajouter un mot ou un terme personnalisé à votre URL qui dirige vers la page de détails de votre application sur la place de marché. Google, Bing, LinkedIn et nombreux autres sites vous encouragent à créer une publication et à établir un lien vers le site de votre choix à partir de leurs propres pages.

En général, ces efforts visent à attirer de nouveaux clients vers votre produit et, à cet égard, il est essentiel de mesurer le succès de chacun de vos canaux. C’est là qu’interviennent les campagnes.

Il y a deux façons de générer votre propre campagne.

1. Ajoutez à votre URL le paramètre de requête **mktcmpid** qui décrit la nature de la campagne et la page/l’événement d’où proviennent ces clients.

Par exemple, vous pouvez utiliser : <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign>

1. (Avancé) : utilisez l’un de nos identifiants de campagne génériques pris en charge dans l’URL. Nous voulons nous accommoder de balises ref supplémentaires que vous devez utiliser, c’est pourquoi nous soutenons la convention de reconnaissance automatique de ces balises supplémentaires :
    
    1. **utm\_campaign**
    2. **utm\_source**
    3. **ref**
    4. **src**

Par exemple, vous pouvez utiliser : <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign>

Vous pouvez choisir d’avoir une combinaison de plusieurs de ces identifiants de campagne afin d’identifier plusieurs sources de trafic pour la campagne, telles que l’origine du client (e-mail, blog, source de réseaux sociaux, etc.).

Par exemple :

1. Référenceur de newsletter :  <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter>
2. Référenceur LinkedIn :  <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn>

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**S’assurer que les campagnes englobent toutes vos pages**

Il peut y exister un scénario où vos campagnes possèdent une page intermédiaire vers laquelle vous dirigez le trafic et qui procède ensuite à la redirection des clients sur la place de marché. Il est important de passer par vos identifiants de campagne initiaux dans l’URL finale que vous envoyez à la place de marché.

Voici un exemple :

1. Un employé du marketing achète des publicités à Google afin d’orienter le trafic vers la page d\'accueil de la société <https://contoso.com>. Cette page d’accueil possède un lien \"essayer mon produit\" qui pointe vers <https://appsource.com>.
2. Un utilisateur clique sur la publicité et accède à la page d\'accueil de sa société.
    1.  URL de référence = google.com
    2.  URL de la page d’accueil = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
3. L’utilisateur clique sur le lien \"essayer mon produit\" et accède à AppSource.
    1. URL de référence =  <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
    2. URL de la Page d’accueil (**vérifiez que l’URL comporte utm\_campaign et utm\_source**, dans le format suivant) = [https://appsource.microsoft.com/product/dynamics-365/contoso.offername?**utm\_ campaign=MyCampaignAdName&utm\_ source=MySourceAdName**](https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName)

<a name="how-to-evaluate-the-success-of-a-campaign"></a>Comment évaluer la réussite d’une campagne
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Visites de page par campagne**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

Il s’agit de la répartition de chacune de vos visites quotidiennes de pages selon la campagne dont elles proviennent.

### <a name="conversion-rate-by-campaign"></a>**Taux de conversion par campagne**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

De la même manière que nous indiquons le taux de conversion de l’ensemble de votre offre, vous pouvez voir dans ce graphique la répartition de vos différentes campagnes. Ce graphique doit vous aider à identifier l’origine de vos campagnes dont le taux de conversion est le plus élevé.

### <a name="distribution-by-campaign"></a>**Distribution par campagne**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

De la même façon dont nous allons examiner les domaines de vos clients, ce graphique illustre la ventilation de vos données par campagne sous laquelle les utilisateurs parviennent à la place de marché. \_NoCampaign signifie que le client parcourait la place de marché à partir d’une URL dépourvue d’ID de campagne.

<a name="next-steps"></a>**Next Steps**
--------------

Maintenant que vous avez la possibilité de suivre la réussite de vos offres, nous voulons vous encourager à créer vos propres campagnes.

Si vous avez des questions/demandes de fonctionnalités, partagez-les via l’option Commentaires, située dans le coin supérieur droit de la page.

![Commentaires dans le Portail Microsoft Cloud Partner](./media/si-getting-started/WebAnalytics5.png)
