---
title: Annotations de version pour Application Insights | Microsoft Docs
description: Ajouter des marqueurs déploiement ou de build aux graphiques Metrics Explorer dans Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mbullwin
ms.openlocfilehash: 4b7b663b95bee12848f4afe2d2f48504a4408266
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2018
ms.locfileid: "51515169"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Annotations sur les graphiques de métriques dans Application Insights

Des annotations sur les graphiques [Metrics Explorer](app-insights-metrics-explorer.md) montrent les endroits où vous avez déployé une nouvelle build ou tout autre événement majeur. Elles vous permettent de mieux voir l’effet de vos modifications sur les performances de votre application. Elles peuvent être créées automatiquement par le [système de génération Azure DevOps Services](https://docs.microsoft.com/azure/devops/pipelines/tasks/). Vous pouvez également créer des annotations pour tout événement en [les créant à partir de PowerShell](#create-annotations-from-powershell).

> [!NOTE]
> Cet article reflète **l’expérience de métriques classique** qui est obsolète. Les annotations ne sont actuellement disponibles que dans l’expérience classique et dans les **[classeurs](app-insights-usage-workbooks.md)**. Pour en savoir plus sur l’expérience actuelle des métriques, vous pouvez consulter [cet article](../monitoring-and-diagnostics/monitoring-metric-charts.md).

![Exemples d’annotations avec corrélation visible avec le délai de réponse de serveur](./media/app-insights-annotations/00.png)

## <a name="release-annotations-with-azure-devops-services-build"></a>Annotations de version avec la build Azure DevOps Services

Annotations de version est une fonctionnalité du service Azure Pipelines basé sur le cloud Azure Pipelines d’Azure DevOps Services. 

### <a name="install-the-annotations-extension-one-time"></a>Installer l’extension Annotations (une fois)
Pour pouvoir créer des annotations de version, vous devez installer l’une des nombreuses extensions Azure DevOps Services disponibles dans Visual Studio Marketplace.

1. Connectez-vous à votre [Azure DevOps Services](https://visualstudio.microsoft.com/vso/) projet.
2. Dans Visual Studio Marketplace, [obtenez l’extension Annotations de version](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)et ajoutez-la à votre organisation Azure DevOps Services.

![En haut à droite de la page web d’Azure DevOps Services, ouvrez Marketplace. Sélectionnez Azure DevOps Services, sous Azure Pipelines, et choisissez Afficher plus.](./media/app-insights-annotations/10.png)

Il vous suffit de le faire une seule fois pour votre organisation Azure DevOps Services. Des annotations de version peuvent désormais être configurées pour n’importe quel projet de votre organisation. 

### <a name="configure-release-annotations"></a>Configurer des annotations de version

Vous devez obtenir une clé API distincte pour chaque modèle de version Azure DevOps Services.

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com) et ouvrez la ressource Application Insights qui surveille votre application. (Ou [créez-en une maintenant](app-insights-overview.md)si vous ne l’avez pas encore fait.)
2. Ouvrez **Accès d’API**, **ID Application Insights**.
   
    ![Dans portal.azure.com, ouvrez votre ressource Application Insights, puis choisissez Paramètres. Ouvrir Accès API. Copier l’ID de l’application](./media/app-insights-annotations/20.png)

4. Dans une fenêtre de navigateur distincte, ouvrez (ou créez) le modèle de version qui gère vos déploiements à partir d’Azure DevOps Services. 
   
    Ajoutez une tâche, sélectionnez la tâche d’annotation de version d’Application Insights à partir du menu.
   
    Collez **l’ID de l’application** que vous avez copié à partir du panneau Accès API.
   
    ![Dans Azure DevOps Services, ouvrez Version, sélectionnez un pipeline de mise en production et choisissez Modifier. Cliquez sur Ajouter une tâche, puis sélectionnez la tâche Annotation de version Application Insights. Collez l’ID Application Insights.](./media/app-insights-annotations/30.png)
4. Affectez au champ **APIKey** une variable `$(ApiKey)`.

5. De retour dans la fenêtre Azure, créez une clé API et copiez-la.
   
    ![Dans le panneau Accès API de la fenêtre Azure, cliquez sur Créer une clé API. Fournissez un commentaire, cochez Écrire des annotations, puis cliquez sur Générer une clé. Copiez la nouvelle clé.](./media/app-insights-annotations/40.png)

6. Ouvrez l’onglet Configuration du modèle de version.
   
    Créez une définition de variable pour `ApiKey`.
   
    Collez votre clé API pour la définition de la variable APIKey.
   
    ![Dans la fenêtre Azure DevOps Services, sélectionnez l’onglet Configuration, puis cliquez sur Ajouter une variable. Définissez le nom ApiKey et, dans Valeur, collez la clé que vous venez de générer, puis cliquez sur l’icône de verrouillage.](./media/app-insights-annotations/50.png)
7. Enfin, **enregistrez** le pipeline de mise en production.


## <a name="view-annotations"></a>Afficher les annotations
Désormais, lorsque vous utilisez le modèle de version pour déployer une nouvelle version, une annotation est envoyée à Application Insights. Les annotations figureront sur les graphiques Metrics Explorer.

Cliquez sur un marqueur d’annotation pour ouvrir les détails sur la version, notamment le demandeur, la branche de contrôle de code source, le pipeline de mise en production, l’environnement et bien plus encore.

![Cliquez sur un marqueur d’annotation de version.](./media/app-insights-annotations/60.png)

## <a name="create-custom-annotations-from-powershell"></a>Créer des annotations personnalisées à partir de PowerShell
Vous pouvez également créer des annotations à partir du processus de votre choix (sans utiliser Azure DevOps Services). 


1. Créez une copie locale du [script Powershell à partir de GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Obtenez l’ID d’application et créez une clé API à partir du panneau Accès d’API.

3. Appelez le script comme suit :

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

Il est facile de modifier le script, par exemple pour créer des annotations pour les événements passés.

## <a name="next-steps"></a>Étapes suivantes

* [Créer des éléments de travail](app-insights-diagnostic-search.md#create-work-item)
* [Automation avec PowerShell](app-insights-powershell.md)