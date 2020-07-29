---
title: Résolution des erreurs d’authentification courantes | Place de marché Azure
description: Fournit de l’aide sur les erreurs d’authentification courantes lors de l’utilisation des API du Portail Cloud Partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: dsindona
ms.author: dsindona
ms.date: 07/14/2020
ms.openlocfilehash: bfcee6a6d9a8448315d59009ea8a6ef62fa71da0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535992"
---
# <a name="troubleshooting-common-authentication-errors"></a>Résolution des erreurs d’authentification courantes

> [!NOTE]
> Les API de Portail Cloud Partner sont intégrées à Espace partenaires et continueront d’y fonctionner. La transition introduit de légères modifications. Passez en revue les changements répertoriés dans les [informations de référence relatives aux API de Portail Cloud Partner](./cloud-partner-portal-api-overview.md) pour vous assurer que votre code continue de fonctionner après la transition vers Espace partenaires. Les API de Portail Cloud Partner doivent uniquement être utilisées pour les produits existants intégrés avant la transition vers Espace partenaires. Les nouveaux produits doivent utiliser les API de soumission d’Espace partenaires.

Cet article fournit de l’aide sur les erreurs d’authentification courantes lors de l’utilisation des API du Portail Cloud Partner.

## <a name="unauthorized-error"></a>Erreur Non autorisé

Si vous recevez régulièrement des erreurs `401 unauthorized`, vérifiez que vous disposez d’un jeton d’accès valide.  Si vous ne l’avez pas déjà fait, créez une application et un principal du service Azure Active Directory (Azure AD) de base comme décrit dans [Utiliser le portail pour créer une application et un principal du service Azure Active Directory pouvant accéder aux ressources](../active-directory/develop/howto-create-service-principal-portal.md). Utilisez ensuite l’application ou une requête HTTP POST simple pour vérifier votre accès.  Vous allez inclure l’ID de locataire, ID d’application, ID d’objet et la clé secrète pour obtenir le jeton d’accès comme illustré dans l’image suivante :

![Résolution de l’erreur 401](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)

## <a name="forbidden-error"></a>Message d’interdiction

Si vous obtenez une erreur `403 forbidden`, assurez-vous que le principal du service approprié a été ajouté à votre compte d’éditeur dans le Portail Cloud Partner.
Suivez les étapes de la page [Prérequis](./cloud-partner-portal-api-prerequisites.md) pour ajouter votre principal du service au portail.

Si le principal du service approprié a été ajouté, vérifiez toutes les autres informations. Soyez très attentif à l’ID d’objet entré sur le portail. Il existe deux ID d’objet dans la page d’inscription d’application Azure Active Directory, et vous devez utiliser l’ID d’objet local. Vous pouvez trouver la valeur appropriée en accédant à la page **Inscriptions des applications** de votre application et en cliquant sur le nom de l’application sous **Application managée dans l’annuaire local**. Vous accédez aux propriétés locales de l’application, où vous trouverez l’ID d’objet approprié dans la page **Propriétés**, comme indiqué dans l’illustration suivante. Assurez-vous également d’utiliser l’ID d’éditeur approprié lors de l’ajout du principal du service et de l’appel d’API.

![Résolution de l’erreur 403](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
