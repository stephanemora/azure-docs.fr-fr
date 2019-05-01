---
title: Restreindre le contenu Azure CDN par pays/région | Microsoft Docs
description: Découvrez comment restreindre l’accès par pays/région à votre contenu Azure CDN à l’aide de la fonctionnalité de filtrage géographique.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: magattus
ms.openlocfilehash: 083d8f66a73471548c812e27325e1ec69ad5c45c
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64869583"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Restreindre le contenu Azure CDN par pays/région

## <a name="overview"></a>Présentation
Par défaut, lorsqu’un utilisateur demande du contenu, ce dernier est fourni, quel que soit l’emplacement d’où vient la demande. Toutefois, dans certains cas, vous souhaiterez limiter l’accès à votre contenu par pays/région. Avec le *géofiltrage* fonctionnalité, vous pouvez créer des règles sur les chemins d’accès spécifiques sur votre point de terminaison CDN afin d’autoriser ou bloquer le contenu dans les pays/régions sélectionnés.

> [!IMPORTANT]
> Les profils **CDN Azure Standard fourni par Microsoft** ne prennent pas en charge le filtrage géographique basé sur le chemin d’accès.
> 

## <a name="standard-profiles"></a>Profils standard
Les procédures de cette section s’appliquent uniquement au profil **Azure CDN Standard fourni par Akamai** et au profil **Azure CDN Standard fourni par Verizon**. 

Pour le profil **Azure CDN Premium de Verizon**, vous devez utiliser le portail **Gérer** pour activer le filtrage géographique. Pour plus d’informations, consultez l’article [Profils Azure CDN Premium de Verizon](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Définir le chemin d’accès au répertoire
Pour accéder à la fonction de filtrage géographique, choisissez le point de terminaison CDN dans le portail, sélectionnez **Filtrage géographique** sous PARAMÈTRES dans le menu de gauche. 

![Filtrage géographique standard](./media/cdn-filtering/cdn-geo-filtering-standard.png)

Dans la zone **CHEMIN D’ACCÈS**, vous devez spécifier le chemin d’accès relatif à l’emplacement auquel les accès utilisateur sont autorisés ou refusés. 

Vous pouvez appliquer le filtrage géographique pour tous vos fichiers avec une barre oblique inversée (/) ou certains dossiers spécifiques en spécifiant les chemins d’accès (au répertoire */pictures/* par exemple). Vous pouvez également appliquer le filtrage géographique dans un fichier unique (par exemple, */pictures/city.png*). Plusieurs règles sont autorisées. Une fois que vous avez saisi une règle, une ligne vide s’affiche pour vous permettre d’entrer la règle suivante.

Par exemple, tous les filtres de chemin d’accès au répertoire suivants sont valides :   
*/*                                 
*/Photos/*     
*/Photos/Strasbourg/*     
*/Photos/Strasbourg/city.png*

### <a name="define-the-type-of-action"></a>Définir le type d’action

À partir de la liste **ACTION**, sélectionnez **Autoriser** ou **Bloquer** : 

- **Autoriser**: Seuls les utilisateurs des pays/régions spécifiées peuvent accéder aux ressources demandées à partir du chemin d’accès récursif.

- **Bloc**: Accès sont refusés aux utilisateurs dans les pays/régions spécifiées pour les ressources demandées à partir du chemin d’accès récursif. Si aucune autre option de filtrage des pays/région n’ont été configurées pour cet emplacement, puis tous les autres utilisateurs pourront accéder.

Par exemple, une règle de filtrage géographique pour le blocage du chemin d’accès */Photos/Strasbourg/* filtre les fichiers suivants :     
*http:\//\<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg*
*http:\//\<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>Définir les pays/régions
À partir de la **CODES de pays** , sélectionnez les pays/régions que vous souhaitez bloquer ou autoriser pour le chemin d’accès. 

Une fois que vous avez terminé de sélectionner les pays/régions, sélectionnez **enregistrer** pour activer la nouvelle règle de filtrage géographique. 

![Règles de filtrage géographique](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Supprimer des ressources
Pour supprimer une règle, sélectionnez-la dans la liste de la page **Filtrage géographique**, puis choisissez **Supprimer**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Profils Azure CDN Premium de Verizon
Pour les profils **Azure CDN Premium de Verizon**, l’interface utilisateur de création d’une règle de filtrage géographique est différente :

1. Dans le menu supérieur de votre profil CDN Azure, sélectionnez **Gérer**.

2. À partir du portail Verizon, sélectionnez **HTTP Large**, puis sélectionnez **Filtrage par pays**.

    ![Filtrage géographique standard](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Sélectionnez **Ajouter un filtre de pays**.

    La page **Étape 1 :** s’affiche.

4. Saisissez le chemin d’accès au répertoire, sélectionnez **Bloquer** ou **Ajouter**, puis **Suivant**.

    La page **Étape 2 :** s’affiche. 

5. Sélectionnez un ou plusieurs pays/régions dans la liste, puis sélectionnez **Terminer** pour activer la règle. 
    
    La nouvelle règle apparaît dans la table de la page **Filtrage par pays**.

    ![Règles de filtrage géographique](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Supprimer des ressources
Dans la table de règles du filtrage de pays/région, sélectionnez l’icône en regard d’une règle pour le supprimer ou l’icône de modification pour le modifier.

## <a name="considerations"></a>Considérations
* Les modifications apportées à la configuration du filtrage géographique ne sont pas appliquées immédiatement :
   * Pour les profils du **CDN Azure Standard fourni par Microsoft**, la propagation s’effectue généralement dans un délai de 10 minutes. 
   * Pour les profils du **CDN Azure Standard fourni par Akamai**, la propagation s’effectue généralement dans un délai d’une minute. 
   * Dans le cas des profils **Azure CDN Standard fourni par Verizon** et **Azure CDN Premium fourni par Verizon**, la propagation s’effectue généralement dans un délai de 10 minutes. 
 
* Cette fonctionnalité ne prend pas en charge les caractères génériques (par exemple, « * »).

* La configuration du filtrage géographique associée au chemin d’accès relatif est appliquée de manière récursive à ce chemin d’accès.

* Il ne peut y avoir qu’une seule règle appliquée à un même chemin d’accès relatif. Autrement dit, vous ne peut pas créer plusieurs filtres de pays/région qui pointent vers le même chemin d’accès relatif. Toutefois, étant donné que les filtres de pays/région sont récursifs, un dossier peut avoir plusieurs filtres de pays/région. En d’autres termes, un filtre de différents pays/région peut être affecté à un sous-dossier d’un dossier déjà configuré.

* La fonctionnalité de filtrage géographique utilise des codes de pays pour définir les pays/régions à partir de laquelle une demande est autorisée ou bloquée pour un répertoire sécurisé. Bien que les profils Akamai et Verizon prennent en charge de nombreux codes de pays communs, il existe quelques différences. Pour en savoir plus, voir [Codes de pays Azure CDN](/previous-versions/azure/mt761717(v=azure.100)). 

