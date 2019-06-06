---
title: Diagnostic des problèmes de performances graphiques dans le Bureau à distance - Azure
description: Cet article décrit comment utiliser les compteurs graphiques RemoteFX dans les sessions de protocole Bureau à distance pour diagnostiquer les problèmes de performances avec des graphiques dans un bureau virtuel Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 05/23/2019
ms.author: v-chjenk
ms.openlocfilehash: 0b4113f1e0024415135aa99d1fb4e881efe448a3
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499263"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Diagnostiquer les problèmes de performances graphiques dans le Bureau à distance

Lorsque le système n’effectue pas comme prévu, il est important d’identifier la source du problème. Cet article vous aide à identifier et résoudre les goulots d’étranglement des performances graphiques liés au cours de sessions de protocole RDP (Remote Desktop).

## <a name="find-your-remote-session-name"></a>Le nom de votre session à distance

Vous aurez besoin de votre nom de session à distance pour identifier les compteurs de performances graphiques. Suivez les instructions dans cette section pour identifier le nom de votre session à distance de Windows Virtual Desktop Preview.

1. Ouvrez l’invite de commandes Windows à partir de votre session à distance.
2. Exécutez le **qwinsta** commande.
    - Si votre session est hébergée dans une machine virtuelle de session multiples (VM) : Le suffixe pour chaque nom de compteur est le même suffixe de nom de votre session, tels que « rdp-tcp 37. »
    - Si votre session est hébergée dans une machine virtuelle qui prend en charge virtuel Graphics unités de traitement (vGPU) : Les compteurs sont stockés sur le serveur et non sur votre machine virtuelle. Les instances du compteur incluent le nom de machine virtuelle au lieu du numéro dans le nom de la session, tels que « Win8 Enterprise machine virtuelle. »

>[!NOTE]
> Bien que les compteurs de RemoteFX dans leurs noms, ils incluent des graphiques de bureau à distance dans les scénarios de vGPU également.

## <a name="access-performance-counters"></a>Accéder aux compteurs de performances

Compteurs de performances dans les graphiques de RemoteFX vous aider à détecter les goulots d’étranglement en vous aidant à effectuer le suivi des éléments tels que frame encodage et ignorés cadres.

Une fois que vous avez déterminé le nom de votre session à distance, suivez ces instructions pour collecter les compteurs de performances graphiques de RemoteFX pour votre session à distance.

1. Sélectionnez **Démarrer** > **outils d’administration** > **Analyseur de performances**.
2. Dans le **Analyseur de performances** boîte de dialogue, développez **outils d’analyse**, sélectionnez **Analyseur de performances**, puis sélectionnez **ajouter**.
3. Dans le **ajouter des compteurs** boîte de dialogue, à partir de la **compteurs disponibles** liste, développez l’objet compteur de performances pour les graphiques de RemoteFX.
4. Sélectionnez les compteurs à surveiller.
5. Dans le **Instances de l’objet sélectionné** , sélectionnez les instances spécifiques à surveiller pour les compteurs sélectionnés, puis sélectionnez **ajouter**. Pour sélectionner toutes les instances de compteur disponibles, sélectionnez **toutes les instances**.
6. Après avoir ajouté les compteurs, sélectionnez **OK**.

Les compteurs de performances sélectionnés seront affiche sur l’écran de l’Analyseur de performances.

>[!NOTE]
>Chaque session active sur un ordinateur hôte possède sa propre instance de chaque compteur de performance.

## <a name="diagnosis"></a>Diagnostic

Problèmes de performances graphiques liés se répartissent généralement en quatre catégories :

- Fréquence d’images faible
- Blocages aléatoires
- Latence élevée d’entrée
- Qualité d’image médiocre

Commencez par adressage basse fréquence d’images, places aléatoire et une latence élevée d’entrée. La section suivante vous indique quels compteurs de performance mesurent chaque catégorie.

### <a name="performance-counters"></a>Compteurs de performances

Cette section vous permet d’identifier les goulots d’étranglement.

Vérifiez tout d’abord le compteur Frames/Second de sortie. Il mesure le nombre de frames accordé au client. Si cette valeur est inférieure au compteur d’entrée Frames/Second, cadres sont ignorées. Pour identifier le goulot d’étranglement, utilisez les Frames les compteurs ignorés par seconde.

Il existe trois types de cadres ignorés/seconde compteurs :

- Cadres ignorés par seconde (ressources réseau insuffisante)
- Cadres ignorés par seconde (ressources insuffisantes Client)
- Cadres ignorés par seconde (ressources serveur insuffisantes)

Une valeur élevée pour tous les cadres ignorés/seconde compteurs implique que le problème est lié à la ressource du compteur effectue le suivi. Par exemple, si le client ne décoder et cadres présents à la même vitesse, le serveur fournit les frames, le compteur de cadres ignorés par seconde (Client ressources insuffisantes) sera élevé.

Si le compteur Frames/Second de sortie correspond au compteur d’entrée Frames/Second, encore persistent lag inhabituel ou bloquées, le problème peut être le temps moyen de codage. Encodage est un processus synchrone qui se produit sur le serveur dans le scénario de session unique (vGPU) et sur la machine virtuelle dans le scénario de session multiples. Temps moyen de codage doit être sous 33 ms. Si le temps moyen de codage est sous 33 ms, mais vous avez toujours des problèmes de performances, il peut y avoir un problème avec l’application ou le système d’exploitation que vous utilisez.

Pour plus d’informations sur le diagnostic des problèmes liés à l’application, consultez [les compteurs de performances de délai d’entrée utilisateur](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters).

Étant donné que RDP prend en charge un temps moyen de codage de 33 ms, il prend en charge une fréquence d’images d’entrée jusqu'à 30 images/seconde. Notez que 33 ms est la fréquence d’images pris en charge maximale. Dans de nombreux cas, la fréquence d’images rencontrée par l’utilisateur sera inférieure, selon la fréquence à laquelle un frame est fourni pour le protocole RDP par la source. Par exemple, les tâches telles que regarder une vidéo nécessitent une fréquence d’images d’entrée de 30 images/seconde, mais moins de tâches nécessitant beaucoup de ressources rarement modification d’un document word ne nécessitent pas un tel taux de trames d’entrée par seconde pour une expérience utilisateur optimale.

Utilisez le compteur de qualité des images pour diagnostiquer les problèmes de qualité d’image. Ce compteur exprime la qualité de l’image de sortie sous forme de pourcentage de la qualité de l’image source. La perte de qualité peut être dû à RemoteFX, ou il peut être inhérente à la source de graphiques. Si RemoteFX a provoqué la perte de qualité, le problème peut être un manque de ressources réseau ou serveur d’envoyer le contenu d’une meilleure fidélité.

## <a name="mitigation"></a>Atténuation

Si les ressources serveur sont à l’origine le goulot d’étranglement, essayez l’une des opérations suivantes pour améliorer les performances :

- Réduisez le nombre de sessions par hôte.
- Augmentez la mémoire et des ressources sur le serveur de calcul.
- Supprimer la résolution de la connexion.

Si les ressources réseau sont à l’origine du goulot d’étranglement, essayez l’une des opérations suivantes pour améliorer la disponibilité du réseau par session :

- Réduisez le nombre de sessions par hôte.
- Supprimer la résolution de la connexion.
- Utilisez un réseau à bande passante supérieure.

Si les ressources client sont à l’origine du goulot d’étranglement, effectuez au moins des opérations suivantes pour améliorer les performances :

- Installer le client Bureau à distance la plus récent.
- Augmentez la mémoire et des ressources sur l’ordinateur client de calcul.

> [!NOTE]
> Nous n’actuellement en charge le compteur Source Frames/Second. Pour l’instant, le compteur Source Frames/Second sera toujours être défini sur 0.

## <a name="next-steps"></a>Étapes suivantes

- Pour créer une machine virtuelle GPU optimisé, consultez [configurer l’accélération graphique (GPU) pour l’environnement de version préliminaire de Windows Virtual Desktop de traitement graphique](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu).
- Pour une vue d’ensemble des pistes de résolution des problèmes et d’escalade de verrous, consultez [résolution des problèmes de vue d’ensemble, commentaires et support](https://docs.microsoft.com/azure/virtual-desktop/troubleshoot-set-up-overview).
- Pour en savoir plus sur le service en version préliminaire, consultez [environnement en version préliminaire de Windows Desktop](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
