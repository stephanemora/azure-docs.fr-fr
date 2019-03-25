---
title: Accès, puissance et mode de connectivité des appareils Microsoft Azure Data Box Gateway | Microsoft Docs
description: Explique comment gérer l’accès, la puissance et le mode de connectivité de l’appareil Azure Data Box Gateway qui aide à transférer des données sur Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: d46d74544181a6b9cbfd049b2f5461b20b928483
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401443"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Gérer l’accès, de puissance et de mode de connectivité pour votre passerelle de zone de données Azure

Cet article explique comment gérer l’accès, la puissance et le mode de connectivité d’Azure Data Box Gateway. Ces opérations sont effectuées sur l’interface utilisateur web locale ou le Portail Azure.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Gérer l’accès aux appareils
> * Gérer le mode de connectivité
> * Gérer la puissance

## <a name="manage-device-access"></a>Gérer l’accès aux appareils

L’accès à votre périphérique de passerelle de zone de données est contrôlé par l’utilisation d’un mot de passe du périphérique. Vous pouvez modifier le mot de passe via l’interface utilisateur web locale. Vous pouvez également réinitialiser le mot de passe dans le portail Azure.

### <a name="change-device-password"></a>Modifier le mot de passe d’un appareil

Suivez ces étapes dans l’interface utilisateur locale pour modifier le mot de passe.

1. Sur l’interface utilisateur web locale, accédez à **Maintenance > Modification du mot de passe**.
2. Entrez le mot de passe actuel, puis le nouveau. Le mot de passe indiqué doit comporter entre 8 et 16 caractères, d’au moins trois types : majuscule, minuscule, chiffre et caractère spécial. Confirmez le nouveau mot de passe.

    ![Modifier le mot de passe](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Cliquez sur **Modifier le mot de passe**.
 
### <a name="reset-device-password"></a>Réinitialiser le mot de passe de l'appareil

Le workflow de réinitialisation n’implique pas de se rappeler l’ancien mot de passe, ce qui est utile en cas de perte. Il s’effectue sur le Portail Azure.

1. Sur le Portail Azure, accédez à **Vue d’ensemble > Réinitialiser le mot de passe d’administrateur**.

    ![Réinitialiser le mot de passe](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Entrez le nouveau mot de passe, puis confirmez-le. Le mot de passe indiqué doit comporter entre 8 et 16 caractères, d’au moins trois types : majuscule, minuscule, chiffre et caractère spécial. Cliquez sur **Réinitialiser**.

    ![Réinitialiser le mot de passe](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>Gérer le mode de connectivité

Mis à part le mode normal par défaut, l’appareil peut s’exécuter en mode déconnecté ou partiellement déconnecté :

- **Partiellement déconnecté** – Dans ce mode, l’appareil ne peut pas charger de données sur les partages. Il est en revanche possible de le gérer sur le Portail Azure.

    Ce mode est généralement utilisé sur un réseau satellite limité, dans l’objectif de réduire la consommation de bande passante réseau. Une consommation réseau minimale peut subsister pour les opérations de monitoring des appareils.

- **Déconnecté** – Dans ce mode, l’appareil est entièrement déconnecté du cloud ; les chargements et téléchargements cloud sont désactivés. L’appareil n’est gérable que sur l’interface utilisateur web locale.

    Ce mode est généralement utilisé pour mettre l’appareil hors connexion.

Pour modifier le mode de l’appareil, suivez ces étapes :

1. Sur l’interface utilisateur web locale de votre appareil, accédez à **Configuration > Paramètres cloud**.
2. Désactivez **Chargement et téléchargement cloud**.
3. Pour exécuter l’appareil en mode partiellement déconnecté, activez **Gestion sur le Portail Azure**.

    ![Mode de connectivité](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Pour exécuter l’appareil en mode déconnecté, désactivez **Gestion sur le Portail Azure**. L’appareil n’est plus gérable que sur l’interface utilisateur web locale.

    ![Mode de connectivité](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Gérer la puissance

Vous pouvez arrêter ou redémarrer votre appareil physique et virtuel sur l'interface utilisateur web locale. Avant de redémarrer, nous vous recommandons de mettre les partages hors connexion sur l’ordinateur hôte. Cette action réduit les risques d’altération des données.

1. Sur l’interface utilisateur web locale, accédez à **Maintenance > Paramètres d'alimentation**.
2. Cliquez sur **Arrêter** ou **Redémarrer** selon ce que vous voulez faire.

    ![Paramètres d’alimentation](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Lorsque la boîte de dialogue de confirmation apparaît, cliquez sur **Oui** pour continuer.

> [!NOTE]
> Si vous arrêtez l’appareil virtuel, vous devrez le démarrer par le biais de la gestion de l’hyperviseur.
