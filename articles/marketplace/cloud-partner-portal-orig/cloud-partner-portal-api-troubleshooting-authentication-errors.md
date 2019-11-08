---
title: Résolution des erreurs d’authentification courantes | Place de marché Azure
description: Fournit de l’aide sur les erreurs d’authentification courantes lors de l’utilisation des API du Portail Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 0990e9aedf17f6d4ad01e4911e47efd60001f3d7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827363"
---
# <a name="troubleshooting-common-authentication-errors"></a>Résolution des erreurs d’authentification courantes

Cet article fournit de l’aide sur les erreurs d’authentification courantes lors de l’utilisation des API du Portail Cloud Partner.

## <a name="unauthorized-error"></a>Erreur Non autorisé

Si vous recevez régulièrement des erreurs `401 unauthorized`, vérifiez que vous disposez d’un jeton d’accès valide.  Si vous ne l’avez pas déjà fait, créez une application et un principal du service Azure Active Directory (Azure AD) de base comme décrit dans [Utiliser le portail pour créer une application et un principal du service Azure Active Directory pouvant accéder aux ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Utilisez ensuite l’application ou une requête HTTP POST simple pour vérifier votre accès.  Vous allez inclure l’ID de locataire, ID d’application, ID d’objet et la clé secrète pour obtenir le jeton d’accès comme illustré dans l’image suivante :

![Résolution de l’erreur 401](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Message d’interdiction

Si vous obtenez une erreur `403 forbidden`, assurez-vous que le principal du service approprié a été ajouté à votre compte d’éditeur dans le Portail Cloud Partner.
Suivez les étapes de la page [Prérequis](./cloud-partner-portal-api-prerequisites.md) pour ajouter votre principal du service au portail.

Si le principal du service approprié a été ajouté, vérifiez toutes les autres informations. Soyez très attentif à l’ID d’objet entré sur le portail. Il existe deux ID d’objet dans la page d’inscription d’application Azure Active Directory, et vous devez utiliser l’ID d’objet local. Vous pouvez trouver la valeur appropriée en accédant à la page **Inscriptions des applications** de votre application et en cliquant sur le nom de l’application sous **Application managée dans l’annuaire local**. Vous accédez aux propriétés locales de l’application, où vous trouverez l’ID d’objet approprié dans la page **Propriétés**, comme indiqué dans l’illustration suivante. Assurez-vous également d’utiliser l’ID d’éditeur approprié lors de l’ajout du principal du service et de l’appel d’API.

![Résolution de l’erreur 403](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
