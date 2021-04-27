---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 04/14/2021
ms.openlocfilehash: a33a702128be0d8e90b3db635001dbfbdb279f2a
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575941"
---
|Type d’image  |Généralisée  |Spécialisée  |
|---------|---------|---------|
|Cible     |Déployée sur n’importe quel système.         | Ciblée sur un système spécifique.        |
|Installation après le démarrage     | Installation requise au premier démarrage de la machine virtuelle.          | Aucune configuration n’est requise. <br> La plateforme active la machine virtuelle.        |
|Configuration     |Nom d’hôte, utilisateur admin et autres paramètres spécifiques à la machine virtuelle obligatoires.         |Préconfigurée.         |
|Utilisée lors de     |Création de plusieurs nouvelles machines virtuelles à partir de la même image.         |Migration d’une machine spécifique ou restauration d’une machine virtuelle à partir d’une sauvegarde précédente.         |
