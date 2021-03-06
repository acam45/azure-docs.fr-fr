---
title: Création d’une solution d’application géolocalisée avec Azure et Azure Stack | Microsoft Docs
description: Découvrez comment créer une solution d’application géolocalisée avec Azure et Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 19e7506dac82e4d12d5aecbdb5ae1c14fb944c29
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961533"
---
# <a name="tutorial-create-a-geo-distributed-app-solution-with-azure-and-azure-stack"></a>Didacticiel : Création d’une solution d’application géolocalisée avec Azure et Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Découvrez comment diriger le trafic vers des points de terminaison spécifiques en fonction de différentes mesures à l’aide du modèle d’applications géolocalisées. En créant un profil Traffic Manager avec configuration du routage et du point de terminaison basée sur la géolocalisation, vous êtes certain que les informations sont dirigées vers les points de terminaison en fonction des exigences régionales, des réglementations organisationnelles et internationales et de vos besoins en matière de données.

Dans ce tutoriel, vous créez un exemple d’environnement pour :

> [!div class="checklist"]
> - Créer une application géolocalisée.
> - Utiliser Traffic Manager pour cibler votre application.

## <a name="use-the-geo-distributed-apps-pattern"></a>Utiliser le modèle d’application géolocalisée.

Avec le modèle d’application géolocalisée, l’application couvre plusieurs régions. Vous pouvez utiliser le cloud public par défaut, mais certains utilisateurs ont besoin que leurs données restent dans leur région. Dans ce cas, orientez les utilisateurs vers le cloud le plus approprié en fonction de leurs besoins.

### <a name="issues-and-considerations"></a>Problèmes et considérations

#### <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité

La solution que vous allez créer avec ce didacticiel ne prend pas en charge l’extensibilité. Toutefois, utilisée en combinaison avec d’autres technologies et solutions Azure et locales, elle s’adapte à ces exigences d’extensibilité. Pour plus d’informations sur la création d’une solution hybride avec mise à l’échelle automatique via Traffic Manager, consultez [Create cross-cloud scaling solutions with Azure](azure-stack-solution-cloud-burst.md) (Création de solutions de mise à l’échelle inter-cloud avec Azure).

#### <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Cette solution ne prend pas directement en charge les besoins de disponibilité. Toutefois, il existe également des solutions et des technologies locales et Azure que vous pouvez implémenter dans cette solution pour garantir la haute disponibilité de tous les composants impliqués.

### <a name="when-to-use-this-pattern"></a>Quand utiliser ce modèle

- Les succursales internationales de votre organisation exigent des politiques de sécurité et de distribution régionales personnalisées.

- Chacun des bureaux de votre organisation extrait des données sur les employés, l’entreprise et les installations, nécessitant la création de rapports d’activité conformes aux réglementations locales et aux fuseaux horaires.

- Les exigences à grande échelle peuvent être satisfaites par la mise à l’échelle horizontale des applications, avec plusieurs déploiements dans une seule ou plusieurs régions, pour gérer les charges extrêmes.

### <a name="planning-the-topology"></a>Planification de la topologie

Avant de créer une empreinte d’application distribuée, il est préférable de disposer des éléments d’informations suivants :

-   **Domaine personnalisé pour l’application :** quel est le nom de domaine personnalisé que les utilisateurs utiliseront pour accéder à l’application ? Pour l’exemple d’application, le nom de domaine personnalisé est *www.scalableasedemo.com.*

-   **Domaine Traffic Manager :** vous devez choisir un nom de domaine au moment de la création d’un [profil Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-manage-profiles). Ce nom est associé au suffixe *trafficmanager.net* pour enregistrer une entrée de domaine gérée par Traffic Manager. Dans l’exemple d’application, le nom choisi est *scalable-ase-demo*. Par conséquent, le nom de domaine complet géré par Traffic Manager est *scalable-ase-demo.trafficmanager.net*.

-   **Stratégie de mise à l’échelle de l’empreinte de l’application :** l’empreinte de l’application sera-t-elle distribuée sur plusieurs environnements App Service dans une seule région ? Plusieurs régions ? Combinaison des deux approches ? La décision doit être prise en fonction des attentes depuis l’emplacement d’origine du trafic du client, ainsi que sur la manière dont peut évoluer le reste de l’application prenant en charge l’infrastructure principale. Par exemple, avec une application à 100 % sans état, une application peut être adaptée à très grande échelle à l’aide d’une combinaison de plusieurs environnements App Service, puis multipliée par les environnements App Service déployés dans plusieurs régions Azure. Avec plus de 15 régions Azure globales disponibles, les clients peuvent véritablement créer une empreinte d’application à échelle mondiale. Pour l’exemple d’application utilisé pour cet article, trois environnements App Service ont été créés dans une seule région Azure (USA Centre Sud).

-   **Convention de dénomination pour les environnements App Service :** chaque environnement App Service exige un nom unique. Au-delà d’un ou deux environnements App Service, une convention d’affectation de noms identifiant chaque environnement App Service peut s’avérer utile. Pour l’exemple d’application, une convention d’affectation de noms simple a été utilisée. Les noms des trois environnements App Service sont respectivement *fe1ase*, *fe2ase* et *fe3ase*.

-   **Convention d’affectation de noms pour les applications :** comme plusieurs instances de l’application vont être déployées, un nom est requis pour chacune d’entre elles. Avec les environnements App Service, le même nom d’application peut être utilisé sur plusieurs environnements App Service. Étant donné que chaque environnement App Service comporte un suffixe de domaine unique, les développeurs peuvent choisir d’utiliser le même nom d’application dans chaque environnement. Par exemple, un développeur peut avoir des applications nommées comme suit : *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*, etc. Pour l’application dans ce scénario, chaque instance de l’application a un nom unique. Les noms d’instance application utilisés sont *webfrontend1*, *webfrontend2* et *webfrontend3*.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack est une extension d’Azure. Azure Stack apporte l’agilité et l’innovation du cloud computing à votre environnement local et active le seul cloud hybride qui vous permet de créer et de déployer des applications hybrides en tous lieux.  
> 
> Le livre blanc [Design Considerations for Hybrid Applications](https://aka.ms/hybrid-cloud-applications-pillars) (Étude des conceptions pour les applications hybrides) se penche sur les fondements de la qualité logicielle (sélection élective, extensibilité, disponibilité, résilience, facilité de gestion et sécurité) en matière de conception, de déploiement et d’exploitation des applications hybrides. Les considérations de conception vous aident à optimiser la conception des applications hybrides, en réduisant les risques dans les environnements de production.

## <a name="part-1-create-a-geo-distributed-app"></a>Partie 1 : Création d’une application géolocalisée

Dans cette partie, vous allez créer une application web.

> [!div class="checklist"]
> - Créer des applications web et publier
> - Ajouter du code au projet VSTS
> - Pointez la génération de l’application sur plusieurs cibles du cloud.
> - Gérer et configurer le processus CD

### <a name="prerequisites"></a>Prérequis

Un abonnement Azure et l’installation d’Azure Stack sont requis.

### <a name="geo-distributed-app-steps"></a>Procédure de création d’une application géolocalisée

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Obtenir un domaine personnalisé et configurer DNS

Mettez à jour le fichier de zone DNS pour Créer des applications web et publier le domaine. Azure AD peut ensuite vérifier la propriété du nom de domaine personnalisé. Utilisez [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) pour les enregistrements DNS Azure/Office 365/externes dans Azure, ou ajouter l’entrée DNS à [un autre bureau d’enregistrement DNS](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1. Enregistrez un domaine personnalisé avec un bureau d’enregistrement public.

2. Connectez-vous au Bureau d’enregistrement des noms de domaine pour le domaine. Un administrateur approuvé devra peut-être effectuer les mises à jour DNS.

3. Mettez à jour le fichier de zone DNS du domaine en ajoutant l’entrée DNS fournie par Azure AD. Elle ne modifie pas les comportements comme le routage du courrier ou l’hébergement web.

### <a name="create-web-apps-and-publish"></a>Créer des applications web et publier

Configurez la CI/CD hybride pour déployer Web App sur Azure et Azure Stack, et pour envoyer (push) les modifications sur les deux clouds.

> [!Note]  
> Vous avez besoin d’Azure Stack avec les images appropriées syndiquées pour s’exécuter (Windows Server et SQL), et App Service doit être déployé. Pour l’opérateur Azure Stack, consultez la section «[ Avant de commencer avec App Service sur Azure Stack ](/articles/azure-stack/azure-stack-app-service-before-you-get-started)» dans la documentation d’App Service.

#### <a name="add-code-to-vsts-project"></a>Ajouter du code au projet VSTS

1. Connectez-vous à Visual Studio avec un **compte disposant des droits de création de projet** sur VSTS.

    Vous pouvez appliquer l’intégration continue/la livraison continue (CI/CD) hybride au code d’application et au code d’infrastructure. Utilisez les [modèles Azure Resource Manager](https://azure.microsoft.com/resources/templates/) pour le développement cloud hébergé et privé.

    ![Alt text](media\azure-stack-solution-geo-distributed\image1.JPG)

2. **Clonez le référentiel** en créant et en ouvrant l’application web par défaut.

    ![Alt text](media\azure-stack-solution-geo-distributed\image2.png)

### <a name="create-web-app-deployment-in-both-clouds"></a>Création d’un déploiement d’application web dans les deux clouds

1.  Modifiez le fichier **WebApplication.csproj** : sélectionnez **Runtimeidentifier** et ajoutez **win10-x64**. (Consultez la documentation sur le [déploiement autonome](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd).)

    ![Alt text](media\azure-stack-solution-geo-distributed\image3.png)

1.  **Archivez le code dans VSTS** avec Team Explorer.

2.  Vérifiez que le **code d’application** a été archivé dans Visual Studio Team Services.

### <a name="create-the-build-definition"></a>Créer la définition de build

1. **Connectez-vous à VSTS** pour vérifier la possibilité de créer des définitions de build.

2. Ajoutez le code **-r win10-x64**. Cette action est nécessaire pour déclencher un déploiement autonome avec .NET Core.

    ![Alt text](media\azure-stack-solution-geo-distributed\image4.png)

3. **Exécutez la build**. Le processus de [build de déploiement autonome](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) publie des artefacts qui peuvent s’exécuter sur Azure et Azure Stack.

**Utilisation d’un agent hébergé sur Azure**

L’utilisation d’un agent hébergé dans VSTS est une option pratique pour créer et déployer des applications web. La maintenance et les mises à niveau sont effectuées automatiquement par Microsoft Azure, ce qui permet le développement, le test et le déploiement continus et sans interruption.

### <a name="manage-and-configure-the-cd-process"></a>Gérer et configurer le processus CD

Visual Studio Team Services (VSTS) et Team Foundation Server (TFS) fournissent un pipeline hautement configurable et gérable pour des mises en production sur plusieurs environnements, comme des environnements de développement, de préproduction, de contrôle qualité et de production, avec des demandes d’approbation à des étapes spécifiques.

#### <a name="create-release-definition"></a>Créer une définition de mise en production


![Alt text](media\azure-stack-solution-geo-distributed\image5.png)

1.  Sélectionnez le bouton **plus** pour ajouter une nouvelle mise en production sous l’onglet **Mises en production** dans la page Build et mise en production de Visual Studio Online (VSO).

    ![Alt text](media\azure-stack-solution-geo-distributed\image6.png)

2. Appliquez le modèle **Déploiement Azure App Service**.

    ![Alt text](media\azure-stack-solution-geo-distributed\image7.png)

3. Sous le menu déroulant Ajouter un artefact, **ajoutez l’artefact** pour l’application de la build Azure Cloud.

    ![Alt text](media\azure-stack-solution-geo-distributed\image8.png)

4. Sous l’onglet Pipeline, sélectionnez le lien **Phase, Tâche** de l’environnement et définissez les valeurs de l’environnement cloud Azure.

    ![Alt text](media\azure-stack-solution-geo-distributed\image9.png)

5. Définissez le **nom de l’environnement** et sélectionnez **Abonnement** Azure pour le point de terminaison Azure Cloud.

    ![Alt text](media\azure-stack-solution-geo-distributed\image10.png)

6. Sous le nom de l’environnement, définissez le **nom de l’App Service Azure**.

    ![Alt text](media\azure-stack-solution-geo-distributed\image11.png)

7. Entrez **VS2017 hébergé** sous la file d’attente de l’agent pour l’environnement hébergé dans le cloud Azure.

    ![Alt text](media\azure-stack-solution-geo-distributed\image12.png)

8. Dans le menu Déployer Azure App Service, sélectionnez **le package ou le dossier** valide pour l’environnement. Sélectionnez OK pour **l’emplacement du dossier**.

    ![Alt text](media\azure-stack-solution-geo-distributed\image13.png)

    ![Alt text](media\azure-stack-solution-geo-distributed\image14.png)

9. Enregistrez toutes les modifications et revenez au **pipeline de mises en production**.

    ![Alt text](media\azure-stack-solution-geo-distributed\image15.png)

10. Ajoutez un **nouvel artefact** en sélectionnant la build pour l’application Azure Stack.

    ![Alt text](media\azure-stack-solution-geo-distributed\image16.png)

11. Ajoutez un autre environnement en appliquant **Déploiement Azure App Service**.

    ![Alt text](media\azure-stack-solution-geo-distributed\image17.png)

12. Nommez le nouvel environnement **Azure Stack**.

    ![Alt text](media\azure-stack-solution-geo-distributed\image18.png)

13. Recherchez l’environnement Azure Stack sous l’onglet **Tâche**.

    ![Alt text](media\azure-stack-solution-geo-distributed\image19.png)

14. Sélectionnez **l’abonnement** pour le point de terminaison Azure Stack.

  ![Alt text](media\azure-stack-solution-geo-distributed\image20.png)

15. Définissez le nom de l’application web Azure Stack comme **nom de l’App Service**.

    ![Alt text](media\azure-stack-solution-geo-distributed\image21.png)

16. Sélectionnez **l’agent Azure Stack**.

    ![Alt text](media\azure-stack-solution-geo-distributed\image22.png)

17. Sous la section Déployer Azure App Service, sélectionnez **le package ou le dossier** valide pour l’environnement. Sélectionnez OK pour **l’emplacement du dossier**.

    ![Alt text](media\azure-stack-solution-geo-distributed\image23.png)

    ![Alt text](media\azure-stack-solution-geo-distributed\image24.png)

18. Sous l’onglet **Variable**, ajoutez une variable nommée `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, définissez sa valeur sur `true` et sa portée sur `Azure Stack`.

    ![Alt text](media\azure-stack-solution-geo-distributed\image25.png)

19. Sélectionnez l’icône du déclencheur de déploiement **Continu** dans les deux artefacts et activez le déclencheur de déploiement **Continu**.

    ![Alt text](media\azure-stack-solution-geo-distributed\image26.png)

20. Sélectionnez l’icône des conditions **Prédéploiement** dans l’environnement Azure Stack et définissez le déclencheur sur **Après la mise en production**.

21. Enregistrez toutes les modifications.

> [!Note]  
>  Certains paramètres des tâches peuvent avoir été automatiquement définis en tant que [variables d’environnement](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) quand vous avez créé une définition de mise en production à partir d’un modèle. Ces paramètres ne peuvent pas être modifiés dans les paramètres de la tâche ; au lieu de cela, vous devez sélectionner l’élément de l’environnement parent pour modifier ces paramètres.

## <a name="part-2-update-web-app-options"></a>Partie 2 : Mise à jour des options de l’application web

[Azure Web Apps](https://docs.microsoft.com/azure/app-service/app-service-web-overview) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques. 

![Alt text](media\azure-stack-solution-geo-distributed\image27.png)

> [!div class="checklist"]
> - Mapper un nom DNS personnalisé existant à des applications web Azure
> - Utilisez un enregistreur **CNAME ou un **enregistrement A** pour mapper un nom DNS personnalisé à App Service.

### <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Mapper un nom DNS personnalisé existant à des applications web Azure

> [!Note]  
>  Utilisez un enregistrement CNAME pour tous les noms DNS personnalisés, à l’exception d’un domaine racine (par exemple, northwind.com).

Pour migrer un site actif et son nom de domaine DNS vers App Service, voir [Migrer un nom DNS actif vers Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-custom-domain-name-migrate).

### <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel :

-   [Créez une application App Service](https://docs.microsoft.com/azure/app-service/), ou utilisez une application créée pour un autre didacticiel.

-   Achetez un nom de domaine et fournissez un accès au registre DNS au fournisseur de domaine.

Mettez à jour le fichier de zone DNS pour le domaine. Azure AD vérifie la propriété du nom de domaine personnalisé. Utilisez [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) pour les enregistrements DNS Azure/Office 365/externes dans Azure, ou ajouter l’entrée DNS à [un autre bureau d’enregistrement DNS](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

-   Enregistrez un domaine personnalisé avec un bureau d’enregistrement public.

-   Connectez-vous au Bureau d’enregistrement des noms de domaine pour le domaine. (Il se peut qu’un administrateur approuvé doive effectuer les mises à jour DNS.)

-   Mettez à jour le fichier de zone DNS du domaine en ajoutant l’entrée DNS fournie par Azure AD.

Par exemple, pour ajouter les entrées DNS fornorthwindcloud.com et www.northwindcloud.com, configurez les paramètres DNS pour le domaine racine thenorthwindcloud.com.

> [!Note]  
>  Vous pouvez acheter un nom de domaine à l’aide du [portail Azure](https://docs.microsoft.com/azure/app-service/custom-dns-web-site-buydomains-web-app).  
> Pour mapper un nom DNS personnalisé à une application web, le [plan App Service](https://azure.microsoft.com/pricing/details/app-service/) de l’application web doit être un niveau payant (**Partagé**, **De base**, **Standard** ou **Premium**).



### <a name="create-and-map-cname-and-a-records"></a>Créer et mapper des enregistrements CNAME et A

#### <a name="access-dns-records-with-domain-provider"></a>Accès aux enregistrements DNS avec le fournisseur de domaine

> [!Note]  
>  Utilisez Azure DNS pour configurer un nom DNS personnalisé pour les applications web Azure. Pour plus d’informations, consultez [Use Azure DNS to provide custom domain settings for an Azure service](https://docs.microsoft.com/azure/dns/dns-custom-domain) (Utiliser DNS Azure pour fournir des paramètres de domaine personnalisé pour un service Azure).

1.  Connectez-vous au site web du fournisseur principal.

2.  Trouvez la page de gestion des enregistrements DNS. Chaque fournisseur de domaine a sa propre interface d’enregistrements DNS. Recherchez les zones du site qui portent les mentions **Nom de domaine**, **DNS** ou **Gestion du nom de serveur**.

Vous pouvez consulter la page d’enregistrements DNS dans **Mes domaines**. Trouvez le lien nommé **Fichier de zone**, **Enregistrements DNS** ou **Configuration avancée**.

La capture d’écran suivante est un exemple d’une page d’enregistrements DNS :

![Exemple de page d’enregistrements DNS](media\azure-stack-solution-geo-distributed\image28.png)

1.  Dans le bureau d’enregistrement de noms de domaine, sélectionnez **Ajouter ou Créer** pour créer un enregistrement. Certains fournisseurs ont différents liens pour ajouter divers types d’enregistrements. Consultez la documentation du fournisseur.

2.  Ajoutez un enregistrement CNAME pour mapper un sous-domaine au nom d’hôte par défaut de l’application.

  Pour l’exemple de domaine www.northwindcloud.com, ajoutez un enregistrement CNAME qui mappe le nom www sur <nom\_application>.azurewebsites.net.

Après avoir ajouté l’enregistrement CNAME, la page d’enregistrements DNS ressemble à l’exemple suivant :

![Navigation au sein du portail pour accéder à l’application Azure](media\azure-stack-solution-geo-distributed\image29.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Activer le mappage d’enregistrement CNAME dans Azure

1.  Dans un nouvel onglet, connectez-vous au portail Azure.

2.  Accédez à App Services.

3.  Sélectionnez l’application web.

4.  Dans le volet de navigation gauche de la page d’application du portail Azure, sélectionnez **Domaines personnalisés**.

5.  Cliquez sur l’icône **+** en regard de **Ajouter un nom d’hôte**.

1.  Tapez le nom de domaine complet, par exemple `www.northwindcloud.com`.

2.  Sélectionnez **Valider**.

3.  Si cela est indiqué, ajoutez des enregistrements supplémentaires d’autres types (`A` ou `TXT`) aux enregistrements DNS du bureau d’enregistrement de noms de domaine. Azure fournit les valeurs et les types de ces enregistrements :

    a.  Un enregistrement **A** pour effectuer un mappage vers l’adresse IP de l’application.

    b.  Un enregistrement **TXT** pour effectuer un mappage vers le nom d’hôte par défaut de l’application <nom_application>.azurewebsites.net. App Service utilise cet enregistrement uniquement au moment de la configuration, pour vérifier la propriété du domaine personnalisé. Après vérification, supprimez l’enregistrement TXT.

4.  Effectuez cette tâche dans l’onglet du bureau d’enregistrement de domaine et validez à nouveau pour que le bouton **Ajouter un nom d’hôte** soit activé.

5.  Assurez-vous que **Type d’enregistrement du nom d’hôte est défini sur **Enregistrement CNAME (exemple.com ou tout sous-domaine)**.

6.  Sélectionnez **Ajouter un nom d’hôte**.

7.  Tapez le nom de domaine complet, par exemple `northwindcloud.com`.

8.  Sélectionnez **Valider**.

9.  **Ajouter** est activé.

10. Assurez-vous que **Type d’enregistrement du nom d’hôte est défini sur **Enregistrement A (exemple.com)**.

11. **Ajoutez un nom d’hôte**.

  Un certain temps peut être nécessaire pour que les nouveaux noms d’hôte soient reflétés sur la page **Domaines personnalisés** de votre application. Essayez d’actualiser le navigateur pour mettre à jour les données.
  
  ![Alt text](media\azure-stack-solution-geo-distributed\image31.png) 
  
  En cas d’erreur, une notification d’erreur de vérification s’affiche en bas de la page. ![Erreur de vérification](media\azure-stack-solution-geo-distributed\image32.png)

> [!Note]  
>  Les étapes ci-dessus peuvent être répétées pour mapper un domaine générique (\*. northwindcloud.com). Cela vous permet d’ajouter n’importe quel sous-domaine à cet App Service sans avoir à créer un enregistrement CNAME distinct pour chacun. Suivez les instructions du bureau d’enregistrement pour configurer ce paramètre.

#### <a name="test-in-a-browser"></a>Test dans un navigateur

Dans votre navigateur, accédez aux noms DNS que vous avez configurés précédemment (par exemple, `northwindcloud.com`, www.northwindcloud.com).

## <a name="part-3-bind-a-custom-ssl-cert"></a>Partie 3 : Lier un certificat SSL personnalisé

Contenu de cette partie :

> [!div class="checklist"]
> - Lier le certificat SSL personnalisé à App Service
> - Appliquer le protocole HTTPS à l’application
> - Automatiser la liaison de certificat SSL avec des scripts

> [!Note]  
> Si nécessaire, obtenez un certificat SSL dans le portail Azure et liez-le à l’application web. Suivez le [didacticiel Certificats App Service](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site).

### <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel :

-   [Création d’une application App Service](https://docs.microsoft.com/azure/app-service/)
-   [Mappage d’un nom DNS personnalisé à une application web](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)
-   Acquisition d’un certificat SSL auprès d’une autorité de certification approuvée et utilisation de la clé pour signer la demande

### <a name="requirements-for-your-ssl-certificate"></a>Conditions requises pour le certificat SSL

Pour l’utiliser dans App Service, un certificat doit remplir toutes les conditions suivantes :

-   Être signé par une autorité de certification approuvée

-   Être exporté sous la forme d’un fichier PFX protégé par mot de passe

-   Contenir une clé privée d’au moins 2048 bits de long

-   Contenir tous les certificats intermédiaires dans la chaîne de certificats

> [!Note]  
>  Les **certificats de chiffrement à courbe elliptique (ECC)** sont compatibles avec App Service, mais ne sont pas couverts dans cet article. Consultez une autorité de certification pour bénéficier d’une assistance lors de la création de certificats ECC. 

#### <a name="prepare-the-web-app"></a>Préparation de l’application web

Pour lier un certificat SSL personnalisé à votre application web, le [plan App Service](https://azure.microsoft.com/pricing/details/app-service/) doit se trouver dans le niveau **De base**, **Standard** ou **Premium**.

#### <a name="sign-in-to-azure"></a>Connexion à Azure

1.  Ouvrez le [Portail Azure](https://portal.azure.com/) et accédez à votre application web.

2.  Dans le menu de gauche, sélectionnez **App Services**, puis le nom de l’application web.

![Sélectionner de l’application web](media\azure-stack-solution-geo-distributed\image33.png)

#### <a name="check-the-pricing-tier"></a>Vérification du niveau tarifaire

1.  Dans le volet de navigation de gauche de la page d’application web, accédez à la section **Paramètres** et sélectionnez **Monter en puissance (plan App Service)**.

    ![Menu Monter en puissance](media\azure-stack-solution-geo-distributed\image34.png)

1.  Vérifiez que l’application web ne se trouve pas dans le niveau **Gratuit** ou **Partagé**. Le niveau actuel de l’application web est encadré d’un rectangle bleu foncé.

    ![Vérification du niveau de tarification](media\azure-stack-solution-geo-distributed\image35.png)

Le SSL personnalisé n’est pas pris en charge aux niveaux **Gratuit** et **Partagé**. Pour modifier cela, suivez la procédure de la prochaine section ou accédez à la page **Choisir votre niveau de tarification** et consultez [Charger et lier votre certificat SSL](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).

#### <a name="scale-up-your-app-service-plan"></a>Évolution de votre plan App Service

1.  Sélectionnez l’un des niveaux **De base**, **Standard** ou **Premium**.

2.  Sélectionnez **Sélectionner**.

![Sélection du niveau tarifaire](media\azure-stack-solution-geo-distributed\image36.png)

L’opération de mise à l’échelle est terminée lorsque la notification s’affiche.

![Notification de montée en puissance](media\azure-stack-solution-geo-distributed\image37.png)

#### <a name="bind-your-ssl-certificate-and-merge-intermediate-certificates"></a>Liaison de votre certificat SSL et fusion des certificats intermédiaires

Fusionnez plusieurs certificats dans la chaîne.

1. **Ouvrez chaque certificat** reçu dans un éditeur de texte.

2. Créez un fichier pour le certificat fusionné, appelé *mergedcertificate.crt*. Dans un éditeur de texte, copiez le contenu de chaque certificat dans ce fichier. L’ordre de vos certificats devrait suivre l’ordre dans la chaîne d’approbation, commençant par votre certificat et finissant par le certificat racine. Cela ressemble à l’exemple suivant :

    ```Text

    -----BEGIN CERTIFICATE-----

    <your entire Base64 encoded SSL certificate>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 1>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 2>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded root certificate>

    -----END CERTIFICATE-----
    ```

#### <a name="export-certificate-to-pfx"></a>Exportation du certificat vers PFX

Exportez un certificat SSL fusionné avec la clé privée générée par le certificat.

Un fichier de clé privée est créé via OpenSSL. Pour exporter le certificat au format PFX, exécutez la commande suivante, en remplaçant les espaces réservés *<fichier-clé-privée>* et *<fichier-certificat-fusionné>* avec les chemins de clés privées et le fichier de certificat fusionné.

```PowerShell
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

Lorsque vous y êtes invité, définissez un mot de passe d’exportation pour charger votre certificat SSL dans App Service ultérieurement.

Lorsque vous utilisez IIS ou **Certreq.exe** pour générer la demande de certificat, installez le certificat sur un ordinateur local, puis [exportez le certificat au format PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

#### <a name="upload-the-ssl-certificate"></a>Charger le certificat SSL

1.  Dans le volet de navigation gauche, sélectionnez **Paramètres SSL**.

2.  Sélectionnez **Charger un certificat**.

3.  Dans **Fichier de certificat PFX**, sélectionnez le fichier PFX.

4.  4. Dans **Mot de passe du certificat**, tapez le mot de passe créé lors de l’exportation du fichier PFX.

5.  Sélectionnez **Télécharger**.

![Téléchargement d’un certificat](media\azure-stack-solution-geo-distributed\image38.png)

Lorsque App Service finit de charger le certificat, celui-ci apparaît dans la page **Paramètres SSL**.

![Alt text](media\azure-stack-solution-geo-distributed\image39.png)

#### <a name="bind-your-ssl-certificate"></a>Liaison de votre certificat SSL

1.  Dans la section **Liaisons SSL**, sélectionnez **Ajouter une liaison**.

    > [!Note]  
    >  Si le certificat a été chargé, mais n’apparaît pas dans les noms de domaine dans la liste déroulante **Nom d’hôte**, essayez d’actualiser la page du navigateur.

1.  Dans la page **Ajouter une liaison SSL**, utilisez les listes déroulantes pour sélectionner le nom de domaine à sécuriser et le certificat à utiliser.

2.  Dans **Type SSL**, choisissez d’utiliser [**l’indication du nom du serveur (SNI)**](http://en.wikipedia.org/wiki/Server_Name_Indication) ou le protocole SSL basé sur IP.

-   **SSL basé sur SNI** : plusieurs liaisons SSL basées sur SNI peuvent être ajoutées. Cette option permet de sécuriser plusieurs domaines sur la même adresse IP avec plusieurs certificats SSL. La plupart des navigateurs actuels (y compris Internet Explorer, Chrome, Firefox et Opera) prennent en charge SNI (plus d’informations sur la prise en charge des navigateurs dans [Indication du nom du serveur](http://wikipedia.org/wiki/Server_Name_Indication)).

-   **SSL basé sur IP** : une seule liaison SSL basée sur IP peut être ajoutée. Cette option permet de sécuriser une adresse IP publique dédiée avec un seul certificat SSL. Pour sécuriser plusieurs domaines, vous devez tous les sécuriser en utilisant le même certificat SSL. Cette option est sélectionnée par défaut pour la liaison SSL.

    1.  Sélectionnez **Ajouter une liaison**.

    ![Alt text](media\azure-stack-solution-geo-distributed\image40.png)

Lorsque App Service finit de charger votre certificat, celui-ci apparaît dans les sections **Liaisons SSL**.

![Alt text](media\azure-stack-solution-geo-distributed\image41.png)

#### <a name="remap-the-a-record-for-ip-ssl"></a>Remappage d’un enregistrement pour IP SSL

Si vous n’utilisez pas un SSL basé sur IP dans l’application web, consultez directement [Tester HTTPS pour votre domaine personnalisé](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).

Par défaut, l’application web utilise une adresse IP publique partagée. Lorsque le certificat est lié avec SSL basé sur IP, App Service crée une adresse IP dédiée pour l’application web.

Lorsqu’un enregistrement A est mappé sur l’application web, le registre de domaine doit être mis à jour avec l’adresse IP dédiée.

La page **Domaine personnalisé** est mise à jour avec la nouvelle adresse IP dédiée. Copiez cette [adresse IP](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain), puis remappez [l’enregistrement A](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain) sur cette nouvelle adresse IP.

#### <a name="test-https"></a>Test du protocole HTTPS

Dans différents navigateurs, accédez à https://<votre.domaine.personnalisé> pour vérifier que l’application web est fournie.

![Alt text](media\azure-stack-solution-geo-distributed\image42.png)

> [!Note]  
> Les erreurs de validation de certificat éventuelles peuvent être dues à un certificat auto-signé ou à des certificats intermédiaires mis de côté lors de l’exportation vers le fichier PFX.

#### <a name="enforce-https"></a>Appliquer le protocole HTTPS

Par défaut, n’importe qui peut accéder à l’application web avec HTTP. Vous pouvez rediriger toutes les requêtes HTTP sur le port HTTPS.

Dans la page d’application web, sélectionnez **Paramètres SSL**. Ensuite, dans **HTTPS uniquement**, sélectionnez **Activer**.

![Appliquer le protocole HTTPS](media\azure-stack-solution-geo-distributed\image43.png)

Lorsque l’opération est terminée, accédez à une des URL HTTP pointant vers l’application. Par exemple : 

-   http://<nom_application>.azurewebsites.net
-   http://northwindcloud.com
-   <http://www.northwindcloud.com>

#### <a name="enforce-tls-1112"></a>Appliquer le protocole TLS 1.1/1.2

L’application autorise [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 par défaut, ce qui est n’est plus considéré comme sécurisé par les normes du secteur telles que [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Pour appliquer des versions ultérieures de TLS, procédez comme suit :

1.  Dans le volet de navigation gauche de la page de l’application web, sélectionnez **Paramètres SSL**.

2.  Dans **Version TLS**, sélectionnez la version minimale de TLS.

![Appliquer le protocole TLS 1.1 ou 1.2](media\azure-stack-solution-geo-distributed\image44.png)

### <a name="create-a-traffic-manager-profile"></a>Créer un profil Traffic Manager

1.  Sélectionnez **Créer une ressource** > **Mise en réseau** > **Profil Traffic Manager** > **Créer**.

2.  Dans **Créer un profil Traffic Manager**, procédez comme suit :

    1.  Sous **Nom**, entrez un nom pour le profil. Ce nom doit être unique au sein de la zone trafficmanager.net et résulte dans le nom DNS trafficmanager.net qui est utilisé pour accéder au profil Traffic Manager.

    2.  Sous **Méthode de routage**, sélectionnez la **méthode de routage géographique**.

    3.  Sous **Abonnement**, sélectionnez l’abonnement sous lequel créer ce profil.

    4.  Sous **Groupe de ressources**, créez un groupe de ressources où placer ce profil.

    5.  Sous **Emplacement du groupe de ressources**, sélectionnez l’emplacement du groupe de ressources. Ce paramètre fait référence à l’emplacement du groupe de ressources et n’a pas d’impact sur le profil Traffic Manager qui sera déployé globalement.

    6.  Sélectionnez **Créer**.

    7.  Lorsque le déploiement global du profil Traffic Manager est terminé, il est répertorié comme l’une des ressources dans le groupe de ressources respectif.

    ![Alt text](media\azure-stack-solution-geo-distributed\image45.png)

### <a name="add-traffic-manager-endpoints"></a>Ajouter des points de terminaison Traffic Manager

1.  Dans la barre de recherche du portail, recherchez le nom du **profil Traffic Manager** créé dans la section précédente et sélectionnez le profil Traffic Manager dans les résultats affichés.

2.  Dans **Profil Traffic Manager**, à la section **Paramètres**, sélectionnez **Points de terminaison**.

3.  Sélectionnez **Ajouter**.

4.  Ajout du point de terminaison Azure Stack.

5.  Pour **Type**, sélectionnez **Point de terminaison externe**.

6.  Fournissez un **nom** pour ce point de terminaison, dans l’idéal, le nom du déploiement Azure Stack.

7.  Pour le nom de domaine complet (**FQDN**), utilisez l’URL externe de l’application web Azure Stack.

8.  Sous Mappage géographique, sélectionnez la région/le continent où se trouve la ressource, par exemple, **Europe.**

9.  Sous la liste déroulante Pays/Région qui apparaît, sélectionnez le pays qui s’applique à ce point de terminaison, par exemple, **Allemagne**.

10. Vérifiez que la case **Ajouter comme désactivé** est désélectionnée.

11. Sélectionnez **OK**.

12. Ajout du point de terminaison Azure :

    1.  Sous **Type**, sélectionnez **Point de terminaison Azure**.

    2.  Fournissez un **Nom** pour ce point de terminaison.

    3.  Sous **Type de ressource cible**, sélectionnez **App Service**.

    4.  Sous **Ressource cible**, sélectionnez **Choisir un service d’application** pour afficher la liste des applications Web sous le même abonnement. Dans **Ressources**, sélectionnez le service d’application à ajouter en tant que premier point de terminaison.

13. Sous Mappage géographique, sélectionnez la région/le continent où se trouve la ressource, par exemple, **Amérique du Nord/Amérique centrale/Caraïbes.**

14. Sous la liste déroulante Pays/Région qui apparaît, laissez ce champ vide pour sélectionner le regroupement régional complet ci-dessus.

15. Vérifiez que la case **Ajouter comme désactivé** est désélectionnée.

16. Sélectionnez **OK**.

  > [!Note]  
  >  Créez au moins un point de terminaison en choisissant la portée géographique Tous (Monde) comme point de terminaison par défaut de la ressource.

1.  Lorsque l’ajout de deux points de terminaison est terminé, ceux-ci s’affichent dans **Profil Traffic Manager** avec leur état de surveillance défini sur **En ligne**.

  ![Alt text](media\azure-stack-solution-geo-distributed\image46.png)

**Les multinationales s’appuient sur les fonctionnalités de géodistribution Azure**

En dirigeant le trafic de données à l’aide d’Azure Traffic Manager et de points de terminaison spécifiquement répartis à travers le monde, les multinationales peuvent se conformer aux réglementations régionales et assurer la conformité et la protection des données, deux éléments indispensables à la réussite des entreprises au niveau local et sur les sites distants.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les modèles Azure Cloud, consultez [Modèles de conception cloud](https://docs.microsoft.com/azure/architecture/patterns).