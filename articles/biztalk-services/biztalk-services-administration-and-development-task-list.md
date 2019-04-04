---
title: Liste des tâches d’administration et de développement dans BizTalk Services | Microsoft Docs
description: Documentation de travail et de planification pour le déploiement d'Azure BizTalk Services.
services: biztalk-services
documentationcenter: ''
author: msftman
manager: erikre
editor: ''
ms.assetid: 0ab70b5b-1a88-4ba5-b329-ec51b785010e
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 6b8e0fea73dc24f7e680482be1f06fba6d702804
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916238"
---
# <a name="administration-and-development-task-list-in-biztalk-services"></a>Liste des tâches d'administration et de développement dans BizTalk Services

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]
> 
> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="getting-started"></a>Mise en route
Lorsque vous utilisez Microsoft Azure BizTalk Services, il existe plusieurs composants locaux et cloud à prendre en compte. Pour commencer, prenez en compte le flux de processus suivant :  

| Étape | Qui est responsable | Tâche | Liens connexes |
| --- | --- | --- | --- |
| 1. |Administrateur |Créer l’abonnement Microsoft Azure à l’aide d’un compte Microsoft ou d’un compte professionnel |[Portail Azure](https://portal.azure.com) |
| 2. |Administrateur |Créer ou approvisionner un BizTalk Service. |[Création d'un service BizTalk](/previous-versions/azure/reference/dn232347(v=azure.100)) |
| 3. |Administrateur |Enregistrer votre déploiement ou le déploiement de votre entreprise de BizTalk Services |[L’inscription et la mise à jour un déploiement BizTalk Service sur le portail BizTalk Services](/previous-versions/azure/hh689837(v=azure.100)) |
| 4. |Administrateur |S’applique si l’application utilise BizTalk Adapter Service pour se connecter à un système métier (LOB) local ou utilise une file d’attente ou une destination de rubrique.  Créer l'espace de noms Azure Service Bus Donner les valeurs espace de noms, nom de l'émetteur Service Bus et clé de l'émetteur Service Bus au développeur. |[Guide pratique pour Créer ou modifier un Namespace de Service Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) et [valeurs obtenir un nom de l’émetteur et la clé de l’émetteur](biztalk-issuer-name-issuer-key.md) |
| 5. |Développeur |Installer le Kit de développement logiciel (SDK) et créer le projet BizTalk Service dans Visual Studio. |[Installer le Kit de développement logiciel (SDK) Azure BizTalk Services](/previous-versions/azure/hh689760(v=azure.100)) et [Créer des points de terminaison de messagerie enrichis sur Azure](/previous-versions/azure/hh689766(v=azure.100)) |
| 6. |Développeur |Déployer votre projet BizTalk Service sur votre BizTalk Service hébergé sur Azure. |[Déploiement et actualisation du projet BizTalk Services](/previous-versions/azure/hh689881(v=azure.100)) |
| 7. |Administrateur |S'applique si vous utilisez EDI.  Vous pouvez ajouter des Partenaires et créer des Accords sur le portail Microsoft Azure BizTalk Services. Lorsque vous créez un accord, vous pouvez ajouter le pont et/ou les transformations créés par le développeur dans les paramètres de l'accord. |[Configuration d’EDI, AS2 et EDIFACT sur le portail BizTalk Services](/previous-versions/azure/hh689853(v=azure.100)) |
| 8. |Administrateur |Avec [REST](/previous-versions/azure/reference/dn232347(v=azure.100)), surveiller l’intégrité de votre service BizTalk, y compris les métriques de performances. |[BizTalk Services : Onglets tableau de bord, surveiller et mettre à l’échelle](https://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |Administrateur |À l'aide du portail Microsoft Azure BizTalk Services, gérer les artefacts utilisés par les Services BizTalk et suivre les messages lorsqu’ils sont traités par les fichiers de pont. |[Utilisation du portail BizTalk Services](/previous-versions/azure/dn874043(v=azure.100)) |
| 10. |Administrateur |Créer un plan de sauvegarde pour sauvegarder BizTalk Service. |[Continuité des activités et récupération d’urgence dans BizTalk Services](/previous-versions/azure/dn509557(v=azure.100)) |

## <a name="next-steps"></a>Étapes suivantes
[Didacticiels et exemples](/previous-versions/azure/hh689895(v=azure.100))

[Créer le projet dans Visual Studio](/previous-versions/azure/hh689811(v=azure.100))

[Installer le SDK des Services BizTalk Azure](/previous-versions/azure/hh689760(v=azure.100))

## <a name="concepts"></a>Concepts
[Créer le projet dans Visual Studio](/previous-versions/azure/hh689811(v=azure.100))  
[EDI, AS2 et messagerie EDIFACT (entreprise-entreprise)](/previous-versions/azure/hh689898(v=azure.100))  

## <a name="other-resources"></a>Autres ressources
[Ajouter une Source, Destination et pont de points de terminaison de messagerie](/previous-versions/azure/hh689877(v=azure.100))  
[En savoir plus et créer des tables des messages et transformations](/previous-versions/azure/hh689905(v=azure.100))  
[À l’aide de BizTalk Adapter Service (BAS)](/previous-versions/azure/hh689889(v=azure.100))  
[Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=303664)

