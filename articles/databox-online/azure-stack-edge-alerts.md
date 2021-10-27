---
title: Examiner les alertes sur Azure Stack Edge
description: Décrit les alertes qui se produisent sur les appareils Azure Stack Edge.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/14/2021
ms.author: alkohli
ms.openlocfilehash: 6db661698c0e7ee2ca19cd7ef17a5397b174667d
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130047990"
---
# <a name="review-alerts-on-azure-stack-edge"></a>Examiner les alertes sur Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique comment afficher les alertes et interpréter la gravité des alertes pour les événements sur vos appareils Azure Stack Edge. Les alertes génèrent des notifications dans le portail Azure. L’article comprend une référence rapide pour les alertes Azure Stack Edge.

## <a name="overview"></a>Vue d’ensemble

Le panneau alertes pour un appareil Azure Stack Edge vous permet de consulter les alertes relatives aux appareils Azure Stack Edge en temps réel. Dans ce panneau, vous pouvez surveiller de manière centralisée les problèmes d’intégrité de vos appareils Azure Stack Edge et l’ensemble de la solution Microsoft Azure Stack Edge.

L’affichage initial est une synthèse générale des alertes de chaque niveau de gravité. Vous pouvez descendre dans la hiérarchie pour voir les alertes à chaque niveau de gravité.

[ ![Capture d’écran du panneau Alertes pour un appareil Azure Stack Edge dans le portail Azure. Les détails de l’alerte sont superposés. L’élément de menu Alertes est en surbrillance.](media/azure-stack-edge-alerts/azure-stack-edge-alerts-summary-and-detail.png) ](media/azure-stack-edge-alerts/azure-stack-edge-alerts-summary-and-detail.png#lightbox)


### <a name="alert-severity-levels"></a>Niveaux de gravité des alertes

Les alertes sont associées à différents niveaux de gravité qui varient selon l’impact de la situation ayant généré l’alerte et la nécessité d’une intervention. Les niveaux de gravité des alertes sont :

- **Critique** : cette alerte répond à une condition qui affecte les performances optimales de votre système. Il est nécessaire d’agir pour vous assurer que le service Azure Stack Edge n’est pas interrompu.
- **Avertissement** : cette condition peut devenir critique si elle n’est pas résolue. Vous devez examiner la situation et prendre les dispositions nécessaires pour résoudre le problème.
- **Information** : cette alerte contient des informations qui peuvent être utiles pour le suivi et la gestion de votre système.

### <a name="configure-alert-notifications"></a>Configurer des notifications d’alerte

Vous pouvez également envoyer des notifications d’alerte par courrier électronique pour les événements sur vos appareils Azure Stack Edge. Pour gérer ces notifications d’alerte, vous créez des règles d’action. Ces règles d’action peuvent déclencher ou supprimer des notifications d’alerte pour les événements au sein d’un groupe de ressources, d’un abonnement Azure ou sur un appareil. Pour plus d’informations, consultez [Utilisation de règles d’action pour gérer les notifications d’alerte](azure-stack-edge-gpu-manage-device-event-alert-notifications.md).

## <a name="alerts-quick-reference"></a>Référence rapide des alertes

Les tableaux suivants répertorient certaines des alertes Azure Stack Edge que vous pouvez rencontrer, avec des descriptions et des actions recommandées. Les alertes sont regroupées dans les catégories suivantes :

* [Alertes de connectivité au cloud](#cloud-connectivity-alerts)
* [Alertes Edge Computing](#edge-compute-alerts)
* [Alertes Azure Resource Manager local](#local-azure-resource-manager-arm-alerts)
* [Alertes de performances](#performance-alerts)
* [Alertes de stockage](#storage-alerts)
* [Alertes de sécurité](#security-alerts)
* [Alertes Key Vault](#key-vault-alerts)
* [Alertes de matériel](#hardware-alerts)
* [Alertes de mise à jour](#update-alerts)
* [Alertes de machine virtuelle](#virtual-machine-alerts)

> [!NOTE]
> Dans les tableaux d’alertes ci-dessous, certaines alertes sont déclenchées par plusieurs types d’événements. Si les événements ont différentes actions recommandées, la table comporte une entrée d’alerte pour chacun des événements.

## <a name="cloud-connectivity-alerts"></a>Alertes de connectivité au cloud

Les alertes suivantes sont déclenchées par l’échec d’une connexion à un appareil Azure Stack Edge ou lorsqu’aucune pulsation n’est détectée.

| Texte d'alerte                       | Gravité | Description/action recommandée |
|----------------------------------|----------|----------------------------------|
| Connexion à Azure impossible.  | Critique | Vérifiez votre connexion Internet. Dans l’interface utilisateur locale de l’appareil, accédez à **Dépannage** > **Tests de diagnostic**. Lancez le test de diagnostic **Connectivité Internet**. |
| Pulsation de l’appareil perdue. | Critique | Si votre appareil est hors ligne, il ne peut pas communiquer avec le service Azure. Cela peut être dû à l’une des raisons suivantes :<ul><li>La connectivité Internet est interrompue.<br>Vérifiez votre connexion Internet. Dans l’interface utilisateur locale de l’appareil, accédez à **Dépannage** > **Tests de diagnostic**. Exécutez les tests de diagnostic. Résolvez les problèmes signalés.</li><li>L’appareil est peut-être désactivé ou en pause sur l’hyperviseur. Activez votre appareil ! Pour plus d’informations, consultez [Gérer l’alimentation](..\databox-gateway\data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).</li><li>Votre appareil peut avoir redémarré en raison d’une mise à jour. Patientez quelques minutes, puis essayez de vous reconnecter.</li></ul>|


## <a name="edge-compute-alerts"></a>Alertes Edge Computing

Les alertes suivantes sont déclenchées pour le calcul Edge ou la carte d’accélération de calcul, qui peut être un processeur graphique (GPU) ou une unité de traitement de vision (VPU) en fonction du modèle d’appareil.

|Texte d'alerte |Gravité |Description/action recommandée |
|-----------|---------|---------------------------------|
|Le calcul Edge Computing n’est pas sain. |Critique | Redémarrez votre appareil pour résoudre le problème. Dans l’interface utilisateur locale de votre appareil, accédez à **Maintenance** > **Paramètres d’alimentation**, puis cliquez sur **Redémarrer**.<br>Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Le calcul Edge Computing a rencontré un problème avec la résolution de noms. |Critique |Assurez-vous que votre serveur DNS {15} est en ligne et accessible. Si le problème persiste, contactez votre administrateur réseau. |
|La configuration de la carte d’accélération de calcul présente un problème.<sup>*</sup> |Critique |Nous avons détecté une configuration de carte d’accélération de calcul non prise en charge.<br>Avant de contacter le support Microsoft, suivez les étapes suivantes :<ol><li>Dans l’interface utilisateur locale, accédez à **Dépannage** > **Support**.</li><li>Créez et téléchargez un package de support.</li><li>[Créez une demande de support](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Joignez le package à la demande de support.</li></ol> |
|La configuration de la carte d’accélération de calcul présente un problème.<sup>*</sup> |Critique |Nous avons détecté une carte d’accélération de calcul non prise en charge.<br>Avant de contacter le support Microsoft, suivez les étapes suivantes :<ol><li>Dans l’interface utilisateur locale, accédez à **Dépannage** > **Support**.</li><li>Créez et téléchargez un package de support.</li><li>[Créez une demande de support](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Joignez le package à la demande de support.</li></ol> |
|La configuration de la carte d’accélération de calcul présente un problème.<sup>*</sup> |Critique |Cela peut être dû à l’une des raisons suivantes :<ol><li>Si la carte est un FPGA, l’image n’est pas valide.</li><li>La carte d’accélération de calcul n’est pas correctement installée.</li><li>Problèmes sous-jacents liés au pilote d’accélération de calcul.</li></ol>Pour résoudre le problème, redéployez le module Azure IoT Edge. Une fois le problème résolu, l’alerte disparaît.<br>Si le problème persiste, procédez comme suit : <ol><li>Dans l’interface utilisateur locale, accédez à **Dépannage** > **Support**.</li><li>Créez et téléchargez un package de support.</li>[Créez une demande de support](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Joignez le package à la demande de support.</li></ol> |
|La configuration de la carte d’accélération de calcul présente un problème.<sup>*</sup> |Critique |Ceci est dû à une erreur interne.<br>Avant de contacter le support Microsoft, suivez les étapes suivantes :<ol><li>Dans l’interface utilisateur locale, accédez à **Dépannage** > **Support**.</li><li>Créez et téléchargez un package de support.</li>[Créez une demande de support](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Joignez le package à la demande de support.</li></ol> |
|La configuration de la carte d’accélération de calcul présente un problème.<sup>*</sup> |Critique |Lorsque votre module Azure IoT Machine Learning démarre, vous risquez de voir ce problème de façon temporaire. Attendez quelques minutes pour voir si le problème se résout de lui-même.<br>Si le problème persiste, procédez comme suit :<ol><li>Dans l’interface utilisateur locale, accédez à **Dépannage** > **Support**.</li><li>Créez et téléchargez un package de support.</li>[Créez une demande de support](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Joignez le package à la demande de support.</li></ol> |
|Le logiciel du pilote de la carte d’accélération de calcul n’est pas en cours d’exécution. |Critique |Ceci est dû à une erreur interne.<br>Avant de contacter le support Microsoft, suivez les étapes suivantes :<ol><li>Dans l’interface utilisateur locale, accédez à **Dépannage** > **Support**.</li><li>Créez et téléchargez un package de support.</li><li>[Créez une demande de support](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Joignez le package à la demande de support.</li></ol> |
|La carte d’accélération de calcul sur votre appareil n’est pas saine. |Critique |Ceci est dû à une erreur interne.<br>Avant de contacter le support Microsoft, suivez les étapes suivantes :<ol><li>Dans l’interface utilisateur locale, accédez à **Dépannage** > **Support**.</li><li>Créez et téléchargez un package de support.</li><li>[Créez une demande de support](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Joignez le package à la demande de support.</li></ol> |
|Arrêt de la carte d’accélération de calcul, car sa température a dépassé la limite de fonctionnement ! |Critique |Ceci est dû à une erreur interne.<br>Avant de contacter le support Microsoft, suivez les étapes suivantes :<ol><li>Dans l’interface utilisateur locale, accédez à **Dépannage** > **Support**.</li><li>Créez et téléchargez un package de support.</li><li>[Créez une demande de support](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Joignez le package à la demande de support.</li></ol> |
|Les performances de la carte d’accélération de calcul sont dégradées. |Avertissement |Cela peut être dû au fait que la carte d’accélération de calcul connaît une utilisation intensive. Envisagez d’arrêter la charge de travail ou de la réduire sur le module Azure IoT Machine Learning.<br>Avant de contacter le support Microsoft, suivez les étapes suivantes :<ol><li>Dans l’interface utilisateur locale, accédez à **Dépannage** > **Support**.</li><li>Créez et téléchargez un package de support.</li><li>[Créez une demande de support](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Joignez le package à la demande de support.</li></ol> |
|La température de la carte d’accélération de calcul augmente. |Avertissement |Cela peut être dû au fait que la carte d’accélération de calcul connaît une utilisation intensive. Envisagez d’arrêter la charge de travail ou de la réduire sur le module Azure IoT Machine Learning.<br>Avant de contacter le support Microsoft, suivez les étapes suivantes :<ol><li>Dans l’interface utilisateur locale, accédez à **Dépannage** > **Support**.</li><li>Créez et téléchargez un package de support.</li><li>[Créez une demande de support](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Joignez le package à la demande de support.</li></ol> |
|Le calcul Edge Computing n’a pas pu accéder aux données sur le partage {16}. |Avertissement |Vérifiez que vous avez accès au partage {16}. Si vous pouvez accéder au partage, cela indique un problème lié au calcul Edge Computing. <br> Pour résoudre le problème, redémarrez votre appareil. Dans l’interface utilisateur locale de votre appareil, accédez à **Maintenance** > **Paramètres d’alimentation**, puis cliquez sur **Redémarrer**. <br> Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Le calcul Edge Computing n’a pas pu accéder aux données sur le partage {16}. Ceci peut être dû au fait que le partage n’existe plus. |Avertissement |Si le partage n’existe pas {16}, redémarrez votre appareil pour résoudre le problème. Dans l’interface utilisateur locale de votre appareil, accédez à **Maintenance** > **Paramètres d’alimentation**, puis cliquez sur **Redémarrer**. <br> Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|L’agent IoT Edge n’est pas en cours d’exécution. |Avertissement |Redémarrez votre appareil pour résoudre le problème. Dans l’interface utilisateur locale de votre appareil, accédez à **Maintenance** > **Paramètres d’alimentation**, puis cliquez sur **Redémarrer**. <br> Si le problème persiste, [contactez le support Microsoft](azure-stack-edge-contact-microsoft-support.md).md). |
|Le service IoT Edge n’est pas en cours d’exécution. |Avertissement |Redémarrez votre appareil pour résoudre le problème. Dans l’interface utilisateur locale de votre appareil, accédez à **Maintenance** > **Paramètres d’alimentation**, puis cliquez sur **Redémarrer**. <br> Si le problème persiste, [contactez le support Microsoft](azure-stack-edge-contact-microsoft-support.md).md). |
|Le stockage utilisé par le calcul Edge Computing est presque plein. | Avertissement | [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md) . |
|Votre module de calcul Edge Computing {20} est déconnecté d’IoT Edge. |Avertissement |Redémarrez votre appareil pour résoudre le problème. Dans l’interface utilisateur locale de votre appareil, accédez à **Maintenance** > **Paramètres d’alimentation**, puis cliquez sur **Redémarrer**. <br> Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Vos modules de calcul Edge Computing utilisent peut-être un point de montage local {15} différent de celui utilisé par un partage. |Avertissement |Assurez-vous que le point de montage local {15} utilisé est celui qui est mappé sur le partage.<ul><li>Dans le portail Azure de votre ressource Data Box Edge, accédez à **Partages**.</li><li>Sélectionnez un partage pour voir le point de montage local du module de calcul Edge Computing.</li><li>Assurez-vous que ce chemin d’accès est utilisé dans le module et déployez de nouveau le module.</li></ol>Redémarrez l’appareil. Dans l’interface utilisateur locale de votre appareil, accédez à **Maintenance** > **Paramètres d’alimentation**, puis cliquez sur **Redémarrer**. <br> Si l’erreur persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |

<sup>*</sup> Cette alerte est déclenchée par plusieurs types d’événements, avec différentes actions recommandées.


## <a name="local-azure-resource-manager-arm-alerts"></a>Alertes Azure Resource Manager (ARM) local

Les alertes suivantes sont déclenchées par l’Azure Resource Manager local (ARM), qui est utilisé pour se connecter aux API locales sur les appareils Azure Stack Edge.

|Texte d'alerte |Gravité |Description/action recommandée |
|-----------|---------|---------------------------------|
|Le certificat d’authentification de service spécifié avec l’empreinte numérique {0} n’a pas de clé privée |Critique |Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Le certificat avec l’empreinte numérique {0} à l’emplacement {1} est introuvable ou n’est pas accessible. |Critique |Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Impossible de se connecter au point de terminaison : {0} |Critique |Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Une erreur s’est produite lors de la demande web : {0} |Critique |Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Délai d’attente de la demande dépassé pour l’URL : {0} |Critique |Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Impossible d’obtenir le jeton à l’aide du point de terminaison de connexion {0} pour la ressource {1} |Critique |Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Une erreur inconnue s’est produite. CodeErreur : {0}. Détails : {1} | Critique |Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Impossible de démarrer le service de machine virtuelle sur l’appareil. |Critique | Si vous voyez cette alerte, [contactez le Support Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Le service de machine virtuelle n’est pas en cours d’exécution sur l’appareil. |Critique |Si vous voyez cette alerte, [contactez le Support Microsoft](azure-stack-edge-contact-microsoft-support.md). |


## <a name="performance-alerts"></a>Alertes de performances

Les alertes suivantes indiquent des problèmes de performances liés au stockage ou à l’utilisation du processeur, de la mémoire ou du disque sur un appareil Azure Stack Edge.

|Texte d'alerte |Gravité |Description/action recommandée |
|-----------|---------|---------------------------------|
|L’utilisation de processeur sur votre appareil a dépassé le seuil pendant une durée prolongée. |Critique |Réduisez les charges de travail ou les modules exécutés sur votre appareil. Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Les processeurs réservés pour les machines virtuelles sur votre appareil dépassent le seuil configuré. |Critique |Exécutez l’une des étapes suivantes :<ol><li>Réduisez la réservation du processeur pour les machines virtuelles exécutées sur votre appareil.</li><li>Retirez quelques machines virtuelles de votre appareil.</li></ol> |
|La mémoire utilisée par les machines virtuelles sur votre appareil dépassent le seuil configuré. |Critique |Exécutez l’une des étapes suivantes :<ol><li>Réduisez la mémoire allouée aux machines virtuelles exécutées sur votre appareil.</li><li>Retirez quelques machines virtuelles de votre appareil.</li></ol> |
|Le volume de données sur l’appareil est plein à {0} %. Les écritures sur l’appareil sont limitées. |Critique                    |<ol><li>Répartissez l’ingestion des données pour cibler les heures creuses.</li><li>Ceci peut être dû au fait que le réseau est lent. Dans l’interface utilisateur web locale de l’appareil, accédez à **Résolution des problèmes** > **Tests de Diagnostic** et cliquez sur **Exécuter les tests de diagnostic**. Résolvez les problèmes signalés.</li></ol>Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|La mémoire utilisée par les machines virtuelles sur le nœud {0} de votre appareil dépassent le seuil configuré. |Critique |L’appareil essaiera d’équilibrer la charge sur les autres nœuds. Envisagez de réduire certaines charges de travail de machine virtuelle sur votre appareil. Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Votre appareil n’a presque plus d’espace de stockage. Si un disque tombe en panne, vous ne pourrez peut-être pas restaurer les données sur cet appareil. |Critique |Supprimez des données pour libérer de la capacité sur votre appareil. |
|L’utilisation du processeur sur le nœud {0} de votre appareil a dépassé le seuil pendant une durée prolongée.<sup>*</sup> |Avertissement |L’appareil essaiera d’équilibrer la charge sur les autres nœuds. Envisagez de réduire certaines charges de travail de machine virtuelle sur votre appareil. Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|L’utilisation du processeur sur le nœud {0} de votre appareil a dépassé le seuil pendant une durée prolongée.<sup>*</sup> |Avertissement |Réduisez les charges de travail ou les modules exécutés sur votre appareil. Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Le nœud {0} sur votre appareil utilise plus de mémoire que prévu. |Avertissement |Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Les processeurs réservés pour les machines virtuelles sur le nœud {0} de votre appareil dépassent le seuil configuré. |Avertissement |Exécutez l’une des étapes suivantes :<ol><li>Réduisez la réservation du processeur pour les machines virtuelles exécutées sur votre appareil.</li><li>Retirez quelques machines virtuelles de votre appareil.</li></ol> |
|La mémoire utilisée par les machines virtuelles sur votre appareil dépassent le seuil configuré. |Avertissement |Exécutez l’une des étapes suivantes :<ol><li>Réduisez la mémoire allouée aux machines virtuelles exécutées sur votre appareil.</li><li>Retirez quelques machines virtuelles de votre appareil.</li></ol> |
|Trop de machines virtuelles sont actives sur le nœud {0} de votre appareil. |Avertissement |L’appareil essaiera d’équilibrer la charge sur les autres nœuds. Envisagez de réduire certaines charges de travail de machine virtuelle sur votre appareil. Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Le disque dur virtuel {0} est proche de sa limite de capacité. |Avertissement |Supprimez des données pour libérer de la capacité. |

<sup>*</sup> Cette alerte est déclenchée par plusieurs types d’événements, avec différentes actions recommandées.


## <a name="storage-alerts"></a>Alertes de stockage

Les alertes suivantes concernent les problèmes qui se produisent lors de l’accès ou du téléchargement de données sur le Stockage Azure.

|Texte d'alerte |Gravité |Description/action recommandée |
|-----------|---------|---------------------------------|
|Impossible d’accéder au volume {0}.<sup>*</sup> |Critique |Cela peut se produire lorsque le volume est hors connexion ou si un trop grand nombre de lecteurs ou de serveurs ont échoué ou se sont déconnectés. Procédez comme suit :<ol><li>Reconnectez les lecteurs manquants et redémarrez les serveurs arrêtés.</li><li>Faites en sorte que la synchronisation se termine.</li><li>Remplacez tous les disques défaillants et restaurez les données perdues à partir de la sauvegarde.</li></ol> |
|Impossible d’accéder au volume {0}.<sup>*</sup> |Critique |Dans l’interface utilisateur locale de l’appareil, accédez à **Résolution des problèmes** > **Tests de Diagnostic** et cliquez sur **Exécuter les tests de diagnostic**. Résolvez les problèmes signalés.<br>Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Volume {0} introuvable.<sup>*</sup> |Critique |Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Volume {0} introuvable.<sup>*</sup> |Critique<br>Avertissement |Développez le volume ou migrez les charges de travail vers d’autres volumes. |
|Certaines données de ce volume {0} ne sont pas complètement résilientes. Elles restent accessibles. |Informationnel |Restauration de la résilience des données. |
|Impossible de charger {1} fichier(s) du partage {0}. |Critique |Cela peut être dû à l’une des raisons suivantes :<ol><li>En raison d’infractions aux conventions d’attribution de noms et de dimensionnement du stockage Azure. Pour plus d’informations, accédez à [Conventions d’affectation de noms](../azure-resource-manager/management/resource-name-rules.md).</li><li>Étant donné que les fichiers téléchargés ont été modifiés dans le cloud par d’autres applications en dehors de l’appareil.<ul><li>{2} dans le partage {1} ou</li><li>{3} dans le compte {4}.</li></ol></ul> |
|Impossible de se connecter au compte de stockage {0}.<sup>*</sup> |Critique |Cela peut être dû au fait que les clés d’accès du compte de stockage ont été régénérées. Si les clés ont été régénérées, vous devez synchroniser les nouvelles.<br>Pour résoudre le problème, dans le portail Azure, accédez à **Partages**, sélectionnez le partage et actualisez les clés de stockage. |
|Impossible de se connecter au compte de stockage {0}.<sup>*</sup> |Critique |Ceci est peut-être dû à des problèmes de connectivité Internet. L’appareil n’est pas en mesure de communiquer avec le service du compte de stockage. Dans l’interface utilisateur web locale de l’appareil, accédez à **Résolution des problèmes** > **Tests de Diagnostic** et cliquez sur **Exécuter les tests de diagnostic**. Résolvez les problèmes signalés. |
|L’appareil contient {0} fichiers. Un maximum de {1} fichiers est pris en charge. |Critique |Supprimez certains fichiers de l’appareil. |
|Débit faible vers et depuis le Stockage Azure. |Avertissement  |Dans l’interface utilisateur web locale de l’appareil, accédez à **Résolution des problèmes** > **Tests de Diagnostic** et cliquez sur **Exécuter les tests de diagnostic**. Résolvez les problèmes signalés. Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |

<sup>*</sup> Cette alerte est déclenchée par plusieurs types d’événements, avec différentes actions recommandées.


## <a name="security-alerts"></a>Alertes de sécurité

Les alertes suivantes signalent des problèmes d’accès liés à des mots de passe, des certificats, des clés ou des tentatives d’accès à un appareil Azure Stack Edge.

|Texte d'alerte |Gravité |Description/action recommandée |
|-----------|---------|---------------------------------|
|{0} de {1} expire dans {2} jours. |Critique<br>Avertissement |Vérifiez votre certificat et chargez un nouveau certificat avant la date d’expiration. |
|{0} de type {1} n’est pas valide. |Critique |Vérifiez votre certificat. Si le certificat n’est pas valide, chargez un nouveau certificat. |
|Échec de la rotation du certificat interne |Critique |Impossible d’appliquer la rotation des certificats internes. Si les services sont altérés, [contactez le support Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Impossible de se connecter à {0}. Nombre d’échecs de tentatives de connexion : {1} |Critique<br>Avertissement<br>Informationnel |Assurez-vous que vous avez entré le bon mot de passe.<br>Un utilisateur autorisé peut tenter de se connecter à votre appareil avec un mot de passe incorrect. Vérifiez que ces tentatives proviennent d’une source légitime.<br>Si vous continuez à voir les échecs de tentatives de connexion, contactez votre administrateur réseau. |
|La rotation du protecteur de clé SED sur le nœud {0} ne s’est pas terminée à temps. |Avertissement |La tentative de rotation du protecteur de clé SED avec la nouvelle valeur par défaut ne s’est pas terminée à temps. Vérifiez si le nœud et les disques physiques sont dans un état sain. Le système va réessayer. |
|Le mot de passe de l’appareil a changé |Informationnel |Le mot de passe administrateur de l’appareil a changé. Il s’agit d’une action requise dans le cadre de la première configuration de l’appareil ou de la réinitialisation standard du mot de passe. Aucune action supplémentaire n’est requise. |
|Une session de support est activée. |Informationnel |Il s’agit d’une alerte d’information pour que les administrateurs puissent vérifier que l’activation de la session de support est légitime. Aucune action n'est nécessaire. |
|Une session de support a démarré. |Informationnel |Il s’agit d’une alerte d’information pour que les administrateurs puissent vérifier que la session de support est légitime. Aucune action n'est nécessaire. |


## <a name="key-vault-alerts"></a>Alertes Key Vault

Les alertes suivantes sont liées à votre configuration Azure Key Vault.

|Texte d'alerte |Gravité |Description/action recommandée |
|-----------|---------|---------------------------------|
|Azure Key Vault n’est pas configuré<sup>*</sup> |Critique<br>Avertissement |<ol><li>Vérifiez que Key Vault n’est pas supprimé.</li><li>Attribuez les autorisations appropriées à votre appareil pour obtenir et définir les secrets. Pour obtenir des instructions détaillées, consultez [Configuration requise pour une ressource Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#prerequisites).</li><li>Si les secrets sont supprimés de manière réversible, suivez les étapes [ci-dessous](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates) pour récupérer les secrets.</li><li>Actualisez les détails de Key Vault pour effacer l’alerte.</li></ol> |
|Azure Key Vault n’est pas configuré<sup>*</sup> |Avertissement |Configurez Key Vault pour votre ressource Azure Stack Edge. Pour obtenir des instructions détaillées, consultez [Créer un coffre de clés](../key-vault/general/quick-create-portal.md). |
|Le coffre de clés est supprimé |Critique |Si le coffre de clés est supprimé et que la durée de la protection de purge de 90 jours n’est pas écoulée, procédez comme suit pour [récupérer votre de coffre de clés](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-a-soft-deleted-key-vault). |
|Impossible de récupérer les secrets à partir du Key Vault |Critique |<ol><li>Vérifiez que Key Vault n’est pas supprimé.</li><li>Attribuez les autorisations appropriées à votre appareil pour obtenir et définir les secrets. Les autorisations requises sont présentes [ici](azure-stack-edge-gpu-deploy-prep.md#prerequisites).</li><li>Actualisez les détails de Key Vault pour effacer l’alerte.</li></ol> |
|Impossible d’accéder au Key Vault |Critique |<ol><li>Vérifiez que Key Vault n’est pas supprimé.</li><li>Attribuez les autorisations appropriées à votre appareil pour obtenir et définir les secrets. Pour plus d’informations, consultez les [étapes détaillées](azure-stack-edge-gpu-deploy-prep.md#prerequisites).</li><li>Actualisez les détails de Key Vault pour effacer l’alerte.</li></ol> |

<sup>*</sup> Cette alerte est déclenchée par plusieurs types d’événements, avec différentes actions recommandées.


## <a name="hardware-alerts"></a>Alertes de matériel

Les alertes suivantes indiquent un problème avec un composant matériel, par exemple un disque physique, une carte réseau ou une unité d’alimentation sur un appareil Azure Stack Edge.

|Texte d'alerte |Gravité |Description/action recommandée |
|-----------|---------|---------------------------------|
|{0} sur {1} a échoué. |Critique |Cela est dû au fait que l’alimentation n’est pas correctement connectée ou a échoué. Pour résoudre ce problème, procédez comme suit :<ol><li>Assurez-vous que la connexion de l’alimentation est correcte.</li><li>[Contactez le support Microsoft](azure-stack-edge-contact-microsoft-support.md) pour commander une alimentation de rechange. |
|Impossible d’accéder à {1}. |Critique      |Si le contrôleur est désactivé, redémarrez-le.<br>Assurez-vous que l’alimentation fonctionne. Pour plus d’informations sur la surveillance des voyants de l’alimentation, consultez https://www.microsoft.com/2.<!--Need new link target. This one goes nowhere--><br>Si le problème persiste, [contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|{0} est hors tension. |Avertissement |Branchez le bloc d’alimentation sur une unité de distribution d’alimentation. |
|Un ou plusieurs composants de l’appareil ne fonctionnent pas correctement. |Critique |[contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md) . |
|Impossible de remplacer {0}. |Avertissement |[contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md) . |
|Remplacement de {0} démarré. |Informationnel |Aucune action de votre part n’est nécessaire. |
|{0} remplacé. |Informationnel |Aucune action de votre part n’est nécessaire. |
|{0} est déconnecté. |Avertissement |Vérifiez que {0} est correctement branché et que l’interface réseau est opérationnelle. |
|{0} a échoué.<sup>*</sup> |Critique |L’appareil doit être remplacé. [Contactez le support Microsoft](azure-stack-edge-contact-microsoft-support.md) pour remplacer l’appareil. |
|{0} a échoué.<sup>*</sup> |Critique |Vérifiez que {0} est correctement branché et que l’interface réseau est opérationnelle.<br>Dans l’interface utilisateur web locale de l’appareil, accédez à **Résolution des problèmes** > **Tests de Diagnostic** et cliquez sur **Exécuter les tests de diagnostic**. Résolvez les problèmes signalés.<br>Si le problème persiste, contactez le support technique Microsoft à l’adresse [https://aka.ms/getazuresupport](azure-stack-edge-contact-microsoft-support.md). |
|Impossible de lire certaines données du disque physique de cache {1} sur le nœud {0}, nous ne pouvons pas les déplacer sur des lecteurs de grande capacité. |Avertissement |Remplacez le disque physique. |
|Des opérations de lecture ou d’écriture du disque physique de cache {0} sur le nœud {1} ont échoué. Pour protéger vos données, nous les avons déplacées sur des lecteurs de grande capacité. |Avertissement | Remplacez le disque physique. |
|Le disque physique {0} sur le nœud {1} n’a pas pu lire ou écrire plusieurs fois au cours des deux derniers jours. Si ce problème persiste, cela peut signifier que le lecteur fonctionne mal, est endommagé ou devient défectueux. |Avertissement |Si le problème persiste, envisagez de remplacer le disque physique. |
|Le disque physique {0} sur le nœud {1} a des problèmes de lecture ou d’écriture. |Avertissement |Si le problème persiste, envisagez de remplacer le disque physique. |
|Le disque physique {0} sur le nœud {1} a atteint 100 % de son endurance d’écriture nominale et est maintenant en lecture seule, ce qui signifie qu’il ne peut plus effectuer d’opérations d’écriture. |Avertissement |Envisagez de remplacer le disque physique. |
|Le disque physique {0} sur le nœud {1} a échoué. |Avertissement |Remplacez le disque physique. |
|Le disque physique {0} sur le nœud {1} rencontre des problèmes.<sup>*</sup> |Avertissement |Le disque physique a rencontré plusieurs blocs erronés pendant les écritures au cours des deux derniers jours. Cela peut indiquer que le lecteur fonctionne mal, est endommagé ou devient défectueux.<br>Si le problème persiste, envisagez de remplacer le disque physique. |
|Le disque physique {0} sur le nœud {1} rencontre des problèmes.<sup>*</sup> |Avertissement |Le disque physique {0} sur le nœud {1} a rencontré plusieurs blocs erronés pendant les écritures au cours des deux derniers jours. Cela peut indiquer que le lecteur fonctionne mal, est endommagé ou devient défectueux.<br>Si le problème persiste, envisagez de remplacer le disque physique. |
|Le disque physique {0} sur le nœud {1} rencontre des problèmes. |Avertissement |Si le problème persiste, envisagez de remplacer le disque physique. |
|Le disque physique {0} sur le nœud {1} montre des signes d’usure. Il risque de passer en lecture seule, c’est-à-dire qu’il ne peut plus effectuer d’opérations d’écriture, quand il atteint 100 % de son endurance nominale. |Avertissement |Envisagez de remplacer le disque physique. |
|Le disque physique {0} sur le nœud {1} est lent. |Avertissement |Si le problème persiste, envisagez de remplacer le disque physique. |
|Aucune connectivité pour le disque physique {0} sur le nœud {1}. |Avertissement |Vérifiez que le disque physique fonctionne et qu’il est correctement connecté.| 
|{0} a échoué ou est manquant. |Critique |L’état de votre appareil s’est dégradé. L’appareil sera défectueux en cas de défaillance d’un autre disque. [Contactez le support Microsoft](azure-stack-edge-contact-microsoft-support.md) pour commander un disque de rechange. Remplacez le disque. |
|Le disque physique {0} sur le nœud {1} risque de bientôt échouer. |Avertissement |Remplacez le disque physique. |
|Une opération de remplacement de disque est en cours d’exécution. PercentComplete = {0}, Disk = {2}. |Critique |Il s'agit d'un événement d'information. Aucune action à effectuer pour l’instant. |
|Le disque physique {0} sur le nœud {1} a échoué. |Avertissement |Remplacez le disque physique. |
|Le disque physique {0} sur le nœud {1} répond par intermittence. |Avertissement |Remplacez le disque physique. |
|Le disque physique {0} sur le nœud {1} n’a pas de protecteur de clé SED par défaut actuellement défini. |Avertissement |Le système va tenter de mettre à jour le protecteur de clé SED vers la dernière version. Si le problème persiste, vérifiez si le lecteur est dans un état sain. |
|Le disque physique {0} sur le nœud {1} a échoué lors de la rotation du protecteur de clé SED. |Avertissement |La tentative de rotation du protecteur de clé SED avec la nouvelle valeur par défaut a échoué. Vérifiez si le disque physique est dans un état sain. Le système réessaiera. Si le problème persiste, remplacez le lecteur. |
|Le disque physique {0} sur le nœud {1} contient des métadonnées non reconnues. |Critique |Le disque peut contenir des données d’un pool de stockage inconnu. Remplacez ce disque par un disque pris en charge par Microsoft pour votre appareil et qui ne contient aucune donnée. |
|Le disque physique {0} sur le nœud {1} exécute une version du microprogramme non prise en charge. |Avertissement |[Contactez le support Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Le disque physique {0} sur le nœud {1} n’est pas un disque pris en charge. |Avertissement |Remplacez le disque physique par du matériel pris en charge. |
|Le capteur de température sur la carte mère du serveur {0} a généré un avertissement. |Avertissement |Vérifiez la température du nœud. |

<sup>*</sup> Cette alerte est déclenchée par plusieurs types d’événements, avec différentes actions recommandées.


## <a name="update-alerts"></a>Alertes de mise à jour

Les alertes suivantes concernent les mises à jour Microsoft et les mises à jour de microprogramme pour les composants de l’appareil physique.

|Texte d'alerte |Gravité |Description/action recommandée |
|-----------|---------|---------------------------------|
|Impossible de télécharger les mises à jour. Message d’erreur : {0}. |Critique |{0} |
|Impossible d’installer les mises à jour. Message d’erreur : {0}. |Critique |Corrigez l’erreur : {0} |
|Impossible de rechercher les mises à jour. Message d’erreur : {0}. |Critique |Corrigez l’erreur : {0} |
|{0} mise(s) à jour disponible(s). |Informationnel |Nous vous conseillons fortement d’installer ces mises à jour. Pour plus d’informations, consultez [Comment installer les mises à jour](azure-stack-edge-gpu-install-update.md). |
|Impossible de mettre à jour le microprogramme du disque. |Critique |[contactez le support technique Microsoft](azure-stack-edge-contact-microsoft-support.md) . |
|Impossible de mettre à jour le microprogramme du disque physique {1} sur le nœud {0}. |Avertissement |[Contactez le support Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Impossible d’effectuer l’opération pendant le lancement du microprogramme. |Avertissement |Vérifiez que tous les espaces de stockage sont sains et qu’aucun domaine d’erreur n’est actuellement en mode maintenance. |
|Le lancement du microprogramme a été annulé en raison d’informations de version illisibles ou inattendues après l’application de la mise à jour de microprogramme. |Avertissement |Relancez le microprogramme une fois le problème résolu. |
|Le lancement du microprogramme a échoué, car la mise à jour du microprogramme a échoué sur trop de disques physiques. |Avertissement |Relancez le microprogramme une fois le problème résolu. |
|Mise à jour du microprogramme de disque démarrée. |Informationnel |Aucune action de votre part n’est nécessaire. |
|Microprogramme du disque mis à jour. |Informationnel |Aucune action de votre part n’est nécessaire. |
|Le lancement d’un microprogramme de disque physique est en cours. PercentComplete = {0}. |Informationnel |Il s'agit d'un événement d'information. Aucune action à effectuer pour l’instant. |


## <a name="virtual-machine-alerts"></a>Alertes de machine virtuelle

Les alertes suivantes sont déclenchées pour les machines virtuelles sur un appareil Azure Stack Edge.

|Texte d'alerte |Gravité |Description/action recommandée |
|-----------|---------|---------------------------------|
|La machine virtuelle {0} n’est pas saine. |Avertissement  |Pour résoudre les problèmes liés à une machine virtuelle, consultez https://aka.ms/vmtroubleshoot. |
|La machine virtuelle {0} ne fonctionne pas correctement. |Avertissement  |Pour résoudre les problèmes liés à une machine virtuelle, consultez https://aka.ms/vmtroubleshoot. |
|La machine virtuelle {0} n’est pas en cours d’exécution. |Avertissement  |Si le problème persiste, supprimez et redéployez la machine virtuelle.|
|Le système d’exploitation invité de la machine virtuelle {0} n’est pas sain. |Avertissement  |Pour résoudre les problèmes liés à une machine virtuelle, consultez https://aka.ms/vmtroubleshoot. |
|Votre machine virtuelle {0} n’a presque plus de mémoire. |Avertissement  |Réduisez l’utilisation de la mémoire sur votre machine virtuelle. |
|Votre machine virtuelle {0} ne répond pas aux demandes d’hôte. |Avertissement |Pour résoudre les problèmes liés à une machine virtuelle, consultez https://aka.ms/vmtroubleshoot. |


## <a name="next-steps"></a>Étapes suivantes

- [Créer des règles d’action pour gérer les notifications d’alerte](azure-stack-edge-gpu-manage-device-event-alert-notifications.md).
- [Utiliser des graphiques de métriques](azure-stack-edge-monitor.md#view-metrics).
- [Configurer Azure Monitor](azure-stack-edge-gpu-enable-azure-monitor.md).
