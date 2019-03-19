---
title: Onglet Version d’évaluation de l’offre d’application Dynamics 365 for Customer Engagement - Place de marché Azure | Microsoft Docs
description: Guide pratique pour configurer la version d’évaluation d’une offre d’application Dynamics 365 for Customer Engagement sur la Place de marché AppSource.
services: Azure, Marketplace, AppSource, Cloud Partner Portal, Dynamics 365 for Customer Engagement
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/25/2018
ms.author: pbutlerm
ms.openlocfilehash: 373312b4c7f05fe41c9ca8165b8ff6f1b0e56f1b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454967"
---
# <a name="dynamics-365-for-customer-engagement-application-test-drive-tab"></a>Onglet Version d’évaluation d’une application Dynamics 365 for Customer Engagement

Utilisez l’onglet **Version d’évaluation** pour créer une expérience d’évaluation pour vos clients.  La version d’évaluation permet aux clients d’essayer les principales fonctionnalités de votre offre et d’en apprécier les avantages grâce à un scénario d’implémentation réel.  Parmi les options d’évaluation disponibles, la version d’évaluation est la plus efficace pour générer des prospects pertinents et les transformer en clients.  Pour plus d’informations, consultez la rubrique [Qu’est-ce qu’une version d’évaluation ?](../test-drive/what-is-test-drive.md)

L’expérience d’évaluation des applications Dynamics 365 s’exécute automatiquement comme une solution hébergée par Microsoft.  Pour plus d’informations, consultez [Version d’évaluation hébergée](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/hosted-test-drive).

L’onglet Version d’évaluation comprend trois sections potentielles : **Version d’évaluation**, **Détails** et **Configuration technique**.  Les deux dernières sections ne s’affichent qu’une fois que vous avez activé la fonctionnalité Version d’évaluation.  Si un astérisque (*) se trouve en regard du nom du champ, cela signifie que ce champ est obligatoire. 


## <a name="test-drive-section"></a>Section Version d’évaluation

Pour activer cette fonctionnalité, sélectionnez **Oui** pour **Activer une version d’évaluation**.


## <a name="details-section"></a>Section Détails

Vous fournissez les informations de base relatives à la version d’évaluation dans la section **Détails**.   

![Section Détails de la version d’évaluation](./media/test-drive-tab-details.png)

Le tableau suivant décrit les champs obligatoires pour configurer la version d’évaluation de votre application Dynamics 365.

|      Champ                    |    Description                  |
|    ---------                  |  ---------------                |
|      Description              |   Décrivez ce qui peut être fait sur votre version d’évaluation. Vous pouvez utiliser des balises HTML de base pour mettre en forme cette description. Par exemple, &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;et les en-têtes.  |
|  Manuel utilisateur                  |   Téléchargez un manuel utilisateur que vos clients peuvent utiliser pour se guider au cours de l’expérience d’évaluation. Ce document doit être au format pdf.              |
|  Vidéo de démonstration de version d’évaluation (facultative) |  Vous pouvez fournir une vidéo de procédure pas à pas de votre version d’évaluation. Un client peut regarder cette vidéo avant de se procurer une version d’évaluation. Fournissez une URL vers la vidéo sur YouTube ou Vimeo. Si vous sélectionnez **+ Ajouter une vidéo**, vous être invité à fournir les informations suivantes :<ul><li>Nom</li><li>URL</li><li>Miniature (au format PNG, 533 x 324 pixels)</li></ul>  |
|   |   |


## <a name="technical-configuration-section"></a>Section Configuration technique

Dans cette section, vous fournissez des détails techniques concernant votre version d’évaluation.

![Section Détails de la version d’évaluation](./media/test-drive-tab-tech-config.png)

Les champs de cette section ont les objectifs suivants :

|      Champ                    |    Description                  |
|    ---------                  |  ---------------                |
| Type de version d’évaluation            | Choisissez **Hébergé par Microsoft (Dynamics 365 for Customer Engagement)**.  |
| Versions d’évaluation simultanées max.    | Nombre d’instances simultanées d’une version d’évaluation active à un moment donné. Chaque utilisateur se sert d’une licence Dynamics quand sa version d’évaluation est active. Vous allez donc devoir vous assurer que vous avez au moins autant de licences Dynamics disponibles que d’utilisateurs. La valeur recommandée est de 3 à 5.  |
| Durée d’une version d’évaluation (heures)   | Nombre maximal d’heures pendant lesquelles une instance de version d’évaluation de l’utilisateur sera active. Une fois cette période écoulée, l’instance est déprovisionnée de votre client. La valeur recommandée de 2 à 24 heures dépend de la complexité de votre application. L’utilisateur peut toujours demander une autre version d’évaluation s’il n’a pas eu assez de temps et qu’il souhaite effectuer une nouvelle évaluation.  |
| URL d’instance                  | URL à laquelle la version d’évaluation accède initialement. Il s’agit généralement de l’URL de votre instance Dynamics 365 sur laquelle votre application et les exemples de données sont installés.  |
| ID de locataire Azure AD            | GUID du locataire Azure pour votre instance Dynamics 365. Pour récupérer cette valeur, connectez-vous au portail Azure et accédez à **Azure Active Directory** > **Sélectionner des propriétés** >  **Copier l’ID de répertoire**.  |
| ID de l’application Azure AD               | GUID de votre application Azure AD  |
| Clé d’application Azure AD              | Secret de votre application Azure AD ; par exemple : `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` |
| Nom du locataire Azure AD          | Nom du locataire Azure pour votre instance Dynamics 365. Utilisez le format <nom_locataire.>onmicrosoft.com ; par exemple : `testdrive.onmicrosoft.com`  |
| URL d’API web de l’instance          | URL d’API web pour votre instance Dynamics 365. Vous pouvez récupérer cette valeur en vous connectant à votre instance Microsoft Dynamics 365 et en accédant à **Paramètres** > **Personnalisation** > **Ressources du développeur** > **API web de l’instance (Copier cette URL)**. Exemple de valeur : `https://testdrive.crm.dynamics.com/api/data/v9.0`  |
| Nom de rôle                     | Nom du rôle de sécurité Dynamics 365 personnalisé que vous avez créé pour votre version d’évaluation et qui sera attribué aux utilisateurs quand ils l’exécuteront ; par exemple, `testdriveuser`. |
|  |  |

Après avoir fourni toutes les informations nécessaires, sélectionnez **Enregistrer**.


## <a name="next-steps"></a>Étapes suivantes

Vous devez ensuite fournir des informations de ventes et marketing sous l’[onglet Détails de la vitrine](./cpp-storefront-details-tab.md).

