---
title: Guide pratique pour déployer l’outil d’étiquetage des exemples Form Recognizer
titleSuffix: Azure Cognitive Services
description: Découvrez les différentes façons de déployer l’outil d’étiquetage des exemples Form Recognizer pour faciliter l’apprentissage supervisé.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: pafarley
ms.openlocfilehash: fa419d7dd9668ac2ce8f2b0eb904117c7e22692d
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207831"
---
# <a name="deploy-the-sample-labeling-tool"></a>Déployer l’outil d’étiquetage des exemples

L’outil d’étiquetage des exemples Form Recognizer est une application qui s’exécute dans un conteneur Docker. Il fournit une interface utilisateur utile que vous pouvez utiliser pour étiqueter manuellement les documents de formulaire dans le cadre d’un apprentissage supervisé. Le guide de démarrage rapide [Effectuer l’entraînement avec des étiquettes](./quickstarts/label-tool.md) vous montre comment exécuter l’outil sur votre ordinateur local, ce qui est le scénario le plus courant. 

Ce guide décrit les différentes façons dont vous pouvez déployer et exécuter l’outil d’étiquetage des exemples. 

## <a name="deploy-with-azure-container-instances"></a>Déployer avec Azure Container Instances

Vous pouvez exécuter l’outil d’étiquetage dans un conteneur d’application web Docker. Tout d’abord, [créez une ressource d’application web](https://ms.portal.azure.com/#create/Microsoft.WebSite) dans le portail Azure. Renseignez le formulaire avec les détails de votre abonnement et du groupe de ressources. Entrez les informations suivantes dans les champs obligatoires :
* **Publier** : Conteneur Docker
* Système **d’exploitation** : Linux

Dans la page suivante, renseignez les champs suivants pour la configuration du conteneur Docker :

* **Options** : Conteneur unique
* **Source d’image** : Azure Container Registry
* **Type d’accès** : public
* **Image et étiquette** : mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest

Les étapes qui suivent sont facultatives. Une fois le déploiement de votre application terminé, vous pouvez l’exécuter et accéder à l’outil d’étiquetage en ligne.

### <a name="connect-to-azure-ad-for-authorization"></a>Se connecter à Azure AD pour l’autorisation

Nous vous recommandons de connecter votre application web à Azure Active Directory (AAD) de façon à ce que seule une personne disposant de vos informations d’identification puisse se connecter et utiliser l’application. Suivez les instructions qui figurent dans [Configurer votre application App Service ](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) pour vous connecter à AAD.

## <a name="next-steps"></a>Étapes suivantes

Revenez au guide de démarrage rapide [Effectuer l’entraînement avec des étiquettes](./quickstarts/label-tool.md) afin d’apprendre à utiliser l’outil pour étiqueter manuellement les données d’entraînement et effectuer un apprentissage supervisé.
