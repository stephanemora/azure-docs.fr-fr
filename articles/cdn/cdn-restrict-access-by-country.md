---
title: Limiter l’accès à votre contenu CDN Azure par pays | Microsoft Docs
description: Découvrez comment limiter l’accès à votre contenu CDN Azure par le biais de la fonctionnalité de filtrage géographique.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: v-deasim
ms.openlocfilehash: 93321c4c8a7f8d79835d702ca07132eed94f6493
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260750"
---
# <a name="restrict-azure-cdn-content-by-country"></a>Limiter l’accès à votre contenu CDN Azure par pays

## <a name="overview"></a>Vue d'ensemble
Par défaut, lorsqu'un utilisateur demande du contenu, le contenu est pris en charge, quel que soit l’endroit d’où vient la demande. Dans certains cas, vous souhaiterez limiter l'accès à votre contenu par pays. Cet article explique comment utiliser la fonctionnalité de *filtrage géographique* pour configurer le service dans l’optique d’autoriser ou de bloquer l’accès par pays.

> [!IMPORTANT]
> Les produits CDN Azure fournissent tous la même fonctionnalité de filtrage géographique, mais avec une prise en charge des codes de pays légèrement différente. Pour plus d’informations, voir [Codes de pays Azure CDN](https://msdn.microsoft.com/library/mt761717.aspx).


Pour plus d’informations sur les considérations qui s’appliquent à la configuration de ce type de restriction, consultez [Considérations](cdn-restrict-access-by-country.md#considerations).  

![Filtrage par pays](./media/cdn-filtering/cdn-country-filtering-akamai.png)

## <a name="step-1-define-the-directory-path"></a>Étape 1 : définir le chemin d'accès
> [!IMPORTANT]
> Les profils **CDN Azure Standard fourni par Microsoft** ne prennent pas en charge le filtrage géographique basé sur le chemin d’accès.
>


Sélectionnez votre point de terminaison dans le portail et trouvez l’onglet Filtrage géographique dans le volet de navigation gauche pour trouver cette fonctionnalité.

Lorsque vous configurez un filtre de pays, vous devez spécifier le chemin d'accès relatif à l'emplacement auquel les accès utilisateurs sont autorisés ou refusés. Vous pouvez appliquer le filtrage géographique pour tous vos fichiers avec une barre oblique inversée (/) ou certains dossiers en spécifiant les chemins d’accès au répertoire */pictures/*. Vous pouvez également appliquer le filtrage géographique à un seul fichier en spécifiant le fichier et en omettant la barre oblique finale */pictures/city.png*.

Exemple de filtre de chemin d'accès au répertoire :

    /                                 
    /Photos/
    /Photos/Strasbourg/
      /Photos/Strasbourg/city.png

## <a name="step-2-define-the-action-block-or-allow"></a>Étape 2 : Définir l'action : autoriser ou bloquer
**Bloquer** : l’accès aux ressources demandées à partir de ce chemin d'accès récursif est bloqué aux utilisateurs des pays spécifiés. Si aucune autre option de filtrage par pays n'a été configurée pour cet emplacement, tous les autres utilisateurs sont autorisés à y accéder.

**Autoriser** : l’accès aux ressources demandées à partir de ce chemin d'accès récursif est autorisé aux seuls utilisateurs des pays spécifiés.

## <a name="step-3-define-the-countries"></a>Étape 3 : définir les pays
Sélectionnez les pays que vous souhaitez bloquer ou autoriser pour le chemin d'accès. 

Par exemple, la règle de blocage /Photos/Strasbourg/ filtre les fichiers, notamment :

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


### <a name="country-codes"></a>Codes de pays
La fonctionnalité de filtrage géographique utilise des codes de pays pour définir les pays à partir desquels une demande est autorisée ou bloquée pour un répertoire sécurisé. Bien que les produits CDN Azure fournissent tous la même fonctionnalité de filtrage géographique, on constate une légère différence de prise en charge des codes de pays. Pour plus d’informations, voir [Codes de pays Azure CDN](https://msdn.microsoft.com/library/mt761717.aspx). 

## <a name="considerations"></a>Considérations
* Les modifications apportées à votre configuration de filtrage par pays ne prennent pas effet immédiatement :
   * Pour les profils du **CDN Azure Standard fourni par Microsoft**, la propagation s’effectue généralement dans un délai de 10 minutes. 
   * Pour les profils du **CDN Azure Standard fourni par Akamai**, la propagation s’effectue généralement dans un délai d’une minute. 
   * Dans le cas des profils **Azure CDN Standard fourni par Verizon** et **Azure CDN Premium fourni par Verizon**, la propagation s’effectue généralement dans un délai de 10 minutes. 
 
* Cette fonctionnalité ne prend pas en charge les caractères génériques (par exemple, « * »).

* La configuration de filtrage géographique associée avec le chemin d'accès relatif de filtrage est appliquée de manière récursive à ce chemin d’accès.

* Il ne peut y avoir qu’une seule règle appliquée à un même chemin d’accès relatif. Autrement dit, il n’est pas possible de créer plusieurs filtres de pays qui pointent vers le même chemin d’accès relatif. Toutefois, un dossier peut comporter plusieurs filtres de pays, en raison de la nature récursive de ces derniers. En d'autres termes, un filtre par pays différent peut être attribué à un sous-dossier d'un dossier déjà configuré.

