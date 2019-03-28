---
title: 'Tutoriel : Archiver des journaux Azure Active Directory dans un compte de stockage (préversion) | Microsoft Docs'
description: Découvrez comment configurer Azure Diagnostics pour envoyer des journaux Azure Active Directory sur un compte de stockage (préversion)
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d34b41e780466d9ab734b9dbd0e58016dba7b10
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58434719"
---
# <a name="tutorial-archive-azure-ad-logs-to-an-azure-storage-account-preview"></a>Didacticiel : Archiver des journaux Azure AD dans un compte de stockage Azure (préversion)

Dans ce didacticiel, vous découvrez comment configurer les paramètres de diagnostic Azure Monitor pour acheminer des journaux Azure Active Directory (Azure AD) vers un compte de stockage Azure.

## <a name="prerequisites"></a>Prérequis 

Pour utiliser cette fonctionnalité, vous avez besoin des éléments suivants :

* Un abonnement et un compte de stockage Azure. Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/free/).
* Un locataire Azure AD.
* Utilisateur considéré comme *administrateur général* ou *administrateur de la sécurité* pour un locataire Azure AD.

## <a name="archive-logs-to-an-azure-storage-account"></a>Archiver les journaux dans un compte de stockage Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com). 

2. Sélectionnez **Azure Active Directory** > **Activité** > **Journaux d’audit**. 

3. Sélectionnez **Paramètres d’exportation**. 

4. Dans le volet **Paramètres de diagnostic**, effectuez l’une des opérations suivantes :
   * Pour modifier les paramètres existants, sélectionnez **Modifier le paramètre**.
   * Pour ajouter de nouveaux paramètres, sélectionnez **Ajouter un paramètre de diagnostic**.  
     Vous pouvez avoir jusqu’à trois paramètres. 

     ![Paramètres d’exportation](./media/quickstart-azure-monitor-route-logs-to-storage-account/ExportSettings.png)

5. Entrez un nom convivial pour le paramètre afin de vous rappeler son rôle (par exemple, *Envoyer à un compte de stockage Azure*). 

6. Cochez la case **Archiver dans un compte de stockage**, puis sélectionnez **Compte de stockage**. 

7. Sélectionnez l’abonnement Azure et compte de stockage vers lesquels vous voulez acheminer les journaux.
 
8. Cliquez sur **OK** pour quitter la configuration.

9. Effectuez une ou plusieurs des actions suivantes :
    * Cochez la case **AuditLogs** pour envoyer les journaux d’audit vers le compte de stockage. 
    * Cochez la case **SignInLogs** pour envoyer les journaux de connexion vers le compte de stockage.

10. Utilisez le curseur pour définir la rétention de vos données de journal. Par défaut, cette valeur est *0*, ce qui signifie que les journaux sont conservés indéfiniment dans le compte de stockage. Si vous définissez une valeur différente, les événements plus anciens que le nombre de jours indiqué sont automatiquement effacés.

11. Sélectionnez **Enregistrer** pour enregistrer le paramètre.

    ![Paramètres de diagnostic](./media/quickstart-azure-monitor-route-logs-to-storage-account/DiagnosticSettings.png)

12. Après environ 15 minutes, vérifiez que les journaux sont transmis à votre compte de stockage. Accédez au [portail Azure](https://portal.azure.com), sélectionnez **Comptes de stockage**, sélectionnez le compte de stockage que vous avez utilisé précédemment, puis sélectionnez **Blobs**. Dans **Journaux d’audit**, sélectionnez **insights-log-audit**. Dans **Journaux de connexion**, sélectionnez **insights-logs-signin**.

    ![Compte de stockage](./media/quickstart-azure-monitor-route-logs-to-storage-account/StorageAccount.png)

## <a name="next-steps"></a>Étapes suivantes

* [Interpréter le schéma des journaux d’audit dans Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpréter le schéma des journaux de connexion dans Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Questions fréquentes et problèmes connus](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
