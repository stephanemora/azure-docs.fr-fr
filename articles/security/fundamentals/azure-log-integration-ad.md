---
title: Intégration des journaux d’activité Azure aux journaux d’audit Azure Active Directory | Microsoft Docs
description: Découvrez comment installer le service d’intégration des journaux d’activité Azure et intégrer les journaux d’activité à partir des journaux d’audit Azure.
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: e7f3a9bc9aade5f8ade857e9c2f14f8e9898a7da
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244669"
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Intégrer des journaux d’audit Azure Active Directory

Les événements d’audit Azure Active Directory (Azure AD) vous aident à identifier les actions privilégiées survenues dans Azure Active Directory. Vous pouvez voir les types d’événements que vous pouvez suivre en examinant [les événements du rapport d’audit Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md).


>[!IMPORTANT]
> La fonctionnalité d’intégration des journaux Azure sera déconseillée à partir du 15/06/2019. Les téléchargements AzLog ont été désactivés le 27 juin 2018. Pour obtenir des conseils pour évoluer, consultez la publication [Utiliser Azure Monitor pour intégrer avec des outils SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). 

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Procédure d’intégration des journaux d’audit Azure Active Directory

> [!NOTE]
> Avant d’effectuer les étapes décrites dans cet article, vous devez lire l’article [Bien démarrer](azure-log-integration-get-started.md) et y suivre la procédure pertinente.

1. Ouvrez l’invite de commandes et exécutez la commande suivante :

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Exécutez cette commande : 
 
   ``azlog createazureid``

   Cette commande vous invite à entrer votre nom d’utilisateur Azure. La commande crée ensuite un principal du service Azure Active Directory dans les locataires Azure AD qui hébergent les abonnements Azure dans lesquels l’utilisateur connecté est un administrateur, un coadministrateur ou un propriétaire. La commande échoue si l’utilisateur connecté est seulement un utilisateur invité dans le locataire Azure AD. L’authentification à Azure s’effectue avec Azure AD. La création d’un principal du service pour l’intégration de journaux Azure crée l’identité Azure AD qui aura un accès en lecture aux abonnements Azure.

3. Exécutez la commande suivante pour fournir votre ID de locataire. Vous devez être membre du rôle administrateur de locataire pour exécuter la commande.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Exemple :

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Vérifiez les dossiers suivants pour confirmer que les fichiers JSON de journaux d’audit Azure Active Directory y sont créés :

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

La vidéo suivante montre les étapes décrites dans cet article :

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> Pour obtenir des instructions précises sur l’ajout des informations de vos fichiers JSON dans votre système de gestion des événements et des informations de sécurité (SIEM), contactez votre fournisseur SIEM.

L’aide de la Communauté est disponible via le [forum MSDN d’intégration des journaux Azure](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Ce forum permet aux membres de la communauté de l’intégration des journaux Azure de s’entraider, grâce à des questions, des réponses, des conseils et des astuces. En outre, l’équipe d’intégration des journaux Azure supervise ce forum et apporte son aide quand cela est possible.

Vous pouvez également ouvrir une [demande de support](../../azure-supportability/how-to-create-azure-support-request.md). Pour ce faire, sélectionnez **Intégration du journal** comme service pour lequel vous demandez de l’aide.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur l’intégration des journaux Azure, consultez :

* [Microsoft Azure Log Integration pour les journaux d’activité Azure](https://azure.microsoft.com/services/monitor/) : La page Centre de téléchargement contient des informations détaillées, la configuration système requise et des instructions d'installation pour Azure Log Integration.
* [Présentation d'Azure Log Integration](azure-log-integration-overview.md) : Cet article présente Azure Log Integration, ses principales fonctionnalités et son fonctionnement.
* [Forum aux questions sur Azure Log Integration](azure-log-integration-faq.md) : Cet article répond à des questions sur l’intégration des journaux Azure.
* [Nouvelles fonctionnalités de Diagnostics Azure et des journaux d'audit Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) : Ce billet de blog présente les journaux d’audit Azure ainsi que d’autres fonctionnalités pour vous aider à mieux comprendre le fonctionnement de vos ressources Azure.
