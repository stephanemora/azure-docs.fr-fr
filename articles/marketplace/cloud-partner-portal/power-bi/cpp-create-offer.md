---
title: Créer une offre d'application Power BI - Place de marché Azure | Microsoft Docs
description: Créez une offre d'application Power BI destinée à la Place de marché Microsoft AppSource.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: 6a4f7daa337618278c3652fad3053c20557a9e28
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62102048"
---
# <a name="create-a-power-bi-app-offer"></a>Créer une offre d'application Power BI

Cet article répertorie les étapes à suivre pour créer une offre d'application Power BI destinée à [AppSource](https://appsource.microsoft.com). Toutes les offres apparaissent en tant qu'entités distinctes dans AppSource. Lorsque vous créez une offre sur le [Portail Cloud Partner](https://cloudpartner.azure.com/), vous devez lui attribuer quatre groupes de ressources.

Les groupes de ressources sont décrits dans le tableau suivant :

|   Groupe de ressources      | Description                                                                         |
| ----------------   | ----------------                                                                    |
| Paramètres de l’offre     | Identifications principales et nom de l'offre.                                      |
| Informations techniques     | URL d'installation utilisée pour installer l'application dans l'espace de travail Power BI du client. Pour plus d'informations sur la création de cette URL, consultez la [documentation relative aux applications Power BI](https://go.microsoft.com/fwlink/?linkid=2028636). |
| Informations sur les vitrines | Contient des ressources marketing, juridiques et de gestion des prospects. Les ressources marketing incluent la description de l'offre et les logos. Les ressources juridiques incluent une politique de confidentialité, des conditions d'utilisation et d'autres documents juridiques. La stratégie de gestion des prospects vous permet de spécifier comment gérer les prospects à partir du portail des utilisateurs d'AppSource. |
| Contacts           | Contient les informations de contact et de stratégie du support.                                     |

## <a name="new-offer-form"></a>Formulaire de nouvelle offre

Après vous être connecté au Portail Cloud Partner, sélectionnez **Nouvelle offre** dans le volet de gauche. Sélectionnez ensuite **Applications Power BI** pour afficher le formulaire Nouvelle offre et lancer le processus de définition des ressources d'une nouvelle offre d'application.

![Élément du menu Offre Power BI](./media/new-offer-menu.png)

> [!NOTE] 
> Si l'option **Applications Power BI** n'apparaît pas ou n'est pas activée, votre compte n'est pas autorisé à créer ce type d'offre. Vérifiez que vous remplissez toutes les [conditions préalables](./cpp-prerequisites.md) pour ce type d’offre, notamment l'inscription d'un compte de développeur.


## <a name="next-steps"></a>Étapes suivantes

Les articles suivants décrivent les onglets de la page **Nouvelle offre** pour un type d'offre d'application Power BI. Chaque article décrit les groupes de ressources et les services de support correspondant à votre nouvelle offre d'application Power BI.

-  [Onglet des paramètres de l’offre](./cpp-offer-settings-tab.md)
-  [Onglet des informations techniques](./cpp-technical-info-tab.md)
-  [Onglet des informations sur les vitrines](./cpp-storefront-details-tab.md)
-  [Onglet Contacts](./cpp-contacts-tab.md)
