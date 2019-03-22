---
title: Version d’évaluation hébergée | Microsoft Docs
description: Comment configurer et maintenir une version d’évaluation hébergée de la Place de marché
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5057c0c781cb9ec60ecde7dd3f4bf96089b902df
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312680"
---
# <a name="hosted-test-drive"></a>Version d’évaluation hébergée

Une version d’évaluation hébergée simplifie la configuration par l’hébergement Microsoft et assure la maintenance du service qui effectue l’approvisionnement et le déprovisionnement de l’utilisateur de la version d’évaluation. Cet article est destiné aux éditeurs qui proposent leur offre sur AppSource ou qui en créent une et souhaitent proposer une version d’évaluation hébergée qui se connecte avec une instance Dynamics 365 for Customer Engagement, Dynamics 365 for Finance and Operations ou Dynamics 365 Business Central.

## <a name="how-to-publish-a-test-drive"></a>Publication d’une version d’évaluation

Accédez à l’offre existante ou créez-en une.

Dans le menu latéral, sélectionnez l’option Version d’évaluation.

Sélectionnez \'Oui\' pour l’option \'Enable a Test Drive\' (Activer une version d’évaluation).

Fournissez les champs suivants dans la section \'Détails\'.

- **Description** : Fournir une vue d’ensemble de votre version d’évaluation. L’utilisateur pourra voir ce texte pendant l’approvisionnement de la version d’évaluation. Ce champ prend en charge le langage HTML si vous souhaitez proposer du contenu mis en forme.
- **Manuel de l’utilisateur**: Téléchargez un manuel détaillée de l’utilisateur (fichier .pdf de type), ce qui permet aux utilisateurs de Test Drive comprendre comment utiliser votre application.
- **Vidéo de test Drive démonstration**: Si vous le souhaitez charger une vidéo qui présente de votre application.

Accordez l’autorisation AppSource pour approvisionner et déprovisionner les utilisateurs de la version d’évaluation dans votre locataire à l’aide des instructions se trouvant [ici](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

Au cours de cette étape, vous allez générer les valeurs \'Azure AD App Id\' (ID Azure AD App) et \'Azure AD App Key\' (Clé Azure AD App) mentionnées ci-dessous.

Fournissez les champs suivants dans la section \'Technical Configuration\' (Configuration technique) :

- **Type de Test Drive**: Choisissez \'Hosted Microsoft (par exemple Dynamics 365 for Customer Engagement)' option. Cela signifie que Microsoft héberge et assure la maintenance du service qui effectue l’approvisionnement et le déprovisionnement de l’utilisateur de la version d’évaluation.
- **Versions d’évaluation de Max Concurrent**: Définissez ce champ sur le nombre d’utilisateurs simultanés qui peuvent avoir une version d’évaluation active à tout moment donné. Chaque utilisateur se sert d’une licence Dynamics quand sa version d’évaluation est active. Vous allez donc devoir vous assurer que vous avez au moins autant de licences Dynamics disponibles que d’utilisateurs. La valeur recommandée est de 3 à 5.
- **Lecteur de durée du test (heures)**: Définissez ce champ pour le nombre d’heures, les utilisateurs pour que Test Drive sera actif. Une fois ce nombre d’heures dépassé, l’utilisateur est déprovisionné de votre locataire. La valeur recommandée de 2 à 24 heures dépend de la complexité de votre application. L’utilisateur peut toujours demander une autre version d’évaluation s’il n’a pas eu assez de temps et s’il souhaite de nouveau accéder à la version d’évaluation.
- **URL de l’instance**: Fournir une URL de l’utilisateur de Test Drive sera initialement utilisée au démarrage de la version d’évaluation. Il s’agit généralement de l’URL de votre instance Dynamics 365 sur laquelle votre application et les exemples de données sont installés. Exemple de valeur : https :\//testdrive.crm.dynamics.com
- **ID de locataire Azure AD**: Fournissez l’ID du client Azure pour votre Instance Dynamics 365. Pour récupérer cette valeur, connectez-vous au Portail Azure et accédez à \'Azure Active Directory\' -\>Select Properties from menu blade (Sélectionner des propriétés à partir du volet du menu)- \> Copy the Directory ID (Copier l’ID du répertoire). Exemple de valeur : 72f988bf-86f1-41af-91ab-2d7cd0111234
- **ID d’application Azure AD**: ID de l’application Azure AD que vous avez créé à l’étape 7. \ exemple de valeur : 53852862-a2ae-4e43-9461-faa49650a096
- **Clé d’application Azure AD**: Secret pour l’application Azure AD créé à l’étape 7. \ exemple de valeur : IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=
- **Nom du locataire Azure AD**: Indiquez le nom du locataire Azure pour votre Instance de Dynamics 365. Utilisez le format \<tenantname.\>onmicrosoft.com. Exemple de valeur : testdrive.onmicrosoft.com
- **URL de l’API Web de l’instance**: Fournir l’URL de l’API Web pour votre Instance Dynamics 365. Vous pouvez récupérer cette valeur en vous connectant à votre instance Microsoft Dynamics 365 et en accédant à Paramètre -\> Personnalisation -\> Ressources du développeur -\> Instance Web API (Copy this URL) (API web de l’instance (Copier cette URL)). Exemple de valeur : https :\//testdrive.crm.dynamics.com/api/data/v9.0 
- **Nom du rôle**: Indiquez le nom du rôle de sécurité personnalisé du de Dynamics 365 que vous avez créé pour la version d’évaluation. C’est le rôle qui sera assigné aux utilisateurs lors de leur utilisation de la version d’évaluation. Exemple de valeur : testdriverole

## <a name="next-steps"></a>Étapes suivantes

Lorsque vous êtes prêt **publier** votre offre, une fois votre application a passé la certification, vous aurez un **aperçu** de votre offre. Lancez une version d’évaluation dans l’interface utilisateur et vérifiez que vos versions d’évaluation s’exécutent correctement. Une fois que vous sentez à l’aise avec votre offre en version préliminaire, il est maintenant temps de **mise en ligne !**
