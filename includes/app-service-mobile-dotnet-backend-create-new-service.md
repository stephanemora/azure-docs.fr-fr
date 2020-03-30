---
title: Fichier Include
description: Fichier Include
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 06/20/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 72a69359d412a7560472fbb73ec525ab5d4a4fce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67325793"
---
1. Connectez-vous au [Azure portal].

2. Cliquez sur **Créer une ressource**.

3. Dans la zone de recherche, tapez **application web**.
    
4. Dans la liste des résultats, sélectionnez **Application web** à partir de la Place de marché.

5. Sélectionnez votre **Abonnement** et votre **Groupe de ressources** (sélectionnez un groupe de ressources existant _ou_ créez-en un en utilisant le même nom que celui de votre application).

6. Choisissez un **Nom** unique à votre application web.

7. Choisissez l’option par défaut **Publier**, comme **Code**.

8. Dans la **Pile d’exécution**, vous devez sélectionner une version sous **ASP.NET** ou **Nœud**. Si vous générez un serveur principal .NET, sélectionnez une version sous ASP.NET. Autrement, si vous ciblez une application basée sur des nœuds, sélectionnez-en une de la version à partir de Nœud.

9. Sélectionnez le **Système d’exploitation** voulu : Linux ou Windows. 

10. Sélectionnez la **Région** où vous souhaitez que cette application soit déployée. 

11. Sélectionnez le **Plan App Service** qui convient et appuyez sur **Examiner et créer**. 

12. Dans **Groupe de ressources**, sélectionnez un groupe de ressources existant _ou_ créez-en un (en utilisant le même nom que votre application).

13. Cliquez sur **Créer**. Patientez quelques minutes jusqu’au déploiement du service, puis continuez. Regardez l’icône de Notifications (cloche) dans l’en-tête de portail à la recherche de mises à jour de l’état.

14. Une fois le déploiement terminé, cliquez sur la section **Détails du déploiement**, puis sur la Ressource de Type **Microsoft.Web/sites**. Vous accédez alors à l’application web App Service que vous venez de créer. 

15. Cliquez dans le panneau **Configuration** sous **Paramètres** et dans **Paramètres de l’application**, cliquez sur le bouton **Nouveau paramètre d’application**.

16. Dans la page **Ajouter/modifier le paramètre d’application**, entrez **MobileAppsManagement_EXTENSION_VERSION** comme **Nom**, et **La plus récente** comme Valeur, puis appuyez sur OK.

Vous êtes prêt à utiliser cette application web App Service nouvellement créée comme Application mobile.

<!-- URLs. -->
[Azure portal]: https://portal.azure.com/