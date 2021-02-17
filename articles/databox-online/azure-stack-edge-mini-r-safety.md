---
title: Guide de sécurité d’Azure Stack Edge Mini R | Microsoft Docs
description: Décrit les conventions, les instructions et les considérations de sécurité, et explique comment installer et utiliser en toute sécurité un appareil Azure Stack Edge Mini R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: alkohli
ms.openlocfilehash: aa363b1eeddff6c3b10d8e36371becb8b690697c
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981014"
---
# <a name="azure-stack-edge-mini-r-safety-instructions"></a>Instructions de sécurité pour Azure Stack Edge Mini R

![Icône d’avertissement 1](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![Icône Lire la notice de sécurité](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png)
 **LISEZ LES INFORMATIONS RELATIVES À LA SANTÉ ET À LA SÉCURITÉ**

Lisez toutes les informations de sécurité de cet article avant d’utiliser votre appareil Azure Stack Edge Mini R, composé d’une batterie, d’une alimentation secteur, d’un adaptateur d’alimentation de module et d’un module de serveur. Le non-respect des instructions peut entraîner un incendie ou une dégradation de vos biens et être à l’origine d’électrocutions et de blessures. Lisez toutes les informations de sécurité ci-dessous avant d’utiliser Azure Stack Edge Mini R.

## <a name="safety-icon-conventions"></a>Conventions des icônes de sécurité

Voici les termes signalétiques correspondant aux panneaux d’alerte de danger :

| Icône | Description |
|:--- |:--- |
| ![Symbole de danger](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)| **DANGER :** Signale une situation dangereuse qui, si elle n’est pas évitée, entraînera la mort ou des blessures graves. <br> **AVERTISSEMENT :** Signale une situation dangereuse qui, si elle n’est pas évitée, risque d’entraîner la mort ou des blessures graves. <br> **ATTENTION :** Signale une situation dangereuse qui, si elle n’est pas évitée, risque d’entraîner des blessures légères ou moyennement graves.|
|

Les icônes de danger suivantes doivent être observées lors de la configuration et de l’exécution de l’appareil Azure Stack Edge Mini R :

| Icône | Description |
|:--- |:--- |
| ![Lire toutes les instructions au préalable](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png) | Lire toutes les instructions au préalable |
| ![Icône Avis](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **AVIS :** | Signale des informations considérées comme importantes, mais non associées à un danger. |
| ![Symbole de danger](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) | Symbole de danger |
| ![Icône de choc électrique](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) | Risque d’électrocution |
| ![Usage intérieur uniquement](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) | Usage intérieur uniquement |
| ![Icône d'absence de pièces réparables par l'utilisateur](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) | Aucune pièce remplaçable par l’utilisateur. Accès réservé au personnel formé à cet effet. |
|

## <a name="handling-precautions-and-site-selection"></a>Précautions de manipulation et sélection du site

L’appareil Azure Stack Edge Mini R présente les précautions de manipulation et les critères de sélection du site suivants :

![Icône d’avertissement 2](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![Icône Risque d’électrocution](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
![Icône Aucune pièce réparable par l’utilisateur](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **ATTENTION :**

* Inspectez l’appareil *à réception* pour identifier les dommages éventuels. Si le boîtier de l’appareil est endommagé, [contactez le Support Microsoft](azure-stack-edge-placeholder.md) pour bénéficier d’un remplacement. N’essayez pas d’utiliser l’appareil.
* Si vous pensez que l’appareil est défectueux, [contactez le Support Microsoft](azure-stack-edge-placeholder.md) pour bénéficier d’un remplacement. N’essayez pas de réparer l’appareil.
* L’appareil ne contient aucune pièce remplaçable par l’utilisateur. L’intérieur de l’appareil présente des niveaux de tension, de courant et d’énergie dangereux. N’ouvrez pas l’appareil. Retournez l’appareil à Microsoft pour toute réparation.

![Icône d’avertissement 3](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **ATTENTION :**

Il est recommandé d’utiliser le système dans les conditions suivantes :

* Loin de toute source de chaleur, notamment du soleil et des radiateurs
* Dans des emplacements non exposés ni à l’humidité ni à la pluie
* Dans un espace dans lequel les vibrations et les chocs physiques sont limités.  Le système est conçu pour les chocs et les vibrations conformément à la norme MIL-STD-810G.
* Isolé des champs électromagnétiques forts produits par des appareils électriques.
* Ne laissez aucun liquide ni aucun objet étranger entrer dans le système. Ne placez pas de boissons ni autres récipients contenant des liquides sur le système ou à proximité.

![Icône d’avertissement 4](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![Icône Aucune pièce réparable par l’utilisateur](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **ATTENTION :**

* Cet équipement contient une batterie au lithium. N’essayez pas de réparer la batterie. Les batteries de cet équipement ne sont pas remplaçables par l’utilisateur. risque d’explosion si la batterie est remplacée par une batterie de type incorrect.

![Icône d’avertissement 5](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **ATTENTION :**

Ne chargez la batterie que lorsqu’elle est intégrée à l’appareil Azure Stack Edge Mini R. Ne la chargez pas comme s’il s’agissait d’un appareil distinct.

![Icône d’avertissement 6](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **ATTENTION :**

* Le commutateur marche/arrêt présent sur la batterie permet de décharger la batterie dans le module de serveur uniquement. Si l’adaptateur d’alimentation est raccordé à la batterie, l’alimentation est transmise au module de serveur même si le commutateur est en position arrêt.

![Icône d’avertissement 7](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **ATTENTION :**

* Ne brûlez pas la batterie. Ne la mettez pas en court-circuit. Elle doit être recyclée ou éliminée correctement.

![Icône d’avertissement 8](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **ATTENTION :**

* Ce système accepte également une batterie de type 2590 au lieu de l’alimentation secteur fournie. Dans ce cas, l’utilisateur final doit vérifier qu’elle respecte toutes les réglementations en matière de sécurité, de transport et de protection de l’environnement, ainsi que toute autre réglementation nationale/locale applicable.
* Lorsque vous utilisez le système avec une batterie de type 2590, respectez les conditions d’utilisation spécifiées par le fabricant de la batterie.

![Icône d’avertissement 9](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **ATTENTION :**

Cet appareil dispose de deux ports SFP+ qui peuvent être utilisés avec des transmetteurs optiques. Pour éviter des rayonnements laser dangereux, ne recourez qu’à des transmetteurs de classe 1.

## <a name="electrical-precautions"></a>Consignes de sécurité électrique

L’appareil Azure Stack Edge Mini R s’accompagne des précautions électriques suivantes :

![Icône d’avertissement 10](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)![Icône Risque d’électrocution](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **AVERTISSEMENT :**

En cas d’utilisation avec l’adaptateur d’alimentation :

* Fournissez un raccordement à la masse sûr pour le cordon d’alimentation. Le cordon de courant alternatif (CA) est doté d’une fiche de terre à trois fils (fiche présentant une broche de mise à la terre). Cette fiche convient uniquement aux prises secteur mises à la terre. Veillez à ne pas compromettre le rôle de la broche de mise à la terre.
* La fiche du cordon d’alimentation étant le moyen principal de débrancher l’alimentation, assurez-vous que les prises de courant sont situées à proximité de l’appareil et facilement accessibles.
* En présence de l’une des conditions suivantes, débranchez le ou les cordons d’alimentation (en tirant sur la fiche et non sur le cordon) et déconnectez tous les câbles :

  * Le cordon d’alimentation est effiloché ou la prise ou le cordon est endommagé.
  * L’appareil est exposé à la pluie, à une humidité excessive ou à d’autres liquides.
  * L’appareil est tombé et son boîtier a été endommagé.
  * Vous pensez que l’appareil doit faire l’objet d’une maintenance ou d’une réparation.
* Débranchez l’unité de façon permanente avant de la déplacer ou si vous pensez qu’elle a été endommagée d’une quelconque façon.

* Prévoyez une source d’alimentation appropriée offrant une protection contre les surcharges, conforme aux spécifications d’alimentation suivantes :

* Tension : 100 à 240 volts CA
* Intensité : 1,7 ampère
* Fréquence : 50 à 60 Hz

![Icône d’avertissement 11](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![Icône Risque d’électrocution](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **AVERTISSEMENT :**

* N’essayez pas de modifier ou d’utiliser des cordons d’alimentation AC autres que ceux fournis avec l’équipement.

![Icône d’avertissement 12](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![Icône Risque d’électrocution](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
![Usage intérieur uniquement](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) **AVERTISSEMENT :**

* L’alimentation portant ce symbole est conforme pour un usage en intérieur uniquement.

## <a name="regulatory-information"></a>Informations réglementaires

Cette rubrique contient des informations réglementaires sur l’appareil Azure Stack Edge Mini R, dont le numéro de modèle réglementaire est TMA01.

L’appareil Azure Stack Edge Mini R est conçu pour être utilisé avec un équipement de technologies de l’information répertorié par un laboratoire d’essai reconnu nationalement (UL, CSA, ETL, etc.) et conforme à la norme CEI/EN 60950-1 ou CEI/EN 62368-1 (marquage CE).

Dans les pays autres que les États-Unis et le Canada, les câbles réseau (non fournis avec l’équipement) de longueur supérieure à 3 mètres ne doivent pas être installés avec cet équipement.

L’équipement est conçu pour fonctionner dans les environnements suivants :

| Environnement | Spécifications |
|:---  |:--- |
| Spécifications relatives à la température du système | <ul><li>Température de stockage : −20 &deg;C à 50 &deg;C (−4 &deg;F à 122 &deg;F)</li><li>En régime continu : 0 &deg;C à 40 &deg;C (32 &deg;F à 104 &deg;F)</li></ul> |
| Spécifications relatives à l’humidité relative | <ul><li>Stockage : 5 % à 95 % d’humidité relative</li><li>En fonctionnement : 10 % à 90 % d’humidité relative</li></ul>|
| Spécifications relatives à l’altitude maximale | <ul><li>En fonctionnement : 15 000 pieds (4 572 mètres)</li><li>Hors fonctionnement : 40 000 pieds (12 192 mètres)</li></ul>|

> ![Icône Avis - 2](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **AVIS :** &nbsp;Les changements et modifications apportés à l’équipement non expressément approuvés par Microsoft sont susceptibles d’annuler l’autorisation de l’utilisateur à se servir de l’équipement.

#### <a name="canada-and-usa"></a>CANADA et ÉTATS-UNIS :

> ![Icône Avis - 3](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **AVIS :** &nbsp; Cet équipement a été testé et déclaré conforme aux limites applicables aux appareils numériques de classe A, selon la section 15 des règlements de la FCC. Ces limites sont conçues pour offrir une protection raisonnable contre les interférences nuisibles lorsque l’équipement est utilisé dans un environnement commercial. Cet appareil génère, utilise et peut émettre une énergie de fréquence radio. S’il n’est pas installé et utilisé conformément au manuel d’instructions, il peut être source d’interférences nuisibles pour les communications radio. L’utilisation de cet appareil dans une zone résidentielle est susceptible de causer des interférences nuisibles, auquel cas l’utilisateur sera tenu d’y remédier à ses propres frais.

L’adaptateur USB Wi-Fi Netgear A6150 fourni avec cet équipement est destiné à être utilisé à proximité du corps humain. Il est testé pour la conformité au débit d’absorption spécifique (DAS) en cas de port sur le corps. La limite SAR définie par la FCC est de 1,6 W/kg en moyenne sur 1 g de tissu. Lorsque vous transportez le produit ou que vous l’utilisez lorsqu’il est porté sur votre corps, conservez une distance de 10 mm avec le corps pour garantir la conformité aux exigences d’exposition RF.

L’adaptateur USB Wi-Fi Netgear A6150 est conforme à la norme ANSI/IEEE C95.1-1999 et a été testée conformément aux méthodes et procédures de mesure spécifiées dans le Bulletin 65, supplément C de l’OET.

Débit d’absorption spécifique (DAS) du Netgear A6150 : 1,18 W/kg en moyenne sur 1 g de tissu

L’adaptateur USB Wi-Fi Netgear A6150 est destiné à être utilisé uniquement avec les antennes approuvées. Cet appareil et ses antennes ne doivent pas être colocalisés ni fonctionner conjointement avec d’autres antennes ou émetteurs, sauf s’ils sont conformes aux procédures du produit multitransmetteur de le FCC. Pour les produits disponibles sur le marché américain, seul le canal 1~11 peut être utilisé. La sélection d’autres canaux est impossible.

Le fonctionnement sur la bande 5 150 – 5 250 MHz est réservée à l’usage intérieur pour réduire le risque d’interférences nuisibles avec les systèmes satellites mobiles cocanaux.

![Avertissement relatif aux informations réglementaires - usage intérieur](./media/azure-stack-edge-mini-r-safety/regulatory-information-indoor-use-only.png)


Les utilisateurs sont informés que des radars de grande capacité sont alloués en tant qu’utilisateurs principaux (utilisateurs prioritaires) des bandes 5 250 – 5 350 MHz et 5 650 – 5 850 MHz, et que ces radars peuvent générer des interférences et/ou des dégâts sur les appareils LE-LAN.

Cet appareil génère, utilise et peut émettre une énergie de fréquence radio. S’il n’est pas installé et utilisé conformément aux instructions, il peut être source d’interférences nuisibles pour les communications radio. Toutefois, il n’y a aucune garantie que les interférences ne se produiront pas dans une installation particulière.

Si cet équipement génère des interférences nuisibles avec la réception radio ou télévisée (à déterminer en mettant l’équipement hors tension et sous tension), l’utilisateur est encouragé à tenter de corriger l’interférence par une ou plusieurs des mesures suivantes :

- Réorienter ou déplacer l’antenne de réception.
- Augmenter la séparation entre l’équipement et le récepteur.
- Connecter l’équipement à une prise sur un circuit différent de celui auquel le récepteur est connecté.
- Pour obtenir de l’aide, consultez le vendeur ou un technicien radio/TV expérimenté.

Pour plus d’informations sur les problèmes d’interférence, accédez au site web de la FCC à l’adresse [fcc.gov/cgb/consumerfacts/interference.html](https://www.fcc.gov/consumers/guides/interference-radio-tv-and-telephone-signals). Vous pouvez également appeler la FCC au 1-888-APPELER la FCC pour demander les fiches d’informations sur les interférences et les interférences téléphoniques.

Vous trouverez des informations supplémentaires sur la sécurité des radiofréquences sur le site web de la FCC sur [https://www.fcc.gov/general/radio-frequency-safety-0](https://www.fcc.gov/general/radio-frequency-safety-0) et sur le site web d’Industrie Canada sur [http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html](http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html).

Ce produit a démontré la conformité d’EMC dans des conditions qui comprenaient l’utilisation de périphériques compatibles et de câbles protégés entre les composants du système. Il est important que vous utilisiez des périphériques conformes et des câbles protégés entre les composants du système pour réduire le risque d’interférence avec les radios, les téléviseurs et d’autres appareils électroniques.

Cet appareil est conforme à la section 15 des règlements de la FCC et aux normes RSS d’Industrie Canada relatives à une utilisation sans licence. L’utilisation de l’appareil est soumise aux deux conditions suivantes : (1) cet appareil ne peut pas provoquer d’interférences nuisibles et (2) cet appareil doit accepter toute interférence reçue, y compris les interférences pouvant engendrer un fonctionnement indésirable de l’appareil.

![Avertissement relatif aux informations réglementaires 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

CAN ICES-3(A)/NMB-3(A)

Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, États-Unis

États-Unis : (800) 426-9400

Canada : (800) 933 4750

ID FCC de l’adaptateur USB Wi-Fi Netgear A6150 : PY318300429
 
ID IC de l’adaptateur USB Wi-Fi Netgear A6150 : 4054A-18300429

L’adaptateur USB Wi-Fi Netgear A6150 fourni avec cet équipement est conforme au DAS pour les limites d’exposition de population générale/non contrôlée dans IC RSS-102 et a été testé conformément aux méthodes et procédures de mesure spécifiées dans IEEE 1528. Maintenez une distance d’au moins 10 mm en cas de port sur un corps.

L’adaptateur USB Wi-Fi Netgear A6150 est conforme à la limite d’exposition RF portable au Canada définie pour un environnement non contrôlé et sûr pour le fonctionnement prévu, comme décrit dans son manuel. Vous pouvez réduire davantage encore l’exposition RF en gardant le produit aussi loin que possible de votre corps ou en définissant l’appareil sur une puissance de sortie inférieure si ce type de fonction est disponible.

Une table avec un débit d’absorption spécifique (DAS) dont la moyenne est calculée sur 1 g pour chaque produit peut être consultée dans la section relative aux États-Unis ci-dessus.

![Avertissement relatif aux informations réglementaires 2](./media/azure-stack-edge-mini-r-safety/regulatory-information-2.png)

#### <a name="european-union"></a>UNION EUROPÉENNE :

Demandez une copie de la déclaration de conformité UE pour cet équipement.

L’adaptateur USB Wi-Fi Netgear A6150 fourni avec cet équipement est conforme à la directive 2014/53/UE et peut également être fourni à la demande.

> ![Icône d’avertissement 13](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) Il s’agit d’un produit de classe A. Dans un environnement domestique, ce produit peut provoquer des interférences radio, auquel cas l’utilisateur peut être tenu de prendre les mesures adéquates.

Élimination de batteries usagées et d’équipements électriques et électroniques :

![Icône Avertissement 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Ce symbole sur le produit, ses batteries ou son emballage signifie que ce produit et toute batterie qu’il peut contenir ne doivent pas être éliminés avec vos déchets ménagers. Il vous incombe de confier le recyclage des batteries et équipements électriques et électroniques à un point de collecte approprié. Ce processus de collecte et de recyclage spécifique contribue à préserver les ressources naturelles et à éviter les conséquences négatives potentielles sur la santé humaine et l’environnement que pourrait entraîner l’élimination inappropriée de batteries et d’équipements électriques et électroniques susceptibles de contenir des substances dangereuses. Pour plus d’informations sur les lieux où vous pouvez déposer vos batteries et déchets électriques et électroniques, contactez votre service municipal, votre service d’élimination des déchets ménagers ou le magasin dans lequel vous avez acheté ce produit. Contactez erecycle@microsoft.com pour obtenir des informations supplémentaires sur les déchets d’équipements électriques et électroniques.

Ce produit contient une ou plusieurs batteries boutons.

L’adaptateur USB Wi-Fi Netgear A6150 fourni avec cet équipement est destiné à être utilisé à proximité du corps humain. Il est testé pour la conformité au débit d’absorption spécifique (DAS) en cas de port sur le corps (voir les valeurs ci-dessous). Lorsque vous transportez le produit ou que vous l’utilisez lorsqu’il est porté sur votre corps, conservez une distance de 10 mm avec le corps pour garantir la conformité aux exigences d’exposition RF.

**Débit d’absorption spécifique (DAS) du Netgear A6150 :** 0,54 W/kg en moyenne sur 1 g de tissu

 
Cet appareil peut fonctionner dans tous les états membres de l’Union européenne. Observez les réglementations nationales et locales où l’appareil est utilisé. Cet appareil est limité à une utilisation en intérieur uniquement lorsqu’il fonctionne dans la plage de fréquences 5 150-5 350 MHz dans les pays suivants :  

![Pays de l’Union européenne qui nécessitent un usage intérieur uniquement](./media/azure-stack-edge-mini-r-safety/mini-r-safety-eu-indoor-use-only.png)

Conformément à l’article 10.8(a) et à l’article 10.8(b) du RED, le tableau suivant fournit des informations sur les bandes de fréquences utilisées et la puissance de transmission RF maximale des produits sans fil Netgear en vente au sein de l’Union européenne :

**WiFi**

| Plage de fréquences (MHz) | Canaux utilisés | Puissance de transmission max. (dBm/mW) |
| --------------------- | ------------- | --------------------------- |
| 2400-2483,5 | 1-13    | ODFM : 19,9 dBm (97,7 mW) <br> CCK : 17,9 dBm (61,7 mW) |
| 5 150-5 320   | 36-48   | 22,9 dBm (195 mW) |
| 5250-5350   | 52-64   | 22,9 dBm (195 mW) avec TPC <br> 19,9 dBm (97,7 mW) non TPC |
| 5470-5725   | 100-140 | 29,9 dBm (977 mW) avec TPC <br> 29,6 dBm (490 mW) non TPC |

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL

Numéro de téléphone : +353 1 295 3826

Numéro de fax : +353 1 706 4110

#### <a name="singapore"></a>SINGAPOUR :

L’adaptateur USB Wi-Fi Netgear A6150 fourni avec cet équipement est conforme aux normes IMDA.


## <a name="next-steps"></a>Étapes suivantes

- [Préparation du déploiement d’Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-prep.md)
