---
title: Liste de vérification pré-déploiement d’Azure Defender pour IoT
description: Cet article propose des informations et une liste de vérification à utiliser avant un déploiement lors de la préparation de votre site.
ms.date: 07/18/2021
ms.topic: checklist
ms.openlocfilehash: ac60e574a3d61bfa0c3106375d0606b7de6d9494
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114676929"
---
# <a name="pre-deployment-checklist-overview"></a>Présentation de la liste de vérification pour le prédéploiement

Cet article propose des informations et une liste de vérification à utiliser avant un déploiement lors de la préparation de votre site pour garantir une intégration réussie.

- Le capteur physique Defender pour IoT doit se connecter à des commutateurs gérés qui voient les communications industrielles entre les couches 1 et 2 (également la couche 3 dans certains cas).
- Le capteur écoute sur un port miroir commuté (port SPAN) ou un TAP.
- Le port de gestion est connecté au réseau d’entreprise à l’aide du protocole SSL.

## <a name="checklist"></a>Check-list

Grâce à une vue d’ensemble d’un diagramme de réseau industriel, les ingénieurs du site pourront définir l’emplacement approprié pour l’équipement Azure Defender pour IoT.

### <a name="1-global-network-diagram"></a>1. Diagramme de réseau global

Le diagramme du réseau global de l’environnement OT industriel

:::image type="content" source="media/resources-sensor-deployment-checklist/purdue-model.png" alt-text="C’est à cet endroit qu’intervient le capteur Azure Defender pour IoT, dans la configuration.":::

:::image type="content" source="media/resources-sensor-deployment-checklist/backbone-switch.png" alt-text="C’est à cet endroit qu’intervient le capteur Azure Defender pour IoT, dans le modèle Purdue.":::

> [!Note] 
> L’appliance Defender pour IoT doit être connectée à un commutateur de niveau inférieur qui voit le trafic entre les ports du commutateur. 

### <a name="2-committed-devices"></a>2. Appareils validés

Indiquez le nombre approximatif de périphériques réseau qui seront analysés. Vous aurez besoin de ces informations lors de l’intégration de votre abonnement au portail Azure Defender pour IoT. Pendant le processus d’intégration, vous serez invité à entrer le nombre d’appareils par incréments de 1000.

### <a name="3-optional-subnet-list"></a>3. (Facultatif) Liste des sous-réseaux 

Fournissez une liste de sous-réseaux des réseaux de production.

| **#** | **Nom du sous-réseau** | **Description** |
|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

### <a name="4-vlans"></a>4. VLAN

Fournissez une liste de VLAN des réseaux de production.

| **#** | **Nom du VLAN** | **Description** |
|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

### <a name="5-switch-models-and-mirroring-support"></a>5. Modèles de commutateurs et prise en charge de mise en miroir

Pour vérifier que les commutateurs ont la capacité de mettre en miroir les ports, indiquez les numéros de modèle des commutateurs auxquels la plateforme Defender pour IoT doit se connecter.

| **#** | **Switch** | **Modèle** | **Prise en charge de la mise en miroir du trafic (SPAN, RSPAN ou aucune)** |
|--|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

### <a name="6-third-party-switch-management"></a>6. Gestion des commutateurs tiers

Un tiers gère-t-il les commutateurs ? O ou N 

Si oui, qui ? __________________________________ 

Quelle est sa stratégie ? __________________________________ 

### <a name="7-serial-connection"></a>7. Connexion série

Existe-t-il des appareils qui communiquent par le biais d’une connexion série sur le réseau ? Oui ou Non 

Si oui, spécifiez le protocole de communication série : ________________ 

Si oui, indiquez sur le diagramme du réseau quels appareils communiquent avec les protocoles série, et où ils se trouvent.

*Ajoutez le diagramme de votre réseau avec l’indication des connexions série.*

### <a name="8-vendors-and-protocols-industrial-equipment"></a>8. Fournisseurs et protocoles (équipement industriel)

Fournir une liste des fournisseurs et des protocoles de l’équipement industriel. (facultatif)

| **#** | **Fournisseur** | **Protocole de communication** |
|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

Par exemple :

- Siemens

- Rockwell Automation : Ethernet ou IP

- Emerson : DeltaV, Ovation

### <a name="9-qos"></a>9. Qualité de service (QoS)

Pour la Qualité de service, le paramètre par défaut du capteur est de 1,5 Mbits/s. Spécifiez si vous souhaitez le modifier : ________________ 

   Unité commerciale : ________________

### <a name="10-sensor"></a>10. Capteur  

L’appliance de détection est connectée au port SPAN du commutateur via une carte réseau. Elle est connectée au réseau d’entreprise du client à des fins de gestion par le biais d’une autre carte réseau dédiée.

Fournissez des informations sur l’adresse de la carte réseau du capteur qui sera connectée au réseau d’entreprise : 

| Élément | Appliance 1 | Appliance 2 | Appliance 3 |
|--|--|--|--|
| Adresse IP de l’appliance |  |  |  |
| Subnet |  |  |  |
| Passerelle par défaut |  |  |  |
| DNS |  |  |  |
| Nom de l’hôte |  |  |  |

### <a name="11-idraciloserver-management"></a>11. iDRAC/iLO/gestion des serveurs

| Élément | Appliance 1 | Appliance 2 | Appliance 3 |
|--|--|--|--|
| Adresse IP de l’appliance |  |  |  |
| Subnet |  |  |  |
| Passerelle par défaut |  |  |  |
| DNS |  |  |  |

### <a name="12-on-premises-management-console"></a>12. Console de gestion locale  

| Élément | Actif | Passif (en cas d’utilisation de la haute disponibilité) |
|--|--|--|
| Adresse IP |  |  |
| Subnet |  |  |
| Passerelle par défaut |  |  |
| DNS |  |  |

### <a name="13-snmp"></a>13. SNMP  

| Élément | Détails |
|--|--|
| IP |  |
| Adresse IP |  |
| Nom d’utilisateur |  |
| Mot de passe |  |
| Type d'authentification | MD5 ou SHA |
| Chiffrement | DES ou AES |
| Clé secrète |  |
| Chaîne de communauté SNMP v2 |

### <a name="14-ssl-certificate"></a>14. Certificat SSL

Envisagez-vous d’utiliser un certificat SSL ? Oui ou Non

Si oui, quel service allez-vous utiliser pour le générer ? Quels attributs inclurez-vous dans le certificat (par exemple, domaine ou adresse IP) ?

### <a name="15-smtp-authentication"></a>15. Authentification SMTP

Envisagez-vous d’utiliser le protocole SMTP pour transférer des alertes à un serveur de messagerie ? Oui ou Non

Si oui, quelle est la méthode d’authentification que vous utiliserez ?  

### <a name="16-active-directory-or-local-users"></a>16. Active Directory ou utilisateurs locaux

Contactez un administrateur Active Directory pour créer un groupe d’utilisateurs du site Active Directory ou créez des utilisateurs locaux. Veillez à ce que vos utilisateurs soient prêts pour le jour du déploiement.

### <a name="17-iot-device-types-in-the-network"></a>17. Types d’appareils IoT dans le réseau

| Type d’appareil | Nombre d’appareils dans le réseau | Bande passante moyenne |
|--|--|--|
| Ex. Appareil photo |  |
| Ex. Appareil de radiographie |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |

## <a name="next-steps"></a>Étapes suivantes

[À propos de la configuration du réseau d’Azure Defender pour IoT](how-to-set-up-your-network.md)

[À propos de l’installation de Defender pour IoT](how-to-install-software.md)
