---
title: Onglet Version d’évaluation de machine virtuelle dans le portail Cloud Partner pour la Place de marché Microsoft Azure | Microsoft Docs
description: Décrit l’onglet Version d’évaluation utilisé pour créer une offre de machine virtuelle de la Place de marché Microsoft Azure.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 97fb21dc390bd365357f6395c72aa282423c83c9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61344612"
---
# <a name="virtual-machine-test-drive-tab"></a>Onglet Version d’évaluation de machine virtuelle

<!-- TD: The AMP tree needs a conceptual/business overview of Test Drive. I've deleted all the marketing fluff and most of overview from this topic.   See also https://azure.microsoft.com/blog/azure-marketplace-test-drive/ and https://github.com/Azure/AzureTestDrive/wiki/What-is-a-Test-Drive. --> 

L’onglet **Version d’évaluation** de la page **Nouvelle offre** vous permet de fournir à vos clients potentiels une démonstration pratique et autonome des principales fonctionnalités et principaux avantages de votre produit, présentée selon un scénario standardisé.  Version d’évaluation est une fonctionnalité facultative pour les types d’offres qui prennent en charge de la version d’évaluation.  Version d’évaluation nécessite que la prise en charge des ressources soit correctement mise en œuvre.  Pour plus d’informations, consultez l’article [Version d’évaluation de la Place de marché Microsoft Azure](https://azure.microsoft.com/blog/azure-marketplace-test-drive/).  <!--TD: Replace with migrated version of Test Drive article! -->

Pour activer cette fonctionnalité, sur l’onglet **Version d’évaluation**, cliquez sur l’option **Oui** sur **Activer une version d’évaluation**.  L’onglet **Version d’évaluation** affiche les champs pouvant être modifiés.  Un astérisque (*) en regard du nom du champ indique que ce champ est obligatoire.

![Onglet Version d’évaluation du formulaire Nouvelle offre pour les machines virtuelles](./media/publishvm_007.png)

<br/>

Le tableau suivant décrit l’objectif et le contenu de ces champs.


|  **Champ**                |     **Description**                                                          |
|  ---------                |     ---------------                                                          |
|  *Détails*   |  |
| **Description**           | Fournissez une vue d’ensemble de votre scénario de version d’évaluation. L’utilisateur pourra voir ce texte pendant l’approvisionnement de la version d’évaluation. Ce champ prend en charge le langage HTML de base si vous souhaitez proposer du contenu mis en forme.  |
| **Manuel de l’utilisateur**           | Chargez un manuel d’utilisateur détaillé (.pdf) qui permet aux utilisateurs de la version d’évaluation d’apprendre à utiliser votre solution.  |
| **Vidéo de démonstration de version d’évaluation** | Chargez une vidéo qui présente votre solution.  Si vous choisissez cette option, vous devez fournir un nom, une URL vers la vidéo (hébergée sur YouTube ou Vimeo) et une miniature (533 x 324 pixels) de la vidéo. |
| *Configuration technique* |  |
| **Instances**             | Spécifiez la disponibilité dans la région et la disponibilité relative de l’instance de machine virtuelle (cliquez sur l’icône d’informations pour plus d’informations).  <br/>Les sessions de version d’évaluation simultanées ne doivent pas dépasser la limite de quota de votre abonnement.  Elle est calculée comme suit : [Nombre de régions sélectionnées] x [Instances à chaud] + [Nombre de régions sélectionnées] x [Instances tièdes] + [Nombre de régions sélectionnées] x [Instances à froid] |
| **Durée du Test Drive**   | Durée maximum de session en heures. La session de version d’évaluation se termine automatiquement après que cette période s’est écoulée.  |
|**Test Drive ARM Template** (Modèle ARM de version d’évaluation)| Chargez le modèle Azure Resource Manager associé à cette version d’évaluation. Pour plus d’informations, consultez [Transforming Virtual Machine Deployment Template for Test Drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive) (Transformer le modèle de déploiement de machine virtuelle pour Version d’évaluation). |
| **Informations d’accès**    | Informations d’accès et de connexion d’essai d’Azure Resource Manager, écrites en texte brut ou HTML simple. |
| *Détails de l’abonnement de déploiement de la version d’évaluation* |  |
| **ID d’abonnement Azure** | Peut être obtenu en se connectant au [portail Microsoft Azure](https://ms.portal.azure.com) et en cliquant sur **Abonnements** sur la barre de menus de gauche. (Exemple : « a83645ac-1234-5ab6-6789-1h234g764ghty ») Cet identificateur doit être un GUID sous la forme `a83645ac-1234-5ab6-6789-1h234g764ghty`.|
| **ID de locataire Azure AD**    | ID de locataire Azure Active Directory.  Peut être obtenu en se connectant au [portail Microsoft Azure](https://ms.portal.azure.com) et en cliquant sur **Azure Active Directory** sur la barre de menus de gauche, en cliquant sur **Propriétés** sur la barre de menus du milieu, puis en copiant l’**ID d’annuaire** à partir du formulaire.  Cet identificateur doit également être un GUID.  S’il est vide, vous devez créer un ID de locataire pour votre organisation. |
| **ID de l’application Azure AD**       | Identificateur de votre solution de machine virtuelle Azure inscrite  |
| **Clé d’application Azure AD**      | Clé d’authentification de votre solution inscrite |
|  |  |

<br/>

Dans l’onglet [Place de marché](./cpp-marketplace-tab.md) suivant, vous fournissez des informations marketing et juridiques relatives à votre solution.
