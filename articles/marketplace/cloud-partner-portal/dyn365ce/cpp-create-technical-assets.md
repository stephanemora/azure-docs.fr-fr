---
title: Créer de Dynamics 365 pour les ressources techniques d’Engagement des clients | Place de marché Azure
description: Créez les ressources techniques pour une offre d’application Dynamics 365 for Customer Engagement.
services: Dynamics 365 for Customer Engagement, Azure, Marketplace, Cloud Partner Portal, AppSource
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 12/29/2018
ms.author: pabutler
ms.openlocfilehash: eff175264677d6b8ffb885229b5e68b306424335
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943101"
---
# <a name="create-technical-assets-for-azure-application-offer"></a>Créer les ressources techniques pour une offre d’application Azure

En général, vous développez des solutions à l’aide du [SDK pour les applications Dynamics 365 for Customer Engagement](https://docs.microsoft.com/dynamics365/customer-engagement/developer/get-started-sdk).  Les solutions prennent diverses formes, comme décrit dans [Modèles de programmation pour les applications Dynamics 365 Customer Engagement](https://docs.microsoft.com/dynamics365/customer-engagement/developer/programming-models).  Choisissez le formulaire qui respecte le mieux les exigences de votre solution.  Quand vous développez une solution, vous devez résoudre un certain nombre de problèmes, comme les choix de l’extensibilité, les composants de la solution et la compatibilité des versions.  Pour plus d’informations, consultez [Présentation des solutions](https://docs.microsoft.com/dynamics365/customer-engagement/developer/introduction-solutions).

La plupart des solutions Dynamics 365 publiées sur AppSource sont des applications managées distribuées sous la forme de fichiers de package.


## <a name="creating-and-storing-the-package"></a>Création et stockage du package

La documentation parallèle relative à la création d’offres Dynamics 365 for Customer Engagement se trouve dans la section [Publier votre application sur AppSource](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource).  Les rubriques suivantes qu’elle contient expliquent en détail comment créer le fichier de package de la solution et le charger sur le stockage Azure :

- [Étape 4 : Créer un package AppSource pour votre application](https://docs.microsoft.com/dynamics365/customer-engagement/developer/create-package-app-appsource) : explique comment créer un fichier compressé (zip) qui représente votre application managée et qui contient les éléments suivants : dossier des ressources de la solution, DLL de code personnalisé, fichier d’informations sur le type MIME, icône de package AppSource, fichier (HTML) des termes du contrat de licence et fichier (XML) de contenu.
- [Étape 5 : Stocker votre package AppSource sur le Stockage Azure et générer une URL avec une clé SAP](https://docs.microsoft.com/dynamics365/customer-engagement/developer/store-appsource-package-azure-storage) : explique comment stocker un fichier de package AppSource dans un compte de Stockage Blob Microsoft Azure et utiliser une clé SAP (signature d’accès partagé) pour partager le fichier de package. Votre fichier de package est récupéré à partir de votre emplacement de Stockage Azure pour certification. Il est ensuite utilisé pour des essais AppSource et la publication.


## <a name="next-steps"></a>Étapes suivantes

Si vous ne l’avez pas déjà fait, [créez votre offre Dynamics 365 for Customer Engagement](./cpp-create-offer.md).  Vous serez alors prêt à [publier votre offre](./cpp-publish-offer.md).
