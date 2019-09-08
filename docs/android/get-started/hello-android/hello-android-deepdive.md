---
title: 'Hello, Android : Immersion'
description: Dans ce guide en deux parties, vous allez créer votre première application Xamarin.Android, et approfondir votre compréhension des principes fondamentaux du développement d’applications Android avec Xamarin. Vous y découvrirez les différents outils, concepts et étapes qui sont nécessaires à la création et au déploiement d’une application Xamarin.Android.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: EF0E110B-20EA-43F6-9476-1A0F41AFD298
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/05/2018
ms.openlocfilehash: c02e8e3c30508951df071810aff8db429a2f04ea
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756718"
---
# <a name="hello-android-deep-dive"></a>Hello, Android : Immersion

_Dans ce guide en deux parties, vous allez créer votre première application Xamarin.Android, et approfondir votre compréhension des principes fondamentaux du développement d’applications Android avec Xamarin. Vous y découvrirez les différents outils, concepts et étapes qui sont nécessaires à la création et au déploiement d’une application Xamarin.Android._

Dans [Hello, Android - Démarrage rapide](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-quickstart.md), vous avez créé et exécuté votre première application Xamarin.Android. Maintenant, vous allez approfondir votre connaissance du fonctionnement des applications Android, en vue de créer des programmes plus complexes. Ce guide passe en revue les étapes que vous avez suivies dans la procédure pas à pas « Hello, Android », pour que vous puissiez analyser ce que vous avez fait et comprendre les bases du développement d’applications Android.

Ce guide aborde les sujets suivants :

::: zone pivot="windows"

- **Introduction à Visual Studio** &ndash; Introduction à Visual Studio et à la création d’une application Xamarin.Android.

- **Structure d’une application Xamarin.Android** - Présentation des principaux composants d’une application Xamarin.Android.

- **Principes fondamentaux des applications et de l’architecture** &ndash; Introduction aux activités, au manifeste Android et au développement Android.

- **(IU) Interface utilisateur** &ndash; Création d’interfaces utilisateur avec Android Designer.

- **Activités et cycle de vie des activités** &ndash; Présentation du cycle de vie des activités et de la structuration de l’interface utilisateur dans le code.

- **Test, déploiement et finitions** &ndash; Finalisez votre application à l’aide de conseils sur les tests, le déploiement, la conception graphique, etc.

::: zone-end
::: zone pivot="macos"

- **Introduction à Visual Studio pour Mac** &ndash; Introduction à Visual Studio pour Mac et à la création d’une application Xamarin.Android.

- **Structure d’une application Xamarin.Android** &ndash; Présentation des principaux composants d’une application Xamarin.Android.

- **Principes fondamentaux des applications et de l’architecture** &ndash; Introduction aux activités, au manifeste Android et au développement Android.

- **(IU) Interface utilisateur** &ndash; Création d’interfaces utilisateur avec Android Designer.

- **Activités et cycle de vie des activités** &ndash; Présentation du cycle de vie des activités et de la structuration de l’interface utilisateur dans le code.

- **Test, déploiement et finitions** &ndash; Finalisez votre application à l’aide de conseils sur les tests, le déploiement, la conception graphique, etc.

::: zone-end

Ce guide a pour but de vous aider à développer les compétences et les connaissances nécessaires à la génération d’une application Android à écran unique. À la fin de ce guide, vous connaîtrez les différents composants d’une application Xamarin.Android et comprendrez la logique de leur organisation.

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introduction à Visual Studio

Visual Studio est un environnement IDE puissant, développé par Microsoft. Il comprend un concepteur visuel entièrement intégré, un éditeur de texte avec outils de refactorisation, un explorateur d’assembly, l’intégration du code source, et bien plus encore. Dans ce guide, vous allez apprendre à utiliser certaines fonctionnalités de base de Visual Studio avec le plug-in Xamarin.

Visual Studio organise le code en _solutions_ et en _projets_. Une solution est un conteneur qui peut comprendre un ou plusieurs projets. Un projet peut être une application (par exemple, iOS ou Android), une bibliothèque de prise en charge, une application de test, etc. Dans l’application **Phoneword**, vous avez ajouté un nouveau projet Android (à l’aide du modèle **Application Android**) à la solution **Phoneword** que vous avez créée avec le guide [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Introduction à Visual Studio pour Mac

Visual Studio pour Mac est un IDE gratuit et open source, similaire à Visual Studio. Il comprend un concepteur visuel entièrement intégré, un éditeur de texte avec outils de refactorisation, un explorateur d’assembly, l’intégration du code source, et bien plus encore. Dans ce guide, vous allez apprendre à utiliser certaines fonctionnalités de base de Visual Studio pour Mac. Si vous utilisez Visual Studio pour Mac pour la première fois, vous pouvez lire la [Présentation de Visual Studio pour Mac](https://docs.microsoft.com/visualstudio/mac/), qui est plus approfondie.

Tout comme Visual Studio, Visual Studio pour Mac organise le code en _solutions_ et en _projets_. Une solution est un conteneur qui peut comprendre un ou plusieurs projets. Un projet peut être une application (par exemple, iOS ou Android), une bibliothèque de prise en charge, une application de test, etc. Dans l’application **Phoneword**, vous avez ajouté un nouveau projet Android (à l’aide du modèle **Application Android**) à la solution **Phoneword** que vous avez créée avec le guide [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).

::: zone-end

<a name="anatomy" />

## <a name="anatomy-of-a-xamarinandroid-application"></a>Structure d’une application Xamarin.Android

::: zone pivot="windows"

La capture d’écran suivante montre le contenu d’une solution. Il s’agit de l’Explorateur de solutions, qui montre la structure de répertoires et tous les fichiers associés à la solution :

[![Explorateur de solutions](hello-android-deepdive-images/vs/02-solution-structure-sml.png)](hello-android-deepdive-images/vs/02-solution-structure.png#lightbox)

::: zone-end
::: zone pivot="macos"

La capture d’écran suivante montre le contenu d’une solution. Il s’agit du Panneau Solutions, qui montre la structure de répertoires et tous les fichiers associés à la solution :

[![Panneau Solution](hello-android-deepdive-images/xs/02-solution-structure-sml.png)](hello-android-deepdive-images/xs/02-solution-structure.png#lightbox)

::: zone-end

Une solution nommée **Phoneword** a été créée, et le projet Android **Phoneword** a été placé dans cette solution.

Examinez le contenu du projet pour voir chaque dossier et connaître son usage :

- **Properties** &ndash; Contient le fichier [AndroidManifest.xml](~/android/platform/android-manifest.md) qui contient tous les éléments nécessaires pour l’application Xamarin.Android, y compris le nom, le numéro de version et les autorisations. Le dossier **Properties** contient également [AssemblyInfo.cs](xref:Microsoft.VisualBasic.ApplicationServices.AssemblyInfo), qui est un fichier de métadonnées d’assembly .NET. Il est conseillé d’ajouter à ce fichier des informations de base concernant votre application.

- **References** &ndash; Contient les assemblys nécessaires pour générer et exécuter l’application. Si vous développez le répertoire Références, vous verrez les références aux assemblys .NET comme [System](xref:System), System.Core et [System.Xml](xref:System.Xml), ainsi qu’une référence à l’assembly Mono.Android de Xamarin.

- **Assets** &ndash; Contient les fichiers dont a besoin l’application pour être exécutée, notamment les polices, les fichiers de données locales et les fichiers texte. Les fichiers inclus dans cette rubrique sont accessibles via la classe `Assets` générée. Pour plus d’informations sur les ressources Android, consultez le guide Xamarin [Utilisation des ressources Android](~/android/app-fundamentals/resources-in-android/android-assets.md).

- **Resources** &ndash; Contient des ressources d’application, telles que des chaînes, des images et des dispositions. Vous pouvez accéder à ces ressources dans le code, via la classe `Resource` générée. Le guide [Ressources Android](~/android/app-fundamentals/resources-in-android/index.md) fournit davantage de détails sur le répertoire **Ressources**. Le fichier **AboutResources.txt** du modèle d’application fournit lui aussi un guide succinct concernant les ressources.

### <a name="resources"></a>Ressources

Le répertoire **Resources** comprend quatre dossiers nommés **drawable**, **layout**, **mipmap** et **values**, ainsi qu’un fichier nommé **Resource.designer.cs**.

Le tableau ci-dessous décrit chacun de ces éléments :

- **drawable** &ndash; Ce répertoire contient des [ressources Drawable](https://developer.android.com/guide/topics/resources/drawable-resource.html), telles que des images ou des bitmaps.

- **mipmap** &ndash; Ce répertoire contient des fichiers Drawable adaptés aux différentes densités de l’icône du lanceur. Dans le modèle par défaut, le répertoire drawable contient le fichier d’icône d’application **Icon.png**.

::: zone pivot="windows"

- **layout** &ndash; Ce répertoire contient des _fichiers Android Designer_ (.axml) qui définissent l’interface utilisateur de chaque écran ou activité. Le modèle crée une disposition par défaut appelée **activity_main.axml**.

::: zone-end
::: zone pivot="macos"

- **layout** &ndash; Ce répertoire contient des _fichiers Android Designer_ (.axml) qui définissent l’interface utilisateur de chaque écran ou activité. Le modèle crée une disposition par défaut appelée **Main.axml**.

::: zone-end

- **values** &ndash; Ce répertoire contient des fichiers XML qui stockent des valeurs simples, telles que des chaînes, des entiers et des couleurs. Le modèle crée un fichier pour stocker des valeurs de chaîne appelées **Strings.xml**.

- **Resource.Designer.cs** &ndash; Également appelé classe `Resource`, ce fichier est une classe partielle qui contient l’ID unique affecté à chacune des ressources. Il est automatiquement créé par les outils Xamarin.Android, puis est regénéré autant de fois que nécessaire. Ce fichier ne doit pas être modifié manuellement. Xamarin.Android remplacera toute modification manuelle apportée à ce fichier.

## <a name="app-fundamentals-and-architecture-basics"></a>Principes fondamentaux des applications et de l’architecture

Les applications Android n’ont pas de point d’entrée unique. Autrement dit, l’application ne contient pas de ligne destinée à être appelée par le système d’exploitation pour démarrer l’application. Au lieu de cela, l’application démarre lorsqu’Android instancie l’une de ses classes et charge en mémoire l’intégralité du processus de l’application.

Cette fonctionnalité unique d’Android peut être très utile lorsque vous concevez des applications complexes ou interagissez avec le système d’exploitation Android. Toutefois, ces options rendent Android plus complexe lorsque vous utilisez un scénario de base, tel que celui de l’application **Phoneword**. Pour cette raison, l’exploration de l’architecture Android est divisée en deux. Ce guide étudie une application qui utilise le point d’entrée le plus courant pour une application Android : le premier écran. [Hello, Android multi-écran](~/android/get-started/hello-android-multiscreen/index.md) montre toute la complexité de l’architecture Android en abordant les différents moyens de lancer l’application.

### <a name="phoneword-scenario---starting-with-an-activity"></a>Scénario Phoneword - Démarrage d’une activité

Lorsque vous ouvrez l’application **Phoneword** pour la première fois dans un émulateur ou sur un appareil, le système d’exploitation crée la première *activité*. Une activité est une classe Android spéciale qui correspond à un écran d’application. Elle est chargée de dessiner et de gérer l’interface utilisateur. Lorsqu’Android crée la première activité d’une application, il charge l’ensemble de l’application :

[![Chargement de l’activité](hello-android-deepdive-images/01-activity-load-sml.png)](hello-android-deepdive-images/01-activity-load.png#lightbox)

Dans la mesure où il n’existe aucune progression linéaire dans une application Android (vous pouvez lancer l’application à partir de plusieurs points), Android a une façon unique de suivre les classes et les fichiers qui composent une application. Dans l’exemple **Phoneword**, toutes les parties qui composent votre application sont enregistrées dans un fichier XML spécial que l’on appelle **manifeste Android**. Le rôle du **manifeste Android** est d’effectuer le suivi du contenu, des propriétés et des autorisations d’une application, et de les fournir au système d’exploitation Android. Vous pouvez voir l’application **Phoneword** comme étant constituée d’une activité (écran) et d’une collection de fichiers de ressources et d’assistance liées l’une à l’autre par le fichier manifeste Android, comme illustré dans le diagramme ci-dessous :

[![Fichiers d’assistance et ressources](hello-android-deepdive-images/02-resources-helpers-sml.png)](hello-android-deepdive-images/02-resources-helpers.png#lightbox)

Les sections qui suivent explorent les relations entre les différents composants de l’application **Phoneword**. Elles vous permettront de mieux comprendre le diagramme ci-dessus. Cette exploration commence par l’interface utilisateur, avec Android Designer et les fichiers de disposition.

## <a name="user-interface"></a>Interface utilisateur

> [!TIP]
> Les nouvelles versions de Visual Studio prennent en charge l’ouverture de fichiers .xml dans Android Designer.
>
> Les fichiers .axml et .xml sont pris en charge dans Android Designer.

::: zone pivot="windows"

**activity_main.axml** est le fichier de disposition de l’interface utilisateur correspondant au premier écran de l’application. L’extension .axml indique qu’il s’agit d’un fichier Android Designer (AXML est l’acronyme d’*Android XML*). Le nom *Main* est arbitraire du point de vue d’Android. Vous pourriez attribuer n’importe quel nom au fichier de disposition. Quand vous ouvrez **activity_main.axml** dans l’IDE, l’éditeur visuel pour fichiers de disposition Android, appelé *Android Designer*, se lance :

[![Android Designer](hello-android-deepdive-images/vs/03-android-designer-sml.png "Android Designer")](hello-android-deepdive-images/vs/03-android-designer.png#lightbox)

Dans l’application **Phoneword**, l’ID de **TranslateButton** a la valeur `@+id/TranslateButton` :

[![Attribution d’une valeur à l’ID de TranslateButton](hello-android-deepdive-images/vs/04-translatebutton-sml.png "Attribution d’une valeur à l’ID de TranslateButton")](hello-android-deepdive-images/vs/04-translatebutton.png#lightbox)

::: zone-end
::: zone pivot="macos"

**Main.axml** est le fichier de disposition de l’interface utilisateur correspondant au premier écran de l’application. L’extension .axml indique qu’il s’agit d’un fichier Android Designer (AXML est l’acronyme d’*Android XML*). Le nom *Main* est arbitraire du point de vue d’Android. Vous pourriez attribuer n’importe quel nom au fichier de disposition. Lorsque vous ouvrez **Main.axml** dans l’IDE, l’éditeur visuel pour fichiers de disposition Android appelé *Android Designer* est lancé :

[![Android Designer](hello-android-deepdive-images/xs/03-android-designer-sml.png)](hello-android-deepdive-images/xs/03-android-designer.png#lightbox)

Dans l’application **Phoneword**, l’ID de **TranslateButton** a la valeur `@+id/TranslateButton` :

[![Attribution d’une valeur à l’ID de TranslateButton](hello-android-deepdive-images/xs/04-translatebutton-sml.png)](hello-android-deepdive-images/xs/04-translatebutton.png#lightbox)

::: zone-end

Lorsque vous définissez la propriété `id` de **TranslateButton**, Android Designer mappe le contrôle **TranslateButton** à la classe `Resource`, et lui affecte un *ID de ressource* dont la valeur est `TranslateButton`. Ce mappage entre un contrôle visuel et une classe permet de localiser et d’utiliser **TranslateButton**, ainsi que d’autres contrôles du code de l’application. Ce point sera abordé plus en détail lorsque vous analyserez le code qui gère les contrôles. Pour l’instant, il vous suffit de savoir que la représentation sous forme de code d’un contrôle est liée à la représentation visuelle du contrôle dans le concepteur via la propriété `id`.

### <a name="source-view"></a>Mode Source

Tous les éléments définis dans l’aire de conception sont traduits en code XML pour pouvoir être utilisés par Xamarin.Android. Android Designer fournit une vue source contenant le code XML qui a été généré à partir du concepteur visuel. Vous pouvez afficher ce document XML en basculant vers le volet **Source** dans l’angle inférieur gauche de la vue du concepteur, comme illustré ci-dessous :

::: zone pivot="windows"

[![Vue source du concepteur](hello-android-deepdive-images/vs/05-source-view-sml.png "Vue source du concepteur")](hello-android-deepdive-images/vs/05-source-view.png#lightbox)

::: zone-end
::: zone pivot="macos"

[![Vue source du concepteur](hello-android-deepdive-images/xs/05-source-view-sml.png)](hello-android-deepdive-images/xs/05-source-view.png#lightbox)

::: zone-end

Ce code source XML doit contenir quatre éléments de contrôle : deux éléments **TextView**, un élément **EditText** et un élément **Button**. Pour une présentation plus approfondie d’Android Designer, reportez-vous au guide Xamarin Android [Android Designer](~/android/user-interface/android-designer/index.md).

Nous venons de voir les outils et les concepts utilisés pour la partie visuelle de l’interface utilisateur. À présent, il est temps de passer au code qui gère l’interface utilisateur.

## <a name="activities-and-the-activity-lifecycle"></a>Activités et cycle de vie des activités

La classe `Activity` contient le code qui gère l’interface utilisateur.
L’activité est chargée de répondre à une interaction utilisateur et de créer une expérience utilisateur dynamique.
Cette section présente la classe `Activity`, décrit le cycle de vie des activités et examine le code qui gère l’interface utilisateur dans l’application **Phoneword**.

### <a name="activity-class"></a>Classe d’activité

L’application **Phoneword** n’a qu’un seul écran (ou activité). La classe qui gère l’écran est appelée `MainActivity` et réside dans le fichier **MainActivity.cs**. Le nom `MainActivity` n’a aucune signification spéciale dans Android. Même si la convention veut que la première activité d’une application soit nommée `MainActivity`, le nom que porte l’activité n’a pas d’importance pour Android.

Lorsque vous ouvrez **MainActivity.cs**, vous pouvez voir que la classe `MainActivity` est une *sous-classe* de la classe `Activity`, et que l’activité est dotée de l’attribut [Activity](xref:Android.App.ActivityAttribute) :

```csharp
[Activity (Label = "Phone Word", MainLauncher = true)]
public class MainActivity : Activity
{
  ...
}
```

L’attribut `Activity` enregistre l’activité dans le manifeste Android. Ainsi, Android est informé que cette classe fait partie de l’application **Phoneword** qui est gérée par ce manifeste. La propriété `Label` définit le texte qui doit s’afficher en haut de l’écran.

La propriété `MainLauncher` indique à Android qu’il doit afficher cette activité au démarrage de l’application. Cette propriété devient importante lorsque vous ajoutez d’autres activités (ou écrans) à l’application, comme expliqué dans le guide [Hello, Android multi-écran](~/android/get-started/hello-android-multiscreen/index.md).

Maintenant que les principes fondamentaux de `MainActivity` ont été traités, il est temps de plonger dans le code de l’activité en abordant le _cycle de vie des activités_.

### <a name="activity-lifecycle"></a>Cycle de vie des activités

Dans Android, les activités passent par différentes phases d’un cycle de vie, en fonction de leurs interactions avec l’utilisateur. Les activités peuvent être créées, démarrées, mises en pause, relancées, supprimées, etc. La classe `Activity` contient des méthodes que le système appelle à certaines phases du cycle de vie de l’écran. Le diagramme suivant montre la durée de vie typique d’une activité, ainsi que certaines méthodes de cycle de vie correspondantes :

[![Cycle de vie des activités](hello-android-deepdive-images/04-lifecycle-sml.png)](hello-android-deepdive-images/04-lifecycle.png#lightbox)

En substituant les méthodes de cycle de vie `Activity`, vous pouvez contrôler la façon dont l’activité est chargée, la façon dont elle réagit à l’utilisateur, et même ce qui se passe quand elle disparaît de l’écran de l’appareil. Par exemple, vous pouvez substituer les méthodes de cycle de vie du diagramme ci-dessus pour effectuer certaines tâches importantes :

- **OnCreate** &ndash; Crée des vues, initialise des variables et effectue un autre travail de préparation qui doit être effectué avant que l’utilisateur ne voie l’activité. Cette méthode est appelée une seule fois, lorsque l’activité est chargée en mémoire. 

- **OnResume** &ndash; Effectue toutes les tâches qui doivent s’exécuter chaque fois que l’activité retourne à l’écran de l’appareil.

- **OnPause** &ndash; Effectue toutes les tâches qui doivent s’exécuter chaque fois que l’activité quitte l’écran de l’appareil.

Lorsque vous ajoutez du code personnalisé à une méthode du cycle de vie dans `Activity`, vous *substituez* l’*implémentation de base* de cette méthode de cycle de vie. Vous utilisez la méthode de cycle de vie existante (à laquelle du code est déjà attaché), et vous étendez cette méthode avec votre propre code. Vous appelez l’implémentation de base à partir de votre méthode pour vérifier que le code d’origine est exécuté avant votre nouveau code. La section suivante illustre cette exemple.

Le cycle de vie des activités est un thème important et complexe d’Android. Si vous souhaitez en savoir plus sur les activités lorsque vous aurez terminé la série des _Guides pour bien démarrer_, lisez le guide [Cycle de vie des activités](~/android/app-fundamentals/activity-lifecycle/index.md). Dans ce guide, la première partie se concentre sur la première étape du cycle de vie des activités, à savoir `OnCreate`.

### <a name="oncreate"></a>OnCreate

Android appelle la méthode `OnCreate` de `Activity` lorsqu’il crée l’activité (avant que l’écran ne soit présenté à l’utilisateur). Vous pouvez remplacer la méthode de cycle de vie `OnCreate` pour créer des vues et préparer votre activité aux interactions avec l’utilisateur :

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);
    // Additional setup code will go here
}
```

::: zone pivot="windows"

Dans l’application **Phoneword**, la première chose à faire dans `OnCreate` est de charger l’interface utilisateur créée dans Android Designer. Pour charger l’IU, appelez `SetContentView`, puis passez-lui le *nom de la disposition de ressource* pour le fichier de disposition : **activity_main.axml**. La disposition est située dans `Resource.Layout.activity_main`:

```csharp
SetContentView (Resource.Layout.activity_main);
```

Quand `MainActivity` démarre, il crée une vue basée sur le contenu du fichier **activity_main.axml**.

::: zone-end
::: zone pivot="macos"

Dans l’application **Phoneword**, la première chose à faire dans `OnCreate` est de charger l’interface utilisateur créée dans Android Designer. Pour charger l’interface utilisateur, appelez `SetContentView`, puis passez-lui le *nom de la disposition de ressource* pour le fichier de disposition : **Main.axml**. La disposition est située dans `Resource.Layout.Main`:

```csharp
SetContentView (Resource.Layout.Main);
```

Lorsque `MainActivity` démarre, il crée une vue qui est basée sur le contenu du fichier **Main.axml**. Notez que le nom du fichier de disposition correspond au nom de l’activité : *Main*.axml est le fichier de disposition de l’activité *Main*. Ceci n’est pas une exigence Android. Toutefois, au fur et à mesure que vous ajouterez des écrans à l’application, vous vous rendrez compte que cette convention de nommage permet d’associer plus facilement les fichiers de code aux fichiers de disposition.

::: zone-end

Une fois le fichier de disposition préparé, vous pouvez commencer à rechercher les contrôles.
Pour rechercher un contrôle, appelez `FindViewById`, puis passez-lui l’ID de ressource du contrôle :

```csharp
EditText phoneNumberText = FindViewById<EditText>(Resource.Id.PhoneNumberText);
Button translateButton = FindViewById<Button>(Resource.Id.TranslateButton);
TextView translatedPhoneWord = FindViewById<TextView>(Resource.Id.TranslatedPhoneWord);
```

Maintenant que le fichier de disposition contient des références aux contrôles, vous pouvez commencer à programmer leur réponse aux interactions utilisateur.

### <a name="responding-to-user-interaction"></a>Réponse aux interactions de l’utilisateur

Dans Android, l’événement `Click` écoute les interactions tactiles de l’utilisateur. Dans cette application, l’événement `Click` est géré par une expression lambda. Toutefois, un délégué ou un gestionnaire d’événements nommé pourraient également être utilisés. Le code final de **TranslateButton** ressemblait à ceci : 

```csharp
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = string.Empty;
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
    }
};
```

## <a name="testing-deployment-and-finishing-touches"></a>Test, déploiement et finitions

Visual Studio pour Mac et Visual Studio proposent de nombreuses options pour tester et déployer les applications. Cette section traite des options de débogage, montre comment tester des applications sur un appareil, et présente les outils permettant de créer des icônes d’application personnalisées en fonction de la densité de l’écran.

### <a name="debugging-tools"></a>Outils de débogage

Il est parfois difficile de détecter les problèmes dans le code d’une application. Pour mieux diagnostiquer les problèmes complexes qui se trouvent dans votre code, vous pouvez [définir un point d’arrêt](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), [exécuter pas à pas votre code](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) ou [enregistrer des informations dans la fenêtre Journal](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

### <a name="deploy-to-a-device"></a>Déployer l’application sur un appareil

L’émulateur est un bon point de départ pour le déploiement et le test d’une application. Toutefois, les utilisateurs n’utiliseront pas l’application finale dans un émulateur. Il est conseillé de tester régulièrement les applications sur un appareil réel, et assez tôt dans le processus de test.

Pour qu’un appareil Android puisse être utilisé pour tester des applications, il doit être d’abord configuré pour le développement. Le guide [Configurer un appareil pour le développement](~/android/get-started/installation/set-up-device-for-development.md) fournit des instructions précises sur la préparation d’un appareil au développement.

::: zone pivot="windows"

Une fois l’appareil configuré, vous pouvez y déployer des applications. Pour cela, connectez-le à un ordinateur, sélectionnez-le dans la boîte de dialogue **Sélectionner un appareil**, puis démarrez l’application :

![Sélection d’un appareil pour le débogage](hello-android-deepdive-images/vs/06-select-device.png "Sélection d’un appareil pour le débogage")

::: zone-end
::: zone pivot="macos"

Une fois l’appareil configuré, vous pouvez y déployer des applications. Pour cela, appuyez sur **Démarrer (Lire)** , sélectionnez-le dans la boîte de dialogue **Sélectionner un appareil**, puis appuyez sur **OK** :

[![Sélection d’un appareil pour le débogage](hello-android-deepdive-images/xs/06-select-device-sml.png)](hello-android-deepdive-images/xs/06-select-device.png#lightbox)

::: zone-end

Cette opération lance l’application sur l’appareil :

[![Saisie d’un numéro Phoneword](hello-android-deepdive-images/05-enter-phoneword-sml.png)](hello-android-deepdive-images/05-enter-phoneword.png#lightbox)

### <a name="set-icons-for-different-screen-densities"></a>Définir des icônes en fonction des densités d’écran

Les appareils Android peuvent avoir différentes tailles d’écran et différentes résolutions. De fait, toutes les images n’auront pas le même aspect sur tous les écrans. Voici, par exemple, une capture d’écran d’une icône de faible densité sur un appareil Nexus 5 haute densité. Voyez comme l’icône est floue comparée aux autres icônes :

[![Icône floue](hello-android-deepdive-images/06-blurry-icon-sml.png)](hello-android-deepdive-images/06-blurry-icon.png#lightbox)

Pour éviter cela, il est recommandé d’ajouter des icônes de différentes résolutions dans le dossier **Ressources**. Android fournit plusieurs versions du dossier **mipmap** pour gérer les icônes de lanceur de densité différente : *mdpi* pour une densité moyenne, *hdpi* pour une densité élevée, et  *xhdpi*, *xxhdpi* et *xxxhdpi* pour les écrans de très haute densité. Les icônes de tailles différentes sont stockées dans les dossiers **mipmap-**  :

::: zone pivot="windows"

![Dossiers mipmap](hello-android-deepdive-images/vs/07-mipmap-folders.png "Dossiers mipmap")

::: zone-end
::: zone pivot="windows"

[![Dossiers mipmap](hello-android-deepdive-images/xs/07-mipmap-folders-sml.png)](hello-android-deepdive-images/xs/07-mipmap-folders.png#lightbox)

::: zone-end

Android choisira l’icône ayant une densité adaptée :

[![Icônes avec une densité adaptée](hello-android-deepdive-images/07-appropriate-density-sml.png)](hello-android-deepdive-images/07-appropriate-density.png#lightbox)

### <a name="generate-custom-icons"></a>Générer des icônes personnalisées

Tout le monde ne dispose pas d’un concepteur pour créer des icônes personnalisées et lancer les images dont a besoin une application pour se démarquer des autres. Voici plusieurs méthodes que vous pouvez utiliser pour générer des graphiques d’application personnalisés :

::: zone pivot="windows"

- [Android Studio Asset](http://romannurik.github.io/AndroidAssetStudio/index.html) &ndash; Générateur web dans le navigateur pour tous les types d’icônes Android, comprenant des liens vers d’autres outils de la communauté. Son fonctionnement est optimal dans Google Chrome.

- Visual Studio &ndash; Vous pouvez l’utiliser afin de créer un ensemble d’icônes simple pour votre application, directement dans l’IDE.

- [Glyphish](http://www.glyphish.com/) &ndash; Ensemble d’icônes prédéfinies de haute qualité. Certaines peuvent être téléchargées gratuitement, d’autres sont payantes.

- [Fiverr](http://www.fiverr.com/) &ndash; Faites votre choix parmi une variété de concepteurs pour créer l’icône qui vous convient. À partir de 5 $. Parfois aléatoire. Cependant, une bonne ressource si vous avez besoin d’icônes conçues à la volée.

::: zone-end
::: zone pivot="macos"

- [Android Studio Asset](http://romannurik.github.io/AndroidAssetStudio/index.html) &ndash; Générateur web dans le navigateur pour tous les types d’icônes Android, comprenant des liens vers d’autres outils de la communauté. Son fonctionnement est optimal dans Google Chrome.

- [Sketch 3](https://itunes.apple.com/us/app/sketch/id852320343?mt=12) &ndash; Sketch est une application Mac pour la conception d’interfaces utilisateur, d’icônes et bien plus encore. Il s’agit de l’application qui a été utilisée pour créer l’ensemble d’icônes d’application et d’images de lancement Xamarin. Sketch 3 est disponible sur l’App Store et coûte environ 80 $. Vous pouvez aussi essayer gratuitement l’outil [Sketch Tool](http://bohemiancoding.com/sketch/tool/).

- [Pixelmator](http://www.pixelmator.com/) &ndash; Application polyvalente de modification d’image pour Mac. Coûte environ 30 $.

- [Glyphish](http://www.glyphish.com/) &ndash; Ensemble d’icônes prédéfinies de haute qualité. Certaines peuvent être téléchargées gratuitement, d’autres sont payantes.

- [Fiverr](http://www.fiverr.com/) &ndash; Faites votre choix parmi une variété de concepteurs pour créer l’icône qui vous convient. À partir de 5 $. Parfois aléatoire. Cependant, une bonne ressource si vous avez besoin d’icônes conçues à la volée.

::: zone-end

Pour plus d’informations sur les tailles d’icônes, reportez-vous au guide [Ressources Android](~/android/app-fundamentals/resources-in-android/index.md).

::: zone pivot="macos"

### <a name="adding-google-play-services-packages"></a>Ajout de paquets Google Play Services

_Google Play Services_ est un ensemble de bibliothèques de modules complémentaires qui permet aux développeurs Android de tirer parti des fonctionnalités les plus récentes de Google, telles que Google Maps, Google Cloud Messaging et la facturation dans l’application.
Auparavant, les liaisons vers toutes les bibliothèques Google Play Services étaient fournies par Xamarin sous la forme d’un package unique. Désormais, avec Visual Studio pour Mac, une boîte de dialogue Nouveau projet vous permet de sélectionner les packages Google Play Services à inclure dans votre application.

Pour ajouter une ou plusieurs bibliothèques Google Play Services, cliquez sur le nœud **Packages** dans votre arborescence de projets, puis cliquez sur **Ajouter un service Google Play…**  :

[![Ajout d’un service Google Play](hello-android-deepdive-images/xs/08-add-google-play-services-sml.png)](hello-android-deepdive-images/xs/08-add-google-play-services.png#lightbox)

Lorsque la boîte de dialogue **Ajouter un service Google Play** s’affiche, sélectionnez les packages (nugets) que vous souhaitez ajouter à votre projet :

[![Sélection des packages](hello-android-deepdive-images/xs/09-add-dialog-sml.png)](hello-android-deepdive-images/xs/09-add-dialog.png#lightbox)

Lorsque vous sélectionnez un service et cliquez sur **Ajouter un package**, Visual Studio pour Mac télécharge et installe le package que vous avez sélectionné, ainsi que tous les packages Google Play Services dépendants dont il a besoin. Dans certains cas, la boîte de dialogue **Acceptation de la licence** peut s’afficher et vous demander de cliquer sur **Accepter** avant l’installation des packages :

[![Acceptation de la licence](hello-android-deepdive-images/xs/10-license-acceptance-sml.png)](hello-android-deepdive-images/xs/10-license-acceptance.png#lightbox)

::: zone-end

## <a name="summary"></a>Récapitulatif

Félicitations ! Vous devez maintenant avoir une connaissance approfondie des composants des applications Xamarin.Android, ainsi que des outils nécessaires à sa création.

Dans le prochain _Guide pour bien démarrer_, vous allez étendre votre application pour qu’elle puisse gérer plusieurs écrans. Vous découvrirez également des concepts et une architecture Android plus avancés.
