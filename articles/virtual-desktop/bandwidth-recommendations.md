---
title: Recommandations en bande passante pour les sessions à distance - Azure
description: Recommandations en bande passante disponible pour les sessions à distance.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: helohr
ms.openlocfilehash: 8352858b9fd6059257b5ba7637822f85c56eb070
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802626"
---
# <a name="bandwidth-recommendations-for-remote-sessions"></a>Recommandations en bande passante pour les sessions à distance

Lors de l’utilisation d’une session à distance Windows, la bande passante disponible de votre réseau a un impact considérable sur la qualité de votre expérience. Différentes applications et résolutions d’affichage nécessitent des configurations réseau différentes. Dès lors, il est important de vous assurer que votre réseau est configuré en fonction de vos besoins.

>[!NOTE]
>Les recommandations suivantes s’appliquent aux réseaux présentant une perte inférieure à 0,1 %.

## <a name="applications"></a>APPLICATIONS

Le tableau suivant répertorie les bandes passantes minimales recommandées pour une expérience utilisateur fluide. 

|Charge de travail        |Exemples d'applications                                                                                           |Bande passante recommandée|
|----------------|--------------------------------------------------------------------------------------------------------------|---------------------|
|Collaborateur tâche     |Microsoft Word, Outlook, Excel, Adobe Reader                                                                  |1,5&nbsp;Mbit/s        |
|Employé de bureau   |Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Visionneuse de photos                                        |3&nbsp;Mbits/s          |
|Travailleur du savoir|Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Visionneuse de photos, Java                                  |5&nbsp;Mbits/s          |
|Collaborateur avancé    |Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Visionneuse de photos, Java, CAD/CAM, illustration/publication|15&nbsp;Mbits/s         |

>[!NOTE]
>Ces recommandations s’appliquent quel que soit le nombre d’utilisateurs de la session.

N'oubliez pas que la contrainte exercée sur votre réseau dépend de la fréquence d’images de sortie de la charge de travail de votre application et de votre résolution d’écran. Si la fréquence d’images ou la résolution d’affichage augmente, les besoins en bande passante augmenteront également. Par exemple, une charge de travail légère avec affichage haute résolution requiert plus de bande passante qu’une charge de travail légère avec résolution normale ou basse.

Les besoins en bande passante des autres scénarios peuvent varier selon votre utilisation, notamment :

- Conférences vocales ou vidéo
- Communication en temps réel
- Diffusion en continu de vidéo 4K

Assurez-vous de tester la charge de ces scénarios dans votre déploiement à l’aide d’outils de simulation tels que Login VSI. Faites varier la taille de la charge, réalisez des tests de contrainte et testez les scénarios utilisateur courants dans des sessions à distance pour mieux comprendre les besoins de votre réseau. 

## <a name="display-resolutions"></a>Résolutions d’affichage

Différentes résolutions d’affichage requièrent différentes bandes passantes. Le tableau suivant répertorie les bandes passantes recommandées pour une expérience utilisateur optimale à des résolutions d’écran standard, avec une fréquence de 30 images par seconde. Ces recommandations s’appliquent à des scénarios avec utilisateurs uniques et multiples. Gardez à l’esprit que les scénarios impliquant une fréquence d’images inférieure à 30 i/s, tels que la lecture de texte statique, requièrent moins de bande passante. 

|Résolutions d’affichage standard à 30 i/s    |Bande passante recommandée|
|-----------------------------------------|---------------------|
|Environ 1024 × 768 px                      |1,5 Mbit/s             |
|Environ 1280 × 720 px                      |3 Mbits/s               |
|Environ 1920 × 1080 px                     |5 Mbits/s               |
|Environ 3840 × 2160 px (4 Ko)                |15 Mbits/s              |

>[!NOTE]
>Ces recommandations s’appliquent quel que soit le nombre d’utilisateurs de la session.

## <a name="additional-resources"></a>Ressources supplémentaires

La région Azure dans laquelle vous vous trouvez peut autant affecter l’expérience utilisateur que les conditions réseau. Pour plus d'informations, consultez [Outil d’estimation de l’expérience Windows Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/assessment/).
