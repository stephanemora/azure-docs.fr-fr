---
title: Tutoriel - Configurer le routage du trafic prioritaire avec Azure Traffic Manager
description: Ce tutoriel explique comment configurer la méthode de routage du trafic prioritaire dans Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: rohink
ms.openlocfilehash: 2c541f8318bd7e75de7526e6b7a9e3a32462782d
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84706747"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Tutoriel : Configurer la méthode de routage du trafic prioritaire dans Traffic Manager

Indépendamment du mode de site web, Sites web Azure fournit des fonctionnalités basculement pour des sites web à l’intérieur d’un centre de données (également appelé région). Traffic Manager assure le basculement pour des sites web dans différents centres de données.

Dans le cadre du basculement des services, il est courant de diriger le trafic vers un service principal, et de définir un ensemble de services de sauvegarde identiques pour le basculement. Les étapes suivantes expliquent comment configurer ce basculement hiérarchisé avec Azure cloud services et des sites web :

## <a name="to-configure-the-priority-traffic-routing-method"></a>Pour configurer une méthode de routage de trafic prioritaire

1. Dans un navigateur, connectez-vous au [portail Azure](https://portal.azure.com). Si vous ne possédez pas encore de compte, vous pouvez [vous inscrire pour bénéficier d’un essai gratuit d’un mois](https://azure.microsoft.com/free/). 
2. Dans la barre de recherche du portail, recherchez les **profils Traffic Manager** et cliquez sur le nom du profil pour lequel vous souhaitez configurer la méthode de routage.
3. Dans le panneau **Profil Traffic Manager**, vérifiez que les services cloud et les sites web que vous souhaitez inclure dans votre configuration sont présents.
4. Dans la section **Paramètres**, cliquez sur **Configuration** et dans le panneau **Configuration**, procédez comme suit :
    1. Pour les **paramètres de la méthode de routage de trafic**, vérifiez que la méthode de routage du trafic est **Priorité**. Si ce n’est pas le cas, cliquez sur **Priorité** dans la liste déroulante.
    2. Définissez l’option **Paramètres de surveillance des points de terminaison** de manière identique pour tous les points de terminaison de ce profil comme suit :
        1. Sélectionnez le **protocole** approprié et spécifiez le numéro du **port**. 
        2. Pour **Chemin d’accès**, entrez une barre oblique */* . Pour surveiller les points de terminaison, vous devez indiquer un chemin et un nom de fichier. Une barre oblique (« / ») est une entrée valide pour le chemin d’accès relatif. Elle implique que le fichier se trouve dans le répertoire racine (par défaut).
        3. En haut de la page, cliquez sur **Enregistrer**.
5. Dans la section **Paramètres**, cliquez sur **Points de terminaison**.
6. Dans le panneau **Points de terminaison**, passez en revue l’ordre de priorité des points de terminaison. Lorsque vous configurez la méthode de routage du trafic par **Priorité**, l’ordre des points de terminaison sélectionnés est important. Vérifiez l’ordre de priorité des points de terminaison.  Le point de terminaison principal doit se trouver en haut de la liste. Vérifiez l’ordre affiché. Toutes les demandes seront acheminées vers le premier point de terminaison et si Traffic Manager détecte que celui-ci n’est pas intègre, le trafic bascule automatiquement vers le point de terminaison suivant. 
7. Pour modifier l’ordre de priorité des points de terminaison, cliquez sur un point de terminaison et dans le panneau **Point de terminaison** qui s’affiche, cliquez sur **Modifier** et modifiez la valeur **Priorité** en fonction de vos besoins. 
8. Cliquez sur **Enregistrer** pour enregistrer les modifications des paramètres des points de terminaison.
9. Une fois que vous avez terminé de modifier la configuration, cliquez sur **Enregistrer** en bas de la page.
10. Testez les modifications dans votre configuration comme suit :
    1.  Dans la barre de recherche du portail, recherchez le nom du profil Traffic Manager et cliquez sur le profil Traffic Manager dans les résultats affichés.
    2.  Dans le panneau du profil **Traffic Manager**, cliquez sur **Vue d’ensemble**.
    3.  Le panneau **Profil Traffic Manager** affiche le nom DNS de votre profil Traffic Manager nouvellement créé. Celui-ci peut être utilisé par tous les clients (par exemple, en y accédant à l’aide d’un navigateur web) pour être acheminés vers le point de terminaison correct, comme déterminé par le type de routage. Dans ce cas, toutes les demandes sont acheminées vers le premier point de terminaison et si Traffic Manager détecte que celui-ci n’est pas intègre, le trafic bascule automatiquement vers le point de terminaison suivant.
11. Une fois le profil Traffic Manager opérationnel, modifiez l’enregistrement DNS sur le serveur DNS faisant autorité, afin de faire pointer votre nom de domaine d’entreprise vers le nom de domaine Traffic Manager.

![Configuration de la méthode de routage du trafic prioritaire à l’aide de Traffic Manager][1]

## <a name="next-steps"></a>Étapes suivantes


- En savoir plus sur la [méthode de routage du trafic par pondération](traffic-manager-configure-weighted-routing-method.md).
- En savoir plus sur la [méthode de routage basé sur les performances](traffic-manager-configure-performance-routing-method.md).
- En savoir plus sur la [méthode de routage géographique](traffic-manager-configure-geographic-routing-method.md).
- Découvrez comment [tester les paramètres Traffic Manager](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png