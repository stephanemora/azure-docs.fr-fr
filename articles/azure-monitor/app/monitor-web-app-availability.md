---
title: Superviser la disponibilité et la réactivité d’un site web - Azure Monitor
description: Configurez des tests ping dans Application Insights. Recevez des alertes si un site web devient indisponible ou répond lentement.
ms.topic: conceptual
ms.date: 04/15/2021
ms.reviewer: sdash
ms.openlocfilehash: 60698862e26175425221940a4b69867cb414fe86
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598871"
---
# <a name="monitor-the-availability-of-any-website"></a>Superviser la disponibilité d’un site web

Le nom « test ping d’URL » prête un peu à confusion. Pour être clair, ces tests n’utilisent nullement le protocole ICMP (Internet Control Message Protocol) pour vérifier la disponibilité de votre site. Au lieu cela, ils se servent de fonctionnalités de requête HTTP plus avancées pour s’assurer qu’un point de terminaison répond bien. Ils mesurent aussi les performances de cette réponse, et permettent de définir des critères de réussite personnalisés combinés à des fonctionnalités plus avancées telles que l’analyse des requêtes dépendantes et l’autorisation de nouvelles tentatives.

Pour créer un test de disponibilité, vous devez utiliser une ressource Application Insight existante ou [créer une ressource Application Insight](create-new-resource.md).

Pour créer votre première requête de disponibilité, ouvrez le volet Disponibilité et sélectionnez   **Créer un test**.

:::image type="content" source="./media/monitor-web-app-availability/availability-create-test-001.png" alt-text="Capture d’écran de la création d’un test.":::

## <a name="create-a-test"></a>Créer un test

|Paramètre| Explication
|----|----|----|
|**URL** |  L’URL peut être n’importe quelle page web que vous souhaitez tester, mais elle doit être visible à partir de l’Internet public. L’URL peut contenir une chaîne de requête. Vous pouvez donc, par exemple, tester un peu votre base de données. Si l’URL correspond à une redirection, nous allons la suivre, jusqu’à 10 redirections.|
|**Analyser les demandes dépendantes**| Le test demande des images, des scripts, des fichiers de style et d’autres fichiers qui font partie de la page web de test. Le temps de réponse enregistré inclut le temps qui a été nécessaire pour obtenir ces fichiers. Le test échoue si l’une de ces ressources ne peut pas être téléchargée avec succès dans le délai imparti pour l’ensemble du test. Si l’option n’est pas cochée, le test ne demande que le fichier à l’URL spécifiée. L’activation de cette option donne lieu à une vérification plus stricte. Le test peut échouer pour les cas difficiles à remarquer en parcourant le site manuellement.
|**Autoriser les nouvelles tentatives**|Une nouvelle tentative de test est effectuée peu de temps après l’échec du test. L’échec est signalé uniquement après trois tentatives infructueuses. Les tests suivants sont ensuite effectués selon la fréquence de test habituelle. La nouvelle tentative est temporairement suspendue jusqu’à la réussite de la tentative suivante. Cette règle est appliquée indépendamment à chaque emplacement de test. **Nous recommandons cette option.** En moyenne, environ 80 % des échecs disparaissent lors de la nouvelle tentative.|
|**Fréquence de test**| définit la fréquence selon laquelle le test est exécuté à partir de chaque emplacement de test. Avec, par défaut, une fréquence de cinq minutes et cinq emplacements de test, votre site sera testé en moyenne une fois par minute.|
|**Emplacements du test**| Ce sont les lieux d’où nos serveurs envoient des requêtes web à votre URL. **Nous recommandons d’utiliser au moins cinq emplacements de test**, afin de pouvoir faire la distinction entre les problèmes propres à votre site web et les problèmes de réseau. Vous pouvez sélectionner jusqu’à 16 emplacements.

**Si votre URL n’est pas visible à partir de l’Internet public, vous pouvez choisir une ouverture sélective de votre pare-feu pour autoriser uniquement les transactions entrantes à des fins de test**. Pour en savoir plus sur les exceptions de pare-feu applicables à nos agents de test de disponibilité, consultez le [guide des adresses IP](./ip-addresses.md#availability-tests).

> [!NOTE]
> Nous vous recommandons vivement de faire vos tests à partir de **cinq emplacements différents au minimum**. Cela vise à éviter les fausses alarmes qui peuvent provenir de problèmes temporaires rencontrés avec un emplacement spécifique. En outre, nous avons observé que la configuration optimale est d’avoir un **nombre d’emplacements de test égal au seuil d’emplacement de l’alerte + 2**.

## <a name="success-criteria"></a>Critères de réussite

|Paramètre| Explication
|----|----|----|
| **Délai d’expiration du test** |diminuez cette valeur pour être averti des réponses lentes. Le test est compté comme une erreur si des réponses de votre site n’ont pas été reçues pendant cette période. Si vous avez sélectionné **Analyser les demandes dépendantes**, l’ensemble des images, fichiers de style, scripts et autres ressources dépendantes ont dû être reçus pendant cette période.|
| **Réponse HTTP** | le code d’état retourné est comptabilisé comme un succès. 200 est le code qui indique qu’une page web normale a été retournée.|
| **Correspondance du contenu** | Chaîne telle que « Bienvenue ! » Nous vérifions qu’une correspondance exacte respectant la casse est présente dans chaque réponse. Il doit s'agir d'une chaîne standard sans caractère générique. N'oubliez pas que si votre contenu change, vous devrez peut-être l'actualiser. **La correspondance de contenu est prise en charge uniquement pour les caractères anglais** |

## <a name="alerts"></a>Alertes

|Paramètre| Explication
|----|----|----|
|**Quasi-temps réel (préversion)** | Nous vous conseillons d’utiliser les alertes en quasi-temps réel. La configuration de ce type d’alerte s’effectue après avoir créé votre test de disponibilité.  |
|**Seuil d’emplacement de l’alerte**|nous recommandons un minimum de 3 à 5 emplacements. La relation optimale entre le seuil d’emplacement de l’alerte et le nombre d’emplacements de test est **seuil d’emplacement de l’alerte** = **nombre d’emplacements de test - 2, avec un minimum de cinq emplacements de test.**|

## <a name="location-population-tags"></a>Étiquettes de remplissage d’emplacement

Les étiquettes de remplissage suivantes peuvent être utilisées pour l’attribut de géolocalisation lors du déploiement d’un test Ping d’URL de disponibilité à l’aide d’Azure Resource Manager.

### <a name="azure-gov"></a>Azure gov

| Nom d’affichage   | Nom du remplissage     |
|----------------|---------------------|
| USGov Virginia | usgov-va-azr        |
| Gouvernement des États-Unis - Arizona  | usgov-phx-azr       |
| Gouvernement des États-Unis - Texas    | usgov-tx-azr        |
| USDoD Est     | usgov-ddeast-azr    |
| US DoD - Centre  | usgov-ddcentral-azr |

#### <a name="azure"></a>Azure

| Nom d’affichage                           | Nom du remplissage   |
|----------------------------------------|-------------------|
| Australie Est                         | emea-au-syd-edge  |
| Brésil Sud                           | latam-br-gru-edge |
| USA Centre                             | us-fl-mia-edge    |
| Asie Est                              | apac-hk-hkn-azr   |
| USA Est                                | us-va-ash-azr     |
| France Sud (anciennement France Centre) | emea-ch-zrh-edge  |
| France Centre                         | emea-fr-pra-edge  |
| Japon Est                             | apac-jp-kaw-edge  |
| Europe Nord                           | emea-gb-db3-azr   |
| Centre-Nord des États-Unis                       | us-il-ch1-azr     |
| États-Unis - partie centrale méridionale                       | us-tx-sn1-azr     |
| Asie Sud-Est                         | apac-sg-sin-azr   |
| Ouest du Royaume-Uni                                | emea-se-sto-edge  |
| Europe Ouest                            | emea-nl-ams-azr   |
| USA Ouest                                | us-ca-sjc-azr     |
| Sud du Royaume-Uni                               | emea-ru-msa-edge  |

## <a name="see-your-availability-test-results"></a>Consulter les résultats des tests de disponibilité

Vous pouvez afficher les résultats des tests de disponibilité sous forme de vues linéaires et en nuage de points.

Au bout de quelques minutes, cliquez sur **Actualiser** pour voir les résultats de vos tests.

:::image type="content" source="./media/monitor-web-app-availability/availability-refresh-002.png" alt-text="Capture d’écran montrant la page de disponibilité avec le bouton Actualiser mis en surbrillance.":::

La vue en nuage de points montre des exemples de résultats de test contenant des détails de l’étape de test de diagnostic. Le moteur de test stocke les détails de diagnostic pour les tests qui présentent des erreurs. Pour les tests réussis, les détails de diagnostic sont stockés pour un sous-ensemble des exécutions. Pointez sur les points verts/rouges pour voir le test, son nom et son emplacement.

:::image type="content" source="./media/monitor-web-app-availability/availability-scatter-plot-003.png" alt-text="Vue linéaire." border="false":::

Sélectionnez un test ou emplacement spécifique, ou réduisez la période de temps pour voir plus de résultats autour de la période d’intérêt. Utilisez l’Explorateur de recherche pour voir les résultats de toutes les exécutions, ou utilisez les requêtes Analytics pour exécuter des rapports personnalisés sur ces données.

## <a name="inspect-and-edit-tests"></a>Examiner et modifier des tests

Pour modifier, désactiver temporairement ou supprimer un test, cliquez sur les points de suspension à côté du nom du test. La propagation de changements de configuration vers tous les agents de test peut prendre jusqu’à 20 minutes.

:::image type="content" source="./media/monitor-web-app-availability/edit.png" alt-text="Afficher les détails d’un test. Modifier et désactiver un test web." border="false":::

Vous souhaiterez peut-être désactiver les tests de disponibilité ou les règles d’alerte associées lorsque vous effectuez la maintenance de votre service.

## <a name="if-you-see-failures"></a>Si vous constatez des erreurs

Sélectionnez un point rouge.

:::image type="content" source="./media/monitor-web-app-availability/end-to-end.png" alt-text="Capture d’écran de l’onglet des détails de transaction de bout en bout." border="false":::

À partir d’un résultat de test de disponibilité, vous pouvez voir les détails de la transaction pour tous les composants. Ici, vous pouvez :

* Examiner le rapport de résolution des problèmes pour déterminer ce qui a pu provoqué l’échec de votre test, tandis que votre application est toujours disponible.
* Vérifier la réponse reçue à partir de votre serveur.
* Diagnostiquer la défaillance à l'aide des données de télémétrie côté serveur corrélées qui ont été collectées pendant le traitement du test de disponibilité en échec.
* Enregistrer un problème ou un élément de travail dans Git ou Azure Boards pour suivre le problème. Le bogue contient un lien vers cet événement.
* Ouvrir le résultat du test web dans Visual Studio.

Pour en savoir plus sur l’expérience des diagnostics de transaction de bout en bout, consultez la [documentation sur les diagnostics de transaction](./transaction-diagnostics.md).

Cliquez sur la ligne d'une exception pour afficher les détails de l'exception côté serveur qui a provoqué l'échec du test de disponibilité synthétique. Vous pouvez également obtenir la [capture instantanée de débogage](./snapshot-debugger.md) pour des diagnostics de niveau code plus riches.

:::image type="content" source="./media/monitor-web-app-availability/open-instance-4.png" alt-text="Diagnostics côté serveur.":::

Outre les résultats bruts, vous pouvez examiner deux mesures essentielles de la disponibilité dans [Metrics Explorer](../essentials/metrics-getting-started.md) :

1. Disponibilité : pourcentage des tests qui ont réussi, sur l’ensemble des exécutions de test.
2. Durée du test : durée moyenne du test sur toutes les exécutions de test.

## <a name="automation"></a>Automatisation

* [Utilisez des scripts PowerShell pour configurer un test de disponibilité](./powershell.md#add-an-availability-test) automatiquement.
* Configurez un [webhook](../alerts/alerts-webhooks.md) qui est appelé lorsqu’une alerte est déclenchée.


## <a name="next-steps"></a>Étapes suivantes

* [Availability alerts](availability-alerts.md) (Alertes de disponibilité)
* [Tests web à plusieurs étapes](availability-multistep.md)
* [Dépannage](troubleshoot-availability.md)
