---
title: Service Azure Reconnaissance de l’orateur
titleSuffix: Azure Cognitive Services
description: Le service Reconnaissance de l’orateur d’Azure Cognitive Services fournit des algorithmes qui vérifient et identifient les orateurs d’après leurs caractéristiques vocales propres. Le service Reconnaissance de l’orateur est utilisé pour répondre à la question « qui parle ? ».
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: trbye
ms.openlocfilehash: 2d4ce6f274efbd4d8afe2ac48856b0fc312f0a09
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261769"
---
# <a name="what-is-the-azure-speaker-recognition-service"></a>Qu’est-ce que le service Azure Reconnaissance de l’orateur ?

Le service Reconnaissance de l’orateur fournit des algorithmes qui vérifient et identifient les orateurs d’après leurs caractéristiques vocales propres. Le service Reconnaissance de l’orateur est utilisé pour répondre à la question « qui parle ? ». Vous fournissez des données d’entraînement audio pour un seul orateur, ce qui crée un profil d’inscription basé sur les caractéristiques uniques de la voix de l’orateur. Vous pouvez ensuite effectuer une vérification croisée des échantillons audio de la voix par rapport à ce profil pour vérifier que l’orateur est la même personne (vérification de l’orateur) ou par rapport à un *groupe* de profils d’orateur inscrits, pour voir s’ils correspondent à un profil du groupe (identification de l’orateur). En revanche, la [diarisation des orateurs](batch-transcription.md#speaker-separation-diarization) regroupe des segments audio par orateur dans une opération de traitement par lot.

## <a name="speaker-verification"></a>Vérification de l’orateur

La vérification de l’orateur simplifie le processus de vérification de l’identité d’un orateur inscrit avec des phrases secrètes ou une entrée vocale de forme libre. Elle peut servir à vérifier l’identité des individus pour assurer la sécurité et le bon déroulement des engagements clients dans une variété de solutions (de la vérification d’identité des clients dans les centres d’appels à l’accès aux installations sans contact).

### <a name="how-does-speaker-verification-work"></a>Comment fonctionne le service Vérification de l’orateur ?

![Fonctionnement du service Vérification de l’orateur](media/speaker-recognition/speaker-rec.png)

La vérification de l’orateur peut dépendre ou non du texte. La vérification **dépendante du texte** signifie que les orateurs doivent choisir une phrase secrète qu’ils utilisent dans les phases d’inscription et de vérification. La vérification **indépendante du texte** signifie que les orateurs peuvent utiliser le langage de tous les jours dans les phases d’inscription et de vérification.

Pour la vérification **dépendante du texte**, la voix de l’orateur est inscrite en prononçant une phrase secrète choisie dans un ensemble d’expressions prédéfinies. Les caractéristiques de la voix sont extraites de l’enregistrement audio pour constituer une signature vocale unique, activée dès que la phrase secrète choisie par l’utilisateur est reconnue. Conjointement, la signature vocale et la phrase secrète sont utilisées pour vérifier l’identité de l’orateur. 

La vérification **indépendante du texte** n’impose pas de restrictions sur ce que dit l’orateur pendant l’inscription ou dans l’échantillon audio à vérifier, car elle extrait uniquement les caractéristiques vocales pour attribuer un score de similarité. 

Les API ne permettent pas de déterminer si l’audio provient d’une personne en direct, ou s’il s’agit d’une imitation ou d’un enregistrement de la voix d’un utilisateur inscrit. 

## <a name="speaker-identification"></a>Identification de l’orateur

Identification de l’orateur est utilisé pour déterminer l’identité d’un orateur inconnu au sein d’un groupe d’orateurs inscrits. Identification de l’orateur vous permet d’attribuer un discours à des orateurs individuels et de déverrouiller la valeur dans des scénarios avec plusieurs orateurs, par exemple :

* Prendre en charge des solutions pour la productivité des réunions à distance 
* Créer une personnalisation sur les appareils multiutilisateurs

### <a name="how-does-speaker-identification-work"></a>Comment fonctionne le service Identification de l’orateur ?

L’inscription à l’identification de l’orateur est **indépendante du texte**, ce qui signifie qu’aucune restriction ne s’applique au texte prononcé par l’orateur dans l’audio. Tout comme pour la vérification de l’orateur, dans la phase d’inscription, la voix de l’orateur est enregistrée et ses caractéristiques vocales sont extraites pour former une signature vocale unique. Dans la phase d’identification, l’exemple de voix en entrée est comparé à une liste spécifiée de voix inscrites (jusqu’à 50 dans chaque demande).

## <a name="data-security-and-privacy"></a>Sécurité et confidentialité des données

Les données d’inscription de l’orateur sont stockées dans un système sécurisé, y compris le discours audio pour l’inscription et les caractéristiques de la signature vocale. Le discours audio pour l’inscription est utilisé seulement pendant la mise à niveau de l’algorithme, et les caractéristiques doivent être de nouveau extraites. Ce service ne conserve ni l’enregistrement vocal ni les caractéristiques vocales extraites qu’il envoie au service pendant la phase de reconnaissance. 

Vous contrôlez la durée pendant laquelle les données doivent être conservées. Vous pouvez créer, mettre à jour et supprimer les données d’inscription de chaque orateur en effectuant des appels d’API. Lorsqu’une inscription est supprimée, toutes les données d’inscription de l’utilisateur lui étant associées sont aussi supprimées. 

Comme avec toutes les ressources Cognitive Services, les développeurs utilisant le service Reconnaissance de l’orateur doivent connaître les politiques de Microsoft relatives aux données client. Vous devez vérifier que vous avez reçu les autorisations appropriées de la part des utilisateurs du service Reconnaissance de l’orateur. Pour plus d’informations, consultez la  [page Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)  dans le Centre de gestion de la confidentialité Microsoft. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> * Consultez le [tutoriel vidéo](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) sur la vérification de l’orateur indépendante du texte.
