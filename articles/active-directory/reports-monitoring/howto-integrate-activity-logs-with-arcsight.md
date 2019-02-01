---
title: Comment intégrer des journaux Azure Active Directory à ArcSight à l’aide d’Azure Monitor (préversion) | Microsoft Docs
description: Découvrez comment intégrer des journaux Azure Active Directory à ArcSight à l’aide d’Azure Monitor (préversion)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 12/03/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 1e9f9fe5c04e5293c91765795dcbfb9b0800b809
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55168598"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor-preview"></a>Intégrer des journaux Azure Active Directory à ArcSight à l’aide d’Azure Monitor (préversion)

[Micro Focus ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) est une solution SIEM (Security Information and Event Management) qui vous permet de détecter les menaces de sécurité dans votre plateforme et d’y remédier. Vous pouvez désormais acheminer les journaux Azure Active Directory (Azure AD) vers ArcSight avec Azure Monitor à l’aide du connecteur ArcSight pour Azure AD. Cette fonctionnalité vous permet de surveiller votre locataire et de détecter les éléments en compromettant la sécurité à l’aide d’ArcSight.  

Dans cet article, vous allez apprendre à router les journaux Azure AD vers ArcSight à l’aide d’Azure Monitor. 

## <a name="prerequisites"></a>Prérequis

Pour utiliser cette fonctionnalité, vous avez besoin des éléments suivants :
* Un hub d’événements Azure contenant les journaux d’activité d’Azure AD. Découvrez comment [diffuser en continu vos journaux d’activité sur un hub d’événements](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Une instance configurée d’ArcSight Syslog NG Daemon SmartConnector (SmartConnector) ou ArcSight Load Balancer. Si les événements sont envoyés à ArcSight Load Balancer, ils sont ensuite envoyés SmartConnector par l’équilibreur de charge.

Téléchargez et ouvrez le [guide de configuration d’ArcSight SmartConnector pour l’Event Hub Azure Monitor](https://community.softwaregrp.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/2/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf). Ce guide décrit les étapes à suivre pour installer et configurer ArcSight SmartConnector pour Azure Monitor. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Intégrer les journaux Azure AD à ArcSight

1. Tout d’abord, suivez les étapes de la section **Conditions préalables** du guide de configuration. Cette section comprend les étapes suivantes :
    * Définissez les autorisations utilisateur dans Azure, afin de vous assurer qu’un utilisateur a le rôle **propriétaire** pour déployer et configurer le connecteur.
    * Ouvrez les ports sur le serveur avec Syslog NG Daemon SmartConnector, afin qu’il soit accessible depuis Azure. 
    * Le déploiement exécute un script Windows PowerShell, vous devez donc autoriser PowerShell à exécuter des scripts sur l’ordinateur où vous souhaitez déployer le connecteur.

2. Suivez les étapes de la section **Déploiement du connecteur** du guide de configuration pour déployer le connecteur. Cette section vous explique comment télécharger et extraire le connecteur, configurer les propriétés de l’application et exécuter le script de déploiement dans le dossier extrait. 

3. Utilisez les étapes décrites dans **Vérification du déploiement dans Azure** pour vous assurer que le connecteur est configuré et fonctionne correctement. Vérifiez les points suivants :
    * Les fonctions Azure requises sont créées dans votre abonnement Azure.
    * Les journaux Azure AD sont diffusés en continu vers la destination correcte. 
    * Les paramètres d’application de votre déploiement sont conservés dans les paramètres d’application d’Azure Function Apps. 
    * Un nouveau groupe de ressources pour ArcSight est créé dans Azure, avec une application Azure AD pour le connecteur ArcSight et des comptes de stockage contenant les fichiers mappés au format CEF.

4. Enfin, effectuez les étapes post-déploiement décrites dans la section **Configurations post-déploiement** du guide de configuration. Cette section explique comment effectuer une configuration supplémentaire si vous utilisez un plan App Service pour empêcher que les applications de fonction deviennent inactives après un certain délai d’attente, configurer la diffusion en continu des journaux de diagnostic à partir du hub d’événements et mettre à jour le certificat de magasins de clés SysLog NG Daemon SmartConnector pour l’associer avec le compte de stockage nouvellement créé.

5. Le guide de configuration explique également comment personnaliser les propriétés du connecteur dans Azure, et comment le mettre à niveau et le désinstaller. Il contient également une section sur les améliorations des performances, notamment sur la mise à niveau vers un [plan de consommation Azure](https://azure.microsoft.com/pricing/details/functions) et la configuration d’ArcSight Load Balancer si la charge de l’événement est supérieure à ce qu’un seul Syslog NG Daemon SmartConnector peut gérer.

## <a name="next-steps"></a>Étapes suivantes

* [Guide de configuration d’ArcSight SmartConnector pour l’Event Hub Azure Monitor](https://community.softwaregrp.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/2/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf)
