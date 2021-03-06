---
title: Régénérer une application locale de Contoso sur Azure | Microsoft Docs
description: Découvrez comment Contoso régénère une application sur Azure à l’aide d’Azure App Services, de Kubernetes Service, de CosmosDB, d’Azure Functions et de Cognitive Services.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 6c68d90605590ed8a17296e83276c7ef5396d6a2
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092971"
---
# <a name="contoso-migration-rebuild-an-on-premises-app-to-azure"></a>Migration de Contoso : régénérer une application locale sur Azure

Cet article explique comment Contoso migre et regénère l’application SmartHotel360 dans Azure. Contoso migre la machine virtuelle frontale de l’application vers Web Apps dans Azure App Services. Le backend de l'application est généré à l’aide de microservices déployés sur des conteneurs gérés par Azure Kubernetes Service (AKS). Le site interagit avec Azure Functions pour fournir des fonctionnalités de photos d’animaux. 

Ce document fait partie d’une série d’articles qui montrent comment la société fictive Contoso migre ses ressources locales vers le cloud Microsoft Azure. La série comprend des informations générales et des scénarios qui illustrent comment configurer une infrastructure de migration, évaluer des ressources locales pour la migration et exécuter différents types de migration. Les scénarios augmentent en complexité. Nous ajouterons des articles au fil du temps.


**Article** | **Détails** | **État**
--- | --- | ---
[Article 1 : vue d’ensemble](contoso-migration-overview.md) | Fournit une vue d’ensemble de la stratégie de migration de Contoso, de la série d’articles et des exemples d’application que nous utilisons. | Disponible
[Article 2 : déployer une infrastructure Azure](contoso-migration-infrastructure.md) | Décrit comment Contoso prépare son infrastructure locale et son infrastructure Azure pour la migration. La même infrastructure est utilisée pour tous les articles de migration. | Disponible
[Article 3 : Évaluer les ressources locales](contoso-migration-assessment.md)  | Montre comment Contoso évalue une application à deux niveaux SmartHotel360 locale s’exécutant sur VMware. Contoso évalue les machines virtuelles de l’application avec le service [Azure Migrate](migrate-overview.md) et la base de données SQL Server de l’application avec [l’Assistant Migration de données Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponible
[Article 4 : Réhéberger une application sur des machines virtuelles Azure et une instance SQL Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Montre comment Contoso exécute une migration lift-and-shift vers Azure pour l’application SmartHotel360. Elle migre la machine virtuelle frontale de l’application à l’aide d’[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), et la base de données de l’application vers une instance SQL Managed Instance à l’aide du [service Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview). | Disponible
[Article 5 : Réhéberger une application sur des machines virtuelles Azure](contoso-migration-rehost-vm.md) | Montre comment Contoso migre les machines virtuelles de l’application SmartHotel360 en utilisant uniquement Site Recovery. | Disponible
[Article 6 : Réhéberger une application sur des machines virtuelles et un groupe de disponibilité AlwaysOn SQL Server (cet article)](contoso-migration-rehost-vm-sql-ag.md) | Montre comment Contoso migre l’application SmartHotel360. Elle utilise Site Recovery pour migrer la machine virtuelle de l’application, et Database Migration Service pour migrer la base de données de l’application vers un cluster SQL Server protégé par un groupe de disponibilité AlwaysOn. | Disponible
[Article 7 : ré-héberger une application Linux sur des machines virtuelles Azure](contoso-migration-rehost-linux-vm.md) | Montre comment Contoso effectue une migration lift-and-shift de l’application osTicket Linux sur des machines virtuelles Azure à l’aide de Site Recovery. | Disponible
[Article 8 : Réhéberger une application Linux sur des machines virtuelles Azure et un serveur Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Montre comment Contoso migre l’application osTicket Linux vers des machines virtuelles Azure à l’aide de Site Recovery, et migre la base de données de l’application vers une instance Azure MySQL Server à l’aide de MySQL Workbench. | Disponible
[Article 9 : Refactoriser une application sur Azure Web Apps et Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Montre comment Contoso migre l’application SmartHotel360 vers une application web Azure, et migre la base de données de l’application vers une instance Azure SQL Server. | Disponible
[Article 10 : Refactoriser une application Linux vers Azure Web Apps et Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Montre comment Contoso migre l’application Linux osTicket vers Azure Web Apps dans plusieurs sites intégrés avec GitHub pour assurer une livraison continue. Elle migre la base de données d’application vers une instance Azure MySQL. | Disponible
[Article 11 : Refactoriser TFS sur Azure DevOps Services](contoso-migration-tfs-vsts.md) | Montre comment Contoso migre le déploiement TFS (Team Foundation Server) local vers Azure DevOps Services dans Azure. | Disponible
[Article 12 : Réarchitecturer une application sur des conteneurs Azure et SQL Database](contoso-migration-rearchitect-container-sql.md) | Montre comment Contoso migre et réarchitecture son application SmartHotel vers Azure. Elle réarchitecture la couche web d’application en tant que conteneur Windows et la base de données d’application en une base de données Azure SQL Database. | Disponible
Article 13 : Régénérer une application dans Azure | Montre comment Contoso regénère son application SmartHotel à l’aide d’une série de fonctionnalités et services Azure, notamment App Services, Azure Kubernetes, Azure Functions, Cognitive Services et Cosmos DB. | Cet article
[Article 14 : Mettre à l’échelle une migration vers Azure](contoso-migration-scale.md) | Après des essais de différentes combinaisons de migration, Contoso se prépare à une migration complète vers Azure. | Disponible

Dans cet article, Contoso migre Windows à deux niveaux. Application .NET SmartHotel360 s’exécutant sur des machines virtuelles VMware vers Azure. Si vous souhaitez utiliser cette application, elle est disponible en open source et vous pouvez la télécharger à partir de [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Axes stratégiques

L’équipe informatique a travaillé en étroite collaboration avec des partenaires commerciaux pour comprendre le résultat qu’ils souhaitent obtenir avec cette migration :

- **Répondre à la croissance de l’entreprise** : Contoso se développe et souhaite fournir des expériences différenciées pour les clients sur les sites web de Contoso.
- **Agilité** : Contoso doit être en mesure de réagir plus rapidement que l’évolution du marché pour réussir dans une économie mondiale. 
- **Mise à l’échelle** : à mesure que l’entreprise croît, l’informatique de Contoso doit fournir des systèmes capables de croître au même rythme.
- **Coûts**  : Contoso souhaite réduire les coûts de licence.

## <a name="migration-goals"></a>Objectifs de la migration

L’équipe cloud de Contoso a épinglé les exigences d’application pour cette migration. Ces exigences ont été utilisées pour déterminer la meilleure méthode de migration :
 - L’application dans Azure restera aussi critique qu’aujourd'hui. Elle doit offrir de bonnes performances et se mettre à l’échelle facilement.
 - L’application ne doit pas utiliser de composants IaaS. Tout doit être créé pour utiliser des services PaaS ou sans serveur.
 - Les builds de l’application doivent s’exécuter dans des services cloud, et les conteneurs doivent résider dans un registre de conteneurs privé à l’échelle de l’entreprise dans le cloud.
 - Le service API utilisé pour les photos d’animaux doit être précis et fiable dans le monde réel, car les décisions prises par l’application doivent être respectées dans les hôtels. Tout animal auquel l’accès est accordé est autorisé à séjourner dans les hôtels.
 - Pour satisfaire aux exigences d’un pipeline DevOps, Contoso utilisera Azure DevOps pour la gestion du code source avec des dépôts Git.  Des builds et mises en production automatisées seront utilisées pour générer du code et pour le déployer sur Azure Web Apps, Azure Functions et AKS.
 - Différents pipelines CI/CD sont nécessaires pour les microservices sur le serveur backend et pour le site web sur le serveur frontal.
 - Les services backend ont un cycle de mise en production différent de l’application web frontale.  Pour répondre à cette exigence, ils déploieront deux pipelines DevOps différents.
 - Contoso nécessite l’approbation de la direction pour tout le déploiement du site web frontal, et le pipeline CI/CD doit la fournir.


## <a name="solution-design"></a>Conception de la solution

Après avoir défini précisément les objectifs et exigences, Contoso conçoit et examine une solution de déploiement, et identifie le processus de migration, notamment les services Azure à utiliser pour la migration.

### <a name="current-app"></a>Application actuelle

- L’application SmartHotel360 locale est hiérarchisée sur deux machines virtuelles (WEBVM et SQLVM).
- Les machines virtuelles sont situées sur un hôte VMware ESXi **contosohost1.contoso.com** (version 6.5).
- L’environnement VMware est géré par le serveur vCenter Server 6.5 (**vcenter.contoso.com**) s’exécutant sur une machine virtuelle.
- Contoso dispose d’un centre de données local (contoso-datacenter), avec un contrôleur de domaine local (**contosodc1**).
- Les machines virtuelles locales dans le centre de données Contoso seront désaffectées une fois la migration terminée.


### <a name="proposed-architecture"></a>Architecture proposée

- Le serveur frontal de l’application est déployé en tant qu’application web Azure App Services, dans la région Azure primaire.
- Une fonction Azure fournit les chargements de photos d’animaux, et le site interagit avec cette fonctionnalité.
- La fonction de photos d’animaux s’appuie sur l’API Vision de Cognitive Services et CosmosDB.
- Le serveur principal du site est créé à l’aide de microservices. Ceux-ci seront déployés sur des conteneurs gérés sur Azure Kubernetes Service (AKS).
- Les conteneurs seront générés à l’aide d’Azure DevOps, et envoyés à Azure Container Registry (ACR).
- Pour l’instant, Contoso déploie manuellement l’application web et le code de fonction à l’aide de Visual Studio.
- Les microservices sont déployés à l’aide d’un script PowerShell qui appelle des outils de ligne de commande de Kubernetes.

    ![Architecture du scénario](./media/contoso-migration-rebuild/architecture.png) 

  
### <a name="solution-review"></a>Examen de la solution

Contoso évalue la conception proposée en dressant la liste des avantages et des inconvénients.

**Considération** | **Détails**
--- | ---
**Avantages** | L’utilisation de solutions PaaS et sans serveur pour le déploiement de bout en bout réduit considérablement le temps de gestion pour Contoso.<br/><br/> La migration vers une architecture de microservice permet à Contoso d’étendre facilement la solution au fil du temps.<br/><br/> Les nouvelles fonctionnalités peuvent être mises en ligne sans interruption des bases de code de solutions existantes.<br/><br/> L’application web est configurée avec plusieurs instances sans point de défaillance unique.<br/><br/> La mise à l'échelle automatique sera activée afin que l’application puisse traiter les variations de volume du trafic.<br/><br/> Avec la migration vers des services PaaS, Contoso peut mettre hors service des solutions obsolètes s’exécutant sur le système d’exploitation Windows Server 2008 R2.<br/><br/> CosmosDB a une tolérance de panne intégrée qui ne nécessite aucune configuration par Contoso. Cela signifie que la couche Données n’est plus un point de basculement unique.
**Inconvénients** | Les conteneurs sont plus complexes que d’autres options de migration. La courbe d’apprentissage pourrait poser problème à Contoso.  Ils introduisent un nouveau niveau de complexité qui apporte beaucoup de valeur en dépit de la courbe.<br/><br/> L’équipe des opérations chez Contoso doit redoubler d’efforts pour comprendre et prendre en charge Azure, les conteneurs et les microservices pour l’application.<br/><br/> Contoso n’a pas encore totalement implémenté le DevOps pour la solution entière. Contoso doit y réfléchir pour le déploiement de services vers AKS, les fonctions et App Services.



### <a name="migration-process"></a>Processus de migration

1. Contoso approvisionne l’ACR, l’AKS et CosmosDB.
2. Ils approvisionnent l’infrastructure pour le déploiement, notamment Azure Web App, le compte de stockage, la fonction et l’API. 
3. Une fois l’infrastructure en place, ils vont construire leurs images conteneurs de microservices à l’aide d’Azure DevOps qui les envoie à l’ACR.
4. Contoso déploiera ces microservices vers l’AKS à l’aide d’un script PowerShell.
5. Enfin, ils déploieront la fonction Azure et l’application web.

    ![Processus de migration](./media/contoso-migration-rebuild/migration-process.png) 

### <a name="azure-services"></a>Services Azure

**Service** | **Description** | **Coût**
--- | --- | ---
[AKS](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Simplifie le déploiement, la gestion et les opérations de Kubernetes. Utilisez un service d'orchestration de conteneur Kubernetes entièrement géré.  | AKS est un service gratuit.  Vous payez uniquement pour les machines virtuelles, le stockage associé et la consommation des ressources réseau. [Plus d’informations](https://azure.microsoft.com/pricing/details/kubernetes-service/)
[Azure Functions](https://azure.microsoft.com/services/functions/) | Accélère le développement avec une expérience de calcul sans serveur pilotée par événements. Mettez à l’échelle à la demande.  | Payez uniquement pour les ressources consommées. L’offre est facturée sur la base des exécutions et de la consommation de ressources par seconde. [Plus d’informations](https://azure.microsoft.com/pricing/details/functions/)
[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | Stocke les images pour tous types de déploiements de conteneur. | Coût basé sur les fonctionnalités, le stockage et la durée d’utilisation. [Plus d’informations](https://azure.microsoft.com/pricing/details/container-registry/)
[Azure App Service](https://azure.microsoft.com/services/app-service/containers/) | Créez, déployez et mettez à l’échelle rapidement des applications web, mobiles et API de classe entreprise exécutables sur toute plateforme. | Les plans App Service sont facturés par seconde. [Plus d’informations](https://azure.microsoft.com/pricing/details/app-service/windows/)

## <a name="prerequisites"></a>Prérequis

Voici ce dont Contoso a besoin pour ce scénario :

**Configuration requise** | **Détails**
--- | ---
**Abonnement Azure** | Dans un article précédent, Contoso a créé des abonnements. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Si vous créez un compte gratuit, vous êtes l’administrateur de votre abonnement et pouvez effectuer toutes les actions.<br/><br/> Si vous utilisez un abonnement existant et que vous n’êtes pas l’administrateur, vous devez collaborer avec l’administrateur pour qu’il vous donne les autorisations Propriétaire ou Contributeur.
**Infrastructure Azure** | [Découvrez comment](contoso-migration-infrastructure.md) Contoso configure une infrastructure Azure.
**Prérequis pour développeur** | Contoso a besoin des outils suivants sur une station de travail de développeur :<br/><br/> - [Visual Studio 2017 Community Edition version 15.5](https://www.visualstudio.com/)<br/><br/> Charge de travail .NET activée.<br/><br/> [Git](https://git-scm.com/)<br/><br/> [Azure PowerShell](https://azure.microsoft.com/downloads/)<br/><br/> [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)<br/><br/> [Docker CE (Windows 10) ou Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/), configurés pour utiliser des conteneurs Windows.



## <a name="scenario-steps"></a>Étapes du scénario

Voici comment Contoso exécutera la migration :

> [!div class="checklist"]
> * **Étape 1 : Approvisionner AKS et ACR** : Contoso approvisionne le cluster AKS géré et le registre de conteneurs Azure à l’aide de PowerShell
> * **Étape 2 : Créer des conteneurs Docker** : ils configurent l’intégration continue pour les conteneurs Docker à l’aide d’Azure DevOps et les placent sur l’ACR.
> * **Étape 3 : Déployer les microservices principaux** : ils déploient le reste de l’infrastructure qu’utiliseront les microservices principaux.
> * **Étape 4 : Déployer l’infrastructure frontale** : ils déploient l’infrastructure frontale, dont le stockage blob pour les photos d’animaux, Cosmos DB et l’API Vision.
> * **Étape 5 : Migrer le serveur principal** : ils déploient les microservices et de les exécutent sur AKS pour migrer le serveur principal.
> * **Étape 6 : Publier le frontend** : ils publient l’application SmartHotel360 sur Azure App service, ainsi que l’application de fonction qui sera appelée par le service de gestion des animaux.



## <a name="step-1-provision-back-end-resources"></a>Étape 1 : Provisionner les ressources backend

Les administrateurs de Contoso exécutent un script de déploiement pour créer le cluster Kubernetes managé à l’aide d’AKS et d’Azure Container Registry (ACR).

- Les instructions de cette section utilisent le référentiel **SmartHotel360-Azure-backend**.
- Le dépôt GitHub **SmartHotel360-Azure-backend** contient tous les logiciels pour cette partie du déploiement.

### <a name="prerequisites"></a>Prérequis

1. Avant de commencer, les administrateurs de Contoso s’assurent que les logiciels requis sont installés sur la machine de développement qu’ils utilisent pour le déploiement.
2. Ils clonent le dépôt localement sur la machine de développement à l’aide de Git : **git clone https://github.com/Microsoft/SmartHotel360-Azure-backend.git**


### <a name="provision-aks-and-acr"></a>Provisionner AKS et ACR

Les administrateurs de Contoso effectuent le provisionnement comme suit :

1.  Ils ouvrent le dossier à l’aide de Visual Studio Code, et accèdent au répertoire **/deploy/k8s** qui contient le script **gen-aks-env.ps1**.
2. Ils exécutent le script pour créer le cluster Kubernetes managé à l’aide d’AKS et d’ACR.

    ![AKS](./media/contoso-migration-rebuild/aks1.png)
 
3.  Après avoir ouvert le fichier, ils mettent à jour le paramètre $location en définissant **eastus2**, puis enregistrent le fichier.

    ![AKS](./media/contoso-migration-rebuild/aks2.png)

4. Ils cliquent sur **Affichage** > **Terminal intégré** pour ouvrir le terminal intégré dans le Visual Studio Code.

    ![AKS](./media/contoso-migration-rebuild/aks3.png)

5. Dans le terminal intégré PowerShell, ils se connectent à Azure à l’aide de la commande Connect-AzureRmAccount. [En savoir plus](https://docs.microsoft.com/powershell/azure/get-started-azureps) sur la mise en route avec PowerShell.

    ![AKS](./media/contoso-migration-rebuild/aks4.png)

6. Ils authentifient Azure CLI en exécutant la commande **az login**, et en suivant les instructions pour s’authentifier à l’aide de leur navigateur web. [En savoir plus](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) sur la connexion avec Azure CLI.

    ![AKS](./media/contoso-migration-rebuild/aks5.png)

7. Ils exécutent la commande suivante, en passant le nom de groupe de ressources ContosoRG, le nom du cluster AKS smarthotel-aks-eus2, et le nouveau nom de registre.

    ```
    .\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2
    ```
    ![AKS](./media/contoso-migration-rebuild/aks6.png)

8. Azure crée un autre groupe de ressources contenant les ressources pour le cluster AKS.

    ![AKS](./media/contoso-migration-rebuild/aks7.png)

9. Une fois le déploiement terminé, ils installent l’outil en ligne de commande **kubectl**. L’outil est déjà installé sur le CloudShell Azure.

    **az aks install-cli**

10. Ils vérifient la connexion au cluster en exécutant la commande **kubectl get nodes**. Le nœud porte le même nom que la machine virtuelle dans le groupe de ressources créé automatiquement.

    ![AKS](./media/contoso-migration-rebuild/aks8.png)

11. Ils exécutent la commande suivante pour démarrer le tableau de bord Kubernetes : 

    **az aks browse --resource-group ContosoRG --name smarthotelakseus2**

12. Un onglet de navigateur s’ouvre sur le tableau de bord. Il s’agit d’une connexion par tunnel utilisant Azure CLI. 

    ![AKS](./media/contoso-migration-rebuild/aks9.png)




## <a name="step-2-configure-the-back-end-pipeline"></a>Étape 2 : Configurer le pipeline backend

### <a name="create-an-azure-devops-project-and-build"></a>Créer un projet Azure DevOps et une build

Contoso crée un projet Azure DevOps et configure une build CI pour créer le conteneur, puis transmet celui-ci à l’ACR. Les instructions de cette section utilisent le dépôt [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend).

1. À partir de visualstudio.com, ils créent une organisation (**contosodevops360.visualstudio.com**), puis la configurent pour utiliser Git.

2. Ils créent un projet (**SmartHotelBackend**) utilisant Git pour la gestion de version, et Agile pour le flux de travail.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts1.png) 


3. Ils importent le [dépôt GitHub](https://github.com/Microsoft/SmartHotel360-Azure-backend.git).

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts2.png)
    
4. Dans **Pipelines**, ils cliquent sur **Générer** et créent un pipeline en utilisant Azure Repos Git comme source, à partir du dépôt. 

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts3.png)

6. Ils sélectionnent l’option permettant de démarrer avec un projet vide.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts4.png)  

7. Ils sélectionnent **Hosted Linux Preview** (Préversion Linux hébergée) pour le pipeline de build.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts5.png) 
 
8. Dans **Phase 1**, ils ajoutent une tâche **Docker Compose**. Cette tâche génère le Docker compose.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts6.png) 

9. Ils répètent l’opération pour ajouter une autre tâche **Docker Compose**. Celle-ci envoie les conteneurs à l’ACR.

     ![Azure DevOps](./media/contoso-migration-rebuild/vsts7.png) 

8. Ils sélectionnent la première tâche (générer) et configurent la build avec l’abonnement, l’autorisation et l’ACR Azure. 

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts8.png)

9. Ils spécifient le chemin du fichier **docker-compose.yaml** dans le dossier **src** du dépôt. Ils sélectionnent l’option permettant de générer des images de service et incluent la dernière étiquette. Quand l’action devient **Build service images** (Générer les images de service), le nom de la tâche Azure DevOps devient **Build services automatically** (Générer les services automatiquement).

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts9.png)

10. Maintenant, ils configurent la deuxième tâche de Docker (envoyer). Ils sélectionnent l’abonnement et l’ACR **smarthotelacreus2**. 

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts10.png)

11. Là encore, ils définissent le fichier docker-compose.yaml, puis sélectionnent **Push service images** (Envoyer des images de service) et incluent la dernière balise. Quand l’action devient **Push service images** (Envoyer des images de service), le nom de la tâche Azure DevOps devient **Push services automatically** (Envoyer les services automatiquement).

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts11.png)

12. Une fois les tâches Azure DevOps configurées, Contoso enregistre le pipeline de build et démarre le processus de génération.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts12.png)

13. Ils cliquent sur la tâche de build pour vérifier la progression.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts13.png)

14. Une fois la build terminée, l’ACR montre les nouveaux référentiels qui sont remplis des conteneurs utilisés par les microservices.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts14.png)


### <a name="deploy-the-back-end-infrastructure"></a>Déployer l’infrastructure backend

Une fois le cluster AKS créé et les images Docker générées, les administrateurs de Contoso déploient le reste de l’infrastructure qu’exploiteront les microservices principaux.

- Les instructions de cette section utilisent le référentiel [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend).
- Le dossier **/deploy/k8s/arm** contient un script unique pour créer tous les éléments. 

Ils déploient comme suit :

1. Ils ouvrent une invite de commandes de développeur et utilisent la commande az login pour l’abonnement Azure.
2. Ils utilisent le fichier deploy.cmd pour déployer les ressources Azure dans le groupe de ressources ContosoRG et la région EUS2, en tapant la commande suivante :

    **.\deploy.cmd azuredeploy ContosoRG -c eastus2**

    ![Déployer le serveur principal](./media/contoso-migration-rebuild/backend1.png)

2. Dans le portail Azure, ils capturent la chaîne de connexion pour chaque base de données en vue d’une utilisation ultérieure.

    ![Déployer le serveur principal](./media/contoso-migration-rebuild/backend2.png)

### <a name="create-the-back-end-release-pipeline"></a>Créer le pipeline de mise en production backend

Les administrateurs de Contoso doivent à présent procéder comme suit :

- Déployer le contrôleur d’entrée NGINX pour autoriser le trafic entrant vers les services.
- Déployer les microservices sur le cluster AKS.
- Dans un premier temps, ils mettent à jour les chaînes de connexion en fonction des microservices à l’aide d’Azure DevOps. Ensuite, ils configurent un nouveau pipeline de mise en production Azure DevOps pour déployer les microservices.
- Les instructions de cette section utilisent le référentiel [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend).
- Notez que certains paramètres de configuration (par exemple Active Directory B2C) ne sont pas évoqués dans cet article. Pour plus d’informations sur ces paramètres, consultez le dépôt.

Ils créent le pipeline :

1. À l’aide de Visual Studio, ils mettent à jour le fichier **/deploy/k8s/config_local.yml** avec les informations de connexion de base de données qu’ils ont notées précédemment.

    ![Connexions de base de données](./media/contoso-migration-rebuild/back-pipe1.png)

2. Ils ouvrent Azure DevOps et dans le projet SmartHotel360, dans **Mises en production**, ils cliquent sur **+Nouveau pipeline**.

    ![Nouveau pipeline](./media/contoso-migration-rebuild/back-pipe2.png)

3. Ils cliquent sur **Projet vide** pour démarrer le pipeline sans modèle.
4. Ils fournissent les noms de la phase et du pipeline.

      ![Nom de la phase](./media/contoso-migration-rebuild/back-pipe4.png)

      ![Nom du pipeline](./media/contoso-migration-rebuild/back-pipe5.png)

5. Ils ajoutent un artefact.

     ![Ajoutez un artefact](./media/contoso-migration-rebuild/back-pipe6.png)

6. Ils sélectionnent **Git** comme type de source, puis spécifient le projet, la source et la branche principale pour l’application SmartHotel360.

    ![Paramètres de l’artefact](./media/contoso-migration-rebuild/back-pipe7.png)

7. Ils cliquent sur le lien de tâche.

    ![Lien de tâche](./media/contoso-migration-rebuild/back-pipe8.png)

8. Ils ajoutent une nouvelle tâche Azure PowerShell afin de pouvoir exécuter un script PowerShell dans un environnement Azure.

    ![PowerShell dans Azure](./media/contoso-migration-rebuild/back-pipe9.png)

9. Ils sélectionnent l’abonnement Azure pour la tâche, puis sélectionnent le script **deploy.ps1** à partir du dépôt Git.

    ![Exécuter le script](./media/contoso-migration-rebuild/back-pipe10.png)


10. Ils ajoutent des arguments au script. Le script supprimera tout le contenu du cluster (à l’exception de **l’entrée** et du **contrôleur d’entrée**) et déploiera les microservices.

    ![Arguments de script](./media/contoso-migration-rebuild/back-pipe11.png)

11. Ils définissent la version Azure PowerShell par défaut sur la dernière version, puis enregistrent le pipeline.
12. Ils reviennent à la page **Mise en production** et créent manuellement une mise en production.

    ![Nouvelle mise en production](./media/contoso-migration-rebuild/back-pipe12.png)

13. Ils cliquent sur la mise en production après l’avoir créée et, dans **Actions**, ils cliquent sur **Déployer**.

      ![Déployer la mise en production](./media/contoso-migration-rebuild/back-pipe13.png)  

14. Quand le déploiement est terminé, ils exécutent la commande suivante pour vérifier l’état des services, à l’aide d’Azure Cloud Shell : **kubectl get services**.


## <a name="step-3-provision-front-end-services"></a>Étape 3 : Provisionner les services frontaux

Les administrateurs de Contoso doivent déployer l’infrastructure qui sera utilisée par les applications frontales. Ils créent un conteneur de stockage d’objets blob pour stocker les images d’animaux, la base de données Cosmos pour stocker les documents contenant les informations sur les animaux, et l’API Vision pour le site web. 

Les instructions de cette section utilisent le dépôt [SmartHotel360-public-web](https://github.com/Microsoft/SmartHotel360-public-web).

### <a name="create-blob-storage-containers"></a>Créer des conteneurs de Stockage Blob

1.  Dans le portail Azure, ils ouvrent le compte de stockage créé et cliquent sur **Objets blob**.
2.  Ils créent un conteneur (**Pets**) avec le niveau d’accès public défini sur conteneur. Les utilisateurs chargeront leurs photos d’animaux sur ce conteneur.

    ![Objet blob de stockage](./media/contoso-migration-rebuild/blob1.png)

3. Ils créent un deuxième conteneur nommé **Settings** (Paramètres). Un fichier contenant tous les paramètres de l’application frontale sera placé dans ce conteneur.

    ![Objet blob de stockage](./media/contoso-migration-rebuild/blob2.png)

4. Ils capturent les détails d’accès au compte de stockage dans un fichier texte pour référence ultérieure.

    ![Objet blob de stockage](./media/contoso-migration-rebuild/blob2.png)

### <a name="provision-a-cosmos-database"></a>Approvisionner une base de données Cosmos

Les administrateurs de Contoso provisionnent une base de données Cosmos à utiliser pour les informations sur les animaux.

1. Ils créent une **Azure Cosmos DB** dans la Place de marché Azure.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos1.png)

2. Ils spécifient un nom (**contosomarthotel**), sélectionnent l’API SQL, et la placent dans le groupe de ressources de production ContosoRG, dans la région principale USA Est 2.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos2.png)

3. Ils ajoutent une nouvelle collection à la base de données, avec la capacité et le débit par défaut.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos3.png)


4. Ils notent les informations de connexion à la base de données pour référence ultérieure. 

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos4.png)


### <a name="provision-computer-vision"></a>Approvisionner l’API Vision par ordinateur

Les administrateurs de Contoso provisionnent l’API Vision par ordinateur. L’API est appelée par la fonction pour évaluer les images chargées par les utilisateurs.

1. Ils créent une instance de l’**API Vision par ordinateur** dans la Place de marché Azure.

     ![Vision par ordinateur](./media/contoso-migration-rebuild/vision1.png)

2. Ils approvisionnent l’API (**smarthotelpets**) dans le groupe de ressources de production ContosoRG, dans la région principale USA Est 2.

    ![Vision par ordinateur](./media/contoso-migration-rebuild/vision2.png)

3. Ils enregistrent les paramètres de connexion de l’API dans un fichier texte pour référence ultérieure.

     ![Vision par ordinateur](./media/contoso-migration-rebuild/vision3.png)


### <a name="provision-the-azure-web-app"></a>Provisionner l’application web Azure

Les administrateurs de Contoso provisionnent l’application web à l’aide du portail Azure.


1. Ils sélectionnent **Application web** dans le portail.

    ![Application web](media/contoso-migration-rebuild/web-app1.png)

2. Ils fournissent un nom pour l’application (**smarthotelcontoso**), exécutent celle-ci sur Windows, puis la placent dans le groupe de ressources de production **ContosoRG**. Ils créent une instance d’Application Insights pour la supervision de l’application.

    ![Nom de l’application web](media/contoso-migration-rebuild/web-app2.png)

3. Cela fait, ils accèdent à l’adresse de l’application pour vérifier si celle-ci a été correctement créée.

4. À présent, dans le portail Azure, ils créent un emplacement de préproduction pour le code. Le pipeline effectuera le déploiement sur cet emplacement. Ainsi, le code n’est pas mis en production tant que les administrateurs n’effectuent pas une mise en production.

    ![Emplacement de préproduction pour l’application web](media/contoso-migration-rebuild/web-app3.png)



### <a name="provision-the-azure-function-app"></a>Provisionner l’application de fonction Azure

Dans le portail Azure, les administrateurs de Contoso provisionnent l’application de fonction.

1. Ils sélectionnent **Application de fonction**.

    ![Créer une application de fonction](./media/contoso-migration-rebuild/function-app1.png)

2. Ils fournissent un nom d’application (**smarthotelpetchecker**). Ils placent l’application dans le groupe de ressources de production **ContosoRG**. Ils définissent le lieu d’hébergement sur **Plan Consommation** et placent l’application dans la région USA Est 2. Un compte de stockage est créé, ainsi qu’une instance d’Application Insights pour la supervision.

    ![Paramètres Function App](./media/contoso-migration-rebuild/function-app2.png)


3. Une fois l’application déployée, ils accèdent à l’adresse de l’application pour vérifier si celle-ci a été correctement créée.


## <a name="step-4-set-up-the-front-end-pipeline"></a>Étape 4 : Configurer le pipeline frontal

Les administrateurs de Contoso créent deux projets différents pour le site frontal. 

1. Dans Azure DevOps, ils créent un projet **SmartHotelFrontend**.

    ![Projet frontal](./media/contoso-migration-rebuild/function-app1.png)

2. Ils importent le dépôt Git [SmartHotel360 front-end](https://github.com/Microsoft/SmartHotel360-public-web.git) dans le nouveau projet.
3. Pour l’application de fonction, ils créent un autre projet Azure DevOps (SmartHotelPetChecker), puis y importent le dépôt Git [PetChecker](https://github.com/Microsoft/SmartHotel360-PetCheckerFunction ).

### <a name="configure-the-web-app"></a>Configurer l’application web

À présent, les administrateurs de Contoso configurent l’application web pour utiliser des ressources Contoso.

1. Ils se connectent au projet Azure DevOps, puis clonent le dépôt localement sur la machine de développement.
2. Dans Visual Studio, ils ouvrent le dossier pour afficher tous les fichiers contenus dans le référentiel.

    ![Fichiers du dépôt](./media/contoso-migration-rebuild/configure-webapp1.png)

3. Ils mettent à jour la configuration selon les besoins.

    - Lorsque l’application web démarre, elle recherche le paramètres d’application **SettingsUrl**.
    - Cette variable doit contenir une URL pointant vers un fichier de configuration.
    - Par défaut, le paramètre utilisé est un point de terminaison public.

4.  Ils mettent à jour le fichier /config-sample.json/sample.json.

    - Il s’agit du fichier de configuration pour le web en cas d’utilisation d’un point de terminaison public.
    - Ils modifient les sections **urls** et **pets_config** avec les valeurs de points de terminaison, de comptes de stockage et de base de données Cosmos de l’API AKS.
    - Les URL doivent correspondre au nom DNS de la nouvelle application web que Contoso va créer.
    - Pour Contoso, il s’agit de **smarthotelcontoso.eastus2.cloudapp.azure.com**.

    ![Paramètres Json](./media/contoso-migration-rebuild/configure-webapp2.png)

5. Une fois le fichier mis à jour, ils le renomment **smarthotelsettingsurl**, puis le chargent sur l’objet blob de stockage créé précédemment.

    ![Renommer et charger](./media/contoso-migration-rebuild/configure-webapp3.png)

6. Ils cliquent sur le fichier pour obtenir l’URL. Cette URL est utilisée par l’application quand celle-ci extrait les fichiers de configuration.

    ![URL de l’application](./media/contoso-migration-rebuild/configure-webapp4.png)

7. Dans le fichier **appsettings.Production.json**, ils mettent à jour **SettingsURL** sur l’URL du nouveau fichier.

    ![Mettre à jour l’URL](./media/contoso-migration-rebuild/configure-webapp5.png)

### <a name="deploy-the-website-to-the-azure-app-service"></a>Déployer le site web sur Azure App Service

Les administrateurs de Contoso peuvent maintenant publier le site web.


1. Ils ouvrent Azure DevOps et, dans le projet **SmartHotelFrontend**, dans **Builds et mises en production**, ils cliquent sur **+Nouveau pipeline**.
2. Ils sélectionnent **Azure DevOps Git** comme source.
3. Ils sélectionnent le modèle **ASP.NET Core**.
4. Ils passent en revue le pipeline et vérifient que les options **Publier des projets web** et **Compresser les projets publiés** sont sélectionnés.

    ![Paramètres du pipeline](./media/contoso-migration-rebuild/vsts-publishfront2.png)

5. Dans **Déclencheurs**, ils activent l’intégration continue et ajoutent la branche maître. Ainsi, chaque fois que la solution a du nouveau code validé dans la branche principale, le pipeline de build démarre.

    ![Intégration continue](./media/contoso-migration-rebuild/vsts-publishfront3.png)

6. Ils cliquent sur **Enregistrer et mettre en file d’attente** pour démarrer une génération.
7. Une fois la génération terminée, ils configurent un pipeline de mise en production à l’aide du **Déploiement d’Azure App Service**.
8. Ils fournissent **Staging** (Préproduction) comme nom de phase.

    ![Nom de l’environnement](./media/contoso-migration-rebuild/vsts-publishfront4.png)

9. Ils ajoutent un artefact, puis sélectionnent la build qu’ils viennent de configurer.

     ![Ajoutez un artefact](./media/contoso-migration-rebuild/vsts-publishfront5.png)

10. Ils cliquent sur l’icône représentant un éclair qui se trouve sur l’artefact, puis activent le déclencheur de déploiement continu.

    ![Déploiement continu](./media/contoso-migration-rebuild/vsts-publishfront6.png)
11. Dans **Environnement**, ils cliquent sur **1 travail, 1 tâche** sous **Préproduction**.
12. Après avoir sélectionné l’abonnement et le nom de l’application, ils ouvrent la tâche **Déployer Azure App Service**. Le déploiement est configuré pour utiliser l’emplacement de déploiement de **préproduction**. Cette opération génère automatiquement le code en vue de sa revue et de son approbation dans cet emplacement.

     ![Emplacement](./media/contoso-migration-rebuild/vsts-publishfront7.png)

13. Dans le **Pipeline**, ils ajoutent une nouvelle phase.

    ![Nouvel environnement](./media/contoso-migration-rebuild/vsts-publishfront8.png)

14. Ils sélectionnent **Déploiement d’Azure App Service sur un emplacement** et nomment l’environnement **Prod**.
15. Ils cliquent sur **1 travail, 2 tâches**, puis sélectionnent l’abonnement, le nom de l’App Service et l’emplacement de **préproduction**.

    ![Nom de l’environnement](./media/contoso-migration-rebuild/vsts-publishfront10.png)

16. Ils suppriment du pipeline le **déploiement d’Azure App Service sur l’emplacement**. Il y avait été placé au cours des étapes précédentes.

    ![Supprimer du pipeline](./media/contoso-migration-rebuild/vsts-publishfront11.png)

13. Ils enregistrent le pipeline. Sur le pipeline, ils cliquent sur **Conditions de postdéploiement**.

    ![Postdéploiement](./media/contoso-migration-rebuild/vsts-publishfront12.png)

14. Ils activent **Approbations de postdéploiement**, puis ajoutent un responsable du développement en tant qu’approbateur.

    ![Approbations de postdéploiement](./media/contoso-migration-rebuild/vsts-publishfront13.png)

15. Dans le pipeline de build, ils lancent manuellement une build. Cette opération déclenche le nouveau pipeline de mise en production, qui déploie le site sur l’emplacement de préproduction. Pour Contoso, l’URL de l’emplacement est **https://smarthotelcontoso-staging.azurewebsites.net/**.
16. Une fois la build terminée et la mise en production déployée sur l’emplacement, Azure DevOps envoie un e-mail au responsable du développement pour recueillir son approbation.
17. Le responsable du développement clique sur **Afficher l’approbation** et peut approuver ou rejeter la demande dans le portail Azure DevOps.

    ![E-mail d’approbation](./media/contoso-migration-rebuild/vsts-publishfront14.png)

16. Le responsable écrit un commentaire, puis approuve. Cette opération démarre l’échange des emplacements de **préproduction** et de **production**, puis place la build en production.

    ![Approuver et échanger](./media/contoso-migration-rebuild/vsts-publishfront15.png)

17. Le pipeline termine l’échange.

    ![Échange effectué](./media/contoso-migration-rebuild/vsts-publishfront16.png)

18. L’équipe vérifie l’emplacement de **production** pour s’assurer que l’application web est en production à l’adresse **https://smarthotelcontoso.azurewebsites.net/**.


### <a name="deploy-the-petchecker-function-app"></a>Déployer l’application de fonction PetChecker

Les administrateurs de Contoso déploient l’application comme suit.

1. Ils clonent le dépôt localement sur la machine de développement en se connectant au projet Azure DevOps.
2. Dans Visual Studio, ils ouvrent le dossier pour afficher tous les fichiers contenus dans le référentiel.
3. Ils ouvrent le fichier **src/PetCheckerFunction/local.settings.json**, puis ajoutent les paramètres d’application pour le stockage, la base de données Cosmos et l’API Vision par ordinateur.

    ![Déployer la fonction](./media/contoso-migration-rebuild/function5.png)

4. Ils valident le code, puis le synchronisent sur Azure DevOps, en envoyant (push) leurs modifications.
5. Ils ajoutent un nouveau pipeline de build, puis sélectionnent **Azure DevOps Git** comme source.
6. Ils sélectionnent le modèle **ASP.NET Core (.NET Framework)**.
7. Ils acceptent les valeurs par défaut pour le modèle.
8. Dans **Déclencheurs**, ils sélectionnent **Activer l’intégration continue**, puis cliquent sur **Enregistrer et mettre en file d’attente** pour démarrer une build.
9. Une fois la build terminée, ils génèrent un pipeline de mise en production et y ajoutent le **déploiement d’Azure App Service sur un emplacement**.
10. Ils nomment l’environnement **Prod**, puis sélectionnent l’abonnement. Ils définissent le **Type d’application** sur **Application de fonction** et nomment l’App Service **smarthotelpetchecker**.

    ![Conteneur de fonctions](./media/contoso-migration-rebuild/petchecker2.png)

11. Ils ajoutent un artefact **Build**.

    ![Artefact](./media/contoso-migration-rebuild/petchecker3.png)

12. Ils activent **Déclencheur de déploiement continu**, puis cliquent sur **Enregistrer**.
13. Ils cliquent sur **Mettre en file d’attente une nouvelle build** pour exécuter le pipeline CI/CD complet.
14. Une fois déployée, la fonction apparaît dans le portail Azure, avec l’état **En cours d’exécution**.

    ![Déployer la fonction](./media/contoso-migration-rebuild/function6.png)


7. Ils accèdent à l’application pour vérifier que l’intelligence artificielle de Pet Checker (Vérificateur d’animaux) fonctionne comme prévu sur [http://smarthotel360public.azurewebsites.net/Pets](http://smarthotel360public.azurewebsites.net/Pets).
8. Ils cliquent sur l’avatar pour charger une image.

    ![Déployer la fonction](./media/contoso-migration-rebuild/function7.png)

9. La première photo qu'ils souhaitent vérifier est celle d’un petit chien.

    ![Déployer la fonction](./media/contoso-migration-rebuild/function8.png)

10. L’application renvoie un message d’acceptation.

    ![Déployer la fonction](./media/contoso-migration-rebuild/function9.png)






## <a name="review-the-deployment"></a>Examiner le déploiement

Une fois les ressources migrées dans Azure, Contoso doit rendre la nouvelle infrastructure entièrement opérationnelle et la sécuriser.

### <a name="security"></a>Sécurité

- Contoso doit s’assurer que les nouvelles bases de données sont sécurisées. [Plus d’informations](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)
- L’application doit être mise à jour pour utiliser le protocole SSL avec des certificats. L’instance de conteneur doit être redéployée pour répondre sur le port 443.
- Contoso devrait envisager d’utiliser Key Vault pour protéger les secrets de ses applications Service Fabric. [Plus d’informations](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management)

### <a name="backups-and-disaster-recovery"></a>Sauvegardes et récupération d’urgence

- Contoso doit examiner les exigences de sauvegarde pour Azure SQL Database. [Plus d’informations](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)
- Contoso devrait envisager d’implémenter des groupes de basculement SQL afin de fournir un basculement régional pour la base de données. [Plus d’informations](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)
- Contoso peut tirer parti de la géoréplication pour la référence (SKU) premium d’ACR. [Plus d’informations](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)
- Cosmos DB effectue des sauvegardes automatiquement. Contoso peut [en savoir plus](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore) sur ce processus.

### <a name="licensing-and-cost-optimization"></a>Gestion des licences et optimisation des coûts

- Une fois toutes les ressources déployées, Contoso doit affecter des balises Azure en fonction de la [planification de son infrastructure](contoso-migration-infrastructure.md#set-up-tagging).
- Toutes les licences sont intégrées dans le coût des services PaaS que Contoso consomme. Cela sera déduit de l’Accord Entreprise.
- Contoso va activer Azure Cost Management sous licence de Cloudyn, une filiale de Microsoft. Il s’agit d’une solution de gestion des coûts multicloud qui aide à utiliser et à gérer Azure ainsi que d’autres ressources cloud.  [En savoir plus](https://docs.microsoft.com/azure/cost-management/overview) sur Azure Cost Management.

## <a name="conclusion"></a>Conclusion

Dans cet article, Contoso regénère l’application SmartHotel360 dans Azure. La machine virtuelle frontale de l’application locale est regénérée sur Web Apps dans Azure App Services. Le backend de l'application est généré à l’aide de microservices déployés sur des conteneurs gérés par Azure Kubernetes Service (AKS). Contoso a amélioré les fonctionnalités de l’application avec une application de photo d’animaux.




