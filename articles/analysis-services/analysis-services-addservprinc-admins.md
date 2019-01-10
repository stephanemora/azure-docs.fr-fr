---
title: Ajouter un principal de service au rôle d’administrateur du serveur Azure Analysis Services | Microsoft Docs
description: Découvrez comment ajouter un principal de service d’automatisation au rôle d’administrateur du serveur
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5089083862f243a6a6847a58ac0777c52f978e73
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54189726"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Ajouter un principal de service au rôle d’administrateur du serveur 

 Pour automatiser les tâches PowerShell sans assistance, un principal de service doit disposer des privilèges **Administrateur du serveur** sur le serveur Analysis Services géré. Cet article décrit comment ajouter un principal de service au rôle d’administrateurs du serveur sur un serveur Azure AS.

## <a name="before-you-begin"></a>Avant de commencer
Avant d’exécuter cette tâche, vous devez disposer d’un principal de service inscrit dans Azure Active Directory.

[Créer un principal de service - Portail Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Créer un principal de service - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>Autorisations requises
Pour effectuer cette tâche, vous devez disposer d’autorisations [Administrateur du serveur](analysis-services-server-admins.md) sur le serveur Azure AS. 

## <a name="add-service-principal-to-server-administrators-role"></a>Ajouter un principal de service au rôle d’administrateurs du serveur

1. Dans SSMS, connectez-vous à votre serveur Azure AS.
2. Dans **Propriétés du serveur** > **Sécurité**, cliquez sur **Ajouter**.
3. Dans **Sélectionnez un utilisateur ou un groupe**, recherchez votre application inscrite par nom, sélectionnez, puis cliquez sur **Ajouter**.

    ![Rechercher le compte de principal de service](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Vérifiez l’ID de compte de principal de service, puis cliquez sur **OK**.
    
    ![Rechercher le compte de principal de service](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> Pour les opérations de serveur utilisant des applets de commande AzureRm, le planificateur exécutant le principal de service doit également appartenir au rôle **Propriétaire** associé à la ressource dans le [contrôle d’accès en fonction du rôle (RBAC) d’Azure](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Informations connexes

* [Télécharger le module SQL Server PowerShell](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Télécharger SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


