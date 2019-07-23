---
title: Diagnostic des problèmes de performances graphiques dans le Bureau à distance - Azure
description: Cet article décrit comment utiliser les compteurs graphiques RemoteFX dans les sessions de protocole Bureau à distance pour diagnostiquer les problèmes de performances liés aux graphiques dans Windows Virtual Desktop.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 05/23/2019
ms.author: v-chjenk
ms.openlocfilehash: 0b4113f1e0024415135aa99d1fb4e881efe448a3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66499263"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Diagnostic des problèmes de performances graphiques dans le Bureau à distance

Lorsque les performances du système ne sont pas celles attendues, il est important d’identifier la source du problème. Cet article permet d’identifier et de résoudre les goulots d’étranglement liés aux performances graphiques au cours de sessions RDP.

## <a name="find-your-remote-session-name"></a>Rechercher le nom de la session à distance

Vous aurez besoin du nom de la session à distance pour identifier les compteurs de performances graphiques. Suivez les instructions de cette section pour identifier le nom de votre session à distance Windows Virtual Desktop en préversion.

1. Ouvrez l’invite de commandes Windows à partir de votre session à distance.
2. Exécutez la commande **qwinsta**.
    - Si votre session est hébergée dans une machine virtuelle multisession : le suffixe de chaque nom de compteur est le même que celui du nom de votre session, par exemple « rdp-tcp 37 ».
    - Si votre session est hébergée dans une machine virtuelle prenant en charge des processeurs virtuels (vGPU) : Les compteurs sont stockés sur le serveur et non sur votre machine virtuelle. Les instances de compteur incluent le nom de la machine virtuelle au lieu du numéro dans le nom de la session, par exemple « Win8 Enterprise VM ».

>[!NOTE]
> Tandis que les compteurs comportent le terme « RemoteFX » dans leur nom, ils incluent également des graphiques de bureau à distance dans les scénarios vGPU.

## <a name="access-performance-counters"></a>Accéder aux compteurs de performances

Les compteurs de performances de RemoteFX Graphics permettent de détecter les goulots d’étranglement par le suivi des éléments, tels que la durée de codage et les images ignorées.

Une fois que vous avez déterminé le nom de votre session à distance, suivez ces instructions pour collecter les compteurs de performances RemoteFX Graphics de votre session à distance.

1. Sélectionnez **Démarrer** > **Outils d’administration** > **Analyseur de performances**.
2. Dans la boîte de dialogue **Analyseur de performances**, développez **Outils d’analyse**, sélectionnez **Analyseur de performances**, puis **Ajouter**.
3. Dans la boîte de dialogue **Ajouter des compteurs**, à partir de la liste **Compteurs disponibles**, développez l’objet compteur de performances pour RemoteFX Graphics.
4. Sélectionnez les compteurs à analyser.
5. Dans la liste **Instances de l’objet sélectionné**, sélectionnez les instances spécifiques à analyser pour les compteurs sélectionnés, puis sélectionnez **Ajouter**. Pour sélectionner toutes les instances de compteur disponibles, sélectionnez **Toutes les instances**.
6. Après avoir ajouté les compteurs, sélectionnez **OK**.

Les compteurs de performances sélectionnés s’affichent sur l’écran de l’Analyseur de performances.

>[!NOTE]
>Chaque session active sur un ordinateur hôte possède sa propre instance pour chaque compteur de performances.

## <a name="diagnosis"></a>Diagnostic

Les problèmes de performances graphiques se répartissent généralement en quatre catégories :

- Faible fréquence d’images
- Interruptions aléatoires
- Latence d’entrée élevée
- Qualité d’image médiocre

Commencez par traiter les problèmes liés à la faible fréquence d’images, aux interruptions aléatoires et à la latence d’entrée élevée. La section suivante vous indique quels compteurs de performance mesurent chaque catégorie.

### <a name="performance-counters"></a>Compteurs de performances

Cette section permet d’identifier les goulots d’étranglement.

Vérifiez tout d’abord le compteur Images de sortie/seconde. celui-ci mesure le nombre d’images accordées au client. Si cette valeur est inférieure au compteur Images d’entrée/seconde, les images sont ignorées. Pour identifier le goulot d’étranglement, utilisez les compteurs Images ignorées/seconde.

Il existe trois types de compteurs Images ignorées/seconde :

- Images ignorées/seconde (ressources réseau insuffisantes)
- Images ignorées/seconde (ressources client insuffisantes)
- Images ignorées/seconde (ressources serveur insuffisantes)

La valeur élevée d’un compteur Images ignorées/seconde signifie que le problème est lié à la ressource analysée par le compteur. Par exemple, si le client ne décode pas et ne présente pas les images à la vitesse à laquelle celles-ci sont fournies au serveur, le compteur Images ignorées/seconde (ressources client insuffisantes) sera élevé.

Si le compteur Images de sortie/seconde correspond au compteur Images d’entrée/seconde, bien qu’un temps de latence ou une interruption inhabituelle subsiste, le problème peut être lié à la durée moyenne de codage. Le codage est un processus synchrone qui se produit sur le serveur dans un scénario de session unique (vGPU) et sur la machine virtuelle dans un scénario multisession. La durée moyenne de codage doit être inférieure à 33 ms. Si tel est le cas et que des problèmes de performances subsistent, le problème peut être lié à l’application ou au système d’exploitation que vous utilisez.

Pour plus d’informations sur le diagnostic des problèmes liés aux applications, consultez [Compteurs de performances de retard d’entrée utilisateur](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters).

Étant donné que le protocole RDP (Remote Desktop Protocol) prend en charge une durée moyenne d’encodage de 33 ms, il prend en charge une fréquence d’images d’entrée pouvant atteindre 30 images/seconde. Notez que 33 ms est la fréquence d’images maximale prise en charge. Dans de nombreux cas, la fréquence d’images dont bénéficie l’utilisateur est inférieure, selon la fréquence à laquelle l’image est fournie au protocole RDP par la source. Par exemple, la lecture d’une vidéo nécessite une fréquence d’images d’entrée complète de 30 images/seconde, tandis que les tâches moins gourmandes en ressources, telles que la modification ponctuelle d’un document Word, ne nécessitent pas une telle fréquence d’images d’entrée par seconde pour une expérience utilisateur optimale.

Utilisez le compteur de qualité des images pour diagnostiquer les problèmes de qualité d’image. Ce compteur exprime la qualité de l’image de sortie sous forme de pourcentage de qualité de l’image source. La perte de qualité peut être due à RemoteFX, ou peut être inhérente à la source graphique. Si RemoteFX a provoqué une perte de qualité, cela peut être dû au fait que les ressources réseau ou serveur ne sont pas parvenues à envoyer un contenu de plus haute fidélité.

## <a name="mitigation"></a>Atténuation

Si les ressources serveur sont à du goulot d’étranglement, essayez l’une des opérations suivantes pour améliorer les performances :

- Réduisez le nombre de sessions par hôte.
- Augmentez la mémoire et les ressources de calcul sur le serveur.
- Supprimez la résolution de la connexion.

Si les ressources réseau sont à l’origine du goulot d’étranglement, essayez l’une des opérations suivantes pour améliorer la disponibilité du réseau par session :

- Réduisez le nombre de sessions par hôte.
- Supprimez la résolution de la connexion.
- Utilisez un réseau à bande passante supérieure.

Si les ressources serveur sont à l’origine du goulot d’étranglement, essayez l’une des opérations suivantes pour améliorer les performances :

- Installez le client Bureau à distance le plus récent.
- Augmentez la mémoire et les ressources de calcul sur la machine cliente.

> [!NOTE]
> Nous ne prenons pas en charge actuellement le compteur Images source/seconde. Pour l’instant, le compteur Images source/seconde reste défini sur 0.

## <a name="next-steps"></a>Étapes suivantes

- Pour créer une machine virtuelle Azure optimisée pour le GPU, consultez [Configurer l’accélération du GPU pour l’environnement Windows Virtual Desktop en préversion](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu).
- Pour découvrir une vue d’ensemble de la résolution des problèmes et des procédures d’escalade, consultez l’article [Vue d’ensemble du dépannage, commentaires et support](https://docs.microsoft.com/azure/virtual-desktop/troubleshoot-set-up-overview).
- Pour plus d’informations sur le service en préversion, voir [Environnement de bureau Windows en préversion](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
