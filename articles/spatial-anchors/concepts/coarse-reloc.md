---
title: Relocalisation grossière
description: Découvrez comment et quand utiliser la relocalisation grossière. La relocalisation grossière vous aide à trouver des ancres près de vous.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 3f0b04183c4df469d4f723486103790c4f97671b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656173"
---
# <a name="coarse-relocalization"></a>Relocalisation grossière

La relocalisation grossière est une fonctionnalité qui permet une localisation à grande échelle en fournissant une réponse approximative mais rapide aux questions suivantes : 
- *Où se trouve mon appareil maintenant ?* 
- *Quel contenu dois-je observer ?* 
 
La réponse n’est pas précise. Elle est donnée sous la forme : *Vous êtes à proximité de ces ancres. Essayez d’en localiser une*.

La relocalisation grossière fonctionne en marquant des points d’ancrage avec différents relevés de capteur sur l’appareil, utilisés ultérieurement pour accélérer la création de requêtes. Pour les scénarios d’extérieur, les données de capteur sont généralement la position GPS (Global Positioning System) de l’appareil. Lorsque le GPS n’est pas disponible ou n’est pas fiable, par exemple en intérieur, les données de capteur se composent des points d’accès Wi-Fi et des balises Bluetooth situées à portée. Les données de capteur collectées contribuent au maintien d’un index spatial qui est exploité par Azure Spatial Anchors pour déterminer rapidement les ancres situées près de votre appareil.

## <a name="when-to-use-coarse-relocalization"></a>Quand utiliser la relocalisation grossière

Si vous envisagez de gérer plus de 35 ancres spatiales dans un espace plus grand qu’un court de tennis, nous vous conseillons d’utiliser l’indexation spatiale de la relocalisation grossière.

La recherche rapide d’ancres permise par la relocalisation grossière vise à simplifier le développement d’applications grâce aux collections d’ancres, disons des millions d’ancres géodistribuées, à l’échelle mondiale. La complexité de l’indexation spatiale est totalement cachée, ce qui vous permet de vous concentrer davantage sur votre logique d’application. Le gros du travail est réalisé en coulisses par Azure Spatial Anchors.

## <a name="using-coarse-relocalization"></a>Utilisation de la relocalisation grossière

Voici le flux de travail typique pour créer et interroger Azure Spatial Anchors avec une relocalisation grossière :
1.  Créez et configurez un fournisseur de capteur d’empreintes digitales pour collecter les données de capteur de votre choix.
2.  Démarrez une session Azure Spatial Anchor et créez des ancres. Le capteur d’empreintes étant activé, les ancres sont indexées spatialement par la relocalisation grossière.
3.  Interrogez les ancres aux alentours à l’aide de la relocalisation grossière, en utilisant des critères de recherche dédiés dans la session Spatial Anchor.

Vous pouvez vous reporter à l’un de ces tutoriels pour configurer une relocalisation grossière dans votre application :
* [Relocalisation grossière en Unity](../how-tos/set-up-coarse-reloc-unity.md)
* [Relocalisation grossière en Objective-C](../how-tos/set-up-coarse-reloc-objc.md)
* [Relocalisation grossière en Swift](../how-tos/set-up-coarse-reloc-swift.md)
* [Relocalisation grossière en Java](../how-tos/set-up-coarse-reloc-java.md)
* [Relocalisation grossière en C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Relocalisation grossière en C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Capteurs et plateformes

### <a name="platform-availability"></a>Disponibilité de la plateforme

Vous pouvez envoyer ces types de données de capteur au service d’ancrage :

* Position GPS : latitude, longitude, altitude
* Force du signal des points d’accès Wi-Fi à portée
* Intensité du signal des balises Bluetooth à portée

Ce tableau résume la disponibilité des données de capteur sur les plateformes prises en charge et fournit des informations que vous devez connaître :

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | Non<sup>1</sup>  | Oui<sup>2</sup> | Oui<sup>3</sup> |
| **Wi-Fi**        | Oui<sup>4</sup> | Oui<sup>5</sup> | Non  |
| **Balises BLE** | Oui<sup>6</sup> | Oui<sup>6</sup> | Oui<sup>6</sup>|


<sup>1</sup> Un appareil GPS externe peut être associé à HoloLens. Contactez [notre support](../spatial-anchor-support.md) si vous souhaitez utiliser HoloLens avec un dispositif de suivi GPS.<br/>
<sup>2</sup> Prise en charge via les API [LocationManager][3] (GPS et réseau).<br/>
<sup>3</sup> Pris en charge via les API [CLLocationManager][4].<br/>
<sup>4</sup> Prise en charge à un débit d’environ une analyse toutes les 3 secondes. <br/>
<sup>5</sup> À partir du niveau d’API 28, les analyses Wi-Fi sont limitées à quatre appels toutes les 2 minutes. À compter d’Android 10, vous pouvez désactiver cette limitation à partir du menu **Paramètres du développeur**. Pour plus d’informations, consultez la [documentation Android][5].<br/>
<sup>6</sup> Limité à [Eddystone][1] et [iBeacon][2].

### <a name="which-sensor-to-enable"></a>Quel capteur activer

Le choix du capteur est spécifique à l’application que vous développez et à la plateforme.
Le diagramme suivant fournit un point de départ pour déterminer la combinaison de capteurs que vous pouvez activer en fonction du scénario de localisation :

![Diagramme illustrant les capteurs activés pour différents scénarios.](media/coarse-relocalization-enabling-sensors.png)

Les sections suivantes fournissent des informations supplémentaires sur les avantages et les limitations de chaque type de capteur.

### <a name="gps"></a>GPS

GPS est l’option qui convient aux scénarios en extérieur.
Lorsque vous utilisez le GPS dans votre application, gardez à l’esprit que les relevés fournis par le matériel sont généralement :

* Asynchrones et de faible fréquence (inférieurs à 1 Hz).
* Non fiables/bruyants (en moyenne déviation standard de 7 m).

En général, le système d’exploitation de l’appareil et Spatial Anchors effectuent des opérations de filtrage et d’extrapolation sur le signal GPS brut en vue d’atténuer ces problèmes. Ce traitement supplémentaire nécessite du temps pour la convergence. Par conséquent, pour obtenir les meilleurs résultats, vous devez essayer d’effectuer les opérations suivantes :

* Créez un fournisseur d’empreintes digitales du capteur le plus tôt possible dans votre application.
* Maintenez le fournisseur d’empreintes digitales du capteur actif entre plusieurs sessions.
* Partagez le fournisseur d’empreintes digitales du capteur entre plusieurs sessions.

Les appareils GPS grand public manquent généralement de précision. Une étude par [Zandenbergen et Barbeau (2011)][6] rapporte que la précision médiane des téléphones mobiles avec GPS assisté (A-GPS) est d’environ 7 mètres. C’est une marge trop importante pour être ignorée ! Pour tenir compte de ces erreurs de mesure, le service traite les ancres comme des distributions de probabilités dans l’espace GPS. Ainsi, une ancre se trouve désormais dans la zone spatiale qui a le plus de chance (avec un indice de confiance de plus de 95 %) de contenir sa véritable position GPS inconnue.

Le même raisonnement s’applique lorsque vous interrogez à l’aide du GPS. L’appareil est représenté sous la forme d’une autre région spatiale de confiance autour de sa véritable position GPS inconnue. La découverte des ancres situées à proximité se traduit par la simple recherche des ancres dont les régions de confiance sont *suffisamment proches* de la région de confiance de l’appareil, comme illustré ici :

![Diagramme illustrant la recherche d’ancres potentielles à l’aide du GPS.](media/coarse-reloc-gps-separation-distance.png)

### <a name="wi-fi"></a>Wi-Fi

Sur HoloLens et Android, la puissance du signal Wi-Fi peut être une bonne façon d’activer la relocalisation grossière en intérieur.
L’avantage est la disponibilité immédiate potentielle des points d’accès Wi-Fi (courants, par exemple, dans les bureaux ou les centres commerciaux), sans aucune configuration supplémentaire nécessaire.

> [!NOTE]
> iOS ne fournit pas d’API pour la lecture Wi-Fi de la force du signal. Vous ne pouvez donc pas l’utiliser pour la relocalisation grossière activée par Wi-Fi.

Lorsque vous utilisez le Wi-Fi dans votre application, gardez à l’esprit que les relevés fournis par le matériel sont généralement :

* Asynchrones et de faible fréquence (inférieurs à 0,1 Hz).
* Potentiellement limités au niveau du système d’exploitation.
* Non fiables/bruyants (en moyenne déviation standard de 3 dBm).

Spatial Anchors tente de générer une carte relative à la force du signal Wi-Fi filtrée pendant une session afin d’atténuer ces problèmes. Pour obtenir les meilleurs résultats, essayez de procéder ainsi :

* Créez la session avant de placer la première ancre.
* Maintenez la session active le plus longtemps possible. (Autrement dit, créez toutes les ancres et requêtes au cours d’une même session.)

### <a name="bluetooth-beacons"></a>Balises Bluetooth
<a name="beaconsDetails"></a>

Le déploiement minutieux de balises Bluetooth est une bonne solution pour les scénarios de relocalisation grossiste en intérieur à grande échelle, où le signal GPS est absent ou pas précis. C’est également la seule méthode en intérieur prise en charge sur les trois plateformes.

Les balises sont généralement des appareils polyvalents, où tout peut être configuré, y compris les UUID et les adresses MAC. Azure Spatial Anchors s’attend à ce que les balises soient identifiées de manière unique par leur UUID. Si vous n’assurez pas cette unicité, vous aurez probablement des résultats incorrects. Pour de meilleurs résultats :

* Affectez des UUID uniques à vos balises.
* Déployez les balises de manière à couvrir uniformément votre espace, et de sorte qu’au moins trois balises soient accessibles depuis n’importe quel point dans l’espace.
* Transmettez la liste des UUID de balise unique au fournisseur d’empreintes digitales du capteur.

Les signaux radio tels que Bluetooth sont affectés par les obstacles et peuvent interférer avec d’autres signaux radio. Il peut donc être difficile de déterminer si votre espace est uniformément couvert. Pour garantir une meilleure expérience client, nous vous recommandons de tester manuellement la couverture de vos balises. Vous pouvez procéder à un test en parcourant votre espace avec des appareils candidats et une application qui affiche le Bluetooth dans la plage. Lors du test de la couverture, assurez-vous que vous pouvez atteindre au moins trois balises depuis n’importe quelle position stratégique de votre espace. La configuration d’un trop grand nombre de balises peut entraîner des interférences plus nombreuses et n’améliorera pas nécessairement la précision de la relocalisation grossière.

Les balises Bluetooth offrent généralement une couverture de 80 mètres si aucun obstacle n’est présent dans l’espace.
Ainsi, pour un espace sans obstacles importants, il est possible de déployer des balises tous les 40 mètres sur un modèle de grille.

Une balise dont le niveau de batterie est faible affectera les résultats. Veillez donc à surveiller régulièrement votre déploiement pour identifier les batteries dont le niveau est faible ou qui sont épuisées.

Azure Spatial Anchors effectue uniquement le suivi des balises Bluetooth qui figurent dans la liste des UUID de proximité des balises connues. Mais les balises malveillantes programmées pour avoir des UUID figurant sur une liste verte peuvent avoir un impact négatif sur la qualité du service. Vous obtiendrez ainsi les meilleurs résultats dans des espaces organisés dans lesquels vous pouvez contrôler le déploiement des balises.

### <a name="sensor-accuracy"></a>Précision du capteur

La précision du signal GPS, aussi bien lors de la création de l’ancre que lors de l’interrogation, a une influence significative sur l’ensemble des ancres renvoyées. En revanche, les requêtes reposant sur le Wi-Fi/les balises prendront en compte toutes les ancres ayant au moins un point d’accès ou une balise en commun avec la requête. En ce sens, le résultat d’une requête basée sur le Wi-Fi/les balises est généralement déterminé par la plage physique des points d’accès/balises et les obstructions environnementales.
Ce tableau estime l’espace de recherche attendu pour chaque type de capteur :

| Capteur      | Rayon de l’espace de recherche (approx.) | Détails |
|-------------|:-------:|---------|
| **GPS**         | 20 à 30 m | Déterminé par l’incertitude GPS parmi d’autres facteurs. Les nombres signalés sont estimés pour l’exactitude GPS médiane des téléphones mobiles dotés d’un A-GPS, soit 7 mètres. |
| **Wi-Fi**        | 50 m à 100 m | Déterminé par la plage des points d’accès sans fil. Dépend de la fréquence, de la puissance de l’émetteur, des obstructions physiques, des interférences, etc. |
| **Balises BLE** |  70 m | Déterminé par la plage de la balise. Dépend de la fréquence, de la puissance de transmission, des obstructions physiques, des interférences, etc. |

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
