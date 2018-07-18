---
title: Utilisation des balises dans Azure Content Moderator | Microsoft Docs
description: Content Moderator comprend des balises par défaut. Vous pouvez créer des balises personnalisées pour modérer du contenu spécifique à votre entreprise.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: add4c685c07c63944ae89f48a47ac78df28c1623
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367936"
---
# <a name="about-tags"></a>À propos des balises #

Il existe deux balises par défaut (a – isadult et r – isracy), mais vous pouvez créer des balises personnalisées pour effectuer des analyses plus ciblées. Par la suite, ces balises personnalisées sont à disposition des réviseurs qui peuvent les attribuer à des images ou du texte.

## <a name="create-tags"></a>Créer des balises ##

1.  Dans l’onglet Paramètres, sélectionnez Balises.

  ![Balises de modération du contenu](images/tags-1.png)

2.  Saisissez un code court à deux lettres pour la balise.
3.  Saisissez un nom pour la balise. Le nom doit être court et précis. Par exemple, « isNudity ».
4.  Saisissez une description.
5.  Cliquez sur Ajouter.
6.  Lorsque vous avez terminé la création des balises, cliquez sur Enregistrer.

![Définition des balises de modération du contenu](images/tags-2-define.png)

## <a name="using-custom-tags"></a>Utilisation des balises personnalisées ##

Les balises personnalisées sont utilisées au cours des révisions manuelles. Elles s’affichent sur l’aperçu et le réviseur peut cliquer pour les sélectionner.

![Utilisation des balises de modération du contenu](images/tags-3-use.png)

Vous pouvez désactiver les balises en fonction des différentes révisions, en cochant ou désélectionnant Est visible.
 
![Désactivation des balises de modération du contenu](images/tags-4-disable.png)

Vous ne pouvez pas supprimer les deux balises par défaut (isadult et isracy), mais vous pouvez supprimer toutes les balises personnalisées que vous avez définies. Cliquez sur la poubelle à côté de la balise que vous souhaitez supprimer.

![Suppression des balises de modération du contenu](images/tags-5-delete.png)

## <a name="next-steps"></a>Étapes suivantes ##

Pour savoir comment utiliser les balises pour la modération d’images, consultez la page [Révision d’images modérées](Review-Moderated-Images.md).
