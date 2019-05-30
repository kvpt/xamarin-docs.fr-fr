---
title: 'Partie 2 : Architecture'
description: Ce document décrit les modèles d’architecture utiles pour générer des applications inter-plateformes. Il aborde les couches d’application classiques (couche de données, couche d’accès aux données, etc.) et des modèles logiciels mobiles courants (MVVM, MVC, etc.).
ms.prod: xamarin
ms.assetid: 2176DB2D-E84A-3757-CFAB-04A586068D50
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: 4f70690df46b71db75e48eca0fd901a854be0b21
ms.sourcegitcommit: 4a1520dee7759f8355ea65c8bb3d1bac8ba58122
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66354015"
---
# <a name="part-2---architecture"></a>Partie 2 : Architecture

Un principe clé de la création d’applications multiplateformes consiste à créer une architecture qui se prête à une optimisation de partage de code entre plateformes. Adhérant aux principes de programmation orientée objet suivants vous aide à créer une application bien conçue :

-   **Encapsulation** : s’assurer que les classes et les couches architecturales même n’exposent une API minimale qui effectue leur requis functions et masque les détails d’implémentation. Un niveau de la classe, cela signifie que les objets se comportent comme des « boîtes noires » et que la consommation du code n’a pas besoin de savoir comment ils accomplissement leurs tâches. Au niveau de l’architecture, cela signifie que d’implémentation des modèles comme Façade qui soutiennent une API simplifiée qui orchestre les interactions plus complexes pour le compte du code dans les couches plus abstraites. Cela signifie que le code de l’interface utilisateur (par exemple) doit uniquement être responsable de l’affichage des écrans et accepter une entrée de l’utilisateur ; et jamais interagir directement avec la base de données. De même le code d’accès aux données doit uniquement lire et écrire dans la base de données, mais jamais interagir directement avec les boutons ou d’étiquettes.
-   **Séparation des responsabilités** – Vérifiez que chaque composant (les deux à l’architecture et au niveau de la classe) a un objectif clair et bien défini. Chaque composant doit effectuer uniquement ses tâches définies et exposer cette fonctionnalité via une API qui est accessible aux autres classes qui doivent l’utiliser.
-   **Le polymorphisme** – programmation à une interface (ou une classe abstraite) qui prend en charge de plusieurs moyens d’implémentations que le code de base peut être écrite et partagé entre les plateformes, lors de l’interaction toujours avec des fonctionnalités spécifiques à la plateforme.


Le résultat naturel est une application modélisée d’après le monde réel ou des entités abstraites avec des couches logiques distincts. Séparant le code en couches rendre les applications plus faciles à comprendre, tester et à maintenir. Il est recommandé que le code dans chaque couche être physiquement distincts (soit dans les répertoires ou des projets même distincts pour les très grandes applications), ainsi que logiquement séparé (à l’aide d’espaces de noms).

 <a name="Typical_Application_Layers" />


## <a name="typical-application-layers"></a>Couches d’Application classiques

Tout au long de ce document et les études de cas, nous faisons référence dans les couches six application suivantes :

-   **Couche données** – persistance des données Non volatile, susceptible d’être une base de données SQLite mais peut être implémenté avec les fichiers XML ou tout autre mécanisme approprié.
-   **Couche d’accès aux données** – Wrapper autour de la couche de données qui fournit la création, lecture, mise à jour, l’accès aux données sans exposer les détails d’implémentation à l’appelant suppression (CRUD). Par exemple, la couche DAL peut-être contenir des instructions SQL pour interroger ou mettre à jour les données, mais le code de référencement serait inutile de la connaître.
-   **Couche métier** – (parfois appelé la couche de logique métier ou de la couche BLL) contient les définitions des entités métier (le modèle) et la logique métier. Candidat pour le modèle de Façade métier.
-   **Couche d’accès aux service** – utilisé pour accéder aux services dans le cloud : à partir des services web complexe (REST, JSON, WCF) à la récupération simple de données et des images à partir de serveurs distants. Encapsule le comportement de mise en réseau et fournit une API simple pour être consommés par les couches Application et l’interface utilisateur.
-   **Couche d’application** – Code qui est généralement spécifiques à la plateforme (généralement partagées entre les plateformes) ou de code qui est spécifique à l’application (pas généralement réutilisable). Un bon test si vous devez placer le code dans la couche d’Application par rapport à la couche d’interface utilisateur est (a) pour déterminer si la classe possède des contrôles d’affichage réel ou (b) si elle peut être partagée entre plusieurs écrans ou des périphériques (par exemple). iPhone et iPad).
-   **Couche d’Interface (UI) utilisateur** : la couche orientées utilisateur, contient les écrans, widgets et les contrôleurs qui gèrent les.


Une application ne doit pas nécessairement contienne toutes les couches, par exemple la couche d’accès de Service n’existerait pas dans une application qui n’accèdent pas aux ressources réseau. Une application très simple peut fusionner la couche de données et de la couche d’accès aux données, car les opérations sont extrêmement simples.

 <a name="Common_Mobile_Software_Patterns" />


## <a name="common-mobile-software-patterns"></a>Modèles courants de logiciels mobiles

Modèles constituent un moyen établi pour capturer périodique des solutions aux problèmes courants. Il existe quelques modèles clés qui sont utiles comprendre dans la création d’applications mobiles/compréhensible facile à gérer.

-   **Modèle de vue, ViewModel (MVVM)** – Model-View-ViewModel le modèle est souvent utilisée avec les infrastructures qui prennent en charge la liaison de données, telles que Xamarin.Forms. Il a été popularisée par les kits de développement prenant en charge XAML, telles que Windows Presentation Foundation (WPF) et Silverlight ; où le ViewModel agit en tant qu’intermédiaire entre les données (modèle) et d’une interface utilisateur (vue) via les commandes et de la liaison de données.
-   **Vue, contrôleur MVC (Model)** – un modèle commun et souvent mal compris, MVC est plus souvent utilisé lors de la création d’Interfaces utilisateur et fournit une séparation entre la définition réelle d’un écran de l’interface utilisateur (vue), le moteur derrière elle gère interaction (Controller) et les données qui remplit (modèle). Le modèle est en fait une partie facultative et par conséquent, l’essentiel de comprendre ce modèle se trouve dans la vue et le contrôleur. MVC est une approche courante pour les applications iOS.
-   **Façade métier** – également appelé modèle de gestionnaire fournit un point simplifié d’entrée pour un travail complexe. Par exemple, dans une application de suivi des tâches, vous pouvez avoir un `TaskManager` classe avec des méthodes telles que `GetAllTasks()` , `GetTask(taskID)` , `SaveTask (task)` , etc. Le `TaskManager` classe fournit une Façade pour le fonctionnement interne de réellement l’enregistrement/récupération d’objets de tâches.
-   **Singleton** – modèle de Singleton de l’offre un moyen dans lesquels qu’une seule instance d’un objet particulier peut déjà exister. Par exemple, lors de l’utilisation de SQLite dans les applications mobiles, vous souhaitez qu’une seule instance de la base de données. Le modèle Singleton constitue un moyen simple pour ce faire.
-   **Fournisseur** – un modèle a été formulée par Microsoft (sans doute semblable à base d’Injection de dépendance ou stratégie) à encourager la réutiliser le code dans les applications Silverlight, WPF et WinForms. Code partagé peut être écrites par rapport à une interface ou une classe abstraite et spécifique à la plateforme des implémentations concrètes sont écrites et transmies lorsque le code est utilisé.
-   **Async** – à ne pas confondre avec le mot clé Async, l’opération asynchrone de modèle est utilisé quand le travail long doit être exécutée sans maintenir de l’interface utilisateur ou le traitement en cours. Dans sa forme la plus simple, le modèle asynchrone décrit simplement que les tâches longues doivent être démarrés dans un autre thread (ou similaire abstraction thread telle qu’une tâche) pendant le thread actuel continue à traiter et attend une réponse à partir du processus d’arrière-plan , puis met à jour l’interface utilisateur lorsque les données et ou état est retourné.


Chacun des modèles sera examinée plus en détail comme leur utilisation pratique est illustrée dans les études de cas. Wikipedia a plus des descriptions détaillées de la [MVVM](https://en.wikipedia.org/wiki/Model–view–viewmodel), [MVC](https://en.wikipedia.org/wiki/Model–view–controller), [façade](https://en.wikipedia.org/wiki/Facade_pattern), [Singleton](https://en.wikipedia.org/wiki/Singleton_pattern), [stratégie](https://en.wikipedia.org/wiki/Strategy_pattern)et [fournisseur](https://en.wikipedia.org/wiki/Provider_model) modèles (et de [modèles de conception](https://en.wikipedia.org/wiki/Design_Patterns) généralement).
