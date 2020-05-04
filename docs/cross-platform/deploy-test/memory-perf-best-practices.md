---
title: Niveau de performance multiplateforme
description: Ce document décrit différentes techniques que vous pouvez utiliser pour améliorer les performances d’une application mobile. Il traite du profileur, de la ressource IDisposable, des références faibles, du récupérateur de mémoire SGen, des techniques de réduction de taille et bien plus encore.
ms.prod: xamarin
ms.assetid: 9ce61f18-22ac-4b93-91be-5b499677d661
author: davidortinau
ms.author: daortin
ms.date: 03/24/2017
ms.openlocfilehash: f99b64b67f4f1cabf3a5884fabb1301e5ee39eee
ms.sourcegitcommit: 06043f6a5628a7326ac6690eb62ead8e4780f4a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82738793"
---
# <a name="cross-platform-performance"></a>Niveau de performance multiplateforme

Le mauvais niveau de performance d’une application se présente de plusieurs façons. L’application semble ne pas répondre, le défilement de l’affichage est ralenti et la durée de vie de la batterie de l’appareil réduite. Toutefois, l’optimisation des performances implique davantage de choses que l’implémentation d’un code efficace. L’expérience utilisateur liée au niveau de performance de l’application doit également être prise en compte. Par exemple, pour contribuer à améliorer l’expérience utilisateur, vous devez vérifier que les opérations s’exécutent sans empêcher l’utilisateur d’effectuer d’autres activités.

<a name="profiler" />

## <a name="use-the-profiler"></a>Utiliser le profileur

Lorsque vous développez une application, il est important d’attendre que le code ait été profilé avant de tenter de l’optimiser. Le profilage est une technique qui permet de déterminer les endroits où les optimisations de code seront les plus efficaces pour réduire les problèmes de performances. Le profileur suit la quantité de mémoire qu’utilise l’application, et enregistre l’heure d’exécution des méthodes de l’application. Ces données aident à explorer les chemins d’exécution de l’application et le coût d’exécution du code, ce qui vous permet de découvrir les meilleures possibilités d’optimisation.

Xamarin Profiler mesure, évalue et aide à détecter les problèmes de performances dans une application. Il peut être utilisé pour profiler des applications Xamarin.iOS et Xamarin.Android dans Visual Studio pour Mac et Visual Studio. Pour plus d’informations sur Xamarin Profiler, consultez [Présentation de Xamarin Profiler](~/tools/profiler/index.md).

Les bonnes pratiques suivantes sont recommandées lorsque vous profilez une application :

- Évitez de profiler une application dans un simulateur, car les performances obtenues via un simulateur peuvent ne pas refléter la réalité.
- Dans l’idéal, le profilage doit être effectué sur plusieurs types d’appareils. En effet, les performances mesurées sur un appareil ne seront pas forcément les mêmes sur un autre appareil. Cependant, le profilage doit au moins être effectué sur un appareil dont les spécifications anticipées sont les moins élevées.
- Fermez toutes les autres applications pour être sûr de ne mesurer que l’impact de l’application en cours de profilage, et non celui d’autres applications.

<a name="idisposable" />

## <a name="release-idisposable-resources"></a>Libérer des ressources IDisposable

L’interface `IDisposable` fournit un mécanisme permettant de libérer des ressources. Elle fournit une méthode `Dispose` qui doit être implémentée pour libérer explicitement des ressources. `IDisposable` n’est pas un destructeur et doit être implémenté seulement dans les circonstances suivantes :

- Lorsque la classe contient des ressources non managées. Les ressources non managées qui nécessitent le plus souvent d’être libérées sont les fichiers, les flux et les connexions réseau.
- Lorsque la classe contient des ressources `IDisposable` managées

Les consommateurs de type peuvent ensuite appeler l’implémentation `IDisposable.Dispose` pour libérer des ressources lorsque l’instance n’est plus nécessaire. Il existe, pour cela, deux méthodes :

- L’encapsulation de l’objet `IDisposable` dans une instruction `using`
- L’encapsulation de l’appel à `IDisposable.Dispose` dans un bloc `try`/`finally`

### <a name="wrapping-the-idisposable-object-in-a-using-statement"></a>Encapsulation de l’objet IDisposable dans une instruction using

L’exemple de code suivant montre comment encapsuler un objet `IDisposable` dans une instruction `using` :

```csharp
public void ReadText (string filename)
{
  ...
  string text;
  using (StreamReader reader = new StreamReader (filename)) {
    text = reader.ReadToEnd ();
  }
  ...
}
```

La classe `StreamReader` implémente `IDisposable`, et l’instruction `using` fournit une syntaxe qui appelle la méthode `StreamReader.Dispose` sur l’objet `StreamReader` avant qu’il ne devienne hors de portée. Dans le bloc `using`, l’objet `StreamReader` est en lecture seule et ne peut pas être réassigné. L’instruction `using` garantit également que la méthode `Dispose` sera appelée même si une exception se produit, car le compilateur implémente le langage intermédiaire pour un bloc `try`/`finally`.

### <a name="wrapping-the-call-to-idisposabledispose-in-a-tryfinally-block"></a>Encapsulation de l’appel à IDisposable.Dispose dans un bloc Try/Finally

L’exemple de code suivant montre comment encapsuler l’appel à `IDisposable.Dispose` dans un bloc `try`/`finally` :

```csharp
public void ReadText (string filename)
{
  ...
  string text;
  StreamReader reader = null;
  try {
    reader = new StreamReader (filename);
    text = reader.ReadToEnd ();
  } finally {
    if (reader != null) {
      reader.Dispose ();
    }
  }
  ...
}
```

La classe `StreamReader` implémente `IDisposable`, et le bloc `finally` appelle la méthode `StreamReader.Dispose` pour libérer la ressource.

Pour plus d’informations, consultez [Interface IDisposable](xref:System.IDisposable).

<a name="events" />

## <a name="unsubscribe-from-events"></a>Se désabonner d’événements

Pour éviter les fuites de mémoire, vous devez vous désabonner des événements avant que l’objet d’abonné ne soit supprimé. Tant que vous ne vous êtes pas désabonné de l’événement, le délégué de cet événement situé dans l’objet de publication comporte une référence au délégué qui encapsule le gestionnaire d’événements de l’abonné. Tant que l’objet de publication contient cette référence, le nettoyage de la mémoire ne va pas récupérer la mémoire de l’objet d’abonné.

L’exemple de code suivant montre comment se désabonner d’un événement :

```csharp
public class Publisher
{
  public event EventHandler MyEvent;

  public void OnMyEventFires ()
  {
    if (MyEvent != null) {
      MyEvent (this, EventArgs.Empty);
    }
  }
}

public class Subscriber : IDisposable
{
  readonly Publisher publisher;

  public Subscriber (Publisher publish)
  {
    publisher = publish;
    publisher.MyEvent += OnMyEventFires;
  }

  void OnMyEventFires (object sender, EventArgs e)
  {
    Debug.WriteLine ("The publisher notified the subscriber of an event");
  }

  public void Dispose ()
  {
    publisher.MyEvent -= OnMyEventFires;
  }
}
```

La classe `Subscriber` se désabonne de l’événement dans sa méthode `Dispose`.

Les cycles de référence peuvent également se produire si vous utilisez des gestionnaires d’événements et la syntaxe lambda, car les expressions lambda peuvent référencer et maintenir des objets actifs. Par conséquent, une référence à la méthode anonyme peut être stockée dans un champ et utilisée pour vous désabonner de l’événement, comme illustré dans l’exemple de code suivant :

```csharp
public class Subscriber : IDisposable
{
  readonly Publisher publisher;
  EventHandler handler;

  public Subscriber (Publisher publish)
  {
    publisher = publish;
    handler = (sender, e) => {
      Debug.WriteLine ("The publisher notified the subscriber of an event");
    };
    publisher.MyEvent += handler;
  }

  public void Dispose ()
  {
    publisher.MyEvent -= handler;
  }
}
```

Le champ `handler` conserve la référence à la méthode anonyme, et est utilisé pour l’abonnement et le désabonnement aux événements.

<a name="weakreferences" />

## <a name="use-weak-references-to-prevent-immortal-objects"></a>Utiliser des références faibles pour empêcher les objets immortels

> [!NOTE]
> Les développeurs iOS doivent consulter la documentation sur [éviter les références circulaires dans iOS](~/ios/deploy-test/performance.md#avoid-strong-circular-references) pour s’assurer que leurs applications utilisent la mémoire de manière efficace.

<a name="lazy" />

## <a name="delay-the-cost-of-creating-objects"></a>Retarder le coût de la création d’objets

L’initialisation tardive peut être utilisée pour retarder la création d’un objet jusqu’à sa première utilisation. Cette technique est principalement utilisée pour améliorer les performances, éviter les calculs et réduire les besoins en mémoire.

Vous pouvez utiliser l’initialisation tardive pour les objets dont la création est coûteuse dans ces deux scénarios :

- L’application ne va pas utiliser l’objet.
- D’autres opérations coûteuses doivent être terminées avant que l’objet ne puisse être créé.

La classe `Lazy<T>` est utilisée pour définir un type initialisé tardivement, comme illustré dans l’exemple de code suivant :

```csharp
void ProcessData(bool dataRequired = false)
{
  Lazy<double> data = new Lazy<double>(() =>
  {
    return ParallelEnumerable.Range(0, 1000)
                 .Select(d => Compute(d))
                 .Aggregate((x, y) => x + y);
  });

  if (dataRequired)
  {
    if (data.Value > 90)
    {
      ...
    }
  }
}

double Compute(double x)
{
  ...
}
```

L’initialisation tardive se produit lors du premier accès à la propriété `Lazy<T>.Value`. Le type encapsulé est créé et retourné lors du premier accès, puis il est stocké en vue d’une utilisation ultérieure.

Pour plus d’informations sur l’initialisation tardive, consultez [Initialisation tardive](https://msdn.microsoft.com/library/dd997286(v=vs.110).aspx).

<a name="async" />

## <a name="implement-asynchronous-operations"></a>Implémenter des opérations asynchrones

.NET fournit également des versions asynchrones de plusieurs de ses API. Contrairement aux API synchrones, les API asynchrones font en sorte que le thread d’exécution actif ne bloque jamais le thread appelant pendant une période trop longue. Par conséquent, lorsque vous appelez une API à partir du thread d’interface utilisateur, vous devez utiliser l’API asynchrone, si celle-ci est disponible. Ainsi, le thread d’interface utilisateur reste non bloqué, ce qui améliore l’expérience utilisateur.

En outre, les opérations de longue durée doivent être exécutées sur un thread d’arrière-plan, pour éviter de bloquer le thread d’interface utilisateur. .NET fournit les mots clés `async` et `await`. Ceux-ci permettent d’écrire du code asynchrone qui exécute des opérations de longue durée sur un thread d’arrière-plan, et accède aux résultats une fois les opérations terminées. Toutefois, même si les opérations de longue durée peuvent être exécutées de façon asynchrone avec le mot clé `await`, cela ne garantit pas qu’elles seront exécutées sur un thread d’arrière-plan. Pour cela, vous devez passer les opérations de longue durée à `Task.Run`, comme illustré dans l’exemple de code suivant :

```csharp
public class FaceDetection
{
  ...
  async void RecognizeFaceButtonClick(object sender, EventArgs e)
  {
    await Task.Run(() => RecognizeFace ());
    ...
  }

  async Task RecognizeFace()
  {
    ...
  }
}
```

La méthode `RecognizeFace` s’exécute sur un thread d’arrière-plan, et la méthode `RecognizeFaceButtonClick` attend que la méthode `RecognizeFace` soit terminée avant de continuer.

Les opérations de longue durée doivent également prendre en charge l’annulation. Par exemple, il peut devenir inutile de poursuivre l’exécution d’une opération longue si l’utilisateur navigue au sein de l’application. Le modèle d’implémentation de l’annulation est le suivant :

- Créez une instance `CancellationTokenSource`. Cette instance va gérer et envoyer des notifications d’annulation.
- Passez la valeur de propriété `CancellationTokenSource.Token` à chaque tâche qui doit être annulable.
- Fournissez un mécanisme pour que chaque tâche puisse répondre à l’annulation.
- Appelez la méthode `CancellationTokenSource.Cancel` pour fournir une notification d’annulation.

> [!IMPORTANT]
> La classe `CancellationTokenSource` implémente l’interface `IDisposable`, ainsi, la méthode `CancellationTokenSource.Dispose` doit être appelée une fois l’utilisation de l’instance `CancellationTokenSource` terminée.

Pour plus d’informations, consultez [Présentation de la prise en charge asynchrone](~/cross-platform/platform/async.md).

<a name="sgen" />

## <a name="use-the-sgen-garbage-collector"></a>Utiliser le récupérateur de mémoire SGen

Les langages managés tels que C# utilisent le nettoyage de mémoire pour libérer la mémoire allouée aux objets qui ne sont plus utilisés. Les deux récupérateurs de mémoire utilisés par la plateforme Xamarin sont les suivants :

- [**SGen**](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/) : Il s’agit d’un récupérateur de mémoire générationnel qui est utilisé par défaut sur la plateforme Xamarin.
- [**Boehm**](http://www.hboehm.info/gc/) : Il s’agit d’un récupérateur de mémoire classique, non générationnel. Il s’agit du récupérateur de mémoire utilisé par défaut pour les applications Xamarin.iOS qui utilisent l’API classique.

SGen utilise l’un des trois segments de mémoire pour allouer de l’espace aux objets :

- **La « nursery »**  : Là où sont alloués les nouveaux petits objets. Lorsque la nursery manque d’espace, un nettoyage mineur de la mémoire est effectué. Tous les objets actifs seront déplacés vers le segment de mémoire principal.
- **Le segment de mémoire principal** : Là où sont conservés les objets de longue durée. Si le segment de mémoire principal manque d’espace, un nettoyage majeur de la mémoire est effectué. Si un nettoyage de la mémoire ne parvient pas à libérer suffisamment de mémoire, SGen demande davantage de mémoire au système.
- **L’espace des objets volumineux** : Là où sont conservés les objets qui nécessitent plus de 8 000 octets. Les objets volumineux ne passeront pas par la nursery et iront directement dans ce segment.

L’un des avantages de SGen est que le temps qu’il prend pour effectuer un nettoyage mineur de la mémoire est proportionnel au nombre de nouveaux objets actifs qui ont été créés depuis le dernier nettoyage mineur de la mémoire. Cela permet de réduire l’impact du nettoyage de la mémoire sur les performances d’une application, car les nettoyages mineurs seront moins longs que les nettoyages majeurs. Les nettoyages majeurs de la mémoire continueront d’être effectués, mais moins fréquemment.

Le récupérateur de mémoire SGen est le garbage collector par défaut dans Xamarin. iOS 9.2.1 et supérieur. par conséquent, il sera utilisé automatiquement. Notez que la possibilité de modifier le garbage collector a été supprimée des versions plus récentes de Visual Studio. Pour plus d’informations, consultez [nouveau système de comptage de références](~/ios/internals/newrefcount.md).

### <a name="reducing-pressure-on-the-garbage-collector"></a>Réduction de la pression sur le récupérateur de mémoire

Lorsque SGen démarre une opération de nettoyage de la mémoire, il arrête les threads de l’application pendant la récupération de la mémoire. Lorsque la mémoire est récupérée, l’exécution de l’application peut être brièvement suspendue ou son interface utilisateur peut être perturbée. Le degré de perceptibilité de cette suspension dépend de deux facteurs :

1. **La fréquence** : C’est-à-dire, le nombre de fois que se produit le nettoyage de la mémoire. La fréquence des nettoyages augmente avec le nombre d’allocations de mémoire qui se produisent entre chaque nettoyage.
1. **La durée** : C’est-à-dire le temps que prend chaque nettoyage de la mémoire. Elle est à peu près proportionnelle au nombre d’objets actifs nettoyés.

Collectivement, cela signifie que si plusieurs objets sont alloués, mais ne restent pas actifs, plusieurs garbage collections courts seront effectués. À l’inverse, si de nouveaux objets sont alloués lentement et si les objets restent actifs, les nettoyages de mémoire auront lieu moins fréquemment, mais dureront plus longtemps.

Pour réduire la pression sur le récupérateur de mémoire, suivez ces instructions :

- Pour éviter qu’un nettoyage de la mémoire ne soit effectué dans une boucle étroite, utilisez des pools d’objets. Ceci est particulièrement utile pour les jeux, qui doivent créer la plupart de leurs objets à l’avance.
- Libérez explicitement les ressources telles que les flux de données, les connexions réseau, les grands blocs de mémoire et les fichiers, lorsque ceux-ci ne sont plus nécessaires. Pour plus d’informations, consultez [Libérer des ressources IDisposable](#idisposable).
- Désinscrivez les gestionnaires d’événements lorsqu’ils ne sont plus nécessaires pour permettre le nettoyage des objets. Pour plus d’informations, consultez [Se désabonner d’événements](#events).

<a name="linker" />

## <a name="reduce-the-size-of-the-application"></a>Réduire la taille de l’application

Il est important de comprendre le processus de compilation de chaque plateforme pour savoir d’où vient la taille de l’exécutable d’une application :

- Les applications iOS sont compilées en langage d’assembly ARM, à l’aide d’une compilation AOT (Ahead Of Time). Le .NET Framework est inclus, et les classes inutilisées ne sont supprimées que si l’option de l’éditeur de liens associée est activée.
- Les applications Android sont compilées en langage intermédiaire et empaquetées avec MonoVM et la compilation juste-à-temps (JIT). Les classes de framework inutilisées ne sont supprimées que si l’option de l’éditeur de liens associée est activée.
- Les applications Windows Phone sont compilées en langage intermédiaire et exécutées par le runtime intégré.

En outre, si une application utilise de façon intensive des génériques, la taille finale de l’exécutable va augmenter, puisqu’il contiendra des versions compilées en mode natif des possibilités génériques.

Pour aider à réduire la taille des applications, la plateforme Xamarin a ajouté un éditeur de liens à ses outils de génération. Par défaut, l’éditeur de liens est désactivé et doit être activé dans les options de projet de l’application. Au moment de la génération, il effectue une analyse statique de l’application pour déterminer les types et les membres qui sont effectivement utilisés par l’application. Il va ensuite supprimer les types et les méthodes inutilisés de l’application.

La capture d’écran suivante présente les options de l’éditeur de liens dans Visual Studio pour Mac pour un projet Xamarin.iOS :

![](memory-perf-best-practices-images/linker-options-ios.png)

La capture d’écran suivante présente les options de l’éditeur de liens dans Visual Studio pour Mac pour un projet Xamarin.Android :

![](memory-perf-best-practices-images/linker-options-droid.png)

L’éditeur de liens fournit trois paramètres pour contrôler son comportement :

- **Ne pas lier** : L’éditeur de liens ne supprime aucun des types et méthodes inutilisés. Pour des raisons de performances, il s’agit du paramètre utilisé par défaut pour les builds de débogage.
- **Lier les SDK Framework uniquement/Assemblys de SDK uniquement** : Ces paramètres réduisent uniquement la taille des assemblys qui sont fournis par Xamarin. Le code utilisateur n’est pas concerné.
- **Lier tous les assemblys** : Il s’agit d’une optimisation plus agressive qui cible les assemblys du SDK et le code utilisateur. Pour les liaisons, ce paramètre supprime les champs de stockage inutilisés et allège chaque instance (ou objet lié), ce qui permet de consommer moins de mémoire.

L’option *Lier tous les assemblys* doit être utilisée avec précaution, car elle peut endommager l’application de façon inattendue. L’analyse statique qui est effectuée par l’éditeur de liens peut ne pas identifier correctement tout le code nécessaire, et entraîner la suppression d’une trop grande quantité de code dans l’application compilée. Vous ne pourrez constater cette situation qu’au moment de l’exécution, lorsque l’application se bloquera. Pour cette raison, il est important de bien tester une application après avoir modifié le comportement de l’éditeur de liens.

Si les tests ne révèlent pas que l’éditeur de liens a supprimé à tort une classe ou une méthode, il est possible de marquer les types ou les méthodes qui ne sont pas statiquement référencés, mais dont l’application a besoin, avec l’un des attributs suivants :

- `Xamarin.iOS.Foundation.PreserveAttribute` : Cet attribut est celui qui est utilisé pour les projets Xamarin.iOS.
- `Android.Runtime.PreserveAttribute` : Cet attribut est celui qui est utilisé pour les projets Xamarin.Android.

Par exemple, il peut être nécessaire de conserver les constructeurs par défaut des types qui sont instanciés dynamiquement. En outre, l’utilisation de la sérialisation XML peut nécessiter la conservation des propriétés de types.

Pour plus d’informations, consultez [Éditeur de liens pour iOS](~/ios/deploy-test/linker.md) et [Éditeur de liens pour Android](~/android/deploy-test/linker.md).

### <a name="additional-size-reduction-techniques"></a>Autres techniques de réduction de la taille

Il existe une grande variété d’architectures d’UC pour les appareils mobiles. Par conséquent, Xamarin.iOS et Xamarin.Android produisent des *binaires FAT* qui contiennent une version compilée de l’application pour chaque architecture de processeur. Ainsi, l’application mobile pourra s’exécuter sur un appareil, quelle que soit l’architecture du processeur.

Les étapes suivantes peuvent être effectuées pour réduire encore davantage la taille de l’exécutable de l’application :

- Vérifiez qu’une version release est générée.
- Réduisez le nombre d’architectures pour lesquelles l’application est générée, afin d’éviter la production d’un binaire FAT.
- Utilisez le compilateur LLVM pour générer un fichier exécutable plus optimisé.
- Réduisez la taille du code managé de l’application. Pour cela, activez l’éditeur de liens pour chaque assembly (*Lier tout* pour les projets iOS et *Lier tous les assemblys* pour les projets Android).

Les applications Android peuvent également être placées dans un APK distinct pour chaque ABI (« architecture »).
Pour en savoir plus, lisez ce billet de blog : [How To Keep Your Android App Size Down](https://montemagno.com/how-to-keep-your-android-app-size-down/).

<a name="optimizeimages" />

## <a name="optimize-image-resources"></a>Optimiser les ressources d’images

Les images font partie des ressources les plus coûteuses qui sont utilisées par les applications et sont souvent capturées en haute résolution. Même si cela permet d’avoir des images nettes et riches en détails, les applications qui affichent ce type d’images nécessitent généralement une plus grande utilisation du processeur pour décoder les images, et davantage de mémoire pour stocker les images décodées. Le décodage d’une image haute résolution dans la mémoire peut être vu comme un gaspillage de ressources lorsque l’on sait que l’image va être réduite pour son affichage dans l’application. Au lieu de cela, vous pouvez réduire l’utilisation du processeur et l’encombrement de la mémoire en créant plusieurs résolutions pour les images stockées qui s’approchent des tailles d’affichage prévues. Par exemple, une image qui doit s’afficher dans une liste aura probablement besoin d’une résolution inférieure à celle d’une image devant s’afficher en plein écran. En outre, les versions réduites des images haute résolution peuvent être chargées afin d’être affichées de manière efficace, avec une incidence minimale sur la mémoire. Pour plus d’informations, consultez [Charger des bitmaps volumineux de manière efficace](https://github.com/xamarin/recipes/tree/master/Recipes/android/resources/general/load_large_bitmaps_efficiently).

Quelle que soit la résolution des images, l’affichage de ressources d’images peut augmenter considérablement l’encombrement mémoire de l’application. Par conséquent, elles ne doivent être créées que si elles sont nécessaires, et doivent être libérées dès que l’application n’en a plus besoin.

<a name="activationperiod" />

## <a name="reduce-the-application-activation-period"></a>Réduire la période d’activation de l’application

Toutes les applications ont une *période d’activation*. Cette période représente la durée qui s’écoule entre le moment où l’application est démarrée et le moment où elle est prête à être utilisée. Cette période d’activation fournit une première impression de l’application. Il est donc important de réduire la période d’activation et la perception qu’en ont les utilisateurs, pour que leur première impression soit favorable.

Avant d’afficher son interface utilisateur initiale, une application doit fournir un écran de démarrage pour indiquer à l’utilisateur qu’elle démarre. Si l’application ne peut pas afficher rapidement son interface utilisateur initiale, un écran de démarrage doit être utilisé pour informer l’utilisateur de l’avancement de l’activation, pour que l’utilisateur comprenne que l’application n’est pas bloquée. Pour cela, vous pouvez utiliser une barre de progression ou un autre contrôle similaire.

Pendant la période d’activation, les applications exécutent une logique d’activation, ce qui inclut souvent le chargement et le traitement des ressources. Pour réduire la période d’activation, empaquetez les ressources nécessaires dans l’application, au lieu de les récupérer à distance. Par exemple, dans certains cas, il peut être judicieux de charger des données d’espace réservé stockées localement pendant la période d’activation. Ensuite, une fois que l’interface utilisateur initiale est affichée et que l’utilisateur peut interagir avec l’application, les données d’espace réservé peuvent être remplacées progressivement à partir d’une source distante. En outre, la logique d’activation de l’application doit exécuter uniquement les tâches qui permettent à l’utilisateur de commencer à utiliser l’application. Il peut être utile de différer le chargement des assemblys supplémentaires, car les assemblys sont chargés lors de leur première utilisation.

<a name="webservicecommunication" />

## <a name="reduce-web-service-communication"></a>Réduire la communication du service web

La connexion à un service web à partir d’une application peut avoir un impact sur les performances de l’application. Par exemple, une utilisation accrue de la bande passante réseau entraîne une utilisation accrue de la batterie de l’appareil. En outre, les utilisateurs peuvent utiliser l’application dans un environnement où la bande passante est limitée. Par conséquent, il est préférable de limiter l’utilisation de la bande passante entre une application et un service web.

L’une des méthodes possibles pour réduire l’utilisation de la bande passante d’une application est de compresser les données avant de les transférer sur le réseau. Toutefois, l’utilisation supplémentaire du processeur qui sert au processus de compression peut elle aussi entraîner une utilisation accrue de la batterie. Il s’agit là d’un compromis qu’il faut bien évaluer avant de prendre une décision.

Un autre problème à prendre en considération est celui du format des données qui sont transmises entre une application et un service web. Les deux principaux formats sont le format XML et le format JSON. Le format XML est un format d’échange de données textuelles qui produit des charges utiles de données relativement volumineuses, car il contient un grand nombre de caractères de mise en forme. Le format JSON est un format d’échange de données textuelles qui produit des charges utiles de données compactes, ce qui réduit les besoins en bande passante lors de l’envoi et de la réception de données. Par conséquent, le format JSON est souvent utilisé par défaut pour les applications mobiles.

Il est recommandé d’utiliser des objets de transfert de données lorsque vous transférez des données entre une application et un service web. Un objet de transfert de données contient un jeu de données destiné à être transféré via le réseau. En utilisant des objets de transfert de données,vous pouvez transmettre davantage de données dans un même appel distant, ce qui peut contribuer à réduire le nombre d’appels distants effectués par l’application. En règle générale, un appel distant qui transporte une importante charge utile de données va prendre à peu près autant de temps qu’un appel qui transporte une charge de données moins volumineuse.

Les données récupérées à partir du service web doivent être mises en cache localement. Les données mises en cache doivent être utilisées au lieu d’être récupérées de manière répétée à partir du service web. Toutefois, si vous choisissez cette méthode, vous devez implémenter une stratégie de mise en cache adaptée pour mettre à jour les données dans le cache local lorsque celles-ci sont modifiées dans le service web.

## <a name="summary"></a>Résumé

Cet article a abordé les techniques qui permettent d’accroître les performances des applications conçues sur la plateforme Xamarin. Utilisées conjointement, ces techniques peuvent considérablement réduire la charge de travail d’une UC, ainsi que la quantité de mémoire consommée par une application.

## <a name="related-links"></a>Liens connexes

- [Performances des applications Xamarin.iOS](~/ios/deploy-test/performance.md)
- [Performances de Xamarin.Android](~/android/deploy-test/performance.md)
- [Introduction de Xamarin Profiler](~/tools/profiler/index.md)
- [Performances de Xamarin. Forms](~/xamarin-forms/deploy-test/performance.md)
- [Vue d’ensemble de la prise en charge Async](~/cross-platform/platform/async.md)
- [IDisposable](xref:System.IDisposable)
