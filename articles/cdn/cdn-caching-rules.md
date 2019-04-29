---
title: Contrôler le comportement de mise en cache d’Azure CDN avec des règles de mise en cache | Microsoft Docs
description: Vous pouvez utiliser les règles de mise en cache CDN pour définir ou modifier le comportement d’expiration du cache par défaut, globalement et avec des conditions, telles qu’un chemin d’URL et des extensions de fichier.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: magattus
ms.openlocfilehash: 3a94b8252feb7c5c345d678579c477fce02d6e03
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679168"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Contrôler le comportement de mise en cache d’Azure CDN avec des règles de mise en cache

> [!NOTE] 
> Les règles de mise en cache sont disponibles uniquement pour les profils **CDN Azure Standard fourni par Verizon** et **CDN Azure Standard fourni par Akamai**. Pour les profils **CDN Azure Premium fourni par Verizon**, vous devez utiliser le [moteur de règles du CDN Azure](cdn-rules-engine.md) dans le portail **Gérer** pour une fonctionnalité similaire.
 
Azure Content Delivery Network (CDN) offre deux moyens de contrôler la façon dont les fichiers sont mis en cache : 

- Règles de mise en cache : Cet article décrit comment vous pouvez utiliser le réseau de distribution de contenu (CDN) mise en cache de règles pour définir ou modifier le comportement d’expiration de cache par défaut globalement et avec des conditions personnalisées, comme une extension de fichier et le chemin des URL. Azure CDN fournit deux types de règles de mise en cache :

   - Règles de mise en cache globales : Vous pouvez définir une règle de mise en cache globale pour chaque point de terminaison dans votre profil, ce qui affecte toutes les requêtes au point de terminaison. La règle de mise en cache globale se substitue à tous les en-têtes à directive de cache HTTP, s’ils sont définis.

   - Personnalisé les règles de mise en cache : Vous pouvez définir une ou plusieurs règles mise en cache personnalisées pour chaque point de terminaison dans votre profil. Les règles de mise en cache personnalisées correspondent à des chemins et extensions de fichier spécifiques, elles sont traitées dans l’ordre et remplacent la règle de mise en cache globale, si elle est définie. 

- Mise en cache de la chaîne de requête : Vous pouvez ajuster la manière dont Azure CDN traite la mise en cache pour les requêtes avec des chaînes de requête. Pour plus d’informations, consultez [Contrôler le comportement de mise en cache d’Azure CDN avec des chaînes de requête](cdn-query-string.md). Si le fichier ne peut pas être mis en cache, le paramètre de mise en cache des chaînes de requête n’a aucun effet, compte tenu des règles de mise en cache et des comportements CDN par défaut.

Pour plus d’informations sur le comportement de mise en cache par défaut et sur les en-têtes à directive de mise en cache, consultez [Fonctionnement de la mise en cache](cdn-how-caching-works.md). 


## <a name="accessing-azure-cdn-caching-rules"></a>Accès aux règles de mise en cache de CDN Azure

1. Ouvrez le portail Azure, sélectionnez un profil CDN, puis sélectionnez un point de terminaison.

2. Dans le volet gauche, sous Paramètres, sélectionnez **Règles de mise en cache**.

   ![Bouton Règles de mise en cache CDN](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   La page **Règles de mise en cache** s’affiche.

   ![Page des règles de mise en cache de CDN](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Paramètres du comportement de mise en cache
Pour les règles de mise en cache globales et personnalisées, vous pouvez spécifier les paramètres de **Comportement de mise en cache** suivants :

- **Ignorer le cache**: Ne pas mettre en cache et ignorer les en-têtes à directive de cache fournis à l’origine.

- **Remplacer**: Ignorer la durée du cache fournis à l’origine ; Utilisez la durée du cache fourni à la place. Cela ne remplace pas le cache-control : non-cache.

- **Définir des cas d’absence**: Respecte les en-têtes à directive de cache fournis à l’origine, s’ils existent ; Sinon, utilisez la durée du cache fourni.

![Règles de mise en cache générales](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Règles de mise en cache personnalisées](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Durée d’expiration du cache
Pour les règles de mise en cache globales et personnalisées, vous pouvez spécifier la durée d’expiration du cache en jours, heures, minutes et secondes :

- Pour les paramètres de **Comportement de mise en cache** **Remplacer** et **Définir en cas d’absence**, la plage des durées de cache valide est comprise entre 0 et 366 jours. Pour une valeur de 0 seconde, le CDN met en cache le contenu, mais doit revalider chaque requête avec le serveur d’origine.

- Pour le paramètre **Ignorer le cache**, la durée du cache est automatiquement définie sur 0 seconde et ne peut pas être modifiée.

## <a name="custom-caching-rules-match-conditions"></a>Conditions de correspondance des règles de mise en cache personnalisées

Pour les règles de cache personnalisées, deux conditions de correspondance sont disponibles :
 
- **Chemin d’accès** : Cette condition correspond au chemin d’accès de l’URL, à l’exclusion du nom de domaine et prend en charge le caractère générique (\*). Par exemple, _/myfile.html_, _/my/folder/*_ et _/my/images/*.jpg_. La longueur maximale est de 260 caractères.

- **Extension**: Cette condition correspond à l’extension de fichier du fichier demandé. Vous pouvez fournir une liste d’extensions de fichier séparées par des virgules pour la correspondance. Par exemple, _.jpg_, _.mp3_ ou _.png_. Le nombre maximal d’extensions est de 50 et le nombre maximal de caractères par extension est de 16. 

## <a name="global-and-custom-rule-processing-order"></a>Ordre de traitement des règles globales et personnalisées
Les règles de mise en cache globales et personnalisées sont traitées dans l’ordre suivant :

- Les règles de mise en cache globales sont prioritaires sur le comportement de mise en cache CDN par défaut (paramètres d’en-tête à directive de cache HTTP). 

- Les règles de mise en cache personnalisées sont prioritaires sur les règles de mise en cache globales, où elles s’appliquent. Les règles de mise en cache personnalisées sont traitées de haut en bas. Autrement dit, si une requête remplie ces deux conditions, les règles situées au bas de la liste sont prioritaires sur celles qui sont situées en haut. Par conséquent, vous devez placer les règles vraiment spécifiques plus bas dans la liste.

**Exemple**:
- Règles de mise en cache générales : 
   - Comportement de mise en cache : **Override**
   - Durée d’expiration du cache : 1 jour

- Règle no1 de mise en cache personnalisée :
   - Condition de correspondance : **Chemin d’accès**
   - Valeur de correspondance : _/home/*_
   - Comportement de mise en cache : **Override**
   - Durée d’expiration du cache : 2 jours

- Règle no2 de mise en cache personnalisée :
   - Condition de correspondance : **Extension**
   - Valeur de correspondance : _.html_
   - Comportement de mise en cache : **Définir si manquant**
   - Durée d’expiration du cache : 3 jours

Lorsque ces règles sont définies, une demande de  _&lt;nom d’hôte du point de terminaison&gt;_ déclencheurs.azureedge.net/home/index.html #2, qui a la valeur de règle de mise en cache personnalisée : **Définir des cas d’absence** et 3 jours. Par conséquent, si le fichier *index.html* est doté des en-têtes HTTP `Cache-Control` ou `Expires`, ils sont respectés ; si ces en-têtes ne sont pas définis, le fichier est mis en cache pendant trois jours.

> [!NOTE] 
> Les fichiers qui sont mis en cache avant une modification de règle conservent leur paramètre de durée de cache d’origine. Pour réinitialiser leur durée de cache, vous devez [vider le fichier](cdn-purge-endpoint.md). 
>
> La propagation sur le réseau des changements de configuration d’Azure CDN peut prendre un certain temps : 
> - Pour les profils du **CDN Azure Standard fourni par Akamai**, la propagation s’effectue généralement dans un délai d’une minute. 
> - Pour les profils du **CDN Azure Standard fourni par Verizon**, la propagation s’effectue généralement dans un délai de 10 minutes.  
>

## <a name="see-also"></a>Voir aussi

- [Comment fonctionne la mise en cache](cdn-how-caching-works.md)
- [Tutoriel : Définir des règles de mise en cache d’Azure CDN](cdn-caching-rules-tutorial.md)
