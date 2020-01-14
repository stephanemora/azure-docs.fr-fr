---
title: Utiliser des révisions pour apporter des changements non cassants en toute confiance dans Gestion des API
titleSuffix: Azure API Management
description: Suivez les étapes de ce didacticiel pour apprendre à apporter des modifications sans rupture à l’aide de révisions dans Gestion des API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: c91d42f3445bb2ffc02366e6cb9ae49ec2db1be4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442748"
---
# <a name="use-revisions-to-make-non-breaking-changes-safely"></a>Utiliser des révisions pour apporter des modifications sans rupture en toute sécurité
Une fois que votre API est prête et commence à être utilisée par les développeurs, vous devez finir par lui apporter des modifications sans interrompre les appelants de l’API. Il est également utile d’informer les développeurs des modifications apportées. Les **révisions** le permettent dans Gestion des API Azure. Pour plus d’informations, consultez [Versions et révisions](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/) et [Gestion des versions d’API avec la Gestion des API Azure](https://blogs.msdn.microsoft.com/apimanagement/2017/09/13/api-versioning-with-azure-api-management/).

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Ajouter une révision
> * Apporter des modifications sans rupture à une révision
> * Rendre cette révision actuelle et ajouter une entrée au journal des modifications
> * Parcourir le portail des développeurs pour voir les modifications et le journal des modifications

![Journal des modifications sur le portail des développeurs](media/api-management-getstarted-revise-api/azure_portal.PNG)

## <a name="prerequisites"></a>Conditions préalables requises

+ Apprenez la [terminologie relative à Gestion des API Azure](api-management-terminology.md).
+ Suivez ce guide de démarrage rapide : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
+ Effectuez également toutes les étapes du tutoriel suivant : [Importer et publier votre première API](import-and-publish.md).

## <a name="add-a-new-revision"></a>Ajouter une révision

![Ajouter une révision de l’API](media/api-management-getstarted-revise-api/07-AddRevisions-01-AddNewRevision.png)

1. Sélectionnez la page **API**.
2. Dans la liste des API, sélectionnez **API de conférence de démonstration** (ou une autre API à laquelle vous souhaitez ajouter des révisions).
3. Cliquez sur l’onglet **Révisions** dans le menu situé en haut de la page.
4. Sélectionnez **+ Ajouter une révision**

    > [!TIP]
    > Vous pouvez également choisir **Ajouter une révision** dans le menu contextuel ( **...** ) de l’API.

5. Indiquez une description pour la nouvelle révision qui vous aidera à vous souvenir à quoi elle sert.
6. Sélectionnez **Créer**
7. Vous venez de créer une nouvelle révision.

    > [!NOTE]
    > Votre API d’origine reste dans **Revision 1**. Il s’agit de la révision que vos utilisateurs continuent à appeler, jusqu’à ce que vous choisissiez d’actualiser une autre révision.

## <a name="make-non-breaking-changes-to-your-revision"></a>Apporter des modifications sans rupture à une révision

![Modifier une révision](media/api-management-getstarted-revise-api/07-AddRevisions-02-MakeChanges.png)

1. Dans la liste des API, sélectionnez **API de conférence de démonstration**.
2. Sélectionnez l’onglet **Conception** situé près du haut de l’écran.
3. Notez que dans le **sélecteur de révision** (juste au-dessus de l’onglet Conception), **Revision 2** est sélectionné.

    > [!TIP]
    > Utilisez le sélecteur de révision pour basculer entre les révisions sur lesquelles vous souhaitez travailler.

4. Sélectionnez **+ Ajouter une opération**.
5. Définissez votre opération sur **POST**, puis attribuez au nom, au nom d’affichage et à l’URL de l’opération la valeur **test**.
6. **Enregistrez** votre nouvelle opération.
7. Nous avons modifié **Revision 2**. Utilisez le **sélecteur de révision** situé près du haut de la page pour revenir à **Revision 1**.
8. Notez que votre nouvelle opération n’apparaît pas dans **Revision 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Rendre cette révision actuelle et ajouter une entrée au journal des modifications

1. Sélectionnez l’onglet **Révisions** dans le menu situé en haut de la page.

    ![Menu de révision sur l’écran de révision.](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)

2. Ouvrez le menu contextuel ( **...** ) pour **Revision 2**.
3. Sélectionnez **Rendre actuel**.
4. Cochez **Publier sur le journal des modifications public de cette API**, si vous souhaitez publier les remarques relatives à cette modification. Fournissez une description de votre modification destinée aux développeurs, par exemple : **Test des révisions. Nouvelle opération « test » ajoutée.**
5. **Revision 2** est maintenant la révision actuelle.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Parcourir le portail des développeurs pour voir les modifications et le journal des modifications

1. Dans le Portail Azure, sélectionnez **API**.
2. Sélectionnez **Portail des développeurs** dans le menu supérieur.
3. Sélectionnez **API**, puis **API de conférence de démonstration**.
4. Notez que votre nouvelle opération **test** est désormais disponible.
5. Cliquez sur **Journal des modifications** en regard du nom de l’API.
6. Notez que l’entrée du journal des modifications apparaît dans cette liste.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Ajouter une révision
> * Apporter des modifications sans rupture à une révision
> * Rendre cette révision actuelle et ajouter une entrée au journal des modifications
> * Parcourir le portail des développeurs pour voir les modifications et le journal des modifications

Passez au tutoriel suivant :

> [!div class="nextstepaction"]
> [Publier plusieurs versions de votre API](api-management-get-started-publish-versions.md)
