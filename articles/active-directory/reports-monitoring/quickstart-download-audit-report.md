---
title: Démarrage rapide - Télécharger un rapport d’audit avec le portail Azure | Microsoft Docs
description: Découvrez comment télécharger un rapport d’audit avec le portail Azure
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f3e5dd1c42537ce6ff419d7d81d69d824242ec4
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989692"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Démarrage rapide : Télécharger un rapport d’audit avec le portail Azure

Dans ce guide de démarrage rapide, vous apprenez à télécharger un fichier CSV des journaux d’audit des dernières 24 heures pour votre locataire. Vous pouvez télécharger jusqu’à 250 000 enregistrements à partir du portail Azure. Les enregistrements sont triés par date. Par défaut, les 250 000 enregistrements les plus récents s’affichent. 

## <a name="prerequisites"></a>Prérequis

Ce dont vous avez besoin :

* Un locataire Azure Active Directory. 
* Un utilisateur dans le rôle **Administrateur de la sécurité**, **Lecteur Sécurité** ou **Administrateur général** pour le locataire. De plus, tout utilisateur du locataire peut accéder à ses propres journaux d’audit.

## <a name="quickstart-download-an-audit-report"></a>Démarrage rapide : Télécharger un rapport d’audit

1. Accédez au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Azure Active Directory** à partir du volet de navigation gauche et utilisez le bouton **Changer de répertoire** pour sélectionner votre instance Active Directory.
3. Dans le tableau de bord, sélectionnez **Azure Active Directory**, puis **Journaux d’audit**. 
4. Choisissez **Dernières 24 heures** dans la liste déroulante du filtre **Plage de dates** et sélectionnez **Appliquer** pour afficher les journaux d’audit des dernières 24 heures. 
5. Sélectionnez le bouton **Télécharger**, sélectionnez **CSV** comme format de fichier, et spécifiez un nom de fichier pour télécharger un fichier CSV contenant les enregistrements filtrés. 

![Reporting](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>Étapes suivantes

* [Rapports d’activité de connexion dans le portail Azure Active Directory](concept-sign-ins.md)
* [Rétention des rapports Azure Active Directory](reference-reports-data-retention.md)
* [Latences de création de rapports Azure Active Directory - Préversion](reference-reports-latencies.md)
