---
title: Test standard de disponibilité - Azure Monitor Application Insights
description: Configurez la fonctionnalité Tests standard dans Application Insights pour vérifier la disponibilité d'un site web à l'aide d'un test à requête unique.
ms.topic: conceptual
ms.date: 07/13/2021
ms.openlocfilehash: 2a8741d874fdfad8a76465fd8127dfb4752a1a55
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113799133"
---
# <a name="standard-test"></a>Test standard

Les tests standard sont des tests à requête unique semblables au [test Ping d'URL](monitor-web-app-availability.md) mais en plus avancé. Outre la validation de la réponse d'un point de terminaison et la mesure des performances, les tests standard portent également sur la validité du certificat SSL, la vérification proactive de la durée de vie, le verbe de requête HTTP (par exemple `GET`,`HEAD`,`POST`, etc.), les en-têtes personnalisés et les données personnalisées associées à votre requête HTTP.

> [!NOTE]
> La fonctionnalité Tests standard est actuellement en préversion publique. Les préversions sont fournies sans contrat de niveau de service. Certaines fonctionnalités peuvent être limitées ou non prises en charge.

> [!NOTE]
> Pour le moment, aucuns frais supplémentaires ne s'appliquent à la fonctionnalité d'évaluation Tests standard. La tarification des fonctionnalités en préversion sera annoncée à l’avenir et un avis sera fourni avant le début de la facturation. Si vous choisissez de continuer à utiliser la fonctionnalité Tests standard après la période de préavis, vous serez facturé au tarif en vigueur.

Pour créer un test de disponibilité, vous devez utiliser une ressource Application Insights existante ou [créer une ressource Application Insights](create-new-resource.md).

> [!TIP]
> Si vous utilisez actuellement d'autres tests de disponibilité, comme les tests Ping d'URL, vous pouvez y ajouter des tests standard. Si vous souhaitez utiliser un test standard à la place de l'un de vos autres tests, ajoutez-le et supprimez votre ancien test. 

## <a name="create-a-standard-test"></a>Créer un test standard

Pour créer un test standard : 

1. Accédez à votre ressource Application Insights et sélectionnez le volet **Disponibilité**.
1. Sélectionnez **Ajouter un test standard (préversion)** .
    
    :::image type="content" source="./media/availability-standard-test/standard-test.png" alt-text="Capture d'écran du volet Disponibilité dans lequel l'onglet Ajouter un test standard est ouvert." lightbox="./media/availability-standard-test/standard-test.png":::

1. Entrez le nom du test, l'URL et les paramètres supplémentaires (explication ci-dessous), puis sélectionnez **Créer**.


|Paramètre | Explication |
|--------|-------------|
|**URL** |  L’URL peut être n’importe quelle page web que vous souhaitez tester, mais elle doit être visible à partir de l’Internet public. L’URL peut contenir une chaîne de requête. Vous pouvez donc, par exemple, tester un peu votre base de données. Si l’URL correspond à une redirection, nous allons la suivre, jusqu’à 10 redirections.|
|**Analyser les demandes dépendantes**| Le test demande des images, des scripts, des fichiers de style et d’autres fichiers qui font partie de la page web de test. Le temps de réponse enregistré inclut le temps qui a été nécessaire pour obtenir ces fichiers. Le test échoue si l’une de ces ressources ne peut pas être téléchargée avec succès dans le délai imparti pour l’ensemble du test. Si l'option n'est pas cochée, le test demande uniquement le fichier à l'URL spécifiée. L’activation de cette option donne lieu à une vérification plus stricte. Le test peut échouer pour les cas difficiles à remarquer en parcourant le site manuellement. |
|**Autoriser les nouvelles tentatives**| Lorsque le test échoue, une nouvelle tentative est effectuée peu après. L’échec est signalé uniquement après trois tentatives infructueuses. Les tests suivants sont ensuite effectués selon la fréquence de test habituelle. La nouvelle tentative est temporairement suspendue jusqu’à la réussite de la tentative suivante. Cette règle est appliquée indépendamment à chaque emplacement de test. **Nous recommandons cette option.** En moyenne, environ 80 % des échecs disparaissent lors de la nouvelle tentative.|
| **Test de validation du certificat SSL** | Vous pouvez vérifier le certificat SSL de votre site web pour vous assurer qu'il est correctement installé, valide, fiable, et qu'il ne provoque aucune erreur chez vos utilisateurs. |
| **Vérification proactive de la durée de vie** | Vous permet de définir une durée au terme de laquelle votre certificat SSL expirera. Une fois qu'il aura expiré, votre test échouera. |
|**Fréquence de test**| définit la fréquence selon laquelle le test est exécuté à partir de chaque emplacement de test. Avec, par défaut, une fréquence de cinq minutes et cinq emplacements de test, votre site sera testé en moyenne une fois par minute.|
|**Emplacements du test**|  Emplacements à partir desquels nos serveurs envoient des requêtes web à votre URL. **Nous recommandons d'utiliser au moins cinq emplacements de test** pour pouvoir faire la distinction entre les problèmes propres à votre site web et les problèmes de réseau. Vous pouvez sélectionner jusqu’à 16 emplacements.|
| **En-têtes personnalisés** | Paires clé-valeur qui définissent les paramètres de fonctionnement. |
| **Verbe de requête HTTP** | Indiquez l'action à associer à votre requête. |
| **Corps de la demande** | Données personnalisées associées à votre requête HTTP. Vous pouvez charger vos propres fichiers, entrer votre propre contenu ou désactiver cette fonctionnalité. |

## <a name="success-criteria"></a>Critères de réussite

|Paramètre| Explication|
|-------|------------|
| **Délai d’expiration du test** |diminuez cette valeur pour être averti des réponses lentes. Le test est compté comme une erreur si des réponses de votre site n’ont pas été reçues pendant cette période. Si vous avez sélectionné **Analyser les demandes dépendantes**, l’ensemble des images, fichiers de style, scripts et autres ressources dépendantes ont dû être reçus pendant cette période.|
| **Réponse HTTP** | le code d’état retourné est comptabilisé comme un succès. 200 est le code qui indique qu’une page web normale a été retournée.|
| **Correspondance du contenu** | Chaîne telle que « Bienvenue ! » Nous vérifions qu’une correspondance exacte respectant la casse est présente dans chaque réponse. Il doit s'agir d'une chaîne standard sans caractère générique. N'oubliez pas que si votre contenu change, vous devrez peut-être l'actualiser. **La correspondance de contenu est prise en charge uniquement pour les caractères anglais** |

## <a name="alerts"></a>Alertes

|Paramètre| Explication|
|-------|------------|
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

### <a name="azure-china"></a>Azure Chine

| Nom d’affichage   | Nom du remplissage     |
|----------------|---------------------|
| Chine orientale     | mc-cne-azr          |
| Chine orientale 2   | mc-cne2-azr         |
| Chine du Nord    | mc-cnn-azr          |
| Chine Nord 2  | mc-cnn2-azr         |

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

Au bout de quelques minutes, sélectionnez **Actualiser** pour voir les résultats de vos tests.

:::image type="content" source="./media/monitor-web-app-availability/availability-refresh-002.png" alt-text="Capture d’écran montrant la page de disponibilité avec le bouton Actualiser mis en surbrillance.":::

La vue en nuage de points montre des exemples de résultats de test contenant des détails de l’étape de test de diagnostic. Le moteur de test stocke les détails de diagnostic pour les tests qui présentent des erreurs. Pour les tests réussis, les détails de diagnostic sont stockés pour un sous-ensemble des exécutions. Pointez sur les points verts/rouges pour voir le test, son nom et son emplacement.

:::image type="content" source="./media/monitor-web-app-availability/availability-scatter-plot-003.png" alt-text="Vue linéaire." border="false":::

Sélectionnez un test ou emplacement spécifique, ou réduisez la période de temps pour voir plus de résultats autour de la période d’intérêt. Utilisez l’Explorateur de recherche pour voir les résultats de toutes les exécutions, ou utilisez les requêtes Analytics pour exécuter des rapports personnalisés sur ces données.

## <a name="inspect-and-edit-tests"></a>Examiner et modifier des tests

Pour modifier, désactiver temporairement ou supprimer un test, sélectionnez les points de suspension en regard du nom du test. La propagation de changements de configuration vers tous les agents de test peut prendre jusqu’à 20 minutes.

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

Pour en savoir plus sur l'expérience de diagnostic des transactions de bout en bout, consultez la [documentation relative aux diagnostics des transactions](./transaction-diagnostics.md).

Sélectionnez la ligne d'une exception pour afficher les détails de l'exception côté serveur qui a provoqué l'échec du test de disponibilité synthétique. Vous pouvez également obtenir la [capture instantanée de débogage](./snapshot-debugger.md) pour des diagnostics de niveau code plus riches.

:::image type="content" source="./media/monitor-web-app-availability/open-instance-4.png" alt-text="Diagnostics côté serveur.":::

Outre les résultats bruts, vous pouvez examiner deux mesures essentielles de la disponibilité dans [Metrics Explorer](../essentials/metrics-getting-started.md) :

* Disponibilité : pourcentage des tests qui ont réussi, sur l’ensemble des exécutions de test.
* Durée du test : durée moyenne du test sur toutes les exécutions de test.

## <a name="next-steps"></a>Étapes suivantes

* [Availability alerts](availability-alerts.md) (Alertes de disponibilité)
* [Tests web à plusieurs étapes](availability-multistep.md)
* [Dépannage](troubleshoot-availability.md)
* [Modèle Azure Resource Manager pour les tests web](/azure/templates/microsoft.insights/webtests?tabs=json)
