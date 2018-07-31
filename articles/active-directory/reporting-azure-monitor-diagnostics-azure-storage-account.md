---
title: 'Didacticiel : Archiver des journaux Azure Active Directory dans un compte de stockage Azure (préversion) | Microsoft Docs'
description: Découvrez comment configurer Azure Diagnostics pour envoyer des journaux Azure Active Directory sur un compte de stockage (préversion)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ea26da91e2a0b88e8afa54a4210b7a2365e4949b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240186"
---
# <a name="tutorial-archive-azure-active-directory-logs-to-an-azure-storage-account-preview"></a>Didacticiel : Archiver des journaux Azure Active Directory dans un compte de stockage Azure (préversion)

Dans ce didacticiel, découvrez comment configurer les paramètres de diagnostic Azure Monitor pour acheminer des journaux Azure Active Directory vers un compte de stockage Azure.

## <a name="prerequisites"></a>Prérequis 

Ce dont vous avez besoin :

* Un abonnement et un compte de stockage Azure. Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/free/).
* Un locataire Azure Active Directory.
* Un utilisateur, qui est un administrateur général ou un administrateur de la sécurité pour ce locataire.

## <a name="archive-logs-to-an-azure-storage-account"></a>Archiver les journaux dans un compte de stockage Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com). 
2. Cliquez sur **Azure Active Directory** -> **Activité** -> **journaux d’audit**. 
3. Cliquez sur **Exporter les paramètres** pour ouvrir le panneau Paramètres de diagnostic. Cliquez sur **Modifier les paramètres** si vous souhaitez modifier les paramètres existants, ou sur **Add diagnostic setting (Ajouter un paramètre de diagnostic)** pour ajouter un nouveau paramètre. Vous pouvez avoir un maximum de trois paramètres. 
    ![Exporter les paramètres](./media/reporting-azure-monitor-diagnostics-azure-storage-account/ExportSettings.png "Exporter les paramètres")

4. Donnez au paramètre un nom qui vous permettra de vous rappeler de sa fonction. Par exemple, « Envoyer vers un compte de stockage Azure ». 
5. Cochez la case **Archive to a storage account (Archiver dans un compte de stockage)**, puis cliquez sur **Compte de stockage** pour choisir le compte de stockage Azure. 
6. Sélectionnez un abonnement et un compte de stockage Azure vers lesquels vous souhaitez acheminer les journaux, puis cliquez sur **OK** pour fermer la configuration.
7. Cochez la case **AuditLogs** pour envoyer les journaux d’audit vers le compte de stockage. 
8. Cochez la case **SignInLogs** pour envoyer les journaux de connexion vers le compte de stockage.
9. Utilisez le curseur pour définir la rétention de vos données de journal. Par défaut, cette valeur est « 0 » et les journaux sont conservés indéfiniment dans le compte de stockage. Vous avez toutefois la possibilité de définir une valeur. Si vous le faites, les événements plus anciens que le nombre de jours indiqué seront automatiquement effacés.
10. Cliquez sur **Enregistrer** pour enregistrer le paramètre.
    ![Paramètres de diagnostic](./media/reporting-azure-monitor-diagnostics-azure-storage-account/DiagnosticSettings.png "Paramètres de diagnostic")

11. Après environ 15 minutes, vérifiez que les journaux sont transmis à votre compte de stockage. Accédez au portail Azure, cliquez sur **Comptes de stockage**, choisissez le compte de stockage que vous avez utilisé précédemment, puis cliquez sur **Blobs**. 
12. Dans **Journaux d’audit**, cliquez sur **insights-log-audit**. Dans **Journaux de connexion**, cliquez sur **insights-logs-signin**.
    ![Compte de stockage](./media/reporting-azure-monitor-diagnostics-azure-storage-account/StorageAccount.png "Compte de stockage")

## <a name="next-steps"></a>Étapes suivantes

* [Interpréter le schéma des journaux d’audit dans Azure Monitor](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Interpréter le schéma des journaux de connexion dans Azure Monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Questions fréquentes et problèmes connus](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)