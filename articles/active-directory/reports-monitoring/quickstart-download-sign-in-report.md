---
title: Démarrage rapide - Télécharger un rapport de connexion avec le portail Azure | Microsoft Docs
description: Découvrez comment télécharger un rapport de connexion avec le portail Azure
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeb5a06aa4eb21ba374344623f9f96ce92a59ff5
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56168815"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Démarrage rapide : télécharger un rapport de connexion à l’aide du portail Azure

Dans ce guide de démarrage rapide, vous apprenez à télécharger les données de connexion des dernières 24 heures pour votre locataire. Vous pouvez télécharger jusqu’à 5 000 enregistrements à partir du portail Azure. Les enregistrements sont triés par date. Par défaut, les 5 000 enregistrements les plus récents s’affichent. 

## <a name="prerequisites"></a>Prérequis

Ce dont vous avez besoin :

* Un locataire Azure Active Directory, avec une licence Premium pour afficher le rapport d’activité de connexion. Consultez [Bien démarrer avec Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) pour mettre à niveau votre édition d’Azure Active Directory. Notez que si vous n’aviez aucune donnée d’activité avant la mise à niveau, l’affichage des données dans les rapports prendra quelques jours une fois la mise à niveau vers une licence premium effectuée.
* Un utilisateur dans le rôle **Administrateur de la sécurité**, **Lecteur Sécurité**, **Lecteur de rapport** ou **Administrateur général** pour le locataire. De plus, tout utilisateur du locataire peut accéder à ses propres connexions.

## <a name="quickstart-download-a-sign-in-report"></a>Démarrage rapide : Télécharger un rapport de connexion

1. Accédez au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Azure Active Directory** à partir du volet de navigation gauche et utilisez le bouton **Changer de répertoire** pour sélectionner votre instance Active Directory.
3. Dans le tableau de bord, sélectionnez **Azure Active Directory**, puis **Connexions**. 
4. Choisissez **Dernières 24 heures** dans la liste déroulante du filtre **Date** et sélectionnez **Appliquer** pour afficher les connexions des dernières 24 heures. 
5. Sélectionnez le bouton **Télécharger** pour télécharger un fichier CSV contenant les enregistrements filtrés. 

![Reporting](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>Étapes suivantes

* [Rapports d’activité de connexion dans le portail Azure Active Directory](concept-sign-ins.md)
* [Rétention des rapports Azure Active Directory](reference-reports-data-retention.md)
* [Latences de création de rapports Azure Active Directory - Préversion](reference-reports-latencies.md)
