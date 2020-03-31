---
title: Conseils relatifs au SEO sur la Place de marché Azure
description: Fournit des conseils pour optimiser l’optimisation du référencement auprès d’un moteur de recherche.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: dsindona
ms.openlocfilehash: 761cdc2233bce3619d4c2c9ce1d7d7177d3bc407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280148"
---
# <a name="azure-marketplace-seo-guidance"></a>Conseils relatifs au SEO sur la Place de marché Azure

Cet article explique comment optimiser la détectabilité de votre offre via la fonctionnalité de recherche dans la [Place de marché Azure](https://azuremarketplace.microsoft.com) et [AppSource](https://appsource.microsoft.com). 


## <a name="general-explanation-of-algorithm"></a>Présentation générale de l’algorithme

Les Places de marché Microsoft utilisent la Recherche cognitive Azure pour optimiser les fonctionnalités de recherche des sites. L’algorithme est basé sur la fréquence des termes-la fréquence inverse dans le document ([TF/IDF](https://en.wikipedia.org/wiki/Tf–idf)). L’analyseur Lucene [standard](https://lucene.apache.org/core/) est utilisé.

En règle générale, tout le texte des champs, des catégories et des secteurs d’activité est inclus dans la pondération de la pertinence. Les termes spécialisés qui sont rarement utilisés par les applications, mais fréquemment employés dans votre application produisent un score plus élevé lors de la recherche. Par conséquent, des termes comme « VM » offrent peu d’avantages, tandis que « Recherche Azure » est un terme beaucoup plus spécialisé.
Voici les champs les plus importants à prendre en compte.

 
|  Champ                   | importance | Assistance                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| Nom de l’offre               |  Élevé      | Valeur exacte ou proche d’une correspondance exacte avec requête qui génère un classement élevé.                       |
| Nom de l’éditeur           |  Élevé      | Valeur exacte ou proche d’une correspondance exacte avec requête qui génère un classement élevé.                       |
| Description courte        |  Moyenne    | Étant que le nom de l’application et de l’éditeur garantit presque automatiquement un classement élevé, ce nom peut ne pas être le choix le plus approprié. Dans ce cas, une brève description est importante. Le texte doit être concis et précis. Mentionnez les mots clés et les termes de recherche attendus pour un résultat optimal.  Par exemple « Ceci est la meilleure solution de point de vente basée entièrement sur Dynamics 365 » est moins efficace que « POS (point de vente) de vente au détail pour Dynamics 365 ».  | 
| Description longue         |  Faible       | La description permet d’aller plus loin dans les détails. Les descriptions les plus efficaces sont de longueur raisonnable et reprennent les mots clés.  Une description claire qui utilise les mots clés fonctionne mieux qu’un long texte. Vérifiez que les mots clés, par exemple « IoT », sont présents dans la description.  |
| Catégories de produits       | Moyenne     |  Les catégories de produits sont déterminées par les choix effectués par l’éditeur et par Microsoft. Sélectionnez ces catégories afin que les utilisateurs puissent facilement trouver les applications dans la bonne catégorie. |
|  |  |  |


## <a name="other-tips"></a>Autres conseils

-   Les suggestions de recherches bénéficient d’une importante activité utilisateur. Elles hiérarchisent les correspondances par rapport au nom de l’éditeur et à celui de l’application. La description courte devient le champ clé lorsque le terme de recherche ne correspond pas exactement au nom de l’éditeur ou au nom de l’application.
-   Les documents à télécharger ne sont pas repris dans la pondération de la recherche.
-   Votre acquisition réelle des applications et votre utilisation aura aussi un impact votre classement dans les recherches. Par exemple, pour deux applications équivalentes, celle qui présente un plus grand nombre d’utilisateurs obtiendra un classement plus élevé.
