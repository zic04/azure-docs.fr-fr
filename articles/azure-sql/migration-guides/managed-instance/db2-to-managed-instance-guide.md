---
title: 'Guide de migration : DB2 vers SQL Managed Instance'
description: Suivez ce guide pour migrer vos bases de données DB2 vers Azure SQL Managed Instance.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: f691ec2d6ba873ba8052c5fab35162932668f185
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94496185"
---
# <a name="migration-guide-db2-to-sql-managed-instance"></a>Guide de migration : DB2 vers SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Ce guide vous apprend à migrer vos bases de données DB2 vers Azure SQL Managed Instance à l’aide de l’Assistant Migration SQL Server pour DB2. 

Pour d’autres scénarios, consultez le [guide de migration de base de données](https://datamigration.microsoft.com/).

## <a name="prerequisites"></a>Prérequis 

Pour migrer votre base de données DB2 vers SQL Managed Instance, vous devez effectuer les opérations suivantes :

- vérifier que votre environnement source est pris en charge.
- télécharger l’[Assistant Migration SQL Server (SSMA) pour DB2](https://www.microsoft.com/download/details.aspx?id=54254).
- créer un compte [Azure SQL Managed Instance](../../database/single-database-create-quickstart.md) cible.


## <a name="pre-migration"></a>Prémigration

Une fois que vous avez rempli les prérequis, vous êtes prêt à découvrir la topologie de votre environnement et à évaluer la faisabilité de votre migration. 

### <a name="assess-and-convert"></a>Évaluer et convertir

Créez une évaluation à l’aide de l’Assistant Migration SQL Server (SSMA). 

Pour créer une évaluation, effectuez les étapes suivantes :

1. Ouvrez l’Assistant Migration SQL Server (SSMA) pour DB2. 
1. Sélectionnez **Fichier**, puis choisissez **Nouveau projet**. 
1. Fournissez un nom de projet et un emplacement d’enregistrement de votre projet, puis sélectionnez Azure SQL Managed Instance comme cible de migration dans la liste déroulante. Sélectionnez **OK**. 

   :::image type="content" source="media/db2-to-managed-instance-guide/new-project.png" alt-text="Fournissez les détails du projet, puis sélectionnez OK pour effectuer l’enregistrement.":::


1. Entrez des valeurs pour les informations de connexion à DB2 dans la boîte de dialogue **Se connecter à DB2**. 

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-db2.png" alt-text="Se connecter à votre instance DB2":::


1. Cliquez avec le bouton droit sur le schéma DB2 à migrer, puis choisissez **Créer un rapport**. Cette opération génère un rapport HTML. Vous pouvez également choisir **Créer un rapport** à partir de la barre de navigation après avoir sélectionné le schéma. 

   :::image type="content" source="media/db2-to-managed-instance-guide/create-report.png" alt-text="Cliquer avec le bouton droit sur le schéma et choisir Créer un rapport":::

1. Examinez le rapport HTML pour comprendre les statistiques de conversion et les erreurs ou avertissements. Vous pouvez également ouvrir le rapport dans Excel pour obtenir un inventaire des objets DB2 et de l’effort nécessaire pour effectuer des conversions de schémas. Le dossier de rapport situé dans SSMAProjects est l’emplacement par défaut du rapport.

   Par exemple : `drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-managed-instance-guide/report.png" alt-text="Examiner le rapport pour identifier les erreurs ou les avertissements":::


### <a name="validate-data-types"></a>Valider les types de données

Validez les mappages de types de données par défaut et changez-les en fonction des exigences, si nécessaire. Pour ce faire, procédez comme suit : 

1. Sélectionnez **Outils** dans le menu. 
1. Sélectionnez **Paramètres du projet**. 
1. Sélectionnez l’onglet **Mappage de types**. 

   :::image type="content" source="media/db2-to-managed-instance-guide/type-mapping.png" alt-text="Sélectionner le schéma, puis Mappage de types":::

1. Vous pouvez changer le mappage de type pour chaque table en sélectionnant la table dans l’**explorateur de métadonnées DB2**. 

### <a name="schema-conversion"></a>Conversion de schéma 

Pour convertir le schéma, effectuez les étapes suivantes :

1. (Facultatif) Ajoutez des requêtes dynamiques ou ad hoc à des instructions. Cliquez avec le bouton droit sur le nœud, puis choisissez **Ajouter des instructions**. 
1. Sélectionnez **Se connecter à Azure SQL Database**. 
    1. Entrez les informations de connexion pour vous connecter à votre compte Azure SQL Managed Instance.  
    1. Choisissez votre base de données cible dans la liste déroulante. 
    1. Sélectionnez **Connecter**. 

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-sql-managed-instance.png" alt-text="Renseigner les détails pour vous connecter à SQL Server":::


1. Cliquez avec le bouton droit sur le schéma, puis choisissez **Convertir le schéma**. Vous pouvez également choisir **Convertir le schéma** à partir de la barre de navigation supérieure après avoir sélectionné votre schéma. 

   :::image type="content" source="media/db2-to-managed-instance-guide/convert-schema.png" alt-text="Cliquer avec le bouton droit sur le schéma et choisir Convertir le schéma":::

1. Une fois la conversion terminée, comparez et examinez la structure du schéma afin d’identifier les problèmes potentiels et de les traiter en fonction des recommandations. 

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-review-schema-structure.png" alt-text="Comparer et examiner la structure du schéma afin d’identifier les problèmes potentiels et de les traiter en fonction de recommandations.":::

1. Enregistrez le projet localement pour un exercice de correction de schéma hors connexion. Dans le menu **Fichier**, sélectionnez **Enregistrer le projet**. 


## <a name="migrate"></a>Migrer

Une fois que vous avez terminé l’évaluation de vos bases de données et que vous traité toutes les anomalies, l’étape suivante consiste à exécuter le processus de migration.

Pour publier votre schéma et migrer vos données, effectuez les étapes suivantes :

1. Publiez le schéma : cliquez avec le bouton droit sur la base de données dans le nœud **Bases de données** de l’**Explorateur de métadonnées Azure SQL Managed Instance**, puis choisissez **Synchroniser avec la base de données**.

   :::image type="content" source="media/db2-to-managed-instance-guide/synchronize-with-database.png" alt-text="Cliquer avec le bouton droit sur la base de données et choisir Synchroniser avec la base de données":::

1. Migrer les données : cliquez avec le bouton droit sur le schéma dans l’**Explorateur de métadonnées DB2**, puis choisissez **Migrer les données**. 

   :::image type="content" source="media/db2-to-managed-instance-guide/migrate-data.png" alt-text="Cliquer avec le bouton droit sur le schéma et choisir Migrer les données":::

1. Fournissez les informations de connexion pour les instances DB2 et Azure SQL Managed Instance. 
1. Affichez le **Rapport de migration des données**. 

   :::image type="content" source="media/db2-to-managed-instance-guide/data-migration-report.png" alt-text="Examiner le rapport de migration des données":::

1. Connectez-vous à SQL Managed Instance à l’aide de SQL Server Management Studio et validez la migration en examinant les données et le schéma. 

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-schema-in-ssms.png" alt-text="Comparer le schéma dans SSMS":::

## <a name="post-migration"></a>Postmigration 

Après avoir terminé l’étape de migration, vous devez effectuer une série de tâches de post-migration pour garantir que tout fonctionne de manière aussi fluide et efficace que possible.

### <a name="remediate-applications"></a>Corriger les applications 

Une fois les données migrées vers l’environnement cible, toutes les applications qui consommaient la source doivent commencer à consommer la cible. Dans certains cas, l’accomplissement de cette tâche nécessitera d’apporter des changements aux applications.


### <a name="perform-tests"></a>Effectuer des tests

L’approche de test pour la migration de base de données comprend les activités suivantes :

1. **Développer des tests de validation** : pour tester la migration d’une base de données, vous devez utiliser des requêtes SQL. Vous devez créer les requêtes de validation à exécuter sur les bases de données source et cible. Vos requêtes de validation doivent couvrir l’étendue que vous avez définie.
1. **Configurer un environnement de test**: l’environnement de test doit contenir une copie de la base de données source et de la base de données cible. Veillez à isoler l’environnement de test.
1. **Exécuter des tests de validation** : exécutez les tests de validation sur la source et sur la cible, puis analysez les résultats.
1. **Exécuter des tests de performances**: exécutez un test de performances sur la source et sur la cible, puis analysez et comparez les résultats.

   > [!NOTE]
   > Pour obtenir de l’aide sur le développement et l’exécution de tests de validation post-migration, envisagez d’utiliser la Solution de qualité des données disponible dans le partenaire [QuerySurge](https://www.querysurge.com/company/partners/microsoft). 


## <a name="leverage-advanced-features"></a>Tirer parti des fonctionnalités avancées 

Veillez à tirer parti des fonctionnalités informatiques avancées offertes par Azure SQL Managed Instance, telles que la [haute disponibilité intégrée](../../database/high-availability-sla.md), la [détection des menaces](../../database/advanced-data-security.md) et la [surveillance et le paramétrage de votre charge de travail](../../database/monitor-tune-overview.md). 


Certaines fonctionnalités de SQL Server sont disponibles uniquement lorsque le [niveau de compatibilité de la base de données](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) est remplacé par le niveau de compatibilité le plus récent (150). 

## <a name="migration-assets"></a>Ressources de migration 

Pour obtenir une aide supplémentaire, consultez les ressources suivantes, qui ont été développées dans le cadre d’un engagement de projet de migration réel :

|Asset  |Description  |
|---------|---------|
|[Outil et modèle d’évaluation d’une charge de travail de données](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Cet outil fournit les plateformes cibles, la préparation du cloud et le niveau de correction des applications/bases de données « les mieux adaptés » pour une charge de travail donnée. Il propose une génération de rapports et des calculs simples en un clic qui permettent d’accélérer les évaluations d’un vaste domaine en fournissant un processus de décision de plateforme cible automatisé et uniforme.|
|[Package de découverte et d’évaluation de ressources de données DB2 zOS](https://github.com/Microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Après avoir exécuté le script SQL sur une base de données, vous pouvez exporter les résultats vers un fichier sur le système de fichiers. Plusieurs formats de fichier sont pris en charge, notamment *.csv, afin que vous puissiez capturer les résultats dans des outils externes, comme des feuilles de calcul. Cette méthode peut être utile si vous voulez partager facilement des résultats avec des équipes pour lesquelles le banc d’essai n’est pas installé.|
|[Artefacts et scripts d’inventaire IBM DB2 LUW](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Cette ressource comprend une requête SQL qui accède à des tables système IBM DB2 LUW version 11.1 et fournit un nombre d’objets par schéma et type d’objet, une estimation des « données brutes » dans chaque schéma et le dimensionnement des tables présentes dans chaque schéma, avec les résultats stockés au format CSV.|
|[Échelle pure DB2 LUW sur Azure - Guide de configuration](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Ce guide sert de point de départ pour un plan d’implémentation de DB2. Même si les besoins métier sont différents, le même modèle de base s’applique. Ce modèle d’architecture peut également être utilisé pour les applications OLAP sur Azure.|

Ces ressources ont été développées dans le cadre du programme Data SQL Ninja, qui est sponsorisé par l’équipe d’ingénierie Groupe de données Azure. La charte fondamentale du programme Data SQL Ninja a pour objet d’initier et d’accélérer une modernisation complexe et de faire face aux opportunités de migration de plateforme de données vers la plateforme de données Azure de Microsoft. Si vous pensez que votre organisation aimerait participer au programme Data SQL Ninja, contactez votre équipe en charge des compte et demandez-lui de soumettre une candidature.


## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une matrice des services et outils Microsoft et tiers qui peuvent vous aider dans les différents scénarios de migration de données et de base de données ainsi que leurs tâches spécialisées, consultez [Services et outils de migration de données](../../../dms/dms-tools-matrix.md).

- Pour en savoir plus sur Azure SQL Managed Instance, consultez :
   - [Vue d’ensemble de SQL Managed Instance](../../managed-instance/sql-managed-instance-paas-overview.md)
   - [Calculatrice du coût total de possession Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Pour plus d’informations sur l’infrastructure et le cycle d’adoption pour les migrations cloud, consultez :
   -  [Cloud Adoption Framework pour Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Meilleures pratiques pour l’évaluation des coûts et le dimensionnement des charges de travail migrées vers Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Pour évaluer la couche d’accès aux applications, consultez [Data Access Migration Toolkit (préversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Pour savoir comment exécuter un test A/B sur la couche d’accès aux données, consultez [Assistant Expérimentation de base de données](/sql/dea/database-experimentation-assistant-overview).
