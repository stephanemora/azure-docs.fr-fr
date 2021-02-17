---
title: Fichier include
description: Fichier include
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 03/01/2020
ms.author: allensu
ms.custom: include file
ms.openlocfilehash: e1d4d29f8edca87ec1cca0ffced7b3e1bca90717
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808451"
---
## <a name="create-the-virtual-network-and-subnet"></a>Créer le réseau virtuel et le sous-réseau

Dans cette section, vous allez créer un réseau virtuel et un sous-réseau.

1. En haut à gauche de l’écran, sélectionnez **Créer une ressource > Réseau > Réseau virtuel**, ou recherchez **Réseau virtuel** à partir de la zone de recherche.

2. Dans **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes sous l’onglet **Général** :

    | **Paramètre**          | **Valeur**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Détails du projet**  |                                                                 |
    | Abonnement     | Sélectionner votre abonnement Azure                                  |
    | Groupe de ressources   | Sélectionnez **Créer**, entrez **\<resource-group-name>** , puis sélectionnez OK, ou sélectionnez un **\<resource-group-name>** existant en fonction de paramètres. |
    | **Détails de l’instance** |                                                                 |
    | Nom             | Entrez **\<virtual-network-name>**                                    |
    | Région           | Sélectionnez **\<region-name>** |

3. Sélectionnez l’onglet **Adresses IP**, ou sélectionnez le bouton **Suivant : Adresses IP** au bas de la page.

4. Sous l’onglet **Adresses IP**, entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Espace d’adressage IPv4 | Entrez **\<IPv4-address-space>** |

5. Sous **Nom de sous-réseau**, sélectionnez le mot **par défaut**.

6. Dans **Modifier le sous-réseau**, entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Nom du sous-réseau | Entrez **\<subnet-name>** |
    | Plage d’adresses de sous-réseau | Entrez **\<subnet-address-range>**

7. Sélectionnez **Enregistrer**.

8. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton **Vérifier + créer**.

9. Sélectionnez **Create** (Créer).