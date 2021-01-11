---
title: Identifier les appareils nécessaires
description: En savoir plus sur les appliances matérielles et virtuelles pour les capteurs certifiés Defender pour IoT et la console de gestion locale.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/21/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: b22f880eee1b691b6b50b8151a64a2d501b891b3
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835679"
---
# <a name="identify-required-appliances"></a>Identifier les appareils nécessaires

Cet article fournit des informations sur les appliances de détection certifiées Defender pour IoT. Defender pour IoT peut être déployée sur des appliances physiques et virtuelles.

Cela comprend des appliances *préconfigurées* certifiées, sur lesquelles les logiciels sont déjà installés, ainsi que des appliances certifiées non configurées sur lesquelles vous pouvez télécharger et installer les logiciels requis.

L’article fournit également des spécifications pour une appliance de console de gestion locale. La console de gestion locale n’est pas disponible en tant qu’appliance préconfigurée.

- Si vous souhaitez acheter un capteur préconfiguré, passez en revue les modèles disponibles dans la section [Appliances de détection](#sensor-appliances), puis procédez à l’achat.

- Si vous souhaitez acheter votre propre appliance, passez en revue les modèles disponibles dans la section [Appliances de détection](#sensor-appliances) et dans la section [Autres appliances certifiées](#additional-certified-appliances). Une fois que vous avez acquis l’appliance, vous pouvez télécharger et installer le logiciel.

- Si vous souhaitez acheter la console de gestion locale, passez en revue les informations contenues dans la section [Appliance de console de gestion locale](#on-premises-management-console-appliance). Une fois que vous avez acquis l’appareil, vous pouvez télécharger et installer le logiciel.

Une fois que vous avez terminé les tâches décrites ici, vous pouvez installer le logiciel et configurer votre réseau.

## <a name="sensor-appliances"></a>Appliances de détection

Defender pour IoT prend en charge les déploiements physiques et virtuels.

### <a name="physical-sensors"></a>Capteurs physiques

Cette section fournit une vue d’ensemble des modèles de capteur physique disponibles. Vous pouvez acheter des capteurs avec des logiciels préconfigurés ou acheter des capteurs qui ne sont pas préconfigurés.

| Type de déploiement | Entreprise | Enterprise | SMB |
|--|--|--|--|
| Image | :::image type="content" source="media/how-to-prepare-your-network/corporate-hpe-proliant-dl360-v2.png" alt-text="Modèle de niveau société."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="Modèle de niveau entreprise."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="Modèle de niveau SMB."::: |
| Modèle | HPE ProLiant DL360 | HPE ProLiant DL20 | HPE ProLiant DL20 |
| Ports de surveillance | jusqu’à 15 RJ45 ou 8 OPT | jusqu’à 8 RJ45 ou 6 OPT | 4 RJ45 |
| Bande passante maximum[1](#anchortext) | 3 Go par seconde | 1 Go par seconde | 200 Mo par seconde |
| Nombre maximal d’appareils protégés | 30,000 | 15,000 | 1 000 |

Pour plus d’informations sur le fournisseur, consultez [Spécifications des appliances](#appliance-specifications).

À propos des capteurs préconfigurés : Microsoft s’est associé à Arrow pour fournir des capteurs préconfigurés. Pour acheter un capteur préconfiguré, contactez Arrow à l’adresse suivante : <hardware.sales@arrow.com>.

À propos de l’utilisation de votre propre appliance : Passez en revue les modèles pris en charge décrits ici. Une fois que vous avez acquis votre appliance, accédez à **Defender pour IoT** > **ISO des capteurs de réseau** > **Installation** pour télécharger le logiciel.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="ISO des capteurs de réseau.":::

<a id="anchortext">1</a> La capacité de bande passante peut varier, en fonction de la distribution des protocoles.

### <a name="virtual-sensors"></a>Capteurs virtuels

Cette section fournit une vue d’ensemble des capteurs virtuels qui sont disponibles.

| Type de déploiement | Entreprise | Enterprise | SMB |
|--|--|--|--|
| Bande passante maximum | 2,5 Go/s | 800 Mo/s | 160 Mo/s |
| Nombre maximal d’appareils protégés | 30,000 | 10 000 | 2 500 |

## <a name="on-premises-management-console-appliance"></a>Appliance de console de gestion locale

La console de gestion est disponible en tant que déploiement virtuel.

| Type de déploiement | Enterprise |
|--|--|
| Type d’appliance | Machine virtuelle HPE DL20 |
| Nombre de capteurs gérés | Jusqu’à 300 |

Une fois que vous avez acquis une console de gestion locale, accédez à **Defender pour IoT** > **Console de gestion locale** > **Installation ISO** pour télécharger le fichier ISO.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-iso-download-screen.png" alt-text="Console de gestion locale.":::

## <a name="appliance-specifications"></a>Spécifications des appliances

Cette section décrit les spécifications matérielles pour les appliances suivantes :

- Déploiement de société : HPE ProLiant DL360

- Déploiement d’entreprise : HPE ProLiant DL20

- Déploiement SMB : HPE ProLiant DL20

- Spécifications de l’appliance virtuelle

## <a name="corporate-deployment-hpe-proliant-dl360"></a>Déploiement de société : HPE ProLiant DL360

| Composant | Spécifications techniques |
|--|--|
| Châssis | Serveur rack 1U |
| Dimensions | 42,9 x 43,46 x 70,7 cm/1,69 x 17,11 x 27,83 po |
| Poids | 16,27 kg max (35,86 lb) |
| Processeur | Intel Xeon Silver 4215 R 3,2 GHz, 11M cache, 8c/16T, 130 W |
| Circuit microprogrammé | Intel C621 |
| Mémoire | 32 Go = 2 x DDR4 ECC UDIMM 16 Go 2 666 MT/s |
| Stockage | 6 x Disque dur enfichable à chaud 1,2 To SAS 12G Enterprise 10K SFF (2,5 po) – RAID 5 |
| Contrôleur de réseau | Intégré : 2 x Broadcom BCM5720 1 Go<br>Carte LOM intégrée : carte de port iDRAC 1 Go Broadcom BCM5720<br><br>Externe : 1 x Adaptateur serveur 1 Go Intel Ethernet i350 QP, profil bas |
| Gestion | Licence HPE iLO Advanced |
| Accès sur l’appareil | Deux ports USB 3.0 à l’arrière<br>Un USB 2.0 frontal<br>Un USB 3.0 interne |
| Power | 2 x Kit d’alimentation HPE platine, à logement flexible, enfichable à chaud, faible teneur en halogène, 500 W |
| Support du rack | Kit de glissières faciles à installer à petit facteur de forme HPE 1U de 10e génération |

### <a name="appliance-bom"></a>Nomenclature de l’appliance

| PN | Description | Quantité |
|--|--|--|
| P19766-B21 | Serveur HPE NC CTO DL360 8SFF de 10e génération | 1 |
| P19766-B21 | Europe : localisation multilingue | 1 |
| P24479-L21 | Kit Intel Xeon-S 4215 R FIO pour DL360 Gen10 | 1 |
| P24479-B21 | Kit Intel Xeon-S 4215 R pour DL360 de 10e génération | 1 |
| P00922-B21 | Module de mémoire HPE 16 Go 2Rx8 PC4-2933Y-R | 2 |
| 872479-B21 | HDD HPE 1,2 To SAS 10K SFF SC DS | 6 |
| 811546-B21 | Adaptateur HPE BASE-T I350 1 GbE 4 ports | 1 |
| P02377-B21 | Câble HPE Smart Hybrid Capacitor 145 mm | 1 |
| 804331-B21 | Contrôleur HPE Smart Array P408i-a SR de 10e génération | 1 |
| 665240-B21 | Adaptateur HPE FLR-T I350 1 GbE 4 ports | 1 |
| 871244-B21 | Kit de ventilateurs haute performance HPE DL360 de 10e génération | 1 |
| 865408-B21 | Kit d’alimentation HPE platine, à logement flexible, enfichable à chaud, faible teneur en halogène, 500 W | 2 |
| 512485-B21 | Licence pour HPE iLO Advanced 1 serveur avec 1 an d’assistance | 1 |
| 874543-B21 | Kit de glissières faciles à installer à petit facteur de forme HPE 1U de 10e génération | 1 |

## <a name="enterprise-deployment-hpe-proliant-dl20"></a>Déploiement d’entreprise : HPE ProLiant DL20

| Composant | Spécifications techniques |
|--|--|
| Châssis | Serveur rack 1U |
| Dimensions (hauteur x largeur x profondeur) | 4,32 x 43,46 x 38,22 cm/1,70 x 17,11 x 15,05 po |
| Poids | 7,9 kg/17,41 lb |
| Processeur | Intel Xeon E-2234, 3,6 GHz, 4C/8T, 71 W |
| Circuit microprogrammé | Intel C242 |
| Mémoire | 2 x Module de mémoire DDR4-2666 Dual Rank x8 16 Go |
| Stockage | 3 x SATA 1 To 6G Midline 7,2 K-SFF (2,5 po) – RAID 5 avec contrôleur Smart Array P408i-a SR |
| Contrôleur de réseau | Intégré : 2 x 1 Go <br>Intégré : carte de ports iLO 1 Go <br>Externe : 1 x Adaptateur HPE Ethernet 366FLR 1 Go 4 ports |
| Gestion | Licence HPE iLO Advanced |
| Accès sur l’appareil | Avant : 1 x USB 3.0, 1 x port USB du service iLO <br>Arrière : 2 x USB 3.0 <br>Interne : 1 x USB 3.0 |
| Power | Double alimentation enfichable à chaud 500 W |
| Support du rack | Kit de rail de friction pour armoire HPE 1U |

### <a name="appliance-bom"></a>Nomenclature de l’appliance

| PN | Description : haut de gamme | Quantité |
|--|--|--|
| P06963-B21 | Serveur CTO 4SFF HPE DL20 de 10e génération | 1 |
| P06963-B21 | Serveur CTO 4SFF HPE DL20 de 10e génération | 1 |
| P17104-L21 | Kit FIO E-2234 HPE DL20 de 10e génération | 1 |
| 879507-B21 | Kit STND HPE 16 Go 2Rx8 PC4-2666V-E | 2 |
| 655710-B21 | HDD SATA HPE 1 To 7,2 K SFF SC DS | 3 |
| P06667-B21 | Kit d’extension HPE DL20 de 10e génération x8x16 FlexibleLOM | 1 |
| 665240-B21 | Adaptateur HPE Ethernet 366FLR 1 Go 4 ports | 1 |
| 782961-B21 | Batterie HPE Smart Storage 12 W | 1 |
| 869081-B21 | Contrôleur LH modulaire HPE Smart Array P408i-a SR de 10e génération | 1 |
| 865408-B21 | Kit d’alimentation HPE platine, à logement flexible, enfichable à chaud, faible teneur en halogène, 500 W | 2 |
| 512485-B21 | Licence pour HPE iLO Advanced 1 serveur avec 1 an d’assistance | 1 |
| P06722-B21 | Kit RPS Enablement FIO HPE DL20 de 10e génération | 1 |
| 775612-B21 | Kit de rail de friction pour armoire HPE 1U | 1 |

## <a name="smb-deployment-hpe-proliant-dl20"></a>Déploiement SMB : HPE ProLiant DL20

| Composant | Spécifications techniques |
|--|--|
| Châssis | Serveur rack 1U |
| Dimensions (hauteur x largeur x profondeur) | 4,32 x 43,46 x 38,22 cm/1,70 x 17,11 x 15,05 po |
| Poids | 7,88 kg/17,37 lb |
| Processeur | Intel Xeon E-2224, 3,4 GHz, 4C, 71 W |
| Circuit microprogrammé | Intel C242 |
| Mémoire | 1 x Module de mémoire DDR4-2666 Dual Rank x8 8 Go |
| Stockage | 2 x SATA 1 To 6G Midline 7,2 K-SFF (2,5 po) – RAID 1 avec Smart Array P208i-a |
| Contrôleur de réseau | Intégré : 2 x 1 Go <br>Intégré : carte de ports iLO 1 Go <br>Externe : 1 x Adaptateur HPE Ethernet 366FLR 1 Go 4 ports |
| Gestion | Licence HPE iLO Advanced |
| Accès sur l’appareil | Avant : 1 x USB 3.0, 1 x port USB du service iLO <br>Arrière : 2 x USB 3.0 <br>Interne : 1 x USB 3.0 |
| Power | Alimentation enfichable à chaud 290 W |
| Support du rack | Kit de rail de friction pour armoire HPE 1U |

### <a name="appliance-bom"></a>Nomenclature de l’appliance

| PN | Description | Quantité |
|--|--|--|
| P06961-B21 | Serveur CTO 2LFF NHP HPE DL20 de 10e génération | 1 |
| P06961-B21 | Serveur CTO 2LFF NHP HPE DL20 de 10e génération | 1 |
| P17102-L21 | Kit FIO E-2224 HPE DL20 de 10e génération | 1 |
| 879505-B21 | Kit STND HPE 8 Go 1Rx8 PC4-2666V-E | 1 |
| 801882-B21 | HDD SATA HPE 1 To 7,2 K LFF RW | 2 |
| P06667-B21 | Kit d’extension HPE DL20 de 10e génération x8x16 FlexibleLOM | 1 |
| 665240-B21 | Adaptateur HPE Ethernet 366FLR 1 Go 4 ports | 1 |
| 869079-B21 | Contrôleur LH modulaire HPE Smart Array E208i-a SR de 10e génération | 1 |
| P21649-B21 | Kit d’alimentation FIO HPE DL20 de 10e génération platine 290 W | 1 |
| P06683-B21 | Kit de câbles AROC HPE DL20 de 10e génération M.2 SATA/LFF | 1 |
| 512485-B21 | Licence pour HPE iLO Advanced 1 serveur avec 1 an d’assistance | 1 |
| 775612-B21 | Kit de rail de friction pour armoire HPE 1U | 1 |

## <a name="virtual-appliance-specifications"></a>Spécifications de l’appliance virtuelle

### <a name="sensors"></a>Capteurs

| Type | Entreprise | Enterprise | SMB |
|--|--|--|--|
| Processeurs virtuels | 32 | 8 | 4 |
| Mémoire | 32 Go | 32 Go | 8 Go |
| Stockage | 5,6 To | 1,8 To | 500 Go |

### <a name="on-premises-management-console-appliance"></a>Appliance de console de gestion locale

| Type | Enterprise |
|--|--|
| Description | Appliance virtuelle pour les déploiements de type entreprise |
| Processeurs virtuels | 8 |
| Mémoire | 32 Go |
| Stockage | 1,8 To |

Hyperviseurs pris en charge : VMware ESXi 5.0 et versions ultérieures, Hyper-V

## <a name="additional-certified-appliances"></a>Autres appliances certifiées

Cette section détaille les appliances supplémentaires qui ont été certifiées par Microsoft, mais qui ne sont pas proposées en tant qu’appliances préconfigurées.

| Type de déploiement | Enterprise |
|--|--|
| Image | :::image type="content" source="media/how-to-prepare-your-network/deployment-type-enterprise-for-azure-defender-for-iot-v2.png" alt-text="Déploiement de type entreprise."::: |
| Modèle | Dell PowerEdge R340 XL |
| Ports de surveillance | Jusqu’à neuf RJ45 ou six OPT |
| Bande passante maximale [1](#anchortext2)| 1G Mb/sec |
| Nombre maximal d’appareils protégés | 10 000 |

<a id="anchortext2">1</a> La capacité de bande passante peut varier, en fonction de la distribution des protocoles.

Une fois que vous avez acheté l’appliance, accédez à **Defender pour IoT** > **ISO des capteurs de réseau** > **Installation** pour télécharger le logiciel.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="ISO des capteurs de réseau.":::

## <a name="enterprise-deployment-dell-poweredge-r340-xl"></a>Déploiement d’entreprise : Dell PowerEdge R340 XL

| Composant | Spécifications techniques |
|--|--|
| Châssis | Serveur rack 1U |
| Dimensions | 42,8 x 434 x 596 mm/1,67 x 17,09 x 23,5 po |
| Poids | Maximum 13,6 kg/29,98 lb |
| Processeur | Intel Xeon E-2144G 3,6 GHz, 8 Mo de cache, 4C/8T, Turbo (71 W) |
| Circuit microprogrammé | Intel C246 |
| Mémoire | 32 Go = 2 x DDR4 ECC UDIMM 16 Go 2 666 MT/s |
| Stockage | 3 x Disque dur SATA 2 To 7,2 K RPM 6 Gbits/s 512n 3,5 po enfichable à chaud – RAID 5 |
| Contrôleur de réseau | Intégré : 2 x Broadcom BCM5720 1 Go<br>Carte LOM intégrée : carte de port iDRAC 1 Go Broadcom BCM5720 <br><br>Externe : 1 x Adaptateur serveur 1 Go Intel Ethernet i350 QP, profil bas |
| Gestion | iDRAC9 Entreprise |
| Accès sur l’appareil | Deux ports USB 3.0 à l’arrière <br> Un USB 3.0 frontal |
| Power | Double alimentation enfichable à chaud 350 W |
| Support du rack | Rails coulissants ReadyRails II pour un montage sans outil dans des racks à quatre montants avec des trous carrés ou ronds non filetés ou pour un montage avec outil dans des racks à quatre montants avec des trous filetés, avec support pour le bras de gestion des câbles sans outil en option. |

## <a name="dell-r340-bom"></a>Nomenclature Dell R340

:::image type="content" source="media/how-to-prepare-your-network/enterprise-deployment-for-azure-defender-for-iot-dell-r340-bom.png" alt-text="Nomenclature Dell R340.":::

## <a name="smb-deployment-neousys-nuvo-5006lp"></a>Déploiement SMB : Neousys Nuvo-5006LP

| Composant | Spécifications techniques |
|--|--|
| Construction | Aluminium, conception sans ventilateur et étanche à la poussière |
| Dimensions | 240 mm (L) x 225 mm (P) x 77 mm (H) |
| Poids | 3,1 kg (y compris l’UC, la mémoire et le HDD) |
| UC | Intel Core i5-6500TE (6 Mo de cache, jusqu’à 3,30 GHz) S1151 |
| Circuit microprogrammé | Concentrateur de contrôleur de plateforme Intel Q170 |
| Mémoire | Module SODIMM 8 Go DDR4 2133 MHz à large plage de température |
| Stockage | SSD mSATA 128 Go 3ME3 à large plage de température |
| Contrôleur de réseau | 6 x ports Gigabit Ethernet par Intel I219 |
| Accès sur l’appareil | 4 ports USB : deux frontaux, deux arrières, un interne |
| Adaptateur secteur | 120/240VAC-20VDC/6A |
| Montage | Kit de montage, rail DIN |
| Température de fonctionnement | De \-25 °C à 70 °C |
| Température de stockage | De \-40 °C à 85 °C |
| Humidité | De 10 % à 90 % sans condensation |
| Vibration | En fonctionnement, 5 Grms, 5 à 500 Hz, 3 axes <br>(avec SSD, selon IEC60068-2-64) |
| Choc | En fonctionnement, 50 Grms, demi-sinusoïde de 11 ms (avec SSD, selon IEC60068-2-27) |
| CEM | CE/FCC Classe A, selon EN 55022, EN 55024 et EN 55032 |

## <a name="next-steps"></a>Étapes suivantes

[À propos de l’installation d’Azure Defender pour IoT](how-to-install-software.md)

[À propos de la configuration du réseau d’Azure Defender pour IoT](how-to-set-up-your-network.md)
