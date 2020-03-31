---
title: Version d’évaluation hébergée | Place de marché Azure
description: Comment configurer et maintenir une version d’évaluation hébergée de la Place de marché
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d054064e236e121e02bf58a0eb73b5a62f24a09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278261"
---
# <a name="hosted-test-drive"></a>Version d’évaluation hébergée

Une version d’évaluation hébergée simplifie la configuration par l’hébergement Microsoft et assure la maintenance du service qui effectue l’approvisionnement et le déprovisionnement de l’utilisateur de la version d’évaluation. Cet article est destiné aux éditeurs qui proposent leur offre sur AppSource ou qui en créent une et souhaitent proposer une version d’évaluation hébergée qui se connecte avec une instance Dynamics 365 for Customer Engagement, Dynamics 365 for Finance and Operations ou Dynamics 365 Business Central.

## <a name="how-to-publish-a-test-drive"></a>Publication d’une version d’évaluation

Accédez à l’offre existante ou créez-en une.

Dans le menu latéral, sélectionnez l’option Version d’évaluation.

Sélectionnez \'Oui\' pour l’option \'Enable a Test Drive\' (Activer une version d’évaluation).

Fournissez les champs suivants dans la section \'Détails\'.

- **Description** : fournit une présentation de votre version d’évaluation. L’utilisateur pourra voir ce texte pendant l’approvisionnement de la version d’évaluation. Ce champ prend en charge le langage HTML si vous souhaitez proposer du contenu mis en forme.
- **Manuel de l’utilisateur**: chargez un manuel d’utilisateur détaillé (un fichier au format .pdf) qui permet aux utilisateurs de la version d’évaluation d’apprendre à utiliser votre application.
- **Test Drive Demo Video** (Vidéo de démonstration de la version d’évaluation) : si vous le souhaitez, chargez une vidéo de présentation de votre application.

Accordez l’autorisation AppSource pour approvisionner et déprovisionner les utilisateurs de la version d’évaluation dans votre locataire à l’aide des instructions se trouvant [ici](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

Au cours de cette étape, vous allez générer les valeurs \'Azure AD App Id\' (ID Azure AD App) et \'Azure AD App Key\' (Clé Azure AD App) mentionnées ci-dessous.

Fournissez les champs suivants dans la section \'Technical Configuration\' (Configuration technique) :

- **Type of Test Drive** (Type de version d’évaluation) : choisissez l’option \'Microsoft Hosted (example Dynamics 365 for Customer Engagement)’ (Hébergé par Microsoft (par exemple Dynamics 365 for Customer Engagement)). Cela signifie que Microsoft héberge et assure la maintenance du service qui effectue l’approvisionnement et le déprovisionnement de l’utilisateur de la version d’évaluation.
- **Max Concurrent Test Drives** (Versions d’évaluation simultanées max.) : définissez ce champ sur le nombre d’utilisateurs simultanés qui peuvent avoir une version d’évaluation active à un moment donné. Chaque utilisateur se sert d’une licence Dynamics quand sa version d’évaluation est active. Vous allez donc devoir vous assurer que vous avez au moins autant de licences Dynamics disponibles que d’utilisateurs. La valeur recommandée est de 3 à 5.
- **Test Drive Duration (hours)** (Durée de la version d’évaluation (heures)) : définissez ce champ sur le nombre d’heures durant lesquelles la version d’évaluation des utilisateurs sera active. Une fois ce nombre d’heures dépassé, l’utilisateur est déprovisionné de votre locataire. La valeur recommandée de 2 à 24 heures dépend de la complexité de votre application. L’utilisateur peut toujours demander une autre version d’évaluation s’il n’a pas eu assez de temps et s’il souhaite de nouveau accéder à la version d’évaluation.
- **URL d’instance** : fournissez une URL vers laquelle l’utilisateur de la version d’évaluation est dirigé quand il démarre la version d’évaluation. Il s’agit généralement de l’URL de votre instance Dynamics 365 sur laquelle votre application et les exemples de données sont installés. Exemple de valeur : https:\//testdrive.crm.dynamics.com
- **ID de locataire Azure AD** : fournissez l’ID du locataire Azure pour votre instance Dynamics 365. Pour récupérer cette valeur, connectez-vous au Portail Azure et accédez à \'Azure Active Directory\' -\>Select Properties from menu blade (Sélectionner des propriétés à partir du volet du menu)- \> Copy the Directory ID (Copier l’ID du répertoire). Exemple de valeur : 72f988bf-86f1-41af-91ab-2d7cd0111234
- **Azure AD App ID** (ID Azure AD App) : ID d’Azure AD App que vous avez créé à l’étape 7.\ Exemple de valeur : 53852862-a2ae-4e43-9461-faa49650a096
- **Azure AD App Key** (Clé Azure AD App) : clé secrète d’Azure AD App créée à l’étape 7.\ Exemple de valeur : IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=
- **Nom du locataire Azure AD** : fournissez le nom du locataire Azure pour votre instance Dynamics 365. Utilisez le format \<tenantname.\>onmicrosoft.com. Exemple de valeur : testdrive.onmicrosoft.com
- **Instance Web API URL** (URL d’API web de l’instance) : fournissez l’URL d’API web de votre instance Dynamics 365. Vous pouvez récupérer cette valeur en vous connectant à votre instance Microsoft Dynamics 365 et en accédant à Paramètre -\> Personnalisation -\> Ressources du développeur -\> Instance Web API (Copy this URL) (API web de l’instance (Copier cette URL)). Exemple de valeur : https:\//testdrive.crm.dynamics.com/api/data/v9.0
- **Nom du rôle** : indiquez le nom du rôle de sécurité Dynamics 365 personnalisé que vous avez créé pour la version d’évaluation. C’est le rôle qui sera assigné aux utilisateurs lors de leur utilisation de la version d’évaluation. Exemple de valeur : testdriverole

## <a name="next-steps"></a>Étapes suivantes

Lorsque vous êtes prêt à **publier** votre offre, une fois votre application aura passé la certification, vous aurez une **préversion** de votre offre. Lancez une version d’évaluation dans l’interface utilisateur et vérifiez que vos versions d’évaluation s’exécutent correctement. Une fois que vous vous sentez à l’aise avec votre offre en préversion, il est temps de la faire **entrer en service** !
